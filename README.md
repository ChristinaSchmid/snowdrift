# snowdrift

Implementation of a snowdrift scheme in the Weather Research and Forecasting Model (WRF)

Detail code information and results published in: Schmid, C (2021). Implementierung eines Schneedriftmoduls in das Weather Research and Forecasting (WRF) Modell und eine erste Evaluation, https://opus4.kobv.de/opus4-fau/frontdoor/index/index/docId/17236.

Please read careful: The code only works with the land-surface-model NoahMP (sf_surface_physics= 4)

Copy the files: module_snowdriver.F, module_snower.F, module_snowset.F, module_snowsubl.F, module_snowvel.F in the ~/WRFV4/phys folder. Open the file "Makefile" in this folder and add the lines 
module_snowdriver.o \
module_snower.o  \
module_snowset.o \ 
module_snowsubl.o  \
module_snowvel.o \

The other files already exist in WRF, just a few lines need to be added. For WRF Version 4.1.2 the files could be replaced by the ones here.. but its safer to just copy the lines and add them to your existing files.

Search for “cschmid” in the file Registry.EM_COMMON and add the additional lines in ~/WRFV4/Registry/Registry.EM_COMMON This will add an additional package and several variables. The description of variables and constants can be found in the file: List of variables and constants.txt

Search for “cschmid” in the file module_first_rk_step_part1.F: add the lines in the file ~WRFV4/dyn_em/module_first_rk_step_part1.F. This will call the snowdrift driver “module_snowdriver.F“

Search for “cschmid” in the file solve_em.F: add the lines in the file: ~WRFV4/dyn_em/solve_em.F This will set negative concentrations of the snowdrift tracer to zero. If not, negative concentration may occur and will affect the snowdrift fluxes. But by setting the concentration to zero, mass conservation could be hurt. We assume that this effect is only small. Other solutions could be useful.

If you want to use the code for an ideal setup without NoahMP as land-surface-model: Please search in the module_snower.F, module_snowset.F, and module_snowdriver.F for the variable "snicexy" and delete the variable / lines, that contain the variable.

Namelist example: 
&physics 
sf_surface_physics= 4, 4, 4, 4 !noah_mp (4) (snowdrift scheme only works with noah_mp) 
snow_opt= 1, 1, 1, 1 !turn on (1) snow_opt for every domain! 
er_opt= 0, 0, 0, 1 !turn erosion on (1), for domains where snowdrift is calculated 
subl_opt= 0, 0, 0, 2 !turn on sublimation with feedback (2) or without feedback (1) 
dep_opt= 0, 0, 0, 1 !turn on deposition (1) 
ustlim= 0, 0, 0, 1.5 !set a limit for ustar, if needed (and er_opt =1) 
rgro= 0, 0, 0, 5E-2 !set ground particle radius 
esalt= 0, 0, 0, 5E-4 !set saltation coefficient, default 5E-4 
pavel= 0, 0, 0, 0.2 !set a constant settling velocity
