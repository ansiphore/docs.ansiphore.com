# Build and deploy

Strart from [v2.8.0](https://github.com/ansible-semaphore/semaphore/releases/tag/v2.8.0) Semaphore supports `build` and `deploy` tasks. You can use variable `semaphore_task_type` (or environment variable `SEMAPHORE_TASK_TYPE`) in your Ansible tasks to get what type of task is run.

### Build

This type of task is used to create [artifacts](https://en.wikipedia.org/wiki/Artifact\_\(software\_development\)). Each build task has autogenerated version. You should use variable `semaphore_task_version` (or environment variable `SEMAPHORE_TASK_VERSION`) in your Ansible playbook to get what version of the artifact should be created. After the artifact is created, it can be used for deployment.

{% hint style="info" %}
Example of `build` Ansible role:

1. Get app source code from GitHub
2. Compile source code
3. Pack result binary to tarball with name `app-{{semaphore_task_version}}.tar.gz`
4. Send `app-{{semaphore_task_version}}.tar.gz` to S3 bucket
{% endhint %}

### Deploy

This type of task is used to deploy artifacts to destination servers. Each deployment task is associated with the build task. You should use variable `semaphore_task_version` (or environment variable `SEMAPHORE_TASK_VERSION`) in your Ansible playbook to get what version of the artifact should be deployed.

{% hint style="info" %}
Example of `deploy` Ansible role:

1. Download `app-{{semaphore_task_version}}.tar.gz` from S3 bucket to destination servers
2. Unpack `app-{{semaphore_task_version}}.tar.gz` to destination directory
3. Create or update configuration files
4. Restart app service
{% endhint %}

