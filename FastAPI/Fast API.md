https://fastapi.tiangolo.com/tutorial/first-steps/

Video: https://youtu.be/0sOvCWFmrtA?si=ZLZ0BLEG7vqej5Z9
<h1>Environment</h1>


**Step 1:**  Creating Environment of environment name ==venv==
 ``` 
 python -m venv venv 
 ```
- The above creates a environment folder
- ![[Screenshot from 2026-03-12 17-08-01.png]]


**Step 2:**  Starting the Environment
- To run the environment there is a activate file in bin.
- ![[Pasted image 20260312172246.png]]
```
source myenvp1/bin/activate
```


**Step 3:**  Once inside the virtual environment `pip list` shows all the libraries / dependencies. New environment wont have any libraries.


**Step 4:**  Install `fastapi` framework and `uvicorn` web server.
```
pip install fastapi uvicorn
```
OR
```
pip install fastapi[all]
```


**Step 5:**  Run the Web server to run the main code
```
uvicorn main:app
```

Hello World: 
```
from fastapi import FastAPI

  

app= FastAPI()

  

@app.get("/")

async def root():

return {"message": "Hello World"}
```

- @app.get("/") = is called Path Operation


- def root(): - this is a Root function, like a default page of the URL
- Local URL
```
 http://127.0.0.1:8000
```

- `http://127.0.0.1:8000/` -> with only / is the default Page. Hence root() function.


- #### Live Server
	`uvicorn main:app --reload`

- #### Path Operation
	- ![[Pasted image 20260312215217.png]]
	- Path: Endpoint URL
	- Method Decorator: HTTP Type 

NOTE: If there are more than 1 method with same path, Only the first one will be considered.

GET and POST Request:

![[Pasted image 20260314160056.png]]

```
@app.post("/createposts")
def create_posts(PayLoad: dict=Body(...)):
	print(PayLoad)
	return {"message": "post created succesfully"}
```

![[Pasted image 20260314162456.png]]

- Pydantic Models

	- **Pydantic** is a Python library used for **data validation and parsing using type annotations**.
	- ![](Pasted%20image%2020260314181541.png)


- **POST Method**
	- ![](Pasted%20image%2020260314181615.png)
	- `newPost.title` prints only the title 
	- `newPost.content` print only the content
	- API Call
		- ![](Pasted%20image%2020260314181832.png)
	- Output:
		- ![](Pasted%20image%2020260314181957.png)

	- Data Validation
		- ![](../attachments/Screenshot%20from%202026-04-03%2022-11-24.png)
		- Normal Response
			- ![](../attachments/Screenshot%20from%202026-04-03%2022-08-21.png)
			- ![](../attachments/Screenshot%20from%202026-04-03%2022-08-55.png)
		- Error response due to invalid rating datatype
			- ![](../attachments/Screenshot%20from%202026-04-03%2022-09-46.png)
			- ![](../attachments/Screenshot%20from%202026-04-03%2022-10-56.png)

- #### CRUD Operation
	- ![](../attachments/Screenshot%20from%202026-04-03%2022-14-05.png)
	- PUT 
		- Sending the Entire Field and updating 
	- PATCH 
		- Sending only the required Fields 


- `@app.get("/posts/{id}")` - > Path Parameter
	- ![304](../attachments/Screenshot%20from%202026-04-03%2022-55-14.png)
	- **OUTPUT**
	- ![](../attachments/Screenshot%20from%202026-04-03%2023-03-39.png)
	- Path Parameter always returns a string , hence we need to typecast id to integer
	- Hence we can use **Type Hinting**
	- ![](../attachments/Screenshot%20from%202026-04-03%2023-01-36.png)
	- Type hinting will return an clean error output as well
	- **OUTPUT**
	- ![](../attachments/Screenshot%20from%202026-04-03%2023-04-21.png)

- #### ORDER Matters
	- ![](../attachments/Screenshot%20from%202026-04-04%2016-53-10.png)
	- **OUTPUT**
	- ![](../attachments/Screenshot%20from%202026-04-04%2016-56-05.png)
	- This returns error because it considers `post/latest` as `post/{id}` , since id can be string as well 
	- We are defining id in the function
	- once we change order
	- ![334](../attachments/Screenshot%20from%202026-04-04%2017-00-58.png)
	- **OUTPUT**
	- ![](../attachments/Screenshot%20from%202026-04-04%2017-01-38.png)

- #### Response Codes
	- **Documentation** : https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Status
	- **USE Case:** 
		- If user sends a path id which does not exist , we want to send a 404 response code and a proper message
		- Current CODE OUTPUT
		- ![](../attachments/Screenshot%20from%202026-04-04%2017-13-34.png)
	- **Response  and status library**
		- `from fastapi import FastAPI, Response, status`
		- ![](../attachments/Screenshot%20from%202026-04-04%2017-15-56.png)
		- **OUTPUT**
		- ![](../attachments/Screenshot%20from%202026-04-04%2017-16-31.png)
	-  **USING HTTPException**
		- `from fastapi import FastAPI, Response, status, HTTPException`
		- ![](../attachments/Screenshot%20from%202026-04-04%2017-20-45.png)
		- Same OUTPUT
	- **Updating POST with correct Default Response code**
		- ![](../attachments/Screenshot%20from%202026-04-04%2017-23-01.png)
		- We can update the decorator to customize the **default** response code
		- **Output**
		- ![](../attachments/Screenshot%20from%202026-04-04%2017-26-25.png)

- #### DELETE
	- Function to find index
		- ![](../attachments/Screenshot%20from%202026-04-04%2017-41-46.png)
	- Delete API call status code is = 204
	- Delete API call should not return any OUTPUT 
	- ![](../attachments/Screenshot%20from%202026-04-04%2017-48-44.png)
	- **OUTPUT**
	- ![](../attachments/Screenshot%20from%202026-04-04%2017-43-56.png)
	- ![](../attachments/Screenshot%20from%202026-04-04%2017-49-15.png)


- #### PUT - Updating Content by passing the entire content in the body
	- 
	- ![](../attachments/Pasted%20image%2020260404181332.png)
	- **OUTPUT:**
	- ![](../attachments/Screenshot%20from%202026-04-04%2018-14-51.png)


- **File Structure**
	-  A folder with a `__init__.py` file in python is called package .
	- We move our `main.py` file into the app package
	- ![](../attachments/Pasted%20image%2020260404182148.png)
	- To launch uvicorn web server
		- **`uvicorn app.main:app --reload`**
		- app.main points to the directory



### Databases - PostgreSQL

- Create a Server - add the Database Hostname , admin user and password
	- ![](../attachments/Screenshot%20from%202026-04-04%2022-06-02.png)
- Under Database -> create table
	- ![](../attachments/Screenshot%20from%202026-04-04%2022-05-03.png)

### Database connection in python

- ##### For Postgre Database import
	- `import psycopg`
	- `from psycopg.rows import dict_row`
	
- **Initialize the DB connection**
	- ![](../attachments/Pasted%20image%2020260405162840.png)
	- ![](../attachments/Screenshot%20from%202026-04-05%2016-29-02.png)

- There is no point of the application to run if the DB connection is not successful, Hence we add a while loop till there is a successful connection

	- ![](../attachments/Pasted%20image%2020260405163306.png)


-  **Password is HARD CODED, which is a bad practice** 

- **GET API call using Database**
	- ![](../attachments/Screenshot%20from%202026-04-05%2016-41-54.png)
	- **OUTPUT**
	- ![](../attachments/Screenshot%20from%202026-04-05%2016-42-56.png)

- **POST API call using DB**
	- NEVER DO 
		- `cursor.execute(f"INSERT INTO posts (title, content, published) VALUES({post.title}, {post.content})")`
		- This can lead to **SQL INJECTION**
		- SQL INJECTION
			- ![](../attachments/Screenshot%20from%202026-04-05%2018-50-33.png)
			- Passing SQL statements into body to access SQL data

	- API Call
		- ![](../attachments/Screenshot%20from%202026-04-05%2018-59-36.png)
		- Code with ERROR handling
		- ![](../attachments/Screenshot%20from%202026-04-05%2019-06-08.png)
	- OUTPUT
		- ![](../attachments/Screenshot%20from%202026-04-05%2018-59-59.png)
		- Database Updated as well
		

- **Finding Single POST API call**

	- ![](../attachments/Screenshot%20from%202026-04-05%2020-18-51.png)
	- NOTE : Here we are removing the response variable and raising **HTTPException** 
	- NOTE: Always convert the **id** as string in **execute** command as the SQL query is in **string**.
	- OUTPUT
		- ![](../attachments/Screenshot%20from%202026-04-05%2019-27-24.png)


- **DELETE API Call**
	- ![](../attachments/Screenshot%20from%202026-04-05%2020-19-33.png)
	- OUTPUT of API call is same as 204 , no content output
	- ![](../attachments/Screenshot%20from%202026-04-05%2019-50-02.png)


- **PUT API Call**
	- ![](../attachments/Screenshot%20from%202026-04-05%2020-25-38.png)
	- **OUTPUT**
	- ![](../attachments/Screenshot%20from%202026-04-05%2020-27-02.png)

### ORM  - Object Relational Mapper

- Layer of abstraction that sites between the database and code
- We can perform all database operations through traditional python code
- We talk to ORM and the ORM will talk to the Database.
- ![](../attachments/Screenshot%20from%202026-04-06%2014-05-56.png)
- Instead of manually defining tables in Postgres, we can define tables as python models
- ![](../attachments/Screenshot%20from%202026-04-06%2014-46-44.png)
- **SQLalchemy** -> python ORM
-  Standalone library
- psycopg2 ( for Postgres) is a prerequisite for ORM to run
- So the respective database driver needs to be installed for an ORM to work as the database driver ( example psycopg ) is the one that connects with the database.

- #### **ORM DB Connection**
	- We can create a new python file under app directory with the name `database.py`

	- **Default Steps for every DB** 
	- Template -> DB path URL
		- `SQLALCHEMY_DATABSE_URL = 'postgresql://<username>:<password>@<ip-address/hostname>/<database_name>'`

		- **NOTE :  sqlalchemy defaults to the psycopg2 driver, but we have installed latest one which is 3. Hence updated code**
			- `SQLALCHEMY_DATABSE_URL = 'postgresql+psycopg://postgres:admin123@localhost/DesUno'`
			- We added **+psycopg** to indicate SQLalchemy to use the latest current driver installed

	- similar to connecting to a database 
			`conn = psycopg.connect(`
						`host="localhost",`
						`dbname='DesUno',`
						`user='postgres'`
						`password = 'admin123',`
						`row_factory=dict_row`
				`)`
	- Initializing the sqlachemy object 
		- `engine = create_engine(SQLALCHEMY_DATABSE_URL)`
	- Its similar to starting a connection session for your db
		`SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)`
	- Creating Base class which will be used to define the db model
		-`Base = declarative_base()`
	- **NOTE : NEVER HARDCODE Credentials in the Code , Use environment variables**

- #### Models - Schema of your Database
	- create a new file `models.py` to code your models
	- ![](../attachments/Screenshot%20from%202026-04-06%2015-21-31.png)
	
- #### **Changes to the `main.py` file**	
	- Import the database and models file 
	- ![](../attachments/Screenshot%20from%202026-04-06%2015-22-46.png)
	- . mean current directory

	- ![](../attachments/Screenshot%20from%202026-04-06%2015-53-28.png)
		-   This is your Python file (`models.py`) where you defined your database tables using Python classes (like your `Posts` class).
			`Base`
			When you set up SQLAlchemy, you created a `declarative_base()`. Think of `Base` as a giant registry or a tracking system. Every time you create a class like `class Posts(Base):`, SQLAlchemy automatically adds that class to its internal registry.
			`metadata`
			This is a collection inside `Base` that holds all the detailed information about your tables. It knows that the `posts2` table has an `id` column that is an Integer, a `title` column that is a String, etc.
			`create_all(...)`
			This is the command that triggers the action. It tells SQLAlchemy to take all the information stored in `metadata` and translate it into actual SQL commands (specifically, `CREATE TABLE` statements).
			- _Crucial Note:_ `create_all` is smart enough to check if the table already exists. If the `posts2` table is already in your database, it just skips it. It **will not** overwrite or drop your existing data.	
			`bind=engine`
			You are handing SQLAlchemy the `engine` you created earlier. It says, _"When you generate those `CREATE TABLE` commands, use this specific connection (the engine) to send them to my PostgreSQL database."_
	
	- Initialize a function to start and close a db connection
		- ![](../attachments/Screenshot%20from%202026-04-06%2015-23-34.png)

- Testing the **API/DB Call**
	- New get API call
		- ![](../attachments/Screenshot%20from%202026-04-06%2015-36-26.png)
	- Once we save and restart our webserver ( uvicorn ), a new table should be created because of 
		- `models.Base.metadata.create_all(bind=engine)`

**The above steps are default steps to follow**

To clean the code we can move get_db to database file

NOTE : The `models.py` can create table only once , if you need to make any changes it is called **database migration** and is done using **Alembic**. So right now to make any changes to table you need to delete the table and then again run the code.

- ### **GET all posts:**
	- ![](../attachments/Screenshot%20from%202026-04-06%2017-46-52.png)
	- Updated with better error handling
		- ![](../attachments/Screenshot%20from%202026-04-06%2018-14-36.png)
	- **OUTPUT**
	- ![](../attachments/Screenshot%20from%202026-04-06%2017-47-55.png)

- ### POST ORM Call

	- ![](../attachments/Screenshot%20from%202026-04-06%2018-15-40.png)
		- `db.commit()` -> saves changes to the db
		- `db.refresh() `-> saves the updated new_post output .
	- **OUTPUT**
		- ![](../attachments/Screenshot%20from%202026-04-06%2018-18-11.png)
	- Using Unpacking
		- ![](../attachments/Screenshot%20from%202026-04-06%2018-30-00.png)

- ### GET 1 post
	- ![](../attachments/Screenshot%20from%202026-04-06%2018-47-39.png)
	- OUTPUT
		- ![](../attachments/Screenshot%20from%202026-04-06%2018-46-46.png)
