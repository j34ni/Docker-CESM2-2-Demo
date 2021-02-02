# Docker-CESM2-2-Demo


This presentation was prepared for a demonstration (done online at a *NorESM kafe* on 2020-02-02) of the use
of Docker containers with NCAR's ESCOMP/CESM2.2 image [on the Google Cloud Shell](https://shell.cloud.google.com):

![alt text](img/Cloud_Shell_Editor.png "Cloud Shell")

Docker is already available and we only have to pull the image from DockerHub:

```
docker pull escomp/cesm-2.2:latest
```
![alt text](img/Pulling_from_escomp.png "Pulling the image")

and if everything went well this is what you should get


![alt text](img/Docker_image_downloaded.png "Download finished")

We can now start the container in detached mode:

```
docker run -td escomp/cesm-2.2:latest
```
To for example check the version of the operating system inside the container (Centos-8 in this case).
![alt text](img/Docker_run.png "Docker run")

and start an interactive processe like a bash shell 
(Notice that *-t* allocate a pseudo-tty and *-i* keeps STDIN open even if not attached) 

```
docker exec -it 192 bash
```
![alt text](img/Docker_exec.png "Docker exec")
The promp has now changed to **[user@cesm2.2 ~]$** to show that we are in the container.

To for example check the version of the operating system inside the container (Centos-8 in this case).

```
cat /etc/os-release
```
![alt text](img/Bash_shell.png "Bash shell")

__The container comes with CESM-2.2 and all the software environment necessary to run it, 
including compilers and libraries.__

This particular virtual machine only offers limited resources to effectively run an experiment, therefore we will
only try a simple model configuration 
[Moist baroclinic wave with Kessler microphysics](https://www.cesm.ucar.edu/models/simpler-models/fkessler/index.html) and use a coarse resolution:

![alt text](img/T31_g37.png "From https://www.cesm.ucar.edu/models/cesm2/config/grids.html")

To create a new case:
```
create_newcase --case fkessler --compset FKESSLER --res T31_g37 --run-unsupported
```
![alt text](img/Create_newcase.png "Create new case")

Since there are only 2 processors on this machine the number of tasks has to be decreased (the default is 256), and we will run the simulation for 1 day only (instead of 5 days). To do so we go in the case directory and use the *./xmlchange* script:
```
cd /home/user/fkessler
./xmlchange NTASKS=2
./xmlchange STOP_N=1
```
Then we set-up, build and submit the case:
```
./case.setup
```
![alt text](img/Case_setup.png "Case setup")
```
./case.build
```
The compilation should take less than 6 minutes and hopefully end with the message "MODEL BUILD HAS FINISHED SUCCESSFULLY".
![alt text](img/Case_build_successful.png "Case built successfully")
```
./case.submit
```
The missing data is downloaded first, the run begins, finishes after a minute or so and the output files are archived (do not worry about the warning):

![alt text](img/Run_finished.png "Run finished and data archived")

The timing information shows that the model throughput was 8.14 simulated_years/day.
![alt text](img/Timing.png "Timing")
