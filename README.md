# Ollama Support for Vim

This plugin adds Copilot-like code completion support to Vim. It uses [Ollama](https://ollama.com) as a backend, which
can run locally and does not require cloud services, thus preserving your privacy.

<p align="center">
<img src="screenshots/VimOllama_256x256.png" alt="Vim-Ollama Logo">
</p>

## Motivation

[Copilot.vim](https://github.com/github/copilot.vim) by Tim Pope is an excellent plugin for both Vim and NeoVim.
However, it is limited to Microsoft's Copilot, a commercial cloud-based AI that requires sending all your data to
Microsoft.

With Ollama and freely available LLMs (e.g., Llama3, Codellama, Deepseek-coder-v2, etc.), you can achieve similar
results without relying on the cloud. While other plugins are available, they typically require NeoVim, which isn't an
alternative for me. I prefer using Vim in the terminal and do not want to switch to NeoVim for various reasons.

## Features

- Intelligent AI-based code completion (aka tab completion)
- Integrated chat support for code reviews and other interactions
- Automatic code editing based in human description (NEW in V1.0)
  - Supports inline-diff view for accepting changes interactively
  - Or accept without prompt for a Git based workflow using Vim fugutive (:Gvdiffsplit)

![Demo](screenshots/game.gif)

## Screencasts

### Demo of Vim-Ollama 1.0

[![Vim-Ollama Demo](screenshots/vlcsnap-2025-02-09-19h31m08s418.png)](https://www.youtube.com/watch?v=adakWGm1BDs)

### Creating Enum to String Conversion function using AI

[![Enum to String Conversion](screenshots/screenshot2.png)](https://www.youtube.com/watch?v=G-ivVUXCKQk)

### Code Review

[![Code Review](screenshots/screenshot3.png)](https://www.youtube.com/watch?v=kLkFr4rbPUo)

### Custom Prompts - Spellcheck Example

[![Custom Prompts](screenshots/screenshot4.png)](https://www.youtube.com/watch?v=aWEQTktv6fs)

## How It Works

The plugin uses Python scripts, e.g. `complete.py` and `chat.py`, to communicate with Ollama via its REST API. The first
script handles code completion tasks, while the second script is used for interactive chat conversations. The Vim plugin
uses these scripts via I/O redirection to integrate AI results into Vim.

This plugin supports Vim only, not NeoVim. If you're looking for a NeoVim plugin, check out
[LLM](https://github.com/huggingface/llm.nvim).

## Requirements

- Python 3.x
- Python package: `httpx>=0.23.3`, `requests`

### Debian-based Systems

If you're using a Debian-based distribution, you can install the required library directly:

```sh
sudo apt install python3-httpx
```

### Other systems

System wide installation using `pip install` is not recommended,
use a virtual environment instead.

You need to run Vim from a shell with this Python environment to make this working.

Example:
```sh
python -m venv $HOME/vim-ollama
source $HOME/vim-ollama/bin/activate
pip install httpx>=0.23.3
pip install requests
```

Testing: You can test the python script on the shell to verify that it is working and all requirements are found.
The script should output a completion as shown below:

```sh
$> cd path/to/vim-ollama/python
$> echo -e 'def compute_gcd(x, y): <FILL_IN_HERE>return result' | ./complete.py -u http://localhost:11434 -m codellama:7b-code
  if x == 0:
    return y
  else:
    return compute_gcd(y % x, x)

def compute_lcm(x, y):
  result = (x * y) / compute_gcd(x, y)
```

## Installation

Install `gergap/vim-ollama` using vim-plug or any other plugin manager.

vim-plug example:
```vim
call plug#begin()
...
Plug 'gergap/vim-ollama'
call plug#end()
```

## First Run

Since V0.4, the plugin includes a setup wizard that helps you set up your
initial configuration. This is especially useful for new users who are not
familiar with Ollama or the different LLMs available as Open Source.

The plugin will run the wizard automatically if the configuration file
`~/.vim/config/ollama.vim` does not yet exist. If you want to start the wizard
again, you can use the command `:Ollama setup` at any time, but be aware that
it will overwrite the configuration file at the end.

![First Use](screenshots/firstuse.gif)

## Configuration

It is recommended to use the file `~/.vim/config/ollama.vim` for configuring Vim-Ollama,
but you can also override the settings in `~/.vimrc` as in previous versions.

If you are migrating from previous versions, note that the FIM tokens are not configured anymore in Vim,
but in the bundled [JSON config files](python/configs). You can simply remove the old settings from your
`.vimrc`. The plugin should work with the most popular models out-of-the-box.

| Variable              | Default                  | Description                            |
|-----------------------|--------------------------|----------------------------------------|
| `g:ollama_host`       | `http://localhost:11434` | The URL of the Ollama server.          |
| `g:ollama_model`      | `starcoder2:3b`          | The LLM for code completions.          |
| `g:ollama_edit_model` | `qwen2.5-coder:3b`       | The LLM for code editing tasks.        |
| `g:ollama_chat_model` | `llama3.1:8b`            | The LLM for chat conversations.        |

When adding new unsupported code completion models you will see an error like `ERROR - Config file .../python/configs/foobar.json not found.`.
Simply add this missing file and create a merge request to get it included upstream.
Consult the model's documentation to find out the correct tokens.

## Usage

Simply start coding. The completions will appear as "ghost text" and can be accepted by pressing `<tab>`. To ignore
them, just continue typing or press `<C-]>` to dismiss the suggestion.

You can also accept just the one line using `<M-Right>` (Alt-Right) or one word
using `<M-C-Right>` (Alt-Ctrl-Right) if you don't want to use the complete suggestion.

See `:help vim-ollama` for more information.
