seek
----

*seek* is a drop-in replacement for `locate`.

It creates an index with `find` and compresses the result down with the specified compression tool. It allows for fast searching of files with the specified search tool. By default these are `lz4` and `grep` by default for speed and size. It also uses `/tmp` by default to help with both of those. An index is created for each user, which means the privacy relies solely on the permissions given to the index directory and file (and of course the files you plan on indexing).
