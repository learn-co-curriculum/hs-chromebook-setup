---
tags: setup, environment, bash, kids
languages: ruby
---

## Environment Setup for Students Using Chromebooks
Having trouble with the Chrubuntu install script. No worries! Follow the steps below to get set up.

Don't worry too much about the how's and why's of all of this. Our goal is to get you up and running as fast as possible!

##1. Installing RVM

RVM stands for Ruby Version Manager. Programming languages, just like software, has newer and older versions. RVM allows you to have multiple versions of ruby installed on your computer that you can easily switch back and forth between.

```
gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3

\curl -sSL https://get.rvm.io | bash -s stable --ruby
```

This command will install the latest stable version of ruby - it may ask you to input your computer password during installation. Type `ruby -v` in your terminal to make sure ruby installed properly. You should see `ruby 2.2.0` or later. If you don't see that paste this into your terminal and try one more time:

```
source /home/flatiron/.rvm/scripts/rvm
```

##2. Install bundler

Type this command into your terminal to download the bundler gem:
```
gem install bundler
```

##3. Install the Learn gem

Now that we have RVM and bundler installed, let's install our first custom gem from the Flatiron School.

This gem isn't open-sourced, so we won't be downloading it from [RubyGems.org](https://rubygems.org/), which is where most gems are hosted. Before we download it, we will need to specify where it's coming from, which is a private server at Flatiron. Type this into your command line:

`gem sources -a http://flatiron:33west26@gems.flatironschool.com/`

Next, download the gem:

`gem install learn-co`

The learn gem will allow us to run the tests for challenges and labs - to see if your code is working properly. It's based off of [RSpec](https://www.relishapp.com/rspec), a popular testing framework in Ruby, but it does a bit more like help track your progress on labs.

Go ahead and install RSpec too: `gem install rspec`

Normally when we run our tests, we would type `rspec`, but to run the tests for challenges and labs, you'll type `learn` into your command line instead, within the root directory of the challenge/lab.


##4. Set Up Your `.bashrc`.

We'll spend a lot more time in terminal today, but it's helpful if your `.bashrc` looks just like mine. We're going to open terminal and enter the following commands:

`cd ~`

`ls -lah`

This command should bring up a list of all the files on your computer, including secret hidden files that begin with a `.`. We're looking for a file called `.bashrc`.
If it's there go ahead and enter `open .bashrc`.
It it's not there, enter `touch .bashrc` followed by `open .bashrc`.

We're going to use the standard for ours. Copy and paste this code below into your `.bashrc` and save it.

```bash
# THIS IS CONFIGURED FOR UBUNTU

# Configuring Our Prompt
# ======================
  # if you install git via homebrew, or install the bash autocompletion via homebrew, you get __git_ps1 which you can use in the PS1
  # to display the git branch.  it's supposedly a bit faster and cleaner than manually parsing through sed. i dont' know if you care
  # enough to change it

  # This function checks if your branch has uncommited changes on them.
  function parse_git_dirty {
    [[ $(git status 2> /dev/null | tail -n1) == "nothing to commit, working directory clean" ]] && echo " ✔"
    [[ $(git status 2> /dev/null | tail -n1) != "nothing to commit, working directory clean" ]] && echo " ✘"
  }

  # This function is called in your prompt to output your active git branch.
  function parse_git_branch {
      git branch --no-color 2> /dev/null | sed -e '/^[^*]/d' -e "s/* \(.*\)/ (\1$(parse_git_dirty))/"
  }

  # This function builds your prompt. It is called below
  function prompt {
    # Define some local colors
    local         RED="\[\033[0;31m\]" # This syntax is some weird bash color thing I never
    local   LIGHT_RED="\[\033[1;31m\]" # really understood
    local        CHAR="♥"
    # ♥ ☆ - Keeping some cool ASCII Characters for reference

    # Here is where we actually export the PS1 Variable which stores the text for your prompt
    export PS1="\u [\[\e[37;44;1m\]\t\[\e[0m\]]$RED\$(parse_git_branch) \[\e[32m\]\W\[\e[0m\]\n\[\e[0;31m\]$CHAR \[\e[0m\]"
      PS2='> '
      PS4='+ '
    }

  # Finally call the function and our prompt is all pretty
  prompt

  # For more prompt coolness, check out Halloween Bash:
  # http://xta.github.io/HalloweenBash/

  # If you break your prompt, just delete the last thing you did.
  # And that's why it's good to keep your dotfiles in git too.

# Environment Variables
# =====================
  # Library Paths
  # These variables tell your shell where they can find certain
  # required libraries so other programs can reliably call the variable name
  # instead of a hardcoded path.

    # NODE_PATH
    # Node Path from Homebrew I believe
    export NODE_PATH="/usr/local/lib/node_modules:$NODE_PATH"

    # Those NODE & Python Paths won't break anything even if you
    # don't have NODE or Python installed. Eventually you will and
    # then you don't have to update your bash_profile

  # Configurations

    # GIT_MERGE_AUTO_EDIT
    # This variable configures git to not require a message when you merge.
    export GIT_MERGE_AUTOEDIT='no'

    # Editors
    # Tells your shell that when a program requires various editors, use sublime.
    # The -w flag tells your shell to wait until sublime exits
    export VISUAL="subl -w"
    export SVN_EDITOR="subl -w"
    export GIT_EDITOR="subl -w"
    export EDITOR="subl -w"

  # Paths

    # The USR_PATHS variable will just store all relevant /usr paths for easier usage
    # Each path is seperate via a : and we always use absolute paths.

    # A bit about the /usr directory
    # The /usr directory is a convention from linux that creates a common place to put
    # files and executables that the entire system needs access too. It tries to be user
    # independent, so whichever user is logged in should have permissions to the /usr directory.
    # We call that /usr/local. Within /usr/local, there is a bin directory for actually
    # storing the binaries (programs) that our system would want.
    # Also, Homebrew adopts this convetion so things installed via Homebrew
    # get symlinked into /usr/local
    export USR_PATHS="/usr/local:/usr/local/bin:/usr/local/sbin:/usr/bin"

    # Hint: You can interpolate a variable into a string by using the $VARIABLE notation as below.

    # We build our final PATH by combining the variables defined above
    # along with any previous values in the PATH variable.

    # Our PATH variable is special and very important. Whenever we type a command into our shell,
    # it will try to find that command within a directory that is defined in our PATH.
    # Read http://blog.seldomatt.com/blog/2012/10/08/bash-and-the-one-true-path/ for more on that.
    export POSTGRES_PATH="/usr/lib/postgresql/9.3/bin"

    export PATH="$USR_PATHS:$POSTGRES_PATH:$PATH"
    # If you go into your shell and type: $PATH you will see the output of your current path.
    # For example, mine is:
    # /Users/avi/.rvm/gems/ruby-1.9.3-p392/bin:/Users/avi/.rvm/gems/ruby-1.9.3-p392@global/bin:/Users/avi/.rvm/rubies/ruby-1.9.3-p392/bin:/Users/avi/.rvm/bin:/usr/local:/usr/local/bin:/usr/local/sbin:/usr/bin:/usr/local/mysql/bin:/usr/local/share/python:/bin:/usr/sbin:/sbin:

# Helpful Functions
# =====================

# A function to CD into the desktop from anywhere
# so you just type desktop.
# HINT: It uses the built in USER variable to know your OS X username

# USE: desktop
#      desktop subfolder
function desktop {
  cd /$HOME/Desktop/$@
}

# A function to easily grep for a matching process
# USE: psg postgres
function psg {
  FIRST=`echo $1 | sed -e 's/^\(.\).*/\1/'`
  REST=`echo $1 | sed -e 's/^.\(.*\)/\1/'`
  ps aux | grep "[$FIRST]$REST"
}

# A function to extract correctly any archive based on extension
# USE: extract imazip.zip
#      extract imatar.tar
function extract () {
    if [ -f $1 ] ; then
        case $1 in
            *.tar.bz2)  tar xjf $1      ;;
            *.tar.gz)   tar xzf $1      ;;
            *.bz2)      bunzip2 $1      ;;
            *.rar)      rar x $1        ;;
            *.gz)       gunzip $1       ;;
            *.tar)      tar xf $1       ;;
            *.tbz2)     tar xjf $1      ;;
            *.tgz)      tar xzf $1      ;;
            *.zip)      unzip $1        ;;
            *.Z)        uncompress $1   ;;
            *)          echo "'$1' cannot be extracted via extract()" ;;
        esac
    else
        echo "'$1' is not a valid file"
    fi
}

# Aliases
# =====================
  # LS
  alias l='ls -lah'

  # Git
  alias gst="git status"
  alias gl="git pull"
  alias gp="git push"
  alias gd="git diff | mate"
  alias gc="git commit -v"
  alias gca="git commit -v -a"
  alias gb="git branch"
  alias gba="git branch -a"

  # short cuts for running a postgres server
  # NOTE this only works on Ubuntu
  # ex psql_server start, psql_server stop, psql_server restart
  alias psql_server="sudo /etc/init.d/postgresql"

# Case-Insensitive Auto Completion
bind "set completion-ignore-case on"

# Final Configurations and Plugins
# =====================

  # RVM
  # Mandatory loading of RVM into the shell
  # This must be the last line of your bash_profile always
  [[ -s "/$HOME/.rvm/scripts/rvm" ]] && source "/$HOME/.rvm/scripts/rvm"  # This loads RVM into a shell session.

export PATH="$PATH:$HOME/.rvm/bin" # Add RVM to PATH for scripting
```

## Congrats!

Phew! That was a lot of work, but now you are all set.





