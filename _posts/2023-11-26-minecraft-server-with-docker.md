---
title: Creating a Vanilla Minecraft Server With Docker
categories: [gaming, docker]
tags: [minecraft, java]
---

## Minecraft Servers

If you've ever tried to start a Minecraft server for your friends, you've probably quickly come to realize that there are really only two options:

* Pay to have an official Minecraft Server
* Host it yourself (whether it be on your own computer at home or using a cloud provider)

There are pros an cons to both.

### Official Servers: Pros and Cons

The main drawback of official servers boils down to the lack of control. When you self-host, you can do whatever you want. However, the biggest reason to pay for an official server is the ease of use. No need to worry about storage, port-forwarding, vpn's, or how much [dedotated wam](https://www.youtube.com/watch?v=_pVNvSuA2mM) to allocate.

| Pros | Cons |
| ---- | ---- |
| - Easy to set up<br>- Official support and updates<br>- No need to worry about hardware or network issues | - Recurring cost<br>- Limited customization |

### Self-Hosting: Pros and Cons

Self-hosting give you complete control of your Minecraft server. You can install whatever mods you want and shutdown or startup the server.

Starting up or shutting down the server on demand really comes in handy when your friend group randomly gets the urge to play Minecraft, plays it for two weeks and then forgets about it. If that happens to you when you pay for a dedicated server, you would have paid for a whole month of service and then only used two weeks, leaving you with ~2 weeks of unused paid server time. With a self-hosted server however, you can just shutdown the server when your group gets tired of the game, and then turn it back on a few months later when people remember that the game exists.

| Pros | Cons |
| ---- | ---- |
| - Complete control over server settings and mods<br>- Shutdown/Startup on demand | - Requires technical knowledge<br>- Need to manage hardware and network<br>- Potential performance and stability issues |

## Running a Minecraft Server With Docker

### Creating the Dockerfile

```docker
# Install a compatible version of java, in this case java 17
FROM eclipse-temurin:17-jdk-alpine

# Install screen to allow the server to run in the background
RUN apk add screen

# Add a user to run the server as
RUN adduser -D mcserver 
USER mcserver

# Create a directory to store the server files
WORKDIR /home/mcserver
VOLUME /home/mcserver

# Copy over the server .jar file
COPY minecraft_server.1.20.1.jar ./

# Run the server once to generate the eula.txt file
RUN java -Xmx1024M -Xms1024M -jar minecraft_server.1.20.1.jar nogui

# Use sed to change the eula.txt file to true, thereby agreeing to the eula
# without having to exec into the container and manually edit the file
RUN sed -i 's/=false/=true/' eula.txt

# Expose the port that the server runs on
EXPOSE 25565

# Run the server in a screen session with the name minecraft_server
# Set the memory allocation to 1024 Megabytes and run in headless mode
CMD [  "screen", "-S", "minecraft_server", "java", "-Xmx1024M", "-Xms1024M", "-jar", "minecraft_server.1.20.1.jar", "nogui" ]
```

Use a compatible version of java
Install screen to be able to exect into the container and reattach to the

`docker image build -t <image_name> .`
`docker volume create minecraft-storage`

### Running and Managing the Server

Exec into the running container - `docker exec -it <conatiner_name> sh`
Find the running screen process with - `screen -ls`
Attach to the running screen with - `screen -rd`
Run minecraft server commands as needed
Detach from screen session with `CTRL + A + D`
