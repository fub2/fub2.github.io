	
With Git version 1.7.9 and later

Since Git 1.7.9 (released in late January 2012), there is a neat mechanism in Git to avoid having to type your password all the time for HTTP / HTTPS, called credential helpers. (Thanks to dazonic for pointing out this new feature in the comments below.)

With Git 1.7.9 or later, you can just use one of the following credential helpers:

```git config --global credential.helper cache```

... which tells Git to keep your password cached in memory for (by default) 15 minutes. You can set a longer timeout with:

```git config --global credential.helper "cache --timeout=3600"```

(That example was suggested in the GitHub help page for Linux.) You can also store your credentials permanently if so desired:

```git config credential.helper store```

GitHub's help also suggests that if you're on Mac OS X and used Homebrew to install Git, you can use the native Mac OS X keystore with:

```git config --global credential.helper osxkeychain```

For Windows, there is a helper called Git Credential Manager for Windows or wincred in msysgit.

```git config --global credential.helper wincred # obsolete```

With Git for Windows 2.7.3+ (March 2016):

```git config --global credential.helper manager```
