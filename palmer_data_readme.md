# Palmer_data_version3.ipynb

Hydrography cross-section notebook for the Palmer NBP0702 CTD dataset in 2007 from the Amundsen Sea. This code builds hydrographic cross-sections and reference-velocity/transport estimates using a **chosen** level of no motion (LNM).  
It uses different methods i.e. constant v_ref, isotherm LNM, isopycnal LNM, based on the LNM it assumes a reference velocity from the velocity at that depth. Based on thermal wind equations. 


## Data 
- The Palmer/NBP0702 CTD data as a dataframe `amdf`, set up as one column set per station 

- GEBCO bathymetry: used for cross section and map bathymetry  
- need to download from the GEBCO website as file was too large to download 
- https://www.gebco.net/data-products/gridded-bathymetry-data/southern-ocean

- RTopo-2 (ice shelf geometry), used for the map/ice-shelf plots near the top. (provided data set)
- if needed: https://doi.pangaea.de/10.1594/PANGAEA.856844 

## Functions 
1. **`bottom_triangles`** 
   - Thompson & Heywood (2008) treatment for the depth mismatch between two stations in a pair
   - extends the shallower station in a station pair to the deeper one 

2. **`isotherm_lnm` / `isopycnal_lnm`** 
   - locate a spatially-varying level of no motion from a chosen isotherm or isopycnal
   - good to use 0 C or -0.5 C for the isotherm 
   - good to use 27.5 for isopycnal 

3. **`get_station_locs` / `bathy_profile` / `ctd_bottom_profile`** 
   — station positions, along-track distance, and a bottom depth that takes deeper position from GEBCO and station's 
     max CTD depth

4. **`cross_section_depth` / `cross_section_density`** 
   — build the per-pair velocity/shear/transport table, in depth-space or density-space, for chosen LNM
   - processes through data by bins of depth or density in sequential order 

5. **`interp_cast` / `interp_pair`** 
   — grid the sparse station/pair data onto a dense, continuous along-track axis so the plots look smooth 

6. **`method_config`** 
   — maps a method name (`"lnm_isotherm"`, `"lnm_isopycnal"`, `"zero_profile"`, `"zero_section"`) to its default LNM depth/       isotherm temperature/isopycnal density and label names

## Plotting
1. **`make_cross_sec_plots`** 
   — the main figure: temperature, salinity, density, shear, and transport-per-unit-depth panels, with GEBCO bathymetry and station labels.

2. **`make_TS_plots` / `make_combined_TS_plots` / `make_TS_heatmap`** 
   — T-S diagrams. 
   - make_TS_plots : simple T-S 
   - make_combined_TS_plots : combine multiple sections (a box) into one T-S 
   - make_TS_heatmap : a nice plot to show water binned by T and S properties, with the count of points and transport per bin 

3. **`plot_overturning_sections` / `depth_density_scale_functions`** 
   — overturning streamfunction in density coordinates (style of Ruan et al.'s Fig. 10)
   - uses depth_density_scale_functions to translate depth scale to equivalent density scale 

## Sections already set up 
There are stations set up for the West, Shelf, and East stations for different size boxes across the Amundsen region. 
Standard analysis is done on the 'huge box', theres also three-sided box, a smaller box. Some ice shelf sections are included for separate analysis. 

## Relationship to the inverse model notebook
This notebook assumes a level of no motion rather than solving for one. For a box-closure
inverse solve that solves for a reference velocity instead of assuming it, see
`inverse_model_toshare.ipynb`. Many of this notebook's functions (bathymetry,
`get_station_locs`, `make_cross_sec_plots`) were copied over there and adapted to use the
solved velocity instead of a chosen LNM.

