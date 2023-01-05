---
date: 2023-01-03
tags:
  - productivity
categories:
  - Productivity

---

# Tracking time spent

Time is the most precious resource at our disposal. As someone with ADHD, I
tend to get absorbed in tasks that I am excited about and in the process
often neglect the less engaging but important tasks.
The first step to deal with this is to log how I spend my time. From this
I hope to analyze and take measures to better organize my work and utilize
my available time wisely.

Thankfully, there are several software based solutions available to
do this which makes life easier. For my needs, I need an app that's
simple to use, stays out of my way for the most part and importantly stores
data locally. I have no needs for the bells and whistles that come with
the paid apps.

## Bartib
I came across this CLI utility called [Bartib](https://github.com/nikolassv/bartib).
Bartib is a simple time tracking tool that is accessed via the command line and
stores the log of tracked activities in plaintext file locally. It also has the
capability to produce reports.

### Installation
To install Bartib you need Rust to be installed on your system. If you don't
have rust you can install it by running the following command:
`curl https://sh.rustup.rs -sSf | sh`

To install the app itself:
`cargo install bartib`

Mac users can skip these steps and directly install it using `brew`.