---
title: "Your First Plot"
teaching: 30
exercises: 60
questions:
- What does the invariant mass spectrum of the two leading jets look like?
- What are two of the most commonly used "tools" in particle physics analysis?
objectives:
- Produce histograms of event and jet properties.
- Get practice implementing basic C++ ROOT functionality.
keypoints:
- The m(jj) spectrum is peaked near the Higgs mass of ~125 GeV.
- There seems to be a long tail on the high mass side.
- Important Tool #1 : We examine data via a "projection" onto histograms (`TH1` in ROOT).
- Important Tool #2 : Much of what we study in analysis is the behavior of four-momenta (`TLorentzVector` in ROOT).
---


## Plot Jet Properties

As an exercise, before the start of the bootcamp, and as a means by which to get familiar with
the workflow while also thinking about some physics, try to complete the following exercise.
The output of this exercise will be something that will evolve during the bootcamp and will
not be left as a one-and-done exercise now.  So if you are struggling to make these two plots,
**ASK SOMEONE**!

> ## Exercise
>
> Similar to how you would in an analysis, fill histograms of
> - The number of jets in the event that pass the
> - and the invariant mass of the two "leading jets", by which we mean the jets with the largest transverse momentum among the collection of jets
> and then save the output to a ROOT file and draw the resulting histograms (be sure to label your axes!).
>
> If you have done things properly, then you should be able to reproduce the following results.
>
> <center>
> <img src="../fig/spectrum_njets.png" style="width:500px;height:400px">
> <img src="../fig/spectrum_mjj.png" style="width:500px;height:400px">
> </center>
>
> You are not meant to struggle with this exercise.  If you are having troubles, please ask on the Mattermost channel.  However
> if you find it more useful, a sample solution is included below. However please try your best to complete the exercise
> before attending the workshop.  It will be assumed on Monday that everyone has a working version of this code locally on their
> computer.
>
> > ## Solution
> >
> > ~~~cpp
> > // stdlib functionality
> > #include <iostream>
> > // ROOT functionality
> > #include <TFile.h>
> > #include <TH1D.h>
> > // ATLAS EDM functionality
> > #include "xAODRootAccess/Init.h"
> > #include "xAODRootAccess/TEvent.h"
> > #include "xAODEventInfo/EventInfo.h"
> > #include "xAODJet/JetContainer.h"
> >
> > int main() {
> >
> >   // initialize the xAOD EDM
> >   xAOD::Init();
> >
> >   // open the input file
> >   TString inputFilePath = "/home/atlas/Bootcamp/Data/mc16_13TeV.345055.PowhegPythia8EvtGen_NNPDF3_AZNLO_ZH125J_MINLO_llbb_VpT.deriv.DAOD_EXOT27.e5706_s3126_r10724_p3840/DAOD_EXOT27.17882744._000026.pool.root.1";
> >   xAOD::TEvent event;
> >   std::unique_ptr< TFile > iFile ( TFile::Open(inputFilePath, "READ") );
> >   event.readFrom( iFile.get() );
> >
> >   // make histograms for storage
> >   TH1D *h_njets_raw = new TH1D("h_njets_raw","",20,0,20);
> >
> >   TH1D *h_mjj_raw = new TH1D("h_mjj_raw","",100,0,500);
> >
> >   // for counting events
> >   unsigned count = 0;
> >
> >   // get the number of events in the file to loop over
> >   const Long64_t numEntries = event.getEntries();
> >
> >   // primary event loop
> >   for ( Long64_t i=0; i<numEntries; ++i ) {
> >
> >     // Load the event
> >     event.getEntry( i );
> >
> >     // Load xAOD::EventInfo and print the event info
> >     const xAOD::EventInfo * ei = nullptr;
> >     event.retrieve( ei, "EventInfo" );
> >     std::cout << "Processing run # " << ei->runNumber() << ", event # " << ei->eventNumber() << std::endl;
> >
> >     // retrieve the jet container from the event store
> >     const xAOD::JetContainer* jets = nullptr;
> >     event.retrieve(jets, "AntiKt4EMTopoJets");
> >
> >     // make temporary vector of jets for those which pass selection
> >     std::vector<xAOD::Jet> jets_raw;
> >
> >     // loop through all of the jets and make selections with the helper
> >     for(const xAOD::Jet* jet : *jets) {
> >       // print the kinematics of each jet in the event
> >       std::cout << "Jet : pt=" << jet->pt() << "  eta=" << jet->eta() << "  phi=" << jet->phi() << "  m=" << jet->m() << std::endl;
> >
> >       jets_raw.push_back(*jet);
> >
> >     }
> >
> >     // fill the analysis histograms accordingly
> >     h_njets_raw->Fill( jets_raw.size() );
> >
> >     if( jets_raw.size()>=2 ){
> >       h_mjj_raw->Fill( (jets_raw.at(0).p4()+jets_raw.at(1).p4()).M()/1000. );
> >     }
> >
> >     // counter for the number of events analyzed thus far
> >     count += 1;
> >   }
> >
> >   // open TFile to store the analysis histogram output
> >   TFile *fout = new TFile("myOutputFile.root","RECREATE");
> >
> >   h_njets_raw->Write();
> >
> >   h_mjj_raw->Write();
> >
> >   fout->Close();
> >
> >   // exit from the main function cleanly
> >   return 0;
> > }
> > ~~~
> >
> {: .solution}
{: .challenge}

> ## Hint
>
> If you do not want to just look at the solution, and are struggling, then here are some hints :
> - You will probably need to use these two libraries in [ROOT](https://root.cern.ch/) :
>   - [TH1D](https://root.cern.ch/doc/master/classTH1D.html) : For making histograms. Google if you need examples on how to use this.
>   - [TFile](https://root.cern.ch/doc/master/classTFile.html) : For saving the histograms so you can access/draw them later.
>   - [TLorentzVector](https://root.cern.ch/doc/master/classTLorentzVector.html) : Helpful when doing 4-vector math.
> - Remember you will need to include the header (`#include`) of these libraries, like any external C++ functionality.
> - Don't forget to `TFile::Close` your output file.
>
{: .callout}


{% include links.md %}

