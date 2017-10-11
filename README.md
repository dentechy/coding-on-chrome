# Tutorial on how to code through Chrome browser

## Introduction

As web developers, we can’t help but ask since we are ultimately writing code to display information that would be seen through a browser, why not just write that code on the browser itself? The advantage here is that you could theoretically have your code in one tab, then view the progress on the webpage in a separate one. You would not have to change windows to use a separate text editor, only change tabs.

This tutorial below will set up Chrome as a development environment. It should work on all major platforms: mac OS, Windows, Linux, Chrome OS. This tutorial assumes working knowledge of the Linux/Unix command line. 

The end result will be a terminal that can be accessed in a browser tab, as shown below. 

![chrome coding screenshot](assets/chrome-tmux.PNG)

## Prerequisites

The following software will be needed to successfully code in Chrome:

* Google Chrome
* Secure Shell Chrome Extension
* Terminal (built-in for mac OS and Linux users)
* Bash on Ubuntu on Windows (for Windows 10 users)
* SSH server
* Vim, a command line text editor [recommended]
* Tmux, a terminal multiplexer [recommended]

The general flow is the same across platforms:

1. Install Chrome
2. Install Secure Shell Chrome Extension
3. Setup SSH server
4. Login to SSH server through Secure Shell extension

However, the general details will differ depending on your platform of choice. Throughout the tutorial, platform-specific installation issues will be addressed.

## Chrome browser installation

Visit the following link to install Google Chrome:

https://www.google.com/chrome/browser/desktop/index.html

Install the Secure Shell Chrome Extension:

https://chrome.google.com/webstore/detail/secure-shell/pnhechapfaindjhompbnflcldabbghjo?hl=en

## Secure Shell extension setup

Once the Secure Shell extension is installed, you will want to change its default behavior from opening in a window to opening in a tab. This will make life much easier when developing web pages and needing to see on the fly changes. Also be sure to enable the bookmark bar on Chrome, so that you can easily bookmark the extension as shown below for easy access in the future.

