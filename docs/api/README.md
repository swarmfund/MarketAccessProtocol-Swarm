
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

Token Issuer, depending on the blockchain on which the token is, will use different sort of signature address format, and signed public address. Hence, by sending the address, signed address, and specific blockchain, MAP system can know which kind of signature algorithm to use in order to check wheather the sender is the real owner of this address.
Field `data` is not required, and serves as a additional data about issuer if Token Issuer is willing to provide some.

#### Assets

##### Register
Registered Token Issuer can add a new Asset to the system, represented by a (Smart Contract Address/Specific Blockchain) pair.
- Action - `POST`
- Path - `/asset/register`
- Request Body  
```
{
    assetAddress: "1HZwkjkeaoZfTSaJxDw6aKkxp45agDiEzN",
    blockchain: "Ethereum",
    assetOwnerAddress: "2HZwkjkeaoZfTSaJxDw6aKkxp45agDiEzN"
    signedOwnerAddress: "1HZwkjkeaoZfTSaJxDw6aKkxp45agDiEzNG8tFK3dNwQaiYA0VOuhMBOjcdydR5gyKh/IAr9rSqDXy93cVqKDZkYv/LjfLpJ8PfJKGrUovjisT9rhMWg8Z5wM=",
    assetDNA: "QmWmyoMoctfbAaiEs2G46gpeUmhqFRDW6KWo64y5r581Vz"
}
```
Token Issuer with it's Owner Address, from which asset itself was deployed to specific blockchain, specifies address of the Asset and sends signed Owner public address. With these informations, MAP can check if Token Issuer is really the Issuer of that Asset on the specified blockchain.
AssetDNA is IPFS hash of additional info about the token itself.
- Return body
```
{
    mapAssetAddress: "1HZwkjkeaoZfTSaJxDw6aKkxp45agDiEzN"
}
```
##### Register Qualification Provider
Register Qualification Provider for the Token, API call is performed by registered Token Issuer. Multiple Qualification Providers can be added to one Asset. 
- Action - `POST`
- Path - `/asset/qp/register`
- Request Body  
```
{
    mapAssetAddress: "1HZwkjkeaoZfTSaJxDw6aKkxp45agDiEzN",
    mapQualificationProviderAddress: "3HZwkjkeaoZfTSaJxDw6aKkxp45agDiEzN" 
}
```

Qualification Provider Address is the address of QP that is already registered provider on MAP system.

##### Get Qualification Provider
Get Qualification Provider for a Token API call returns a list of Qualification Provider associated with the Token.
- Action - `GET`
- Path - `/asset/{mapAssetAddress}/qp`
- Return Body  
```
[
{
    mapQualificationProviderAddress: "3HZwkjkeaoZfTSaJxDw6aKkxp45agDiEzN",
    additionalInfo: {...}
},
...
]
```

As a response sender will receive a list of Qualification Providers, and associted details, for the desired token.


### Qualification Provider

#### Register
Register API calls for Qualification Provider to be registered on the MAP.
- Action - `POST`
- Path - `/qp/register`
- Request Body  
```
{
    address: "1HZwkjkeaoZfTSaJxDw6aKkxp45agDiEzN",
    blockchain: "Bitcoin",
    signedAddress: "1HZwkjkeaoZfTSaJxDw6aKkxp45agDiEzNG8tFK3dNwQaiYA0VOuhMBOjcdydR5gyKh/IAr9rSqDXy93cVqKDZkYv/LjfLpJ8PfJKGrUovjisT9rhMWg8Z5wM=",
    data: "0x1d4d691d0000000000000000000000001550d41be3651686e1aeeea073d8d403d0bd2e3000000000000000000000000000000000000000000000000c328093e61ee40000000000000000000000000000960b236a07cf122663c4303350609a66a7b288c000000000000000000000000038bc9915922ba42bb67a804befa510e4bbbfb4ba0000000000000000000000000000000000000000000000000c2d86e5d90486a00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000005c3dd901000000000000000000000000000000000000000000000000000000000335afd4000000000000000000000000000000000000000000000000000000000000001cd2fc85ad3c239eb9c917f2701ba9c24e6a7730b40fd6abe5eea3a0b24a4b844131eb70cd49838d8cec541c4073d209eedc4400a042c771140f86086fe2cde64d"
}
```
Field `data` serves as a additional data about QP.

As a response from the MAP QP will receive `qp.txt` that needs to be placed in the root of QP's website. The MAP system will check if the file is present on the server and active the QP in the system after the check is done.

#### Broadcast Qualification for a DID
Once Investor obtains info about QP by using [Get Qualification Provider](#get-qualification-provider) call, and has done all the required checks on the QP's platform to get Qualification, the QP will broadcast Qualification to MAP.
- Action - `POST`
- Path - `/qp/qualification/broadcast`
- Request Body  
```
{
    mapQualificationProviderAddress: "3HZwkjkeaoZfTSaJxDw6aKkxp45agDiEzN",
    signedQualificationProvideAddress: "1HZwkjkeaoZfTSaJxDw6aKkxp45agDiEzNG8tFK3dNwQaiYA0VOuhMBOjcdydR5gyKh/IAr9rSqDXy93cVqKDZkYv/LjfLpJ8PfJKGrUovjisT9rhMWg8Z5wM=",
    qualificationData: "0x1d4d691d0000000000000000000000001550d41be3651686e1aeeea073d8d403d0bd2e3000000000000000000000000000000000000000000000000c328093e61ee40000000000000000000000000000960b236a07cf122663c4303350609a66a7b288c000000000000000000000000038bc9915922ba42bb67a804befa510e4bbbfb4ba0000000000000000000000000000000000000000000000000c2d86e5d90486a00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000005c3dd901000000000000000000000000000000000000000000000000000000000335afd4000000000000000000000000000000000000000000000000000000000000001cd2fc85ad3c239eb9c917f2701ba9c24e6a7730b40fd6abe5eea3a0b24a4b844131eb70cd49838d8cec541c4073d209eedc4400a042c771140f86086fe2cde64d"
    did: "4HZwkjkeaoZfTSaJxDw6aKkxp45agDiEzN"
    qualificationHash: "0xf9925d96a17cd75d084cecafd8f630a6af9c0423306239f7e326f724891909cc"
}
```

DID is basically an public key that Investor, while doing a check on QP services, provided and which he can claim to be his own. Field qualificationData holds specific details of this qualification.

### Investor

#### Broadcast Association
Once Investor has obtained and claimed, via DID, the Qualification he can broadcast Association data connected with this Qualification via this API call. Because of this Proofs of Qualification and Association, behind the scenes Investor’s Qualification data, Wallet address, Quality provider are all connected.
- Action - `POST`
- Path - `investor/association/broadcast`
- Request Body  
```
{
    address: "1HZwkjkeaoZfTSaJxDw6aKkxp45agDiEzN",
    blockchain: "Bitcoin",
    signedAddress: "1HZwkjkeaoZfTSaJxDw6aKkxp45agDiEzNG8tFK3dNwQaiYA0VOuhMBOjcdydR5gyKh/IAr9rSqDXy93cVqKDZkYv/LjfLpJ8PfJKGrUovjisT9rhMWg8Z5wM=",
    did: "4HZwkjkeaoZfTSaJxDw6aKkxp45agDiEzN",
    signedDid: "4HZwkjkeaoZfTSaJxDw6aKkxp45agDiEzNG8tFK3dNwQaiYA0VOuhMBOjcdydR5gyKh/IAr9rSqDXy93cVqKDZkYv/LjfLpJ8PfJKGrUovjisT9rhMWg8Z5wM=",
    qualificationHash: "0xf9925d96a17cd75d084cecafd8f630a6af9c0423306239f7e326f724891909cc"
}
```

By sending his Address, proof of ownership for this Address, DID (public key) and proof of ownership for this DID, and finally qualificationHash, MAP system can know that this Address can be associated with specific qualification.
### Services (e.g. Exchanges, Wallets)

#### Check Qualification and Association for an Address
Check if the Wallet Address has Qualification and which is Qualification Provider connected with that Address.
- Action - `GET`
- Path - `/check/{blockchain}/{address}`
- Return Body  
```
{
    mapQualificationProviderAddress: "3HZwkjkeaoZfTSaJxDw6aKkxp45agDiEzN",
    qualificationData: "0x1d4d691d0000000000000000000000001550d41be3651686e1aeeea073d8d403d0bd2e3000000000000000000000000000000000000000000000000c328093e61ee40000000000000000000000000000960b236a07cf122663c4303350609a66a7b288c000000000000000000000000038bc9915922ba42bb67a804befa510e4bbbfb4ba0000000000000000000000000000000000000000000000000c2d86e5d90486a00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000005c3dd901000000000000000000000000000000000000000000000000000000000335afd4000000000000000000000000000000000000000000000000000000000000001cd2fc85ad3c239eb9c917f2701ba9c24e6a7730b40fd6abe5eea3a0b24a4b844131eb70cd49838d8cec541c4073d209eedc4400a042c771140f86086fe2cde64d"
    qualificationHash: "0xf9925d96a17cd75d084cecafd8f630a6af9c0423306239f7e326f724891909cc"
}
```
Most important field here is `qualificationData` which holds specific details of this qualification, if any qualification is associated with this address.
### Example flow

Flow of actions described so far is as follows: 
- Register Token Issuer
- Register Asset
- Register Qualification Provider
- Get Qualification Provider 
