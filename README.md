# remote-comm-yakkety
Simple Ubuntu 16.10 image with various packages installed for remote server management. Meant to be deployed on any cloud instance and populated with SSH keys and credentials to manage Kubernetes, `git` repos, etc.

## What's included
* `kubectl`
* `gcloud`
* `gcsfuse`

## Usage
* Run `gcloud init` to get started with Google Cloud

### Cloud Storage FUSE
**Note: Run these commands on the host**
* You'll need [Google Cloud SDK](https://cloud.google.com/sdk/docs/#install_the_latest_cloud_tools_version_cloudsdk_current_version "Google Cloud SDK Documentation"), [`gcsfuse`](https://github.com/GoogleCloudPlatform/gcsfuse/blob/master/docs/installing.md "gcsfuse/installing.md at master · GoogleCloudPlatform/gcsfuse") and [Docker Community Edition](https://store.docker.com/search?type=edition&offering=community "Docker Store")
  * Run the `tools-*.sh` script for your host's distribution, e.g:
  ```bash
  chmod +x tools-ubuntu-debian.sh
  ./tools-ubuntu-debian.sh
  ```
* Obtain Application Default Credentials for `gcsfuse`
  * `gcloud auth application-default login`
  * Or use a [JSON file](https://developers.google.com/identity/protocols/application-default-credentials#howtheywork "How the Application Default Credentials work")
* Mount a GCS Bucket `gcsfuse -o allow_other my-bucket /mnt/my-bucket`.
  * You may need to create your directory, give it permissions, and also edit the `/etc/fuse.conf` file to enable the `allow_other` option.

### Secrets and Credentials
* Create a `Secret` directory in you bucket and encrypt it with `ecryptfs`
  ```bash
  cd /mnt/my-bucket
  mkdir -m 700 .secret
  mkdir -m 500 secret
  sudo mount -t ecryptfs .secret/ secret/
  ```
* Go through the setup steps and write down your chosen passphrase. You can unmount and re-encrypt your secrets with `sudo umount /mnt/my-bucket/secret/`
* Start a container with the bucket mounted
  `docker run -it -v /mnt/my-bucket:/mnt/my-bucket chepurko/remote-comm-yakkety:v1.0.0 /bin/bash`
