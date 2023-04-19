# Linux Tasks
## Stage I.

1. How to see if the environment variable ORACLE_HOME is set up and what value it has?
    In my laptop:

    ```console

       aram@aram-Lenovo:~$ echo $ORACLE_HOME 

    ```

    outputs nothing

    As I was using Docker during SQL task

    ```console

       aram@aram-Lenovo:~$ docker exec -it oracl_db bash
       bash-4.4$ echo $ORACLE_HOME
       /opt/oracle/product/21c/dbhomeXE/

    ```

2. Set up environment variable TEST_VAR with value "test"
   ```console
    aram@aram-Lenovo:~$ export TEST_VAR="test"
    aram@aram-Lenovo:~$ source .bashrc (to reload the current user's bashrc file in the current shell session)
    ```

3. Name console tools in Linux which could be used for monitoring.
    - free - displays information about system memory usage
    - top - displays real-time information about system processes
    - ps - displays information about running processes

4. Check processes started by user "user" oracle database.
```console 
    aram@aram-Lenovo:~$ ps -u $USER (-u option is used to show processes run by current user)
    aram@aram-Lenovo:~$ ps -u $USER | grep oracle
```
## Stage II. 

Directory "./example/" contains big amount of files of different types.
1. Show all files ending on ".sh". The list should be sort by date of the last modification.
   ```consolse
    aram@aram-Lenovo:~$ ls -lt *.sh 
    with -lt: sort by, and show, ctime (time of last modification of file status information)
    ```

2. Change permission of all ".sh" files, so that they could be executed by the owner of the file.
   ```console 
    aram@aram-Lenovo:~$ find . -type f -name '*.sh' -exec chown $USER  {} \;
    aram@aram-Lenovo:~$ find . -type f -name '*.sh' -exec chmod u+x {} \;
    ```

3. Print all ".cfg" files 
   ```console
    aram@aram-Lenovo:~$ find . -type f -name "*.cfg"
    ```

4. Show last 25 lines of file "./example/test.log"
   ```console
    aram@aram-Lenovo:~$ tail -n 25 ./example/test.log
    ```

5. Show the first 25 lines of file "./example/test.log"
   ```console
    aram@aram-Lenovo:~$ head -n 25 ./example/test.log
    ```

6. Show line number 25.
   ```console
    aram@aram-Lenovo:~$ sed -n '25p' .example/test.log
    ```

7. Move all files which have the pattern "trash" in the filename to /tmp/ directory
   ```console
    aram@aram-Lenovo:~$ mv *trash* /tmp
    ```
8. Copy all files which have the pattern "trash" in the filename to /tmp/ directory
   ```console
    aram@aram-Lenovo:~$ cp *trash* /tmp
    ```

## Stage III 

Directory "./example/" contains a big amount of files of different types and sub-directories with files of different types.
1. Find all files in the directory "./example/" (and all sub-directories) which contains text "test_mark". Print only filenames
   ```console
    aram@aram-Lenovo:~$ grep -rl test_mark ./example/
    ```

2. Find all files in directory "./example/" (and all sub-directories) ending with ".log", which were modified in the last 2 days and have size >10 MB
   ```console
    aram@aram-Lenovo:~$ find ./example/ -name "*.log" -mtime -2 -size +10M
    ```

3. You have file "./example/test.log" which have size > 3GB. What is the fastest way to clean the file content without deleting and recreating the file.
   ```console
    aram@aram-Lenovo:~$ truncate -s 0 ./example/test.log
    aram@aram-Lenovo:~$ echo "" > ./example/test.log  
    ```
    
4. Find all files in directory "./example/" (and all sub-directories) ending with ".log", which are older than 30 days, zip them with the replacement of source files.
   ```console 
   aram@aram-Lenovo:~$ find ./example/ -name "*.log" -mtime +30 -exec gzip -f {} \;
    ```