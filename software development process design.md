# Software Development Process Design

## 第一步：明确团队的现状或目标

### 项目类型

- 产品性质：端到端文档处理系统，包括文档处理流程以及文档分类，信息提取等功能。
- 技术栈：React， Java， Spring， Python
- 业务复杂度： 中高等级，包括高定制化文档处理流程，以及文档分类，信息提取等功能。

### 团队现状

- 团队规模： 10人左右
- 团队成员： Java developer为主 (8人左右)，没有专门UI developer，少量python developer (2人左右)。
- 团队协作方式： Scrum 2周一个迭代。

### 工具现状

- 代码管理工具： Git
- CICD工具： Jenkins + Argo
- 监控工具： Prometheus + Grafana
- 日志工具： Log viewer in GCP

### 当前问题

- 缺乏technical lead，没有既定的技术规范，代码质量参差不齐。
- 缺乏详细设计文档，开发时各施各法，没有统一的标准。
- 团队开发时更多关注模块本身，缺乏模块之间的联调，导致整合难度大。
- 缺乏详细的测试用例，测试覆盖率低。
- 缺乏详细的用户体验测试用例，用户体验测试覆盖率低。

## 第二步：确定流程框架

### 开发方法论

- 敏捷开发：Scrum 2周一个sprint, 每个sprint开始前进行需求评审，每个sprint结束前进行sprint demo. 每一个quarter进行一个PI review.

### 核心开发流程

| 阶段   | 输出物           | 工具建议                        |
| ---- | ------------- | --------------------------- |
| 需求分析 | 用户故事     | JIRA / Confluence         |
| 技术设计 | 架构图、接口协议、组件设计 | wireframe / Draw\.io / Markdown |
| 开发   | 源码、单元测试、提交记录  | Git + PR 模式                 |
| 测试   | 测试用例、Bug 记录   | TestRail / tapd / 自定义表格     |
| 部署上线 | 发布包、部署文档      | Git + Jenkins CI + Arog CD           |
| 迭代回顾 | 会议纪要、问题改进点    | Confluence               |

每个阶段有清晰的入口条件 + 输出交付项 + 责任人

## 第三步：制定具体规范

### 规范文档

| 环节   | 建议制定规范                   | 样例说明                              |
| ---- | ------------------------ | --------------------------------- |
| 提交代码 | Git 分支、Commit message 格式 | JIRA: XXXX-0000 feat: 支持用户登录                      |
| 代码评审 | Code Review 流程           | 所有 PR 需 1 个 reviewer 通过 （标准化代码评审）          |
| 接口开发 | API 定义规范                 | 使用 Swagger / OpenAPI 规范接口结构       |
| 分支策略 | Git Flow / trunk-based   | 主分支只用于发布，开发走 feature 分支           |
| 代码结构 | 目录划分、命名约定                | `services/` 放业务逻辑，`models/` 放数据结构 |
| 测试   | 单元测试/集成测试范围              | 覆盖率目标 > 80%；CI 不通过禁止合并            |

### 工具

- 代码：Git
- 接口：Swagger / OpenAPI
- 文档：Confluence
- 任务流：Jira
- 自动化：Jenkins, Argo CD

### 团队开发流程

1. 团队开发流程概览
每个 User Story 流程简图：  
需求分析 → 技术设计 → 编码 & 单测 → 静态扫描 → Code Review → 合并 → 联调 → 部署 UAT → 验收 → 上线

2. 编码前：需求分析和技术设计
**目标**：确保开发前设计充分，风险预判到位

    1. 每个User Story必须由开发人员review并细化
        - 技术实现简要说明（可用 Markdown 附在 Jira 上，或者写在confluence上）
        - 流程图/数据结构/接口设计草图（推荐使用 Draw.io / Swagger）
        - 在设计阶段同步draft测试用例
        - 统一进行评审，确保设计充分

    2. 每个User Story必须定义 Acceptance Criteria
        - 明确增强的目标与范围：确定需要修改或增加的功能。
        - 具体化每个功能点：使用 Given/When/Then 格式清晰表达行为和预期结果。  
            *例如：*
            - Given 该 API 已经在 GET /users/{id} 上运行，When 客户端发起请求并包含有效的 role 参数（如 role=admin），Then 系统应返回角色为 admin 的所有用户数据。
            - Given 客户端请求时提供了 status 参数（如 status=active），When 客户端发起请求，Then 系统应返回状态为 active 的所有用户数据。
            - Given 用户在注册页面，When 用户输入有效的用户名、密码和邮箱并点击提交，Then 用户账户应成功创建，系统显示“注册成功”的提示信息，用户被重定向到登录页面。
        - 覆盖边界情况和错误处理：确保考虑到所有的合法和非法输入，保证 API 的健壮性。
        - 增加请求和响应示例：帮助开发人员和测试人员理解预期行为。

    3. Story Size 估算 (Story Point)
        - 建立锚点 Story 快速对照表

        1. 基础任务（1 ~ 3 Story Points）
        | 类型         | 描述                        | Point |
        | ---------- | ------------------------- | ----- |
        | 后端接口开发（简单） | 单一业务逻辑，无外部依赖（如查询用户信息）     | **1** |
        | 前端小改动      | 页面上文案、颜色、样式调整             | **1** |
        | 接口参数校验     | 后端增加字段合法性校验（如手机号格式）       | **1** |
        | 数据模型新增字段   | 表结构新增一个非核心字段 + 更新返回数据     | **2** |
        | 前端表单组件     | 新建一个静态表单（3\~5个字段）并提交接口    | **2** |
        | 登录页开发      | 前后端联调，表单校验 + 登录请求 + 错误提示  | **3** |
        | 接口鉴权处理     | 添加 JWT 鉴权中间件逻辑 + 接口统一身份校验 | **3** |

        2. 中等复杂任务（5 ~ 8 Story Points）
        | 类型             | 描述                         | Point    |
        | -------------- | -------------------------- | -------- |
        | 表单 + 接口 + 状态管理 | 编辑页 + 数据回填 + 提交保存 + 错误处理   | **5**    |
        | 数据分页与筛选功能      | 实现后端分页逻辑，支持多字段筛选、排序        | **5**    |
        | 报表导出           | 后端生成 Excel/CSV 文件，支持过滤、下载  | **5**    |
        | 与第三方系统集成       | 调用第三方 API（如短信平台），处理成功/失败回调 | **5\~8** |
        | 新模块初始化         | 包括建表、接口、权限、前端页面结构搭建        | **8**    |
        | 多用户角色处理        | 管理端支持不同角色权限行为差异            | **8**    |
        | 单点登录集成（SSO）    | 集成 CAS/OAuth 平台，涉及前后端修改    | **8**    |

        3. 大型复杂任务（13+ Points，建议拆分）
        | 类型        | 描述                            | Point   |
        | --------- | ----------------------------- | ------- |
        | 全新业务流程开发  | 如：申请审批流程（含多步骤页面、审批逻辑、日志记录等）   | **13+** |
        | 架构重构任务    | 改造服务分层、抽象逻辑、模块重命名             | **13+** |
        | 权限系统设计    | 基于 RBAC/ABAC 的权限模型设计与接入       | **13+** |
        | 支持国际化/多语言 | 全站 i18n 改造 + 语言包维护机制          | **13+** |
        | 消息通知中心    | 包含消息模型、websocket 推送、页面展示、已读管理 | **13+** |

        4. T-Shirt Size 对应
        | Story Size | 估算点数     | 特征                 |
        | ---------- | -------- | ------------------ |
        | XS         | 0\~1 pts | 非开发任务 / 文案 / 样式    |
        | S          | 1\~2 pts | 单一逻辑、无依赖、1人当天完成    |
        | M          | 3\~5 pts | 有依赖/调用，开发+测试需1\~3天 |
        | L          | 8 pts    | 多模块协作，需联调、测试、权限处理  |
        | XL         | 13+ pts  | 可交互、流程型、或架构改造，应拆分  |

3. 编码 & 单测

    1. 单元测试
        - 所有核心逻辑必须编写单元测试
        - 单元测试覆盖率：默认 ≥ 70%，核心逻辑 ≥ 85%
        - 前端使用：Jest + React Testing Library
        - 后端使用：JUnit / Pytest

    2. 静态扫描（SonarQube / Sonatype / Cyberflow）
        - 所有PR需通过静态扫描，无 blocker/critical 问题才能合并 （如何实现？）
        - SonarQube：Maintainability、Security Hotspots 无警告， Coverage 达标
        - Sonatype：所用依赖必须无高危漏洞
        - Cyberflow：符合公司安全规范

    3. Code Review 流程
        - 所有 PR 必须由至少 1 名同事 Review（非本人）
        - 重点检查点：
            - commit message规范
            - 是否符合命名规范
            - 是否存在重复逻辑
            - 单元测试是否完善
            - 错误处理是否规范
            - 安全敏感点（如 SQL 注入、XSS）
            - 性能考虑
        - 可使用 Review Checklist 工具插件，如：GitHub PR Checklist

    4. Code Review checklist
        1. 通用规范
            - [ ] 提交信息规范（含 JIRA 编号 + 类型）
            - [ ] 命名是否清晰、语义明确
            - [ ] 是否有未使用的变量/方法
            - [ ] 是否有重复代码可复用
            - [ ] 日志打印是否合理，避免打印敏感信息
            - [ ] 是否遵循团队代码风格

        2. 功能与逻辑
            - [ ] 是否满足 Acceptance Criteria
            - [ ] 是否处理了边界/异常情况
            - [ ] 是否存在潜在空指针 / 数据校验遗漏
            - [ ] 单元测试是否覆盖主要逻辑

        3. 安全性与性能
            - [ ] 是否防止 SQL 注入 / XSS 等漏洞
            - [ ] 是否存在无必要循环、N+1 查询等性能问题

    5. 代码分支策略
    | 分支类型            | 命名规范                          | 用途                   |
    | --------------- | ----------------------------- | -------------------- |
    | `main` or `master`  | -                             | 稳定上线代码，仅从 release 合并 |
    | `release/x.x.x` | `release/1.2.0`               | 预发布版本，UAT 环境部署用      |
    | `develop`       | -                             | 所有开发功能合并汇总分支         |
    | `feature/xxx`   | `feature/XXXX-0000_login-api`           | 单个 story/功能          |
    | `bugfix/xxx`    | `bugfix/XXXX-0000_login-null-token`     | 修复线上的缺陷              |
    | `hotfix/xxx`    | `hotfix/XXXX-0000_critical-login-error` | 生产紧急修复               |

    6. 代码合并
        - feature → develop：需通过 Code Review + 测试通过
        - develop → release：需验收通过 + 产品确认
        - release → main：上线后立即打 tag

4. 部署策略

    | 环境   | 来源分支        | 触发方式     | 特点       |
    | ---- | ----------- | -------- | -------- |
    | dev  | develop     | 自动 CI/CD | 用于开发日常自测 |
    | uat  | release/x.x | 半自动      | 产品验收测试   |
    | prod | main        | 手动 + 审批  | 上线正式版本   |

    - 使用 Git tag 标记每次上线版本，如：v1.2.0

5. 其他

    1. 文档协同标准化
        - 所有设计、接口、组件说明统一 Confluence 编写
        - 技术设计必须在开发前审阅通过

    2. 公共组件沉淀
        - 建立 shared 包，封装常用组件、工具函数
        - 明确“哪些逻辑需复用，不能复制粘贴开发”

    3. 安全规范建议
        - 明确密码加密/脱敏规则
        - 登录/鉴权/权限控制应统一由中间件封装
