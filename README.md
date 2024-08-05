# Project Update: Resolving Git LFS Issues

## Changes Made

1. Initialized Git LFS for the repository.
2. Tracked large files using Git LFS:
   - data/db/journal/WiredTigerPreplog.0000000001
   - data/db/journal/WiredTigerLog.0000000001
   - usr/bin/mongod
   - usr/bin/mongos
   - usr/bin/mongosh
   - usr/lib/mongosh_crypt_v1.so

3. Removed large MongoDB-related files from Git history to resolve file size limit issues.

## Resolution Process

1. Removed large files from Git history:
git filter-branch --force --index-filter
"git rm --cached --ignore-unmatch usr/bin/mongod usr/bin/mongos usr/bin/mongosh usr/lib/mongosh_crypt_v1.so"
--prune-empty --tag-name-filter cat -- --all

2. Successfully pushed changes to the repository after removing large files from Git history.

## Note

- MongoDB-related large binary files were excluded from the repository to comply with GitHub's file size limits.
