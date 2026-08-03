Jenkins
Day 01: Set Up Jenkins Server.
Day 02: Install Jenkins Plugins.
Day 03: Jenkins Parameterized Builds
Day 04: Jenkins Scheduled Jobs
Day 05: Jenkins Deploy Pipeline
Day 06: Jenkins Conditional Pipeline
Day 07: Jenkins Deployment Job
Day 08: Jenkins Chained Builds
Day 09: Jenkins Multistage Pipeline

Solutions:
Day 01: Set Up Jenkins Server.
Solution:

Step 1: Update the System
#sudo apt update
#sudo apt upgrade -y

Step 2: Install Java
#java -version
#sudo apt install openjdk-17-jdk -y

Verify:
#java -version
o/p-
OpenJDK Runtime Environment (build 21.0.11+10-1-26.04.2-Ubuntu)

Step 3: Install Jenkins

#sudo wget -O /etc/apt/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2026.key

#echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.asc]" \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
#sudo apt update
#sudo apt install jenkins -y

Start Jenkins
#sudo systemctl enable jenkins
#sudo systemctl start jenkins
#sudo systemctl status jenkins


Step 4: Open Port 8080
Go to:
AWS Console → EC2 → Security Groups → Inbound Rules
Add:
Type	Port	Source
Custom TCP	8080	Anywhere (0.0.0.0/0) (For learning only; restrict in production.)

Step 5: Access Jenkins

Open your browser:
http://<EC2-Public-IP>:8080

Example:
http://13.233.xxx.xxx:8080

Q1. Why is Java required for Jenkins?
Answer:
Jenkins is a Java-based application, which means it is developed using the Java programming language. To run Jenkins, the system must have the Java Runtime Environment (JRE) or Java Development Kit (JDK) installed.

