# Compression and File Movement

## Rsync

Using rsync is better than tar or zip and scp because it only copies over changes. Existing files that have not been modified don’t move over and therefore this is much faster.

```bash
#! below works for files and directories
> rsync -zavP path/to/file user@ip_address:~/path/to/location
> rsync -zavP path/to/dir user@ip_address:~/path/to/location
> rsync -zavP path/to/dir/ user@ip_address:~/path/to/location
> rsync -zavP path/to/dir/ path/to/other/dir

#! examples:
> rsync -zavP files/ brandonkessler@100.00.111.55:~/files
> rsync -zavP files brandonkessler@100.00.111.55:~/.
```

### Path Differences

* By adding the “/“ at the end of the path in the third command, it will only move the files within the directory as opposed to the entire directory itself.
	* The first example moves all files/folders within files in the files folder saved in the remote server
	* The second example moves the entire files folder into the root directory of the remote server

### Flags

* The -z flag compresses the file on send and will decompress upon delivery
* The -a flag stands for archive and syncs recursively while preserving symbolic links, device files, modification times, permissions, etc. Much better than the -r flag.
* The -v flag stands for verbose and logs which files are being sent
* The -P flag shows progress of the files being moved

### Other Options

Exclusion patterns can be used to exclude specific files that follow a pattern, we can also utilize include patterns to override those exclusion patterns:

```bash
> rsync -a --exclude=pattern_to_exclude source destination
> rsync -a --exclude=pattern_to_exclude --include=pattern_to_include source destination
```

There is also the ability to back these files up into a backups directory:

```bash
> rsync -a --delete --backup --backup-dir=/path/to/backups /path/to/source destination
```
