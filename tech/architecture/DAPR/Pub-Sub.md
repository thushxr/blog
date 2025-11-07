# Pub/Sub Messaging
Pub/Sub messaging is one of the feature of DAPR where it allows to multiple services to communicate each other without depending each services. As Pub/Sub messaging is built on event driven architecture, which means here two services are loosely coupled. 

### **Why Pub/Sub Messaging**
- When compared with service to service invocation feature of DAPR, which is tightly coupled.
- Here one service is not dependent on other service.
- Publisher publishes the message to the broker and subscriber subscribes from the other side. Here we can see that the no service is dependent on each other

### **How Pub/Sub Messaging works?**
- We will be having publisher, subscriber and broker concept here.
- Publisher is the service which will send the data to the broker, and this is not dependent on any other service, the only job of publisher is to publish the message to the broker.
- Subscriber will subscribe the data from the message broker.
- We need to configure the message broker configuration in yaml file, keeping it under the components folder is good practice.
- When executing or running the DAPR sidecar we need to mention about the yaml file.

### **Downside of PubSub**
- We cannot subscribe multiple times from the same message broker and same application.
- When using Redis message broker, and subscriber service is not up and running, we may loose data.