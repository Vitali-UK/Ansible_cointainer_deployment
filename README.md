# Ansible_cointainer_deployment

This playbook installs the .NET Core SDK, clones the Git repository, builds and publishes the application, and configures Nginx as a reverse proxy. The git_repo and git_branch variables are defined earlier in the playbook, and are passed into the task as {{ git_repo }} and {{ git_branch }} respectively.

It's important to note that you will need to replace the following variables with actual values:

https://github.com/user/myblazorapp.git with the actual URL of the Git repository
master with the actual branch name.
nginx.conf.j2 with the actual path of the Nginx config
