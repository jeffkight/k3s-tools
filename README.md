# K3S Tools

K3S is a certified Lightweight Kubernetes distro from Rancher that is focused on:
* Edge
* IoT
* CI
* ARM

see https://k3s.io/

## Tools

Ansible roles for k3s_masters, k3s_workers

## Usage

### Deploy physical or virtual hosts with Ubuntu or CentOS.

Tested:
* Ubuntu 18.04 LTS
* CentOS 7.7

### Modify hosts file for local environment

[hosts-example.yml example](hosts-example.yml)

Take care to specify hosts for the different roles.  
*At this point, only a single master is supported, although you can include multiple masters in the list*
*Only the first host in the k3s_masters group will be included in the cluster*

### Run k3s-site.yml

```
$ ansible-playbook k3s-site.yml
```

### Note you will see output from k3s installer for the master and workers

#### Master sample output

```
TASK [k3s-master : install k3s on master without agent] ******************************************************************************************************************
changed: [node-19]

TASK [k3s-master : results of installation] ******************************************************************************************************************************
ok: [node-19] => {
    "master_install_result.stdout_lines": [
        "[INFO]  Finding latest release",
        "[INFO]  Using v0.10.2 as release",
        "[INFO]  Downloading hash https://github.com/rancher/k3s/releases/download/v0.10.2/sha256sum-amd64.txt",
        "[INFO]  Downloading binary https://github.com/rancher/k3s/releases/download/v0.10.2/k3s",
        "[INFO]  Verifying binary download",
        "[INFO]  Installing k3s to /usr/local/bin/k3s",
        "[INFO]  Creating /usr/local/bin/kubectl symlink to k3s",
        "[INFO]  Creating /usr/local/bin/crictl symlink to k3s",
        "[INFO]  Creating /usr/local/bin/ctr symlink to k3s",
        "[INFO]  Creating killall script /usr/local/bin/k3s-killall.sh",
        "[INFO]  Creating uninstall script /usr/local/bin/k3s-uninstall.sh",
        "[INFO]  env: Creating environment file /etc/systemd/system/k3s.service.env",
        "[INFO]  systemd: Creating service file /etc/systemd/system/k3s.service",
        "[INFO]  systemd: Enabling k3s unit",
        "[INFO]  systemd: Starting k3s"
    ]
}
```

#### Workers sample output
Note that the output specifies that an agent has been started.
```
TASK [k3s-workers : install k3s on workers] ******************************************************************************************************************************
changed: [node-30]
changed: [node-27]
changed: [node-28]
changed: [node-29]

TASK [k3s-workers : results of installation] *****************************************************************************************************************************
ok: [node-27] => {
    "workers_install_result.stdout_lines": [
        "[INFO]  Finding latest release",
        "[INFO]  Using v0.10.2 as release",
        "[INFO]  Downloading hash https://github.com/rancher/k3s/releases/download/v0.10.2/sha256sum-amd64.txt",
        "[INFO]  Downloading binary https://github.com/rancher/k3s/releases/download/v0.10.2/k3s",
        "[INFO]  Verifying binary download",
        "[INFO]  Installing k3s to /usr/local/bin/k3s",
        "[INFO]  Creating /usr/local/bin/kubectl symlink to k3s",
        "[INFO]  Creating /usr/local/bin/crictl symlink to k3s",
        "[INFO]  Creating /usr/local/bin/ctr symlink to k3s",
        "[INFO]  Creating killall script /usr/local/bin/k3s-killall.sh",
        "[INFO]  Creating uninstall script /usr/local/bin/k3s-agent-uninstall.sh",
        "[INFO]  env: Creating environment file /etc/systemd/system/k3s-agent.service.env",
        "[INFO]  systemd: Creating service file /etc/systemd/system/k3s-agent.service",
        "[INFO]  systemd: Enabling k3s-agent unit",
        "[INFO]  systemd: Starting k3s-agent"
    ]
}
```

### kubeconfig gets copied to localhost

```
TASK [k3s-master : create credentials directory] *************************************************************************************************************************
changed: [node-19 -> localhost]

TASK [k3s-master : fetch k3s.yaml] ***************************************************************************************************************************************
changed: [node-19]

TASK [k3s-master : update master ip in k3s.yaml] *************************************************************************************************************************
changed: [node-19 -> localhost]
```

### rancher-site.yml

Utilizes Ansible roles for docker and rancher to install rancher

#### Usage
```
$ ansible-playbook rancher-site.yml

PLAY [rancher] ***********************************************************************************************************************************************************

TASK [Gathering Facts] ***************************************************************************************************************************************************
ok: [node-3]
```
output filtered
```
TASK [rancher : start rancher in docker] *********************************************************************************************************************************
changed: [node-3]

PLAY RECAP ***************************************************************************************************************************************************************
node-3              : ok=24   changed=20   unreachable=0    failed=0    skipped=12   rescued=0    ignored=0
```

#### Access Rancher
To access the Rancher server UI, open a browser and go to the hostname or address where the container was installed. You will be guided through setting up your first cluster.

## Troubleshooting

### .vaultpass
The [ansible.cfg](ansible.cfg) file assumes a local `.vaultpass` file.  
If you don't have one, you may see an error like:
```
$ ansible-playbook k3s-site.yml
ERROR! The vault password file /Users/jeff/work/k3s-tools/.vaultpass was not found
```
FIX: remove the `vault_password_file = .vaultpass` line from ansible.cfg, or create .vaultpass

## Next Steps / Ideas

- [ ] Update roles for manual installation, instead of *trusting* the shell script
