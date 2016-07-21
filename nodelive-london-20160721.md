<!--
$theme: gaia
template: invert
page_number: true
$size: 16:9
-->

# Node.js Deployment Patterns

##### Node Live London, July 2016

###### Luke Bond

###### :wave: `T: @lukeb0nd | E: luke@yld.io | G: @lukebond`

---

# What You Will Learn From This Talk

- Common Node.js architectures & how best to deploy them
- Process monitors (spoiler: use Linux)
- Scaling and service discovery considerations
- A little about containers along the way

---

# Node.js Deployment Patterns

For simplicity, let's consider the following common app types:

- Simple web-app
- Simple web-app + API + websockets + cache + database
- Microservice-based distributed system with a message bus

Let's explore deployment patterns for these types of apps.

_TL;DR:_ don't overcomplicate; you probably don't need Kubernetes.

---

# Simple Web-App

# ![](/home/luke/Development/nodejs-deployment-patterns-talk/images/DeploymentPatterns-Type1.png)

---

# Simple Web-App

- Easily horizontally scalable
- No communication between components or across hosts
  - Hence no service discovery needed
- Run one Node process for each core
- Scale boxes horizontally; every box is the same
- Load balance (e.g. _nginx_, _HAProxy_, _balance_)

_Recommendation_: use __systemd__ (or __PM2__ if you must!).

---

# Aside: Process Monitors

There are various popular ones in use today for Node.js:

- mon
- nodemon
- forever
- PM2

The first three are simple process restarters; PM2 does has a built in load-balancer, and does logging, deployment and monitoring.

---

# Use systemd as your Process Monitor

- Core PM2 features can be easily recreated with _systemd_ & _balance_
- See my talk here where I did this in 20 minutes:
  - Video: https://opbeat.com/events/nodeconf-oslo-2016/#deploying-and-running-node-js-to-production-in-2016
  - Slides: https://github.com/lukebond/nodeconf-oslo-20160604/blob/master/nodeconf-oslo-20160604.pdf
- Read/watch this for the _how-to_; I won't go into it here

---

# Why Bother when PM2 Just Works?

- The tooling is better (especially `journalctl`)
- You have more control; you can choose your components
- Run your Node.js apps the same way you run non-Node.js apps
- Any Linux sysadmin will understand it
- No need to reinvent the wheel
- Learn more about the OS on which your apps run

`</Aside>`

---

# More Complex App w/ API + WS + DB

# ![](/home/luke/Development/nodejs-deployment-patterns-talk/images/DeploymentPatterns-Type2.png)

---

# More Complex App w/ API + WS + DB

- Easily horizontally scalable
- Communication between components required
- Scale out WS & API by process and across hosts
  - Communication between hosts then required
  - Host affinity becomes important; e.g. Redis on same host as APIs
- How to do service discovery without too much complexity?

---

# More Complex App w/ API + WS + DB

_Recommendations_: you need service discovery but keep it simple

- Use __AWS CloudFormation__ and/or __Elastic Beanstalk__
- Use __Google App Engine__
- Use __Fleet__ and a dynamically configured __nginx__ or __HAProxy__
  - Easy to transition from using __systemd__ to __CoreOS Fleet__ :ok_hand:
- Use the new __Docker Swarm__ in Docker 1.12

---

# Distributed Microservices System

# ![](/home/luke/Development/nodejs-deployment-patterns-talk/images/DeploymentPatterns-Type3.png)

---

# Distributed Microservices System

- You don't want to be thinking about any plumbing
- You don't want to be specifying exactly what runs where
- You want a declarative platform with service discovery
- You want a platform that will reschedule on app or host failures

---

# Distributed Microservices System

_Recommendation_:

- Use __Kubernetes__
- Use hosted Kubernetes such as __Tectonic__ or __Google App Engine__
- Use a PaaS such as __Deis__
- __Docker Swarm__ someday- not yet proven at scale IMO

---

# Conclusion & General Guidelines

- __Keep it simple__ or suffer the operational pain
  - No unnecessary complexity in stack or deployment
- Being __12-factor__ saves you operational pain & brings portability
- __Containers__ help if your application is sufficiently complex
  - Devs can test and debug whole system on their laptop
  - Homogeneous deployments even for heterogenous stacks
- Test, test, test; smoke test releases, ensure you can rollback easily
- __systemd__ over __PM2__ :muscle:

---

# Thanks!

###### :wave: `T: @lukeb0nd | E: luke@yld.io | G: @lukebond`
