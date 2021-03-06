# Getting started with IVR - Interactive Voice Response

This walkthrough describes how to create an IVR (Interactive Voice Response) based on Asterisk / Nexmo / ippi.fr / AWS in few easy steps and how to run it on your EC2 (Amazon Web Service).

### Prerequisites

- An AWS account

### Asterisk / Nexmo / ippi.fr / AWS

Asterisk is an open source framework for building communications applications. Asterisk turns an ordinary computer into a communications server. Asterisk powers IP PBX systems, VoIP gateways, conference servers and other custom solutions. It is used by small businesses, large businesses, call centers, carriers and government agencies, worldwide. Asterisk is free and open source.

Nexmo is the global cloud communications platform leader providing innovative communication APIs and SDKs for voice, text, chat app and phone verification. Here you can rent a number for 0.50 cts per month (French mobile number)

ippi.fr is a SIP provider where we will buy some credits to be able to transfer calls from our IVR to our mobile phone number.

Amazon Web Services (AWS) is a secure cloud services platform, offering compute power, database storage, content delivery and other functionality to help businesses scale and grow. We will use EC2.

### 1. Create an EC2 instance

First of all, you need to create an EC2 instance. On Amazon Web Service, **select EC2** :

<img src="https://cloud.githubusercontent.com/assets/1462301/14611818/6c605b88-0594-11e6-8e10-86e8bc62791b.png" width="50%">

In the left menu, choose **"Instances"** :

<img src="https://cloud.githubusercontent.com/assets/1462301/14611937/00c9737c-0595-11e6-86f5-7f8604b00d45.png" height="300">

On the Instance page, click on **"Launch Instance"** on the top menu :

<img src="https://cloud.githubusercontent.com/assets/1462301/14612021/5ee09dbe-0595-11e6-9b44-839bd08faa8d.png" width="50%">

"Step 1: Choose an Amazon Machine Image (AMI)" : You should select **"Ubuntu Server 14.04 LTS (HVM), SSD Volume Type - ami-f95ef58a"** :

<img src="https://cloud.githubusercontent.com/assets/1462301/14612107/e0682ab4-0595-11e6-862f-3417b92efad9.png" width="80%">

"Step 2: Choose an Instance Type" : Verify that the instance **t2.micro**  is selected and click on **"Review and launch"**

<img src="https://cloud.githubusercontent.com/assets/1462301/14612294/a60656d8-0596-11e6-804b-81da57f9689d.png" width="80%">

"Step 7: Review Instance Launch" : click on **"Launch"** (Right bottom of the page)

If you already used AWS, you already have a key pair so you can use it and for finish, click on **"Launch Instance"**

<img src="https://cloud.githubusercontent.com/assets/1462301/14612500/97472d9c-0597-11e6-9630-f4de9d139dc2.png" width="50%">

If you never used AWS, you should create a new key pair : Enter the name of your key pair (can be what you want) and click on **"Download Key Pair"** and for finish, click on **"Launch Instance"**

<img src="https://cloud.githubusercontent.com/assets/1462301/14612584/28291816-0598-11e6-9f16-bcf17e36e714.png" width="50%">

Now, you should have this page : click on **"View Instances"**  (Right bottom of the page)

<img src="https://cloud.githubusercontent.com/assets/1462301/14612712/f2e36282-0598-11e6-9c45-d9b59cf3b759.png" width="100%">

A new instance is launching :

<img src="https://cloud.githubusercontent.com/assets/1462301/14612771/42503606-0599-11e6-9e2e-b86c049eca43.png" width="100%">

After few minutes, when the EC2 instance is ready, you should see this :

<img src="https://cloud.githubusercontent.com/assets/1462301/14612823/94b4cdee-0599-11e6-8a80-aba0d38c2911.png" width="100%">

If you click on it, you will see all the details about the EC2 Instance.

<img src="https://cloud.githubusercontent.com/assets/1462301/14612929/074f2002-059a-11e6-8073-dacc0677347e.png" width="80%">

### 2. Connect to your EC2 Instance

On Amazon Web Service, select EC2, Instances (in the left menu), select your instance and click on **"Connect"** (in the top menu).

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

### 4. Install Asterisk and Sendmail

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


### 5. Configuration Sendmail

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
"Subject: New message on your Voicemail from $2 !" \
"To: Voicemail <tony@myprovider.com>" \
"Content-Type: application/wav" \
"Content-Disposition: attachment; filename=$(basename $1)" \
"Content-Transfer-Encoding: base64" \
""; base64 $1) | /usr/sbin/sendmail -t

```

Save your file Ctrl + X + S, then press y and enter

Then, run this on your EC2 Instance :

```bash
cd /home/ubuntu/ivr-guillaume-teillet
chmod 777 wavmail.sh
```

### 6. Create an ippi.fr account to activate redirection call on your mobile phone.

One of the functionality of this IVR is to redirect urgent call to your mobile phone.

For this functionality, we need a SIP account. I choose to use a ippi.fr account but you can use another provider if you want. You can sign up here : https://www.ippi.com/index.php?page=sp-offer&lang=44&referrer=guillaumeteilletpro

<img src="https://cloud.githubusercontent.com/assets/1462301/17658223/b438058a-62fc-11e6-9757-563a5f85e0b1.png" width="100%">

**OPTIONAL :**

When your free ippi account is ready, you need to add some credits or apply for a package to be able to use the redirection feature (it's not free of charge).

<img src="https://cloud.githubusercontent.com/assets/1462301/17658276/1bf04b74-62fd-11e6-8aeb-414cc8fe8cf6.png" width="25%">

### 7. Add an elastic ip to your EC2 Instance

We will add an elastic ip to your EC2 Instance. On the left menu, in the "Network & Security" section, select "Elastic IPs".

<img src="https://cloud.githubusercontent.com/assets/1462301/17658885/3f1c0dbe-6301-11e6-84a4-b07aeedf4eff.png" width="50%">

On the Elastic IP page, click on the blue button "Allocate new address".

<img src="https://cloud.githubusercontent.com/assets/1462301/17658909/6b8cf318-6301-11e6-8978-02b9cb4d3222.png" width="50%">

A popup appears, click on "Yes, allocate"

<img src="https://cloud.githubusercontent.com/assets/1462301/17658941/a4a537b4-6301-11e6-85e0-b06a58a11e34.png" width="50%">

AWS will allocate an new elastic ip to your account. Click on "Close"

<img src="https://cloud.githubusercontent.com/assets/1462301/17658972/f841de4a-6301-11e6-9005-a508eff38b5e.png" width="50%">

Then, click on "Actions" in the menu and select "Associate Address"

<img src="https://cloud.githubusercontent.com/assets/1462301/17658993/2a4750d2-6302-11e6-8027-1847a0703d51.png" width="50%">

A popup appears, select your instance in the first field and then click on "Associate"

<img src="https://cloud.githubusercontent.com/assets/1462301/17659038/807f5aa8-6302-11e6-9e25-91f5972e74d5.png" width="50%">

Now, on the left menu, click on Instances.

<img src="https://cloud.githubusercontent.com/assets/1462301/17659223/b9741d3e-6303-11e6-8626-9882510c5264.png" width="50%">

On the Instances page, select your EC2 Instances to get all the details of your instance :

<img src="https://cloud.githubusercontent.com/assets/1462301/17659302/2ac914e4-6304-11e6-91c9-5e744ca74d26.png" width="100%">

We will need the private IP and the public IP in the next step !

Now, your public address has changed. **You need to ssh again on your EC2 Instance (See step 2 - Connect to your EC2 Instance)**

### 8. Configuration Asterisk

Run this on your EC2 instance :

```bash
cd /home/ubuntu/ivr-guillaume-teillet
nano sip.conf
```

After "externip=" replace YOUR_PUBLIC_IP_AWS by your elastic IP (public IP address).
After "localnet=" replace YOUR_PRIVATE_IP_AWS by your private IP address.

If you want to activate the redirection feature, replace all "YOUR_USERNAME_IPPI" by your ippi.fr username and all "YOUR_PASSWORD_IPPI" by your ippi.fr password.

If you want to activate the redirection feature, your sip.conf file should look like this :

```bash
[general]

bindaddr = 0.0.0.0
context = ivr_menu
host=dynamic
type=friend
encryption=yes
externip=52.209.221.205
localnet=172.31.34.144/255.255.255.0
nat=yes
register=guillaumeteilletpro:mypwd33lol@ippi.fr


[ippi]

type=peer
host=ippi.fr
username=guillaumeteilletpro
secret=mypwd33lol
fromuser=guillaumeteilletpro
fromdomain=ippi.fr
nat=yes
canreinvite=no
```

If you **DO NOT** want to activate the redirection feature, your sip.conf file should look like this :

```bash
[general]

bindaddr = 0.0.0.0
context = ivr_menu
host=dynamic
type=friend
encryption=yes
externip=52.209.221.205
localnet=172.31.34.144/255.255.255.0
nat=yes
```

Save your file Ctrl + X + S, then press y and enter


A) If you want to activate the redirection feature

Run this on your EC2 instance :

```bash
cd /home/ubuntu/ivr-guillaume-teillet
rm extensions_without_redirection.conf
nano extensions.conf
```

This file is divided in 2 part. First part (l1 -l113) is for the french version of the IVR. Second part (l122 - l225) is for the english version.

We have to update a parameter in the Dial command in [fr_option_3_1] and [en_option_3_1]

```bash
[fr_option_3_1]
exten => 35,1,Background(/home/ubuntu/ivr-guillaume-teillet/sounds/fr/fr7)
exten => 35,2,Dial(SIP/ippi/YOUR_PHONE_NUMBER);
```

```bash
[en_option_3_1]
exten => 45,1,Background(/home/ubuntu/ivr-guillaume-teillet/sounds/en/en7)
exten => 45,2,Dial(SIP/ippi/YOUR_PHONE_NUMBER);
```

Here, replace YOUR_PHONE_NUMBER by your own phone number with the International code (for example france is 33, USA is 1). So you should have something like (for a french number) :

```bash
[fr_option_3_1]
exten => 35,1,Background(/home/ubuntu/ivr-guillaume-teillet/sounds/fr/fr7)
exten => 35,2,Dial(SIP/ippi/33612345678);
```

```bash
[en_option_3_1]
exten => 45,1,Background(/home/ubuntu/ivr-guillaume-teillet/sounds/en/en7)
exten => 45,2,Dial(SIP/ippi/33612345678);
```

Save your file Ctrl + X + S, then press y and enter.

I will explain later the commands (Section "Customize your IVR").

B) If you **DO NOT** want to activate the redirection feature

Run this on your EC2 instance :

```bash
cd /home/ubuntu/ivr-guillaume-teillet
rm extensions.conf
cp extensions_without_redirection.conf extensions.conf
nano extensions.conf
```

This file is divided in 2 part. First part (l1 -l88) is for the french version of the IVR. Second part (l96 - l174) is for the english version.

I will explain later the commands (Section "Customize your IVR").

### 9. Configuration of the voicemail folder

Run this on your EC2 instance :

```bash
cd /home/ubuntu/ivr-guillaume-teillet
mkdir voicemail
chmod 777 voicemail
cp extensions.conf /etc/asterisk/extensions.conf
cp sip.conf /etc/asterisk/sip.conf
```

### 10. Open a port on your EC2 Instance

Now we will open the UDP port 5060 in the security group of our EC2 instance.

Go to the instance page :

<img src="https://cloud.githubusercontent.com/assets/1462301/14611937/00c9737c-0595-11e6-86f5-7f8604b00d45.png" width="50%">

Select your instance, and click on the security group.

<img src="https://cloud.githubusercontent.com/assets/1462301/17685645/80552f6c-6398-11e6-901a-d264fdee5cd0.png" width="100%">

Then, select the Inbound Tab, and click on "Edit"

<img src="https://cloud.githubusercontent.com/assets/1462301/17686244/910487f4-639d-11e6-9041-1f4f65a2d2cf.png" width="100%">

A popup appears, click on "Add rule", select "Custom UDP rule" in the type field, set the "Port range" at 5060 and set "Source" at "Anywhere". Click on "Save"

<img src="https://cloud.githubusercontent.com/assets/1462301/17686425/f2f48594-639e-11e6-88fb-b08033cbb316.png" width="100%">

### 11. Create a Nexmo account and buy your first number

Open a free account and get 2 euros welcome credit : https://dashboard.nexmo.com/sign-up

Then, select Numbers in the top menu.

<img src="https://cloud.githubusercontent.com/assets/1462301/17686665/1920d374-63a1-11e6-813b-f2ea62aa343c.png" width="100%">

On the Numbers page, select Buy number in the left menu.

<img src="https://cloud.githubusercontent.com/assets/1462301/17686747/a2525776-63a1-11e6-8b65-b54aa4eb8829.png" width="50%">

On the Buy Numbers page, select a country, a feature (VOICE OR VOICE + SMS), and a type, then click on "Search"

<img src="https://cloud.githubusercontent.com/assets/1462301/17686790/1428ee8c-63a2-11e6-9adc-5ab5e8170421.png" width="100%">

Choose your number on the list and click on "Buy"

<img src="https://cloud.githubusercontent.com/assets/1462301/17686819/48528bb4-63a2-11e6-8083-38901e5634bc.png" width="100%">

A popup appears, click on yes to confirm you want to buy this number.

<img src="https://cloud.githubusercontent.com/assets/1462301/17686853/7f6c1476-63a2-11e6-9153-b7428bed1ff1.png" width="50%">

Then, click to "Your numbers" in the left menu.

<img src="https://cloud.githubusercontent.com/assets/1462301/17687000/e9eb81a0-63a3-11e6-817f-c95dfdafabfb.png" width="50%">

On the "Your numbers" page, select "Edit" for you number.

<img src="https://cloud.githubusercontent.com/assets/1462301/17687010/f61a8bf6-63a3-11e6-8599-ed8eb94d2f1f.png" width="100%">

A popup appears, in the "Voice" section, select "Forward to SIP", and set "Number, URL or String" to "30@YOUR_PUBLIC_IP_AWS" (for French version) or to "40@YOUR_PUBLIC_IP_AWS" (for English version)

<img src="https://cloud.githubusercontent.com/assets/1462301/17687043/3a1c37a0-63a4-11e6-84c1-7041287da26e.png" width="50%">

Save the modification.

### 12. Try it with your phone !

Run this on your EC2 instance :

```bash
asterisk -rvvvvvvvv
core restart now
```

Now, you can use your phone to call the number you just bought on Nexmo. You should be able to hear the IVR.

If you want to see the incoming call on your number, run this on your EC2 instance :

```bash
asterisk -rvvvvvvvv
```

Now, the asterisk CLI is launched. After updating extensions.conf or sip.conf, you should always run this on your EC2 instance :

```bash
asterisk -rvvvvvvvv
core restart now
```

# Customize your IVR

###  Convert Audio file

If you want to convert your own WAV file for customize your EC2 server, you can run this command on your EC2 :

```bash
sox myinitialfile.wav -r 8000 -c 1 -e signed-integer mynewfile.wav -q
```

Then change the path of the audio file in extensions.conf.

###  Asterisk CMD

#### Answer([delay])

If the channel is ringing, answer it, otherwise do nothing. If a delay is specified, Asterisk will wait this number of milliseconds AFTER answering the call. If you want to add a delay prior answering, use Wait.

#### Goto([[context|]extension|]priority)

Set the priority to the specified value, optionally setting the extension and optionally the context as well. The extension BYEXTENSION is special in that it uses the current extension, thus permitting you to go to a different context, without specifying a specific extension. Please note that the LEADING arguments to Goto() are optional, not the trailing arguments.

#### Playback(filename1[&filename2...][,options])

Plays the specified sound or video file(s) (you need to omit the filename extension). Sound files are stored in the /var/lib/asterisk/sounds directory by default (the directory path can be changed in asterisk.conf).

Playback is Multi-Language-compliant. It will look in a subdirectory corresponding with the current language code (as set by the SetLanguage command, or the channel's default language code. Failing that, it will play the non-language-specific edition.

Playback will play the whole sound file(s), and when complete, return control. Compare with the Background command, which plays a sound file but returns control immediately, allowing Asterisk to perform other commands on this channel while the sound file is playing.

#### Background(filename1[&filename2...][|options[|langoverride][|context]])

This application will play the given list of files while waiting for an extension to be dialed by the calling channel. To continue waiting for digits after this application has finished playing files, the WaitExten application should be used. The 'langoverride' option explicity specifies which language to attempt to use for the requested sound files. If a 'context' is specified, this is the dialplan context that this application will use when exiting to a dialed extension. If one of the requested sound files does not exist, call processing will be terminated.

#### WaitExten(seconds) or WaitExten([seconds][|options])

Waits for the user to enter a new extension for the specified number of seconds, then returns 0. Seconds can be passed with fractions of a second. (eg: 1.5 = 1.5 seconds).

#### Set(variablename=value[|variable2=value2][|options]) (up to and including Asterisk 1.2)

This application can be used to set the value of channel variables or dialplan functions. It will accept up to 24 name/value pairs upto Asterisk 1.2, but only one name/value pair in Asterisk 1.4 or later.
When setting variables, if the variable name is prefixed with _ the variable will be inherited into channels created from the current channel. If the variable name is prefixed with __ the variable will be inherited into channels created from the current channel and all children channels.
Next to the Set() command there is also the SET function available.

#### Record(filename.format[|silence][|maxduration][|option])

Records from the current channel to a sound file saved with the given filename. The format parameter specifies the sound format and the extension of the file. If you don't specify a full path, the file will be stored in the /var/lib/asterisk/sounds directory. If a file with the same name and extension already exists, it will be overwritten.
In the case of combined audio & video recording the 'format' refers only to the audio portion, while the video portion of the recording is automatically set to the active video codec (Asterisk 1.2/1.4/1.6 cannot transcode video, at least not without out-of-tree patches). You will, for example, end up with two files: sample.wav (audio) and sample.h263 (video).

The optional parameters are:
- silence: seconds of silence allowed before the recording is stopped. If missing or 0, silence detection is disabled.
- maxduration: maximum recording duration in seconds. If missing or 0, there is no maximum.
- option: may be 'skip' to return immediately if the line is not up, or 'noanswer' to record even if the line is not up.

#### Hangup(causecode)

This application hangs up the calling channel unconditionally and returns -1. If a causecode is given the channel's hangup cause is set to the given value.

#### System(command arg1 arg2 etc)

Execute a system (Linux shell) command

#### Dial(type1/identifier1[&type2/identifier2[&type3/identifier3... ] ], timeout, options, URL)

Attempts to "dial out" on all the specified channels (each specified by a type and identifier) simultaneously. The first channel that answers "wins", and all the other outgoing channels are hung up. The originating channel that triggered this Dial command is then Answered, if necessary, and the two channels are connected together ("bridged") allowing a conversation to take place between them. When the channel that triggered the Dial command hangs up, the Dial command exits.

# Troubleshooting

###  The sound stutters

Asterisk needs bandwidth to work properly. Verify that another program does not consume too much bandwidth or start a a bigger EC2.

###  Need Help ?

If something doesn't work or if you need help, please open a ticket on this repository.

# Useful links

- Asterisk CLI : http://www.voip-info.org/wiki/view/Asterisk+CLI
- Asterisk CMD documentation : http://www.voip-info.org/wiki/view/Asterisk+-+documentation+of+application+commands
- The Complete IVR Setup Guide for Asterisk : https://www.ringroost.com/ivr-setup-asterisk.php
