# OpenALPR MVC


OpenALPR MVC integrates the OpenALPR library into an MVC Application with the required DLLs, settings and hassle. Only a few tweaks in the IIS is needed and you're ready!

Creator (Me): https://www.linkedin.com/in/kevin-jensen-petersen-740a5a73/ (Feel free to connect!)

---

# Requirements

  - IIS (Internet Information Services)
  - Visual Studio (or another IDE that can compile .NET/MVC Applications)
  

# How to use (Guide)
1. Download this Repository (OpenALPR MVC)
2. Open IIS and create a Website that points to the Repository folder 
![Create Site](http://kevinjp.dk/openalprmvc/images/IIS_Create_Site.PNG "Create site")
3. Go to the "Application Pools" and click on the one that is attached to your newly created website (It usually have the same name as you gave the website) 
![Application pools](http://kevinjp.dk/openalprmvc/images/IIS_Select_Apppool.PNG "Application pools")
4. In the right side of IIS, click the button "Advanced Settings"

![Advanced settings](http://kevinjp.dk/openalprmvc/images/IIS_AdvancedSettings.PNG "Advanced settings")

5. Find the option "**Enable 32-Bit Applications**" (second from the top) and set it to "**True**"
![Set to true](http://kevinjp.dk/openalprmvc/images/IIS_32bitapplicationstrue.PNG "Set to true")
6. Run the website you created (Either by localhost or if you attached a binding)
7. Voila! You have OpenALPR integration in MVC!
![Voila](http://kevinjp.dk/openalprmvc/images/IIS_Voila.PNG "Voila")


# Technical Guide (How was it achieved)
It took countless hours to figure out, why OpenALPR doesn't by default worked in an MVC Application. It worked in an Windows Forms or Console Application, but not web. That's why I sat a goal of figuring out what was going on.

## x86 vs x64
An important thing to note when working with web, is that OpenALPR only works with x86 (32- bit) in a IIS environment (as far as I researched), which for many results in the following error, because they use the 64- bit DLLs:
```csharp
Could not load file or assembly 'openalpr-net.DLL' or one of its dependencies. The specified module could not be found
```
However, you may have tried to use the x86 version aswell, and this resulted in the same error. Theres a reason for that.

## IIS Application Pool
By default, your computer determines if IIS uses 64 or 32- bit for it's Application Pools. On most computers, it's always the case that this is 64- bit. As we covered above, OpenALPR only runs in x86 when it comes to IIS, so how do we tell IIS to use 32- bit application pool? Simple.
1. Go to your Application Pool and click on it
2. In the right side, there's a button called "Advanced Settings", click on that
3. Now a window appears with alot of settings. The second option from the top is called "Enable 32-Bit Applications". This is by default set to **False**, so by changing this to **True** we will be one step closer to having OpenALPR run, but we're not there yet.

## Dependencies
Now, since we got covered that x86 (32- bit) is the only version that runs for an MVC Application, and that we changed the IIS Application Pool to use 32-bit applications instead of 64-bit, there's a last step to this whole thing, before we can get OpenALPR to run without errors.

This is it's missing **Dependencies**. 

A requirement of OpenALPR is that Microsoft Visual C++ is installed on the machine that should run the application, but even if this is the case, MVC has a weird way of loading binary files (dlls) which results in OpenALPR complaining about missing dependencies still.

To fix this, we need to do a couple of things:
1. Change the Web.config of the MVC Application to not make a shadow copy of the required binaries at build-time, as this weirdly enough doesn't work.
2. Copy all the required dependencies of **openalpr-net.dll** into the "**bin**" folder of the MVC application.

##### Web.config change
In the Web.config, you need to add this line of code, in the **<system.web>** section:
```xml
<system.web>
    <hostingEnvironment shadowCopyBinAssemblies="false"/>
</system.web>
```
What this does is disabling shadow copying of binary assemblies from the Windows System32 folder, as we we'll copy these files ourself.

##### Copying the required dependencies
Here's a list of all the required dependencies that needs to be copied into the "**bin**" folder of the application. These DLL files are already placed in the OpenALPR MVC Repository.

**Important to note:** All of these DLLs HAS to be the 32-bit (x86) version of the DLL, not the 64- bit version.

| DLLs |
| ------ |
| openalpr-net.dll |
| openalpr.dll | 
| openalprjni.dll |
| openalprpy.dll |
| opencv_ffmpeg300.dll |
| opencv_ffmpeg300_64.dll |
| opencv_world300.dll |
| concrt140.dll |
| kernel32.dll |
| liblept170.dll |
| mscoree.dll |
| msvcp140.dll |
| vcamp140.dll |
| vccorlib140.dll |
| vcomp140.dll |
| vcruntime140.dll |
| ws2_32.dll |


# Questions
Feel free to ask any questions you might have about integration or if you have an issue with this guide or project, by creating an **Issue** on this Github page. I'll be very active so expect an answer within 24 hours.

You are also very welcome to ask me about integrations in other platforms, frameworks or languages. I'd be happy to help.


