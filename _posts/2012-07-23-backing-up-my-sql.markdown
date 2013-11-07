---
layout: post
title:  "Backing Up MySql - It Shouldn't be this Hard'"
date:   2012-07-23 09:00:00
categories: jekyll update
---
I'm not against scripting or anything, but come on... I don't want to believe that I have to write a script that runs periodically to back up my MySql DB. There should be some way to do it automatically like in MS Sql Server. All of my googling and asking around has led me to believe that I do have to script it out. To do that, I wrote the following Powershell script to run nightly to back up a wiki that I have hosted at work.

{% highlight powershell %}
$date = Get-Date

#where you want your backups to go
$FilePath = "\\YourServer\WikiBackUps\"

#PSDrive creates a drive that is local to the powershell instance
#I did this because I am storing my backups remotely in case the DB server decides to
#go all cocker spaniel and shit itself.
$Success = New-PSDrive -Name J -PSProvider FileSystem -Root $FilePath

if ($Success)
{
  #arbitrary drive name
  $root = "J:\"

  #get all of the .sql files in the directory
  $Files = Get-ChildItem $root -include *.sql

  #HACK: I can't figure out why, but PS errors when there is nothing in the directory...
  if ($Files.count -gt 0)
  {
    foreach ($file in $Files)
    {
      if ($file.LastWriteTime -lt $date.AddDays(-10))
      {
        Write-Host Deleting $file
        Remove-Item $file
      }
    }
  }
}
#point the script at mysqldump.exe which takes your back up
$exe = "D:\wamp\bin\mysql\mysql5.5.24\mysqldump.exe"

#format the file name
$fileName = $FilePath + " dbname_wiki.backup" + $date.ToString("yyyy-MM-ddHHmm") + ".sql"

#run the backup utility
& $exe -u root dbname_wiki --result-file $fileName[/sourcecode]
{% endhighlight %}
