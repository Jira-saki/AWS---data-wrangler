#  ✨AWS Data Wrangler with Amazon Redshift ✨
[![N|Solid](https://cldup.com/dTxpPi9lDf.thumb.png)](https://nodesource.com/products/nsolid)

[![Build Status](https://travis-ci.org/joemccann/dillinger.svg?branch=master)](https://travis-ci.org/joemccann/dillinger)


## Prerequisites
- AWS account
- Create IAM role for Glue uses for the authorization to call other AWS Services.
- Redshift cluster
- AWS Glue

## Getting Started


- IAM Management console, Create role for AWS Glue, select PowerUserAccess as the policy.
- Create Redshift cluster.
- Setting up Database configurations.
- Connect to database for connect to Redshift cluster.
- create a table ( name as wstable) 
```sh
create table wstable
(
    firstname varchar,
    lastname varchar,
    age int
);
```
- insert sample data to wstable
```sh
insert into wstable values ('James','Smith',34),('Henary','Club',41),('Jony','Whack',25);
```
 - create S3 Endpoint for Glue in the Default VPC
 - Select the default route table. Select Full Access.
 - In AWS Glue, Configure Glue Connection which is used by AWS Data Wrangler to connect to the Amazon Redshift Database.
 - Setting up  Development endpoint to launch Jupyter Notebook. Select recently created IAM role.
 - When developer endpoint is ready, select Action to Create Sagemaker notebook.
 - The notebook is associated with an IAM Role AWSGlueServiceSageMakerNotebookRole-<DEFAULT>. This role needs required Glue permission in order to use Glue Connection. Open this role details in AWS IAM Management Console and attach AWSGlueServiceRole policy to it.
- When The notebook instance is ready. Write code using Data Wrangler to work with Redshift.
- Install AWS Data Wrangler in the notebook instance
```sh
!pip install awswrangler
```
- import pandas and awswrangler modules.
```sh
import awswrangler as wr
import pandas as pd
from datetime import datetime
```
- Run following command to connect to Redshift cluster and fetch all the records from the WStable. It will use Glue connection to connect to the Redshift cluster.
```sh
myconnection = wr.redshift.connect("<yourGlueConnection>")
df = wr.redshift.read_sql_query("SELECT * FROM wstable", con=myconnection)
df
```
- run the following command. It first transforms the data in df data frame by selecting firstname and lastname fields only and then writes that data to the wscustomers table in the Redshift database.
```sh
df = df [["firstname","lastname"]]
wr.redshift.to_sql(df=df,table="wscustomers",schema="public",con=myconnection)
```
- creates a new dataframe newdf with some dummy data and then appends the data to the dojocustomers table.
```sh
newdf = pd.DataFrame({"firstname": ["fname1", "fname2"], "lastname": ["lname1", "lname2"]})
wr.redshift.to_sql(df=newdf, table="wscustomers",  schema="public", con=myconnection, mode="Append")
```
- check the appended records in the Redsfhit table wscustomers
```sh
updatedf = pd.DataFrame({"firstname": ["fname1", "fname2"], "lastname": ["lastname1", "lastname2"]})
wr.redshift.to_sql(df=updatedf, table="wscustomers",  schema="public", con=myconnection, mode="upsert", primary_keys=["firstname"])	
```

  -  run the following command to close the connection to the Redshift database.

  ```sh
myconnection.close()
```






