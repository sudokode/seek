seek
====

`seek` is a "drop-in" replacement for `locate`. Its sole purpose is reducing the size of the index and the time spent updating and searching it while allowing for more advanced searches.

It creates an index with `find` and compresses the result down with the specified compression tool. It allows for fast searching of files with the specified search tool. By default these are `zstd` and `grep`, respectively, for speed and size. It also uses `/tmp` by default to help with both of those. An index is created for each user, which means the privacy relies solely on the permissions given to the index directory and file (and of course the files you plan on indexing).

Dependencies
------------

- A shell (sh)
- coreutils
- findutils (could use something else for this :D )
- grep (default search tool)
- zstd (default compression tool)

`grep` is very robust as search tool. `awk` might also be an interesting choice. There is currently no way to override these except by modifying.

`zstd` seems to be the best choice for this sort of fast, on-demand compression. The decompression time is just untouchable. The problem with these not being overridable is that you have to change both files for the the compression tool. Usually to add a decompress flag, but that's not always true... I still prefer the idea of having the option there; flags can easily be added. This thing just needs to listen to some environment variables. Beware of suppressing `lz4` or `zstd` stdout. It breaks the output.

Usage
-----

- `updatedb` - Updates the index for the current user
- `locate <search>` - Searches for filenames containing \<search>

By default, this uses `grep` to search, so anything `grep` can do, `locate` can do as well. All arguments to `locate` are passed to the search tool.

Updating
--------

`seek` will attempt to run `updatedb` if it cannot find the index.

Since updating is a very inexpensive process, it's easily done with cron, which also makes it easy to determine which user's index is to be updated automatically.

Run:  
`crontab -e`

Enter:  
`42 * * * * updatedb`

That will update the user's index every hour (42 minutes past).

Reasoning
---------

I've always thought `locate` was rather useless.

**Size and speed**:

find:  
`find / -name '*.desktop' 2>/dev/null`  
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

*(Size refers to the index file, not the memory used.)*

**Accuracy**:

find:  
`find / -iregex 'p(or|r0)n$' 2>/dev/null`  
Results: *none*

mlocate:  
`locate -ir 'p(or|r0)n$'`  
Results: *none*

seek:  
`locate -iE 'p(or|r0)n$'`  
Results: `/home/zoidberg/lobsterpr0n`

So why is `locate` a thing? It seems like it was designed for an earlier time, but doesn't really meet its goal even for then. It has a large disk footprint, isn't stored in a tmpfs by default, is slow at searching, doesn't support extended regex (or any others), and always seems like just another thing a sysadmin has to set up. `find` works just fine, but if you wanna do regex with it, it might be best to use `-exec grep -E /foo/ {} \;`.

Or if you prefer a very simple `locate` that doesn't get in the way, let the user handle it by adding `updatedb` to their cron without worrying about permissions. If they wanna modify how it works, they can copy it to their home. It's just a shell script, and the index is just a simple compressed text file.

Caveats
-------

- With more than a few users, using one index per user makes the size required more than `mlocate`, but this could be remedied with a global index. The problem there is `mlocate` honors permissions, while `seek` relies on each index containing only what the user would be allowed to view with `find`. This is a design choice in the end.

- Some cute features of `locate` are missing, like `--basename`. That could possibly be handled with regex.

- `locate` is actually slightly faster than `seek` when updating the index, but when you consider the index is bigger, `locate` is slower at searching, and `seek` is more robust since it just uses `grep`, the difference is rather neglible.
