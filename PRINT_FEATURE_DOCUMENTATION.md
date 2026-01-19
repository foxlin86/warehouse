# 打印功能实现说明

## 实现时间
2026年1月19日

## 功能概述
为库存管理系统的三个核心页面添加打印功能,支持一键打印表格数据。

---

## 一、功能范围

### 1.1 已添加打印功能的页面
1. **出库管理** (OutboundPage.tsx)
   - 打印出库单列表
   - 包含出库单号、出库类型、出库日期、版本号、状态、创建时间

2. **入库管理** (InboundPage.tsx)
   - 打印入库单列表
   - 包含入库单号、入库类型、入库日期、状态、创建时间

3. **库存查询** (InventoryPage.tsx)
   - 打印库存列表
   - 包含产品编号、产品名称、货架、样式、数量

---

## 二、实现方式

### 2.1 打印按钮
在每个页面的标题区域添加"打印表单"按钮:
```tsx
<Button variant="outline" onClick={() => window.print()} className="no-print">
  <Printer className="mr-2 h-4 w-4" />
  打印表单
</Button>
```

### 2.2 打印触发
使用浏览器原生的 `window.print()` API触发打印对话框。

### 2.3 样式控制
通过CSS `@media print` 媒体查询控制打印样式。

---

## 三、打印样式优化

### 3.1 隐藏不需要打印的元素
使用 `.no-print` 类标记不需要打印的元素:
- 打印按钮本身
- 创建/编辑/删除等操作按钮
- 搜索框
- 侧边栏导航
- 页面头部和底部

```css
@media print {
  .no-print,
  aside,
  nav,
  header,
  footer,
  .sidebar {
    display: none !important;
  }
}
```

### 3.2 页面设置
```css
@page {
  size: A4 landscape;  /* A4横向 */
  margin: 1cm;         /* 1厘米边距 */
}
```

### 3.3 表格样式优化
```css
table {
  width: 100% !important;
  border-collapse: collapse !important;
  page-break-inside: auto;
}

th, td {
  border: 1px solid #000 !important;
  padding: 8px !important;
  text-align: left !important;
}

th {
  background-color: #f0f0f0 !important;
  font-weight: bold !important;
}
```

### 3.4 分页控制
```css
thead {
  display: table-header-group;  /* 每页都显示表头 */
}

tr {
  page-break-inside: avoid;     /* 避免行内分页 */
  page-break-after: auto;
}
```

---

## 四、代码修改详情

### 4.1 OutboundPage.tsx

#### 导入Printer图标
```typescript
import { Plus, Edit, CheckCircle, Trash2, Eye, Printer } from 'lucide-react';
```

#### 添加打印按钮
```tsx
<div className="flex gap-2">
  <Button variant="outline" onClick={() => window.print()} className="no-print">
    <Printer className="mr-2 h-4 w-4" />
    打印表单
  </Button>
  <Button onClick={() => navigate('/outbound/create')} className="no-print">
    <Plus className="mr-2 h-4 w-4" />
    创建出库单
  </Button>
</div>
```

#### 隐藏操作列
```tsx
<TableHead className="no-print">操作</TableHead>
...
<TableCell className="no-print">
  <div className="flex gap-2">
    {/* 操作按钮 */}
  </div>
</TableCell>
```

### 4.2 InboundPage.tsx

修改内容与OutboundPage.tsx类似:
- 导入Printer图标
- 添加打印按钮
- 隐藏操作列

### 4.3 InventoryPage.tsx

#### 导入Printer图标
```typescript
import { Search, Download, Printer } from 'lucide-react';
```

#### 添加打印按钮
```tsx
<div className="flex gap-2">
  <Button variant="outline" onClick={() => window.print()} className="no-print">
    <Printer className="w-4 h-4 mr-2" />
    打印表单
  </Button>
  <Button onClick={handleExport} disabled={loading || exporting || inventory.length === 0} className="no-print">
    <Download className="w-4 h-4 mr-2" />
    {exporting ? '导出中...' : '导出Excel'}
  </Button>
</div>
```

#### 隐藏搜索框
```tsx
<div className="mb-4 flex items-center gap-2 no-print">
  <Search className="h-4 w-4 text-muted-foreground" />
  <Input
    placeholder="搜索产品编号、名称或货架..."
    value={searchTerm}
    onChange={(e) => setSearchTerm(e.target.value)}
    className="max-w-sm"
  />
</div>
```

### 4.4 index.css

在全局样式文件中添加打印样式:
```css
/* 打印样式 */
@media print {
  /* 隐藏不需要打印的元素 */
  .no-print,
  aside,
  nav,
  header,
  footer,
  .sidebar,
  [role="navigation"],
  [role="banner"],
  [role="contentinfo"] {
    display: none !important;
  }

  /* 页面设置 */
  @page {
    size: A4 landscape;
    margin: 1cm;
  }

  /* 主容器样式 */
  body {
    background: white !important;
    color: black !important;
    font-size: 12pt;
  }

  /* 表格样式优化 */
  table {
    width: 100% !important;
    border-collapse: collapse !important;
    page-break-inside: auto;
  }

  thead {
    display: table-header-group;
  }

  tr {
    page-break-inside: avoid;
    page-break-after: auto;
  }

  th,
  td {
    border: 1px solid #000 !important;
    padding: 8px !important;
    text-align: left !important;
  }

  th {
    background-color: #f0f0f0 !important;
    font-weight: bold !important;
  }

  /* 标题样式 */
  h1,
  h2,
  h3 {
    color: black !important;
    page-break-after: avoid;
  }

  /* 卡片样式 */
  .card,
  [class*="card"] {
    border: 1px solid #ddd !important;
    box-shadow: none !important;
    page-break-inside: avoid;
  }

  /* Badge样式 */
  .badge,
  [class*="badge"] {
    border: 1px solid #000 !important;
    padding: 2px 6px !important;
  }

  /* 移除阴影和圆角 */
  * {
    box-shadow: none !important;
    text-shadow: none !important;
  }

  /* 确保内容可见 */
  .overflow-x-auto {
    overflow: visible !important;
  }

  /* 优化间距 */
  .space-y-4 > * + * {
    margin-top: 1rem !important;
  }
}
```

---

## 五、使用方法

### 5.1 打印步骤
1. 进入出库管理、入库管理或库存查询页面
2. 点击页面右上角的"打印表单"按钮
3. 浏览器会弹出打印预览对话框
4. 选择打印机或保存为PDF
5. 调整打印设置(如页面方向、边距等)
6. 点击"打印"按钮完成打印

### 5.2 打印预览
在打印对话框中可以预览打印效果:
- 表格会自动适应页面宽度
- 操作按钮和搜索框不会显示
- 侧边栏和导航栏不会显示
- 表格有清晰的边框和表头
- 如果数据较多,会自动分页

### 5.3 保存为PDF
1. 在打印对话框中选择"保存为PDF"
2. 选择保存位置
3. 点击"保存"按钮
4. 生成的PDF文件可以分享或存档

---

## 六、打印效果

### 6.1 页面布局
- **纸张大小**: A4
- **页面方向**: 横向(Landscape)
- **边距**: 1厘米
- **字体大小**: 12pt

### 6.2 表格样式
- **边框**: 黑色实线边框
- **表头**: 灰色背景,粗体文字
- **单元格**: 8px内边距
- **对齐方式**: 左对齐

### 6.3 颜色处理
- **背景**: 纯白色
- **文字**: 纯黑色
- **表头背景**: 浅灰色(#f0f0f0)
- **边框**: 黑色(#000)

### 6.4 分页处理
- 表头在每页都会显示
- 表格行不会在中间断开
- 标题不会单独在页面底部

---

## 七、浏览器兼容性

### 7.1 支持的浏览器
- ✅ Chrome 90+
- ✅ Firefox 88+
- ✅ Safari 14+
- ✅ Edge 90+

### 7.2 打印功能支持
所有现代浏览器都支持:
- `window.print()` API
- `@media print` 媒体查询
- `@page` 规则
- 分页控制

### 7.3 已知限制
- **IE浏览器**: 不支持部分CSS打印样式
- **移动浏览器**: 打印功能可能受限
- **PDF生成**: 依赖浏览器的PDF生成能力

---

## 八、优化建议

### 8.1 打印前准备
1. **筛选数据**: 使用搜索功能筛选需要打印的数据
2. **检查数据**: 确保数据完整和准确
3. **预览效果**: 使用打印预览检查效果
4. **调整设置**: 根据需要调整页面方向和边距

### 8.2 打印质量优化
1. **选择合适的纸张大小**: A4横向适合表格打印
2. **调整缩放比例**: 如果表格过宽,可以调整缩放比例
3. **使用彩色打印**: 如果需要保留状态颜色
4. **选择高质量**: 选择高质量打印模式

### 8.3 性能优化
1. **限制打印数据量**: 如果数据过多,建议分批打印
2. **使用导出功能**: 对于大量数据,建议使用Excel导出
3. **关闭背景图形**: 可以加快打印速度

---

## 九、常见问题

### 9.1 打印预览空白
**问题**: 点击打印按钮后,预览窗口显示空白页面。

**解决方案**:
1. 检查浏览器是否支持打印功能
2. 检查是否有浏览器扩展阻止打印
3. 尝试刷新页面后重新打印
4. 使用其他浏览器尝试

### 9.2 表格显示不完整
**问题**: 打印时表格列被截断或显示不完整。

**解决方案**:
1. 在打印设置中选择"横向"页面方向
2. 调整缩放比例(如80%或90%)
3. 调整页边距为"最小"
4. 使用"适应页面"选项

### 9.3 样式显示异常
**问题**: 打印预览中样式与屏幕显示不一致。

**解决方案**:
1. 确保浏览器版本是最新的
2. 检查是否启用了"背景图形"选项
3. 清除浏览器缓存后重试
4. 使用Chrome浏览器(打印支持最好)

### 9.4 分页位置不理想
**问题**: 表格在不合适的位置分页。

**解决方案**:
1. 调整页边距
2. 调整缩放比例
3. 使用"适应页面"选项
4. 手动调整数据量

### 9.5 无法保存为PDF
**问题**: 打印对话框中没有"保存为PDF"选项。

**解决方案**:
1. 使用Chrome浏览器(内置PDF生成)
2. 安装PDF打印机驱动
3. 使用第三方PDF生成工具
4. 使用导出Excel功能后转换为PDF

---

## 十、未来改进方向

### 10.1 功能增强
1. **自定义打印模板**: 允许用户自定义打印格式
2. **打印设置保存**: 记住用户的打印偏好
3. **批量打印**: 支持选择多个单据批量打印
4. **打印预览**: 在页面内显示打印预览
5. **水印功能**: 添加公司水印或"副本"标记

### 10.2 样式优化
1. **公司Logo**: 在打印页面添加公司Logo
2. **页眉页脚**: 添加自定义页眉页脚
3. **页码**: 显示"第X页/共Y页"
4. **打印日期**: 显示打印时间
5. **签名栏**: 添加签名和日期栏

### 10.3 数据优化
1. **选择性打印**: 允许选择要打印的列
2. **数据汇总**: 在打印页面添加汇总信息
3. **分组打印**: 按类别或日期分组打印
4. **筛选条件显示**: 显示当前的筛选条件
5. **统计信息**: 显示总数、合计等统计信息

### 10.4 用户体验
1. **打印提示**: 打印前显示提示信息
2. **打印历史**: 记录打印历史
3. **快捷键**: 支持Ctrl+P快捷键
4. **打印进度**: 显示打印进度
5. **打印成功提示**: 打印完成后显示提示

---

## 十一、测试建议

### 11.1 功能测试
- [ ] 测试出库管理打印功能
- [ ] 测试入库管理打印功能
- [ ] 测试库存查询打印功能
- [ ] 测试打印按钮点击响应
- [ ] 测试打印预览显示

### 11.2 样式测试
- [ ] 测试表格边框显示
- [ ] 测试表头样式
- [ ] 测试操作列隐藏
- [ ] 测试搜索框隐藏
- [ ] 测试侧边栏隐藏

### 11.3 分页测试
- [ ] 测试单页数据打印
- [ ] 测试多页数据打印
- [ ] 测试表头在每页显示
- [ ] 测试行不被截断
- [ ] 测试分页位置合理

### 11.4 兼容性测试
- [ ] 在Chrome浏览器测试
- [ ] 在Firefox浏览器测试
- [ ] 在Safari浏览器测试
- [ ] 在Edge浏览器测试
- [ ] 测试保存为PDF功能

### 11.5 数据量测试
- [ ] 测试空数据打印
- [ ] 测试少量数据打印(1-10条)
- [ ] 测试中等数据打印(10-50条)
- [ ] 测试大量数据打印(50+条)
- [ ] 测试超长文本显示

---

## 十二、总结

### 12.1 实现内容
- ✅ 为三个核心页面添加打印按钮
- ✅ 实现一键打印功能
- ✅ 优化打印样式和布局
- ✅ 隐藏不需要打印的元素
- ✅ 支持自动分页
- ✅ 保持代码质量(通过lint检查)

### 12.2 技术特点
- **简单易用**: 一键打印,无需额外配置
- **样式优化**: 专门的打印样式,效果美观
- **兼容性好**: 支持所有现代浏览器
- **性能良好**: 使用原生API,性能优秀
- **可维护性**: 代码结构清晰,易于维护

### 12.3 用户价值
- **提高效率**: 快速打印表格数据
- **便于存档**: 可以保存为PDF存档
- **便于分享**: 打印后可以分享给他人
- **专业美观**: 打印效果专业美观
- **灵活方便**: 支持各种打印设置

### 12.4 维护建议
1. 定期测试打印功能
2. 收集用户反馈
3. 根据需求优化打印样式
4. 关注浏览器更新
5. 考虑添加更多打印选项

---

**文档版本**: v1.0  
**最后更新**: 2026年1月19日  
**维护人员**: 系统管理员
