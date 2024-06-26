# Heterogenous Agents Model with Frictions

The subfolder solves three-dimenisonal heterogenous-agent production economies in sections 5.3.1, 5.3.2, 5.3.3 using neural nets.

## Scripts Overview

1. **run_1.sh**
   - **main_BFGS.py**: Solves the model RF / SG / PR using the scipy L-BFGS-B solver.
     - **utils_para.py**: Generates parameters in the model
     - **utils_training.py**: Defines HJB and FOC loss functions and BFGS training function
     - **utils_DGM.py**: Neural nets basic structures
2. **run_2.sh**
   - **main_variable.py**: Evaluates the solved models on validation sets, calculate stationary densities and validation errors
3. **run_3.sh**
   - **main_pde_shock_elasticity.py**: Calculates shock elasticities by solving PDE
     - **utils_pde_shock_elasticity.py**: Contains source files for continuous-time shock elasticity PDE computations.
4. **run_4.sh** (run_3.sh and run_4.sh can be run simultaneously)
   - **main_sim_shock_elasticity.py**: Calculates shock elasticities by simulation
     - **utils_sim_shock_elasticity.py**: Contains source files for continuous-time shock elasticity simulations.
5. **results.ipynb**: Load model solution, plot risk prices, equilibrium varibles, shock elasticities.

## Generated Directory Structure

Running the above bash scripts organizes the outputs and logs into specific directories to streamline troubleshooting and monitoring of script execution:

1. **job-outs**
   - Contains all log and error files associated with the script runs.
     - **run_'seed'.err**: Generated by run_1.sh, records the training error information
     - **run_'seed'.out**: Generated by run_1.sh, records the training time information
     - **val_'seed'.err**: Generated by run_2.sh, records the validation error information
     - **val_'seed'.out**: Generated by run_2.sh
     - **ela_'seed'.err**: Generated by run_3.sh
     - **ela_'seed'.our**: Generated by run_3.sh
     - **simela_'seed'\_W\_percentile.err**: Generated by run_4.sh
     - **simela_'seed'\_W\_percentile.our**: Generated by run_4.sh
2. **bash**
   - Stores sbatch files for each parameter set, which are used to submit jobs to a computing cluster.
3. **output**
   - Holds the computed model solutions and other outputs.
     - **sim_NN**: Contains the solved neural network models.
     - ***_NN.npy**: Contains state variables, equilibrium variables, stationary densities, and important derivatives, all evaluated on state-space validation sets.
     - ***_MSE.npy**: Contains validation errors
     - **NN_info.json**: Contains neural net structure and training information
     - **eva_V_*.npz**: Contains evaluations of important state variables, equilibrium variables evaluated on specific percentile validation sets 
     - ***.npz**: Contains shock elasticities
4. **data**
   - Contains all parameters used in the models, generated automatically

## Estimated Running Time
1. **run_1.sh**: < 3 mins
2. **run_2.sh**: < 3 mins
3. **run_3.sh**: < 15 mins
4. **run_4.sh**: < 2 mins (tested on 45 cores)

Each task was tested on a single core of Intel Xeon Gold 6248R using the parameters in the bash file, with multiple tasks run simultaneously, except for **run_4.sh**, which was tested on 45 cores.

## Neural Networks vs. Finite Difference 

Neural networks and finite difference methods both provide solutions to the heterogeneous-agent model presented in the paper. Neural networks are faster; they do not rely on boundary conditions and capable of extending to solve a four-dimensional model with idiosyncratic volatility (not included in this repository). Finite difference solutions, on the other hand, are stable and can generate monotonic decreasing capital risk prices for the model IP, which neural networks cannot achieve directly. Therefore, we chose to use the finite difference method to produce results for the model IP.

## Shock Elasticity PDE vs. Simulation Method

Due to its parallelization potential, the simulation method achieves high speed in shock elasticity computation. When the initial points are not near the boundary, both simulation and PDE methods generate similar shock elasticity. However, PDE methods are influenced by the specified boundary conditions. For example, the capital shock price elasticity remains relatively flat when the initial expert wealth is 0.012 in model RF and SG, whereas the simulation method exhibits a stronger mean reversion, see online appendix for details.
