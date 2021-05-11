---
id: 166
title: Reduce Your Redundant Commits With Git
date: 2019-09-16T12:47:57+01:00
author: waz
layout: post
image: /wp-content/uploads/2019/09/git-logo.png
---
## How to reduce commits using Git CLI

So you&#8217;re working on a bugfix locally and you commit your changes. But then you miss a line, and have to create another commit. Then you realise that you&#8217;ve badly formatted your code, so in goes another.

After a while you may get a bunch of commits all related to your bugfix:

```
commit aa28d77448f50b66d93912aa172d458f1104f527 (HEAD -> bug-fix01)
Author: Wayne Covell &lt;wayne.covell@xxx.com>
Date:   Mon Sep 16 13:35:22 2019 +0100

    Formatting bugfix

commit 067358682330fe3a9c7313b5333fb0c92083e121
Author: Wayne Covell &lt;wayne.covell@xxx.com>
Date:   Mon Sep 16 13:35:04 2019 +0100

    Really fixing bug

commit 0be13c104d2d4fca1e96e42bd0e84874f4dd27fe
Author: Wayne Covell &lt;wayne.covell@xxx.com>
Date:   Mon Sep 16 13:34:42 2019 +0100

    Fixing bug

commit 4e215ff228e061c83a8866963c5f2aebf873b21c (origin/master, master)
Author: Wayne Covell &lt;wayne.covell@xxx.com>
Date:   Wed Jul 24 14:03:13 2019 +0100

    formatting
```

Now if you push this to your repository, then merge the branch, it&#8217;s not going to look very pretty. Plus if you need to revert the change, it&#8217;s a headache as it spans multiple commits.

Here&#8217;s where rebase comes in.

```
git rebase -i HEAD~3
```

This tells git that you want an interactive (manual) rebase, starting at the HEAD and for a depth of 3 commits.

Now you&#8217;ll get some options for your 3 latest commits, in your default editor:

```
pick 0be13c1 Fixing bug
pick 0673586 Really fixing bug
pick aa28d77 Formatting bugfix
```

In order to reduce them into one, first change &#8216;pick&#8217; on the latest two commits to &#8216;fixup&#8217; or &#8216;f&#8217; for short. Then, if you want to rename the commit that results, change the top &#8216;pick&#8217; to &#8216;reword&#8217; or &#8216;r&#8217;.

```
r 0be13c1 Fixing bug
f 0673586 Really fixing bug
f aa28d77 Formatting bugfix
```

You&#8217;re then presented with an editor where you can reword &#8216;Fixing bug&#8217; to something more useful like &#8220;Amending method doStuff() to add a String parameter to prevent not doing stuff&#8221;

Now if you do a git log, you&#8217;ll see you have reduced the 3 commits to 1, and gave it a useful description

```
commit 9401745ec178bb87ee25ef6820a9c2548d94278c (HEAD -> bug-fix01)
Author: Wayne Covell &lt;wayne.covell@xxx.com>
Date:   Mon Sep 16 13:34:42 2019 +0100

    Amending method doStuff() to add a String parameter to prevent not doing stuff

commit 4e215ff228e061c83a8866963c5f2aebf873b21c (origin/master, master)
Author: Wayne Covell &lt;wayne.covell@xxx.com>
Date:   Wed Jul 24 14:03:13 2019 +0100

    formatting
```

Note that this only works if you haven&#8217;t already pushed your changes. You can do the same if things are pushed but then you have to force push, which can override anyone else&#8217;s commit since then, which can be dangerous. So use with caution but the command is: 

```git push -f ```