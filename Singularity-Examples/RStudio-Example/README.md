# Running RStudio Using a Singularity Image

In some circumstances, you may want to run RStudio using your own Singularity image. For example, this allows access to different versions of R not provided when using our OOD application.  

To do this, log into HPC using an [Open OnDemand](https://ood.hpc.arizona.edu/) Desktop session and open a terminal. A Desktop session is the easiest solution to access RStudio since it eliminates the need for port forwarding.

In the terminal, make an RStudio directory where all of the necessary files will be stored. In this example, we'll be working in our home directory and will pull an RStudio image from Dockerhub to use as a test. If you're interested, you can find different RStudio images under [rocker in Dockerhub](https://hub.docker.com/u/rocker).

```console
[netid@gpu22 ~]$ mkdir $HOME/RStudio
[netid@gpu22 ~]$ cd $HOME/RStudio
[netid@gpu22 RStudio]$ singularity pull ./geospatial.sif docker://rocker/geospatial.sif
```

Next, create the necessary directories RStudio will use to generate temporary files. You will also generate a [secure cookie key](https://docs.rstudio.com/ide/server-pro/1.2.1047-1/load-balancing.html).

```console
[netid@gpu22 RStudio]$ TMPDIR=$HOME/RStudio/rstudio-tmp
[netid@gpu22 RStudio]$ mkdir -p $TMPDIR/tmp/rstudio-server
[netid@gpu22 RStudio]$ uuidgen > $TMPDIR/tmp/rstudio-server/secure-cookie-key
[netid@gpu22 RStudio]$ chmod 600 $TMPDIR/tmp/rstudio-server/secure-cookie-key
[netid@gpu22 RStudio]$ mkdir -p $TMPDIR/var/{lib,run}
```

Next, create a file in your RStudio directory called ```rserver.sh``` and make it an executable:
```console
[netid@gpu22 RStudio]$ touch rserver.sh
[netid@gpu22 RStudio]$ chmod u+x rserver.sh
```
Open the file in your favorite editor and enter the content below. Modify the variables under USER OPTIONS to match your account if necessary. You can change PASSWORD to any password you'd like to use. Once you've entered the contents, save and exit:
```bash
#!/bin/bash
 
# --- USER OPTIONS --- #
WD=$HOME/RStudio
SIFNAME=geospatial.sif
PASSWORD="PASSWORD"
 
# --- SERVER STARTUP EXECUTED BELOW --- #
NETID=$(whoami)
TMPDIR=$WD/rstudio-tmp
SIF=$WD/$SIFNAME
PASSWORD=$PASSWORD singularity exec -B $TMPDIR/var/lib:/var/lib/rstudio-server -B $TMPDIR/var/run:/var/run/rstudio-server  -B $TMPDIR/tmp:/tmp $SIF rserver --auth-none=0 --auth-pam-helper-path=pam-helper --server-user=$NETID --www-address=127.0.0.1
```
Now, in your desktop session's terminal, execute the rserver.sh script using:
```console
[netid@gpu22 RStudio]$ ./rserver.sh
```
<img src="Screen Shot 2022-04-18 at 11.17.25 AM.png" alt="execute-rserver" width="600"/> 

Next, open a Firefox window and enter ```localhost:8787``` for the URL. In your browser, you will be prompted to log into your RStudio server. Enter your NetID under Username. Under Password, enter the password you defined in the script server.sh.

<img src="Screen Shot 2022-04-18 at 11.17.49 AM.png" alt="access-from-firefox" width="600"/> 

This will open your RStudio session:

<img src="Screen Shot 2022-04-18 at 11.26.37 AM.png" alt="rstudio" width="600"/> 

