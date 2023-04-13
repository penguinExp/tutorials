# 20 System Design Concepts Explained

Welcome to the world of system design, where engineers build castles in the air and then figure out how to put foundations under them. If you're ready to dive into a world of scalable, efficient, and user-friendly software, then grab your hard hat and get ready to dig in!

### Vertical and Horizontal Scaling

Let's say you have a single [server](#server) up and running. It's accepting requests over an HTTP network in the [Request-Response Lifecycle](#rrl). After some time you're getting more user's who are making tons of API calls which your server cannot handle.

To tackle this issue we've decided to increase the power of our sever by having a powerful CPU, more RAM. This is known as **Vertical Scaling**

It is pretty easy but very limited, there is a limit on how much resources we can provide to our server.

The better approach would be to create replicas of our server, so each server can handle subset of requests.

This is known as **Horizontal Scaling**. This is more powerful because we can scale this infinitely. This also adds `Redundancy` & `Fault Tolerance` in our system, because if one of our server goes down the request can easily be routed to another server. This eliminates our previous [`single point of failure`](#spf). The downside is that this approach is much more complicated.

### Load Balancer

It also arises the problem that how do we route equal amount of request to each of the server so none of the server gets overloaded with requests and others are just sitting idle.

### Definitions

<a name="server"></a>

> 💻 A **Server** is a computer system or software that provides resources and services to other devices or programs connected to it over a network, such as the internet. It stores and manages data, applications, and information, and can handle requests and deliver responses to clients or users. Simply put, a server is like a central hub that facilitates communication and sharing between multiple devices or users.

<a name="rrl"></a>

> 🔄️ The **request-response lifecycle** is the process by which a client sends a request to a server and receives a response back. The server processes the request, sends back a response, and the client uses the response to continue its own processing. This cycle repeats for each request and response exchanged between the client and server.

<a name="spf"></a>

> ⚠️ A **single point of failure** is a component or system that, if it fails, will cause the entire system to fail or become unavailable. This means that there is no redundancy or backup in place, and if the single point of failure goes down, the entire system will go down with it. In other words, the failure of one critical component can bring the whole system crashing down.
