# Gathering and Organizing Data  
## Intro
As we introduced in the first writeup, our team are building an image processing and object detection pipeline, which can be integrated into a motion-detector-based security system to reduce the false positive rate. The first problem we need to slove for this project is the storage of data, including establish the storage place and decides the ways to orgranize data. The analysis and storage of all data can not happend on our local laptop, thre should be a place for other s to sned us the data and automately process those data.on the other side of the storage, is where we going to build our additional processing structure, processing data and develop insights out of that. 

In this document, I will guide you through the decision process of our team concerning the gathering and orgizaiton of our data, and give you some insight on what decision you should make during the process, how you should approch those decisions and related things worth of extra care. Figure out how we going to setup that place to store the data, how we are going to organize the data, and varies things we need to be careful about when we processing people’s data.  

This stage comes first and is crutial beacuse Garbage in garbage out, any downsteam procesing of data would not produce stisfing results if the origianl data is of problems.On you laptop, you set your model up, high accuracy, essentially solve the problem. But you on a team at business that is process those image for the customers, this is their business use case. Translate value into company did on laptop. From inbrease to preprocessing to doing that Automate way running all the time. Intellectually solve problem, but things up to process data and  integrate that to the rest of the work flow of the business. The real value happened at the stage, experimentation. Goal is on laptop, now aht, otherwise get over the step. 

Putting together some set of things, for the laptop to store the data and store in a way with other layers on top of that towhere we can process that at scale. We can go from processing 100 imga on laptop to process image from our users. Thts going to involve object storage. Why a business what choose the soloution, putting acouple layers on top of that to enable us actually put data in object storage. Connect to one of the system nd put some data to see how it look like in a actual work scenario. 

## Storage Type Selection
Depends on your data type and teh calse of your data, there are three storage options, which are file storage, block ctorage and object sotrage. given our data are large unsturctured data, we will choose object storage, which is store data as objects in scaable buckets, for the following advantages and we will use aws(amazon cloud web services) s3 for most of examples:

### Advantages of Object Storage
**1. Cheap**
Object strage can provide us large amount of saving on stroage. To illustrate, we can make a comparision between the pricing of object storage and that of blcok storage. Take aws(amazon cloud web services) s3 for example. The pricing varies by region, since our team is located in soemwhere near ohio, we are going to choose the ohio region, and I will further explana our consideration of location later in the documnet. As you can see 50TB storage only cost 0.023 dollar per GB per month. Object stroage ususally using tiered pricing, which means it is even cheaper when you access data more frequently. To make this more claer, let us look at th pricing of a rds service, same region, same provider, Amazon RDS for PostgreSQL. The pricing structure is a little different, is per hour per instance (database serve you have in the cloud). Typically you would have a single databse everyone can access, but not neserraily correspond to a sinlge instants noe, could be multiple Working together to support the databse.Assume we have 1000 GB to store per month, then the monthly cost would be 23 dollars a month. Assume we need a meleidum size and speed server, the db.m4.large, then the monthly charge would be around 135 dollars, equals to store around 6TB data on object storage. If you have a large team or doing a lot things like dployment that will accesing the data a lot, and then a larger databse with higher performance is needed, the ratio goes higher than 10TB. Typically for comapnies doing data analytics, the need for monthly data storage is less than 10TB. If we can leverage object storage here for our purpose, we can potentially save a lot of money. The pricing from other web service providers shows similar pattern.


**2. Flexibility**
In addition to cost saving, object storage allows storage of varies type and amount of data. There is no need to consider the schema or herirachy of data in advance, which is easier to use for unstructured data that is hard to fit in a schema. The SQL databse in hte previous comparision using setted up schema, allows nice operation,serchaing over data very quickly or performing joint between data. But it restircts the data type you can put in. So it's realy depends on the functionality you want ot acheive with your data. You can store virtually any kind of data in any format.

**3. Scalbility**
According to FAQ of aws, the data volume and object number that can be stored are unlimited, with individual s3 object size ranging from 0 bytes to 5 TB. At least curently, 5TB is more than enough to cover any company's proceesing need.

**4. Simple**

### Interaction with Object Storage
We interact with object storage through the action of put, get and delete. One way is through aws conslue, log in heres my s3 service form amazon. Have a buchket there, have some staff. Go and create abukcet. Big danl bucket. 
manage at scale




Storagae is cheap in general, commodity, cheap cross industry, as oppose to servers,  people are wanting rn more and more cpu gpu more intensive things on their server they are get their cost are still pretty high.to buy operate and run a server,cost of hardwarae asscoaite with that and also costof power consumption. Save money tradeoff with pricing. Each cloud have different names. Amazon have ectu, you can spine up your own server in the cloud, don’t configure it for you. Engineering,deploy Paying , more expensive than s3, cheaofer than ,put more burden on you on deployment, we need a computer in the cloud to run off your laptop. Avoid those cost for storage . stao, databse, application databse, structured data.

Out of servers they run, thye partition running vm. Ebs harddruve. If you want ot store on that machine or atcht to tht machineHardwrive in the cloud the you toe on harddrive, the more you gonna spend. Instants gives yo acess to computing poer, attach storage to that. Rds, certain maount fodaa you can traoe, more expensive than datbse. Computing in the cloud, pay actual processing and for the storage. S3 is just stroarge, nothing about computing processing cpu, you just putting and gettingdata form that service. We’ll do later in s3, spne up in many of this serves to process data from s3,fro individual ahrdward  we hav eto attach to our dirvers. storage data together in a simpl unified place. Using server to get processing.  




Layer, abstruct that out, if you havea analytics program procees ifle in and rout run the same way. Nice and simple No layer, some special client. only have to know at theend. At integration part, come to analytics team, Where data is where we gonna process data from, we have to know that integration point how to put egrees to the end , which might be s3 or different place. we don’t really care, untl e have use case for soenthing else.

Ther eare tradeoff

The comparison is probably more between any system that require some structure format for the data and associate data processing with that data. Either hwve to run onec2 or a hoster service like rds. Pay for process asscociate with them .here we just paying for the storage and address the process using something like ec2 for processing not storage.

Jusfity the cost for the infrasuteucture you need. cost are tied a lot of time.  

### Selection of Cloud
After deciding on deploy the object storage, we need to deicide which cloud to use. Currently on the makret, a few big players are amazon, Google and Microsoft, with amazon as the first palyer in the dominance palce. We have been using amazon for other parts, we would like to keep things in the same cloud. The benefit for that is primarlily speed. Threr are two part about the cloud selection ,the provider and the goegrapyica location. If you store things in the same providers' clouds, even if in multile locations, they usually have special wire to link those clouds around the world, so that your data don't have to go thorugh the public Internet, where the transmission of data is wired and relatively time consuming. On the other hand, if data are stored in diferent payers' cloud, your data have to go through the public Internet even the phisical facility of two clouds are next to each other.

Instructure project, great support, more competitive. Stop paynign to competitor for tech we need.any partner of their can’t run on aws ,singnificant in the market. Its changing.
Create near us.
Speed, down;oading something rom sybirad a lot slower than orhio. Internet I swired, the go around check opoints. 
Coupkes things going on here. 
Aws, bucht of building with competers somewhere, one ,another one, califoienia ,vierginia, Asian. What aws has done, connect allthese place with wires even accros the ocean. If your interacting with another form this ec2 to , youa ctualy don't have to get out here to public internet. As so as you puched something out to the public internet, it sgone boucns aroud all over the world.beforet actually getting to you and it’s very unproedictabe. It you going here where their owenline set up,fast baisclaly, chose to put here aws and runour computing here in the google. They also here have buings all around and connected. Someof google  the same as aws building, chichago Netflix. Competitors, if you running you comptings over here and storage over there, they don’t want you to , they want everying t in their  cloud. So even if you Rea in the dame builing, if you want to transfer data withtin the same building,you have to go out to the public internet and back to the building. Not super efficient. If we put all of our staf in a signle building. In aws, doesbn’t have to go anywhere practically, if we put storage in ohio,computing in sybiran, ok may still, not super efficient , still jumop around a littlt bit, if we using multiple cloud, still pusign around the public internet. We want to stick to a reagion if we can. 

If you are a bifger company. Literally everywhere. Data science team is in californai, and  most users in us are around the east coast, middle west, choose avoid reaching put to public internet when you don’t have to. Putting everything in one cloud is  one choice, trade off with that is. If aws falls up is bad, which actually deosn’t really happen too often. Last year  intenet went down, dns issue. Clutser menmory resources. Providers are pretty good that rarely happened. outages will be one.

What is your client is walmart, building everything for wlamart in aws, they sayyou can't yse aws. Doesn’t happen all the time, but if you hedge all you bets on a sinlge cloud, that’s like why people sometimes spread themselves acroos clouds. Just to get them some diversity just in case, they have to move or for example aws changes their pricing. Googles pricing is beeter, so its easier for them to move. Some companiea were even build this own wire. 

Closer to clint, aceess more often. Specially you have infrusture you are developing on, may be you have a big cluster, trains models for data serecnei team, but its not the on eclinset are interact with, we may take the model form the cluster upload to another pipeline for users to interact with. Degifnltley have to adjust fro that aelement. In gerrnal, if you are working on a single cloud , is not a terrible, most of time, this optimization are firarly grauer, and come only  after you are scaling pretty significantly. If you worse problem is that your cooenct tion with your database your are processing is like slow because of region. You probably doing very well , because youa re processing like milliong of requrest per second form your users. A good indication. Consierd moving things around, expeically services company. Have presence in all of the regions. Even set up duplicate intrastures in saem regions. Neflix have presence in Chicago even they don’t have a;ot personnel there. Have a lot users in middle west.

Detectable to users is  200 miliseconds. Gernally, if under 200 then  inmeiqubie to a user, mya be slightly harder for video steraming.website, good. 




