---
layout: page
title: Test Your S3 Configuration
tagline:
---

## Create a 5MB file to upload

### Linux/Mac OS/xBSD 

```
% head -c 5242880 </dev/urandom >5MB
```

### Windows Powershell

```
Function New-RandomFile {
    Param(
        $Path = '.', 
        $FileSize = 1kb, 
        $FileName = '1kb'
        ) 
    (1..($FileSize/128)).foreach({-join ([guid]::NewGuid().Guid + [guid]::NewGuid().Guid + [guid]::NewGuid().Guid + [guid]::NewGuid().Guid -Replace "-").SubString(1, 126) }) | set-content "$Path\$FileName"
}

New-RandomFile -FileSize 5MB -FileName "5MB"
```

## Set utility variables

Using these will make it easy to change scripts and experiment with the tools.

### Linux/Mac OS/xBSD

```
export UNAME=$USER
export S3_KEY=<Your S3 Key>
export S3_SECRET=<Your S3 Secret>
export S3_ALIAS=s3-data-upload
export S3_URI=s3-data-upload.sd2e.org:9001
export S3_PROTO=https
export S3_BUCKET=test
```

### Windows Powershell

```
$UNAME = [Environment]::UserName
$S3_KEY = "<Your S3 Key>"
$S3_SECRET = "<Your S3 Secret>"
$S3_ALIAS = "s3-data-upload"
$S3_URI = "s3-data-upload.sd2e.org:9001"
$S3_PROTO = "https"
$S3_BUCKET = "test"
```

## AWS CLI

```
# Create a subdirectory for your username
#
# *YOU CAN'T. AWS CLI DISALLOWS CREATION OF EMPTY DIRECTORIES*
#
# Copy 5MB file to the default bucket path including your username to create
# a subdirectory there
aws --endpoint-url $S3_PROTO://$S3_URI s3 cp 5MB s3://$S3_BUCKET/$UNAME/
# List to verify the file was transferred
aws --endpoint-url $S3_PROTO://$S3_URI s3 ls s3://$S3_BUCKET/$UNAME/
# Delete the username directory and file
aws --endpoint-url $S3_PROTO://$S3_URI s3 rm --recursive s3://$S3_BUCKET/$UNAME
```

## Minio Client

Under Windows, use `mc.exe`

```
# Create a subdirectory for your username
mc mb $S3_ALIAS/$S3_BUCKET/$UNAME
# Copy a file to it
mc cp 5MB $S3_ALIAS/$S3_BUCKET/$UNAME/
# List to verify the file was transferred
mc ls $S3_ALIAS/$S3_BUCKET/$UNAME/
# Delete the file
mc rm $S3_ALIAS/$S3_BUCKET/$UNAME/5MB
# Delete the username directory
mc rm $S3_ALIAS/$S3_BUCKET/$UNAME
```

## CyberDuck 

This client program should be self-explanatory and easy to get 
going, so we're not including test instructions for it.  

---
Return to the [S3 Documentation Overview](../index.md)
