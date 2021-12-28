---
layout: post
title: A Top Down approach to learn Git
---

Git is very popular version control tool, but many develpers do not dive into the design aspects and directly dive into the abstractions(ie. git commands) it provides. This approach is not very beneficial when we are trying to fix any git problems. So we try to understand git in a top down approach by first understanding the design and then going into the abstractions it provides. Basically our goal is not to end up in the below state.

![_config.yml]({{ site.baseurl }}/images/gitMeme.png)

## Git Objects
At a high level git is a folder tracking tool, the topmost folder is called as root or a repo.Git internally creates objects to track changes and there are mainly 2 types of objects, snapshots and blobs
### Git Blobs
Within the root folder we have a set of files or folders, any file or folder is called a blob in git terminology. So a blob can be a single file, a set of files and folders, or a set of folders. A blob when converted to byte code is just an array of bits. Git internally computes the hash of the array of bits and uses it while denoting the blob.If you are not familiar with Hash function, you can think it is as a function which converts a large amount of data into a fixed size output which is unique for the input passed to the function.We will discuss as to why we need to compute the hash in later in the post.

You can visualize a blob with the below datastructure
```javascript
Struct Blob {
  List<Blob> blobs,
  String hash,
  String blobName
}
```

### Git Snapshots
Git snapshots our code everytime we make a change , each snapshot is made up of a list of blobs. In addition to a set of blobs, git snapshot contains some metadata related to the snapshot like who created the snapshot, purpose of creating the snapshot, pointer to the parent snapshot etc. In git terminology a snapshot is called as a commit.

```javascript
Struct Snapshot {
  List<Blob> blobs,
  String hash,
  String author,
  String message,
  List<Snapshot> parentSnapshots // The reason we are using array is the , there can be 2 parents when we are merging.
}
```
If you notice the above definitions for Snapshot and Blobs, we are duplicating a lot of content, for example assume a file in the root folder, it will be present in both the snapshot object and under a blob object. Similarly any file will be present in all its parent objects.So instead git maintains a map for every blob objects *Map<HashCode,Blob>* where the Key is the hascode of the Blob, here the hashcode can be thought of as a pointer to the blob. The above defn's for Snapshot and Blob can be refactored and instead of storing the complete objects we store the hascodes of its children blobs.

```javascript
Struct Blob {
  List<String> childBlobHashes,
  String hashCode // Usually this is computed using = hash( blobName + childBlob1 + childBlob1 + ..),
  String blobName
}
```
```javascript
Struct Snapshot {
  List<String> childBlobHashes,
  String hashCode,//Usually this is equal to hash( childBlobHashes )
  String author,
  String message,
  List<String> parentSnapshotHashes 
}
```
Example: 

Assume you have the following directory structure
+ root
  - file1
  - folder1
     + file2

Git will create a map( Note: The below hash values are made up )
| Key | Value |
|-------|--------|
| 4aesd | file1 |
| 3adfd | file2 |
| 54def = hash(3adfd+"folder1") | folder1 |
| 456ef = hash(54def+54def) | root |

The below are the objects created by git
``` javascript
Snapshot{
  blobHashes = [4aesd,54def],//Hash for file1, Hash for folder1
  hashOfAllSubBlobs = 456ef,
  author = arumat,
  message = "first commit",
  parentSnapshotHashes = []
}
```
``` javascript
Folder1_Blob {
  blobHashes = [3adfd]
  hashOfAllSubBlobs = 54def,
  blobName = "Folder1"
}
```
## Summing Up
Everytime we make a change in Git by commiting, it creates a new Snapshot object which contains pointers to the Blobs within it.Everytime a file is changed a new entry gets added in the map


The easiest way to make your first post is to edit this one. Go into /_posts/ and update the Hello World markdown file. For more instructions head over to the [Jekyll Now repository](https://github.com/barryclark/jekyll-now) on GitHub.
