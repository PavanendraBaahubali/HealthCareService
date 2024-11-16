# Health Care Service

# Overview

- This is a simple Health Care Services API that allows users to manage healthcare data.
- Users can add new healthcare services, including service name, description, and price.
- Users can update existing healthcare service details.
- Users can delete healthcare service records.
- The API also allows users to fetch and view all available healthcare services data from the database.
- Data management is streamlined with CRUD (Create, Read, Update, Delete) operations.
- The API is designed to ensure data integrity and provide easy access for healthcare-related data management.

# Set up and Installation

```
"dependencies": {
    "dotenv": "^16.4.5",
    "express": "^4.21.1",
    "mongoose": "^8.8.1"
  }
```

- **dotenv**: This package is used for loading environment variables from a `.env` file into `process.env` in a Node.js application. It helps manage sensitive information like API keys, database URIs, and other configuration values securely without hardcoding them into the application code.

- **express**: Express is a fast, unopinionated, minimalist web framework for Node.js. It is used for building web applications and APIs. Express provides a robust set of features to develop web and mobile applications, such as routing, middleware support, and template rendering.

- **mongoose**: Mongoose is an Object Data Modeling (ODM) library for MongoDB and Node.js. It manages relationships between data, provides schema validation, and offers a straightforward query API for interacting with MongoDB. Mongoose is particularly useful for defining models and handling database operations in a structured manner.

### Establishing Data Base Connection

- Before we start the server, we've to connect our db.

```
const mongoose = require("mongoose");

const connectDB = async () => {
  const dbURI = process.env.dbURI;
  try {
    await mongoose.connect(dbURI);
    console.log("Database connected");
    return mongoose.connection.db; // Return the database connection
  } catch (error) {
    console.error("Database connection error:", error);
    throw error;
  }
};

module.exports = connectDB;

```

### Start the server

After establishing the connection to our database then we can start our server.

```
const startServer = async () => {
  const PORT = process.env.PORT;
  try {
    const db = await connectDB();
    app.use("/api/v1/healthCare", healthServiceRouter());
    app.listen(PORT, () => console.log(`server is running on port ${PORT}`));
  } catch (err) {
    console.log(err);
    return res.status(500).json({ message: "Internal Server Error" });
  }
};

startServer();
```

# API Usage

## Endpoints

### 1) Add Health Care Service

**Endpoint**: `/api/v1/addService`

**Request Method** : `POST`

**Description**:

- This endpoint allows users to add new healthcare services to the database. Users can specify details such as the service name, a description of the service, and its price. Once posted, the data is stored for further operations like updating, fetching, or deleting services. This feature enables easy management and tracking of healthcare offerings within the system.

**Request Body** :

```

 {
 "serviceName",
 "serviceDescription" ,
 "servicePrice",
 }

```

**Example Request**

 - **Example Request Body** :

    ```
    
     {
     "serviceName" : "General Checkup",
     "serviceDescription" : "Routine physical examination to assess overall health.",
     "servicePrice" : "100$"
     }
    
    ```

    <img src="./assets/addServiceExample.PNG" alt="addServiceExample" height="300px">

**Expected Response**

- **Status**: 200 OK

- **Response Body**:

  ```
    {
        "message": "New Service has been successfully added."
    }
  ```
- **Response**:
  
    <img src="./assets/addServiceResponse.PNG" alt="Add Service response" height="300px">

 ### Validation ###
 
   - The API performs basic validation when adding a health care service to ensure that essential fields like `serviceName`, `serviceDescription`, and `servicePrice` are
      provided and correctly formatted. This helps maintain data integrity and avoid incomplete entries. If validation fails, a relevant error message is returned to guide 
      the user.

   - If a user forgets to provide all required fields, such as `serviceName`, `serviceDescription`, or `servicePrice`, the API will respond with a message indicating that         "All fields are required."

   - Additionally, if any specific field is missing, the response will clearly indicate which field is required, guiding the user to complete their input correctly. This         ensures accurate and consistent data entry for every health care service added.
      
   - **Example**
      - Try to add a service without a `servicePrice`
      
         - **Request Body**

             ```
             {
                "serviceName" : "General Checkup",
                "serviceDescription" : "Routine physical examination to assess overall health.",
            }
             ```
      - **Response Body** :

         ```
         {
            "message": "Service Price is required"
        }
         ```

### 2) Get All Available Health Care Services

**Endpoint**: `/api/v1/getAllServices`

**Request Method** : `GET`

**Description**:

- This endpoint retrieves all available healthcare services stored in the database. It provides users with an overview of all services, including their names, descriptions, and prices. Only services that are marked as available will be returned. This feature is useful for users who need to browse, review, or select from the offered healthcare services.

**Example Request**

   <img src="./assets/getAllServicesRequest.PNG" alt="getAllServicesRequest" height="300px">

**Expected Response**

- **Status**: 200 OK

- **Response Body**:

  ```
          [
              {
                  "_id": "6736e8d0420ff10acf48f5de",
                  "serviceName": "General Checkup",
                  "serviceDescription": "Routine physical examination to assess overall health.",
                  "servicePrice": "100$",
                  "isAvailable": true,
                  "__v": 0
              }
          ]
  ```

- **Response**:
  
    <img src="./assets/getAllServicesResponse.PNG" alt="getAllServicesResponse" height="300px">

### 3) Update Health Care Services

**Endpoint**: `/api/v1/updateService`

**Request Method** : `PUT`

- **Request Body**

  ```
  {
  "serviceName" ,
  "serviceDescription" ,
  "servicePrice",
  "serviceId" -- Mandotory
  }

  ```
**Description**:

- This endpoint allows users to update the details of an existing healthcare service. The request must include the unique `serviceId` to identify which service should be modified. Additionally, it accepts fields that the user wants to update, such as the `service name`, `service description`, or `service price`. Upon successful completion of the update, a success message is returned, confirming that the service data has been modified. This feature ensures that healthcare service information remains current and accurate.
  
- `serviceId` must be included to identify which record needs to be deleted.
- `serviceId` is generated by mongoDB, when we are adding a new service.

**Example Request**

   <img src="./assets/updateServiceRequest.PNG" alt="updateServiceRequest" height="300px">

- **Example Request Body**

  ```
  {
  "serviceName" : "General Checkup Health Care",
  "serviceDescription" : "Routine physical examination to assess overall health.",
  "servicePrice" : "100$",
  "serviceId" : "6736e8d0420ff10acf48f5de"
  }

  ```

**Expected Response**

- **Status**: 200 OK

- **Response Body**:

  ```
  {
     "message": "General Checkup Health Care has been successfully updated."
  }
  ```

  - **Response**:

      <img src="./assets/updateServiceResponse.PNG" alt="updateServiceResponse" height="300px">

### 3) Delete Health Care Service

**Endpoint**: `/api/v1/deleteService`

**Request Method** : `DELETE`

- **Request Body**

  ```
  {
  "serviceId"
  }

  ```

**Description**:

- This endpoint allows users to delete an existing healthcare service from the database. The request requires the `serviceId` of the record that needs to be deleted. Once the service is identified and removed, it will no longer be available for retrieval or display in the system. This functionality ensures efficient management of healthcare services by enabling the removal of outdated or unnecessary records.

**Example Request**

<img src="./assets/deleteServiceRequest.PNG" alt="deleteServiceRequest.PNG" height="300px">

- **Example Request Body**

  ```
  {
  "serviceId" : "6736e8d0420ff10acf48f5de"
  }

  ```

**Expected Response**

- **Status**: 200 OK

- **Response Body**:

  ```
  {
     "message": "6736e8d0420ff10acf48f5de has been deleted successfully."
  }
  ```
  
- **Response**:
  
  <img src="./assets/deleteServiceResponse.PNG" alt="deleteServiceResponse.PNG" height="300px">

# Conclusion

- This API simplifies managing healthcare services with endpoints to create, update, delete, and retrieve data. By defining clear data models and leveraging RESTful principles, it aims to enhance service availability and management in healthcare systems.
