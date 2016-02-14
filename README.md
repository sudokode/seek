seek
----

**seek** is a drop-in replacement for `locate`.

It creates an index with `find` and compresses the result down with the specified compression tool. It allows for fast searching of files with the specified search tool. By default these are `lz4` and `grep` by default for speed and size. It also uses `/tmp` by default to help with both of those. An index is created for each user, which means the privacy relies solely on the permissions given to the index directory and file (and of course the files you plan on indexing).

Dependencies
============

- A shell (sh)
- coreutils
- findutils (could use something else for this :D )
- grep (default search tool)
- lz4 (default compression tool)

Usage
=====

`updatedb` - Updates the index for the current user
`locate <search>` - Searches for filenames containing <search>

By default, this uses `grep` to search, so anything `grep` can do, `locate` can do as well. All arguments to `locate` are passed to the search tool.
