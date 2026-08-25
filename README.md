

<p align="center">
  <a href="https://opencode.ai/">
    <img
      width="234"
      height="42"
      alt="OpenCode"
      src="https://github.com/user-attachments/assets/9c5d41a3-c331-4676-81a3-916cef69441b"
      style="vertical-align: middle;"
    />
  </a>

  <span style="font-size: 2rem; margin: 0 14px; vertical-align: middle;">
    +
  </span>

  <a href="https://agentrouter.org/">
    <img
      src="https://github.com/user-attachments/assets/5a4f6e27-bf2e-4cbe-82a6-3837cb9f2a4c"
      height="70"
      alt="AgentRouter"
      style="vertical-align: middle;"
    />
  </a>
</p>

<h1 align="center">AgentRouter for OpenCode</h1>

<p align="center">
  <b>Use AgentRouter models in OpenCode</b>
  <br />
  via a lightweight, local OpenAI-compatible proxy.
</p>

<p align="center">
  <a href="https://nodejs.org/"><img src="https://img.shields.io/badge/node-%3E%3D18-339933?logo=node.js" alt="Node" /></a>
  <a href="https://opencode.ai"><img src="https://img.shields.io/badge/opencode-ready-6366f1" alt="OpenCode" /></a>
  <a href="https://agentrouter.org"><img src="https://img.shields.io/badge/powered%20by-agentrouter-ff6b6b" alt="AgentRouter" /></a>
  <a href="./LICENSE"><img src="https://img.shields.io/badge/license-MIT-blue" alt="License" /></a>
</p>

---

## Table of Contents

- [How it works](#how-it-works)
- [Setup](#setup)
- [Usage](#usage)
- [Project structure](#project-structure)
- [Configuration](#configuration)
- [Customization](#customization)
- [License](#license)

---
<img width="1439" height="801" alt="Screenshot 2026-06-07 113032" src="https://github.com/user-attachments/assets/913c63a7-7f7f-496e-98e0-9193ab08731c" />


---
## How it works

AgentRouter offers an OpenAI-compatible API, but OpenCode needs providers registered through its `@ai-sdk/openai-compatible` adapter. This proxy bridges the two:

```
┌──────────┐     OpenAI-compat      ┌──────────────┐     AgentRouter API     ┌─────────────┐
│ OpenCode │  ──────────────────>   │ Local Proxy  │  ───────────────────>   │ AgentRouter │
│          │  <──────────────────   │  :4000       │  <───────────────────   │             │
└──────────┘    SSE stream back     └──────────────┘      stream relay       └─────────────┘
```

1. **Proxy** starts locally and exposes an OpenAI-compatible REST API
2. **OpenCode** is configured with `@ai-sdk/openai-compatible` pointing to the proxy
3. Chat requests are streamed through the proxy → AgentRouter → back to OpenCode

---
## Setup

### Prerequisites

- [Node.js](https://nodejs.org/) 18 or later
- An API key from [AgentRouter](https://agentrouter.org) (free tier available)

### 1. Clone or download the files

Place both `agenrouter_server.js` and `opencode.jsonc` in the same directory — preferably your OpenCode config directory (`C:\Users\<you>\.config\opencode\`). Keeping them together makes management easier.

### 2. Install dependencies

```bash
npm install express cors openai
```

### 3. Configure your API key

Open `agenrouter_server.js` and replace the placeholder API key on line 11:

```js
apiKey: "sk-your-actual-key-here",
```

### 4. Start the proxy

```bash
node agenrouter_server.js
```

Expected output:

```
Proxy running on http://127.0.0.1:4000
```

### 5. Configure OpenCode

Place the `opencode.jsonc` file in your OpenCode config directory (or keep it there if you already placed both files together in step 1):

| Platform | Config path |
|----------|-------------|
| Windows | `C:\Users\<you>\.config\opencode\opencode.jsonc` |
| macOS / Linux | `~/.opencode/opencode.jsonc` |

You can also merge the `provider` section into your existing `opencode.jsonc`.

---

## Usage

Once everything is running, select **AgentRouter** as your provider in OpenCode and pick one of the available models: **deepseek v4 pro**, **deepseek v4 flash**, or **glm 5.1**.

### Verify the proxy

```bash
curl http://127.0.0.1:4000/
```

```json
{ "status": "ok", "message": "AgentRouter Roo Proxy Running" }
```

```bash
curl http://127.0.0.1:4000/v1/models
```

```json
{ "object": "list", "data": [{ "id": "deepseek-v4-pro", ... }] }
```

---

## Project structure

```
├── agenrouter_server.js   # Proxy server (the core)
├── opencode.jsonc         # OpenCode provider configuration
├── LICENSE
└── README.md
```

---

## Configuration

### `agenrouter_server.js`

| Variable | Location | Description |
|----------|----------|-------------|
| `apiKey` | Line 11 | Your AgentRouter API key **(required)** |
| `PORT` | Line 93 | Port to run the proxy on (default: `4000`) |
| `baseURL` | Line 12 | AgentRouter API base URL |

### `opencode.jsonc`

Registers a custom provider named `agentrouter` using OpenCode's `@ai-sdk/openai-compatible` adapter. It connects to `http://127.0.0.1:4000/v1` and exposes the following models:

| Model ID | Display Name |
|----------|-------------|
| `deepseek-v4-pro` | deepseek v4 pro |
| `deepseek-v4-flash` | deepseek v4 flash |
| `glm-5.1` | glm 5.1 |

---

## Customization

### Change the proxy port

Only needed if port **4000** is already in use. Edit line 93 in `agenrouter_server.js`:

```js
const PORT = 5000; // change to any available port
```

Then update the `baseURL` in `opencode.jsonc` to match the new port.

### Point to a different API base URL

If AgentRouter changes their endpoint, update `agenrouter_server.js:12`:

```js
baseURL: "https://agentrouter.org/v1",
```

---

## Contact

- **Telegram:** [@Jvlock](https://t.me/Jvlock)
- **Email:** [faresnoser0@gmail.com](mailto:faresnoser0@gmail.com)

Feel free to reach out for questions, suggestions, or feedback.

---

## License

[MIT](./LICENSE)
