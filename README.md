# pluginhook

This fork switches off the pipeline filtering functionality. Instead it prints out stdout after each scriptrun.

A simple plugin system for Bash programs that's better than just hook scripts.

## Plugins as a better way

Let's take the core benefits of hook scripts and re-structure it slightly:

 1. Instead of focusing on hook scripts, we focus on plugins -- a directory of hook scripts
 1. Like hook scripts, plugins are active by being in a certain place. But they can be named anything
 1. Multiple plugins can handle a hook. Either for fanout event triggering, or for pipeline filtering

So what is a plugin? A directory full of hook scripts. When a hook is triggered, all arguments provided
in the trigger are given to each hook script. They also receive STDIN in a pipelined fashion.

## Triggering plugin hooks

You use the `pluginhook` command to trigger hooks as if you might call a traditional hook script directly.
Where before you might have triggered by calling something like:

    hooks/post-commit $REV $USER

You'd instead trigger like this:

    pluginhook post-commit $REV $USER

The `pluginhook` command simply loops through all plugin directories found in the path defined by the environment variable `PLUGIN_PATH` and passes the same arguments to any hook scripts by that name. This means installing a plugin is as simple as putting it in your `PLUGIN_PATH`. Then any plugin that has the `post-commit` hook script will be run.

## What's wrong with just hook scripts?

Lots of shell-based systems use hook scripts as a means to allow users to extend or customize behavior. Popular examples
are [Git](https://www.kernel.org/pub/software/scm/git/docs/githooks.html) and [SVN](http://svnbook.red-bean.com/nightly/en/svn.ref.reposhooks.html), but many systems from [libvirt](http://www.libvirt.org/hooks.html) to [NPM](https://npmjs.org/doc/scripts.html) to [OS X](http://superuser.com/questions/295924/how-to-run-a-script-at-login-logout-in-os-x) use this pattern. Shell scripts make 
for a great way to expose hooks because the shell environment is ubiquitous and lets you easily call into
scripts or programs written in your language of choice.

The standard implementation of hook scripts is to have a shell script with an execute bit in a particular location
that's named after the hook. The most famous example is the "post-commit" hook of SVN, which if `hooks/post-commit`
exists in your repository directory with an execute bit, it will trigger this script after each commit. Some systems
let you register hooks by providing a location instead of using convention. 

However, in either case, each hook points to **one** script. The only way for a third-party piece of software to 
"hook in" is to install itself as the one hook script, or have you manually install it by calling it from your 
existing hook script. What's more, if a third-party piece of software wants to use multiple hooks, you have to
deal with this several times over. Not only is this a hassle, but leads to complex and non-obvious configurations.

## Author

Jeff Lindsay <progrium@gmail.com>

## License

MIT
