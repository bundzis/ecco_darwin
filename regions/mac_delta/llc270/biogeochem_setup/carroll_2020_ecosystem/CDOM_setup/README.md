# Baseline Setup Content

## Introduction

The following set up is regional configuration of the Southeastern Beaufort Sea extracted from Carroll et al. 2020 ECCO-Darwin global llc270 setup. This version is validated for darwin3 checkpoint 68o.

This setup is an evolution of tDOC setup including (See Bertin et al., 2023):
- An explicit refractory DOC (rDOC) tracer.
- 6 Mackenzie river biogeochemical elements contribution. (DOC,DIC,Alk,DON,DOP,DSi)
- An explicit CDOM tracer field (forced by rDOC and forcing rDOC)
- Consider CDOM attenuation on physical light and temperature

## Ecosystem Characteristics

The Ecosystem simulated contains llc270 global ecosystem (see Carroll et al 2020):
- 5 phytoplankton type:
  - Diatoms
  - Large Eukaryotes
  - Synechococcus type 1
  - Synechococcus type 2
  - Prochlorococcus
- 2 Zooplankton types:
  - small zooplankton
  - Large zooplankton

## Tracers (n=33)

|1. DIC  | 12. DOFe | 23. c2   |
| ------ | -------- | -------- |
|2. NO3  | 13. POC  | 24. c3   |
|3. NO2  | 14. PON  | 25. c4   |
|4. NH4  | 15. POP  | 26. c5   |
|5. PO4  | 16. POFe | 27. c6   |
|6. FeT  | 17. POSi | 28. c7   |
|7. SiO2 | 18. PIC  | 29. Chl1 |
|8. DOC  | 19. ALK  | 30. Chl2 |
|9. rDOC | 20. O2   | 31. Chl3 |
|10. DON | 21. CDOM | 32. Chl4 |
|11. DOP | 22. c1   | 32. Chl5 |


### Linking Inputs/Setup Files to Run Directory 
(Notes from Bri)
Some of the files are in a slightly different place than in the git repo instructions so here are new ones that I am trying...

New Locations for downloads on EarthData:
- 

New commdands for linking 
```
-- Link to the executable --
ln -sf ../build/mitgcmuv .
-- Link to Atmospheric forcing --
ln -sf ../../../ED-SBS_Bertin_etal_2023/Forcing/era_xx .
-- Link to Freshwater runoff --
ln -sf ../../Forcing/river_runoff/Freshwater/AGRO_interan .
ln -sf ../../Forcing/river_runoff/Temperature/Tokuda_Mac270modif .
-- Link to Biogeochemical runoff --
ln -sf ../../Forcing/river_runoff/Nutrients/CDOM_setup/L50_R48_C2/tDOCl
ln -sf ../../Forcing/river_runoff/Nutrients/CDOM_setup/L50_R48_C2/tDOCr
ln -sf ../../Forcing/river_runoff/Nutrients/CDOM_setup/L50_R48_C2/tDOCc
ln -sf ../../Forcing/river_runoff/Nutrients/CDOM_setup/Tank_DIC/tAlk .
ln -sf ../../Forcing/river_runoff/Nutrients/CDOM_setup/Tank_DIC/tDIC .
ln -sf ../../Forcing/river_runoff/Nutrients/CDOM_setup/AGRO_nutclim/tDON .
ln -sf ../../Forcing/river_runoff/Nutrients/CDOM_setup/AGRO_nutclim/tDOP .
ln -sf ../../Forcing/river_runoff/Nutrients/CDOM_setup/AGRO_nutclim/tDSi .
-- Link to Initial and Boundary Conditions --
ln -sf ../../Forcing/ED-SBSv3/* 
ln -sf ../../Forcing/ED-SBSv3/eta_adjust/OBWu_Mac_46x68.bal_ROFF OBWu_Mac_46x68.balance
-- Copy Setup files --
cp ../../input/* .
```

Run the code 
```
mpirun --oversubscribe -np 8 ./mitgcmuv &
```
Errors and fixes from Running 
- 'filename: Tokuda_ED-SBS/wattmp_mac270modif_2007.data does not exist'
  - Fix: Renamed Tokuda_Mac270modif linked folder to Tokuda_ED_SBS
    - Nope
  - Made copy of original in Forcing and called it Tokuda_ED-SBS then linked it in run directory 
    - ln -sf ../../Forcing/river_runoff/Temperature/Tokuda_ED-SBS .
    - I think that worked?
- New error but no clear message, just a crash and fortran errors: 
      "Program received signal SIGSEGV: Segmentation fault - invalid memory reference.

      Backtrace for this error:

      Could not print backtrace: executable file is not an executable"
  - Trying new run command `/opt/local/bin/mpirun-openmpi-gcc14 --oversubscribe -np 8 ./mitgcmuv`
    - That did not work



Original commands for linking 
```
cd ../run
mkdir diags diags/daily diags/budget
-- Link to the executable --
ln -sf ../build/mitgcmuv .
-- Link to atmospheric forcing --
ln -sf ../../Forcing/era_xx .
-- Link to Freswater runnoff --
ln -sf /../../Forcing/river_runoff/Freswater/AGRO_interan .
ln -sf /../../Forcing/river_runoff/Temperature/Tokuda_Mac270modif .
-- Link to Biogeochemical runoff --
ln -sf /../../Forcing/river_runoff/Nutrients/Bertin_etal_21/Interannual/r46/L50_R48_C2/tDOCl .
ln -sf /../../Forcing/river_runoff/Nutrients/Bertin_etal_21/Interannual/r46/L50_R48_C2/tDOCr .
#ln -sf /../../Forcing/river_runoff/Nutrients/Bertin_etal_21/Interannual/L50_R48_C2/tDOCc . # Does not exist on Earthdata
ln -sf /../../Forcing/river_runoff/Nutrients/Tank_etal_12/Interannual/r46/tAlk .
ln -sf /../../Forcing/river_runoff/Nutrients/Tank_etal_12/Interannual/r46/tDIC .
ln -sf /../../Forcing/river_runoff/Nutrients/GNW2_NutCim/r46/tDON .
ln -sf /../../Forcing/river_runoff/Nutrients/GNW2_NutCim/r46/tDOP .
ln -sf /../../Forcing/river_runoff/Nutrients/GNW2_NutCim/r46/tDSi .
-- Link to Initial and Boundary conditions -- 
ln -sf /../../Forcing/run_template/* .
#ln -sf /../../Forcing/run_template/eta_adjust/OBWu_Mac_46x68.bal_ROFF OBWu_Mac_46x68.balance # OG but OBWu_Mac_46x68.bal_ROFF is not on Earthdata
ln -sf /../../Forcing/run_template/eta_adjust/OBWu_Mac_46x68.balance .
-- Copy setup files -- 
cp ../../input/* .
```