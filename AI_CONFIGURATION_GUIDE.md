# AI智能分析配置指南

## 概述
本系统集成了AI智能分析功能,可以帮助您快速了解库存情况、分析出入库趋势、发现异常数据等。系统支持多种AI服务商,您可以根据需要选择合适的AI模型。

**架构说明**: AI分析在浏览器前端直接调用AI API,支持访问本地Ollama服务,数据不经过云端服务器,确保数据安全和隐私。

## 支持的AI服务商

### 1. 百度AI(文心一言) 🆕
- **服务商ID**: `baidu`
- **支持模型**:
  - `ernie-4.0-turbo-8k`: 文心4.0 Turbo,速度快,性价比高
  - `ernie-4.0-8k`: 文心4.0,最强性能
  - `ernie-3.5-8k`: 文心3.5,平衡性能
  - `ernie-speed-128k`: 文心极速版,超长上下文
  - `ernie-lite-8k`: 文心轻量版,快速响应
- **获取Access Token**:
  1. 访问百度智能云: https://cloud.baidu.com/
  2. 进入"千帆大模型平台": https://console.bce.baidu.com/qianfan/ais/console/applicationConsole/application
  3. 创建应用,获取API Key和Secret Key
  4. 使用API Key和Secret Key获取Access Token:
     ```bash
     curl -X POST 'https://aip.baidubce.com/oauth/2.0/token?grant_type=client_credentials&client_id=你的API_Key&client_secret=你的Secret_Key'
     ```
  5. 返回的access_token即为所需的API密钥
- **默认API地址**: `https://aip.baidubce.com/rpc/2.0/ai_custom/v1/wenxinworkshop/chat/completions?access_token=你的access_token`
- **注意事项**:
  - Access Token有效期为30天,过期需重新获取
  - 建议定期更新Access Token
  - 中文能力优秀,适合国内用户

### 2. 百炼大模型 (阿里云)
- **服务商ID**: `bailian`
- **支持模型**: 
  - `qwen-max`: 最强性能模型
  - `qwen-plus`: 平衡性能和成本
  - `qwen-turbo`: 快速响应模型
- **获取API密钥**:
  1. 访问阿里云百炼平台: https://bailian.console.aliyun.com/
  2. 创建应用并获取API Key
  3. 默认API地址: `https://dashscope.aliyuncs.com/api/v1/services/aigc/text-generation/generation`

### 3. DeepSeek
- **服务商ID**: `deepseek`
- **支持模型**:
  - `deepseek-chat`: 通用对话模型
  - `deepseek-coder`: 代码专用模型
- **获取API密钥**:
  1. 访问DeepSeek官网: https://www.deepseek.com/
  2. 注册账号并获取API Key
  3. 默认API地址: `https://api.deepseek.com/v1/chat/completions`
- **本地部署**: 如果您部署了本地DeepSeek服务,请在API地址中填写本地服务地址

### 4. OpenAI
- **服务商ID**: `openai`
- **支持模型**:
  - `gpt-4`: 最强性能
  - `gpt-3.5-turbo`: 快速响应
- **获取API密钥**:
  1. 访问OpenAI官网: https://platform.openai.com/
  2. 创建API Key
  3. 默认API地址: `https://api.openai.com/v1/chat/completions`

### 5. Ollama本地模型 ⭐推荐
- **服务商ID**: `ollama`
- **支持模型**:
  - `llama2`: Meta的Llama 2模型
  - `llama3`: Meta的Llama 3模型
  - `mistral`: Mistral AI的开源模型
  - `qwen`: 阿里通义千问开源模型
  - `qwen2`: 通义千问2代模型
  - `gemma`: Google的Gemma模型
  - `phi`: Microsoft的Phi模型
  - `custom`: 自定义模型名称
- **安装和配置**:
  1. 下载Ollama: https://ollama.ai/
  2. 安装Ollama到本地计算机
  3. 下载模型: `ollama pull qwen2` (或其他模型)
  4. 启动Ollama服务: `ollama serve` (通常会自动启动)
  5. 验证服务: 访问 http://localhost:11434
- **优势**:
  - ✅ 完全本地运行,数据不出本地
  - ✅ 无需API密钥,免费使用
  - ✅ 响应速度快,不受网络限制
  - ✅ 支持多种开源大模型
  - ✅ 隐私安全,适合企业内部使用
- **默认API地址**: `http://localhost:11434/api/chat`
- **注意事项**:
  - 需要先在本地安装Ollama并下载模型
  - 建议使用qwen2或llama3模型,中文支持较好
  - 首次运行需要下载模型文件(几GB大小)
  - 需要一定的计算资源(建议8GB以上内存)

### 6. 自定义API
- **服务商ID**: `custom`
- **支持模型**: `custom-model` (可自定义)
- **使用场景**: 适用于任何兼容OpenAI格式的API接口
- **配置要求**: 需要提供完整的API地址和密钥

## 配置步骤

### 1. 进入系统设置
- 使用管理员账号登录系统
- 点击左侧菜单 "系统管理" → "系统设置"
- 切换到 "AI配置" 标签页

### 2. 选择AI服务商
- 在 "AI服务商" 下拉框中选择您要使用的服务商
- 系统会自动显示该服务商支持的模型列表

### 3. 配置API密钥
- 在 "API密钥" 输入框中填写您的API Key
- 注意: API密钥会以密码形式显示,确保安全

### 4. 配置API地址(可选)
- 如果使用默认API地址,可以留空
- 如果使用自定义地址(如本地部署),请填写完整的API URL
- 例如: `http://localhost:8000/v1/chat/completions`

### 5. 保存配置
- 点击 "保存配置" 按钮
- 系统会提示保存成功

## 使用AI分析

### 1. 进入AI分析页面
- 点击左侧菜单 "查询报表" → "AI智能分析"

### 2. 提问方式

#### 方式一: 快速提问
- 点击右侧 "快速提问" 区域的预设问题
- 系统会自动填充问题并开始分析

#### 方式二: 自定义提问
- 在文本框中输入您的问题
- 点击 "开始分析" 按钮
- 等待AI分析完成

### 3. 查看分析结果
- AI会根据您的实际数据给出分析结果
- 结果会显示在 "分析结果" 卡片中
- 包含具体的数据统计和洞察建议

## 常见问题示例

### 库存统计类
- "本月入库了哪些产品?数量是多少?"
- "本月出库了哪些产品?数量是多少?"
- "当前库存最多的产品是什么?"
- "当前库存不足的产品有哪些?"

### 趋势分析类
- "本月入库和出库的总体情况如何?"
- "各个货架的库存分布情况如何?"
- "哪些产品的出库频率最高?"
- "哪些货架的库存周转率最高?"

### 异常检测类
- "是否有长期未出库的产品?"
- "是否有库存为0的产品?"
- "是否有异常的大量出库记录?"

## 注意事项

1. **首次使用**: 请确保已在系统设置中正确配置AI服务商和API密钥
2. **数据范围**: AI分析基于系统中已审核的入库单、出库单和实时库存数据
3. **响应时间**: 根据AI服务商和网络情况,分析可能需要几秒到几十秒
4. **API配额**: 注意您的AI服务商API调用配额,避免超限
5. **数据安全**: API密钥存储在数据库中,仅管理员可见和修改

## 故障排查

### 问题: "AI服务未配置或配置错误"
- 检查是否已在系统设置中配置AI服务商
- 检查API密钥是否正确
- 检查API地址是否可访问

### 问题: "AI分析失败"
- 检查网络连接是否正常
- 检查API密钥是否有效
- 检查API配额是否充足
- 查看浏览器控制台的详细错误信息

### 问题: 分析结果不准确
- 确保系统中的数据已正确录入
- 确保入库单、出库单已审核
- 尝试更换AI模型或服务商

## 技术支持

如有其他问题,请联系系统管理员或技术支持团队。

---

## 附录: Ollama快速上手指南

### Windows系统
1. 下载Ollama安装包: https://ollama.ai/download/windows
2. 运行安装程序,按提示完成安装
3. 打开命令提示符(CMD)或PowerShell
4. 下载推荐模型: `ollama pull qwen2`
5. 验证安装: `ollama list` (查看已下载的模型)

### macOS系统
1. 下载Ollama安装包: https://ollama.ai/download/mac
2. 打开.dmg文件,将Ollama拖到应用程序文件夹
3. 打开终端(Terminal)
4. 下载推荐模型: `ollama pull qwen2`
5. 验证安装: `ollama list`

### Linux系统
1. 运行安装命令: `curl -fsSL https://ollama.ai/install.sh | sh`
2. 下载推荐模型: `ollama pull qwen2`
3. 验证安装: `ollama list`

### 常用Ollama命令
- `ollama list`: 查看已下载的模型
- `ollama pull <模型名>`: 下载模型
- `ollama rm <模型名>`: 删除模型
- `ollama serve`: 启动Ollama服务(通常自动启动)
- `ollama run <模型名>`: 在命令行中测试模型

### 推荐模型选择
- **中文优先**: `qwen2` (7B参数,中文能力强,推荐)
- **英文优先**: `llama3` (8B参数,英文能力强)
- **轻量级**: `phi` (3B参数,速度快,适合低配置)
- **代码分析**: `qwen2` 或 `llama3` 均可

### 系统配置示例
配置Ollama后,在系统设置中:
1. AI服务商: 选择 "Ollama本地模型"
2. 模型: 选择 "qwen2" (或您已下载的模型)
3. API密钥: 留空(无需填写)
4. API地址: 留空(使用默认地址 http://localhost:11434/api/chat)
5. 点击"保存配置"

配置完成后,即可在AI智能分析页面使用本地模型进行分析!
