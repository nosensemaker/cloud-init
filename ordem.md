em de Execução dos modulos do cloud.init


1.0 bootcmd

Comandos a serem executados antes de qualquer outro módulo.

Exemplo: montar discos ou alterar configurações de rede imediatamente.


2.0 write_files

Escreve arquivos no sistema.

Esses arquivos são configurados antes da execução dos comandos no runcmd, pois podem ser necessários para esses comandos.

Exemplo: criar arquivos de configuração, como arquivos de log ou scripts personalizados.


3.0 runcmd

Executa comandos no sistema.

Este módulo roda após a criação de arquivos e configurações de rede.

Exemplo: rodar scripts de instalação ou iniciar serviços.


4.0 users

Define usuários e suas permissões.

Pode incluir a configuração de usuários, grupos e permissões SSH.

Executado após write_files para garantir que o sistema de arquivos esteja configurado corretamente antes de adicionar usuários.


5.0 packages

Instala pacotes.

Este módulo pode ser executado para instalar pacotes que você definiu, como servidores web, bibliotecas, etc.


6.0 timezone

Configura o fuso horário.

Definido após a instalação de pacotes para garantir que a configuração da data/hora ocorra depois da instalação.


7.0 final_message

Exibe uma mensagem quando o Cloud-Init terminar sua execução.

Pode ser usado para mostrar um status ou uma mensagem de sucesso para o usuário.


8.0 ssh_keys:

Se você estiver configurando chaves SSH, pode incluir esse módulo para adicionar chaves aos usuários.



---

Ordem de Execução (Sequencial):

1. bootcmd - Executa comandos antes de qualquer outro módulo.


2. write_files - Escreve arquivos no sistema.


3. runcmd - Executa comandos após a escrita dos arquivos.


4. users - Cria e configura usuários.


5. packages - Instala pacotes.


6. timezone - Configura o fuso horário.


7. final_message - Exibe uma mensagem de conclusão.


8. ssh_keys (opcional) - Adiciona chaves SSH aos usuários.
