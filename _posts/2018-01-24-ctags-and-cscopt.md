

# References
https://www.embeddedarm.com/blog/tag-jumping-in-a-codebase-using-ctags-and-cscope-in-vim/
https://ricostacruz.com/til/navigate-code-with-ctags

# index all tags for ctag and cscope

bash script `cscope_ctags_gen.sh`

```
#!/bin/sh
find . -name '*.py' \
-o -name '*.java'   \
-o -iname '*.[ch]'  \
-o -iname '*.[CH]'  \
-o -name '*.cpp'    \
-o -name '*.cc'     \
-o -name '*.hpp'    \
-o -name '*.asm'    \
-o -name '*.S'      \
> cscope.files

# -b: just build
# -q: create inverted index
cscope -b -q  # this will gives cscope database cscope.out

ctags -L cscope.files
```

# how ctags works

* Ctags options in ~/.ctags

```
--recurse=yes
--exclude=.git
--exclude=vendor/*
--exclude=node_modules/*
--exclude=db/*
--exclude=log/*
```

* Generate tag file for your project

By navigating to your project source code folder and run
```ctags .```

* Jumping in VIM

`vim -t <tag> `

Or

```
:tag ClassName
:tn      # next definition
:tp      # Move to previous definition
:ts      # List all definitions
 ^]      # Jump to definition
 ^t      # Jump back from definition
 ^W      # Preview definition
 g]      # See all definition
```

You can just run `:!ctags -R .` and `g]` just works. It also enables that omni complete thing.
At vim editor `:set tags=/home/user/ctags`


# how cscope works
`set cscopetag`

This searches for code that I'm interested in, creates the cscope.files list and creates the database. That way I can run `:!cscope_gen.sh` instead of having to remember all the set up steps.

I map cscope search to ctrl-space x 2 with this snippet, which mitigates the other downer of cscope:

```
nmap <C-@><C-@> :cs find s <C-R>=expand("<cword>")<CR><CR>
```

** Using cscope

```
:cscope add your_cscope_database
:cscope find s [your_symbol]
```

Then commands of

```
:cn
:cp
:cnf
:cpf
:colder
:cnewer
```

* Searching word in VIM

1. Put your cursor on that word, and type 

2. `:cscope find s <CTRL-R><CTRL-W><enter>`

* Or remap key with F3 by:

`noremap <F3> :cscope find s <C-r><c-w><cr>`



