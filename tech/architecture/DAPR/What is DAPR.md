# DAPR
- DAPR stands for Distributed Application Runtime. It's an open-source, event driven runtime that helps developers build microservice and cloud native application.
- DAPR provides features as:
	- Service Discovery
	- Pub/Sub messaging
	- State management
	- Security
	- Retries

### **How DAPR Works?**
- When a .NET application (or any application) is being executed, a DAPR sidecar is also being executed beside the actual application.
- Your actual application interacts with the DAPR using HTTP or GRPC calls.

### **Why DAPR?**
- When building microservice application we usually come up with challenges like:
	- Service to Service communication
	- State consistency
	- Pub/Sub messaging
	- Observability
	- Retries

### **DAPR Architecture**
- Each service will be having its own sidecar
- Service talk to each other using the sidecar communication
- Each sidecar handles discovery, retries...

![Dapr-archetecture](Dapr-architecture.png)

### **Installing DAPR in Local Machine**
- You can refer the [[offiicial website]] of dapr to download and install or refer the below commands.
- Before installig DAPR make sure the Docker is installed in you machine.

**Step 1: Downlod DAPR**
- Open powershell - run below command:
```bash
powershell -Command "iwr -useb https://raw.githubusercontent.com/dapr/cli/master/install/install.ps1 | iex"
```

Step 2: If installation fails or you see something as below,
![Powershell-permission](powershell-permission.png)

Try executing below command and install dapr cli again (skip if download is successful)
```bash
Set-ExecutionPolicy RemoteSigned -Scope CurrentUser
```

Step 3: Verify the DAPR installation
```bash
dapr -h
```

Step 4: Initialize DAPR
```bash
dapr init
```

Step 5: Verify DAPR initialization is successfull
```bash
dapr --version
```

![Dapr version](dapr-version.png)