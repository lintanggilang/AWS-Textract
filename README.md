# AWS Textract will help you get hired !!!

I have news for you all !!! Finally I got my first contract in Upwork because AWS Textract. so in this section I will share about it and we will create a simple OCR application using AWS Textract.

# What is AWS Textract ?
Amazon Textract is a service provided by Amazon Web Services (AWS) that utilizes machine learning technology to perform OCR on various documents. By automating this process, Amazon Textract helps save valuable time and human effort, enhances accuracy with high precision, and enables the extraction of information from various types of documents with complex structures.

# What is OCR ?
OCR (Optical Character Recognition) is a technology used to recognize, interpret, and convert printed or handwritten text into digital data that can be processed by a computer. Using algorithms and image processing, OCR scans documents or images, identifies the characters within them, and transforms them into editable or analyzable digital text. OCR technology has various applications, including text recognition in documents, identification cards, invoices, and more, helping to automate data processing processes and improve efficiency in various fields.

We will create a simple OCR application using AWS Textract from scratch. The scenario involves reading a PDF file from an S3 bucket, performing OCR only on the data table within the document, and extracting the results to an output S3 bucket. The PDF document to be used for OCR contains one page and one table in it. These are the steps :

# Create S3 bucket
Let’s create an S3 bucket named “demo-textract” and two folders inside named “read” and “output”.

# Let’s code in lambda
Create lambada function with S3 and AWS Textract IAM, we will use python as programming language, first step is import all libraries needed

```
from io import BytesIO, StringIO
import pandas as pd
import boto3
import json

textract = boto3.client("textract")
conn = boto3.client("s3")
```

* from io import BytesIO, StringIO : Imports the BytesIO and StringIO classes from the io module. These are commonly used for reading and writing binary data (Bytes) and string data (String).
* import pandas as pd : Imports the pandas library and aliases it as pd. Pandas is a library used for data analysis, particularly for tabular data structures.
* import boto3 : Imports the boto3 library, which serves as the Python interface for AWS (Amazon Web Services) services. It enables access to various AWS services such as S3, EC2, and others.
* import json : Imports the json module for working with data in JSON format.
* textract = boto3.client(“textract”) : Creates a Textract client object from the boto3 library. Textract is an AWS service for extracting text and data from documents.
* conn = boto3.client(“s3”) : Creates an S3 client object from the boto3 library. S3 is an object storage service in AWS.

Second step is create python function, this is the full code

```
def to_csv(input_datafame, bucket, key, client=boto3.client('s3'), sep='|'):
    out_buffer = StringIO()
    input_datafame.to_csv(out_buffer, index=False, sep= sep)
    client.put_object(Bucket=bucket, Key=key, Body=out_buffer.getvalue())
    print('File {} was stored in {}'.format(key,bucket))

def lambda_handler(event, context):
    bucketname = 'demo-textract'
    filename   = 'read/FireFightingPokemon.pdf'
    
    response = textract.analyze_document(
        Document = {"S3Object": {"Bucket": bucketname, "Name": filename}},
        FeatureTypes=["TABLES"],
        )
    
    #### parsing response ####

    dict_word = {}
    for i in range(0,len(response['Blocks'])):
        if response['Blocks'][i]['BlockType'] == 'WORD' :
            a = response['Blocks'][i]['Id']
            b = response['Blocks'][i]['BlockType']
    
            if 'Relationships' in response['Blocks'][i]:
                c = (response['Blocks'][i]['Relationships'][0]['Ids'])
            else :
                c = 'null'
        
            if 'Text' in response['Blocks'][i]:
                d = response['Blocks'][i]['Text']
            else :
                d = 'null'
    
            dict_word[a] = d
    
        else : pass
    
    list_c = []
    list_d = []
    list_e = []

    for i in range(0,len(response['Blocks'])):
        if response['Blocks'][i]['BlockType'] == 'CELL' :
            if 'Relationships' in response['Blocks'][i]:
                _c = response['Blocks'][i]['Relationships'][0]['Ids']
                __c = []
                for j in range(0,len(_c)):
                    __c.append(dict_word[_c[j]])
                c = __c.copy()
                c = ' '.join(c)
            else :
                c = 'null'
    
            d = response['Blocks'][i]['RowIndex']
            e = response['Blocks'][i]['ColumnIndex']
            
            list_c.append(c)
            list_d.append(d)
            list_e.append(e)

    df = pd.DataFrame()
    
    df['word'] = list_c
    df['RowIndex'] = list_d
    df['ColumnIndex'] = list_e
    
    transformed_df = df.pivot(index='RowIndex', columns='ColumnIndex', values='word')
    transformed_df.columns = transformed_df.iloc[0]
    transformed_df = transformed_df.drop(1)
    
    #### Save output to S3 ####    

    to_csv(transformed_df, 'demo-textract', 'output/transformed_df.csv', conn, sep=',')
```

I’ll explain the code in this part, specifically the analysis_document function call

```
response = textract.analyze_document(
        Document = {"S3Object": {"Bucket": bucketname, "Name": filename}},
        FeatureTypes=["TABLES"],
        )
```

The output of this code is JSON, which needs to be parsed so that we can get insights from the document. Here is the detail :

```
textract.analyze_document()
```

Calling the analyze_document function, This line initiates the document analysis process using Textract.

```
Document = {“S3Object”: {“Bucket”: bucketname, “Name”: filename}}
```

This part indicates that the document to be analyzed is located in an S3 bucket.

* bucketname : Represents the name of the S3 bucket where the document resides.
* filename : Specifies the exact file name of the document within that bucket.

```
FeatureTypes=[“TABLES”]
```

This part instructs Textract to focus specifically on extracting tables from the document. Textract offers other features like FORMS and QUERIES for different analysis needs.

# What Next ?
Explore other AWS Textract features such as query, form, and layout features
analyze_document is a synchronous operation. To analyze documents asynchronously, use StartDocumentAnalysis (Multi Page)
Lambda functions can be set to be triggered from S3, so jobs will run automatically
Output of OCR can be stored in a database such as MySQL or PostgreSQL
Create an API that has OCR features

[Here is My Medium](https://medium.com/aws-tip/aws-textract-will-help-you-get-hired-ce77447ee1e9)

Best Regards

Lintang Gilang
