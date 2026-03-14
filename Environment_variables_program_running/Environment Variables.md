Environment variables are **key-value pairs stored in your operating system's environment** that contain configuration data for your applications. They live outside your Python code and can be accessed by any program running on your system.[](https://www.geeksforgeeks.org/python/environment-variables-in-python/)

## What They Store

Environment variables typically contain:[](https://developer.vonage.com/en/blog/python-environment-variables-a-primer)

- **Sensitive information**: API keys, passwords, database credentials

- **Configuration settings**: Debug mode, server ports, file paths

- **System-specific data**: Home directory paths, Python module locations

- **Application behavior settings**: Environment type (development, production)


## Why Use Environment Variables

**Security**: Keep sensitive data like API keys out of your source code. Instead of hardcoding:[](https://www.codecademy.com/article/python-environment-variables)

python

![[Pasted image 20250912003650.png]]

You use:

python

![[Pasted image 20250912003719.png]]

**Configuration Management**: Easily change application behavior without modifying code[](https://dagster.io/blog/python-environment-variables)

**Portability**: Applications can adapt to different environments (development vs production) without code changes[](https://developer.vonage.com/en/blog/python-environment-variables-a-primer)

## How to Access Them in Python

**Using `os.getenv()` (recommended)**:[](https://www.codecademy.com/article/python-environment-variables)

python

![[Pasted image 20250912003733.png]]

**Using `os.environ` (stricter)**:[](https://dagster.io/blog/python-environment-variables)

python

![[Pasted image 20250912003755.png]]

**View all environment variables**:[](https://dagster.io/blog/python-environment-variables)

python

![[Pasted image 20250912003842.png]]


## Common Python Environment Variables

**`PYTHONPATH`**: Adds directories to Python's module search path[](https://www.geeksforgeeks.org/python/environment-variables-in-python/)
**`HOME`**: User's home directory path[](https://dagster.io/blog/python-environment-variables)  
**`DEBUG`**: Controls debug mode in applications[](https://www.codecademy.com/article/python-environment-variables)

Environment variables are essential for secure, configurable Python applications, especially when deploying to different environments or handling sensitive data.