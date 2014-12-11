---
layout: post
title: "Using Your OpenPGP Key as Your SSH Key"
date: 2014-01-26 11:37:03 -0600
comments: true
categories: [OpenPGP, GnuPG Privacy, SSH]
---

What I was able to do is generate a gpg key for myself (you can also use an existing one) 
and then generate a subkey to use as an SSH key for my work. This setup allows me to unlock my key only 
when I actually need it and keeps my key unlocked for a specified TTL. 

<!-- more -->

How to use your OpenPGP (GnuPG) key as an SSH key and setup gpg-agent for SSH
-----------------------------------------------------------------------------

1. If you haven't already, you need to generate your gpg key:

   {% codeblock %}
   gpg --gen-key
   {% endcodeblock %}

   Follow the instructions and look into OpenPGP/GnuPG if you haven't already.

2. Install "monkeysphere" (the following command is for debian based distros):

   {% codeblock %}
   apt-get install monkeysphere
   {% endcodeblock %}

   Monkeysphere provides some useful functionality and tools for working with your
   gpg keys.

3. Use monkeysphere to generate a subkey with gpg for use with SSH:

   {% codeblock %}
   monkeysphere g
   {% endcodeblock %}

   **Note: You only need to do this once for the lifetime of your key - even
   across other machines.**

   This generates a subkey attached to your gpg key which will be trasfered whenever you
   migrate your key. 

4. Install the gnupg-agent (the following is for debian based distros):

   {% codeblock %}
   apt-get install gnupg-agent
   {% endcodeblock %}

5. Start the gpg-agent daemon along with ssh feature functionality:

   {% codeblock %}
   eval $(gpg-agent --daemon --enable-ssh-support)
   {% endcodeblock %}

   If you want this to work accross logins and reboots you should add this to an 
   init script such as your ~/.bashrc file or ~/.zshrc file.

6. Have monkeysphere add the newly generated subkey to the ssh-agent:

   {% codeblock %}
   monkeysphere s
   {% endcodeblock %}

   You should only need to do this once, not every login.
   If you migrate/ want to set this up on another computer you will need to run this once again.

7. Check to see if it works by checking the ssh-agent for the available keys:

   {% codeblock %}
   ssh-add -L
   {% endcodeblock %}

You should see something that resembles a public ssh key that has a comment 
somewhere alongs the lines of what you commented your gpg key with.


Your setup should now work on-demand and automatically. Everytime you first
use ssh in a session, gpg-agent will ask you to unlock your key and then it should
keep it unlocked until it expires based on its TTL.
