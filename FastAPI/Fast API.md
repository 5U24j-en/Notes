https://fastapi.tiangolo.com/tutorial/first-steps/

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


![](../attachments/Pasted%20image%2020260314190218.png)