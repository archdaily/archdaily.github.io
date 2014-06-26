---
layout: post
title:  "Rebasing like a pro"
author: Mauricio Ulloa
date:   2014-06-26 18:00:00
categories: git
tags: git software-engineering rebase
---



`Rebase` is a very complex and unpopular Git command, but it's also a very powerful one.

## What is rebasing?

Rebasing is a Git's concept of changing local history and modifying some changes made in the past. With `rebase` you can edit, merge, change, create and insert commits in a branch's history.

## How does `rebase` works?

A Git rebase "rewinds" to a certain point of the history, makes the changes in a specific commit and then "replays" all the rewinded commits, giving the possibility of resolving conflicts.

## Why I always use `rebase`?

I use `rebase` to insert in the branches that I'm currently working on the new changes in master, so they have the latest features and we can have a [beautiful Git pipeline][Our-Very-Simple-Git-Branching-Model].

For example:

You are working on the uploader of your application in a branch named `fix-uploader`




[Our-Very-Simple-Git-Branching-Model]: "http://archdaily.github.io/softwre-engineering/git/gitflow/2014/03/19/our-gitflow.html"
