# CodeNexus 代码库关系管理工具需求文档

## 概述
CodeNexus 是一个基于 MCP (Model Context Protocol) 协议的代码库关系管理工具，专为 LLM (大语言模型) 设计，旨在为代码库建立智能的关系图谱。通过结构化标签和关联关系管理，帮助 LLM 更好地理解代码库结构，提供精确的代码分析和建议。

## 术语与概念定义
- **MCP (Model Context Protocol)**: 模型上下文协议，用于AI工具与外部系统的标准化通信
- **文件标签**: 采用 `type:value` 结构的分类标识，如 `category:api`、`status:active`
- **文件注释**: 自由文本形式的文件描述和说明信息，仅用于存储不用于搜索
- **关联关系**: 文件间的依赖、调用或逻辑关系
- **关系图谱**: 基于标签和关联关系构建的代码库可视化结构

## 背景
现代软件项目规模日益庞大，代码库结构复杂，LLM 在理解和分析代码时面临以下挑战：
1. **上下文理解困难**: LLM 缺乏代码库的全局视图和语义化描述
2. **模块关系不清**: 文件间依赖关系对 LLM 不透明，影响分析准确性
3. **知识获取困难**: 开发者的理解和经验无法有效传递给 LLM
4. **分析效率低下**: LLM 需要重复分析相同的代码结构和关系
5. **精确度不足**: 缺乏结构化的元数据导致 LLM 分析结果不够精确

## LLM 使用场景

### 1. 代码理解与分析
- **标签发现**: LLM 首先获取系统中所有可用的标签类型和值，了解项目的分类体系
- **快速定位**: LLM 通过标签快速找到特定类型的文件（如所有API文件、测试文件）
- **依赖分析**: LLM 通过关联关系理解文件间的依赖关系，提供准确的影响分析
- **上下文获取**: LLM 通过注释获取文件的详细上下文信息，提高分析准确性

### 2. 代码重构与优化
- **重构规划**: LLM 基于关联关系分析重构的影响范围
- **代码分类**: LLM 通过标签识别需要重构的文件类型和优先级
- **团队协作**: LLM 通过owner标签了解代码责任归属，提供合适的建议

### 3. 代码审查与质量保证
- **质量评估**: LLM 通过status标签识别需要审查的文件
- **技术栈分析**: LLM 通过tech标签了解项目的技术栈分布
- **测试覆盖**: LLM 通过关联关系分析测试文件与源码的对应关系

### 4. 项目维护与理解
- **快速理解**: LLM 基于标签快速理解项目结构和文件分类
- **关系梳理**: LLM 通过关联关系理解模块间的依赖关系
- **上下文获取**: LLM 获取文件的完整上下文信息进行分析

## 核心功能需求

### 1. 文件标签管理系统

#### 1.1 标签结构设计
- **格式**: 采用 `type:value` 结构化标签
- **灵活性**: type 和 value 都是用户自定义的文本，系统不预设限制
- **建议的类型示例**:
  - `category`: 功能分类 (api, ui, database, auth, util, test, config)
  - `status`: 状态管理 (active, deprecated, needs-review, needs-refactor, experimental)
  - `tech`: 技术栈 (react, nodejs, typescript, rust, python)
  - `owner`: 责任归属 (frontend-team, backend-team, john-doe)
  - `priority`: 优先级 (high, medium, low, critical)
  - `module`: 模块归属 (user-management, payment, notification)
- **用户自由度**: 用户可以创建任意的 type:value 组合，如 `team:mobile`、`complexity:high`、`review-by:alice` 等

#### 1.2 标签操作功能
- **添加标签**: 为文件添加一个或多个标签，支持任意 type:value 格式
- **删除标签**: 移除文件的指定标签
- **修改标签**: 更新文件的标签信息
- **批量标签操作**: 同时为多个文件添加/删除标签
- **标签验证**: 检查标签格式是否符合 type:value 规范

#### 1.3 标签查询功能
- **简单查询**: `category:api` - 查找指定类型的文件
- **组合查询**: `category:api AND status:active` - 多条件组合查询
- **排除查询**: `category:api NOT status:deprecated` - 排除特定条件
- **模糊查询**: `tech:react*` - 支持通配符匹配
- **获取无标签文件**: 查找未添加任何标签的文件
- **标签发现功能**: 获取系统中所有已使用的标签类型和值，帮助LLM了解可用的标签

### 2. 文件注释管理系统

#### 2.1 注释功能
- **添加注释**: 为文件添加详细的文本描述
- **修改注释**: 更新文件的注释内容，覆盖旧内容避免 LLM 混淆
- **删除注释**: 移除文件的注释信息
- **单一版本**: 只维护最新版本的注释，确保 LLM 获取的信息准确无歧义

#### 2.2 注释查询功能
- **获取文件注释**: 查看指定文件的注释信息
- **批量注释查询**: 获取多个文件的注释信息

### 3. 文件关联关系管理

#### 3.1 关联关系设计
- **关联格式**: 关联(目标文件, 关联描述)
- **目标文件**: 被关联的文件路径
- **关联描述**: 自由文本，描述关联关系的性质和用途
- **灵活性**: 用户可以用自然语言描述任意类型的关联关系
- **示例**:
  - 关联("src/api/user.js", "依赖此文件的用户API接口")
  - 关联("tests/user.test.js", "对应的测试文件")
  - 关联("config/database.json", "使用此配置文件连接数据库")
  - 关联("docs/api.md", "相关的API文档")

#### 3.2 关联操作功能
- **添加关联**: 为文件添加关联关系，指定目标文件和关联描述
- **删除关联**: 移除文件的指定关联关系
- **修改关联**: 更新关联关系的描述信息
- **批量关联操作**: 同时为多个文件添加相同的关联关系
- **关联搜索**: 基于关联描述的文本搜索功能

#### 3.3 关联查询功能
- **获取出向关联**: 查看指定文件指向其他文件的关联关系
- **获取入向关联**: 查看指向指定文件的所有关联关系
- **关系图查询**: 获取文件的关系图谱数据
- **关联描述搜索**: 在关联描述中搜索关键词


### 4. 综合查询功能
- **关联+标签查询**: 查找具有特定标签且存在关联关系的文件
- **多维度筛选**: 支持标签和关联关系的任意组合查询



### 5. MCP 接口规范

#### 5.1 工具接口定义
- `add_file_tags`: 为文件添加标签，支持任意 type:value 格式
- `remove_file_tags`: 移除文件标签
- `query_files_by_tags`: 基于标签查询文件
- `get_all_tags`: 获取系统中所有已使用的标签，按类型分组

- `add_file_comment`: 添加文件注释
- `update_file_comment`: 更新文件注释
- `add_file_relation`: 添加文件关联关系
- `remove_file_relation`: 移除文件关联关系
- `query_file_relations`: 查询文件的出向关联关系
- `query_incoming_relations`: 查询文件的入向关联关系
- `get_file_info`: 获取文件完整信息（标签+注释+关联）

#### 5.2 数据格式规范（LLM 友好设计）
- **标签格式**: `["category:api", "status:active", "tech:nodejs"]` - 简洁的字符串数组，便于 LLM 解析
- **标签发现格式**: `{"category": ["api", "ui", "database"], "status": ["active", "deprecated"], "tech": ["nodejs", "react"]}` - 按类型分组的标签值列表
- **关联格式**: `{"target": "path/to/file.js", "description": "依赖此文件的用户API接口"}` - 简化的JSON格式，语义明确
- **查询结果**: 统一的JSON格式，包含文件路径、标签、注释、关联关系，便于 LLM 理解和处理
- **语义化描述**: 所有字段名和值都使用自然语言，避免缩写和编码
- **上下文信息**: 查询结果包含足够的上下文信息，减少 LLM 的推理负担

## 非功能性需求

### 1. LLM 友好性要求
- **响应格式**: 所有响应都采用结构化JSON格式，便于 LLM 解析
- **语义化命名**: 字段名和值使用自然语言，避免技术缩写
- **上下文完整**: 每次响应包含足够的上下文信息，减少 LLM 的额外查询
- **错误信息**: 错误信息使用自然语言描述，便于 LLM 理解和处理
- **批量操作**: 支持批量查询和操作，提高 LLM 的工作效率
- **信息唯一性**: 避免历史版本和重复信息，确保 LLM 获取的信息准确无歧义

### 2. 性能要求
- **查询响应时间**: 单次查询响应时间不超过1秒，确保 LLM 交互流畅
- **批量操作**: 支持同时处理1000个文件的批量操作
- **内存占用**: 工具运行时内存占用不超过100MB
- **并发支持**: 支持 LLM 的并发查询请求

### 3. 可用性要求
- **错误处理**: 提供清晰的错误信息和恢复建议，便于 LLM 自动处理
- **数据一致性**: 确保标签、注释、关联关系的数据一致性
- **备份恢复**: 支持数据的备份和恢复功能
- **渐进式查询**: 支持从粗粒度到细粒度的渐进式查询模式

### 4. 兼容性要求
- **跨平台**: 支持Windows、macOS、Linux操作系统
- **MCP协议**: 完全兼容MCP协议规范
- **文件系统**: 支持各种常见的文件系统格式
- **LLM兼容**: 与主流 LLM 工具（Claude、GPT、Gemini等）兼容

## 验收标准

### 1. 功能验收标准
- [ ] 所有标签操作功能正常工作，支持type:value格式
- [ ] 标签发现功能能够正确返回所有已使用的标签类型和值
- [ ] 注释系统支持添加、更新和查看功能
- [ ] 关联关系管理支持所有定义的关系类型
- [ ] 复合查询功能能够正确处理复杂的查询条件
- [ ] MCP接口完全符合协议规范，能够与主流 LLM 工具正常通信
- [ ] 响应格式对 LLM 友好，包含完整的上下文信息
- [ ] 所有数据只保留最新版本，避免历史信息对 LLM 造成混淆

### 2. 性能验收标准
- [ ] 在包含10,000个文件的代码库中，查询响应时间不超过1秒
- [ ] 批量操作能够稳定处理1000个文件
- [ ] 工具运行时内存占用控制在100MB以内

### 3. 质量验收标准
- [ ] 代码覆盖率达到90%以上
- [ ] 所有功能都有对应的单元测试和集成测试
- [ ] 错误处理机制完善，用户体验良好
- [ ] 文档完整，包含用户手册和开发者文档

## 风险与挑战

### 1. 技术风险
- **数据一致性**: 多文件操作时可能出现数据不一致
- **性能瓶颈**: 大型代码库的查询性能优化
- **MCP协议兼容**: 确保与不同AI工具的兼容性

### 2. LLM 使用风险
- **理解偏差**: LLM 可能误解标签或注释的含义
- **数据质量**: 标签和注释质量直接影响 LLM 的分析准确性
- **上下文限制**: 大型代码库的信息可能超出 LLM 的上下文窗口

### 3. 应对措施
- 实施严格的数据验证和事务处理机制
- 采用高效的索引和缓存策略优化性能
- 设计语义化的标签和注释规范，提高 LLM 理解准确性
- 提供分层查询机制，支持 LLM 的渐进式信息获取
- 实现智能的上下文压缩，确保关键信息在 LLM 上下文窗口内
