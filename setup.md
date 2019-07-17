---
title: HEP Computing "Basics"
---

## What you need to know

Throughout this workshop we will assuming previous knowledge in Unix, Python, C++ and compiling to a basic level. Luckily these topics are heavily documented already and there is a lot of material available so if you feel you are not very confident with any of these prerequisites please read the links below and work through the examples before you arrive.

It is not necessary for you to be an expert but it would be beneficial to have some experience and to feel comfortable working with them.

> ## Prerequisite Checklist
> - Unix and the Terminal : Review and understand the contents of the [Software Carpentry Tutorial](http://swcarpentry.github.io/shell-novice/).
> - Essential C++ : Ensure you understand the concepts in the "Bare Minimum" checklist.
> - Building Code : Know the difference between compiling and linking.
> - Python : Review and understand the contents of the [Software Carpentry Tutorial](http://swcarpentry.github.io/python-novice-inflammation/).
>
{: .checklist}


## Unix and Shells

In Unix operating systems like Linux or OSX, the shell ("**the terminal**") is a program that interprets commands and acts as an intermediary
between the user and the inner workings of the operating system. It can help you navigate
your files and directories.  You can create (`touch` and `mkdir`), copy (`cp`) and delete (`rm`) files and repositories. Even more
powerful things are possible such as working remotely with `ssh`, the transfer of files with `scp` and
shell scripting tasks you do regularly.

> ## What are those commands?
>
> If you are wondering what any of the commands are in the above paragraph, then you can always use the shell to
> learn more by using the `man` command.  Read more about the `ssh` command by trying the following
> ```bash
> man ssh
> ```
> More importantly, if you are wondering how any of these commands work, then it is _imperative_ that you spend a day reviewing
> how the shell works **prior to the bootcamp**.
{: .callout}

Software Carpentry provides two very nice in-depth tutorials on the shell
- [Unix and the Shell](http://swcarpentry.github.io/shell-novice/) : <span style="color:red">**All participants should review this tutorial and ensure they are comfortable with its contents prior to the bootcamp.**</span>
- [Advanced Techniques](http://swcarpentry.github.io/shell-extras/) : This is useful to review, but not necessary for this bootcamp.

A few other very good go-to resources that are good to work through

* [History of Unix and the Shell](https://www.distributednetworks.com/basic-unix-shell-scripts/module2/available-unix-shells.php) : The backstory of all this
* [tutorialspoint.com](https://www.tutorialspoint.com/unix/unix-what-is-shell) : A good introduction to the shell and the basic utilities it has
* [Shell Scripting](https://www.shellscript.sh/) : How to write code in the "bash" programming language


Please take some time before the workshop to read through these.





## Essential C++

Throughout your studies and career in particle physics, you will find a large amount
of your code and software will be written in C++. Within ATLAS for example, the software
is very C++ based and many analysis frameworks are constructed using C++. The problem with
teaching C++ in the context of research is that it is such a broad programming language
that it becomes very difficult to adequately explain "the essential" things that you need to know.
It would be very worth your while to enroll in a formal university level introduction to C++
course and this _will_ pay dividends in your research. Fortunately, there is a large amount of C++
material available and hopefully you can gain some basic understanding of C++ essentials if
you are not familiar already. And for the purposes of this bootcamp, a certain amount of requisite
knowledge is necessary, as highlighted below.  A few good references are :

* [cplusplus.com](http://www.cplusplus.com/doc/tutorial/) : _The_ go-to reference for c++ with a solid tutorial.  <span style="color:red"> **All participants should review this tutorial and ensure they are comfortable with its contents.**</span> If you have questions about whether a particular concept is important or frequently used in HEP, please ask!
* [learncpp](https://www.learncpp.com/) : A more long and drawn out tutorial.  It presents the same thins as [cplusplus.com](http://www.cplusplus.com/doc/tutorial/), but from a different perspective.
* [tutorialspoint.com tutorial](https://www.tutorialspoint.com/cplusplus/) : Another very good reference with tutorials
* [W3School tutorial](https://www.w3schools.com/cpp/) : W3 Schools covers more than just c++ including HTML, Java, etc. as well


> ## The bare minimum
>
> Though the essential aspects and "bare minimum" is very subjective, a few of the most basic concepts that you should
> be comfortable with are the following.
> - Loops : [https://www.tutorialspoint.com/cplusplus/cpp_loop_types](https://www.tutorialspoint.com/cplusplus/cpp_loop_types)
> - Vectors : [https://www.tutorialspoint.com/cpp_standard_library/vector](https://www.tutorialspoint.com/cpp_standard_library/vector)
> - Pointers : [https://www.tutorialspoint.com/cplusplus/cpp_pointers](https://www.tutorialspoint.com/cplusplus/cpp_pointers)
> - Function : [https://www.tutorialspoint.com/cplusplus/cpp_functions](https://www.tutorialspoint.com/cplusplus/cpp_functions)
> - Classes : [https://www.tutorialspoint.com/cplusplus/cpp_classes_objects](https://www.tutorialspoint.com/cplusplus/cpp_classes_objects)
> - Namespaces : [https://www.tutorialspoint.com/cplusplus/cpp_namespaces](https://www.tutorialspoint.com/cplusplus/cpp_namespaces)
> - Inheritance/Polymorphism : [https://www.tutorialspoint.com/cplusplus/cpp_inheritance](https://www.tutorialspoint.com/cplusplus/cpp_inheritance)
>
> You can use this as the "bare minimum" checklist for this bootcamp and you will be expected to be familiar with these concepts throughout the bootcamp.
>
{: .callout}





## The Build Process

One of the computing bootcamp days will cover build systems, specifically [CMake](). To many individuals,
compilation of C++ source code into machine executables is simply "something you do" with the `g++` or `gcc`
commands.  However, there are a number of details and understanding them (at least the language to start)
will make things much more clear.  A few very nice tutorials that you should review and understand can be found here.
- [Tutorial 1](https://www3.ntu.edu.sg/home/ehchua/programming/cpp/gcc_make.html) : Very complete including a bit of history as well.
- [Tutorial 2](https://www.geeksforgeeks.org/compiling-with-g-plus-plus/) : A bit more succinct.
- [Tutorial 3](http://www.cplusplus.com/articles/2v07M4Gy/) : Only the concepts.

> ## The bare minimum
>
> Though the essential aspects and "bare minimum" is very subjective, a few of the most basic concepts that you should
> be comfortable with are the following.
> - Source Code
> - Headers
> - Compilation
> - Linking
> - Libraries
>
> You can use this as the "bare minimum" checklist for this bootcamp and you will be expected to be familiar with these concepts throughout the bootcamp.
>
{: .callout}



## Python

[Python](https://www.python.org/) is a popular programming language that runs on an interpreter system.
This means that unlike C++, you do not need to explicitly compile the code prior to running it.  Instead
the code can be executed as soon as it is written. This makes it very appropriate for writing scripts and testing
things quickly.  Python was designed for readability, and has some similarities to the English language with
influence from mathematics (e.g. [sets](https://docs.python.org/2/library/sets.html)). Furthermore, it is possible
to link C++ compiled libraries with python code, allowing you to form an "interface" between the two, thereby
taking advantage of the best of both worlds.

It is rare to not come across Python in some form when working in particle physics and data analysis, particularly
when using advanced techniques in machine learning. In the ATLAS
experiment, python is used in many places, most notably as a mechanism by which we "steer" our C++ code.  This is
done in the form of [jobOptions]().  However, in this tutorial, we will not explicitly be using jobOptions to execute our code.
This is something that is rather left for the [ATLAS software tutorial]().

Software Carpentry provides a very nice introductory tutorial to python - [Link to Tutorial](http://swcarpentry.github.io/python-novice-inflammation/).
<span style="color:red"> **All participants should review this tutorial and ensure they are comfortable with its contents prior to the bootcamp.**</span>

Beyond this, a few additional resources that are very useful
- [Python3](https://docs.python.org/3/) : Main point of entry for all things python3
- [Python3 Tutorial](https://docs.python.org/3/tutorial/index.html) : Included linked from the page above.  This is the official tutorial.
- [W3School Python Tutorial](https://www.w3schools.com/python/default.asp) : Like C++, W3Schools has their own tutorial. A different perspective.
- [TutorialsPoint Python Tutorial](https://www.tutorialspoint.com/python/) : ... and another perspective.




{% include links.md %}
