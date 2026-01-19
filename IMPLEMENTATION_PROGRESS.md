# 新功能实施进度

## 已完成部分

### 1. 数据库结构 ✅
- ✅ 创建版本管理表(versions)
- ✅ 创建销售合同表(sales_contracts)
- ✅ 创建发票管理表(invoices)
- ✅ 出库单表添加version_id字段
- ✅ 序列号表添加product_name_text和product_code_text字段
- ✅ 创建Storage buckets(contracts, invoices)
- ✅ 配置RLS策略和Storage策略

### 2. 类型定义 ✅
- ✅ 更新SerialNumber接口(添加文本字段)
- ✅ 创建Version接口
- ✅ 创建SalesContract接口
- ✅ 创建Invoice接口
- ✅ 更新OutboundOrder接口(添加version_id和versions关联)

### 3. Excel导入导出服务 ✅
- ✅ 创建excelService.ts
- ✅ 实现exportToExcel通用导出函数
- ✅ 实现importFromExcel通用导入函数
- ✅ 实现客户导入模板生成
- ✅ 实现入库单导入模板生成
- ✅ 实现出库单导入模板生成
- ✅ 实现客户数据导出
- ✅ 实现入库单数据导出
- ✅ 实现出库单数据导出

## 待实现部分

### 1. 数据库API扩展 ⏳
需要在src/db/api.ts中添加:
- [ ] 版本管理CRUD API
- [ ] 销售合同CRUD API
- [ ] 发票管理CRUD API
- [ ] 文件上传API(合同、发票)

### 2. 页面组件 ⏳
需要创建以下页面:
- [ ] VersionsPage - 版本管理页面
- [ ] SalesContractsPage - 销售合同管理页面
- [ ] InvoicesPage - 发票管理页面

需要更新以下页面:
- [ ] CustomersPage - 添加导入/导出按钮
- [ ] InboundPage - 添加导入/导出按钮
- [ ] OutboundPage - 添加导入/导出按钮,显示版本号列
- [ ] SerialNumbersPage - 显示产品名称和编号文本字段

### 3. OCR识别功能 ⏳
- [ ] 创建OCR服务(ocrService.ts)
- [ ] 集成百度OCR或其他OCR服务
- [ ] 实现合同信息识别
- [ ] 实现发票信息识别

### 4. 路由和导航 ⏳
- [ ] 添加版本管理路由
- [ ] 添加销售合同路由
- [ ] 添加发票管理路由
- [ ] 更新侧边栏导航菜单

### 5. 表单组件 ⏳
- [ ] 创建版本管理表单
- [ ] 创建合同上传表单
- [ ] 创建发票上传表单
- [ ] 更新出库表单(添加版本选择)

## 实施建议

由于功能较多且复杂,建议分阶段实施:

### 阶段1: Excel导入导出(优先级高)
1. 更新CustomersPage添加导入/导出功能
2. 更新InboundPage添加导入/导出功能
3. 更新OutboundPage添加导入/导出功能
4. 测试导入导出功能

### 阶段2: 版本管理(优先级高)
1. 创建版本管理API
2. 创建VersionsPage页面
3. 更新OutboundFormPage添加版本选择
4. 更新OutboundPage显示版本号
5. 测试版本关联功能

### 阶段3: 序列号增强(优先级中)
1. 更新SerialNumbersPage显示文本字段
2. 更新序列号表单添加文本字段输入
3. 测试序列号功能

### 阶段4: 合同管理(优先级中)
1. 创建合同管理API
2. 创建SalesContractsPage页面
3. 实现文件上传功能
4. 集成OCR识别(可选)
5. 测试合同管理功能

### 阶段5: 发票管理(优先级中)
1. 创建发票管理API
2. 创建InvoicesPage页面
3. 实现文件上传功能
4. 集成OCR识别(可选)
5. 测试发票管理功能

## OCR服务说明

OCR识别需要第三方服务支持,推荐方案:

### 方案1: 百度OCR(推荐)
- 优点: 中文识别准确,支持合同、发票等多种文档
- 需要: 百度智能云账号和API密钥
- 文档: https://cloud.baidu.com/doc/OCR/index.html

### 方案2: 阿里云OCR
- 优点: 识别准确,服务稳定
- 需要: 阿里云账号和API密钥
- 文档: https://help.aliyun.com/product/30413.html

### 方案3: 腾讯云OCR
- 优点: 价格实惠,识别效果好
- 需要: 腾讯云账号和API密钥
- 文档: https://cloud.tencent.com/product/ocr

### 实施步骤:
1. 注册OCR服务并获取API密钥
2. 在系统设置中添加OCR配置
3. 创建ocrService.ts封装OCR调用
4. 在合同/发票上传后调用OCR识别
5. 将识别结果填充到表单

## 当前状态

- 数据库结构: 100% 完成
- 类型定义: 100% 完成
- Excel服务: 100% 完成
- 整体进度: 约30% 完成

## 下一步行动

建议优先实现Excel导入导出功能,因为:
1. 不依赖第三方服务
2. 用户需求明确
3. 实施难度较低
4. 可以快速交付价值

然后再逐步实现版本管理、合同管理、发票管理等功能。
