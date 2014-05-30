{
  "title": "The Cleaner: A Bash Script to Delete Old Backup Files",
  "description": "The Cleaner: A Bash Script to Delete Old Backup Files",
  "date": "2011-02-11",
  "url": "the-cleaner-a-bash-script-to-delete-old-backup-files",
  "type": "post",
  "tags": [
    "linux",
    "scripts"
  ]
}
I wrote this backup directory cleaner a while ago, and I use it quite often. When I originally looked around for a solution, I could not find any existent scripts that really suited my needs. Cleaning directories is a pretty straight-forward need, and I assume that most people have something like this sitting around or just write something a little simpler whenever they need it. Nonetheless, since I put this together, and I use it regularly, I thought I would make it available here.

This script basically lets you choose a string to match, and delete the files matching that string until only a certain number are left. There is also a -q (quiet) flag, so that you can use the script in crontab (the default is verbose AND requires user confirmation). You can also pass it a directory from which to delete files; the default directory is the current working directory.

You can see some examples and read a little bit more in the help in the file contents, or by executing:
<pre>
./cleaner.sh -h
</pre>

#### cleaner.sh

<script src="https://gist.github.com/2727200.js?file=cleaner.sh"></script>

TODO - add a -l flag that takes a parameter. "-l" indicates that you want to log data, and the parameter is the file in which logs are added. This allows users with more abstract cleaning efforts to log periodically delete and keep a log of how many files were deleted and what parameters were used (or something to that effect). Most of my cleaning needs involved daily backups of files or databases, and so the logs would be extremely predictable. But I can see a use for this, and _may_ update the script soon to accommodate this case.

I hope this saves someone a few minutes of searching and script authoring.  Let me know if you have other needs that could be easily added, or alternative implementations that I should consider.
