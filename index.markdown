---
layout: default
title: Home
---

<div class="description">
  <p><strong>Command line tips and tricks</strong>. Contributions are welcome: just <a href="https://github.com/rgrp/cli-zen/blob/gh-pages/index.markdown">fork the file</a> and submit a pull request.</p>
</div>

* This will become a table of contents (this text will be scraped).
{:toc}

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

## Count Number of Files in Directories

In a single directory:

    ls -l | wc -l

In all subdirectories of a given directory:

    find . -type f | wc -l

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

-----------------------------------------------------------

## curl

curl is *awesome*. It connects unix command-line zen with the wide open world
of the Internet.

### Piping uploads

This is pretty cool ...

    curl http://example.com/down | curl -T - ftp://mysite.org/up

The -T option is very powerful - here's the man page section in its entirety:

     -T/--upload-file <file>

        This transfers the specified local file to the  remote  URL.  If
        there is no file part in the specified URL, Curl will append the
        local file name. NOTE that you must use a trailing / on the last
        directory  to really prove to Curl that there is no file name or
        curl will think that your last directory name is the remote file
        name to use. That will most likely cause the upload operation to
        fail. If this is used on a HTTP(S) server, the PUT command  will
        be used.

        Use  the file name "-" (a single dash) to use stdin instead of a
        given file.  Alternately, the file name "."  (a  single  period)
        may  be  specified  instead  of "-" to use stdin in non-blocking
        mode to  allow  reading  server  output  while  stdin  is  being
        uploaded.

        You can specify one -T for each URL on the command line. Each -T
        + URL pair specifies what to upload and to where. curl also sup‐
        ports "globbing" of the -T argument, meaning that you can upload
        multiple files to a single URL by using the  same  URL  globbing
        style supported in the URL, like this:

        curl -T "{file1,file2}" http://www.uploadtothissite.com

        or even

        curl -T "img[1-1000].png" ftp://ftp.picturemania.com/upload/

-----------------------------------------------------------

## convert (ImageMagick)

    # convert image
    convert -scale 10% {in} {out}

    # convert to black and white
    convert -type Grayscale {in} {out}
    convert -monochrome {in} {out}

    # invert colours
    convert -negate in out

    # rotation
    convert -rotate {degrees} {in} {out} 

    # change background color

    # make the given colour (e.g. here white) transparent
    convert -transparent white {in} {out}
    # make transparent white
    convert -fill white -opaque none {in} {out}


    # Make square (for thumbnailing)
    convert -background transparent -gravity center -extent 145x145 file1 file2

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

