# Hardware configuration (mmu_hardware.cfg explained)

This will vary slightly depending on your particular brand of MMU but the steps are essentially the same with some being dependent on hardware configuration.

### Step 1. Validate your mmu_hardware.cfg configuration
This can be daunting but the interactive installer will make the process easy for common mcu's designed for a MMU (e.g. ERCF EASY-BRD, Burrows ERB, etc)

Assuming you are familiar with all that there is one new IMPORTANT step that must be perform by hand.  You must move most of your `[extruder]` definition into `mmu_hardware.cfg`. This is best illustrated with my actual configuration (pulled from the top of mmu_hardware.cfg):
  
    # HOMING CAPABLE EXTRUDER --------------------------------------------------------------------------------------------------
    # With Happy Hare, it is important that the extruder stepper definition is moved here to allow for sophisticated homing and syncing
    # options.  This definition replaces the stepper definition part of you existing [extruder] definition.
    #
    # IMPORTANT: Move the complete stepper driver configuration associated with regular extruder here
    [tmc2209 manual_extruder_stepper extruder]
    uart_pin: E_TMCUART
    interpolate: true
    run_current: 0.55				# LDO 36STH20-1004AHG.  Match to macro below
    hold_current: 0.4
    sense_resistor: 0.110
    stealthchop_threshold: 0			# Spreadcycle (better for extruder)
    #
    # Uncomment two lines below if you have TMC and want the ability to use filament "touch" homing to nozzle
    diag_pin: E_DIAG				# Set to MCU pin connected to TMC DIAG pin for extruder
    driver_SGTHRS: 60				# 255 is most sensitive value, 0 is least sensitive
    
    # Define just your printer's extruder stepper here. Valid config options are:
    # step_pin, dir_pin, enable_pin, rotation_distance, gear_ratio, microsteps, full_steps_per_rotation
    # pressure_advance, pressure_advance_smooth_time
    # IMPORTANT: REMOVE these settings from your existing [extruder] configuration BUT LEAVE ALL OTHER parameters!
    #
    [manual_extruder_stepper extruder]
    step_pin: E_STEP
    dir_pin: !E_DIR
    enable_pin: !E_ENABLE
    microsteps: 64
    rotation_distance: 22.4522               # Calibrated by hand
    gear_ratio: 50:10
    full_steps_per_rotation: 200
    pressure_advance: 0.035                 # Fairly arbitary default
    pressure_advance_smooth_time: 0.040		  # Recommended default
    #
    # Uncomment two lines below to enable filament "touch" homing option to nozzle
    extra_endstop_pins: tmc2209_extruder:virtual_endstop
    extra_endstop_names: mmu_ext_touch

The first TMC definition was previous `[tmc2209 extruder]` and is moved in here as `[tmc2209 manual_extruder_stepper extruder]`. The original `[tmc2209 extruder]` should be deleted or commented out.
The second definion is the elements that define the extruder stepper motor taken from my original `[extruder]` definition. These parameters include only: `step_pin`, `dir_pin`, `enable_pin`, `rotation_distance`, `gear_ratio`, `microsteps`, `full_steps_per_rotation`, `pressure_advance` and `pressure_advance_smooth_time`.  Leave all the other parameters (things like pid controls, sensor type, etc) in the original `[extruder]` definition in your `printer.cfg` file. Make sense? The stepper definition moved here, the rest of the toolhead extruder definition left where it was originally.

For now, ignore all the enstop setup.  A separate guide on endstops and homing can be found in the MMU movement guide [found here](doc/movement_and_homing.md)

### Step 2. TODO

TODO