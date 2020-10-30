<h2>AWS Regions</h2>
AWS has regions all around the world. It's just what it says, a region somewhere in the world.
Ex us-east-1. Each region has their own data. So data that's stored in one region
doesn't automatically become accessible in another region.

Each region has availability zones. Ex. us-east-1a, us-east-1b. Each availability zone represents
a physical data center in the region. But they're physically separate from one another. Try and 
minimize threat from physical damage. Ex. disasters. They are still connected
with each other with a high bandwidth, ultra-low latency network.

AWS Consoles are region scoped (except IAM and S3). When you perform an action it will be 
performed in the specific region. Choose the closest region to you.

<h2>IAM (Identity and Access Management)</h2>
This is where the AWS security is.
* Users
* Groups
* Roles

Root account should never be used (and shared). Only use it for the very first time to
create an account and then never again.

**Users** are usually physical people. They are grouped into **groups**, usually by function, team
or something similar.

**Roles** are given to machines. They're for internal usage within AWS resources.

Policies are defined as JSON for those above entities. It defines what each of the above
can and cannot do. It's always good to give the users the minimal amount of permissions
they need to perform their job (least privilege principles).

Multi factor authentication can be setup. Additionally there are predefined policies, that 
can be used.

IAM Federation is something that big enterprises can use. It integrates their own repository
of users with IAM. This way people can use their corporate logins in AWS. It uses the SAML
standard (Active Directory).

So the basic rules are:
* One IAM User per PHYSICAL PERSON
* One IAM Role per Application
* IAM credentials should never be shared
* Never write IAM credentials in code
* Never use ROOT account except for initial setup
* Never use ROOT IAM credentials

Permissions should be applied using groups, as it's more easily manageable. 
The users are put into groups from which they extend permissions.

IAM password policy can be applied to make sure that the users create strong 
passwords.

<h2>EC2</h2>

It consists of:
* Renting virtual machines (EC2)
* Storing data on virtual drives (EBS)
* Distributing load across machines (ELB)
* Scaling the services using an auto-scaling group (ASG)

The first part of creating an image is choosing an operating system for the machine.
Amazon Linux comes with a lot of Amazon features and it's kind of the way that 
Amazon imagines you using the services, so it's a good place to start. 
Additionally, custom images can be used. AMI are built for a specific AWS
region.

Then you have to select the type, which means how powerful your machine should be.
Different instances are specialized for different things. R/C/P/G/H/X/I/F/Z/CR 
are specialized in RAM, CPU, I/O, Network, and/or GPU. M instance types are
balanced. So they do everything decently, but doesn't excel at anything.
T2/T3 instance types are "burstable". Burstable means that the instance has
OK CPU performance, but when it needs to process something unexpected (a 
spike in load for example), it can burst, and the CPU can be VERY good.
If the machine bursts, it utilizes "burst credits". If all the credits are gone,
then the CPU becomes BDA. If the machine stops bursting, credits are accumulated
over time. If your instance consistenly runs low on credit, you need to move
to a different kind of non-burstable instance. T2 unlimited can be used for 
unlimited burst credit balance, but you pay extra for going over the credit 
balance.

Tags can be added to make identifying the instance easier.

With EC2 connect behind the scenes AWS will generate a key to connect to 
it temporarily. If SSH is turned off, then it won't work. It works using
Amazon Linux 2 AMI (Amazon Machine Images).

Elastic IP can be used to give an EC2 machine a static IP. It can be attached
to a single machine at a time. By default the EC2 IP can change with each 
restart. This often reflects poor architectural decisions, so it would be
better to avoid it. Instead, use a random public IP and register a DNS name
to it. Or we can use a load balancer and not use a public IP at all.

Having an Elastic IP on your account, even if unassociated, will cost money.

<h2>Security Group</h2>
The fundamental of network security in AWS. They control how traffic is allowed 
into or out of our EC2 Machines. They are like the firewall for EC2 instances.

![diagram](sec_group.JPG)

Inbound rules are used to control traffic coming in and outbound traffic is
used to define the traffic going out of the machine.

Security groups can be attached to multiple instances. They're locked to region
/VPC combination. It lives outside of the EC2 instance, so traffic that is blocked
is not visible in the EC2 instance.

**It's good to maintain one separate security group for SSH access.**

If you get a timeout when trying to connect, then it's a security group issue.

If you get a "connection refused" error, then it's an application error or it's
not launched.

By default all inbound traffic is blocked and all outbound traffic is allowed.

Security groups can be referenced from other security groups. This can be used
to allow other EC2 instances to connect to another EC2 instance without dealing
with IPs.

<h2>EC2 User Data</h2>
EC2 user data scripts can be used to bootstrap our instances. That is launching
commands when a machine starts. The script is run only once at the instance
first start. It runs with the sudo rights.
EC2 user data is used to automate boot tasks such as:
* Installing updates
* Installing software
* Downloading common files from the internet
* Anything you can think of

Under `Configure details` you can find `User data` under `Advanced details`.

<h2>EC2 Instance Launch Types</h2>
**On demand instances - short workload, predictable pricing. Great for elastic
workloads. That is a system that's able to adapt to the required workload.**
* Pay for what you use (billing per second, after the first minute)
* Has the highest cost but no upfront payment
* No long term commitment
* Recommended for short-term and un-interrupted workloads, where you 
can't predict how the application will behave.

**Reserved (Minimum 1 year) - closer to regular IT.**

**Reserved instances - long workloads**
* Up to 75% discount compared to On-demand.
* Pay upfront for what you use with long term commitment.
* Reservation period can be 1 or 3 years.
* Reserve a specific instance type.
* Recommended for steady state usage applications (think database).

**Convertible reserved instances: long workloads with flexible instances**
* Can change the EC2 instance type
* Up to 54% discount

**Scheduled reserved instances: example - every Thursday between 3 and 6 PM**
* launch within time window you reserve
* when you require a fraction of day / week / month

**Spot instances - short workloads, for cheap, can lose instances (less reliable)**
* Can get a discount of up to 90% compared to On-demand
* Instances that you can lose at any point of time if your max price is less
than the current spot price.
* The MOST cost-efficient instances in AWS
* Useful for workloads that are resilient to failure. Ex: batch jobs,
data analysis, image processing
* Not great for critical jobs or databases
* You specify the maximum price that you are willing to pay. When you reserve
then you'll be paying the current price and your spot will be kept as long as
the current price remains under your set maximum price.

**Great combo: Reserved instances for baseline + On-Demand & Spot
(based on failure resilience) for peaks**

**Dedicated instances - no other customers will share your hardware**
* Instances running on hardware that's dedicated to you
* May share hardware with other instances in same account
* No control over instance placement (can move hardware after Stop / Start)

**Dedicated hosts - book an entire physical server, control instance placement**
* Physical dedicated EC2 server for your use
* Full control of EC2 instance placement
* Visibility into the underlying sockets / physical cores of the hardware
* Allocated for your account for a 3 year period reservation
* More expensive
* Useful for software that have complicated licensing model.
* Or for companies that have strong regulatory or compliance needs. That is,
cannot share hardware with other customers.

**Which host is right for me?**
* On demand - coming and staying in resort whenever we like, we pay the full
price.
* Reserved - like planning ahead and if we plant to stay for a long time, we
may get a good discount.
* Spot instances - the hotel allows people to bid for the empty rooms and the
highest bidder keeps the rooms. You can get kicked out at any time.
* Dedicated hosts - we book an entire building of the resort.

You do not pay for an instance, if that instance is stopped.

Price Comparison
Example – m4.large – us-east-1

| Price Type  | Price (per hour)  |
|---|---|
|On-demand   | $0.10  |
|Spot Instance (Spot Price) | $0.032 - $0.045 (up to 90% off)|
| Spot Block (1 to 6 hours) | ~ Spot Price |
| Reserved Instance (12 months) – no upfront | $0.062 |
| Reserved Instance (12 months) – all upfront | $0.058 |
| Reserved Instance (36 months) – no upfront | $0.043 |
| Reserved Convertible Instance (12 months) – no upfront | $0.071 |
| Reserved Dedicated Instance (12 months) – all upfront | $0.064 |
| Reserved Scheduled Instance (recurring schedule on 12 months term) | $0.090 – $0.095 (5%-10% off) |
| Dedicated Host | On-demand price |
| Dedicated Host Reservation | Up to 70% off |

**Elastic Network Interfaces (ENI)**
* Logical component in a VPC (<i>Virtual Private Cloud. Just a network that 
you have on the cloud</i>) that represents a virtual network card
(<i>Network card is a piece of hardware that the computer uses to connect 
to the internet</i>). It's what gives EC2 instances access to the network.
* The ENI can have the following attributes:
    * Primary private IPv4, one or more secondary IPv4
    * One Elastic IP(IPv4) per private IPv4
    * One Public IPv4
    * One or more security groups
    * A MAC address
* ENI can be created independently and attach them on the fly on EC2 instances
for failover. That is, you can move that private IPv4 over to another EC2
instances so that the system would start using that instead of the failing one.
* They're bound to specific availability zones.

<h2> Scalability & High Availability </h2>

* Scalability means that an application / system can handle greater loads by
adapting.
* There are two kinds of scalability:
    * Vertical scalability - increasing the size of the instance. For example
    running on t2.micro and then changing to t2.large.RDS, ElastiCache are services
    that can scale vertically. There's a hardware limit how much you can 
    vertically scale. Common for a non-distributed system, ex. database.
    * Horizontal scalability (=elasticity) - increasing the number of instances
    / systems for your application. Used for distributed systems. Common for
    web applications / modern applications. 
* Scalability is linked but different to High Availability.
* High availability means running your application / system in at least 2 data 
centers (= Availability Zones). The goal is to survive a data center loss.

<h4>Load balancing</h4>
Load balances are servers that forward internet traffic to multiple servers
(EC2 Instances) downstream. It does regular health checks to your instance.
It provides high availability across zones. It cleanly separates public
traffic from private traffic.

The reasons for using an EC2 Load Balancer are:
* An ELB (EC2 Load Balancer) is a managed load balancer, which means
    * AWS guarantees that it will be working
    * AWS takes care of upgrades, maintenance, high availability
    * AWS provides only a few configuration knobs
* It costs less to setup your own load balancer, but it will be a lot more
effort on your end.
* It is integrated with many AWS offerings/services.

The health checks are run on a specific port and route. (/health is common)
If the response is not 200, then the instance is deemed unhealthy.

AWS has 3 kinds of managed Load Balancers
* Classic Load Balancer (v1 - old generation) - 2009
    * HTTP, HTTPS (Layer 7), TCP (Layer 4)
    * Health checks are TCP or HTTP based
    * Fixed hostname XXX.region.elb.amazonaws.com
* Application Load Balancer (v2 - new generation) - 2016
    * HTTP, HTTPS, WebSocket (Layer 7)
    * Load balancing to multiple HTTP applications across machines (target groups)
    * Load balancing to multiple applications on the same machine (ex. containers)
    * Support redirects (from HTTP to HTTPS for example)
    * Routing tables to different target groups:
        * Routing based on path in URL (example.com/**users** and example.com/**posts**)
        * Routing based on hostname in URL (**one.example.com** and **other.example.com**)
        * Routing based on query string, headers (example.com/users?**id=123&order=false**)
    * ALB is a great fit for micro services & container based applications (ex. Docker and Amazon ECS)
    * Has a port mapping feature to redirect to a dynamic port in ECS.
    * Can control where traffic goes from the `Listeners` tab.
* Network Load Balancer (v2 - new generation) - 2017
    * TCP, TLS (secure TCP), UDP (Layer 4)
        * Forward TCP & UDP traffic to your instances
        * Handle millions of requests per second. Higher performance than ALB.
        * Less latency ~100 ms (vs 400 ms for ALB)
    * NLB has one static IP per AZ, and supports assigning Elastic IP (helpful for 
    whitelisting specific IP). ALB and CLB do not have a static IP.
    * NLB are used for extreme performance/TCP or UDP traffic.
    * Not included in the AWS free tier
    * With NLBs, the instances do not see traffic coming from the NLB, but they see it as
    coming from the outside. Have to add a rule to allow TCP traffic from anywhere.
* Overall it's better to use V2 as they provide newer features.
* You can setup interval(private) or external(public) ELBs.


The source references the load balancer security group ID.
![diagram](load_balance_setup.JPG)

**Load balancer good to know**
* LBs can scale but not instantaneously - contact AWS for a "warm-up"
* Troubleshooting
    * 4xx errors are client induced errors
    * 5xx errors are application induced errors
    * Load Balancer Errors 503 means at capacity or no registered target
    * If the LB can't connect to your application, check your security groups
* Monitoring
    * ELB access logs will log all access requests
    * CloudWatch Metrics will give you aggregate statistics

With the load balancer, you'd turn off access to your EC2 access directly.
So HTTP traffic would only be allowed if it's coming from the ELB and not
directly from the client.

**Application load balance (v2) target groups**
* EC2 instances (can be managed by an Auto Scaling Group) - HTTP
* ECS tasks (managed by ECS itself) - HTTP
* Lambda functions - HTTP request is translated into a JSON event
* IP Addresses - must be private IPs
* Health checks are at the target group level

**ALB v2 good to know**
* Fixed hostname (XXX.region.elb.amazonaws.com)
* The application servers don't see the IP of the client directly
    * The true IP of the client is inserted in the header X-Forwarded-For
    * We can also get Port (X-Forwarded-Port) and protocol (X-Forwarded-Proto)