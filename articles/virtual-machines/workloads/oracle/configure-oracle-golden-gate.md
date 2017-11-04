---
title: Implementar Oracle Golden porta numa VM com Linux do Azure | Microsoft Docs
description: "Obter rapidamente uma porta de Golden Oracle cópias de segurança e em execução no seu ambiente do Azure."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: v-shiuma
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: rclaus
ms.openlocfilehash: a05711357d345267647c02e42336fd37c09e1bff
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="implement-oracle-golden-gate-on-an-azure-linux-vm"></a>Implementar Oracle Golden porta numa VM com Linux do Azure 

A CLI do Azure é utilizada para criar e gerir recursos do Azure a partir da linha de comandos ou em scripts. Este guia fornece detalhes sobre como utilizar a CLI do Azure para implementar uma base de dados Oracle 12c da imagem de galeria do Azure Marketplace. 

Este documento mostra-lhe passo a passo como criar, instalar e configurar a porta de Golden Oracle numa VM do Azure.

Antes de começar, certifique-se de que a CLI do Azure foi instalada. Para obter mais informações, veja [Guia de instalação da CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="prepare-the-environment"></a>Preparar o ambiente

Para efetuar a instalação de Oracle Golden porta, terá de criar duas VMs do Azure no mesmo conjunto de disponibilidade. A imagem do Marketplace que utilizar para criar as VMs é **Oracle: Oracle-base de dados-Ee:12.1.0.2:latest**.

Também tem de estar familiarizado com vi do editor de Unix e ter uma compreensão básica do x11 (X Windows).

Segue-se um resumo da configuração do ambiente:
> 
> |  | **Site primário** | **Replicar site** |
> | --- | --- | --- |
> | **Versão de Oracle** |Oracle 12c versão 2 – (12.1.0.2) |Oracle 12c versão 2 – (12.1.0.2)|
> | **Nome do computador** |myVM1 |myVM2 |
> | **Sistema operativo** |Oracle Linux 6. x |Oracle Linux 6. x |
> | **Oracle SID** |CDB1 |CDB1 |
> | **Esquema de replicação** |TESTE|TESTE |
> | **Porta Golden proprietário/replicar** |C ##GGADMIN |REPUSER |
> | **Processo de porta de Golden** |EXTORA |REPORA|


### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure 

Inicie sessão na sua subscrição do Azure com o [início de sessão az](/cli/azure/#login) comando. Em seguida, siga o ecrã as direções.

```azurecli
az login
```

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#create). Um grupo de recursos do Azure é um contentor lógico para os recursos do Azure são implementados e para que possam ser geridos. 

O exemplo seguinte cria um grupo de recursos com o nome `myResourceGroup` na localização `westus`.

```azurecli
az group create --name myResourceGroup --location westus
```

### <a name="create-an-availability-set"></a>Criar um conjunto de disponibilidade

O passo seguinte é opcional mas recomendado. Para obter mais informações, consulte [guia de conjuntos de disponibilidade do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines).

```azurecli
az vm availability-set create \
    --resource-group myResourceGroup \
    --name myAvailabilitySet \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

### <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Crie uma VM com o comando [z vm create](/cli/azure/vm#create). 

O exemplo seguinte cria duas VMs com o nome `myVM1` e `myVM2`. Crie chaves SSH se estes ainda não existir numa localização chave predefinido. Para utilizar um conjunto específico de chaves, utilize a opção `--ssh-key-value`.

#### <a name="create-myvm1-primary"></a>Crie myVM1 (principal):
```azurecli
az vm create \
     --resource-group myResourceGroup \
     --name myVM1 \
     --availability-set myAvailabilitySet \
     --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
     --size Standard_DS1_v2  \
     --generate-ssh-keys \
```

Depois da VM foi criada, a CLI do Azure mostra informações semelhante ao seguinte exemplo. (Tome nota do `publicIpAddress`. Este endereço é utilizado para aceder a VM.)

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westus",
  "macAddress": "00-0D-3A-36-2F-56",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.64.104.241",
  "resourceGroup": "myResourceGroup"
}
```

#### <a name="create-myvm2-replicate"></a>Criar myVM2 (replicar):
```azurecli
az vm create \
     --resource-group myResourceGroup \
     --name myVM2 \
     --availability-set myAvailabilitySet \
     --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
     --size Standard_DS1_v2  \
     --generate-ssh-keys \
```

Tome nota do `publicIpAddress` bem depois de terem sido criadas.

### <a name="open-the-tcp-port-for-connectivity"></a>Abra a porta TCP para conectividade

O passo seguinte consiste em configurar pontos finais externos, que lhe permite aceder remotamente a base de dados Oracle. Para configurar os pontos finais externos, execute os seguintes comandos.

#### <a name="open-the-port-for-myvm1"></a>Abra a porta para myVM1:

```azurecli
az network nsg rule create --resource-group myResourceGroup\
    --nsg-name myVm1NSG --name allow-oracle\
    --protocol tcp --direction inbound --priority 999 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 1521 --access allow
```

Os resultados devem ter um aspeto semelhantes a seguinte resposta:

```bash
{
  "access": "Allow",
  "description": null,
  "destinationAddressPrefix": "*",
  "destinationPortRange": "1521",
  "direction": "Inbound",
  "etag": "W/\"bd77dcae-e5fd-4bd6-a632-26045b646414\"",
  "id": "/subscriptions/<subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myVmNSG/securityRules/allow-oracle",
  "name": "allow-oracle",
  "priority": 999,
  "protocol": "Tcp",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sourceAddressPrefix": "*",
  "sourcePortRange": "*"
}
```

#### <a name="open-the-port-for-myvm2"></a>Abra a porta para myVM2:

```azurecli
az network nsg rule create --resource-group myResourceGroup\
    --nsg-name myVm2NSG --name allow-oracle\
    --protocol tcp --direction inbound --priority 999 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 1521 --access allow
```

### <a name="connect-to-the-virtual-machine"></a>Ligar à máquina virtual

Utilize o seguinte comando para criar uma sessão SSH com a máquina virtual. Substitua o endereço IP pelo `publicIpAddress` da máquina virtual.

```bash 
ssh <publicIpAddress>
```

### <a name="create-the-database-on-myvm1-primary"></a>Criar a base de dados myVM1 (principal)

O software Oracle já está instalado na imagem de mercado, pelo que o próximo passo é instalar a base de dados. 

Execute o software como de Superutilizador 'oracle':

```bash
sudo su - oracle
```

Crie a base de dados:

```bash
$ dbca -silent \
   -createDatabase \
   -templateName General_Purpose.dbc \
   -gdbname cdb1 \
   -sid cdb1 \
   -responseFile NO_VALUE \
   -characterSet AL32UTF8 \
   -sysPassword OraPasswd1 \
   -systemPassword OraPasswd1 \
   -createAsContainerDatabase true \
   -numberOfPDBs 1 \
   -pdbName pdb1 \
   -pdbAdminPassword OraPasswd1 \
   -databaseType MULTIPURPOSE \
   -automaticMemoryManagement false \
   -storageType FS \
   -ignorePreReqs
```
Saídas devem ter um aspeto semelhantes ao seguinte resposta:

```bash
Copying database files
1% complete
2% complete
8% complete
13% complete
19% complete
27% complete
Creating and starting Oracle instance
29% complete
32% complete
33% complete
34% complete
38% complete
42% complete
43% complete
45% complete
Completing Database Creation
48% complete
51% complete
53% complete
62% complete
70% complete
72% complete
Creating Pluggable Databases
78% complete
100% complete
Look at the log file "/u01/app/oracle/cfgtoollogs/dbca/cdb1/cdb1.log" for more details.
```

Defina as variáveis ORACLE_SID e ORACLE_HOME.

```bash
$ ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME
$ ORACLE_SID=gg1; export ORACLE_SID
$ LD_LIBRARY_PATH=ORACLE_HOME/lib; export LD_LIBRARY_PATH
```

Opcionalmente, pode adicionar ORACLE_HOME e ORACLE_SID ao ficheiro .bashrc, para que estas definições são guardadas para consulta futuros inícios de sessão:

```bash
# add oracle home
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
# add oracle sid
export ORACLE_SID=gg1
# add Oracle library path
export LD_LIBRARY_PATH=$ORACLE_HOME/lib
```

### <a name="start-oracle-listener"></a>Iniciar o serviço de escuta do Oracle
```bash
$ sudo su - oracle
$ lsnrctl start
```

### <a name="create-the-database-on-myvm2-replicate"></a>Criar a base de dados myVM2 (replicar)

```bash
sudo su - oracle
```
Crie a base de dados:

```bash
$ dbca -silent \
   -createDatabase \
   -templateName General_Purpose.dbc \
   -gdbname cdb1 \
   -sid cdb1 \
   -responseFile NO_VALUE \
   -characterSet AL32UTF8 \
   -sysPassword OraPasswd1 \
   -systemPassword OraPasswd1 \
   -createAsContainerDatabase true \
   -numberOfPDBs 1 \
   -pdbName pdb1 \
   -pdbAdminPassword OraPasswd1 \
   -databaseType MULTIPURPOSE \
   -automaticMemoryManagement false \
   -storageType FS \
   -ignorePreReqs
```
Defina as variáveis ORACLE_SID e ORACLE_HOME.

```bash
$ ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME
$ ORACLE_SID=cdb1; export ORACLE_SID
$ LD_LIBRARY_PATH=ORACLE_HOME/lib; export LD_LIBRARY_PATH
```

Opcionalmente, pode adicionado ORACLE_HOME e ORACLE_SID ao ficheiro .bashrc, para que estas definições são guardadas para consulta futuros inícios de sessão.

```bash
# add oracle home
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
# add oracle sid
export ORACLE_SID=cdb1
# add Oracle library path
export LD_LIBRARY_PATH=$ORACLE_HOME/lib
```

### <a name="start-oracle-listener"></a>Iniciar o serviço de escuta do Oracle
```bash
$ sudo su - oracle
$ lsnrctl start
```

## <a name="configure-golden-gate"></a>Configurar a porta Golden 
Para configurar Golden porta, siga os passos nesta secção.

### <a name="enable-archive-log-mode-on-myvm1-primary"></a>Ativar o modo de registo de arquivo em myVM1 (principal)

```bash
$ sqlplus / as sysdba
SQL> SELECT log_mode FROM v$database;

LOG_MODE
------------
NOARCHIVELOG

SQL> SHUTDOWN IMMEDIATE;
SQL> STARTUP MOUNT;
SQL> ALTER DATABASE ARCHIVELOG;
SQL> ALTER DATABASE OPEN;
```
Ativar o registo de imposição e certifique-se, pelo menos, um ficheiro de registo está presente.

```bash
SQL> ALTER DATABASE FORCE LOGGING;
SQL> ALTER SYSTEM SWITCH LOGFILE;
SQL> ALTER SYSTEM set enable_goldengate_replication=true;
SQL> ALTER PLUGGABLE DATABASE PDB1 OPEN;
SQL> ALTER SESSION SET CONTAINER=PDB1;
SQL> ALTER DATABASE ADD SUPPLEMENTAL LOG DATA;
SQL> EXIT;
```

### <a name="download-golden-gate-software"></a>Transferir o software de porta de Golden
Para transferir e preparar o software Oracle Golden porta, execute os seguintes passos:

1. Transferir o **fbo_ggs_Linux_x64_shiphome.zip** de ficheiros do [página de transferência do Oracle Golden porta](http://www.oracle.com/technetwork/middleware/goldengate/downloads/index.html). Sob o título de transferência **12.x.x.x Oracle GoldenGate para Oracle Linux x86-64**, deverá haver um conjunto de ficheiros. zip para transferir.

2. Depois de transferir os ficheiros. zip no seu computador cliente, utilize o protocolo Secure de cópia (SCP) para copiar os ficheiros para a VM:

  ```bash
  $ scp fbo_ggs_Linux_x64_shiphome.zip <publicIpAddress>:<folder>
  ```

3. Mova os ficheiros. zip para o **/ optar ativamente por participar** pasta. Em seguida, altere o proprietário dos ficheiros da seguinte forma:

  ```bash
  $ sudo su -
  # mv <folder>/*.zip /opt
  ```

4. Descomprimir os ficheiros (instalar o Linux deszipe utilitário se ainda não estiver instalado):

  ```bash
  # yum install unzip
  # cd /opt
  # unzip fbo_ggs_Linux_x64_shiphome.zip
  ```

5. Permissão de alteração:

  ```bash
  # chown -R oracle:oinstall /opt/fbo_ggs_Linux_x64_shiphome
  ```

### <a name="prepare-the-client-and-vm-to-run-x11-for-windows-clients-only"></a>Preparar o cliente e a VM para executar x11 (para clientes Windows)
Este passo é opcional. Pode ignorar este passo se estiver a utilizar um cliente Linux ou já ter x11 programa de configuração.

1. Transfira o PuTTY e Xming no seu computador Windows:

  * [Transfira o PuTTY](http://www.putty.org/)
  * [Transferir Xming](https://xming.en.softonic.com/)

2.  Depois de instalar o PuTTY, na pasta PuTTY (por exemplo, c:\Programas\Microsoft Files\PuTTY), execute puttygen.exe (gerador de chave PuTTY).

3.  No gerador de chave PuTTY:

  - Para gerar uma chave, selecione o **gerar** botão.
  - Copie o conteúdo da chave (**Ctrl + C**).
  - Selecione o **Guardar chave privada** botão.
  - Ignorar o aviso é apresentado e, em seguida, selecione **OK**.

    ![Captura de ecrã da página PuTTY gerador de chaves](./media/oracle-golden-gate/puttykeygen.png)

4.  Na sua VM, execute estes comandos:

  ```bash
  # sudo su - oracle
  $ mkdir .ssh (if not already created)
  $ cd .ssh
  ```

5. Crie um ficheiro denominado **authorized_keys**. Colar o conteúdo da chave deste ficheiro e, em seguida, guarde o ficheiro.

  > [!NOTE]
  > A chave tem de conter a cadeia `ssh-rsa`. Além disso, o conteúdo da chave tem de ser uma única linha de texto.
  >  

6. Inicie o PuTTY. No **categoria** painel, selecione **ligação** > **SSH** > **Auth**. No **ficheiro de chave privada para autenticação** caixa, navegue para a chave que gerou anteriormente.

  ![Captura de ecrã da página de definir a chave privada](./media/oracle-golden-gate/setprivatekey.png)

7. No **categoria** painel, selecione **ligação** > **SSH** > **X11**. Em seguida, selecione o **reencaminhamento de X11 de ativar** caixa.

  ![Captura de ecrã da página ativar X11](./media/oracle-golden-gate/enablex11.png)

8. No **categoria** painel, aceda a **sessão**. Introduza as informações do anfitrião e, em seguida, selecione **abra**.

  ![Captura de ecrã da página sessão](./media/oracle-golden-gate/puttysession.png)

### <a name="install-golden-gate-software"></a>Instalar software Golden porta

Para instalar a porta de Golden Oracle, execute os seguintes passos:

1. Inicie sessão como oracle. (, Deverá conseguir iniciar sessão sem pedidos para uma palavra-passe.) Certifique-se de que Xming está em execução antes de começar a instalação.
 
  ```bash
  $ cd /opt/fbo_ggs_Linux_x64_shiphome/Disk1
  $ ./runInstaller
  ```
2. Selecione 'GoldenGate Oracle para a base de dados Oracle 12c'. Em seguida, selecione **seguinte** para continuar.

  ![Captura de ecrã da página de instalação selecionar instalador](./media/oracle-golden-gate/golden_gate_install_01.png)

3. Altere a localização do software. Em seguida, selecione o **iniciar o Gestor de** caixa e introduza a localização da base de dados. Selecione **seguinte** para continuar.

  ![Captura de ecrã da página selecionar instalação](./media/oracle-golden-gate/golden_gate_install_02.png)

4. Altere o diretório de inventário e, em seguida, selecione **seguinte** para continuar.

  ![Captura de ecrã da página selecionar instalação](./media/oracle-golden-gate/golden_gate_install_03.png)

5. No **resumo** ecrã, selecione **instalar** para continuar.

  ![Captura de ecrã da página de instalação selecionar instalador](./media/oracle-golden-gate/golden_gate_install_04.png)

6. Poderá ser-lhe pedido para executar um script como 'raiz'. Se assim for, abra uma sessão separada, o ssh para a VM, sudo raiz em seguida, execute o script. Selecione **OK** continuar.

  ![Captura de ecrã da página selecionar instalação](./media/oracle-golden-gate/golden_gate_install_05.png)

7. Quando tiver concluído a instalação, selecione **fechar** para concluir o processo.

  ![Captura de ecrã da página selecionar instalação](./media/oracle-golden-gate/golden_gate_install_06.png)

### <a name="set-up-service-on-myvm1-primary"></a>Configurar o serviço em myVM1 (principal)

1. Criar ou atualizar o ficheiro tnsnames.ora:

  ```bash
  $ cd $ORACLE_HOME/network/admin
  $ vi tnsnames.ora

  cdb1=
    (DESCRIPTION=
      (ADDRESS=
        (PROTOCOL=TCP)
        (HOST=localhost)
        (PORT=1521)
      )
      (CONNECT_DATA=
        (SERVER=dedicated)
        (SERVICE_NAME=cdb1)
      )
    )

  pdb1=
    (DESCRIPTION=
      (ADDRESS=
        (PROTOCOL=TCP)
        (HOST=localhost)
        (PORT=1521)
      )
      (CONNECT_DATA=
        (SERVER=dedicated)
        (SERVICE_NAME=pdb1)
      )
    )
  ```

2. Crie as contas de proprietário e utilizador Golden porta.

  > [!NOTE]
  > A conta de proprietário deve ter o prefixo de C# #.
  >

    ```bash
    $ sqlplus / as sysdba
    SQL> CREATE USER C##GGADMIN identified by ggadmin;
    SQL> EXEC dbms_goldengate_auth.grant_admin_privilege('C##GGADMIN',container=>'ALL');
    SQL> GRANT DBA to C##GGADMIN container=all;
    SQL> connect C##GGADMIN/ggadmin
    SQL> ALTER SESSION SET CONTAINER=PDB1;
    SQL> EXIT;
    ```

3. Crie a conta de utilizador de teste Golden porta:

  ```bash
  $ cd /u01/app/oracle/product/12.1.0/oggcore_1
  $ sqlplus system/OraPasswd1@pdb1
  SQL> CREATE USER test identified by test DEFAULT TABLESPACE USERS TEMPORARY TABLESPACE TEMP;
  SQL> GRANT connect, resource, dba TO test;
  SQL> ALTER USER test QUOTA 100M on USERS;
  SQL> connect test/test@pdb1
  SQL> @demo_ora_create
  SQL> @demo_ora_insert
  SQL> EXIT;
  ```

4. Configure o ficheiro de parâmetros de extração.

 Inicie a interface de linha de comandos de porta dourada (ggsci):

  ```bash
  $ sudo su - oracle
  $ cd /u01/app/oracle/product/12.1.0/oggcore_1
  $ ./ggsci
  GGSCI> DBLOGIN USERID test@pdb1, PASSWORD test
  Successfully logged into database  pdb1
  GGSCI>  ADD SCHEMATRANDATA pdb1.test
  2017-05-23 15:44:25  INFO    OGG-01788  SCHEMATRANDATA has been added on schema test.
  2017-05-23 15:44:25  INFO    OGG-01976  SCHEMATRANDATA for scheduling columns has been added on schema test.

  GGSCI> EDIT PARAMS EXTORA
  ```
5. Adicione o seguinte para o ficheiro de parâmetros de EXTRAÇÃO (utilizando os comandos de vi). Chave de Esc prima, ': wq!' Para guardar o ficheiro. 

  ```bash
  EXTRACT EXTORA
  USERID C##GGADMIN, PASSWORD ggadmin
  RMTHOST 10.0.0.5, MGRPORT 7809
  RMTTRAIL ./dirdat/rt  
  DDL INCLUDE MAPPED
  DDLOPTIONS REPORT 
  LOGALLSUPCOLS
  UPDATERECORDFORMAT COMPACT
  TABLE pdb1.test.TCUSTMER;
  TABLE pdb1.test.TCUSTORD;
  ```
6. Registar extraia - extrair integrada:

  ```bash
  $ cd /u01/app/oracle/product/12.1.0/oggcore_1
  $ ./ggsci

  GGSCI> dblogin userid C##GGADMIN, password ggadmin
  Successfully logged into database CDB$ROOT.

  GGSCI> REGISTER EXTRACT EXTORA DATABASE CONTAINER(pdb1)

  2017-05-23 15:58:34  INFO    OGG-02003  Extract EXTORA successfully registered with database at SCN 1821260.

  GGSCI> exit
  ```
7. Configurar pontos de verificação de extração e iniciar extrair em tempo real:

  ```bash
  $ ./ggsci
  GGSCI>  ADD EXTRACT EXTORA, INTEGRATED TRANLOG, BEGIN NOW
  EXTRACT (Integrated) added.

  GGSCI>  ADD RMTTRAIL ./dirdat/rt, EXTRACT EXTORA, MEGABYTES 10
  RMTTRAIL added.

  GGSCI>  START EXTRACT EXTORA

  Sending START request to MANAGER ...
  EXTRACT EXTORA starting

  GGSCI > info all

  Program     Status      Group       Lag at Chkpt  Time Since Chkpt

  MANAGER     RUNNING
  EXTRACT     RUNNING     EXTORA      00:00:11      00:00:04
  ```
Neste passo, localize o SCN inicial, que será utilizado posteriormente, numa secção diferentes:

  ```bash
  $ sqlplus / as sysdba
  SQL> alter session set container = pdb1;
  SQL> SELECT current_scn from v$database;
  CURRENT_SCN
  -----------
      1857887
  SQL> EXIT;
  ```

  ```bash
  $ ./ggsci
  GGSCI> EDIT PARAMS INITEXT
  ```

  ```bash
  EXTRACT INITEXT
  USERID C##GGADMIN, PASSWORD ggadmin
  RMTHOST 10.0.0.5, MGRPORT 7809
  RMTTASK REPLICAT, GROUP INITREP
  TABLE pdb1.test.*, SQLPREDICATE 'AS OF SCN 1857887'; 
  ```

  ```bash
  GGSCI> ADD EXTRACT INITEXT, SOURCEISTABLE
  ```

### <a name="set-up-service-on-myvm2-replicate"></a>Configurar o serviço no myVM2 (replicar)


1. Criar ou atualizar o ficheiro tnsnames.ora:

  ```bash
  $ cd $ORACLE_HOME/network/admin
  $ vi tnsnames.ora

  cdb1=
    (DESCRIPTION=
      (ADDRESS=
        (PROTOCOL=TCP)
        (HOST=localhost)
        (PORT=1521)
      )
      (CONNECT_DATA=
        (SERVER=dedicated)
        (SERVICE_NAME=cdb1)
      )
    )

  pdb1=
    (DESCRIPTION=
      (ADDRESS=
        (PROTOCOL=TCP)
        (HOST=localhost)
        (PORT=1521)
      )
      (CONNECT_DATA=
        (SERVER=dedicated)
        (SERVICE_NAME=pdb1)
      )
    )
  ```

2. Crie uma conta de replicar:

  ```bash
  $ sqlplus / as sysdba
  SQL> alter session set container = pdb1;
  SQL> create user repuser identified by rep_pass container=current;
  SQL> grant dba to repuser;
  SQL> exec dbms_goldengate_auth.grant_admin_privilege('REPUSER',container=>'PDB1');
  SQL> connect repuser/rep_pass@pdb1 
  SQL> EXIT;
  ```

3. Crie uma conta de utilizador de teste Golden porta:

  ```bash
  $ cd /u01/app/oracle/product/12.1.0/oggcore_1
  $ sqlplus system/OraPasswd1@pdb1
  SQL> CREATE USER test identified by test DEFAULT TABLESPACE USERS TEMPORARY TABLESPACE TEMP;
  SQL> GRANT connect, resource, dba TO test;
  SQL> ALTER USER test QUOTA 100M on USERS;
  SQL> connect test/test@pdb1
  SQL> @demo_ora_create
  SQL> EXIT;
  ```

4. Ficheiro de parâmetros REPLICAT para replicar as alterações: 

  ```bash
  $ cd /u01/app/oracle/product/12.1.0/oggcore_1
  $ ./ggsci
  GGSCI> EDIT PARAMS REPORA  
  ```
  Conteúdo do ficheiro de parâmetros REPORA:

  ```bash
  REPLICAT REPORA
  ASSUMETARGETDEFS
  DISCARDFILE ./dirrpt/repora.dsc, PURGE, MEGABYTES 100
  DDL INCLUDE MAPPED
  DDLOPTIONS REPORT
  DBOPTIONS INTEGRATEDPARAMS(parallelism 6)
  USERID repuser@pdb1, PASSWORD rep_pass
  MAP pdb1.test.*, TARGET pdb1.test.*;
  ```

5. Configure um ponto de verificação replicat:

  ```bash
  GGSCI> ADD REPLICAT REPORA, INTEGRATED, EXTTRAIL ./dirdat/rt
  GGSCI> EDIT PARAMS INITREP

  ```

  ```bash
  REPLICAT INITREP
  ASSUMETARGETDEFS
  DISCARDFILE ./dirrpt/tcustmer.dsc, APPEND
  USERID repuser@pdb1, PASSWORD rep_pass
  MAP pdb1.test.*, TARGET pdb1.test.*;   
  ```

  ```bash
  GGSCI> ADD REPLICAT INITREP, SPECIALRUN
  ```

### <a name="set-up-the-replication-myvm1-and-myvm2"></a>Configurar a replicação (myVM1 e myVM2)

#### <a name="1-set-up-the-replication-on-myvm2-replicate"></a>1. Configurar a replicação em myVM2 (replicar)

  ```bash
  $ cd /u01/app/oracle/product/12.1.0/oggcore_1
  $ ./ggsci
  GGSCI> EDIT PARAMS MGR
  ```
Atualize o ficheiro com o seguinte:

  ```bash
  PORT 7809
  ACCESSRULE, PROG *, IPADDR *, ALLOW
  ```
Em seguida, reinicie o serviço Gestor de:

  ```bash
  GGSCI> STOP MGR
  GGSCI> START MGR
  GGSCI> EXIT
  ```

#### <a name="2-set-up-the-replication-on-myvm1-primary"></a>2. Configurar a replicação em myVM1 (principal)

Inicie o carregamento inicial e verifique a existência de erros:

```bash
$ cd /u01/app/oracle/product/12.1.0/oggcore_1
$ ./ggsci
GGSCI> START EXTRACT INITEXT
GGSCI> VIEW REPORT INITEXT
```
#### <a name="3-set-up-the-replication-on-myvm2-replicate"></a>3. Configurar a replicação em myVM2 (replicar)

Alterar o SCN number com o número que obteve anteriormente:

  ```bash
  $ cd /u01/app/oracle/product/12.1.0/oggcore_1
  $ ./ggsci
  START REPLICAT REPORA, AFTERCSN 1857887
  ```
A replicação foi iniciada e pode testá-lo através da inserção de novos registos para tabelas de teste.


### <a name="view-job-status-and-troubleshooting"></a>Ver tarefa e o estado de resolução de problemas

#### <a name="view-reports"></a>Ver relatórios
Para ver relatórios no myVM1, execute os seguintes comandos:

  ```bash
  GGSCI> VIEW REPORT EXTORA 
  ```
 
Para ver relatórios no myVM2, execute os seguintes comandos:

  ```bash
  GGSCI> VIEW REPORT REPORA
  ```

#### <a name="view-status-and-history"></a>Ver estado e histórico
Para ver o estado e histórico no myVM1, execute os seguintes comandos:

  ```bash
  GGSCI> dblogin userid c##ggadmin, password ggadmin 
  GGSCI> INFO EXTRACT EXTORA, DETAIL
  ```

Para ver o estado e histórico no myVM2, execute os seguintes comandos:

  ```bash
  GGSCI> dblogin userid repuser@pdb1 password rep_pass 
  GGSCI> INFO REP REPORA, DETAIL
  ```
Este passo conclui a instalação e configuração da porta Golden no Oracle linux.


## <a name="delete-the-virtual-machine"></a>Eliminar a máquina virtual

Quando este já não é necessário, pode ser utilizado o seguinte comando para remover o grupo de recursos, a VM e todos os recursos relacionados.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

[Tutorial sobre a criação de máquinas virtuais altamente disponíveis](../../linux/create-cli-complete.md)

[Explorar amostras de CLI de implementação de VM](../../linux/cli-samples.md)
