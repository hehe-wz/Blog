# Add hg Prompt Support in zsh

## Issue
Normally we add {hg_prompt_info} in theme config file to support hg prompt. [Link](https://github.com/robbyrussell/oh-my-zsh/tree/master/plugins/mercurial). But it become very slow when working with large repo like FB codebase, ~5 seconds.

## Solution
Since what I need for now is only the current bookmark name, I don't need the flexbile extension offered by existing {hg_prompt_info} or hg extension [hg-prompt]()https://bitbucket.org/sjl/hg-prompt).

### Edit
In ~/.oh-my-zsh/plugins/mercurial/mercurial.plugin.zsh, change in_hg() and hg_prompt_info() to
```bash

function in_hg() {
#  if [[ -d .hg ]] || $(hg summary > /dev/null 2>&1); then
#    echo 1
#  fi
  # Check if the current directory all any parent directory has .hg folder	
  parent=`pwd`
  while [[ $parent == $HOME/* ]]; do
    if [[ -d "$parent/.hg" ]]; then
      echo 1
      break
    fi
    parent=`dirname $parent`
  done
}

function hg_prompt_info {
  if [ $(in_hg) ]; then
    parent=`pwd`
    while [[ $parent == $HOME/* ]]; do
      if [[ -d "$parent/.hg" ]]; then
        echo \<`cat $parent/.hg/bookmarks.current 2>/dev/null`\>
        break
      fi
      parent=`dirname $parent`
    done
  fi
}
```

### Need to notice
In in_hg(), though ```hg --cwd . root``` can do that same trick, it takes around half second, which is reasonably large considering how many "enter" you'll type in terminal.