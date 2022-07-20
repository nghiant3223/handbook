# What is the cloud?
## Introduction
- "The cloud" refers to servers that are accessed over the Internet, and the software and databases that run on those servers.
- ![img.png](img/cloud.png)
- Cloud servers are located in data centers all over the world.
- A data center is a facility housing many networked computers that work together to process, store, and share data. Most major tech companies rely heavily upon data centers as a central component in delivering online services.
- By using cloud computing, users and companies do not have to manage physical servers themselves or run software applications on their own machines.
- The cloud enables users to access the same files and applications from almost any device, because the computing and storage takes place on servers in a data center, instead of locally on the user device.
- For businesses, switching to cloud computing removes some IT costs and overhead: for instance, they no longer need to update and maintain their own servers, as the cloud vendor they are using will do that.
- The cloud can also make it easier for companies to operate internationally, because employees and customers can access the same files and applications from any location.
## How does cloud computing work?
- Cloud computing is possible because of a technology called virtualization
- Virtualization allows for the creation of a simulated, digital-only "virtual" computer that behaves as if it were a physical computer with its own hardware.The technical term for such a computer is virtual machine.
- Virtual machines on the same host machine are sandboxed from one another, so they do not interact with each other at all, and the files and applications from one virtual machine are not visible to the other virtual machines even though they are on the same physical machine.
- Virtual machines also make more efficient use of the hardware hosting them
  - By running many virtual machines at once, one server becomes many servers, and a data center becomes a whole host of data centers, able to serve many organizations
  - Cloud providers can offer the use of their servers to far more customers at once
- If individual servers go down, cloud servers in general should be always online and always available. 
- Cloud vendors generally back up their services on multiple machines and across multiple regions.
- Users access cloud services either through a browser or through an app, connecting to the cloud over the Internet — that is, through many interconnected networks — regardless of what device they are using.
## What are the main service models of cloud computing?
- Define how services are offered via the cloud
![img_1.png](img/cloud_model.png)
- Software-as-a-Service (SaaS):
  - Instead of users installing an application on their device, SaaS applications are hosted on cloud servers, and users access them over the Internet. 
  - Examples of SaaS applications include Salesforce, MailChimp, and Slack.
- Platform-as-a-Service (PaaS):
  - Companies don't pay for hosted applications; instead they pay for the things they need to build their own applications. 
  - PaaS vendors offer everything necessary for building an application, including development tools, infrastructure, and operating systems, over the Internet.
  - PaaS examples include Heroku and Microsoft Azure.
- Infrastructure-as-a-Service (IaaS):
  - Company rents the servers and storage they need from a cloud provider. They then use that cloud infrastructure to build their applications.
  - IaaS is like a company leasing a plot of land on which they can build whatever they want — but they need to provide their own building equipment and materials.
  - IaaS providers include DigitalOcean, Google Compute Engine, and OpenStack.
- Function-as-a-Service (FaaS):
  - Known as serverless computing, breaks cloud applications down into even smaller components that only run when they are needed.
  - FaaS or serverless applications still run on servers, as do all these models of cloud computing.
  - But they are called "serverless" because they do not run on dedicated machines, and because the companies building the applications do not have to manage any servers
## What are the different types of cloud deployments?
- Defines where the cloud servers are and who manages them
- Private cloud:
  - A server, data center, or distributed network wholly dedicated to one organization
- Public cloud:
  - A service run by an external vendor that may include servers in one or multiple data centers
  - Unlike a private cloud, public clouds are shared by multiple organizations
## Ref
- https://www.youtube.com/watch?v=i9x0UO8MY0g
- https://www.cloudflare.com/learning/cloud/what-is-the-cloud/