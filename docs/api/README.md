
# What is the MAP API?

Market Access Protocol API enables convenient access to MAP via RESTful experience. 

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

- [Token Issuer](#token-issuer)
  - [Register](#register)
  - [Assets](#assets)
    - [Register](#register-1)
    - [Register Qualification Provider](#register-qualification-provider)
    - [Get Qualification Provider](#get-qualification-provider)
- [Qualification Provider](#qualification-provider)
  - [Register](#register-2)
  - [Broadcast Qualification](#broadcast-qualification-for-a-did)
- [Investor](#investor)
  - [Broadcast Association](#broadcast-association)
- [Services (e.g. Exchanges, Wallets)](#services-eg-exchanges-wallets)
  - [Check Qualification for an Address](#check-qualification-for-and-address)

### Token Issuer

#### Register
Register API call for Token Issuer to be registered on the MAP.
- Action - `POST`
- Path - `/issuer/register`
- Request Body  
```
{
    address: "1HZwkjkeaoZfTSaJxDw6aKkxp45agDiEzN",
    blockchain: "Bitcoin",
    signedAddress: "1HZwkjkeaoZfTSaJxDw6aKkxp45agDiEzNG8tFK3dNwQaiYA0VOuhMBOjcdydR5gyKh/IAr9rSqDXy93cVqKDZkYv/LjfLpJ8PfJKGrUovjisT9rhMWg8Z5wM=",
    data: "0x1d4d691d0000000000000000000000001550d41be3651686e1aeeea073d8d403d0bd2e3000000000000000000000000000000000000000000000000c328093e61ee40000000000000000000000000000960b236a07cf122663c4303350609a66a7b288c000000000000000000000000038bc9915922ba42bb67a804befa510e4bbbfb4ba0000000000000000000000000000000000000000000000000c2d86e5d90486a00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000005c3dd901000000000000000000000000000000000000000000000000000000000335afd4000000000000000000000000000000000000000000000000000000000000001cd2fc85ad3c239eb9c917f2701ba9c24e6a7730b40fd6abe5eea3a0b24a4b844131eb70cd49838d8cec541c4073d209eedc4400a042c771140f86086fe2cde64d"
}
```

Basic flow is as follows. Token Issuer, depending on the blockchain on which the token is, will use different sort of signature address format, and signed public address. Hence, by sending the address, signed address, and specific blockchain, MAP system can know which kind of signature algorithm to use in order to check wheather the sender is the real owner of this address.

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

#### Broadcast Qualification for a DID
Investor initiates this action to get Qualification. A Qualification check, i.e. KYC check, is performed by sending all necessary data to the Swarm Fund API (both document scans and textual info). It is expected that after a couple of hours, the results will be back and the user can check them by sending another request to the Swarm API. 

This is important to note that the Swarm API will never initiate requests (e.g. notifying when something is finished), it will only dispense responses to the best of it's knowledge. 

The response of this API call is Qualification - ID and data connected with this Qualification and Qualification Providers that has approved Investor.

### Investor

#### Broadcast Association
Once Investor has obtained Qualification he can broadcast Association data connected with this Qualification via this API call. Because of this Proofs of Qualification and Association, behind the scenes Investor’s Qualification data, Wallet address, Quality provider are all connected.

### Services (e.g. Exchanges, Wallets)

#### Check Qualification for an Address
Check if the Wallet Address has Qualification and which is Qualification Provider connected with that Address.

### Association

### Qualification


### Example flow

Flow of actions described so far is as follows: 
- Register Token Issuer
- Register Asset
- Register Qualification Provider
- Get Qualification Provider 
