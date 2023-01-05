---
date: 2023-01-05
comments: true
categories:
  - How-Tos
tags:
  - Github
  - Website
---

# Setup multiple github pages with different custom domains

Github Pages is an easy and nice solution to hosting static websites free of cost.
It's as easy as navigating to the settings of your Repo and enabling Pages and
choosing your folder and branch that hosts the files. Recently I got stuck with a
small hick up while setting this up. I wanted to have separate domains for multiple
projects/repos.

## Objective
When you create a repository with the name as your `username` then the gh-pages
creates a url `username.github.io` by default. When you create a new repository
`repo` and set up gh-pages for that, the URL will be `username.github.io/repo`.
Github allows you set a global custom domain with all other `repo` gh-pages will
be available as a sub-domain under the global one. For eg. my global domain is
`thenoetic.me` and this sites URL was set to `thenoetic.me/ruminer.`This was an
issue as I wanted to set `ruminer.space` as the domain for my digital garden.

## Instructions
Fortunately there's an easy fix for this. The setup procedure is same for both
domains. Configure the DNS server on the domain service by adding 2 custom DNS
records following the instructions [here](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/managing-a-custom-domain-for-your-github-pages-site)

- Configure an Apex domain by setting up an `A` record with the IPs specified in
  the [link](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/managing-a-custom-domain-for-your-github-pages-site)
- Configure a `CNAME` record. Set the `Host name` to `www` and the `Data` field to
  your github url `username.github.io`.
- Do the above steps for both your domains.

!!! warning

    The `Data` entry should be the same for both domains:
    `username.github.io` and NOT `username.github.io/repo` for your 2nd domain

- Now go to the settings page of your github repositories and set their individual Custom domains with their corresponding URLs.
- Voila !! You're done.

!!! note

    It takes a few hours for the changes to reflect. So it's okay if you don't
    see your page load immediately.