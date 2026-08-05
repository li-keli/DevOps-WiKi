<div align="center">

<img src="01%20基础设施/img.resource/Devops.svg.png" alt="DevOps" width="280" />

# DevOps Wiki

### 从 0 到 1：后端系统架构 · DevOps 实践 · 微服务落地

[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](LICENSE)
[![Topics](https://img.shields.io/badge/topics-DevOps%20%7C%20Microservice-informational)](https://github.com/li-keli/DevOps-WiKi)
[![Platform](https://img.shields.io/badge/Lab-CentOS%207%20×%203-success)](#实验环境)
[![Status](https://img.shields.io/badge/docs-实践笔记-orange)](#文档导航)

**基于真实三机实验环境的 DevOps 全链路实践笔记**  
覆盖代码托管 · 持续集成 · 容器编排 · 私有仓储 · 微服务 · 自动化测试

[文档导航](#文档导航) · [架构总览](#架构总览) · [学习路径](#推荐学习路径) · [技术栈](#技术栈一览)

</div>

---

## 简介

本仓库是一套**可复现的 DevOps + 微服务实践 Wiki**。内容来自真实架构设计与落地过程，结合部分经典书籍与官方文档整理而成，目标是回答一个很实际的问题：

> 从一台空白的 CentOS 机器开始，如何一步步搭出「可持续、可快速交付」的开发运维一体化体系？

核心理念：

| 理念 | 说明 |
|------|------|
| **DevOps 一体化** | 可持续、快速的交付模式；线上问题由开发团队作为第一响应 |
| **基础设施即交付链** | GitLab → Runner/CI → Docker → Swarm → Nexus → YApi 串联闭环 |
| **微服务可落地** | Spring Boot 基架 + Dubbo RPC + 私有 Maven + API 文档管理 |
| **自动化兜底** | Selenium + Headless Chrome，把回归测试纳入流水线 |

适合人群：后端 / 运维工程师、希望从零搭内网 DevOps 平台的同学、微服务入门实践者。

---

## 架构总览

```text
┌─────────────────────────────────────────────────────────────────────────┐
│                        DevOps 交付闭环（实验拓扑）                         │
│                                                                         │
│   开发者 ──push──▶ GitLab (node01)                                      │
│                       │                                                 │
│                       │ Webhook / Pipeline                              │
│                       ▼                                                 │
│               GitLab Runner (node02)                                    │
│                       │                                                 │
│          ┌────────────┼────────────┐                                    │
│          ▼            ▼            ▼                                    │
│      构建/测试     Docker 镜像    发布制品                                │
│          │            │            │                                    │
│          │            ▼            ▼                                    │
│          │     Swarm 集群      Nexus 私有仓储 (Maven/npm/Docker…)       │
│          │   (node01~03)              │                                 │
│          │            │               │                                 │
│          ▼            ▼               ▼                                 │
│     Selenium 自动化   无状态服务      微服务依赖 / 镜像拉取                 │
│                       │                                                 │
│                       ▼                                                 │
│              YApi 接口文档中心  ◀── Swagger 导入                          │
└─────────────────────────────────────────────────────────────────────────┘
```

### CI/CD 流水线示意

<p align="center">
  <img src="01%20基础设施/img.resource/cicd_pipeline_infograph.png" alt="CI/CD Pipeline" width="900" />
  <br />
  <sub>持续集成与持续交付流水线示意（仓库内配图）</sub>
</p>

### 实验环境

| 节点 | 角色建议 | 典型组件 |
|------|----------|----------|
| **node01** | 控制面 / 核心服务 | GitLab、Swarm Manager、部分中心服务 |
| **node02** | 构建与执行 | GitLab Runner、构建任务 |
| **node03** | 扩展 Worker | Swarm Worker、业务容器副本 |

- **OS**：CentOS 7 × 3 台物理机  
- **目标**：在小规模内网环境复现完整交付链路，而不是单机玩具 Demo  

---

## 文档导航

### ① 基础设施 — 搭好「工厂」

> 代码怎么管、流水线怎么跑、镜像怎么编、集群怎么扩、依赖怎么存、接口文档怎么统一。

| 章节 | 内容摘要 | 链接 |
|------|----------|------|
| 简介 | DevOps 设计思想与实验资源说明 | [01.简介](01%20基础设施/01.简介.md) |
| GitLab | 代码托管、邮件、自动备份、Webhook | [02.Gitlab](01%20基础设施/02.Gitlab.md) |
| 持续集成 | GitLab Runner / Jenkins 选型与 Runner 注册 | [03.持续集成](01%20基础设施/03.持续集成.md) |
| Docker | 容器基础、与 `.gitlab-ci.yml` 集成发布 | [04.Docker](01%20基础设施/04.Docker.md) |
| Docker 集群 | Swarm 初始化、服务创建与网络 | [05.Docker集群](01%20基础设施/05.Docker集群.md) |
| Nexus 仓储 | 通用私有仓库（Maven / npm / Docker 等） | [06.Nexus仓储](01%20基础设施/06.Nexus仓储.md) |
| YApi 文档中心 | Docker 快速部署可视化 API 平台 | [07.YApi文档中心](01%20基础设施/07.YApi文档中心.md) |

<p align="center">
  <img src="01%20基础设施/img.resource/2018-04-08_152128.png" alt="GitLab Runner 配置" width="720" />
  <br />
  <sub>GitLab 项目内 Runner / CI·CD 配置界面</sub>
</p>

<p align="center">
  <img src="01%20基础设施/img.resource/2018-04-10_2.06.png" alt="Nexus 仓储" width="720" />
  <br />
  <sub>Nexus Repository 3：统一托管 Maven、npm、NuGet、Docker、PyPI 等</sub>
</p>

---

### ② 微服务 — 把业务拆成可交付单元

> 从 Spring Boot 脚手架，到 Dubbo 服务拆分、私有 Maven 发布、Swagger → YApi 文档闭环。

#### Spring Boot

| 章节 | 内容摘要 | 链接 |
|------|----------|------|
| Spring Boot 初始化 | IDEA 快速建项与工程结构 | [01.Spring Boot](02%20微服务/01%20Spring%20Boot/01.Spring%20Boot.md) |
| Druid 连接池 | 数据源与监控接入 | [02.druid连接池](02%20微服务/01%20Spring%20Boot/02.druid连接池.md) |
| MyBatis Generator | 代码生成插件实践 | [03.mybatis generator插件](02%20微服务/01%20Spring%20Boot/03.mybatis%20generator插件.md) |
| MyBatis 访问数据 | 数据访问层落地 | [04.mybatis访问数据](02%20微服务/01%20Spring%20Boot/04.mybatis访问数据.md) |

#### Dubbo

| 章节 | 内容摘要 | 链接 |
|------|----------|------|
| Dubbo 相关 | 手册索引与框架概览 | [01.Dubbo相关](02%20微服务/02%20Dubbo/01.Dubbo相关.md) |
| Dubbo 样例 | 基础样例 | [02.Dubbo样例](02%20微服务/02%20Dubbo/02.Dubbo样例.md) |
| 快速创建基架 | Maven 多模块服务骨架 | [03.快速创建Dubbo基架](02%20微服务/02%20Dubbo/03.快速创建Dubbo基架.md) |
| Spring Boot + Dubbo | 与 Boot 集成 | [04.Spring-boot-dubber](02%20微服务/02%20Dubbo/04.Spring-boot-dubber.md) |
| 集成 SwaggerUI | 接口可视化与契约输出 | [05.集成SwaggerUI](02%20微服务/02%20Dubbo/05.集成SwaggerUI.md) |
| 集成 Docker | 服务容器化 | [06.集成Docker](02%20微服务/02%20Dubbo/06.集成Docker.md) |

#### 交付与文档

| 章节 | 内容摘要 | 链接 |
|------|----------|------|
| Maven 发布到私有仓 | 对接 Nexus 的 `distributionManagement` | [maven发布到私有仓储](02%20微服务/03%20maven发布到私有仓库/01.maven发布到私有仓储.md) |
| YApi 使用 | Swagger JSON 导入与接口联调 | [YAPI使用](02%20微服务/04%20API文档管理/01.YAPI使用.md) |

---

### ③ 自动化 — 让质量可重复执行

| 章节 | 内容摘要 | 链接 |
|------|----------|------|
| Selenium | Web 自动化测试；Headless Chrome；Docker 部署 WebDriver Hub | [01.Selenium](03%20自动化/01%20自动化测试/01.Selenium.md) |

---

## 推荐学习路径

按「先工厂、后产品、再质量」顺序阅读，投入产出最高：

```text
1. 基础设施
   简介 → GitLab → 持续集成(Runner) → Docker → Swarm 集群 → Nexus → YApi
        │
        ▼
2. 微服务
   Spring Boot 基架 → MyBatis/Druid → Dubbo 多模块 → Swagger → 发私有仓 → 导入 YApi
        │
        ▼
3. 自动化
   Selenium + Chrome Headless（Docker Hub）→ 接入 CI 做回归
```

**最小闭环建议（周末可跑通）：**

1. 装好 GitLab + 注册一个 Shell Executor Runner  
2. 写一个会 `docker build` 的 `.gitlab-ci.yml`  
3. 把镜像推到 Nexus（或内网 Registry），在 Swarm 上 `docker service create`  
4. 用 YApi 管理一份 API 文档  

---

## 技术栈一览

| 领域 | 选型 | 在本仓库中的角色 |
|------|------|------------------|
| 代码托管 | **GitLab CE** | 源码、Issue、Pipeline、邮件通知 |
| CI/CD | **GitLab Runner**（可选 Jenkins） | 构建、测试、打包、发布 |
| 容器 | **Docker** | 应用标准化打包与运行 |
| 编排 | **Docker Swarm**（文中亦对比 K8s） | 多机服务副本与端口暴露 |
| 制品库 | **Nexus Repository 3** | Maven / npm / Docker 等统一私有仓 |
| API 文档 | **YApi** + Swagger | 前后端契约与联调 |
| 应用框架 | **Spring Boot** | 微服务业务基架 |
| RPC | **Apache Dubbo** | 服务拆分与远程调用 |
| 数据访问 | **MyBatis** + **Druid** | ORM / 连接池与监控 |
| 自动化测试 | **Selenium** + Chrome Headless | Web 回归与无界面采集场景 |

---

## 仓库结构

```text
DevOps-WiKi/
├── 01 基础设施/                 # GitLab · CI · Docker · Swarm · Nexus · YApi
│   ├── 01.简介.md
│   ├── 02.Gitlab.md
│   ├── 03.持续集成.md
│   ├── 04.Docker.md
│   ├── 05.Docker集群.md
│   ├── 06.Nexus仓储.md
│   ├── 07.YApi文档中心.md
│   └── img.resource/            # 基础设施章节配图
├── 02 微服务/
│   ├── 01 Spring Boot/          # Boot · Druid · MyBatis
│   ├── 02 Dubbo/                # 基架 · Swagger · Docker 集成
│   ├── 03 maven发布到私有仓库/
│   └── 04 API文档管理/          # YApi 使用与 Swagger 导入
├── 03 自动化/
│   └── 01 自动化测试/           # Selenium 实践
├── LICENSE                      # Apache License 2.0
└── README.md                    # 本说明
```

---

## 实践亮点摘录

### 1. GitLab 不止是 Git

除代码托管外，文档覆盖：

- 国内镜像加速安装（清华源等）  
- SMTP / 收信邮件协同（Pipeline、Issue 通知）  
- 定时全量备份（`gitlab-rake gitlab:backup:create`）  
- Webhook 对接第三方 IM（如钉钉）  

<p align="center">
  <img src="01%20基础设施/img.resource/2018-04-08_142627.png" alt="GitLab 邮件配置" width="420" />
  <br />
  <sub>GitLab Admin 中的邮件相关配置结果</sub>
</p>

### 2. 从 Shell Runner 理解 CI，再演进到 Docker Executor

文档刻意先用 **Shell 执行器** 走通脚本，再过渡到 Docker 构建与集群部署，降低「第一次写 Pipeline」的心智负担。

```yaml
# 文档中的极简发布思路（示意）
deploy_api:
  stage: deploy
  tags:
    - Build
  script:
    - # 编译产物
    - docker build -t test/api:v1 .
    - docker run -d -p 8001:80 --name test_api_server test/api:v1
  only:
    - master
```

### 3. Swarm：小团队也能上的多机服务

三机环境下初始化 Manager、Worker 加入、`docker service create` 多副本与端口发布，适合作为理解「编排」的第一站（进阶可对照 Kubernetes）。

### 4. 微服务交付闭环

```text
多模块 Dubbo 工程
    → Maven 发布到 Nexus
    → Swagger 产出契约
    → 导入 YApi 统一管理 / 联调
    → Docker 镜像化 → Swarm 运行
```

### 5. 自动化测试容器化

通过 `selenium/standalone-chrome` 镜像快速拉起 WebDriver Hub，避免在每台机器上手工安装浏览器与驱动，便于挂到 CI。

---

## 使用说明

1. **在线阅读**：直接在 GitHub 浏览 Markdown 与配图即可。  
2. **本地阅读**：  

```bash
git clone https://github.com/li-keli/DevOps-WiKi.git
cd DevOps-WiKi
# 使用任意 Markdown 预览工具打开各章节（VS Code / Typora / Obsidian 等）
```

3. **实验复现**：建议准备 3 台可互通的 CentOS 7（或兼容环境），按「基础设施 → 微服务 → 自动化」顺序搭建。  
4. **注意时效**：主体内容写于 **2018** 前后，命令、版本号与 UI 截图可能与最新发行版有差异；思路与链路设计仍可复用，落地时请对照各组件**当前官方文档**微调。

---

## 相关截图墙

| GitLab Runner 下载 | Nexus 概览 |
|:------------------:|:----------:|
| <img src="01%20基础设施/img.resource/2018-04-08_150317.png" width="400" alt="Runner" /> | <img src="01%20基础设施/img.resource/2018-04-10_2.06.png" width="400" alt="Nexus" /> |

| CI 配置页 | 更多实践图 |
|:---------:|:----------:|
| <img src="01%20基础设施/img.resource/2018-04-08_152128.png" width="400" alt="CI" /> | <img src="01%20基础设施/img.resource/2018-04-27_11.36.png" width="400" alt="实践" /> |

更多操作截图见各章节目录下的 `img` / `img.resource`。

---

## 许可协议

本仓库采用 [**Apache License 2.0**](LICENSE)。

---

## 写在后面

这是一套偏「工程实录」的笔记：不追求面面俱到的理论百科，而是把**一条能跑通的交付链**写清楚。

若本仓库对你有帮助，欢迎 Star ⭐；有勘误或补充实践，欢迎提 Issue / PR。

<div align="center">

**从 0 到 1，把 DevOps 做成日常。**

</div>
