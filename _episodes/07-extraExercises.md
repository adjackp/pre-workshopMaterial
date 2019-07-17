---
title: "Extra Exercises"
teaching: 0
exercises: 100
questions:
- Did you find the previous portions of the pre-work easy? Care to take it a next step?
objectives:
- Think more about the experimental aspects of the physics that we are investigating. How to the charged leptons come into play?
- Make our code adhere to some ATLAS coding standard by checking for errors.
- Examine what we find if we execute our analysis on some background samples.
keypoints:
- Writing good code requires care, especially when it gets complicated.
- Using configuration files allows us to compile our code less frequently.
- The search for VH(bb) is hard!
---

## Additional Exercises

If you found the previous exercises and setup too easy then here is a list of other tasks to try if you get time before the computing bootcamp.

> ## Exercise 1
>
> Adapt the `AnalysisPayload.cxx` code so that it only considers jets in the event that pass some minimum `jet->pt()` requirement
> of 50 GeV, and are within `std::abs(jet->eta())<2.5`.  If this is easy, then write a configuration file and a parser that will
> allow you to configure these selection criteria. While you are at it, make this configuration file read in the path to the
> input file that you want to analyze.
>
{: .challenge}

> ## Exercise 2
>
> Put in success checks throughout the `AnalysisPayload.cxx` code where they are needed to avoid errors and exceptions.
> The details of what these are and how to use them can be found here - [Return Codes (a.k.a. Status Codes)](https://atlassoftwaredocs.web.cern.ch/ABtutorial/basic_status_code/). There should be an unchecked status code counter
> at the end of your output as a hint of where they might be.
>
> > ...
> >
> > Jet : 19045.4-0.4281811.751874285.59
> >
> > Jet : 15588.91.74852-1.104962493.13
> >
> > xAOD::TFileAccessTracer   INFO    Sending file access statistics to http://rucio-lb-prod.cern.ch:18762/traces/
> >
> > Warning in <xAOD::TReturnCode>:
> >
> > Warning in <xAOD::TReturnCode>: Unchecked return codes encountered during the job
> >
> > Warning in <xAOD::TReturnCode>: Number of unchecked successes: **<font color="red">114686</font>**
> >
> > Warning in <xAOD::TReturnCode>: To fail on an unchecked code, call xAOD::TReturnCode::enableFailure() at the job's start
> >
> > Warning in <xAOD::TReturnCode>:
> >
> > ...
> {: .output}
>
{: .challenge}

> ## Exercise 3
>
> Because this is a ZH process, where the Z boson decays to two charged leptons, there are not only `xAOD::Jets` in the event.
> If you inspect your input DAOD file, you will also fine that there is are `xAOD::Electrons` and `xAOD::Muons` containers.  Try
> to retrieve these in a similar way as the jets (but this time calling them `xAOD::Electrons` and `xAOD::Muons`).  You may need to
> refer to the EDM to learn about the [`xAOD::Egamma`](https://gitlab.cern.ch/atlas/athena/tree/21.2/Event/xAOD/xAODEgamma) and
> [`xAOD::Muon`](https://gitlab.cern.ch/atlas/athena/tree/21.2/Event/xAOD/xAODMuon) classes.
>
> Try printing out the number of electrons and muons in a given event.  What do you notice? Does it make sense?
>
> Now, if all of this makes sense and is relatively easy, here comes the kicker.  Experimentally *ELECTRONS ARE JETS*!!!  They
> show up in the calorimeter as blobs of energy.  Can you confirm this by studying the spatial/angular (eta,phi) separation
> between electrons and jets in a given event.  In the case that you have two muons and not two electrons, the situation even gets
> and you should be sure to bring it up at the bootcamp.
>
{: .challenge}

> ## Exercise 4
>
> Get another file from the grid and run over that instead. As a suggestion, try running over one of the samples which is a
> very challenging (i.e. "irreducible") background in the ZH search.  This is the standard model production of a Z boson which
> decays to two electrons produced in association with b quarks (see that `BFilter` in the name?).
> ```
> mc16_13TeV.364122.Sherpa_221_NNPDF30NNLO_Zee_MAXHTPTV140_280_BFilter.deriv.DAOD_EXOT27.e5299_s3126_r9364_p3840
> ```
>
> Can you find the `jobOptions` that were used to produce this DAOD?
>
{: .challenge}

{% include links.md %}

