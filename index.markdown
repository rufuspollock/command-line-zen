## Find and Replace Across Multiple Files

1\. Use sed

    sed -i 's/foo/foo_bar/g'  *.html

2\. use the old perl hack

    perl -w -pi~ -e 's/foo/bar/' [files]

Notes: -p: loop, -i edit files in place (backup with extension if supplied), -w enable warnings

Combining either (1) or (2) with *find* is pretty powerful. E.g. to do a find and replace on all html files in all subdirectories:
    
    perl -w -pi -e 's/foo/bar/' `find <path> -name '*.html'`


## Find and Remove Broken Symbolic Links

     find -L ${directory} -type l 
     # remove the broken links
     find -L ${directory} -type l -print0 | xargs -0 rm 

## Bulk rename files

For example to change files with extension mkd to rst:

<pre>
find . -name "*.mkd" | sed "s/\(.*\).mkd/mv \1.mkd \1.rst/g" | sh
<pre>

## s3cmd

a: 2010-06-17
t: s3cmd css

    # does not seem to auto-detect file type w/o prompting
    s3cmd put --guess-mime-type --acl-public *.css s3://your-bucket/your-dir/

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

<pre>
hg diff > patch
hg update -C .
</pre>

then import the patch later ...
