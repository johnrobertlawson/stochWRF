# stochWRF
Stochastic edits to WRF 3.8.1. Repo contains (a) Python scripts that are used before running WRF, and (b) .F files (i.e., source code) and the Registry from WRF source code.

Code developed by John R. Lawson at CIMMS/NSSL and overseen by Nusrat Yussouf and Jack Kain. Successfully compiled on Cray at NSSL, March 4 2017.

## METHODOLOGY and SCIENCE
Coming soon to a paper near you.

## TO DO
  * In-line documentation to supplement this README
  * Brief outline of each stochastic edit
  * Include Python scripts that
    * imposes a fractal perturbation on the soil moisture ICs

## LIST OF CHANGES
Search for “JRL” to find locations that have been edited from WRF 3.8.1 source code. Line numbers refer to WRF 3.8.1 code, but may not line up exactly to source code due to commenting, edits afterwards, etc. Use the following numbering to refer to any issues or changes.

1. ### `dyn_em/module_first_rk_step_part1.F`

  1. Line 834 in CALL surface_driver (adding morph values from namelist for SMSM)
  2. Line 1006 in CALL pbl_driver (adding asymptotic mixing length for PSP)

2. ### `dyn_em/module_first_rk_step_part2.F`

  1. Line 205 to turn off RAND_PERT_UPDATE (we've hacked SPPT for other uses).
  1. Line 670 in CALL perturb_physics_tend (changing SPPT-scheme arguments for PSP. If SPPT scheme is to be kept, need to create duplicate of this scheme)
  2. Line 679 in CALL perturb_physics_tend (ditto)

3. ### `dyn_em/module_stoch.F`
  1. Line 716 in SUBROUTINE perturb_physics_tend (SPPT arguments for PSP in stochastic forcing)
  2. Line 723 “” “”
  3. Line 745 “” (adding types for new arguments)
  4. Line 751 “” “”
  5. Line 775 “” (Long block of commenting out old SPPT and modifying it for use with PSP)

4. ### `phys/module_physics_init`
  1. Line 198: Adding three extra SPP parameters
  2. Line 748: Defining
  3. Line 1283: Adding these parameters to MP_INIT call
  4. Line 3575: Adding these parameters to MP_INIT arguments
  5. Line 3641: Defining
  6. Line 3683: Adding to the nssl_params list to pass later

5. ### `dyn_em/start_em.F`
  1. Line 1169 in CALL phy_init (NSSL variables for SPP)
  2. Line 1420 (mod for data assimilation)

6. ### `phys/module_bl_myjpbl.F`
  1. Line 141 in SUBROUTINE MYJPBL (Adding arguments for PSP in MYJ)
  2. Line 251 (Adding types)
  3. List 267 Initialise in tile dimensions
  3. Line 417 in CALL MIXLEN (Adding L_INF to arguments)
  4. Line 447 in CALL DIFCOF (Adding ESHK, ESMK to arguments)
  5. Line 463 (Assigning ESHK ESMK to the larger array for later use)
  6. Line 786 (Compute TSQ and QSQ)
  7. Line 818 in SUBROUTINE MIXLEN (Adding EL0 to arguments)
  8. Line 850: Change intent of EL0 and remove from previous type assignment
  9. Line 1224: in SUBROUTINE DIFCOF (Adding ESH, ESM to arguments)
  10. Line 1251: Making ESH, ESM an array
  11. Line 1305: similar edits
  12. Line 1319: similar edits

7. ### `phys/module_bl_mynn.F`
(Already contains variances needed to perturb tendencies)

8. ### `phys/module_bl_shinhong.F`
  1. Line 28: Passing tsq,qsq into script
  2. Line 113: Change shinhong_tke_diag to in/out, because we want to change to "1" for PSP
  2. Line 207: Creating temporary variables for later comp, and defining tsq, qsq; initialising
  3. Line 271: Adding xkzhl to 2D call (this is the K_H values we need for variances)
  4. Line 283: Computing variances
  5. Line 319: Adding xkzhl
  6. Line 538: Added definition
  7. Line 540: Removed old definition

9. ### `phys/module_mp_nssl_2mom.F`
  1. Line 794: Added three new SPP parameters to overwrite old (fixed) values

10. ### `phys/module_pbl_driver.F`
  1. Line 127: Added L_INF to call
  2. Line 506: Added L_INF as definition
  3. Line 1245: Passes tsq, qsq to ShinHong
  4. Line 1442: Pass tsq, qsq,l_inf

11. ### `phys/module_sf_noahmpdrv.F`
  1. Line 49: Adding morphing parameters to arguments
  2. Line 418: Defined parameters
  3. Line 750: Added the parameters to call to noahmp_sflx

12. ### `phys/module_sf_noahmplsm.F`
  1. Line 369: Added morphing parameters to arguments
  2. Line 425: Define them
  3. Line 730: Adding morphing parameters to CALL ENERGY
  4. Line 1471: Adding morphing parameters to ENERGY arguments
  5. Line 1730: Defining morphing params; new temporary variables for weighting in ENERGY
  6. Line 1843: Adding morphing parameters to RADIATION call
  7. Line 1874: Weighting equations
  8. Line 1988: Adding morphing params to VEGE_FLUX call
  9. Line 2090: Adding morph params to TSNOSOT call
  10. Line 2412: Morphing params in RADIATION
  11. Line 2491: Defining params in RADIATION
  12. Line 2509: Morphing params in ALBEDO call
  13. Line 2548: Morphing params in ALBEDO arguments
  14. Line 2608: Defining params in ALBEDO
  15. Line 2699: Adding morphing params to TWOSTREAM call #1
  16. Line 2709: Adding morphing params to TWOSTREAM call #2
  17. Line 3090: Adding morphing params to TWOSTREAM arguments
  18. Line 3177: Defining params
  19. Line 3220: Computing weighting in TWOSTREAM
  20. Line 3391: Adding weighting params to VEGE_FLUX arguments
  21. Line 3604: Defining params; temporary variables for weighting
  22. Line 3761: Computing weighting in VEGE_FLUX
  23. Line 5038: Adding morphing params to TSNOBOI arguments
  24. Line 5090: Definiting params; temporary variables for weighting
  25. Line 5119: Adding weighting params to HRT call
  26. Line 5136: Computing weighting in TSNOBOI
  27. Line 5181: Adding weighting params to HRT
  28. Line 5229: Defining params
  29. Line 5262: Computing weighting in HRT

13. ### `phys/module_surface_driver`
  1. Line 274: Adding morphing parameters to  SURFACE_DRIVER
  2. Line 1324: Defining those params
  3. Line 2940: Adding morphing parameters to NOAHMPLSM call

14. ### `Registry/Registry.EM_COMMON`
(Search for the following)
  1. L_INF (added)
  2. Tsq (editing to output to history)
  3. QSQ (edited to output to history)
  4. RTHBLTEN (“)
  5. RQVBLTEN (“)
  6. NSSL_ALPHAR (added)
  7. NSSL_EHW0 (added)
  8. NSSL_EHLW0 (added)
  9. MORPHR_CRS (added)
  10. MORPHR_TBOT (added)
  11. MORPHR_BTR (added)
  12. MORPHR_RAD (added)
  13. MORPHTH_CRS (added)
  14. MORPHTH_TBOT (added)
  15. MORPHTH_BTR (added)
  16. MORPHTH_RAD (added)
  17. MYJ package declaration needs "L_INF","TSQ", "QSQ"
  18. Shinhong package declaration needs "TSQ", "QSQ"

15. ### `Registry/registry.stoch`
(Search for the following)
  1. AML (added)
  2. ASHC (added)
