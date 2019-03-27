    ```
            runtimeParams: {
                geometry: {
                    refArea: [float],
                    momentCenter : [float, float, float],
                    momentLength : [float, float, float]
                },
                volumeOutput: {
                    primitiveVars : [true | false],
                    Cp : [true | false],
                    vorticity : [true | false],
                    residualNavierStokes: [true | false],
                    residualTurbulence : [true | false],
                    T: [true | false],
                    s: [true | false],
                    mut: [true | false],
                    mutRatio: [true | false]
                },
                surfaceOutput : {
                    primitiveVars : [true | false],
                    Cp : [true | false],
                    Cf : [true | false],
                    CfVec : [true | false],
                    yPlus : [true | false],
                    wallDistance : [true | false]
                }
                navierStokesSolver: {
                    tolerance : [float],
                    maxSteps : [int],
                    CFL: {
                        initial : [float],
                        final : [float],
                        rampSteps : [integer]
                    },
                    linearIterations : [integer],
                    kappaMUSCL : [float]
                },
                turbulenceModelSolver :
                {
                    modelType: "SpalartAllmaras" | "None",
                    tolerance : [float],
                    CFL: {
                        initial : [float],
                        final : [float],
                        rampSteps : [integer]
                    },
                    linearIterations : [integer],
                    kappaMUSCL : [float]
                },
                freestream :
                {
                    Reynolds : [float],
                    Mach : [float],
                    Temperature : [float],
                    alphaAngle : [float],
                    betaAngle : [float]
                },
                boundaries : {
                    1 : {
                        type : "NoSlipWall" | "SlipWall" | "Freestream"
                    },
                    ...
                }
            }
    ```
