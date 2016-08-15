# Getting started with IVR - Interactive Voice Response

This walkthrough describes how to create an IVR (Interactive Voice Response) based on Asterisk / Nexmo / ippi.fr / AWS in few easy steps and how to run it on your EC2 (Amazon Web Service).

### Prerequisites

- An AWS account

### 1. Create an EC2 instance

First of all, you need to create an EC2 instance. On Amazon Web Service, **select EC2** :

<img src="https://cloud.githubusercontent.com/assets/1462301/14611818/6c605b88-0594-11e6-8e10-86e8bc62791b.png" width="50%">

In the left menu, choose **"Instances"** :

<img src="https://cloud.githubusercontent.com/assets/1462301/14611937/00c9737c-0595-11e6-86f5-7f8604b00d45.png" height="300">

On the Instance page, clic on **"Launch Instance"** on the top menu :

<img src="https://cloud.githubusercontent.com/assets/1462301/14612021/5ee09dbe-0595-11e6-9b44-839bd08faa8d.png" width="50%">

"Step 1: Choose an Amazon Machine Image (AMI)" : You should select **"Ubuntu Server 14.04 LTS (HVM), SSD Volume Type - ami-f95ef58a"** :

<img src="https://cloud.githubusercontent.com/assets/1462301/14612107/e0682ab4-0595-11e6-862f-3417b92efad9.png" width="80%">

"Step 2: Choose an Instance Type" : Verify that the instance **t2.micro**  is selected and clic on **"Review and launch"**

<img src="https://cloud.githubusercontent.com/assets/1462301/14612294/a60656d8-0596-11e6-804b-81da57f9689d.png" width="80%">

"Step 7: Review Instance Launch" : Clic on **"Launch"** (Right bottom of the page)

If you already used AWS, you already have a key pair so you can use it and for finish, clic on **"Launch Instance"**

<img src="https://cloud.githubusercontent.com/assets/1462301/14612500/97472d9c-0597-11e6-9630-f4de9d139dc2.png" width="50%">

If you never used AWS, you should create a new key pair : Enter the name of your key pair (can be what you want) and clic on **"Download Key Pair"** and for finish, clic on **"Launch Instance"**

<img src="https://cloud.githubusercontent.com/assets/1462301/14612584/28291816-0598-11e6-9f16-bcf17e36e714.png" width="50%">

Now, you should have this page : Clic on **"View Instances"**  (Right bottom of the page)

<img src="https://cloud.githubusercontent.com/assets/1462301/14612712/f2e36282-0598-11e6-9c45-d9b59cf3b759.png" width="100%">

A new instance is launching :

<img src="https://cloud.githubusercontent.com/assets/1462301/14612771/42503606-0599-11e6-9e2e-b86c049eca43.png" width="100%">

After few minutes, when the EC2 instance is ready, you should see this :

<img src="https://cloud.githubusercontent.com/assets/1462301/14612823/94b4cdee-0599-11e6-8a80-aba0d38c2911.png" width="100%">

If you clic on it, you will see all the details about the EC2 Instance.

<img src="https://cloud.githubusercontent.com/assets/1462301/14612929/074f2002-059a-11e6-8073-dacc0677347e.png" width="80%">

### 2. Connect to your EC2 Instance

On Amazon Web Service, select EC2, Instances (in the left menu), select your instance and clic on **"Connect"** (in the top menu).

<img src="https://cloud.githubusercontent.com/assets/1462301/14646829/615f68ce-065b-11e6-9d32-bcace60ce58e.png" width="50%">

Copy the ssh command. Mine is **"ssh -i "Guillaume.pem" ubuntu@ec2-54-171-135-46.eu-west-1.compute.amazonaws.com"**. Open a terminal, go to the folder that contains the key pair and paste the ssh command.

<img src="https://cloud.githubusercontent.com/assets/1462301/14647960/475d5e04-0660-11e6-806f-62fc6b5754a3.png" width="50%">

Now your are connected on your EC2 Instance.

### 3. Get the code

Run this on your EC2 instance :

```bash
sudo su
apt-get update
apt-get install -y git
cd /home/ubuntu/
git clone https://github.com/guillaumeteillet/ivr-guillaume-teillet
cd ivr-guillaume-teillet
```

### 3. Install Asterisk and Sendmail

Run this on your EC2 instance :

```bash
apt-get install asterisk
```

When the system is asking you "Do you want to continue? [Y/n]" Press Y and Enter.
<img src="https://cloud.githubusercontent.com/assets/1462301/17657647/a2247860-62f7-11e6-8ee6-51f9805baf07.png" width="50%">

Run this on your EC2 instance :

```bash
apt-get install sendmail
```

When the system is asking you "Do you want to continue? [Y/n]" Press Y and Enter.
<img src="https://cloud.githubusercontent.com/assets/1462301/17657699/f61ddfce-62f7-11e6-9995-e883829955a4.png" width="50%">


### 4. Configuration

Run this on your EC2 instance :

```bash
cd /home/ubuntu/ivr-guillaume-teillet
nano wavmail.sh
```

<img src="https://cloud.githubusercontent.com/assets/1462301/17657799/dc2fe1c4-62f8-11e6-9fde-3cdc6e38f3d3.png" width="50%">

Update "your-email-address@domain.tld" with your email address. Don't remove the "<>", they are important ! So if your email is tony@myprovider.com, you should have something like this :

```bash
#!/usr/bin/env bash

(printf "%s\n" \
"Subject: New message on your Voicemail !" \
"To: Voicemail <tony@myprovider.com>" \
"Content-Type: application/wav" \
"Content-Disposition: attachment; filename=$(basename $1)" \
"Content-Transfer-Encoding: base64" \
""; base64 $1) | /usr/sbin/sendmail -t

```

Save your file Ctrl + X + S, then press y and enter
