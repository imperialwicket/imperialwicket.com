{
  "title": "Automatically deploy Hugo to S3 and Cloudfront",
  "description": "Automatically deploy Hugo to S3 and Cloudfront",
  "date": "2014-06-05",
  "url": "automatically-deploy-hugo-to-s3-and-cloudfront/",
  "type": "post",
  "tags": []
}
# Automatically deploy Hugo to S3 and Cloudfront

This could be better automated, but for now, it was easy enough to set up and it works well for my needs.

## S3cmd

[S3cmd](http://s3tools.org/s3cmd) is mature and full featured. Since [Hugo](http://hugo.spf13.com/) ships a binary only (awesome!), using s3cmd seems like the easiest corresponding solution to get your static site onto S3. Out of the box, s3cmd gives us the following awesome features:

 - `sync` behavior - only upload changed files
 - `--acl-public` - set public ACL (this way you do not need to set a policies on the bucket)
 - `--delete-removed` - when something is removed locally, remove it from s3

First install s3cmd and run `s3cmd --configure` with appropriate IAM credentials for your target bucket. Be sure this is configured for the user who will edit your site (or add the explicit config file option to the s3cmd command).

## Hugo

I stop any local `hugo server` before running `git commit`. Before pushing files, `hugo` runs to be sure that any localhost data is removed.

## Githook

The post-commit hook:

````
#!/bin/bash
#
# Deploy to s3 when master gets updated. 
#
# This expects (and does NOT check for) s3cmd to be installed and configured!
# This expects (and does NOT check for) hugo to be installed and on your $PATH

bucket='yourBucketName'
prefix=''

branch=$(git rev-parse --abbrev-ref HEAD)

if [[ "$branch" == "master" ]]; then
  hugo
  echo "Syncing public/* with s3://$bucket/$prefix."
  s3cmd --acl-public --delete-removed --no-progress sync public/* s3://$bucket/$prefix
  echo -e "\nUpdated s3://$bucket/$prefix."
else
  echo "*** s3://$bucket/$prefix only syncs when master branch is updated! ***"
fi

exit 0
````

Write a post, commit it to master, let the githook push your updates to s3 after making sure that your public dir is current. You could add a command to clean the public dir before rebuilding (to avoid possible detritus content), but I manage this manually as it does not occur often for me.
