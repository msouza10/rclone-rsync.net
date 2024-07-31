## Tutorial: Montando rsync.net com rclone

Este tutorial detalhado demonstra como montar seu servidor rsync.net usando o rclone, proporcionando acesso direto ao seu armazenamento via sistema de arquivos.

**Passo 1: Criar um Diretório para Ponto de Montagem**

```bash
mkdir -p /mnt/rsync
```

Este comando cria o diretório `/mnt/rsync` que servirá como ponto de montagem para o seu servidor rsync.net.

**Passo 2: Instalar o rclone**

```bash
apt install rclone
```

Este comando instala o rclone, uma ferramenta de linha de comando que permite montar vários serviços de armazenamento em nuvem como sistemas de arquivos locais.

**Passo 3: Iniciar o Processo de Configuração**

```bash
rclone config
```

Este comando inicia o processo de configuração do rclone, guiando você através de uma série de prompts para configurar um novo "remoto" (neste caso, o seu servidor rsync.net).

**Passo 4: Adicionar um Nome**

```plaintext
name> rsync
```

Quando solicitado, adicione um nome para o novo remoto. Aqui, escolhemos "rsync" para facilitar a identificação.

**Passo 5: Selecionar o SFTP**

```plaintext
Storage> sftp
```

Escolha a opção "SFTP" da lista de tipos de armazenamento.

**Passo 6: Adicionar o Endereço do SFTP**

```plaintext
host> <seu_host_rsync.net>
```

Insira o endereço do seu servidor rsync.net, substituindo `<seu_host_rsync.net>` pelo endereço real.

**Passo 7: Adicionar o Usuário**

```plaintext
user> <seu_usuario>
```

Insira o nome de usuário que você utiliza para acessar o seu servidor rsync.net.

**Passo 8: Selecionar a Porta**

```plaintext
port> 22
```

Insira a porta SSH utilizada pelo seu servidor rsync.net. A maioria dos servidores utiliza a porta padrão 22.

**Passo 9: Selecionar o Método de Senha**

```plaintext
y/g/n> y
```

Escolha "y" para inserir a sua senha.

**Passo 10: Continuar com a Chave (se aplicável)**

```plaintext
key_pem>
key_file>
```

Se você utiliza uma chave SSH para autenticação, insira os detalhes da chave. Caso contrário, pressione Enter para pular esta etapa.

**Passo 11: Senha para a Chave (se aplicável)**

```plaintext
y/g/n> 
```

Se a sua chave SSH possui uma senha, insira-a aqui. Caso contrário, pressione Enter para pular esta etapa. (O mesmo para as proximas ate advanced config.)

**Passo 12: Configurações Avançadas (Opcional)**

```plaintext
Edit advanced config?
y/n> n
```

Para a maioria dos usuários, pressione "n" para não editar as configurações avançadas.

**Passo 13: Confirmar e Salvar a Configuração**

```plaintext
y/e/d> y
```

Verifique as informações da sua configuração e confirme com "y" para salvar.

**Passo 14: Montar o Sistema de Arquivos**

```bash
rclone mount rsync:/ /mnt/rsync --daemon
```

Este comando monta o seu servidor rsync.net no diretório `/mnt/rsync`.

**Testar a Montagem**

```bash
ls /mnt/rsync
```

```bash
df -h
```

Verifique se a montagem foi bem-sucedida listando o conteúdo do diretório.




**Desmontando apos os testes.**

```bash
umount -l /mnt/rsync
```

```bash
df -h
```

Verifique se a desmontagem foi bem-sucedida.

**Configurar o Serviço do Systemd para Montagem Automática**

Para garantir que o seu servidor rsync.net seja montado automaticamente no boot, siga os passos abaixo para configurar um serviço systemd:


**Passo 15: Criar o Arquivo de Serviço do Systemd**

Crie o arquivo de serviço `/etc/systemd/system/rclone-mount.service` com o seguinte conteúdo:

```ini
[Unit]
Description=Mount Rclone Remote
After=network-online.target
Wants=network-online.target

[Service]
Type=simple
ExecStart=/usr/bin/rclone mount rsync:/ /mnt/rsync --config /root/.config/rclone/rclone.conf --allow-other --vfs-cache-mode writes
ExecStop=/bin/fusermount -u /mnt/rsync
Restart=always
RestartSec=10

[Install]
WantedBy=multi-user.target
```

**Passo 16: Recarregar o Daemon do Systemd**

```bash
systemctl daemon-reload
```

**Passo 17: Iniciar o Serviço**

```bash
systemctl start rclone-mount.service
```

**Passo 18: Habilitar o Serviço para Inicialização Automática**

```bash
systemctl enable rclone-mount.service
```

**Passo 19: Verificar o Status do Serviço**

```bash
systemctl status rclone-mount.service
```

Após completar estes passos, o seu servidor rsync.net será montado automaticamente no boot, proporcionando acesso direto ao seu armazenamento através do sistema de arquivos.

**Observação:**

* Adapte os comandos acima, especialmente os caminhos e nomes dos arquivos, de acordo com a sua configuração específica.
* Certifique-se de ter as permissões necessárias para executar os comandos.
* Este tutorial assume o uso do sistema Debian/Ubuntu. Para outras distribuições, os comandos podem variar ligeiramente.

Com este tutorial, você terá aprendido como montar o seu servidor rsync.net com rclone, permitindo o acesso ao seu armazenamento de forma prática e eficiente.
