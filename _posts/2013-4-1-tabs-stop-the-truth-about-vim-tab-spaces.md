---
title: "Tabs, stop! The truth about tab and spaces in Vim"
layout: post
type: text
category: development
published: true
comments: true
---
I'm one of those Jurassic reptiles that enjoys using [Vim]; don't ask, just let
me be and in case you feel like spending a weekend with me and my pack of
laser-eyed tamed T-Rex's playing with keyboard shortcuts just have a look at
the [ad-hoc collection of resources](vim-resources) I collected over time and/or
at my [vimrc settings].

Now Vim, being a neat piece of software developed by smart people, has no less
than **4 settings related to code indentation** with pretty similar names just
to make things easier; so far I managed surviving by teaching to the aforementioned
T-Rex collective to leave comments in my vimrc to remind me why each of those 4
settings in being used, until I realized moving that arcane knowledge to a more
reliable destination was a better idea, hence this post.

Bear with me if you want to learn everything about the magic world of
indentation in Vim!

We'll walk through each setting connected to indentation in detail to understand
what each does and how it works together with his siblings.


tabstop
-------

This setting tells Vim
**how many columns a tab should be made up of in the editor view**, it takes
care only of **how tabs will be rendered** and has no effect on the actual text.


expandtab and noexpandtab
-------------------------

Enabling this option via the `set` command will
**insert the appropriate number of spaces when in insert mode**.


shiftwidth
----------

Set a value for this option to control
**how many columns text will be indented when using indent operations**
(such as `<<` or `>>`) in _normal_ or _visual mode_; this also covers automatic
_C-style_ indentation.


softtabstop
-----------

This option results in different behaviours depending on its own value and the
one set for `tabstop` and the status of the `expandtab` toggle:

- `softtabstop` < `tabstop`, `noexpandtab` - This will result in a combination
  of tabs and spaces to make up the total spacing
- `softtabstop` == `tabstop`, `noexpandtab` - This will always force the use of
  tabs
- `expandtab` - The value of `softtabstop` will be ignored and spaces will be
  forced


Examples
--------

So here's an example of how to use all of the above in your _vimrc_ file or in
_ftplugin_ resources:

```vimscript
" Example of typical indentation for Python code, i.e. 4 spaces
set expandtab
set tabstop=4
set shiftwidth=4
```

You can also set those values on depending on the file type directly in _vimrc_:

```vimscript
autocmd FileType ruby setlocal expandtab tabstop=2 shiftwidth=2 softtabstop=2
```

If you're insane enough to feel like learning even more about indentation in
Vim, I'd suggest going through the [reference material] put together by Ted
Logan.

Happy indenting!


[reference material]: http://tedlogan.com/techblog3.html
[vim]: http://www.vim.org
[vim-resources]: https://springpad.com/#!/federico.lox/explore/learningvim/blocks
[vimrc settings]: https://github.com/federico-lox/dotfiles/blob/master/vimrc
