* [GetMeshInfo](#getmeshinfo)
* [DeleteMesh](#deletemesh)
* [AddMesh](#addmesh)
* [UploadMesh](#uploadmesh)

----
# GetMeshInfo
----
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

* **Data Params**

  None

* **Success Response:**
  
    * **Code:** 200 <br />
    **Content:** 
        ```
        {
        id : [integer],
        size : [integer size in bytes],
        upload_time: [string in yyyy:mm:dd:hh:mm:ss.ssssss format],
        status: [uploading | uploaded | deleted],
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

  Perhaps the status field in the return can be enricched if the
  upload is split into chunks.


----
# DeleteMesh
----
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

* **Data Params**

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
----
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
    }
    ```

    **Optional:**

    None

* **Success Response:**
  
    * **Code:** 200 <br />
    **Content:** 
        ```
        {
        id : [integer],
        upload_time: [string in yyyy:mm:dd:hh:mm:ss.ssssss format]
        }
        ```
 
* **Error Response:**

  * **Code:** 401 UNAUTHORIZED <br />
    **Content:** `{ error : "Log in" }`

  OR

  * **Code:** 400 BAD REQUEST <br />
    **Content:** `{ error : "invalid size" }`

  OR

  * **Code:** 400 BAD REQUEST <br />
    **Content:** `{ error : "invalid name" }`

  OR

  * **Code:** 400 BAD REQUEST <br />
    **Content:** `{ error : "invalid tag" }`

* **Sample Call:**

   ```
    $.ajax({
    url: "/mesh",
    dataType: "json",
    type : "POST",
    data : {
        size: 1323483768,
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
  the server will return different meshId's. So this call is not idempotent.


----
# UploadMesh
----
  Upload the content of a mesh that is alread added through addMesh.

* **URL**

  /mesh/:meshId

* **Method:**
  
  `PUT`
  
*  **URL Params**

   **Required**

   meshId: [integer]

   **Optional**

* **Data Params**

    None

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
    formData.append('file', 'multiGigaByteMeshFile.bin');

    $.ajax({
    url: "/mesh/12",
    dataType: "json",
    type : "PUT",
    data : formData,
    success : function() {
      console.log("Upload completed");
    }
  });
  ```

* **Notes:**

    May get more complex if upload in chunks.
