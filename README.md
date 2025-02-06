# cloud-init
Descrição dos principais módulos do **Cloud-Init**, suas funções e a ordem em que são executados. 

## Para mais informações, exemplos, como usar, ou para que serve:

- [Documentação do QM (Proxmox)](https://pve.proxmox.com/pve-docs/qm.1.html)
- [Módulos do Cloud-Init](https://cloudinit.readthedocs.io/en/22.1_a/topics/modules.html)
- [Documentação Oficial do Cloud-Init](https://cloudinit.readthedocs.io/en/latest/)

# Execução dos Módulos do Cloud-Init


## **1.0 bootcmd**
Executa comandos antes de qualquer outro módulo.

**Uso:** Ideal para montar discos ou modificar configurações de rede imediatamente após a inicialização do sistema.

## **2.0 write_files**
Escreve arquivos no sistema antes da execução do `runcmd`.

**Uso:** Criar arquivos de configuração, registros de logs ou scripts necessários para a execução posterior de comandos.

## **3.0 runcmd**
Executa comandos após a configuração do sistema.

**Uso:** Executar scripts de instalação, iniciar serviços ou modificar o sistema conforme necessário.

## **4.0 users**
Define usuários e suas permissões.

**Uso:** Criar e configurar usuários, grupos e permissões SSH. Este módulo é executado após o `write_files` para garantir a correta configuração do sistema de arquivos.

## **5.0 packages**
Instala pacotes definidos pelo usuário.

**Uso:** Instalação de software essencial, como servidores web, bibliotecas e ferramentas necessárias para a execução da instância.

## **6.0 timezone**
Define o fuso horário do sistema.

**Uso:** Configurar a data e a hora após a instalação de pacotes, garantindo um funcionamento correto dos serviços.

## **7.0 final_message**
Exibe uma mensagem ao concluir a execução do Cloud-Init.

**Uso:** Fornecer um status ou uma mensagem de sucesso ao usuário, confirmando que o processo foi finalizado corretamente.

## **8.0 ssh_keys (Opcional)**
Configura chaves SSH para os usuários criados.

**Uso:** Adicionar chaves SSH para conexões seguras ao servidor.

---

## **Ordem de Execução dos Módulos**

1. **bootcmd** - Executa comandos iniciais.
2. **write_files** - Cria arquivos no sistema.
3. **runcmd** - Executa scripts e comandos personalizados.
4. **users** - Configura usuários e permissões.
5. **packages** - Instala pacotes necessários.
6. **timezone** - Configura o fuso horário.
7. **final_message** - Exibe uma mensagem de conclusão.
8. **ssh_keys** (Opcional) - Adiciona chaves SSH para acessos remotos.

---


