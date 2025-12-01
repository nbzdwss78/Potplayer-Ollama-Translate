# Potplayer Live Translate Plugin via Ollama

This is a plugin for Potplayer that allows real-time subtitle translation using Ollama.

- Native Ollama API support, with added thinking-strength support for gpt-oss
- [Features](#features)
- Tested work with ollama 0.13.0

<div align="center">
  <a href="https://github.com/Nuo27/Potplayer-Ollama-Translate/blob/master/README.md">简体中文</a> | <strong>English</strong>
</div>

## Table of Contents

- [Potplayer Live Translate Plugin via Ollama](#potplayer-live-translate-plugin-via-ollama)
  - [Table of Contents](#table-of-contents)
  - [Features](#features)
  - [Usage](#usage)
  - [NOTES](#notes)
  - [Customization](#customization)
  - [Performance](#performance)
  - [References](#references)
  - [License](#license)

## Features

- Native Ollama API support, with added thinking-strength support for gpt-oss
- Supports reasoning/thinking features for models like qwen3, deepseek-r1, gpt-oss, etc.
- Configurable context history
- Customizable model parameter settings
- New translation prompts and strategies

## Usage

1. Download the `.as` and `.ico` files put them to your Potplayer's installation directory under `...\DAUM\PotPlayer\Extension\Subtitle\Translate` folder.
2. Open the `.as` file and modify `DEFAULT_MODEL_NAME` to your target model name. Or you can leave it and set it up in the extension settings later.
3. Feel free to twerk around with the **prompts**, **model configuration** and context history size if you want to.
4. Make sure to set up reasoning model configuration if you are using a reasoning model. `Reasoning is highly recommended to be turned off.`
5. Run PotPlayer, right click and open up settings / f5. Then go to `Subtitles -> Subtitle Translation -> Online Subtitle Translation Settings`, select and enable the plugin.
6. In the extension settings, set up your model name if you want to use a different one than the default. You wont need the API key since its for ollama
7. All done. Enjoy live translation!

## NOTES

- **Please ensure the model and Ollama are updated to version >= 0.9.0** ~~to use Ollama’s native thinking support. The thinking prompt for qwen3 has not been removed yet because in my tests it didn’t really work..~~
- Version v2.1 of the plugin uses the native Ollama API and fully supports Ollama’s thinking parameters. It has been tested under ollama 0.13.0.
- Older plugin versions still use the OpenAI-compatible API. Custom parameters remain supported, but native thinking is not available.
- ~~Qwen3, Deepseek-r1 with old template & capabilities and ollama <0.9.0 are **compatible** but other models might not compatible and you can manually add their think tags and `bool` value under `ModelConfig` to add a item in `options` field.~~
- The plugin provides several prompt templates; adjust them as needed based on translation quality.
- Ensure you use a model that supports multilingual tasks.
- Generally, reasoning/thinking should be turned off because it significantly slows down translation and is usually unnecessary for simple translation tasks.
- Highly recommended to use Instruct models, such as `qwen3:30b-a3b-instruct-2507-q4_K_M`
- Tested models can be found in [Performance](#performance)

## Customization

**Model Selection**
| Variable | Description |
| -------- | ----------- |
| `DEFAULT_MODEL_NAME` | Default model name (default: `"qwen3:30b-a3b-instruct-2507-q4_K_M"`). **Used when no model is configured in Potplayer.** |

**Model Configuration**  
| Variable | Example Value | Description |
| -------- | ------------- | ----------- |
| `temperature` | `0.1 - 0.3` | Lower values give more deterministic results. Slightly increase for paraphrased translation. |
| `topP` | `0.8 - 0.95` | Considers only token sets whose cumulative probability ≥ topP. |
| `topK` | `20-40` | Considers the top K most probable tokens at each step. |
| `minP` | `0.01 - 0.1` | Filters tokens below this probability, even if included in topP/topK. |
| `repeatPenalty` | `1.0 - 2.0` | Penalizes repeated tokens to reduce duplication. |
| `maxTokens` | `1024-2048` | Max number of tokens generated. |

> You can add more parameters if needed, but usually only temperature and topP require adjustment. Make sure to update the `GetActiveParams` method accordingly

**Reasoning/Thinking Configuration**  
| Variable | Example Value | Description |
| -------- | ------------- | ----------- |
| `enableThinking` | `false` | Enables reasoning mode. Strongly recommended to keep this off. |
| `thinkStrength` | `"low"` `"medium"` `"high"` `""` | Thinking strength for gpt-oss. Only applies to gpt-oss. If `enableThinking` = false, `low` is applied automatically. |

**Context History**  
| Variable | Recommended Value | Description |
|--------|-------------|-------------|
| `enabled` | `true` | Whether to use context history for translation |
| `contextCount` | `10` | Number of recent sentences to include in the context
| `maxSize` | `50` | Maximum number of history entries |

> if you increase the entries significantly, the response time might also increase significantly due to the larger context size. and you got to adjust tokens as well.

**Prompts**  
The plugin provides several prompt templates that can be freely customized.
| Prompt | Description |
|--------|-------------|
| `SYSTEM_PROMPT_BASE` | Base system prompt, combined with context prefix to form final system prompt. |
| `SYSTEM_PROMPT_END` | Appended to end of system prompt to specify user task. |
| `USER_PROMPT_BASE` | Base user prompt. |
| `CONTEXT_PROMPT` | Context prefix specifying history. |
| `SYSTEM_PROMPT_OLD` | Prompt used in previous version. |
| `SYSTEM_PROMPT_BASIC` | Simplified system prompt for weaker models or low context tolerance. |
| `SYSTEM_PROMPT_BASIC_OLD_TWO_STEP` | Two-step translation strategy from previous version. |

| Variable          | Default              | Description                              |
| ----------------- | -------------------- | ---------------------------------------- |
| `userPrompt`      | `USER_PROMPT_BASE`   | Uses USER_PROMPT_BASE as user prompt     |
| `systemPrompt`    | `SYSTEM_PROMPT_BASE` | Uses SYSTEM_PROMPT_BASE as system prompt |
| `systemPromptEnd` | `SYSTEM_PROMPT_END`  | Appended at end of system prompt         |

> **Note**: Ensure your model can handle these prompts to avoid inaccurate or failed translations.

## Performance

**Supported Models:**

- Newly added support for gpt-oss
- and the plugin should now support all official Ollama models
- including most models in huggingface as long as the model is officially supported by ollama

> this means any model should work as long as it runs in Ollama app or through ollama cli

**Recommendations**

- qwen3:30B-A3B-Instruct-2507-Q3_K_S
- gpt-oss:20b
- gemma3:12b / gemma3n:e4b
- for lower-end users, consider qwen3:4b-instruct

> Please test your tokens/s. Slow models may delay or fail translation.

## References

- Inspired by [PotPlayer_ollama_Translate](https://github.com/yxyxyz6/PotPlayer_ollama_Translate) and further built upon.
- Written in [Angel Script](https://www.angelcode.com/angelscript/).
- [Ollama](https://ollama.com/) for LLMs and API usage.

## License

MIT License
