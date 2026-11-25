# 燧瞳 FlintPupil（社区版）

Linux 服务器实时安全观测与攻击溯源系统。**只观察，不干预。**

FlintPupil 在你的服务器本机采集主机与 Web 访问数据，识别针对服务器的攻击行为，还原攻击者来源、手法与影响范围，并以大屏形式实时呈现。

> 社区版免费，单节点；专业版支持多节点、在线授权与集中管理。

## 功能

- **主机观测**：CPU、内存、磁盘、网络、进程、端口、服务、登录用户
- **Web 安全事件**：采集 Nginx / Apache 等访问日志，识别扫描、注入、上传、WebShell 等攻击
- **入侵行为溯源**：基于 auditd 的命令执行、进程链、文件变更、提权与持久化、日志篡改
- **攻击者画像**：来源 IP、归属地与 ASN、目标站点与端口、工具指纹、置信度与证据
- **实时大屏**：态势总览、事件检索、攻击者画像、节点健康，WebSocket 实时推送
- **告警通知**：钉钉、企业微信、飞书、通用 Webhook 等多通道
- **离线 GeoIP**：内置 IP 地理与 ASN 库，无需联网查询

## 系统要求

| 项目 | 要求 |
| --- | --- |
| 系统 | x86_64 Linux（CentOS 7+ / Ubuntu 20.04+ / Debian 11+ 等） |
| 运行时 | Docker Engine 24+，Docker Compose v2 |
| 权限 | root（安装与系统采集需要） |
| 资源 | 建议 2 核 / 4 GB 内存 / 10 GB 可用磁盘 |
| 端口 | 默认 80，可用 `--port` 修改 |

## 下载与安装

从 [Releases](../../releases) 下载社区版交付包，校验后安装：

```bash
# 1. 下载（以 v0.18.7 为例）
curl -LO https://github.com/YottaMeta/FlintPupil/releases/download/v0.18.7/FlintPupil-community-0.18.7-linux-amd64.tar.gz

# 2. 校验
sha256sum -c FlintPupil-community-0.18.7-linux-amd64.tar.gz.sha256

# 3. 解压并安装
tar xzf FlintPupil-community-0.18.7-linux-amd64.tar.gz
cd FlintPupil-community-0.18.7-linux-amd64
sudo ./install.sh
```

安装器参数：

```
sudo ./install.sh [选项]
  --dir PATH          安装目录（默认 /opt/flintpupil）
  --port N            HTTP 端口（默认 80）
  --project NAME      Compose 项目名（同机多套部署时区分）
  --public-url URL    对外访问地址（用于生成节点接入命令）
  --admin NAME        管理员用户名（默认 admin）
  --no-totp           不启用动态验证码（不推荐）
  --upgrade           覆盖程序文件并重启，保留数据与 .env
  --force             允许写入非空目录（危险，仅排障用）
```

安装完成后：

1. 浏览器打开安装器输出的访问地址，用 `/opt/flintpupil/admin.txt` 中的账号登录，并按其中的 `otpauth` 地址绑定动态验证码；
2. 进入「节点管理 → 接入新节点」生成接入命令，在被监控主机上以 root 执行；
3. 社区版最多接入 **1 个节点**。

完整步骤见 [安装手册](docs/安装手册-0.18.7.md)。

## 升级

用新版本交付包执行：

```bash
sudo ./install.sh --upgrade
```

数据与配置保留在安装目录，`.env` 不会被覆盖。

## 卸载

```bash
sudo ./uninstall.sh            # 停止容器，保留数据
sudo ./uninstall.sh --purge    # 二次确认后删除安装目录与全部数据
sudo ./uninstall.sh --images   # 同时删除本地镜像
```

## 数据与保留

- 全部数据保存在安装目录下的本地 PostgreSQL 容器卷中，不向外部上报；
- 默认事件保留 7 天，可在 `.env` 中通过 `FP_EVENT_RETENTION_DAYS` 调整；
- 安装包自带镜像与依赖，安装过程不拉取外网镜像。

## 安全说明

- FlintPupil 是**只观测、不干预**的安全产品：不拦截流量、不修改被监控站点、不阻断进程；
- 采集使用主机 auditd 与日志文件只读访问；
- 请勿将控制台直接暴露在公网，建议置于内网或通过 VPN / 反向代理加访问控制。

## 许可

社区版为专有软件，免费用于单节点部署。详见 [LICENSE](LICENSE)。源码不公开；如需多节点、OEM 或二次开发授权，请通过 Issues 联系。

## 镜像仓库

- GitHub：<https://github.com/YottaMeta/FlintPupil>
- Gitee：<https://gitee.com/yottameta/flint-pupil>

## 反馈

问题与建议请提交 [Issues](../../issues)。提交前请先搜索是否已有同类问题，并附上版本号与安装环境。
