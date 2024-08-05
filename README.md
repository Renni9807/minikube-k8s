# Kubernetes MongoDB Deployment Project

## Overview
This project demonstrates the deployment of a MongoDB instance in a Kubernetes cluster. It showcases the process of transforming a static Docker image into a running container within a Kubernetes pod.

## Deployment Process
When I run `kubectl create deployment mongo-depl --image=mongo`, Kubernetes initiates the following process:
1. Pulls the MongoDB Docker image
2. Creates a deployment, which is managed by the Control Manager
3. The Control Manager creates a ReplicaSet based on the deployment specification
4. A pod is created by the ReplicaSet
5. Transforms the static image into an executable container
6. Initializes the MongoDB process

During initialization, MongoDB generates logs which can be viewed using `kubectl logs`.

## File System Exploration
I used `kubectl cp` commands to explore the file system of the running MongoDB container: sudo kubectl cp mongo-depl-887485654-qwkhf:/ /Users/woosangwon/k8s-project
kubectl cp mongo-depl-887485654-qwkhf:/data /Users/woosangwon/k8s-project/data

## Large File Handling
Upon attempting to push the copied files to Git, I encountered issues with large file sizes. Initially, I suspected that the initialization logs might be the cause. However, further investigation revealed that the executable files and libraries within the MongoDB image were the primary contributors to the large file sizes.

### Understanding Large Files
The MongoDB image contains all necessary files to run a database server, including:
- Executable files (mongod, mongos, mongosh)
- Library files (e.g., mongosh_crypt_v1.so)

These files are essential for the full functionality of MongoDB, allowing operations like user authentication, database creation, and various database operations. While not all of these files are executed during the initial startup, they remain in the container, occupying significant space.

### Attempted Solution
I attempted to remove large files using Git filter-branch: git filter-branch --force --index-filter
"git rm --cached --ignore-unmatch usr/bin/mongod usr/bin/mongos usr/bin/mongosh usr/lib/mongosh_crypt_v1.so"
--prune-empty --tag-name-filter cat -- --all

### Conclusion
The large file sizes are primarily due to the comprehensive nature of the MongoDB image, which includes all necessary components for a fully functional database server. While the initial logs contribute to the file system's content, they are not the main cause of the size issues encountered during Git operations. Despite attempting to use Git LFS (Large File Storage), the push operation still failed due to file size limitations on GitHub's free tier and some files not being properly tracked by LFS.

### Future Considerations
For future development, the following strategies will be considered:

Optimize Git LFS usage:
Ensure all large files are properly tracked by Git LFS.
Review the .gitattributes file to confirm correct LFS configurations.
Repository cleanup: Use tools like 'git filter-branch' or 'BFG Repo-Cleaner' to remove large files from Git history. Add large binary files (like mongod, mongos, mongosh) to .gitignore to prevent accidental commits.

Alternative storage solutions:
Explore GitHub's paid tiers or other Git hosting services that offer larger file size limits.
Consider using external artifact repositories for large binary files.
Integration of cloud storage services with Git: Integration of Amazon S3 with Git LFS
Minimize container image size:
Selectively copy only necessary files from the container to reduce overall repository size.
Use multi-stage builds to create smaller, more focused container images.
Documentation and scripts:
Instead of including large binaries, provide documentation on how to obtain and set up required components.
Create scripts to automate the download and setup of necessary large files during deployment.
