# nrun.vim
Vim-native "which" and "exec" functions targeted at local node project bin, falling back to `which`, for easy vim integration with dev-depenendencies in node-based build processes.

## Install
Place `autoload/nrun.vim` in your autoload directory, or add to your config with plugin tool of your choice (`Plug 'jaawerth/nrun.vim'` with [vim-plug](https://github.com/junegunn/vim-plug), `Plugin 'jaawerth/nrun.vim'` with [Vundle](https://github.com/VundleVim/Vundle.vim), etc)

## Usage Examples

### With vim + [Syntastic](https://github.com/scrooloose/syntastic)
In your `~/.vimrc` or in a javascript ftplugin
```vim
au BufEnter *.js let b:syntastic_javascript_eslint_exec = nrun#Which('eslint')
```

### With [neovim](https://github.com/neovim/neovim) + [Neomake](https://github.com/benekastah/neomake)
This is my favorite setup. In your `~/<nvim-config>/init.vim` or `~/<nvim-config>/ftplugin/javascript.vim`:
```nvim
" you can set your enabled makers globally (like below) or on the buffer level as part of an autocmd - see Neomake docs for details
let g:neomake_javascript_enabled_makers = ['eslint']
" when switching/opening a JS buffer, set neomake's eslint path, and enable it as a maker
au BufEnter *.js let b:neomake_javascript_eslint_exe = nrun#Which('eslint')
```
For more on Neomake config, see their docs - I like to combine the above with:
```vimscript
au BufWinEnter,BufWritePost *.js,*.jsx,*.whateverelse Neomake
```
or simply
```vimscript
au BufWinEnter,BufWritePost * Neomake
```

## Functions
### nrun#Which('command')
A "which" that first tries to see if you're in a node project (traversing the current directly up looking for `package.json` and `node_modules/.bin/<command>`), falling back to "which" if root or home folders are hit without a match.

### nrun#Exec('command')
Executes the above and passes the results to system() for you. Throws if no command found.

### nrun#StrTrim(string)
Trim the whitespace around a string (used internally to account for the newline from `which`)

## Configuration
### Single function call
To **change** the fallback command, pass it in as the second argument. For example, on Windows shell you may want to use `where`: `nrun#Which('tape', 'where')`

To **disable** fallback so it only looks within the local project, pass in **0** as the second argumetN: `nrun#Which('tape', 0)`

### Global
`g:nrun_which_cmd` can be set to globally set a fallback, and `let g:nrun_disable_which = 1` will disable it unless overriden by the function call.

## What's the point?
Eases the pain of integrating vim into your JavaScript/node build tools.

Many of the development and build processes inherent to node require the use of locally-installed executables for linting, testing, building, etc. While npm scripts often work well for this, it can be inconvenient to set an npm script for every runnable dependency. There's `npm bin` to find your local bin directory, but it's noticeably slow, and can bog down vim plugins.

For a time I used a bash script, or the node-based [npm-which](https://www.npmjs.com/package/npm-which), but preferred a self-contained vimscript implementation with no global dependencies (`which` aside).
