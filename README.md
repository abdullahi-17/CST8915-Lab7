# CST8915 Lab 7 – Introduction to Docker

**Student Name**: Abdullahi Omer
**Student ID**: 040943215
**Course**: CST8915 Full-stack Cloud-native Development
**Semester**: Fall 2026

---

## Demo Video

🎥 [Watch Demo Video](https://youtu.be/5y6YwA_H1i4)

## Reflection Questions

### 1. Is RabbitMQ a stateless or stateful application?

RabbitMQ is a stateful application because it manages messages, queues, and configurations that need to be maintained between restarts. Unlike a stateless application, RabbitMQ depends on stored data to continue processing messages. If that data is lost, messages waiting to be processed may disappear, which can affect communication between services.

### 2. What are the implications of running RabbitMQ without persistent storage?

Without persistent storage, RabbitMQ stores its data inside the container's writable filesystem. If Kubernetes replaces the Pod, the data stored in the previous container can be lost. This can result in missing messages and queues, even when they are configured as durable. In the lab, the RabbitMQ Deployment did not have a PersistentVolumeClaim (PVC), meaning there was no storage configured to preserve its data when the Pod was replaced.

### 3. What happens when the RabbitMQ Pod is deleted or restarted?

To test this, I created a durable queue called `test-queue` through the RabbitMQ Management interface and published a test message. I then deleted the RabbitMQ Pod using:

```bash
kubectl delete pod -l app=rabbitmq
```

Kubernetes automatically created a new Pod because RabbitMQ was managed by a Deployment. However, when I logged back into RabbitMQ, the queue and message were gone. This showed that Kubernetes could restore the application, but it could not recover the data without persistent storage. A container restart within the same Pod may behave differently from replacing the Pod.

### 4. What are potential solutions to the RabbitMQ configuration problem?

One solution is to use a PersistentVolumeClaim (PVC) with Azure Managed Disks. This would allow RabbitMQ to store its data outside the container so it can be recovered when the Pod is replaced. Another option is to use a StatefulSet instead of a Deployment. StatefulSets are designed for stateful applications and provide stable Pod identities and persistent storage when configured with volume claims.

### 5. Would Azure Service Bus solve the RabbitMQ configuration issues?

Azure Service Bus could solve the persistence issue because it is a fully managed messaging service. Instead of storing messages inside a RabbitMQ container, Azure manages the message storage and infrastructure. This means deleting a Kubernetes Pod would not cause the messages stored in Service Bus to disappear.
