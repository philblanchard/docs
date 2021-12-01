
# Directline Token Fetch

The following is a sample, but usable, app designed to be used as an Azure Serverless Function.

The app is written in GO and uses the GIN Web Framework - who doesn't like Gin!

## main 

```
// ./cmd/api.go
package main

import (
	"bytes"
	"encoding/json"
	"io/ioutil"
	"net/http"
	"os"
	"github.com/gin-gonic/gin"
)
//Defining the type response we get from the DirectLine API
type DirectlineResponse struct {
	ConversationId string `json:"conversationId"`
	Token string `json:"token"`
	Expires_In int `json:"expires_in"`
}

//This function makes the post request to the DirectLine API.
func getDirectlineToken(c *gin.Context) {
	//Grab the userId and userName from the URL query params
	userId := c.Query("userId")	
	userName := c.DefaultQuery("userName", "")
	
	//Create full request url
	url := "https://directline.botframework.com/v3/directline/tokens/generate"
	jsonBodyStr, err := json.Marshal(map[string]map[string]string{
		"user": {
			"id": userId,
			"name": userName,
		},
})
	
	//Create the request
	req, err := http.NewRequest("POST", url, bytes.NewBuffer(jsonBodyStr))
	req.Header.Add("Content-Type", "application/json")
	req.Header.Add("Authorization", "Bearer "+getSecret())
	client := http.Client{}
	
	//MAKE IT GO
	resp, err := client.Do(req)
	if err != nil {
		panic(err)
	}
	defer resp.Body.Close()
	body, _ := ioutil.ReadAll(resp.Body)
	var directlineResponse DirectlineResponse
	//Unmarshal into the type we created before
	json.Unmarshal(body, &directlineResponse)
	c.IndentedJSON(http.StatusOK, directlineResponse)
}

//In this case I stored the DirectLine secret as an env var. Easy to do on Azure app service
func getSecret() string {
	secret := ""
	if val, ok := os.LookupEnv("DIRECTLINE_WEBCHAT_SECRET"); ok {
	secret = val
	}
	return secret
}

//Get the hardcoded or the env variable port
func get_port() string {
	port := ":8080"
	if val, ok := os.LookupEnv("FUNCTIONS_CUSTOMHANDLER_PORT"); ok {
		port = ":" + val
	}
	return port
}

//Make it go
func main() {
	r := gin.Default()
	r.GET("/api/token", getDirectlineToken)
	r.Run(get_port())
}
```

## In your Project Root...
These steps arem ore specific for an Azure Serverless Func...

Create a `host.json` file with the following contents:
```
{
  "version": "2.0",
  "logging": {
    "applicationInsights": {
      "samplingSettings": {
        "isEnabled": true,
        "excludedTypes": "Request"
      }
    }
  },
  "extensionBundle": {
    "id": "Microsoft.Azure.Functions.ExtensionBundle",
    "version": "[2.*, 3.0.0)"
  },
  "customHandler": {
    "enableForwardingHttpRequest": true,
    "description": {
      "defaultExecutablePath": "api",
      "workingDirectory": "",
      "arguments": []
    }
  }
}
```

## Create a ./token/ folder 
Add a `function.json` file with the following:
```

{
  "bindings": [
    {
      "authLevel": "Anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    }
  ]
}
```
