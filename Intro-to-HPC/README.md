# Intro to HPC

[![](/Images/Download-Button.png)](intro-to-HPC.tar.gz)

This short training class is available to users periodically throughout each academic year. Information on upcoming workshops can be found in our [training documentation](https://public.confluence.arizona.edu/display/UAHPC/Training). Before each workshop, an email will be sent to all HPC users through hpc-announce with information on dates, times, location, and how to register.

This training course will help you get started on using HPC resources.  If you have recently started, you might learn there is a better way, or there are capabilities you are not taking advantage of. You may also want to go through our [quick start](https://public.confluence.arizona.edu/display/UAHPC/Puma+Quick+Start) for additional information.

A PDF of the slides from past workshops can be found in our training documentation linked above. Instructions and training material for the interactive component of this workshop can be found below.

# Accessing the Data

To begin, start a terminal to log into the system and copy the necessary files into your account. If you’re unsure of how to use or access a terminal, see our online documentation for information (or, if you’re in a live workshop, flag one of us down and we can help). To get the files you need, use the following commands:

```
ssh your_netid@hpc.arizona.edu
shell
ocelote
mkdir intro_to_hpc
cd intro_to_hpc
cp /xdisk/chrisreidy/workshops/* .
```
If you get a permission denied message using cp, you likely haven’t been added to the group chrisreidy. If this is the case, stay in your intro_to_hpc directory and use:
```
wget https://ua-researchcomputing-hpc.github.io/Intro-to-HPC/intro-to-HPC.tar.gz
tar xzvf intro-to-HPC.tar.gz --strip-components=1
rm intro-to-HPC.tar.gz
```



*****
[![](/Images/home.png)](https://ua-researchcomputing-hpc.github.io/) 
[![](/Images/back.png)](../)
