# 序列号管理与出库管理绑定功能说明

## 功能概述
实现了序列号管理与出库管理的完整绑定功能。当创建出库单并选择序列号时,系统将自动更新序列号状态为"已出库",实现序列号的全生命周期管理。

## 核心功能

### 1. 序列号选择
- **位置**: 出库明细表单中,在备注字段下方
- **触发条件**: 选择产品后,如果该产品有可用序列号,则显示序列号选择区域
- **选择方式**: 下拉选择框,支持多选(用逗号分隔)
- **显示信息**: 序列号 + 产品型号(如果有)

### 2. 可用序列号过滤
- **状态过滤**: 只显示状态为"in_stock"(在库)的序列号
- **产品过滤**: 只显示当前选择产品的序列号
- **实时更新**: 切换产品时自动更新可用序列号列表

### 3. 序列号标签显示
- **已选序列号**: 以标签形式显示在选择框下方
- **删除功能**: 每个标签右侧有"×"按钮,可单独删除
- **数量提示**: 显示当前可用序列号总数

### 4. 自动状态更新
- **保存时**: 创建出库单时,自动将选择的序列号状态更新为"out_stock"(已出库)
- **关联记录**: 同时记录outbound_order_item_id,建立序列号与出库明细的关联
- **批量更新**: 支持一次更新多个序列号的状态

### 5. 出库明细表格
- **新增列**: 在出库明细表格中添加"序列号"列
- **显示方式**: 显示完整的序列号文本(多个用逗号分隔)
- **截断显示**: 超长文本自动截断,鼠标悬停显示完整内容

## 数据库设计

### serial_numbers表
```sql
- id: uuid (主键)
- serial_number: text (序列号)
- product_id: uuid (产品ID)
- product_model: text (产品型号)
- product_name_text: text (产品名称)
- product_code_text: text (产品编号)
- inbound_order_item_id: uuid (入库明细ID)
- outbound_order_item_id: uuid (出库明细ID) ← 关键字段
- current_shelf_id: uuid (当前货架ID)
- current_style: text (当前样式)
- status: text (状态: in_stock/out_stock) ← 关键字段
- notes: text (备注)
- created_at: timestamp
- updated_at: timestamp
```

### outbound_order_items表
```sql
- id: uuid (主键)
- outbound_order_id: uuid (出库单ID)
- product_id: uuid (产品ID)
- quantity: numeric (数量)
- shelf_id: uuid (货架ID)
- style: text (样式)
- notes: text (备注)
- serial_numbers_text: text (序列号文本) ← 新增字段
- ... 其他字段
```

## API接口

### 新增接口

#### getAvailableSerialNumbers
```typescript
// 获取指定产品的可用序列号(状态为in_stock)
getAvailableSerialNumbers(productId: string): Promise<SerialNumber[]>
```

#### updateSerialNumbersByNumbers
```typescript
// 批量更新序列号状态
updateSerialNumbersByNumbers(
  serialNumbers: string[],  // 序列号数组
  updates: Partial<SerialNumber>  // 更新内容
): Promise<SerialNumber[]>
```

## 业务流程

### 创建出库单流程
1. **选择产品**: 用户在出库明细中选择产品
2. **加载序列号**: 系统自动加载该产品的可用序列号(status='in_stock')
3. **选择序列号**: 用户从下拉框中选择一个或多个序列号
4. **显示标签**: 已选序列号以标签形式显示,可单独删除
5. **添加明细**: 点击"添加"按钮,将明细(含序列号)添加到列表
6. **保存出库单**: 点击"保存"按钮
7. **创建出库单**: 系统创建出库单主记录
8. **创建明细**: 系统创建出库明细记录,包含serial_numbers_text字段
9. **更新序列号**: 系统批量更新序列号状态:
   - status: 'in_stock' → 'out_stock'
   - outbound_order_item_id: 记录出库明细ID
10. **完成**: 提示成功,返回出库单列表

### 序列号状态变化
```
入库时: status = 'in_stock', inbound_order_item_id = xxx
出库时: status = 'out_stock', outbound_order_item_id = xxx
```

## 用户界面

### 出库明细表单
```
┌─────────────────────────────────────────────────────────┐
│ 产品 *        货架 *       样式 *       数量 *    备注   │
│ [选择产品▼]  [选择货架▼]  [新品▼]    [数量]    [备注]  │
└─────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────┐
│ 序列号(可选,多个用逗号分隔)                              │
│ [选择序列号▼]                                            │
│                                                          │
│ 已选序列号:                                              │
│ [SN001 ×] [SN002 ×] [SN003 ×]                           │
│                                                          │
│ 可用序列号: 15个                                         │
└─────────────────────────────────────────────────────────┘

                                              [+ 添加]
```

### 出库明细表格
```
┌──────────────────────────────────────────────────────────────┐
│ 产品编号 │ 产品名称 │ 数量 │ 货架 │ 样式 │ 序列号 │ 操作 │
├──────────────────────────────────────────────────────────────┤
│ P001     │ 产品A    │ 3    │ A1   │ 新品 │ SN001,SN002,SN003 │ 🗑️ │
│ P002     │ 产品B    │ 1    │ B2   │ 样机 │ -                 │ 🗑️ │
└──────────────────────────────────────────────────────────────┘
```

## 技术实现

### 前端实现 (OutboundFormPage.tsx)

#### 状态管理
```typescript
// 可用序列号列表
const [availableSerialNumbers, setAvailableSerialNumbers] = useState<SerialNumber[]>([]);

// 新增明细项(包含序列号)
const [newItem, setNewItem] = useState({
  product_id: '',
  quantity: '',
  shelf_id: '',
  style: '新品',
  notes: '',
  serial_numbers_text: '',  // 序列号文本
});
```

#### 自动加载序列号
```typescript
useEffect(() => {
  if (newItem.product_id) {
    loadAvailableSerialNumbers(newItem.product_id);
  } else {
    setAvailableSerialNumbers([]);
  }
}, [newItem.product_id]);
```

#### 序列号选择逻辑
```typescript
// 多选序列号,用逗号分隔
onValueChange={(v) => {
  const currentSerials = newItem.serial_numbers_text 
    ? newItem.serial_numbers_text.split(',').map(s => s.trim()) 
    : [];
  if (currentSerials.includes(v)) return;
  const newSerials = currentSerials.length > 0 
    ? `${newItem.serial_numbers_text},${v}` 
    : v;
  setNewItem({ ...newItem, serial_numbers_text: newSerials });
}}
```

#### 保存时更新序列号
```typescript
// 创建出库明细
const createdItem = await createOutboundOrderItem({
  outbound_order_id: orderId,
  product_id: item.product_id,
  quantity: item.quantity,
  shelf_id: item.shelf_id,
  style: item.style,
  notes: item.notes || undefined,
  serial_numbers_text: item.serial_numbers_text || undefined,
});

// 更新序列号状态
if (item.serial_numbers_text && createdItem) {
  const serialNumbers = item.serial_numbers_text
    .split(',')
    .map((s: string) => s.trim())
    .filter(Boolean);
  if (serialNumbers.length > 0) {
    await updateSerialNumbersByNumbers(serialNumbers, {
      status: 'out_stock',
      outbound_order_item_id: createdItem.id,
    });
  }
}
```

### 后端实现 (api.ts)

#### 获取可用序列号
```typescript
export const getAvailableSerialNumbers = async (productId: string) => {
  const { data, error } = await supabase
    .from('serial_numbers')
    .select('*')
    .eq('product_id', productId)
    .eq('status', 'in_stock')  // 只获取在库状态
    .order('created_at', { ascending: false });
  if (error) throw error;
  return Array.isArray(data) ? data : [];
};
```

#### 批量更新序列号
```typescript
export const updateSerialNumbersByNumbers = async (
  serialNumbers: string[], 
  updates: Partial<SerialNumber>
) => {
  const { data, error } = await supabase
    .from('serial_numbers')
    .update({
      ...updates,
      updated_at: new Date().toISOString(),
    })
    .in('serial_number', serialNumbers)  // 批量匹配
    .select();
  if (error) throw error;
  return Array.isArray(data) ? data : [];
};
```

## 使用场景

### 1. 医疗设备出库
- 每台设备有唯一序列号
- 出库时必须记录具体设备序列号
- 便于设备追溯和售后管理

### 2. 高价值产品管理
- 贵重产品需要精确追踪
- 序列号与出库单关联
- 支持质保和维修记录

### 3. 批次管理
- 同一产品不同批次有不同序列号
- 出库时记录具体批次
- 便于质量追溯

### 4. 库存盘点
- 通过序列号状态快速了解库存情况
- in_stock: 在库
- out_stock: 已出库

## 注意事项

### 1. 序列号唯一性
- 每个序列号只能被一个出库明细使用
- 已出库的序列号不会在可用列表中显示

### 2. 数据一致性
- 序列号状态与出库明细保持一致
- 删除出库单时需要恢复序列号状态(待实现)

### 3. 可选功能
- 序列号选择是可选的,不是必填项
- 适用于部分产品需要序列号管理的场景

### 4. 性能考虑
- 序列号列表按产品过滤,避免加载过多数据
- 批量更新序列号状态,减少数据库操作次数

## 未来优化方向

### 1. 序列号扫描
- 支持扫码枪快速录入序列号
- 批量扫描多个序列号

### 2. 序列号验证
- 验证序列号格式
- 检查序列号是否存在
- 防止重复选择

### 3. 出库单编辑
- 编辑出库单时恢复原序列号状态
- 重新选择序列号时更新状态

### 4. 序列号历史
- 记录序列号的完整流转历史
- 入库 → 在库 → 出库 → 退货等

### 5. 序列号报表
- 序列号流转报表
- 序列号库存统计
- 序列号追溯查询

### 6. 批量导入
- 支持Excel批量导入序列号
- 自动关联产品和出库单

### 7. 序列号打印
- 打印序列号标签
- 生成序列号二维码

## 相关文件

- **页面文件**: `src/pages/OutboundFormPage.tsx`
- **API文件**: `src/db/api.ts`
- **类型定义**: `src/types/database.ts`
- **数据库表**: `serial_numbers`, `outbound_order_items`

## 测试建议

### 1. 基本功能测试
- 选择产品后是否显示序列号选择区域
- 可用序列号列表是否正确过滤
- 选择序列号后是否正确显示标签
- 删除序列号标签是否正常工作

### 2. 保存功能测试
- 创建出库单时序列号是否正确保存
- 序列号状态是否更新为out_stock
- outbound_order_item_id是否正确关联

### 3. 数据验证测试
- 已出库的序列号是否不在可用列表中
- 切换产品时序列号列表是否正确更新
- 多个序列号是否正确用逗号分隔

### 4. 边界情况测试
- 产品没有序列号时的处理
- 所有序列号都已出库时的处理
- 不选择序列号直接保存的处理

### 5. 用户体验测试
- 序列号选择是否流畅
- 标签显示是否美观
- 提示信息是否清晰

## 总结

序列号管理与出库管理的绑定功能实现了:
1. ✅ 序列号选择功能
2. ✅ 可用序列号过滤
3. ✅ 序列号标签显示
4. ✅ 自动状态更新
5. ✅ 出库明细关联
6. ✅ 批量更新支持
7. ✅ 用户友好界面

该功能为库存管理系统提供了完整的序列号追溯能力,满足医疗设备等高价值产品的精细化管理需求。
