
Email:   jashley9779@gmail.com for any questions.


There are two models for the soluble-lead flow battery included, each in separate files titled "SLFB_MCST.edp"and "SLFB_DST.edp". The former uses moderately concentrated solution theory (based on the Stefan-Maxwell equations) to model ion transport, while the latter uses dilute solution theory (Nernst-Planck equations). Hopefully these models can act as a foundation for further modelling efforts. Also included is a parameter file, which contains the majority of the parameters that the user may want to adjust. The model allows for the user to prescribe an applied current (in Amps) function Iapp(t), where t is time. The model then determines how the system responds to Iapp(t), in particular, it calculates how the cell voltage changes with time.

The solver uses SI base units. E.g., time is in seconds, length in meters, etc. 

 The DST-based model solves significantly faster than the MCST-based model due to its much greater simplicity. It is likely that the MCST-based model solver could be further optimised such that it solves faster.  
      
      
      
              ---
--- Parameter file instructions ---
              ---
Prior to running the main code, the user must choose the parameters they want to use. This file contains: model parameters, initial data, solver tolerances, spatial mesh generation time step-size tolerances, macros for the applied current, and the total desired simulation time. The user must provide the applied current function they want to use. It may be necessary to fiddle with the solver tolerances to get the solver going, although the pre-set values will work well for most cases.





                ---
--- Breakpoint code instructions ---
                ---
The breakpoints are needed to ensure that the full detail of Iapp(t) is captured i.e., they control the time step-size in regions near points of maximum absolute curvature in Iapp(t) and prevents the solving from skipping over important features of Iapp(t). There are two breakpoint codes, both written in Python, and are titled "BreakpointsA.py" and "BreakpointsB.py". Both do the same job, and either can be used. The breakpoint script creates a file titled "BreakpointsForFreeFemCode.txt", which the FreeFem++ scripts then use when selecting the time step-size for each step in the time loop. The user MUST ensure that the breakpoint text file is saved to the correct directory before running main code, or else the solver will not run (i.e., just make sure the file is in the same directory in which the main FreeFem++ code is saved).

The user must provide: (1) the applied current function, Iapp(t),   (2) The first derivative of Iapp(t),   (3) The second derivative of Iapp(t),   (4) The maximum time over which Iapp(t) is to be applied.

The script will then compute the breakpoints. It is worth the user checking to ensure that the breakpoints are in roughly the correct locations before proceeding; i.e., they should be located near the points where Iapp(t) exhibits greatest curvature. To help with this, the script will plot Iapp(t) versus time along with the compute breakpoints.





           ---
--- Solver instructions ---
           ---
Once the parameters have been chosen and the breakpoints computed, the main code is ready to run. Both solvers currently output numerous quantities (e.g., cell voltage, time, etc.) that may be of interest as .txt files into the same directory in which the code is saved; unwanted outputs can easily be prevented by commenting out the relevant lines of code. The solver may fail if the solution undergoes extremely rapid change, e.g., towards the end of a deep discharge or during charge when using a current that is high enough to cause local depletion of reactant concentrations near the electrodes. In such a situation, the solver will print some suggestions of how to get around the issue, which is almost always due to time step-size selection and error control. The solver is capable of resolving regions of very fast evolution, but requires strict error tolerances which will slow down the overall execution. It was observed that the solution obtained using BDF3 can become unstable when the time step-size is forced to be very small; in particular, rapidly growing oscillations were observed in the solution. Therefore, the user has the option of only using BDF1 and BDF2, which were never observed to result in such behaviour.

The finite element functions used by the solver are P1, but can be changed within the main solver files to different varieties. It was found that good results are produced with P1 along with a moderately coarse mesh. The key is to ensure the mesh is dense enough near the two electrodes where the solutions in the ion molarities exhibit sharp boundary layers.

The solvers will work in both the sequential and parallel (MPI) versions of FreeFem++, where it is noted that the solvers do NOT make use of parallel computing, but could be adapted to do so. Users are recommended to use Visual Studio Code, which has a FreeFem++ plugin.



