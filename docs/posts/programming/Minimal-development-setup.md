---
date: 2022-12-31
comments: true
tags:
  - Productivity
categories:
  - Programming
---

# My Development Setup

Developers have strong personal opinions about the tools they use for programming. Considering we
spend a significant amount of time using these tools, a functional development setup is essential
for productive programming sessions.

<!-- more -->
Over the past years I have continuously upgraded my programming setup striving to improve it.
I prefer a setup that is easy to configure, minimal, functional and lightweight. As a Machine
Learning developer most of my work is in the cloud and for that reason I want my setup to be as
portable as possible so that I can easily configure them on any new instance I spawn.

As a fan of minimalism my setup simply consists of a terminal, a browser and Roam Research. The
tools I use are CLI based and consist of a text editor, a git client and few CLI
Rust utilities all of which are Rust apps. Below are the detailed instructions to recreate my setup.

## Installing Pre-requisites

First order of business install some basic packages to setup the rest of the env. These include
`curl, cmake, g++, git`. MacOS comes with all of them installed. On linux run
```bash
sudo apt install -y curl cmake g++ git
```

Most of my development environment is Rust based and hence we next install Rust with
```bash
curl https://sh.rustup.rs -sSf | sh
```

If you are using MacOS, then it is a good idea to install the [brew](https://brew.sh/) package manager as well.
```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

Next we install an archiver utility called [ouch]() for compressing and decompressinf files by running
`cargo install ouch`

## Terminal

After experimenting with hyper (which was too heavy) and iterm, I went with [Alacritty](https://alacritty.org/) a
Rust based OpenGL terminal emulator that is super fast (Claims to be the fastest temrinal), minimal and ships
with sensible defaults. Some of the features include Vi mode for moving around and selections, and
built-in search. 

### Installation
To install Alacritty run:
```bash
cargo install alacritty
```
Alacritty can be easily configured/extended by specifying the settings in a `alacritty.yml` file
placed in `~/.config/alacritty/`. Below is my configuration

```yaml
window:
  padding:
    x: 25
    y: 15
  dynamic_padding: true
  decorations: buttonless
  dynamic_title: true

scrolling:
  history: 100000

font:
  size: 13
  offset:
    x: 1
    y: 5
  normal:
    family: MonoLisa Nerd Font

# Colors (Gruvbox Material Dark Medium)
colors:
  primary:
    background: '0x282828'
    foreground: '0xdfbf8e'

  normal:
    black:   '0x665c54'
    red:     '0xea6962'
    green:   '0xa9b665'
    yellow:  '0xe78a4e'
    blue:    '0x7daea3'
    magenta: '0xd3869b'
    cyan:    '0x89b482'
    white:   '0xdfbf8e'

  bright:
    black:   '0x928374'
    red:     '0xea6962'
    green:   '0xa9b665'
    yellow:  '0xe3a84e'
    blue:    '0x7daea3'
    magenta: '0xd3869b'
    cyan:    '0x89b482'
    white:   '0xdfbf8e'
 
key_bindings:
- { key: S, mods: Alt, chars: "\x1bs" }
- { key: C, mods: Alt, chars: "\x1bC" }
- { key: Period, mods: Alt, chars: "\x1b." }
- { key: Semicolon, mods: Alt, chars: "\x1b;" }
```

## Editor

For a long time I was using Neovim until I came across [helix](https://helix-editor.com/). 
Neovim is fast, lightweight and highly extensible with a rich plugin ecosystem supported by a large
community. However, configuring neovim is an arduous process and often takes a several hours of hacking
to get it working correctly. This also makes it difficult to recreate the setup on multiple machines.

Helix is a Rust based terminal that is refreshingly functional, minimal and fast out of the box. It ships with
inbuilt support for Tree-Sitter, LSPs, multiple-cursors, Fuzzy finder, navigation of syntax tree nodes
and many more. It has all the functionality I need out of the box without need for any plugins/complex
configuration. 
Written in Rust, Helix is exceptionally fast and handles large files with ease. Considering it has
been around only for a year I find Helix to be impressive. The development community is active and
a plugin ecosystem is expected to be implemented in future releases.

### Installation
On the MacOS Helix can be installed with
```bash
brew install helix
```

On Linux, download the binary from [here](https://github.com/helix-editor/helix/releases). Extract
the contents and copy the `hx` binary to `~/.local/bin` or `/usr/opt/bin`. If you use the former
ensure that `~/.local/bin` exists in your `PATH`. Then move the `runtime` folder to `~/.config/helix`.
As the final step run:
```bash
hx --grammar fetch
hx --grammar build
```

Helix can also be configured like alacritty via a `config.toml` placed in `~/.config/helix/`. My
config simply installs the gruvbox material theme and the buffer and status lines.

```toml
theme = "gruvbox_material_dark_soft"

[editor]
bufferline = "always"
color-modes = true
true-color = true

[editor.statusline]
left = ['mode', 'file-name']
right = ["diagnostics", "position", "total-line-numbers", "spacer", "file-encoding", "file-type"]
mode.normal = "NORMAL"
mode.insert = "INSERT"
mode.select = "SELECT"

[keys.insert]
j = { k = "normal_mode" }

[keys.normal]
C-o = ":open ~/.config/helix/config.toml"
'$' = "no_op"
'$' = "goto_line_end"
'0' = "goto_line_start"

[editor.indent-guides]
render = true
```

## Terminal Multiplexer

Without doubt the most mature and feature-rich terminal multiplexer is [tmux](https://github.com/tmux/tmux/wiki).
Tmux is lightweight and can be as minimal or feature-rich as you want it to be. It does require a little bit of
tweaking to suit my needs but nothing too complex. Tmux is configured by a `.tmux.conf` file placed in the root
directory or inside the `~/.config`

There's plenty of open source tmux configs available enabling a variety of features. Below is my configuration 
for tmux that I have amassed over several sources to suit my needs. 

```
# --------#
# General #
# --------#

# Default shell
set -g default-shell $SHELL

# Mouse
set -g mouse on

# History
set -g history-limit 102400

# Set windows and page index to base 1
set -g base-index 1
setw -g pane-base-index 1

# Re-number windows when creating/closing new windows
set -g renumber-windows on

# Use vim key bindings in status line
set-option -g status-keys vi

# Use vim key bindings in copy mode
setw -g mode-keys vi

# Fix ESC delay in vim
set -g escape-time 10

# ------------#
# Keybindings #
# ------------#

# Set prefix to C-a
# unbind C-b
# set -g prefix C-a

# Copy-mode
unbind-key -T copy-mode-vi v
bind-key -T copy-mode-vi v send-keys -X begin-selection
bind-key -T copy-mode-vi 'C-v' send-keys -X rectangle-toggle
bind-key -T copy-mode-vi y send-keys -X copy-pipe "pbcopy"
bind-key -T copy-mode-vi MouseDragEnd1Pane send-keys -X copy-pipe-and-cancel "pbcopy"
bind-key -T copy-mode-vi Escape send-keys -X cancel

# Send command on double press
bind C-a send-prefix
bind C-l send-keys 'C-l'

# Reload tmux.conf on prefix r
bind r source-file ~/.tmux.conf \; display "Config reloaded!"

# Split panes and remember current path
bind '\' split-window -h -c '#{pane_current_path}'
bind - split-window -v -c '#{pane_current_path}'

# Remember current path when creating new windows
bind c new-window -c '#{pane_current_path}'

# Break pane into new window and keep focus on the current window
bind b break-pane -d

# Smart pane switching with awareness of Vim splits.
is_vim="ps -o state= -o comm= -t '#{pane_tty}' \
    | grep -iqE '^[^TXZ ]+ +(\\S+\\/)?g?(view|n?vim?x?)(diff)?$'"
bind-key -n C-h if-shell "$is_vim" "send-keys C-h"  "select-pane -L"
bind-key -n C-j if-shell "$is_vim" "send-keys C-j"  "select-pane -D"
bind-key -n C-k if-shell "$is_vim" "send-keys C-k"  "select-pane -U"
bind-key -n C-l if-shell "$is_vim" "send-keys C-l"  "select-pane -R"
bind-key -n 'C-\' if-shell "$is_vim" "send-keys 'C-\'" "select-pane -l"
bind-key -T copy-mode-vi C-h select-pane -L
bind-key -T copy-mode-vi C-j select-pane -D
bind-key -T copy-mode-vi C-k select-pane -U
bind-key -T copy-mode-vi C-l select-pane -R
bind-key -T copy-mode-vi 'C-\' select-pane -l

# -----------#
# Status bar #
# -----------#

set-option -g status-justify left
set-option -g status-left '#[bg=colour72] #[bg=colour237] #[bg=colour236] #[bg=colour235]#[fg=colour185] #S #[bg=colour236] '
set-option -g status-left-length 16
set-option -g status-bg colour237
set-option -g status-right '#[bg=colour236] #[bg=colour235]#[fg=colour185] %a %R #[bg=colour236]#[fg=colour3] #[bg=colour237] #[bg=colour72] #[]'
set-option -g status-interval 60

set-option -g pane-active-border-style fg=colour246
set-option -g pane-border-style fg=colour238

set-window-option -g window-status-format '#[bg=colour238]#[fg=colour107] #I #[bg=colour239]#[fg=colour110] #[bg=colour240]#W#[bg=colour239]#[fg=colour195]#F#[bg=colour238] '
set-window-option -g window-status-current-format '#[bg=colour236]#[fg=colour215] #I #[bg=colour235]#[fg=colour167] #[bg=colour234]#W#[bg=colour235]#[fg=colour195]#F#[bg=colour236] '

setw -g monitor-activity on
set -g visual-activity on

# resize panes
bind -r h resize-pane -L 5
bind -r j resize-pane -D 5
bind -r k resize-pane -U 5
bind -r l resize-pane -R 5

```
## Git Client

Another essential utility I need is a git client. Although the git CLI provides a rich
set of functionality some operations are just easier with a dedicated program.
My choice of git client is another Rust based terminal interface app called [gitui](https://github.com/extrawurst/gitui)
gitui is blazing fast and supports all features I need.

There were a couple things I configured which were the key-bindings and minor tweaks to the UI. The default
UI had some readability issues which were easily fixed by changing colors.

### Installation
gitui can be installed from its crate by running
```bash
cargo install gitui
```

The key bindings can be configured by putting a `key_bindings.ron` file in `~/.config/gitui` and the theme via
a `theme.ron` in the same directory.

My `key_bindings.ron`

```ron
(
    focus_right: Some(( code: Char('l'), modifiers: ( bits: 0,),)),
    focus_left: Some(( code: Char('h'), modifiers: ( bits: 0,),)),
    focus_above: Some(( code: Char('k'), modifiers: ( bits: 0,),)),
    focus_below: Some(( code: Char('j'), modifiers: ( bits: 0,),)),
    
    move_left: Some(( code: Char('h'), modifiers: ( bits: 0,),)),
    move_right: Some(( code: Char('l'), modifiers: ( bits: 0,),)),
    move_up: Some(( code: Char('k'), modifiers: ( bits: 0,),)),
    move_down: Some(( code: Char('j'), modifiers: ( bits: 0,),)),

    open_help: Some(( code: F(1), modifiers: ( bits: 0,),)),
)
```

My `theme.ron`

```ron
(
    selected_tab: Reset,
    command_fg: White,
    selection_bg: Blue,
    selection_fg: White,
    cmdbar_bg: Blue,
    cmdbar_extra_lines_bg: Blue,
    disabled_fg: DarkGray,
    diff_line_add: Green,
    diff_line_delete: Red,
    diff_file_added: LightGreen,
    diff_file_removed: LightRed,
    diff_file_moved: LightMagenta,
    diff_file_modified: Yellow,
    commit_hash: Magenta,
    commit_time: LightCyan,
    commit_author: Green,
    danger_fg: Red,
    push_gauge_bg: Blue,
    push_gauge_fg: Reset,
    tag_fg: LightMagenta,
    branch_fg: LightYellow,
)
```

## Terminal Utilities

Apart from the above tools, I like to replace few of the bash commands/apps with a better version of them
written in Rust. Some of them are for appearance and some for functionality. All of these apps can be
installed via cargo by running `cargo install <app-name>`

- [Starship](https://starship.rs) is a custom command cross-shell prompt that is minimal, customizable and blazing fast.
  - Add `eval "$(starship init bash)"` to your `.bashrc`
- [xcp](https://github.com/tarka/xcp/) is a clone of the Unix `cp` which is optimized and more efficient.
- [lsd](https://github.com/Peltoche/lsd) is a rewrite of GNU `ls` with added features like color, icons, tree-view etc
- [bartib](https://github.com/nikolassv/bartib) A small command line tool for time tracking
- [topgrade](https://github.com/topgrade-rs/topgrade) A tool that detects and updates all apps across all package managers

