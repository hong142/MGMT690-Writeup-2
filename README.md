# Gathering and Organizing Data  
## Intro
As we introduced in the first writeup, our team are building an image processing and object detection pipeline. We expect to integrate it into a motion-detector-based security system to reduce the false positive rate. The storage and organization stage comes first and is crucial because “garbage in, garbage out” is always true for analytics. When we setup the model on our laptop, we can hopefully come up with a model with high accuracy that essentially solves the false positive problem. However, we are on a team processing images for probably millions of customers. The analysis and storage of data should not and cannot happen on a local laptop. We need to carefully select and establish the storage place where everyone can upload data for automated processing. Any downstream processing structure will interact with this storage space to process data and develop insights out of that.

This document will guide you through the decision process of our team about the setup of the storage space, and give you some insights on criteria as well as tradeoffs you might want to consider if you are choosing a storage space for a similar system. 
## Storage Type Selection
There are three storage options, which are file storage, block storage and object storage. Given our data are large unstructured data, we will choose object storage, storing data as objects in scalable buckets.

### Reasons to Choose Object Storage
**1. Cheap**

Object storage can provide us large amount of saving on storage. To illustrate, we can make a comparison between the pricing of object storage and that of block storage. Take AWS (amazon cloud web services) as an example. 
The pricing of its object storage varies by region. Since our team locates somewhere near Ohio, we are going to choose the Ohio region, and I will further explain location selection later in the Cloud Selection part. As you see in the first picture below, first 50TB storage only cost 0.023 dollar per GB per month. Assume we have 1000 GB to store per month, then the monthly cost would be 23 dollars. Object storage usually uses tiered pricing, which means the unit cost could be lower when you access data more frequently.
 Then, let us look at the pricing of an RDS service for the same region. The pricing structure is a little different. It is per hour per instance (database server you have in the cloud). Typically, a company would have a single database everyone can access, but the number of servers to support the database varies based on needs. Assume we need a medium size and speed server, such as the “db.m4.large”. Then the monthly charge would be around 135 dollars, which equals to the cost of storing around 6TB data on object storage. If your team access data more often, and then a larger database with higher performance is needed, the ratio goes higher than 10TB. 
 
**2. Flexibility**

In addition to cost saving, object storage allows storage of varies types and amount of data. The SQL database in the previous comparison requires preset schema. Though it provides some nice data operations, it restricts the types and formats of data you can put in. However, the tradeoff between the flexibility of object storage and certain functionalities of other types is really depends on what you want to do with your data.

**3. Scalability**

According to FAQ of AWS, the data volume and object number that can be stored are unlimited, with individual s3 object size ranging from 0 bytes to 5 TB. At least currently, 5TB is more than enough to cover any company's processing need.

**4. Simple**

If we set up the object storage properly, we will just need to dump and retrieve data anytime as we need. We do not have to consider the schema or hierarchy of data in advance, which is easier to adopt for our unstructured data that is hard to fit in a schema. And things like the data versioning and access control will also be taken care of.


## Cloud Selection
After deciding on deploying the object storage, we need to select the cloud. Currently on the market, a few big players are Amazon, Google and Microsoft, with Amazon in dominance. Our team have been using Amazon for other parts, so we would like to keep things in the same AWS Ohio cloud. 
### Goals for Cloud Layout
**1. Cheap**

To choose among cloud players, cost matters the most given all of them can fulfill our functionality requirements. Usually the costs of object storage services are close to each other, so the tradeoff between functionality and cost are relatively unnecessary. 

**2. Fast**

Our team also want to ensure the transmission of data is fast, so the users would not experience any obvious delay. Storing things in the same providers' clouds, even if in multiple locations, is a better choice. Service providers usually have special wires to link their own clouds facilities around the world, so that your data don't have to go through the public Internet, where the transmission of data is unpredictable and relatively time consuming. On the other hand, if data are stored in different providers’ clouds, your data must go through the public Internet even when the physical facilities of two clouds are next to each other.
As for geolocation, we want to stick to a region nearby. Usually you can expect to download or upload more quickly if you are interacting with a cloud near you than with a cloud located at the other end of the world. That's why we choose the region of Ohio. And the transmission process tends to be most efficient when all the things are in the same cloud. 

**3. Other Tradeoff & Concern**

Generally, you should choose to avoid reaching out to public internet when you don’t have to. Even for big companies, whose personnel and customers are literally everywhere, putting everything in one cloud is still a good choice most of time. But this does reduce the flexibility. You could choose to backup or diversify if you can justify the extra costs, for example some risks with this option is highly possible and will generate large losses for your company. 
One risk here is when incidents like Internet outage preventing the usage of the only service cloud you are using, your whole business would be significantly impacted. But we believe this is a rare scenario.
Another risk is associated with some companies’ policy about their vendors’ could usage. One typical example would be Walmart, who is developing its own wed service platform and prohibited its vendors from using the AWS service. If your client has such requirement, and the client is important enough to you, you definitely want to factor this in. Besides, such policy might change the future market structure and thus alter other companies’ choices, eventually requiring your adaptation. 
Changing pricing can also be a risk added to the need of flexibility to move around among clouds.
When you scale significantly, it could indicate need for multiple locations. Especially for large service companies, they may want to have presences in locations where they have a lot of customers regardless of their personnel location.
 

### Setup of s3
The detail instruction of setup and usagea of s3 can be found on the [s3 website](http://docs.aws.amazon.com/AmazonS3/latest/gsg/GetStartedWithS3.html), at the intila stage, all you need is an s3 account, bucket name and choose a location.

### Interaction with Object Storage
We interact with object storage through the action of put, get and delete. 

**Ways to interact**
1. through AWS Management Console
2. through AWS Command Line Interface
3. Using a programming laguage such as python with certain packages

**create bucket**

log in heres my s3 service form amazon. Have a buchket there, have some staff. Go and create abukcet. Big danl bucket. 
manage at scale
Create bucket, varies option here, take the default, I can upload and add files here. Free tier, to certain point. Storage layer.

### Extra Layer
By now, we are already able to create buckets and interact with the s3 cloud. However, we believe an extra layer is needed for Integration.

Typically, When we interact with the cloud through a public API created using conlsoe, we do get another layer on it. but it still
Introduces complication in code, potential bug, behave dfierentlyon the server,than beanve on my laptop. Ideally, we could take the code, run it the same way as we did on our laptop,without having a lot complication to it, potneitally change th behave of our code such that, if behave the  same way on the cloud as its on our laptop. Not inru duig a lot of bug as we deploy it.  Make the  rest of our engering srening at uswe deploy thins they break.

Wrute things, go to dploy stage and a large block,try to dploy thsing in a coll way m, and break. Go to uing, know everything, optimiezer change this, as a business, the work flow I sbad, add risk into the pipieline, increase liability, process heal data or image form their house, sensitiveity data. A lot of tiem a lot of money, the botllmneck, everybody has to  go to the person, and if I lose the person as an employee, totally scrude. Golas here as a team find a unified way of doing locally in the cloudl connecting with the storage, is scalable. Upload ,doload didferent team dfiernt location. Working form  your local laptop and running your code in a development cluster, even if you can connect to s3, you ccan’t download. You ight be connect to mahcines . deceide as a team , we need there’s seoemthiglakcing not what we can functionally do sa we can storage data, but the interactionwe goona have. Data goence things lacking.

**1. Organization**
Organization unified, Provide governce things. Give interface to data to reda in and wirte out files just like on loacla laptop. And under the hood, perderm will take care of storing those We can worte our python sript on our laptop, then wegoongna able to deploy on the cloud, own’t have to integrate additional packages, complication, just so we can acess and run it the same way. 
Their language whiter api without this layer.
**2. Pratical Data Versioning**
 Prositeery of data version, give us file interfacae.and versioning in object store. 
**3. Access Control**
We are going to porcseing data from people's houses, so ew have to maintain a straict control of data access. Instead of giving everyone the logh gcreatial to aws for image uploading, the right of processing data internally will be given to certain persons.Pckyuderm will make the connection between the user id to a certian people, so that we will able to avoid a large amount of manually managemnt expense for acess control for data from thousandn sof houses.

ideally we  want to just throuw data to s3 and mange who have the access.to which data interally.

 we are geeting imppages form users, unlikely we gonna give users the access to our aws account. We put results somewhere back in s3, not doing things interactively, we are going to write a program that acully does things and put data somewhere.its not going to loging in and doing things actively. Not quite enough, ther is a potential other,need soething more a grapignsc interface, something you interact iwththrough  scrip ro batch procee,

**4. Compliance & privacy**
potentialy track changes in our data overtime. we find frudenlm an drejce them,for insurance claime we need to able to say what the data we procee how we procee whne we proceee, 






The automation piece, to automate script.



 

 Amazon, goolge , maicrosoft. What users you are deal with. Interactitivity. Working  goalbly and moving to the cloud. Pakcyderm over data versioning still have the same interface as beforein setado gf  using s3 clint or package or somethingto acess oall those data in s3 pykerderm can provide all the layer on top,later we process  acee thdata as we do on l local files,write on local files. Petkderm will habnlde the staorge nder the hoo do f objective storage.

Storagae is cheap in general, commodity, cheap cross industry, as oppose to servers,  people are wanting rn more and more cpu gpu more intensive things on their server they are get their cost are still pretty high.to buy operate and run a server,cost of hardwarae asscoaite with that and also costof power consumption. Save money tradeoff with pricing. Each cloud have different names. Amazon have ectu, you can spine up your own server in the cloud, don’t configure it for you. Engineering,deploy Paying , more expensive than s3, cheaofer than ,put more burden on you on deployment, we need a computer in the cloud to run off your laptop. Avoid those cost for storage . stao, databse, application databse, structured data.

Out of servers they run, thye partition running vm. Ebs harddruve. If you want ot store on that machine or atcht to tht machineHardwrive in the cloud the you toe on harddrive, the more you gonna spend. Instants gives yo acess to computing poer, attach storage to that. Rds, certain maount fodaa you can traoe, more expensive than datbse. Computing in the cloud, pay actual processing and for the storage. S3 is just stroarge, nothing about computing processing cpu, you just putting and gettingdata form that service. We’ll do later in s3, spne up in many of this serves to process data from s3,fro individual ahrdward  we hav eto attach to our dirvers. storage data together in a simpl unified place. Using server to get processing.

Ther eare tradeoff

The comparison is probably more between any system that require some structure format for the data and associate data processing with that data. Either hwve to run onec2 or a hoster service like rds. Pay for process asscociate with them .here we just paying for the storage and address the process using something like ec2 for processing not storage.



Putting together some set of things, for the laptop to store the data and store in a way with other layers on top of that towhere we can process that at scale. We can go from processing 100 imga on laptop to process image from our users. Thts going to involve object storage. Why a business what choose the soloution, putting acouple layers on top of that to enable us actually put data in object storage. Connect to one of the system nd put some data to see how it look like in a actual work scenario. 

From inbrease to preprocessing to doing that Automate way running all the time. Intellectually solve problem, but things up to process data and  integrate that to the rest of the work flow of the business. The real value happened at the stage, experimentation. Goal is on laptop, now aht, otherwise get over the step.

Processing level, actually foing processing in python
Create and run on laptop, do we need to do differently to run on s3 using packages, 
Written python package, processing image on local file and out put a model. Put this up somewhere, script what it does, locally to s3, write  some data pralleting coed, get this back to users results to api, after the stage you build the model and acorss those files, 

if you not talnking about using anything here, chere to change a lot thins in my code, integrate more packages,change the crautaute of my code, such that parereler, and more complicated. To do things in a automated way, between s3 and severput my code upther ehave to change a lot, since its not  gonna to know any thing about s3abotu where to put data, pareller,






