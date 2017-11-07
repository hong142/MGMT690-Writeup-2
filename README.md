# Gathering and Organizing Data  
## Intro
As we introduced in the first writeup, our team are building an image processing and object detection pipeline. We expect to integrate it into a motion-detector-based security system to reduce the false positive rate. The storage and organization stage comes first and is crucial because “garbage in, garbage out” is always true for analytics. When we setup the model on our laptop, we can hopefully come up with a model with high accuracy that essentially solves the false positive problem. However, we are on a team processing images for probably millions of customers. The analysis and storage of data should not and cannot happen on a local laptop. We need to carefully select and establish the storage place where everyone can upload data for automated processing. Any downstream processing structure will interact with this storage space to process data and develop insights out of that.

This document will guide you through the decision process of our team about the setup of the storage space, and give you some insights on criteria as well as tradeoffs you might want to consider if you are choosing a storage space for a similar system. 
## Storage Type Selection
There are three storage options, which are file storage, block storage and object storage. Given our data are large unstructured data, we will choose object storage, storing data as objects in scalable buckets.

### Reasons to Choose [Object Storage](https://blog.rackspace.com/introduction-to-object-storage)
**1. Cheap**

Object storage can provide us large amount of saving on storage. To illustrate, we can make a comparison between the pricing of object storage and that of block storage. Take AWS (amazon cloud web services) as an example. 

The pricing of its [s3](https://aws.amazon.com/s3/) object storage varies by region. Since our team locates somewhere near Ohio, we are going to choose the Ohio region, and I will further explain location selection later in the Cloud Selection part. As you see in the first picture below, first 50TB storage only cost 0.023 dollar per GB per month. Assume we have 1000 GB to store per month, then the monthly cost would be 23 dollars. Object storage usually uses tiered pricing, which means the unit cost could be lower when you access data more frequently.

<img src="https://github.com/hong142/MGMT690-Writeup-2/blob/master/1.png" width="400" />

Then, let us look at the pricing of an RDS service for the same region. The pricing structure is a little different. It is per hour per instance (database server you have in the cloud). Typically, a company would have a single database everyone can access, but the number of servers to support the database varies based on needs. Assume we need a medium size and speed server, such as the “db.m4.large”. Then the monthly charge would be around 135 dollars, which equals to the cost of storing around 6TB data on object storage. If your team access data more often, and then a larger database with higher performance is needed, the ratio goes higher than 10TB. 

<img src="https://github.com/hong142/MGMT690-Writeup-2/blob/master/2.png" width="600" />

**2. Flexibility**

In addition to cost saving, object storage allows storage of varies types and amount of data. The SQL database in the previous comparison requires preset schema. Though it provides some nice data operations, it restricts the types and formats of data you can put in. However, the tradeoff between the flexibility of object storage and certain functionalities of other types is really depends on what you want to do with your data.

**3. Scalability**

According to FAQ of AWS, the data volume and object number that can be stored are unlimited, with individual s3 object size ranging from 0 bytes to 5 TB. At least currently, 5TB is more than enough to cover any company's processing need.

**4. Simple**

If we set up the object storage properly, we just need to dump and retrieve data anytime as we need. We do not have to consider the schema or hierarchy of data in advance, which is easier to adopt for our unstructured data that is hard to fit in a schema. And things like the data versioning and access control will also be taken care of.


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
 

## s3 Setup & Interaction 
The detail instruction of setup and use of s3 can be found on the [s3 website](http://docs.aws.amazon.com/AmazonS3/latest/gsg/GetStartedWithS3.html). At the initial stage, all you need is an s3 account, a bucket name and a cloud location. Eventually, you will interact with object storage through the action of put, get and delete. 

**Ways to interact**

1. Through AWS Management Console

<img src="https://github.com/hong142/MGMT690-Writeup-2/blob/master/4.png" width="400" />

2. Through AWS Command Line Interface

<img src="https://github.com/hong142/MGMT690-Writeup-2/blob/master/3.png" width="400" />

3. Using a programming language such as python with certain packages

## Extra Layer - [Pachyderm](http://pachyderm.io/)
By now, we are already able to create buckets and interact with the s3 cloud. However, we believe an extra layer is needed for Integration. When we interact with the cloud through a public API created using console, we do get another layer on it for interaction. However, because sometimes things behave differently on the server than on our local laptop, we would have to add packages to our code to have model run as expected on the server. As a result, we would introduce complication in our code and potentially bugs, as well.  What’s worse is the associated cost and liability due to added risks. Ideally, we would like to create an extra layer that enables us to run the code on server the same way as on our laptops without having a lot complication to it. We will use pachyderm platform as the layer to provide necessary data governance.
### Benefits of Pachyderm
**1. Data Organization**

Pachyderm takes care of everything of interaction behind the scene, including parallelism and resource management. Thus, it enables us to run our code on the server without integrating additional packages, providing a unified way of processing from local laptop to the storage. We can use the same interface all the time to read in and write our files.

**2. Practical Data Versioning**

All the data flow through the pachyderm pipeline is version controlled and stored in the repository, so we can keep track of data states or model results, which is important for ensuring consistency among teammates in development phases. 

**3. Access Control**

We are going to process images from people's houses, which are sensitive private data, so we must maintain a strict control of data access. Instead of giving everyone the login credentials to AWS for direct image uploading, the right of processing data internally will be given to only a small group. Pachyderm will make the connection between the user IDs and people with authority, so that we can avoid a large amount of management expense for doing access control manually. 

**4. Compliance & Privacy**

Because of the private feature of our data, we may need to comply with some laws that require records of data usage. With the data versioning of pachyderm, we can accurately and easily track changes in our data, analysis and results overtime. Additionally, the access control offered by pachyderm helps prevent unnecessary data exposure, protected the privacy.
## Pachyderm Setup & Interaction 
There are two ways to interact with Pachyderm, one is through Command Line Interface and the other is through its dashboard. You can follow the [instruction](http://pachyderm.readthedocs.io/en/latest/getting_started/getting_started.html) to activiate and manage your pachyderm.
