---
title: "Analysis Setup (Part 1)"
teaching: 15
exercises: 15
questions:
- "Key question (FIXME)"
objectives:
- "Start up the AnalysisBase Docker image"
- "Copy the code to loop over events"
- "Compile the payload to produce an executable"
- "Run the executable and write out jet properties"
keypoints:
- "First key point. Brief Answer to questions. (FIXME)"
---

> ## Important
>
> If you have not completed the Setup section of the Pre-Workshop material please go do this now before going any further.
>
{: .prereq}

## Start up a container from the Docker image

Working from your shell create a directory on your laptop for the work you will be doing, called `Bootcamp` for example and enter that directory

~~~bash
mkdir Bootcamp
cd Bootcamp
~~~

Within this directory please make another directory named `Data` and put the VH file you downloaded earlier into this directory

For example, moving the file over using the terminal... 

~~~bash
mkdir Data
mv <DataLocation/File.root> Data/.
~~~

You can begin by starting up the container from the `AnalysisBase` Docker image using the command...

~~~
docker run --rm -it -v $PWD:/home/atlas/Bootcamp atlas/analysisbase:21.2.75 bash
cd Bootcamp
~~~
{: .language-bash}

this which will place the entire file structure within the `/home/atlas/Bootcamp` directory
of the image. 

You should have also access to the DxAOD VH sample you just moved over. 

## Copy code

Create a file within `Bootcamp` called `AnalysisPayload.cxx`

~~~bash
touch AnalysisPayload.cxx
~~~

Open this file with your chosen editor, copy in the code below and then save.

~~~cpp
// stdlib functionality
#include <iostream>
// ROOT functionality
#include <TFile.h>
// ATLAS EDM functionality
#include "xAODRootAccess/Init.h"
#include "xAODRootAccess/TEvent.h"
#include "xAODEventInfo/EventInfo.h"
#include "xAODJet/JetContainer.h"

int main() {

  // initialize the xAOD EDM
  xAOD::Init();

  // open the input file
  TString inputFilePath = "/home/atlas/Bootcamp/Data/mc16_13TeV.345055.PowhegPythia8EvtGen_NNPDF3_AZNLO_ZH125J_MINLO_llbb_VpT.deriv.DAOD_EXOT27.e5706_s3126_r10724_p3840/DAOD_EXOT27.17882744._000026.pool.root.1";
  xAOD::TEvent event;
  std::unique_ptr< TFile > iFile ( TFile::Open(inputFilePath, "READ") );
  event.readFrom( iFile.get() );

  // for counting events
  unsigned count = 0;

  // get the number of events in the file to loop over
  const Long64_t numEntries = event.getEntries();

  // primary event loop
  for ( Long64_t i=0; i<numEntries; ++i ) {

    // Load the event
    event.getEntry( i );

    // Load xAOD::EventInfo and print the event info
    const xAOD::EventInfo * ei = nullptr;
    event.retrieve( ei, "EventInfo" );
    std::cout << "Processing run # " << ei->runNumber() << ", event # " << ei->eventNumber() << std::endl;

    // retrieve the jet container from the event store
    const xAOD::JetContainer* jets = nullptr;
    event.retrieve(jets, "AntiKt4EMTopoJets");

    // loop through all of the jets and make selections with the helper
    for(const xAOD::Jet* jet : *jets) {
      // print the kinematics of each jet in the event
      std::cout << "Jet : " << jet->pt() << jet->eta() << jet->phi() << jet->m() << std::endl;
    }

    // counter for the number of events analyzed thus far
    count += 1;
  }

  // exit from the main function cleanly
  return 0;
}
~~~

The `inputFilePath` can be changed to reflect the location of the VH sample within the container, it should be something like `/home/atlas/Bootcamp/Data/<filename>.root`.

Have a read through the code and make sure you have an idea of what it is trying to do.

## Compiling and Linking

Now it is time to compile your code so you are able to run. 

Within the `Bootcamp` directory you can work through the following exercise to compile and link your program to produce an executable. 

> ## Exercise
>
> Run the commands below to prepare your environment to be able to compile
> 
> ~~~bash
> source /opt/lcg/binutils/2.28/x86_64-slc6/setup.sh
> source /opt/lcg/gcc/6.2.0binutils/x86_64-slc6/setup.sh
> ~~~
> 
> Now you are able to run the `g++` compiler on your `AnalysisPayload.cxx`
>
> ~~~bash
> /opt/lcg/gcc/6.2.0binutils/x86_64-slc6/bin/g++   -DATLAS -DHAVE_64_BITS -DHAVE_PRETTY_FUNCTION -DROOTCORE -DROOTCORE_RELEASE_SERIES=25 -DXAOD_ANALYSIS -DXAOD_STANDALONE -D__IDENTIFIER_64BIT__  -I/usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/RootCore/include  -I/usr/AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/include  -isystem /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/src/Event/xAOD/xAODEventInfo  -isystem /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/src/Control/AthContainers  -isystem /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/src/Control/AthContainersInterfaces  -isystem /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/../../../../AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/include  -isystem /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/src/Control/AthLinksSA  -isystem /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/src/Control/xAODRootAccessInterfaces  -isystem /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/src/Control/CxxUtils  -isystem /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/src/Event/xAOD/xAODCore  -isystem /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/src/Control/xAODRootAccess  -isystem /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/src/Event/xAOD/xAODEventFormat  -isystem /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/src/Event/xAOD/xAODJet  -isystem /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/src/Event/xAOD/xAODBase  -isystem /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/src/Event/xAOD/xAODBTagging  -isystem /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/src/Event/xAOD/xAODTracking  -isystem /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/../../../../AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/include/eigen3  -isystem /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/src/DetectorDescription/GeoPrimitives  -isystem /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/src/Event/EventPrimitives  -isystem /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/src/Event/xAOD/xAODMuon  -isystem /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/src/Event/xAOD/xAODCaloEvent  -isystem /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/src/Calorimeter/CaloGeoHelpers  -isystem /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/src/Event/xAOD/xAODPrimitives  -isystem /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/src/MuonSpectrometer/MuonIdHelpers  -isystem /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/src/Event/xAOD/xAODPFlow  -isystem /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/src/Event/xAOD/xAODTrigger   -DNDEBUG -O2 -g -Wall -Wno-long-long -Wno-deprecated -Wno-unused-local-typedefs -Wwrite-strings -Wpointer-arith  -Woverloaded-virtual -Wextra -Werror=return-type -pedantic   -pthread -std=c++14  -o AnalysisPayload.cxx.o  -c /home/atlas/Bootcamp/AnalysisPayload.cxx
> ~~~
>
> And finally link the multiple object files into a single executable...
>
> ~~~bash
> /opt/lcg/gcc/6.2.0binutils/x86_64-slc6/bin/g++  -DNDEBUG -O2 -g -Wall -Wno-long-long -Wno-deprecated -Wno-unused-local-typedefs -Wwrite-strings -Wpointer-arith -Woverloaded-virtual -Wextra -Werror=return-type -pedantic   -Wl,--as-needed -Wl,--hash-style=both AnalysisPayload.cxx.o  -o AnalysisPayload -Wl,-rpath,/usr/AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib:/usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib:/usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/../../../../AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib  /usr/AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libCore.so /usr/AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libImt.so /usr/AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libRIO.so /usr/AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libNet.so /usr/AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libHist.so /usr/AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libGraf.so /usr/AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libGraf3d.so /usr/AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libGpad.so /usr/AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libROOTDataFrame.so /usr/AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libTree.so /usr/AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libTreePlayer.so /usr/AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libRint.so /usr/AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libPostscript.so /usr/AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libMatrix.so /usr/AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libPhysics.so /usr/AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libMathCore.so /usr/AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libThread.so /usr/AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libMultiProc.so /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libxAODEventInfo.so /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libxAODRootAccess.so /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libxAODJet.so /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libxAODBTagging.so /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/../../../../AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libNet.so  /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/../../../../AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libMathCore.so  /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libxAODEventFormat.so /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libxAODMuon.so  /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libxAODPrimitives.so /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libMuonIdHelpersLib.so  /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libxAODPFlow.so  /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/../../../../AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libGenVector.so  /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libxAODTracking.so  /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libxAODTrigger.so  /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libxAODCaloEvent.so  /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libxAODCore.so  /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/../../../../AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libHist.so  /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/../../../../AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libTree.so  /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/../../../../AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libRIO.so  /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libxAODBase.so  /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libAthContainers.so  /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libAthLinks.so  /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libxAODRootAccessInterfaces.so  /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/../../../../AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libCore.so  /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/../../../../AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libPhysics.so  /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libCaloGeoHelpers.so  /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libCxxUtils.so  -pthread /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/../../../../AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libboost_program_options.so  /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/../../../../AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libboost_regex.so  /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/../../../../AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libboost_filesystem.so  /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/../../../../AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libboost_thread.so  /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/../../../../AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libboost_system.so  /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/../../../../AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libboost_timer.so  /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/../../../../AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libboost_chrono.so  /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/../../../../AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libboost_date_time.so  /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/../../../../AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libboost_atomic.so
> ~~~
>
> an executable should have been created within your directory called `AnalysisPayload`
>
> > ## Hints
> > 
> > * If you are getting errors it can sometimes help to start again with a fresh file and session of your container.
> > * You may be getting permission denied messages, this is because of your root-user privilages with Docker have not been set up properly.
> >
> {: .callout}
{: .challenge}

## Run executable

It is time to run this executable and if everything worked out fine, it should print output after looping over the events in the VH xAOD ROOT file. 

Within the `Bootcamp` directory where your AnalysisPayload executable is, run the command.. 

~~~bash
./AnalysisPayload
~~~

If you have followed the instructions (and hopefully with no unexpected errors) you should get an output that looks something like this 

> ...
>
> Processing run # 284500, event # 4940346
>
> Jet : 58884.9-0.728219-3.107958552.21
>
> Jet : 30194.40.952566-1.234023686.78
>
> Jet : 25141.3-0.8969511.879484527.64
>
> Jet : 23075.2-0.167431-2.139482755.09
>
> Jet : 20312-1.11594-2.714523810.94
>
> Jet : 15962.82.02741-0.01260682330.59
>
> Jet : 9609.71-4.31337-0.9151630.00368174
>
> Jet : 6887.7-1.53393.021861617.57
>
> Jet : 6706.07-2.832740.4439791139.5
>
> Jet : 5659.63-1.97297-0.4942081476.55
>
> Jet : 5460.870.321482-2.522641408.24
>
> Jet : 5456.12.092550.9007921405.56
>
> Jet : 5441.143.579441.80799519.316
>
> Jet : 5086.05-2.80756-1.118850.00024513
>
> ...
>
{: .output}

> ## Takeaway Point
> 
> As you can see, it is actually possible to compile and link your analysis code manually to produce an executable that runs over your xAODs. However, it is fair to say that the commands are ridiculously long and are prone to error. This example only accesses a jet container and print some properties but it is already very complicated, imagine the commands needed to run a full analysis framework. 
>
> This is why ATLAS software uses things like CMake and GNU Make which you will learn more about this week but for now we will quickly convert our setup to use CMake to compile in the next part.
{: .callout}


{% include links.md %}
`
