# NGSI-Encryption-Layer-as-a-Service

This document describes the encryption service developed at ITESM as a tool for encrypt and decrypt FIWARE data models. The application can be seen as two stand-alone services, one that uses tokens as a security measure and the second one that uses sessions as security measure. Both stand-alone services enable the encryption and decryption of all up-to-date available FIWARE data models published in [FIWARE Data Models official site](https://www.fiware.org/developers/data-models/). 
First, the overall overview of the encryption service is described; secondly, the stand-alone service that use tokens is introduced; lastly, the stand-alone service that use sessions is detailed.

## Overall overview
In the following, the service specifications are described. This specifications applies to both stand-alone services mentioned above.

### Prerequisites
The encryption service can be installed on every Operative System that supports Docker.

The following software must be previously installed in the server which will hold the encryption service.
1. Docker
1. Postman/Insomnia

Furthermore, the following ports containers are required.
1. ngsi_nodejs 8000 (only for stand-alone encryption service that uses tokens)
1. ngsi_python 2121 (only for stand-alone encryption service that uses sessions)

### Overall input definition
1. The inputs that the encryption service expects are listed below. These inputs definitions are used across the document.
1. Orion URL Source or a JSON payload
1. Orion URL Sink or a JSON payload
1. User name
1. User email
1. Keys generated by the encryption process
1. Token generated by the encryption service

### Relevant notes
In the following are described several details about the encryption service.

* Due to context broker’s limits on attribute lengths, the encryption algorithm relies on 8-bit keys.
* For each attribute of the JSON payload, a key is generated. All the keys are integrated into a single file, which is sent to the user.
* Sends one key (5) via email to user (whenever you encrypt at least one thing).

### Initial service authentication
The initial authentication procedure perform by the encryption service is performed via an email verification process. This process allows a user to authenticate itself within the service. It is noteworthy that the authentication procedure currently works only with Gmail accounts. The process is simplified as follows:

```
Input: 3 and 4
Output: 6
```

Postman/Insomnia specifications
```
HTTP endpoint:
	POST
	Uri: http://ngsi-edlayer/signup
	
	Params (Form URL Encoded):
		name: 3
		email: 4
```

### Services within encryption service
The services that both stand-alone implemented services comprises are:
* Sign up
* Sign in
* Encrypt (Encryption of a local JSON file)
* Decrypt (Decryption of a local JSON file)
* Encrypt/ocb (Encryption of entities from Context Broker URL)
* Decrypt/ocb (Decryption of entities from Context Broker URL)

## Encryption service with tokens
The stand-alone encryption service that uses tokens as verification measure is described below. First, the instructions to deploy and execute the service is described; then the sign up, sign in, encrypt, decrypt, encrypt/ocb, and decrypt/ocb processes are detailed.

### Deployment and execution instructions
The following procedure denotes the instructions to deploy and execute the encryption service that will reside in the server.

1. Locate at the service folder location: ```cd ../NGSI_NODEJS_DOCKER```
1. Execute the docker pull command: ```docker pull ngsi/encryption_layer_as_a_service_nodejs```
1. Start the docker compose: ```sudo docker-compose up -d```
1. Check docker containers status: ```docker ps```
1. Verify that the service is up and running: ```http://localhost:8000```

### Use of services supported by the encryption service
The requirements and procedure to use the several services within encryption service are described below. 

#### Sign up
This service allows the user to authenticate itself for the first time into the encryption service.
```
Input:
	3 and 4
Output:
	6 via postman/insomnia interface

Postman/Insomnia specifications
	Http endpoint:
		URL: http://127.0.0.1:8000/signup
		Input params:
			name: 3
			email: 4
```

#### Sign in
This service allows the user to authenticate itself into the encryption service and use the several encryption and decryption services only if the user loses his token.
```
Input:
	3 and 4
Output:
	6 via postman/insomnia interface

Postman/Insomnia specifications
	Http endpoint:
		URL: http://127.0.0.1:8000/authenticate
		Input params:
			name: 3
			email: 4
```

#### Encrypt
This service allows the user to upload a local JSON file for encryption.
```
Input:
	1 and 6
Output:
	2 via postman/insomnia interface and 5 via email

Postman/Insomnia specifications
	Http endpoint:
		URL: http://127.0.0.1:8000/encrypt
		Input params:
			json: document in JSON format
		Headers:
			Authorization: user token
```

#### Decrypt
This service allows the user to upload a local JSON file for decryption.
```
Input:
	1 and 5
Output:
	2 via postman/insomnia interface

Postman/Insomnia specifications
	Http endpoint:
		URL: http://127.0.0.1:8000/decrypt
		Input params:
			json: document in JSON format
			key: keys sent to users email
		Headers:
			Authorization: user token
```

#### Encrypt/ocb
This service allows the user to encrypt entities from a Context Broker URL.
```
Input:
	1 and 2
Output:
	2 via postman/insomnia interface and 5 via email

Postman/Insomnia specifications
	Http endpoint:
		URL: http://127.0.0.1:8000/encrypt/ocb
		Input params:
			urlFrom: example http://127.0.0.1:1026/v2/entities
			id: id attribute to identify the entity
			type: type attribute to identify the entity
			urlTo: http://192.168.10.193:1026/v2/entities
		Headers:
			Authorization: user token
```

#### Decrypt/ocb
This service allows the user to decrypt entities from a Context Broker URL.
```
Input:
	1, 2, and 5
Output:
	2 via postman/insomnia interface

Postman/Insomnia specifications
	Http endpoint:
		URL: http://127.0.0.1:8000/decrypt/ocb
		Input params:
			urlFrom: example http://127.0.0.1:1026/v2/entities
			id: id attribute to identify the entity
			type: type attribute to identify the entity
			urlTo: http://192.168.10.193:1026/v2/entities
			key : keys sent to users email
		Headers:
			Authorization: user token
```

## Encryption service with sessions
The stand-alone encryption service that uses sessions as verification measure is described below. First, the instructions to deploy and execute the service is described; then the sign up, sign in, encrypt, decrypt, encrypt/ocb, and decrypt/ocb processes are detailed.

### Deployment and execution instructions
The following procedure denotes the instructions to deploy and execute the encryption service that will reside in the server.

1. Locate at the service folder location: ```cd ../NGSI_PYTHON_DOCKER/```
1. Create a new folder named “data”: ```mkdir data```
1. Start the docker compose: ```sudo docker-compose up -d```
1. Check docker containers status: ```docker ps```
1. Verify that the service is up and running: ```http://localhost:2121```

### Use of services supported by the encryption service
The requirements and procedure to use the several services within encryption service are described below. 

#### Sign up
This service allows the user to authenticate itself for the first time into the encryption service.
```
Input:
	3 and 4
Output:
	Verification via email

Postman/Insomnia specifications
	Http endpoint:
		URL: http://127.0.0.1:2121/signup
		Input params:
			name: 3
			email: 4
			password: user password
```

#### Sign in
This service allows the user to authenticate itself into the encryption service and use the several encryption and decryption services.
```
Input:
	3
Output:
	6 via email or postman/insomnia interface

Postman/Insomnia specifications
	Http endpoint:
		URL: http://127.0.0.1:2121/login
		Input params:
			name: 3
			password: user password
```

#### Encrypt
This service allows the user to upload a local JSON file for encryption.
```
Input:
	1
Output:
	2 via postman/insomnia interface and 5 via email

Postman/Insomnia specifications
	Http endpoint:
		URL: http://127.0.0.1:2121/encrypt
		Input params:
			json: document in JSON format
```

#### Decrypt
This service allows the user to upload a local JSON file for decryption.
```
Input:
	1 and 5
Output:
	2 via postman/insomnia interface

Postman/Insomnia specifications
	Http endpoint:
		URL: http://127.0.0.1:2121/decrypt
		Input params:
			json: document in JSON format
			key: keys sent to users email
```

#### Encrypt/ocb
This service allows the user to encrypt entities from a Context Broker URL.
```
Input:
	1 and 2
Output:
	5 via email or postman/insomnia interface

Postman/Insomnia specifications
	Http endpoint:
		URL: http://127.0.0.1:2121/encrypt/ocb
		Input params:
			urlFrom: example http://127.0.0.1:1026/v2/entities
			id: id attribute to identify the entity
			type: type attribute to identify the entity
			urlTo: http://192.168.10.193:1026/v2/entities
```

#### Decrypt/ocb
This service allows the user to decrypt entities from a Context Broker URL.
```
Input:
	1, 2, and 5
Output:
	2 via postman/insomnia interface

Postman/Insomnia specifications
	Http endpoint:
		URL: http://127.0.0.1:2121/decrypt/ocb
		Input params:
			urlFrom: example http://127.0.0.1:1026/v2/entities
			id: id attribute to identify the entity
			type: type attribute to identify the entity
			urlTo: http://192.168.10.193:1026/v2/entities
			key : keys sent to users email
```

#### Sign out
This service allows the user to sign out of the encryption service.
```
Input:
	None
Output:
	None

Postman/Insomnia specifications
	Http endpoint:
		URL: http://127.0.0.1:2121/logout
		Input params:
		  None
```

