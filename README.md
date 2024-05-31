# How to use multiple ssh for github on Mac

## Generate ssh keys

Use these commands to generate ssh key for every user we need

```ssh
ssh-keygen -t rsa -C "user1@email.com" -f "user1"
ssh-keygen -t rsa -C "user2@email.com" -f "user2"
ssh-keygen -t rsa -C "user3@email.com" -f "user3"
...
```

These commands generate a pair of key files in `~/.ssh`

```
user1 user1.pub
user2 user2.pub
user3 user3.pub
```
## Add ssh keys to agent

```sh
ssh-add ~/.ssh/user1
ssh-add ~/.ssh/user2
ssh-add ~/.ssh/user3
```

note: we need to run ssh-add everytime we restart the laptop, or we can add this command automatically in our zsh on bootstrap.

## Add keys to every github profile

copy every pub key and past it in the ssh section in every github profile

for copy the content without opening the file use this command
```ssh
pbcopy < ~/.ssh/user1.pub
```

## Check global profile, this will be used by default

```
git config --global -e
```

```
[user]
        name = user1
        email = user1@email.com
```

## Test connection

```sh
ssh -T git@github.com
```

```
Hi user1! You've successfully authenticated, but GitHub does not provide shell access.
```

by default every repo will use the global one.


## clone the repo with the correct ssh key

```
git clone git@provider.com:user1/projectName.git --config core.sshCommand="ssh -i ~/.ssh/user1"
```


## define local github user
To use a different user we need modify the repo `.git/config` file

in the repo folder run `git config --local -e` to access and edit the file

```sh
[core]
        repositoryformatversion = 0
        filemode = true
        bare = false
        logallrefupdates = true
        ignorecase = true
        precomposeunicode = true
[remote "origin"]
        url = git@github.com:user2/reponame.git
        fetch = +refs/heads/*:refs/remotes/origin/*
[branch "main"]
        remote = origin
        merge = refs/heads/main
```

we need to add the user block and the sshCommand in the core block as following

```
[user]
        name=user2
        email=user2@email.com
[core]
        sshCommand = ssh -i ~/.ssh/user2 -F /dev/null
        repositoryformatversion = 0
        filemode = true
        bare = false
        logallrefupdates = true
        ignorecase = true
        precomposeunicode = true
[remote "origin"]
        url = git@github.com:dcomedevelopers/learning-react-query.git
        fetch = +refs/heads/*:refs/remotes/origin/*
[branch "main"]
        remote = origin
        merge = refs/heads/main
```

from now on this repo will use the defined user automatically.
