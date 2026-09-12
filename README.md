GIT + MAVEN + DOCKER + TOMCAT — LAB PROCEDURE
================================================

IMPORTANT https://github.com/LightYagami-glitch/cicd/blob/main/README.md
---------
The WAR procedure below follows the uploaded SE Lab instructions document.
The JAR procedure is added separately as a standard Java/Maven Docker procedure
because the document does not provide a proper JAR Dockerfile.

================================================
1. MAVEN PROJECT — FROM THE LAB DOCUMENT
================================================

1. Open Eclipse IDE (preferably Eclipse IDE for Enterprise Java Developers).

2. Import/clone the GitHub repository:
   File -> Import -> Git -> Projects from Git (with smart import) -> Next
   Choose Clone URI -> Next
   Enter the GitHub repository URL.
   Select the branch (usually main/master).
   Select the directory.
   Finish.

3. Check pom.xml:
   - Verify pom.xml is correct.
   - Resolve Maven dependencies using pom.xml.
   - Understand .m2/repository, JUnit, test, build, finalName, etc.

4. Build the Maven project to generate the WAR/JAR.

5. Verify the generated artifact inside:
   target/

================================================
2. GIT PROCEDURE — FROM THE LAB DOCUMENT
================================================

Open Git Bash in Eclipse:
Right-click Maven project -> Show in Local Terminal -> Git Bash
(or use PowerShell/Git Bash).

Run:

git init
git add .
git commit -m "first commit"

Set Git username and email:

git config --global user.name "your user name"
git config --global user.email "your email id"

Solve the required Git SQBs:
- branch
- merge
- revert
- stash
- restore
- merge conflict
- gitignore
- clone

Push the Maven project to GitHub.

1. Create a GitHub repository for the Maven project.

2. Before pushing, check Windows Credentials and remove old GitHub
   accounts/logins if necessary.

3. Rename the branch:
git branch -M main

4. Add the remote:
git remote add origin1 https://github.com/YOURGITHUBUSERNAME/LMSWEBP.git

5. Push:
git push -u origin1 main

If access is denied because origin points to another owner's repository,
use origin1 (or another remote name) and clear old GitHub credentials
from Windows Credentials.

Alternative:
git remote set-url origin https://github.com/YOURGITHUBUSERNAME/LMSWEBP.git

Refresh GitHub and verify that the project files exist.

================================================
3. DOCKER BASIC COMMANDS — FROM THE LAB DOCUMENT
================================================

Open Docker Desktop and log in to Docker Hub.

Check Docker:
docker --version

List containers:
docker ps -a

List local images:
docker image ls

Login:
docker login

Other SQBs include:
- pull an image locally
- run a container
- list containers
- stop a container
- start a container
- run in interactive mode
- build an image
- run on localhost using a given port

================================================
4. WAR FILE — EXACT DOCKER PROCEDURE FROM THE DOCUMENT
================================================

STEP 1 — BUILD THE WAR
----------------------

Build the Maven project.

Typical Maven command:
mvn clean package

Verify the WAR exists in:
target/

Example:
target/LMSWEBP.war

The lab document then uses a wildcard:
target/*.war

STEP 2 — CREATE Dockerfile
--------------------------

Create a file named exactly:
Dockerfile

The Dockerfile shown in the lab document is:

FROM tomcat:9.0
COPY target/*.war /usr/local/tomcat/webapps/ROOT.war
EXPOSE 7089
CMD ["catalina.sh","run"]

NOTE:
Tomcat itself normally listens on container port 8080.
EXPOSE 7089 does NOT change Tomcat's listening port.

STEP 3 — BUILD THE IMAGE
------------------------

From the Maven project's directory (the directory containing Dockerfile):

docker build -t lmsimage .

For Docker Hub tagging directly:

docker build -t YOURDOCKERHUBUSERNAME/lmsimage:latest .

STEP 4 — RUN THE WAR CONTAINER
------------------------------

Use:

docker run -d -p 7089:8080 --name lmcontainer lmsimage

Meaning:
-d              = detached/background mode
-p 7089:8080    = host port 7089 -> container/Tomcat port 8080
--name          = container name
lmsimage        = image to run

STEP 5 — OPEN THE APPLICATION
-----------------------------

Open:

http://localhost:7089/

Because the WAR is copied as ROOT.war, Tomcat deploys it as the root
web application.

STEP 6 — TAG THE IMAGE
----------------------

docker tag lmsimage YOURDOCKERHUBUSERNAME/lmsimage:latest

STEP 7 — PUSH TO DOCKER HUB
---------------------------

docker push YOURDOCKERHUBUSERNAME/lmsimage:latest

Then open Docker Hub and verify the lmsimage repository.

================================================
5. JAR FILE — STANDARD PROCEDURE
================================================

The uploaded lab document does not provide a proper JAR Dockerfile.
For a normal Maven Spring Boot/executable Java JAR, use the following.

STEP 1 — BUILD THE JAR
----------------------

From the Maven project directory:

mvn clean package

Verify:

target/

Example:
target/myapp-0.0.1-SNAPSHOT.jar

STEP 2 — CREATE Dockerfile
--------------------------

Create:

Dockerfile

For a standard executable JAR:

FROM eclipse-temurin:17-jre
WORKDIR /app
COPY target/*.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java","-jar","app.jar"]

IMPORTANT:
Use the Java version required by the project.
For Java 8, use an appropriate Java 8 runtime image.
For Java 11, use an appropriate Java 11 runtime image.
For Java 17, the Dockerfile above is suitable.

If the application uses port 8080, EXPOSE 8080 and map it as below.

STEP 3 — BUILD THE JAR IMAGE
----------------------------

docker build -t jarapp .

STEP 4 — RUN THE JAR CONTAINER
------------------------------

docker run -d -p 8080:8080 --name jarcontainer jarapp

Meaning:
host 8080 -> container 8080

If you want localhost port 7089:

docker run -d -p 7089:8080 --name jarcontainer jarapp

Then open:

http://localhost:7089/

STEP 5 — TAG FOR DOCKER HUB
---------------------------

docker tag jarapp YOURDOCKERHUBUSERNAME/jarapp:latest

STEP 6 — PUSH
-------------

docker push YOURDOCKERHUBUSERNAME/jarapp:latest

================================================
6. JAR VS WAR — WHICH DOCKERFILE?
================================================

JAR:
-----
FROM eclipse-temurin:17-jre
WORKDIR /app
COPY target/*.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java","-jar","app.jar"]

The Java process itself runs the application.

WAR:
-----
FROM tomcat:9.0
COPY target/*.war /usr/local/tomcat/webapps/ROOT.war
EXPOSE 7089
CMD ["catalina.sh","run"]

Tomcat runs the WAR.

================================================
7. DOCKER IMAGE / CONTAINER COMMANDS
   FROM THE LAB DOCUMENT
================================================

Run an image:
docker run <image-name>

Example:
docker run ubuntu

Run a container interactively:
docker run -it <container-id-or-name> bash

Stop a container:
docker stop <container-id-or-name>

Example:
docker stop 96724fc65bd8

Remove a container:
docker rm <container-id-or-name>

Example:
docker rm 96724fc65bd8

List containers:
docker ps -a

List images:
docker image ls

Pull an image:
docker pull <image-name>

Login:
docker login

Push:
docker push <image-name>

================================================
8. COMPLETE WAR LAB FLOW — MEMORIZE THIS ORDER
================================================

1. Open Eclipse.
2. Import/clone Maven project.
3. Check pom.xml and dependencies.
4. Build project:
   mvn clean package
5. Confirm WAR in target/.
6. Open terminal/Git Bash in project directory.
7. Create Dockerfile.
8. Put in:
   FROM tomcat:9.0
   COPY target/*.war /usr/local/tomcat/webapps/ROOT.war
   EXPOSE 7089
   CMD ["catalina.sh","run"]
9. Start Docker Desktop.
10. Check:
    docker ps -a
    docker image ls
11. Login:
    docker login
12. Build:
    docker build -t lmsimage .
13. Run:
    docker run -d -p 7089:8080 --name lmcontainer lmsimage
14. Test:
    http://localhost:7089/
15. Tag:
    docker tag lmsimage YOURDOCKERHUBUSERNAME/lmsimage:latest
16. Push:
    docker push YOURDOCKERHUBUSERNAME/lmsimage:latest
17. Verify the image in Docker Hub.

================================================
9. COMPLETE JAR LAB FLOW
================================================

1. Open Eclipse.
2. Import/clone Maven project.
3. Check pom.xml and dependencies.
4. Build:
   mvn clean package
5. Confirm JAR in target/.
6. Create Dockerfile.
7. Use:
   FROM eclipse-temurin:17-jre
   WORKDIR /app
   COPY target/*.jar app.jar
   EXPOSE 8080
   ENTRYPOINT ["java","-jar","app.jar"]
8. Build:
   docker build -t jarapp .
9. Run:
   docker run -d -p 7089:8080 --name jarcontainer jarapp
10. Test:
    http://localhost:7089/
11. Tag:
    docker tag jarapp YOURDOCKERHUBUSERNAME/jarapp:latest
12. Push:
    docker push YOURDOCKERHUBUSERNAME/jarapp:latest

================================================
10. IMPORTANT PORT POINT
================================================

WAR:
Tomcat listens on 8080 inside the container.

This:
docker run -d -p 7089:8080 ...

means:
localhost:7089 -> container:8080

The Dockerfile's:
EXPOSE 7089

does NOT make Tomcat listen on 7089.

================================================
END
================================================
