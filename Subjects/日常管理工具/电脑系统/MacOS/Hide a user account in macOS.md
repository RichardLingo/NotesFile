---
title: Hide a user account in macOS
authors: Ethan Lin
year: 2024-12-01
tags:
  - 类型/笔记
  - 日期/2024-12-01
  - 内容/电脑
aliases:
  - Hide a user account in macOS
---
# Hide a user account in macOS



[Hide a user account in macOS - Apple Support](https://support.apple.com/en-us/102099)


# Hide a user account in macOS

If you need to assist a user, but don't want them to see your user account when they log in, learn how to hide a user account in the macOS login window.

This article is intended for system administrators. If you believe this issue affects you, contact the system administrator for your business or school.

## Hide a user account in the macOS login window

1. Log in with an administrator account.

2. Use this Terminal command. Substitute the short name of the user that you want to hide for _hiddenuser_:

`sudo dscl . create /Users/hiddenuser IsHidden 1`

The user account is also hidden in System Settings (or System Preferences) the next time it's opened. This command can't be used with the Guest user account. [Learn how to manage the Guest user account](https://support.apple.com/guide/mac-help/change-users-groups-guest-user-preferences-mh15600/mac).

If FileVault is enabled, a hidden user might still appear in the first login window when you turn on or restart your computer. 

## Show a hidden user account

If you want the hidden user to appear again in the login window, set the user’s IsHidden attribute to 0:

`sudo dscl . create /Users/hiddenuser IsHidden 0`

If you want, you can delete the IsHidden attribute instead.

## Hide the home directory and share point

This command hides the "/Users/hiddenuser" home directory:

`sudo chflags hidden /Users/hiddenuser`

These commands remove the Public Folder share point for the user with the long name “Hidden User”:

`sudo dscl delete Local/Defaults/SharePoints/Hidden\ User’s\ Public\ Folder/ exit`


> Published Date: October 20, 2023