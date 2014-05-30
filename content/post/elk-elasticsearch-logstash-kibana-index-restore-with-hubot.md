{
  "title": "ELK (Elasticsearch, Logstash, Kibana) index restore with Hubot",
  "description": "ELK (Elasticsearch, Logstash, Kibana) index restore with Hubot",
  "date": "2014-04-18",
  "url": "elk-elasticsearch-logstash-kibana-index-restore-with-hubot",
  "type": "post",
  "tags": [
    "logstash",
    "elasticsearch",
    "hubot",
    "elk"
  ]
}
After getting ELK (Elasticsearch, Logstash, Kibana) up and running, an early challenge is managing your indices. I'm assuming Elasticsearch 1.0.0 and greater, and given that version the API provides dead simple mechanisms for everything from closing/deleting indices to snapshots and restore procedures (closure/delete have been around since pre-1.0.0, snapshot/restore is 1.0.0 and newer).

It's easy to put together a quick shell script and cron job to manage the regular tasks (I did: [elasticsearch-logstash-index-mgmt](https://github.com/imperialwicket/elasticsearch-logstash-index-mgmt)). However, consistency is important, and now that [Elasticsearch](http://www.elasticsearch.org) is managing the entire ELK stack, their own [Curator](https://github.com/elasticsearch/curator) is worth careful investigation.

### Restoring daily indices in Elasticsearch

After you have data flowing into Elasticsearch, it starts grow and before you know it, you need some script to back up yesterday's index, move indices/shards from SSD to slower disks, close indices that should not be using system resources, and delete indices that you no longer want taking up precious disk space. But now your finely tuned logging machine needs to support random developer requests for data that's not in the elasticsearch cluster. [Elasticsearch snapshot restore](http://www.elasticsearch.org/guide/en/elasticsearch/reference/1.x/modules-snapshots.html) is your new best friend. After restoring a handful of indices, you realize that anyone should be able to do this without bothering you. [Hubot-elk-restore](https://github.com/imperialwicket/hubot-elk-restore).

### Hubot ELK Restore

If you aren't using [Hubot](https://hubot.github.com/) (or some chat bot), you should be. Installation and configuration are detailed on [Github](https://github.com/imperialwicket/hubot-elk-restore) or [npm](https://www.npmjs.org/package/hubot-elk-restore). Now, your users can help themselves. With a few checks to be certain disk space isn't going to be a problem when a developer goes wild restoring indices, you no longer need to lift a finger when an index requires restoration.

Guide your users as follows:

<pre>
me> hal help elk
hal> elk close <yyyy.mm.dd> - Close a particular index
elk indices - Show summary of indices currently available
elk restore <yyyy.mm.dd> - Restore a particular index
elk snapshots - Show summary of snapshots available
me> elk indices
hal> ELK indices: logstash-2014.04.05 - logstash-2014.04.18
me> elk snapshots
hal> Elk snapshots: logstash-2014.04.04 - logstash-2014.04.07
me> elk restore 2014.04.04
hal> Restoring logstash-2014.04.04.
me> elk indices
hal> ELK indices: logstash-2014.04.04 - logstash-2014.04.18
me> elk close 2014.04.04
hal> Closing logstash-2014.04.04.
me> elk indices
hal> ELK indices: logstash-2014.04.05 - logstash-2014.04.18
</pre>

At a glance, users can check the currently loaded indices, see if a target index is available in the configured snapshots, and restore that index. Users can optionally close the index after a search is completed (saving system resources) but deletes are left to be managed by your daily scripts.
