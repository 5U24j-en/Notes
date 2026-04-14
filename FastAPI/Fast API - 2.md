 
### User table and Authentication

- We will create a User table for users who are creating an account
	- **STEP 1 - Defining SQL Alchemy Model**
		- ![](../attachments/Screenshot%20from%202026-04-10%2018-08-02.png)
		- ![](../attachments/Screenshot%20from%202026-04-10%2018-08-20.png)
	- **STEP -2 - Defining API pydantic Schemas**
		- Request Body Schema
			- ![](../attachments/Screenshot%20from%202026-04-10%2018-09-28.png)
		- In pydantic we are importing 
			- `from pydantic import BaseModel, EmailStr, SecretStr, Field`
			- **EmailStr** - will validate the email
			- **SecretStr** - will help in hiding the password
			- **Field** - Helps in creating default values uuid
		- Response Body Schema
			- ![](../attachments/Screenshot%20from%202026-04-10%2018-10-03.png)
	- **STEP - 3 - POST API Call**
		- ![](../attachments/Screenshot%20from%202026-04-10%2018-12-16.png)
		- **OUTPUT**
			- ![](../attachments/Screenshot%20from%202026-04-10%2018-13-24.png)

	- ### Hashing 

		- ##### Storing passwords directly in Database is not secure
		- Hence we will Hash the password and then store it in the database
		- **STEP - 1 - create `utils.py` file**
			- `utils.py` file will have all the utility functions
			- ![](../attachments/Screenshot%20from%202026-04-10%2020-30-58.png)
		- **STEP - 2 - Use the function by importing the util file**
			- ![](../attachments/Screenshot%20from%202026-04-10%2020-31-30.png)
		- **DB:**
			- ![](../attachments/Pasted%20image%2020260410203341.png)
			- Password is stored in a Hash

	- ##### GET API - > for a id
		- **Code**
			- ![](../attachments/Pasted%20image%2020260410211513.png)
		- **OUTPUT**
			- ![](../attachments/Pasted%20image%2020260410211541.png)

- ### ROUTERS 
	- Routers are used to access APIs functions from different files 
	-  Since there is lot of code in `main.py` file we need to restructure the code
	- **STEP 1 -**  Create **routers** folder
		- Create `posts.py`
		- Create `users.py`
		- ![](../attachments/Screenshot%20from%202026-04-10%2022-40-18.png)
		- Move the posts API calls ( ORM ) into the `posts.py`
		- Move the users API calls into the `users.py`
		- Import necessary libraries 
	- **STEP 2 - Import APIRouter**
		- ![](../attachments/Screenshot%20from%202026-04-10%2022-46-35.png)
		- Initialize APIRouter
				-  ![](../attachments/Screenshot%20from%202026-04-10%2022-48-00.png)
		- prefix = > Instead to passing the entire route in the decorator, we just need to add the prefix in the router object itself
		- Replace **app.get(dsada)** with **router.get(sdad)**
			- ![](../attachments/Pasted%20image%2020260410225256.png)
	- **STEP 3 -  Updating**  `main.py`
		- Import the files 
			- ![](../attachments/Screenshot%20from%202026-04-10%2022-51-29.png)
		- And then add the routers
		- ![](../attachments/Screenshot%20from%202026-04-10%2022-50-55.png)

	- **STEP 5 - Adding Tags for Clean Documentation**
		- ![](../attachments/Screenshot%20from%202026-04-10%2022-58-48.png)
		- DOCS
			- ![](../attachments/Screenshot%20from%202026-04-10%2022-59-46.png)

### JWT Token Authentication

- ![](../attachments/Pasted%20image%2020260412134053.png)
-  We create a **"/login"** - path operation ( API ) , where the front end sends us the login username and password.
- `Request body`
	- `username ( can be email )`
	- `password` 
- The API path operation **"/login"** will then validate the credentials with the Database, if the credentials are correct the API returns a JWT Token , with the type
- Now , whenever User calls an API ( path operation ) which requires an proper user verification , example - updating a post. The API now must **pass the JWT token** which was created in the **/login**   in the **request header**
- Hence every critical login related API calls will require **a** **JWT token**

- #### JTW Token is NOT ENCRYPTED
	- Sample Token
		- `eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiYWRtaW4iOnRydWUsImlhdCI6MTUxNjIzOTAyMn0.KMUFsIDTnFmyG3nMiGM6H9FNFUROf3wh7SmqJp-QV30`
	- Here you can decode your token -> https://www.jwt.io/
	- The JWT Token contains 3 things
		- **Header**
			- It has the algorithm 
			- Type
		- **Payload**
			- Any Data to identify user like userID , etc
			- Payload is up to the API/developer , it can be anything 
			- But the payload must be non critical Data as anyone can decrypt a JWT token
			- Also try to keep minimal critical headers as too much data will increase the packet size
		- **Verify Signature**
			-  A signature is the combination if the 
				- **Header** - same as the JWT token
				- **Payload** - same as the JWT token
				- **A secret code** - **ONLY THE API / Backend has the Secret**
				- ![](../attachments/Pasted%20image%2020260412140031.png)
	- ![](../attachments/Screenshot%20from%202026-04-12%2013-51-02.png)


- #### Purpose of Signature

	- **JWT Token** - This is the Token
		- ![](../attachments/Screenshot%20from%202026-04-12%2014-00-59.png)
		- Signature is the combination of the Header , Payload and the Secret 
	- Scenario when a hacker tries to change/pass the Role of the User as admin
		- ![](../attachments/Screenshot%20from%202026-04-12%2014-06-30.png)
		- He can change the Header and Payload, but he cannot change the Signature as he does **NOT HAVE** the **secret** , hence its in red color
	- How he sends the Token to the backend
		- ![](../attachments/Screenshot%20from%202026-04-12%2014-08-34.png)
		- The Backend API Takes the **Header and Payload from the Token and then uses its own stored secret to create a Test Signature**
		- **It then verifies if the Signature from the Token is same as the Signature from the Test Signature**
		- Since the Hacker was not able to change the Signature, they will not be equal, hence Authentication Fails

- ### Creating a Login Path
	- ![](../attachments/Screenshot%20from%202026-04-12%2014-13-55.png)
	- DB has stored password in a Hashed Format
	- Hashing is One way, we cannot convert it back
	- To verify password we will Hash the input password and compare if it works

	- Create a New file as `auth.py`
		- ![](../attachments/Screenshot%20from%202026-04-12%2014-17-04.png)
		- We Imported the below to use **OAuth2PasswordRequestForm**
			- `from fastapi.security.oauth2 import OAuth2PasswordRequestForm`
			- Here the object has variables **username** and **password**
			- Now we need to pass the payload in the **form-data in POSTMAN**
				- ![](../attachments/Screenshot%20from%202026-04-12%2014-19-45.png)
	- In `utils.py`  create a function to verify hashed password and login password
			- ![](../attachments/Screenshot%20from%202026-04-12%2014-32-26.png)
	- In the code , we are only passing the **id (from the database)  as the payload to the JWT Token**
	- **Token Creation** 
		- Create a new file `oauth2.py`
		- ![](../attachments/Screenshot%20from%202026-04-12%2014-27-21.png)
			- **`jwt`** library from **`jose`** will have the functions to create the JWT Token
			- the **`jwt.encode`** needs the following parameters
				- SECRET KEY
				- ALGORITHM
				- Data to encode ( Like id , email , etc)
			- NOTE NEVER HARDCODE SECRETS LIKE SECRET_KEY. USE ENV VARIABLES
	- Add the Route
		- ![](../attachments/Screenshot%20from%202026-04-12%2014-36-22.png)


- #### Adding Dependencies to Routes which need authentication

	- For Example when Updating a Post requires user authentication 
	- Hence we will add Dependencies to the API call and create a function that will verify the user

	
	- Creating a Schema to verify the tokeData
		- This schema will be used to get the id of the user from the token itself
		- ![](../attachments/Screenshot%20from%202026-04-14%2020-18-35.png)
	- Function to Verify the Access Token from the protected API routes
		- ![](../attachments/Screenshot%20from%202026-04-14%2020-21-26.png)
			- **jwt.decode** will decode the token and store it in a variable payload
			- **payload.get("user_id")** will extract the user id from the JWT token ( remember headers)
			- This function returns the **user_id** from the token
			- **NOTE: Depending on the developer, the token_data can have more variables like id, email, etc . We are only passing id in the headers of the JWT token , but we can add more data depending on the use case**

	- The Dependency for the Protected API (like POST /posts) 
		- Initialize the 
			- ![](../attachments/Screenshot%20from%202026-04-14%2020-28-37.png)
			- **In Short - This basically extracts the Header from the POST /posts call**
		- 1. `OAuth2PasswordBearer` (The Security Scheme)
			- This is a class imported from `fastapi.security`. It implements a specific type of OAuth2 flow called the **"Resource Owner Password Credentials"** flow.
			- **Bearer Token:** It dictates that the token must be sent in the HTTP headers of the request, formatted specifically like this: `Authorization: Bearer <your_actual_token_here>`.
			- When you use this as a dependency in your routes, FastAPI will automatically look for that specific header, extract the token, and pass it to your function. If the header is missing or formatted incorrectly, FastAPI automatically returns a `401 Unauthorized` error.
		 2. `tokenUrl="login"` (The Sign-In Address)
			- This parameter specifies the endpoint (relative URL) where a client should send their credentials (username and password) to obtain the token.
			- **Important Note:** This does **not** actually create the `/login` route for you. It strictly acts as a pointer. It is primarily used by FastAPI's automatic interactive documentation (Swagger UI). When you click the "Authorize" button in the docs, Swagger knows to send your test username and password to the `/login` endpoint because you defined it here.

	- **The function the Protected API will call**
		- ![](../attachments/Screenshot%20from%202026-04-14%2019-43-02.png)
		- **Since We are extracting the JWT Token from the Headers ( with the help of oauth2_schema dependency) and checking for an exception if the JWT token does not exist

	- Protected API Updates
		- ![](../attachments/Screenshot%20from%202026-04-14%2020-34-07.png)

**Flowcharts** 

**Login Route**
![](../attachments/Screenshot%20from%202026-04-14%2020-19-41.png)

**Protected API route**
![](../attachments/Screenshot%20from%202026-04-14%2020-24-01.png)