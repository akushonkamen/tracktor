# Plane → Tracktor 全量改造计划

> 生成日期: 2026-04-06
> 分支: infra/phase-2-package-scope-reename
> 状态: DRAFT

## 需求总结

将 Plane (makeplane/plane) fork 全量改造为 Tracktor 品牌：

- 替换所有 Plane 相关命名、品牌、标识为 Tracktor
- 在 GitHub 创建独立仓库 akushonkamen/tracktor（全新初始化）
- 使用 claude-auto-gates 维护门禁
- 每次更改提 Issue + PR，细粒度拆分
- 顺序执行，每步验证

## 用户决策记录

| 决策项            | 选择                                      |
| ----------------- | ----------------------------------------- |
| Python 模块重命名 | 分阶段逐步重命名（非核心模块 → 核心模块） |
| 版权头            | 完全替换为 Tracktor（用户接受法律风险）   |
| PR 粒度           | 细粒度（每类别一个 Issue + PR）           |
| GitHub 仓库       | 新建仓库 + 重新初始化，commit 像全新开发  |
| 执行方式          | 顺序执行，逐步验证                        |

## 验收标准

1. 所有用户可见的 "Plane" 文本替换为 "Tracktor"
2. 所有 `makeplane` / `plane.so` 引用替换为 Tracktor 对应内容
3. Python 模块 `apps/api/plane/` → `apps/api/tracktor/`（分阶段完成）
4. Docker 容器/镜像命名使用 Tracktor 品牌名
5. CI/CD 工作流指向 Tracktor 仓库
6. 所有 PR 通过 claude-auto-gates 质量检查
7. 项目可正常构建 (`pnpm build`) 和运行 (`pnpm dev`)
8. 新 GitHub 仓库初始化并包含所有改造内容

---

## Phase 3: 品牌层 — UI 文本与常量

### Issue/PR 3.1: 替换 packages/constants 中的品牌信息

**范围**: `packages/constants/src/` 下的所有常量文件

**具体变更**:

- `metadata.ts` (11处): `SITE_NAME`, `SPACE_SITE_NAME`, `SPACE_TWITTER_USER_NAME` 等
- `endpoints.ts` (7处): `WEBSITE_URL`, `SUPPORT_EMAIL`, 营销链接
- `payment.ts` (8处): `PLANE_COMMUNITY_PRODUCTS` 常量名及 "Plane Pro/Business/Enterprise"

**验证**: `pnpm check:types && grep -ri "plane" packages/constants/src/` 应无残留品牌引用

---

### Issue/PR 3.2: 替换 packages/i18n 翻译字符串

**范围**: `packages/i18n/src/locales/` 下所有语言文件

**具体变更**:

- `en/translations.ts` 及其他语言文件中的 "Plane" 品牌文本
- 维护/启动错误消息中的品牌名

**验证**: `grep -ri "plane" packages/i18n/src/` 应无残留品牌引用

---

### Issue/PR 3.3: 替换 apps/web 用户可见品牌文本

**范围**: `apps/web/` 下所有用户可见的 "Plane" 文本

**具体变更**:

- `core/constants/plans.tsx` (~24处): "without leaving Plane" 等
- `ce/components/instance/maintenance-message.tsx`: 错误页面文本
- 产品更新组件、帮助命令、onboarding tour
- `powered-by.tsx`, `sidebar-help-section.tsx`
- `@planepowers` Twitter handle (7处)
- Root layout, error pages 中的 brand metadata

**验证**: 浏览器访问 http://127.0.0.1:3000/ 无 "Plane" 品牌文本

---

### Issue/PR 3.4: 替换 apps/admin 用户可见品牌文本

**范围**: `apps/admin/` 下所有用户可见的 "Plane" 文本

**具体变更**:

- Admin 布局和页面组件中的品牌引用
- `@planepowers` Twitter handle
- Metadata 和标题

**验证**: `grep -ri "plane" apps/admin/ --include="*.tsx" --include="*.ts"` 应无品牌残留

---

### Issue/PR 3.5: 替换 apps/space 用户可见品牌文本

**范围**: `apps/space/` 下所有用户可见的 "Plane" 文本

**具体变更**:

- `components/account/terms-and-conditions.tsx`: 法律/条款页面
- `@planepowers` Twitter handle
- 品牌引用和 metadata

**验证**: `grep -ri "plane" apps/space/ --include="*.tsx" --include="*.ts"` 应无品牌残留

---

### Issue/PR 3.6: 替换邮件模板品牌

**范围**: `apps/api/templates/emails/` 下 14 个 HTML 模板

**具体变更**:

- 所有模板中的 "Plane" → "Tracktor"
- `media.docs.plane.so` 图片 URL → Tracktor 对应 URL
- `support@plane.so` → Tracktor 支持邮箱
- `github.com/makeplane` → Tracktor GitHub URL
- `forum.plane.so` 链接 → Tracktor 对应链接
- Footer 中的 "Plane Software, Inc." → Tracktor

**验证**: `grep -ri "plane" apps/api/templates/emails/` 应无残留

---

### Issue/PR 3.7: 批量替换版权头

**范围**: 全项目 ~860+ 文件

**具体变更**:

- 使用脚本批量替换所有 `.py`, `.ts`, `.tsx`, `.js`, `.jsx` 文件的版权头
- `Copyright (c) 2023-present Plane Software, Inc. and contributors` → `Copyright (c) 2023-present Tracktor Contributors`
- SPDX 许可证标识符保持 `AGPL-3.0-only` 不变

**执行方法**:

```bash
# Python 文件
find apps/api/plane -name "*.py" -exec sed -i '' 's/Plane Software, Inc. and contributors/Tracktor Contributors/g' {} +
# TypeScript/TSX 文件
find apps packages -name "*.ts" -o -name "*.tsx" | xargs sed -i '' 's/Plane Software, Inc. and contributors/Tracktor Contributors/g'
```

**验证**: `grep -r "Plane Software" --include="*.py" --include="*.ts" --include="*.tsx"` 应返回 0 结果

---

### Issue/PR 3.8: 替换 Logo 和品牌资产

**范围**: 品牌相关图片和 SVG 组件

**具体变更**:

- 图片文件重命名:
  - `apps/admin/app/assets/images/plane-takeoff.png` → `tracktor-takeoff.png`
  - `apps/space/app/assets/instance/plane-instance-not-ready.webp` → `tracktor-instance-not-ready.webp`
  - `apps/space/app/assets/instance/plane-takeoff.png` → `tracktor-takeoff.png`
  - `apps/space/app/assets/plane-logo.svg` → `tracktor-logo.svg`
  - `apps/web/app/assets/plane-takeoff.png` → `tracktor-takeoff.png`
  - `apps/web/public/plane-logos/` → `tracktor-logos/`
  - `packages/propel/public/plane-lockup-light.svg` → `tracktor-lockup-light.svg`

- SVG 组件重命名:
  - `packages/propel/src/icons/brand/plane-logo.tsx` → `tracktor-logo.tsx` (导出 `TracktorLogo`)
  - `packages/propel/src/icons/brand/plane-wordmark.tsx` → `tracktor-wordmark.tsx` (导出 `TracktorWordmark`)
  - `packages/propel/src/icons/brand/plane-lockup.tsx` → `tracktor-lockup.tsx` (导出 `TracktorLockup`)
  - `packages/propel/src/icons/sub-brand/plane-icon.tsx` → `tracktor-icon.tsx` (导出 `TracktorIcon`)

- 更新 ~20 个导入这些组件的文件中的 import 路径

**验证**: `pnpm build` 成功 + `grep -r "PlaneLogo\|PlaneWordmark\|PlaneLockup\|PlaneNewIcon" apps/ packages/` 无残留

---

### Issue/PR 3.9: 替换 PWA manifest、favicon、metadata

**范围**: Web 应用配置文件

**具体变更**:

- `apps/web/public/manifest.json` (如有): "Plane" → "Tracktor"
- Favicon / apple-touch-icon 相关引用
- 各 app 的 HTML 模板中的 `<title>` 和 meta 标签
- `packages/constants/src/metadata.ts` 中的 PWA 相关配置

**验证**: `pnpm build` 成功 + 浏览器标签页显示 Tracktor 品牌

---

## Phase 4: 基础设施层 — Docker、CI、部署

### Issue/PR 4.1: 替换 docker-compose 容器和服务命名

**范围**: `docker-compose.yml`, `docker-compose-local.yml`

**具体变更**:

- 服务名: `plane-db` → `tracktor-db`, `plane-redis` → `tracktor-redis`, `plane-mq` → `tracktor-mq`, `plane-minio` → `tracktor-minio`
- 环境变量默认值: `POSTGRES_USER=plane` → `POSTGRES_USER=tracktor`, 同理密码和数据库名
- `REDIS_HOST=plane-redis` → `REDIS_HOST=tracktor-redis`
- `RABBITMQ_HOST=plane-mq` → `RABBITMQ_HOST=tracktor-mq`
- 网络名: `dev_env` 保持或改为 `tracktor_dev_env`
- 卷名: `redisdata`, `rabbitmq_data` 等

**验证**: `docker compose -f docker-compose-local.yml up -d` 所有服务正常启动

---

### Issue/PR 4.2: 替换环境变量和配置文件

**范围**: `.env`, `apps/api/.env`, `apps/api/.env.example`

**具体变更**:

- `WEB_URL` 中 "plane" 相关内容
- 数据库默认值 (`POSTGRES_USER=plane` → `POSTGRES_USER=tracktor`)
- `RABBITMQ_USER`, `RABBITMQ_PASSWORD`, `RABBITMQ_VHOST`
- `CORS_ALLOWED_ORIGINS` 中端口保持不变
- `ADMIN_BASE_URL`, `SPACE_BASE_URL`, `APP_BASE_URL` 保持 localhost

**验证**: 重启 Docker 容器后 API 正常响应

---

### Issue/PR 4.3: 替换 Dockerfile 中的品牌引用

**范围**: 8 个 Dockerfile

**具体变更**:

- `apps/api/Dockerfile.api`, `Dockerfile.dev`: `COPY plane plane/` → `COPY tracktor tracktor/`, `/code/plane/logs` → `/code/tracktor/logs`
- `apps/space/Dockerfile.space`: `VITE_WEBSITE_URL="https://plane.so"` → Tracktor URL
- `apps/admin/Dockerfile.admin`: 同上
- `apps/web/Dockerfile.web`: 同上
- `apps/live/Dockerfile.live`: 如有 Plane 引用
- `apps/proxy/Dockerfile.ce`: Caddy 配置中的 Plane 引用
- `deployments/aio/community/Dockerfile`: `PLANE_VERSION` → `TRACKTOR_VERSION`, `FROM makeplane/plane-*` → Tracktor 镜像

**验证**: `docker compose -f docker-compose-local.yml build` 成功

---

### Issue/PR 4.4: 替换部署配置

**范围**: `deployments/` 目录

**具体变更**:

- `deployments/cli/community/docker-compose.yml` (~43处): 所有 `makeplane/plane-*` 镜像名、服务名、环境变量
- `deployments/cli/community/install.sh` (~33处): 安装脚本中的品牌引用
- `deployments/cli/community/variables.env` (~11处): 默认环境变量
- `deployments/swarm/community/swarm.sh` (~23处): Swarm 部署脚本
- `deployments/kubernetes/` 相关文件
- `deployments/aio/` 相关文件

**验证**: 检查脚本语法正确 + `grep -ri "plane" deployments/` 仅保留合法残留

---

### Issue/PR 4.5: 替换 GitHub 工作流

**范围**: `.github/workflows/`

**具体变更**:

- `build-branch.yml` (~23处): `makeplane/actions` → Tracktor CI Action, `makeplane/plane-*` → Tracktor 镜像, `PLANE_VERSION` → `TRACKTOR_VERSION`
- `feature-deployment.yml` (~2处): `makeplane/plane-aio-feature`, `feature.plane.tools`
- 仓库引用从 `makeplane/plane` → `akushonkamen/tracktor`

**验证**: YAML 语法正确 + 所有引用指向 Tracktor

---

### Issue/PR 4.6: 替换 GitHub Issue 模板

**范围**: `.github/ISSUE_TEMPLATE/`

**具体变更**:

- `--bug-report.yaml`: 标签 `[plane]` → `[tracktor]`, 描述文本
- `--feature-request.yaml`: 同上
- `config.yaml`: `support@plane.so` → Tracktor 支持邮箱, "Plane" 引用

**验证**: YAML 语法正确

---

## Phase 5: 文档层

### Issue/PR 5.1: 重写 README.md

**范围**: `/README.md`

**具体变更**:

- 完全重写为 Tracktor 项目介绍
- 替换所有 `plane.so` URL
- 替换所有 `makeplane` 引用
- 更新安装说明、截图引用、徽章链接
- 声明 fork 来源（合规要求）

**验证**: `grep -i "plane" README.md` 仅保留 fork 声明中的原始项目引用

---

### Issue/PR 5.2: 更新贡献和安全文档

**范围**: `CONTRIBUTING.md`, `SECURITY.md`, `CODE_OF_CONDUCT.md`, `COPYRIGHT.txt`, `LICENSE.txt`

**具体变更**:

- `CONTRIBUTING.md`: 更新贡献指南、仓库链接、联系信息
- `SECURITY.md`: 更新安全报告邮箱
- `CODE_OF_CONDUCT.md`: 更新联系信息
- `COPYRIGHT.txt`: 更新版权声明（保留原始版权声明以满足 AGPL-3.0）
- `LICENSE.txt`: 更新版权声明

**验证**: 文档内容一致且正确

---

### Issue/PR 5.3: 更新 CLAUDE.md 和 AGENTS.md

**范围**: `/CLAUDE.md`, `/AGENTS.md`

**具体变更**:

- `CLAUDE.md`: 更新项目名称、仓库链接、命令示例
- `AGENTS.md`: 更新项目引用

**验证**: `grep -i "plane" CLAUDE.md` 仅保留 fork 声明引用

---

## Phase 6: Python 后端 — 分阶段模块重命名

### Issue/PR 6.1: 重命名非核心 Python 模块（第一批）

**范围**: `apps/api/plane/` 下的非核心模块

**具体变更**:

- `apps/api/plane/utils/` → `apps/api/tracktor/utils/`（目录重命名 + 所有内部 import）
- `apps/api/plane/middleware/` → `apps/api/tracktor/middleware/`
- `apps/api/plane/settings/` → `apps/api/tracktor/settings/`
- `apps/api/plane/license/` → `apps/api/tracktor/license/`
- `apps/api/plane/bgtasks/` → `apps/api/tracktor/bgtasks/`
- `apps/api/plane/seeds/` → `apps/api/tracktor/seeds/`
- 更新 `settings/` 中对这些模块的所有引用（`INSTALLED_APPS`, `MIDDLEWARE`, `CELERY_IMPORTS` 等）
- 更新 `manage.py`, `wsgi.py`, `asgi.py` 中的路径

**验证**: Django `check` 命令通过 + API 服务正常启动

---

### Issue/PR 6.2: 重命名非核心 Python 模块（第二批）

**范围**: `apps/api/plane/` 下的半核心模块

**具体变更**:

- `apps/api/plane/authentication/` → `apps/api/tracktor/authentication/`
- `apps/api/plane/space/` → `apps/api/tracktor/space/`
- `apps/api/plane/web/` → `apps/api/tracktor/web/`
- 更新所有引用这些模块的 import 语句
- 更新 URL 配置

**验证**: Django `check` 命令通过 + 登录/认证流程正常

---

### Issue/PR 6.3: 重命名核心 Python 模块

**范围**: `apps/api/plane/` 下的核心模块

**具体变更**:

- `apps/api/plane/api/` → `apps/api/tracktor/api/`
- `apps/api/plane/app/` → `apps/api/tracktor/app/`
- `apps/api/plane/db/` → `apps/api/tracktor/db/`
- `apps/api/plane/analytics/` → `apps/api/tracktor/analytics/`
- `apps/api/plane/tests/` → `apps/api/tracktor/tests/`
- 更新所有 import、URL 路由、Django app registry

**验证**: `docker compose -f docker-compose-local.yml up -d` + API 健康检查通过

---

### Issue/PR 6.4: 更新 Django 配置和入口文件

**范围**: Django 项目配置

**具体变更**:

- `apps/api/plane/__init__.py` → `apps/api/tracktor/__init__.py`
- `apps/api/plane/urls.py` → `apps/api/tracktor/urls.py`
- `apps/api/plane/wsgi.py` → `apps/api/tracktor/wsgi.py`
- `apps/api/plane/asgi.py` → `apps/api/tracktor/asgi.py`
- `apps/api/plane/celery.py` → `apps/api/tracktor/celery.py`
- `manage.py`: 更新 Django settings 模块路径
- `ROOT_URLCONF`, `WSGI_APPLICATION` 设置中的 `plane.` → `tracktor.`
- `CELERY_IMPORTS` 中的 `plane.` → `tracktor.`

**验证**: `python manage.py check` 通过 + `python manage.py runserver` 正常启动

---

### Issue/PR 6.5: 更新数据库迁移文件

**范围**: `apps/api/plane/db/migrations/` (129 个迁移文件)

**具体变更**:

- 目录迁移: `apps/api/plane/db/migrations/` → `apps/api/tracktor/db/migrations/`
- 所有迁移文件中的 `from plane.db.migrations` → `from tracktor.db.migrations`
- 迁移依赖引用中的 `plane.` → `tracktor.`
- `Migration` 类中的 `app_label` 保持不变（Django 内部标识符，修改会破坏迁移历史）

**验证**: `python manage.py migrate --check` 通过 + `python manage.py showmigrations` 显示完整迁移历史

---

### Issue/PR 6.6: 最终目录重命名

**范围**: `apps/api/plane/` → `apps/api/tracktor/`

**具体变更**:

- 重命名根目录 `apps/api/plane/` → `apps/api/tracktor/`
- 验证所有 import、配置、路径引用已更新
- 清理任何残留的 `plane` 引用

**验证**:

- `grep -r "from plane\." apps/api/ --include="*.py"` 返回 0 结果
- `grep -r "import plane" apps/api/ --include="*.py"` 返回 0 结果
- `docker compose -f docker-compose-local.yml up -d` 成功
- API 健康检查通过
- 前端连接 API 正常

---

## Phase 7: 新仓库初始化与最终验证

### Issue/PR 7.1: 创建新 GitHub 仓库

**具体变更**:

- 使用 `gh repo create akushonkamen/tracktor` 创建新仓库
- 设置分支保护规则（`preview` 为默认分支）
- 配置 `tracktor` remote 指向新仓库

---

### Issue/PR 7.2: 设置 claude-auto-gates 质量门禁

**范围**: 项目质量检查配置

**具体变更**:

- 运行 `/gates setup` 配置 25 项质量检查
- 配置 pre-commit hooks
- 配置 lint-staged
- 设置 CI 质量检查

**验证**: `/gates check` 全部通过

---

### Issue/PR 7.3: 全量验证与推送

**具体变更**:

- 运行 `pnpm build` 确保构建成功
- 运行 `pnpm check` 确保类型和 lint 通过
- 运行 `docker compose -f docker-compose-local.yml up -d` 确保后端正常
- 访问前端验证 UI 显示 Tracktor 品牌
- 重新初始化 git 历史并推送到新仓库

**验证**:

- 新仓库 `akushonkamen/tracktor` 包含所有改造内容
- 所有质量门禁通过
- 项目可正常构建和运行

---

## 风险与缓解措施

| 风险                                  | 影响 | 缓解措施                                               |
| ------------------------------------- | ---- | ------------------------------------------------------ |
| Python 模块重命名导致 Django 应用崩溃 | 高   | 每个 Phase 后运行 `python manage.py check` 和 API 测试 |
| 版权头替换不符合 AGPL-3.0             | 法律 | 在 README 中保留 fork 声明和原始许可证                 |
| 数据库迁移文件损坏                    | 高   | 迁移中 `app_label` 保持不变，仅修改 import 路径        |
| Docker 网络名变更导致服务无法通信     | 中   | 统一更新所有 compose 文件中的网络引用                  |
| 新仓库推送后 CI 不工作                | 中   | 先在本地验证所有检查，再推送                           |
| 大量文件修改引入意外错误              | 中   | 每个 PR 粒度小，逐个验证                               |

## 执行顺序

```
Phase 3 (品牌层) → Phase 5 (文档层) → Phase 4 (基础设施层) → Phase 6 (Python 后端) → Phase 7 (新仓库)
```

**理由**: 先改无风险的 UI 和文档，再改基础设施配置，最后处理风险最高的 Python 模块重命名。品牌和文档先行可以为后续阶段提供一致的命名参考。
