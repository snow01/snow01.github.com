---
layout: post
title: Key based authentication for SSH (no password prompt)
date: 2009-11-17 18:49
author: shailendra
comments: true
categories: [Authorized Key, Known Hosts, Public Key, SSH, SSH Keygen, Tech]
---
<span style="color:#1f497d;font-size:11pt;">1.<span style="font-family:'Times New Roman';font-size:7pt;font-style:normal;font-variant:normal;font-weight:normal;line-height:normal;">       </span></span><span style="color:#1f497d;font-size:11pt;">On your server, do following: <strong>ssh-keygen –t rsa</strong></span>
<span style="color:#1f497d;font-size:11pt;">2.<span style="font-family:'Times New Roman';font-size:7pt;font-style:normal;font-variant:normal;font-weight:normal;line-height:normal;">       </span></span><span style="color:#1f497d;font-size:11pt;">Just keep pressing return key on all prompts – you must chose empty pass-phrase.</span>
<span style="color:#1f497d;font-size:11pt;">3.<span style="font-family:'Times New Roman';font-size:7pt;font-style:normal;font-variant:normal;font-weight:normal;line-height:normal;">       </span></span><span style="color:#1f497d;font-size:11pt;">It would generate a file named “<strong>id_rsa.pub</strong>” in <strong>/home//.ssh </strong>location. This location would also be shown in prompts of command (1).</span>
<span style="color:#1f497d;font-size:11pt;">4.<span style="font-family:'Times New Roman';font-size:7pt;font-style:normal;font-variant:normal;font-weight:normal;line-height:normal;">       </span></span><span style="color:#1f497d;font-size:11pt;">Sftp id_rsa.pub to the server you want key based login – <strong>sftp @ the-target-server </strong>and do “<strong>put /home/ssh/id_rsa.pub</strong>”. If file is already there overwrite it.</span>
<span style="color:#1f497d;font-size:11pt;">5.<span style="font-family:'Times New Roman';font-size:7pt;font-style:normal;font-variant:normal;font-weight:normal;line-height:normal;">       </span></span><span style="color:#1f497d;font-size:11pt;">Do ssh loging to the target server – <strong>ssh </strong></span><span style="color:#1f497d;font-size:11pt;"><strong>@ the-target-server</strong></span>
<span style="color:#1f497d;font-size:11pt;">6.<span style="font-family:'Times New Roman';font-size:7pt;font-style:normal;font-variant:normal;font-weight:normal;line-height:normal;">       </span></span><span style="color:#1f497d;font-size:11pt;">In the home directory (i.e. /home/user) you would see <strong>.ssh/authorized_keys</strong> file. </span>
<span style="color:#1f497d;font-size:11pt;">7.<span style="font-family:'Times New Roman';font-size:7pt;font-style:normal;font-variant:normal;font-weight:normal;line-height:normal;">       </span></span><span style="color:#1f497d;font-size:11pt;">Append the content of sftp’d id_rsa.pub to authorized_keys – <strong>cat id_rsa.pub &gt;&gt; .ssh/authorized_keys</strong></span>
<span style="color:#1f497d;font-size:11pt;">8.<span style="font-family:'Times New Roman';font-size:7pt;font-style:normal;font-variant:normal;font-weight:normal;line-height:normal;">       </span></span><span style="color:#1f497d;font-size:11pt;">Try ssh and sftp login from your server – this time you won’t be asked for password.</span>
<span style="color:#1f497d;font-size:11pt;">9.   Make sure your home directory (/home/user) have no write permission for the group and others. That is 0755 is the highest allowed permission.</span>
<span style="color:#1f497d;font-size:11pt;">10. Make sure same as (9) for /home/user/.ssh too.</span>
<span style="color:#1f497d;font-size:11pt;">11. Make sure /home/user/.ssh/authorized_keys file has highest permission as 644.
</span>
<div class="blogger-post-footer"><img alt="" width="1" height="1" /></div>
