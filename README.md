# NIDMOWR
This repository hosts the source code of the NIDMOWR model, as described in the following article : "Reference to add"

# I.Introduction
The non-intrusive dual-media observation well representation (NIDMOWR) model non-intrusively postprocesses time series of total water head respectively in the fracture and in the matrix media at the vicinitude of an observation well, as simulated by a planar bidimensional dual-continuum hydrogoelogical model, to simulate the water level in the observation well. NIDMOWR performs its calculations in a physically based way, by calculating exchange fluxes between the well and each of the two media as a function of head gradients, and introduces as a parameter the altitude of lowest interception of the fracture network by the well. This is a low-parameterized way to describe in the simulations the level of connection between the well and the fractures. The model is expected to enable researchers better interpret observation wells data of fractured porous aquifers when using bidimensional dual-media hydrogeological models.

# **II.	Conceptual model**
![fig1](https://user-images.githubusercontent.com/67539849/194730193-9fae40c2-eaa4-4e1c-92e3-ce047f77d1d0.png)

Fig 1. Conceptual model used in NIDMOWR for an observation well in a fractured aquifer. Rin,   Rout  and Rdrill are respectively the inner, outer, and drill radius of the observation well. The yellow dots represent the soil of porosity ωdrill used to fill the space between Rout  and Rdrill . ztube and zsurf are respectively the altitude of the top of the well and of the surface. The red plain lines represent the fracture network. Among those, the bold lines represent the saturated portion of the fracture network. The brown and blue backgrounds represent respectively the saturated and unsaturated zones of the matrix. zm and zf are the water heads respectively in the matrix and in the fracture network, and zw is the water level in the well. zbot_m and zbot_f are respectively the altitude of the bottom of the well and the altitude of the lowest interception of the well by the fracture network. The absence of any direct water exchanges between the fracture network and the well below zbot_f has a significant impact on zw. For example, for the represented situation where both zm and zf are below zbot_f, assuming zm and zf are constant in time, then at steady state zw is equal to zm.

For more details on the conceptual model and its undelrying hypotheses, and for explanations about its implementation in the numerical model underpinning NIDMOWR, see Jeannot et al(submitted)

# **III.	How to use NIDMOWR**
  ## 1. Requirements

  System : NIDMOWR uses python and works both on Windows and Linux systems. It has not been tested on Mac.
  
  Python libraries : numpy, matplotlib

  ## 2. Step by step procedure
  - Download the repository
  - Open a command line prompt in the folder containing "NIDMOWR.py" and type : 
    python NIDMOWR.py testcase
    
    In the above command, "testcase" is the name of a folder containing an "Inputs" subfolder. The "Inputs" subfolder contains input files corresponding to the test case, as described below. An "Outptus" subfolder will be created in the "testcase" folder as a result of running NIDMOWR, and outputs of NIDMOWR will be saved in this subfolder.
    
  For example : "python NIDMOWR.py Test_case_1" and "python NIDMOWR.py Test_case_2"  simulate respectively the synthetic test cases 1 and 2 presented in Jeannot et al.(submitted).
    
  ## 3. Description of input files
### zm_zf.txt
The first column is the time in seconds, the second column is total water head in the matrix (zm) in meters, and the third column is the total water head in the fracture network (zf) in meters, as simulated by a dual-continuum bidimensional hydrogeological model at the vicinitude of an observation well. The time step must be constant.
### settings_NIDMOWR.txt
- nbr_Interp_deltaT : The time step at which zm and zf are given might be coarser than the time step at which it is relevant to compute the water level in the observation well (zw). nbr_Interp_deltaT is the number of subdivisions to make on the time step of zm and zf.
- nbr_pts_rect_int_K : Number of rectangles to use for rectangular integration of the hydraulic conductivity in the vadose zone
- niter_max : maximum number of iterations allowed for convergence at a specific time-step.
- n_failmax : total number of times a set of niter_max iterations can be tried for reaching convergence at a specific-time step. Because NIDMOWR works with a random relaxation factor from a convergence iteration to the next, convergence can be failed after niter_max iterations on the first try but not on the second one. After n_failmax attemps, the whole NIDMOWR simulation is restarted from scratch, with a reduced time step.
- epsilon : convergence critetion, in meters.
- AveragingK : criterion stating if an arithmetic mean or geometric mean is to use when averaging hydraulic conductivities at the interface between the well and the media, in the case where the well exfiltrates water into the vadose zone. (0=arithmetic,1=geometric)
### params_well.txt
- ztube(m): altitude of the top of the tube of the well
- zsurf(m): altitude of the surface
- zbot_f(m) : altitude of lowest interception of the fracture network by the well
- zbot_m(m) : altitude of lthe bottom of the well
- rin(m) : inner radius of the tube
- rout(m) : outer radius of the tube
- rdrill(m) : drill radius of the well
- DeltaE(coupling_length)(m) : width of the interface between the well and the media
### params_matrix.txt
- Ks(m/s) : saturated hydraulic conductivity of the matrix
- n(-) : n coefficient in the Mualem-van Genuchten formula, for the matrix
- alpha(/m): alpha coefficient in the Mualem-van Genuchten formula, for the matrix
- K_unsat_mini(m/s) : lower limit on the saturated hydraulic conductivity calculated with the van Genuchten formula, for the matrix
### params_fractures.txt
- Ks(m/s) : saturated hydraulic conductivity of the fractures
- n(-) : n coefficient in the Mualem-van Genuchten formula, for the fractures
- alpha(/m): alpha coefficient in the Mualem-van Genuchten formula, for the fractures
- K_unsat_mini(m/s) : lower limit on the saturated hydraulic conductivity calculated with the van Genuchten formula, for the fractures

For more details about input formatting, please refer to the example input files provided in the folder "Test_case_1/Inputs" and "Test_case_2/Inputs"

  ## 4. Description of output files
### zw.txt
The first column is the time in seconds and the second column is the water level in the observation well (zw) in meters, as simulated by NIDMOWR
### Fluxes.txt
The first column is the time in seconds. The second and third columns are respectively the flux from the well to the matrix and the flux from the well to the fractures in m3/s, as simulated by NIDMOWR.
### Sum_fluxes.txt
The first column is the time in seconds. The second and third columns are respectively the cumulative flux from the well to the matrix and the cumulative the flux from the well tos the fractures in m3/s, as simulated by NIDMOWR.
### Output_graph.png
Chart displaying two panels (a) and (b).
Panel (a): Variation over time of total water head in the matrix, noted zm (black dotted line), of total water head in the fracture, noted zf (red dotted line), and of water level in the observation well as simulated by NIDMOWR, noted zw (blue plain line) . 
Panel (b) : Infiltrated flux from the fracture network to the well (in red) and exfiltrated flux from the well to the matrix (in black), as simulated by NIDMOWR.

Example for test case 1 :

![Output_graph](https://user-images.githubusercontent.com/67539849/194750310-a5e8e68d-8516-4a3b-94db-c9531fb7f190.png)
