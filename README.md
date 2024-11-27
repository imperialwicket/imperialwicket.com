# imperialwicket.com blog

This is the imperialwicket.com blog, powered by [Hugo](https://github.com/spf13/hugo/), hosted on S3/Cloudfront.

## Deploy

A post-commit would be great, but it's something like this:

````
hugo
cd public
aws s3 sync . s3://imperialwicket.com --acl public-read --dryrun
# good?
aws s3 sync . s3://imperialwicket.com --acl public-read
cd ..
````

Requires aws cli, and expects hugo at or around 0.137.
