---
subcategory: "S3 (Simple Storage)"
layout: "aws"
page_title: "AWS: aws_s3_bucket"
description: |-
    Provides details about a specific S3 bucket
---

# Data Source: aws_s3_bucket

Provides details about a specific S3 bucket.

This resource may prove useful when setting up a Route53 record, or an origin for a CloudFront
Distribution.

## Example Usage

### Route53 Record

```terraform
data "aws_s3_bucket" "selected" {
  bucket = "bucket.test.com"
}

data "aws_route53_zone" "test_zone" {
  name = "test.com."
}

resource "aws_route53_record" "example" {
  zone_id = data.aws_route53_zone.test_zone.id
  name    = "bucket"
  type    = "A"

  alias {
    name    = data.aws_s3_bucket.selected.website_domain
    zone_id = data.aws_s3_bucket.selected.hosted_zone_id
  }
}
```

### CloudFront Origin

```terraform
data "aws_s3_bucket" "selected" {
  bucket = "a-test-bucket"
}

resource "aws_cloudfront_distribution" "test" {
  origin {
    domain_name = data.aws_s3_bucket.selected.bucket_domain_name
    origin_id   = "s3-selected-bucket"
  }
}
```

## Argument Reference

This data source supports the following arguments:

* `region` - (Optional) Region where this resource will be [managed](https://docs.aws.amazon.com/general/latest/gr/rande.html#regional-endpoints). Defaults to the Region set in the [provider configuration](https://registry.terraform.io/providers/hashicorp/aws/latest/docs#aws-configuration-reference).
* `bucket` - (Required) Name of the bucket

## Attribute Reference

This data source exports the following attributes in addition to the arguments above:

* `id` - Name of the bucket.
* `arn` - ARN of the bucket. Will be of format `arn:aws:s3:::bucketname`.
* `bucket_domain_name` - Bucket domain name. Will be of format `bucketname.s3.amazonaws.com`.
* `bucket_region` - AWS region this bucket resides in.
* `bucket_regional_domain_name` - The bucket region-specific domain name. The bucket domain name including the region name. Please refer to the [S3 endpoints reference](https://docs.aws.amazon.com/general/latest/gr/s3.html#s3_region) for format. Note: AWS CloudFront allows specifying an S3 region-specific endpoint when creating an S3 origin. This will prevent redirect issues from CloudFront to the S3 Origin URL. For more information, see the [Virtual Hosted-Style Requests for Other Regions](https://docs.aws.amazon.com/AmazonS3/latest/userguide/VirtualHosting.html#deprecated-global-endpoint) section in the AWS S3 User Guide.
* `hosted_zone_id` - The [Route 53 Hosted Zone ID](https://docs.aws.amazon.com/general/latest/gr/rande.html#s3_website_region_endpoints) for this bucket's region.
* `website_endpoint` - Website endpoint, if the bucket is configured with a website. If not, this will be an empty string.
* `website_domain` - Domain of the website endpoint, if the bucket is configured with a website. If not, this will be an empty string. This is used to create Route 53 alias records.
* `block_public_acls` - Whether Amazon S3 blocks public ACLs for this bucket.
* `block_public_policy` - Whether Amazon S3 blocks public bucket policies for this bucket.
* `ignore_public_acls` - Whether Amazon S3 ignores public ACLs for this bucket.
* `restrict_public_buckets` - Whether Amazon S3 restricts public bucket policies for this bucket.
* `server_side_encryption_configuration` - Default server-side encryption configuration of the bucket. See [`server_side_encryption_configuration`](#server_side_encryption_configuration) below.

### server_side_encryption_configuration

* `rule` - Server-side encryption configuration rules. See [`rule`](#rule) below.

### rule

* `apply_server_side_encryption_by_default` - Default server-side encryption applied to new objects in the bucket. See [`apply_server_side_encryption_by_default`](#apply_server_side_encryption_by_default) below.
* `blocked_encryption_types` - Server-side encryption algorithms blocked for new object uploads to the bucket.
* `bucket_key_enabled` - Whether an Amazon S3 Bucket Key is enabled for SSE-KMS.

### apply_server_side_encryption_by_default

* `sse_algorithm` - Server-side encryption algorithm used.
* `kms_master_key_id` - AWS KMS master key ID used for the SSE-KMS encryption, if applicable.
