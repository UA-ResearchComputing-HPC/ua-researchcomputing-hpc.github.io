# Running RStudio Using a Singularity Image

In some circumstances, you may want to run RStudio using your own Singularity image. For example, this allows access to different versions of R not provided when using our OOD application.  

To do this, log into HPC using an [Open OnDemand](https://ood.hpc.arizona.edu/) Desktop session and open a terminal. A Desktop session is the easiest solution to access RStudio since it eliminates the need for port forwarding.

In the terminal, make an RStudio directory where all of the necessary files will be stored. In this example, we'll be working in our home directory and will pull an RStudio image from Dockerhub to use as a test. If you're interested, you can find different RStudio images under [rocker in Dockerhub](https://hub.docker.com/u/rocker).

```console
(puma) [netid@r2u13n2 ~]$ mkdir $HOME/RStudio
(puma) [netid@r2u13n2 ~]$ cd $HOME/RStudio
(puma) [netid@r2u13n2 ~]$ singularity pull ./geospatial.sif docker://rocker/geospatial.sif
```

Next, create the necessary directories RStudio will use to generate temporary files. You will also generate a [secure cookie key](https://docs.rstudio.com/ide/server-pro/1.2.1047-1/load-balancing.html).

```console
(puma) [netid@r2u13n2 ~]$ TMPDIR=$HOME/RStudio/rstudio-tmp
(puma) [netid@r2u13n2 ~]$ mkdir -p $TMPDIR/tmp/rstudio-server
(puma) [netid@r2u13n2 ~]$ uuidgen > $TMPDIR/tmp/rstudio-server/secure-cookie-key
(puma) [netid@r2u13n2 ~]$ chmod 600 $TMPDIR/tmp/rstudio-server/secure-cookie-key
(puma) [netid@r2u13n2 ~]$ mkdir -p $TMPDIR/var/{lib,run}
```

Next, create a file in your RStudio directory called ```rserver.sh``` and make it an executable:
```console
(puma) [netid@r2u13n2 ~]$ touch rserver.sh
(puma) [netid@r2u13n2 ~]$ chmod u+x rserver.sh
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
(puma) [netid@r2u13n2 ~]$ ./rserver.sh
```

