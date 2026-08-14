# 🚀 Day 08: Create a Docker Network

Learn how to create a **custom Docker network** and enable communication between multiple Docker containers using Docker's built-in networking and DNS capabilities.

---

# 🎯 Objective

Understand how Docker networking works and learn how to:

- Create a custom Docker network
- List and inspect Docker networks
- Connect containers to a custom network
- Enable communication between containers
- Communicate using container names
- Disconnect and reconnect containers
- Understand Docker's built-in DNS resolution

---

# 🌍 Scenario

In real-world DevOps projects, applications are often divided into multiple containers.

For example:

- Frontend Container
- Backend Container
- Database Container

These containers need to communicate with each other.

Docker Networks provide an isolated networking environment that allows containers to communicate securely without requiring every container to expose its ports directly to the host.

A typical architecture may look like:

```text
Frontend
    │
    ▼
Backend
    │
    ▼
Database

All three containers can communicate through a dedicated Docker network.

🛠️ Prerequisites
Docker installed
Basic understanding of Docker Images and Containers
Ubuntu image available locally
Basic Linux command knowledge
📁 Step 1: View Existing Docker Networks

Run:

docker network ls

Example output:

NETWORK ID     NAME      DRIVER    SCOPE
xxxxxxxx       bridge    bridge    local
xxxxxxxx       host      host      local
xxxxxxxx       none      null      local
Docker Default Networks
Network	Driver	Purpose
bridge	bridge	Default network for containers
host	host	Container shares the host network
none	null	Disables networking
🌐 Step 2: Create a Custom Docker Network

Create a custom network named:

devops-network

Run:

docker network create devops-network

Verify:

docker network ls

Example output:

NETWORK ID     NAME             DRIVER    SCOPE
xxxxxxxx       bridge           bridge    local
xxxxxxxx       host             host      local
xxxxxxxx       none             null      local
xxxxxxxx       devops-network   bridge    local

Docker creates the custom network using the bridge driver by default.

🔍 Step 3: Inspect the Docker Network

Run:

docker network inspect devops-network

Initially, no containers are connected.

You should see something similar to:

{
    "Name": "devops-network",
    "Driver": "bridge",
    "Containers": {}
}

The following section:

"Containers": {}

means that no containers are currently attached to the network.

📦 Step 4: Create the First Container

Create a container named:

container1

and attach it directly to devops-network.

Run:

docker run -dit --name container1 --network devops-network ubuntu

Verify:

docker ps

Expected output should show:

container1
📦 Step 5: Create the Second Container

Create another container named:

container2

Run:

docker run -dit --name container2 --network devops-network ubuntu

Verify:

docker ps

You should now see:

container1
container2

Both containers are connected to:

devops-network
🔍 Step 6: Verify Connected Containers

Inspect the network again:

docker network inspect devops-network

This time, the network configuration should contain both containers.

Example:

Containers:

container1
container2

This confirms that both containers are attached to the same Docker network.

🔗 Step 7: Test Container-to-Container Communication

Open an interactive shell inside container1:

docker exec -it container1 bash

You are now inside the container.

Update the package repository:

apt update

Install the ping utility:

apt install -y iputils-ping

Now ping container2 using its container name:

ping container2

Expected output:

PING container2 (172.xx.xx.xx)

64 bytes from container2...
64 bytes from container2...
64 bytes from container2...

Press:

Ctrl + C

to stop the ping.

Exit the container:

exit
What did we prove?

We proved that:

container1
     │
     │
     ▼
devops-network
     │
     │
     ▼
container2

can communicate with each other.

More importantly, we used:

container2

instead of a hard-coded IP address.

Docker provides automatic DNS resolution between containers connected to the same user-defined network.

🔌 Step 8: Disconnect a Container

Disconnect container2 from the network:

docker network disconnect devops-network container2

Verify:

docker network inspect devops-network

Now only container1 should appear under the connected containers.

🔗 Step 9: Connect the Container Again

Reconnect container2:

docker network connect devops-network container2

Verify:

docker network inspect devops-network

Both containers should appear again.

🔄 Docker Network Workflow
                    Docker Network
                          │
             ┌────────────┴────────────┐
             │                         │
             ▼                         ▼
       Container 1               Container 2
             │                         │
             └────────────┬────────────┘
                          │
                          ▼
                Container Communication
                          │
                          ▼
                  Docker DNS / Name
🌍 Real-World Project Example

Consider a typical three-tier application.

Application Components
Frontend
React / Angular
      │
      ▼
Backend
Spring Boot
      │
      ▼
Database
MySQL

Each component can run inside its own container.

For example:

frontend-container
backend-container
mysql-db

All containers can be connected to:

devops-network

The backend can communicate with the database using:

mysql-db

instead of:

172.18.0.4
💡 Why Use Container Names Instead of IP Addresses?

Container IP addresses can change when containers are recreated.

For example:

172.18.0.4

might become:

172.18.0.7

after the container is recreated.

Hard-coding the IP address would therefore make the application unreliable.

Instead, use the container or service name:

mysql-db

Docker's internal DNS resolves the name to the current container IP address.

Therefore:

Application
     │
     ▼
mysql-db
     │
     ▼
Docker DNS
     │
     ▼
Current Container IP

This makes container communication more reliable and easier to manage.

🧰 Common Docker Network Commands
List Networks
docker network ls
Create a Network
docker network create my-network
Inspect a Network
docker network inspect my-network
Connect a Container
docker network connect my-network container1
Disconnect a Container
docker network disconnect my-network container1
Remove a Network
docker network rm my-network
🔍 Docker Network Drivers

Docker provides multiple network drivers.

Driver	Purpose
bridge	Container networking on a single Docker host
host	Container shares the host's network
none	Disables networking
overlay	Enables networking across multiple Docker hosts
macvlan	Gives containers their own MAC address on the network

For this lab, we use:

bridge
🆚 Default Bridge vs User-Defined Bridge
Feature	Default Bridge	User-Defined Bridge
Container communication	✅	✅
Automatic DNS by container name	Limited	✅
Custom configuration	Limited	✅
Network isolation	Basic	Better control
Recommended for applications	❌	✅

For multi-container applications, user-defined bridge networks are generally preferred.

🏗️ DevOps Perspective

Docker networking becomes especially important when applications are divided into multiple services.

A simplified architecture:

                         Docker Host
                              │
                       devops-network
                              │
              ┌───────────────┼───────────────┐
              │               │               │
              ▼               ▼               ▼
          Frontend         Backend         Database
          Container        Container        Container
              │               │               │
              └───────────────┼───────────────┘
                              │
                    Internal Communication

The goal is to allow services that need to communicate with each other to do so through a controlled network.

✅ Expected Outcome

After completing this lab:

Custom Docker network created successfully
Multiple containers attached to the network
Container-to-container communication verified
Containers communicated using container names
Docker DNS resolution understood
Container successfully disconnected from the network
Container successfully reconnected to the network
Docker network commands practiced
🎯 Interview Questions
Q1. What is a Docker Network?

Answer:

A Docker Network provides networking capabilities that allow containers to communicate with each other, the host, and external systems while providing network isolation.

Q2. Why do we create a custom Docker network?

Answer:

A custom Docker network provides better control over container communication and enables automatic DNS-based service discovery using container names.

Q3. What is the default network driver commonly used for containers?

Answer:

The commonly used default network driver is:

bridge
Q4. How do containers communicate within the same user-defined network?

Answer:

Containers can communicate using their container names because Docker provides built-in DNS resolution on user-defined networks.

Example:

ping container2
Q5. Which command displays all Docker networks?

Answer:

docker network ls
Q6. How do you inspect a Docker network?

Answer:

docker network inspect <network-name>

Example:

docker network inspect devops-network
Q7. How do you connect an existing container to a Docker network?

Answer:

docker network connect <network-name> <container-name>

Example:

docker network connect devops-network container2
Q8. How do you disconnect a container from a Docker network?

Answer:

docker network disconnect <network-name> <container-name>

Example:

docker network disconnect devops-network container2
Q9. Why should applications avoid hard-coded container IP addresses?

Answer:

Container IP addresses can change when containers are recreated.

Using container or service names provides more reliable service discovery through Docker's DNS mechanism.

📌 Key Takeaways
Docker Networks enable communication between containers.
User-defined bridge networks provide DNS-based service discovery.
Containers on the same network can communicate using container names.
Container IP addresses should generally not be hard-coded.
docker network inspect is useful for troubleshooting network connectivity.
Docker networking is fundamental for multi-container applications.
Understanding Docker networking provides a strong foundation for Docker Compose and Kubernetes networking.
🎉 Conclusion

Today, I learned how to create a custom Docker network and connect multiple containers to it.

I also verified container-to-container communication using container names and explored Docker's built-in DNS resolution.

This hands-on exercise helped me understand an important DevOps concept:

Reliable container communication should be designed around networks and service names rather than hard-coded IP addresses.
