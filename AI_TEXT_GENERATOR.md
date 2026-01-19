# AI文本生成功能说明

## 功能概述
在系统设置中添加了AI文本生成大模型功能,能够智能匹配主要字段、生成统计报告和编写各类文档内容。该功能利用AI大模型技术,自动分析系统数据并生成专业的文本内容。

## 主要功能

### 1. 库存统计报告
- **功能**: 自动分析当前库存数据,生成详细的库存统计报告
- **包含内容**:
  - 总体概况(产品种类、总库存量)
  - 库存分布(各产品详细信息)
  - 库存预警(低库存、零库存产品)
  - 优化建议
- **数据来源**: inventory_view表

### 2. 入库分析报告
- **功能**: 分析入库趋势,生成入库数据分析报告
- **包含内容**:
  - 入库概况(入库单数量、审核状态)
  - 入库趋势(采购入库、退货入库等)
  - 主要供应商分析
  - 优化建议
- **数据来源**: inbound_orders表

### 3. 出库分析报告
- **功能**: 分析出库趋势,生成出库数据分析报告
- **包含内容**:
  - 出库概况(出库单数量)
  - 出库类型分布(销售、领用、报废)
  - 出库明细分析
  - 改进建议
- **数据来源**: outbound_orders表

### 4. 产品描述生成
- **功能**: 为产品生成专业的产品描述和规格说明
- **包含内容**:
  - 产品概述
  - 规格参数(型号、编号、单位、分类)
  - 产品特点(高精度、易操作、高可靠、多功能)
  - 适用场景
  - 售后服务
- **数据来源**: products表

### 5. 合同内容生成
- **功能**: 根据客户和产品信息生成标准销售合同模板
- **包含内容**:
  - 甲乙双方信息
  - 产品信息
  - 合同金额
  - 交货方式
  - 付款方式
  - 质量保证
  - 违约责任
  - 争议解决
  - 其他约定
- **数据来源**: sales_contracts表、customers表

### 6. 自定义生成
- **功能**: 输入自定义需求,AI根据描述生成相应内容
- **适用场景**: 任何需要文本生成的场景

## 技术实现

### 前端组件
- **文件**: `src/pages/AITextGeneratorPage.tsx`
- **技术栈**: React + TypeScript + shadcn/ui
- **主要功能**:
  - 生成类型选择
  - 提示词输入
  - 数据加载和统计
  - 文本生成和显示
  - 复制和下载功能

### 数据处理
1. **数据加载**: 根据生成类型自动加载相关数据
2. **数据统计**: 生成数据摘要,提供给AI作为上下文
3. **提示词构建**: 模板提示词 + 数据摘要
4. **文本生成**: 调用AI模型生成内容(当前为演示版本)

### 用户交互
- **左侧面板**: 生成配置
  - 生成类型选择
  - 提示词编辑
  - 数据加载状态
  - 生成按钮
- **右侧面板**: 生成结果
  - 文本显示
  - 复制按钮
  - 下载按钮

## 使用流程

1. **选择生成类型**
   - 在下拉菜单中选择需要生成的内容类型

2. **查看/编辑提示词**
   - 系统自动加载模板提示词
   - 可根据需要修改提示词

3. **等待数据加载**
   - 系统自动加载相关数据
   - 显示数据加载状态和统计信息

4. **点击生成**
   - 点击"生成内容"按钮
   - 等待AI生成结果

5. **查看和使用结果**
   - 在右侧面板查看生成的内容
   - 可以复制到剪贴板
   - 可以下载为文本文件

## 导航路径
- **路由**: `/ai-text-generator`
- **菜单位置**: 系统管理 > AI文本生成
- **图标**: Sparkles(星星图标)

## 扩展说明

### 集成真实AI模型
当前版本为演示版本,使用模拟数据生成。实际使用时需要:

1. **配置AI服务商**
   - 在系统设置中配置AI服务商API密钥
   - 支持的服务商: 百炼、OpenAI、Claude等

2. **创建Edge Function**
   - 创建Supabase Edge Function调用AI API
   - 处理提示词和返回结果
   - 实现流式输出(可选)

3. **更新前端代码**
   - 将模拟生成替换为真实API调用
   - 使用`supabase.functions.invoke`调用Edge Function
   - 处理加载状态和错误

### 示例Edge Function代码
```typescript
// supabase/functions/ai-generate/index.ts
import { serve } from 'https://deno.land/std@0.168.0/http/server.ts'
import { createClient } from 'https://esm.sh/@supabase/supabase-js@2'

serve(async (req) => {
  const { prompt, context } = await req.json()
  
  // 调用AI API
  const response = await fetch('https://api.openai.com/v1/chat/completions', {
    method: 'POST',
    headers: {
      'Authorization': `Bearer ${Deno.env.get('OPENAI_API_KEY')}`,
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({
      model: 'gpt-4',
      messages: [
        { role: 'system', content: '你是一个专业的文本生成助手' },
        { role: 'user', content: prompt + '\n\n' + context }
      ],
    }),
  })
  
  const data = await response.json()
  return new Response(JSON.stringify({ text: data.choices[0].message.content }))
})
```

## 注意事项

1. **数据隐私**: 生成内容时会使用系统数据,注意数据安全
2. **API成本**: 调用AI API会产生费用,建议设置使用限制
3. **内容审核**: AI生成的内容仅供参考,使用前请人工审核
4. **网络要求**: 需要稳定的网络连接访问AI服务

## 未来优化方向

1. **历史记录**: 保存生成历史,支持查看和重用
2. **模板管理**: 支持自定义模板,保存常用提示词
3. **批量生成**: 支持批量生成多个产品描述或报告
4. **导出格式**: 支持导出为PDF、Word等格式
5. **实时预览**: 支持流式输出,实时显示生成过程
6. **多语言**: 支持生成多语言内容
