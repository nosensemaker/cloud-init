# Guia de Configuração do Cloud-Init

Para criar um arquivo de configuração personalizado e vinculá-lo a um template de VM, garantindo que as configurações desejadas sejam aplicadas automaticamente no momento da inicialização.

## **Pré-requisitos**

- Um template de VM já criado para utilizar o Cloud-Init.

---

## **Passo 1: Criar o arquivo de configuração**

Crie um arquivo de configuração no diretório `/snip/snippets/`, utilizando qualquer nome, mas com a extensão `.yml`:

```bash
vim /snip/snippets/custom.yml
```

Adicione o conteúdo de configuração desejado no arquivo. Abaixo está um exemplo:

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
  - systemctl start apache2
  - systemctl enable apache2
  - echo "Bem-vindo, Mestre!" > /home/manager/boas-vindas.txt
  - apt purge -y cloud-init && rm -rf /etc/cloud /var/lib/cloud
```

---

## **Passo 2: Configurar o Template para Utilizar o Arquivo Cloud-Init**

Agora, vincule o arquivo de configuração ao template de VM no Proxmox. Utilize o comando abaixo, substituindo `9000` pelo ID do template:

```bash
qm set 9000 --cicustom "user=snip:snippets/custom.yml"
```

**Explicação:**  
- `qm set 9000`: Define as configurações da VM de ID 9000.  
- `--cicustom "user=snip:snippets/custom.yml"`: Especifica o caminho do arquivo de configuração do Cloud-Init.  

---

## **Passo 3: Clonar e Iniciar a VM**

Após definir o arquivo Cloud-Init no template, clone o template para criar uma nova VM e inicie-a.

**⚠️ Importante:** Antes de iniciar a VM, é necessário modificar o IP e o Gateway no Playbook ou manualmente, pois sem essa configuração o Cloud-Init não funcionará corretamente.

### **Opção 1: Clonar via Playbook Ansible**

Caso utilize o Ansible para gerenciar suas VMs, execute o seguinte Playbook para clonar a VM automaticamente:

📌 **Link do Playbook:** [Clone VM Playbook](https://github.com/nosensemaker/cloud-init/blob/main/ansible/roles/clone-newvm.yml)

```bash
ansible-playbook ~/roles/clone-newvm.yml
```

### **Opção 2: Clonar Manualmente**

Se preferir clonar a VM manualmente, utilize os comandos abaixo:

```bash
qm clone 9000 100 --name minha-vm
```

Configure o IP e o Gateway da nova VM:

```bash
qm set 100 --ipconfig0 ip=192.168.17.178/24,gw=192.168.17.1
```

Inicie a VM recém-criada:

```bash
qm start 100
```

**Explicação:**  
- `qm clone 9000 100 --name minha-vm`: Clona o template `9000` para criar a VM `100`.  
- `qm set 100 --ipconfig0 ip=192.168.17.178/24,gw=192.168.17.1`: Define o IP e Gateway da nova VM.  
- `qm start 100`: Inicia a VM recém-criada.  

Quando a VM for iniciada, o **Cloud-Init** aplicará automaticamente as configurações especificadas no arquivo YAML.  

Caso precise modificar as configurações, edite o arquivo `custom.yml` e relacione-o novamente ao template utilizando o comando `qm set`.   

---


