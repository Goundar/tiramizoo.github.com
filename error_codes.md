---
layout: page
title: Error Codes & Responses
---


Tiramizoo REST APIs use the HTTP protocol for sending and retrieving data. Client code utilizing the REST APIs make an HTTP request to the Tiramizoo's Server and process the HTTP response accordingly. Included with the HTTP response data is the HTTP response code. The response code gives the client some indication as to the success of the HTTP request, and can provide information on how the client should handle the included response data.


### 200 OK
The request succeeded.

### 201 Created
The resource was created.

### 204 No Content
The request has been processed and no content was returned.

### 400 Bad Request
The request was not valid. The request may be incorrect, or the data in the request is not in the correct format. Check the message details for more information concerning the bad request, correct the request data, and try the invocation again.

### 401 Unauthorized
The user is not authorized to access the requested resource.

### 403 Forbidden
The user is not granted to access the requested resource.

### 404 Not Found
The requested resource was not found or no corresponding data available.

### 405 Method Not Allowed
parameter or verb not supported.

### 406 Not Acceptable
Request of a format that is currently not supported.

### 422 Unprocessable Entity
Data supplied is not valid. See detailed errors in the response.

### 503 Service Unavailable
Application is offline for maintenance.

### Internal Server Error
Server encountered an unexpected condition which prevented it from processing the request.
