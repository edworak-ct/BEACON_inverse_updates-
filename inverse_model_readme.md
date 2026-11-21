# inverse_model_toshare.ipynb

Geostrophic inverse box model for the Bellingshuasen James JR165 Ruan and Amundsen Palmer NBP0702
CTD datasets. Instead of assuming a level of no motion (like `Palmer_data_version3.ipynb`), 
this notebook **solves for** a barotropic reference-velocity (`V_ref`) per station pair, by forcing heat/salt/oxygen anomaly transport to balance across a closed box built from one or more CTD sections.

## Data it needs
1. Long format CTD tables 
   - `jr165_ctd_long.pkl` / `palmer_nbp0702_ctd_long.pkl` 
   - one row per sample: `station`, `lat`, `lon`, `pres`, `potemp`, `salin`, `oxy`
   - combined James data into one long dataframe, was previously split into three matlab files 

2. GEBCO bathymetry
   - `/home/edworak/eady-data1/Palmer/gebco_2025_sub_ice.nc` 
   - only needed for `make_cross_sec_plots`
   - need to download from the GEBCO website as file was too large to download 
   - https://www.gebco.net/data-products/gridded-bathymetry-data/southern-ocean

## Functions
1. **`tracer_inverse`** 
   - grids each station's T/S/O onto a common pressure axis and
      computes a bottom-triangle tracer estimate for mismatched station pairs

2. **`vel_inverse`** 
   — geostrophic shear from the density field, the bottom-triangle
    velocity treatment, and a per-section velocity calibration (`adj_table`)
   - uses dynamic method with geopotential anomaly instead of thermal wind 

3. **`inverse_solver`** 
   — combines any number of sections into one closed box, builds the tracer-budget sensitivity matrix, and solves the weighted-least-norm `V_ref` (one value per station pair)

4. **`build_pair_df`** 
   — flattens everything into one per-pair, per-depth dataframe for plotting
   - makes it easier to handle 

## Plotting functions 
1. `vertical_profile_subplots`
2. `transport_adjustment_subplots` 
   - the "before"/"after barotropic adjustment" overturning curve
3. `transport_sections_subplots`
   - show the transport for sections in the box and for the box 
4. `reference_vel_transport_bystation` 
   - display reference velocity and transport with and without adjustment per station pair 
5. `make_cross_sec_plots` 
   - from `Palmer_data_version3.ipynb`, now with `V_ref` instead of a chosen LNM, with GEBCO bathymetry    and a `max_latlon` box-edge cutoff

## Known limitation 
`inverse_solver` solves a **3-equation** (heat/salt/oxygen) tracer budget, solves for 
one `V_ref` unknown per station pair. System is very underdetermined and not well conditioned since theres many unkowns and only 3 scalar equations.

A 4th row (`A_vol`/`B_vol`, commented out) would also enforce box volume
conservation across the box. The system is too unstable right now to do this, but if the code is reset to solve for reference velocity per section instead of per station pair then volume conservation could be enforced (3–4 unknowns against 3–4 equations, a well-posed system). 

## Other notes
- Station display order in `make_cross_sec_plots` always matches whatever order you pass
  into `station_list` — it does not reorder to match geography
- The bottom line in `make_cross_sec_plots` blends GEBCO with each station's own recorded
  CTD depth (trusts whichever is deeper) — it is not a dedicated bathymetry survey
