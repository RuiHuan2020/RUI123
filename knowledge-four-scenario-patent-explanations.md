# 四种 Knowledge Deployment Scenario：附图注释与专利说明

## 一、四张图的统一图例

- **实线箭头**：查询、知识访问、KnowledgeJob 提交、Knowledge Content 或处理结果返回路径。
- **虚线箭头**：MnS 注册、MnSInfo 更新、Knowledge Descriptor 发布、同步或跨域汇聚路径。
- **外层矩形**：部署边界，例如 Knowledge MnF、管理域、行政域、边缘站点或 Network Function。
- **内层矩形**：功能实体或逻辑存储实体。
- 图中箭头用于表达主要逻辑关系。实际实现中可以通过一个或多个消息完成，也可以由中间网关、代理或服务治理功能转发。

Knowledge Catalogue 与 Knowledge Base 是逻辑分离的：Knowledge Catalogue 保存 Knowledge Descriptor；Knowledge Base 保存 Knowledge Content。逻辑分离不要求两者必须部署在不同物理节点。

---

## 二、Figure 9.2.2.x-1：Co-located deployment

### 1. 图中实体

- **Knowledge MnS Consumer**：请求发现、检索或消费知识的实体。
- **Knowledge MnF — Single Deployment Unit**：单一部署单元形式的知识管理功能。
- **KnowledgeFunction**：执行授权、知识查询、贡献、生命周期管理和内容处理。
- **KnowledgeJob**：表示同步或异步知识任务。
- **Knowledge Catalogue**：保存 Knowledge Descriptor。
- **Knowledge Base**：保存 Knowledge Content。
- **MnS Registry / MRDF**：保存 Knowledge MnS 的 MnSInfo，支持服务发现。

### 2. 编号路径

1. Knowledge MnS Consumer 查询 MnS Registry / MRDF，发现能够提供知识服务的 KnowledgeFunction。
2. Knowledge MnS Consumer 向 KnowledgeFunction 提交知识检索或访问请求。
3. KnowledgeFunction 完成授权和内容处理后，返回授权范围内的 Knowledge Content。
- **(R)** KnowledgeFunction 或其所在 Knowledge MnF 通过虚线路径向 MnS Registry / MRDF 注册 Knowledge MnS。

### 3. 可直接用于专利说明书的描述

在一实施例中，KnowledgeFunction、KnowledgeJob、Knowledge Catalogue 和 Knowledge Base 被部署在同一 Knowledge Management Function 中。Knowledge Management Function 对外提供 Knowledge MnS，并将该 Knowledge MnS 的 MnSInfo 注册至 MnS Registry and Discovery Function。

Knowledge MnS Consumer 首先基于 MnS Registry and Discovery Function 获得 Knowledge MnS 的服务地址和能力信息，随后向 KnowledgeFunction 提交知识访问请求。KnowledgeFunction 根据访问主体、KnowledgeContext、Knowledge Descriptor 和访问策略确定允许访问的知识范围，并从 Knowledge Base 获取对应 Knowledge Content。KnowledgeFunction 可以返回完整内容，也可以返回知识子集、知识图子图、摘要、聚合结果或推理结果。

该方案适合单管理域、单厂商、早期部署或者不需要对 Catalogue 与 Base 独立扩容的场景。

### 4. 可强调的技术效果

- 部署结构简单；
- 内部访问时延较低；
- Knowledge Descriptor 与 Knowledge Content 仍保持逻辑隔离；
- 通过统一 KnowledgeFunction 实施访问控制，避免消费者直接访问 Knowledge Base。

---

## 三、Figure 9.2.2.x-2：Central Catalogue with distributed Knowledge Bases

### 1. 图中实体

- **Operator-level Knowledge Catalogue**：运营商级集中目录，保存脱敏后的 Knowledge Descriptor。
- **RAN / Vendor Domain A**：第一网络域或第一厂商域，包含 KnowledgeFunction A 和 Knowledge Base A。
- **CN / Vendor Domain B**：第二网络域或第二厂商域，包含 KnowledgeFunction B 和 Knowledge Base B。
- **MnS Registry / MRDF**：注册并发现 Catalogue MnS 和各域 Knowledge MnS。

### 2. 编号路径

1. 消费者从 MnS Registry / MRDF 发现 Knowledge Catalogue 和 KnowledgeFunction 服务。
2. 消费者向运营商级 Knowledge Catalogue 查询 Knowledge Descriptor。
3. Catalogue 返回与候选知识对应的 KnowledgeFunction 引用。
4. 消费者向选定的 KnowledgeFunction A 提交 KnowledgeJob。
5. KnowledgeFunction A 从本地 Knowledge Base A 获取内容，并返回经过过滤的知识或知识子图。

虚线路径表示：

- KnowledgeFunction A 和 KnowledgeFunction B 向中央 Catalogue 发布脱敏 Descriptor；
- KnowledgeFunction A、KnowledgeFunction B 和中央 Catalogue 向 MnS Registry / MRDF 注册相应 MnSInfo。

### 3. 可直接用于专利说明书的描述

在一实施例中，知识目录采用集中式部署，而知识内容采用分布式部署。运营商级 Knowledge Catalogue 保存由多个网络域或厂商域发布的 Knowledge Descriptor。每个域保留本域 Knowledge Base，并通过本域 KnowledgeFunction 对 Knowledge Content 实施访问控制。

当 Knowledge MnS Consumer 查询候选知识时，运营商级 Knowledge Catalogue 返回满足检索条件且允许向该消费者披露的 Knowledge Descriptor。Knowledge Descriptor 可以包含对应 KnowledgeFunction 的间接引用，但不包含 Knowledge Base 的直接访问地址。消费者基于该引用向对应 KnowledgeFunction 提交 KnowledgeJob，由 KnowledgeFunction 返回授权内容或处理结果。

通过该方式，可以在提供统一知识发现能力的同时，使完整 Knowledge Content 保留在原始网络域或厂商域中。

### 4. 隐私保护要点

- 中央 Catalogue 只保存脱敏 Descriptor；
- 精确拓扑、租户标识、专有规则和模型参数保留在本地 Base；
- Descriptor 中使用 KnowledgeFunction 间接引用，而不是 Base 地址；
- KnowledgeFunction 在内容离开本域前执行过滤、投影或聚合；
- 不同消费者可以看到不同粒度的 Descriptor view。

### 5. 可强调的技术效果

- 统一发现与分布式内容控制兼容；
- 降低厂商专有知识泄露风险；
- 支持各域独立扩容和生命周期管理；
- 减少将完整知识集中复制所产生的存储和同步开销。

---

## 四、Figure 9.2.2.x-3：Federated Catalogue across administrative domains

### 1. 图中实体

- **Federated Knowledge Catalogue**：保存或汇聚跨域最小化 Descriptor。
- **Administrative Domain A/B**：分别维护本域完整 Catalogue、KnowledgeFunction 和 Knowledge Base。
- **Local Catalogue A/B**：保存本域完整 Descriptor。
- **KnowledgeFunction A/B**：执行本域访问策略和内容处理。

### 2. 编号路径

1. 跨域消费者向 Federated Knowledge Catalogue 发起跨域发现请求。
2. Federated Knowledge Catalogue 返回消费者有权获知的提供方引用。
3. 消费者向提供方域内 KnowledgeFunction A 提交远程查询或 KnowledgeJob。
4. KnowledgeFunction A 在本域完成查询或推理，并返回聚合结果、推理结果或授权投影。

虚线路径表示 Local Catalogue A/B 仅向联邦 Catalogue 发布最小化 Descriptor，而不发布完整 Knowledge Content。

### 3. 可直接用于专利说明书的描述

在一实施例中，不同行政域分别维护本域 Knowledge Catalogue 和 Knowledge Base。各本地 Knowledge Catalogue 通过联邦目录机制发布经过最小化处理的 Knowledge Descriptor。所述最小化处理可以包括字段删除、空间范围粗化、时间范围粗化、来源隐藏、标识匿名化、质量等级离散化或访问条件替换。

跨域消费者基于联邦 Catalogue 获得提供方 KnowledgeFunction 的引用。完整 Knowledge Content 不通过联邦 Catalogue 传输。提供方 KnowledgeFunction 在本域内访问 Knowledge Base，并仅返回消费者被授权获得的知识投影、聚合结果、推理结论或操作建议。

### 4. 可强调的技术效果

- 完整 Knowledge Content 不跨越行政域边界；
- 支持跨运营商、跨厂商或跨租户知识发现；
- 联邦 Catalogue 无需复制各域完整目录；
- 提供方保留对知识用途、范围和披露粒度的控制；
- 适用于数据主权和知识产权保护场景。

---

## 五、Figure 9.2.2.x-4：Embedded or edge deployment

### 1. 图中实体

- **Edge Site**：边缘站点部署边界。
- **Network Function**：位于 Edge Site 中的网络功能。
- **Embedded Knowledge Capability**：嵌入 Network Function 的知识能力。
- **Embedded KnowledgeFunction**：本地执行知识查询和策略控制。
- **Local Knowledge Base**：保存站点敏感或设备敏感的 Knowledge Content。
- **Domain Knowledge Catalogue**：保存由嵌入式 KnowledgeFunction 同步的选定 Descriptor。

### 2. 编号路径

1. 消费者从 MnS Registry / MRDF 发现嵌入式 Knowledge MnS。
2. 消费者向 Domain Knowledge Catalogue 查询选定 Descriptor。
3. 消费者向 Embedded KnowledgeFunction 提交低时延 KnowledgeJob。
4. Embedded KnowledgeFunction 返回本地决策或经过过滤的结果。

虚线路径表示：

- Embedded KnowledgeFunction 向 MnS Registry / MRDF 注册嵌入式 Knowledge MnS；
- Embedded KnowledgeFunction 向 Domain Knowledge Catalogue 同步选定 Descriptor；
- Domain Knowledge Catalogue 向 MnS Registry / MRDF 注册 Catalogue MnS。

### 3. 可直接用于专利说明书的描述

在一实施例中，KnowledgeFunction 和 Knowledge Base 嵌入 Network Function，并部署在边缘站点。Knowledge Base 保存与该 Network Function、边缘站点、设备或局部网络状态有关的敏感 Knowledge Content。

Embedded KnowledgeFunction 向域级 Knowledge Catalogue 同步选定 Knowledge Descriptor。所同步的 Descriptor 可以不包含完整地理位置、设备标识、故障细节或专有推理规则。消费者基于域级 Catalogue 获得候选知识信息，并通过嵌入式 Knowledge MnS 提交 KnowledgeJob。知识查询和推理在本地执行，仅将决策结果或经过过滤的信息返回消费者。

### 4. 可强调的技术效果

- 缩短知识访问和决策时延；
- 减少边缘与中心之间的数据传输；
- 使敏感 Knowledge Content 保留在设备或站点；
- 在保持本地自治的同时提供统一注册和发现能力；
- 在上层 Catalogue 中仅暴露选定 Descriptor。

---

## 六、四个场景共同的核心发明构思

四个场景的共同点不是具体部署位置，而是三级信息分离：

1. **MnS Registry / MRDF** 保存服务级信息，解决“哪个服务可以提供知识”；
2. **Knowledge Catalogue** 保存知识级 Descriptor，解决“可能存在哪些知识”；
3. **Knowledge Base** 保存 Knowledge Content，解决“知识的实际内容是什么”。

KnowledgeFunction 位于三者之间，负责根据消费者身份、KnowledgeContext、Descriptor、访问策略和披露级别控制 Knowledge Content 的访问。

该三级分离关系可以作为系统独立权利要求的核心结构；四种 deployment scenario 可以作为从属权利要求或说明书中的不同实施例。

## 七、权利要求可进一步限定的特征

- Knowledge Descriptor 与 Knowledge Content 分离保存；
- Descriptor 包含 KnowledgeFunction 的间接引用；
- Knowledge Base 不向 Knowledge MnS Consumer 直接暴露；
- 基于消费者属性生成不同 Descriptor view；
- 对 Context 进行空间或时间粒度粗化；
- 返回知识子图、聚合结果、推理结果或授权投影；
- Catalogue 集中部署而 Base 分布式部署；
- 不同域之间只同步最小化 Descriptor；
- KnowledgeFunction 和 Base 嵌入 Network Function；
- KnowledgeJob 支持同步和异步执行；
- Descriptor 随 Knowledge Content 的版本、有效性或生命周期状态更新。
