---
layout: default
title: Home
---

----

## Find and Replace Across Multiple Files

### 1. Use sed

    sed -i 's/foo/foo_bar/g'  *.html

### 2. use the old perl hack

    perl -w -pi~ -e 's/foo/bar/' [files]

Notes: -p: loop, -i edit files in place (backup with extension if supplied), -w enable warnings

### Combine with find

Combining either (1) or (2) with *find* is pretty powerful. E.g. to do a find and replace on all html files in all subdirectories:
    
    perl -w -pi -e 's/foo/bar/' `find <path> -name '*.html'`

----

## Find and Remove Broken Symbolic Links

     find -L ${directory} -type l 
     # remove the broken links
     find -L ${directory} -type l -print0 | xargs -0 rm 

----

## Bulk rename files

For example to change files with extension mkd to rst:

    find . -name "*.mkd" | sed "s/\(.*\).mkd/mv \1.mkd \1.rst/g" | sh

----

## Extract every nth line of a file (sed)

Extract every 4th line starting at line 0:

    sed -n '0~4p' filepath

## Delete the first or nth line of a file (sed)

    # first line
    sed '1d' filepath

    # last line
    sed '$d'  filepath
    
    # 10th line ...
    sed '10d' filepath
    
    # remove 7-12th line
    sed '7,12d' filepath

----

## s3cmd

    # does not seem to auto-detect file type w/o prompting
    s3cmd put --guess-mime-type --acl-public *.css s3://your-bucket/your-dir/

----

## Mercurial

### Stash working copy changes

1\. Use shelve extension

2\. Use Mercurial Queues (MQ)

    # -f needed as we have local changes
    hg qnew -f patch
    hg qpop

    # later
    hg import --no-commit .hg/patches/patch
    hg qdelete patch

3\. Or without MQ:

    hg diff > patch
    hg update -C .

then import the patch later ...

