 # Table of Contents
[Overview](#overview)

[Software prerequisites](#software-prerequisites)

[Main tasks](#main-tasks)

[Command-line-interface CLI](#command-line-interface-CLI)

[How to use in examples](#how-to-use-in-examples)

[Introduction to modules](#introduction-to-modules)

[Features in the future](#features-in-the-future)

### Overview
This **toyband** model is to provide some insights into Landau fans observed in electronic materials under strong magnetic fields.
The electronic property, i.e., the band structure (Energy versus momentum) of materials can be defined and is assumed to be isotropic for simplicity. 
To dive deeper, please visit [my personal website](http://lixianphwang.com/projects/3_project/).

### Software prerequisites
- Install Python package (v3.8.7 recommended)
- Pip install dependencies list in requirements.txt `pip install -r requirements.txt`
- It is highly recommended to use `virtualenv` to generate an enviroment for this project. Consult Google if not sure how to do this. 
### Command line interface CLI
- If you are the first time to use a CLI:
	Use a teminal (powershell, gitbash for Win user) and switch current directory to `cd ..\toyband>` and start the command with `python` to invoke your python program and the `toybands` module follows. 
	An example is `..\toyband> python xxx.py`.  To access a brief help doc, just add argument `-h` by the end like `python toybands/addband.py -h`. As of now, I will introduce the CLI and its usage, covering common scenarios. Note that you can always interrupt the script by `Ctrl+C` but nothing will be saved after this interruption.
- else: Go to `toyband\` directory.
 ### How to use in examples
 Note that `[]` means a "must", `{}` means optional, and `[{case1},{case2}]` means one of them must exist. All the arguments can be in any order within the command line. 
 - add a band to the system
 `python toybands/addband.py [-density] {-is_dirac} {-is_cond} [-gfactor GFACTOR] [{-dp VF D} {-cp MEFF SPIN}]` 
 - delete band(s) from the system without UI (useful when you run a script)
 `python toybands/delband.py [-i INDEX or all]`
 using UI `python toybands/delband.py`
 - have a peek into the system
 `python tobybands/peeksys.py`
 - plot the E-B relationship of Landau levels
 `python toybands/run.py [-enplot] {-dir DIR} {-fnm FNM} [--enrange Estart Eend Enum] [--bfrange Bstart Bend Bnum] {-nmax NMAX} {-angle ANGLE}` If no `-dir` and `-fnm` is specified, the output figure will be stored in `./DEFAULT_AUTONAME/DEFAULT_AUTONAME.DEFAULT_FORMAT`  
 - plot the n-B relationship of Landau levels
 `python toybands/run.py [-denplot] {-dir DIR} {-fnm FNM} [--enrange Estart Eend Enum] [--bfrange Bstart Bend Bnum] {-nmax NMAX} {-angle ANGLE}`
 - plot the n-B relationship for a set of given densities (specified by  `--allden` and `-nos`) as a simulation of Landau fan chart.
 `python toybands/run.py [-simu] [--allden "NS1 NE1 NS2 NE2 ..."] [-nos NOS] {-dir DIR} {-fnm FNM} [--enrange Estart Eend Enum] [--bfrange Bstart Bend Bnum] {-nmax NMAX} {-angle ANGLE}`

 	Alternatively, one can load densitites stored in columns of a csv file (no header) elsewhere like:
 `python toybands/run.py [-simu] [-loadden path-to-csvfile] {-dir DIR} {-fnm FNM} [--enrange Estart Eend Enum] [--bfrange Bstart Bend Bnum] {-nmax NMAX} {-angle ANGLE}`
 - plot the DOS-B relationship at a fixed chemical potential
 `python toybands/run.py [-dos] {-dir DIR} {-fnm FNM} [--enrange Estart Eend Enum] [--bfrange Bstart Bend Bnum] {-nmax NMAX} {-angle ANGLE}`
 - plot the DOS mapping onto (n,B) for a set of given densities
 substitute `[-simu]` with `[-dosm]` in simulation.
 - plot the data from csv file directly from the CLI
 `python toybands/plotfile.py [-f path-to-csvfile]`
 
### Default settings
You can change many of default settings in IO(path, save), plotting and some model parameters (no need to change most of time) in `toymodel/config.py`.

### Main tasks
- build a band with preset parameters and add it to an existing system (`addband.py`)
- build a system consisting of bands specified above , the system can contain arbitrary number of bands with arbitrary parameters. (`addband.py`)
- delete band(s) from a system. (`delband.py`)
- snapshot the system (`peeksys.py`)
- do some calculations on the system (`run.py`)
	- calculate the density of state (DOS) at arbitrary B-field and energy E given the position of band bottom/a specific set of carrier densities. (They are equivalent in our model)
	- Based on DOS results, draw the chemical potentail line at each B-field
	- plot the E(energy)-B(field) relation of Landau levels
	- plot the n(density)-B relation of Landau levels. Here we need to use the DOS information to map energy (E) to density (n)
	- plot the n-B relation of Landau levels at the chemical potential to show the simulation of experimentally obtained Landau fan chart
	- map the density of state (DOS) at each (B,n)
- replot the results stored in csv file(`plotfile.py`)
### Introduction to modules

##### module `addband` 
`python toybands/addband.py [-option][parameters]`
Add a new band into the system

- `density`: positional, density for this band in units of 1/m^2
- `-is_cond`: optional, is a conduction band
- `-gfactor`: optional, gfactor for this band
- `-dp DP1 DP2`: DP1: fermi velocity (in units of m/s) DP2: D (meV nm^2) for a Dirac-like band. D(kx^2+ky^2) accounts for the deviation from a linear Dirac dispersion.
- `-cp CP1 CP2`: CP1: absolute value of effective mass (in units of rest mass of electron) CP2: spin number (+1 or -1 or 0) for a conventional band. The spin number stands for spin-up (+1), spin-down (-1) and spinless (0), respectively.
- Initialize a system: A system will be automatically initiated upon the creation of the first band.

	
#####  module `peeksys`
`python peaksys.py`
Peek into the system you created

 Once you'd like to see what is in your system, how many bands and their parameters. The above command will print out a summary of bands in table-like format.

 ##### module `delband`
`python toybands/delband.py`
Remove bands from a system

It will prompt a dialogue `which band to delete? Input the index number: Or press 'e' to exit`. Type the number in front of all the parameters within a row for that band you want to delete. Or quit by typing `e`.  In some cases, you need to delete bands in multiple-line scipts, then just using `python toybands/delband.py -i [INDEX or all]`  'all' will let you empty the system. 
 
 ##### module `run`
 `python toybands/run.py [-option][parameters]`
Carry out various tasks in the existing material system.

 - `--enrange`: energy range: start end NumofPoints
 - `--bfrange`: magnetic field range: start end NumofPoints
 - `-enplot`: optional, plot the energy versus bfield
 - `-denplot`: optional, plot the density versus bfield
 - `-simu`: optional, dynamically generate relationship between the density and the bfield at steps of input density (yes/no)
 - `-dosm`:optional, mapping of DOS onto (n,B)
 - `-dos`: optional, plot dos versus bfield
 - `--allden`: optional, densities for each band: start1 end1 start2 end2 ...
 - `-nos`: optional, number of steps in the simulation
 - `-dir`: optional, relative output directory
 - `-fnm`: optional, filename
 - `-nmax`: optional, number of Landau levels to be considered (default=20)
 - `-angle`: optional, angle in degree made with the sample plane norm by the external field (default=0)
 - `-scond`: optional, set Landau level broadening parameter sigma at B = 1 T for conduction bands.  
 - `-sval`: optional, set Landau level broadening parameter sigma at B = 1 T for valence bands.
 - `-zll`: optional, reduce the density of state (DOS) of zero LLs to its half compared to other LLs.

 ### Features in the future
 - Input customized E-B relationship [*]
 - allow separate figures in a single pdf file for a batch input [*]
 - Is it possible to abort the calculation until I continue it? I mean, keep the memory usage but free its CPU usage. It might be helpful when you just want your PC to handle other CPU-heavy tasks during a long calculation. [*]
 - connect the same LL in simu [**] realize via the output .csv to plot lines with the same band, N at once.
