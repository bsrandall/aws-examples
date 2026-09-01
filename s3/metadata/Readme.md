## Create a bucket

```sh
aws s3 mb s3://metadata-fun-sr-0001
```

## Create a new file

```sh
echo "Hello Mars" > hello.txt
```

## Upload file with metadata

```sh
aws s3api put-object --bucket metadata-fun-sr-0001 --key hello.txt --body hello.txt --metadata Planet=Mars
```

## Get Metadata through head object

```sh
aws s3api head-object --bucket metadata-fun-sr-0001 --key hello.txt
```

## RESULTS

```
{
    "AcceptRanges": "bytes",
    "LastModified": "2026-08-30T20:39:21+00:00",
    "ContentLength": 11,
    "ETag": "\"8ed2d86f12620cdba4c976ff6651637f\"",
    "ContentType": "binary/octet-stream",
    "ServerSideEncryption": "AES256",
    "Metadata": {
        "planet": "Mars"
    }
}
```

Notice how it lowercased the Key name of Planet that we specified.