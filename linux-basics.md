# Linux Command Basics

- [Create a File](#create-a-file)
- [Create a Folder](#create-a-folder)
- [Display Your Current Path](#display-your-current-path)
- [What Commands to Use](#what-commands-to-use)
- [Write Text to a File](#write-text-to-a-file)
- [How to Edit Text in a File](#how-to-edit-text-in-a-file)
- [Copy a File](#copy-a-file)
- [Move a File](#move-a-file)
- [Delete a File](#delete-a-file)
- [Delete a Folder](#delete-a-folder)
- [How to Use curl](#how-to-use-curl)
- [Check Your Memory Usage](#check-your-memory-usage)

## Create a File

Create an empty file:

```bash
touch myfile.txt
````

Create a file with initial text:

```bash
echo "Hello Linux" > myfile.txt
```

Create and type multiple lines (finish with `Ctrl + D`):

```bash
cat > myfile.txt
```

## Create a Folder

Create a single folder:

```bash
mkdir myfolder
```

Create nested folders in one command:

```bash
mkdir -p projects/2025/demo
```

## Display Your Current Path

Show the full path of your current directory:

```bash
pwd
```

Example output:

```text
/home/username/projects
```

## What Commands to Use

Some core commands you will use all the time:

```bash
pwd       # show current path
ls        # list files and folders
ls -l     # detailed list
ls -a     # show hidden files
cd NAME   # change directory
cd ..     # go up one level
clear     # clear the terminal screen
man ls    # open manual for 'ls'
ls --help # quick help for 'ls'
```

## Write Text to a File

Overwrite a file with new text:

```bash
echo "First line" > notes.txt
```

Append text to the end of a file:

```bash
echo "Another line" >> notes.txt
```

Create a file and type many lines (end with `Ctrl + D`):

```bash
cat > notes.txt
This is line 1
This is line 2
Ctrl + D to save and exit
```

## How to Edit Text in a File

Use a simple terminal editor like `nano`:

```bash
nano notes.txt
```

Basic nano shortcuts:

* Edit text normally with keyboard
* `Ctrl + O` → save (Write Out)
* `Enter`    → confirm file name
* `Ctrl + X` → exit

(If `nano` is not installed, you can ask your system admin to install it or use another editor like `vim`.)

## Copy a File

Copy a file to a new file name:

```bash
cp source.txt backup.txt
```

Copy a file into a folder:

```bash
cp notes.txt backup/notes.txt
```

Copy a folder and all its contents:

```bash
cp -r myfolder myfolder-backup
```

## Move a File

Move a file into another folder:

```bash
mv notes.txt backup/
```

Rename a file (same folder):

```bash
mv oldname.txt newname.txt
```

Move and rename at the same time:

```bash
mv notes.txt archive/notes-2025.txt
```

## Delete a File

Delete a single file:

```bash
rm notes.txt
```

Ask for confirmation before deleting:

```bash
rm -i notes.txt
```

Delete multiple files:

```bash
rm file1.txt file2.txt
```

⚠️ **Warning:** `rm` does not go to a recycle bin. Be careful.

## Delete a Folder

Delete an empty folder:

```bash
rmdir emptyfolder
```

Delete a folder and everything inside it:

```bash
rm -r myfolder
```

Ask for confirmation on each file and folder:

```bash
rm -ri myfolder
```

⚠️ **Warning:** `rm -r` is dangerous. Double-check the folder name before running it.

## How to Use curl

Basic `curl` usage to show a webpage in the terminal:

```bash
curl https://example.com
```

Download a file with its original name:

```bash
curl -O https://example.com/file.zip
```

Download and save as a custom name:

```bash
curl -o myfile.zip https://example.com/file.zip
```

Call an API endpoint (GET request):

```bash
curl -X GET https://api.example.com/status
```

Send data with a POST request:

```bash
curl -X POST https://api.example.com/login \
  -H "Content-Type: application/json" \
  -d '{"username": "user", "password": "secret"}'
```

## Check Your Memory Usage

Show memory usage in a human-readable format:

```bash
free -h
```

Example output:

```text
              total        used        free      shared  buff/cache   available
Mem:           15Gi       4.0Gi       6.5Gi       512Mi       4.5Gi        10Gi
Swap:         2.0Gi          0B       2.0Gi
```

You can also watch live system usage with:

```bash
top
```

(Press `q` to exit `top`.)
