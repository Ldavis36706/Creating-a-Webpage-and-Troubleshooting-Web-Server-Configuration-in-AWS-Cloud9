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
 
![Figure 1](https://github.com/user-attachments/assets/a04afe38-bd83-4a8c-9480-0f03c0caffd5)

<i>Figure 1: The diagram illustrates a website developer working in an AWS Cloud environment using an AWS Cloud9 IDE hosted on an Amazon Elastic Compute Cloud (Amazon EC2) instance. The instance is set up in a security-controlled public subnet of a virtual private cloud (VPC) and is accessible over the internet. The hosted website is available to consumers through the Apache HTTP Server running on the EC2 instance. For more information, refer to the following detailed diagram overview.</i>
<br />
<br />
<br />


My first step was connecting to AWS Cloud 9 IDE. After connecting to the Cloud 9 environment I verified that the web server was running by using the following command: <b> sudo systemctl status apache2</b>.

![Verify Apache Server Working](https://github.com/user-attachments/assets/bbe078dc-6376-4ec1-b8ba-84799b14f32f)



<br />

I wanted to observe the web server document root directory and contents. To discover the default web server DocumentRoot location, I ran the following command: <b> cat /etc/apache2/sites-available/000-default.conf</b>. In the web server’s configuration file, webpages are served to users from the default directory /var/www/html. To list the contents of the default directory,  you can run the following command: <b> ls /var/www/html</b>.
![Document Root location](https://github.com/user-attachments/assets/22448fd9-25f4-4fc7-a62f-d047cca98a92)



<br />

To retrieve the IPv4 public IP address of the instance AWS Cloud9 is running on, run this command: <b> curl 169.254.169.254/latest/meta-data/public-ipv4</b>. You will use this public IP address to load the webpage.
![Retrieve IP Address to load webpage](https://github.com/user-attachments/assets/81c30c1b-7eab-4a6d-a354-a4473d678f22)



<br />

The webpage would not load for me.
 <br/>
![Unable to load webpage](https://github.com/user-attachments/assets/fc3791eb-6776-4385-bcfb-fe2fc6ef6105)



<br />

I began troubleshooting. I wanted to check for connectivity issues so I started checking for any network issues. I looked at the security group rules. The security group acts as a virtual firewall. When I checked the inbound rules I noticed there was no rule to allow traffic from the internet.
![Instance Inbound Rules](https://github.com/user-attachments/assets/b47ef338-c2e4-45ba-91b6-75e97eb0e990)


<br />

I added an inbound rule to allow all traffic from any IP address.
![Updated inbound rules](https://github.com/user-attachments/assets/0e8f4572-969a-4639-bbe2-6a8d6c3bc345)



<br />

I reloaded the webpage after my configuration changes. It was successful this time.
![Successfully loaded webpage](https://github.com/user-attachments/assets/0947084f-eea7-4eee-939e-ad14f1f82cc2)


<br />

I moved the existing index.html file to a backup location. To do this: Return to the AWS Cloud9 Bash terminal, and run the following command: <b> sudo mv /var/www/html/index.html /var/tmp/</b>.
I tried to reload the webpage after making these changes, but the page would not load.
![No files in Root location](https://github.com/user-attachments/assets/92144ab3-8f4b-4fc0-a23a-547ecd74b64a)

<i>Figure 2: This message is displayed because there are currently no files in the DocumentRoot location.</i>


<br />

I configured the AWS Cloud9 IDE environment window to show the web server document root. To do so, in the AWS Cloud9 Bash terminal, run the following commands: <b> ln -s /var/www/ /home/ubuntu/environment/www
sudo chown -R ubuntu /var/www/</b>.  The first command created a symlink to the /var/www directory in your environment directory. The second command changed ownership of the files in the /var/www directory so the ubuntu user (which is you) can edit the files in this www directory. If you want, you can verify the user by running whoami.
![Configure root directory](https://github.com/user-attachments/assets/a2bd39a4-98ff-488f-807f-0b75c87e2a58)


<br />

I began creating a webpage for "Anycompany Bicycle parts". I was so proud of myself for getting the header to display that I deviated from the project. Here is a temporary message where I'm asking for a job.
![Successful webpage](https://github.com/user-attachments/assets/576b59f4-0434-4933-a737-fcd9fbcef71a)


Now it's time to get back on track. I added some heading and paragraph elements. I was able to add hyperlinks to jump to different sections in my webpage. Additionally, I was able to open links in a different browswer from my webpage. I added a target attribute and gave it the value of "_blank." I added an HTML entity as well. I figured out how to include the copyright symbol. It can be achieved by typing: <b><p>&copy; </b>. Below you will see a snippet of my HTML code.
![HTML code](https://github.com/user-attachments/assets/be5b5706-5f3a-43f1-a119-17471606231e)


<br />

Finally I completed my webpage. It turned out better than I expected.
![Completed Webpage](https://github.com/user-attachments/assets/de1a58fb-1875-431d-a32f-35b14e18a029)

<br/>

<h2>Summary</h2>
The project commenced with the verification of the Apache2 web server's status in AWS Cloud9. Upon encountering networking issues that hindered webpage loading, I implemented troubleshooting steps to resolve them, facilitating access to the default Apache2 page. Subsequently, I backed up the existing index.html file, adjusted the AWS Cloud9 environment settings to properly display the document root, and created a new webpage incorporating hyperlinks, an HTML entity, and a favicon. This project gave me the confidence and skills to create webpages. I'm using the knowledge I gained to go back and enhance the repositories of my previous projects. I learned how to use features like, bold and italicize text. These changes help to visually enhance my work. I will be adding to this website in the future.
<br />




