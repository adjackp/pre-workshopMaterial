---
title: "Introduction"
teaching: 10
exercises: 0
questions:
- "Why learn these computing techniques?"
- "How does this apply to particle physics and my analysis?"
- "How will this workshop help me?"
objectives:
- "Learn the context for this computing bootcamp"
- "Be introduced to the VH(bb) sample we will be using"
keypoints:
- "To explain the important computing concepts we will be running a dummy analysis"
- "The sample we are using is a V(ll)H(bb) DxAOD"
---

## Introduction

The aim of this computing bootcamp is to give students, post-docs and even academics an understanding of the underlying computing techniques they will be using throughout their analysis and particle physics careers. Not only introducing people to helpful concepts like version control and continuous integration but also looking at what actually happens when you compile your analysis code with CMake, so it is no longer seen as a black box. 

The introduce the modules throughout the week we will be asking you to create a small, simple pseudo-analysis before the workshop using a provided piece of code to loop over events in a ROOT file. You will then adapt this code to produce some histograms and add some event selection. The idea is to use this analysis as a 'payload' which you can adapt throughout the week and add what you learn each day of the tutrials. 

> ## Note
> 
> The point of this pre-workshop material and analysis is not to test your physics knowledge but to set you up with a context where you can see how the computing techniques you are learning can be applied to your own analysis.
>
>
{: .callout}

## V(ll)H(bb) Sample

Higgs boson physics is a large area of research at the LHC and gives an opportunity to understand the consistency of the SM and explore new physics effects. The Higgs decaying to b-quarks has the largest branching fraction of 58% and is a direct probe of Higgs boson-to-quark interactions. With VH production, you can exploit leptonic decays of the vector boson for an easy triggering / background reduction. 

This is the process we will be using for our analysis payload throughout this workshop. For more information, the dataset ID is 345055.


![](../fig/VHbb.png)

![](../fig/ZllHbb.png)

## Add Section on EDM????

Maybe, ask others

{% include links.md %}

