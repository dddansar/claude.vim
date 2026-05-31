# Credits

This plugin is based on [pasky/claude.vim](https://github.com/pasky/claude.vim).

Significant modifications have been made to the original code, and although originally just for Anthropic's Claude AI, this plugin now also supports OpenAI/ChatGPT, Google's Gemini, and even locally running your own AI such as Qwen with Ollama.

Here is a summary of the modifications from pasky/claude.vim:
- Option to disable chat folding
- Fixed various errors
- Option to increase context window from 200k to 1m
- Fixed accuracy of token usage
- Improved chat readability
- Added option to disable tools
- Improved tool efficiency (less token waste)
- Added batch option
- Added support for Ollama/Qwen AI
- Added support for OpenAI/ChatGPT and Gemini AIs
- Added support for Claude caching
- Added support for Claude Adaptive thinking
- Can scroll up as the AI types

For a full list of modification from pasky/claude.vim see the modifications section at the end of this README.

# Programming with AI API in Vim/Neovim

This Vim plugin integrates Claude (and other AIs) into your Vim workflow, where you can chat about all your currently opened Vim buffers. Chat about what to build or how to debug problems, and the AIs will offer their suggestions while seeing your actual code. The AIs are also capable of making the modifications themselves if you have tools enabled.

This plugin is NOT:
* "code completion" like Github Copilot or Codeium.
  This plugin rather provides a chat / instruction centric interface.
* CLI coding framework or AI Agents.
  You may want to agree on design decisions before Claude writes code.
  And it is going to need feedback and change review in order to be helpful.

This plugin will give you a partner who will one-shot new features in your codebase:

https://github.com/pasky/claude.vim/assets/18439/73ffcaac-d5b4-4508-b9fa-077c189d2c93

You can let it refactor your code if it's a bit messy, and have an ongoing discussion about it:

https://github.com/pasky/claude.vim/assets/18439/625060ca-600f-4774-adbe-ec93f94a30e9

You can ask it to modify or extend just a selected piece of your code:

https://github.com/pasky/claude.vim/assets/18439/71544b57-e87d-4dd4-a7e6-4051fa080d18

![implement diff example](https://raw.githubusercontent.com/dddansar/vimrc/assets/implement.png)

It can use Claude Tools interface - it will open files and execute vim commands as needed.

![Claude realizes it needs to open another file, opens it, and then executes a series of vim commands to uppercase its first line.](https://pbs.twimg.com/media/GSjaXLnW8AEuFE_?format=jpg&name=4096x4096)

![open_web tool example](https://raw.githubusercontent.com/dddansar/vimrc/assets/open_web.png)

It can also (with your case-by-case consent) evaluate Python expression when figuring
out what you asked:

![When asked for refactoring suggestions, Claude evaluates short Python snippets to get basic source code stats, and even autonomously iterates the Python execution when one of the snippets fails.](https://pbs.twimg.com/media/GSXpOY2WsAI6aFt?format=jpg&name=4096x4096)

And it can execute complex tasks by first reading documentation, then cloning git respositories, browsing the directory tree, etc.

![Based on a short sentence, Claude reads documentation for another software project, clones the repo, installs dependencies etc.](https://pbs.twimg.com/media/GSjasfZXoAAvtKs?format=jpg&name=4096x4096)

Actually, the current version can execute also shell script. And it can help you also with sysadmin tasks, not just coding.

![Claude is asked to diagnose a RAID1 after disk replacement, so it just runs the appropriate commands using the shell tool.](https://pbs.twimg.com/media/GTc9x4nWoAAZK0M?format=jpg&name=4096x4096)

And ultimately, Claude.vim can act as pretty much a full text terminal replacement of Claude.ai or ChatGPT. And it will search the web if it doesn't know something.

![Claude is asked for a simple medical advice. It searches the web for the germ, then summarizes the results.](https://pbs.twimg.com/media/GTWwKLPWIAAEPub?format=jpg&name=medium)

## Now also supports additional AIs:

In addition to claude AIs, the plugin now also supports OpenAI/ChatGPT, Google's Gemini, and even locally running your own AI such as Qwen with Ollama

![open_web tool example](https://raw.githubusercontent.com/dddansar/vimrc/assets/sonnet%20vs%20gpt.png)

----

Sonnet 4.6 is not yet good enough to completely autonomously perform complex tasks. This is why you can chat with it, review and reject its changes and tool execution attempts, etc. You still do the "hard thinking" and decide and tell it *what* to do.

That said, about 95% of the original Pasky's code of this plugin has been written by Claude Sonnet 3.5 (and similarly about 95% of the modifications/additions to the original plugin have been written by Claude Sonnet 4.6).

**NOTE: This is early alpha software.** It may further evolve... and not just in backwards compatible way.

## Installation

First, install using your favourite package manager, or use Vim's built-in package support.

Vim:

```bash
mkdir -p ~/.vim/pack/pasky/start
cd ~/.vim/pack/pasky/start
git clone https://github.com/dddansar/claude.vim.git
```

Neovim:

```bash
mkdir -p ~/.config/nvim/pack/pasky/start
cd ~/.config/nvim/pack/pasky/start
git clone https://github.com/dddansar/claude.vim.git
```

To allow web access, you need to get a free API key from https://brave.com/search/api/ and set g:ai_web_search_api_key to the key. I tried using elinks and other methods but it seems search engines are now just blocking such accesses and I've even seen CAPTCHA requests...


## Configuration

Obtain your Claude API key by signing up at https://platform.claude.com/
You can also get ChatGPT and Gemini APIs if you want to test those AIs out. As of this writing you can test ChatGPT and Gemini for free for a limited number of tokens until the limit is reached. Lite models such as gpt-5.4-mini and gemini-2.5-flash-lite, seem to allow more free usage than the non-lite versions. Claude also has a free tier but not for the API.

**NOTE: This is a cloud service that costs actual money based on the amount of tokens consumed and produced. Be careful when working with big content, observe your usage / billing dashboard on Anthropic etc.**

(You can also use AWS Bedrock as your Claude provider instead - in that case, set `let g:claude_use_bedrock = 1`.)

Here is a list of configuration settings you can set in your .vimrc:

```vim
"=================================== AI API ===================================
"------------------------------------------------------------------------------
" NOTE: You can get an AI API (Claude Sonnet/Opus/Haiku, Google's Gemini,
" OpenAI/ChatGPT, Ollama/Qwen) to work with Vim/GVim/NeoVim!!!
" 1) Grab the AI plugin and place in your .vim/pack/.../start/ folder.
" Although initially intended just for Claude AIs, I made modifications to
" Pasky's Claude plugin to address bugs I was seeing and to add support for
" additional AIs that you can find in:
"    .vim/pack/pasky/start/claude.vim/plugin/claude.vim
" 2) Get an API key from https://platform.claude.com or from one of the other
" AI platforms (NOTE: Gemini and ChatGPT will let you use a limited number of
" tokens for free if you just want to test it out...)
" 3a) Add API key in g:claude_api_key
" let g:claude_api_key='add_api_key_here'
" OR
" 3b) add to your .bash_aliases file:
" export API_KEY=add_api_key_here
" and use the following line to get the value from API_KEY
let g:claude_api_key=$CLAUDE_API_KEY
let g:gemini_api_key=$GEMINI_API_KEY
let g:openai_api_key=$GPT_API_KEY
" 4) Set the model to use in g:ai_model. Defaults to claude-sonnet-4-6
" 5) To confirm it works, open a new Vim window, press <leader>cc
" (g:ai_map_open_chat) to open a Claude prompt, type your question or command
" in the prompt, press ctrl-] to send you question or command, and the AI
" should start replying in the prompt window.
"------------------------------------------------------------------------------


" Settings for all AI models:
"------------------------------------------------------------------------------
" Open a Chat to start chatting with AI
let g:ai_map_open_chat = "<leader>cc"
" Once you type your question or command in the chat window, you can use this
" mapping to send the message. <C-]> only affects AI window, not tags jumping.
let g:ai_map_send_chat_message = "<c-]>"
" The implement key sends just the selection instead of the entire file. May
" result in a vimdiff operation.
let g:ai_map_implement = "<leader>ci"
" Cancel the response at any time.
let g:ai_map_cancel_response = "<c-c>"
" If set, AI will not add indentation to it's answers.
let g:ai_no_indent = 1
" Allow AI to give longer responses (max = 64k for Claude 4.6)
let g:ai_max_output_tokens = 64000
" Turns tools off to prevent AI from editing files, opening new files,
" searching the web... This will save cost as AI won't send 5000+ lines of code
" or documentation from a file or website it decided to open...
" I usually just turn tools on manually if/when I need them...
let g:ai_enable_tool_use = 0
if g:ai_enable_tool_use == 0
   " let g:ai_tools_list = ""
   let g:ai_tools_list = []
endif
" API key used for the web search tool. You can get a free api key from
" https://brave.com/search/api/
let g:ai_web_search_api_key=$BRAVE_API_KEY
"------------------------------------------------------------------------------


" Settings for Claude models:
"------------------------------------------------------------------------------
" Increase input token limit from 200k to 1m tokens
" NOTE: Using 1m tokens can easily increase costs. To decrease costs, ask
" question in small (or even empty) files and all only keep the relevant
" code/text to the question.
let g:claude_use_1m_context = 0
" Manually save (append) history to ~/claude_history.txt with <leader>cs
nnoremap <leader>cs :w >> ~/claude_history.txt<cr>
" NOTE: You can see all the current session's token usages with :messages.
" Enable batch mode. Results come back later (most complete within an hour, results are guaranteed within 24 hours). Uses a polling mechanism to retrieve results (every 30 seconds by default). No tool use during batch. A 50% cost discount is applied during batch mode.
let g:claude_batch_api = 0
" Prompt caching: 0 = disabled, 1 = 5-minute TTL, 2 = 1-hour TTL
" Caching reduces costs by reusing the system prompt and conversation history
" across API calls. 5-minute TTL (default): cache write tokens cost 1.25× base
" input price; cache read tokens cost 0.1× (10%). 1-hour TTL (extended): cache
" write tokens cost 2× base input price; still 0.1× for reads
" NOTE: caching only activates when the cached prefix meets the model's
" minimum token threshold (typically 1024–4096 tokens depending on model).
" NOTE: The plugin sends: system prompt -> buffer contents -> message history.
"       - The system prompt is very stable - it'll cache on the first call and
"       hit every subsequent call in the session. This is always a win.
"       - The buffer contents are prepended to the system prompt as one block.
"       If you edit a file between messages, that block changes and busts the
"       cache for everything downstream.
"       - The message history grows with every turn. The plugin puts
"       cache_control on the last user message, which means the cache point
"       moves forward with each exchange - the whole prior conversation gets
"       cached and the model only pays full price for the new message.
" The API only allows 4 cache breakpoints per request total. We're already
" using one on the last user message (conversation history). So you have 3 left
" to distribute across system prompt + buffers. If you have more than 3 files
" open, you'd need to group the least-edited ones together into a single block
" without a breakpoint, and put breakpoints only on the last 3 or so. The
" system prompt would share a breakpoint with the oldest files.
let g:claude_caching = 0
" When enabled, Claude dynamically decides when and how much to use extended
" thinking based on task complexity. Supported on Opus 4.7, Opus 4.6,
" Sonnet 4.6 (and Opus 4.5 with a beta header - handled automatically).
" Note: switching thinking on/off invalidates message-level cache breakpoints;
" system prompt and tool definition caches remain unaffected.
let g:claude_thinking = 0
" Effort level for adaptive thinking.
"   "low"    - fast, minimal thinking; good for simple/chat tasks
"   "medium" - balanced speed, cost, and quality; Anthropic's recommended
"              default for Sonnet 4.6 agentic/coding workflows
"   "high"   - deep reasoning; the API default on Opus 4.6 and Sonnet 4.6
"   "xhigh"  - between high and max; available on Opus 4.7 only
"   "max"    - maximum reasoning depth; available on Opus 4.6 only
let g:claude_thinking_effort = 'high'
"   "summarized" - default; returns a condensed summary of Claude's reasoning.
"                  You are billed for full thinking tokens, not summary tokens.
"   "omitted"    - no thinking text returned (lower bandwidth, same quality).
let g:claude_thinking_display = 'summarized'
"------------------------------------------------------------------------------


" NOTE: Also supports interacting with Qwen (local and free AI)!!!
" 1) Install ollama
" curl -fsSL https://ollama.com/install.sh | sh
" 2) Download and run the desired model with ollama (ex: qwen3:8b)
" ollama run qwen3:8b
"------------------------------------------------------------------------------
let g:ollama_base_url = 'http://localhost:11434'
" Increase or decrease ollama context number of tokens limit.
" NOTE: These values actually affect how long a response will take...
" let g:ollama_num_ctx = 2048
let g:ollama_num_ctx = 4096
" let g:ollama_num_ctx = 8192
" let g:ollama_num_ctx = 32768
" let g:ollama_num_ctx = 262144
"------------------------------------------------------------------------------


" Select the model to use. By default this is set to claude-sonnet-4-6
"------------------------------------------------------------------------------
let g:ai_model = 'claude-sonnet-4-6'
" let g:ai_model = 'claude-opus-4-6'
" let g:ai_model = 'claude-opus-4-7'
" let g:ai_model = 'claude-haiku-4-5'
" let g:ai_model = 'gemini-2.5-flash-lite'
" let g:ai_model = 'gemini-2.5-flash'
" let g:ai_model = 'gemini-3.1-flash-lite-preview'
" let g:ai_model = 'gpt-5.4-mini'
" let g:ai_model = 'gpt-5.4'
" let g:ai_model = 'gpt-5.5'
" let g:ai_model = 'qwen2.5-coder:32b'
" let g:ai_model = 'qwen3:8b'
" let g:ai_model = 'qwen3-coder:30b'
"------------------------------------------------------------------------------
"==============================================================================
```

### Switching AI

claude-sonnet-4-6 is set as the default AI. This can be changed by setting in your .vimrc file the desired AI model you want to use instead. Above in g:ai_model are some examples of AI models that you can select from.

This is not a complete list as the claude.vim plugin also supports older models as well as newer models that have not even been released yet. Newer AI models from the same company usually re-use the same API format, and therefor when a new model is out, you should be able to switch to it without any changes to claude.vim.

### Ollama/Qwen

Qwen is a free locally running AI model that does not require internet access and runs locally on your own PC. Note that locally run models do require a lot of resources such as RAM, VRAM and disk space. The quality and speed of the answers you get depend on your hardware setup, how advanced the model is and what settings you have chosen to run the models with.

That being said, being able to run your AI model locally and not having to send any data to an external company can be appealing to some.

To do so in Linux
1) Install Ollama with:
```sh
curl -fsSL https://ollama.com/install.sh | sh
```
2) Download and run the desired model with Ollama (for ex: qwen3:8b) (NOTE that this is not the strongest model they have, make sure your hardware can support the model you choose as the bigger models can be quite demanding.)
```sh
ollama run qwen3:8b
```
3) in Vim select the Qwen model downloaded
```vim
let g:ai_model = 'qwen3:8b'
```
Now you can use the same commands to chat with Qwen AI as with the other models.


### Prompts

claude.vim uses system prompts in plugin/claude_system_prompt.md and implement prompts in plugin/claude_implement_prompt.md. In my setup I keep these files empty as I prefer the non prompt defaults, but you can add any text you want to the prompt.md files and they will get loaded automatically when first opening the chat or implement mode. Some common text in prompts include sentences such as "Be brief" or "Don't make mistakes" and descriptions/instructions of how you want the AI to behave.


## Usage

First, a couple of vim concepts you should be roughly familiar with:

- Switching between windows (`:help windows`) - at least `<C-W><C-W>` to cycle between active windows
- Diff mode (`:help diff`) - at least `d` `o` to accept the change under cursor
- Folds (`:help folding`) - at least `z` `o` to open a fold (chat interaction) and `z` `c` to close it
- Leader (`:help leader`) - if you are unsure, most likely `\` or `space` is the key to press whenever `<Leader>` is mentioned

Claude.vim currently offers two main interaction modes:

1. Simple implementation assistant
2. Chat interface

### ClaudeImplement

In this mode, you select a block of code and ask Claude to modify it in some way; Claude proposes the change and lets you review and accept it.

1. Select code block in visual mode. (Note that this selection is all Claude
   "sees", with no additional context! Therefore, select liberally, e.g.
   a whole function.)
2. `<Leader>ci` - shortcut for `:'<,'>ClaudeImplement ...`
3. Enter your instruction (e.g. "Fix typos" or "Factor out common code" or "Add error handling" or "There's some bug here") as a ClaudeImplement parameter in the command mode
4. Review and accept proposed changes in diff mode
5. Switch to the scratch window (`<C-W>l`) and `:q` it.

### ClaudeChat

In this mode, you chat with Claude.  You can chat about anything, really,
but the twist is that Claude also sees the full content of all your buffers
(listed in `:buffers` - _roughly_ any files you currently have open in your vim).

1. `<Leader>cc` - shortcut for opening Claude chat window
2. Enter a message on the `You: ` line (and/or indented(!) below it)
3. `<C-]>` (in insert or normal mode) to send your message and get a reply
4. Read the reply in the Claude window etc.
5. If Claude proposes a code change, diff mode automatically pops up to apply it whenever possible.

You can e.g. ask Claude how to debug or fix a bug you observe, or ask it to propose implementation of even fairly complex new functionality. For example:

    You: Can you write a short README.md for this plugin, please?
    Claude:
        Here's a draft README.md for the Claude Vim plugin:

        ```markdown
        # Claude Vim Plugin

        A Vim plugin for integrating Claude, an AI assistant, directly into your Vim workflow.
        ...

Previous interactions are automatically folded for easy orientation (Claude can be a tad bit verbose), but the chat history is also visible to Claude when asking it something. However, you can simply edit the buffer to arbitrarily redact the history (or just delete it).

**NOTE: For every single Claude Q&A roundtrip, full chat history and full content of all buffers is sent. This can consume tokens FAST. (Even if it is not too expensive, remember that Claude also imposes a total daily token limit.) Prune your chat history regularly.**

### Tools

Claude.vim is capable of using various tools to answer you questions or to follow your instructions. The following is a list of tools that it is capable of using:

python: Execute a Python one-liner code snippet and return the standard output. NEVER just print a constant or use Python to load the file whose buffer you already see. Use the tool only in cases where a Python program will generate a reliable, precise response than you cannot realistically produce on your own.
web_search: Perform a web search and return the top 5 results. Use this to find information beyond your knowledge on the web (e.g. about specific APIs, new tools or to troubleshoot errors). Strongly consider using open_web next to open one or several result URLs to learn more.
open_web: Open a new buffer with the text content of a specific webpage. Use this for accessing documentation or other search results.
shell: Execute a shell or even perl commands and return both stdout and stderr. Use with caution as it can potentially run harmful commands.
open: Open a new file, directory or URL, so that it gets access to its content. Returns the buffer name, or 'ERROR' for non-existent paths.
new: Create a new file, opening a buffer for it so that edits can be applied. Returns an error if the file already exists.
vimdiff: Diffs your loaded buffer with it's proposed changes.

**NOTE: To save costs, I personally set "let g:ai_enable_tool_use = 0" in my vimrc to turn tools off and prevent AI from editing files, opening new files, searching the web... This will allow me to review the AI suggestions in chat before manually applying them and to save cost as AI won't read 5000+ lines of code or documentation from multiple files or websites it decided to open... Instead, I turn tools on manually if/when I need them...**

### Tracking Token Usage

After every completed/successful AI response, the plugin will display the token usage and cost of the command. To view all past token usages in the current session you can use the :messages command in Vim.


# List of Major Modifications to [pasky/claude.vim](https://github.com/pasky/claude.vim) that I Asked Claude to Generate:

In keeping the spirit of vibe coding alive, I vibe coded the following modifications and fixes:

- You: Generate in depth comments to explain the code functionality
- You: Disable chat folding by adding g:claude_enable_folding = 0
- You: When I open a large file and I try to send a message to Claude. I get an error: Error: executing job failed: Argument list too long. How can I updated claude.vim to fix this error for large files when interacting with Claude? Show the proposed fix.
- You: Now I get the following error: Unknown Claude protocol output: "{"type":"error","error":{"type":"invalid_request_error","message":"prompt is too long: 315952 tokens > 200000 maximum"},"request_id":"req_011CYX2LSayJnEjuQiQYMKcq"}". propose a fix.
- You: Keep my claude.vim/\*.md files empty as I prefer the default settings and it saves some cost by sending less data through the API. What additional changes do I need to make.
- You: how can I edit claude.vim to increase the context window form 200k to 1m?
- You: Help me understand what the claude.vim file does by adding a lot of comments. Add comments that explain in detail what each function does.
- You: Show a map of how the functions in claude.vim are connected
- You: How can I modify the system prompt to only have the "You:" line?
- You: The printed token usage is not accurate, how can I make it more accurate?
- You: What value should I set 'max_tokens' to?
- You: When you search through internet links you are getting the message: elinks Update your browser Your browser isn't supported anymore. To continue your search, upgrade to a recent version. Propose a fix for this issue.
- You: How can I set g:claude_map_cancel_response = "<c-c>" and have it only affect the Claude chat window and not any other file?
- You: I want to have the functions print "claude-sonnet-4-6:" using g:claude_model instead of printing "Claude:"
- You: I asked you to show me a diff before making the changes yet you gave me an error... Suggest a fix for the following error:
Unknown Claude protocol output: "{"type":"error","error":{"type":"invalid_request_error","message":"messages: text content blocks must be non-empty"},"request_id":"req_011CZwmNjfkhCsMGwEaheCjt"}"
- You: When I ask the Claude API to compare 2 websites, I believe the final cost is a bit too high. Are there inefficiencies in the code that may be causing the issue that I'm seeing. See if you can find any issues that may cause wasteful and inefficient code.
- You: I want an option to use batch API in my Claude API in vim. How can I add it as an additional option?
- You: Add support for qwen local AI!
- You: Add support for OpenAI/ChatGPT and Gemini AIs!
- You: Add support for claude caching in the claude.vim file. Also implements a recency-sort on the buffers so last edited buffers gets moved to the end as they will break the cache downstream.
- You: Adaptive thinking is now supported by the API. Add an adaptive thinking option and effort option for Claude AI.
- You: When the AI is responding in the vim chat, if I try to scroll up as the AI types, it will bring the screen back down. Is there a way to scroll up to see what was written while the AI is still writing at the bottom of chat?
- You: Show the full thinking text when thinking is enabled and print the thinking text in the messages window.

