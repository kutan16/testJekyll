## Trust Info ##
### Request ###
<code>
POST| .../<span style="color:gray;">{external-application-trust-service}</span>/trust-info/applications/
</code>

### Request body ###

Query multiple applications at a time. The request body is a dictionary mapping <span style="color:orange;">appId</span>
to a JSON object which contains the redirect URL for the application. The <span style="color:#FF8C00;">appUrl</span>
should be the same redirect URL that is registered for the provided <span style="color:orange;">appId</span> at the time
the application was registered. It is the responsibility of consumers of the API to ensure
the <span style="color:orange;">appId</span> and <span style="color:#FF8C00;">appUrl</span> correlate to the same
application. Null values for <span style="color:orange;">appId</span> will be ignored.

#### Schema ####
[OpenAPI specification](https://pages.github.cerner.com/CloudAuthorization/external-application-trust/)

```
{
     applications [
          {
               id: The application ID provided when the application was registered with Ignite
               urls: [ The list of application URLs ]
          }
     ]
}
```

#### Example Value ###

```
{
  "applications": [
    {
      "id": "220e15b9-e4d5-470d-9e10-ef53e5164e42",
      "urls": [
        "https://www.some-url.com"
      ]
    },
    {
      "id": "6adeb21e-e8d3-43d7-a216-1f16df82e94a",
      "urls": [
        "https://www.some-other-url.com",
        "invalid.url.com"
      ]
    },
    {
      "id": "eab00e8a-f0c5-4dc0-8330-3b46f93578da",
      "urls": [
        ""
      ]
    }
  ]
}
```


### Example JSON Response ###

**<span style="color:#3CB83C">Success response example</span>**:

| Status Code                | Media type       | Description                                                                                                                                                         |                                                                                                                                                      
| :------------------------- | :--------------- |:--------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 200                        | application/json | Data relevant to making trust determinations about multiple applications. The response contains a mapping of appId to the trust information about that application. |

```
{
  "220e15b9-e4d5-470d-9e10-ef53e5164e42": [
    {
      "requestedAppUrl": "https://www.some-url.com",
      "trustInfo": {
        "trustLevel": "KNOWN_OV",
        "domain": {
          "name": "some-url.com",
          "trusted": "true",
          "reason": "urn:cerner:external-application:dns:txt-record-contains-appid"
        },
        "organization": {
          "name": "SomeUrl Corporation",
          "trusted": "true",
          "location": {
            "region": "US",
            "province": "MO",
            "locality": "Kansas City"
          },
          "reason": "urn:cerner:external-application:certificate:organization-validated"
        }
      }
    }
  ],
  "6adeb21e-e8d3-43d7-a216-1f16df82e94a": [
    {
      "requestedAppUrl": "https://www.some-other-url.com",
      "trustInfo": {
        "trustLevel": "UNKNOWN",
        "domain": {
          "name": "some-other-url.com",
          "trusted": "false",
          "reason": "urn:cerner:external-application:dns:txt-record-missing-appid"
        },
        "organization": {
          "trusted": "false",
          "name": null,
          "location": null,
          "reason": "urn:cerner:external-application:certificate:domain-validated"
        }
      }
    },
    {
      "requestedAppUrl": "invalid.url.com",
      "error": {
        "message": "appUrl is malformed"
      }
    }
  ],
  "eab00e8a-f0c5-4dc0-8330-3b46f93578da": [
    {
      "requestedAppUrl": "",
      "error": {
        "message": "appUrl was not provided"
        "reason": "urn:cerner:external-application:error:invalid-url:no-protocol"
      }
    }
  ]
}
```

**<span style="color:#E08E65">Errors Response example</span>**:

| Status Code                | Media type       | Description                    |                                                        
| :------------------------- | :--------------- |:-------------------------------| 
| 400                        | application/json | When the request body is empty |
| 400                        | application/json | Malformed JSON cannot be processed |
| 400                        | application/json | There is no valid application "id" and "url" in the request |
| 500                        | application/json | Internal Server Error          |

```
{
  "error": "BAD_REQUEST",
  "statusCode": 400,
  "message": "request body is empty"
}
```
