## 为什么

Gemini API 提供了[免费](https://ai.google.dev/gemini-api/docs/pricing#free)层级，配额*非常慷慨*，但仍有许多工具只能使用 OpenAI API。

本项目提供了一个免费的、个人使用的 OpenAI 兼容端点。


## 无服务器？

虽然它在云端运行，但不需要服务器维护。
可以轻松部署到各种服务商的免费套餐
（配额足够个人使用）。

> [!提示]
> 在本地运行代理端点也是一个[选项](#本地服务---node-deno-bun)！


## 如何开始

你需要一个 Google 个人 [API 密钥](https://aistudio.google.com/app/api-keys)。

> [!重要]
> 即使你位于[不支持的区域](https://ai.google.dev/gemini-api/docs/available-regions#available_regions)，
> 仍然可以使用 VPN 获取一个。

使用下面的说明将项目部署到其中一个服务商。
你需要在那里设置一个账户。

如果你选择"按钮部署"，你会被引导先 fork 仓库，
这是持续集成（CI）所必需的。


### 使用 Vercel 部署

 [![使用 Vercel 部署](https://vercel.com/button)](https://vercel.com/new/clone?repository-url=https://github.com/PublicAffairs/openai-gemini&repository-name=my-openai-gemini)
- 也可以使用 [cli](https://vercel.com/docs/cli) 部署：
  `vercel deploy`
- 本地服务：`vercel dev`
- Vercel _函数_ [限制](https://vercel.com/docs/functions/limitations)（使用 _Edge_ 运行时）


### 部署到 Netlify

[![部署到 Netlify](https://www.netlify.com/img/deploy/button.svg)](https://app.netlify.com/start/deploy?repository=https://github.com/PublicAffairs/openai-gemini&integrationName=integrationName&integrationSlug=integrationSlug&integrationDescription=integrationDescription)
- 也可以使用 [cli](https://docs.netlify.com/cli/get-started/) 部署：
  `netlify deploy`
- 本地服务：`netlify dev`
- 提供两个不同的 API 基础地址：
  - `/v1`（例如 `/v1/chat/completions` 端点）
    _函数_ [限制](https://docs.netlify.com/build/functions/get-started/#synchronous-function)
  - `/edge/v1`
    _边缘函数_ [限制](https://docs.netlify.com/build/edge-functions/limits/)


### 部署到 Cloudflare

[![部署到 Cloudflare Workers](https://deploy.workers.cloudflare.com/button)](https://deploy.workers.cloudflare.com/?url=https://github.com/PublicAffairs/openai-gemini)
- 也可以手动部署，将 [`src/worker.mjs`](src/worker.mjs) 的内容
  粘贴到 https://workers.cloudflare.com/playground（查看那里的 `部署` 按钮）。
- 也可以使用 [cli](https://developers.cloudflare.com/workers/wrangler/) 部署：
  `wrangler deploy`
- 本地服务：`wrangler dev`
- _Worker_ [限制](https://developers.cloudflare.com/workers/platform/limits/#worker-limits)


### 部署到 Deno

详情见[这里](https://github.com/PublicAffairs/openai-gemini/discussions/19)。


### 本地服务 - 使用 Node、Deno、Bun

仅 Node 需要：`npm install`。

然后 `npm run start` / `npm run start:deno` / `npm run start:bun`。


#### 开发模式（监视源代码更改）

仅 Node 需要：`npm install --include=dev`

然后：`npm run dev` / `npm run dev:deno` / `npm run dev:bun`。


## 如何使用
如果你在浏览器中打开新部署的站点，你只会看到 `404 Not Found` 消息。这是预期的，因为 API 不是为直接浏览器访问设计的。
要使用它，你应该在你的软件设置中输入你的 API 地址和你的 Gemini API 密钥。

> [!注意]
> 并非所有软件工具都允许覆盖 OpenAI 端点，但许多工具都允许
>（不过这些设置可能隐藏得很深）。

通常，你应该按以下格式指定 API 基础地址：
`https://my-super-proxy.vercel.app/v1`

相关字段可能标记为 "_OpenAI 代理_"。
你可能需要在"_高级设置_"或类似部分下查找。
或者，它可能在某些配置文件中（详细信息请查看相关文档）。

对于某些命令行工具，你可能需要设置环境变量，例如：
```sh
OPENAI_BASE_URL="https://my-super-proxy.vercel.app/v1"
```
_..或者_：
```sh
OPENAI_API_BASE="https://my-super-proxy.vercel.app/v1"
```


## 模型

请求使用指定的 [模型]（如果其名称以 "gemini-"、"gemma-" 或 "models/" 开头）。
否则，应用以下默认值：

- `chat/completions`：`gemini-flash-latest`
- `embeddings`：`gemini-embedding-001`

[model]: https://ai.google.dev/gemini-api/docs/models


## 内置工具

要使用 **网络搜索** 工具，请在模型名称后附加 ":search"
（例如，"gemini-2.5-flash:search"）。

注意：`annotations` 消息属性未实现。


## 媒体

按照 OpenAI [规范]支持 [视觉]和[音频]输入。
通过 [`inlineData`](https://ai.google.dev/api/caching#Part) 实现。

[vision]: https://platform.openai.com/docs/guides/images-vision?api-mode=chat&format=url#giving-a-model-images-as-input
[audio]: https://platform.openai.com/docs/guides/audio?example=audio-in&lang=curl#add-audio-to-your-existing-application
[specs]: https://platform.openai.com/docs/api-reference/chat/create


## Gemini 特定功能

Gemini 支持的几个功能在 OpenAI 模型中不可用，
但可以使用 `extra_body` 字段启用。
其中最著名的是 [`thinking_config`](https://ai.google.dev/gemini-api/docs/openai#thinking)。

更多详情，请参阅 [Gemini API 文档](https://ai.google.dev/gemini-api/docs/openai#extra-body)。

---

## 支持的 API 端点和适用参数

- [x] `chat/completions`

  目前，已实现对两个 API 都适用的大多数参数。
  <details>

  - [x] `messages`
      - [x] `content`
      - [x] `role`
          - [x] "system"（=>`system_instruction`）
          - [x] "user"
          - [x] "assistant"
          - [x] "tool"
      - [x] `tool_calls`
  - [x] `model`
  - [x] `frequency_penalty`
  - [ ] `logit_bias`
  - [ ] `logprobs`
  - [ ] `top_logprobs`
  - [x] `max_tokens`、`max_completion_tokens`
  - [x] `n`（`candidateCount` <8，不用于流式）
  - [x] `presence_penalty`
  - [x] `reasoning_effort`
  - [x] `response_format`
      - [x] "json_object"
      - [x] "json_schema"（OpenAPI 3.0 架构对象的选定子集）
      - [x] "text"
  - [x] `seed`
  - [x] `stop`：字符串|数组（`stopSequences` [1,5]）
  - [x] `stream`
  - [x] `stream_options`
      - [x] `include_usage`
  - [x] `temperature`（OpenAI 为 0.0..2.0，但 Gemini 支持无限大）
  - [x] `top_p`
  - [x] `tools`
  - [x] `tool_choice`
  - [ ] `parallel_tool_calls`（在 Gemini 中始终处于活动状态）
  - [x] [`extra_body`](#gemini特定功能)

  </details>
- [ ] `completions`
- [x] `embeddings`
  - [x] `dimensions`
- [x] `models`
