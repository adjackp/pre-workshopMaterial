---
title: "Obtaining The Test Files"
teaching: 30
exercises: 20
questions:
- What is an xAOD and a DxAOD?
- What can we learn from a DSID and filename?
- How can we obtain a test file?
objectives:
- Obtain the input DAOD file for the ZH(bb) process.
keypoints:
- An AOD and DAOD file are the same thing, but a DAOD has "what you want".
- The input file container name
---

## ATLAS Input Files

The ATLAS experiment uses an "Analysis Object Data" (AOD) file to store the event information which is the result
of a long chain of data taking or simulation and reconstruction - [Link to reco_tf.py]().  In fact, this file format
is made more user friendly by allowing it to interface and be browsable as an *__x__*AOD.  So if you have an xAOD available
(soon you will), you can simply open a TBrowser and [look at it like a normal ROOT file](https://twiki.cern.ch/twiki/bin/view/AtlasComputing/SoftwareTutorialxAODEDM#Browsing_the_xAOD_with_the_TBrow).

> ## A Root TTree
>
> A TTree in ROOT is an n-tuple and can be thought of as a very large, multi-dimensional vector that stores information.  This organization
> schema works well in collider physics because the analysis that we do is "event-based" where we care about studying the correlation
> of information within an event but (rarely) between events.
>
> If you are not familiar with the concept and usage of TTrees, please spend a couple hours reading about them here - [Link to TTree Documentation](https://root.cern.ch/doc/master/classTTree.html).
>
{: .callout}

This AOD format is what is generically produced during ATLAS reconstruction.  However, the content that is accessible may not be
precisely what you need for your analysis.  In some cases, it may be insufficient (i.e. you want to "add stuff") while in other cases it
may be overkill (i.e. you want to "remove stuff").  For this reason, AOD's are fed through a "derivation process" ([Link to DerivationFramework](https://twiki.cern.ch/twiki/bin/viewauth/AtlasProtected/DerivationFramework)).
What is produced is called a *__D__*AOD and is what most people use to perform analysis.  What comes out the other side is of **the same** file type
but it will contain different information and hopefully be smaller in size.

## Download the Analysis ROOT File

We will be working with events from a DAOD ROOT file that was initially produced to study the decays of Higgs to b-quarks.
The verbose name of the dataset container is
>```
>mc16_13TeV.345055.PowhegPythia8EvtGen_NNPDF3_AZNLO_ZH125J_MINLO_llbb_VpT.deriv.DAOD_EXOT27.e5706_s3126_r10724_p3840
>```

> ## Condensed Information
> From this long name, you can extract quite a bit of information about its origin :
> - `mc16_13TeV` : Production campaign and center of mass energy
> - `345055` : The **D**ata **S**et **ID**entifier of the container.  The technical details can be found by looking at the jobOptions - [pmg/infrastructure/mc15joboptions](https://gitlab.cern.ch/atlas-physics/pmg/infrastructure/mc15joboptions/tree/master/share)
> - `PowhegPythia8...LO_llbb_VpT` : Verbose "short" name for the dataset from which you can guess some things - but its best to always look at the production jobOptions directly.
> - `e5706_s3126_r10724_p3840` : Production and reconstruction tags - [What do all these letters and numbers mean?](https://twiki.cern.ch/twiki/bin/view/AtlasProtected/AtlasProductionGroup#FAQ). The central source for browsing the details of what these tags precisely mean is the [ATLAS Metadata Interface (AMI)](https://ami.in2p3.fr/).
>
> Unfortunately, the files contained within this container are not so verbosely named and take the form of something like `DAOD_EXOT27.17882744._000026.pool.root.1` which allows for more automated and robust record keeping, even if it is not as human-readable.
{: .callout}

To allow for reproducibility of results and compatibility among all of the participants at the bootcamp, a single one of the files from this
container has been extracted and placed on the [CERNBox](https://cernbox.cern.ch). Please download this file and place it somewhere in the
directory that you will be using for the bootcamp
<center>
<font size="10">
<a href="https://cernbox.cern.ch/index.php/s/YXbCrQkwnZuc3yU">Link to Download File</a>
</font>
</center>




> ## Rucio and the Grid
>
> As a bonus (**NOT** necessary) considering trying to use the grid to obtain your test dataset.  The grid is a very
> powerful infrastructure that you will use throughout your work.  It stores data and functions like a worldwide
> batch system for processing compute jobs.  In this case, we are only going to use it to obtain a file
> by using the [Rucio](https://twiki.cern.ch/twiki/bin/view/AtlasComputing/SoftwareTutorialGettingDatasets) interface.
> If you have things set up properly and are working on a machine (e.g. lxplus) which has the proper infrastructure, then
> this should be as easy as
>
>```bash
> rucio get --nrandom 1 mc16_13TeV.345055.PowhegPythia8EvtGen_NNPDF3_AZNLO_ZH125J_MINLO_llbb_VpT.deriv.DAOD_EXOT27.e5706_s3126_r10724_p3840
>```
> If you do this, you may not get the same file as the one in the [CERNBox](https://cernbox.cern.ch) space above, but within statistical
> uncertainties, the events are the same.
>
{: .challenge}

{% include links.md %}

