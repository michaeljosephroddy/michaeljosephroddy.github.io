---
layout: post
title: "Understanding Archive Files: .zip vs .tar | Made Simple"
categories: blog programming tar zip
---

Hey, today I'm going to do a short article on compression file types and how to use them on Ubuntu Linux.

So what are compressed files like .tar or .zip files? Well both of these file types are what we would call archive files, they store files as a collection.

To give you an idea of what this looks like please see the visual representation below:

```bash
myCollection.tar
    /path/file1.txt
    /path/file2.png
    /path/file3.go
```

As you can see we have our file called `myCollection` and it is of type `.tar`, the tar file holds the contents of multiple other files of different types. The same goes for `.zip` files.

Maybe another way to imagine this would be using a list, for example imagine that you have a list that can store data of any type and your list is the collection, please see the example below:

```bash
var myCollection <anyFileType> = [file1.txt, file2.png, file3.go]
```

This is essentially what an archive file looks like conceptually using a list for an example.

Now, the key difference here between `.tar` and `.zip` files is the way in which the files are compressed or not compressed we could say.

### Tar files

For example, the `.tar` archive file type is actually an uncompressed collection of files by default. The key thing here is that the `.tar` file itself is not compressed, but you can apply compression algorithms to it. Usually, the `gzip` compression algorithm is used with tar files which results in the `.tar.gz` file extension that you commonly see, although you could use other compression algorithms like `bzip2` or `xz` for example. The compressed file is what we then call the `tarball`.

Basically with the tar file, the entire file is compressed, this makes the file size smaller because all of the data is compressed as a single unit or package, but it makes the data less accessible because to access one file in the tar file, you would need to decompress the entire file first and then access the file you need.

So, tar files are more space efficient but less accessible.

### Zip files

In contrast to tar files, a zip file is a collection of individually compressed files, meaning that the zip file itself is not compressed but the files inside it are individually compressed. So when you zip files you are essentially applying a compression algorithm like `Deflate` which is commonly used with zip format, to each file in the archive file, although again, you can use other compression algorithms like `lzma`, `wavpack` or `ppmd`.

This produces a file with the extension `.zip`, this makes zip files less space efficient because each file is compressed individually so the resulting file size is larger than tar files but the zip file is more accessible, in the sense that you can access each individually compressed file without having to decompress the entire collection.

So, zip files are less space efficient but more accessible.

### Example Usage

Now I will give you a brief example on how to compress and decompress files using both `tar` and `zip` formats. Please see the example below:

#### Using tar

**Compress (create archive)**
```bash
tar -czvf archive.tar.gz file1 file2 folder/
```

c = create  
z = gzip compression  
v = verbose (show files being archived)  
f = filename  

`tar -czvf archive.tar.gz` = create a compressed archive called archive.tar.gz.  
`file1 file2 folder/` = include file1, file2, and everything inside the folder folder/.

So the result is a single file called archive.tar.gz that contains:
- file1
- file2
- All contents of folder/ (including subfolders). This will automatically include everything inside folder/, including subfolders, without needing a special recursive flag.

**Decompress (extract archive)**
```bash
tar -xzvf archive.tar.gz
```

This extracts the contents of archive.tar.gz into the current directory.

#### Using zip

**Compress**
```bash
zip archive.zip file1 file2 folder/ -r
```

`zip archive.zip` = create a compressed archive called archive.zip.  
`file1 file2 folder/` = include file1, file2, and everything inside the folder folder/.  
`-r` (recursive) = makes sure that if folder/ contains subfolders and files, they are all included in the zip.

So the result is a single file called archive.zip that contains:
- file1
- file2
- All contents of folder/ (including subfolders, recursively).

**Decompress**
```bash
unzip archive.zip
```

This extracts the zip file into the current directory.

### Conclusion

So that's it for tar and zip files, I hope you learned something useful from the blog post. The main takeaway here being that tar files are an uncompressed collection of files with the `.tar` extension and where you need to apply a compression algorithm like `gzip` to get your `.tar.gz` compressed tarball. A zip file on the other hand is a collection of individually compressed files, where each file in the zip is compressed individually using some compression algorithm like the ones we listed above. The main trade offs being that tarballs are more memory efficient but less accessible and zip files are less memory efficient but more accessible.

That's all for this blog post.

Thanks for reading, Mike.
