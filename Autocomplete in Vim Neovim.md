Type code, the editor tires to guess what you're typing (functions, variables etc.). 
An Autocomplete engine shows suggestions. 

Optionally, a language server helps with smarter completions. 

On Linux, this typically involves. 

`nvim-cmp` - Completion engines 

`lspconfig` - configures language servers for LSP support. 

Snippet Plugin - Adds code snippets (`luasnip, vsnip, ultisnips`). 

Source Plugins - Feed completion suggestions from different sources. 

LSP's - provides language intelligence tools - code completion, syntax highlighting and marking warnings and errors. 

### Package Manager
## `lazy.nvim`
Lazy due to the lazy loading, better config. 
Declarative plugin setup : `lua/plugins/*.lua` structure. 
Built almost entirely in Lua. 

We load `lazy.nvim` into a special path. 

Declare plugin in a Lua Table, usually in `lua/plugins/*.lua` files. 

It runs on startup, reads your plugin specs. 
- Clones if needed. 
- 