---
title: shell
date: 2021-11-01 14:41:13
categories:
tags:
cover:
---

### A Learning Tutorial of Bash Shell Scripts

### Basic Grammar

#### The list command

a list command is composited of a list of commands separated by **||** , **;** , **&&** **&** 

**( list )** this command will execute the subcommands of  list in a subshell environment

**{ list; }** this command use current shell environment to execute the subcommands of list

**(( expression ))**  evaluate the expression via arithmetic evaluation

**[[ expression ]]**  evaluate the expression via conditional evaluation

 

#### IF grammar

```bash
if command ;
then command2;
elif comand1;
then command2;
else command_f;
fi
```

