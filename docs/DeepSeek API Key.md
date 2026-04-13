好的，我为你整理了一份完整的文档，说明如何使用 DeepSeek API Key 来驱动 `claw` 工具。

---

# 使用 DeepSeek API Key 配置 Claw 的完整指南

## 一、原理说明

`claw` 工具原生设计用于调用 Anthropic（Claude）的 API，但 DeepSeek 官方已经**原生支持 Anthropic API 格式**。因此，我们只需要将 `claw` 的请求端点指向 DeepSeek 的兼容接口，就可以使用 DeepSeek 的模型来驱动 `claw`。

**核心优势：**
- ✅ 无需安装任何代理或中间件
- ✅ DeepSeek 价格远低于 Claude（约 1/20）
- ✅ 配置简单，只需修改环境变量

---

## 二、准备工作

### 1. 获取 DeepSeek API Key

访问 [DeepSeek 开放平台](https://platform.deepseek.com/)：

1. 注册/登录账号
2. 进入「API Keys」页面
3. 点击「创建 API Key」
4. 复制并保存生成的 Key（格式类似 `sk-xxxxxxxxxxxxxxxx`）

> ⚠️ **安全提示**：API Key 只会显示一次，请妥善保存，不要上传到公开代码仓库。

### 2. 确认已克隆 claw 代码

```bash
# 如果还没有克隆仓库
git clone https://github.com/ultraworkers/claw-code
cd claw-code/rust

# 构建项目（首次需要几分钟）
cargo build --workspace
```

---

## 三、配置环境变量

`claw` 调用 DeepSeek 需要设置三个关键环境变量：

| 环境变量 | 作用 | 值 |
|:---|:---|:---|
| `ANTHROPIC_BASE_URL` | 修改 API 请求地址 | `https://api.deepseek.com/anthropic` |
| `ANTHROPIC_AUTH_TOKEN` | DeepSeek 认证凭证 | 你的 DeepSeek API Key |
| `ANTHROPIC_MODEL` | 指定使用的模型 | `deepseek-chat` 或 `deepseek-reasoner` |

> 📌 **注意**：变量名是 `ANTHROPIC_AUTH_TOKEN` 而不是常见的 `ANTHROPIC_API_KEY`，这是 DeepSeek 官方兼容接口的特定要求。

---

## 四、按操作系统配置

### macOS / Linux

**临时配置（仅当前终端会话有效）：**
```bash
export ANTHROPIC_BASE_URL="https://api.deepseek.com/anthropic"
export ANTHROPIC_AUTH_TOKEN="你的DeepSeek-API-Key"
export ANTHROPIC_MODEL="deepseek-chat"
```

**永久配置（推荐）：**

将以下命令添加到你的 Shell 配置文件中：
- Bash 用户：`~/.bashrc` 或 `~/.bash_profile`
- Zsh 用户：`~/.zshrc`

```bash
echo '# Claw + DeepSeek 配置' >> ~/.zshrc
echo 'export ANTHROPIC_BASE_URL="https://api.deepseek.com/anthropic"' >> ~/.zshrc
echo 'export ANTHROPIC_AUTH_TOKEN="你的DeepSeek-API-Key"' >> ~/.zshrc
echo 'export ANTHROPIC_MODEL="deepseek-chat"' >> ~/.zshrc

# 重新加载配置
source ~/.zshrc
```

---

### Windows

**PowerShell（当前窗口有效）：**
```powershell
$env:ANTHROPIC_BASE_URL = "https://api.deepseek.com/anthropic"
$env:ANTHROPIC_AUTH_TOKEN = "你的DeepSeek-API-Key"
$env:ANTHROPIC_MODEL = "deepseek-chat"
```

**永久配置（推荐）：**

1. 打开「控制面板」→「系统」→「高级系统设置」
2. 点击「环境变量」
3. 在「用户变量」区域点击「新建」
4. 分别添加以下三个变量：

| 变量名 | 变量值 |
|:---|:---|
| `ANTHROPIC_BASE_URL` | `https://api.deepseek.com/anthropic` |
| `ANTHROPIC_AUTH_TOKEN` | `你的DeepSeek-API-Key` |
| `ANTHROPIC_MODEL` | `deepseek-chat` |

5. 点击「确定」保存
6. **重启 PowerShell** 使配置生效

---

## 五、验证配置

运行 `claw` 自带的健康检查命令：

```bash
# 进入 claw 目录
cd /path/to/claw-code/rust

# 运行 doctor 命令
./target/debug/claw doctor
```

**预期输出：**
- 如果配置正确，会显示 `✓ All systems ready`
- 如果出现错误，请检查：
  - API Key 是否正确
  - 环境变量名称是否拼写正确（特别是 `ANTHROPIC_AUTH_TOKEN`）
  - 网络是否能访问 `api.deepseek.com`

---

## 六、测试运行

配置验证通过后，可以运行一个简单的测试：

```bash
# 测试基础对话
./target/debug/claw prompt "你好，请简单介绍一下你自己"

# 测试代码生成
./target/debug/claw prompt "用 Python 写一个快速排序算法"
```

---

## 七、模型选择

DeepSeek 提供两种模型可选，通过 `ANTHROPIC_MODEL` 变量指定：

| 模型代码 | 特点 | 适用场景 |
|:---|:---|:---|
| `deepseek-chat` | 通用对话模型，速度快 | 日常任务、代码生成、问答 |
| `deepseek-reasoner` | 深度推理模型，擅长复杂逻辑 | 数学推理、复杂算法、多步规划 |

切换模型只需修改环境变量后重新运行 `claw` 即可。

---

## 八、常见问题

### Q1：运行 `claw doctor` 提示 API 连接失败？

**可能原因：**
- API Key 无效或已过期 → 登录 DeepSeek 平台重新生成
- 网络无法访问 DeepSeek API → 检查网络连接
- 环境变量名错误 → 确认使用的是 `ANTHROPIC_AUTH_TOKEN` 而非 `ANTHROPIC_API_KEY`

### Q2：`claw` 提示模型不支持？

确保 `ANTHROPIC_MODEL` 设置为 `deepseek-chat` 或 `deepseek-reasoner`，DeepSeek 目前只支持这两个模型。

### Q3：是否需要绑定信用卡？

DeepSeek 新用户通常有免费额度（约 5 元），可以直接使用。免费额度用完后需要充值，支持支付宝。

### Q4：Windows 上运行 `claw.exe` 提示找不到文件？

确保在正确的路径下执行：
```powershell
# 使用完整路径
.\target\debug\claw.exe doctor

# 或使用 cargo run
cargo run -- doctor
```

---

## 九、完整配置示例（一键脚本）

为了方便使用，以下是一键配置脚本：

**macOS/Linux (`setup_claw_deepseek.sh`)：**
```bash
#!/bin/bash
echo "请输入你的 DeepSeek API Key："
read -s DEEPSEEK_KEY

cat >> ~/.zshrc << EOF

# Claw + DeepSeek 配置
export ANTHROPIC_BASE_URL="https://api.deepseek.com/anthropic"
export ANTHROPIC_AUTH_TOKEN="$DEEPSEEK_KEY"
export ANTHROPIC_MODEL="deepseek-chat"
EOF

echo "配置完成！请执行 source ~/.zshrc 或重新打开终端"
```

**Windows PowerShell (`setup_claw_deepseek.ps1`)：**
```powershell
$key = Read-Host -Prompt "请输入你的 DeepSeek API Key" -AsSecureString
$plainKey = [System.Runtime.InteropServices.Marshal]::PtrToStringAuto([System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($key))

[Environment]::SetEnvironmentVariable("ANTHROPIC_BASE_URL", "https://api.deepseek.com/anthropic", "User")
[Environment]::SetEnvironmentVariable("ANTHROPIC_AUTH_TOKEN", $plainKey, "User")
[Environment]::SetEnvironmentVariable("ANTHROPIC_MODEL", "deepseek-chat", "User")

Write-Host "配置完成！请重启 PowerShell" -ForegroundColor Green
```

---

## 十、总结

通过以上配置，你可以用 DeepSeek 的高性价比 API 来运行 `claw` 工具，享受：

- 💰 **成本更低**：DeepSeek 价格约为 Claude 的 1/20
- 🇨🇳 **国内友好**：无需海外手机号/信用卡，支持支付宝
- 🚀 **功能完整**：代码生成、会话管理、工具调用等全部支持

配置完成后，尽情享受 `claw` + DeepSeek 的强大组合吧！🎉

---

如果遇到任何问题，可以查阅：
- [DeepSeek 官方文档](https://platform.deepseek.com/api-docs/)
- [claw-code 仓库](https://github.com/ultraworkers/claw-code)