# Gathering and Organizing Data  
## Intro
As we introduced in the first writeup, our team are building an image processing and object detection pipeline, which can be integrated into a motion-detector-based security system to reduce the false positive rate. The first problem we need to slove for this project is the storage of data, including to establish the storage place and to decide the ways to orgranize data. The storage and orgainzation stage comes first and is crutial beacuse for analytics Garbage in garbage out is alwasy ture. We are going to build our additional processing structure, processing data and develop insights out of that. Any downstream procesing of data would not produce stisfing results if the origianl data is of problems. When we setup the model on our laptop, we can hopefully come up with a model with high accuracy that essentially solve the problem. However, we are on a team at business that is process those image for the customers. he analysis and storage of all data for a company should not and cannot happend on a local laptop, so a place for others to upload data for automated processing is needed Translate value into company did on laptop.

This document will guide you through the decision process of our team concerning the gathering and orgizaiton of our data, and give you some insight on what decision you should make during the process, how you should approch those decisions and related things worth of extra care. Figure out how we going to setup that place to store the data, how we are going to organize the data, and varies things we need to be careful about when we processing people’s data.  

## Storage Type Selection
There are three storage options, which are file storage, block ctorage and object sotrage. Given our data are large unsturctured data, we will choose object storage, storing data as objects in scaable buckets.

### Advantages of Object Storage
**1. Cheap**

Object strage can provide us large amount of saving on stroage. To illustrate, we can make a comparision between the pricing of object storage and that of blcok storage. Take aws(amazon cloud web services) s3 for example. The pricing varies by region, since our team is located in soemwhere near ohio, we are going to choose the ohio region, and I will further explana our consideration of location later. As you can see in the first picture, first 50TB storage only cost 0.023 dollar per GB per month. Assume we have 1000 GB to store per month, then the monthly cost would be 23 dollars. Object stroage ususally using tiered pricing, which means it is even cheaper when you access data more frequently. Then,let us look at th pricing of a rds service, same region, same provider. The pricing structure of aws RDS for PostgreSQL is a little different, it is per hour per instance (database server you have in the cloud). Typically, a company would have a single databse everyone can access, but the number of servers to suport the databse could devier based on needs. Assume we need a meleidum size and speed server, such as the db.m4.large, then the monthly charge would be around 135 dollars, which equals to the cost of storing around 6TB data on object storage. If you have a large team or doing a lot things like dployment that will accesing the data a lot, and then a larger databse with higher performance is needed, the ratio goes higher than 10TB. Typically for comapnies doing data analytics, the need for monthly data storage is less than 10TB. If we can leverage object storage here for our purpose, we can potentially save a lot of money. The pricing from other web service providers shows similar pattern.


**2. Flexibility**

In addition to cost saving, object storage allows storage of varies type and amount of data. The SQL databse in hte previous comparision requires preset schema. Though it allows nice operation,serchaing over data very quickly or performing joint between data, it restircts the data type you can put in. So it's realy depends on the functionality you want ot acheive with your data. 

**3. Scalbility**

According to FAQ of aws, the data volume and object number that can be stored are unlimited, with individual s3 object size ranging from 0 bytes to 5 TB. At least curently, 5TB is more than enough to cover any company's proceesing need.

**4. Simple**

Set up porperly, you just need to dump and reteirve data as you needed. There is no need to consider the schema or herirachy of data in advance, which is easier to use for unstructured data that is hard to fit in a schema. And things like the data versioning and access contorl will also be taken care of.

### Selection of Cloud
After deciding on deploy the object storage, we need to deicide which cloud to use. Currently on the makret, a few big players are amazon, Google and Microsoft, with amazon as the first palyer in the dominance palce. We have been using amazon for other parts, we would like to keep things in the same aws ohio cloud. Threr are two part about the cloud selection ,the provider and the goegrapyica location. 

**1. cost**

To choose between cloud payers, its alwasy athe cost matters the most, if all of them can fulfill your fucnnaltiy requriemernt.Jusfity the cost for the infrasuteucture you need. cost are tied a lot of time.  

**2. Speed**

Another ceratira we consiedring is primarlily speed.

If you store things in the same providers' clouds, even if in multile locations, they usually have special wire to link those clouds around the world, so that your data don't have to go thorugh the public Internet, where the transmission of data is unpredictable and relatively time consuming. On the other hand, if data are stored in diferent payers' cloud, your data have to go through the public Internet even the phisical facility of two clouds are next to each other, so the process is not efficient, either.

As for geolocation, usually you can expect to download something or upload some thing more quickly if you are interact with a cloud near you than form a cloud located at the other end of the world. That's why we choose the region of ohio. And aws has more than a dozen of fasilities, which covers most part of the world, tis would not be a chalegneing to find one nearby. And the speed is fatest if all the things are in the same cloud, so We want to stick to a reagion if we can. 

Detectable to users is  200 miliseconds. Gernally, if under 200 then  inmeiqubie to a user, mya be slightly harder for video steraming.website, good. Detectable to users is  200 miliseconds. Gernally, if under 200 then  inmeiqubie to a user, mya be slightly harder for video steraming.website, good. 

**Other Tradeoff & Concern**

Generally, you should choose to avoid reaching put to public internet when you don’t have to. For big companies, their personnel and customers are literally everywhere, Putting everything in one cloud is one choice, trade off with that is. If aws falls up is bad, which actually deosn’t really happen too often. Last year  intenet went down, dns issue. Clutser menmory resources. Providers are pretty good that rarely happened. outages will be one. In gerrnal, if you are working on a single cloud , is not a terrible, most of time, 

scaling pretty significantly, they may need to considering more of the location. youa re processing like milliong of requrest per second form your users. A good indication. Consierd moving things around, expeically services company. Have presence in all of the regions. Even set up duplicate intrastures in saem regions.Either find a location balnce the distance to most facilities or priotize the larges and most freuent users. Specially you have infrusture you are developing on, may be you have a big cluster, trains models for data serecnei team, but its not the on eclinset are interact with, we may take the model form the cluster upload to another pipeline for users to interact with. Degifnltley have to adjust fro that aelement. One exampel would be nefliz has presence in CHICAGO, sipmply becaue they have a lot users in middle west. closer to client.

Another problem is some companies have sepcific polciy about clud can be used. TYpical sample would be Walmart, who is developing its own wed service platform, prohibited its vendors form using the aws service. If your client has such requirment, and the client is important enogh to you, you definite want to take care of this. Besides, such requriemrtn has significant impact for future makret share, which might change the market and thus impact other companies choice and eventaully requires your adaptation. Or the pricing might change, so the other cloud offers better pricing, you want to have adeaquate flexbility to move among those clouds wihtout significantly affect normal bussienss acativities. Doesn’t happen all the time, but if you hedge all you bets on a sinlge cloud, that’s like why people sometimes spread themselves acroos clouds. Just to get them some diversity just in case, they have to move or for example aws changes their pricing. Some companiea were even build this own wire. 

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

Maybe something with a script, smething we write in a program language. Potentially an api, amazon. The public api for those things is another layer on top of it. When you interact with htfs or from some other languages. Vm console machine. 
Introduce complication in code, potential bug, behave dfierentlyon the server,than beanve on my laptop, problem homogenous platform. Ideally we could take the code, run it the same way as we did on our laptop,without having a lot complication to it, potneitally change th behave of our code such that, if behave the  same way on the cloud as its on our laptop. Not inru duig a lot of bug as we deploy it.  Make the  rest of our engering srening at uswe deploy thins they break.

Wrute things, go to dploy stage and a large block,try to dploy thsing in a coll way m, and break. Go to uing, know everything, optimiezer change this, as a business, the work flow I sbad, add risk into the pipieline, increase liability, process heal data or image form their house, sensitiveity data. A lot of tiem a lot of money, the botllmneck, everybody has to  go to the person, and if I lose the person as an employee, totally scrude. Golas here as a team find a unified way of doing locally in the cloudl connecting with the storage, is scalable. Upload ,doload didferent team dfiernt location. Working form  your local laptop and running your code in a development cluster, even if you can connect to s3, you ccan’t download. You ight be connect to mahcines . deceide as a team , we need there’s seoemthiglakcing not what we can functionally do sa we can storage data, but the interactionwe goona have. Data goence things lacking.
**1. Organization**
Organization unified, 
**2. Pratical Data Versioning**
Provide governce things. Give interface to data to reda in and wirte out files just like on loacla laptop. And under the hood, perderm will take care of storing those and versioning in object store. We can worte our python sript on our laptop, then wegoongna able to deploy on the cloud, own’t have to integrate additional packages, complication, just so we can acess and run it the same way. 
Their language whiter api without this layer. Prositeery of data version, give us file interfacae.
**3. Access Control**
porcseing data from peoples house, ew have to really maintain straict control who can aces the adata, not gonna give e very bodythe logh gcreatial to aws and have them login to upload things see eberybody’es image. Have some amngementhwo  can ceee who can process the data internally. Pracicy extra layer basic facility. Trakc user id performing tracking. Id are unique to object stroagre, not necessrtiey unique to a certain person. Howve to make that connection. Basic quthentica system. Processing data form hurned thousand differnet house a lot management overhead creatae a huednr thaousna d diferetn buckts. Manually mange the acesss to all of those, ideally we  want to just throuw data to s3 and mange who have the access.to which data interally.

 we are geeting imppages form users, unlikely we gonna give users the access to our aws account. We put results somewhere back in s3, not doing things interactively, we are going to write a program that acully does things and put data somewhere.its not going to loging in and doing things actively. Not quite enough, ther is a potential other,need soething more a grapignsc interface, something you interact iwththrough  scrip ro batch procee,

**4. Compliance & privacy**
potentialy track changes in our data overtime. we find frudenlm an drejce them,for insurance claime we need to able to say what the data we procee how we procee whne we proceee, 






The automation piece, to automate script.



 

 Amazon, goolge , maicrosoft. What users you are deal with. Interactitivity. Working  goalbly and moving to the cloud. Pakcyderm over data versioning still have the same interface as beforein setado gf  using s3 clint or package or somethingto acess oall those data in s3 pykerderm can provide all the layer on top,later we process  acee thdata as we do on l local files,write on local files. Petkderm will habnlde the staorge nder the hoo do f objective storage.







