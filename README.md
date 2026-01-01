# Agam Space - LazyCat 应用

基于您的 docker-compose.yml 配置，我已为您生成完整的懒猫应用配置。

## 📊 智能优化结果

### 服务分析
| 服务 | 类型 | 配置方式 |
|------|------|---------|
| postgres | 内部服务 | ✅ 自动配置密码 |
| agam | 外部服务 | ⚠️ 用户配置 |

### 参数优化
- **原始需要**: 2 个参数 (DB_PASSWORD, ALLOW_NEW_SIGNUP)
- **优化后**: 1 个参数 (ALLOW_NEW_SIGNUP)
- **自动生成**: DB_PASSWORD 通过 `{{.INTERNAL.db_password}}` 自动管理

**配置减少: 50%** 🎉

## 📁 生成的文件

```
agam-space-lzcapp/
├── lzc-manifest.yml          # 应用主配置 (v1.4.1+ 格式)
├── lzc-deploy-params.yml     # 设置向导 (仅 1 个参数)
├── lzc-build.yml             # 构建配置
├── build.sh                  # 自动化发布脚本
├── icon.png                  # 应用图标 (需要您提供)
└── README.md                 # 本文件
```

## 🚀 快速开始

### 前置要求
- ✅ LazyCat CLI 已安装
- ✅ 已登录懒猫应用商店 (`lzc-cli appstore login`)

### 🔐 OIDC SSO 集成说明 (懒猫 v1.3.5+)

**懒猫 v1.3.5+ 提供统一 OIDC 支持，配置极其简单！**

**核心配置 (仅需 2 步)：**

1. **设置回调路径** (在 manifest 中已配置):
   ```yaml
   application:
     oidc_redirect_path: /api/v1/auth/sso/oidc/callback
   ```

   **⚠️ 关键规则**:
   - ✅ **必须设置此字段，OIDC 环境变量才会注入**
   - ❌ **未设置则不会生成任何 OIDC 变量**
   - ⚠️ 如果不确定正确值，可以先填写常见路径，通过浏览器报错确认

2. **懒猫自动生成环境变量**:

   **前提**: `oidc_redirect_path` 已设置，系统自动注入以下变量：
   | 懒猫生成 | 应用接收 | 说明 |
   |---------|---------|------|
   | `LAZYCAT_AUTH_OIDC_CLIENT_ID` | `OIDC_CLIENT_ID` | ✅ 客户端 ID |
   | `LAZYCAT_AUTH_OIDC_CLIENT_SECRET` | `OIDC_CLIENT_SECRET` | ✅ 客户端密钥 |
   | `LAZYCAT_AUTH_OIDC_ISSUER_URI` | `OIDC_ISSUER_URI` | ✅ OIDC 发行者 URI |
   | `LAZYCAT_AUTH_OIDC_AUTH_URI` | `OIDC_AUTH_URI` | ✅ 授权端点 |
   | `LAZYCAT_AUTH_OIDC_TOKEN_URI` | `OIDC_TOKEN_URI` | ✅ Token 端点 |
   | `LAZYCAT_AUTH_OIDC_USERINFO_URI` | `OIDC_USERINFO_URI` | ✅ 用户信息端点

**优势：**
- ✅ **零配置**: 只需设置回调路径，其余自动完成
- ✅ **完整端点**: 自动获取所有 OIDC 端点信息
- ✅ **安全**: 密钥由懒猫统一管理，永不暴露
- ✅ **标准**: 完全符合懒猫 v1.3.5+ 规范

**使用流程：**
1. 在懒猫平台配置 OIDC 提供商 (Authelia/Authentik/Keycloak)
2. 启用 Agam Space 的 SSO 功能
3. 懒猫自动生成所有 OIDC 参数
4. 应用自动接收并使用

### 步骤 1: 准备应用图标

创建一个 512x512 像素的 PNG 图标文件：

```bash
# 示例: 使用 ImageMagick 创建占位图标
convert -size 512x512 xc:blue -pointsize 72 -fill white -gravity center -annotate +0+0 "AGAM" icon.png

# 或者准备您自己的图标
# 保存为: icon.png
```

### 步骤 2: 使用自动化脚本

```bash
# 查看应用信息
./build.sh
# 选择 5 - 查看应用信息

# 一键发布 (推荐)
./build.sh
# 选择 4 - 一键构建+镜像复制+发布
```

### 步骤 3: 手动操作

#### 选项 1: 仅构建
```bash
./build.sh
# 选择 1 - 构建应用包
# 输出: agam-space-1.0.0.lpk
```

#### 选项 2: 仅复制镜像
```bash
./build.sh
# 选择 2 - 镜像复制到懒猫仓库
# 自动更新 manifest 并重新构建
```

#### 选项 3: 仅发布
```bash
./build.sh
# 选择 3 - 发布到应用商店
# 需要先构建完成
```

## 📋 配置说明

### lzc-manifest.yml

**关键特性:**
- ✅ 使用 v1.4.1+ 格式 (无 `lzc-sdk-version`)
- ✅ `min_os_version: 1.3.8` (现代应用要求)
- ✅ `healthcheck` 字段 (Docker Compose 兼容)
- ✅ `upstreams` 代替 `routes` (推荐)
- ✅ 自动配置数据库密码

**服务配置:**
```yaml
services:
  postgres:
    # 密码自动生成，无需用户配置
    environment:
      - POSTGRES_PASSWORD={{.INTERNAL.db_password}}

  agam:
    # 用户配置的参数
    environment:
      - ALLOW_NEW_SIGNUP={{.U.allow_new_signup}}
```

### lzc-deploy-params.yml

**设置向导仅需 1 个参数:**

```yaml
params:
  - id: allow_new_signup
    type: bool
    name: "allow new signup"
    description: "Allow new user registration"
    default_value: true
    optional: true
```

**中文界面:**
- 名称: "允许新用户注册"
- 描述: "是否允许新用户注册"

### lzc-build.yml

```yaml
manifest: ./lzc-manifest.yml
pkgout: ./
icon: ./icon.png
```

## 🎯 发布流程

### 完整流程 (使用 build.sh 选项 4)

```
阶段 1: 初始构建
  ↓ 使用原始镜像 agamspace/agam-space:latest
  ↓ 输出: agam-space-1.0.0.lpk

阶段 2: 镜像复制
  ↓ 执行: lzc-cli appstore copy-image
  ↓ 自动更新 manifest
  ↓ 注释原镜像，使用新镜像

阶段 3: 重新构建
  ↓ 使用新镜像 registry.lazycat.cloud/...
  ↓ 输出: agam-space-1.0.1.lpk

阶段 4: 发布审核
  ↓ 执行: lzc-cli appstore publish
  ↓ 等待 1-3 个工作日审核
```

### 手动流程

```bash
# 1. 构建
lzc-cli project build -o agam-space-1.0.0.lpk

# 2. 复制镜像
lzc-cli appstore copy-image agamspace/agam-space:latest

# 3. 更新 manifest (手动)
# 编辑 lzc-manifest.yml，替换 image 字段

# 4. 重新构建
lzc-cli project build -o agam-space-1.0.1.lpk

# 5. 发布
lzc-cli appstore publish agam-space-1.0.1.lpk
```

## 🔧 配置参数

### 用户配置 (部署时)

| 参数 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| allow_new_signup | bool | true | 是否允许新用户注册 |

### 自动配置 (无需用户输入)

| 参数 | 来源 | 说明 |
|------|------|------|
| db_password | {{.INTERNAL.db_password}} | 数据库密码，自动生成并管理 |

### 环境变量 (应用内)

| 变量 | 值 | 说明 |
|------|---|------|
| DATABASE_HOST | postgres | 数据库主机 |
| DATABASE_PORT | 5432 | 数据库端口 |
| DATABASE_USERNAME | postgres | 数据库用户名 |
| DATABASE_PASSWORD | {{.INTERNAL.db_password}} | 数据库密码 |
| DATABASE_NAME | agam_space | 数据库名 |
| HTTP_PORT | 3331 | HTTP 端口 |
| ALLOW_NEW_SIGNUP | {{.U.allow_new_signup}} | 用户配置 |
| CORS_ORIGIN | * | CORS 配置 |
| OIDC_CLIENT_ID | ${LAZYCAT_AUTH_OIDC_CLIENT_ID} | ✅ OAuth 客户端 ID |
| OIDC_CLIENT_SECRET | ${LAZYCAT_AUTH_OIDC_CLIENT_SECRET} | ✅ OAuth 密钥 (每次重启会变动) |
| OIDC_ISSUER_URI | ${LAZYCAT_AUTH_OIDC_ISSUER_URI} | ✅ OAuth issuer 地址 |
| OIDC_AUTH_URI | ${LAZYCAT_AUTH_OIDC_AUTH_URI} | ✅ AUTH endpoint 地址 |
| OIDC_TOKEN_URI | ${LAZYCAT_AUTH_OIDC_TOKEN_URI} | ✅ Token endpoint 地址 |
| OIDC_USERINFO_URI | ${LAZYCAT_AUTH_OIDC_USERINFO_URI} | ✅ userinfo endpoint 地址

**OIDC 说明**:
- 🔐 **密钥安全**: `OIDC_CLIENT_SECRET` 每次容器重启都会重新生成，**不要保存在数据库中**
- 🤖 **自动管理**: 所有 OIDC 变量由懒猫 v1.3.5+ 自动注入
- ⚙️ **零配置**: 开发者无需手动设置任何 OIDC 参数

## 📦 构建输出

构建成功后，您将获得:

```
agam-space-1.0.0.lpk  # 初始构建 (原始镜像)
agam-space-1.0.1.lpk  # 最终构建 (懒猫仓库镜像)
```

## 🎓 技术细节

### 智能分析

**内部服务检测:**
- ✅ postgres: 无外部端口 → 内部服务
- ❌ agam: 有外部端口 (3331) → 外部服务

**参数分类:**
- ✅ DB_PASSWORD: 内部 + 敏感 + 环境变量 → `{{.INTERNAL.db_password}}`
- ⚠️ ALLOW_NEW_SIGNUP: 外部 → `{{.U.allow_new_signup}}`

### v1.4.1+ 特性

**使用 `healthcheck` (而非 `health_check`):**
```yaml
# ✅ 正确
healthcheck:
  test:
    - CMD-SHELL
    - pg_isready -U postgres

# ❌ 旧格式 (已弃用)
health_check:
  test: ["CMD", "pg_isready"]
```

**使用 `upstreams` (而非 `routes`):**
```yaml
# ✅ 推荐
application:
  upstreams:
    - location: /
      backend: http://agam:3331/

# ⚠️ 旧格式 (仍支持)
application:
  routes:
    - /=http://agam.cloud.lazycat.app.agam-space.lzcapp:3331
```

## 🐛 故障排除

### 问题 1: 构建失败
```bash
# 检查文件完整性
ls -la lzc-*.yml icon.png

# 验证 YAML 格式
yq e 'true' lzc-manifest.yml
```

### 问题 2: 镜像复制失败
```bash
# 检查登录状态
lzc-cli appstore my-images

# 检查镜像是否可访问
docker pull agamspace/agam-space:latest
```

### 问题 3: 发布失败
```bash
# 检查版本号
cat version.txt

# 检查构建包
ls -lh agam-space-*.lpk
```

## 📚 相关文档

- [LazyCat 开发者文档](https://developer.lazycat.cloud)
- [应用发布指南](https://developer.lazycat.cloud/docs/publish-app.html)
- [lzc-cli 参考](https://developer.lazycat.cloud/docs/lzc-cli.html)

## 🔄 版本管理

```bash
# 更新版本号
./build.sh
# 选择 6 - 更新版本号

# 或手动编辑
echo "1.0.1" > version.txt
# 并更新 lzc-manifest.yml 中的 version 字段
```

## ✅ 检查清单

发布前请确认:

- [ ] 准备了 512x512 PNG 图标 (icon.png)
- [ ] 已安装 LazyCat CLI
- [ ] 已登录应用商店 (`lzc-cli appstore login`)
- [ ] 已申请开发者账号
- [ ] 已阅读应用商店审核要求

## 🎉 总结

您已获得完整的懒猫应用配置，包括:

1. ✅ **智能优化**: 仅需 1 个用户配置参数
2. ✅ **自动管理**: 数据库密码自动生成
3. ✅ **现代格式**: v1.4.1+ 兼容
4. ✅ **一键发布**: 完整自动化脚本
5. ✅ **完整文档**: 使用说明和故障排除

**下一步**: 准备 icon.png，然后运行 `./build.sh` 开始发布！

---

**生成时间**: 2025-12-31
**懒猫版本**: v1.4.1+
**智能优化**: 50% 配置减少
