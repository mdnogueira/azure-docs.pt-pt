---
title: "Configurar Oracle ASM numa máquina virtual com Linux do Azure | Microsoft Docs"
description: "Obter rapidamente Oracle ASM cópias de segurança e em execução no seu ambiente do Azure."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: RicksterCDN
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/19/2017
ms.author: rclaus
ms.openlocfilehash: 117212a2e7e3da7c3e249798eec804a652e0ef58
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-oracle-asm-on-an-azure-linux-virtual-machine"></a>Configurar Oracle ASM numa máquina virtual com Linux do Azure  

Máquinas virtuais do Azure fornecem um ambiente informático totalmente configurável e flexível. Este tutorial abrange uma implementação de máquinas virtuais do Azure básica combinada com a instalação e configuração de Oracle automatizada armazenamento gestão (ASM).  Saiba como:

> [!div class="checklist"]
> * Crie e ligue a uma VM de base de dados Oracle
> * Instalar e configurar a gestão de armazenamento de automatizada Oracle
> * Instalar e configurar a infraestrutura de grelha Oracle
> * Inicializar uma instalação de ASM de Oracle
> * Criar uma base de dados Oracle geridos pelo ASM


[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este tutorial, necessita que está a executar a CLI do Azure versão 2.0.4 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar o Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="prepare-the-environment"></a>Preparar o ambiente

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Para criar um grupo de recursos, utilize o [criar grupo az](/cli/azure/group#create) comando. Um grupo de recursos do Azure é um contentor lógico na qual os recursos são implementados e geridos. Neste exemplo, um grupo de recursos denominado *myResourceGroup* no *eastus* região.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

### <a name="create-a-vm"></a>Criar uma VM

Para criar uma máquina virtual com base na imagem de base de dados Oracle e configurá-lo para utilizar Oracle ASM, utilize o [az vm criar](/cli/azure/vm#create) comando. 

O exemplo seguinte cria uma VM com o nome myVM que tem um tamanho de Standard_DS2_v2 com quatro discos de dados anexados de 50 GB. Caso ainda não existam na localização predefinida da chave, também cria chaves SSH.  Para utilizar um conjunto específico de chaves, utilize a opção `--ssh-key-value`.  

   ```azurecli-interactive
   az vm create --resource-group myResourceGroup \
    --name myVM \
    --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
    --size Standard_DS2_v2 \
    --generate-ssh-keys \
    --data-disk-sizes-gb 50 50 50 50
   ```

Depois de criar a VM, CLI do Azure apresenta informações semelhantes ao seguinte exemplo. Tenha em atenção o valor para `publicIpAddress`. Utilize este endereço aceda à VM.

   ```azurecli
   {
     "fqdns": "",
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
     "location": "eastus",
     "macAddress": "00-0D-3A-36-2F-56",
     "powerState": "VM running",
     "privateIpAddress": "10.0.0.4",
     "publicIpAddress": "13.64.104.241",
     "resourceGroup": "myResourceGroup"
   }
   ```

### <a name="connect-to-the-vm"></a>Ligar à VM

Para criar uma sessão SSH com a VM e configurar definições adicionais, utilize o seguinte comando. Substituir o endereço IP com o `publicIpAddress` valor para a VM.

```bash 
ssh <publicIpAddress>
```

## <a name="install-oracle-asm"></a>Instalar Oracle ASM

Para instalar Oracle ASM, execute os seguintes passos. 

Para obter mais informações sobre a instalação de ASM do Oracle, consulte [Oracle ASMLib transfere para Oracle Linux 6](http://www.oracle.com/technetwork/server-storage/linux/asmlib/ol6-1709075.html).  

1. Terá de iniciar sessão como raiz para prosseguir a instalação de ASM:

   ```bash
   sudo su -
   ```
   
2. Execute estes comandos adicionais para instalar os componentes de ASM de Oracle:

   ```bash
    yum list | grep oracleasm 
    yum -y install kmod-oracleasm.x86_64 
    yum -y install oracleasm-support.x86_64 
    wget http://download.oracle.com/otn_software/asmlib/oracleasmlib-2.0.12-1.el6.x86_64.rpm 
    yum -y install oracleasmlib-2.0.12-1.el6.x86_64.rpm 
    rm -f oracleasmlib-2.0.12-1.el6.x86_64.rpm
   ```

3. Certifique-se de que a opção Oracle ASM está instalado:

   ```bash
   rpm -qa |grep oracleasm
   ```

    O resultado deste comando deve listar os seguintes componentes:

    ```bash
   oracleasm-support-2.1.10-4.el6.x86_64
   kmod-oracleasm-2.0.8-15.el6_9.x86_64
   oracleasmlib-2.0.12-1.el6.x86_64
    ```

4. ASM requer utilizadores e funções específicos para funcionar corretamente. Os comandos seguintes criam pré-requisitos contas de utilizador e grupos: 

   ```bash
    groupadd -g 54345 asmadmin 
    groupadd -g 54346 asmdba 
    groupadd -g 54347 asmoper 
    useradd -u 3000 -g oinstall -G dba,asmadmin,asmdba,asmoper grid 
    usermod -g oinstall -G dba,asmdba,asmadmin oracle
   ```

5. Certifique-se de que os utilizadores e grupos foram criados corretamente:

   ```bash
   id grid
   ```

    O resultado deste comando deve listar os utilizadores e grupos que se seguem:

    ```bash
    uid=3000(grid) gid=54321(oinstall) groups=54321(oinstall),54322(dba),54345(asmadmin),54346(asmdba),54347(asmoper)
    ```
 
6. Crie uma pasta para o utilizador *grelha* e alterar o proprietário:

   ```bash
   mkdir /u01/app/grid 
   chown grid:oinstall /u01/app/grid
   ```

## <a name="set-up-oracle-asm"></a>Configurar Oracle ASM

Para este tutorial, o utilizador predefinido é *grelha* e o grupo predefinido está *asmadmin*. Certifique-se de que o *oracle* utilizador faz parte do grupo asmadmin. Para configurar a instalação de ASM do Oracle, execute os seguintes passos:

1. Configurar o controlador de biblioteca Oracle ASM envolve a definir o utilizador predefinido (grelha) e o grupo predefinido (asmadmin), bem como configurar a unidade para iniciar o arranque (escolher y) e para procurar discos no arranque (escolher y). Terá de responder os pedidos do seguinte comando:

   ```bash
   /usr/sbin/oracleasm configure -i
   ```

   O resultado deste comando deverá ter um aspeto semelhante ao seguinte, parar com solicita a serem respondidas.

    ```bash
   Configuring the Oracle ASM library driver.

   This will configure the on-boot properties of the Oracle ASM library
   driver. The following questions will determine whether the driver is
   loaded on boot and what permissions it will have. The current values
   will be shown in brackets ('[]'). Hitting <ENTER> without typing an
   answer will keep that current value. Ctrl-C will abort.

   Default user to own the driver interface []: grid
   Default group to own the driver interface []: asmadmin
   Start Oracle ASM library driver on boot (y/n) [n]: y
   Scan for Oracle ASM disks on boot (y/n) [y]: y
   Writing Oracle ASM library driver configuration: done
   ```

2. Ver a configuração do disco:
   ```bash
   cat /proc/partitions
   ```

   O resultado deste comando deve ter um aspeto semelhante a lista seguinte de discos disponíveis

   ```bash
   8       16   14680064 sdb
   8       17   14678976 sdb1
   8        0   52428800 sda
   8        1     512000 sda1
   8        2   51915776 sda2
   8       48   52428800 sdd
   8       64   52428800 sde
   8       80   52428800 sdf
   8       32   52428800 sdc
   11       0       1152 sr0
   ```

3. Disco de formato */dev/sdc* , executando o seguinte comando e responder a pedidos com:
   - *n*para a nova partição
   - *p* para a partição primária
   - *1* para selecionar a primeira partição
   - Prima `enter` para o cilindro primeiro predefinido
   - Prima `enter` para o cilindro último predefinido
   - Prima *w* ao escrever as alterações para a tabela de partição  

   ```bash
   fdisk /dev/sdc
   ```
   
   Utilizar as respostas fornecidas acima, o resultado para o comando fdisk deve o seguinte aspeto:

   ```bash
   Device contains not a valid DOS partition table, or Sun, SGI or OSF disklabel
   Building a new DOS disklabel with disk identifier 0xf865c6ca.
   Changes will remain in memory only, until you decide to write them.
   After that, of course, the previous content won't be recoverable.

   Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

   The device presents a logical sector size that is smaller than
   the physical sector size. Aligning to a physical sector (or optimal
   I/O) size boundary is recommended, or performance may be impacted.

   WARNING: DOS-compatible mode is deprecated. It's strongly recommended to
           switch off the mode (command 'c') and change display units to
           sectors (command 'u').

   Command (m for help): n
   Command action
     e   extended
     p   primary partition (1-4)
   p
   Partition number (1-4): 1
   First cylinder (1-6527, default 1):
   Using default value 1
   Last cylinder, +cylinders or +size{K,M,G} (1-6527, default 6527):
   Using default value 6527

   Command (m for help): w
   The partition table has been altered!

   Calling ioctl() to re-read partition table.
   Syncing disks.
   ```

4. Repita o comando fdisk anterior para `/dev/sdd`, `/dev/sde`, e `/dev/sdf`.

5. Verifique a configuração do disco:

   ```bash
   cat /proc/partitions
   ```

   O resultado do comando deve ter o seguinte aspeto:

   ```bash
   major minor  #blocks  name

     8       16   14680064 sdb
     8       17   14678976 sdb1
     8       32   52428800 sdc
     8       33   52428096 sdc1
     8       48   52428800 sdd
     8       49   52428096 sdd1
     8       64   52428800 sde
     8       65   52428096 sde1
     8       80   52428800 sdf
     8       81   52428096 sdf1
     8        0   52428800 sda
     8        1     512000 sda1
     8        2   51915776 sda2
     11       0    1048575 sr0
   ```

6. Verificar o estado do serviço de ASM de Oracle e iniciar o serviço de ASM de Oracle:

   ```bash
   service oracleasm status 
   service oracleasm start
   ```

   O resultado do comando deve ter o seguinte aspeto:
   
   ```bash
   Checking if ASM is loaded: no
   Checking if /dev/oracleasm is mounted: no
   Initializing the Oracle ASMLib driver:                     [  OK  ]
   Scanning the system for Oracle ASMLib disks:               [  OK  ]
   ```

7. Crie discos Oracle ASM:

   ```bash
   service oracleasm createdisk ASMSP /dev/sdc1 
   service oracleasm createdisk DATA /dev/sdd1 
   service oracleasm createdisk DATA1 /dev/sde1 
   service oracleasm createdisk FRA /dev/sdf1
   ```    

   O resultado do comando deve ter o seguinte aspeto:

   ```bash
   Marking disk "ASMSP" as an ASM disk:                       [  OK  ]
   Marking disk "DATA" as an ASM disk:                        [  OK  ]
   Marking disk "DATA1" as an ASM disk:                       [  OK  ]
   Marking disk "FRA" as an ASM disk:                         [  OK  ]
   ```

8. Listar Oracle ASM discos:

   ```bash
   service oracleasm listdisks
   ```   

   O resultado do comando deve listar desativar os discos de ASM de Oracle seguintes:

   ```bash
    ASMSP
    DATA
    DATA1
    FRA
   ```

9. Altere as palavras-passe para os utilizadores de raiz, oracle e grelha. **Anote estas novas palavras-passe** como estiver a utilizar mais tarde durante a instalação.

   ```bash
   passwd oracle 
   passwd grid 
   passwd root
   ```

10. Altere as permissões da pasta:

   ```bash
   chmod -R 775 /opt 
   chown grid:oinstall /opt 
   chown oracle:oinstall /dev/sdc1 
   chown oracle:oinstall /dev/sdd1 
   chown oracle:oinstall /dev/sde1 
   chown oracle:oinstall /dev/sdf1 
   chmod 600 /dev/sdc1 
   chmod 600 /dev/sdd1 
   chmod 600 /dev/sde1 
   chmod 600 /dev/sdf1
   ```

## <a name="download-and-prepare-oracle-grid-infrastructure"></a>Transferir e preparar a infraestrutura de grelha Oracle

Para transferir e preparar o software de infraestrutura de grelha do Oracle, execute os seguintes passos:

1. Oracle grelha infraestrutura a partir de transferir o [página de transferência do Oracle ASM](http://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-linux-download-2240591.html). 

   Sob a transferência intitulada **base de dados Oracle 12c versão 1 grelha infraestrutura (12.1.0.2.0) para Linux x86-64**, transferir os dois ficheiros. zip.

2. Depois de transferir os ficheiros. zip no seu computador cliente, pode utilizar o protocolo Secure de cópia (SCP) para copiar os ficheiros para a VM:

   ```bash
   scp *.zip <publicIpAddress>:.
   ```

3. SSH no VM Oracle no Azure para mover os ficheiros. zip no / optar ativamente por participar pasta. Em seguida, altere o proprietário dos ficheiros:

   ```bash
   ssh <publicIPAddress>
   sudo mv ./*.zip /opt
   cd /opt
   sudo chown grid:oinstall linuxamd64_12102_grid_1of2.zip
   sudo chown grid:oinstall linuxamd64_12102_grid_2of2.zip
   ```

4. Descomprimir os ficheiros. (O instalar o Linux deszipe ferramenta se ainda não estiver instalado.)
   
   ```bash
   sudo yum install unzip
   sudo unzip linuxamd64_12102_grid_1of2.zip
   sudo unzip linuxamd64_12102_grid_2of2.zip
   ```

5. Permissão de alteração:
   
   ```bash
   sudo chown -R grid:oinstall /opt/grid
   ```

6. Espaço de comutação de atualização configurada. Componentes de grelha Oracle precisam de, pelo menos, 6.8 GB de espaço de comutação para instalar a grelha. O tamanho do ficheiro de troca predefinido para as imagens de Oracle Linux no Azure é apenas de 2048MB. Precisa de aumentar `ResourceDisk.SwapSizeMB` no `/etc/waagent.conf` de ficheiros e reiniciar o serviço de WALinuxAgent para as definições atualizadas, entre em vigor. Porque é um ficheiro só de leitura, terá de alterar as permissões de ficheiro para ativar o acesso de escrita.

   ```bash
   sudo chmod 777 /etc/waagent.conf  
   vi /etc/waagent.conf
   ```
   
   Procurar `ResourceDisk.SwapSizeMB` e altere o valor para **8192**. Terá de prima `insert` para introduzir o modo de inserção, escreva o valor de **8192** e, em seguida, prima `esc` para regressar ao modo de comando. Para guardar as alterações e sair o ficheiro, escreva `:wq` e prima `enter`.
   
   > [!NOTE]
   > Recomendamos vivamente que utilize sempre `WALinuxAgent` para configurar o espaço de comutação, de modo a que é sempre criado no disco efémeras local (disco temporário) para um melhor desempenho. Para obter mais informações sobre, consulte [como adicionar um ficheiro de comutação em máquinas virtuais Linux Azure](https://support.microsoft.com/en-us/help/4010058/how-to-add-a-swap-file-in-linux-azure-virtual-machines).

## <a name="prepare-your-local-client-and-vm-to-run-x11"></a>Preparar o seu cliente local e a VM para executar x11
Configurar Oracle ASM requer uma interface gráfica para concluir a instalação e configuração. Estamos a utilizar o x11 protocolo para facilitar esta instalação. Se estiver a utilizar um sistema de cliente (Mac ou Linux) que já tenha X11 capacidades ativada e configurada - pode ignorar esta configuração e o programa de configuração exclusivo para computadores Windows. 

1. [Transfira o PuTTY](http://www.putty.org/) e [transferir Xming](https://xming.en.softonic.com/) no seu computador Windows. Será necessário concluir a instalação de ambos os valores predefinidos antes de continuar a estas aplicações.

2. Depois de instalar o PuTTY, abra uma linha de comandos, mude para a pasta PuTTY (por exemplo, c:\Programas\Microsoft Files\PuTTY) e executar `puttygen.exe` para gerar uma chave.

3. No gerador de chave PuTTY:
   
   1. Gerar uma chave selecionando o `Generate` botão.
   2. Copie o conteúdo da chave (Ctrl + C).
   3. Selecione o `Save private key` botão.
   4. Ignorar o aviso sobre como proteger a chave com uma frase de acesso e, em seguida, selecione `OK`.

   ![Captura de ecrã do PuTTY gerador de chaves](./media/oracle-asm/puttykeygen.png)

4. Na sua VM, execute estes comandos:

   ```bash
   sudo su - grid
   mkdir .ssh 
   cd .ssh
   ```

5. Crie um ficheiro denominado `authorized_keys`. Colar o conteúdo da chave deste ficheiro e, em seguida, guarde o ficheiro.

   > [!NOTE]
   > A chave tem de conter a cadeia `ssh-rsa`. Além disso, o conteúdo da chave tem de ser uma única linha de texto.
   >  

6. No seu sistema de cliente, inicie o PuTTY. No **categoria** painel, aceda a **ligação** > **SSH** > **Auth**. No **ficheiro de chave privada para autenticação** caixa, navegue para a chave que gerou anteriormente.

   ![Captura de ecrã das opções de autenticação SSH](./media/oracle-asm/setprivatekey.png)

7. No **categoria** painel, aceda a **ligação** > **SSH** > **X11**. Selecione o **reencaminhamento de X11 de ativar** caixa de verificação.

   ![Captura de ecrã do SSH X11 opções de reencaminhamento](./media/oracle-asm/enablex11.png)

8. No **categoria** painel, aceda a **sessão**. Introduza a VM de ASM Oracle `<publicIPaddress>` na caixa de diálogo de nome de anfitrião, de preencher um novo `Saved Session` nome e, em seguida, clique em `Save`.  Depois de guardar, clique em `open` para ligar à máquina virtual Oracle ASM.  Na primeira vez que o se ligar são avisado que sistema remoto não é colocado em cache no seu registo. Clique em `yes` para adicioná-lo e continuar.

   ![Captura de ecrã das opções de sessão PuTTY](./media/oracle-asm/puttysession.png)

## <a name="install-oracle-grid-infrastructure"></a>Instalar a infraestrutura da grelha Oracle

Para instalar a infraestrutura da grelha Oracle, execute os seguintes passos:

1. Inicie sessão como **grelha**. (, Deverá conseguir iniciar sessão sem pedidos para uma palavra-passe.) 

   > [!NOTE]
   > Se estiver a executar o Windows, certifique-se de que iniciou Xming antes de começar a instalação.

   ```bash
   cd /opt/grid
   ./runInstaller
   ```

   Abre a infraestrutura de grelha Oracle 12c versão 1 instalador. (Poderá demorar alguns minutos para que o instalador iniciar.)

2. No **selecionar a opção de instalação** página, selecione **instalar e configurar a infraestrutura de grelha Oracle para um servidor autónomo**.

   ![Captura de ecrã da página de selecionar a opção de instalação do programa de instalação](./media/oracle-asm/install01.png)

3. No **selecionar idiomas do produto** página, certifique-se **inglês** ou o idioma que pretende está selecionado.  Clique em `next`.

4. No **criar grupo de disco de ASM** página:
   - Introduza um nome para o grupo de disco.
   - Em **redundância**, selecione **externo**.
   - Em **tamanho da unidade de alocação**, selecione **4**.
   - Em **adicionar discos**, selecione **ORCLASMSP**.
   - Clique em `next`.

5. No **especificar a palavra-passe de ASM** página, selecione o **utilizar as mesmas palavras-passe para estas contas** opção e introduza uma palavra-passe.

   ![Captura de ecrã da página de especificar a palavra-passe de ASM do programa de instalação](./media/oracle-asm/install04.png)

6. No **especificar opções de gestão** página, tem a opção para configurar o controlo de nuvem de IT. Estamos a ignorar esta opção, clique `next` para continuar. 

7. No **grupos privilegiados do sistema operativo** página, utilize as predefinições. Clique em `next` para continuar.

8. No **especificar localização de instalação** página, utilize as predefinições. Clique em `next` para continuar.

9. No **Criar inventário** página, altere o diretório de inventário para `/u01/app/grid/oraInventory`. Clique em `next` para continuar.

   ![Captura de ecrã da página de inventário de criar o instalador](./media/oracle-asm/install08.png)

10. No **configuração de execução de scripts de raiz** página, selecione o **automaticamente executar scripts de configuração** caixa de verificação. Em seguida, selecione o **utilizar credencial de utilizador de "raiz"** opção e introduza a palavra-passe de utilizador de raiz.

    ![Captura de ecrã da página de configuração de execução de script de raiz do programa de instalação](./media/oracle-asm/install09.png)

11. No **executar verificações dos pré-requisitos** página, a configuração atual irá falhar com erros. Este é um comportamento esperado. Selecione `Fix & Check Again`.

12. No **a correção do Script** caixa de diálogo, clique em `OK`.

13. No **resumo** página, reveja as definições selecionadas e, em seguida, clique em `Install`.

    ![Captura de ecrã da página de resumo do programa de instalação](./media/oracle-asm/install12.png)

14. Uma caixa de diálogo de aviso é apresentado a informar configuração scripts têm de ser executado como um utilizador com privilégios. Clique em `Yes` para continuar.

15. No **concluir** página, clique em `Close` para concluir a instalação.

## <a name="set-up-your-oracle-asm-installation"></a>Configurar a instalação de ASM de Oracle

Para configurar a instalação de ASM do Oracle, execute os seguintes passos:

1. Certifique-se de que ainda tem sessão iniciada como **grelha**, da sua X11 sessão. Poderá ter de acessos `enter` para revive terminal. Em seguida, inicie o Oracle automatizada armazenamento gestão configuração assistente:

   ```bash
   cd /u01/app/grid/product/12.1.0/grid/bin
   ./asmca
   ```

   É aberto Assistente de configuração de ASM do Oracle.

2. No **configurar ASM: disco grupos** caixa de diálogo, clique no `Create` botão e, em seguida, clique em `Show Advanced Options`.

3. No **criar grupo de disco** caixa de diálogo:

   - Introduza o nome do grupo de disco **dados**.
   - Em **selecionar discos de membro**, selecione **ORCL_DATA** e **ORCL_DATA1**.
   - Em **tamanho da unidade de alocação**, selecione **4**.
   - Clique em `ok` ao criar o grupo de disco.
   - Clique em `ok` para fechar a janela de confirmação.

   ![Captura de ecrã da caixa de diálogo Criar grupo de disco](./media/oracle-asm/asm02.png)

4. No **configurar ASM: disco grupos** caixa de diálogo, clique no `Create` botão e, em seguida, clique em `Show Advanced Options`.

5. No **criar grupo de disco** caixa de diálogo:

   - Introduza o nome do grupo de disco **FRA**.
   - Em **redundância**, selecione **externo (nenhum)**.
   - Em **selecionar discos de membro**, selecione **ORCL_FRA**.
   - Em **tamanho da unidade de alocação**, selecione **4**.
   - Clique em `ok` ao criar o grupo de disco.
   - Clique em `ok` para fechar a janela de confirmação.

   ![Captura de ecrã da caixa de diálogo Criar grupo de disco](./media/oracle-asm/asm04.png)

6. Selecione **saída** para fechar o Assistente de configuração do ASM.

   ![Captura de ecrã da ASM configurar: caixa de diálogo de grupos de disco com o botão de saída](./media/oracle-asm/asm05.png)

## <a name="create-the-database"></a>Criar a base de dados

O software de base de dados Oracle já está instalado na imagem do Azure Marketplace. Para criar uma base de dados, conclua os seguintes passos:

1. Mude de utilizadores para o Superutilizador do Oracle e, em seguida, iniciar o serviço de escuta para o registo:

   ```bash
   su - oracle
   cd /u01/app/oracle/product/12.1.0/dbhome_1/bin
   ./dbca
   ```
   É aberto o Assistente de configuração de base de dados.

2. No **operação da base de dados** página, clique em `Create Database`.

3. No **modo de criação de** página:

   - Introduza um nome para a base de dados.
   - Para **tipo de armazenamento**, certifique-se **gestão automática de armazenamento (ASM)** está selecionada.
   - Para **localização de ficheiros de base de dados**, utilize a predefinição ASM sugerida localização.
   - Para **área de recuperação rápida**, utilize a predefinição ASM sugerida localização.
   - Escreva um **palavra-passe administrativa** e **Confirmar palavra-passe**.
   - Certifique-se `create as container database` está selecionada.
   - Escreva um `pluggable database name` valor.

4. No **resumo** página, reveja as definições selecionadas e, em seguida, clique em `Finish` para criar a base de dados.

   ![Captura de ecrã da página de resumo](./media/oracle-asm/createdb03.png)

5. A base de dados foi criado. No **concluir** página, tem a opção para desbloquear contas adicionais para utilizar esta base de dados e alterar as palavras-passe. Se pretender fazê-lo, selecione **gestão de palavra-passe** -caso contrário, clique em `close`.

## <a name="delete-the-vm"></a>Eliminar a VM

A configuração Oracle automatizada com gestão de armazenamento com êxito na imagem Oracle DB do Azure Marketplace.  Quando já não precisar desta VM, pode utilizar o seguinte comando para remover o grupo de recursos, VM e todos os recursos relacionados:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

[Tutorial: Configurar Oracle DataGuard](configure-oracle-dataguard.md)

[Tutorial: Configurar Oracle GoldenGate](Configure-oracle-golden-gate.md)

Reveja [Architect um Oracle DB](oracle-design.md)
