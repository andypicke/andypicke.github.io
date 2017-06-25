

# Amazon S3 and Python

[Amazon S3](https://aws.amazon.com/s3/) is a cloud storage service offered by Amazon Web Services ([AWS]https://aws.amazon.com/).

- Set up AWS account and config file (CLI?)

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
key='test.pdf'
dest_file='testdown.pdf'
s3.Bucket('andysfirstbucket').download_file(key,dest_file)
```

## Uploading files to your bucket

```python
fname='adafruit-guide-excellent-soldering.pdf'
data = open(fname, 'rb')
s3.Bucket('andysfirstbucket').put_object(Key=fname, Body=data)
```

## Deleting a bucket

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
for key in s3.Bucket('tripdata').objects.all():
    print(key.key)
```

