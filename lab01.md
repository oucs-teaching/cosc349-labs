## Lab 1—Git, CS labs, and the Unix shell

This week, the computers in Lab E and Lab F are iMacs but are running Microsoft Windows. Apparently soon the machines will change to non-Apple hardware running Windows. Linux and macOS are Unix-based operating systems, so have ready access to a Unix shell, e.g., if you are using your own computer.

On Windows computers at the University of Otago there are many ways to get access to a Unix terminal, and each option below should also already have Git installed:
- Use Unix functionality on the computer in front of you.
- Connect to the Otago Student Desktop, e.g., I just used the light version by starting a web browser (I used Google Chrome) and navigating to https://www.otago.ac.nz/studentdesktop
- (In the past, you could SSH to computers such as hextreme.otago.ac.nz but it was not turned back on after last week's power cut to the Owheo building.)

On Owheo Lab Computers running Windows, or the Otago Student Desktop I was able to reach a Unix shell by:
- Searching for `Git bash` and running the program that was found (it's under the "Git" folder in the Start menu). Note that the home directory location does not seem to be set up correctly on the CS lab computers, but was OK on the (remote) student desktop. See below for a fix.
On the Student Desktop I was also able to access a Unix shell by:
- Searching for and running the `bash` application. Note that the home directory location does not seem to be set up correctly. See below for a fix.

Fixing the home directory location on the Otago Student Desktop or the CS lab computers.
- It seems that the environment is sometimes not entirely set up as expected regarsding where your home directory is, etc.
- To fix this I found the following to work:
    - For `Git bash` try running the command `export HOME=/c/users/$USERNAME`
    - for `bash` try running the command `export HOME=/cygdrive/c/users/$USERNAME`
- After applying that fix, subsequent commands within that same terminal window such as `cd` or `cd ~` placed me where I expected to be---i.e., the same directory as where the "My Documents" shortcut on the Student Desktop desktop gets to.

:::info
Note that the COSC349 was launched back in 2019 with the expectation of an updated CS 200-level set of papers. These were first run in 2022. For the next few years there will be students who have taken different paths through the CS (and InfoSci) 200-level papers, and this may affect your experience in COSC349. Please just let the COSC349 teaching team know where the pace is too fast (or too slow).
:::

### Lab objectives

1. To ensure all students understand how to gain basic proficiency with the Unix shell and the Department of Computer Science (CS) teaching lab environment. The majority of students in COSC349 will already have this proficiency, and can skip [Lab 1(a)][].
1. To ensure all students have at least basic proficiency in using the Git version management tool. We will provide you with two different resources for learning Git in [Lab 1(b)][].

[Lab 1(a)]: #Lab-1a—Proficiency-with-Unix-shell-and-the-CS-lab-environment
[Lab 1(b)]: #Lab-1b—Gaining-git-proficiency

### Software Carpentry

We link to Software Carpentry material both for Unix shell and Git teaching within this lab. For some more context, as stated online: "Software Carpentry is a volunteer project dedicated to teaching basic computing skills to researchers". All of the Software Carpentry lessons are open source and freely available at at https://software-carpentry.org/

I (Dave) am a certified Software Carpentry instructor, and have been involved in the development and use of the Software Carpentry teaching material.

## Lab 1(a)—Proficiency with Unix shell and the CS lab environment

:::warning
:exclamation: All students in COSC349 are expected to be able to work effectively on the CS lab machines, including at least basic proficiency in using the Unix shell to navigate the directory structure, manipulate files, and use command-line tools.
:::

For students unfamiliar with the CS lab environment, we provide two resources that you can work through:
- We reproduce the first lab of COSC241, a paper last taught in 2021. COSC241's first lab is/was often the first time that CS students need to interact with the Unix shell. The Unix shell information is relevant except where ITS are updating the CS labs (this is a moving target); and
- We link to the Software Carpentry Unix shell lesson. This teaching material aims to support use of the shell on any computer, and is not written with respect to the CS labs.

### COSC241 Lab 1

The first COSC241 lab is still available at https://www.cs.otago.ac.nz/cosc241/pdf/01-linux-intro.pdf although since you are not actually doing the exercises as part of COSC241 you should note the following:
- You should ignore the sections:
    - **Updating your configuration files**
    - **Getting Help**
    - **Changing your password**
- Do note the section discussing the `hex.otago.ac.nz` server and accessing files from off campus, although how you access your files in the CS environment is something that ITS are progressively changing.
- Use `hextreme.otago.ac.nz` instead of `hex.otago.ac.nz`
- The **Assessed exercises** can be taken as exercises, but they will not be assessed (in COSC349).
- Do not perform any of the steps under the **Marking** section.

### Software Carpentry Shell lesson

The Software Carpentry Unix Shell lesson is available at https://swcarpentry.github.io/shell-novice/

You should aim to be comfortable with all of the seven episodes of that lesson. 


## Lab 1(b)—Gaining Git proficiency

:::warning
:exclamation:
All students in COSC349 are expected to be able to use the Git version control system to create and manage your own repositories, and to clone and collaborate on others' repositories.
:::

### (optional) We're keen to study your Git learning journey

The COSC349 teaching team, and other members of the CS Department, are investigating which resources best teach Git to different types of learners. So beyond you learning how to use Git from these materials, we are keen to hear (informally) whether the resources suited you, and indeed any other feedback that you might have about these resources and your experiences with them.

### Resource-A: Software Carpentry git lesson

The Software Carpentry git lesson is targeted at learners with less technical confidence and experience than 300-level CS students, however we believe that this will just mean that you should be able to progress rapidly through the lesson material.

The Git lesson available at https://swcarpentry.github.io/git-novice/

You should expect to cover the material up to and including episode 9 "Conflicts". The material is presented so that you can progress non-linearly, and you can choose whether or not you want to complete all of the exercises included on the lesson's pages.

### Resource-B: Learn git branching

These exercises are targeted at those who already have some familiarity with Git. If you are completely new to Git we recommend starting with the Software Carpentry lesson (above).

"Learn Git Branching" treats its Git exercises rather like a puzzle game. It is accessed through an interactive website, combining a pseudo-shell input on the left, and a visualisation of the Git repository on the right. **NB:** The traffic-light buttons on the pretend windows are purely decorative.

They are available at https://learngitbranching.js.org/

You should try to complete the first ten "Main" levels (the "Introduction Sequence", "Ramping Up", and "Moving Work Around" sections), and the first seven "Remote" levels (the "Push & Pull -- Git Remotes!" section). They do not have to be completed in order.

You can change level at any time by entering the command `levels` into the interpreter. Help is available via the `help` command.
