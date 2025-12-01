# Potplayer Ollama 实时翻译插件

这是一个为 Potplayer 开发的插件，可以使用 Ollama 进行实时字幕翻译。

- Ollama 原生 api 支持，新增对 gpt-oss 的思考强度支持
- [功能特性](#功能特性)
- 测试支持到 ollama 0.13.0 版本

<div align="center">
  <strong>简体中文</strong> | <a href="https://github.com/Nuo27/Potplayer-Ollama-Translate/blob/master/README_EN.md">English</a>
</div>

## 目录

- [Potplayer Ollama 实时翻译插件](#potplayer-ollama-实时翻译插件)
  - [目录](#目录)
  - [功能特性](#功能特性)
  - [使用方法](#使用方法)
  - [注意事项](#注意事项)
  - [自定义配置](#自定义配置)
  - [性能表现](#性能表现)
  - [参考资料](#参考资料)
  - [许可证](#许可证)

## 功能特性

- Ollama 原生 api 支持，新增对 gpt-oss 的思考强度支持
- 支持推理模型的思考功能，包括 qwen3, deepsseek-r1, gpt-oss 等
- 可以配置的上文历史
- 自定义模型参数配置
- 全新的翻译提示词和翻译策略

## 使用方法

1. 下载 `.as` 和 `.ico` 文件，将它们放置到 Potplayer 安装目录下的 `...\DAUM\PotPlayer\Extension\Subtitle\Translate` 文件夹中。
2. 打开 `.as` 文件，修改 `DEFAULT_MODEL_NAME` 为目标模型名称。或者也可以保持默认设置，稍后在扩展设置中配置。
3. 如果需要，可以自由调整**提示词**、**模型配置**和上下文历史大小。
4. 如果使用推理模型，请确保设置推理模型配置。`强烈建议关闭推理功能。`
5. 运行 PotPlayer，右键打开设置 / 按 F5。然后前往 `字幕 -> 字幕翻译 -> 在线字幕翻译设置`，选择并启用插件。
6. 在扩展设置中，如果想使用不同于默认的模型，请设置模型名称。由于使用的是 ollama，不需要 API 密钥。
7. 完成

## 注意事项

- **请确保将模型和 ollama 更新到 >= 0.9.0 版本** ~~以使用 ollama 的原生思考支持。qwen3 的思考提示词尚未移除，因为在我的测试中它并没有真正起作用~~
- v2.1 版本的插件使用 ollama 的原生 api，并完全支持 ollama 的原生思考参数，基于 ollama 0.13.0 测试。
- 旧版本的插件仍然使用 OpenAI compatibility 的 api，仍支持对模型的自定义参数，但是原生的思考是不可用的。
- ~~使用旧模板和功能的 Qwen3、Deepseek-r1 以及 ollama <0.9.0 版本是**兼容的**，但其他模型可能不兼容。可以在 `ModelConfig` 下手动添加它们的思考标签和 `bool` 值，在 `options` 字段中添加项目~~
- 插件提供了一些**提示词**，请根据翻译质量自行调整。
- 请确保使用**支持多语言任务**的模型。
- 通常来说，推理/思考（thinking）**应该关闭**，它会显著影响翻译速度，并且简单的翻译任务也不怎么需要推理。
- 非常推荐使用**Instruct** 模型，如`qwen3:30b-a3b-instruct-2507-q4_K_M`，测试的模型可以参考[性能表现](#性能表现)部分。

## 自定义配置

**模型选择**
| 变量 | 描述 |
|--------|-------------|
| `DEFAULT_MODEL_NAME` | 默认模型名称（默认值：`"qwen3:30b-a3b-instruct-2507-q4_K_M"`）。**如果没有在 Potplayer 设置中配置模型，将使用该模型** |

**模型配置**  
| 变量 | 示例值 | 描述 |
|--------|-------------|-------------|
| `temperature` | `0.1 - 0.3` | 较低的值使输出更确定性，更少创造性。**如果想要改述翻译，可能需要稍微增加此值**|
| `topP` | `0.8 - 0.95` | 只考虑累计概率 ≥ topP 的最小顶级 token 集合。|
|`topK`| `20-40` | 在每个生成步骤中只考虑最可能的前 K 个 token。|
| `minP` | `0.01 - 0.1` | 过滤概率低于 minP 的 token，即使它们在 `topP` 或 `topK` 中|
|`repeatPenalty` | `1.0 - 2.0` | 对已生成的 token 进行惩罚，阻止重复|
|`maxTokens` | `1024-2048` | 可生成的最大 token 数量。|

> 可根据需要添加其他参数，但通常来说你只需要调整温度和 topP。请确保相应更新 `GetActiveParams` 方法。

**推理配置**  
| 变量 | 示例值 | 描述 |
|--------|-------------|-------------|
| `enableThinking` | `false` | 激活模型中的推理功能。强烈建议`关闭` |
| `thinkStrength` | `"low"` `"medium"` `"high"` `""` | 调整 gpt-oss 模型的思考强度。仅适用于 gpt-oss 且 gpt-oss 模型的思考不能被关闭，如果`enableThinking`被设置成`false`，会自动使用`"low"`强度。默认情况请留空。|

**上文历史**  
| 变量 | 示例值 | 描述 |
|--------|-------------|-------------|
| `enabled` | `true` | 是否使用上文历史进行翻译 |
| `contextCount` | `10` | 包含在上文中的最近句子数量
| `maxSize` | `50` | 历史记录条目的最大数量 |

> 如果显著增加条目数量，由于上下文大小增加，响应时间也可能显著增加。还需要相应调整 token 数量。

**提示词**  
插件提供了一些提示词模板供你使用，你可以随意修改以适应你的模型。
| 提示词 | 描述 |
| ---- | ------------- |
| `SYSTEM_PROMPT_BASE` | 基础系统提示词，和上文历史提示词前缀将合并形成模型的最终系统提示词。 |
| `SYSTEM_PROMPT_END` | 位于系统提示词的末尾，用于指定用户任务。 |
| `USER_PROMPT_BASE` | 用户提示词 |
| `CONTEXT_PROMPT` | 上文历史提示词前缀，用于指定上文历史，接在基础系统提示词后。 |
| `SYSTEM_PROMPT_OLD` | 上个版本的系统提示词 |
| `SYSTEM_PROMPT_BASIC` | 非常简单的系统提示词，仅在你的模型可能接受不了高上下文大小或者你的模型跟随能力比较弱的时候使用， |
| `SYSTEM_PROMPT_BASIC_OLD_TWO_STEP` | 上个版本的两步翻译策略系统提示词 |

| 变量              | 默认值               | 描述                                         |
| ----------------- | -------------------- | -------------------------------------------- |
| `userPrompt`      | `USER_PROMPT_BASE`   | 使用 `USER_PROMPT_BASE` 作为用户提示词       |
| `systemPrompt`    | `SYSTEM_PROMPT_BASE` | 使用 `SYSTEM_PROMPT_BASE` 作为系统提示词     |
| `systemPromptEnd` | `SYSTEM_PROMPT_END`  | 使用 `SYSTEM_PROMPT_END`作为系统提示词的结尾 |

> **注意：** 请确保你的模型能够处理这些提示词，否则可能会导致翻译结果不准确或无法正常工作。

## 性能表现

**支持模型：**

- 新增支持 gpt-oss
- 现在插件应该支持所有 ollama 官方模型。
- 也包括 hugginface 上的大部分模型框架，包括 qwen3moe，olmo granitehybrid apertus 等

只要模型是原生 ollama api 支持的，插件应该都可以正常运行

> 意思是只要你的模型能在 ollama app 或 Ollama cli 里运行，插件就是支持的。

**推荐**

- qwen3:30B-A3B-Instruct-2507-Q3_K_S
- gpt-oss:20b
- gemma3:12b/gemma3n:e4b
- 对于配置不高的用户，可以考虑 qwen3:4b-instruct

> 请测试你的 token/s，响应过慢的模型可能会导致翻译延迟或失败。根据你的硬件配置和需求进行配置，以确保最佳性能。

## 参考资料

- 受 [PotPlayer_ollama_Translate](https://github.com/yxyxyz6/PotPlayer_ollama_Translate) v1 版本启发并在此基础上进一步开发。
- 使用 [Angel Script](https://www.angelcode.com/angelscript/) 编写。
- 使用 [Ollama](https://ollama.com/) 提供 LLM 和 API 支持。

## 许可证

MIT 许可证
