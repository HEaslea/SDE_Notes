They provide protocols: 
- Autocompletion
- "Go to definition"
- Hover info
- Diagnostics
- Refactoring

**TO BE CLEAR**: they are not servers, in a networking sense. They are local protocols and programs (often CLI tools) that run in the background while we do our thing. 

The idea of a Server, can also mean that we just have a request-response. That eludes to how this all works. 
The usually communicate with the editor in a `stdin/stdout` or local sockets, not over the internet at all. 

C++     -> `clangd` or `ccls`

Python -> `pyright` `pylance`

Rust     -> `rust-analyzer`

[[Language Servers]]

The protocols are the ways in which the editors and the language servers are talking to each other. 

### How? 
Editor starts a language server. 
For C++ that might be `clangd`. 

LSP communication then begins. 
VSCode (the client in this case), and the language server (the server in this case) talk via JSON-RPC over `stdin/stdout`, which are streams for input, output etc. 

The server listens for standardized requests like: 

The request might look something like this: 
```
{ 
	"method" : "textDocument/completion", 
	"params" : {...}
}
```
The server might reply something like: 
```
{ 
	"items" : ["std::vector", "std::map", ...]
}
```

This means that we are decoupling the editor from the language server, and LSP makes that possible. 

This means that we can use the same server `pyright` for instance, in other editors `vim` `emacs`, if they support LSP. 

##### Simple Example

We open a `.cpp` file. 
VSCode (or whichever editor) starts `clangd`. 
You might start typing `std::v..` the editor asks `clangd` for completions
`clangd` replies with `std::vector` etc.
Then the editor needs a way to display that. 


