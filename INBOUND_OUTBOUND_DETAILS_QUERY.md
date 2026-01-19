# 入库/出库明细查询功能说明

## 功能概述
在入库管理和出库管理页面添加了明细查询功能。当入库单或出库单状态为"已审核"时,用户可以点击眼睛图标查看该单据的详细明细信息,包括基本信息和明细列表。

## 核心功能

### 1. 入库明细查询

#### 触发方式
- **位置**: 入库管理页面,状态列
- **条件**: 只有"已审核"状态的入库单才显示查看按钮
- **操作**: 点击眼睛图标(👁️)按钮

#### 显示内容
**基本信息**:
- 入库单号
- 入库类型
- 入库日期
- 供应商
- 备注

**明细表格**:
- 产品编号
- 产品名称
- 数量
- 货架
- 样式
- 备注

### 2. 出库明细查询

#### 触发方式
- **位置**: 出库管理页面,状态列
- **条件**: 只有"已审核"状态的出库单才显示查看按钮
- **操作**: 点击眼睛图标(👁️)按钮

#### 显示内容
**基本信息**:
- 出库单号
- 出库类型
- 出库日期
- 客户
- 领用人
- 版本号
- 备注

**明细表格**:
- 产品编号
- 产品名称
- 数量
- 货架
- 样式
- 序列号
- 备注

## 用户界面

### 入库管理页面
```
┌────────────────────────────────────────────────────────────┐
│ 入库单号    │ 入库类型 │ 入库日期   │ 状态          │ 操作 │
├────────────────────────────────────────────────────────────┤
│ IN12345     │ 采购入库 │ 2026/1/19  │ [已审核] 👁️  │ ...  │
│ IN12346     │ 退货入库 │ 2026/1/18  │ [草稿]       │ ...  │
└────────────────────────────────────────────────────────────┘
```

### 出库管理页面
```
┌────────────────────────────────────────────────────────────┐
│ 出库单号    │ 出库类型 │ 出库日期   │ 状态          │ 操作 │
├────────────────────────────────────────────────────────────┤
│ OUT12345    │ 销售出库 │ 2026/1/19  │ [已审核] 👁️  │ ...  │
│ OUT12346    │ 领用出库 │ 2026/1/18  │ [草稿]       │ ...  │
└────────────────────────────────────────────────────────────┘
```

### 明细弹窗
```
┌─────────────────────────────────────────────────────────────┐
│ 入库明细 - IN12345                                     [×]  │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│ ┌─────────────────────────────────────────────────────┐   │
│ │ 入库类型: 采购入库        入库日期: 2026/1/19      │   │
│ │ 供应商: ABC公司                                     │   │
│ │ 备注: 批量采购                                      │   │
│ └─────────────────────────────────────────────────────┘   │
│                                                             │
│ 入库明细                                                    │
│ ┌─────────────────────────────────────────────────────┐   │
│ │ 产品编号 │ 产品名称 │ 数量 │ 货架 │ 样式 │ 备注 │   │
│ ├─────────────────────────────────────────────────────┤   │
│ │ P001     │ 产品A    │ 100  │ A1   │ 新品 │ -    │   │
│ │ P002     │ 产品B    │ 50   │ B2   │ 新品 │ -    │   │
│ └─────────────────────────────────────────────────────┘   │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

## 技术实现

### 入库管理 (InboundPage.tsx)

#### 状态管理
```typescript
const [detailsOpen, setDetailsOpen] = useState(false);
const [selectedOrder, setSelectedOrder] = useState<InboundOrder | null>(null);
const [orderItems, setOrderItems] = useState<InboundOrderItem[]>([]);
const [products, setProducts] = useState<Product[]>([]);
const [shelves, setShelves] = useState<Shelf[]>([]);
const [suppliers, setSuppliers] = useState<any[]>([]);
const [loadingDetails, setLoadingDetails] = useState(false);
```

#### 查看明细函数
```typescript
const handleViewDetails = async (order: InboundOrder) => {
  setSelectedOrder(order);
  setDetailsOpen(true);
  setLoadingDetails(true);
  try {
    const items = await getInboundOrderItems(order.id);
    setOrderItems(items);
  } catch (error) {
    toast.error('获取入库明细失败');
  } finally {
    setLoadingDetails(false);
  }
};
```

#### UI实现
```typescript
// 状态列添加查看按钮
<TableCell>
  <div className="flex items-center gap-2">
    <Badge variant={order.status === 'approved' ? 'default' : 'secondary'}>
      {order.status === 'draft' ? '草稿' : order.status === 'approved' ? '已审核' : '已取消'}
    </Badge>
    {order.status === 'approved' && (
      <Button
        size="sm"
        variant="ghost"
        onClick={() => handleViewDetails(order)}
        title="查看明细"
      >
        <Eye className="h-4 w-4" />
      </Button>
    )}
  </div>
</TableCell>

// Dialog组件
<Dialog open={detailsOpen} onOpenChange={setDetailsOpen}>
  <DialogContent className="max-w-4xl max-h-[80vh] overflow-y-auto">
    <DialogHeader>
      <DialogTitle>入库明细 - {selectedOrder?.order_number}</DialogTitle>
    </DialogHeader>
    {/* 基本信息和明细表格 */}
  </DialogContent>
</Dialog>
```

### 出库管理 (OutboundPage.tsx)

#### 状态管理
```typescript
const [detailsOpen, setDetailsOpen] = useState(false);
const [selectedOrder, setSelectedOrder] = useState<OutboundOrder | null>(null);
const [orderItems, setOrderItems] = useState<OutboundOrderItem[]>([]);
const [products, setProducts] = useState<Product[]>([]);
const [shelves, setShelves] = useState<Shelf[]>([]);
const [customers, setCustomers] = useState<any[]>([]);
const [loadingDetails, setLoadingDetails] = useState(false);
```

#### 查看明细函数
```typescript
const handleViewDetails = async (order: OutboundOrder) => {
  setSelectedOrder(order);
  setDetailsOpen(true);
  setLoadingDetails(true);
  try {
    const items = await getOutboundOrderItems(order.id);
    setOrderItems(items);
  } catch (error) {
    toast.error('获取出库明细失败');
  } finally {
    setLoadingDetails(false);
  }
};
```

#### 序列号显示
```typescript
<TableCell>
  {item.serial_numbers_text ? (
    <div className="text-xs max-w-xs" title={item.serial_numbers_text}>
      {item.serial_numbers_text}
    </div>
  ) : (
    <span className="text-muted-foreground">-</span>
  )}
</TableCell>
```

## 数据加载

### 入库管理
```typescript
const fetchData = async () => {
  try {
    const [ordersData, productsData, shelvesData, suppliersData] = await Promise.all([
      getInboundOrders(),
      getProducts(),
      getShelves(),
      getSuppliers(),
    ]);
    setOrders(ordersData);
    setProducts(productsData);
    setShelves(shelvesData);
    setSuppliers(suppliersData);
  } catch (error) {
    toast.error('获取入库单失败');
  } finally {
    setLoading(false);
  }
};
```

### 出库管理
```typescript
const fetchData = async () => {
  try {
    const [ordersData, productsData, shelvesData, customersData] = await Promise.all([
      getOutboundOrders(),
      getProducts(),
      getShelves(),
      getCustomers(),
    ]);
    setOrders(ordersData);
    setProducts(productsData);
    setShelves(shelvesData);
    setCustomers(customersData);
  } catch (error) {
    toast.error('获取出库单失败');
  } finally {
    setLoading(false);
  }
};
```

## 使用场景

### 1. 审核后查看
- 管理员审核入库单/出库单后
- 查看具体入库/出库了哪些产品
- 确认数量和库位信息

### 2. 历史记录查询
- 查看历史入库/出库记录
- 追溯产品来源和去向
- 核对库存变动

### 3. 数据核对
- 与实际库存对比
- 检查入库/出库是否正确
- 发现数据异常

### 4. 报表生成
- 导出明细数据
- 制作入库/出库报表
- 统计分析

### 5. 序列号追溯
- 查看出库产品的序列号
- 追踪设备流向
- 售后服务支持

## 用户体验优化

### 1. 按钮显示逻辑
- 只在"已审核"状态显示查看按钮
- 草稿和已取消状态不显示
- 避免查看未完成的单据

### 2. 加载状态
- 打开Dialog时显示加载动画
- 使用Skeleton组件占位
- 提升用户体验

### 3. 数据展示
- 基本信息使用卡片布局
- 明细使用表格展示
- 信息层次清晰

### 4. 响应式设计
- Dialog最大宽度4xl
- 最大高度80vh
- 内容超出自动滚动

### 5. 数据关联
- 自动查找产品名称
- 自动查找货架名称
- 自动查找供应商/客户名称

## 注意事项

### 1. 权限控制
- 所有用户都可以查看已审核单据的明细
- 不涉及编辑和删除操作
- 只读查询功能

### 2. 数据完整性
- 确保产品、货架、供应商/客户数据已加载
- 处理数据不存在的情况
- 显示"-"占位符

### 3. 性能考虑
- 使用Promise.all并行加载数据
- 减少页面加载时间
- 明细数据按需加载

### 4. 错误处理
- 加载失败显示错误提示
- 使用toast提示用户
- 不影响主页面功能

## 未来优化方向

### 1. 打印功能
- 添加打印按钮
- 生成打印格式
- 支持批量打印

### 2. 导出功能
- 导出单个明细为Excel
- 导出PDF格式
- 自定义导出字段

### 3. 明细编辑
- 支持在Dialog中直接编辑明细
- 实时保存修改
- 权限控制

### 4. 更多信息
- 显示审核人和审核时间
- 显示创建人和创建时间
- 显示修改历史

### 5. 统计汇总
- 在Dialog底部显示汇总信息
- 总数量、总金额等
- 快速了解单据概况

### 6. 快速操作
- 从明细Dialog直接跳转到产品详情
- 查看产品库存
- 查看产品历史记录

### 7. 搜索过滤
- 在明细表格中搜索产品
- 按货架过滤
- 按样式过滤

## 相关文件

- **入库管理**: `src/pages/InboundPage.tsx`
- **出库管理**: `src/pages/OutboundPage.tsx`
- **API接口**: `src/db/api.ts`
- **类型定义**: `src/types/database.ts`
- **UI组件**: `src/components/ui/dialog.tsx`

## 测试建议

### 1. 基本功能测试
- 点击查看按钮是否打开Dialog
- Dialog是否显示正确的单据信息
- 明细数据是否正确加载

### 2. 数据显示测试
- 产品名称是否正确显示
- 货架名称是否正确显示
- 供应商/客户名称是否正确显示
- 序列号是否正确显示(出库)

### 3. 边界情况测试
- 无明细数据时的显示
- 产品/货架/供应商不存在时的处理
- 数据加载失败时的提示

### 4. 用户体验测试
- 加载动画是否流畅
- Dialog关闭是否正常
- 滚动是否正常工作

### 5. 响应式测试
- 不同屏幕尺寸下的显示
- 移动端的体验
- 表格的横向滚动

## 总结

入库/出库明细查询功能实现了:
1. ✅ 已审核单据的明细查看
2. ✅ 基本信息展示
3. ✅ 明细表格展示
4. ✅ 产品/货架/供应商/客户关联
5. ✅ 序列号显示(出库)
6. ✅ 加载状态处理
7. ✅ 响应式设计
8. ✅ 用户友好界面

该功能为库存管理系统提供了完整的单据明细查询能力,方便用户查看历史记录、核对数据、追溯产品流向,提升了系统的可用性和透明度。
