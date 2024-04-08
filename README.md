The following is a copy of the README file contained in https://www.doi.org/10.5281/zenodo.10937557


# **Contents**
This is an artifact used for experiments in the CAV 2024 paper “Lexicographic Ranking Supermartingales with Lazy Lower Bounds”.

**[Artifact summary]** This artifact runs automated synthesis algorithms of lexicographic ranking supermartingale that verify almost-sure termination of probabilistic programs. The artifact contains 4 variants of the algorithm, namely two instances of the algorithm proposed in the paper (**SMC** and **EMC**), and two baselines (**STR** and **LWN**).

**[Feature summary]** Our new algorithm (Alg. 1 in P34 of the paper) admits a weaker non-negativity of LexRSMs to be synthesized, which improves its feasibility compared to baselines. The most relevant parts of the source code are 
- the *if* branch that corresponds to Line 6-14 in Alg. 1 of the paper (Line 1314 in `main_alg2.cpp`; Line 1375 in `main_alg3.cpp`)
- the function `generate_equations()`, which declares LP constraints

**[Relation to the paper]** In Section 7 of the paper, we performed experiments by running 4 algorithms above for a benchmark, whose results are given in Table 1 (p17) and Table 2 (p36-39). We also reported in p17 the numbers of benchmark programs for which each algorithm successfully verified almost-sure termination of programs (**STR** = 98; **LWN** = 105; **SMC** = 119; **EMC** = 119). The artifact contains necessary materials to reproduce these results.


# **Artifact Requirements**

**[Resource requirement]** 
Below we provide environments we tested, together with the running time of the main script `runAllExp.sh` under them. The main script has an option to replicate a subset of the results (the `-fast` option). For details, see *The main experiments* section below.
- Mobile laptop (Intel Core i7-1260P, 2.10 GHz, 12 cores / 16GB RAM), 12 cores/4GB to the VM => 7.5 hours with the `-full` option; 2.5hours with the `-fast` option 
- Gaming laptop (Intel Core i9-13900HX, 2.20GHz, 24 cores / 16GB RAM), 24 cores/4GB to the VM => 2 hours with the `-full` option

**[Estimated running time of evaluation task]** See also each section in *How to replicate the experiment result* and *How to confirm the replication*.
- Installation: 10-15min
- Smoke test: 2-3min (if you encounter no error)
- Main experiment: 2-8hours, depending on your machine spec
- Confirmation of replication: about 5min


# **Strture and Content**
An explanation on files in this repository is as follows.

- `README.md`    ... this file
- `examples`    ... the benchmarks of the experiment
  - `ForExperiments`    ... benchmarks with "p.l. = False, p.a. = False" in Table 2 of the paper
  - `probloops`    ... benchmarks with "p.l = True, p.a. = False" in Table 2 of the paper
  - `probAssignAndWhile`    ... benchmarks with "p.l = True, p.a. = True" in Table 2 of the paper
  - `counterex`    ... benchmarks *counterexStr1* and *counterexStr2*
- `files`    ... some resources used to compile the codes
- `results`    ... (to be generated by `runAllExp.sh`) the folder to store the experiment log
- `result_table`    ... (to be generated by `runAllExp.sh`) the folder to store the result tables
- `main_alg[algorithm number].cpp`     ... the main code that implements the algorithm (the algorithms are numbered as: **STR** = 0, **LWN** = 1, **SMC** = 2, and **EMC** = 3)
- `runAllExp.sh` ... script to run the experiment, and generate the result tables
- `compile.sh` ... script to compile the codes

# How to replicate the experiment result

The following procedure has been tested over a virtual machine with Ubuntu 22.04.3.

## Installation

- Clone this repository to your environment.
- Install CPLEX (https://www.ibm.com/products/ilog-cplex-optimization-studio/cplex-optimizer).
  - Academic licence is available for free (registration necessary; cf. https://community.ibm.com/community/user/ai-datascience/blogs/xavier-nodet1/2020/07/09/cplex-free-for-students)
  - There is also the Community Edition for free, with the 1,000 cap on the number of variables/constraints (cf. https://www.ibm.com/products/ilog-cplex-optimization-studio/pricing). For complete replication of the experiment result, the full version is necessary; but Community Edition also does most part of it. See the section *How to confirm the replication*.
  - Download the installer, and run it to install the software. 
  - CPLEX is called via the script `$/Desktop/implementation/files/script.sh`. Update the path info in Line 3 if necessary.
  - We checked the artifact correctly reproduces the experiment results with CPLEX 12.7.1. We also checked with the Community Edition 22.1.1; with this version, we were able to reproduce the same success counts as the unrestricted version, while we observed the algorithm aborts in a few instances whose results are "False" with the unrestricted version.  

- Install ASPIC (http://laure.gonnord.org/pro/aspic/aspic.html).
  - Download the binary at http://laure.gonnord.org/pro/aspic/aspicV3.4
  - Put the binary at `$/Desktop/implementation/files`
  - We checked the artifact works correctly with ASPIC v3.4.

## Smoke test
- Open terminal in desktop, do `cd $/Desktop/implementation`. 
- Do `./compile.sh 0` to compile the code (for the **STR** variant). If there is nothing wrong, a file `alg0.out` will be generated in the current directory.
- Do `./alg0.out < examples/ForExperiments/easy1.prob` to run an experiment on a single benchmark. If there is nothing wrong, you will see something like the following on the terminal:

```
Time:0.000512
Parsing over and CFG constructed
The CFG constructed has 8 states
Time:0.001047
Fast file printed.
Time:0.077487
Invariants generated using aspic
Time:0.077749
Invariants read and equations generated.
Time:0.118815
Found a solution of dimension 1.
Total time taken: 0.118815
Time taken by aspic: 0.07644
```

## The main experiments
- Open terminal in desktop, do `cd $/Desktop/implementation`.
- Do either `./runAllExp.sh -full` or `./runAllExp.sh -fast` to run the experiment, depending on the PC spec (see also the examples in the previous section to estimate the runtime). The former performs the experiments with all combinations of benchmarks and algorithms that are tried in the paper, while the latter does all but the most time-consuming case (benchmark = *complex* with probabilistic loop, algorithm = **EMC**). 
- The script first generates the experiment log in `$/Desktop/implementation/result`. Different files are generated for each benchmark set and algorithm; their names are of the form `Output_[folder name of the benchmark]_alg[algorithm number]`, where the algorithms are numbered as: **STR** = 0, **LWN** = 1, **SMC** = 2, and **EMC** = 3.
- The script then generates two result tables `table1.csv` and `table2.csv` in `$/Desktop/implementation/result_table`. To browse them on the terminal, do `column -s, -t [file path]`.

# How to confirm the replication
- `table1.csv` and `table2.csv` replicates Table 1 and Table 2, respectively. These csv files respect the structure of tables in the paper, so confirming the replication should be straightforward. `table2.csv` also contains the success count of each algorithm at its end, so one can check if they matches with the number in the paper. 

- The expected replication result with each option is as follows.
  - By running `./runAllExp.sh -full` with the full version of CPLEX, the artifact should completely replicate the feasibility result in the paper (i.e., the every value in "Synthesis result" in Table 1 and "dim." in Table 2 should be replicated). 
  - If `./runAllExp.sh -fast` is used instead, it should skip the experiment of just one benchmark (Model = *complex*; p.l. = True; p.a. = False; Algorithm = **EMC**) and the corresponding result should be displayed as "N/A". The success count of **EMC** will also decrease by one.
  - If the Community Edition of CPLEX is used instead, then the results on successful benchmarks should be still completely replicated, while the results on a couple of unsuccessful ones should be a failure due to the variables/constraints number excess instead of failing a LexRSM synthesis.


# Additional artifact description

## How to write a new input program (+ some tips)
- Declare all the variables in the beginning of the program as
 `var <variable-1>`, `<variable-2>`, ...;
- After variable declaration, write the program in the format described in the paper. But, for probabilistic assignments, use the following syntax instead:
  - The syntax of uniform distribtions is `[lower_bound,upper_bound]`. Ex: `x:=x+[-1,1]`. 
  - For a distribution with a mean m, lower bound lb (can be -infinity) and upper bound ub (can be infinity), the syntax is `[m,lb,ub]`. Ex: `x:=x+[0,-infty,infty]`, where `[0,-infty,infty]` describes e.g., the normal distribution.
- The invariants if provided, maually should be put in before the statements in square brackets. The invariant provided at start is particularly useful because it is provided into the invariant generation tool and hence can propagate to other nodes in the pCFG as well.
- The code calls the implementation/files/invariant_script.sh to call the invariant generator executable i.e. aspic. Sometimes changing the delay and descend paramters help in finding out the LexRSM in this tool. Use aspic help to know usage. Or read its documentation.

## How to perform an experiment for specific benchmark and algorithm
- Do `cd $/Desktop/implementation` and then `./compile.sh [algorithm number]` to compile the code of a specific algorithm, where the algorithms are numbered as: **STR** = 0, **LWN** = 1, **SMC** = 2, and **EMC** = 3. If nothing is wrong, a file `alg[algorithm number].out` will be generated.
  - For example, do `./compile.sh 0` to compile the code of the STR variant and generate `alg0.out`.
- Do `./alg[algorithm number].out < [path to the input program]` to perform the experiment. The result appears on the terminal.
  - For example, `./alg0.out < examples/ForExperiments/easy1.prob` performs the experiment for: Model = *easy1*; p.l = False; p.a. = False; algorithm = **STR**.
- If there is no error, the algorithm reports either a success of LexRSM synthesis with its dimension, or a failure of synthesis with the number of iteration tried. Examples of execution logs are as follows.
```
Time:0.000512
Parsing over and CFG constructed
The CFG constructed has 8 states
Time:0.001047
Fast file printed.
Time:0.077487
Invariants generated using aspic
Time:0.077749
Invariants read and equations generated.
Time:0.118815
Found a solution of dimension 1.
Total time taken: 0.118815
Time taken by aspic: 0.07644
```

```
Time:0.012135
Parsing over and CFG constructed
The CFG constructed has 15 states
Time:0.012427
Fast file printed.
Time:0.085419
Invariants generated using aspic
Time:0.085668
Invariants read and equations generated.
Time:0.110717
No solution found, stopped after 2 iterations
Total time taken: 0.110717
Time taken by aspic: 0.072992
```

- When one uses CPLEX Community Edition and the algorithm generates an LP problem that exceeds the cap on the variables/constraints, the algorithm aborts with the following message (this happens e.g. when one runs `./alg0.out < examples/ForExperiments/sipmamergesort.prob`).
```
The problem is too large for CPLEX community edition!
```

## Notes on running the main script
- When you run the algrithm, you maybe meet the error : *error while loading shared libraries: libmpfr.so.4: cannot open shared object file: No such file or directory*. You can resolve by typing *sudo ln -s /usr/lib/x86_64-linux-gnu/libmpfr.so.6 /usr/lib/x86_64-linux-gnu/libmpfr.so.4* in the terminal.
- The computation of `sipmamergesort`of **EMC** is aborted by our experiment platform due to *out of memory*; So, when you run **EMC**, you will get a interrupt in your terminal.
- The correctness of the algorithm **STR** is guaranteed only when the probabilistic assignments in the input program are done by bounded-support distributions. The benchmark `counterexStr1` does not satisfy this requirement, and therefore, the script manually makes the result “N/A” for this instance (cf. line 72-74 of `CollectResult.py`).
- The following logs will be created in the `files` directory, but will not be maintained for every experiment instance. To inspect them for a specific experiment instance, run it individually (cf. *How to perform an experiment for specific benchmark and algorithm*).
  - `aspic.fast` ... the pCFG (program model) generated from the input program
  - `equations.lp[dim]` ... the successfully solved LP problem in the [dim]-th iteration of the algorithm (if exists). Note that **SMC** and **EMC** may try multiple LP problems in a single iteration. When no LP problems were successfully solved in a given iteration (in with case the algorithm reports failure and terminates), the file is the last LP problems tried.
  - `EquationsOutput[dim]` ... the value of the LexRSM in the [dim]-th dimension
  - `InvariantOutput` ... the invariant generated by ASPIC


# References
This artifact, including this README, has been made by modifying the existing one by S. Agrawal et al.: 
- https://github.com/Sheshansh/prob_termination

It is the implementation used in the following paper:
- S. Agrawal, K. Chatterjee, P. Novotny. *Lexicographic ranking supermartingales: an efficient approach to termination of probabilistic programs*. Proc. POPL 2018. https://dl.acm.org/doi/10.1145/3158122
