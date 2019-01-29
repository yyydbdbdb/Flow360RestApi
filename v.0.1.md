* [GetMeshInfo](#getmeshinfo)
* [DeleteMesh](#deletemesh)
* [AddMesh](#addmesh)
* [ListMeshes](#listmeshes)
* [GetCaseInfo](#getcaseinfo)
* [GetCaseStdout](#getcasestdout)
* [GetCaseConvergence](#getcaseconvergence)
* [GetCaseSolution](#getcasesolution)
* [DeleteCase](#deletecase)
* [PauseResumeCase](#pauseresumecase)
* [SubmitCase](#submitcase)
* [ForkCase](#forkcase)
* [ListCases](#listcases)

----
# GetMeshInfo

Get information about an uploading or uploaded mesh.

* **URL**

  /get-mesh-info?meshId=someid

* **Method:**
  
  `GET`
    
*  **URL Params** 

   **Required:**
 
   `meshId=[string]`

   **Optional:**
 
   None

* **Success Response:**
  
    * **Code:** 200 <br />
    **Content:** 
        ```
        {
            meshId : [string],
            size : [integer size in bytes],
            addTime: [string in yyyy:mm:dd:hh:mm:ss.ssssss format],
            status: [ waitingForUpload | uploading | uploaded | deleted],
            name: [string],
            tags: [list of strings],
            format: ["aflr3"],
            endianness: ["little" | "big"]
        }
        ```
 
* **Error Response:**

  * **Code:** 401 UNAUTHORIZED <br />
    **Content:** `{ error : "unauthorized" }`

  OR

  * **Code:** 404 NOT FOUND <br />
    **Content:** `{ error : "objectNotFound" }`

* **Sample Call:**
  ```
  $.ajax({
      url: "/get-mesh-info?meshId=abdcefg",
      dataType: "json",
      type : "GET",
      success : function(r) {
           console.log(r);
      }
  });
  ```

* **Notes:**

    Time should be in UTC.


----
# DeleteMesh

  Delete a mesh 

* **URL**

  /delete-mesh?meshId=someid

* **Method:**
  
  `DELETE`
  
*  **URL Params**

   **Required:**
 
   `meshId=[string]`

   **Optional:**
 
   None

* **Success Response:**
  
  * **Code:** 204 <br />
    **Content:** None
 
* **Error Response:**

  * **Code:** 401 UNAUTHORIZED <br />
    **Content:** `{ error : "unauthorized" }`


* **Sample Call:**

  ```
  $.ajax({
      url: "/delete-mesh?meshId=abcdef",
      dataType: "json",
      type : "DELETE",
      success : function() {
          console.log("mesh deleted");
      }
  });
  ```


----
# AddMesh

  Add a mesh with name, tags, etc, but do not start upload yet.

* **URL**

  /mesh/add-mesh

* **Method:**
  
  `POST`
  
*  **URL Params**

   None

* **Data Params**

    **Required:**

    ```
    {
        name: [string],
        tag: [list of strings]
        format: ["aflr3"],
        endianness: ["little" | "big"],
        noSlipWalls: [list of integers]
    }
    ```

    **Optional:**

* **Success Response:**
  
    * **Code:** 200 <br />
    **Content:** 
        ```
        {
        meshId : [integer],
        addTime: [string in yyyy:mm:dd:hh:mm:ss.ssssss format]
        }
        ```
 
* **Error Response:**

  * **Code:** 401 UNAUTHORIZED <br />
    **Content:** `{ error : "unauthorized" }`

  OR

  * **Code:** 400 BAD REQUEST <br />
    **Content:** `{ error :
        "invalidName" | "invalidTag"
        | "invalidFormat" | "invalidEndianness" }`


* **Sample Call:**

   ```
    $.ajax({
    url: "/add-mesh",
    dataType: "json",
    type : "POST",
    data : {
        endianness: "little",
        name: "FullGoemTakeOffExtFlap20degRefineLvl5",
        tags: ["Full Geometry", "Take off", "Extended Flap", "20 Degrees",
               "Potential stall"],
        noSlipWalls: [2, 3, 5, 7, 9]
    },
    success : function(r) {
      console.log(r);
    }
  });
  ```

* **Notes:**

  A user is allowed to add multiple meshes of the same name, for which
  the server will return different meshId's.
  Thus, UI designers should beware that this call is not idempotent.

  Time in response should be in UTC.


----
# ListMeshes

List all meshes belonging to the user

* **URL**

  /list-meshes?name=somename,status=somestatus

* **Method:**
  
  `GET`
    
*  **URL Params** 

   **Required:**
 
    None

   **Optional:**
 
    `name=[string]`

    `status=[waitingForUpload | uploading | uploaded | deleted]`

* **Success Response:**
  
    * **Code:** 200 <br />
    **Content:** 
        ```
        {
        meshIds : [list of strings]
        }
        ```
 
* **Error Response:**

  * **Code:** 401 UNAUTHORIZED <br />
    **Content:** `{ error : "Log in" }`

* **Sample Call:**
   ```
    $.ajax({
    url: "/mesh?name=somename",
    dataType: "json",
    type : "GET",
    success : function(r) {
      console.log(r);
    }
  });
  ```

* **Notes:**
      
    Time should be in UTC.

    If `tag` contains multiple tags, server will select meshes that has
    *ALL* these tags.  If one want to list meshes that has
    *ANY* tag from a list, the client should call this method multiple
    times, each with a single tag, then perform union on the returned
    values.


----
# GetCaseInfo

Get information about a submitted case.

* **URL**

  /case/:caseId

* **Method:**
  
  `GET`
    
*  **URL Params** 

   **Required:**
 
   `caseId=[integer]`

   **Optional:**
 
   None

* **Success Response:**
  
    * **Code:** 200 <br />
    **Content:** 
    ```
        {
            caseId : [integer],
            meshId : [integer],
            parentCaseId : [none | integer],
            cost : [integer in simulation cost units],
            diskUsage : [integer in disk usage units],
            submitTime: [string in yyyy:mm:dd:hh:mm:ss.ssssss format],
            status: [Queued | Preprocessing | Starting | Running | Paused
                     | Deleted | Completed | Failed],
            name: [string],
            tag: [list of strings],
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
                    modelType: "SpalartAllmaras" | none,
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
        }
    ```
 
* **Error Response:**

  * **Code:** 401 UNAUTHORIZED <br />
    **Content:** `{ error : "Log in" }`

  OR

  * **Code:** 404 NOT FOUND <br />
    **Content:** `{ error : "Case does not exist" }`

* **Sample Call:**
   ```
    $.ajax({
    url: "/case/1",
    dataType: "json",
    type : "GET",
    success : function(r) {
      console.log(r);
    }
  });
  ```

* **Notes:**
    
    Time in response should be in UTC.



----
# GetCaseStdout

Get full details of stdout, either
starting from beginning or from a given line number.

* **URL**

  /case/:caseId/stdout

* **Method:**
  
  `GET`
    
*  **URL Params** 

   **Required:**
 
   `caseId=[integer]`

   **Optional:**
 
   `startLine=[integer]`

* **Success Response:**
  
    * **Code:** 200 <br />
    **Content:** 
        ```
        {
        numLines: [integer],
        stdout: [a potentially quite long string]
        }
        ```
 
* **Error Response:**

  * **Code:** 401 UNAUTHORIZED <br />
    **Content:** `{ error : "Log in" }`

  OR

  * **Code:** 404 NOT FOUND <br />
    **Content:** `{ error : "Case does not exist" }`

* **Sample Call:**
   ```
    $.ajax({
    url: "/case/1/stdout",
    dataType: "json",
    type : "GET",
    data : {
        startLine: 1000
    },
    success : function(r) {
      console.log(r);
    }
  });
  ```

* **Notes:**

    When a case is running, stdout is constantly updated.  Expect maxage
    of a few seconds to sub-second.

    If startLine exceeds the current length of stdout, numLines in response
    will be negative.


----
# GetCaseConvergence

Get convergence history

* **URL**

  /case/:caseId/convergence

* **Method:**
  
  `GET`
    
*  **URL Params** 

   **Required:**
 
   `caseId=[integer]`

   **Optional:**
 
   None

* **Success Response:**
  
    * **Code:** 200 <br />
    **Content:** 
        ```
        {
        numSteps: [integer],
        residualDensity: [list of floats of length :numSteps],
        residualMomX: [list of floats of length :numSteps],
        residualMomY: [list of floats of length :numSteps],
        residualMomZ: [list of floats of length :numSteps],
        residualMomEnergy: [list of floats of length :numSteps],
        residualMomNut: [list of floats of length :numSteps],
        forceX: [list of floats of length :numSteps],
        forceY: [list of floats of length :numSteps],
        forceZ: [list of floats of length :numSteps],
        ...
        }
        ```
 
* **Error Response:**

  * **Code:** 401 UNAUTHORIZED <br />
    **Content:** `{ error : "Log in" }`

  OR

  * **Code:** 404 NOT FOUND <br />
    **Content:** `{ error : "Case does not exist" }`

* **Sample Call:**
   ```
    $.ajax({
    url: "/case/1/convergence",
    dataType: "json",
    type : "GET",
    success : function(r) {
      console.log(r);
    }
  });
  ```

* **Notes:**

    When a case is running, response is constantly updated.  Expect maxage
    of a few seconds to sub-second.



----
# GetCaseSolution

Get computed solution as a tarball

* **URL**

  /case/:caseId/solution

* **Method:**
  
  `GET`
    
*  **URL Params** 

   **Required:**
 
   `caseId=[integer]`

   **Optional:**
 
   None

* **Success Response:**
  
    * **Code:** 200 <br />

    **Content:** 

      A .tar.gz file containing the computed solution
 
* **Error Response:**

  * **Code:** 401 UNAUTHORIZED <br />
    **Content:** `{ error : "Log in" }`

  OR

  * **Code:** 404 NOT FOUND <br />
    **Content:** `{ error : "Case does not exist" | "No solution yet"}`

* **Sample Call:**

    Not supposed to be called by javascript.

* **Notes:**

    This URL should be revealed in a UI only when a case status shows
    "Completed" or "Failed".
    It should be clickable to initiate a download.



----
# DeleteCase

  Delete a case

* **URL**

  /case/:caseId

* **Method:**
  
  `DELETE`
  
*  **URL Params**

   **Required:**
 
   `caseId=[integer]`

   **Optional:**
 
   None

* **Success Response:**
  
  * **Code:** 204 <br />
    **Content:** None
 
* **Error Response:**

  * **Code:** 401 UNAUTHORIZED <br />
    **Content:** `{ error : "Log in" }`

  OR

  * **Code:** 404 NOT FOUND <br />
    **Content:** `{ error : "Case does not exist" }`

* **Sample Call:**

   ```
    $.ajax({
    url: "/case/1",
    dataType: "json",
    type : "DELETE",
    success : function() {
      console.log("case deleted");
    }
  });
  ```


----
# PauseResumeCase

Pause or resume a case.

* **URL**

  /case/:caseId/:action

* **Method:**
  
  `PUT`
    
*  **URL Params** 

   **Required:**
 
   `caseId=[integer]`

   `action=[pause | resume]`

   **Optional:**
 
   None

* **Success Response:**
  
    * **Code:** 202 <br />
    **Content:** None
 
* **Error Response:**

  * **Code:** 401 UNAUTHORIZED <br />
    **Content:** `{ error : "Log in" }`

  OR

  * **Code:** 404 NOT FOUND <br />
    **Content:** `{ error : "Case does not exist" }`

  OR

  * **Code:** 403 FORBIDDEN <br />
    **Content:** `{ error : "account locked" }`

  OR

  * **Code:** 403 FORBIDDEN <br />
    **Content:** `{ error : "case deleted", deleteTime: [time in utc] }`

* **Sample Call:**
   ```
    $.ajax({
    url: "/case/1/resume",
    dataType: "json",
    type : "PUT",
    success : function() {
      console.log("Resume request accepted.");
    }
  });
  ```

* **Notes:**

    Successful return of this method does not mean that status has changed.
    Call [GetCaseInfo](#getcaseinfo) to confirm the status.


----
# SubmitCase

  Submit a new case

* **URL**

  /case

* **Method:**
  
  `POST`
  
*  **URL Params**

   None

* **Data Params**

    **Required:**

    ```
    {
        name : [string],
        tag : [list of strings],
        meshId : [integer],
        runtimeParams: {
            geometry: {
                refArea: [float]
            },
            volumeOutput: {
                vorticity : [true | false],
                residual: [true | false],
                T: [true | false],
                s: [true | false],
                mut: [true | false],
                mutRatio: [true | false]
            },
            navierStokesSolver: {
                tolerance : [float],
                CFL: {
                    initial : [float],
                    final : [float],
                    rampSteps : [integer]
                },
                linearIterations : [integer]
            },
            turbulenceModelSolver :
            {
                modelType: "SpalartAllmaras" | none,
                tolerance : [float],
                CFL: {
                    initial : [float],
                    final : [float],
                    rampSteps : [integer]
                },
                JacobiSweeps : [integer]
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
    }
    ```

    **Optional:**

    None

* **Success Response:**
  
    * **Code:** 200 <br />
    **Content:** 
        ```
        {
        caseId : [integer],
        submitTime: [string in yyyy:mm:dd:hh:mm:ss.ssssss format]
        }
        ```
 
* **Error Response:**

  * **Code:** 401 UNAUTHORIZED <br />
    **Content:** `{ error : "Log in" }`

  OR

  * **Code:** 400 BAD REQUEST <br />
    **Content:** `{ error : detailed error message }`

  OR

  * **Code:** 403 FORBIDDEN <br />
    **Content:** `{ error : "account locked" }`

* **Sample Call:**

    ```
    $.ajax({
        url: "/case",
        dataType: "json",
        type : "POST",
        data : {
            name: "M01A0B0",
            tags: ["Low Mach", "Zero AoA"]
            meshId : 123,
            runtimeParams: {
                geometry: {
                    refArea: 100.0
                },
                volumeOutput: {
                    vorticity : false,
                    residual: false,
                    T: false,
                    s: false,
                    mut: false,
                    mutRatio: false
                },
                navierStokesSolver :
                {
                    tolerance : 1.0e-10,
                    CFL: {
                        initial : 0.5,
                        final : 200,
                        rampSteps : 200
                    },
                    linearIterations : 30
                },
                turbulenceModelSolver :
                {
                    modelType: "SpalartAllmaras",
                    tolerance : 1.0e-8,
                    CFL: {
                        initial : 0.5,
                        final : 200,
                        rampSteps : 200
                    },
                    linearIterations : 20
                },
                freestream :
                {
                    Reynolds : 1e7,
                    Mach : 0.25,
                    Temperature : 300,
                    alphaAngle : 0.0,
                    betaAngle : 0.0
                },
                boundaries : {
                    1 : {
                        type : NoSlipWall
                    },
                    2 : {
                        type : SlipWall
                    },
                    3 : {
                        type : Freestream
                    }
                }
            }
        },
        success : function(r) {
          console.log(r);
        }
    });
    ```

* **Notes:**

  A user is allowed to add multiple cases of the same name, for which
  the server will return different caseId's.
  Thus, UI designers should be aware that this call is not idempotent.

  Time in response should be in UTC.

  The JSON interface may have minor updates frequently.  These minor
  updates will preserve backwards compatibility, so that the client
  may not have to be updated simutaenously.  Any major upgrades, which
  cannot be backwards compatible and therefore requires simultaneous
  upgrade of all clients, should be less frequent.

  Possible modelType may grow.

  Types of boundary conditions may grow.

  Some boundary conditions will have additional data.

  Angles are in radians.


----
# ForkCase

Fork a child case with identical mesh as the parent but modified parameters.
The child case starts immediately from the final state of the parent case.

* **URL**

  /case/:caseId

* **Method:**
  
  `POST`
    
*  **URL Params** 

   **Required:**
 
   `caseId=[integer]`

   **Optional:**
 
   None

* **Data Params**

    Identical to [SubmitCase](#submitcase).

* **Success Response:**
  
    * **Code:** 200 <br />
    **Content:** 
        ```
        {
        caseId : [integer],
        submitTime: [string in yyyy:mm:dd:hh:mm:ss.ssssss format]
        }
        ```
 
* **Error Response:**

  * **Code:** 401 UNAUTHORIZED <br />
    **Content:** `{ error : "Log in" }`

  OR

  * **Code:** 404 NOT FOUND <br />
    **Content:** `{ error : "Case does not exist" }`

  OR

  * **Code:** 400 BAD REQUEST <br />
    **Content:** `{ error : detailed error message }`

  OR

  * **Code:** 403 FORBIDDEN <br />
    **Content:** `{ error : "account locked" }`

  OR

  * **Code:** 403 FORBIDDEN <br />
    **Content:** `{ error : "case deleted", deleteTime: [time in utc] }`

* **Sample Call:**
    ```
    $.ajax({
        url: "/case/542",
        dataType: "json",
        type : "POST",
        data : {
            name: "M01A0B0",
            tags: ["Low Mach", "Zero AoA"]
            meshId : 123,
            ...
        }
    });
    ```

* **Notes:**

    A user is allowed to fork multiple cases from the same parent with
    the identical name, for which the server will return different caseId's.
    Thus, UI designers should be aware that this call is not idempotent.

    See [SubmitCase](#submitcase) for details of the `data` field.

    `meshId`, if provided, must be identical to that of the parent case.


----
# ListCases

List all cases belonging to the user

* **URL**

  /case

* **Method:**
  
  `GET`
    
*  **URL Params** 

   **Required:**
 
    None

   **Optional:**
 
    `caseName=[string]`

    `meshName=[string]`

    `meshId=[integer]`

    `caseTag=[list of strings]`

    `meshTag=[list of strings]`
    
    `submittedAfter=[yyyy:mm:dd:hh:mm:ss.ssssss]`

    `submittedBefore=[yyyy:mm:dd:hh:mm:ss.ssssss]`

* **Success Response:**
  
    * **Code:** 200 <br />
    **Content:** 
        ```
        {
        caseId : [list of integers]
        }
        ```
 
* **Error Response:**

  * **Code:** 401 UNAUTHORIZED <br />
    **Content:** `{ error : "Log in" }`

* **Sample Call:**
   ```
    $.ajax({
    url: "/case",
    data: {
        meshTag: ["Potential stall"],
        caseTag: ["Zero AoA"],
        submittedAfter: "2018:07:01:13:59:59.999999"
    },
    dataType: "json",
    type : "GET",
    success : function(r) {
      console.log(r);
    }
  });
  ```

* **Notes:**
      
    Time should be in UTC.

    If `caseTag` or `meshTag` contains multiple tags, server will select
    casees that has *ALL* these tags.  If one want to list cases that has
    *ANY* tag from a list, the client should call this method multiple
    times, each with a single tag, then perform union on the returned
    values.
