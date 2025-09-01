---
title: "Linux Kernel Mentorship"
date: 2025-09-01
---

## My experience in a nutshell

The Linux foundation organizes a bunch of mentorships, some paid, some unpaid, including the Linux Kernel Bug Fixing one. I joined in March 2025, after I submitted a bunch of tasks required for the admission.
The task are fairly easy and they make sure you have a good basis of how kernel development works. Be prepared to know how to build a kernel, generate an oeps, write a simple kernel module. It also is required to complete the [LFD103 course](https://training.linuxfoundation.org/training/a-beginners-guide-to-linux-kernel-development-lfc103/) which explains how you can contribute upstream.

After joining, you are asked to choose two subsystems of interest. This is needed to give you some focus because the kernel is quite big and looking everywhere is quite overwhelming and impossible for many.
I personally choose bcachefs because I am interested in filesystems. Later I also choose faux device and platform device.

The mentorship is mainly about solving bugs, but it encourages you to explore the kernel and find bugs yourselves. Running kselftests with special compile flags enabled is an option. Otherwise, checking [syzcaller reported bugs](https://syzkaller.appspot.com/upstream) provides plenty of opportunity to dive into debugging.

That's what I did, even though I managed to send only one fix so far.
I recommend checking the KMSAN ones for uninitialized variables before jumping to harder ones.

I also explored some KSPP issues like [replacing strncpy() uses](https://github.com/KSPP/linux/issues/90) and I ended up doing that for bcachefs. 

In early spring, a new type of device was introduced faux device. I took that opportunity to learn about it and find drivers that could use them. I came across a new shortcomming of probing a faux device, the lack of deferred probing. I submited a [suggestion](https://lore.kernel.org/linux-kernel-mentees/20250506125133.108786-1-nicolescu.roxana@protonmail.com/) and I am still waiting for feedback on it. 

I managed to have 5 patches merged upstream, and 2 of them are still in review. The changes were modest but it gave me some courage to keep trying.

I totally recommend this mentorship. You participate in weekly meeting where you can ask questions to your mentor and it makes you stay on track. My personal opinion is that I wish it provided a bit more guidance. If you expect someone to give you advice on what to do, this is not a good place for you. This mentorship gives you tools, but in the end you are the one figuring out what to do by exploring the kernel.
   
