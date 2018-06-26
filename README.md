* [GetMeshInfo](#getmeshinfo)
* [DeleteMesh](#deletemesh)
* [AddMesh](#addmesh)
* [UploadMesh](#uploadmesh)
* [ListMeshes](#listmeshes)
* [GetCaseInfo](#getcaseinfo)
* [GetCaseStdout](#getcasestdout)
* [GetCaseConvergence](#getcaseconvergence)
* [ChangeCaseStatus](#changecasestatus)
* [SubmitCase](#submitcase)
* [ListCases](#listcases)

----
# GetMeshInfo

Get information about an uploading or uploaded mesh.

* **URL**

  /mesh/:meshId

* **Method:**
  
  `GET`
    
*  **URL Params** 

   **Required:**
 
   `meshId=[integer]`

   **Optional:**
 
   None

* **Success Response:**
  
    * **Code:** 200 <br />
    **Content:** 
        ```
        {
        meshId : [integer],
        size : [integer size in bytes],
        uploadTime: [string in yyyy:mm:dd:hh:mm:ss.ssssss format],
        status: [uploading xx% | uploaded | deleted],
        name: [string],
        tag: [list of strings]
        }
        ```
 
* **Error Response:**

  * **Code:** 401 UNAUTHORIZED <br />
    **Content:** `{ error : "Log in" }`

  OR

  * **Code:** 404 NOT FOUND <br />
    **Content:** `{ error : "Mesh does not exist" }`

* **Sample Call:**
   ```
    $.ajax({
    url: "/mesh/1",
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

  /mesh/:meshId

* **Method:**
  
  `DELETE`
  
*  **URL Params**

   **Required:**
 
   `meshId=[integer]`

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
    **Content:** `{ error : "Mesh does not exist" }`

* **Sample Call:**

   ```
    $.ajax({
    url: "/mesh/1",
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

  /mesh

* **Method:**
  
  `POST`
  
*  **URL Params**

   None

* **Data Params**

    **Required:**

    ```
    {
        size : [integer size in bytes],
        name: [string],
        tag: [list of strings]
        format: "aflr3" | "gmsh" | "fluent" | "hdf5",
        endianness: "little" | "big"
    }
    ```

    **Optional:**

* **Success Response:**
  
    * **Code:** 200 <br />
    **Content:** 
        ```
        {
        meshId : [integer],
        uploadTime: [string in yyyy:mm:dd:hh:mm:ss.ssssss format]
        }
        ```
 
* **Error Response:**

  * **Code:** 401 UNAUTHORIZED <br />
    **Content:** `{ error : "Log in" }`

  OR

  * **Code:** 400 BAD REQUEST <br />
    **Content:** `{ error :
        "invalid size" | "invalid name" | "invalid tag"
        | "format not supported" | "unrecognized endianness" }`

  OR

  * **Code:** 403 FORBIDDEN <br />
    **Content:** `{ error : "account locked" }`

* **Sample Call:**

   ```
    $.ajax({
    url: "/mesh",
    dataType: "json",
    type : "POST",
    data : {
        size: 1323483768,
        endianness: "little",
        name: "FullGoemTakeOffExtFlap20degRefineLvl5",
        tags: ["Full Geometry", "Take off", "Extended Flap", "20 Degrees",
               "Potential stall"]
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
# UploadMesh

  Upload the content of a mesh that is alread added through addMesh.

* **URL**

  /mesh/:meshId

* **Method:**
  
  `PUT`
  
*  **URL Params**

   **Required**

   meshId: [integer]

   **Optional**

* **Success Response:**
  
    * **Code:** 204 <br />
    **Content:** None
 
* **Error Response:**

  * **Code:** 401 UNAUTHORIZED <br />
    **Content:** `{ error : "Log in" }`

  OR

  * **Code:** 404 NOT FOUND <br />
    **Content:** `{ error : "mesh not found" }`

  OR

  * **Code:** 400 BAD REQUEST <br />
    **Content:** `{ error : "mesh already deleted" }`

* **Sample Call:**

   ```
    var formData = new FormData();        
    formData.append('file', 'multiGigaByteMeshFile.ugrid');

    $.ajax({
    url: "/mesh/12",
    dataType: "json",
    type : "PUT",
    processData: false,
    contentType: false,
    data : formData,
    success : function() {
      console.log("Upload completed");
    }
  });
  ```

* **Notes:**

    May get more complex if upload in chunks.


----
# ListMeshes

List all meshes belonging to the user

* **URL**

  /mesh

* **Method:**
  
  `GET`
    
*  **URL Params** 

   **Required:**
 
    None

   **Optional:**
 
    `caseName=[string]`

    `tag=[list of strings]`
    
    `uploadedAfter=[yyyy:mm:dd:hh:mm:ss.ssssss]`

    `uploadedBefore=[yyyy:mm:dd:hh:mm:ss.ssssss]`

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
    url: "/mesh",
    data: {
        tag: ["Potential stall"],
        uploadedAfter: "2018:07:01:13:59:59.999999"
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
        cost : [integer in simulation cost units],
        diskUsage : [integer in disk usage units],
        submitTime: [string in yyyy:mm:dd:hh:mm:ss.ssssss format],
        status: [Preprocessing | Starting | Running | Paused
                 | Cancelled | Completed | Error],
        name: [string],
        tag: [list of strings]
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
        residual: [list of floats of length :numSteps],
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
# ChangeCaseStatus

Pause, Resume, or Cancel a case.

* **URL**

  /case/:caseId/:status

* **Method:**
  
  `PUT`
    
*  **URL Params** 

   **Required:**
 
   `caseId=[integer]`

   `status=[pause | resume | cancel]`

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
            navierStokesSolver :
            {
                tolerance : 1.0e-10,
                CFL: {
                    initial : 1.0,
                    final : 500,
                    rampSteps : 200
                },
                linearIterations : 30
            },
            turbulenceModelSolver :
            {
                modelType: "SpalartAllmaras",
                tolerance : 1.0e-6,
                CFL: {
                    initial : 0.1,
                    final : 5.0,
                    rampSteps : 100
                },
                linearIterations : 20
            },
            freestream :
            {
                Reynolds : 1e3,
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
        },
        success : function(r) {
          console.log(r);
        }
    });
    ```

* **Notes:**

  A user is allowed to add multiple cases of the same name, for which
  the server will return different caseId's.
  Thus, UI designers should beware that this call is not idempotent.

  Time in response should be in UTC.

  Possible modelType may grow.

  Types of boundary conditions may grow.

  Some boundary conditions will have additional data.

  Angles are in radians.


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
