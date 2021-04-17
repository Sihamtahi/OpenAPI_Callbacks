# OpenAPI_Callbacks
This repository explain how to use callback specification in an OpenAPI document.

OpenAPI 3.0.0 only supports callbacks without webhooks. In this document an explanation on how to perform a publish/subscribe operation using webhooks. 
This part will be divided into several sections to explain the communication process : 


#1- Subscription Process  : 
The pubish part has 3 parts: POST, Links and callbaks. We will detail each one in the following sections : 
-  Post part :
``` post:
      parameters:
        - name: callbackUrl    ### the url where to send 
          in: query
          type: string
          format: uri
        - name: event     ### the url where to send 
          in: query
          type: string
      responses:
        '201':
          description: Added
          content:
            application/json:
              type: object
              properties:
                subscriberId: 
                  type: string
                  example: AAA-123-BBB-45
   ```

                  
                  
The API wanting to subscribe will first send a callback URL ("callbaclUrl") and the event it wants to subscribe to ("event"). These two elements are specified as parameters in the request sent to the API in question.

To inform the API that sent the subscription request that its subscription is successful, we send a subscription code in the responses. This code will be used in the unsubscribe operation. 

-  Links part :
```links:  # Link the returned id with the unsubscribe operation
        unsubscribeOp:
          operationId: unsubscribeOperation
              parameters: 
                Id: $response.body#/subscriberId 
 ```


-  Callback part :

```callbacks:
        myEvent:
          '{$request.query.callbackUrl}?event={$request.query.event}':  #https://monserveur.com/NvlOperation
            post:
              requestBody:
                content:
                  application/json:
                    example:
                      message: Some event
              responses:
                '200':
                  description: OK
            
            '{$request.query.callbackUrl}?event={$request.query.event}?id': ##https://monserveur.com/NvlOperation/15
            post:
              requestBody:
                content:
                  application/json:
                    schema :     
                      $ref: '#/components/schemas/Structure1'
                     
                responses:
                  '200':
                    description: OK 
 ```



#1- unsubscribe Process  : 
``` /unsubscribe:
    operationId: unsubscribeOperation
    post:
      parameters:
        - name: Id
          id: query
          type: string
 ```
