[![Donate](https://img.shields.io/badge/Paypal-2f343f.svg?style=for-the-badge&logo=paypal&label=Donate)](https://www.paypal.com/donate?hosted_button_id=ZW3CDCANHJCWJ) [![Star](https://img.shields.io/github/stars/nathaneltitane/chatty.svg?style=for-the-badge&logo=github&label=Star&color=2f343f)](https://github.com/nathaneltitane/chatty)

⭐ Useful? Star the repository - it helps others find it.

[[ Chatty // Project Page ]](https://github.com/nathaneltitane/chatty) [ Version // 2026-10-17 ]

---

### NOTICE

10-17-2026 ↴

- Full rewrite on [Frobulator](https://github.com/nathaneltitane/frobulator)
- Current OpenAI models - chat: `gpt-6-luna` - images: `gpt-image-2.5-flare`
- Legacy completions endpoint and DALL·E removed - both retired by OpenAI
- API key kept private in `~/.config/chatty` - no more key file in the working directory
- Clean output for pipes and redirections - answers only

---

### Welcome to Chatty

Chatty is a lightweight BASH utility that brings ChatGPT straight to your Linux terminal (TTY). No browser, no clutter - just clean command-line conversations with AI.

With Chatty, you can:

- Chat with OpenAI models in an interactive session that remembers the conversation.
- Ask a single question and get the answer on the standard output - ready for pipes and scripts.
- Pipe files, logs or command output into a prompt.
- Generate images from a description.
- Get a shell command for a task - shown first, run only once you confirm.
- Keep a private history of every conversation.

---

### TL;DR

The script automatically installs and manages what it needs:

- curl
- jq
- frobulator (auto-downloaded to ~/.local/bin/frobulator if missing)

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

Save your OpenAI API key - asked once ↴

```
./chatty -k
```

Start chatting ↴

```
./chatty
```

Or install globally ↴

```
sudo install -m 755 chatty /usr/local/bin/chatty
```

---

### API Key

Chatty needs an [OpenAI API key](https://platform.openai.com/api-keys). It looks for one in this order:

1. `OPENAI_API_KEY` environment variable
2. `OPENAI_KEY` environment variable - older setups
3. `~/.config/chatty/key` - saved with `chatty -k`

The saved key is typed masked and stored readable by your user only.

---

### Features

- Interactive chat with conversation context - oldest messages dropped first once the context budget is reached
- Single prompts from an option, a file or a pipe - answer only on the standard output
- System prompt from text or file - persona, language, output format
- Reasoning effort and temperature passed only when given - works with reasoning and classic models alike
- Image generation - saved as PNG - shown inline in kitty - opened on request elsewhere
- Shell command suggestions with risky command warnings and confirmation before running
- Markdown answers rendered with [glow](https://github.com/charmbracelet/glow) when installed - wrapped to the terminal width otherwise
- Private history of questions and answers with time stamps and model names
- Colorized interface and ticker through frobulator - Termux compatible

---

### Usage

```
Usage: ./chatty [-p Prompt | -f File] [-s System] [-m Model] [-r Effort] [-t Temperature] [-x Tokens] [-c Tokens] [-i Model] [-z Size] [-b] | -l | -k | -h

Options:

-p, --prompt          Ask a single [p]rompt and print the answer.

-f, --file            Read the prompt from a [f]ile.

-s, --system          [S]ystem prompt - instructions kept for the whole conversation - text or file.

-m, --model           Chat [m]odel.
                      [ gpt-6-luna ] [ Default ]

-r, --reasoning       [R]easoning effort - reasoning models only.
                      [ none | low | medium | high ]

-t, --temperature     Sampling [t]emperature - models that accept it only.
                      [ 0 - 2 ]

-x, --max-tokens      Ma[x]imum tokens per answer - reasoning included.
                      [ 4096 ] [ Default ]

-c, --context         Conversation [c]ontext kept - estimated tokens - oldest messages dropped first.
                      [ 32000 ] [ Default ]

-i, --image-model     [I]mage model.
                      [ gpt-image-2.5-flare ] [ Default ]

-z, --size            Image si[z]e - width x height.
                      [ 1024x1024 ] [ Default ]

-b, --big             [B]ig prompts - multiple lines - [ ↵ ] then [ ctrl + d ] to send.

-l, --list            [L]ist the models available to the API key.

-k, --key             Save the API [k]ey - asked once - kept private in ~/.config/chatty.

-h, --help            Show [h]elp and usage information.
```

The default chat model can also be set with the `CHATTY_MODEL` environment variable.

---

### Chat Commands

Type these at the prompt during a chat session:

```
image: [description]    Generate an image - saved in ~/Pictures/chatty.
command: [task]         Suggest a shell command - run only once confirmed.
model: [id]             Show the details of a model.
models                  List the available models.
history                 Show the saved conversations.
clear                   Start a new conversation - context emptied.
exit | q                Quit.
```

---

### Examples

Interactive chat ↴

```
chatty
```

One question - answer only ↴

```
chatty -p "Explain the difference between a hard link and a symbolic link"
```

Pipe something in ↴

```
dmesg | tail -n 50 | chatty -s "Explain these kernel messages briefly"
```

```
cat script.sh | chatty -s "Review this bash script for bugs"
```

Save an answer ↴

```
chatty -p "Write a haiku about terminals" > haiku.txt
```

Prompt from a file - more reasoning ↴

```
chatty -f question.md -r high
```

Persona for a whole session ↴

```
chatty -s "Answer in French, in one paragraph"
```

Generate an image during a chat ↴

```
image: a mecha made of grey bricks standing in the rain, isometric
```

Get a command during a chat ↴

```
command: find every file over 100 MB in my home folder
```

---

### Files

```
~/.config/chatty/key          API key - readable by your user only
~/.local/share/chatty/history  Conversation history - readable by your user only
~/Pictures/chatty/             Generated images
```

---

### Safety

Commands suggested with `command:` are never run on their own. Chatty shows the command, flags fragments that can change files or download data - `rm`, `mv`, `dd`, `chmod`, `sudo`, redirections, `curl`, `wget` and others - and runs it only when you answer `y`. Read a command before you run it.

---

### API Reference

Uses the [OpenAI API](https://developers.openai.com/api/docs) for:

- `/v1/chat/completions` - chat and single prompts
- `/v1/images/generations` - images
- `/v1/models` - model list and details

- Integrates with frobulator for interface styling, requirements and error handling
- Tested on Debian-based systems (Termux compatible)

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

[[ Chatty // Project Page ]](https://github.com/nathaneltitane/chatty) [ Version // 2026-10-17 ]

### Enjoying Chatty? Buy me a coffee to show your appreciation!

[![Donate](https://img.shields.io/badge/Paypal-2f343f.svg?style=for-the-badge&logo=paypal&label=Donate)](https://www.paypal.com/donate?hosted_button_id=ZW3CDCANHJCWJ)
