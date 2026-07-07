# macOS Git 多环境代理分流配置

> **环境**：macOS / Git
> **最后更新**：2026-03-30

## 概述

针对 macOS 环境下 **GitHub 走代理、公司 GitLab 直连** 的标准配置指南，涵盖 HTTP/HTTPS 域名分流、SSH 配置、Clash 规则及系统级绕过。

---

## 1. 清理全局 Git 代理（关键）
为避免全局代理设置覆盖特定域名规则，首先需清除现有的全局代理配置。

```bash
# 清除全局 HTTP/HTTPS 代理
git config --global --unset http.proxy
git config --global --unset https.proxy
```

## 2. 配置域名特定代理 (HTTP/HTTPS)
针对不同域名应用不同的代理策略。假设 Clash 代理端口为 `7890`，公司 GitLab 域名为 `gitlab.yourcompany.com`。

```bash
# GitHub：强制使用代理
git config --global http.https://github.com.proxy http://127.0.0.1:7890

# GitLab：强制禁用代理（置空）
git config --global http.http://gitlab.yourcompany.com.proxy ""
git config --global http.https://gitlab.yourcompany.com.proxy ""
```

## 3. 配置 SSH 访问分流 (SSH)
若使用 SSH 协议（`git@...`），需修改 `~/.ssh/config` 文件：

```bash
# 编辑配置文件
vim ~/.ssh/config

# --- 内容如下 ---
# 针对 GitHub 走代理
Host github.com
    HostName github.com
    User git
    ProxyCommand nc -X 5 -x 127.0.0.1:7890 %h %p

# 针对公司 GitLab 直连
Host gitlab.yourcompany.com
    HostName gitlab.yourcompany.com
    User git
    ProxyCommand none
```

## 4. Clash 与系统层绕过
确保流量在进入 Clash 或系统代理逻辑前被拦截：

* **Clash 规则**：在 `rules` 顶部添加：
    ```yaml
    - DOMAIN-SUFFIX,yourcompany.com,DIRECT
    ```
* **macOS 系统设置**：
    1.  进入 **系统设置** -> **网络** -> **详细信息** -> **代理**。
    2.  在 **忽略这些主机与域的代理设置** 中添加：`*.yourcompany.com`。

## 5. 验证配置
执行以下命令检查 Git 配置项：

```bash
git config --global -l | grep proxy
```

**预期结果：**
* **不应出现** `http.proxy=...` 或 `https.proxy=...` 的全局行。
* **应出现** 针对 `github.com` 的代理行。
* **应出现** 针对 `gitlab.yourcompany.com` 且值为空的行。

---

**注意**：如配置后仍未生效，请检查 Shell 配置文件（如 `~/.zshrc`）中是否包含 `export http_proxy=...` 等环境变量，如有请将其删除或注释。