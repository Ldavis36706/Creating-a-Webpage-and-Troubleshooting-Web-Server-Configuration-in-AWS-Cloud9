# Creating a Webpage and Troubleshooting Web Server Configuration in AWS Cloud9

 ### [YouTube Demonstration](https://youtu.be/7eJexJVCqJo)

<h2>Description</h2>
This project involved setting up, configuring, and verifying a web server within an AWS Cloud9 environment running on an Ubuntu Linux EC2 instance. The tasks included resolving networking issues to ensure the server's accessibility, configuring the environment to display the web server's document root, and developing a basic webpage featuring hyperlinks, an HTML entity, and a favicon.<br />

<h2>Languages and Utilities Used</h2>

- <b> HTML: </b> For creating the basic webpage, including hyperlinks, an HTML entity, and a favicon.
- <b> Bash: </b> For running commands in the AWS Cloud9 terminal (e.g., verifying the Apache2 service, moving files).
- <b> AWS Cloud9 IDE: </b> Integrated development environment where the web server was configured and the webpage was created.
- <b> Amazon EC2: </b>  The virtual server (Ubuntu Linux instance) hosting the Apache2 web server.
- <b> Apache2: </b> The web server that served the webpage.
- <b> Linux Commands (via Bash): </b>  Used for troubleshooting and configuring the server, moving files, and running system commands (e.g., systemctl to check Apache2 status).

<h2>Environments Used </h2>

 <b>Cloud Platform:
AWS (Amazon Web Services): This project was conducted entirely in AWS, utilizing several of its services.
</b> 
<h2>Program walk-through:</h2>

<p align="center">

The following diagram depicts the basic architecture of the lab environment. The resources depicted in the diagram already exist in your Amazon Web Services (AWS) account when you start the lab.

 <br/>
 
 ![Figure 1](https://github.com/user-attachments/assets/000087ad-92cf-4e56-8ab3-0beb75c5f3af)
Figure 1: The diagram illustrates a website developer working in an AWS Cloud environment using an AWS Cloud9 IDE hosted on an Amazon Elastic Compute Cloud (Amazon EC2) instance. The instance is set up in a security-controlled public subnet of a virtual private cloud (VPC) and is accessible over the internet. The hosted website is available to consumers through the Apache HTTP Server running on the EC2 instance. For more information, refer to the following detailed diagram overview.
<br />
<br />
<br />


My first step was connecting to AWS Cloud 9 IDE. After connecting to the Cloud 9 environment I verified that the web server was running by using the following command: <b> sudo systemctl status apache2</b>.

 ![Figure 2](https://github.com/user-attachments/assets/007b8b94-a848-4dff-83c0-b34d812fdec9)
Figure 2: From the original architecture, a VPC peering connection between VPC 1 and VPC 3 has been made. An Amazon S3 gateway endpoint has been added to VPC 1.


<br />

I used a prebuilt template to analyze available traffic paths from an internet gateway. Network Access Analyzer uses automated reasoning algorithms to analyze the network paths that a packet can take between resources in an Amazon Web Services (AWS) network. The key concepts of the Network Access Analyzer are Network Access Scope and Findings. The Network Access Analyzer determines the types of findings that the analysis produces. You add entries to MatchPaths to specify the types of network paths to identify. You add entries to ExcludePaths to specify the types of network paths to exclude. Findings are potential paths in your network that match any of the MatchPaths entries in your Network Access Scope, but do not match any of the ExcludePaths entries in your Network Access Scope.
![network access scope template](https://github.com/user-attachments/assets/8d824e51-dc02-48fe-8994-765524416718)


<br />

I created an inbound path and performed an analysis for it. The inbound path analysis starts from the internet gateway of vpc3 all the way up to the network interface of vpc3-public-ec2.
If you review the network diagram provided at the top of this page. Both VPC 2 and VPC 3 have an internet gateway. You might ask why didn’t the analysis display an inbound path for vpc2?
The reason is that the Network Access Scope definition for this analysis has a source of internet gateway and a destination of network interface. In this case, vpc2-private-ec2 is in a private subnet, and internet gateways do not have a direct path to network interfaces in a private subnet.
![inbound path analysis](https://github.com/user-attachments/assets/2734ed81-b42f-48db-a2b6-55d51ff66fe8)


<br />

I created a VPC endpoint for the Amazon S3 service and verified its path using the Network Access Analyzer. VPC endpoints increase the security posture of a VPC by permitting connectivity to services without the need of an internet gateway. So your traffic stays within the AWS network.
 <br/>
![Create VPC Endpoint 1](https://github.com/user-attachments/assets/ae7b1a48-ed68-4e98-9492-44993003a27d)


<br />

The outbound path analysis starts from the network interface of the EC2 in vpc1 all the way up to the Amazon S3 endpoint. This analysis helps verify traffic paths, and can even demonstrate compliance in certain use cases.
![s3 analyzer results](https://github.com/user-attachments/assets/c07115f9-ebb3-45f1-b485-a6adc93bc726)

<br />

I used a custom Network Access Scope to verify that a private subnet does not have internet access. For a subnet to be private, there shouldn’t be a route associated with an internet gateway.
![Verify private subnet analysis](https://github.com/user-attachments/assets/a31fdfb0-ff6b-4191-8598-ebbbf7f281ee)


<br />

I defined a custom Network Access Scope to verify VPC segmentation. I assumed there was a use case where vpc1 and vpc3 require a private connection through VPC peering. The analysis verifies that there aren’t any VPC peering connections.
![verify vpc segmentation analysis](https://github.com/user-attachments/assets/544200c7-96f8-4380-9ad7-ac6eff0fc5cc)



I created a VPC peering connection between vpc1 and vpc3.
![Create peering connection request](https://github.com/user-attachments/assets/49440e9b-85a0-438c-9323-3a86d0ab5063)


<br />

I ran the previous analysis to test for VPC peering connection. The analysis shows that vpc1 and vpc3 are peered. Therefore, vpc2 has no peering connection to any other VPC. 
![vpc segmentation analysis after peering connection created](https://github.com/user-attachments/assets/bcac1025-f4ff-4d0f-98e9-c5df26d122c6)

<br />

I used a Network Access Analyzer to validate that the private subnet in vpc2 uses a NAT gateway when accessing the internet. At the same time, validate that the private subnet in vpc1 does not have access to the internet. A common use case for having a NAT gateway in a VPC is to establish internet access for a private subnet. There are some use cases where you have a private subnet that does not require access to the internet.

Review the private subnets in the architecture diagram provided in the beginning section of this lab. Both VPC 1 and VPC 2 have private subnets. However, only VPC 2 contains a NAT gateway. The diagram depicts a use case where the private subnet in VPC 2 requires access to the internet, but the private subnet in VPC 1 does not require internet access. The findings do not include the private EC2 instance in vpc1.
 <br/>
![NAT use verification analysis](https://github.com/user-attachments/assets/6b56a585-0557-475a-aee2-18d5420f213d)

<br />

Network Access Scopes can be duplicated, modified, and then used to run a new analysis. This can help save time from creating a new Network Access Scope.
I duplicated the verify-NAT-use Network Access Scope created in the previous task, and use the new scope to check any internet access path that doesn’t include a NAT gateway. The analysis confirms what you observed in the architecture diagram: VPC 3 can access the internet directly, without the need of a NAT gateway.


 <br/>
![Duplicate and modify scope](https://github.com/user-attachments/assets/3a163b9d-3380-499c-b58e-1ccb89349e36)


<br />

I configured a network change to demonstrate a compliant configuration. I assumed a use case where the private instance in vpc2 is required to access a specific IP address and port number. The requirement is as follows: 
Allow egress traffic to destination IP address 205.251.242.103 (this is an Amazon.com IP address).
Destination port number is 443.

Before making changes, use the Network Access Analyzer to review the current internet bound configuration path for vpc2-private-ec2.
![vpc2 private outbound analysis](https://github.com/user-attachments/assets/fc4e221b-823c-4a18-aee1-212042016b5a)

<br/>

Here are the outbound rules before I made changes. 
![Edit outbound rules before](https://github.com/user-attachments/assets/931007ba-580b-4511-a81f-f8fa43942688)

<br/>

Here are the updated outbound rules. 
![Updated outbound rules](https://github.com/user-attachments/assets/19af4fae-faa3-46a2-9c78-4a5d4bdcb9a2)

<br/>

I ran the analysis which shows:
The security group destination CIDR address is 205.251.242.103/32, and the outbound port is 443.
This analysis validates the current configuration is compliant with the requirement.
![Updated outbound analysis compliant](https://github.com/user-attachments/assets/f124e271-d683-43a1-afd3-d30d970c1307)


<br/>

<h2>Summary</h2>
The project commenced with the verification of the Apache2 web server's status in AWS Cloud9. Upon encountering networking issues that hindered webpage loading, I implemented troubleshooting steps to resolve them, facilitating access to the default Apache2 page. Subsequently, I backed up the existing index.html file, adjusted the AWS Cloud9 environment settings to properly display the document root, and created a new webpage incorporating hyperlinks, an HTML entity, and a favicon..
<br />




