## What is Fiware

Quoting Fiware main site:  
> The FIWARE Community is an independent open community whose members are committed to materialise the FIWARE mission, that is: “to build an open sustainable ecosystem around public, royalty-free and implementation-driven software platform standards that will ease the development of new Smart Applications in multiple sectors”.

EU launched a "fiware accelerate" program to support the developing of fiware related software.  
So, fiware gives developers APIs and components(fiware GEs) to achieve their goals easily and without having to care about these components implementation theirselves.


## Fiware lab

> FIWARE Lab is a working instance of FIWARE available for experimentation.

Here, with a community account available on fiware lab, we could deploy two VMs for free, with the two components we needed. Fast and simple.  
It is a very nice adding to fiware capabilities that simplifies even more developers life.  
No need to use docker, ngrok, or anything else. If you're granted a community account you are ready to start deploying your own VM instance, selecting from lots of images available, or creating your own.


## Aforementioned used components inside the app

* **Orion Context Broker**:  
thanks to this component, we were able to "subscribe" certain application (in our case fiware Proton CEP) to whatever change happened to entities stored on the ContextBroker, automatically.  
Practically, we stored needed entities (eg: internal/external temperature) on Orion, and we updated their values when needed. By having subscribed Proton CEP to every change on Orion, every time an entity has its values updated, a notification event is sent to Proton CEP. 
Simple and really useful.  

* **Proton Complex Event Processing**:  
Proton is an engine that waits for incoming events, and once received, evaluates some rules. If any rule is met, an output event is generated and directed to a "consumer" of the event.
Rules can be easily generated through AuthoringTool web interface, that lets users create their own rules starting from templates, with a simple UI.  


![CB-CEP integration](assets/CB-CEP.png)

## Problems encountered:

Unfortunately, we also encountered some issues with these components:  
* recent Orion versions dropped xml support, but we discovered that Proton CEP only expects incoming events to be in xml format. This thing is not clearly stated anywhere.
So we had to use an old Orion version: 0.28.  
* Proton docker image has some issues: AuthoringTool is not able to export the created rule inside its correct path on the fs, so you need to manually creating a rule in the right path and pasting inside it the rule.  
Secondly, it seems it expects tomcat7 to be placed in "/usr/share/tomcat7/", while path on the docker image is indeed "/var/lib/tomcat7".  So we had to manually symlink "/var/lib/tomcat7" to "/usr/share/tomcat7" to make it work.  
* Lastly, documentation is not always precise and up to date (specially Proton's one).
