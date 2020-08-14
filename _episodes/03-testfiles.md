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
- The input file container name gives you a lot of nice information about how the file was produced.
- You can inspect a file using native ROOT, and looking at `CollectionTree` or using the `checkxAOD.py` executable within the release.
---

## ATLAS Input Files

The ATLAS experiment uses an "Analysis Object Data" (AOD) file to store the event information which is the result
of a long chain of data taking or simulation and reconstruction.  In fact, this file format
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
directory that you will be using for the bootcamp. It is quite large so don't worry if it takes some time (2.7GB).
<center>
<font size="10">
<a href="https://cernbox.cern.ch/index.php/s/YXbCrQkwnZuc3yU">Link to Download File</a>
</font>
</center>

> ## What is an ATLAS dataset?
> - **A set of files** : It is not possible to put all the data from one run into one file. ATLAS uses aggregations of files containing events. These aggregations are called datasets.
>
> - **A container of datasets** : The distribution of ATLAS data introduces some additional complications. All the events for one particular set of simulation conditions is not necessarily produced all on one site, or all from one production task. Typically the first task will only produce a few events, and when everything works, more events will be produced by another task or tasks. Some datasets are the result of more than 20 succesful tasks. The set of files produced by one production task is known wirthin ATLAS as a "tid dataset" because they are named with a suffix "_tidnnnnnn" where nnnnnn is the task number. A dataset which is useful for physics contains in general several tid datasets.  The distributed data management manipulates these datasets using a concept of containers.  The container name is the physics dataset + a slash suffix.
> The datasets which AMI catalogues for ProdDB output are the logical equivalent of the DDM container datasets, but AMI does not add a slash as the last character of every name.
>
> - **Physics Containers** : Data Preparation and Group production coordination use an interface in AMI to place datasets and dataset containers in "super" containers, based on Data Quality data (Good Run Lists), so that user analysis jobs can run over all of them without having to make their own selections. At present ATLAS DDM does not permit a second level of hierarchy. The AMI interface for making physics containers simulates a second level of hierarchy.
{: .callout}

> ## Rucio and the Grid
>
> As a bonus (**NOT** necessary) considering trying to use the grid to obtain your test dataset.  The grid is a very
> powerful infrastructure that you will use throughout your work.  It stores data and functions like a worldwide
> batch system for processing compute jobs.  In this case, we are only going to use it to obtain a file
> by using the [Rucio](https://twiki.cern.ch/twiki/bin/view/AtlasComputing/SoftwareTutorialGettingDatasets) interface.
> This will require that you set up your [Grid Certificate](https://twiki.cern.ch/twiki/bin/view/AtlasComputing/SoftwareTutorialGettingDatasets)
> but if you do this properly (Don't be afraid to ask for help if you haven't done this yet!) and are working on a machine (e.g. lxplus) which has the proper infrastructure, then
> this should be as easy as.
>
>```bash
> rucio get --nrandom 1 mc16_13TeV.345055.PowhegPythia8EvtGen_NNPDF3_AZNLO_ZH125J_MINLO_llbb_VpT.deriv.DAOD_EXOT27.e5706_s3126_r10724_p3840
>```
> If you do this, you may not get the same file as the one in the [CERNBox](https://cernbox.cern.ch) space above, but within statistical
> uncertainties, the events are the same.
>
{: .challenge}

## Inspecting the File
Now that you have the DAOD file, let's inspect it to see what it contains.  Recall that the **x** in the xAOD
file format means that it is fully viewable in ROOT, both in a `TBrowser` and through the command line.  However
there are also utilities contained within the ATLAS release that allow you to inspect the file in a more
rigorous way.  We will see how to do both here.

### Manually
Booting up the image like so

```bash
docker run --rm -it -v $PWD:/home/atlas/Bootcamp atlas/analysisbase:21.2.125 bash
```

This command is slightly extended from what you did previously.  It now includes this `-v $PWD:/home/atlas/Bootcamp`
option which will place the entire file structure of your current working directory within the `/home/atlas/Bootcamp` directory
of the image. Therefore you should have access to the xAOD file you just downloaded.


Open the file in the ROOT interpreter (**NOTE** : You may need to use the `-b` option
here if your `DISPLAY` is not configured.)

~~~bash
root -b DAOD_EXOT27.17882744._000026.pool.root.1
~~~

~~~
   ------------------------------------------------------------
  | Welcome to ROOT 6.18/04                  https://root.cern |
  |                               (c) 1995-2019, The ROOT Team |
  | Built for linuxx8664gcc on Sep 11 2019, 15:38:23           |
  | From tags/v6-18-04@v6-18-04                                |
  | Try '.help', '.demo', '.license', '.credits', '.quit'/'.q' |
   ------------------------------------------------------------

root [0] 
Attaching file DAOD_EXOT27.17882744._000026.pool.root.1 as _file0...
...
...
...
Warning in <TClass::Init>: no dictionary for class TriggerInfo_p2::StreamTag_p2 is available
Warning in <TClass::Init>: no dictionary for class EventStreamInfo_p3 is available
(TFile *) 0x32b99e0
root [1]
~~~
{: .output}

Now, inspect the contents of the file, in which you should see a `TTree` called `CollectionTree`.
This is the standard place where nearly all of the information that you will care about is
stored.

~~~bash
root [1] _file0->ls()
~~~

~~~
TFile**		DAOD_EXOT27.17882744._000026.pool.root.1
 TFile*		DAOD_EXOT27.17882744._000026.pool.root.1
  KEY: TTree	MetaData;2	MetaData
  ...
  ...
  ...
  KEY: TTree	##Links;1	##Links
  KEY: TTree	CollectionTree;1	CollectionTree
~~~
{: .output}

Now, finally, since you can see the `CollectionTree` is nothing more than a `TTree`, print
its contents like you would for any `TTree`.

~~~bash
root [2] CollectionTree->Print()
~~~

~~~
******************************************************************************
*Tree    :CollectionTree: CollectionTree                                         *
*Entries :    57342 : Total =     14729418877 bytes  File  Size = 2836779765 *
*        :          : Tree compression factor =   5.20                       *
******************************************************************************
*Br    0 :McEventInfo : PileUpEventInfo_p5                                   *
*Entries :    57342 : Total  Size=  100931270 bytes  File Size  =   46000729 *
*Baskets :      273 : Basket Size=     714240 bytes  Compression=   2.19     *
*............................................................................*
*Br    1 :HLT_xAOD__TrigMissingETContainer_TrigEFMissingET : DataVector<xAOD:*
*         | :TrigMissingET_v1>                                               *
*Entries :    57342 : Total  Size=    1187254 bytes  File Size  =     138993 *
*Baskets :      261 : Basket Size=      14848 bytes  Compression=   8.50     *
  ...
  ...
  ...
*Br 1779 :TrigMatch_HLT_2g25_loose_g15_looseAuxDyn.TrigMatchedObjects :      *
*         | vector<vector<ElementLink<DataVector<xAOD::IParticle> > > >      *
*Entries :    57342 : Total  Size=     846049 bytes  File Size  =     156673 *
*Baskets :      252 : Basket Size=       8192 bytes  Compression=   5.36     *
*............................................................................*
*Br 1780 :TrigMatch_HLT_3g20_looseAuxDyn.TrigMatchedObjects :                *
*         | vector<vector<ElementLink<DataVector<xAOD::IParticle> > > >      *
*Entries :    57342 : Total  Size=     843489 bytes  File Size  =     154151 *
*Baskets :      252 : Basket Size=       8192 bytes  Compression=   5.44     *
*............................................................................*
~~~
{: .output}

OOF-DA!  Now that is a lot of branches and that is a lot of information to look at.
How much space do all of these things take.  Why is this file 2.7 Gb in size?
How can we easily find out this information?  For that, we turn to the set
of scripts that are packaged with the release.

### With the Fancy ATLAS Release Scripts

An alternative way to view the contents of an xAOD file is to use some of the utilities
that come available with the release.  These are nothing more than scripts that you could
write yourself, but carry out common tasks that many people use frequently.  In this case
we will be using the `checkxAOD.py` executable which is contained within the the `bin`
directory of the image `/usr/AnalysisBase/21.2.125/InstallArea/x86_64-centos7-gcc8-opt/bin`.
However, many more executables exist with useful utilities, and for those, we would refer you
to attend the [ATLAS Software Tutorial](https://twiki.cern.ch/twiki/bin/view/AtlasComputing/SoftwareTutorial).

For now, just start by booting up the image and setting up the release (you should be
getting familiar with doing this and it may be a good idea to create an [alias](https://www.tutorialspoint.com/unix_commands/alias)
to allow yourself to not have to copy and paste each time.).  

Once you are inside your image and you have setup your release, the `checkxAOD.py` executable should now be available to you. Execute the `checkxAOD.py` script with the path to your xAOD file as the first argument.

~~~bash
checkxAOD.py Data/DAOD_EXOT27.17882744._000026.pool.root.1
~~~

This will show a considerable amount of detailed information about the contents of the xAOD, in terms
of the different types of data structures and containers that are within it, as well as the size
of each of the bits of its content.

~~~
========================================================================================================================
 File: Data/DAOD_EXOT27.17882744._000026.pool.root.1
------------------------------------------------------------------------------------------------------------------------
    Memory size        Disk Size       Size/Event  Compression Entries  Name (Type)
------------------------------------------------------------------------------------------------------------------------
       920.69 kB        141.58 kB     0.00 kB/event    6.50     57342   TrigConfKeys (xAOD::TrigConfKeys_v1) [Trig]
      1145.96 kB        214.84 kB     0.00 kB/event    5.33     57342   AntiKt4LCTopoJets (DataVector<xAOD::Jet_v1>) [Jet]
      4206.56 kB        319.32 kB     0.01 kB/event   13.17     57342   TrigMatch_HLT_2g22_tight_L1EM7_EMPTY (DataVector<xAOD::TrigComposite_v1>) [Trig]
      ...
      ...
      ...
      1294627.32 kB     208848.41 kB     3.64 kB/event    6.20     57342   TrigNavigation (xAOD::TrigNavigation_v1) [Trig]
      2081659.78 kB     525454.90 kB     9.16 kB/event    3.96     57342   TruthParticles (DataVector<xAOD::TruthParticle_v1>) [Truth]
------------------------------------------------------------------------------------------------------------------------
  14369442.73 kB    2764852.98 kB    48.22 kB/event                     Total
========================================================================================================================
~~~
{: .output}

This can be a very complementary view of the file compared to that which inspects the detailed
contents of all of the branches.

**COOL**! Now you have your test file _and_ you know how to inspect it.  But how can we actually
analyze it and begin to think about physics?

{% include links.md %}

