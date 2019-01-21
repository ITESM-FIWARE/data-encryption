# NGSI-Encryption-Layer-as-a-Service (data encryption)

This document describes the encryption service developed at ITESM as a tool for encrypting and decrypting [FIWARE data models](https://www.fiware.org/developers/data-models/). 

[FIWARE](https://www.fiware.org/) is a curated framework of open source platform components to accelerate the development of Smart solutions. The [FIWARE platform](https://www.fiware.org/developers/catalogue/) provides a rather simple yet powerful set of APIs (Application Programming Interfaces) that ease the development of Smart Applications in multiple vertical sectors. 

The main and only mandatory component of any "Powered by FIWARE" platform or solution is the [FIWARE Orion Context Broker Generic Enabler](https://fiware-orion.readthedocs.io/en/master/), which brings a cornerstone function in any smart solution: the need to manage context information in a highly decentralized and large-scale manner, enabling to perform updates and bring access to context.

[FIWARE data models](https://www.fiware.org/developers/data-models/) have been harmonized to enable data portability for different applications including, but not limited, to Smart Cities. They are intended to be used together with [FIWARE NGSI version 2](https://www.fiware.org/2016/06/08/fiware-ngsi-version-2-release-candidate/).

The application can be seen as two stand-alone services, one that uses tokens as a security measure and the second one that uses sessions as a security measure. Both stand-alone services enable the encryption and decryption of all up-to-date available FIWARE data models published in [FIWARE Data Models official site](https://www.fiware.org/developers/data-models/).


## Prerequisites
The encryption service can be installed on any Operative System.

The following software must be previously installed in the server which will hold the encryption service.
1. [Docker](https://www.docker.com/get-started)
1. [Postman](https://www.getpostman.com/apps)/[Insomnia](https://insomnia.rest/download/)

Furthermore, the following ports containers are required.
1. ngsi_nodejs 8000 (only for the token-based service)
1. ngsi_python 2121 (only for the session-based service)


## How to use
In the following, the service specifications are described. These specifications apply to both stand-alone services mentioned above.
The inputs that the encryption service expect are listed below.
1. Orion URL Source or a JSON payload
2. Orion URL Target or a JSON payload
3. User name
4. User email
5. Keys generated by the encryption process
6. Token generated by the encryption service

In the following are described several details about the encryption service.

* Due to context broker’s limits on attribute lengths, the encryption algorithm relies on 8-bit keys.
* For each attribute of the JSON payload, a key is generated. All the keys are integrated into a single file. This single file is then sent to the user via email.

The services that both stand-alone implemented services comprise are:
* Sign up
* Sign in
* Encryption of a local JSON file
* Encryption of entities from an Orion Context Broker URL
* Encryption of a local JSON file or entities from an Orion Context Broker URL and send them to a Context Broker URL
* Decryption of a local JSON file 
* Decryption of entities from an Orion Context Broker URL
* Decryption of a local JSON file or entities from an Orion Context Broker URL and send them to a Context Broker URL

The initial authentication procedure carried out by the encryption service is performed via an email verification process. This process allows a user to authenticate itself within the service. It is noteworthy that the authentication procedure currently works only with Gmail accounts. The process is simplified as follows:

```
Input: User name (#3 in the overall input definition) and User email (#4 in the overall input definition)
Output: Token generated by the encryption service
```

Postman/Insomnia specifications
```
HTTP endpoint:
	POST
	Uri: http://ngsi-edlayer/signup
	
	Params (Form URL Encoded):
		name: User name (3)
		email: User email (4)
	
	Headers:
		Fiware-Service:'default'
		Fiware-ServicePath:'/'

```


## A walkthrough
### Token-based service
The stand-alone encryption service that uses tokens as a verification measure is described below.

#### Deployment and execution instructions
The following procedure denotes the instructions to deploy and execute the encryption service that will reside in the server.

1. Locate at the service folder location: ```cd ../NGSI_NODEJS_DOCKER```

	![imagen1](https://user-images.githubusercontent.com/39604832/46701972-2d23b980-cbe7-11e8-9010-24025322c196.png)

2. Execute the docker pull command: ```docker pull ngsi/encryption_layer_as_a_service_nodejs```
3. Start the docker compose: ```sudo docker-compose up -d```

	![imagen2](https://user-images.githubusercontent.com/39604832/46699889-c6030680-cbe0-11e8-8fe3-8c19b8ae3e7b.png)

4. Check docker containers status: ```docker ps```

	![imagen3](https://user-images.githubusercontent.com/39604832/46701974-2d23b980-cbe7-11e8-9139-03e789c82c44.png)
	![imagen4](https://user-images.githubusercontent.com/39604832/46701975-2d23b980-cbe7-11e8-8de1-fa66adfac05b.png)

5. Verify that the service is up and running: ```http://localhost:8000```

	![imagen5](https://user-images.githubusercontent.com/39604832/46701976-2d23b980-cbe7-11e8-8345-385e0cca8aea.png) 

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

<p align="center"><img src="https://user-images.githubusercontent.com/38046220/51491456-fc215c00-1d73-11e9-8876-2e1dc14e4cb1.png"/></p>

<p align="center"><img src="https://user-images.githubusercontent.com/38046220/51491527-33900880-1d74-11e9-8c17-d3c05ed3e384.png"/></p>

   ![imagen6](https://user-images.githubusercontent.com/39604832/46701977-2dbc5000-cbe7-11e8-9a5d-9cf84960f03d.png)

#### Sign in
This service allows the user to authenticate itself into the encryption service and use the several encryption and decryption services implemented.
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

<p align="center"><img src="https://user-images.githubusercontent.com/38046220/51497398-a0150280-1d88-11e9-9fd6-ea3df1eea328.png"/></p>

<p align="center"><img src="https://user-images.githubusercontent.com/38046220/51497411-a3a88980-1d88-11e9-96d5-03a5e1ea98d8.png"/></p>

#### Encryption of a local JSON file
This process shows an example of how a user uploads a local JSON file into the service, encrypt it, sees the encrypted JSON file through the postman/insomnia interface, and receives the keys for decrypt the JSON file via email. To download the encrypted JSON file, the user must copy the postman/insomnia response into a text file and save it with the JSON extension or download it directly from the postman/insomnia interface. This service does not send the encrypted JSON file to an Orion Context Broker URL; the service that allows this is explained below.
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
			Fiware-Service:'default'
			Fiware-ServicePath:'/'
```

<p align="center"><img src="https://user-images.githubusercontent.com/38046220/51498123-09961080-1d8b-11e9-88ab-c85f346b43d1.png"/></p>

<p align="center"><img src="https://user-images.githubusercontent.com/38046220/51498142-14e93c00-1d8b-11e9-8d94-fb5c3c0a0a48.png"/></p>

<p align="center"><img src="https://user-images.githubusercontent.com/38046220/51498146-174b9600-1d8b-11e9-8ee3-59636cdce19e.png"/></p>

<p align="center"><img src="https://user-images.githubusercontent.com/38046220/51498147-187cc300-1d8b-11e9-9367-9739ea7c0e2f.png"/></p>

<p align="center"><img src="https://user-images.githubusercontent.com/38046220/51498149-19adf000-1d8b-11e9-9514-0c272e15cca8.png"/></p>

<p align="center"><img src="https://user-images.githubusercontent.com/38046220/51498151-1adf1d00-1d8b-11e9-8b0d-7cbe07ca2096.png"/></p>

#### Encryption of entities from an Orion Context Broker URL
This process shows an example of how a user specifies entities from an Orion Context Broker URL, encrypt them, sees the encrypted entities through the postman/insomnia interface, and receives the keys for decrypt the JSON file via email. To download the encrypted JSON file, the user must copy the postman/insomnia response into a text file and save it with the JSON extension or download it directly from the postman/insomnia interface. This service does not send the encrypted entities to an Orion Context Broker URL; the service that allows this is explained below.
```
Input:
	1 and 2
Output:
	2 via postman/insomnia interface and 5 via email

Postman/Insomnia specifications
	Http endpoint:
		URL: http://127.0.0.1:8000/encrypt/ocb-local
		Input params:
			urlFrom: example http://127.0.0.1:1026/v2/entities
			id: id attribute to identify the entity
			type: type attribute to identify the entity
		Headers:
			Authorization: user token
			Fiware-Service:'default'
			Fiware-ServicePath:'/'
```

<p align="center"><img src="https://user-images.githubusercontent.com/38046220/51498873-c12c2200-1d8d-11e9-8f02-36516efc461c.png"/></p>

<p align="center"><img src="https://user-images.githubusercontent.com/38046220/51498778-6bf01080-1d8d-11e9-99d7-404dae4c2609.png"/></p>

<p align="center"><img src="https://user-images.githubusercontent.com/38046220/51498779-6c88a700-1d8d-11e9-86e7-a62752b70cb6.png"/></p>

<p align="center"><img src="https://user-images.githubusercontent.com/38046220/51498780-6e526a80-1d8d-11e9-82d0-d0558203ec26.png"/></p>

<p align="center"><img src="https://user-images.githubusercontent.com/38046220/51498781-6f839780-1d8d-11e9-9c0a-85c6ec9774cd.png"/></p>

<p align="center"><img src="https://user-images.githubusercontent.com/38046220/51498783-70b4c480-1d8d-11e9-8af5-0f83e03ceea4.png"/></p>

#### Encryption of a local JSON file or entities from an Orion Context Broker URL and send them to a Context Broker URL
This process shows an example of how a user specifies entities from an Orion Context Broker URL or uploads a local JSON file, encrypt them/it, and send the encrypted entities to an Orion Context Broker URL. Furthermore, the service shows the encrypted JSON file or entities through the postman/insomnia interface and the user receives the keys for decrypt the information via email. To download the encrypted JSON file, the user must copy the postman/insomnia response into a text file and save it with the JSON extension or download directly from the postman/insomnia interface.
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
			Fiware-Service:'default'
			Fiware-ServicePath:'/'
```

<p align="center"><img src="https://user-images.githubusercontent.com/38046220/51499146-c0e05680-1d8e-11e9-9f5f-6d69b970318c.png"/></p>

<p align="center"><img src="https://user-images.githubusercontent.com/38046220/51499148-c178ed00-1d8e-11e9-818a-e268ddc7d51f.png"/></p>

<p align="center"><img src="https://user-images.githubusercontent.com/38046220/51499154-c342b080-1d8e-11e9-9801-59633caad762.png"/></p>

<p align="center"><img src="https://user-images.githubusercontent.com/38046220/51499157-c473dd80-1d8e-11e9-85da-a03ed2c22413.png"/></p>

<p align="center"><img src="https://user-images.githubusercontent.com/38046220/51499158-c50c7400-1d8e-11e9-850d-544d1af72f19.png"/></p>

<p align="center"><img src="https://user-images.githubusercontent.com/38046220/51499159-c63da100-1d8e-11e9-8f75-412d3211104e.png"/></p>

<p align="center"><img src="https://user-images.githubusercontent.com/38046220/51499160-c8076480-1d8e-11e9-8940-a6074c3a9d24.png"/></p>

#### Decryption of a local JSON file
This process shows an example of how a user uploads a local encrypted JSON file and its corresponding key's file into the service, decrypt it, and sees the decrypted JSON file through the postman/insomnia interface. To download the decrypted JSON file, the user must copy the postman/insomnia response into a text file and save it with the JSON extension or download it directly from the postman/insomnia interface. This service does not send the decrypted JSON file to an Orion Context Broker URL; the service that allows this is explained below.
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
			Fiware-Service:'default'
			Fiware-ServicePath:'/'
```

<p align="center"><img src="https://user-images.githubusercontent.com/38046220/51499993-0e11f780-1d92-11e9-9a36-be417109e3e9.png"/></p>

<p align="center"><img src="https://user-images.githubusercontent.com/38046220/51499997-0fdbbb00-1d92-11e9-8224-6c6c3f9563cf.png"/></p>

<p align="center"><img src="https://user-images.githubusercontent.com/38046220/51499994-0f432480-1d92-11e9-8f55-cf0efebd12e5.png"/></p>

<p align="center"><img src="https://user-images.githubusercontent.com/38046220/51499998-10745180-1d92-11e9-987f-373585f4f0e5.png"/></p>

<p align="center"><img src="https://user-images.githubusercontent.com/38046220/51500000-11a57e80-1d92-11e9-8f9a-aab0880f3ab2.png"/></p>

#### Decryption of entities from an Orion Context Broker URL
This process shows an example of how a user specifies entities from an Orion Context Broker URL and uploads its corresponding key's file into the service, decrypt them, and sees the decrypted entities through the postman/insomnia interface. To download the decrypted JSON file, the user must copy the postman/insomnia response into a text file and save it with the JSON extension or download it directly from the postman/insomnia interface. This service does not send the decrypted entities to an Orion Context Broker URL; the service that allows this is explained below.
```
Input:
	1, 2, and 5
Output:
	2 via postman/insomnia interface

Postman/Insomnia specifications
	Http endpoint:
		URL: http://127.0.0.1:8000/decrypt/ocb-local
		Input params:
			urlFrom: example http://127.0.0.1:1026/v2/entities
			id: id attribute to identify the entity
			type: type attribute to identify the entity
			key : keys sent to users email
		Headers:
			Authorization: user token
			Fiware-Service:'default'
			Fiware-ServicePath:'/'
```

<p align="center"><img src="https://user-images.githubusercontent.com/38046220/51500113-84165e80-1d92-11e9-8638-e200aed2b316.png"/></p>

<p align="center"><img src="https://user-images.githubusercontent.com/38046220/51500115-85478b80-1d92-11e9-99b3-1917f31b9c98.png"/></p>

<p align="center"><img src="https://user-images.githubusercontent.com/38046220/51500117-85e02200-1d92-11e9-9b17-b3f2a32735eb.png"/></p>

<p align="center"><img src="https://user-images.githubusercontent.com/38046220/51500118-87114f00-1d92-11e9-8537-bda38909d52a.png"/></p>

<p align="center"><img src="https://user-images.githubusercontent.com/38046220/51500121-87a9e580-1d92-11e9-907f-7a90307c303a.png"/></p>

#### Decryption of a local JSON file or entities from an Orion Context Broker URL and send them to a Context Broker URL
This process shows an example of how a user specifies entities from an Orion Context Broker URL or uploads a local JSON file with its corresponding key's file, decrypt them/it, and then send the decrypted entities to an Orion Context Broker URL. Furthermore, the service shows the decrypted JSON file or entities through the postman/insomnia interface. To download the decrypted JSON file, the user must copy the postman/insomnia response into a text file and save it with the JSON extension or download it directly from the postman/insomnia interface.
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
			Fiware-Service:'default'
			Fiware-ServicePath:'/'
```

<p align="center"><img src="https://user-images.githubusercontent.com/38046220/51500316-2fbfae80-1d93-11e9-9e3c-119a3d9a0de3.png"/></p>

<p align="center"><img src="https://user-images.githubusercontent.com/38046220/51500318-30584500-1d93-11e9-8d14-0d74e7d5bb77.png"/></p>

<p align="center"><img src="https://user-images.githubusercontent.com/38046220/51500319-31897200-1d93-11e9-9016-96bef8948b93.png"/></p>

<p align="center"><img src="https://user-images.githubusercontent.com/38046220/51500321-32220880-1d93-11e9-8d6e-b725316b8d0c.png"/></p>

<p align="center"><img src="https://user-images.githubusercontent.com/38046220/51500324-33533580-1d93-11e9-96b6-24a9519f4cbd.png"/></p>

<p align="center"><img src="https://user-images.githubusercontent.com/38046220/51500326-33ebcc00-1d93-11e9-98ec-5e3b85dfd639.png"/></p>

### Session-based service
The stand-alone encryption service that uses sessions as a verification measure is described below.

#### Deployment and execution instructions
The following procedure denotes the instructions to deploy and execute the encryption service that will reside in the server.

1. Locate at the service folder location: ```cd ../NGSI_PYTHON_DOCKER/```

	![imagen15](https://user-images.githubusercontent.com/39604832/46701987-2e54e680-cbe7-11e8-9634-c62be7b676d9.png)

2. Create a new folder named “data”: ```mkdir data```

	![imagen16](https://user-images.githubusercontent.com/39604832/46701988-2e54e680-cbe7-11e8-8e7f-e00182ba71f8.png)

3. Start the docker compose: ```sudo docker-compose up -d```

	![imagen17](https://user-images.githubusercontent.com/39604832/46701990-2e54e680-cbe7-11e8-8602-270fa121f8a4.png)

4. Check docker containers status: ```docker ps```

	![imagen18](https://user-images.githubusercontent.com/39604832/46701991-2e54e680-cbe7-11e8-84fc-18a46010a29d.png)

5. Verify that the service is up and running: ```http://localhost:2121```

	![imagen19](https://user-images.githubusercontent.com/39604832/46701992-2eed7d00-cbe7-11e8-8603-8dd47f8066e4.png)

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
<div>
	<img src="https://user-images.githubusercontent.com/38957081/51502836-88944480-1d9d-11e9-9eb6-57340c7fbe38.png">
	<br><br><br>
	<img src="https://user-images.githubusercontent.com/38957081/51338867-991c8600-1a50-11e9-913f-64476d88a355.png">
	<br><br><br>
	<img src="https://user-images.githubusercontent.com/38957081/51286622-5cea1680-19b9-11e9-9076-ca0619096126.png">
</div>	

#### Sign in
This service allows the user to authenticate itself into the encryption service and use the several encryption and decryption services implemented.
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
		Headers:
			Fiware-Service:'default'
			Fiware-ServicePath:'/'
```
<div>
	<img src="https://user-images.githubusercontent.com/38957081/51502737-fdb34a00-1d9c-11e9-9ca0-1058ba9970b4.png">
	<br><br><br>
	<img src="https://user-images.githubusercontent.com/38957081/51287553-ad637300-19bd-11e9-87fd-05024da4db31.png">
</div>

#### Encryption of a local JSON file
This process shows an example of how a user uploads a local JSON file into the service, encrypt it, sees the encrypted JSON file through the postman/insomnia interface, and receives the keys for decrypt the JSON file via email. To download the encrypted JSON file, the user must copy the postman/insomnia response into a text file and save it with the JSON extension or download it directly from the postman/insomnia interface. This service does not send the encrypted JSON file to an Orion Context Broker URL; the service that allows this is explained below.
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
		Headers:
			Fiware-Service:'default'
			Fiware-ServicePath:'/'
```
<div>
	<img src="https://user-images.githubusercontent.com/38957081/51502626-82519880-1d9c-11e9-9132-a801805a5052.png">
	<br><br><br>
	<img src="https://user-images.githubusercontent.com/38957081/51497062-89ba7700-1d87-11e9-9f80-47324104eacf.png">
	<br><br><br>
	<img src="https://user-images.githubusercontent.com/38957081/51497071-9048ee80-1d87-11e9-8ddc-613f5b7cc3d4.png">
	<br><br><br>
	<img src="https://user-images.githubusercontent.com/38957081/51497085-9a6aed00-1d87-11e9-95ae-7e825db24f30.png">
</div>

#### Encryption of entities from an Orion Context Broker URL
This process shows an example of how a user specifies entities from an Orion Context Broker URL, encrypt them, sees the encrypted entities through the postman/insomnia interface, and receives the keys for decrypt the JSON file via email. To download the encrypted JSON file, the user must copy the postman/insomnia response into a text file and save it with the JSON extension or download it directly from the postman/insomnia interface. This service does not send the encrypted entities to an Orion Context Broker URL; the service that allows this is explained below.
```
Input:
	1 and 2
Output:
	5 via email or postman/insomnia interface

Postman/Insomnia specifications
	Http endpoint:
		URL: http://127.0.0.1:2121/encrypt/ocb/local
		Input params:
			urlFrom: example http://127.0.0.1:1026/v2/entities
			id: id attribute to identify the entity
			type: type attribute to identify the entity
		Headers:
			Fiware-Service:'default'
			Fiware-ServicePath:'/'
```
<div>
	<img src="https://user-images.githubusercontent.com/38957081/51500329-34846280-1d93-11e9-9cd9-43955a899f1e.png">
	<br><br><br>
	<img src="https://user-images.githubusercontent.com/38957081/51502447-ca23f000-1d9b-11e9-9d3c-7e007d37e0c1.png">
	<br><br><br>
	<img src="https://user-images.githubusercontent.com/38957081/51500331-351cf900-1d93-11e9-9eb0-d37baac8bb64.png">
	<br><br><br>
	<img src="https://user-images.githubusercontent.com/38957081/51500332-351cf900-1d93-11e9-978d-bfea85960e26.png">
	<br><br><br>
	<img src="https://user-images.githubusercontent.com/38957081/51500333-351cf900-1d93-11e9-8351-065b8d8b0a3f.png">
	<br><br><br>
	<img src="https://user-images.githubusercontent.com/38957081/51500335-351cf900-1d93-11e9-859e-af96f577d5aa.png">
	<br><br><br>
	<img src="https://user-images.githubusercontent.com/38957081/51500336-351cf900-1d93-11e9-9aab-c12fdc38e2ed.png">
</div>

#### Encryption of a local JSON file or entities from an Orion Context Broker URL and send them to a Context Broker URL
This process shows an example of how a user specifies entities from an Orion Context Broker URL or uploads a local JSON file, encrypt them/it, and then send the encrypted entities to an Orion Context Broker URL. Furthermore, the service shows the encrypted JSON file or entities through the postman/insomnia interface and the user receives the keys for decrypt the information via email. To download the encrypted JSON file, the user must copy the postman/insomnia response into a text file and save it with the JSON extension or download it directly from the postman/insomnia interface.
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
		Headers:
			Fiware-Service:'default'
			Fiware-ServicePath:'/'
```
<div>
	<img src="https://user-images.githubusercontent.com/38957081/51501003-d5741d00-1d95-11e9-8833-4b8d2e30073d.png">
	<br><br><br>
	<img src="https://user-images.githubusercontent.com/38957081/51501001-d5741d00-1d95-11e9-8628-ae715c24622f.png">
	<br><br><br>
	<img src="https://user-images.githubusercontent.com/38957081/51501005-d5741d00-1d95-11e9-81f7-e5cc839217a9.png">
	<br><br><br>
	<img src="https://user-images.githubusercontent.com/38957081/51501006-d60cb380-1d95-11e9-87b6-b9d0c0895048.png">
	<br><br><br>
	<img src="https://user-images.githubusercontent.com/38957081/51501007-d60cb380-1d95-11e9-8b36-88a82de0a8ac.png">
	<br><br><br>
	<img src="https://user-images.githubusercontent.com/38957081/51501008-d60cb380-1d95-11e9-8bfb-ed50a5a03f89.png">
	<br><br><br>
	<img src="https://user-images.githubusercontent.com/38957081/51501009-d60cb380-1d95-11e9-9945-cedd2064abe2.png">
	<br><br><br>
	<img src="https://user-images.githubusercontent.com/38957081/51501011-d6a54a00-1d95-11e9-93d0-8599aa9abf39.png">
	<br><br><br>
	<img src="https://user-images.githubusercontent.com/38957081/51501012-d6a54a00-1d95-11e9-857b-822bb6d71fde.png">
</div>

#### Decryption of a local JSON file
This process shows an example of how a user uploads a local encrypted JSON file and its corresponding key's file into the service, decrypt it, and then sees the decrypted JSON file through the postman/insomnia interface. To download the decrypted JSON file, the user must copy the postman/insomnia response into a text file and save it with the JSON extension or download it directly from the postman/insomnia interface. This service does not send the decrypted JSON file to an Orion Context Broker URL; the service that allows this is explained below.
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
		Headers:
			Fiware-Service:'default'
			Fiware-ServicePath:'/'
```
<div>
	<img src="https://user-images.githubusercontent.com/38957081/51501843-2f2a1680-1d99-11e9-9704-aea414486781.png">
	<br><br><br>
	<img src="https://user-images.githubusercontent.com/38957081/51498128-0ac73d80-1d8b-11e9-916c-46f0bf69b6f4.png">
	<br><br><br>
	<img src="https://user-images.githubusercontent.com/38957081/51498129-0ac73d80-1d8b-11e9-9f20-e5e45fd736f4.png">
	<br><br><br>
	<img src="https://user-images.githubusercontent.com/38957081/51498130-0ac73d80-1d8b-11e9-9e43-2deb7d48600c.png">
</div>
 
#### Decryption of entities from an Orion Context Broker URL
This process shows an example of how a user specifies entities from an Orion Context Broker URL and uploads its corresponding key's file into the service, decrypt them, and then sees the decrypted entities through the postman/insomnia interface. To download the decrypted JSON file, the user must copy the postman/insomnia response into a text file and save it with the JSON extension or download it directly from the postman/insomnia interface. This service does not send the decrypted entities to an Orion Context Broker URL; the service that allows this is explained below.
```
Input:
	1, 2, and 5
Output:
	2 via postman/insomnia interface

Postman/Insomnia specifications
	Http endpoint:
		URL: http://127.0.0.1:2121/decrypt/ocb/local
		Input params:
			urlFrom: example http://127.0.0.1:1026/v2/entities
			id: id attribute to identify the entity
			type: type attribute to identify the entity
			key : keys sent to users email
		Headers:
			Fiware-Service:'default'
			Fiware-ServicePath:'/'
```
<div>
	<img src="https://user-images.githubusercontent.com/38957081/51501466-9e9f0680-1d97-11e9-84ef-6b88926c5adb.png">
	<br><br><br>
	<img src="https://user-images.githubusercontent.com/38957081/51501465-9e9f0680-1d97-11e9-9dce-241bad2a10c4.png">
	<br><br><br>
	<img src="https://user-images.githubusercontent.com/38957081/51501467-9f379d00-1d97-11e9-8444-56cd2ebbce6d.png">
	<br><br><br>
	<img src="https://user-images.githubusercontent.com/38957081/51501468-9f379d00-1d97-11e9-8e13-cc30f7f38cc4.png">
	<br><br><br>
	<img src="https://user-images.githubusercontent.com/38957081/51501469-9f379d00-1d97-11e9-946d-1f6c1290ec34.png">
	<br><br><br>
	<img src="https://user-images.githubusercontent.com/38957081/51501470-9f379d00-1d97-11e9-993d-142b959f3ca3.png">
	<br><br><br>
	<img src="https://user-images.githubusercontent.com/38957081/51501471-9f379d00-1d97-11e9-9eae-d73da1da9937.png">
</div>

#### Decryption of a local JSON file or entities from an Orion Context Broker URL and send them to a Context Broker URL
This process shows an example of how a user specifies entities from an Orion Context Broker URL or uploads a local JSON file with its corresponding key's file, decrypt them/it, and then send the decrypted entities to an Orion Context Broker URL. Furthermore, the service shows the decrypted JSON file or entities through the postman/insomnia interface. To download the decrypted JSON file, the user must copy the postman/insomnia response into a text file and save it with the JSON extension or download it directly from the postman/insomnia interface.
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
		Headers:
			Fiware-Service:'default'
			Fiware-ServicePath:'/'
```
<div>
	<img src="https://user-images.githubusercontent.com/38957081/51503047-8ed6f080-1d9e-11e9-88e8-a265f68890e5.png">
	<br><br><br>
	<img src="https://user-images.githubusercontent.com/38957081/51503045-8ed6f080-1d9e-11e9-9bc7-c4db7c8e9677.png">
	<br><br><br>
	<img src="https://user-images.githubusercontent.com/38957081/51503048-8f6f8700-1d9e-11e9-947e-0cde14238e16.png">
	<br><br><br>
	<img src="https://user-images.githubusercontent.com/38957081/51503049-8f6f8700-1d9e-11e9-9ac6-886f5bd682d6.png">
	<br><br><br>
	<img src="https://user-images.githubusercontent.com/38957081/51503050-8f6f8700-1d9e-11e9-8381-de36162b853f.png">
	<br><br><br>
	<img src="https://user-images.githubusercontent.com/38957081/51503051-8f6f8700-1d9e-11e9-9bba-9b86e1f49f90.png">
	<br><br><br>
	<img src="https://user-images.githubusercontent.com/38957081/51503052-8f6f8700-1d9e-11e9-9319-59920af603f3.png">
	<br><br><br>
	<img src="https://user-images.githubusercontent.com/38957081/51503053-8f6f8700-1d9e-11e9-918d-5fd5b983a851.png">
	<br><br><br>
	<img src="https://user-images.githubusercontent.com/38957081/51503054-90081d80-1d9e-11e9-81f3-f97efb62617d.png">
</div>

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

![sign out](https://user-images.githubusercontent.com/38957081/51338291-0b8c6680-1a4f-11e9-8723-04b97d71d8de.png)

