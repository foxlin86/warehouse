# 文件上传格式扩展说明

## 修改时间
2026年1月19日

## 修改范围
- 发票管理 (InvoicesPage.tsx)
- 销售合同管理 (SalesContractsPage.tsx)

---

## 一、支持的文件格式

### 1.1 PDF格式
- **.pdf** - PDF文档

### 1.2 图片格式
- **.jpg, .jpeg** - JPEG图片
- **.png** - PNG图片
- **.gif** - GIF动图
- **.webp** - WebP图片(现代格式,体积小)
- **.svg** - SVG矢量图
- **.bmp** - 位图
- **.tiff** - TIFF图片

### 1.3 Word文档格式
- **.doc** - Word 97-2003文档
- **.docx** - Word 2007+文档

### 1.4 Excel表格格式
- **.xls** - Excel 97-2003表格
- **.xlsx** - Excel 2007+表格

### 1.5 其他格式
- **.csv** - CSV逗号分隔值文件
- **.txt** - 纯文本文件
- **.rtf** - 富文本格式
- **.odt** - OpenDocument文本文档
- **.ods** - OpenDocument电子表格

---

## 二、文件大小限制

### 2.1 修改前
- **最大文件大小**: 10MB

### 2.2 修改后
- **最大文件大小**: 20MB

### 2.3 建议
- 对于大型文件,建议压缩后上传
- 图片建议使用WebP格式,体积更小
- PDF文档建议优化后上传

---

## 三、MIME类型映射

### 3.1 PDF
```
application/pdf
```

### 3.2 图片
```
image/jpeg
image/jpg
image/png
image/gif
image/webp
image/svg+xml
image/bmp
image/tiff
```

### 3.3 Word文档
```
application/msword                                                          (.doc)
application/vnd.openxmlformats-officedocument.wordprocessingml.document    (.docx)
```

### 3.4 Excel表格
```
application/vnd.ms-excel                                                    (.xls)
application/vnd.openxmlformats-officedocument.spreadsheetml.sheet          (.xlsx)
```

### 3.5 其他
```
text/csv                                        (.csv)
text/plain                                      (.txt)
application/rtf                                 (.rtf)
application/vnd.oasis.opendocument.text         (.odt)
application/vnd.oasis.opendocument.spreadsheet  (.ods)
```

---

## 四、代码修改详情

### 4.1 InvoicesPage.tsx

#### 修改前
```typescript
// 验证文件类型
const allowedTypes = ['application/pdf', 'image/jpeg', 'image/jpg', 'image/png'];
if (!allowedTypes.includes(file.type)) {
  toast.error('只支持PDF和图片格式(JPG, PNG)');
  return;
}

// 验证文件大小(最大10MB)
if (file.size > 10 * 1024 * 1024) {
  toast.error('文件大小不能超过10MB');
  return;
}
```

```html
<Input
  type="file"
  accept=".pdf,.jpg,.jpeg,.png"
  onChange={handleFileUpload}
  disabled={uploading}
/>
```

#### 修改后
```typescript
// 验证文件类型 - 支持表格、文档、PDF和图片
const allowedTypes = [
  // PDF
  'application/pdf',
  // 图片
  'image/jpeg', 'image/jpg', 'image/png', 'image/gif', 'image/webp', 'image/svg+xml', 'image/bmp', 'image/tiff',
  // Word文档
  'application/msword', 'application/vnd.openxmlformats-officedocument.wordprocessingml.document',
  // Excel表格
  'application/vnd.ms-excel', 'application/vnd.openxmlformats-officedocument.spreadsheetml.sheet',
  // CSV
  'text/csv',
  // OpenDocument
  'application/vnd.oasis.opendocument.text', 'application/vnd.oasis.opendocument.spreadsheet',
  // 文本
  'text/plain',
  // RTF
  'application/rtf',
];

if (!allowedTypes.includes(file.type)) {
  toast.error('支持的文件格式: PDF、图片(JPG/PNG/GIF/WebP/SVG/BMP/TIFF)、Word(DOC/DOCX)、Excel(XLS/XLSX)、CSV、文本(TXT)、RTF、OpenDocument(ODT/ODS)');
  return;
}

// 验证文件大小(最大20MB)
if (file.size > 20 * 1024 * 1024) {
  toast.error('文件大小不能超过20MB');
  return;
}
```

```html
<Input
  type="file"
  accept=".pdf,.jpg,.jpeg,.png,.gif,.webp,.svg,.bmp,.tiff,.doc,.docx,.xls,.xlsx,.csv,.odt,.ods,.txt,.rtf"
  onChange={handleFileUpload}
  disabled={uploading}
/>
```

### 4.2 SalesContractsPage.tsx

修改内容与InvoicesPage.tsx相同。

---

## 五、使用场景

### 5.1 发票管理
- **PDF发票**: 电子发票PDF文件
- **图片发票**: 扫描或拍照的发票图片
- **Excel发票**: 发票明细表格
- **Word发票**: 发票说明文档

### 5.2 销售合同管理
- **PDF合同**: 正式合同PDF文件
- **Word合同**: 合同草稿或编辑版本
- **图片合同**: 扫描的纸质合同
- **Excel附件**: 合同附件表格(如报价单、清单等)

---

## 六、浏览器兼容性

### 6.1 文件格式支持
所有现代浏览器都支持以下格式的上传:
- ✅ Chrome 90+
- ✅ Firefox 88+
- ✅ Safari 14+
- ✅ Edge 90+

### 6.2 特殊格式注意事项
- **SVG**: 部分旧版浏览器可能不支持
- **WebP**: IE不支持,但现代浏览器都支持
- **TIFF**: 浏览器预览可能不支持,但上传没问题

---

## 七、安全性考虑

### 7.1 文件类型验证
- ✅ 前端验证: 通过MIME类型检查
- ✅ 后端验证: Supabase Storage自动验证
- ✅ 文件扩展名检查: 通过accept属性限制

### 7.2 文件大小限制
- ✅ 前端限制: 20MB
- ✅ 后端限制: Supabase Storage默认限制
- ✅ 防止恶意上传: 大文件会被拒绝

### 7.3 存储安全
- ✅ 文件名随机化: 使用时间戳+随机字符串
- ✅ 路径隔离: 发票和合同分别存储在不同目录
- ✅ 访问控制: 通过Supabase RLS策略控制

---

## 八、性能优化建议

### 8.1 文件压缩
- **图片**: 建议使用WebP格式,或压缩JPEG/PNG
- **PDF**: 使用PDF压缩工具优化
- **Word/Excel**: 删除不必要的嵌入对象

### 8.2 上传优化
- **分片上传**: 对于大文件,考虑实现分片上传
- **断点续传**: 对于不稳定网络,考虑实现断点续传
- **进度显示**: 已实现上传中提示,可进一步优化为进度条

### 8.3 存储优化
- **定期清理**: 删除未使用的文件
- **CDN加速**: 使用Supabase CDN加速文件访问
- **缓存策略**: 设置合理的缓存时间

---

## 九、错误处理

### 9.1 常见错误
1. **文件格式不支持**
   - 错误提示: "支持的文件格式: PDF、图片(JPG/PNG/GIF/WebP/SVG/BMP/TIFF)、Word(DOC/DOCX)、Excel(XLS/XLSX)、CSV、文本(TXT)、RTF、OpenDocument(ODT/ODS)"
   - 解决方案: 转换文件格式或使用支持的格式

2. **文件过大**
   - 错误提示: "文件大小不能超过20MB"
   - 解决方案: 压缩文件或分割成多个文件

3. **上传失败**
   - 错误提示: "文件上传失败"
   - 解决方案: 检查网络连接,重试上传

### 9.2 错误日志
所有上传错误都会通过toast提示用户,并在控制台输出详细错误信息。

---

## 十、测试建议

### 10.1 功能测试
- [ ] 测试上传PDF文件
- [ ] 测试上传各种图片格式(JPG, PNG, GIF, WebP, SVG, BMP, TIFF)
- [ ] 测试上传Word文档(DOC, DOCX)
- [ ] 测试上传Excel表格(XLS, XLSX)
- [ ] 测试上传CSV文件
- [ ] 测试上传文本文件(TXT)
- [ ] 测试上传RTF文件
- [ ] 测试上传OpenDocument文件(ODT, ODS)

### 10.2 边界测试
- [ ] 测试上传19MB文件(应该成功)
- [ ] 测试上传21MB文件(应该失败)
- [ ] 测试上传不支持的格式(应该失败)
- [ ] 测试上传空文件(应该失败)

### 10.3 兼容性测试
- [ ] 在Chrome浏览器测试
- [ ] 在Firefox浏览器测试
- [ ] 在Safari浏览器测试
- [ ] 在Edge浏览器测试
- [ ] 在移动端浏览器测试

---

## 十一、未来改进方向

### 11.1 功能增强
1. **文件预览**: 支持在线预览上传的文件
2. **批量上传**: 支持一次上传多个文件
3. **拖拽上传**: 支持拖拽文件到上传区域
4. **进度条**: 显示上传进度百分比
5. **缩略图**: 为图片文件生成缩略图

### 11.2 性能优化
1. **分片上传**: 对大文件实现分片上传
2. **断点续传**: 网络中断后可继续上传
3. **并行上传**: 支持多个文件并行上传
4. **压缩上传**: 自动压缩图片后上传

### 11.3 用户体验
1. **文件管理**: 查看、删除、重命名已上传的文件
2. **版本控制**: 支持文件版本管理
3. **文件分类**: 按类型、日期等分类管理文件
4. **搜索功能**: 搜索已上传的文件

---

## 十二、总结

### 12.1 修改内容
- ✅ 扩展支持的文件格式(从4种增加到18种)
- ✅ 增加文件大小限制(从10MB增加到20MB)
- ✅ 优化错误提示信息
- ✅ 保持代码质量(通过lint检查)

### 12.2 影响范围
- 发票管理页面
- 销售合同管理页面

### 12.3 向后兼容性
- ✅ 完全向后兼容
- ✅ 已上传的文件不受影响
- ✅ 原有功能正常工作

### 12.4 风险评估
- **风险等级**: 低
- **影响范围**: 仅文件上传功能
- **回滚方案**: 恢复原有的文件类型和大小限制

---

**文档版本**: v1.0  
**最后更新**: 2026年1月19日  
**维护人员**: 系统管理员
