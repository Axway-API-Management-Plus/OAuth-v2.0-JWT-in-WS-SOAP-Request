# Description
The described usecase is to have the API Gateway to handle the OAuth v2.0 JWT flow.
In this context, the implementation is with SOAP Request. The goal is to avoid managing all technical exchanges with salesforce.com .

The API GAteway is acting as the client to handle the OAuth dance for JWT.

The internal application sends the SOAP Request to the API Gateway, that will manage the JWT flavour of OAuth v2.0 :
1.Check incoming request in front of the salesforce.com WSDL
2.Request Access Token to Authorization Server
3.Put the token inside the incoming SOAP Request, and send the request to the Resource Server
4.Propagate the response to the Client Application

  
![alt text][Screenshot1]

[Screenshot1]: https://github.com/Axway-API-Management/OAuth-v2.0-JWT-in-WS-SOAP-Request/blob/master/Readme/Screenshot1.png  "Screenshot1"   


## API Management Version Compatibilty
This artefact was successfully tested for the following versions:
- To be completed


## Install

- The salesforce.com WSDL is imported to virtualize it.
- The "Manage Salesforce WebService flow with Oauth Access token" policy is created, that is set as the routing policy from the "Service Handler" filter for salesforce.com Web Service :
  
![alt text][Screenshot2]

[Screenshot2]: https://github.com/Axway-API-Management/OAuth-v2.0-JWT-in-WS-SOAP-Request/blob/master/Readme/Screenshot2.png  "Screenshot2"   

- Then the client Credentials the API Gateway needs to act as a client to salesforce.com is created :

![alt text][Screenshot3]

[Screenshot3]: https://github.com/Axway-API-Management/OAuth-v2.0-JWT-in-WS-SOAP-Request/blob/master/Readme/Screenshot3.png  "Screenshot3"   

- Details of the OAuth2 Credentials : 
  * Client ID and the corresponding Secret are needed
  * The private key used to sign the JWT claim is set (this key is also used in the salesforce.com settings )
  * The proper additional JWT claims is set to make sure the authentication is propagated to salesforce.com

![alt text][Screenshot4]

[Screenshot4]: https://github.com/Axway-API-Management/OAuth-v2.0-JWT-in-WS-SOAP-Request/blob/master/Readme/Screenshot4.png  "Screenshot4"   

- Details of the OAuth2 Provider Settings: 
  * The Authorization Endpoint and the Token Endpoint must be set as per salesforce.com settings:

![alt text][Screenshot5]

[Screenshot5]: https://github.com/Axway-API-Management/OAuth-v2.0-JWT-in-WS-SOAP-Request/blob/master/Readme/Screenshot5.png  "Screenshot5"   

- As the request is SOAP, an incoming field is used to retrieve the identifier of the requester (here an e-mail address) and to put it in the authentication.subject.id
  * Incoming sample request
```
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:urn="urn:enterprise.soap.sforce.com">
 <soapenv:Header>
      <urn:SessionHeader>
         <urn:sessionId>xxxxxxxxxxx@company.com</urn:sessionId>
      </urn:SessionHeader>
 </soapenv:Header>
 <soapenv:Body>
      <urn:getUserInfo/>
 </soapenv:Body>
</soapenv:Envelope>
```

- Once the Access Token from salesforce.com is retrieved, it is put in the outgoing request in lieue of the incoming e-mail :
  * Outgoing sample request
```
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:urn="urn:enterprise.soap.sforce.com">
 <soapenv:Header>
      <urn:SessionHeader>
       <urn:sessionId>XXXXXXXXXXXXXXXXXX ... XXXXXXXXXXXXXXXXXXXXX</urn:sessionId>
      </urn:SessionHeader>
 </soapenv:Header>
 <soapenv:Body>
      <urn:getUserInfo/>
 </soapenv:Body>
</soapenv:Envelope>
```

- Expiration management and some renegociation are added. The resulting policy is :

![alt text][Screenshot6]

[Screenshot6]: https://github.com/Axway-API-Management/OAuth-v2.0-JWT-in-WS-SOAP-Request/blob/master/Readme/Screenshot6.png  "Screenshot6"   

- NOTES :
  * To enable Oauth support, it must be set up in the instance by running the corresponding script deployOAuthConfig.py (refer p12 of "AxwayAPIGateway_OAuth_Guide.pdf" for more details )
  * To have a working salesforce.com app, it must be set up in the salesforce.com env, to enable OAuthv2.0 support, and make sure all proper options are enabled to have the JWT flow up and running ( import certificate use to sign/validate signature, etc ...). 
  But this is out of the scope of current document. You can refer to salesforce.com docs such as OAuth 2.0 JWT Bearer Token Flow.

## Usage
```
To be completed
```

## Bug and Caveats
```
To be completed
```

## Contributing

Please read [Contributing.md] (https://github.com/Axway-API-Management/Common/blob/master/Contributing.md) for details on our code of conduct, and the process for submitting pull requests to us.

## Team

![alt text][Axwaylogo] Axway Team

[Axwaylogo]: https://github.com/Axway-API-Management/Common/blob/master/img/AxwayLogoSmall.png  "Axway logo"


## License
Apache License 2.0 (refer to document [license] (/LICENSE))

