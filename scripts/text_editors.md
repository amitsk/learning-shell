# Text Editors on the Command Line

Sooner or later you will `ssh` into a box with no GUI, no mouse, and a `git commit` that just opened **Vim**. This chapter is how you survive that moment, laugh about it later, and pick an editor you can actually live with.

You do not need to join a decades-old editor war. You *do* need a way to edit a file over SSH, write a shell script, and get out again.

[← Back: Getting Started](getting_started.md) | [Next: Bash Shell & Scripting Basics →](basic_shell.md)

---

## 1. Why bother with a terminal editor?

GUIs are great until they are gone. Terminal editors show up when you:

- edit a file on a remote server
- write a commit message (`git` often launches `$EDITOR`)
- run `crontab -e` or `sudo visudo`
- fix a config in a container or recovery shell

Set a default so Git and other tools stop guessing:

```sh
export EDITOR=nano    # or vim, nvim, emacs, hx, edit
export VISUAL="$EDITOR"
```

Put those in your shell rc file (`~/.bashrc` or `~/.zshrc`). Git can be more specific:

```sh
git config --global core.editor nano
```

---

## 2. nano (and pico): the friendly ones

[Pico](https://en.wikipedia.org/wiki/Pico_(text_editor)) was the editor bundled with the Pine email client. The joke writes itself: people learned a whole text editor because they needed to send mail. [GNU nano](https://www.nano-editor.org/) is the free, friendlier clone you will actually find on modern Linux (`nano's ANOther editor`).

If you remember one editor for emergencies, make it nano. The shortcuts are listed on the screen. That is not an accident.

```sh
nano hello.sh
```

The cheat sheet is the two lines at the **bottom of the screen**. Trust those over memory: Debian nano, Homebrew nano, and nano 8+ do not all bind the same keys.

Classic GNU nano (what most Linux servers still ship):

| Key | What it does |
| --- | --- |
| `Ctrl+G` | Help |
| `Ctrl+O` then Enter | Save ("Write Out") |
| `Ctrl+X` | Exit (it will ask to save) |
| `Ctrl+K` / `Ctrl+U` | Cut / paste a line |
| `Ctrl+W` | Search ("Where Is") |
| `Ctrl+\` | Search and replace |

Newer nano (8+) moved some of those to `Ctrl+S` (save), `Ctrl+F` (find), and `Alt+R` (replace). If a shortcut does nothing, look at the footer — it is labeled on purpose.

That is enough coverage. Nano is the editor you use when you want to *finish the task*, not when you want a personality.

```sh
# Debian/Ubuntu
sudo apt install nano

# Fedora
sudo dnf install nano

# macOS (often already present; Homebrew has a newer build)
brew install nano
```

**Further reading:** [GNU nano manual](https://www.nano-editor.org/dist/latest/nano.html) · [nano cheatsheet](https://www.nano-editor.org/dist/latest/cheatsheet.html)

---

## 3. Vim basics (and the joke about never leaving)

[Vim](https://www.vim.org/) ("Vi IMproved") is the editor that ships on almost every Unix-like system. It is modal: keys mean different things depending on the mode. That is its superpower and the source of every meme.

### The joke

A rite of passage for new developers:

> I opened Vim by accident. That was three days ago. Send snacks.

This is not just folklore. Stack Overflow's [How do I exit the Vim editor?](https://stackoverflow.com/questions/11828270/how-do-i-exit-vim) became one of the site's most-visited questions. In 2017 the Stack Overflow blog celebrated it in [Helping One Million Developers Exit Vim](https://stackoverflow.blog/2017/05/23/stack-overflow-helping-one-million-developers-exit-vim/) — at the time, roughly one in every 20,000 visits to Stack Overflow was someone trying to leave the editor.

You are now in on the joke. Here is the punchline, which is also the actual answer:

```text
Esc          leave Insert mode (mash it if you are unsure)
:q           quit (only works if nothing changed)
:q!          quit and throw away changes   ("I regret everything")
:w           save
:wq          save and quit
ZZ           save and quit, without the colon (normal mode)
```

If Vim is yelling at you, you are probably still in Insert mode. Hit `Esc` first, *then* type the colon command.

A popular variant of the joke is to "exit" by killing the process from another terminal (`killall vim`). That works. It is also how you lose the file you were editing. Prefer `:q!`.

### Modes, in one minute

| Mode | How you get there | What keys do |
| --- | --- | --- |
| **Normal** | `Esc` (this is the default) | Movement and commands (`h j k l`, `dd`, `yy`) |
| **Insert** | `i` / `a` / `o` | Typing text, like a normal editor |
| **Visual** | `v` / `V` | Select, then operate |
| **Command-line** | `:` | Ex commands (`:w`, `:q`, `:s/foo/bar/g`) |

Think of Normal mode as a keyboard with the safety on. Insert mode is "now I may type." Beginners get stuck because they type `hello` in Normal mode and Vim interprets that as a series of commands. (Sometimes the file even changes. That is not a ghost. That is `h` `e` `l` `l` `o`.)

### A tiny survival kit

```text
vim filename

i            insert before the cursor
a            insert after the cursor
Esc          back to Normal mode
h j k l      left / down / up / right (or just use the arrows)
dd           delete the current line
yy           yank (copy) the current line
p            paste after the cursor
u            undo
Ctrl+r       redo
/pattern     search forward; n for next match
:set number  show line numbers
```

Practice without risk (needs the full `vim` package, not the tiny `vi` some distros ship):

```sh
vimtutor
```

Thirty minutes in `vimtutor` beats six months of opening Vim, panicking, and switching to nano. After that, [Open Vim](https://openvim.com/) is a good interactive tour, and [Vim Adventures](https://vim-adventures.com/) will teach `hjkl` by turning them into a game.

```sh
# Debian/Ubuntu
sudo apt install vim

# macOS
brew install vim
```

**Further reading:** [Vim documentation](https://www.vim.org/docs.php) · [Learn Vim Progressively](https://yannesposito.com/Scratch/en/blog/Learn-Vim-Progressively/) · [MIT Missing Semester: Editors (Vim)](https://missing.csail.mit.edu/2020/editors/)

---

## 4. Neovim: Vim's hyperextensible cousin

[Neovim](https://neovim.io/) is a modern fork of Vim. Same modal muscle memory (`i`, `Esc`, `:wq` still work), different engine: Lua configuration, a built-in terminal, first-class LSP (language server) support, and a plugin ecosystem that looks like 2026 instead of 1996.

```sh
nvim filename
```

Why people switch:

- configs in Lua (`~/.config/nvim/init.lua`) instead of a growing `.vimrc`
- [LSP](https://microsoft.github.io/language-server-protocol/) for completion and diagnostics
- treesitter highlighting
- a plugin culture built around [lazy.nvim](https://github.com/folke/lazy.nvim)

You can start small (Neovim as "Vim with better defaults") or go nuclear with a distribution:

- [kickstart.nvim](https://github.com/nvim-lua/kickstart.nvim) — a readable starter config, not a black box
- [LazyVim](https://www.lazyvim.org/) — batteries included

```sh
# Debian/Ubuntu (version may be older than upstream)
sudo apt install neovim

# Fedora
sudo dnf install neovim

# macOS
brew install neovim
```

`:q` still quits. The memes transfer. The plugins get weirder (in a good way).

**Further reading:** [Neovim](https://neovim.io/) · [Neovim docs](https://neovim.io/doc/) · [From Vim to Neovim](https://neovim.io/doc/user/nvim.html)

---

## 5. Emacs: the other religion

If Vim is a modal text editor that grew a plugin universe, [GNU Emacs](https://www.gnu.org/software/emacs/) is an operating system that happens to edit text. The classic joke, attributed in various forms to old Usenet:

> Emacs is a great operating system, lacking only a decent editor.

The other classic is the backronym **E**ight **M**egabytes **A**nd **C**onstantly **S**wapping — funny in the 1990s, slightly less fair on a machine with 32 GiB of RAM, still fair if you open Magit, Org, and twelve LSP clients at once.

Emacs is *not* modal by default. You hold modifier keys:

| Keys | What it does |
| --- | --- |
| `C-x C-f` | Open a file (find-file) |
| `C-x C-s` | Save |
| `C-x C-c` | Exit Emacs |
| `C-g` | Cancel whatever you just started (your new best friend) |
| `C-h t` | The built-in tutorial. Use it. |
| `C-h k` then a key | "What does this key do?" |

`C-` means Ctrl. `M-` means Meta, which is usually Alt (or Esc then the key).

```sh
emacs filename          # GUI if available
emacs -nw filename      # terminal ("no window")
```

People stay in Emacs for the universe around the editor:

- [Org mode](https://orgmode.org/) — notes, todos, literate programming
- [Magit](https://magit.vc/) — Git, but pleasant
- [TRAMP](https://www.gnu.org/software/tramp/) — edit remote files over SSH
- [Evil](https://github.com/emacs-evil/evil) — Vim keybindings inside Emacs, for diplomats

Distributions such as [Doom Emacs](https://github.com/doomemacs/doomemacs) and [Spacemacs](https://www.spacemacs.org/) give you a batteries-included setup (often with Vim keys via Evil).

```sh
# Debian/Ubuntu
sudo apt install emacs

# macOS
brew install emacs
```

**Further reading:** [GNU Emacs](https://www.gnu.org/software/emacs/) · [Emacs tutorial](https://www.gnu.org/software/emacs/tour/) · [Learn Emacs](https://www.emacswiki.org/emacs/LearningEmacs) · [Mastering Emacs](https://www.masteringemacs.org/)

---

## 6. Newer editors: Helix and Microsoft Edit

The classics still matter. A few newer terminal editors are worth knowing by name.

### Helix

[Helix](https://helix-editor.com/) is a modal editor in the Vim family with a different slogan: **select first, then act**. Multiple cursors and syntax-aware editing are built in. LSP and treesitter come with the editor instead of a weekend of plugin archaeology.

```sh
hx filename
```

Helix still has modes, still uses `Esc`, and still has a learning curve — but the curve is "learn Helix," not "learn Helix plus a plugin manager plus a color scheme plus why did my LSP die."

- Site: [https://helix-editor.com/](https://helix-editor.com/)
- Docs: [https://docs.helix-editor.com/](https://docs.helix-editor.com/)
- Repo: [https://github.com/helix-editor/helix](https://github.com/helix-editor/helix)

### Microsoft Edit

[Edit](https://github.com/microsoft/edit) is Microsoft's open-source terminal editor: a modern nod to MS-DOS Edit, with input that feels closer to VS Code than to Vim. It is modeless. You type. You save. You leave. No Stack Overflow rescue thread required.

```sh
# Windows
winget install Microsoft.Edit
edit filename

# macOS (Homebrew); Linux builds often use the same name
brew install msedit
msedit filename
```

On Linux, `edit` is frequently a *different* program. Prefer `msedit` unless you know the `edit` on your `$PATH` is Microsoft Edit. Install notes for every OS: [microsoft/edit](https://github.com/microsoft/edit). Microsoft's docs: [Edit command-line editor](https://learn.microsoft.com/en-us/windows/edit/).

If nano is "please just let me edit this file" for Unix, Edit is that same energy for Windows terminals — and a nice option anywhere you want VS Code muscle memory without starting VS Code.

---

## 7. Vim and Emacs modes in the shell (and in coding agents)

Here is the plot twist: you can use Vim or Emacs keys *without opening Vim or Emacs*. Readline (Bash), Zsh, and several coding TUIs all speak those dialects.

### Bash: `set -o vi` and `set -o emacs`

Bash line editing defaults to Emacs-style keys. That is why `Ctrl+A` jumps to the start of the line and `Ctrl+E` to the end, even if you have never started Emacs in your life.

```sh
set -o emacs    # default: Ctrl+A / Ctrl+E / Ctrl+K / Ctrl+Y ...
set -o vi       # Esc then hjkl, 0, $, dd — Vim on the command line
```

Check which one you are in:

```sh
set -o | grep -E 'emacs|vi'
```

Make it permanent in `~/.bashrc`:

```sh
set -o vi
```

In vi mode, the prompt starts in insert-style editing. Hit `Esc` to go to command mode, then `k` / `j` to walk history, `0` / `$` for start / end of line, `cw` to change a word. `i` or `a` to type again. Same joke, smaller editor.

Zsh uses `bindkey`:

```sh
bindkey -e    # Emacs (Zsh default)
bindkey -v    # Vim
```

Fish has `fish_vi_key_bindings` / `fish_default_key_bindings`.

### `$EDITOR` is how tools call you back

Anything that needs a full-screen editor (Git, `fc`, `crontab -e`, `visudo`) looks at `$VISUAL` then `$EDITOR`. Keep those pointed at something you can exit.

```sh
export EDITOR=nvim
export VISUAL=nvim
git config --global core.editor nvim
```

`sudo visudo` and similar "please do not brick the machine" tools often ignore your alias and launch `vi` or `nano` on purpose. Know both.

### OpenAI Codex CLI

[Codex](https://github.com/openai/codex) is OpenAI's terminal coding agent. The TUI composer understands **Vim modal editing**.

- In a session, type `/vim` to toggle Vim mode for the prompt.
- To make it the default, in `~/.codex/config.toml`:

```toml
[tui]
vim_mode_default = true
```

With that set, the composer **starts in Normal mode**, not Insert. Hit `i` to type a prompt, `Esc` for commands. If keys seem to "do nothing" or eat characters, you are in Normal mode — same joke as Vim, smaller box.

Docs: [Codex slash commands](https://developers.openai.com/codex/cli/slash-commands) (`/vim`) · [Codex config](https://developers.openai.com/codex/config-reference)

The default composer is closer to ordinary (Emacs/readline-style) text editing. `/vim` is for people who want `Esc` / `hjkl` while writing prompts.

### Grok Build

[Grok Build](https://docs.x.ai/build/overview) (the `grok` TUI) splits the idea in two, which is easy to mix up:

| Setting | Where it lives | What it changes |
| --- | --- | --- |
| `[ui] simple_mode` | prompt (the composer) | `true` (default) = readline-style editing; `false` = **experimental** Vim modal editing in the prompt |
| `[ui] vim_mode` | scrollback | `false` (default) = arrows to move; `true` = `j`/`k`/`h`/`l`/`g`/`G` in the log |

```toml
# ~/.grok/config.toml
[ui]
simple_mode = false   # Vim keys while typing a prompt
vim_mode = true       # Vim keys while browsing the conversation
```

In the TUI:

- `/vim-mode` toggles scrollback Vim keys
- `/settings` can flip prompt Vim editing ("Disable vim input mode" / the simple-mode toggle)

Docs: [Grok Build keyboard shortcuts](https://docs.x.ai/build/keyboard-shortcuts) · [Modes and commands](https://docs.x.ai/build/modes-and-commands) (`/vim-mode`)

The pattern is the same everywhere: **Emacs/readline keys are the default for "just type," Vim keys are opt-in for people who already have that muscle memory.** Learn one well; the bindings will follow you from Bash to Git to agents.

---

## 8. A practical way to choose

| If you... | Try |
| --- | --- |
| want to edit a file and leave, today | **nano** or **Edit** |
| live on remote servers and like modal editing | **Vim**, then maybe **Neovim** |
| want an OS that edits text | **Emacs** |
| want modal editing with batteries included | **Helix** |
| want VS Code keys in a terminal | **Edit** |
| only care about the command line, not a full editor | `set -o vi` or `set -o emacs` |

There is no final exam. There *is* a `git commit` waiting on a server at 2 a.m. Know how to save, know how to quit, and you are already ahead of a million Stack Overflow visitors.

---

## 9. References

Editors

- [GNU nano](https://www.nano-editor.org/) and [cheatsheet](https://www.nano-editor.org/dist/latest/cheatsheet.html)
- [Pico (Wikipedia)](https://en.wikipedia.org/wiki/Pico_(text_editor))
- [Vim](https://www.vim.org/) · built-in `vimtutor` · [Open Vim](https://openvim.com/) (in-browser)
- [How do I exit the Vim editor? (Stack Overflow)](https://stackoverflow.com/questions/11828270/how-do-i-exit-vim)
- [Helping One Million Developers Exit Vim (Stack Overflow Blog)](https://stackoverflow.blog/2017/05/23/stack-overflow-helping-one-million-developers-exit-vim/)
- [Neovim](https://neovim.io/)
- [GNU Emacs](https://www.gnu.org/software/emacs/) · [Emacs tour](https://www.gnu.org/software/emacs/tour/)
- [Helix](https://helix-editor.com/)
- [Microsoft Edit](https://github.com/microsoft/edit) · [Microsoft Learn: Edit](https://learn.microsoft.com/en-us/windows/edit/)

Shell and agents

- [Bash command-line editing (`set -o vi` / `emacs`)](https://www.gnu.org/software/bash/manual/html_node/Command-Line-Editing.html)
- [Zsh line editor](https://zsh.sourceforge.io/Doc/Release/Zsh-Line-Editor.html)
- [Codex CLI](https://github.com/openai/codex) · [`/vim` slash command](https://developers.openai.com/codex/cli/slash-commands)
- [Grok Build](https://docs.x.ai/build/overview) · [`/vim-mode`](https://docs.x.ai/build/modes-and-commands)

Courses that pair well with this chapter

- [MIT Missing Semester: Editors](https://missing.csail.mit.edu/2020/editors/)
- [Learn Vim Progressively](https://yannesposito.com/Scratch/en/blog/Learn-Vim-Progressively/)

---

Happy editing. May your `:q!` be intentional.

[← Back: Getting Started](getting_started.md) | [Next: Bash Shell & Scripting Basics →](basic_shell.md)
