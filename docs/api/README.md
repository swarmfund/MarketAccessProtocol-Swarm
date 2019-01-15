
## RESTful
The MAP API is RESTful, meaning that all API calls are stateless and can be used without maintaining local duplicated state in the implementing application.

The API is designed according to a set of ground rules which simplify the implementation process:

- All API requests are sent with form encoded body to make file attachments easier to send
- All API responses are sent in JSON format
- All API responses include a descriptive message which is ready to be shown to the user and a corresponding limited set of HTTP status codes:
  - `HTTP 200` / OK
  - `HTTP 400` / malformed request format or body
  - `HTTP 401` / missing or bad Authorization header
  - `HTTP 403` / request is authenticated but not authorized for access to this particular resource
  - `HTTP 422` / request cannot be fulfilled because of unmet user requirements
  - `HTTP 503` / request cannot be fulfilled at the given moment, please try again later
  
## Authentication and authorization
Authentication and authorization is handled via Authorization header which brings ease of implementation which does not require setting different CORS headers to allow different origins from accessing the API.	

Aside from this, using the JWT (Json Web Tokens) implementation scheme allows for bundling encrypted metadata with each request allowing managing arbitrary user data throughout the session and expiration of tokens.


## SDK
We aim to support most languages for frontend and backend developers. We will provide examples in: curl, Python, PHP, Java, NodeJS, and more. Front-end DApps, such as wallets, will be able to use via C++, JavaScript and other relevant languages via client-side SDK.

## API Reference

- [Token Issuer](#Token Issuer)

### Token Issuer

#### Register
Register API call for Token Issuer to be registered on the MAP.

#### Assets

##### Register
Registered Token Issuer can add a new Asset to the system, represented by a (Smart Contract Address/Specific Blockchain) pair.

##### Register Qualification Provider
Register Qualification Provider for the Token, API call is performed by registered Token Issuer. Multiple Qualification Providers can be added to one Asset. 

##### Get Qualification Provider
Get Qualification Provider for a Token API call returns a list of Qualification Provider associated with the Token.

### Qualification Provider

#### Register
Register API calls for Qualification Provider to be registered on the MAP.

#### Register Qualification for an Address
Investor initiates this action to get Qualification. A Qualification check, i.e. KYC check, is performed by sending all necessary data to the Swarm Fund API (both document scans and textual info). It is expected that after a couple of hours, the results will be back and the user can check them by sending another request to the Swarm API. 

This is important to note that the Swarm API will never initiate requests (e.g. notifying when something is finished), it will only dispense responses to the best of it's knowledge. 

The response of this API call is Qualification - ID and data connected with this Qualification and Qualification Providers that has approved Investor.

### Investor

#### Register Qualification
see [Register Qualification For An Address](#Register Qualification for an Address)

#### Broadcast Association
Once Investor has obtained Qualification he can broadcast Association data connected with this Qualification via this API call. Because of this Proofs of Qualification and Association, behind the scenes Investor’s Qualification data, Wallet address, Quality provider are all connected.

### Services (e.g. Exchanges, Wallets)

#### Check Qualification
Check if the Wallet Address has Qualification and which is Qualification Provider connected with that Address.

### Association

### Qualification


### Example flow

Flow of actions described so far is as follows: 
- Register Token Issuer
- Register Asset
- Register Qualification Provider
- Get Qualification Provider 
