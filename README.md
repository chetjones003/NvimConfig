<a name="readme-top"></a>

<!-- PROJECT SHIELDS -->
[![Contributors][contributors-shield]][contributors-url]
[![Forks][forks-shield]][forks-url]
[![Stargazers][stars-shield]][stars-url]
[![Issues][issues-shield]][issues-url]
[![MIT License][license-shield]][license-url]
[![LinkedIn][linkedin-shield]][linkedin-url]

<!-- PROJECT LOGO -->
<br />
<div align="center">
  <a 
    href="https://github.com/chetjones003/NvimConfig">
    <img src="images/neovimlogo.png" alt="Logo" width="80" height="80">
  </a>

<h3 align="center">Neovim Lua Configuration</h3>

  <p align="center">
    My personal configuration of neovim. Simple and lightweight while trying to stick to default neovim as much as possible. Featuring plugins for editing markdown, latex, rust and python.
    <br />
    <a href="https://github.com/chetjones003/NvimConfig/issues">Report Bug</a>
    ·
    <a href="https://github.com/chetjones003/NvimConfig/issues">Request Feature</a>
  </p>
</div>

# Table of Contents
1. [Try out this config](#Try-out-this-config)
2. [Prerequisites](#Prerequisites)
3. [Upgrade to Neovim v0.8.0](#Upgrade-to-Neovim-v0.8.0)
4. [Plugins](#Plugins)
    * [Packer](#Packer)
    * [Undo Tree](#Undo-Tree)
    * [Treesitter](#Treesitter)
    * [Telescope](#Telescope)
    * [Plenary](#Plenary)
    * [Lsp-Zero](#Lsp-Zero)
5. [Options](#Options)
6. [Contributing](#Contributing)

## Try out this config

This config requires [Neovim v0.8.0](https://github.com/neovim/neovim/releases). Please [upgrade](#upgrade-to-neovim-v080) if you're on an earlier version of the editor.

Clone the repository into the correct location (make a backup your current `nvim` directory if you want to keep it).

```
git clone https://github.com/chetjones003/NvimConfig ~/.config/nvim
```

**NOTE** [Mason](https://github.com/williamboman/mason.nvim) is used to install and manage LSP servers, DAP servers, linters, and formatters via the `:Mason` command.

## Prerequisites

You will need [python](https://www.python.org/downloads/) and [node](https://nodejs.org/en/) support for these plugins to work.

After you have installed python and node paste these commands into your terminal.

- Neovim python support

  ```
  pip install pynvim
  ```

- Neovim node support

  ```
  npm i -g neovim
  ```

**NOTE**  I recommend a node manager like [fnm](https://github.com/Schniz/fnm).


## Upgrade to Neovim v0.8.0

Assuming you [built from source](https://github.com/neovim/neovim/wiki/Building-Neovim#quick-start), `cd` into the folder where you cloned `neovim` and run the following commands. 
```
git pull
make distclean && make CMAKE_BUILD_TYPE=Release
git checkout v0.8.0
sudo make install
nvim -v
```

## Plugins

### Packer
[Packer](https://github.com/wbthomason/packer.nvim) is a plugin/package manager for Neovim inspired by [use-package](https://github.com/jwiegley/use-package).

Reasons I chose packer:
1. Uses native packages
2. Written in Lua and configured in Lua
3. Supports lazy loading of plugins
4. Supports local plugins

I have decided not to include automatic installing of plugins because I like doing everything manually. But, if you wish to have this feature then your packer.lua file should look like this.

```lua
local fn = vim.fn

-- Automatically install packer
local install_path = fn.stdpath("data") .. "/site/pack/packer/start/packer.nvim"
if fn.empty(fn.glob(install_path)) > 0 then
	PACKER_BOOTSTRAP = fn.system({
		"git",
		"clone",
		"--depth",
		"1",
		"https://github.com/wbthomason/packer.nvim",
		install_path,
	})
	print("Installing packer close and reopen Neovim...")
	vim.cmd([[packadd packer.nvim]])
end

-- Autocommand that reloads neovim whenever you save the plugins.lua file
vim.cmd([[
  augroup packer_user_config
    autocmd!
    autocmd BufWritePost plugins.lua source <afile> | PackerSync
  augroup end
]])

-- Use a protected call so we don't error out on first use
local status_ok, packer = pcall(require, "packer")
if not status_ok then
	return
end

-- Have packer use a popup window
packer.init({
	display = {
		open_fn = function()
			return require("packer.util").float({ border = "rounded" })
		end,
	},
})

-- Install your plugins here
return packer.startup(function(use)
    -- Packer can manage itself
    use 'wbthomason/packer.nvim'

	-- To Automatically set up your configuration after cloning packer.nvim
	-- Put this at the end after all plugins
	if PACKER_BOOTSTRAP then
		require("packer").sync()
	end
end)
```

### Undo Tree
[Undo Tree](https://github.com/mbbill/undotree) can visualize your undo history and allows you to browse and switch between different undo branches. This feature is built into neovim, this plugin just allows you to visualize it.

1. Markers
    * Every changes has a sequence number and it is displayed before timestamps.
    * The current state is marked as `> number <`.
    * The next state which will be restored by `:redo` or `<ctrl-r>` is marked as `{ number }`.
    * The `[ number ] marks the most recent change.
    * The undo history is sorted by timestamps.
    * Saved changes are marked as `s` and the big `S` indicates the most recent saved change.
2. Press `?` in undotree window for quick help.
3. Persistent undo
    * You can store the undo files in a separate place like below:

```bash
if has("persistent_undo")
   let target_path = expand('~/.undodir')

    " create the directory and any parent directories
    " if the location does not exist.
    if !isdirectory(target_path)
        call mkdir(target_path, "p", 0700)
    endif

    let &undodir=target_path
    set undofile
endif
```

Configuration

[Here](https://github.com/mbbill/undotree/blob/master/plugin/undotree.vim#L15) is a list of options

### Treesitter
Treesitter is a plugin that parses and builds a syntax tree for a source file and can update the syntax tree as the source file is edited. 

Installing a language is as simple as:
```
:TSInstall <language_to_install>
```

You can ensure some languages are installed by adding them in the tresitter.lua file like this:

```
ensure_installed = { "<language>" }
```

### Telescope
Awesome plugin with great extensibility. See [Docs](https://github.com/nvim-telescope/telescope.nvim)

### Plenary
[Plenary](https://github.com/nvim-lua/plenary.nvim) is a library of lua functions and is a must have.

### Lsp-Zero
[Lsp-Zero](https://github.com/VonHeikemen/lsp-zero.nvim) is a one-stop, minimal coding, out-of-the-box plugin for getting lsp in neovim.

It relies on these plugins to work:
1. [lspconfig](https://github.com/neovim/nvim-lspconfig)
2. [mason](https://github.com/williamboman/mason.nvim)
3. [mason-lspconfig](https://github.com/williamboman/mason-lspconfig.nvim)
4. [nvim-cmp](https://github.com/hrsh7th/nvim-cmp)
5. [cmp-nvim-lsp](https://github.com/hrsh7h/cmp-nvim-lsp)
6. [cmp-buffer](https://github.com/hrsh7th/cmp-buffer)
7. [cmp-path](https://github.com/hrsh7th/cmp-path)
8. [cmp_luasnip](https://github.com/saadparwaiz1/cmp_luasnip)
9. [cmp-nvim-lua](https://github.com/hrsh7th/cmp-nvim-lua)
10. [LuaSnip](https://github.com/L3MON4D3/LuaSnip)
11. [friendly-snippets](https://github.com/rafamadriz/friendly-snippets)

The only setup needed is:
```lua
local lsp = require('lsp-zero')
lsp.preset('recommended')

-- (Optional) Configure lua language server for neovim
lsp.nvim_workspace()

lsp.setup()
```

The recommended preset will enable automatic suggestions of language servers. So any time you open a filetype for the first lsp-zero will ask you to install a language server that supports it.

## Options

Setting the colorsheme and transparent background
```lua
vim.cmd.colorscheme 'melange'
vim.api.nvim_set_hl(0, "Normal", { bg = "none" })
vim.api.nvim_set_hl(0, "NormalFloat", { bg = "none" })
```

Relative line numbers
```lua
vim.opt.nu = true
vim.opt.relativenumber = true
```

Tab sizes
```lua
vim.opt.tabstop = 4
vim.opt.softtabstop = 4
vim.opt.shiftwidth = 4
vim.opt.expandtab = true
```

Word wrap
```lua
vim.opt.wrap = true
```

Don't generate swap or backup file and set location of undodir
```lua
vim.opt.swapfile = false
vim.opt.backup = false
vim.opt.undodir = os.getenv("HOME") .. "/.vim/undodir"
vim.opt.undofile = true
```

Better `:/` search
```lua
vim.opt.hlsearch = false
vim.opt.incsearch = true
```

Excellent
```lua
vim.opt.termguicolors = true
```

Speed up neovim
```lua
vim.opt.updatetime = 50
```


Can't hurt to specify
```lua
vim.g.mapleader = " "
```

<!-- CONTRIBUTING -->
## Contributing

Contributions are what make the open source community such an amazing place to learn, inspire, and create. Any contributions you make are **greatly appreciated**.

If you have a suggestion that would make this better, please fork the repo and create a pull request. You can also simply open an issue with the tag "enhancement".
Don't forget to give the project a star! Thanks again!

1. Fork the Project
2. Create your Feature Branch (`git checkout -b feature/AmazingFeature`)
3. Commit your Changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the Branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

<p align="right">(<a href="#readme-top">back to top</a>)</p>


<!-- MARKDOWN LINKS & IMAGES -->
<!-- https://www.markdownguide.org/basic-syntax/#reference-style-links -->
[contributors-shield]: https://img.shields.io/github/contributors/chetjones003/NvimConfig.svg?style=for-the-badge
[contributors-url]: https://github.com/chetjones003/NvimConfig/graphs/contributors
[forks-shield]: https://img.shields.io/github/forks/chetjones003/NvimConfig.svg?style=for-the-badge
[forks-url]: https://github.com/chetjones003/NvimConfig/network/members
[stars-shield]: https://img.shields.io/github/stars/chetjones003/NvimConfig.svg?style=for-the-badge
[stars-url]: https://github.com/chetjones003/NvimConfig/stargazers
[issues-shield]: https://img.shields.io/github/issues/chetjones003/NvimConfig.svg?style=for-the-badge
[issues-url]: https://github.com/chetjones003/NvimConfig/issues
[license-shield]: https://img.shields.io/github/license/chetjones003/NvimConfig?style=for-the-badge
[license-url]: https://github.com/chetjones003/NvimConfig/blob/master/LICENSE.md
[linkedin-shield]: https://img.shields.io/badge/-LinkedIn-black.svg?style=for-the-badge&logo=linkedin&colorB=555
[linkedin-url]: https://linkedin.com/in/chet-jones-767205202
[product-screenshot]: images/screenshot.png
