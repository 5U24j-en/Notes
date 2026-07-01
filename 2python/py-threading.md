
# Multithreading

##### Sample Program
- Lets have a sample program that sleeps for 1 second and print output
```python
import time

start = time.perf_counter()
def do_something():
    print('Sleeping 1 sound..')
    time.sleep(1)
    print("Done sleeping ... ")
do_something()
finish = time.perf_counter()
print(f"Finished in {round(finish-start,2)} seconds")
```
- OUTPUT
```bash
Sleeping 1 sound..
Done sleeping ... 
Finished in 1.0 seconds
```
- Now lets call the same function twice
```python
import time

start = time.perf_counter()
def do_something():
    print('Sleeping 1 sound..')
    time.sleep(1)
    print("Done sleeping ... ")
do_something()
do_something()
finish = time.perf_counter()
print(f"Finished in {round(finish-start,2)} seconds")
```
- OUTPUT
```bash
Sleeping 1 sound..
Done sleeping ... 
Sleeping 1 sound..
Done sleeping ... 
Finished in 2.0 seconds
```

- **Execution Flow**
- ![](../attachments/Pasted%20image%2020260602181744.png)
- The above flow is called **Synchronous** 

- ### Types of Task

	- **CPU Bound task**
		- Complex processing
	- **Input / Output ( I/O ) tasks**
		- Reading and writing files
		- Network operations
		- Downloading Files
	- Threading is beneficial for Input and Output Tasks
	- If our tasks are CPU bound means lot of Data calculation, threading is not worth it.
		- CPU bound operations need Multi-processing


### Threading

- **Flow**
- ![](../attachments/Pasted%20image%2020260602182257.png)

- **Legacy Threading Way** 
```python
import threading
import time

start = time.perf_counter()

def do_something():
    print('Sleeping 1 sound..')
    time.sleep(1)
    print("Done sleeping ... ")

t1 = threading.Thread(target=do_something) # Thread Objects
t2 = threading.Thread(target=do_something) # Thread Objects

t1.start()
t2.start()

t1.join()
t2.join()

finish = time.perf_counter()

print(f"Finished in {round(finish-start,2)} seconds")
```
- OUTPUT
```bash
Sleeping 1 sound..
Sleeping 1 sound..
Done sleeping ... 
Done sleeping ... 
Finished in 1.0 seconds
```

- ### join()
	- Without join the threads will execute parallel along with the main code 
	- The main code gets executed without waiting for the threads to complete
	- OUTPUT without join()
	- ![](../attachments/Pasted%20image%2020260602183206.png)
	- **Flow with Join**
		- ![](../attachments/Pasted%20image%2020260602183935.png)
	- **Flow with Join**
		- ![](../attachments/Pasted%20image%2020260602184000.png)

#### Creating threads with a function that has arguments
```python
import threading
import time

start = time.perf_counter()

def do_something(seconds):
    print(f"Sleeping {seconds} second(s)...")
    time.sleep(seconds)
    print(f"Done sleeping {seconds}")

threads = []

sleep_times = [5,4,3,2,1]

for sec in sleep_times:
    t = threading.Thread(
        target=do_something,
        args=(sec,)     # tuple containing one argument
    )
    t.start()
    threads.append(t)

for t in threads:
    t.join()

finish = time.perf_counter()

print("All threads completed")
print(f"Finished in {round(finish-start,2)} seconds")
```
- Each thread now has a different Argument
- **Output**
```bash
Sleeping 5 second(s)...
Sleeping 4 second(s)...
Sleeping 3 second(s)...
Sleeping 2 second(s)...
Sleeping 1 second(s)...
Done sleeping 1
Done sleeping 2
Done sleeping 3
Done sleeping 4
Done sleeping 5
All threads completed
Finished in 5.0 seconds
```

## New threading Way

- New Library - ` import concurrent.futures `
- Main Function - ` with concurrent.futures.ThreadPoolExecutor() as executor `

- #### First Case
	- Results are returned in the order they **finish**.
	- If task 1 takes 5 seconds and task 2 takes 1 seconds , the output of task 1 should be printed first and not wait
			- `submit()` + `as_completed()`
	- **USE CASE:**
		- If output order does not matter
```python
import concurrent.futures
import time

start = time.perf_counter()

def do_something(seconds):
    print(f"Sleeping {seconds} second(s)...")
    time.sleep(seconds)
    return f"Done sleeping {seconds}"


with concurrent.futures.ThreadPoolExecutor() as executor:
    seconds = [5,4,3,2,1]
    results = [executor.submit(do_something, sec) for sec in seconds]

    for f in concurrent.futures.as_completed(results):
        print(f.result())

finish = time.perf_counter()

print("All threads completed")
print(f"Finished in {round(finish-start,2)} seconds")
```
**OUTPUT:**
- ![](../attachments/Pasted%20image%2020260602194335.png)

- #### Second Case
	- Returns results in the **same order as the input iterable**.
	- At 1 second: ----> ` Task 1 finished ` **BUT** but `map()` says: ` "Nope, first result should be task 5" `
	- At 5 seconds: ----> Results gets printed almost immediately.
	- **`map()` waits until the first submitted task (5s) completes before yielding its result.**
	- **USE CASE:**
		- When **OUTPUT** Order Matters
```python
import concurrent.futures
import time

start = time.perf_counter()

def do_something(seconds):
    print(f"Sleeping {seconds} second(s)...")
    time.sleep(seconds)
    return f"Done sleeping {seconds}"

with concurrent.futures.ThreadPoolExecutor() as executor:
    seconds = [5,4,3,2,1]
    results = executor.map(do_something, seconds)
    for result in results:
        print(result)

finish = time.perf_counter()

print("All threads completed")
print(f"Finished in {round(finish-start,2)} seconds")
```
- **OUTPUT**
```bash
Sleeping 5 second(s)...
Sleeping 4 second(s)...
Sleeping 3 second(s)...
Sleeping 2 second(s)...
Sleeping 1 second(s)...
Done sleeping 5
Done sleeping 4
Done sleeping 3
Done sleeping 2
Done sleeping 1
All threads completed
Finished in 5.0 seconds
```

**NOTE : The above will create 5 threads**

- #### Limiting the Threads

	- If the total time for your tasks ` x ` time and the threadpool is ` y ` then the total time it will take to complete is ` x/y ` + some other tasks
	- Setting - ThreadPoolExecutor(5)
	- You have total --> 25 tasks
	- Total work
		- (5+4+3+2+1) * 5 = 75 seconds
	- Distributed across 5 workers:
		- 75 / 5 = 15 seconds

	- #### Case Where Output order does not matter - ` submit() + as_completed() `
```python
import concurrent.futures
import time

start = time.perf_counter()

def do_something(seconds):
    print(f"Sleeping {seconds} second(s)...")
    time.sleep(seconds)
    return f"Done sleeping {seconds}"


with concurrent.futures.ThreadPoolExecutor(5) as executor:
    seconds = [5,4,3,2,1,5,4,3,2,1,5,4,3,2,1,5,4,3,2,1,5,4,3,2,1]
    results = [executor.submit(do_something, sec) for sec in seconds]

    for f in concurrent.futures.as_completed(results):
        print(f.result())

finish = time.perf_counter()

print("All threads completed")
print(f"Finished in {round(finish-start,2)} seconds")
```
- OUTPUT
	- **Time taken - 17 seconds**
	- ![](../attachments/Pasted%20image%2020260602201043.png)
- 
	- #### Case where output matters - ` executor.map() `
```python
import concurrent.futures
import time

start = time.perf_counter()

def do_something(seconds):
    print(f"Sleeping {seconds} second(s)...")
    time.sleep(seconds)
    return f"Done sleeping {seconds}"

with concurrent.futures.ThreadPoolExecutor(5) as executor:
    seconds = [5,4,3,2,1,5,4,3,2,1,5,4,3,2,1,5,4,3,2,1,5,4,3,2,1]
    results = executor.map(do_something, seconds)
    for result in results:
        print(result)

finish = time.perf_counter()

print("All threads completed")
print(f"Finished in {round(finish-start,2)} seconds")
```
- **OUTPUT**
	- **Time Taken - 17 seconds**
	- ![](../attachments/Pasted%20image%2020260602201245.png)
