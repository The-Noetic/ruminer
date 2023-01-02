---
date: 2022-12-31
categories:
  - Dev
  - Coding
---

# Minimal development setup for programming

A functional development setup is essential for productive programming sessions. Over the past years
I have continuously upgraded my programming setup striving to improve it. Each developer has their
own preferences when it comes to the tools they use for coding. 

## Terminal

I'm a big fan of the CLI and hence have constructed my workflow around the terminal. Naturally the
choice of terminal is the most important one. I went with [Alacritty](https://alacritty.org/) an
OpenGL terminal emulator that is super fast (Claims to be the fastest temrinal), minimal and ships
with sensible defaults. Some of the features include Vi mode for moving around and selections, and
built-in search. 

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
- { key: S,        mods: Alt,     chars: "\x1bs"                       }
- { key: C,        mods: Alt,     chars: "\x1bC"                       }
```

## Editor

For a long time I was using Neovim until I came across [helix](https://helix-editor.com/). 
Neovim is fast, lightweight and highly extensible with a rich plugin ecosystem supported by a large
community. However, configuring neovim is an arduous process and often takes a few hours of hacking
to get it right exactly. That and the need for constantly looking out for new plugins for the features
I need was tiring. Moreover it's not easy to port the configuration from one machine to another.

Helix on the other hand is refreshingly functional, minimal and fast out of the box. It ships with
inbuilt support for Tree-Sitter, LSPs, multiple-cursors, Fuzzy finder, navigation of syntax tree nodes
and many more. It has all the functionality I need out of the box without need for any configuration. 
Written in Rust, Helix is exceptionally fast and handles large files with ease. Considering it has
been around only for a year I find Helix to be impressive. The development community is active and
a plugin ecosystem is expected to be implemented in future releases.

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
