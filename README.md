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

'''
from io import BytesIO, StringIO
import pandas as pd
import boto3
import json

textract = boto3.client("textract")
conn = boto3.client("s3")
'''
