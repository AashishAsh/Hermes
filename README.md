# Hermes

Hermes is an environment for Ruby and JavaScript developers in Darwin using
[Tmux](http://tmux.sourceforge.net/), [Vim](http://www.vim.org/) and [iTerm
2](http://www.iterm2.com/) that focuses on speed and ease of use.

Hermes is opinionated where having an opinion is important, but does not
prevent you from customizing your tools.

Hermes gives you a lot of things for free:

- Sensible defaults for developers.
- Integration of Vim into tmux and tmux into iTerm 2.
- Mouse and window integration, allowing selections within tmux and Vim panes,
  not across them.
- Vim packages that provide git integration, command- and block-completion,
  fuzzy file search and ease of testing.

We feel that good documentation is a key part of using any new technology with
lots of moving parts, so we will be improving Hermes' documentation in the days
and weeks to come.

![Hermes screenshot](https://raw.github.com/New-Bamboo/Hermes/master/hermes.png)


## Preliminary Thanks

Hermes combines plugins, settings, snippets, gists, and ideas from countless
developers around the world. We would like to thank:

- The [Vim](http://www.vim.org/) team.
- The [Tmux](http://tmux.sourceforge.net/) team.
- The [GNU Bash](http://www.gnu.org/software/bash/bash.html) team
- The [Homebrew](http://mxcl.github.com/homebrew/) team.
- [Tim Pope](http://tpo.pe/). Seriously, you're awesome.
- [Thoughtbot](http://thoughtbot.com/) for their dotfiles, essential in getting
  the Tmux configuration right.
- [Vimcasts](http://vimcasts.org/), for showing the world just how powerful Vim can be.


## Installation

**Warning!** Hermes is still early in development, so just to be
careful, we strongly encourage you to install it in a separate
user account, not your main one. That said, we *have* tested it on
our own user accounts, where it worked just fine.

You can check to see which files will be overwritten in
`manifests/dotfile_manifest`, or
[follow this link](https://github.com/New-Bamboo/Hermes/blob/master/manifests/dotfile_manifest)
to view it on Github.


### Prerequisites

Hermes relies on Homebrew and RVM to work properly. While Homebrew is a de
facto standard developers using OS X, there are a good number of people that
use RBenv, so support for that is in the pipeline. We are happy to look at any
pull requests.

If these two tools are not available, the installer script will halt. Please
refer to these tools' excellent documentation for installation instructions.


### Fork first!

As the very first step, you should fork the Hermes on Github since this will
make it easier for you to customize your installation. After you're done, you
can run:

    mkdir -p ~/.hermes
    git clone https://github.com/<your_github_username>/Hermes.git ~/.hermes
    cd ~/.hermes
    ./install.bash

This will perform the following actions:

- Check that you have all the needed Homebrew dependencies
- Back up any file or folder that would be overwritten by the installer process
- Install all dotfiles and plugins available in the `hermes` directory and
  symlink them to the right locations in your home folder

You may also want to add Hermes's repository as an upstream repository, so you
can pull in the changes done on the main trunk whenever you need to.

![Hermes installation](https://raw.github.com/New-Bamboo/Hermes/master/hermes-install.png)

### What's included in the installer

The installer will:

- check for dependencies
- backup any existing dotfile that would be overwritten in a timestamped tar
  file that you can use to restore your previous configuration
- install a number of required Homebrew packages
- create a `~/hermes` directory and symlink its content to your home folder
  where every piece of software expects to find its main configuration file(s)

Hermes includes:

- configuration and plugins for Vim
- configuration for Tmux
- configuration for git
- configuration and additional functionality for two shells: Bash and Fish.
- settings for `gem`, `ack`, `pow`, `pry` and `irb`

In addition, Hermes glues all components together so they play nicely with each
other and the OS. Two examples of this integration are are Hermes' support for
the system clipboard in OS X and window/pane aware mouse integration.


### Updates

Being a git-based project, you can update Hermes by simply pulling from the
remote. If you forked the project, please remember to add the original repo as
an upstream repository to make getting new project updates easier.


## Documentation foreword

This document provides enough information to make you productive with Hermes,
but it doesn't cover the totality of what's provided by all plugins, especially
when it comes to Vim. Please refer to their original documentation for more
details.

Hermes' goal is to provide a solid structure for you to build on top of without
having to deal with any intermediate configuration layers.  For example, Vim's
entire configuration is managed canonically through the `~/.vimrc` file and the
`~/.vim` folder. The only significant difference is that under the hood, those
files are actually symlinks to your `hermes` folder.

Knowing how Hermes ties everything together is useful when it comes time to
configure it.


## Vim

A stock vim installation with a basic configuration can go a long way and can
be really beneficial when it comes to editing files on a server.

There is however a very simple problem with the default Vim installation that
OS X provides: it cannot access the system clipboard. That means if you copy
anything from outside the editor, it's not available in any of Vim's registers.
Worse yet, if you copy anything in Vim using its internal commands, it won't be
available to the rest of the system

To sort this out, Hermes installs Homebrew's version of Vim, which is
available through the MacVim package:

    brew install macvim --override-system-vim

This has some additional benefits, like having support for Ruby in plugins.

Let's now go with some defaults for a basic `.vimrc` file:

    set nocompatible    "don't need Vi compatibility
    set nobackup        "don't create backup files
    set nowritebackup
    set notimeout
    set ttimeout
    set ttimeoutlen=10
    set noswapfile      "don't create swap files
    set history=50      "keep a small history
    set ruler           "always show position
    set showcmd
    set incsearch
    set laststatus=2    "full status bar
    set t_Co=256        "256 colors - requires a properly configured terminal emulator
    syntax on           "turn syntax highlight on

    filetype plugin indent on "let plugins manage indentation

    " Send more characters for redraws
    set ttyfast
    " Enable mouse use in all modes
    set mouse=a
    set ttymouse=xterm2

    " Fix backspace
    set backspace=indent,eol,start
    fixdel

    " Softtabs, 2 spaces
    set tabstop=2
    set shiftwidth=2
    set expandtab

    " Display extra whitespace at the end of the line
    set list listchars=tab:»·,trail:·
    " Clipboard fix for OsX
    set clipboard=unnamed

    " Numbers
    set number
    set numberwidth=2

    "Folding
    set foldmethod=indent
    set foldlevelstart=99

    " Autocompletion options
    set wildmode=list:longest,list:full
    set complete=.,w,b"

These settings will allow you to efficiently edit any file whose type is
supported by default, so Javascript and Ruby are already covered. The settings
enable standard features like line numbering and syntax highlighting and also
turn on features like mouse support and clipboard sharing that are useful in
integrating Vim into iTerm and OS X.


### Plugins

Plugins are a powerful way to extend Vim's capabilities. The implementation may
change, but we feel you should be able to expect the following from a modern
text editor:

- Support for fuzzy search inside a directory tree. You should be able to
  easily open a file by name without navigating the tree.
- Full text search inside a directory tree.
- Snippet support with expansion, tab stops and completion. Like Textmate.
- Integration with testing frameworks. You should be able to run tests without
  leaving the editor.
- Tabs and split windows. You should be able to see tests and the corresponding
  code at the same time and be able to easily switch from one to the other.
- Language specific features, like syntax-aware indentation and navigation.

Needless to say, a number of other text editors support these features. Vim,
however, combines this with its extremely efficient modal editing approach.

Hermes provides a good number of plugins, aiming to strike a balance between
features and speed. You can see the complete list under `hermes/vim/bundle`,
but here are some highlights:

- [Ctrlp](https://github.com/kien/ctrlp.vim): a tool for fuzzy searching by
  file and tag.
- [Snipmate](https://github.com/msanders/snipmate.vim): unashamedly borrowing
  from Textmate, Snipmate provides tab completion based on snippet files.
- [The silver searcher](https://github.com/epmatsw/ag.vim): `ag` is a faster
  alternative to Ack.
- [TComment](https://github.com/vim-scripts/tComment): toggles comments in
  nearly any language.
- [Rails.vim](https://github.com/tpope/vim-rails): provides shortcuts,
  generators and settings for working with Ruby on Rails projects.  Absolutely
  killer.
- [Vimux](https://github.com/benmills/vimux): forms a bridge with Tmux to send
  text and commands to a Tmux pane. Vimux is essential for Hermes' testing
  support.

However, we encourage you to be wary of plugins for several reasons:

- Vim has many conventional ways to accomplish certain tasks, and while it's
  possible to do things in many ways, it's important to try to understand the
  Vim way of doing things and play to its strengths.
- One of Vim's benefits is speed and low memory footprint, making it responsive
  even when opening huge files. Increasing Vim's footprint through excessive
  numbers of plugins can eliminate this benefit.
- Sometimes a plugin is not necessary. Similar or identical effects can often
  be achieved with smaller, well thought-out changes in your .vimrc.
- Although powerful, Vim is a text editor and should do just this one job well.

Vim's approach to plugin management is a little counterintuitive:
by default, Vim looks for additional scripts to load in `~/.vim`,
which has subfolders that determine when the configuration is
loaded. For example, a script can be split across the `plugin` and
the `autoload` directories, the former for the bulk, load-once
functionality while the latter for anything that requires constant
recalculation. This means that a manual installation may be spread
across multiple directories, resulting in a structure that is
difficult to maintain and update.

Enter [Vundle](https://github.com/gmarik/vundle), a package
manager that makes this process painless and that inverts the
usual installation pattern, as it lets you organize plugins based
on their name.

Vundle has been inspired by [Bundler](http://gembundler.com), the package
manager for Ruby and it uses a similar approach. Let's say that we want to add
the NerdTree plugin to our configuration, available at
<http://github.com/scrooloose/nerdtree>.

We can just open `~/.vimrc`, add a single line:

    Bundle 'scrooloose/nerdtree'

And then run `:BundleInstall` as a normal mode command. Done!

Under the hood, Vundle will download the plugin, store it into `vim/bundle` and
load it into the runtime.

Using Vundle requires just a preliminary installation (included in the Hermes
installation script) and a few lines at the top of our `~/.vimrc`:

    " Options required by Vundle
    set nocompatible
    filetype off

    " Setup Vundle
    set rtp+=bundle/vundle/
    call vundle#rc()

Vundle is capable of searching, updating and installing other plugins and
features an interactive mode. Be sure to run `:help vundle` to learn all of
these tricks.

### Managing configuration

If you keep extending your `.vimrc`, it comes to a point where
it's simply too long, so it makes sense to split it into separate
chunks of related configuration. Here's an example from the bottom
of a `.vimrc`:

    source $HOME/.vim/autocommands.vim
    source $HOME/.vim/plugins.vim
    source $HOME/.vim/shortcuts.vim

**Tip**: Pressing `gf` in Vim's normal mode will open the file under
the cursor. This works with many other file types, including html documents.

![Using gf](https://raw.github.com/New-Bamboo/Hermes/gh-pages/images/hermes/gf.gif)

We recommend that when working with new plugins, you add one at a
time and pay close attention to their documentation. Plugins
are often extremely configurable, as you can see in Hermes'
`plugins.vim` file. Taking the time to develop a feel for how each
plugin works and configuring them for your specific needs can go a
long way in optimising your workflow.

Documentation is usually available by typing `:help <term-to-search>`. 
However, Hermes has a custom shortcut you can use: by pressing `<leader>h` 
with the cursor over a word, you can search for that word in Vim's help.

As an example, let's look at the configuration Hermes supplies for
**Ctrl-p** (in `~/.hermes/hermes/vim/plugins.vim`):

    set wildignore+=*/.hg/*,*/.svn/*,*/vendor/cache/*,*/public/system/*,*/tmp/*,*/log/*,*/.git/*,*/.jhw-cache/*,*/solr/data/*,*/node_modules/*,*/.DS_Store

The `wildignore` flag is not Ctrl-p specific, as it's used by Vim
or many autocompletion and expansion functions: the more unlikely
targets we remove, the better Vim's performance will be. Since
Ctrl-p uses this pattern to determine a baseline for excluding
files when creating its index, this simple addition will help keep
it snappy.


### Daily use cases

Here are a few examples of what you can do with Vim, bearing in
mind that this is not meant to be an exhaustive guide. Instead, we
will focus on frequent everyday tasks:


#### Shelling out

Having the shell at your disposal can speed up your workflow many
times over, but to really take advantage of this it's important to
learn how to alternate between Vim and the command line.

Sometimes you just need to run a simple shell command, like
creating a file or directory (i.e. folder). In that situation,
press `:` in normal mode to enter the command mode. Then type `!`
to tell Vim to shell out and perform the command in the shell. So,
if you want to create a `sample` directory, you can type:

    :!mkdir sample

The command will be performed from within the current working
directory, you can verify that with `:pwd`.

![Shelling out](https://raw.github.com/New-Bamboo/Hermes/gh-pages/images/hermes/shelling-out.gif)

When you need to step out of the file you're editing, perform a
few tasks and then go back, your best option is to suspend Vim
using the shell via `ctrl-z` and then resume it with by typing
the command `fg` (foreground) when you're done. This is a very
straightforward approach and widely used in the Unix world. It
works out of the box.

![Suspending](https://raw.github.com/New-Bamboo/Hermes/gh-pages/images/hermes/suspending.gif)

Alternatively, you can use a different window or pane with Tmux,
as we shall explain later on.

As always, you can associate a shortcut for a shell command you
want to run: a good example is creating a leader command to run
the current file as a spec.

    noremap <leader>s :!bundle exec rspec %<cr>

We use `noremap` to tell Vim to create a key map for normal mode,
assign it to `<leader>s` and then specify the command, a simple
`bundle exec rspec` where we include the current file as an argument
and then press enter (carriage return).


#### Reading from a source into the current buffer

Another common use case is having to add content from a different source, like
another file or a unix process.

Vim provides a very simple way to do this: the `:r` command.

If you have two files, `a.txt` and `b.txt`, you can open the first one and type:
`:r b.txt`. This will add the contents of the second file in the current buffer
(where you have opened `a.txt`).

You can combine the `:r` command with `!` to shell out and get the contents from
any command you wish. For example, you can use `ls` to list the contents of a
directory to easily generate a manifest file. The full command would then be `:r!
ls`.

![Reading from the shell](https://raw.github.com/New-Bamboo/Hermes/gh-pages/images/hermes/reading-from-shell.gif)

#### Search and replace

Search and replace in Vim is a kind of regular expression usage.
Vim expects you to provide a range and then a substitution command
to perform.

So if you type:

    :%s/foo/bar

It will search in the whole buffer `%` and substitute the first
occurence of `foo` with `bar`. As you can imagine, you can pass
flags to the command, like:

    :%s/foo/bar/gi

The `g` flag predictably tells Vim to perform a global search and
replace, with multiple replacements, while the `i` (interactive)
flag will allow you to confirm each substitution individually.

![Search and replace on the whole file](https://raw.github.com/New-Bamboo/Hermes/gh-pages/images/hermes/search-and-replace.gif)

If you need to act on a specific number of lines, you have two
options:

- You can pass a range of lines, like `:12,15s/foo/bar`
- You can select a visual block, press `:` followed by the
  substitution command (`s/foo/bar`). Note that the command bar
  will be prepopulated with the expression indicating the currently
  selected visual range (`'<,'>`)

A common complaint by many people who switch from a graphical
editor to Vim is that there's no facility to execute a
substitution command across files. Vim provides such tools by
following a simple pattern:

- Add all the files to the arguments list
- Perform a search and replace on each file contained in such list

This can be tricky: the argument list is the files of all
currently open files and can be completely different from the
buffer list. So if we wanted to perform a search and replace on
all `*.rb` files in the current working directory we would do:

    :args ./**.rb
    :argdo %s/foo/bar/gi

Note the `i` flag, which is a lifesaver. The `argdo` command
iterates over the argument list and performs the sspecified
command (we still need the `%` to act on the whole file).

(If you use Vim's tabs feature, see also `tabdo` for a way of
doing operations across all your tabs.)

A different approach, and what we suggest, is not using Vim
altogether but a shell based substitution.

First of all, you should make sure that you're working with some
sort of VCS, because what we're about to do is not easily
reversible.

We will be using Perl, as it's fast, powerful and simple.

The aforementioned substitution can be achieved with:

    perl -i.bak -pe's/foo/bar/g' ./**.rb

A VERY IMPORTANT NOTE: the above pattern is usually safe in the
context of a Rails application, but if the pattern you used finds
files inside `.git`, it will perform the substitution on them as
well, potentially damaging your Git index. This can happen if your
glob pattern is too loose or if you have submodules written in the
same language.

However, the `-i.bak` argument does create a backup of the
original file, so you'll need to delete the .bak files after the
substitution. If you really know what you're doing, you can just
use the `-i` by itself.

A safer approach, and one which makes also the search and replace
command easier to manage, is to move into subfolders and perform
it in different steps. This will also make it easier to check,
test and manage.


#### Visual mode

You can access Vim's "visual" model by pressing `v` (character
selection) or `V` (line selection). When in visual mode, any
movement will modify the selection. You can also click-drag a
screen selection with the mouse, which will automatically put you
in visual mode. Working in visual mode can be powerful, but in
general it's advisable not to use it too often, since the actions
you take in visual mode are not recorded in a way that can easily
be repeated, e.g. `.` in normal mode.

There are, however, situations where visual mode has a clear
advantage. One technique, making use of a "visual block", is great
for doing the same thing to several lines at once.

Imagine this text in Vim:

    a = 1
    b = 2
    c = 3

If we wanted to prepend the keyword `var` to every line shown
above, we could to the following:

- With the cursor on `a`, create a visual block by pressing
  `ctrl-v`.
- Hit `jj` to move down two lines.
- Press `I`, type `var ` (with a space at the end) and then press
  `Esc` to return to normal mode.

The var keyword should be prepended to each of the lines.

![Visual block editing](https://raw.github.com/New-Bamboo/Hermes/gh-pages/images/hermes/visual-block-substitution.gif)

Note that this is not the only way to do this. For example, a
macro or a normal mode command would have worked equally well.
This latter approach is in fact usually more effective:

- With the cursor on `a`, press `V` and `jj` to highlight all 3 lines
- Enter `:` and ` :norm Ivar `
- Press Enter

When a visual range is selected, pressing `:` opens the command
prompt with the range prefilled. By typing `:norm`, Vim
temporarily switches to normal mode, executing the subsequent
command for each line in the visual range. We just used `I` to
jump before the first letter in normal mode and type `var`.

![Visual block normal mode](https://raw.github.com/New-Bamboo/Hermes/gh-pages/images/hermes/visual-block-normal-mode.gif)

This approach is good when the change we're making doesn't need to
be repeated. In other situations, a macro is more effective.

#### Ctrl-p and fuzzy file search

![Using Ctrl-p](https://raw.github.com/New-Bamboo/Hermes/gh-pages/images/hermes/ctrl-p.gif)

Ctrl-p is a native Vim fuzzy finder. It can be used to search for files, buffers
and tags with great configurability.

Hermes ships a ctrl-p configuration that uses the following defaults:

- uses the VCS root as a working directory, falling back to the current one if
  you're working outside a git repository;
- ignores tmp, database, log and VCS specific directories;
- current match at the top of the window;
- tag extension to search into the ctags database

Ctrl-p can be easily invoked with... `ctrl-p`. As reported in [its original
readme](https://github.com/kien/ctrlp.vim), here are some commands you can use:

- Press `<c-f>` and `<c-b>` to cycle between modes.
- Press `<c-d>` to switch to filename only search instead of full path.

Note that any filesystem change (new or deleted files) requires a cache refresh,
achievable by typing `:CtrlPClearCache`.


### Working with Rails

Rails.vim supercharges Vim with functions, shortcuts and a general
'rails-awareness' factor that proves to be invaluable when editing a Rails
project.

#### File navigation

Due to Rails's conventional nature, any project uses the same folder structure,
and all files the same naming conventions. Rails.vim leverages this factor and
provides a series of commands to open specific files in a Rails project without
manually navigating to the file and keeping the current working directory at the
root of the Rails application.

These commands always follow the same pattern and are prefixed with `R` and are
followed by the name of the file you want to open (stripped of the extension).
Some examples are `:Rmodel` to open a model, `:Rcontroller` for a controller,
and so on.

All commands support variations to tweak the behaviour: for example,
`RVcontroller` will open the file in a vertically split pane. For a complete
list, type `:help rails-navigation`.

#### Alternate and related files

When working on a certain feature, it's common to switch between certain files:
model to test, controller to related view and so on. Rails.vim provides
shortcuts for this file jumps: every file has got two counterparts: alternate
and related. As reported in the guide (`:help rails-alternate`):

Current file           | Alternate file     | Related file
-----------------------| ------------------ | ------------
model                  | unit test          | schema definition
controller (in method) | functional test    | template (view)
template (view)        | functional test    | controller (jump to method)
migration              | previous migration | next migration
config/database.yml    | config/routes.rb   | config/environments/\*.rb

So pressing `:A` will switch between a model and its test file, while `:R` on a
controller `index` action will take us to the related `index` view. Again, this
commands can be combined with modifiers to open the file in a new tab or split
(`:RV`, `:RE`, etc.).

#### Overcharged gf

Other commands, like the afore-mentioned `gf`, get a proper boost, becoming
shortcuts to jump to the right file when pressed over a certain keyword. As an
example, let's look at the following code:

    class Post < ActiveRecord::Base

      belongs_to :author

    end

Pressing `gf` on `:author` (any character) will open `app/models/author.rb`.
Other examples are included in the relevant help section (`help rails-gf`).

#### Generators and Rake

You can use generators straight from Vim with `RGenerate`, with the nice side
effects that the first generated file is automatically opened in the editor.

So, if you want to generate a new migration you can:

- type `:Rgenerate migration ...`
- make the relevant changes in the file
- type `:Rake` to execute it

This leads us to the `:Rake` command: depending on the open file, it performs
different functions. See `:help rails-rake` for details. Note that another
plugin included with Hermes,
[vim-bundler](https://github.com/tpope/vim-bundler), takes care of prepending
`bundle exec` to all commands.

#### Partial refactoring

Another common operation is partial extraction, i.e. moving a certain portion of
erb code into a separate partial file.

Let's assume you have a file called `app/views/users/show.html.erb` with this
content:

    <ul>
      <li><%= @user.name %></li>
      <li><%= @user.email %></li>
    </ul>

Using visual block mode (`V`), highlight the two `<li>` tags. Then type
`:Rextract user` and press enter. This will create a file called
`app/views/users/_user.html.erb` with the following content:

    <li><%= user.name %></li>
    <li><%= user.email %></li>

It will also update the `show` view by referencing that new partial:

    <ul>
      <%= render :partial => 'user' %>
    </ul>


## Working with Tmux

Even if Vim by itself is indeed extremely powerful, it just shines when paired
with Tmux. Tmux is a terminal multiplexer, a program to manage multiple shell
instances in the scope of a single session (whether it's local or SSH it doesn't
matter).

In other words, Tmux allows the creation of separate tabs (called windows) and
splits (called panes), interaction between them and an external api for
programmatic control.

The recurring question that people ask when hearing about Tmux for the first
time is "Why should I use this instead of the native functionality provided by
my terminal emulator?". Here's why:

- Tmux is terminal emulator independent, i.e. you can use it with any terminal
  emulator (there are a few edge cases in terms of compatibility, but the usual
suspects on Mac OsX and Linux are well supported).
- Tmux can start a session, suspend it (called detach) and resume it (called
  attach), everything from different machines and over a network connection as
well.
- Tmux has an api for external control by a 3rd party software and we'll see
  that this is key in our setup.
- If you accidentally close your terminal emulator, you don't lose anything as
  it runs in a separate process.
- Tmux is designed to be controlled with keyboard only and it features a
  powerful Vim compatibility mode that uses identical shortcuts.

Hermes includes an opinionated Tmux setup that solves a few compatibility issues
with OsX, rebinds many shortcuts to an easier to remember layout and adds a few
bells and whistles (like date, time and battery information in the status bar).
Huge thanks to Thoughtbot for sharing most of the code that made it into this
configuration.

### Basic interaction

If you type `tmux` in your shell, you will start a new session. As we haven't
passed a name, the session will receive an incremental number to identify it.
Tmux allows switching between different sessions, so ideally you would want a
separate one for each project you're working on.

At the bottom, you can see the list of windows on the left. This shows the
current session windows, highlighting the current one. Windows also have an
index, shown right on the left of the name.

The window behaves exactly like a "normal" terminal window, with just a couple
of exceptions:

- Some shortcuts (like Cmd+k to clear the screen) can have unexpected
  behaviours. Where sensible, the configuration provides fallbacks.
- Mouse interaction is supported, but only with iTerm2 as terminal emulator (the
  default OsX terminal doesn't support it)

All tmux commands start with a prefix, set in this configuration to `Ctrl-a`: as
a convention, this document will call this shortcut 'prefix', so `prefix-c`
means 'press Ctrl-a, then c'.

![Using Tmux windows](https://raw.github.com/New-Bamboo/Hermes/gh-pages/images/hermes/tmux-windows.gif)

Here are some basic commands:

- `prefix-1`, `prefix-2` switches to the window identified by that index;
- `prefix-c` creates a new window;
- `prefix-|` splits the current window **vertically**;
- `prefix-_` splits the current window **horizontally**;
- `prefix-spacebar` switches between horizontal and vertical layout for panes;
- `prefix-x` closes the current pane.
- `prefix-a` cycles focus among the current window panes;
- `prefix-h/j/k/l` moves the focus respectively to the left, below, above and
  right from the current pane (very similar to Vim);
- `prefix-r` reloads the tmux configuration (useful if you make some changes);
- `prefix-:` enters tmux command mode, where you can type tmux commands to
  perform certain actions in a dedicated command line (this is advanced usage).

You can also change focus from one pane to another using the mouse, however that
is usually slower than mastering keyboard shortcuts.

![Using Tmux panes](https://raw.github.com/New-Bamboo/Hermes/gh-pages/images/hermes/tmux-panes.gif)

### Scrolling, copy and paste

As expected, you can scroll inside a pane with your mouse, but Tmux supports
complete mouseless interaction even for this kind of operation. This is possible
by entering 'copy mode', where (similarly to Vim's normal and visual modes,
pressing keyboard keys doesn't enter text but performs actions). Copy mode is
identified by a status indicator in the top right corner of the pane (showing
your cursor position in the current scroll buffer).

Copy mode can be entered by pressing `prefix-esc`, but it can be alternatively
activated by:

- pressing `prefix-pageUp` or `prefix-pageDown`, so that after entering copy
  mode it scrolls up or down.
- scrolling with the mouse on a pane, or initiating a drag and drop selection.

Commands in copy mode are pretty much identical to Vim and that's because Tmux
handily supports a Vim compatibility mode, so that you don't have to change your
habits.

- `/` will initiate a forward search. Simmetrically, `?` will initiate a
  backward one;
- `pageUp` and `pageDown` will scroll the pane;
- `g` and `G` will go respectively to the top and the bottom;
- `v` initiates visual selection, so that you can select a visual portion of
  text and then, for example, press `y` to copy it.

![Using Tmux copy mode](https://raw.github.com/New-Bamboo/Hermes/gh-pages/images/hermes/tmux-copy-mode.gif)


To see a list of all possible combinations, press `prefix-:` to enter command
mode and type `list-keys -t vi-copy`. Note also that a good set of motions are
supported, so you can type `v4w` to select 4 words from the current one.

Allow yourself some time to master copy mode, as it's extremely powerful.

Note that if you use the mouse and perform a drag selection, text will be
automatically copied into the clipboard upon releasing the left mouse button.

## Tmux and Vim

Let's assume you are working on Rails application. Thanks to Rails.vim, you can
easily navigate the codebase, but running tests is still a bit painful. You can
create some bindings as shown above, but reality is, it would be great if you
could type a shortctut to run tests "somewhere" else without interrupting your
flow.

Hermes ships a combination of plugins that let you control your test suite runs
from Vim using a separate pane in a Tmux session, so that you can benefit from
asynchronous test runs without leaving your editor. This is achieved thanks to
different plugins (vimux, vimux-ruby-test, vimux-cucumber and vim-turbux). It
may seem a complicated setup, but in reality it allows to work in a much more
natural way.

As an example, navigate to a Rails application folder on your machine and start
a Tmux session (if you're not inside one already). Then, open `vim`.

Let's assume you have a `User` model (really, any model is fine, this is just
for example purposes), so open `app/models/user.rb`. We already know that `:A`
lets us navigate to the corresponding spec file and back, but we can also do
better:

- Press `leader-t`: that creates (if not present) a pane below in the current
  window to run the spec for the current file. So if you press it with
`app/models/user.rb` visible, it's smart enough to execute the test for that
file, independently from the testing framework (RSpec, TestUnit or MiniTest).

- Now switch to the spec file and press `leader-f`: it will run the test passing
  the current line number as an extra argument (a properly focused test).

- If you now switch back to the implementation file and rerun `leader-f`, Vim
  will remember the line number used in step 2, so that you can easily run only
the test you need for the implementation you're working on.

- What if you want to run a complex piece of code in the rails console? Just
  open one in the split pane, select a visual block in Vim and press `leader-r`.
It will send it to the split pane, running it into the console.

# License

**This code is free to use under the terms of the MIT license.**

Permission is hereby granted, free of charge, to any person obtaining
a copy of this software and associated documentation files (the
"Software"), to deal in the Software without restriction, including
without limitation the rights to use, copy, modify, merge, publish,
distribute, sublicense, and/or sell copies of the Software, and to
permit persons to whom the Software is furnished to do so, subject to
the following conditions:

The above copyright notice and this permission notice shall be included
in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND,
EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF
MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT.
IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY
CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT,
TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE
SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

# Copyright

Copyright ©2012 New Bamboo Web Development Limited
