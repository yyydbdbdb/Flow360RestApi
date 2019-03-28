# runtimeParameters
## freestream :
This dictionary defines the freestream condition.  These
conditions apply at all *freestream* boundary conditions.  They also serve
as reference quantities for nondimensionalizing forces and moments.

Flow360 assumes the ideal gas law with a ratio of the specific heats of 1.4.
The solver is nondimensionalized with respect to the freestream density and
speed of sound.  Therefore, the quantities needs to be set are:

Example: 
```
freestream: {
    Reynolds: 5000000.0,
    Mach: 0.8,
    Temperature: 300.0,
    alphaAngle: 8.0,
    betaAngle: 4.0
}
```

### Reynolds : *float* / "inf"
The Reynolds number is rho\_inf * U\_inf * L / mu\_inf. The four ingredients are
1. rho\_inf is the freestream density.  **Note**: the user does not set
rho\_inf because the code is nondimensionalized with respect to rho\_inf.
2. U\_inf is freestream velocity, which is equal to C\_inf * Mach
where C\_inf is speed of sound at freestream condition.
**Note**: the user does not set
C\_inf because the code is nondimensionalized with respect to C\_inf.
3. L = 1 is the grid unit.
**Note**: that this Reynolds number is not based on any length scale set in
the *geometry* dictionary.
4. mu\_inf, the viscosity in freestream temperature, is thus controlled by
setting this Reynolds number.

The actual viscosity in the flow field is affected by temperature (see
description of Sutherland's law under *Temperature* shortly below)
and turbulence model (see *turbulenceModelSolver*.)

A special value, "inf" (make sure use quotes), sets the viscosity to 0.

### Mach : *float*
Sets the freestream Mach number.  This number is also sets
the freestream velocity, because C\_inf, the speed of sound at freestream
condition, is the unit of velocity in the code.

### Temperature : *float*
Sets the freestream temperature T\_inf in **Kelvin**.  This temperature is used
to compute laminar viscosity via the Sutherland's law,
mu\_lam T\_inf^1.5 / (T\_inf + 110.4K) = mu\_inf T^1.5 / (T + 110.4K).
Here mu\_inf is set by the *Reynolds* number descripbed above.

### alphaAngle : *float*
Sets the angle of attack in degrees by setting the direction of the freestream
velocity together with *betaAngle*.
Positive *alphaAngle* indicates a positive z-component in the
freestream velocity.
When the freestream velocity vector is projected to the x-z plane,
*alphaAngle* is the angle between the projected vector and the x-axis.
See formula in *betaAngle*.

### betaAngle : *float*
Sets the side slip angle in degrees by setting the direction of the freestream
velocity together with *alphaAngle*.  It is the angle between the freestream
velocity and the x-z plane.  Positive *betaAngle* indicates freestream
coming from the positive y-axis towards the negative y-axis.  *betaAngle* of
+30 degrees, for example, indicates that wind is coming from one o'clock,
assuming twelve o'clock is facing towards the x-axis and the z-axis points
up.

The direction of the freestream vector is,

* x-component: +cos(betaAngle) * cos(alphaAngle)
* y-component: -sin(betaAngle)
* z-component: +cos(betaAngle) * sin(alphaAngle)

## geometry:
This dictionary defines the reference geometric values for nondimensionalizing
forces to produce Cl, Cd, Cfx, Cfy, Cfz, Cmx, Cmy, and Cmz.
Note that these geometric quantities are **not** used in definining the
Reynolds number in the *freestream* dictionary.

Example:
```
geometry: {
    refArea: 100.0,
    momentCenter : [100.0, 0.0, 10.0],
    momentLength : [100.0, 500.0, 500.0]
}
```

### refArea: *float*,
  Provides the reference area for calculating
  Cl, Cd, Cfx, Cfy, Cfz, Cmx, Cmy, and Cmz.

  Cfx, Cfy and Cfz are the forces in the x, y, and z directions
  divided by the dynamic pressure at freestream then divided by this reference
  area.

  Cd is the force in the freestream direction (see *betaAngle* for the
  freestream vector) nondimensionalized by dividing the same freestream dynamic
  pressure and this reference area.  Cl is the force in the x-z plane that is
  perpendicular to the freestream direction, nondimensionalized by the
  same procedure.  In particular,
  Cl = Cfz * cos(alphaAngle) - Cfx * sin(alphaAngle)

### momentCenter: [*float*, *float*, *float*],
  Provides the reference location about which Cmx, Cmy, and Cmz are defined.
  These moments are the torque about this moment center, nondimensionalized
  by dividing the moments by the dynamic pressure at freestream, then by
  the reference area, and finaly by the respective *momentLength*.

### momentLength: [*float*, *float*, *float*],
  Provides the length with which Cmx, Cmy, and Cmz are nondimensionalized.

## navierStokesSolver:
This dictionary controls the Navier-Stokes solver.

### tolerance : *float* (default: 1E-10)
The solution is considered converged when the residual norm of all five
equations, including the mass conservation equation, the x, y, and z momentum
conservation equations, and the energy conservation equation drop below the
tolerance.

Here, the residual norm is defined as the average of the absolute value of
the residual on all grid points.  We define the residual of a differential
equation on a grid point as

* The residual of the differential equation, multiplied by the size of the
control volume associated with the grid point, divided by the total surface area
of that control volume.

Because all five Navier-Stokes equations are conservation laws, this
definition is equivalent to the residual of the integral form of the
conservation law on the control volume divided by the total surface area of
that control volume.

### maxSteps : *int* (default: 10000)
If *tolerance* is not reached, the solver will advance for *maxSteps* steps
before completion.

### CFL:
This is a dictionary that controls the psuedo time stepping.
It containing three entries:

####     initial : *float* (default: 10.0)
This is the CFL (Courant–Friedrichs–Lewy) number of the first time step.

####     final : *float* (default: 200.0)
This is the CFL (Courant–Friedrichs–Lewy) number after the CFL-ramping is
completed.

####     rampSteps : *integer* (default: 200)
This is the CFL (Courant–Friedrichs–Lewy) number after the CFL-ramping is
completed.

### linearIterations : *integer* (default: 25)
### kappaMUSCL : *float*

## turbulenceModelSolver :
### modelType: "SpalartAllmaras" | "None"
### tolerance : *float*
### CFL: {
#### initial : *float*
#### final : *float*
#### rampSteps : *integer*
### linearIterations : *integer*
### kappaMUSCL : *float*

## boundaries:
### *int*:
####     type : "NoSlipWall" | "SlipWall" | "Freestream"
## volumeOutput:

The booleans in this section controls what variables will be in the volume
visualization file.  Default value is highlighted.

### primitiveVars : **true** | false
These variables include density, x, y, z velocity, and pressure.

### Cp : true | **false**
Pressure, nondimensionalized by the dynamic pressure, 1/2 rho U^2,
calculated with the freestream density and velocity, both prescribed in the
**freestream** dictionary.

### T: true | **false**
Temperature field.

### s: true | **false**
Entropy field.

### vorticity : true | **false**
Vorticity field.

### mut: true | **false**
Turbulent viscosity field.

### mutRatio: true | **false**
Turbulent viscosity over laminar viscosity ratio.

### wallDistance : true | **false**
Distance to the nearest no slip wall.

### residualNavierStokes: true | **false**
This include the mass, x, y, z, momentum and energy residuals of the
Navier-Stokes equation.

### residualTurbulence : true | **false**
Residual(s) of the turbulence equation(s).

## surfaceOutput:
The booleans in this section controls what variables will be in the surface
visualization files.  Default value is highlighted

### primitiveVars : **true** | false
These variables include density, x, y, z velocity, and pressure.

### Cp : **true** | false
Pressure, nondimensionalized by the dynamic pressure, 1/2 rho U^2,
calculated with the freestream density and velocity, both prescribed in the
**freestream** dictionary.

### Cf : **true** | false
Magnitude of friction force per unit area, a.k.a., wall shear stress,
nondimensionalized by the dynamic pressure,
1/2 rho U^2, calculated with the freestream density and velocity, both
prescribed in the **freestream** dictionary.

### CfVec : **true** | false
Nondimensionalized wall shear stress (see Cf) as a vector tangential
to the wall.

### yPlus : **true** | false
Wall distance of the nearest off-wall grid point, nondimensionalized by
dividing the laminar viscosity and multiplying the friction velocity.
Here the friction velocity is the sqaure root of tau/rho, the wall shear stress
divided by the local density at the wall.

