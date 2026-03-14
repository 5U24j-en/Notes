## The Directory Structure Issue

Here's why this is a common misconception. Your file structure looks like this:


![[Pasted image 20250911231426.png]]

## What Happens Step by Step

1. After `os.chdir("/home/diablo/")`, your current working directory becomes `/home/diablo/`
    
2. When you use `open("text.txt", "r")`, Python looks for `text.txt` **directly** in `/home/diablo/`
    
3. But your file is actually in `/home/diablo/Documents/text.txt` (one level deeper)


## The Key Point

Just because Documents is a **subfolder** of diablo doesn't mean Python can find files inside Documents when looking in the diablo directory. **Python only looks in the exact current directory**, not in subdirectories.

## What Would Work

From `/home/diablo/`, you need to specify the path to the Documents folder:

python

![[Pasted image 20250911231400.png]]


## The Correct Mental Model

Think of it like this:

- `"text.txt"` means "look for text.txt in the **current** directory"
    
- `"Documents/text.txt"` means "look in the Documents subdirectory of the current directory"


So while Documents is inside diablo, you still need to explicitly tell Python to look **inside** the Documents folder by including it in your path.