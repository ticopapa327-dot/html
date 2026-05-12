# SH-MEDVISION Entity & Knowledge Architecture

> 版本：v1.0  
> 日期：2026-05-12  
> 定位：企业知识工程 / AI 可读系统 / SEO-AIO 基础设施  
> 适用对象：内容工程、研发、品牌、售前、交付、运维、AI 协同团队

---

## 0. 文档目标与边界

本文件定义 SH-MEDVISION 从“官网内容管理”升级为“企业知识系统”的工程架构，不讨论宣传表达与视觉设计，聚焦：

1. **实体建模（Entity Modeling）**
2. **关系建模（Knowledge Graph）**
3. **事实建模（Fact Layer）**
4. **AI 可读与可引用（AI Readiness / Citation Probability）**
5. **仓库工程化（Knowledge Repository）**
6. **分阶段落地（1/3/6/12 个月）**

---

## 一、Entity（实体）系统

### 1.1 实体定义原则

实体应满足以下条件：

- 可被唯一命名（有标准中文名/英文名/别名）
- 有稳定属性（不是一次性营销文案）
- 与其他对象可形成关系（belongsTo / supports / deliveredAt / references）
- 可被持续更新并具备“事实沉淀价值”
- 对 SEO/AIO 有长期检索价值

---

### 1.2 核心实体清单（建议基线）

#### A. Company Entity（公司实体）

- 上海首视信息科技有限公司
- SH-MEDVISION
- 首视医疗

**关键属性**：
- legal_name
- brand_name
- founded_date
- headquarters
- official_domains
- certifications
- key_products
- key_solutions

**现状判断（基于常见官网结构）**：
- 通常已有“关于我们”页面，但**实体字段不完整**（缺规范 ID、别名、统一描述）。
- 缺少可机器读取的公司统一档案（JSON-LD + entity JSON）。

#### B. Product Entity（产品实体）

- 医疗视讯软件 SmartView
- 数字化手术室软件 SmartOR
- 手术示教软件 SmartST
- 远程会诊软件 SmartRC
- ICU 探视软件 SmartICU
- 手术室行为管理软件 SmartBM

**关键属性**：
- product_id
- product_name_zh / product_name_en
- aliases
- product_line
- supported_solutions
- core_capabilities
- deployment_mode
- compliance_scope
- release_status

**现状判断**：
- 大概率存在产品介绍页，但“参数化事实”弱。
- 产品之间缺少“边界差异”与“组合关系”声明。
- 缺标准化 FAQ /版本/适用科室字段。

#### C. Solution Entity（解决方案实体）

- 数字化手术室
- 手术示教系统
- ICU探视系统
- 手术室行为管理系统
- 远程会诊系统
- 医疗视讯平台

**关键属性**：
- solution_id
- applicable_scenarios
- mapped_products
- mapped_technologies
- typical_project_types
- KPI_targets

**现状判断**：
- 解决方案页通常偏叙述，缺结构化映射（solution → product → capability）。

#### D. Technology Entity（技术能力实体）

- PANO/FIELD/SCOPE/AUX 四路模型
- 医疗音视频融合
- OR协同
- 多源影像接入
- 手术直播
- 远程协同

**关键属性**：
- tech_id
- protocol_support
- input_output_matrix
- latency_range
- security_controls
- compatible_products

**现状判断**：
- 技术名词存在，但缺“可验证事实层”（如协议、延迟等级、可用条件）。

#### E. Project Entity（项目实体）

- 医院项目
- 项目案例
- 区域
- 医院类型
- 项目类型

**关键属性**：
- project_id
- hospital_id
- region
- project_type
- solution_set
- product_set
- delivery_status
- visibility_level
- evidence_level

**现状判断**：
- 案例页面可能存在，但未形成结构化项目库。
- “是否公开”“证据来源”“交付状态”字段常缺。

#### F. Organization Entity（组织实体）

- 医院
- 军创会
- 合作单位

**关键属性**：
- organization_id
- org_type
- organization_name
- region
- cooperation_type
- related_projects

**现状判断**：
- 医院常被当作正文名词，而非可检索实体节点。

---

### 1.3 实体成熟度分层

- **L0（文本提及）**：只有文章提到
- **L1（独立页面）**：有 URL 与介绍
- **L2（结构化）**：有 schema.org + JSON facts
- **L3（关系化）**：进入知识图谱并具备双向链接
- **L4（可推理）**：具备证据等级、时间戳、版本、变更记录

建议优先把 Product / Solution / Project / Hospital 做到 **L3**，Company 做到 **L4**。

---

### 1.4 哪些实体需要独立页面 / schema / llms.txt / SEO 沉淀

| 实体类型 | 独立页面 | schema.org | 纳入 llms.txt | SEO/AIO 长期价值 |
|---|---|---|---|---|
| Company | 必须 | Organization | 必须 | 高 |
| Product | 必须 | Product / SoftwareApplication | 必须 | 极高 |
| Solution | 必须 | Service / WebPage | 必须 | 极高 |
| Technology | 建议 | TechArticle / DefinedTerm | 建议 | 高 |
| Project Case | 必须 | CreativeWork / CaseStudy(扩展) | 必须 | 极高 |
| Hospital/Organization | 建议 | Organization / Place | 建议 | 高 |
| FAQ Item | 聚合页+分组页 | FAQPage / Question / Answer | 必须 | 极高 |

---

## 二、Knowledge Graph（知识图谱）

### 2.1 关系图（逻辑模型）

```text
Company
 ├─ owns → Product
 ├─ provides → Solution
 ├─ develops → Technology
 ├─ delivers → Project
 └─ cooperatesWith → Organization

Project
 ├─ implementedAt → Hospital(Organization)
 ├─ uses → Product
 ├─ realizes → Solution
 ├─ dependsOn → Technology
 ├─ hasEvidence → Evidence
 └─ hasFAQ → FAQ

Solution
 ├─ composedOf → Product
 ├─ enabledBy → Technology
 └─ targets → Scenario/Department

Product
 ├─ partOf → ProductLine
 ├─ supports → Solution
 ├─ includesCapability → Capability
 └─ hasCertification → Certification
```

---

### 2.2 推荐数据结构（图 + 文档双轨）

- **图结构层**：实体节点 + 关系边（可落到 Neo4j / Neptune / graph tables）
- **文档层**：Markdown（人类阅读）+ JSON（机器读取）
- **索引层**：按 entity_id 建立统一 registry
- **事实层**：每条事实含来源、时间、证据等级、状态

---

### 2.3 推荐 JSON 模板（示例）

```json
{
  "entity_id": "product-smartor",
  "entity_type": "Product",
  "name": {
    "zh": "数字化手术室软件 SmartOR",
    "en": "SmartOR"
  },
  "aliases": ["Smart OR", "首视 SmartOR"],
  "belongs_to_company": "company-sh-medvision",
  "supports_solutions": ["solution-digital-or", "solution-teaching"],
  "core_technologies": ["tech-or-collaboration", "tech-multi-source-input"],
  "facts": [
    {
      "fact_key": "deployment_mode",
      "fact_value": "on-premise",
      "effective_date": "2026-05-12",
      "evidence_level": "B",
      "source": "internal_product_doc_v3"
    }
  ],
  "last_updated": "2026-05-12",
  "status": "active"
}
```

---

### 2.4 推荐 Markdown 结构（实体页）

```md
# [实体名称]

## 1. 标准信息
- Entity ID:
- 类型:
- 别名:
- 首次发布时间:
- 最近更新时间:

## 2. 定义

## 3. 关键能力/属性

## 4. 关联实体
- 上游：
- 下游：
- 横向：

## 5. 事实与证据
| Fact | Value | Evidence | Date | Status |

## 6. 相关FAQ

## 7. 结构化数据
- JSON: ./entity.json
- Schema: Product/Organization/FAQPage
```

---

### 2.5 推荐目录结构

```text
/content
  /pages
  /news
/entities
  /company
  /products
  /solutions
  /technologies
  /organizations
  /projects
/knowledge
  /graph
  /taxonomy
  /glossary
/faq
/prompts
/seo
  /schema
  /sitemaps
  /llms
/projects
/products
/solutions
/research
/reports
```

---

### 2.6 内链结构与 SEO 聚类

**内链主链路（必须双向）**：
- Product ↔ Solution
- Solution ↔ Project
- Project ↔ Hospital
- Technology ↔ Product
- FAQ ↔ Product/Solution

**SEO Cluster 建议**：
1. 数字化手术室集群（SmartOR 为核心）
2. 手术示教集群（SmartST + 直播/录播能力）
3. 远程会诊集群（SmartRC + 区域协同）
4. ICU 探视集群（SmartICU + 院感/流程）
5. 医疗视讯平台集群（SmartView + 多源接入）
6. 手术行为管理集群（SmartBM + 行为规范）

---

## 三、Fact Layer（事实层）

### 3.1 当前常见缺口（目标导向）

应补齐以下结构化事实字段：

- 交付状态（规划中/实施中/已验收/运维中）
- 医院类型（三甲/专科/教学/区域医疗中心等）
- 可公开性（公开/匿名公开/内部）
- 证据等级（A 官方文档 / B 内部记录 / C 口述）
- 时间锚点（发布日期、交付日期、更新时间）
- 产品归属与版本
- 技术归属与适配条件

---

### 3.2 Project Fact Schema

```json
{
  "project_id": "proj-2024-xxx",
  "project_name": "某医院数字化手术室建设项目",
  "hospital_id": "org-hospital-xxx",
  "hospital_type": "tertiary_general",
  "region": "CN-31",
  "project_type": "digital_or",
  "solutions": ["solution-digital-or"],
  "products": ["product-smartor", "product-smartview"],
  "technologies": ["tech-or-collaboration", "tech-live-streaming"],
  "delivery_status": "accepted",
  "visibility": "public_anonymous",
  "evidence_level": "A",
  "evidence_refs": ["contract_no_xxx", "acceptance_report_xxx"],
  "go_live_date": "2024-10-15",
  "published_date": "2025-01-08",
  "last_verified": "2026-05-12"
}
```

### 3.3 Product Fact Schema

```json
{
  "product_id": "product-smartview",
  "category": "medical_av_platform",
  "version": "v5.2",
  "lifecycle": "active",
  "deployment_modes": ["on_premise", "hybrid"],
  "supported_solutions": ["solution-medical-av", "solution-remote-consultation"],
  "compliance": ["等保支持", "日志审计"],
  "release_date": "2025-06-01",
  "last_updated": "2026-05-12",
  "evidence_level": "B"
}
```

### 3.4 FAQ Fact Schema

```json
{
  "faq_id": "faq-smartor-001",
  "question": "SmartOR 是否支持多路术野画面同步？",
  "answer": "支持，基于 PANO/FIELD/SCOPE/AUX 模型进行多路协同。",
  "entity_scope": ["product-smartor", "tech-four-stream-model"],
  "intent_tags": ["capability", "integration"],
  "source_type": "product_doc",
  "evidence_level": "B",
  "created_at": "2026-05-12",
  "updated_at": "2026-05-12"
}
```

### 3.5 Certification Fact Schema

```json
{
  "cert_id": "cert-iso-xxxx",
  "holder_entity": "company-sh-medvision",
  "cert_type": "quality_management",
  "cert_name": "ISO 9001",
  "issuer": "xxx",
  "valid_from": "2025-03-01",
  "valid_to": "2028-02-28",
  "scope": ["software_development", "delivery_service"],
  "public_url": "https://...",
  "evidence_level": "A",
  "last_verified": "2026-05-12"
}
```

---

## 四、AI Readiness（AI可读性）

### 4.1 针对主要 AI 平台的共性判断

面向 ChatGPT、DeepSeek、Kimi、豆包、Perplexity、百度 AI，最关键并非“平台差异”，而是**内容结构化程度**。普遍规律：

- 有 schema + 可爬 sitemap + 清晰 URL + FAQ 的站点，更易被稳定引用
- 有实体页但无事实层，AI 容易“泛化描述”而非“精确引用”
- 新闻很多但事实版本管理弱，易造成信息漂移

---

### 4.2 八项检查与改造方向

1. **llms.txt**
   - 现状风险：可能缺失或仅有基础声明。
   - 目标：提供“可抓取知识入口清单”（产品、方案、FAQ、案例、术语、报告）。

2. **sitemap**
   - 现状风险：仅页面 sitemap，缺实体 sitemap（products.xml / solutions.xml / projects.xml）。
   - 目标：建立分域 sitemap 并定期增量更新。

3. **FAQ**
   - 现状风险：FAQ 零散在正文。
   - 目标：实体级 FAQ 库（按产品/方案/技术分桶）。

4. **schema.org**
   - 现状风险：仅 Organization / WebSite，缺 Product / FAQPage / Breadcrumb。
   - 目标：核心模板全部输出 JSON-LD。

5. **Product Entity**
   - 现状风险：多为营销文案，参数化不足。
   - 目标：每个产品至少 20+ 可验证字段。

6. **Solution Entity**
   - 现状风险：缺与项目、产品、技术的关系链。
   - 目标：solution 页面引出“可证明交付”。

7. **内链结构**
   - 现状风险：栏目式导航多，语义型内链少。
   - 目标：实体关系驱动内链（见 2.6）。

8. **长文本结构与 AI 引用概率**
   - 现状风险：长段落、少小标题、少表格、缺结论句。
   - 目标：每段 1 个可引用事实，附时间与适用范围。

---

## 五、Knowledge Repository（知识仓库）

### 5.1 仓库升级目标

从“网页源文件仓库”升级为“企业知识仓库”，要求：

- 内容与事实分离（Narrative vs Facts）
- 实体统一 ID
- 可审计变更记录
- 可自动生成 schema / llms / sitemap / entity index

---

### 5.2 建议目录与职责

```text
/content       # 面向站点的页面内容（Markdown）
/entities      # 实体主数据（JSON+MD）
/knowledge     # 图谱、术语表、关系定义
/faq           # FAQ 数据与渲染模板
/prompts       # AI 内容生产与校验提示词
/seo           # schema、sitemap、llms、重定向规则
/projects      # 项目事实库（重点）
/products      # 产品参数与版本
/solutions     # 方案结构与映射
/research      # 行业研究、竞品与概念沉淀
/reports       # 阶段性架构报告与审计记录
```

---

### 5.3 存储介质建议（MD / JSON / DB）

- **Markdown 适合**：官网正文、报告、研究、说明文档
- **JSON 适合**：实体卡、事实层、FAQ、schema 生成源
- **数据库适合**：高频查询事实（项目、实体关系、版本、日志）
- **AI 自动生成适合**：FAQ 初稿、摘要、标签、内链建议、schema 草案
- **人工审核必须**：证据等级、项目状态、对外可发布性

---

## 六、长期路线图（1/3/6/12 个月）

### 6.1 第 1 个月：建模与基线

- 完成实体字典 v1（Company/Product/Solution/Project/Organization/Technology）
- 建立 entity_id 命名规范
- 上线 llms.txt v1 + sitemap 分域
- 落地 3 个产品页 + 3 个方案页的 JSON-LD 模板
- 建立 FAQ 数据结构与提交流程

**验收指标**：
- 核心实体覆盖率 ≥ 60%
- 关键页面 schema 覆盖率 ≥ 40%

### 6.2 第 3 个月：事实层上线

- 项目事实库（至少 30 个项目结构化条目）
- 产品事实库（6 大核心产品全部结构化）
- FAQ 库（每产品 ≥ 20 条）
- 建立证据等级与发布审核机制

**验收指标**：
- 实体 L2 覆盖率 ≥ 70%
- FAQ 可检索命中率显著提升

### 6.3 第 6 个月：图谱化与自动化

- 构建知识图谱初版（实体关系可视化 + API 查询）
- 自动生成：schema / sitemap / llms 索引
- 建立“内容变更 → 事实校验 → 发布”的 CI 流程

**验收指标**：
- 实体 L3 覆盖率 ≥ 60%
- 重点专题（SmartOR/SmartView）形成完整聚类页群

### 6.4 第 12 个月：AI 驱动知识系统

- 建立企业知识 API（供官网、AI 助手、售前工具共用）
- 上线引用追踪（哪些 AI/页面引用了哪些事实）
- 形成季度知识审计机制（过时事实、弱证据、断链）

**验收指标**：
- 核心实体 L4 覆盖率 ≥ 50%
- 高价值查询（产品能力/案例证明/方案边界）响应稳定

---

## 七、实施细则（工程约束）

### 7.1 ID 与命名规范

- `company-sh-medvision`
- `product-smartor`
- `solution-digital-or`
- `tech-four-stream-model`
- `project-cn-sh-xxxxx`
- `org-hospital-xxxxx`

原则：稳定、可读、可扩展，不使用中文作为主键。

### 7.2 版本策略

- 内容版本（文案）：`content_version`
- 事实版本（字段）：`fact_version`
- 架构版本（模型）：`schema_version`

### 7.3 质量门禁（建议 CI）

- JSON Schema 校验
- 断链检查
- 必填字段检查（entity_id / updated_at / evidence_level）
- schema.org 输出校验
- sitemap 与页面一致性检查

---

## 八、结论（执行导向）

SH-MEDVISION 的下一阶段重点不应是“继续堆页面”，而是构建：

1. **实体主数据层（Entity Master）**
2. **关系图谱层（Knowledge Graph）**
3. **结构化事实层（Fact Layer）**
4. **AI 可读发布层（llms/sitemap/schema/FAQ）**
5. **仓库化治理层（GitHub + CI + 审核流程）**

这五层建立后，官网只是知识系统的一个输出端，企业将具备长期可维护、可引用、可复用的“数字神经系统”能力。
