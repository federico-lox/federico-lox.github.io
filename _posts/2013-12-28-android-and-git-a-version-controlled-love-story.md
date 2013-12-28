---
title: "Android and Git: a (version controlled) love story"
layout: post
type: text
category: development
published: true
---
Back in August last year, having a _big phone_ (or _phablet_ as those are called
nowadays) was unusual, having a Bluetooth keyboard connected to such
a device was seen as weird but wanting to use that combination for writing code
and store it in a VCS was considered as **borderline**.

In those days getting [Git] up and running on Android wasn't for the faint of
heart as there was no app that dared going any further than supporting read-only
operations (i.e. _clone_ and _pull_) and there was virtually no tutorial, guide
or _how-to_ to help in such a quest; that's when I decided to write what ended
up being the most popular piece of text I've ever published on the web, i.e. a
full length, step by step tutorial on how to get the Git _CLI_ experience
working on Android in a Bash shell with the title of "Damngit! Git on Android
FTW or how to painfully set up Git on your phone" (which now is nothing more
than a redirect to this page).

Due to its popularity and to the set of instructions being a tad out of date,
I've decided to dust that post and refresh on the topic especially since the
apps landscape has changed a bit since then (and there are many more
_phablet/tablet_-equipped developers willing to hack on the go than there used
to be back in 2012).


Git apps: what's available in the store(s)?
-------------------------------------------

So let's start with a _state of the union_ on what is available today on the
[Google Play Store] in term of both dedicated and git-enabled apps since the
_"[There's an app for that](appft)"_ meme is more valid today than ever,
there's a chance you'll find what you need before investing more time.

The following is a list of apps I played with so far, grouped by category.

### GUI clients ###

- [Agit] is a read-only Git client with a $2.99 price tag; it has a lot of
  limitations dictated by the version of JGit being used (e.g. no support for
  _symlinks_ and _submodules_, very slow at cloning big repositories) although
  it offers a couple features not found in any other app, i.e. _periodic sync_
  (it fetches the list of commits every 15 minutes) and animated diffs
- [Git](git.android) is a very basic, read-only, free client open sourced under
  _GPLv3_ (which is its only virtue for now), at the time of writing it is a
  decent _repository browser_
- [CubeGit] is probably the best commercial Git client on Android, it comes in
  at $1.60 but has many valuable features (e.g. generating SSH keys and support
  for _push_ of course) and it leverages native binaries (i.e. it's fast)
- [SGit] is a full-fledged Git client under active development; it's free,
  open sourced under _GPLv3_ and apart from allowing to push to a repository it
  also supports merging branches, importing existing local repositories and
  cherry-picking commits, all wrapped in a functional UI (especially if compared
  to other apps in the same category)

My pick in this category is _SGit_.

### Git-enabled IDEs and Editors ###

- [AIDE] is a fully backed IDE for Android development in Java and C++ (NDK),
  it could be easily labeled as _"Eclipse in a pocket"_ (it can actually import
  Eclipse projects!); the free version includes read-only git support, being
  able to commit, branch and push will cost you $6.99 and there's also a version
  targeted at [PhoneGap development] \(that will cost you twice as much\)
- [DroidEdit] is a glorified programmer's editor with support for many languages,
  git integration is available only in the paid version that comes with a $2
  price tag and supports pushing code to remotes

I'm a happy _DroidEdit_ user which is also what I use as a note-taking "cloud" app
on my phone after I abandoned _Evernote_ and _SimpleNote_ in favor of a private
git repository hosted at [BitBucket] filled with an organized collection of
[markdown] text files, but that's material for another post.

### CLI environments ###

- [ZShaolin] is a fully featured, _GNU-powered_ ZSH environment; while it is an
  open source project that comes packaged with the git, ssh and rsync binaries
  among many others (Lighthttpd! Emacs!!), getting it to run on your device
  without the need of compiling it yourself will cost $3.25
- [Terminal IDE] predates and inspired ZShaolin, it's a free and open source
  Bash environment which also offers git, the name refers to it's original goal
  of delivering a Vim setup targeted at Java development on Android; also, it
  only support git over SSH for remotes

I have to admit _ZShaolin_ is a very attractive package and while _Terminal IDE_
needs a bit of tweaking to get _Git_ to work correctly with remotes, my
preference goes to the latter as easy access to tools that have been in the
public domain for over 30 years **should be free on any platform** and also
because I have a mild preference for Bash over ZSH.

So, if you haven't found and IDE, Editor or GUI client that satisfies your VCS
needs while going through the lists above and for whatever reason _ZShaolin_ is
not your cup of tea then fear not and hold tight as we'll go through the steps
to set up _Terminal IDE_'s Git tool-chain to work as expected.

Setting up Terminal IDE for Git work-flows
------------------------------------------

Follow this steps to get through a spiral of painful joy and be able to
successfully run `git push` against your remotes and call yourself a victor:

1. The obvious, we need to **get [Terminal IDE] installed on our devices**
2. Since there's only support for _SSH_, we'll also need a
   **pair of private/public RSA keys**
    - You can generate those on any machine that can run `ssh-keygen` and then
      move it to `/data/data/com.spartacusrex.spartacuside/files/.ssh` (which
      could be a bit tricky depending on your understanding of Android's
      sandboxed file system for apps' data)
    - You can generate the keys using `dropbearkey` directly in _Terminal IDE_'s
      prompt:
```bash
mkdir ~/.ssh
dropbearkey -t rsa -f ~/.ssh/id_rsa
```
      This will create a passphrase-less pair of keys fitting our Git needs and
      print out the public token (you can retrieve it at any time by running
      `dropbearkey -y -f ~/.ssh/id_rsa`)
3. We'll have to **deploy the public key on the remote(s)**, this varies
   depending on your remote, e.g. on _GitHub_ this can be done through the user
   settings page
4. Now that we have keys we need to tell SSH and Git to
   **use the correct identity**, you can use either `vim` or `nano` as the shell
   supports both

    1. Create a script for Git to use the identity, e.g. nano `~/local/bin/ssh-git`
    ```bash
    #!/data/data/com.spartacusrex.spartacuside/files/system/bin/bash
    exec ssh -i ~/.ssh/id_rsa "$@"
    ```
    2. Make the script executable by running `chmod +x ~/local/bin/ssh-git`
    3. Edit the `.bashrc` settings file that comes with Terminal IDE, e.g. nano ~/.bashrc:
    ```bash
    export GIT_SSH=~/local/bin/ssh-git
    ```
    4. Shutdown and re-start Terminal IDE
5. Now we need to **associate a Git user to the identity**, unfortunately the
   version of git bundled with Terminal IDE has issues with reading user name
   and email from config files set via the `git config` command so we'll have to
   resort to a different method

    1. Edit `.bashrc` once again and add the following
    ```bash
    export GIT_AUTHOR_NAME="USER NAME"
    export GIT_AUTHOR_EMAIL="user@email.address"
    export GIT_COMMITTER_NAME=$GIT_AUTHOR_NAME
    export GIT_COMMITTER_EMAIL=$GIT_AUTHOR_EMAIL
    ```
       If your remote is GitHub make sure the user name and email match the ones
       used for your GitHub user
    2. Shutdown and re-start Terminal IDE

If you got so far to read this line, then congratz! You can start using your
pocket-able Git setup!

To save any remain of sanity I'd suggest you get an `USB OTG` or Bluetooth
keyboard, the soft keyboard that comes with Terminal IDE is ok but nothing can
replace a physical keyboard when coding/hacking.

Happy Git-ing!


[aide]: https://play.google.com/store/apps/details?id=com.aide.ui
[agit]: https://play.google.com/store/apps/details?id=com.madgag.agit
[appft]: http://appft.com/
[bitbucket]: https://bitbucket.org/
[cubegit]: https://play.google.com/store/apps/details?id=de.f0i.cube.git
[droidedit]: https://play.google.com/store/apps/details?id=com.aor.droidedit
[git]: http://git-scm.com
[git.android]: https://play.google.com/store/apps/details?id=com.romanenco.gitt
[google play store]: http://play.google.com
[jgit]: http://www.eclipse.org/jgit/
[markdown]: http://daringfireball.net/projects/markdown/
[phonegap development]: https://play.google.com/store/apps/details?id=com.aide.phonegap
[sgit]: https://play.google.com/store/apps/details?id=me.sheimi.sgit
[terminal ide]: https://play.google.com/store/apps/details?id=com.spartacusrex.spartacuside
[zshaolin]: https://play.google.com/store/apps/details?id=org.dyne.zshaolin
