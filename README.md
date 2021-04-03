# InterfacePhononsToolkit — a Python Set of Libraries for Computing Phonons Behavior at the Interfaces

<div align="justify">
  
*InterfacePhononsToolkit* is a collection of tools for setting up, performing, analyzing molecular dynamic simulations of heat flow at the interfaces and computing thermodynamic properties of the boundaries. This collection includes a set of libraries to generate phonon Gaussian wavepackets to compute phonon transmission matrices across solid-solid interfaces, a generic Monte Carlo ray tracing model to unveil anticorrelated heat current phenomenon in materials containing nanoscale pores (with a new rigorous mathematical implementation), a set of libraries to analyze lattice vibrational behavior in heterogeneous structures, including atomic partition ratio, local phonon density of state, etc., and a Monte Carlo model to compute phonon lifetime in microstructures. The codes have interface with LAMMPS molecular dynamics simulator.

</div>


- [GETTING STARTED](#GETTING-STARTED)
- [INSTALLATION](#INSTALLATION)
- [RAY TRACING MODEL](#RAY-TRACING-MODEL)
  * [The Enhancement to Thermoelectric Performance That Could Be Obtained by Designing the Electron Scattering to Optimally Harness the Mechanism of Electron Energy Filtering](#The-Enhancement-to-Thermoelectric-Performance-That-Could-Be-Obtained-by-Designing-the-Electron-Scattering-to-Optimally-Harness-the-Mechanism-of-Electron-Energy-Filtering)
  * [Design the Nanoscale Morphology of a Thermoelectric to Obtain Favorable Electron Scattering](#Design-the-Nanoscale-Morphology-of-a-Thermoelectric-to-Obtain-Favorable-Electron-Scattering)
- [THEORY](#THEORY)
  * [Model Electron Transport Coefficients In Bulk Thermoelectrics](#Model-Electron-Transport-Coefficients-In-Bulk-Thermoelectrics)
  * [Model Fermi Level](#Model-Fermi-Level)
  * [Model Electron Lifetime](#Model-Electron-Lifetime)
  * [Model Electron Transport Coefficients in Nanostructured Thermoelectrics](#Model-Electron-Transport-Coefficients-in-Nanostructured-Thermoelectrics)
- [CASE STUDY SI BASED NANOCOMPOSITE](#CASE-STUDY-SI-BASED-NANOCOMPOSITE)
  * [Silicon Band Structure](#Silicon-Band-Structure)
  * [Model Prediction for Bulk Si](#Model-Prediction-for-Bulk-Si)
  * [Ideal Electron Filtering](#Ideal-Electron-Filtering)
  * [Effect of Nanopores on Lorenz Number](#Effect-of-Nanopores-on-Lorenz-Number)
- [REFERENCES](#REFERENCES)
- [CITATION](#Citation)

## GETTING STARTED

<div align="justify">

The overall structure of the repository is as below.
    
    ├── Examples             # Tutorials, sanity and performance checks
    ├── Figures              # List of figures generated by Thermoelectric.py
    ├── ThirdPartyTools      # Thirs party tools used in this project
    ├── docs                 # The pdf manual of Thermoelectric.py
    ├── src                  # Python source files of the project 
    ├── requirements.txt 
    └── README.md

The best place to start is the *Examples* folder, where there are a couple of scripts available to get you started with the code. A quick description of the notebooks is as the following.

    ├── Examples 
    │   ├── Si_Ideal_Filtering    # Example of running simulation to model idal filtering Si
    │   ├── Si_Nanocomposite      # More involving example of thermoelectric properties in Si containing SiC nanoparticles
    │   ├── README.md            

</div>

# INSTALLATION

Compatible with python 3.0 and upwards
```bash
git clone https://github.com/ariahosseini/thermoelectric.py.git
cd thermoelectric.py/
pip install --upgrade pip
pip install -r Requirements.txt
```

You need to define the following environment variable in your .bashrc or .bash_profile

```bash
export PYTHONPATH=/PATH/TO/THERMOELECTRICPYl/thermoelectric.py/src:/PATH/TO/THERMOELECTRICPYl/thermoelectric.py/ThirdPartyTools:$PYTHONPATH
```

# RAY TRACING MODEL

<div align="justify">
  
  <p>
  
  It is difficult to infer the phonon scattering behavior that gives rise to the anticorrelation in HCACF directly from the shape of the HCACF curve. To circumvent this difficulty, we have developed a Monte Carlo phonon ray tracing model which allows us to test how different scattering behavior that could be occurring at pores would be manifest in the HCACF, allowing us to seek signatures of different scattering phenomena in the HCACF obtained from the MD simulations. Phonon ray tracing models have been used to great effect by several other researchers to identify signatures of ballistic transport in a variety of nanoscale geometries. What sets our use of the ray tracing model apart is that rather than using it to predict thermal transport, we use it to construct the heat current autocorrelation function. 
 </p>
 
## Analytic Model of Uncorrelated Scattering
<p>
  
To begin, we lay out the theoretical basis for the ray tracing model by establishing that macroscopic thermal conductivity can be computed from the sum of the autocorrelation functions of the individual phonon modes’ occupancy fluctuations. 
 </p>
 
 <p>
  
The Green Kubo equation gives the thermal conductivity tensor as the integral of the total heat current autocorrelation function:
 </p>
 
<p align="center">
<img src="https://latex.codecogs.com/png.latex?%5Cdpi%7B300%7D%20%5Ctiny%20%5Cmathbf%7B%5Ckappa%7D%20%3D%20%5Cfrac%7BV%7D%7Bk_BT%5E2%7D%5Cint_%7B0%7D%5E%7B%5Cinfty%20%7D%20d%5Ctau%20%5Cleft%5Clangle%20%5Cmathbf%7BJ%7D%28t%29%20%5Cbigotimes%20%5Cmathbf%7BJ%7D%28t&plus;%5Ctau%29%5Cright%5Crangle%2C" align="center" alt="drawing">
</p>

<p>
  
where J<sub>kp</sub>(t) is the total instantaneous heat flux at time t, in the volume of materials V that is at thermal equilibrium at temperature T. The autocorrelation function 〈J<sub>kp</sub>  (t)⊗J<sub>kp</sub>(t+τ)〉 is the time-averaged dyadic of the heat fluxes an interval τ apart. The contribution to the heat current from the mode with wave vector k and polarization p is
 </p>
 
<p align="center">
<img src="https://latex.codecogs.com/png.latex?%5Cdpi%7B300%7D%20%5Ctiny%20%5Cmathbf%7BJ%7D_%7B%5Cmathbf%7Bk%7Dp%7D%28t%29%20%3D%20%5Cleft%28%20%5Cfrac%7B1%7D%7B2%7D&plus;n_%7B%5Cmathbf%7Bk%7Dp%7D%28t%29%5Cright%29%20%5Cfrac%7B%5Chbar%20%5Comega_%7B%5Cmathbf%7Bk%7Dp%7D%20%5Cnu_%7B%5Cmathbf%7Bk%7Dp%7D%7D%7BV%7D%2C" align="center" alt="drawing">
</p>

<p>
  
where n<sub>kp</sub>(t) is the mode’s occupancy at time t. This can be rewritten in terms of the average flux and the instantaneous excursion from the average:
</p>

<p align="center">
<img src="https://latex.codecogs.com/png.latex?%5Cdpi%7B300%7D%20%5Ctiny%20%5Cmathbf%7BJ%7D_%7B%5Cmathbf%7Bk%7Dp%7D%28t%29%20%3D%20%5Cleft%5Clangle%20%5Cmathbf%7BJ%7D_%7B%5Cmathbf%7Bk%7Dp%7D%20%5Cright%5Crangle%20&plus;%20%5Cleft%28%20n_%7B%5Cmathbf%7Bk%7Dp%7D%28t%29%20-%20%5Cleft%5Clangle%20n_%7B%5Cmathbf%7Bk%7Dp%7D%20%5Cright%5Crangle%20%5Cright%29%20%5Cfrac%7B%5Chbar%20%5Comega_%7B%5Cmathbf%7Bk%7Dp%7D%20%5Cnu_%7B%5Cmathbf%7Bk%7Dp%7D%7D%7BV%7D%2C" align="center" alt="drawing">
</p>

<p>
  
where 〈n<sub>kp</sub>〉 is the Bose-Einstein occupancy
</p>

<p align="center">
<img src="https://latex.codecogs.com/png.latex?%5Cdpi%7B300%7D%20%5Ctiny%20%5Cleft%5Clangle%20n_%7B%5Cmathbf%7Bk%7Dp%7D%20%5Cright%5Crangle%20%3D%20%5Cfrac%7B1%7D%7Be%5E%7B%5Cwidetilde%7B%5Comega%7D_%7B%5Cmathbf%7Bk%7Dp%7D%7D-1%7D%2C" align="center" alt="drawing">
</p>

<p>
  
and ω ̃<sub>kp</sub> is the dimensionless mode frequency ω ̃<sub>kp</sub>= ℏω<sub>kp</sub>/(k<sub>B</sub>T). The total instantaneous heat flux is then the sum of contributions from all modes,
</p>

<p align="center">
<img src="https://latex.codecogs.com/png.latex?%5Cdpi%7B300%7D%20%5Ctiny%20%5Cmathbf%7BJ%7D%28t%29%20%3D%20%5Csum_%7B%5Cmathbf%7Bk%7Dp%7D%20%5Cmathbf%7BJ%7D_%7B%5Cmathbf%7Bk%7Dp%7D%28t%29%20%3D%20%5Csum_%7B%5Cmathbf%7Bk%7Dp%7D%20%5Cleft%28%20n_%7B%5Cmathbf%7Bk%7Dp%7D%28t%29%20-%20%5Cleft%5Clangle%20n_%7B%5Cmathbf%7Bk%7Dp%7D%20%5Cright%5Crangle%20%5Cright%29%20%5Cfrac%7B%5Chbar%20%5Comega_%7B%5Cmathbf%7Bk%7Dp%7D%20%5Cnu_%7B%5Cmathbf%7Bk%7Dp%7D%7D%7BV%7D." align="center" alt="drawing">
</p>

<p>
  
where the mean flux from pairs of modes with opposite k has canceled one another so that the net flux only depends on the sum of occupancy excursions from the mean. If the occupancy fluctuations in one mode are uncorrelated with the fluctuations in the other modes then when expanding the product of the sum of modal fluxes, the cross-correlations between modes will be zero simplifying the total correlation function to the sum of autocorrelation functions for each mode individually:
</p>

<p align="center">
<img src="https://latex.codecogs.com/png.latex?%5Cdpi%7B300%7D%20%5Ctiny%20%5Cleft%5Clangle%20%5Cmathbf%7BJ%7D%28t%29%20%5Cbigotimes%20%5Cmathbf%7BJ%7D%28t&plus;%5Ctau%29%5Cright%5Crangle%20%3D%20%5Csum_%7B%5Cmathbf%7Bk%27%7Dp%7D%5Csum_%7B%5Cmathbf%7Bk%7Dp%7D%5Cleft%5Clangle%20%5Cmathbf%7BJ%7D_%7B%5Cmathbf%7Bk%27%7Dp%27%7D%28t%29%20%5Cbigotimes%20%5Cmathbf%7BJ%7D_%7B%5Cmathbf%7Bk%7Dp%7D%28t%29%5Cright%5Crangle%20%3D%20%5Csum_%7B%5Cmathbf%7Bk%7Dp%7D%5Cleft%5Clangle%20%5Cmathbf%7BJ%7D_%7B%5Cmathbf%7Bk%7Dp%7D%28t%29%20%5Cbigotimes%20%5Cmathbf%7BJ%7D_%7B%5Cmathbf%7Bk%7Dp%7D%28t&plus;%5Ctau%29%5Cright%5Crangle." align="center" alt="drawing">
</p>

<p>
  
This, in turn, depends only on the autocorrelation of the occupancy fluctuations 
</p> 

<p align="center">
<img src="https://latex.codecogs.com/png.latex?%5Cdpi%7B300%7D%20%5Ctiny%20%5Cleft%5Clangle%20%5Cmathbf%7BJ%7D_%7B%5Cmathbf%7Bk%7Dp%7D%28t%29%20%5Cbigotimes%20%5Cmathbf%7BJ%7D_%7B%5Cmathbf%7Bk%7Dp%7D%28t&plus;%5Ctau%29%5Cright%5Crangle%20%3D%20%5Cleft%20%28%20%5Cfrac%7B%5Chbar%20%5Comega_%7B%5Cmathbf%7Bk%7Dp%7D%7D%7BV%7D%20%5Cright%20%29%5E2%20%5Cmathbf%7B%5Cnu%7D%20%5Cmathbf%7B%5Cnu%7D_%7B%5Cmathbf%7Bk%7Dp%7D%20%5Cleft%5Clangle%20%5Cleft%28%20n_%7B%5Cmathbf%7Bk%7Dp%7D%28t%29%20-%20%5Cleft%5Clangle%20n_%7B%5Cmathbf%7Bk%7Dp%7D%20%5Cright%5Crangle%20%5Cright%29%20%5Cleft%28%20n_%7B%5Cmathbf%7Bk%7Dp%7D%28t&plus;%5Ctau%29%20-%20%5Cleft%5Clangle%20n_%7B%5Cmathbf%7Bk%7Dp%7D%20%5Cright%5Crangle%20%5Cright%29%20%5Cright%5Crangle%2C" align="center" alt="drawing">
</p>

<p>
  
Where the shorthand notation vv<sub>kp</sub> = (v<sub>kp</sub>⊗v<sub>kp</sub>) has been used for the tensor product of the group velocity. 
</p>

<p>
  
The occupancy n(t) of a phonon mode will be a random stepped function in time as shown in Figure below with phonon mode holding a constant excitation for some duration before anharmonic interactions with other phonon modes lead to scattering and a reset of the mode’s excitation. This function can be expressed as a sum of boxcar functions that represent the occupancy during the interval between successive scattering events 
</p>

<p align="center">
<img src="https://latex.codecogs.com/png.latex?%5Cdpi%7B300%7D%20%5Ctiny%20n%28t%29%20%3D%20%5Csum_%7Bi%3D1%7D%5E%7B%5Cinfty%7D%20n_i%20%5CPi_%7Bt_i%2C%20t_i&plus;%5Ctheta_i%7D%28t%29%2C" align="center" alt="drawing">
</p>

<p>
 
where Π<sub>(a,b)</sub>(t) is the boxcar function (plotted in blue in the top pane of Figure below) 
</p>

<p align="center">
<img src="https://latex.codecogs.com/png.latex?%5Cdpi%7B300%7D%20%5Ctiny%20%5CPi_%7Ba%2Cb%7D%28t%29%20%3D%20%5Cbegin%7Bcases%7D%201%20%26%20%5Ctext%7B%20for%20%7D%20a%20%5Cleq%20t%3C%20b%20%5C%5C%200%20%26%20%5Ctext%7B%20otherwise%20%7D%20%5Cend%7Bcases%7D%2C" align="center" alt="drawing">
</p>

<p>
  
and n<sub>i</sub> and θ<sub>i</sub> are the size and duration of the i-th occupancy fluctuation, and the fluctuations abut one another so that  θ<sub>i</sub>=t<sub>i+1</sub>-t<sub>i</sub>. 
</p>

<p>
  
If the probability of the i-th occupancy n<sub>i</sub>  and its duration θ<sub>i</sub> are independent from the fluctuations that proceeded it (as in the figure above), then the occupancy is only correlated during the intervals between scattering and so the occupancy correlation function reduces to simply the average of the correlation functions for each excursion with itself
</p>

<p align="center">
<img src="https://latex.codecogs.com/png.latex?%5Cdpi%7B300%7D%20%5Ctiny%20%5Cleft%5Clangle%20%5Cleft%28%20n%28t%29%20-%20%5Cleft%5Clangle%20n%20%5Cright%5Crangle%20%5Cright%29%20%5Cleft%28%20n%28t&plus;%5Ctau%29%20-%20%5Cleft%5Clangle%20n%20%5Cright%5Crangle%20%5Cright%29%20%5Cright%5Crangle%20%3D%20%5Csum_%7Bn%3D0%7D%5E%7B%5Cinfty%7D%20%5Cint_%7B0%7D%5E%7B%5Cinfty%7D%20d%20%5Ctheta%27%20P_%7B%5Coverline%7B%5Ctheta%7D%7D%28%5Ctheta%27%29%20R_n%28n-%20%5Clangle%20n%20%5Crangle%29%5E2%20A%28%5Ctau%2C%20%5Ctheta%27%29%2C" align="center" alt="drawing">
</p>

<p>
  
where the subscripts have been dropped temporarily for clarity. Here R<sub>n</sub> is the average rate of scattering events that leave the mode with occupancy n, and P<sub>θ ̅</sub>(θ) is the probability that a fluctuation of occupancy n survives for time θ before scattering given that the average duration of fluctuations is θ ̅. The term A(τ,θ) is the autocorrelation function of a single boxcar function
</p>

<p align="center">
<img src="https://latex.codecogs.com/png.latex?%5Cdpi%7B300%7D%20%5Ctiny%20A%28%5Ctau%2C%20%5Ctheta%29%20%3D%20%5Cint_%7B0%7D%5E%7B%5Cinfty%7D%20dt%27%20%5CPi_%7Ba%2C%20a&plus;%5Ctheta%7D%28t%27%29%20%5CPi_%7Ba%2C%20a&plus;%5Ctheta%7D%28t%27&plus;%5Ctau%29%20%3D%20%5Ctheta%20%5Cleft%28%201-%20%5Cfrac%7B%5Ctau%7D%7B%5Ctheta%7D%5Cright%29%20H%28%5Ctheta-%5Ctau%29%2C" align="center" alt="drawing">
</p>

<p>
  
with H(x) is the Heaviside theta function. The integral of the boxcar’s autocorrelation function is 
</p>

<p align="center">
<img src="https://latex.codecogs.com/png.latex?%5Cdpi%7B300%7D%20%5Ctiny%20C%28%5Ctau%2C%20%5Ctheta%29%20%3D%20%5Cint_%7B0%7D%5E%7B%5Cinfty%7D%20d%5Ctau%27%20A%28%5Ctau%27%2C%20%5Ctheta%29%20%3D%20%5Cleft%28%20%5Ctheta%20%5Ctau%20-%20%5Cfrac%7B%5Ctau%5E2%7D%7B2%7D%5Cright%29%20H%28%5Ctheta-%5Ctau%29&plus;%5Ctheta%5E2%281-H%28%5Ctheta-%5Ctau%29%29." align="center" alt="drawing">
</p>

<p>
  
The upper pane of Figure below shows the boxcar function for a single fluctuation, with the lower pane showing its autocorrelation function and integral. The integrated ACF converges to 
</p>

<p align="center">
<img src="https://latex.codecogs.com/png.latex?%5Cdpi%7B300%7D%20%5Ctiny%20C%28%5Cinfty%2C%20%5Ctheta%29%20%3D%20%5Cfrac%7B1%7D%7B2%7D%20%5Ctheta%5E2." align="center" alt="drawing">
</p>

<p>
  
If the scattering processes that lead to the occupation fluctuations are random, then occupation times θ are drawn from the Poisson distribution of waiting times
</p>

<p align="center">
<img src="https://latex.codecogs.com/png.latex?%5Cdpi%7B300%7D%20%5Ctiny%20P_%7B%5Coverline%7B%5Ctheta%7D%7D%28%5Ctheta%29%20%3D%20%5Cfrac%7B1%7D%7B%5Coverline%7B%5Ctheta%7D%7D%20e%5E%7B-%5Ctheta/%5Coverline%7B%5Ctheta%7D%7D." align="center" alt="drawing">
</p>

<p>
  
The rate, R<sub>n</sub>, of scattering into occupancy n is related to the thermodynamic probability P<sub>n</sub> of finding the mode in its n-th state of occupancy by
</p>

<p align="center">
<img src="https://latex.codecogs.com/png.latex?%5Cdpi%7B300%7D%20%5Ctiny%20R_n%20%3D%20%5Cfrac%7BP_n%7D%7B%5Coverline%7B%5Ctheta%7D%7D%2C" align="center" alt="drawing">
</p>

<p>
  
where P<sub>n</sub> is the probability distribution for the canonical ensemble
</p>

<p align="center">
<img src="https://latex.codecogs.com/png.latex?%5Cdpi%7B300%7D%20%5Ctiny%20P_n%20%3D%20e%5E%7B-n%5Cwidetilde%7B%5Comega%7D%7D%20%5Cleft%281%20-%20e%5E%7B-%5Cwidetilde%7B%5Comega%7D%7D%20%5Cright%29." align="center" alt="drawing">
</p>

<p>
  
Using Equations above and performing the integral over lifetimes and summing over n gives:
</p>

<p>
  
Performing the averaging over all possible occupancies gives
</p>
  
<p align="center">
<img src="https://latex.codecogs.com/png.latex?%5Cdpi%7B300%7D%20%5Ctiny%20%5Cleft%5Clangle%20%5Cleft%28%20n%28t%29%20-%20%5Cleft%5Clangle%20n%20%5Cright%5Crangle%20%5Cright%29%20%5Cleft%28%20n%28t&plus;%5Ctau%29%20-%20%5Cleft%5Clangle%20n%20%5Cright%5Crangle%20%5Cright%29%20%5Cright%5Crangle%20%3D%20%5Cfrac%7B1%7D%7B%5Coverline%7B%5Ctheta%7D%7D%20%5Cfrac%7Be%5E%7B%5Cwidetilde%7B%5Comega%7D%7D%7D%7B%28e%5E%7B%5Cwidetilde%7B%5Comega%7D%7D-1%29%5E2%7D%5Cint_%7B0%7D%5E%7B%5Cinfty%7D%20d%5Ctheta%27A%28%5Ctau%2C%5Ctheta%29e%5E%7B-%5Ctheta%27/%5Coverline%7B%5Ctheta%7D%7D%2C" align="center" alt="drawing">
</p> 

<p>
  
and averaging over all possible fluctuation durations gives 
</p>

<p align="center">
<img src="https://latex.codecogs.com/png.latex?%5Cdpi%7B300%7D%20%5Ctiny%20%5Cleft%5Clangle%20%5Cleft%28%20n%28t%29%20-%20%5Cleft%5Clangle%20n%20%5Cright%5Crangle%20%5Cright%29%20%5Cleft%28%20n%28t&plus;%5Ctau%29%20-%20%5Cleft%5Clangle%20n%20%5Cright%5Crangle%20%5Cright%29%20%5Cright%5Crangle%20%3D%20%5Cfrac%7Be%5E%7B%5Cwidetilde%7B%5Comega%7D%7D%7D%7B%28e%5E%7B%5Cwidetilde%7B%5Comega%7D%7D-1%29%5E2%7D%20e%5E%7B-%5Ctheta%27/%5Coverline%7B%5Ctheta%7D%7D." align="center" alt="drawing">
</p> 

<p>
  
The cumulative thermal conductivity tensor is then
  </p>
  
  <p align="center">
<img src="https://latex.codecogs.com/png.latex?%5Cdpi%7B300%7D%20%5Ctiny%20%5Cmathbf%7B%5Ckappa%7D%28%5Ctau%29%3D%20%5Cfrac%7BV%7D%7Bk_B%20T%5E2%7D%20%5Csum_%7B%5Cmathbf%7Bk%7Dp%7D%20%5Cleft%28%20%5Cfrac%7B%5Chbar%20%5Comega_%7B%5Cmathbf%7Bk%7Dp%7D%7D%7BV%7D%20%5Cright%29%5E2%20%5Cfrac%7Be%5E%7B%5Cwidetilde%7B%5Comega%7D%7D%7D%7B%28e%5E%7B%5Cwidetilde%7B%5Comega%7D%7D-1%29%5E2%7D%20%5Cnu%20%5Cnu_%7B%5Cmathbf%7Bk%7Dp%7D%20%5Cint_%7B0%7D%5E%7B%5Ctau%7D%20d%5Ctau%27e%5E%7B-%5Ctau%27/%5Coverline%7B%5Ctheta%7D_%7B%5Cmathbf%7Bk%7Dp%7D%7D%2C" align="center" alt="drawing">
</p> 

<p>
  
which after some mathematical manipulation simplifies to
</p> 

 <p align="center">
<img src="https://latex.codecogs.com/png.latex?%5Cdpi%7B300%7D%20%5Ctiny%20%5Cmathbf%7B%5Ckappa%7D%28%5Ctau%29%3D%20%5Csum_%7B%5Cmathbf%7Bk%7Dp%7D%20%5Cleft%28%20%5Cfrac%7B%5Chbar%20%5Comega_%7B%5Cmathbf%7Bk%7Dp%7D%7D%7BV%7D%20%5Cfrac%7Bd%20%5Clangle%20n_%7B%5Cmathbf%7Bk%7Dp%7D%5Crangle%7D%7BdT%7D%20%5Cright%29%20%5Cnu%20%5Cnu_%7B%5Cmathbf%7Bk%7Dp%7D%20%5Coverline%7B%5Ctheta%7D_%7B%5Cmathbf%7Bk%7Dp%7D%20%5Cleft%28%201%20-%20e%5E%7B-%5Ctau/%5Coverline%7B%5Ctheta%7D_%7B%5Cmathbf%7Bk%7Dp%7D%7D%20%5Cright%29." align="center" alt="drawing">
</p>

<p>
  
The first term in parenthesis is the mode’s volumetric specific heat. If the phonon modes all have the same scattering rate and group velocity and assuming cubic symmetry, the first element of the thermal conductivity tensor reduces to the well-known result from kinetic theory:
  </p>
  
  <p align="center">
<img src="https://latex.codecogs.com/png.latex?%5Cdpi%7B300%7D%20%5Ctiny%20%5Ckappa_%7Bxx%7D%28%5Cinfty%29%20%3D%20%5Cfrac%7B1%7D%7B3%7D%20C_v%20%5Coverline%7B%5Cnu%7D_g%20%5Clambda." align="center" alt="drawing">
</p>

<p>
  
where C<sub>v</sub>, v ̅<sub>g</sub> and λ are the systems volumetric specific heat, group velocity, and mean free path.  This result shows that in order to understand the HCACF and the reduction in thermal conductivity we need only consider just the autocorrelation of individual occupancy fluctuation or phonon wavepackets by themselves, and thus provides the formal theoretical footing for the ray tracing model that follows. Before moving on to the ray tracing model, however, we make note of several insights that can be obtained from the analysis above. 
</p>

<p>
  
The correlation function in Equations above decays (and the cumulative conductivity saturates) exponentially over a correlation time equal to the mean phonon scattering time θ ̅, so is all modes have the same lifetime the cumulative thermal conductivity distribution over correlation time τ is
</p>

<p align="center">
<img src="https://latex.codecogs.com/png.latex?%5Cdpi%7B300%7D%20%5Ctiny%20%5Cfrac%7B%5Ckappa%28%5Ctau%29%7D%7B%5Ckappa_%7B%5Cinfty%7D%7D%20%3D%201%20-%20e%5E%7B-%5Ctau/%5Coverline%7B%5Ctheta%7D%7D." align="center" alt="drawing">
</p>

<p>
  
This masks the importance for the transport of heat of long-lived occupancy fluctuations in the tail of the Poisson distribution of lifetimes. The contribution that occupancy fluctuations make to the thermal conductivity is proportional to their lifetime squared, and so we can compute the cumulative thermal conductivity distribution over phonon lifetimes as 
</p>

<p align="center">
<img src="https://latex.codecogs.com/png.latex?%5Cdpi%7B300%7D%20%5Ctiny%20%5Cfrac%7B%5Ckappa%28%5Ctheta%29%7D%7B%5Ckappa_%7B%5Cinfty%7D%7D%20%3D%20%5Cfrac%7B%5Cint_%7B0%7D%5E%7B%5Ctheta%7Dd%5Ctheta%27%20%5Ctheta%27%5E2%20P_%7B%5Coverline%7B%5Ctheta%7D%7D%28%5Ctheta%27%29%7D%7B%5Cint_%7B0%7D%5E%7B%5Cinfty%7Dd%5Ctheta%27%20%5Ctheta%27%5E2%20P_%7B%5Coverline%7B%5Ctheta%7D%7D%28%5Ctheta%27%29%7D%20%3D%201-%20%5Cfrac%7B1%7D%7B2%7D%20%5Cleft%28%202%20&plus;%20%5Cfrac%7B%5Ctheta%7D%7B%5Coverline%7B%5Ctheta%7D%7D%20%5Cleft%28%202%20&plus;%20%5Cfrac%7B%5Ctheta%7D%7B%5Coverline%7B%5Ctheta%7D%7D%5Cright%29%20%5Cright%29e%5E%7B-%5Ctheta/%5Coverline%7B%5Ctheta%7D%7D." align="center" alt="drawing">
</p>

<p>
  
These equations are plotted in figure below where it can be seen that the short-lived fluctuations (small θ) contribute very little to the thermal conductivity while the cumulative conductivity distribution in τ  rises rapidly at small correlation times. This illustrates the conceptual difference between fluctuation duration θ and correlation time τ; the change in κ(τ) at correlation time τ includes contributions from all fluctuations with θ> τ.  It is common in heat transport texts to see plots of the cumulative contribution to thermal conductivity over the distribution of phonon modes’ mean free paths in a crystal κ(λ), and so we take pains here to point out that the κ(θ) distribution in the figure is due to the shot noise in a mode with average scattering rate θ ̅, it is not from a distribution different θ ̅ over different modes. However, the effect of this noise is significant, with a large contribution to the total conductivity coming from a small number of phonons that travel ballistically over distances many times longer than the mean free path before scattering. This point is further emphasized by computing the cumulative conductivity distribution over the fraction ϕ of occupancy fluctuations ranked in ascending order of their longevity,
</p>

<p align="center">
<img src="https://latex.codecogs.com/png.latex?%5Cdpi%7B300%7D%20%5Ctiny%20%5Cfrac%7B%5Ckappa%28%5Cphi%29%7D%7B%5Ckappa_%7B%5Cinfty%7D%7D%20%3D%20%5Cphi%20-%20%281-%5Cphi%29%20%5Cln%7B%281-%5Cphi%29%7D%5Cleft%28%201%20-%20%5Cfrac%7B1%7D%7B2%7D%20%5Cln%7B%281-%5Cphi%29%7D%5Cright%29%2C" align="center" alt="drawing">
</p>

<p>
  
as is plotted in Figure below. In fact, more than 50% of the heat transported is carried by just 7% of phonons that survive for more than 2.7 times the average phonon lifetime θ ̅. It can also be seen that 80% of the heat current comes from fewer than 20% of the phonon mode occupancy fluctuations. This matches Pareto’s 80-20 rule that is a signature of Lévy flights, although in this case, the distribution of flight lengths is not fractal (power-law), and so this behavior is not a true Lévy flight. The outsized importance of the long-lived phonons is significant as it implied that ballistic effects for heat transport in nanoscale systems should become significant at system sizes significantly larger than the phonons’ nominal mean free path. This observation also has important ramifications for deterministic simulations of the Boltzmann transport equation (BTE) for phonons, implying that to correctly predict the heat conduction due to phonon transport in nanostructured material, one must smear the intrinsic distribution of average phonon lifetimes by the Poisson distribution—a practice that is often overlooked in frequency-dependent and multi-grey BTE simulation.
</p>

<p>
  
In addition to computing the contribution from different occupancy durations, it is also insightful to consider the cumulative distribution contribution from different occupancy
</p>

<p align="center">
<img src="https://latex.codecogs.com/png.latex?%5Cdpi%7B300%7D%20%5Ctiny%20%5Cfrac%7B%5Ckappa%28n%29%7D%7B%5Ckappa_%7B%5Cinfty%7D%7D%20%3D%20%5Csum_%7Bn%27%3D0%7D%5E%7Bn%7D%20P_%7Bn%27%7D%28n%27-%5Clangle%20n%20%5Crangle%29%5E2/%5Csum_%7Bn%27%3D0%7D%5E%7B%5Cinfty%7D%20P_%7Bn%27%7D%28n%27-%5Clangle%20n%20%5Crangle%29%5E2%2C" align="center" alt="drawing">
</p>
  
<p>
  
as plotted in Figure below with values for ω ̅ of 0.5, 1.0, and 1.5 (which corresponds to average occupancies of 〈n〉 of 0.16, 0.58, and 1.1). It can be seen that because of the (n-〈n〉)<sup>2</sup> dependence on the ACF infrequency multiple phonon excitations of the modes account for a large fraction of the heat carried, even at temperatures where 〈n〉~0.5. This suggests that scattering processes involving four or more phonons, although rare, will have a significant impact on the overall thermal conductivity, even at low temperatures.
</p>

## Monte Carlo ray tracing model of correlated scattering 

<p>
  
To isolate the effects that arise from correlated scattering we study a simpler model system consisting of a grey population of phonons in which we assume that all phonon modes have the same frequency ω, group velocity v<sub>g</sub>, and mean intrinsic scattering lifetime θ ̅.  The derivation above shows that the HCACF can be constructed by considering each correlated heat carrier event in isolation and then averaging their contribution to the total HCACF. For a wave packet scattered elastically from one mode (with wavevector k and polarization p) into another mode (k'p') by an interface, one must consider the flight of both the incident wavepacket and the scattered wavepacket together as the occupancy fluctuations in kp and k'p' modes are now (anti)correlated. This is true even in the case of diffuse scattering, where the choice of the scattered k' mode is independent of the incident mode, as after scattering the sign of the velocity component perpendicular to the interface is reversed, making heat flux in that direction after scattering anticorrelated with the flux before scattering. As we now have to consider the sequential occupancy of two or more modes, rather than compute the occupancy auto- and cross-correlations of the modes we instead consider the heat current from individual wavepackets of lattice vibration, starting from their birth, and following them as they are scattered elastically through a series of different phonon modes, up until the uncorrelated phonon-phonon scattering event that causes their annihilation. An example of such a trajectory is shown in the figure with the resulting heat flux along x and its autocorrelation function shown in Figure bellow.
</p>

<p>
  
In using this conceptually subtly different approach of following wavepackets rather than occupancy fluctuations, we can apply some of the insights from the derivation above to write the autocorrelation function of the heat current fluctuations along the x direction as the average of the heat flux autocorrelation functions of wavepackets with unit magnitude
</p>

<p align="center">
<img src="https://latex.codecogs.com/png.latex?%5Cdpi%7B300%7D%20%5Ctiny%20%5Clangle%20J_x%28t%29J_x%28t&plus;%5Ctau%29%20%3D%20%5Cfrac%7BD%7D%7BV%20%5Coverline%7B%5Ctheta%7D%7D%5Cfrac%7Be%5E%7B%5Cwidetilde%7B%5Comega%7D%7D%7D%7B%28e%5E%7B%5Cwidetilde%7B%5Comega%7D%7D-1%29%5E2%7D%28%5Chbar%20%5Comega%20%5Cnu_g%29%5E2%20%5Clangle%20A_%7Bxx%7D%28%5Ctau%2C%20%5Ctheta%2C%20%5Cmathbf%7Br%7D%2C%20%5Cwidehat%7B%5COmega%7D%29%20%5Crangle%2C" align="center" alt="drawing">
</p>

<p>
  
where D is the density of states (the number of phonon modes per unit volume), and A<sub>xx</sub>(τ,θ,r,Ω ̂ ) is the autocorrelation function of the heat flux along x created by a unit wavepacket that was born at location r, traveled initially along direction Ω ̂, and lived for duration θ, before being annihilation into the phonon bath. The total thermal conductivity reduction can thus be computed as: 
</p>

<p align="center">
<img src="https://latex.codecogs.com/png.latex?%5Cdpi%7B300%7D%20%5Ctiny%20%5Cfrac%7B%5Ckappa%28%5Ctau%29%7D%7B%5Ckappa_%7B%5Cmathrm%7Bbulk%7D%7D%7D%20%3D%20%5Cfrac%7B3%7D%7B%5Coverline%7B%5Ctheta%7D%5E2%7D%20%5Cint_0%5E%7B%5Ctau%7Dd%5Ctau%27%20%5Clangle%20A_%7Bxx%7D%28%5Ctau%27%2C%20%5Ctheta%2C%20%5Cmathbf%7Br%7D%2C%5Cwidehat%7B%5COmega%7D%29%20%5Crangle%20%3D%20%5Cfrac%7B3%7D%7B%5Coverline%7B%5Ctheta%7D%5E2%7D%20%5Clangle%20C_%7Bxx%7D%28%5Ctau%2C%5Ctheta%2C%20%5Cmathbf%7Br%7D%2C%5Cwidehat%7B%5COmega%7D%29%20%5Crangle." align="center" alt="drawing">
</p>

<p>
  
As this population phonons are in a volume that includes physical scattering centers such as pores and inclusions, in addition to averaging over the phonon lifetimes θ, the average 〈A<sub>xx</sub>(τ^',θ,r,Ω ̂ )〉 is also taken over the spatial domain r, the phonon modes Ω ̂, and the various possibilities for the reflected wave packet at each correlated scattering events. 
</p>

<p>
  
Rather than perform the average analytically, we average using Monte Carlo sampling — tracing the trajectory of wavepackets as they collide with pores and are scattered off into new directions. In this scheme we took the average HCACF from 20,000 randomly sampled wavepacket trajectories; each beginning from a randomly selected starting point, with random initial direction, and with a lifetime drawn randomly from the Poisson distribution. The single wavepacket HCACFs in the x, y, and z directions were computed numerically out to a correlation time of 20 times the mean scattering time θ ̅ to prevent truncation of contributions from long-lived phonons in the tails of the Poisson distribution. This averaging was sufficient to reduce the uncertainty in the computed values of κ<sub>bulk</sub> to < 3%. The details of how the correlated scattering from pores was modeled are described in the next section.
</p>

## Pore scattering models

<p>
  
The HCACF from the MD simulations of material containing cylindrical pores shows a single pronounced region of anticorrelation. The correlation time at which the HCACF is most negative changes with the distance d between the ranks of pores but is characterized by single effective velocity v_dip which is found to be 0.6XXX of the averaged acoustic phonon velocity. Finally, the anti-correlation effect can be very large, with the final thermal conductivity of the material containing pores κ<sub>∞</sub> being as little as 20% of the peak cumulative thermal conductivity, κ<sub>peak</sub>. This implies that the motion of phonons after scattering, on average, undoes 80% of the heat conduction by the phonons before they were scattered from the pores. With the ray tracing model, we aim to understand what factors in the scattering of phonons from pores affect these three factors: single vs multiple anticorrelation, v<sub>dip</sub>, and κ<sub>peak</sub>/κ<sub>∞</sub>.  For this, we consider the effect on the HCACF from four phenomenological models of phonon scattering from scattering centers separated by distance d in the material. Two geometries of scattering centers are considered: an array of parallel permeable planer walls, and a series of palisades of cylindrical pores. For each geometry we examined the effect of both specular and diffuse phonon scattering by the obstacles, giving us four scattering models in total. The two geometries have a single geometric degree of freedom α that is related to the probability that phonons are not scattered when they encounter the wall/palisade. 
</p>

### Diffuse and specular wall scattering models  

<p>
  
We represent the pores in MD simulation as a series of parallel planer interfaces perpendicular to the x-axis. The spacing, d, between planes is described in terms of the Knudsen number K_n=(v_g θ ̅)/d. When a phonon encounters an interface there is a finite probability 1-α that it will pass through with its trajectory unaltered, otherwise, the phonon is reflected. For the specular model, reflection involves only flipping the direction of the x-component of the velocity. In the diffuse model, a new random directory is chosen in the half-space perpendicular to the reflection plane, so that the x-component of the velocity of the scattered phonon has the opposite sign from the incident phonon.  Simulations were performed sweeping α from 0 to 1 and log⁡(Kn) from -1 to 1. Although we refer to this model as the interface model, it is a reasonable representation of scattering from rectangular pores.

### Diffuse and specular palisade scattering models 

<p>
  
With the palisade model we aim to more closely mimic the pore geometry simulate in the MD simulations. We assume that the material contains rows of cylindrical pores, each with radius r and aligned with their axis parallel to the y-direction. The pores are arrayed in the z-direction with spacing L to form a palisade fence. The geometry of the palisade is parameterized the parameter α=2r/L which is the scattering probability for phonons that meeting the palisade head-on. In general, the probability that phonons are scattered by the pores rather than passing between them is a function of incidence angle, χ, of the phonon trajectory in the x-z plane with the x-axis. The transmission probability depends on the gap between cylinders that is visible to the phonons as they travel towards the palisade, given by

<p align="center">
<img src="https://latex.codecogs.com/png.latex?%5Cdpi%7B300%7D%20%5Ctiny%20T%28%5Cchi%29%20%3D%201%20-%20%5Cfrac%7B%5Calpha%7D%7B%7C%5Ccos%20%5Cchi%7C%7D." align="center" alt="drawing">
</p>

<p>
  
Here tan⁡χ=v<sub>z</sub>/v<sub>x</sub>. The probability of transmission drops to zero at grazing angles of incidence where the shadow of the cylinder one another. Rather than models the location of cylinders explicitly we consider the probability that an incident phonon strikes a cylinder at a position with normal vector at an angle of β relative to the phonon direction. This probability is given by P(β)=cos⁡β/(sin⁡(β<sub>max</sub>)-sin⁡(β_<sub>min</sub>)), where β_<sub>min</sub> and β<sub>max</sub> are the limits to the possible incident angles that a phonon could strike the surface of a cylinder. If there is no shadowing β_<sub>min</sub>=-π/2, and β<sub>max</sub>=π/2. Shadowing occurs at angles when T(χ)<0, and in these cases: 
</p>

<p align="center">
<img src="https://latex.codecogs.com/png.latex?%5Cdpi%7B300%7D%20%5Ctiny%20%5Cbeta_%7Bmin%7D%20%3D%20%5Cbegin%7Bcases%7D%20%5Carcsin%20%5Cleft%28%201-%5Cfrac%7B2%20%5Ccos%20%5Cchi%7D%7B%5Calpha%7D%20%5Cright%20%29%20%26%20%5Ctext%7B%20for%7D%20%5Ccos%20%5Cchi%20%5Csin%20%5Cchi%20%3E%200%5C%5C%20-%5Cfrac%7B%5Cpi%7D%7B2%7D%26%20%5Ctext%7B%20otherwise%20%7D%20%5Cend%7Bcases%7D%2C" align="center" alt="drawing">
</p>

<p align="center">
<img src="https://latex.codecogs.com/png.latex?%5Cdpi%7B300%7D%20%5Ctiny%20%5Cbeta_%7Bmax%7D%20%3D%20%5Cbegin%7Bcases%7D%20-%5Carcsin%20%5Cleft%28%201-%5Cfrac%7B2%20%5Ccos%20%5Cchi%7D%7B%5Calpha%7D%20%5Cright%20%29%20%26%20%5Ctext%7B%20for%7D%20%5Ccos%20%5Cchi%20%5Csin%20%5Cchi%20%3C%200%5C%5C%20%5Cfrac%7B%5Cpi%7D%7B2%7D%26%20%5Ctext%7B%20otherwise%20%7D%20%5Cend%7Bcases%7D." align="center" alt="drawing">
</p>

<p>
  
For the specular scattering model, when a phonon’s x position lies on a palisade a pseudorandom number generator is used to decide if the phonon passed through the barrier unscattered or if it struck on of the pillars. If scattering occurs the incidence of a random incidence angle is selected from P(β) and the phonon is set on a new trajectory with a new angle 
</p>

<p align="center">
<img src="https://latex.codecogs.com/png.latex?%5Cdpi%7B300%7D%20%5Ctiny%20%5Cchi%27%20%3D%20%5Cchi&plus;%5Cpi-2%5Cbeta." align="center" alt="drawing">
</p>

<p>
  
Note that this mean-field model of scattering does not resolve the physical size of the pores and the possibility of multiple scattering between neighboring cylinders within a palisade is not considered. 
</p>

<p>
  
The model for diffuse scattering from cylindrical pores uses the same procedure to stochastically select the normal at the point on the surface of the cylinder that the phonon strikes (this has an angle χ'=χ+π-β).  A new random direction is then selected for the phonon in the half-space defined by this normal vector. 
</p>

  
