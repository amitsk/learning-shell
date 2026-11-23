# Text Editors on the Command Line

Sooner or later a `git commit` opens **Vim**. This chapter is how you survive that, and how you pick an editor the day you want one.

**About 20 minutes. Done when** you can edit a file in nano and leave Vim with `:q!`.

[← hello.sh](getting_started.md#3-writing-your-first-script) | [Next: five minutes in scratch/ →](basic_shell.md#1-five-minutes-in-scratch)

---

## 1. Why bother with a terminal editor?

You do not need a favorite yet. You need an exit key. GUIs are great until they are gone. Terminal editors show up when you:

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

`git commit`, `crontab -e`, and `visudo` will open that editor. Saving and quitting is the whole skill they require.

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

Newer nano (8+) changed the *search* keys, not the save key. `Ctrl+O` still writes out, `Ctrl+X` still exits, and `Ctrl+\` still replaces. You only get `Ctrl+S` to save and `Ctrl+Q` to quit if you launch nano with `--modernbindings` (`nano -/`), which most distros do not enable by default. If a shortcut does nothing, look at the footer. It is labeled on purpose.

```sh
nano --modernbindings hello.sh   # opt in to Ctrl+S / Ctrl+Q
```

That is enough coverage. Nano is the editor you use when you want to *finish the task* without picking a lifelong hobby.

Install via your package manager (`apt`/`dnf`/`brew install nano`) if it is not already there. **Further reading:** [GNU nano manual](https://www.nano-editor.org/dist/latest/nano.html) · [nano cheatsheet](https://www.nano-editor.org/dist/latest/cheatsheet.html)

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

## You can close this tab

You can open nano, and you can leave Vim. That is this visit.

Next: [five minutes in `scratch/`](basic_shell.md#1-five-minutes-in-scratch), through [looking at files](basic_shell.md#4-looking-at-files). Stop when that page says the light path ends.

Everything under this line is for the day you pick an editor and stay: a slightly bigger Vim kit, then Neovim, Emacs, Helix, and the keys coding agents use.

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

[Neovim](https://neovim.io/) (`nvim filename`) is a modern fork of Vim with the same modal muscle memory (`i`, `Esc`, `:wq`) and a newer engine: Lua config (`~/.config/nvim/init.lua`), built-in [LSP](https://microsoft.github.io/language-server-protocol/) and treesitter support, and a lively plugin ecosystem around [lazy.nvim](https://github.com/folke/lazy.nvim).

Start from scratch or pick a starter kit: [kickstart.nvim](https://github.com/nvim-lua/kickstart.nvim) (readable, single-file) or [LazyVim](https://www.lazyvim.org/) (batteries included). `:q` still quits; the memes transfer.

**Install & docs:** [Neovim install guide](https://github.com/neovim/neovim/blob/master/INSTALL.md) (distro packages can lag upstream) · [Neovim docs](https://neovim.io/doc/) · [From Vim to Neovim](https://neovim.io/doc/user/nvim.html)

---

## 5. Emacs: the other religion

If Vim is a modal text editor that grew a plugin universe, [GNU Emacs](https://www.gnu.org/software/emacs/) is an operating system that happens to edit text. The classic Usenet joke:

> Emacs is a great operating system, lacking only a decent editor.

Its classic backronym — **E**ight **M**egabytes **A**nd **C**onstantly **S**wapping — is less fair on 32 GiB of RAM, unless you open Magit, Org, and twelve LSP clients at once.

Emacs is *not* modal by default; you hold modifier keys (`C-` = Ctrl, `M-` = Alt/Esc). Launch in a terminal with `emacs -nw filename`.

| Keys | What it does |
| --- | --- |
| `C-x C-f` / `C-x C-s` / `C-x C-c` | Open / Save / Exit |
| `C-g` | Cancel whatever you just started (your new best friend) |
| `C-h t` / `C-h k` | Built-in tutorial / "what does this key do?" |

People move in permanently for [Org mode](https://orgmode.org/) (notes & literate programming), [Magit](https://magit.vc/) (Git made pleasant), [TRAMP](https://www.gnu.org/software/tramp/) (remote editing over SSH), and [Evil](https://github.com/emacs-evil/evil) (Vim keys inside Emacs, for diplomats) — or batteries-included distros like [Doom Emacs](https://github.com/doomemacs/doomemacs) and [Spacemacs](https://www.spacemacs.org/).

**Install & docs:** [Download GNU Emacs](https://www.gnu.org/software/emacs/download.html) · [Emacs tour](https://www.gnu.org/software/emacs/tour/) · [Mastering Emacs](https://www.masteringemacs.org/)

---

## 6. Newer editors: Helix and Microsoft Edit

- **[Helix](https://helix-editor.com/)** (`hx filename`) — Modal like Vim, except **select first, then act**, with multiple cursors, treesitter, and LSP built in so you spend the weekend editing code instead of debugging a plugin manager. [Install](https://docs.helix-editor.com/install.html) · [Docs](https://docs.helix-editor.com/)
- **[Microsoft Edit](https://github.com/microsoft/edit)** (`edit` on Windows; `msedit` via Homebrew/Linux so it doesn't collide with an existing `edit`) — Modeless, VS Code-ish terminal editor. You type, save, and leave — no Stack Overflow rescue thread required. [Install & docs](https://learn.microsoft.com/en-us/windows/edit/)

---

## 7. Vim and Emacs modes in the shell (and in coding agents)

Plot twist: you can use Vim or Emacs keys *without opening Vim or Emacs*. Bash (Readline), Zsh, and terminal coding agents speak those dialects too.

### Shell line editing

Bash defaults to Emacs-style keys (`Ctrl+A` / `Ctrl+E` for start/end of line). Switch with `set -o vi` (put it in `~/.bashrc` to keep it) or `set -o emacs`, and check with `set -o | grep -E 'emacs|vi'`. In vi mode, `Esc` enters command mode (`k`/`j` walk history, `0`/`$` jump to ends, `i`/`a` resume typing) — tiny Vim, right at the prompt.

Zsh uses `bindkey -e` / `bindkey -v`; Fish uses `fish_vi_key_bindings`. See [Bash Command-Line Editing](https://www.gnu.org/software/bash/manual/html_node/Command-Line-Editing.html) and [Zsh Line Editor](https://zsh.sourceforge.io/Doc/Release/Zsh-Line-Editor.html).

### `$EDITOR` is how tools call you back

Git, `fc`, `crontab -e`, and `visudo` look at `$VISUAL` then `$EDITOR` (keep them pointed at an editor you know how to exit; `sudo visudo` may still fall back to `vi` or `nano` on purpose).

### Coding agents: Codex CLI and Grok Build

Terminal coding agents follow the same convention — **readline/Emacs keys by default, Vim keys opt-in**:

- **[OpenAI Codex CLI](https://github.com/openai/codex):** Toggle with `/vim` in a session, or set `vim_mode_default = true` under `[tui]` in `~/.codex/config.toml` (starts in Normal mode — hit `i` to type before blaming your keyboard). [Slash commands](https://developers.openai.com/codex/cli/slash-commands) · [Config reference](https://developers.openai.com/codex/config-reference)
- **[Grok Build](https://docs.x.ai/build/overview):** Splits prompt Vim editing (`[ui] simple_mode = false`, toggled in `/settings`) from scrollback Vim navigation (`[ui] vim_mode = true`, toggled with `/vim-mode`) in `~/.grok/config.toml`. [Keyboard shortcuts](https://docs.x.ai/build/keyboard-shortcuts) · [Modes & commands](https://docs.x.ai/build/modes-and-commands)

Learn one keybinding family well and it follows you from Bash to Git to agents.

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

[← Two-hour path](../README.md#if-you-are-new) | [Next: five minutes in scratch/ →](basic_shell.md#1-five-minutes-in-scratch)
