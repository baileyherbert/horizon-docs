@title Scripting

# Introduction

Horizon's update library has implemented a simple scripting language designed to help guide the installation of
updates. This allows you to not only update files, but make changes to the database, run code, delete files, and even
run tests before installing.

---

# Syntax

Here is an example upgrade script.

```bash
# Loads the payload archive
stage 'payload'

# Runs a script inside the archive
execute 'tests/compatibility.php'

# Makes sure the previous execution didn't throw any exceptions
executed ok

# Runs a drop query
query 'DROP TABLE some_old_table'

# Runs a prepared query with a script variable
query 'UPDATE settings SET app_version = ?' '$version'

# Selects all files inside the archive's /changes directory
target '/changes'

# Extracts the selected files
extract

# Creates a directory if it doesn't exist
mkdir '/app/vendor'

# Creates an empty file if it doesn't exist
touch '/app/vendor/autoload.php'

# Removes a file
rm '/app/tests/ObsoleteTest.php'

# Recursively removes a directory and its files
rmdir '/app/tests/old/'
```

---

# Commands

## Stage

Tells the utility which archive to use for extraction. Possible values are:

|Value|Description|
|---|---|
|`payload`|Contains update files|
|`backup`|Contains copies of files changed in upgrade|

## Target

Narrows down an archive to a directory from which files will be extracted. Aliases include `scope` and `select`.

```bash
target '/some/dir'
```

## Extract

Extracts all files from the archive under the current [target](#target). There are two extraction commands.

- `extract` will overwrite existing files
- `gextract` will skip existing files (graceful)

## Execute

Looks for the specified script inside the archive under the current [target](#target), extracts it into the mounted
directory, executes it, and then removes it. If this file errors or throws an exception, it will be caught and ignored.

## ExecuteMatch

Also known as `executed`, this command checks the output of the previous execution and halts the update if it doesn't
match. You can also pass `ok` to only check if the execution threw an error or exception.

```bash
# Fails if the previous execution threw an exception
executed ok

# Fail if the previous execution didn't return the same value
# Note: These also fail if the execution threw an exception
executed 6
executed true
executed 'hello world'
```

## Query

Runs a query on the database, and throws an exception if it fails for any reason. Passing multiple parameters
will turn it into a prepared statement. To ignore errors, prefix it with `@`.

```bash
query 'SELECT * FROM users'
query 'SELECT * FROM users WHERE id = ? AND username = ?' 15502 'Bailey'
@query 'This will error but it will be ignored'
```

## File operations

These make changes to the filesystem.

```bash
# Create a directory (recursive)
mkdir '/some/dir'
mkdir '/some/dir' 0777

# Create a blank file
touch '/some/file'

# Change a file or directory's chmod
chmod '/some/file' 0777

# Remove files or directories (recursive)
rm '/some/file'
rmdir '/some/dir'
```

## File verifications

These test the filesystem to make sure things are proper. They'll throw an exception if not.

```bash
# Ensure a file or directory exists
vexists '/some/file'

# Ensure a file or directory does *not* exist
vmissing '/some/file'

# Ensure a file or directory's chmod equals a specific value
vchmod '/some/file' 0777
```

---

# Exceptions

If any commands in your script throw an exception, the update is halted. To prevent this, you may prefix the command
name with an `@` character to silence any exceptions. They still occur, but don't cause the update to fail.

```bash
# This throws an exception but doesn't halt the update
@query 'SELECT * FROM table_doesnt_exist'
```