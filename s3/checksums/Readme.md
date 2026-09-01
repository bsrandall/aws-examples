## Create a new s3 bucket

```sh
aws s3 mb s3://checksums-examples-sr-1234
```

## Create a file we will do a checksum on

```sh
echo "Hello Mars" > myfile.txt
```

## Upload a file to S3

```sh
aws s3 cp myfile.txt s3://checksums-examples-sr-1234
aws s3api head-object --bucket checksums-examples-sr-1234 -key myfile.txt
```

## Upload a file with a different kind of checksum

```sh
aws s3api put-object \
--bucket="checksums-examples-sr-1234" \
--key="myfilecrc32.txt" \
--body="myfile.txt" \
--checksum-algorithm="CRC32" \
--checksum-crc32="e7c80b87"
 ```