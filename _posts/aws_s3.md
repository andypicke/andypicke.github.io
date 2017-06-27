

# Amazon S3 and Python

[Amazon S3](https://aws.amazon.com/s3/) is a cloud storage service offered by Amazon Web Services ([AWS]https://aws.amazon.com/). You might use S3 as a backup for your files. You can store data on S3 so you can access it from any device if you work on multiple computers. You also might want to use S3 to store data for a web app like [Dash](https://plot.ly/products/dash/) or [Shiny](https://shiny.rstudio.com/). A nice thing about S3 is that you can interact with it (and other AWS services) via python using the [boto3](https://boto3.readthedocs.io/en/latest/guide/quickstart.html) module. I've been learning how to do this recently, and below I compiled examples on how to do some of the most common tasks.

Before using S3 or boto3, you'll need to have an AWS account and set up your AWS credentials, as explained [here](https://boto3.readthedocs.io/en/latest/guide/quickstart.html#configuration).

## Import boto3 
```python
import boto3
s3 = boto3.resource('s3')
```

## Listing all your buckets

```python
for bucket in s3.buckets.all():
    print(bucket.name)
```

## Listing all files (objects) in your buckets

```python
for bucket in s3.buckets.all():
    for key in bucket.objects.all():
        print(key.key)
```

## Downloading files from your buckets

```python
key='file_key.pdf'
dest_file='file_downloaded.pdf'
s3.Bucket('my_bucket').download_file(key,dest_file)
```

## Uploading files to your bucket

```python
fname='name_of_file'
data = open(fname, 'rb')
s3.Bucket('my_bucket').put_object(Key=fname, Body=data)
```

## Deleting a bucket
- You need to first delete all objects in the bucket, then delete the bucket.
```python
for key in bucket.objects.all():
    key.delete()
bucket.delete()
```

## Create a bucket
```python
s3.create_bucket(Bucket='mybucket')
```

## Getting files from a public bucket

```python
for key in s3.Bucket('bucket_name').objects.all():
    print(key.key)
```

