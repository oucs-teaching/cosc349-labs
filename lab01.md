---
tags: cosc349
---
# COSC349 Lab 1—Cloud Architecture—2020
## Lab 1—`git`, CS labs, and the Unix shell

The computers in Lab E and Lab F can be (re)booted into macOS or Linux. Some COSC349 labs will require you to boot into macOS, although for the first part of this lab you can choose whichever operating system suits you better.

### Lab objectives

1. To ensure all students understand how to gain basic proficiency with the Unix shell and the Department of Computer Science (CS) teaching lab environment. The majority of students in COSC349 will already have this proficiency, and can skip [Lab 1(a)][].
1. To ensure all students have at least basic proficiency in using the `git` version management tool. We will provide you with two different resources for learning `git` in [Lab 1(b)][].

[Lab 1(a)]: #Lab-1a—Proficiency-with-Unix-shell-and-the-CS-lab-environment
[Lab 1(b)]: #Lab-1b—Gaining-git-proficiency

### Software Carpentry

We link to Software Carpentry material both for Unix shell and `git` teaching within this lab. For some more context, as stated online: "Software Carpentry is a volunteer project dedicated to teaching basic computing skills to researchers". All of the Software Carpentry lessons are open source and freely available at at https://software-carpentry.org/

I (Dave) am a certified Software Carpentry instructor, and have been involved in the development and use of the Software Carpentry teaching material.

## Lab 1(a)—Proficiency with Unix shell and the CS lab environment

:::warning
:exclamation: All students in COSC349 are expected to be able to work effectively on the CS lab machines, including at least basic proficiency in using the Unix shell to navigate the directory structure, manipulate files, and use command-line tools.
:::

For students unfamiliar with the CS lab environment, we provide two resources that you can work through:
- a reference to the first lab within COSC241: often the first time that CS students need to interact with the Unix shell; and
- a reference to the Software Carpentry Unix shell lesson. This teaching material aims to support use of the shell anywhere, and is not written with respect to the CS labs.

### COSC241 Lab 1

The first lab is available at https://www.cs.otago.ac.nz/cosc241/pdf/01-linux-intro.pdf although since you are not actually doing the exercises as part of COSC241 you should note the following:
- The section about **Getting Help** is not relevant: we are not using the `democall` tool, not least since COSC349 labs are not assessed.
- You can probably ignore the **Changing your password** section, as you have likely already set your account password to something secure.
- Do note the section discussing the `hex.otago.ac.nz` server and accessing files from off campus.
- The **Assessed exercises** can be taken as exercises, but they will not be assessed.
- Do not perform any of the steps under the **Marking** section.

### Software Carpentry Shell lesson

The Software Carpentry Unix Shell lesson is available at http://swcarpentry.github.io/shell-novice/

You should aim to be comfortable with all of the seven episodes of that lesson. 


## Lab 1(b)—Gaining git proficiency

:::warning
:exclamation:
All students in COSC349 are expected to be able to use the `git` version control system to create and manage your own repositories, and to clone and collaborate on others' repositories.
:::

### (optional) We're keen to study your `git` learning journey

The COSC349 teaching team, and other members of the CS Department, are investigating which resources best teach `git` to different types of learners. So beyond you learning how to use `git` from these materials, we are keen to hear (informally) whether the resources suited you, and indeed any other feedback that you might have about these resources and your experiences with them.

### Resource-A: Software Carpentry git lesson

The Software Carpentry git lesson is targeted at learners with less technical confidence and experience than 300-level CS students, however we believe that this will just mean that you should be able to progress rapidly through the lesson material.

The git lesson available at http://swcarpentry.github.io/git-novice/

You should expect to cover the material up to and including episode 9 "Conflicts". The material is presented so that you can progress non-linearly, and you can choose whether or not you want to complete all of the exercises included on the lesson's pages.

### Resource-B: Learn git branching

These exercises are targeted at those who already have some familiarity with `git`. If you are completely new to `git` we recommend starting with the Software Carpentry lesson (above).

"Learn Git Branching" treats its `git` exercises rather like a puzzle game. It is accessed through an interactive website, combining a pseudo-shell input on the left, and a visualisation of the `git` repository on the right. **NB:** The traffic-light buttons on the pretend windows are purely decorative.

They are available at https://learngitbranching.js.org/

You should try to complete the first ten "Main" levels (the "Introduction Sequence", "Ramping Up", and "Moving Work Around" sections), and the first seven "Remote" levels (the "Push & Pull -- Git Remotes!" section). They do not have to be completed in order.

You can change level at any time by entering the command `levels` into the interpreter. Help is available via the `help` command.
