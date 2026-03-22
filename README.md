# hide-comment.lua

Hide comment lines using Neovim's built-in conceal feature, with smart cursor correction for concealed regions.

![Alt text](hide-comment.gif)

## Fork Note

The [original repository](https://github.com/wroyca/hide-comment.nvim) is gone.

This plugin was originally created by William Roy as seen in the [license](LICENSE.md). They appear
to have abandoned and deleted the plugin. I took this copy from my local neovim config at some
arbitrary date because I liked it despite the issues. I've slopped-up some fixes to address issues.


* hijacking key bindings is now window local
* save/restore conceal settings per window
* debounce recomputation of concealed sections on change
* improve language detection so aliases (rb -> ruby, tsx -> typescript) are handled properly
* improvements to partial-comment line handling

Now there are two modes for addressing a cursor that moved into a concealed region
  * reveal: we unhide that region containing the cursor
  * skip: move the cursor out of the concealed region (forward or back depending on movement dir)

Movement is still quite quirky and maybe I'll grow to hate this plugin and abandon it like the
original author. There's a good reason most of these "hide a comment" plugins default to just
folding them and not removing them entirely. (Neo)vim has been handling folded sections for a long
time and we shouldn't be re-inventing the world.

## Features

- **Comment Detection**: Automatically detects comment lines using treesitter queries
- **Smart Navigation**: Vertical `j/k` stays visual and motion landings can skip or temporarily reveal concealed regions
- **Auto Enable**: Optionally auto-enable for all supported filetypes
- **Buffer Local**: Support for buffer-local configuration
- **Refresh on Change**: Automatically refresh when buffer content changes
- **User Commands**: Easy-to-use commands for controlling comment hiding

## Installation

### With [lazy.nvim](https://github.com/folke/lazy.nvim)

```lua
{
  "cutehax0r/hide-comment.nvim",
  config = function ()
    require ("hide-comment").setup ({
      -- Your configuration here
    })
  end
}
```

### With [packer.nvim](https://github.com/wbthomason/packer.nvim)

```lua
use {
  "cutehax0r/hide-comment.nvim",
  config = function ()
    require ("hide-comment").setup ()
  end
}
```

## Setup

```lua
require ("hide-comment").setup ({
  -- Whether to automatically enable for all supported languages
  auto_enable = false,

  -- Whether to keep cursor out of concealed regions after motions
  smart_navigation = true,

  -- How smart navigation handles concealed landings: "skip" | "reveal"
  smart_navigation_mode = "skip",

  -- The conceallevel to set when concealing (0-3)
  conceal_level = 3,

  -- Refresh concealing when buffer content changes
  refresh_on_change = true,

  -- Debounce delay for refresh_on_change (in milliseconds)
  refresh_debounce_ms = 100,

  -- Enable debug logging
  debug = false,

  -- Don't show non-error feedback
  silent = false,
})
```

## Usage

### Manual Control

```lua
-- Hide comments in current buffer
HideComment.enable ()

-- Show comments in current buffer
HideComment.disable ()

-- Toggle comments in current buffer
HideComment.toggle ()

-- Check if enabled
if HideComment.is_enabled () then
  print ("Comments are hidden")
end

-- Get statistics
local stats = HideComment.get_stats ()
print (string.format ("Hidden %d/%d lines (%.1f%%)",
       stats.concealed_lines,
       stats.total_lines,
       stats.concealed_percentage))

-- Toggle navigation mode at runtime
HideComment.toggle_navigation_mode ()
```

### User Commands

- `:HideCommentEnable` - Hide comments in current buffer
- `:HideCommentDisable` - Show comments in current buffer
- `:HideCommentToggle` - Toggle comments in current buffer
- `:HideCommentStatus` - Show current status
- `:HideCommentNavMode [skip|reveal|toggle]` - Get or change smart navigation mode

### Buffer-local Configuration

You can override settings per buffer:

```lua
-- Disable auto-refresh for this buffer only
vim.b.hidecomment_config = {
  refresh_on_change = true,
  refresh_debounce_ms = 250,
}
```

### Disabling

Disable globally:
```lua
vim.g.hidecomment_disable = true
```

Disable for current buffer:
```lua
vim.b.hidecomment_disable = true
```

## Requirements

- Neovim >= 11
- Treesitter parser for the target language
- The language must have comment syntax supported by treesitter

## Supported Languages

Any language with treesitter support that includes comment detection should work, including:

- Lua
- JavaScript/TypeScript
- Python
- Rust
- Go
- C/C++
- And many more...

## Development

### Running Tests

```bash
nvim --headless -u scripts/minimal_init.lua -l scripts/run_tests.lua
```

### Generating Documentation

```bash
nvim --headless -u scripts/minimal_init.lua -l scripts/gen_doc.lua
```

## Inspiration

This plugin replicates the functionality of [hide-comnt.el](https://github.com/emacsmirror/hide-comnt) from Emacs, bringing it to Neovim with modern treesitter integration.

## License

MIT License
