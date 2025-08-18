
- [Cursor – Using CLI](https://docs.cursor.com/en/cli/using)
- [Fetching Title#nql9](https://docs.cursor.com/zh-Hant/tools/mcp)


- mcp and rules -> use original IDE setting


handy command:
```shell

Cursor Agent


cursor resume

cursor ls

使用 `-p` 或 `--print` 以非互動模式運行 Agent。這會將回應列印到控制台






```


|Command|Description|
|---|---|
|`/model <model>`|Set or list models|
|`/auto-run [state]`|Toggle auto-run (default) or set [on\|off\|status]|
|`/new-chat`|Start a new chat session|
|`/vim`|Toggle Vim keys|
|`/help [command]`|Show help (/help [cmd])|
|`/feedback <message>`|Share feedback with the team|
|`/resume <chat>`|Resume a previous chat by folder name|
|`/copy-req-id`|Copy last request ID|
|`/logout`|Sign out from Cursor|
|`/quit`|Exit|



|Option|Description|
|---|---|
|`-V, --version`|Output the version number|
|`-a, --api-key <key>`|API key for authentication (can also use CURSOR_API_KEY env var)|
|`-p`|Print responses to console (for scripts or non-interactive use)|
|`--output-format <format>`|Output format (only works with `-p`): `text`, `json`, or `stream-json` (default: `stream-json`)|
|`--fullscreen`|Enable fullscreen mode|
|`--resume [chatId]`|Resume a chat session|
|`-m, --model <model>`|Model to use (e.g., sonnet-4, sonnet-4-thinking, gpt-5)|
|`-f, --force`|Force allow commands unless explicitly denied|
|`-h, --help`|Display help for command|


Commands

|Command|Description|Usage|
|---|---|---|
|`login`|Authenticate with Cursor|`cursor-agent login`|
|`logout`|Sign out and clear stored authentication|`cursor-agent logout`|
|`status`|Check authentication status|`cursor-agent status`|
|`update\|upgrade`|Update Cursor Agent to the latest version|`cursor-agent update` or `cursor-agent upgrade`|
|`ls`|Resume a chat session|`cursor-agent ls`|
|`resume`|Resume the latest chat session|`cursor-agent resume`|
|`help [command]`|Display help for command|`cursor-agent help [command]`|

When no command is specified, Cursor Agent starts in interactive chat mode by default.



Arguments

When starting in chat mode (default behavior), you can provide an initial prompt:**Arguments:**

- `prompt` — Initial prompt for the agent


All commands support the global `-h, --help` option to display command-specific help.