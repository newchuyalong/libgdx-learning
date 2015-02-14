# Third Party Extension Support in the LibGDX setup
---
  
  
The LibGDX setup includes a section for 3rd party extensions, these are extensions made by members of the community that aren't managed by the LibGDX development team.  This gives users an easy way to generate projects that depend on these 3rd party extensions without having to edit build scripts themselves. (Not that that is terribly difficult). 

## How to get your extension in the LibGDX setup
### Requirements
* [A living breathing extension](#am-i-an-extension)
* [Approval by the LibGDX core development team](#approval)
* [Extension definition in the LibGDX setup repository](#extension-defintion)
  
----
  
### Am I an extension?
As much as you may try... no you are not (Unless you are an advanced AI extension, in which case, citation needed), but you may be developing one! 

Does your project aim to extend LibGDX with a specific goal in mind?
Is your project useful to LibGDX users? 
Is your project well established?
Is your project being pushed to Maven central?

Congratulations! It's an extension!

 
### Approval
To get your beautiful extension in the setup, you must sneak past/bribe/bewitch the LibGDX developers into thinking that your extension belongs in the setup. 

TODO: What is required for approval

### Extension definition
We use a simple xml file in the LibGDX core repository to define external extensions.  

The file can be found [here](https://github.com/libgdx/libgdx/blob/master/extensions/gdx-setup/src/com/badlogic/gdx/setup/data/extensions.xml)

An example of this file:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<extensions>
    <extension>
       <name>My Extension</name> //Extension name
       <description>What my extension does</description> //Short description of your extension
       <package>my.package.cheeky</package> //Package name
       <version>0.0.1</version>             //Current release version of your extension
       <compatibility>1.5.3</compatibility> //Latest version of LibGDX your extension is compatible with
       <website>http://mywebsite.com</website>  //Url of your extension, either your extension website/github
       <projects>
           <core>                                 //All dependencies for the core project
               <dependency>groupId:artifactId</dependency> //A single dependency
           </core>
           <desktop>
               <dependency>groupId:artifactId:classifier</dependency> //A single dependency
           </desktop>                                          //All dependencies for the desktop project
           <android></android>                                //All dependencies for the android project
           <ios>null</ios>                                    //All dependencies for the ios project
           <html>groupId:artifactIdTwo:classifierTwo</html>   //All dependencies for the html project
       </projects>
    </extension>
</extensions>
```

This provides all the information required to display your extension and add it to user's projects in the setup.  

You __must__ provide all the data shown above, appended to the extensions.xml file and submit your addition as a PR.




