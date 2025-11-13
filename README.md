<img src="https://cdn.prod.website-files.com/677c400686e724409a5a7409/6790ad949cf622dc8dcd9fe4_nextwork-logo-leather.svg" alt="NextWork" width="300" />

# VPC Monitoring with Flow Logs

**Project Link:** [View Project](http://learn.nextwork.org/projects/aws-networks-monitoring)

**Author:** Bao Luong  
**Email:** baodevops21@gmail.com
**LinkedIn:** [linkedin.com/in/bluong21](https://www.linkedin.com/in/bluong21)  

---

## VPC Monitoring with Flow Logs

![Image](http://learn.nextwork.org/stimulated_brown_festive_kaffir_lime/uploads/aws-networks-monitoring_3e1e79a1)

---

## Introducing Today's Project!

### What is Amazon VPC?

 (Virtual Private Cloud) is a service that lets you provision a logically isolated section of the AWS Cloud where you can launch AWS resources in a virtual network that you define. Think of it as your own private, isolated network within AWS.

It's incredibly useful because it gives you complete control over your virtual networking environment, including your own IP address range, subnets, route tables, and network gateways. 

### How I used Amazon VPC in this project

In today's project, I used Amazon VPC to established the peering connection between them. Then I learnt how to monitor network traffic using VPC flow logs.

### One thing I didn't expect in this project was...

One thing I didn't expect in this project was creating IAM policy and creating IAM role as well for the VPC flow logs. 

### This project took me...

This project took me about 2 hours.

---

## In the first part of my project...

### Step 1 - Set up VPCs

In this step, I will set up two VPCs using VPC wizard because I want to monitor and analyze the traffic flow.

### Step 2 - Launch EC2 instances

In this step, I will create an EC2 because it will send data to each other later in the project, which gives us network activity to monitor.

### Step 3 - Set up Logs

In this step, I will set VPC flow logs because I want to track all inbound and outbound network traffic

### Step 4 - Set IAM permissions for Logs

In this step, I will set up a flow log IAM policy and role because I need to have permission to give VPC Flow Logs the permission to write logs and send them to CloudWatch.

---

## Multi-VPC Architecture

I started my project by launching two VPCs and two public subnets. One public subnet is in each of the VPC, and we selected no private subnets. 

The CIDR blocks for VPCs 1 and 2 are 10.1.0.0/16 and 10.2.0.0/16

They have to be unique because having overlapping CIDR block will cause network routing/traffic when the traffic is flowing from one VPC to another. 

### I also launched EC2 instances in each subnet

My EC2 instances' security groups allow SSH and ICMP type. This is because EC2 instance connect will need to access to the EC2 using SSH type traffic, and we need to allow ICMP type traffic for connectivity test later.

![Image](http://learn.nextwork.org/stimulated_brown_festive_kaffir_lime/uploads/aws-networks-monitoring_e7fa8775)

---

## Logs

Logs are like a diary for your computer systems. They record everything that happens, from users logging in to errors popping up. It's the go-to place to understand what's going on with your systems, troubleshoot problems, and keep an eye on who’s doing what.

Log groups are where you keep related logs together. Usually, logs from the same source or application will go into the same log group, BUT logs are also region-specific. This means log data gets created and saved in the region it was created, although you can use CloudWatch dashboards to bring together logs from different regions.

### I also set up a flow log for VPC 1

![Image](http://learn.nextwork.org/stimulated_brown_festive_kaffir_lime/uploads/aws-networks-monitoring_e8398869)

---

## IAM Policy and Roles

I created an IAM policy because VPC Flow Logs by default don't have the permission to record logs and store them in the CloudWatch log group. This policy makes sure that your VPC can now send log data to your log group!

I also created an IAM role because we need to assign that role to the VPC Flow Logs to ensures that the service has the necessary permissions to perform its tasks.

A custom trust policy is specific type of policy! They're different from IAM policies. While IAM policies help you define the actions a user/service can or cannot do, custom trust policies are used to very narrowly define who can use a role.

Here's another way to think about it: using a custom trust policy is like using a special VIP list - only the services you pinpoint in your policy would be allowed to use your role.

![Image](http://learn.nextwork.org/stimulated_brown_festive_kaffir_lime/uploads/aws-networks-monitoring_4334d777)

---

## In the second part of my project...

### Step 5 - Ping testing and troubleshooting

In this step, I will to generate network traffic by trying to get our instance in VPC 1 to send a message to our instance in VPC 2.

### Step 6 - Set up a peering connection

In this step, I will create a Peering Connection and configure Route Tables because we need the peering connection in this step to bridge our VPCs together

### Step 7 - Analyze flow logs

In this step, I will check out what VPC Flow Logs has recorded about the network's activity!

---

## Connectivity troubleshooting

My first ping test between my EC2 instances had no replies, which means it's usually a sign that there's a problem with the connection

![Image](http://learn.nextwork.org/stimulated_brown_festive_kaffir_lime/uploads/aws-networks-monitoring_99d4ba42)

I could receive ping replies if I ran the ping test using the other instance's public IP address, which means the public IPv4 address means Instance 2 is correctly configured to respond to ping requests, and Instance 1 can actually communicate with Instance 2 if it traffic goes across the public internet!

---

## Connectivity troubleshooting

Looking at VPC 1's route table, I identified that the ping test with Instance 2's private address failed because the missing ingredient in our architecture is the VPC peering connection that directly connects VPCs 1 and 2.

### To solve this, I set up a peering connection between my VPCs

I also updated both VPCs' route tables so that traffic could flow correctly between your two VPCs via the VPC Peering connection. Without the correct entries in the route tables, your instances in different VPCs wouldn't know how to reach each other, which is why your ping test failed.

![Image](http://learn.nextwork.org/stimulated_brown_festive_kaffir_lime/uploads/aws-networks-monitoring_7316a13d)

---

## Connectivity troubleshooting

I received ping replies from Instance 2's private IP address! This means I have successfully resolved the connectivity issue between the two VPCs! The instances in VPC 1 and VPC 2 can now communicate with each other through the VPC Peering connection.

![Image](http://learn.nextwork.org/stimulated_brown_festive_kaffir_lime/uploads/aws-networks-monitoring_4ec7821f)

---

## Analyzing flow logs

Flow logs tell us about the source and destination of the network traffic, the amount of data being transferred, whether the traffic was accepted or rejected.

Flow logs tell us that 344 bytes of data were sent successfully from the IP address 18.237.140.165 to 10.1.5.112 using TCP protocol on port 22, with 4 packets transferred and the traffic was allowed ("ACCEPT").

![Image](http://learn.nextwork.org/stimulated_brown_festive_kaffir_lime/uploads/aws-networks-monitoring_d116818e)

---

## Logs Insights

Logs Insights is a CloudWatch feature that analyzes your logs. In Log Insights, you use queries to filter, process and combine data to help you troubleshoot problems or better understand your network traffic!

I ran the query the top 10 byte transfers by source and destination IP addresses. This query analyzes the flow logs collected on instance 1, and the top 10 pairs of IP addresses based on the amount of data transfer between them.

![Image](http://learn.nextwork.org/stimulated_brown_festive_kaffir_lime/uploads/aws-networks-monitoring_3e1e79a1)

---

---
