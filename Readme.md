## Collection of random notes gathered installing Kubespray in a 10 Raspberry Pi 4 cluster with Ubuntu 20.04



The official Ubuntu 20.04.1 failed installing the K8s cluster in many aspects, seems like Linux kernel 5.4.x is half-build, couldn't make Raspberry PIs be masters with official or unofficial Ubuntu. I'll try again when new version of Kubespray + Ubuntu appear.

Alternative installation:

- Used https://kubespray.io/#/docs/getting-started v2.14.0-208-gbbab1013

- Installing master in Amd64 based Ubuntu 20.04 machines.

- Installed unofficial Ubuntu 18.04 for Raspberry Pi and upgraded to 20.04 keeping kernel 4.19, no way to use kernel 5.4.x with any tried combination. https://github.com/TheRemote/Ubuntu-Server-raspi4-unofficial/releases/tag/v28

- Calico doesn't work well, I used flannel.

- My  post continues working for Ubuntu 18.04 https://www.mozcode.com/blog/rasberry-pi-4-kubernetes-cluster-multi-master-ubuntu-kubespray

  

**Renew ssh key in controller:**
*ssh-keygen -f "/home/ubuntu/.ssh/known_hosts" -R "192.168.1.31"*
*ssh-copy-id 192.168.1.31*

**Commands to install proposed status kernels:**
**sudo nano /etc/apt/sources.list*
*deb http://ports.ubuntu.com/ubuntu-ports focal-proposed main*
*deb http://ports.ubuntu.com/ubuntu-ports focal-proposed main restricted*
*sudo apt-get update*
*sudo apt-get install flash-kernel linux-raspi2 linux-image-raspi2 linux-headers-raspi2 linux-firmware-raspi2 linux-headers-5.4.0-1026-raspi linux-image-5.4.0-1026-raspi linux-modules-5.4.0-1026-raspi linux-raspi-headers-5.4.0-1026 linux-firmware linux-headers-raspi linux-image-raspi linux-rasp*

**Linux kernel 5.4.x failure message:**

  *24   │ - name: Create kubeadm token for joining nodes with 24h expiration (default)*
  *25 ~ │   command: "{{ bin_dir }}/kubeadm token create --kubeconfig /etc/kubernetes/admin.conf"*
  *26   │   register: temp_token*
  *27   │   delegate_to: "{{ groups['kube-master'][0] }}"*
  *28   │   when: kubeadm_token is not defined*

**Declare command:**
declare -a IPS=(192.168.1.5 192.168.1.7 192.168.1.9 192.168.1.4 192.168.1.6 192.168.1.30 192.168.1.31 192.168.1.32 192.168.1.33 192.168.1.34 192.168.1.35 192.168.1.36 192.168.1.37 192.168.1.38 192.168.1.39)

**Common commands used while trying and trying combinations:**
*ansible-playbook --flush-cache -i inventory/mycluster/hosts.yaml --become --become-user=root cluster.yml*
*ansible-playbook --flush-cache -i inventory/mycluster/hosts.yaml --become --become-user=root reset.yml*
*ansible-playbook --flush-cache -i inventory/mycluster/hosts.yaml --become --become-user=root scale.yml*
*ansible-playbook --flush-cache -i inventory/mycluster/hosts.yaml --become --become-user=root remove-node.yml --extra-vars "node=pi4"*
*kubectl get pods --all-namespaces -o wide*
*kubectl get nodes -o wide***

Executing "scale.yml" adding new PIs, leaves the new node in notReady status and even configured to use flannel complains with a couple of calico messages from masters.
