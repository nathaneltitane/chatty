[![Donate](https://img.shields.io/badge/Paypal-2f343f.svg?style=for-the-badge&logo=paypal&label=Donate)](https://www.paypal.com/donate?hosted_button_id=ZW3CDCANHJCWJ)<br>
Show your appreciation with a PayPal donation!

---

[![Star](https://img.shields.io/github/stars/nathaneltitane/chatty.svg?style=for-the-badge&logo=github&label=Star&color=2f343f)](https://github.com/nathaneltitane/chatty)<br>
Useful? Star the repository - it helps others find it.

---

[[ Chatty // Project Page ]](https://github.com/nathaneltitane/chatty)&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&nbsp;&nbsp;[ Version // 2026-10-17 ]

---

### NOTICE

10-17-2026 ↴

- Full rewrite on [Frobulator](https://github.com/nathaneltitane/frobulator)
- Current OpenAI models - chat: `gpt-6-luna` - images: `gpt-image-2.5-flare`
- API key installed once - typed, pasted or from a file - kept private in `~/.config/chatty`
- Clean output for pipes and redirections - answers only

---

### Contents

- [Welcome to Chatty](#welcome-to-chatty)
- [TL;DR](#tldr)
- [Requirements](#requirements)
- [Installation](#installation)
- [API Key](#api-key)
- [How Chatty Runs](#how-chatty-runs)
- [Options](#options)
- [Chat Commands](#chat-commands)
- [Models, Tokens and Context](#models-tokens-and-context)
- [Images](#images)
- [Shell Commands](#shell-commands)
- [Files and Privacy](#files-and-privacy)
- [Examples](#examples)
- [Troubleshooting](#troubleshooting)
- [Updating and Removing](#updating-and-removing)
- [License](#license)

---

### Welcome to Chatty

Chatty is a lightweight BASH utility that brings ChatGPT straight to your Linux terminal (TTY). No browser, no clutter - just clean command-line conversations with AI.

With Chatty, you can:

- Chat with OpenAI models in an interactive session that remembers the conversation.
- Ask a single question and get the answer on the standard output - ready for pipes and scripts.
- Pipe files, logs or command output into a prompt.
- Give the model standing instructions - a persona, a language, an output format.
- Generate images from a description.
- Get a shell command for a task - shown first, run only once you confirm.
- Keep a private history of every conversation.

---

### TL;DR

```
git clone https://github.com/nathaneltitane/chatty.git
cd chatty
chmod +x chatty
./chatty -k
./chatty
```

`./chatty -k` asks for your OpenAI API key once - paste it and press [ ↵ ]. `./chatty` starts a chat.

---

### Requirements

- Linux or Termux on Android
- BASH 4 or newer
- An [OpenAI API key](https://platform.openai.com/api-keys) with billing enabled on your OpenAI account
- An internet connection

Chatty installs and manages the rest on its first run:

- **curl** - talks to the OpenAI API
- **jq** - builds requests and reads answers
- **frobulator** - interface, colors, requirement checks - downloaded to `~/.local/bin/frobulator` and replaced only when a newer release exists

Installing curl or jq may ask for your password, as packages are installed system-wide. On Termux, packages install without root.

Optional:

- **[glow](https://github.com/charmbracelet/glow)** - renders answers as formatted markdown - headings, lists, code blocks
- **kitty** terminal - shows generated images inline
- **xdg-open** - opens generated images in your image viewer - present on most desktops

---

### Installation

Clone the repository ↴

```
git clone https://github.com/nathaneltitane/chatty.git
```

Move into the directory ↴

```
cd chatty
```

Make it executable ↴

```
chmod +x chatty
```

Run it from the directory ↴

```
./chatty
```

Or install it for every user - then run `chatty` from anywhere ↴

```
sudo install -m 755 chatty /usr/local/bin/chatty
```

Or for your user only - no root - make sure `~/.local/bin` is in your `PATH` ↴

```
install -m 755 chatty ~/.local/bin/chatty
```

---

### API Key

Chatty needs an OpenAI API key. Create one at [platform.openai.com/api-keys](https://platform.openai.com/api-keys). Requests are billed to your OpenAI account - see [Models, Tokens and Context](#models-tokens-and-context) for how to keep costs low.

#### Saving the key

**Type or paste it** - asked once - the key is masked as you type or paste it ↴

```
chatty -k
```

```
[  +  ] OpenAI API key: ••••••••••••••••••••••••••••••••
```

**Install it from a file** that holds the key - the first line is used, spaces and line endings are removed ↴

```
chatty -k ~/path/to/keyfile
```

Either way, the key is written to `~/.config/chatty/key`, readable by your user only. Run `chatty -k` again any time to replace it.

#### Using an environment variable instead

Chatty also reads the key from the environment, which is handy for scripts, containers and CI ↴

```
export OPENAI_API_KEY="sk-..."
```

Or from a file kept elsewhere ↴

```
export OPENAI_API_KEY="$(< ~/path/to/keyfile)"
```

#### Where Chatty looks - first match wins

1. `OPENAI_API_KEY` environment variable
2. `OPENAI_KEY` environment variable - older setups
3. `~/.config/chatty/key`

#### Keeping the key safe

- Never commit the key file to a repository or paste it into a chat.
- Prefer `chatty -k` over `export` in `~/.bashrc` - the key then stays out of your shell environment and history.
- If a key leaks, revoke it at [platform.openai.com/api-keys](https://platform.openai.com/api-keys) and save a new one.

---

### How Chatty Runs

Chatty works in two ways, chosen automatically.

#### Chat - interactive

Run `chatty` with no prompt. Type a message, press [ ↵ ], read the answer, continue. The conversation is remembered until you quit or type `clear`, so follow-up questions work:

```
[  +  ] Prompt: What is a symbolic link?
...
[  +  ] Prompt: How do I make one?
```

Quit with `exit`, `q` or [ ctrl + d ] on an empty prompt.

#### Single prompt - one answer

Chatty answers once and exits when it gets a prompt from:

- `-p "text"` - the prompt itself
- `-f file` - a file holding the prompt
- a pipe or redirection - `command | chatty` or `chatty < file`

When several are given, `-f` wins over `-p`, and `-p` wins over a pipe.

#### Standard output - answers only

Answers go to the standard output. Everything else - the script notice, the ticker, information and errors - goes to the standard error. So:

```
chatty -p "Write a haiku about terminals" > haiku.txt
```

writes the haiku and nothing else to `haiku.txt`, while the interface still shows on your screen. To hide the interface completely ↴

```
chatty -p "..." 2> /dev/null
```

Chatty exits with status `0` on success and `1` on any error, so it can be tested in scripts:

```
if summary="$(chatty -f report.txt -s "Summarize in one line" 2> /dev/null)"
then
	echo "${summary}"
fi
```

---

### Options

```
Usage: ./chatty [-p Prompt | -f File] [-s System] [-m Model] [-r Effort] [-t Temperature] [-x Tokens] [-c Tokens] [-i Model] [-z Size] [-b] | -l | -k [File] | -u | -h
```

#### -p, --prompt [text]

Ask a single prompt and print the answer. Quote the prompt so the shell passes it as one argument.

```
chatty -p "Explain what /etc/fstab does"
```

#### -f, --file [file]

Read the prompt from a file - useful for long or prepared prompts.

```
chatty -f question.md
```

#### -s, --system [text | file]

System prompt: standing instructions the model follows for the whole conversation - a role, a tone, a language, an output format. Give the text itself or a path to a file that holds it. Works in chat and with single prompts.

```
chatty -s "You are a senior Linux administrator. Answer in short bullet points."
chatty -s ~/prompts/translator.txt
```

#### -m, --model [id]

Chat model. Default: `gpt-6-luna`. The default can also be set for every run with the `CHATTY_MODEL` environment variable - `-m` still overrides it.

```
chatty -m gpt-6.1-sol
export CHATTY_MODEL="gpt-6-astra"
```

List the models your key can use with `chatty -l`.

#### -r, --reasoning [none | low | medium | high]

Reasoning effort, for reasoning models: how much the model thinks before answering. Higher is slower, uses more tokens and suits harder problems; `none` or `low` is fastest for simple questions. Not sent unless given - the model then uses its own default.

```
chatty -r high -p "Find the bug in this function" < function.sh
```

#### -t, --temperature [0 - 2]

Sampling temperature: lower is more focused and repeatable, higher is more varied. Not sent unless given. Some models - most reasoning models - only accept their default and answer with an error when it is set; leave it out for those.

```
chatty -m gpt-4o -t 0.2 -p "..."
```

#### -x, --max-tokens [number]

Maximum tokens for one answer. Default: `4096`. For reasoning models this budget also covers the hidden reasoning, so a hard question with a small budget can come back empty - raise it if that happens.

```
chatty -x 16000 -r high -f problem.md
```

#### -c, --context [number]

How much of the conversation is kept and sent with each message, in estimated tokens. Default: `32000`. When the conversation grows past it, the oldest messages are dropped first. Lower it to save cost on long chats, raise it to keep more history.

```
chatty -c 8000
```

#### -i, --image-model [id]

Image model used by the `image:` chat command. Default: `gpt-image-2.5-flare`.

#### -z, --size [width x height]

Image size. Default: `1024x1024`. Each image model accepts its own set of sizes - `1024x1024`, `1536x1024` (landscape) and `1024x1536` (portrait) are widely supported. An unsupported size comes back as an API error.

```
chatty -z 1536x1024
```

#### -b, --big

Big prompts in chat: write several lines - paste code, logs, long text - then press [ ↵ ] and [ ctrl + d ] to send.

#### -l, --list

List the models available to your API key, one per line, then exit.

#### -k, --key [file]

Save the API key - typed or pasted at a masked prompt, or installed from a file. See [API Key](#api-key).

#### -u, --uninstall

Remove every installed copy of chatty found on your `PATH`, then ask whether to remove the saved API key and history too. Generated images in `~/Pictures/chatty` are always kept. Copies in system folders such as `/usr/local/bin` need `sudo` - chatty tells you the command.

```
chatty -u
```

#### -h, --help

Show help and usage information, chat commands included.

---

### Chat Commands

Type these at the prompt during a chat:

```
image: [description]    Generate an image - saved in ~/Pictures/chatty.
command: [task]         Suggest a shell command - run only once confirmed.
model: [id]             Show the details of a model.
models                  List the available models.
history                 Show the saved conversations.
clear                   Start a new conversation - context emptied.
exit | q                Quit.
```

- **image:** - see [Images](#images).
- **command:** - see [Shell Commands](#shell-commands).
- **model: [id]** - prints what the API knows about a model, for example `model: gpt-6-luna`.
- **models** - same list as `chatty -l`, without leaving the chat.
- **history** - prints every saved question and answer, oldest first.
- **clear** - forgets the conversation so far; the next message starts fresh. The saved history is kept.

Anything else you type is sent to the model.

---

### Models, Tokens and Context

#### Choosing a model

- `gpt-6-luna` - default - fast and inexpensive - everyday questions, summaries, commands
- `gpt-6.1-sol` - stronger reasoning at a moderate price
- `gpt-6-astra` - most capable - hardest problems - most expensive

Model names change over time. `chatty -l` shows exactly what your key can use, and OpenAI's [models page](https://developers.openai.com/api/docs/models) lists current models and prices.

#### Tokens and cost

OpenAI bills by tokens - roughly four characters of English text each. Every message you send in a chat includes the kept conversation, so cost grows with the length of a chat. To keep it low:

- use `clear` when you change subject,
- lower `-c` for long chats,
- keep `-r` low or unset for simple questions,
- use single prompts (`-p`) for one-off questions.

#### Empty answers

A reasoning model that spends its whole token budget thinking returns no text. Chatty then warns `Empty answer - token limit reached before the answer` and leaves the question out of the conversation. Raise `-x` or lower `-r` and ask again.

---

### Images

In a chat, start a message with `image:` and describe the picture:

```
image: a mecha made of grey bricks standing in the rain, isometric
```

- The image is saved as a PNG in `~/Pictures/chatty/`, named with the date and time - for example `chatty-20261017-142530.png`.
- The file path is printed on the standard output.
- In the kitty terminal the image is shown inline.
- Elsewhere, Chatty asks whether to open it in your image viewer.

Pick the model with `-i` and the size with `-z`. Images are billed separately from chat - see OpenAI's pricing page.

---

### Shell Commands

In a chat, start a message with `command:` and describe what you want to do:

```
command: find every file over 100 MB in my home folder
```

Chatty asks the model for a single shell command, shows it, and asks before doing anything:

```
[  i  ] Suggested command
find ~ -type f -size +100M
[  ?  ] Run it? [ y / N ]
```

- Nothing runs unless you answer `y`. Any other answer, or just [ ↵ ], skips it.
- Commands containing fragments that can change or delete files, change permissions, gain root or download data - `rm`, `rmdir`, `mv`, `dd`, `mkfs`, `chmod`, `chown`, `sudo`, `shred`, `truncate`, redirections (`>`), `curl`, `wget` - get a warning first.
- The command runs in a new `bash` with your user's permissions, in the current directory.

Models make mistakes. Read every command before you run it, and never run one you do not understand.

---

### Files and Privacy

```
~/.config/chatty/key            API key - readable by your user only
~/.local/share/chatty/history   Every question and answer - readable by your user only
~/Pictures/chatty/              Generated images
~/.local/bin/frobulator         Interface library - shared with other frobulator scripts
```

History entries look like this:

```
[ 2026-10-17 14:25 // gpt-6-luna ]
> What is a symbolic link?
A symbolic link is ...
```

What leaves your machine: your prompts, the kept conversation, your system prompt and image descriptions are sent to OpenAI's API over HTTPS. Do not send passwords, keys or private data you would not share with OpenAI. See [OpenAI's data usage policies](https://openai.com/policies/api-data-usage-policies).

To erase your history ↴

```
rm ~/.local/share/chatty/history
```

---

### Examples

Interactive chat ↴

```
chatty
```

One question ↴

```
chatty -p "Explain the difference between a hard link and a symbolic link"
```

Explain recent kernel messages ↴

```
dmesg | tail -n 50 | chatty -s "Explain these kernel messages briefly"
```

Review a script ↴

```
chatty -s "Review this bash script for bugs" < script.sh
```

Summarize a long file into another file ↴

```
chatty -s "Summarize in five bullet points" -f notes.txt > summary.md
```

Hard problem - more reasoning and a bigger budget ↴

```
chatty -f problem.md -r high -x 16000
```

French session with a stronger model ↴

```
chatty -m gpt-6.1-sol -s "Answer in French, in one paragraph"
```

Paste a long log in chat ↴

```
chatty -b
```

Landscape images ↴

```
chatty -z 1536x1024
```

Then in the chat ↴

```
image: a quiet harbor town at dawn, watercolor
```

Commit message from your staged changes ↴

```
git diff --staged | chatty -s "Write a one-line commit message for this diff" 2> /dev/null
```

---

### Troubleshooting

**No API key found**
Save one with `chatty -k`, or export `OPENAI_API_KEY`. See [API Key](#api-key).

**Request failed [ invalid_request_error ] - Incorrect API key provided**
The key is wrong, revoked or has a typo. Save it again with `chatty -k`.

**Request failed [ insufficient_quota ]**
Your OpenAI account has no credit left or billing is not set up. Check [platform.openai.com/settings/organization/billing](https://platform.openai.com/settings/organization/billing).

**Request failed [ rate_limit_exceeded ]**
Too many requests in a short time. Wait a moment and try again.

**Request failed - model does not exist**
The model name is misspelled or not available to your key. Check `chatty -l`.

**Request failed - temperature not supported**
The model only accepts its default temperature. Run without `-t`.

**Request failed - size not supported**
The image model does not accept that size. Use `1024x1024`, `1536x1024` or `1024x1536`.

**Empty answer - token limit reached before the answer**
Raise `-x` or lower `-r`. See [Empty answers](#empty-answers).

**No answer from the API**
No response arrived - check your internet connection, proxy or firewall.

**Token counts must be whole numbers / Temperature must be a number**
`-x` and `-c` take whole numbers; `-t` takes a number such as `0.7`.

**Answers show raw markdown - asterisks, hashes**
Install [glow](https://github.com/charmbracelet/glow) for formatted answers.

**Unable to fetch frobulator**
The first run downloads frobulator from `get.frbltr.app`. Check your connection and run Chatty again.

---

### Updating and Removing

Update ↴

```
cd chatty
git pull
```

Reinstall afterwards if you installed it to `/usr/local/bin` or `~/.local/bin`.

Remove Chatty - asks before removing your key and history ↴

```
chatty -u
```

Generated images in `~/Pictures/chatty` are kept - remove them separately if you want.

---

### API Reference

Uses the [OpenAI API](https://developers.openai.com/api/docs):

- `/v1/chat/completions` - chat and single prompts
- `/v1/images/generations` - images
- `/v1/models` - model list and details

Integrates with frobulator for interface styling, requirements and error handling. Tested on Debian-based systems; Termux compatible.

---

### License

Chatty is released under the [GNU General Public License v3.0](LICENSE.md).

---

### Reports:

[Submit bug report or feature request](https://github.com/nathaneltitane/chatty/issues)

### Projects:

[![GitHub Repo stars](https://img.shields.io/github/stars/nathaneltitane/dextop?style=for-the-badge&logo=gnubash&logoColor=ffffff&label=DEXTOP)](https://github.com/nathaneltitane/dextop)

[![GitHub Repo stars](https://img.shields.io/github/stars/nathaneltitane/frobulator?style=for-the-badge&logo=gnubash&logoColor=ffffff&label=FROBULATOR)](https://github.com/nathaneltitane/frobulator)

[![GitHub Repo stars](https://img.shields.io/github/stars/nathaneltitane/gutengrab?style=for-the-badge&logo=gnubash&logoColor=ffffff&label=GutenGrab)](https://github.com/nathaneltitane/gutengrab)

[![GitHub Repo stars](https://img.shields.io/github/stars/nathaneltitane/chatty?style=for-the-badge&logo=gnubash&logoColor=ffffff&label=CHATTY)](https://github.com/nathaneltitane/chatty)

[![GitHub Repo stars](https://img.shields.io/github/stars/nathaneltitane/l2cu?style=for-the-badge&logo=gnubash&logoColor=ffffff&label=L²CU)](https://github.com/nathaneltitane/l2cu)

[![GitHub Repo stars](https://img.shields.io/github/stars/nathaneltitane/terminal?style=for-the-badge&logo=gnubash&logoColor=ffffff&label=TERMINAL)](https://github.com/nathaneltitane/terminal)

[![GitHub Repo stars](https://img.shields.io/github/stars/nathaneltitane/mechablocks?style=for-the-badge&logo=gnubash&logoColor=ffffff&label=MECHA%20//%20BLOCKS)](https://github.com/nathaneltitane/mechablocks)

[![GitHub Repo stars](https://img.shields.io/github/stars/nathaneltitane/pixtrm?style=for-the-badge&logo=gnubash&logoColor=ffffff&label=PIXTRM)](https://github.com/nathaneltitane/pixtrm)

[![GitHub Repo stars](https://img.shields.io/github/stars/nathaneltitane/nathaneltitane?style=for-the-badge&logo=gnubash&logoColor=ffffff&label=NATHANEL%20%2b%20TITANE)](https://github.com/nathaneltitane/nathaneltitane)

[![GitHub Repo stars](https://img.shields.io/github/stars/nathaneltitane/pewpewprints?style=for-the-badge&logo=gnubash&logoColor=ffffff&label=PEW%21%20PEW%21%20PRINTS)](https://github.com/nathaneltitane/pewpewprints)

---

[[ Chatty // Project Page ]](https://github.com/nathaneltitane/chatty)&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&nbsp;&nbsp;[ Version // 2026-10-17 ]

---

[![Donate](https://img.shields.io/badge/Paypal-2f343f.svg?style=for-the-badge&logo=paypal&label=Donate)](https://www.paypal.com/donate?hosted_button_id=ZW3CDCANHJCWJ)<br>
Show your appreciation with a PayPal donation!

---

[![Star](https://img.shields.io/github/stars/nathaneltitane/chatty.svg?style=for-the-badge&logo=github&label=Star&color=2f343f)](https://github.com/nathaneltitane/chatty)<br>
Useful? Star the repository - it helps others find it.
