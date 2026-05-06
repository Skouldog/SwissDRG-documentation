# Docker

---

### Short Introduction:

Docker containerizes applications, allowing them to run consistently across different environments.
Each Container is isolated from the others and has its own filesystem.

-What actually runs inside of a container is specified in a Dockerfile.
-This serves as a Blueprint for the container.

Upon starting he container, Docker then reads what tools, should be included and builds a new image.
This image will then be run as a new container/environement to run your code.

The Idea is: If everyone is sharing the same Dockerfile/Instructions, they will all have the same environment 
to run their code in.

---

### Steps to install Docker:
Installing Docker:
--> Usual Case: Docker Desktop

Making sure the IDE is linked to your installed Docker software.
--> enable Plugins

---

## The Dockerfile:

The dockerfile is structured in layers.
It tells Docker what to install and how do construct the image to run the container with.

!!️ **Note:** The Dockerfile is not a part of the image itself.

>First Layer: _Base Image / Base OS_ 
>
>Second Layer: _Installs the required tools_
>
>Third Layer: _Copies the code in the src folder to the container_
>
>Fourth Layer: _Runs the application_

The idea is that if only the code changes, the first two layers can stay the same.
This makes it easier to update the image.

> Example Dockerfile:
> 
> ```
> 
> ```


### Important Commands:


> ```docker ps``` : _Lists all currently running containers_
> 
> ```docker build -t image_name .``` : _Builds a new image with the specified name_
>
> ```docker run -p 8080:8080 image_name``` : _Runs the container with the specified port mapping_
> 
>_" -p 8080:8080 " : this maps the localhost port 8080 to the 
> container port 8080 (withou this, the specified port is not accesible locally)_
>





>Docker containerizes applications, allowing them to run consistently across different environments.
>Each Container is isolated from the others and has its own filesystem.
>
>What actually runs inside of a container is specified in a Dockerfile.
>This serves as a Blueprint for the container.
>
>The Idea is: If everyone is sharing the same Dockerfile/Instructions, they will all have the same environment
>to run their code in.