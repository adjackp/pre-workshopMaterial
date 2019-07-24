---
title: "Running the Basic Analysis"
teaching: 60
exercises: 30
questions:
- What is the basic order of operations for building/executing your program with CMake?
- How do you access the data of the DAOD within the C++ code?
objectives:
- Start up the `AnalysisBase` Docker image
- Copy the AnalysisPayload code to loop over events
- Compile the payload to produce an executable program
- Run the executable to print out jet properties
keypoints:
- The directory where your source code lives and where you build the code are _two separate directories_.
- When in doubt, you can always remove the entire contents of your `build` directory.
- Information within the (D)AOD is organized in what is called the EDM.
- Compiling/linking ATLAS code "by hand" is super long.  Thank goodness we have CMake.
- Judiciously choose when to parameterize your ignorance.  It is a powerful approach, but remember **you are a smart human**, not a robot.
---

## Start up a container from the Docker image

Working from your shell create a directory on your laptop for the work you will be doing,
called `Bootcamp` for example and enter that directory.  This directory may already
exist from the previous section and need not be named `Bootcamp`.

```bash
mkdir Bootcamp
cd Bootcamp
```

Within this directory, make another directory named `Data` and put the DAOD file you downloaded previously into this directory

For example, moving the file over using the terminal...

```bash
mkdir Data
mv <DataLocation/File.root> Data/.
```

Be sure that you are residing within the `Bootcamp` directory and then start up the
container from the `AnalysisBase` Docker image.

```bash
docker run --rm -it -v $PWD:/home/atlas/Bootcamp atlas/analysisbase:21.2.75 bash
```

This command is slightly extended from what you did previously.  It now includes this `-v $PWD:/home/atlas/Bootcamp`
option which will place the entire file structure of your current working directory within the `/home/atlas/Bootcamp` directory
of the image.  So when you immediately start the image, if you list the files in the current directory, you will see

```bash
[bash][atlas]:~ > ls
Bootcamp  release_setup.sh
[bash][atlas]:~ >
```

Again, you will learn more of these details on Thursday of the bootcamp if you are not familiar.  For now, what
this allows you to do is load the ATLAS environment as before, and then when you move into `Bootcamp`,
it is as if you were working with your local directory/file structure.  If you create or delete a file here,
it will be created or deleted on your laptop.

```bash
source release_setup.sh
cd Bootcamp
```

You should now see the `Data` directory which you created and the test DAOD file contained within it.

## Get the Analysis Code

> ## REMINDER! : This is Not a Working ATLAS Analysis!
>
> The point of this analysis "payload" is to provide a context in which to exercise and
> explore keep computing concepts.  Please appreciate that what you are going to develop
> here should **NOT** be used to perform a full-fledged analysis within ATLAS.  Although
> in principle it *could* be used for this, there are well-established frameworks (e.g. [EventLoop]()) that
> include functionality that you will eventually desire.  They have been developed to
> work efficiently to let you focus on the physics.
>
> With this in mind, have fun using this to think about physics during the bootcamp, but
> when you and your supervisor embark on a "real analysis", follow the guidance about
> the best practices found in the [ATLAS Software Tutorial](https://twiki.cern.ch/twiki/bin/view/AtlasComputing/SoftwareTutorial).
>
> <center>
> <strong>
> <font color="red">
> <font size="+3">
> This is not an example analysis from which you should build a full-fledged "real" analysis.  This should only be used in the context of this bootcamp for learning purposes.
> </font>
> </font>
> </strong>
> </center>
>
>
{: .callout}


Create a file within `Bootcamp` called `AnalysisPayload.cxx`

```bash
touch AnalysisPayload.cxx
```

Open this file with your chosen editor, copy in the code below and then save. The `inputFilePath` may have to be changed to reflect the location of the VH sample within the
container, it should be something like `/home/atlas/Bootcamp/Data/<filename>.root` but
may be slightly different depending on the naming you chose for your directories
and when you booted up the Docker image.  Whatever the case, the file must reside **somewhere** within the directory
structure that is "linked" to your Docker image via the `-v` option.  You will learn more about these details on Thursday.

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
  TString inputFilePath = "/home/atlas/Bootcamp/Data/DAOD_EXOT27.17882744._000026.pool.root.1";
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
      std::cout << "Jet : pt=" << jet->pt() << "  eta=" << jet->eta() << "  phi=" << jet->phi() << "  m=" << jet->m() << std::endl;
    }

    // counter for the number of events analyzed thus far
    count += 1;
  }

  // exit from the main function cleanly
  return 0;
}
~~~

> ## The EDM
>
> Everything in the program that you have just copied, and will soon compile, is nothing more than C++.
> If some of the syntax appears strange to you (e.g. What does `::` mean? And what does this `const` mean?) then
> it indicates you need to go back and review C++.  Please refer to the earlier portions to understand
> what the essential concepts are.
>
> However, from native C++, and using libraries such as [ROOT](https://root.cern.ch/) and [BOOST](https://www.boost.org/), ATLAS has created what is called
> the Event Data Model (EDM).  This is a flexible data structure that is tailored to accomplish the tasks that we
> care about.  In this case, it means storing and retrieving information about hadronic jets.  In the EDM,
> these appear as `xAOD::Jet` ([go look at the code yourself](https://gitlab.cern.ch/atlas/athena/tree/21.2/Event/xAOD/xAODJet))
> objects.  Later you will be using this same EDM to access auxiliary information about an object.  However, the EDM
> can do many more things and you will learn more about it as you work within ATLAS.  To learn more about it in a formal
> way you should attend the [ATLAS Software Tutorial](https://indico.cern.ch/event/772589/) where there are [dedicated
> sessions/talks devoted to the EDM](https://indico.cern.ch/event/772589/contributions/3210474/attachments/1786017/2907791/ATLAS_Event_Data_Model.pdf).
>
> The primary thing to take away here is that **the EDM is nothing more than a set of libraries written in native C++**.
>
{: .callout}

## Compiling and Linking

Now it is time to compile your code so you are able to have it carry out your analysis.  In this first example, we will perform this
build using the full `g++` command, to emphasize the fact that all that is going to be happening "under the hood" throughout the week
(e.g. when we start using CMake) can be done with a single call to `g++`.

To do this, within the `Bootcamp` directory, start by running the command that will prepare your shell environment by setting a few
paths necessary for `g++`.

~~~bash
source /opt/lcg/binutils/2.28/x86_64-slc6/setup.sh
source /opt/lcg/gcc/6.2.0binutils/x86_64-slc6/setup.sh
~~~

Now you are able to run the `g++` compiler on your `AnalysisPayload.cxx`.  Yes, its long and quite esoteric if you are not familiar with
how the ATLAS codebase is organized "under the hood".  And indeed, few people are.  This is an example of a place where it is *totally fine*
to parameterize your ignorance and let someone, or something else, do the heavy lifting.  That something will be CMake ... soon, we promise.

For now, simply copy and paste this command into your terminal.

~~~bash
/opt/lcg/gcc/6.2.0binutils/x86_64-slc6/bin/g++   -DATLAS -DHAVE_64_BITS -DHAVE_PRETTY_FUNCTION -DROOTCORE -DROOTCORE_RELEASE_SERIES=25 -DXAOD_ANALYSIS -DXAOD_STANDALONE -D__IDENTIFIER_64BIT__  -I/usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/RootCore/include  -I/usr/AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/include  -isystem /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/src/Event/xAOD/xAODEventInfo  -isystem /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/src/Control/AthContainers  -isystem /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/src/Control/AthContainersInterfaces  -isystem /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/../../../../AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/include  -isystem /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/src/Control/AthLinksSA  -isystem /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/src/Control/xAODRootAccessInterfaces  -isystem /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/src/Control/CxxUtils  -isystem /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/src/Event/xAOD/xAODCore  -isystem /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/src/Control/xAODRootAccess  -isystem /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/src/Event/xAOD/xAODEventFormat  -isystem /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/src/Event/xAOD/xAODJet  -isystem /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/src/Event/xAOD/xAODBase  -isystem /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/src/Event/xAOD/xAODBTagging  -isystem /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/src/Event/xAOD/xAODTracking  -isystem /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/../../../../AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/include/eigen3  -isystem /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/src/DetectorDescription/GeoPrimitives  -isystem /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/src/Event/EventPrimitives  -isystem /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/src/Event/xAOD/xAODMuon  -isystem /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/src/Event/xAOD/xAODCaloEvent  -isystem /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/src/Calorimeter/CaloGeoHelpers  -isystem /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/src/Event/xAOD/xAODPrimitives  -isystem /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/src/MuonSpectrometer/MuonIdHelpers  -isystem /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/src/Event/xAOD/xAODPFlow  -isystem /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/src/Event/xAOD/xAODTrigger   -DNDEBUG -O2 -g -Wall -Wno-long-long -Wno-deprecated -Wno-unused-local-typedefs -Wwrite-strings -Wpointer-arith  -Woverloaded-virtual -Wextra -Werror=return-type -pedantic   -pthread -std=c++14  -o AnalysisPayload.cxx.o  -c /home/atlas/Bootcamp/AnalysisPayload.cxx
~~~

And now link the multiple object files into a single executable.

~~~bash
/opt/lcg/gcc/6.2.0binutils/x86_64-slc6/bin/g++  -DNDEBUG -O2 -g -Wall -Wno-long-long -Wno-deprecated -Wno-unused-local-typedefs -Wwrite-strings -Wpointer-arith -Woverloaded-virtual -Wextra -Werror=return-type -pedantic   -Wl,--as-needed -Wl,--hash-style=both AnalysisPayload.cxx.o  -o AnalysisPayload -Wl,-rpath,/usr/AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib:/usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib:/usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/../../../../AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib  /usr/AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libCore.so /usr/AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libImt.so /usr/AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libRIO.so /usr/AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libNet.so /usr/AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libHist.so /usr/AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libGraf.so /usr/AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libGraf3d.so /usr/AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libGpad.so /usr/AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libROOTDataFrame.so /usr/AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libTree.so /usr/AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libTreePlayer.so /usr/AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libRint.so /usr/AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libPostscript.so /usr/AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libMatrix.so /usr/AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libPhysics.so /usr/AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libMathCore.so /usr/AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libThread.so /usr/AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libMultiProc.so /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libxAODEventInfo.so /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libxAODRootAccess.so /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libxAODJet.so /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libxAODBTagging.so /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/../../../../AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libNet.so  /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/../../../../AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libMathCore.so  /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libxAODEventFormat.so /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libxAODMuon.so  /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libxAODPrimitives.so /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libMuonIdHelpersLib.so  /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libxAODPFlow.so  /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/../../../../AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libGenVector.so  /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libxAODTracking.so  /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libxAODTrigger.so  /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libxAODCaloEvent.so  /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libxAODCore.so  /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/../../../../AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libHist.so  /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/../../../../AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libTree.so  /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/../../../../AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libRIO.so  /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libxAODBase.so  /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libAthContainers.so  /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libAthLinks.so  /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libxAODRootAccessInterfaces.so  /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/../../../../AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libCore.so  /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/../../../../AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libPhysics.so  /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libCaloGeoHelpers.so  /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libCxxUtils.so  -pthread /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/../../../../AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libboost_program_options.so  /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/../../../../AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libboost_regex.so  /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/../../../../AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libboost_filesystem.so  /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/../../../../AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libboost_thread.so  /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/../../../../AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libboost_system.so  /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/../../../../AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libboost_timer.so  /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/../../../../AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libboost_chrono.so  /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/../../../../AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libboost_date_time.so  /usr/AnalysisBase/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/../../../../AnalysisBaseExternals/21.2.75/InstallArea/x86_64-slc6-gcc62-opt/lib/libboost_atomic.so
~~~

an executable should have been created within your directory called `AnalysisPayload`

> ## Hints
>
> - If you are getting errors during compilation, it may very well be that you
>   did not properly copy and paste all of the code in the previous portion of the exercise.
>   Go back and verify that your source code is not missing something.  Though this should be
>   evident from the error message it displays.
>
> - You may be getting permission denied messages, this is likely due to your root-user privilages
>   with Docker have not been set up properly.
>
{: .callout}


## Run executable

Alright, it is time to run this executable and if everything worked out fine, it should
print some output after looping over the events in the test DAOD file.  Hopefully you have
read the code and made a guess at what it will print out.  In this case, we are printing out
the basic jet kinematics `(pT, eta, phi, m)`.

Within the `Bootcamp` directory, run the `AnalysisPayload` executable.

~~~bash
./AnalysisPayload
~~~

If you have followed the instructions (and hopefully with no unexpected errors) you should get an output that looks something like this

> ...
>
> Processing run # 284500, event # 4940346
>
> Jet : pt=58884.9  eta=-0.728219  phi=-3.1079585 m=52.21
>
> Jet : pt=30194.4  eta=0.952566  phi=-1.2340236  m=86.78
>
> Jet : pt=25141.3  eta=-0.896951  phi=1.8794845  m=27.64
>
> ...
>
{: .output}

**Warning**: A common error here is that the path to the xAOD file is incorrect and your program cannot find it. Be careful to change the `inputFilePath` to reflect the path to the xAOD file on your system. 

As you can see, it is actually possible to compile and link your analysis code manually
to produce an executable that runs over your xAODs. However, it is fair to say that the
commands are ridiculously long and are prone to error. This example only accesses a jet
container and print some properties but it is already very complicated, imagine the commands
needed to run a full analysis framework.

This is why ATLAS software uses things like CMake and GNU Make which you will learn
more about this week but for now we will quickly convert our setup to use CMake to
compile in the next section. Go for it!

> ## Going Deeper
>
> Have a read through the code and make sure you have an idea of what it is trying to do? Getting the code to run is
> half the battle, but it is critical that you read through the code and understand what is happening.
>
> **"But how many details do I need to understand?"**
>
> Well, do you really need to know the details of what is happening in memory when `event.retrieve(jets, "AntiKt4EMTopoJets");` is executed?
> Probably, all you need to know is that it copies a collection from the `EventStore` to the local `xAOD::JetContainer` that
> you have called jets.  This idea of "_all you need to know_" is referred to some as "parameterizing your ignorance".  It
> will be important to get a sense where you, as an individual and for your specific research goal, fall on the spectrum
> of understanding all the details.
>
> However, do **NOT** get in the habit of just running code that someone gives you.  This is not research, and this is why we have computers.
>
{: .callout}

{% include links.md %}

