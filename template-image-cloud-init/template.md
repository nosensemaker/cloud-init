# How to Create a Template VM

## 1. Configure Storage

Go to **Storage** in **PROXMOX WEB** and add a new directory:

```bash
Go to Datacenter (proxmox) > Storage > Add > Directory
```
Set the configuration: 

- **ID**: snip  
- **Directory**: snip  
- **Content**: Snippets  

## 2. Install Cloud-Init on **PROXMOX SERVER**

```bash
apt install cloud-init
```

## 3. Download the Image

```bash
wget https://cloud-images.ubuntu.com/jammy/current/jammy-server-cloudimg-amd64.img
```

## 4. Create a New VM with VirtIO SCSI Controller

```bash
qm create 9000 --memory 2048 --net0 virtio,bridge=vmbr0 --scsihw virtio-scsi-pci
```

## 5. Import the Downloaded Disk to Local Storage

```bash
qm set 9000 --scsi0 local-lvm:0,import-from=/path/to/jammy-server-cloudimg-amd64.img from=/root/img/image
```

## 6. Configure a CD-ROM Drive for Cloud-Init

```bash
qm set 9000 --ide2 local-lvm:cloudinit
```

## 7. Set Boot Parameters

```bash
qm set 9000 --boot order=scsi0
```

## 8. Configure Serial Console for Display

```bash
qm set 9000 --serial0 socket --vga serial0
```

## 9 Set Name for VM

```bash
qm set 9000 --name <string>
```

## 9.1 Convert the VM into a Template

```bash
qm template 9000
```

## 10. Enable QEMU Guest Agent ON **PROXMOX WEB** 

```bash
Go to **Options > QEMU Guest Agent** and enable it.
```

## 10.1 Set USER and Password in Cloud-Init ON **PROXMOX WEB**

```bash
Go to **Template VM > Cloud-init > Set User
Go to **Template VM > CLoud-init > Set Password
```

## 11. Default Cloud-Init Configuration

```bash
qm cloudinit dump 9000 user
```

## 12. Save Cloud-Init Configuration to a New File

```bash
qm cloudinit dump 9000 user > custom.yml
```

Modify the file as needed:

```bash
vim custom.yml
```

Move the file to the correct directory:

```bash
mv custom.yml /snip/snippets
```

## 13. Set Custom Cloud-Init Configuration

```bash
qm set 9000 --cicustom "user=snip:snippets/custom.yml"
```

## 14. Set Encrypted Password (Optional)

Generate an encrypted password:

```bash
qm cloudinit dump 9000 user > custom2.yml
```

Copy the password and edit `custom.yml` accordingly:

```yaml
#cloud-config
hostname: teste
manage_etc_hosts: true
fqdn: teste
chpasswd:
  expire: False
user: manager
password: $5$mnijLZ8G$VEJPtAKycY8dnwZFbC3P4BjlV93E6x.Qrmts0Wg6k79
chpasswd:
  expire: False
users:
  - default
  - name: manager
    groups: sudo
    shell: /bin/bash
    ssh-authorized-keys:
      - "ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAILILsFnLTU/u6PNNyZO/NGKjKcvTNLDGfKDXmMYQ29e+ manager@gunserver"
package_reboot_if_required: true
packages:
  - vim
  - git
  - qemu-guest-agent
  - apache2
runcmd:
  - systemctl start qemu-guest-agent
  - systemctl enable qemu-guest-agent
  - echo "Bem-vindo, Mestre!" > /home/manager/boas-vindas.txt
  - apt-get update && apt-get upgrade -y
  - apt purge -y cloud-init && rm -rf /etc/cloud /var/lib/cloud
```

