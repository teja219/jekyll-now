---
layout: post
title: A Top Down approach to learn Git
---

Git is very popular version control tool, but many develpers do not dive into the design aspects and directly dive into the abstractions(ie. git commands) it provides and try to use it. This approach is not very beneficial when we are trying to fix any git problems. To summarize we have the below meme

![_config.yml]({{ site.baseurl }}/images/gitMeme.png)

## Git Objects
At a high level git is a folder tracking tool, the topmost folder is called as root or a repo.
### Git Blobs
Within the root folder we have a set of files or folders, any file or folder is called a blob in git terminology.So a blob can be a single file, a set of files and folders, or a set of folders. A blob when converted to byte code is just an array of bits. Git internally computer the hash of the bits array and uses it while denoting the blob. We will discuss later as to where this gets used.

You can visualize a blob with the below datastructure
```javascript
Struct Blob {
  List<Blob> blobs,
  String hash
}
```

### Git Snapshots
Git snapshots our code everytime we make a change , each snapshot is made up of a list of blobs. In addition to a set of blobs, it git snapshot contains some metadata related to the snapshot like , who created the snapshot, purpose of creating the snapshot etc. In git a Snapshot is called as a commit.

```javascript
Struct Snapshot {
  List<Blob> blobs,
  String hash,
  String author
  String message
}
```
If you notice the above definitions for Snapshot and Blobs, we are duplicating a lot of content, for example assume a file in the root folder, it will be present in both the snapshot object and under a blob object. Similarly any file will be present in multiple objects.So instead git maintains a map Map<HashCode,Blob> and we just store the hashcodes in the Snapshot and Blob objects. So the above defn's can be refactored as , assuming hashCode is in String format

```javascript
Struct Blob {
  List<String> blobHashes,
  String hashOfAllSubBlobs
}
```
```javascript
Struct Snapshot {
  List<String> blobHashes,
  String hashOfAllSubBlobs,
  String author
  String message
}
```

## Summing Up
Everytime we make a change in Git by commiting it created a new Snapshot object which contains pointers to the Blobs within it.


The easiest way to make your first post is to edit this one. Go into /_posts/ and update the Hello World markdown file. For more instructions head over to the [Jekyll Now repository](https://github.com/barryclark/jekyll-now) on GitHub.
