{
  "title": "Elasticsearch Logstash index backup, restore, rotate",
  "description": "Elasticsearch Logstash index backup, restore, rotate",
  "date": "2013-05-21",
  "url": "elasticsearch-logstash-index-backup-restore-rotate",
  "type": "post",
  "tags": [
    "scripts",
    "logstash",
    "elasticsearch"
  ]
}
UPDATE: I moved these scripts to the [elasticsearch-logstash-index-mgmt repository](https://github.com/imperialwicket/elasticsearch-logstash-index-mgmt).

Here are a set of [scripts for managing Logstash indices in elasticsearch](https://github.com/imperialwicket/elasticsearch-logstash-index-mgmt). This set of scripts is inspired heavily by [a previous collection](http://tech.superhappykittymeow.com/?p=296). The general concept is that I do not want to keep all my logs in elasticsearch, when it is highly likely that I will only be searching for logs from the past couple of days. Nonetheless, I want to persist a much longer time period, and I want it to be easy to bring back a particular index or group of indices. With these scripts, I can. Backups and restores are handled independently, so if you merely want to get rid of an index after some period of time, you can just run the removal script. Backup/restore efforts default to using S3 with s3cmd, but you can hack around this if you prefer to maintain local-only (or scp, or whatever).

I tested these and they are working for me, but I'm not responsible if you delete your data.

My setup runs the backup script around 1 AM and backs up the index from yesterday. I run the remove script at 2 AM (plenty of time for a very nice tar command to complete in my case) and keep the most recent 14 indices around. I have a ch
atbot configured to restore based on date with the restore script. If something happens and I need to investigate logs from many weeks ago, I have the chatbot hit the elasticsearch node and run a restore for that date; these back-dated in
dices are automatically wiped the next morning when the remove script runs.

I'm happy to make changes if bugs are spotted, and if there is interest I can move these to a proper repo. Happy logstashing!
