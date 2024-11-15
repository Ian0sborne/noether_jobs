This short manual walks you through the steps necessary to run a Python script on Noether.

## Noether Basics

1.  Login to Noether as `ssh [your_username]@noether.hep.manchester.ac.uk`.
2.  Once in, use `ls` to see what is in your directory. **Warning: some newer Noether accounts are not created with this folder (and subsequent ones within it). If you do not have them, please make them (for your sake).**
3.  `cd scripts` to go in `scripts`.
4.  `scripts` has 3 folders (`bin`, `etc`, `out`) which are the launching ground for all of your jobs.
    - `bin` (for binary) contains your executable scripts, i.e. the scripts you want to run.
    - `etc` is the submission folder, i.e. where you set up `.sub` files that send job requests to the cluster. Here is a sample `.sub` file:
      
      ```
      executable      = bin/jupyter.sh
      request_memory  = 8G
      request_cpus    = 4
      request_disk    = 5G
      initialdir	= $ENV(HOME)/scripts
      output          = out/jupyter/jupyter-$(Process).out
      error           = out/jupyter/jupyter-$(Process).err
      log             = out/jupyter/jupyter-$(Process).log
      arguments = $(Process)
      
      
      should_transfer_files = yes
      when_to_transfer_output = ON_EXIT
      
      queue 1
      ```
        - `executable`: the script you want to run.
        - `request_memory`: how much RAM you want to use for the job
        - `request_cpus`: how many CPUs you want to use for the job
        - `request_disk`: how much disk space you want to use for the job
        - `initialdir`: the directory where your `out` (as well as `bin` and `etc`) is.
        - `output` `error` `log`: tells the cluster where to send the `.output`, `.error`, and `.log` files that are created when you run your job. These are sent into the `out` folder and are explained below.
        - `queue`: tells the cluster how many times you want to run your job.
    - `out` contains
        - `.err` files which display any errors that occurred.
        - `.log` files which document the CPU usage of the cluster while the job is running.
        - `.out` files which display the terminal output of the code.

## Running a job on Noether

1.  Copy the script you'd like to run into your `bin` folder by typing the following line in the terminal: `scp [local_path]/[your_script].py [your_username]@noether.hep.manchester.ac.uk:/gluster/home/[your_username]/scripts/bin/`. You will now be asked to input your password and then the script will be copied into your `bin` folder in Noether.
    
2.  Once your script has been copied, login to your Noether account and then go into your `etc` folder by typing: `cd scripts/etc`.
    
3.  Once in the `etc` folder type: `cp sleep.sub [your_script].sub` to create a submissions file in the right template. After that, you should edit the `executable`, `output`, `error`, and `log` entries in the `[your_script].sub` as:
    
    - `executable`: `bin/[your_script].sh`
    - `output`: `out/[your_script]-$(ClusterId)-$(Process).out`
    - `error`: `out/[your_script]-$(ClusterId)-$(Process).err`
    - `log`: `out/[your_script]-$(ClusterId)-$(Process).log`
    
    `-$(ClusterId)` is used to keep track of multiple runs of the same job. Every time you run a job, you run it with a different `ClusterId`, by adding that to your output you can keep track of your run history of a specific script.
    
4.  Then go into `bin` folder (`cd ../bin`) and create an empty submission file by typing: `touch [your_script].sh`.
    
5.  In this file you want to add two things:
    
    - Add a source to a Python environment. You do this by typing: `source /gluster/[path_where_python_environment_is]`.
    - Tell the cluster which file to run. You do this by typing: `python3 /gluster/home/[your_username]/scripts/bin/[your_script].py`
6.  Go back in the `scripts` folder and type: `condor_submit etc/[your_script].sub`.
    
7.  This will run your job.
    
8.  You will see the output of the job in the `out` folder. There will be three new files which have been explained earlier:
    
    - `[your_script]-[ClusterId]-0.out`
    - `[your_script]-[ClusterId]-0.err`
    - `[your_script]-[ClusterId]-0.log`
