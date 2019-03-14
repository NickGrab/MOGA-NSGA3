# MOGA-NSGA3

*This software uses the 3rd generation non-dominated search based genetic algorithm (NSGA-III) to train Stillinger-Weber forcefields for use in thermal conductivity simulations. For details related to using the software please follow the instructions below*

<img src="./Media/Picture1.png" width=400>

## USAGE:

### Step 1: Provide data for running the force field fitting
1. Place user-generated phonon dispersion band structure curve(s) in UTIL folder as done in this repo
2. Place cell data corresponding to different states as cell1, cell2, cell3 
3. Put the template forcefield which you wish to train


### Step 2: Compile the nsga3 and workflow code:
1. Go into src/ directory: `cd src/`
2. Modify the following lines in moga.c:

  `int iteration_num = 500;   // number of training iterations`
  
  `int population_num = 300; // population size for ga.in training`
  
  *to the total number of epochs and total population size required for the training*
  
  For example: If the total number of epochs is 300 and population size is 200 then modify as follows:
  
  `int iteration_num = 300;   // number of training iterations`
  
  `int population_num = 200; // population size for ga.in training`
  
  *Once these modifications are done compile the code by running Makefile*
  
  `make`
  
### Step 3: Modify var.in to specify all the input parameters
The structure of var.in is as follows:

`300        # Size of the population (same as population_num in moga.c) `<br/>
`14         # Total number of variables in the force field file that need modification`<br/>
`5          # Total number of objectives` <br/>
`0.8        # Cross-Over Probability`<br/>
`0.2        # Mutation Probability`<br/>
`1.0        # Degree of Mutation between [0-1]`<br/>
`10         # Total number of divisions (will be read from hyperplane.in file, any value here will be ignored)`<br/>
`2.2     5.5     2.8     1.3     0.4     0.24    30.0    10.7    27.0    50.70   15.00   1.60    4.50    74.0 # Lower bound of variables`<br/>
`2.4     5.9     3.0     2.0     0.7     0.38    45.2    16.1    40.7    76.20   23.00   2.41    6.80    85.0 # Upper bound of variables`<br/>
  
 Please modify each line with suitable values consistent with the comments corresponding each line
 
 ### Step 4: Create hyperplane.in file for reference point computation required by NSGA3 algorithm
 
 This can be done by running this code within matlab environment by calling the function <br/>
 `GenerateReferencePoints(M,p)` <br/>
 M = Total number of Objectives <br/>
 p = Total number of divisions  <br/>
 
 This step generates hyperplane.in with appropriate number of reference points

 
 ### Step 5: Running the code
 
 For running the worflow make sure to clean up the workspace first by running <br/>
 `sh cleanup.sh ${N}`
 
 Then run the following command for tuning the force field parameters:
  
  `mpirun -n ${N} ./moga | tee log`
  
  This step runs moga workflow on `${N}` parallel threads, each performing its own calculation independently. Remaining members of the population are assigned to nodes as they are made available when `${N} < population_num` It is recommended to take `${N} = population_num` when possible. The output of the workflow is written to `log`
  
<h2> Example : Forcefields for MoSe<sub>2</sub> monolayers</h2>

The files for this calculation are provided in the Example folder and follow the strategy as mentioned in the paper

### Computed error in specified objectives

<img src="./Media/Picture2.png" width=800>

*Figure: (a) error in lattice constant, (b) error in elastic modulus and (c) error in phonon dispersion curves*
