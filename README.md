seek
====

`seek` is a drop-in replacement for `locate`.

It creates an index with `find` and compresses the result down with the specified compression tool. It allows for fast searching of files with the specified search tool. By default these are `lz4` and `grep`, respectively, for speed and size. It also uses `/tmp` by default to help with both of those. An index is created for each user, which means the privacy relies solely on the permissions given to the index directory and file (and of course the files you plan on indexing).

Dependencies
------------

- A shell (sh)
- coreutils
- findutils (could use something else for this :D )
- grep (default search tool)
- lz4 (default compression tool)

Usage
-----

- `updatedb` - Updates the index for the current user
- `locate <search>` - Searches for filenames containing \<search>

By default, this uses `grep` to search, so anything `grep` can do, `locate` can do as well. All arguments to `locate` are passed to the search tool.

Reasoning
---------

I've always thought `locate` was rather useless.

**Size and speed**:

find:  
`find / -iname '*.desktop' 2>/dev/null`  
Size: 0MiB  
Time: 2.869

mlocate:  
`locate *.desktop`  
Size: 33MiB  
Time: 0.568

seek:  
`locate '\.desktop$'`  
Size: 9.8MiB  
Time: 0.331

**Accuracy**:

find:  
`find / -iregex 'p(or|r0)n$' 2>/dev/null`  
Results: *none*

mlocate:  
`locate -r 'p(or|r0)n$'`  
Results: *none*

seek:
`locate -E 'p(or|r0)n$'`
Results: `/home/zoidberg/lobsterpr0n`

Caveats
-------

- With more than a few users, using one index per user makes the size required more than `mlocate`, but this could be remedied with a global index. The problem there is `mlocate` honors permissions, while `seek` relies on each index containing only what the user would be allowed to view with `find`. This is a design choice in the end.

- Some cute features of `locate` are missing, like `--basename`. That could possibly be handled with regex.
