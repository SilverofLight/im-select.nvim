# im-select.nvim

[中文](../README.md)/English

Based on keaising/im-select.nvim, a hybrid input feature has been added. It can be enabled in the configuration with `hybrid_mode = true`, and it is set to `false` by default.

When the hybrid input mode is enabled, it can automatically determine whether to switch the input method after entering a space. If the character before the space is a Chinese character or a full-width symbol, it switches to the default input method (which is usually set to English input method).

If the character before the space is English, it checks whether the character before this word is an English character. If it is, it does nothing; otherwise, it switches to the previous input method.

Add a `second_method_selected` option. When a character is deleted, if the character before the cursor is an English character or a half-width symbol, switch to the default input method; otherwise, switch to `second_method_selected`.

Switch Input Method automatically depends on NeoVim's edit mode.

The old vim plugins (such as [im-select](https://github.com/daipeihust/im-select)) works weird on my Macbook, so I just create this im-select in pure lua for NeoVim, it works charmingly!

Current version works for NeoVim on:

- macOS
- Windows and WSL
- Linux
  - Fcitx5
  - Fcitx(only switch between inactive and active)
  - IBus

Other frameworks on Linux's support is welcome!

## DEMO

[DEMO](https://youtu.be/wRJ-rMcvqTk)(youtube)

## 1. Install and check binary

`im-select.nvim` use binary tools to switch IM, you need to:

1. Install binary tools on different OS.
2. Make sure the executable file in a path that NeoVim can read them.

### 1.1 Windows / WSL

#### Install

Please install `im-select.exe` and put it into your `PATH`.

Download URL: [im-select](https://github.com/daipeihust/im-select)
(For `x64` platform, please download the `64-bit` version.)

#### Check

You can check if the `im-select` executable can be properly accessed from Neovim/Vim by running the following command from your Command Prompt:

```bash
# find the command
$ where im-select.exe

# Get current im name
$ im-select.exe

# Try to switch to English keyboard
$ im-select.exe 1033
```

Or run shell command directly from NeoVim

```bash
:!where im-select.exe

:!im-select.exe 1003
```

### 1.2 macOS

#### Install

Please install `macism`

Download URL: [macism](https://github.com/laishulu/macism)

#### Check

Check installation in bash/zsh

```bash
# find binary
$ which macism

# Get current im name
$ macism
com.apple.keylayout.ABC

# Try to switch to English keyboard
$ macism com.apple.keylayout.ABC
```

Check in NeoVim

```bash
:!which macism
```

### 1.3 Linux

#### Install

Please install and config one of Input Methods: Fcitx / Fcitx5 / IBus

#### Check

Check installation in bash/zsh

**> Fcitx**

```bash
# find
$ which fcitx-remote

# activate IM
$ fcitx-remote -o

# inactivate IM
$ fcitx-remote -c
```

**> Fcitx5**

```bash
# find
$ which fcitx5-remote

# Get current im name
$ fcitx5-remote -n

# Try to switch to English keyboard
$ fcitx5-remote keyboard-us
```

**> IBus**

```bash
# find
$ which ibus

# Get current im name
$ ibus engine

# Try to switch to English keyboard
$ ibus engine xkb:us::eng
```

Check in NeoVim

```bash
# find
:!which fcitx
:!which fcitx5
:!which ibus
```

## 2. Install and setup this plugin

A good-enough minimal config in Lazy.nvim

```lua
{
    "SilverofLight/im-select.nvim",
    config = function()
        require("im_select").setup({})
    end,
}
```

Options with its default values

```lua
{
    "SilverofLight/im-select.nvim",
    config = function()
        require('im_select').setup({
            -- IM will be set to `default_im_select` in `normal` mode
            -- For Windows/WSL, default: "1033", aka: English US Keyboard
            -- For macOS, default: "com.apple.keylayout.ABC", aka: US
            -- For Linux, default:
            --               "keyboard-us" for Fcitx5
            --               "1" for Fcitx
            --               "xkb:us::eng" for ibus
            -- You can use `im-select` or `fcitx5-remote -n` to get the IM's name
            default_im_select  = "com.apple.keylayout.ABC",

            -- Can be binary's name, binary's full path, or a table, e.g. 'im-select',
            -- '/usr/local/bin/im-select' for binary without extra arguments,
            -- or { "AIMSwitcher.exe", "--imm" } for binary need extra arguments to work.
            -- For Windows/WSL, default: "im-select.exe"
            -- For macOS, default: "macism"
            -- For Linux, default: "fcitx5-remote" or "fcitx-remote" or "ibus"
            default_command = "im-select.exe",

            -- Restore the default input method state when the following events are triggered
            set_default_events = { "VimEnter", "FocusGained", "InsertLeave", "CmdlineLeave" },

            -- Restore the previous used input method state when the following events
            -- are triggered, if you don't want to restore previous used im in Insert mode,
            -- e.g. deprecated `disable_auto_restore = 1`, just let it empty
            -- as `set_previous_events = {}`
            set_previous_events = { "InsertEnter" },

            -- Show notification about how to install executable binary when binary missed
            keep_quiet_on_no_binary = false,

            -- Async run `default_command` to switch IM or not
            async_switch_im = true
            hybrid_mode = false
            second_method_selected = "shuangpin",
        })
    end,
}
```

