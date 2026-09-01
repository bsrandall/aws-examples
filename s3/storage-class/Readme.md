## Create a bucket

```sh
aws s3 mb s3://class-fun-sr-0001
```

## Create a file

```sh
echo "Hello World" > hello.txt
aws s3 cp hello.txt s3://class-fun-sr-0001
```

## Get the storage class
```sh
aws s3api get-object-attributes --bucket class-fun-sr-0001 --key hello.txt --object-attributes StorageClass
```

### Results

```json
> aws s3api get-object-attributes --bucket class-fun-sr-0001 --key hello.txt --object-attributes StorageClass
{
    "LastModified": "2026-08-30T22:34:42+00:00",
    "StorageClass": "STANDARD"
}
```

## Change the storage class
```sh
aws s3 cp hello.txt s3://class-fun-sr-0001 --storage-class REDUCED_REDUNDANCY
```

## Delete the object

```sh
aws s3 rm s3://class-fun-sr-0001/hello.txt
```

## Delete the bucket

```sh
aws s3 rb s3://class-fun-sr-0001
```
