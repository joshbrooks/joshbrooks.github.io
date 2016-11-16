---
layout: post
title: "Using GIT's commit hooks to check syntax"
date: 2016-11-16
---

PEP8 and ESLINT integration into a github project. This will prevent any commit where a modified Python file does not pass PEP8 checks or a JS file does not pass eslint.

Install the commit hook program from https://pypi.python.org/pypi/git-pep8-commit-hook

```
pip install git_pep8_commit_hook
```

Setup setup.cfg in your Git root directory

```
[pep8]
ignore = E501
```
Here we're going to pass on E501: long lines, since the 80 char limit is a bit outdated 

Before a commit git will run the "pre-commit" command in .git/hooks
``` bash
#!/usr/bin/env bash

# Set your virtual environment, if required 
# For instance if git_pep8_commit_hook is installed in your virtualenv

PROJECT=openly_py3
PATH=${WORKON_HOME}/${PROJECT}/bin:$PATH

for f in .git/hooks/pre-commit.d/*; do
    if [ -x "$f" ]; then
        if ! "$f"; then
            echo "DID NOT COMMIT YOUR CHANGES!";
            exit 1
        fi
    fi
done
```

Add linters to .git/hooks/pre-commit.d/

eslint_commit_hook
```
#!/usr/bin/env bash
# returns added (A), modified (M), untracked (??) filenames
function git_changed_files {
  echo $(git status -s | grep -E '[AM?]+\s.+?\.js$' | cut -c3-)
}
# run lint over changed files, if any
files=$(git_changed_files)
if [ ! -z "${files}" ]
    then eslint $files
fi
```

pep8_commit_hook
```
#!/usr/bin/env bash
git_pep8_commit_hook

```











