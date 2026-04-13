# Day 12 — First SAST Scan with Semgrep

## Lab Setup
-- Ran `docker pull webgoat/webgoat` — result: pulled the image and downloaded it 
- Ran `docker run` command — result: docker run -d -p 8080:8080 -p 9090:9090 --name webgoat webgoat/
    db71576ee3d6ec18b50e0cc9b1c60cc52b5eca55dbfbb3d33f380458d7845bd4
- Ran `docker ps` — result: gives the results of image name and container and all 
- Opened browser at localhost:8080/WebGoat — result: yes 
- Docker version:docker --version
Docker version 28.5.2+dfsg3, build 9cc6dea35e9a963f281434761c656fba4ac43aed                                                         
- WebGoat running at: localhost:8080
- Target app scanned: Vulnerable-Flask-App (OWASP)

## Scan Command
semgrep --config=p/owasp-top-ten .
Rules run: 244 | Files scanned: 18 | Findings: 7

## Most Critical Finding
**SQL Injection — python.flask.security.injection.tainted-sql-string**
- File: app.py
- Line 261
- Vulnerable code: str_query = "SELECT first_name, last_name, username FROM customer WHERE username = '%s';" % search_term
- Why dangerous: it direclty parse the userinput and there is no parameterization in this and might cause sql injection if this is the case 
- Fix: parameterized queries — separate data from query structure

## Other Findings
1. 2 were related to docker user if you do not set the user in the docker image and if the user is able to breakout of the image then it might have a root permission and its bad
2. it wasd realted to the unfiltered or un parametereised input that was directly comming orm user and getting rendered in the html page 
3. it was related to md5 hash as it is a easy to crack and its raninbow table is been leaked so its bad 
## What I learned today
to add a user in the docker it seems to the group
sudo usermod -aG docker $USER
also learned about sql injection how if its not parametereised like "# SAFE — parameterized query
str_query = "SELECT first_name, last_name, username FROM customer WHERE username = %s;"
cursor.execute(str_query, (search_term,))" its safe 
i also learned if there is a image by same name and i want to start it again i can just type 
docker start webgoat 
## What I learned today
- Running a container as root is dangerous because if an attacker escapes 
  the container, they get root on the host. Fix: add USER in the Dockerfile.
- SQL injection happens when user input is inserted directly into a query string.
  Fix: use parameterized queries so the DB engine separates data from logic.
- To restart an existing container: `docker start webgoat`
## What confused me
i was not aware with the restarting command because i was not able to download the image 
sudo usermod -aG docker $USER
this is to add a new user it seems to the docker
how to start the docker when my kali got closed 

# Day 12 — Lab Setup

## What I did
- Ran `docker pull webgoat/webgoat` — result: pulled the image and downloaded it 
- Ran `docker run` command — result: docker run -d -p 8080:8080 -p 9090:9090 --name webgoat webgoat/webgoat
db71576ee3d6ec18b50e0cc9b1c60cc52b5eca55dbfbb3d33f380458d7845bd4

- Ran `docker ps` — result: gives the results of image name and container and all 
- Opened browser at localhost:8080/WebGoat — result: yes 

## What confused me
i was not aware with the restarting command because i was not able to download the image 
sudo usermod -aG docker $USER
this is to add a new user it seems to the docker

## One thing I learned today
to add a user in the docker it seems to the group 


