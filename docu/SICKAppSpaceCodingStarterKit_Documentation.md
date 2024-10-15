# Table of content

- [Table of content](#table-of-content)
- [Overview of the SICK AppSpace Coding Starter Kit (CSK)](#overview-of-the-sick-appspace-coding-starter-kit-csk)
- [Introduction](#introduction)
- [Using the code of SICK AppSpace Coding Starter Kit modules](#using-the-code-of-sick-appspace-coding-starter-kit-modules)
  - [Option A – Multiple apps approach](#option-a-multiple-apps-approach)
  - [Option B – Single app approach](#option-b--single-app-approach)
    - [Copying manually CROWN manifest (for AppStudio version < 3.5.0)](#copying-manually-crown-manifest-for-appstudio-version--350)
  - [PRO / CONS of different approaches](#pro--cons-of-different-approaches)
    - [Multiple apps](#multiple-apps)
    - [Single app](#single-app)
- [CSK structure overview](#csk-structure-overview)
- [Using the SICK AppSpace Coding Starter Kit](#using-the-sick-appspace-coding-starter-kit)
  - [FlowConfig](#flowconfig)
- [Developing guideline for modules](#developing-guideline-for-modules)
  - [Structure](#structure)
  - [Files](#files)
    - [[ModuleName]_Model.lua without kind of "classes"](#modulename_modellua-without-kind-of-classes)
    - [[ModuleName]_Model.lua with kind of "classes" for single instance](#modulename_modellua-with-kind-of-classes-for-single-instance)
    - [[ModuleName]_Model.lua with kind of "classes" and multiple instances](#modulename_modellua-with-kind-of-classes-and-multiple-instances)
      - [[ModuleName]_Processing.lua for multiple instances](#modulename_processinglua-for-multiple-instances)
    - [[ModuleName]_Controller.lua](#modulename_controllerlua)
      - [[ModuleName]_Controller.lua for multiple instances](#modulename_controllerlua-for-multiple-instances)
	- [FlowConfig Functions and Events](#flowconfig-functions-and-events)
	- [FlowConfig Parameter Logic](#flowconfig-parameter-logic)
	- [FlowConfig Files](#flowconfig-files)
	  - [Pure Data Provider](#pure-data-provider)
	  - [Pure Data Consumer](#pure-data-consumer)
	  - [Pure Processor](#pure-processor)
	- [User Interface](#user-interface)
  - [CSK_Application](#csk_application)
  - [Relevant functions and events](#relevant_functions_and_events)
	  - [Function: resetModule](#function-reset)
	  - [Function: getStatusModuleActive](#event-onnewstatusmoduleisactive)
  - [Documentation](#documentation)
  - [Persistent Data](#persistent-data)
  - [User Management](#user-management)
  - [Performance](#performance)
    - [App parameter "LuaLoadAllEngineAPI"](#app-parameter-lualoadallengineapi)

# Overview of the SICK AppSpace Coding Starter Kit (CSK)

This article will explain what the approach of the SICK AppSpace Coding Starter Kit is and how you can use its “modules / templates” and how to code your own modules.

There are some [videos](https://sickappspacecodingstarterkit.github.io/) available to give a quick introduction to this approach.

General information regarding SICK AppSpace can be found here: https://supportportal.sick.com/tutorial/sick-appspace-articles/

# Introduction

The SICK AppSpace Coding Starter Kit ("CSK") provides a starting point for coding SensorApps. It consists of a collection of starter kit modules.

Each module provides a specific functionality, such as FTP, TCP/IP, IO-Link, remote camera setup, data persistence management, power management, logging, user management and more.

Modules appear as apps in the SICK AppStudio workspace, provide the key functionalities as served functions, and optionally contain a user interface (UI). If wanted, modules can also easily be merged into a single app for direct access to LUA-based functionality.

In addition, the SICK AppSpace Coding Starter Kit includes templates that represent application-specific combinations of the modules. The templates demonstrate how modules interact and serve as a starting point for further coding. For example, the remote camera template covers a basic image processing application on a SIM with multiple remote cameras and the steps of setup, image acquisition and result communication, where the data processing step can be freely customized at the code level - as well as all other steps of the application and the respective UIs.

Another part is the FlowConfig feature. This makes it possible to link the inputs / outputs of CSK modules with each other in a graphical way (see [FlowConfig](#flowconfig))

![](/docu/media/10.1_FlowConfigFeature.png)

By using SICK AppSpace Coding Starter Kit modules or templates, SensorApp developers do not have to start programming from scratch, but work with a modular, extensible architecture right from the start.

A key component of CSK is common naming, documentation, and code structure to make it easier to share modules, find the relevant code sections, and understand and extend modules. If you make use of the CSK modules, please share as much as possible with the community.

The SICK AppSpace Coding Starter Kit is not specific to a particular application domain, and the intended users are application developers working on source code. For certain application domains or devices, other starting points may be more appropriate, such as SICK Nova for 2D-vision applications on InspectorP-series devices. Users are encouraged to evaluate the different options and work with the ones that meet their needs.

# Using the code of SICK AppSpace Coding Starter Kit modules

There are 2 different possibilities to use the modules.
The modules are developed and structured in a way that you can simply copy the content into a single app or you can run them in parallel apps and use their CROWN interfaces. Both ways will be described in the following (incl. PRO + CONS of these two approaches):

## Option A – Multiple apps approach

You can easily download all wanted modules and add them into the SICK AppStudio working directory of your project (e.g.: CSK_Module_MultiRemoteCamera, CSK_Module_FTPClient, CSK_1stModule_Logger, …).

![](/docu/media/10.1_MultipleAppWorkingDirectory.png)

Now you have different options:

1. You can access the module-specific UIs in your browser via: http://[deviceIP]/#!msdd=[moduleName]&page=[UI name]
   
   (E.g.: http://192.168.0.1/#!msdd=CSK_Module_FTPClient&page=CSK_Module_FTPClient)
   
   For certain use cases it is (at this point in time) already possible to configure the system using the provided user interfaces to solve specific applications.

2. Additionally you can now create another app (e.g. named “CSK_Application_Name”). From this app you can use the provided functionalities of the modules. If needed, this app can include a customized user interface with bindings to the provided CROWNs of the modules (or even implement UIs of modules into the application specific UI). 

3. By default, when you add the [CSK_Module_SensorAppOverview](https://github.com/SICKAppSpaceCodingStarterKit/CSK_Module_SensorAppOverview) app to your working directory, you get an automatically generated UI that is displayed when you connect to the device IP via a browser (e.g. "http://192.168.0.1"). This enables you to easily switch between the different user interfaces such as the specific module UI or your customized user interface.

   ![](/docu/media/10.1_MultipleAppSensorAppOverviewUI.png)

   However, it is also possible to simply copy the relevant UIs (html + css file) of the other modules into the "pages" folder of the application and adapt the "navigation.json" file to display all UIs on one page. Since all relevant events and functions will still be served by the modules, the UI(s) will remain in working condition. 

   (INFO: Please see chapter [Using the SICK AppSpace Coding Starter Kit](#using-the-sick-appspace-coding-starter-kit) if you want to understand and get examples of how to call the functions and get access to the parameters of the modules.)

## Option B – Single app approach

If you want to use different modules in one single app, you could do it as follows:

1. Create a new, blank app with a blank script and the “pages” folder:

   ![](/docu/media/10.1_SingleAppWorkingDirectory.png)

   (INFO: If you do not want to use a customized UI within your SensorApp, you can delete the auto generated html and css file inside of the “pages”-folder)

2. Now you can copy the parts of the modules into this app.

   The modules are structured so that the relevant scripts are located in specific folders. The folder structure must be maintained within the merged single app.

   For example, to insert the "PowerManager" module, you must copy the "Configuration" folder inside "Scripts" into the merged single app. You can add more subfolders to this "Configuration" folder (such as the contents of the "PersistentData" module) or add other modules.
   ![](/docu/media/10.1_SingleAppMergedWorkingDirectory.png)

   (INFO: If you make use of "Multi"-modules, you also need to copy the related Processing scripts located directly in the scripts/ folder of the app, as it is currently not possible to start scripts which are located in a subfolder.)

3. To get access to the functionality of the modules you have to implement each of these modules in your script, e.g. via:

   ```
   _G.loggerModel = require('Communication/Logger/Logger_Model')
   _G.logger = _G.loggerModel.sharedLogger

   _G.powerManager_Model = require('Configuration/PowerManager/PowerManager_Model')
   ```

   This will give e.g. "_G.powerManager_Model" access to all relevant parameters and functions of this module. Before that the logger will be loaded.

   If you use "multi" CSK modules, you must also create an instance and make some additional calls. E.G.:

   ```
   local multiColorSelection_Model = require('ImageProcessing/MultiColorSelection/MultiColorSelection_Model')

   local multiColorSelection_Instances = {}
   table.insert(multiColorSelection_Instances, multiColorSelection_Model.create(1))

   local multiColorSelectionController = require('ImageProcessing/MultiColorSelection/MultiColorSelection_Controller')
   multiColorSelectionController.setMultiColorSelection_Instances_Handle(multiColorSelection_Instances)
   
   -- ...

   local function OnStarted()
   
     multiColorSelectionController.setMultiColorSelection_Model_Handle(multiColorSelection_Model)
   -- ...
   end
   Script.register("Engine.OnStarted", OnStarted)
   ```

   (INFO: Please see chapter [Using the SICK AppSpace Coding Starter Kit](#using-the-sick-appspace-coding-starter-kit) and documentation within the LUA scripts if you want to understand and get examples of how to call the functions and get access to the parameters of the modules.)

4. If you want to use the module specific UIs, you also need to copy them to your merged single app. If the UIs do use specific css classes, the css file must also be copied:

   ![](/docu/media/10.1_SingleAppMergedUIWorkingDirectory.png)

5. Note that if there are other files e.g. in "pages/assets", "resources", "parameters", ... you have to copy them too.

6. In addition, you must copy the module's custom CROWNs from the manifest to the merged manifest of the single app so that the user interfaces work and the application provides all functions and events.

   Since AppStudio 3.5.0 this can easily be done via right-click on the CROWN.

   ![](/docu/media/10.1_CrownCopy.png)

   **INFO**: For older AppStudio versions this needs to be done manually inside of the manifests (see following steps). Otherwise you can skip the next chapter.

### Copying manually CROWN manifest (for AppStudio version < 3.5.0)

1. With right click on the app, you can easily get access to the manifest

   ![](/docu/media/10.1_ExploreFolder.png)
   
   ![](/docu/media/10.1_ExploreFolderManifest.png)
   
   E.g. content of empty single app with default CROWN:
   
   ![](/docu/media/10.1_BlankManifest.png)

2. You can open the manifests of the modules the same way and copy the CROWNs out of the modules manually in the editor with copy+paste.
   E.g. relevant content of “LoggerModule”-manifest:

   ![](/docu/media/10.1_LoggerManifest.png)

3. Merged single app manifest after pasting content:
   ![](/docu/media/10.1_LoggerMergeManifest.png)

4. Repeat this for all wanted modules

## PRO / CONS of different approaches

### Multiple apps

PRO

- Easy implementation of different modules into one working directory
- Versioning of the modules via app manifest
- Clear separation of modules and application specific part

CONS

- No direct access to instances of the other modules out of LUA
- Often necessary to communicate via CROWN interface to share content between the modules (key/value table need to be transformed into Container objects, but there are predefined functions available for this)
- To keep performance if modules perform "cross module function calls", modules should be developed by paying attention regarding garbage collection (see chapter [Performance](#performance))

### Single app

PRO

- Direct access to instances (incl. their parameters) in LUA
- Easier/direct customization for specific apps in code

CONS

- Manual process to copy/merge module context into one single app (“source of error”)
- Versioning of modules need to be managed by the developer
- If scripts were modified (e.g. extended features) just inside of the scripts of the merged app, reuse in other projects is more difficult

# CSK structure overview

![](/docu/media/10.1_CSK_StructureOverview.png)

# Using the SICK AppSpace Coding Starter Kit

You will find templates which show how the code of the modules is used in the 2 mentioned ways (single app / multiple apps) within this GitHub organization.

The templates can be used to understand how to combine the modules as described above.
In addition, the templates can be used "out of the box" for typical use cases (e.g. device setup, camera connection, ...).

In general, each CSK module provides documentation for the features provided, based on the module's manifest documentation (and sometimes with hints on which functions are mainly relevant to use the module's features).

Additionally, inside of the scripts of the modules you will find some internal code explanation and optionally some sample code within the main script to show how typical functions of this module could be used.

The idea is to keep the modules as generic as possible and only use their features (optionally from an application app). This way, it is easy to upgrade certain modules to newer versions without having to adjust the customizations made before.

As a lot of the modules provide an UI, it can be even possible to solve applications out of the box by just running the modules on a device, connect to them via browser and configure them.

## FlowConfig

(**INFO** Watch the [FlowConfig video](https://sickappspacecodingstarterkit.github.io/) to get a short introduction of this feature.)

As already mentioned, each CSK module provides at least one generic feature. To solve specific applications it is very interesting to combine these features with each other.

To do so, a lot of modules provide their results within served events and other modules can register on these events to further process the data.

A lot of modules provide a function to register to these events and provide this setup on the UI as well.

To make it easier to link these inputs / outputs it is possible to use the FlowConfig feature, which is provided as a CSK module as well (see [FlowConfig module](https://github.com/SICKAppSpaceCodingStarterKit/CSK_Module_FlowConfig).

Within the [Developing guideline for modules](#developing-guideline-for-modules) you will find the relevant code to extend a module to support the FlowConfig feature.

![](/docu/media/10.1_FlowConfigFeature.png)

# Developing guideline for modules

This guide is meant as a suggestion to find a common way to code modular apps for the SICK AppSpace Coding Starter Kit, so that it becomes easy to combine these different modules with each other and to benefit from this when sharing these modules and to understand the code written by another developer faster by knowing this guide (“where to find the relevant part inside of the code”).

(**INFO** Watch the [Extending the function scope video](https://sickappspacecodingstarterkit.github.io/) to get an idea what it looks like to extend the code of an existing module.)

As already mentioned: naming, documentation and code structure is a major part of this.

In general, this guideline is based on the AppSpace Application Development Guideline (https://supportportal.sick.com/tutorial/appspace-application-development-guidelines/)

There are different kinds of modules:

1. Modules with very limited functionality (sometimes just “piping” simple AppEngine APIs). 

   Nevertheless it is useful to provide these modules e.g. to easily reuse a developed UI for this functionality in different projects (e.g. the CSK_Module_PowerManager or the CSK_1stModule_Logger).

2. Modules that provide a functional scope using one or more AppEngine APIs and provide additional functionality using these different APIs (e.g. the CSK_Module_DateTime incl. NTP config)

3. Modules with complex functionality using one or more AppEngine APIs and providing customized (kind of) classes of this module (e.g. CSK_Module_MultiRemoteCamera) for object-oriented programming.

Beside that there is the difference between "Single"- and "Multi"- modules:

1. "Single"-module:

   Used to provide single instance features, such as DateTime, DigitalIOManager, PowerManager

2. "Multi"-module:

   Used to provide features in multiple instances, such as multiple sensors, processing algorithms, connections, ...

   This is more complex to code, but it allows multiple instances to run in parallel threads while still using the same code.

Each module should be able to run by itself (no other module running in parallel) and to provide its feature set. Please make also sure to keep the features provided by a module as generic as possible to be able to easily reuse it and to prevent parallel development of the same feature.

If there are optional cross functionalities, it should be checked within the module if the other features are avaiable and then de- or activate these features.

In general, the modules can be separated in different parts:

-	Scripts to provide the module specific functionalities ("model")
-	Scripts to provide module specific interfaces (in combination with the manifest of this module) via CROWN interface ("controller")
-	Optional UIs, making use of the served CROWNs of this module ("view")

The following explains how this can be realized.

**Note:** It is useful to take some already available modules and compare them with the information in this article, as this helps to understand the reasons / sense of the following steps.

There are also "blank" module templates available, which can be easily customized/renamed to create your own (multi) module without having to start from scratch:

https://github.com/SICKAppSpaceCodingStarterKit/csk_module_creationtemplate

https://github.com/SICKAppSpaceCodingStarterKit/csk_module_creationmultitemplate

As mentioned within the [Contribution Guideline](https://github.com/SICKAppSpaceCodingStarterKit/.github/blob/main/Contribution_Guideline.md#1-find-an-issue-that-you-are-interested-in-addressing-or-a-feature-that-you-would-like-to-add): Feel free to request a new repo for a new module which will include the accordingly renamed app content based on the mentioned templates.

Note: We plan to provide a public auto renaming tool in future.

## Structure
Each module should consist of a “main” lua-file, named like the module (eg. CSK_Module_FTPClient with “CSK_Module_FTPClient.lua”).
Mostly this file will load (require) the relevant script(s) first to give access to the function scope of this module.

```
_G.availableAPIs = require('Communication/FTPClient/helper/checkAPIs')
-- Check if module can run on device
if _G.availableAPIs.specific == false then
  _G.logger:warning("CSK_FTPClient: Relevant CROWN(s) not available on device. Module is not supported...")
end
-- ...
_G.ftpClient_Model = require('Communication/FTPClient/FTPClient_Model')
require('Communication/FTPClient/FlowConfig/FTPClient_FlowConfig') -- relevant if the module supports the FlowConfig
```

The upper part of the code loads and checks all relevant APIs for this module if this does not happen automatically because the app property "LuaLoadAllEngineAPI" is set to FALSE (for more info on this topic, see the chapter [Performance](#performance) ).

This global variable can be used inside of the whole code to check if the feature is available on the device.

Also, a SharedLogger + Log.Handler should be created. This way all modules send their logging messages to the same SharedLogger and the "LoggerModule" can be used to collect all these messages.

```
_G.logger = Log.SharedLogger.create('ModuleLogger')
_G.logHandle = Log.Handler.create()
_G.logHandle:attachToSharedLogger('ModuleLogger')
_G.logHandle:setConsoleSinkEnabled(false) --> Set to TRUE if LoggingModule is NOT used
_G.logHandle:setLevel("ALL")
_G.logHandle:applyConfig()
```

Log messages should then be used like this:

```
_G.logger:info("Log message")
```

Additionally, it is good to provide inside of the main-function (called on the “Engine.OnStarted”-event) commented sample code to demonstrate the usage of this module:

```
local function main()

  ----------------------------------------------------------------------------------------
  -- Can be used e.g. like this
  ----------------------------------------------------------------------------------------
  --[[
  -- Set username / password via script
  CSK_FTPClient.setFtpServerIp('192.168.0.123')
  CSK_FTPClient.setFtpPort = 21
  CSK_FTPClient.setUsername = 'tester'
  CSK_FTPClient.setPassword = 'password'
  CSK_FTPClient.setPassiveMode(true)

  local success = CSK_FTPClient.connectFtpClient()
  if success then
    _G.logger:info("Connected")

    local img = Image.create(2000, 2000, "INT8")
    CSK_FTPClient.setAsyncMode = false
    CSK_FTPClient.sendImage(img)

  else
    _G.logger:info("No connection")
  end
  ]]
  ----------------------------------------------------------------------------------------
end
Script.register("Engine.OnStarted", main)

--OR
-- Call function after persistent data was loaded
--Script.register("CSK_FTPClient.OnDataLoadedOnReboot", main)
```

If you make use of modules with "kind of" classes for object-oriented programming it is a little bit different. Please check the chapters:
[[ModuleName]_Model.lua with kind of "classes" for single instance](#modulename_modellua-with-kind-of-classes-for-single-instance)
and
[[ModuleName]_Model.lua with kind of "classes" and multiple instances](#modulename_modellua-with-kind-of-classes-and-multiple-instances)

## Files
Depending on the functionality of the module, it should be decided in which folder the module fits best (e.g. “Communication”, “Sensor”, “Configuration”, …) and a new subfolder with the name of the module should be created. Inside of this subfolder at least 2 LUA files need to be created:

- [ModuleName] _Model.lua

  This file keeps all code regarding parameters and internal functional part of the module

  (**INFO:** “Model” is added here, because quite often the module provides more functionality than the name of the module promises (e.g. the “CSK_Module_FTPClient” includes also a JPG encoder to optionally use it inside its “sendImage” function to reduce the image before sending).

- [ModuleName] __Controller_.lua

  This file keeps all code regarding communication with external code/apps

Additionally, it is possible to place other scripts in the subfolder, if it makes sense. Most modules use a similar "funcs.lua" inside of the "helper"-folder with helpful standard functions, like converting LUA tables to AppSpace "Container"-objects to transfer data between modules (especially to send data to the CSK_PersistentData module).

Beside that, some modules (like e.g. the “CSK_Module_SingleRemoteCamera”, “CSK_Module_MultiIOLinkRemoteDevice", “CSK_Module_MultiColorSelection"...)  include additionally “...Processing.lua” files to keep all relevant script parts regarding the processing of incoming data. This allows the developer to focus on this specific part of the code..

![](/docu/media/10.1_ModuleScriptsContent.png)

For "Multi"-modules, scripts which should run in its own thread must be also located directly in the scripts/ folder of the app, as it is not possible to start scripts which are located in a subfolder via "Script.startScript". 
See chapter [[ModuleName]_Model.lua with kind of "classes" and multiple instances](#modulename_modellua-with-kind-of-classes-and-multiple-instances) for further information.

### [ModuleName]_Model.lua without kind of "classes"

As mentioned before, there are different kinds of modules. If it is about modules without the need of "classes" for object-oriented programming, this script should at least consist of the following parts:

1. Creating a table to hold all module relevant parameters + functions to return this at the end of this script. With this you can “require” this module from another script. E.g.:

   ```
   local ftpClient_Model = {}
   --...
   -- ..Nearly end of the file..
   return ftpClient_Model
   ```

2. Check if modules with general features (like e.g. UserManagement, PersistentData) are available so that their functionalities can be used within the module:

   ```
   ftpClient_Model.userManagementModuleAvailable = CSK_UserManagement ~= nil or false
   ftpClient_Model.persistentModuleAvailable = CSK_PersistentData ~= nil or false
   ```

3. Requiring the related [ModuleName]_Controller.lua and giving this script the handle to the previous created table, e.g.:

   ```
   local setFtpClient_Model_Handle = require('Communication/FTPClient/FTPClient_Controller')
   setFtpClient_Model_Handle(ftpClient_Model)
   ```

   **INFO**: Sometimes just the "set[ModuleName]_Model_Handle"-function is needed to give access to this variable if no other functions from the "Controller" is needed inside of the Model script...

4. Require other relevant scripts for this module:

   ```
   moduleName_Model.helperFuncs = require('Mainfolder/Subfolder/helper/funcs')
   ```

5. Adding all relevant runtime parameters / objects to this table incl. short internal docu what it is for, like:

   ```
   ftpClient_Model.ftpClient = FTPClient.create() -- FTP client to use for FTP connection
   ftpClient_Model.formatter = Image.Format.JPEG.create() -- Formatter instance (JPG per default)
   ftpClient_Model.counter = 1 -- Internal counter, e.g. used to count sent data and use it for naming
   ftpClient_Model.version = Engine.getCurrentAppVersion() -- Version of module
   --...
   ```

   With this, the parameters or internal objects incl. their functions can be directly accessed inside of this module e.g. via:

   ```
   ftpClient_Model.formatter:encode(image)
   ```

6. Additionally it is possible to put all data that is relevant to be saved persistently to put into an additional sub-table like this:

   ```
   ftpClient_Model.parameters = {}
   ftpClient_Model.parameters.flowConfigPriority = CSK_FlowConfig ~= nil or false
   ftpClient_Model.parameters.serverIP = '192.168.0.201'
   ftpClient_Model.parameters.port = 21
   ftpClient_Model.parameters.subTable = {}
   -- ...
   ```

   With this it is quite easy to use the “CSK_Module_PersistentData” to store this data in a binary file on the device (see chapter [Persistent data](#persistent-data)).

7. Provide default values for Persistent data "parameterName" and if those parameters should be loaded on app reboot ( see also chapter [Persistent data](#persistent-data))

   ```
   ftpClient_Model.parametersName = 'CSK_FTPClient_Parameter'
   ftpClient_Model.parameterLoadOnReboot = false
   ```

8. Creating all relevant functions of this module which are needed to perform the internal processing. E.g.:

   ```
   -- Function to send an image to the FTP server
   --@sendImage(img:Image):
   local function sendImage(img)
   --...
   end
   ftpClient_Model.sendImage = sendImage
   ```

   **HINT**: If the function is served for the CROWN interface, it should be placed into the [ModuleName]_Controller.lua file (see following chapters). But sometimes it makes sense to serve internally used functions also as a CROWN function.
   If so, to reduce the code, it can be kept inside of the [ModuleName]_Model.lua + the “Script.serveFunction” command.
   But it is also possible to serve a function inside of the [ModuleName]_Controller.lua and to call the internal function from there to keep script structure…)

9. Return the created model table

   ```
   return ftpClient_Model
   ```

### [ModuleName]_Model.lua with kind of "classes" for single instance

As mentioned before, there is also the more complex module type, providing kind of “classes” (LUA does not support classes but tables can be used in some senses…) for object-oriented programming. There are some differences.

Again, create a table to hold all module relevant parameters + functions to return this at the end of this script. As this time the module will provide the possibility to create objects with internal parameters + functions you will need to have a “create”-function to return this object. For this you can use something like:

```
local remoteCamera= {}
remoteCamera.__index = remoteCamera
remoteCamera.version = Engine.getCurrentAppVersion() -- Version of module

--@ remoteCamera.create():
function remoteCamera.create()
  local self = {}
  setmetatable(self, remoteCamera)
  self.isConnected = false
  self.parameters = {}
  self.parameters.flowConfigPriority = CSK_FlowConfig ~= nil or false
  self.parameters.cameraIP = '192.168.1.100'
  self.CameraProvider = Image.Provider.RemoteCamera.create()
  --...
  local setCameraHandle = require('Sensors/SingleRemoteCamera/SingleRemoteCamera_Controller')
  setCameraHandle(self)
  --...
  self.imageProcessingParams = {}
  self.imageProcessingParams.resizeFactor = 1.0
  --...
  self.imageProcessing_Model = require('Sensors/Camera/ImageProcessing')
  self.imageProcessing_Model.setImageProcessingParams(self.imageProcessingParams)
  --...
  return self
end
-- Function to connect the camera
--@ remoteCamera:connectCamera():
function remoteCamera:connectCamera()
  self.CameraProvider:setIPAddress(self.parameters.cameraIP)
 --...
end
--...
return remoteCamera
```

With this you can create objects of this module e.g. within the main script via:

```
local singleRemoteCamera_Model = require('Sensors/SingleRemoteCamera/SingleRemoteCamera_Model') 
_G. remoteCameraObject = singleRemoteCamera_Model.create()
```

and get access via:

```
_G. remoteCameraObject.parameters.cameraIP = '192.168.1.110'
_G. remoteCameraObject:connectCamera()
```

### [ModuleName]_Model.lua with kind of "classes" and multiple instances

This approach extends the [ModuleName]_Model.lua from the previous chapter.

It is possible to create multiple instances to run in parallel (in their own threads). Note: it is a little bit more complex to develop / understand this approach, so you should be already experienced with SICK AppSpace SensorApp development.

The idea is to still have a single source inside of the code but to be able to communicate / setup the different instances.

![](/docu/media/10.1_MultiInstancesGraph.png)

So this time the create function needs the information of the instance number to create so that the instance knows about it internally, e.g.:

```
function multiColorSelection.create(multiColorSelectionInstanceNo)
--...
self.multiColorSelectionInstanceNo = multiColorSelectionInstanceNo
```

With this later the create function can be used from the main script e.g. via:

```
local multiColorSelection_Instances = {}
table.insert(multiColorSelection_Instances, multiColorSelection_Model.create(1))
table.insert(multiColorSelection_Instances, multiColorSelection_Model.create(2))
-- ...

local multiColorSelectionController = require('ImageProcessing/MultiColorSelection/MultiColorSelection_Controller')
multiColorSelectionController.setMultiColorSelection_Instances_Handle(multiColorSelection_Instances)
```

Each instance will start its own processing script, but can be based on the same code. Because of this the processing LUA file needs to be located directly in the scripts/ folder of the app, as it is not possible to start scripts which are located in a subfolder via the used "Script.startScript"-API.

As the processing of each instance runs in its own thread, all relevant parameters of the instance need to be transferred and to be kept in sync.
(Please check also the information in the following sub chapters...)

This can be done by packing all relevant parameters into a container and to share this as parameters inside of the "Script.startScript" function call, e.g.:

```
function multiColorSelection.create(multiColorSelectionInstanceNo)
  -- ...
  self.parameters.processingFile = 'CSK_MultiColorSelection_Processing'
  -- ...

  -- Parameters to give to the processing script
  self.multiColorSelectionProcessingParams = Container.create()
  self.multiColorSelectionProcessingParams:add('multiColorSelectionInstanceNumber', multiColorSelectionInstanceNo, "INT")
  -- ...

  Script.startScript(self.parameters.processingFile, self.multiColorSelectionProcessingParams)
```

To be able to use the same served functions and one single UI for all instances instead of creating this multiple times, it is necessary to know which instance is currently selected and to sync the parameters between the processing threads and the main part of the app.

Each instance should have a "self.activeInUi" variable to hold the information which instance currently is selected.

#### [ModuleName]_Processing.lua for multiple instances

As already said, this script can be started multiple times and will run in its own thread.
To use the same code multiple times it is necessary to differentiate within the code between the different instances.
To do this the instance number is transferred via the model create function and can now be used within this script, e.g.:

```
local scriptParams = Script.getStartArgument()
local multiColorSelectionInstanceNumber = scriptParams:get('multiColorSelectionInstanceNumber')
```

This variable can now be used to differentiate, e.g. via:

```
Script.serveEvent("CSK_MultiColorSelection.OnNewResult" .. multiColorSelectionInstanceNumberString, "MultiColorSelection_OnNewResult" .. multiColorSelectionInstanceNumberString, 'bool:1, string:?, int:?')
```

By doing this, the different instances can share their internal results e.g. via the events "OnNewResult1", "OnNewResult2", ... and the Controller can receive and handle these results or other modules can directly register on it.

To share internal changes of parameters, the script should make use of the "[ModuleName]_OnNewValueUpdate"-event. With this it is possible to sync a change of an internal parameter to the Controller, so that both values are in sync.

To share internal results and to forward them e.g. to the UI through the Controller, the script should make use of the "[ModuleName]_OnNewValueToForward"-event.
Please check the MultiTemplate or any other Multi-module to see how it can be used.

On the other hand, to receive changes which were done on the parameters via the Controller, it should make use of the "[ModuleName]_OnNewProcessingParameter" to edit the changes.
This event will share the instance number the change is for.

![](/docu/media/10.1_MultiInstancesEvents.png)

### [ModuleName]_Controller.lua

1. Serve all relevant events / functions of the module inside of the custom CROWN (it makes sense to create a custom CROWN, as if you use the app-name specific CROWN it can change by renaming the app and this can lead to a non working module / UI).

   ![](/docu/media/10.1_CustomCrown.png)

2. If you are serving events, please also make sure that the "localEventName" is unique (second parameter of the Script.serveEvent function) and to use this event name within the code. Otherwise it will cause errors if merging different modules into one single app and the local event names are the same between different modules. E.g. you can add the [ModuleName] in front of the event name, like here for the "CSK_PowerManager"-module:

   ```
   Script.serveEvent("CSK_PowerManager.OnPersistentDataModuleAvailable", "PowerManager_OnPersistentDataModuleAvailable")
   ```

3. There is a “Timer” and a “…PageCalled()”-function to start this Timer + a registered “handleOnExpired…()”-function to notify all relevant events. This is usefull to have full control regarding UI value updating without the need of polling these values all the time (which could lead to performance problems).

4. Create all relevant functions to provide the interface for this module and serve them inside of the custom CROWN of this module.

5. Create a function to get access to the module table and return this function if this script is required.
   
   ```
   -- Function to get access to the FtpClient_Model object
   --@setFtpClient_Model_Handle(handle:table):
   local function setFtpClient_Model_Handle(handle)
     ftpClient_Model = handle
   end
   return setFtpClient_Model_Handle
   ```

Inside of this script there are also the relevant functions to provide the "UserManagement" and "PersistentData" features.

#### [ModuleName]_Controller.lua for multiple instances

This approach extends the [ModuleName]_Controller.lua script.

As already mentioned, the Controller needs the "[ModuleName].OnNewProcessingParameter"- event to share parameter changes to the instances.

Beside this, it needs to handle incoming updates from the instances by handling the "[ModuleName]_OnNewValueUpdate"- and "[ModuleName]_OnNewValueToForward"-event.

E.g. like this:

```
local function handleOnNewValueToForward(eventname, value)
  Script.notifyEvent(eventname, value)
end

local function handleOnNewValueUpdate(instance, parameter, value, selectedObject)
    moduleName_Instances[instance].parameters.internalObject[selectedObject][parameter] = value
end

-- ...

local function setModuleName_Instances_Handle(handle)
  -- ...

  for i = 1, #moduleName_Instances do
    Script.register("CSK_ModuleName.OnNewValueToForward" .. tostring(i) , handleOnNewValueToForward)
  end
  
  for i = 1, #moduleName_Instances do
    Script.register("CSK_ModuleName.OnNewValueUpdate" .. tostring(i) , handleOnNewValueUpdate)
  end
```

To pass the information, which instance is currently active, the function "setSelectedInstance" should be used, which passes the information via the event "OnNewProcessingParameter":

```
local function setSelectedInstance(instance)
  selectedInstance = instance
  moduleName_Instances[selectedInstance].activeInUi = true
  Script.notifyEvent('ModuleName_OnNewProcessingParameter', selectedInstance, 'activeInUi', true)
  tmrModuleName:start()
end
Script.serveFunction("CSK_ModuleName.setSelectedInstance", setSelectedInstance)
```

Besides, there are also functions to dynamically add/reset the number of instances.

### FlowConfig Functions and Events

If a module should be available within the FlowConfig, there are some events and function it should provide.

Additionally, there are some other relevant lines of code already mentioned in other chapters. Please search for 'FlowConfig' on this documentation page to find other relevant lines of code.

**INFO: Before you try to extend a module with this feature, you should be sure to understand all the other CSK basics!**

- Make sure to check if the CSK_Module_FlowConfig is available on the device. Place this check within the 'checkAPIs.lua' file like this:

```
...
-- Check if related CSK modules are available to be used
  local appList = Engine.listApps()
  for i = 1, #appList do
    if appList[i] == 'CSK_Module_PersistentData' then
      CSK_PersistentData = require 'API.CSK_PersistentData'
    elseif appList[i] == 'CSK_Module_UserManagement' then
      CSK_UserManagement = require 'API.CSK_UserManagement'
    elseif appList[i] == 'CSK_Module_FlowConfig' then
      CSK_FlowConfig = require 'API.CSK_FlowConfig'
    end
  end
...
```

- Within the main file, require the main FlowConfig file (regarding the FlowConfig files, see chapter [FlowConfig Files](#flowconfig-files)) and hand over the handle of the instances.

```
local setInstanceHandle = require('MainFolder/ModuleName/FlowConfig/ModuleName_FlowConfig')
...
setInstanceHandle(multiDataLogger_Instances)
```  

- Module should provide a function 'setFlowConfigPriority' to set the status if the FlowConfig should have priority (see chapter [FlowConfig Parameter Logic](#flowconfig-parameter-logic) ) and an event named 'OnNewStatusFlowConfigPriority' to provide this status.

- Module should provide a function 'getStatusModuleActive' to inform the CSK_Module_FlowConfig if the features of the module are available on the device the module is running on.

- Module should provide a function 'clearFlowConfigRelevantConfiguration' to clear all FlowConfig relevant configuration if the FlowConfig priority is active for the module (see chapter [FlowConfig Parameter Logic](#flowconfig-parameter-logic) ).

### FlowConfig Parameter Logic

Within the FlowConfig it is possible to link the features of CSK modules and to set some related parameters for these modules.

Because of this, it is possible that the setup of the FlowConfig differs from the setup done within the specific module and could lead to unexpected behaviour.

To prevent this, there is the "FlowConfig priority". The idea of this priority is to clear all configurations of the module which can be done via FlowConfig as well. That means, if the priority is active, these configurations are only controlled via FlowConfig.

If the user is aware of this possible vulnerability, it is still possible to deactivate that priority and to combine module specific configuration with the FlowConfig setup. It is just important that these configurations are in harmony with each other.

In general the logic of loading the parameters via the CSK_Module_PersistentData (see [Persistent Data](#persistent-data) ) is like this:

1) First, each CSK module will load its saved configuration if its 'Load on reboot' status is active (see modules UI)

2) After that, the FlowConfig will load its configuration

3) If the 'FlowConfig priority' status of a module is active, all FlowConfig relevant configurations of these modules will be cleared (the module developer needs to take care of this, see 'clearFlowConfigRelevantConfiguration' function).

4) The configuration stored within the FlowConfig will be set to the related CSK modules

### FlowConfig Files

If the module shoud support the FlowConfig feature, there is even some more additional content to add.

**INFO: Before you try to extend a module with this feature, you should be sure to understand all the other CSK basics!**

First of all, create a "FlowConfig" folder at 'scripts/Mainfolder/Subfolder/' and create a file '[ModuleName]_FlowConfig.lua' in it.

This will be required out of the main file and internally requires all lua files for available FlowConfig features. Additionally it will register to the 'OnClearOldFlow'-event of the FlowConfig CSK module to optionally clear all FlowConfig relevant configuration if a new flow was activated:

```
require('Communication.FTPClient.FlowConfig.FTPClient_Put')
--require('Communication.FTPClient.FlowConfig.FTPClient_OtherFeatureA')
--require('Communication.FTPClient.FlowConfig.FTPClient_OtherFeatureB')

--- Function to react if FlowConfig was updated
local function handleOnClearOldFlow()
  if _G.availableAPIs.specific then
    -- for i = 1, #multiModuleName_Instances do -- only relevant for MultiModules
      if ftpClient_Model.parameters.flowConfigPriority then
        CSK_FTPClient.clearFlowConfigRelevantConfiguration()
      end
	-- end
  end
end
Script.register('CSK_FlowConfig.OnClearOldFlow', handleOnClearOldFlow)

--[[
Only relevant for MultiModules
local function setMultiModuleName_Instances_Handle(handle)
  multiModuleName_Instances = handle
end

return setMultiModuleName_Instances_Handle
]]
```

The code content of the FlowConfig files depends if the module should only provide data as an output, only consume incoming data or do both of it.

#### Pure Data Provider

Create a file like 'ModuleName_OnNewData.lua' within the 'FlowConfig' folder and require this within the 'Mainfolder/Subfolder/[ModuleName]_FlowConfig.lua' as mentioned above.

This file should include code like this:

```
-- Block namespace
local BLOCK_NAMESPACE = "ModuleName_FC.OnNewData"
local nameOfModule = 'CSK_ModuleName'

--*************************************************************
--*************************************************************

local function register(handle, _ , callback)

  Container.remove(handle, "CB_Function")
  Container.add(handle, "CB_Function", callback)

  local function localCallback()
    if callback ~= nil then
      Script.callFunction(callback, 'CSK_ModuleName.OnNewData') -- adapt this accordingly
    else
      _G.logger:warning(nameOfModule .. ": " .. BLOCK_NAMESPACE .. ".CB_Function missing!")
    end
  end
  Script.register('CSK_FlowConfig.OnNewFlowConfig', localCallback)

  return true
end
Script.serveFunction(BLOCK_NAMESPACE ..".register", register)

--*************************************************************
--*************************************************************

local function create()
  local container = Container.create()
  Container.add(container, "CB_Function", "")
  return(container)
end
Script.serveFunction(BLOCK_NAMESPACE .. ".create", create)
```

The 'create' function will be called, as soon as a block of this feature exists within the flow.

If this block is linked to another block, it will call the 'register' function. Within this function it is possible to edit the name of the event to provide the output content of the module.

As you can see, it is just about to provide the information on what event the output data can be received.

Additionally the manifest needs to be edited. For this, create another CROWN like 'ModuleName_FC'.

The ending '_FC' is important, as the CSK_Module_FlowConfig will check for CROWNs ending like that to take these into account for the FlowConfig.

As well, please make sure to set the 'Data-Flow'-attribute of the event to 'Include' so that it will be visible within the BlocksEditor.

See here an example for such a manifest:

![](/docu/media/10.1_FlowConfigManifestProvider.png)

If the block should be available multiple times (e.g. to provide data of multiple instances) the code is a little bit more complex to keep track of the created blocks:

```
-- Block namespace
local BLOCK_NAMESPACE = "MultiRemoteCamera_FC.OnNewImage"
local nameOfModule = 'CSK_MultiRemoteCamera'

--*************************************************************
--*************************************************************

-- Required to keep track of already allocated resource
local instanceTable = {}

local function register(handle, _ , callback)

  Container.remove(handle, "CB_Function")
  Container.add(handle, "CB_Function", callback)

  local instance = Container.get(handle, 'Instance')

  -- Check if amount of instances is valid
  -- if not: add multiple additional instances
  while true do
    local amount = CSK_MultiRemoteCamera.getInstancesAmount()
    if amount < instance then
      CSK_MultiRemoteCamera.addInstance()
    else
      break
    end
  end

  local function localCallback()
    if callback ~= nil then
      Script.callFunction(callback, 'CSK_MultiRemoteCamera.OnNewImageCamera' .. tostring(instance)) -- Add info about specific event of the instance
    else
      _G.logger:warning(nameOfModule .. ": " .. BLOCK_NAMESPACE .. ".CB_Function missing!")
    end
  end
  Script.register('CSK_FlowConfig.OnNewFlowConfig', localCallback)

  return true
end
Script.serveFunction(BLOCK_NAMESPACE ..".register", register)

--*************************************************************
--*************************************************************

local function create(instance)

  -- Check if same instance is already configured
  if nil ~= instanceTable[instance] then
    _G.logger:warning(nameOfModule .. ': Instance already in use, please choose another one')
    return nil
  else
    -- Otherwise create handle and store the restriced resource
    local handle = Container.create()
    instanceTable[instance] = instance
    Container.add(handle, 'Instance', instance)
    Container.add(handle, "CB_Function", "")
    return handle
  end
end
Script.serveFunction(BLOCK_NAMESPACE .. ".create", create)

--- Function to reset instances if FlowConfig was cleared
local function handleOnClearOldFlow()
  Script.releaseObject(instanceTable)
  instanceTable = {}
end
Script.register('CSK_FlowConfig.OnClearOldFlow', handleOnClearOldFlow)

```

#### Pure Data Consumer

If the feature should only process incoming data, create a LUA file like 'ModuleName_DoSomething' within the 'FlowConfig' folder and require this within the 'Mainfolder/Subfolder/[ModuleName]_FlowConfig.lua' as well.

This file should include code like this:

```
-- Block namespace
local BLOCK_NAMESPACE = 'ModuleName_FC.DataSource'
local nameOfModule = 'CSK_ModuleName'

--*************************************************************
--*************************************************************

-- Required to keep track of already allocated resource
local instanceTable = {}

local function setDataSource(handle, dataSource)

  local instance = Container.get(handle, 'Instance')

  -- Check if amount of instances is valid
  -- if not: add multiple additional instances
  while true do
    local amount = CSK_ModuleName.getInstancesAmount()
    if amount < instance then
      CSK_ModuleName.addInstance()
    else
      CSK_ModuleName.setSelectedInstance(instance)
      CSK_ModuleName.setRegisterEvent(dataSource) -- use the received data (name of event with data) to register to it
      break
    end
  end
end
Script.serveFunction(BLOCK_NAMESPACE .. '.dataSource', setDataSource)

--*************************************************************
--*************************************************************

local function create(instance)

  -- Check if same instance is already configured
  if nil ~= instanceTable[instance] then
    _G.logger:warning(nameOfModule .. ': Instance already in use, please choose another one')
    return nil
  else
    -- Otherwise create handle and store the restriced resource
    local handle = Container.create()
    instanceTable[instance] = instance
    Container.add(handle, 'Instance', instance)
    return handle
  end
end
Script.serveFunction(BLOCK_NAMESPACE .. '.create', create)

--- Function to reset instances if FlowConfig was cleared
local function handleOnClearOldFlow()
  Script.releaseObject(instanceTable)
  instanceTable = {}
end
Script.register('CSK_FlowConfig.OnClearOldFlow', handleOnClearOldFlow)
```

As you see, in this case it will use the received data (the name of the event of another module to get the data) to register to this event, so that the module can receive this data.

Manifest can look like this:

![](/docu/media/10.1_FlowConfigManifestConsumer.png)

Of course it is possible to put some more logic within the code and add additional parameters to set, to control what should happen if somebody creates a flow with this feature and to react on optional parameters:

```
-- Block namespace
local BLOCK_NAMESPACE = 'MQTTClient_FC.Publish'
local nameOfModule = 'CSK_MQTTClient'

--*************************************************************
--*************************************************************

-- Required to keep track of already allocated resource
local instanceTable = {}

local function publish(handle, data1, data2, data3, data4)
  local topic = Container.get(handle, 'Topic')
  local qos = Container.get(handle, 'QoS')
  local retain = Container.get(handle, 'Retain')

  if data1 then
    CSK_MQTTClient.addPublishEvent(data1, topic, qos, retain)
  end
  if data2 then
    CSK_MQTTClient.addPublishEvent(data2, topic, qos, retain)
  end
  if data3 then
    CSK_MQTTClient.addPublishEvent(data3, topic, qos, retain)
  end
  if data4 then
    CSK_MQTTClient.addPublishEvent(data4, topic, qos, retain)
  end
end
Script.serveFunction(BLOCK_NAMESPACE .. '.publish', publish)

--*************************************************************
--*************************************************************

local function create(topic, qos, retain)

  if nil ~= instanceTable[topic] then
    _G.logger:warning(nameOfModule .. ": Instance already in use, please choose another one")
    return nil
  else
    -- Otherwise create handle and store the restriced resource
    local handle = Container.create()
    instanceTable[topic] = topic
    Container.add(handle, 'Topic', topic)
    Container.add(handle, 'QoS', qos)
    Container.add(handle, 'Retain', retain)
    return handle
  end
end
Script.serveFunction(BLOCK_NAMESPACE .. '.create', create)

--- Function to reset instances if FlowConfig was cleared
local function handleOnClearOldFlow()
  Script.releaseObject(instanceTable)
  instanceTable = {}
end
Script.register('CSK_FlowConfig.OnClearOldFlow', handleOnClearOldFlow)
```

#### Data Processor

If the feature should receive, process and forward data, this is a kind of combination of the 2 mentioned methods.

Again, create a LUA file like 'ModuleName_ProcessSomething' within the 'FlowConfig' folder and require this within the 'Mainfolder/Subfolder/[ModuleName]_FlowConfig.lua' as well.

This file should include code like this:

```
-- Block namespace
local BLOCK_NAMESPACE = 'ModuleName_FC.Process'
local nameOfModule = 'CSK_ModuleName'

--*************************************************************
--*************************************************************

-- Required to keep track of already allocated resource
local instanceTable = {}

local function process(handle, dataSource)

  local instance = Container.get(handle, 'Instance')

  -- Check if amount of instances is valid
  -- if not: add multiple additional instances
  while true do
    local amount = CSK_ModuleName.getInstancesAmount()
    if amount < instance then
      CSK_ModuleName.addInstance()
    else
      CSK_ModuleName.setInstance(instance)
      CSK_ModuleName.setRegisterEvent(dataSource)
      break
    end
  end

  -- Here it will check for selected mode. This is only optional
  local mode = Container.get(handle, 'Mode')
  if mode == 'TOTAL_SUBRESULTS' then
    return 'CSK_ModuleName.OnNewStringResult' .. tostring(instance) -- This will return the name of the event, providing relevant data for further processing
  else
    return 'CSK_ModuleName.OnNewResult' .. tostring(instance)
  end
end
Script.serveFunction(BLOCK_NAMESPACE .. '.process', process)

--*************************************************************
--*************************************************************

local function create(instance, mode) -- 'mode' is an extra parameter used in this example but not necessary if not used...

  local fullInstanceName = tostring(instance) .. tostring(mode)

  -- Check if same instance is already configured
  if instanceTable[fullInstanceName] ~= nil then
    _G.logger:warning(nameOfModule .. "Instance already in use, please choose another one")
    return nil
  else
    -- Otherwise create handle and store the restriced resource
    local handle = Container.create()
    instanceTable[fullInstanceName] = fullInstanceName
    Container.add(handle, 'Instance', instance)
    Container.add(handle, 'Mode', mode) -- optional
    Container.add(handle, "CB_Function", "")
    return handle
  end
end
Script.serveFunction(BLOCK_NAMESPACE .. '.create', create)

--- Function to reset instances if FlowConfig was cleared
local function handleOnClearOldFlow()
  Script.releaseObject(instanceTable)
  instanceTable = {}
end
Script.register('CSK_FlowConfig.OnClearOldFlow', handleOnClearOldFlow)

```

As you can see, now it will register to an event to receive data and will return the name of event itself will provide data, so that other modules can react on that.

Manifest can look e.g. like this:

![](/docu/media/10.1_FlowConfigManifestProcessor.png)

### User Interface

As already mentioned, it generally makes sense to create a user interface for a module to enable the app user to use the features of the module even without coding anything or at least to give an impression of internal processes by showing results / values.

If you used the mentioned "blank" module templates to create a module, it will provide already a first version of a user interface, named accordingly to the module name in its own "pages" folder:

![](/docu/media/10.1_UserInterfacePage.png)

It includes an example of how to use the user management feature of CSK to show / hide content within the UI and will provide elements with bindings to the persistent data features of CSK (see [Persistent data](#persistent-data) and [User Management](#user-management) )

If you make use of specific css styles within your module UI, they should be named accordingly within the specific css file.

Please make also use of meaningful element ID names to give an orientation within the UI builder:

![](/docu/media/10.1_UserInterfaceIDs.png)

## CSK_Application

As mentioned, it is possible to additionally create an application specific app to "orchestrate" the features of the other modules to realize the application (for an example you can check out the CSK_ColorSortingInspection application).

If you want to have a customized UI, here are some hints:

1. Normally, when calling the IP of the device in your browser, it will show up the first UI of the app ordererd in an alphabetic order. If you want your application's user interface to be displayed, you can use the following lines of code:

   ```
   local function setAppAsMainWebpage()
     local defaultWebpage = Parameters.get("AEDefaultWebpage")
     if defaultWebpage == nil then
       _G.logger:warning("Device does not support setting the default webpage.")
     else
       assert(Parameters.set("AEDefaultWebpage", _APPNAME))
     end
   end
   ```

2. If you want to integrate the UIs of used modules inside of the navigation bar of your application specific UI, currently it is recommended to create "dummy" UIs to forward to the original UI (it is planned to make this easier in future...). To do this you just need to create an empty UI with an iFrame, e.g. like this:

   ![](/docu/media/10.1_iFrame.png)

   ```
   <iframe src="/#!msdd=CSK_Module_MultiColorSelection&page=CSK_Module_MultiColorSelection&fullscreen=true"
        title="ColorSelection" style="width: 99%; height: 99%">
   </iframe>
   ```

   By doing this, your application will automatically make use of updated UIs if you integrate new versions of the related CSK modules.

   Add entries within the navigation.json e.g. like this:

   ```
   {
     "version": "1.0", 
     "pages": [
       {
         "category": "ColorInspectionSorting Application",
         "pages": [
           {
             "path": "CSK_Application_ColorSortingInspection/CSK_Application_ColorSortingInspection.html",
             "name": "Overview"
           },
   
           {
             "path": "CSK_Application_ColorSortingInspection/CSK_Application_MultiColorSelectionCustom.html",
             "name": "Color Objects"
           }
         ]
       },
       {
         "category": "Setup",
         "pages": [
           {
             "path": "CSK_Application_ColorSortingInspection/CSK_Module_DateTime_Frame.html",
             "name": "Date_Time"
           },
   ...
   ```

   **INFO**: There is also the possibility to copy the UI folder into your app, but if doing this it will become necessary to copy/paste them to your app every time you pull an update of the referenced modules.

   Another way would be to create symbolic links to the pages folder of the modules (see https://supportportal.sick.com/tutorial/programming-sensorapps-symbolic-links/ ):

   ![](/docu/media/10.1_ApplicationUIs.png)

2. It is also possible to combine UIs of modules via iFrames into your application specific UI, e.g.:

   ![](/docu/media/10.1_CustomApplicationUI.png)

## Relevant functions and events

There are some functions and events which are quite relevant for the CSK approach and you should know about to understand the background of the code.

Some were already explained within the other chapters. Following you will find some further explainations.

Mostly all these functions and events will exist for each module per default.

### Function: resetModule

Each module should provide a function to reset itself. This function should stop all processing of the module which are not needed per default.

This function should register to the "CSK_PersistentData.OnResetAllModules" event.

This is very helpful if you run multiple processes in different modules but you want to switch to another application and you don't want that unused processes run in the background.

By notifying the "CSK_PersistentData.OnResetAllModules" event all modules will automatically stop and will wait for a new setup.
	  
### Function: getStatusModuleActive

As CSK modules are generic, it is possible that you can run CSK based applications on different devices.

Some modules and their features might be not usable on specific devices (due to missing APIs) but still can be installed to the device without errors.

Each module should have a function named "getStatusModuleActive" to give feedback about the status if the module can be used on the current used device so that applications can react on this information.

## Documentation

Please document the served events, functions and their parameters within the manifest. This will make it easy to create user-friendly documentation for the module by selecting "Create API documentation" in AppStudio when you deploy the SensorApp.

![](/docu/media/10.1_CreateApiDocu.png)

Result:

![](/docu/media/10.1_ApiDocu.png)

The description of the main CROWN within the app manifest should also include all central information about the purpose of the module and how to use it, e.g. like this:

![](/docu/media/10.1_MainCrownDescription.png)

For non served functions and variables within the code please make use of meaningful "in code" documentation (existing modules can be used as guidance).

![](/docu/media/10.1_InternalCodeDocu.png)

## Persistent Data

As mentioned before, to easily load or save data (LUA tables) of modules persistently, there is also already the "CSK_Module_PersistentData" module available. This module is able to receive and provide parameter datasets of (multiple) other modules and will save the bundled data inside of a binary file within the /public folder.
The "parameters" tables of the modules will be used for this. (see chapter [[ModuleName]_Model.lua without kind of "classes"](#modulename_modellua-without-kind-of-classes) - step 6)

![](/docu/media/10.1_PersistentDataParameterHandling.png)

**INFO:**

- Since using PersistentData version 2.0.0 this binary file also includes the information which dataset should be loaded by what module (optionally by app/device reboot). Before it was necessary to create extra entries within the Parameter file of the PersistentData module for each used module...)*

- Since using PersistentData version 3.0.0 this binary file also includes the information how many instances should be created. By doing this, all information is saved now within the binary file and it is not necessary to store the Parameter file of the PersistentData module additionally anymore.*

It is quite easy to use this feature of this module out of other modules and by using this you can store the parameter setup of your application as a backup by saving this single file.

![](/docu/media/10.1_PersistentDataUI.png)

After the "Engine.OnStarted" event of the AppEngine, this module will load the latest configured dataset (including the parameter datasets) and will inform the other modules via the event "PersistentData.OnInitialDataLoaded" that they can load their specific parameter dataset if wanted.

You will find a pre-implementation to use this feature inside of the "CSK_Module_CreationTemplate" / "CSK_Module_CreationMultiTemplate" projects.

Additionally you could check other modules regarding the implementation of the PersistentData function and the Readme of the PersistentData module.

For further information please have a look into the README of the PersistentData module:

https://github.com/SICKAppSpaceCodingStarterKit/csk_module_persistentdata

## User Management

Another main feature provided as CSK module is the 'CSK_Module_UserManagement'. This module comes with a feature set to create users with related user levels.

Currently it supports the user levels 'Operator', 'Maintainer', 'Service' and 'Admin'.

![](/docu/media/10.1_UserManagement.png)

In general the idea is, that the 'CSK_Module_UserManagement' notifies what user level is currently active. For this it will notify events like 'CSK_UserManagement.OnUserLevel[UserLevel]Active'.

If you used the mentioned "blank" module templates to create a module it will provide the relevant code parts to react on these events.

With this it is possible to react on the active user level and e.g. to show / hide or activate / deactive elements / functionalities within the UI or to react on it within the code.

![](/docu/media/10.1_UserManagementBinding.png)

## Performance

### App parameter "LuaLoadAllEngineAPI"

As mentioned earlier, it is possible to only load the used APIs to reduce the time needed by the garbage collection. To do so, it is necessary that the  manifest of the module includes following meta key:

```
<meta key="LuaLoadAllEngineAPI">false</meta>
```

With this set to FALSE, the AppEngine CROWNs are not loaded automatically and the module needs to take care by itself to load all relevant APIs.

This can be done by using the functions within the "checkAPIs.lua" script file located in the subfolder "helper"."

Inside of this script you need to require all the relevant APIs, e.g. like this:


```
Container = require 'API.Container'
```

Please have a look on this page regarding more background information on the garbage collection topic:

https://supportportal.sick.com/tutorial/lua-garbage-collection/
