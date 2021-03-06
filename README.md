# imperialwicket.com blog

This is the imperialwicket.com blog, powered by [Hugo](https://github.com/spf13/hugo/), hosted on S3/Cloudfront.

### Git hook

A post-commit like this works great for me, since everytime I commit to master, I want s3 to sync:

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

Only updating in master lets me investigate template updates and overhauls in alternate branches without worrying about stray deploys.
