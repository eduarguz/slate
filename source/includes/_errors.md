# Errors

The **3DS-MPI** API uses standard HTTP response codes to indicate success of failure of an API request.
In general, codes in the 2xx range indicate success, codes in the 4xx range indicate an error that resulted from the provided information (e.g., a required parameter was missing), and codes in the 5xx range indicate an error with MPI servers.


Error Code | Meaning
---------- | -------
400 | Bad Request -- Your request is invalid.
401 | Unauthorized -- Your API key is wrong.
403 | Forbidden -- Your API key is not authorized to perform the action.
404 | Not Found -- The specified resource could not be found.
405 | Method Not Allowed -- You tried to access the API with an invalid method.
406 | Not Acceptable -- You requested a format that isn't json.
429 | Too Many Requests -- You're requesting too many actions! Slow down!
500 | Internal Server Error -- We had a problem with our server. Try again later.
502 | Bad Gateway Error -- We had a problem while acting as gateway. Try again later.
503 | Service Unavailable -- We're temporarily offline for maintenance. Please try again later.


## 3DS Error

```json
{
  "error_number": "1005",
  "error_description": "The associated franchise is not enabled"
}
```

In some cases, 3DS errors are given in a custom format

Parameter | Type  | Description  
--------- | ------- | -----------
error_number | number  | Error identifier
error_description | number  | Error Description
