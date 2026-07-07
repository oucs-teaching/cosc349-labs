---
title: COSC349 Lab 1—Cloud Architecture—2026
tags: [cosc349, lab]

---

## Lab 1—Git, Ōwheo Building labs, and Unix shells

The computers in the School of Computing's Ōwheo Building teaching labs (SoC labs) have seen many hardware and software (and building) changes over the past few years.

This year the machines are running Microsoft Windows, but with appropriate software to support this lab.

I aim for you to be able to do the labs on your own computers too, although clearly I can't well support all possible environments. If your computer runs Microsoft Windows you should have a similar experience to that in the teaching labs. Linux and macOS are Unix-based operating systems, so have ready access to a Unix shell, e.g., if you are using your own computer.

On SoC lab computers running Windows, I was able to reach a Unix shell by searching for `Git bash` and running the program that was found (it's under the "Git" folder in the Start menu). 

:::warning
:exclamation: Note that the COSC349 was launched back in 2019 with the expectation of an updated CS 200-level set of papers. These were first run in 2022. Finally the School of Computing was formed in 2023, but the curriculum will still be in flux for a few years. Thus there will be students who have taken different paths through the School of Computing, CompSci, and InfoSci papers, and this may affect your experience in COSC349. Please just let the COSC349 teaching team know where the pace is too fast (or too slow).
:::

### Lab objectives

1. To ensure all students understand how to gain basic proficiency with the Unix shell and the teaching lab environment. The majority of students in COSC349 will already have this proficiency, and can skip [Lab 1(a)][].
1. To ensure all students have at least basic proficiency in using the Git version management tool. We will provide you with two different resources for learning Git in [Lab 1(b)][].

[Lab 1(a)]: #Lab-1a—Proficiency-with-Unix-shell-and-the-CS-lab-environment
[Lab 1(b)]: #Lab-1b—Gaining-git-proficiency

### Software Carpentry

We link to Software Carpentry material both for Unix shell and Git teaching within this lab. For some more context, as stated online: "Software Carpentry is a volunteer project dedicated to teaching basic computing skills to researchers". All of the Software Carpentry lessons are open source and freely available at at https://software-carpentry.org/

I (Dave) have been involved in the development and use of the Software Carpentry teaching material, and am a certified Software Carpentry instructor.

## Lab 1(a)—Proficiency with Unix shell and the SoC lab environment

:::warning
:exclamation: All students in COSC349 are expected to be able to work effectively on the SoC lab machines, including at least basic proficiency in using the Unix shell to navigate the directory structure, manipulate files, and use command-line tools.
:::

For students unfamiliar with Unix shells we link to the Software Carpentry Unix shell lesson at 
https://swcarpentry.github.io/shell-novice/

This teaching material aims to support use of the shell on any computer, and is not written with respect to the SoC labs. You should aim to be comfortable with all of the seven episodes of that lesson. 


## Lab 1(b)—Gaining Git proficiency

:::warning
:exclamation:
All students in COSC349 are expected to be able to use the Git version control system to create and manage your own repositories, and to be able to clone and collaborate with others using their repositories.
:::

### (optional) We're keen to study your Git learning journey

The COSC349 teaching team, and other members of the School, are investigating which resources best teach Git to different types of learners. So beyond you learning how to use Git from these materials, we are keen to hear (informally) whether the resources suited you, and indeed any other feedback that you might have about these resources and your experiences with them.

### Resource-A: Software Carpentry Git lesson

The Software Carpentry Git lesson is targeted at learners with less technical confidence and experience than 300-level SoC students, however we believe that this will just mean that you should be able to progress rapidly through the lesson material.

The Git lesson available at https://swcarpentry.github.io/git-novice/

You should expect to cover the material up to and including episode 9 "Conflicts". The material is presented so that you can progress non-linearly, and you can choose whether or not you want to complete all of the exercises included on the lesson's pages.

### Resource-B: Learn Git branching

:::warning
:warning: 
The Microsoft Edge browser has been reported not to work properly with Learn Git branching, but Google Chrome works, and is installed on the SoC lab computers.
:::

These exercises are targeted at those who already have some familiarity with Git. If you are completely new to Git we recommend starting with the Software Carpentry lesson (above).

"Learn Git Branching" treats its Git exercises rather like a puzzle game. It is accessed through an interactive website, combining a pseudo-shell input on the left, and a visualisation of the Git repository on the right. **NB:** The traffic-light buttons on the pretend windows are purely decorative.

They are available at https://learngitbranching.js.org/

You should try to complete the first ten "Main" levels (the "Introduction Sequence", "Ramping Up", and "Moving Work Around" sections), and the first seven "Remote" levels (the "Push & Pull -- Git Remotes!" section). They do not have to be completed in order.

You can change level at any time by entering the command `levels` into the interpreter. Help is available via the `help` command.## Lab 1—Git, Ōwheo Building labs, and Unix shells

The computers in the School of Computing's Ōwheo Building teaching labs (SoC labs) have seen many hardware and software (and building) changes over the past few years.

This year the machines are running Microsoft Windows, but with appropriate software to support this lab.

I aim for you to be able to do the labs on your own computers too, although clearly I can't well support all possible environments. If your computer runs Microsoft Windows you should have a similar experience to that in the teaching labs. Linux and macOS are Unix-based operating systems, so have ready access to a Unix shell, e.g., if you are using your own computer.

On SoC lab computers running Windows, I was able to reach a Unix shell by searching for `Git bash` and running the program that was found (it's under the "Git" folder in the Start menu). 

:::warning
:exclamation: Note that the COSC349 was launched back in 2019 with the expectation of an updated CS 200-level set of papers. These were first run in 2022. Finally the School of Computing was formed in 2023, but the curriculum will still be in flux for a few years. Thus there will be students who have taken different paths through the School of Computing, CompSci, and InfoSci papers, and this may affect your experience in COSC349. Please just let the COSC349 teaching team know where the pace is too fast (or too slow).
:::

### Lab objectives

1. To ensure all students understand how to gain basic proficiency with the Unix shell and the teaching lab environment. The majority of students in COSC349 will already have this proficiency, and can skip [Lab 1(a)][].
1. To ensure all students have at least basic proficiency in using the Git version management tool. We will provide you with two different resources for learning Git in [Lab 1(b)][].

[Lab 1(a)]: #Lab-1a—Proficiency-with-Unix-shell-and-the-CS-lab-environment
[Lab 1(b)]: #Lab-1b—Gaining-git-proficiency

### Software Carpentry

We link to Software Carpentry material both for Unix shell and Git teaching within this lab. For some more context, as stated online: "Software Carpentry is a volunteer project dedicated to teaching basic computing skills to researchers". All of the Software Carpentry lessons are open source and freely available at at https://software-carpentry.org/

I (Dave) have been involved in the development and use of the Software Carpentry teaching material, and am a certified Software Carpentry instructor.

## Lab 1(a)—Proficiency with Unix shell and the SoC lab environment

:::warning
:exclamation: All students in COSC349 are expected to be able to work effectively on the SoC lab machines, including at least basic proficiency in using the Unix shell to navigate the directory structure, manipulate files, and use command-line tools.
:::

For students unfamiliar with Unix shells we link to the Software Carpentry Unix shell lesson at 
https://swcarpentry.github.io/shell-novice/

This teaching material aims to support use of the shell on any computer, and is not written with respect to the SoC labs. You should aim to be comfortable with all of the seven episodes of that lesson. 


## Lab 1(b)—Gaining Git proficiency

:::warning
:exclamation:
All students in COSC349 are expected to be able to use the Git version control system to create and manage your own repositories, and to be able to clone and collaborate with others using their repositories.
:::

### (optional) We're keen to study your Git learning journey

The COSC349 teaching team, and other members of the School, are investigating which resources best teach Git to different types of learners. So beyond you learning how to use Git from these materials, we are keen to hear (informally) whether the resources suited you, and indeed any other feedback that you might have about these resources and your experiences with them.

### Resource-A: Software Carpentry Git lesson

The Software Carpentry Git lesson is targeted at learners with less technical confidence and experience than 300-level SoC students, however we believe that this will just mean that you should be able to progress rapidly through the lesson material.

The Git lesson available at https://swcarpentry.github.io/git-novice/

You should expect to cover the material up to and including episode 9 "Conflicts". The material is presented so that you can progress non-linearly, and you can choose whether or not you want to complete all of the exercises included on the lesson's pages.

### Resource-B: Learn Git branching

:::warning
:warning: 
The Microsoft Edge browser has been reported not to work properly with Learn Git branching, but Google Chrome works, and is installed on the SoC lab computers.
:::

These exercises are targeted at those who already have some familiarity with Git. If you are completely new to Git we recommend starting with the Software Carpentry lesson (above).

"Learn Git Branching" treats its Git exercises rather like a puzzle game. It is accessed through an interactive website, combining a pseudo-shell input on the left, and a visualisation of the Git repository on the right. **NB:** The traffic-light buttons on the pretend windows are purely decorative.

They are available at https://learngitbranching.js.org/

You should try to complete the first ten "Main" levels (the "Introduction Sequence", "Ramping Up", and "Moving Work Around" sections), and the first seven "Remote" levels (the "Push & Pull -- Git Remotes!" section). They do not have to be completed in order.

You can change level at any time by entering the command `levels` into the interpreter. Help is available via the `help` command.