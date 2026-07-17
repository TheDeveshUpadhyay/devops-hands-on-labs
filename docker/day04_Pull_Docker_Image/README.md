Day 04: Pull Docker Image
Solution:

Step 1: Search Ubuntu Image
#docker search ubuntu
________________________________________

Step 2: Pull Ubuntu Image
#docker pull ubuntu

Expected output:
Using default tag: latest
latest: Pulling from library/ubuntu
...
Status: Downloaded newer image for ubuntu:latest
________________________________________

Step 3: Verify
#docker images

 
________________________________________
Step 4: Pull a Specific Version
#docker pull ubuntu:22.04
________________________________________
Step 5: Verify Again
#docker images
 
________________________________________
Step 7: Inspect an Image
#docker image inspect ubuntu
•	This displays detailed information such as:
•	Image ID 
•	Architecture 
•	Operating System 
•	Environment Variables 
•	Layers 
•	Creation Time 
________________________________________
Step 8: View Image History
#docker history ubuntu
This shows the layers that make up the image.
________________________________________
Step 9: Remove an Image
Remove the Ubuntu image.
#docker rmi ubuntu
If the image is being used by a container, Docker returns an error.
________________________________________
Step 10: Remove Unused Images
#docker image prune
To remove all unused images:
#docker image prune -a

#docker image prune         vs     #docker image prune -a
#docker image prune
It removes only dangling images.

What is dangling images?
#docker images
Output:
REPOSITORY     TAG         IMAGE ID
nginx                latest         a12345
ubuntu             latest         b67890
<none>            <none>      c98765

Notice this:
<none>         <none>
This is called a dangling image.
These images are usually created when you:
•	rebuild a Docker image, 
•	build an image with the same tag, 
•	or an image build is interrupted. 

They consume disk space but are not useful anymore.


Think of it like this
Imagine your room:
Room

📚 Book
💻 Laptop
🗑️ Empty cardboard box

Running:
docker image prune
is like throwing away only the empty cardboard box.
You keep the useful items.

docker image prune -a
This removes:
•	Stopped containers 
•	Unused networks 
•	Dangling images 
•	Unused images 
•	Build cache

Unused means:
No running container uses the image. 
No stopped container references the image. 

Example:
Before:
nginx:latest
ubuntu:22.04
mysql:8.0
redis:latest

Suppose:
nginx → used by a container ✅ 
ubuntu → not used ❌ 
mysql → not used ❌ 
redis → not used ❌

Quick Memory Trick
•	docker image prune → P = Partial cleanup (dangling images only). 
•	docker image prune -a → A = All unused images.



