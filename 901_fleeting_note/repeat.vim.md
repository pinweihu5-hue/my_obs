[[vim_idx]]


make your custom mapping repeatable

[http://vimcasts.org/episodes/creating-repeatable-mappings-with-repeat-vim/](http://vimcasts.org/episodes/creating-repeatable-mappings-with-repeat-vim/)

3 example

```tsx
" ex1
                                        
"EmptyLineAbove is the mapping, and will be used in 3 places
noremap <silent> <Plug>EmptyLineAbove mmO<ESC>`m:call repeat#set("\\<Plug>EmptyLineAbove")<CR> 
                                      ^^^^^^^^^^ <--- the actual mapping
nmap <leader>O <Plug>EmptyLineAbove

"ex2
nnoremap <silent> <Plug>TransposeCharacters xp:call repeat#set("\\<Plug>TransposeCharacters")<CR>
nmap cp <Plug>TransposeCharacters
 
 
noremap <silent> <Plug>EmptyLineAbove :VimwikiChangeSymbolTo -<CR>:call repeat#set("\\<Plug>EmptyLineAbove")<CR>
                                      ^^^^^^^^^^^^^^^^^^^^^^^^^^^^ <--- the actual mapping, call other plugin
autocmd FileType vimwiki nmap <buffer> <leader>l <Plug>EmptyLineAbove
```


