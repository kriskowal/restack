[![Build Status](https://travis-ci.org/abhinav/restack.svg?branch=master)](https://travis-ci.org/abhinav/restack)

restack augments the experience of performing an interactive Git rebase to make
it more friendly to workflows that involve lots of interdependent branches.

Installation
============

Binaries
--------

Pre-built 64-bit binaries are available for Linux and Mac at
<https://github.com/abhinav/restack/releases>. To install, simply unpack the
archive and put the binaries somewhere on your `$PATH`.

For example, if you have `$HOME/bin` on your `$PATH`,

    OS=$(uname -s | tr '[:upper:]' '[:lower:]')
    VERSION=v0.1.1
    URL="https://github.com/abhinav/restack/releases/download/$VERSION/restack.$VERSION.$OS.amd64.tar.gz"
    curl -L "$URL" | tar xv -C ~/bin

Build From Source
-----------------

If you have Go installed, you can install `restack` using the following
command.

    $ go get -u github.com/abhinav/restack/cmd/restack

Setup
=====

Automatic Setup
---------------

Run `restack setup` to configure `git` to use `restack`.

    $ restack setup

Manual Setup
------------

If you would rather not have `restack` change your `.gitconfig`, you can set
`restack` up manually.

Point your `sequence.editor` git configuration to the script generated by
`restack setup --print-edit-script`.

    $ restack setup --print-edit-script > bin/restack-edit.sh
    $ chmod +x bin/restack-edit.sh
    $ git config sequence.editor restack-edit.sh

The generated script will rely on the `restack edit` command if available,
falling back to your `GIT_EDITOR` if not.

You can also bypass the checks performed by the script and point
`sequence.editor` to the `restack edit` command directly.

    $ git config sequence.editor 'restack edit'

See `restack edit --help` for the different options accepted by `restack edit`.

Usage
=====

restack automatically recognizes branches being touched by the rebase and adds
rebase instructions which update these branches as their heads move.

The generated instruction list also includes an opt-in commented-out section
that will push these branches to the remote.

For example, given,

    o master
     \
      o A
      |
      o B (feature1)
       \
        o C
        |
        o D (feature2)
         \
          o E
          |
          o F
          |
          o G (feature3)
           \
            o H (feature4, HEAD)

Running `git rebase -i master` from branch `feature4` will give you the
following instruction list.

    pick A
    pick B
    exec git branch -f feature1

    pick C
    pick D
    exec git branch -f feature2

    pick E
    pick F
    pick G
    exec git branch -f feature3

    pick H

    # Uncomment this section to push the changes.
    # exec git push -f origin feature1
    # exec git push -f origin feature2
    # exec git push -f origin feature3

So any changes made before each `exec git branch -f` will become part of that
branch and all following changes will be made on top of that.

Credits
=======

Thanks to [@kriskowal] for the initial implementation of this tool as a
script.

  [@kriskowal]: https://github.com/kriskowal
