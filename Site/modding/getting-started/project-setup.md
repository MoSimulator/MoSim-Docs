---
sidebar_position: 1
id: project-setup
title: 🛠️ Project Setup
sidebar_label: 🛠️ Project Setup
---
import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

# Project Setup

Set up the MoSim Modding Repo on your device

## Prerequisites

* Unity will need about 5 GB of space
* The modding repo may use up to 1 GB
* Unoptimized robot cads can exceed 100 MB each
* Performance in the editor can be noticeably worse than in builds

### Unity Hub

Unity Hub is as a launcher for your Unity versions and projects. You can download it [here](https://unity.com/download). Once installed, open the app, and select a personal license. DO NOT install editor versions during setup.

### Git

MoSimulator depends on some Git packages to open in the editor, you can download it [here](https://git-scm.com/install/).

### Github Desktop (optional)

This is the easiest way to get into version control, you can download it [here](https://desktop.github.com/download/). More experienced programmers may elect to use other tools for this, but it ultimately does the same thing. 

:::tip

Version control lets you maintain a detailed, revertable history of changes (commits), and branches that can be worked on in parallel before merging your changes back in.

You can use version control either locally on your computer, or connected to a remote repository (recommended if you plan to work on multiple computers, collaborate with other people, or simply back up your project online).

:::

## Downloading the Source
We provide a limited source release for the community to develop, test, and build robot mods. You can install it one of three ways:

* **Direct download** - The simplest option to set up, but will need to be set up again if you want to update to a future version, and does not include version control
* **Local clone** - Allows for version control on your computer, as well as easily pulling in future modding releases
* **Forked repository** - Creates a remote repository, providing the same benefits as a local clone while also being stored online
<Tabs>
  <TabItem value="direct" label="Direct Download">
    * Go to the Repository linked below and download the source code using Code -> Download Zip
    * Go to your downloads folder and unzip it to wherever you want your project to be stored
    <p></p>
    <img src="/img/directdownload.png" alt="Direct Download" width="50%"/>
  </TabItem>
  <TabItem value="local" label="Local Clone">
    * Open the GitHub Desktop app
  * File -> Clone Repository -> Choose the Repository linked below and where you want the project to be stored on your computer
  <p></p>
  <img src="/img/clonerepo.png" alt="Clone Repo" width="50%"/>
  </TabItem>
  <TabItem value="fork" label="Forked Repository" default>
    * Go to the repository linked below and use the Fork button to create your own
    <img src="/img/fork.png" alt="Clone Repo" width="25%"/>
  * Open the GitHub Desktop app
  * File -> Clone Repository -> paste your repo link and where you want the project to be stored on your computer
  <p></p>
  <img src="/img/clonerepo.png" alt="Clone Repo" width="50%"/>
  Since you own this repo, you can push your branches and commits up in addition to pulling updates down
  </TabItem>
</Tabs>  

This is the link to our public repository: https://github.com/MoSimulator/MoSimulator-Public

## Adding the project to Unity

* In the Unity Hub app, click the `Add` button in the top right
* Find your project folder, Double click it to open the outer layer, then select the folder with your project name
* If you selected properly, it will ask you if you want to install the correct version of Unity, click yes. You can keep the default modules checked
* Once the download is complete, you will be able to open the project.

:::info

The editor takes a long time to download.

:::

<img src="/img/installunity.png" alt="Unity Hub Modules" width="75%"/>

## Updating

* If you used Github Desktop to install, simply fetch from upstream, then pull the origin on the GitHub Desktop app
* If you used the direct download you will need to start from scratch.

## Codespace Setup

You have 3 main options for your codespace:
* **Jetbrains Rider** - Recommended by the dev team for its excellent Unity integration
* **VS Code** - A lightweight code editor maintained by Microsoft, relies heavily on extensions that may be outdated
* **Visual Studio 2022** - Microsoft's primary IDE, has solid Unity integration but can be quite resource intensive

<Tabs>
  <TabItem value="rider" label="Jetbrains Rider" default>
    Rider has a free non-commercial and Student license. The team highly recommends this for the best experience.

    1. In Unity's top bar, select `Edit -> Preferences -> External Script Editor` then set External Script Editor to Rider.

    2. Now by double clicking a file in the project view it will open Rider with full syntax highlighting and auto complete

    <img src="/img/lynk/ridersetup.png" alt="Select Rider" width="75%"/>
    <p></p>
    :::warning
    If you chose the non-Commercial license the ai logging is OPT OUT and we highly recommend going to the app settings and disabling it.

    <img src="/img/lynk/datasharing.png" alt="Rider Settings" width="75%"/>
    :::
  </TabItem>
  <TabItem value="vsc" label="VS Code">
    1. On the sidebar, find the Extensions tab and install the Unity Extension

    <img src="/img/lynk/unityextension.png" alt="Unity VSC Extension" width="75%"/>

    :::tip
    A snippets extension is recommended to provide autocomplete for certain functions
    :::
    
    2. Finally in Unity on the top bar, do `Edit -> Preferences -> External Script Editor` then set External Script Editor to Visual Studio Code

    3. Now by double clicking a file in the project view it will open Visual Studio Code with full syntax highlighting and auto complete

    <img src="/img/lynk/selectvsc.png" alt="Select VSCode" width="75%"/>
  </TabItem>
  <TabItem value="vs" label="Visual Studio">
    1. Open the installer and add the Game Dev with Unity workload.

    <img src="/img/lynk/vssetup.png" alt="Visual Studio Setup" width="75%"/>

    2. In Unity, on the top bar select `Edit -> Preferences -> External Script Editor` then set External Script Editor to Visual Studio 2022,

    3. Now by double clicking a file in the project view it will open Visual Studio with full syntax highlighting and auto complete

    <img src="/img/lynk/selectvsc.png" alt="Select Visual Studio" width="75%"/>
  </TabItem>
</Tabs>  