# Command Line Zen

## Table of Contents

* [Find and replace across multiple files](#find-and-replace-across-multiple-files)
    * [1. Use sed](#1-use-sed)
    * [2. Use the old perl hack](#2-use-the-old-perl-hack)
    * [Combining with find](#combining-with-find)
* [Find and remove broken symbolic links](#find-and-remove-broken-symbolic-links)
* [Bulk rename files](#bulk-rename-files)
* [Count number of files in directories](#count-number-of-files-in-directories)
* [Working with sed](#working-with-sed)
    * [Delete a range of lines](#delete-a-range-of-lines)
    * [Delete the first or nth line of a file](#delete-the-first-or-nth-line-of-a-file)
    * [Deleting lines matching a pattern in a given file](#deleting-lines-matching-a-pattern-in-a-given-file)
    * [Extract every nth line of a file](#extract-every-nth-line-of-a-file)
    * [Replacing occurrences of a pattern](#replacing-occurrences-of-a-pattern)
* [s3cmd](#s3cmd)
* [curl](#curl)
    * [Piping uploads](#piping-uploads)
* [convert - ImageMagick](#convert---imagemagick)
    * [Change background colour](#change-background-colour)
    * [Convert to black and white](#convert-to-black-and-white)
    * [Invert colours](#invert-colours)
    * [Make square (for thumbnailing)](#make-square-for-thumbnailing)
    * [Optimizing images](#optimizing-images)
    * [Removing edges](#removing-edges)
    * [Rotation](#rotation)
    * [Scale image](#scale-image)
* [Mercurial](#mercurial)
    * [Stash working copy changes](#stash-working-copy-changes)

**Command line tips and tricks**. Contributions are welcome: just fork
the file and submit a pull request.

----

## Find and replace across multiple files

### 1. Use sed

    sed -i 's/foo/foo_bar/g' *.html

### 2. Use the old perl hack

    perl -w -pi~ -e 's/foo/bar/' [files]

**Notes**: `-p`: loop; `-i` edit files in place (backup with extension
if supplied); `-w` enable warnings.

### Combining with find

Combining either **(1)** or **(2)** with *find* is pretty powerful. E.g.
to do a find and replace on all `html` files in all subdirectories:

    perl -w -pi -e 's/foo/bar/' `find <path> -name '*.html'`

----

## Find and remove broken symbolic links

     find -L ${directory} -type l

     # remove the broken links
     find -L ${directory} -type l -print0 | xargs -0 rm

----

## Bulk rename files

For example to change files recursively with extension `mkd` to `rst`:

    find . -name "*.mkd" | sed "s/\(.*\).mkd/mv \1.mkd \1.rst/g" | sh

A simple loop can be used to target only the working directory:

    # rename .log files in the working directory to .bak
    for j in *.log; do mv -- "$j" "${j%.log}.bak"; done

Similarly, we can target directories recursively with a loop without
including the working directory:

    # rename .log files recursively (skipping the working directory) to .bak
    for j in **/*.log; do mv -- "$j" "${j%.log}.bak"; done

----

## Count number of files in directories

In a single directory:

    ls -l | wc -l

In all subdirectories of a given directory:

    find . -type f | wc -l

----

## Working with sed

### Delete a range of lines

    # remove from 3rd line to the end of the file
    sed '3,$d' filepath

### Delete the first or nth line of a file

    # first line
    sed '1d' filepath

    # last line
    sed '$d'  filepath

    # 10th line ...
    sed '10d' filepath

    # remove 7-12th line
    sed '7,12d' filepath

### Deleting lines matching a pattern in a given file

    # the flag "d" is added to specify the operation
    # (the output appears, the file remains intact)
    sed '/pattern/d' filepath

    # to modify the file in place, add the "-i" flag
    sed -i '/pattern/d' filepath

    # same as previous command, but case insensitive
    sed -i '/pattern/Id' filepath

### Extract every nth line of a file

Extract every 4<sup>th</sup> line starting at line 0:

    sed -n '0~4p' filepath

### Replacing occurrences of a pattern

    # replace 'pattern' with 'new_pattern' when it appears for the
    # second time ('2' is the 'nth' occurrence desired)
    sed 's/pattern/new_pattern/2' filepath

    # same as the previous command, but replace greedily the following
    # occurrences up to the end of the line by adding the 'g' flag,
    # starting from the 'nth' occurrence
    sed 's/pattern/new_pattern/2g' filepath

    # we can accomplish the same as above, limiting instead our search
    # to a range of lines in the file
    # (here, from line 3 to the end of the file)
    sed '3,$ s/pattern/new_pattern/' filepath

----

## s3cmd

    # does not seem to auto-detect file type w/o prompting
    s3cmd put --guess-mime-type --acl-public *.css s3://your-bucket/your-dir/

----

## curl

curl is *awesome*. It connects Unix command line Zen with the wide open
world of the Internet.

### Piping uploads

This is pretty cool...

    curl http://example.com/down | curl -T - ftp://mysite.org/up

The -T option is very powerful - here's the man page section in its
entirety:

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

----

## convert - ImageMagick

### Change background colour

    # make the given colour (e.g. here white) transparent
    convert -transparent white {in} {out}

    # make transparent white
    convert -fill white -opaque none {in} {out}

### Convert to black and white

    convert -type Grayscale {in} {out}
    convert -monochrome {in} {out}

### Invert colours

    convert -negate in out

### Make square (for thumbnailing)

    convert -background transparent -gravity center -extent 145x145 file1 file2

### Optimizing images

To save disk space and make files quicker to load on the web, the
following commands will prove very useful (originally from
[this Gist](https://gist.github.com/rkbhochalya/d3557a9d122ab547c040af3adbd565c2)).

    # Optimize all PNG images recursively
    # Print the name of the file being processed
    find . -name "*.png" -exec convert "{}" -strip "{}" \; -exec echo "{}" \;

    # Optimize all JPEG images recursively
    find . -name "*.jpg" -exec convert "{}" -sampling-factor 4:2:0 -strip \
    -quality 85 -interlace JPEG -colorspace RGB "{}" \; -exec echo "{}" \;

The gains, especially for `JPEG` images, can be quite substantial: the file size can be halved while keeping 85% of the original quality.

### Removing edges

`convert` provides a somewhat overwhelming number of ways to do this
including `chop`, `crop` and more. The preferred method, I think, is
`crop`.

Remove top 10px of an image:

    convert -crop +0+10 +repage {in} {out}

Remove right 10px of an image:

    convert -crop -10+0 +repage {in} {out}

Remove bottom 10px of an image:

    convert -crop +0-10 +repage {in} {out}

Remove left 10px of an image:

    convert -crop +10+0 +repage {in} {out}

### Rotation

    convert -rotate {degrees} {in} {out}

### Scale image

    convert -scale 10% {in} {out}

----

## Mercurial

### Stash working copy changes

1. Use shelve extension

2. Use Mercurial Queues (MQ)

```
    # -f needed as we have local changes
    hg qnew -f patch
    hg qpop

    # later
    hg import --no-commit .hg/patches/patch
    hg qdelete patch
```

3. Or without MQ:

```
    hg diff > patch
    hg update -C .
```

Then import the patch later...

