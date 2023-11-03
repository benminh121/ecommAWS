# Build an Analytical Platform for eCommerce using AWS Services
## Project Description
### Business Overview
Ecommerce analytics is the process of collecting data from all of the sources that affect a certain shop. Analysts can then utilize this information to deduce changes in customer behavior and online shopping patterns. Ecommerce analytics spans the whole customer journey, from discovery through acquisition, conversion, and eventually retention and support.

In this project, we will use an eCommerce dataset to simulate the logs of user purchases, product views, cart history, and the user’s journey on the online platform to create two analytical pipelines, Batch and Real-time. The Batch processing will involve data ingestion, Lake House architecture, processing, visualization using Amazon Kinesis, Glue, S3, and QuickSight to draw insights regarding the following:
- Unique visitors per day
- During a certain time, the users add products to their carts but don’t buy them
- Top categories per hour or weekday (i.e. to promote discounts based on trends)
- To know which brands need more marketing

The Real-time channel involves detecting Distributed denial of service (DDoS) and Bot attacks using AWS Lambda, DynamoDB, CloudWatch, and AWS SNS.

### Tech stack
- Languages:
  - SQL
  - Python3
- Services:
  - AWS S3, AWS Glue, AWS Athena, AWS Cloud9, Apache Flink, Amazon Kinesis, Amazon SNS, AWS Lambda, Amazon CloudWatch, QuickSight, Apache Zepplin, Amazon DynamoDB, AWS Glue DataBrew

### Dataset
For this project, we are going to use the e-commerce behavior data from a multi-category store. We can download from this [link](https://www.kaggle.com/datasets/mkechinov/ecommerce-behavior-data-from-multi-category-store)

This file contains behavior data for 7 months (from October 2019 to April 2020) from a large multi-category online store. Each row in the file represents an event. All events are related to products and users. Each event is like many-to-many relation between products and users. Data collected by Open CDP project. Feel free to use open source customer data platform.

### Architecture
![Diagram](images/diagram.avif)
#### Real-Time processing
We are going to build an end-to-end data engineering pipeline where we will start with this e-commerce behavior data from a multi-category store dataset as an input, which we will use to simulate a real-time e-commerce workload.

This input raw stream of data will go into an Amazon Kinesis Data Stream (stream1), which will stream the data to Amazon Kinesis Data Analytics for analysis, where we will use an Apache Flink application to detect any DDoS attack, and the filtered data will be sent to another Amazon Kinesis Data Stream (stream2).

We are going to use SQL to build the Apache Flink application using Amazon Kinesis Data Analytics and, hence, we would need a metadata store, for which we are going to use AWS Glue Data Catalog.

And then this stream2 will trigger an AWS Lambda function which will send an Amazon SNS notification to the stakeholders and shall store the fraudulent transaction details in a DynamoDB table.

#### Batch processing
If we look into the architecture diagram above, we will see that we are not storing the raw incoming data anywhere. As the data enters through Kinesis Data Stream (stream1) we are passing it to Kinesis Data Analytics to analyze. And later on, we might discover some bug in our Apache Flink application, and at that point, we will fix the bug and resume processing the data, but we cannot process the old data (which was processed by our buggy Apache Flink application). This is because we have not stored the raw data anywhere, which can allow us to re-process it later.

That's why it's recommended to always have a copy of the raw data stored in some storage (e.g., on Amazon S3) so that we can revisit the data if needed for reprocessing and/or batch processing.

This is exactly what we are going to do. We will use the same incoming data stream from Amazon Kinesis Data Stream (stream1) and pass it on to Kinesis Firehose which can write the data on S3. Then we will use Glue to catalog that data and perform an ETL job using Glue ETL to process/clean that data so that we can further use the data for running some analytical queries using Athena.

At last, we would leverage QuickSight to build a dashboard for visualization.

[Now begin step-by-step](streaming.md)