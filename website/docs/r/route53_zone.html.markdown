---
layout: "aws"
page_title: "AWS: aws_route53_zone"
sidebar_current: "docs-aws-resource-route53-zone"
description: |-
  Provides a Route53 Hosted Zone resource.
---

# aws_route53_zone

Provides a Route53 Hosted Zone resource.

## Example Usage

```hcl
resource "aws_route53_zone" "primary" {
  name = "example.com"
}
```

For use in subdomains, note that you need to create a
`aws_route53_record` of type `NS` as well as the subdomain
zone.

```hcl
resource "aws_route53_zone" "main" {
  name = "example.com"
}

resource "aws_route53_zone" "dev" {
  name = "dev.example.com"

  tags {
    Environment = "dev"
  }
}

resource "aws_route53_record" "dev-ns" {
  zone_id = "${aws_route53_zone.main.zone_id}"
  name    = "dev.example.com"
  type    = "NS"
  ttl     = "30"

  records = [
    "${aws_route53_zone.dev.name_servers.0}",
    "${aws_route53_zone.dev.name_servers.1}",
    "${aws_route53_zone.dev.name_servers.2}",
    "${aws_route53_zone.dev.name_servers.3}",
  ]
}
```

## Argument Reference

The following arguments are supported:

* `name` - (Required) This is the name of the hosted zone.
* `comment` - (Optional) A comment for the hosted zone. Defaults to 'Managed by Terraform'.
* `tags` - (Optional) A mapping of tags to assign to the zone.
* `vpc_id` - (Optional) The VPC to associate with a private hosted zone. Specifying `vpc_id` will create a private hosted zone.
  Conflicts w/ `delegation_set_id` as delegation sets can only be used for public zones.
* `vpc_region` - (Optional) The VPC's region. Defaults to the region of the AWS provider.
* `delegation_set_id` - (Optional) The ID of the reusable delegation set whose NS records you want to assign to the hosted zone.
  Conflicts w/ `vpc_id` as delegation sets can only be used for public zones.
* `force_destroy` - (Optional) Whether to destroy all records (possibly managed outside of Terraform)
  in the zone when destroying the zone.

## Attributes Reference

In addition to all arguments above, the following attributes are exported:

* `zone_id` - The Hosted Zone ID. This can be referenced by zone records.
* `name_servers` - A list of name servers in associated (or default) delegation set.
  Find more about delegation sets in [AWS docs](https://docs.aws.amazon.com/Route53/latest/APIReference/actions-on-reusable-delegation-sets.html).


## Import

Route53 Zones can be imported using the `zone id`, e.g.

```
$ terraform import aws_route53_zone.myzone Z1D633PJN98FT9
```
