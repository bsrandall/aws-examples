## Create new bucket

```sh
aws s3api create-bucket --bucket acl-example-sr-0001 --region us-east-1
```

## Turn off Block Public Access for ACLs

```sh
aws s3api put-public-access-block \
    --bucket acl-example-sr-0001 \
    --public-access-block-configuration "BlockPublicAcls=false,IgnorePublicAcls=false,BlockPublicPolicy=true,RestrictPublicBuckets=true"
```

## Get Public Access Settings for the Bucket

```sh
aws s3api get-public-access-block --bucket acl-example-sr-0001
```
### Output
```json
{
    "PublicAccessBlockConfiguration": {
        "BlockPublicAcls": false,
        "IgnorePublicAcls": false,
        "BlockPublicPolicy": true,
        "RestrictPublicBuckets": true
    }
}
```
## Change Bucket Ownership to enable ACLs
```sh
aws s3api put-bucket-ownership-controls \
    --bucket acl-example-sr-0001 \
    --ownership-controls=Rules="[{ObjectOwnership=BucketOwnerPreferred}]"
```

## Get Bucket Ownership
```sh
aws s3api get-bucket-ownership-controls --bucket acl-example-sr-0001
```
### Output
```json
{
    "OwnershipControls": {
        "Rules": [
            {
                "ObjectOwnership": "BucketOwnerPreferred"
            }
        ]
    }
}
```
## Change ACL to allow a user access in another account

aws production account Canonical ID:  ade13dcc67f1dc15a1cfcb1a55c98f7686552f1e5360508ea0b819f27c6e2e1e

```sh
aws s3api put-bucket-acl \
    --bucket acl-example-sr-0001 \
    --grant-full-control id=ade13dcc67f1dc15a1cfcb1a55c98f7686552f1e5360508ea0b819f27c6e2e1e
```
I did not receive any type of confirmation from this, so I went to the other AWS account and uploaded an object to the S3 bucket the ACL granted me access to:

```sh
~ $ echo "Hello World" > myplanet.txt
~ $ aws s3api put-object \
> --bucket acl-example-sr-0001 \
> --body myplanet.txt \
> --key myplanet.txt
{
    "ETag": "\"e59ff97941044f85df5297e1c302d260\"",
    "ChecksumCRC64NVME": "1IsfU3fKW1k=",
    "ChecksumType": "FULL_OBJECT",
    "ServerSideEncryption": "AES256"
}
```

And that is a wrap. Object was successfully uploaded! Now, how can I revoke those grants through the CLI?

## Remove the grant-full-control previously given to the Production account

put-bucket-acl doesn't add a grant — it replaces the entire ACL every time you call it. So the cleanest way to revoke that grant isn't to try to subtract it; it's to just call put-bucket-acl again with a fresh, owner-only ACL, which wipes out everything from before, including the grant you just gave:

```sh
aws s3api put-bucket-acl --bucket acl-example-sr-0001 --acl private
```

And the output now only shows our own Canonical ID when we run `aws s3api get-bucket-acl --bucket acl-example-sr-0001`

```json
{
    "Owner": {
        "ID": "5c2e56080444073329fc0d062086557623462dfebd96a8356e219ec7fea21eac"
    },
    "Grants": [
        {
            "Grantee": {
                "ID": "5c2e56080444073329fc0d062086557623462dfebd96a8356e219ec7fea21eac",
                "Type": "CanonicalUser"
            },
            "Permission": "FULL_CONTROL"
        }
    ]
}
```
