---
title: Criar uma base de dados Oracle numa VM do Azure | Microsoft Docs
description: "Obter rapidamente uma base de dados de 12c de base de dados Oracle cópias de segurança e em execução no seu ambiente do Azure."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: rickstercdn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/17/2017
ms.author: rclaus
ms.openlocfilehash: 8683b016c4db2c66fb1dd994405b70c3d137a7fc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-oracle-database-in-an-azure-vm"></a>Criar uma base de dados Oracle numa VM do Azure

Detalhes deste Guia utilizando a CLI do Azure para implementar uma máquina virtual do Azure a partir de [imagem de galeria do marketplace Oracle](https://azuremarketplace.microsoft.com/marketplace/apps/Oracle.OracleDatabase12102EnterpriseEdition?tab=Overview) para criar uma base de dados Oracle 12 c. Depois do servidor é implementado, irá ligar através de SSH para configurar a base de dados Oracle. 

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e usar a CLI localmente, este tópico requer a execução da versão 2.0.4 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar o Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#create). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. 

O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```
## <a name="create-virtual-machine"></a>Criar a máquina virtual

Para criar uma máquina virtual (VM), utilize o [az vm criar](/cli/azure/vm#create) comando. 

O exemplo seguinte cria uma VM com o nome `myVM`. Também cria chaves SSH, se estes ainda não existir numa localização chave predefinido. Para utilizar um conjunto específico de chaves, utilize a opção `--ssh-key-value`.  

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
    --size Standard_DS2_v2 \
    --admin-username azureuser \
    --generate-ssh-keys
```

Depois de criar a VM, CLI do Azure apresenta informações semelhantes ao seguinte exemplo. Tenha em atenção o valor para `publicIpAddress`. Utilize este endereço aceda à VM.

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/{snip}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westus",
  "macAddress": "00-0D-3A-36-2F-56",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.64.104.241",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="connect-to-the-vm"></a>Ligar à VM

Para criar uma sessão SSH com a VM, utilize o seguinte comando. Substituir o endereço IP com o `publicIpAddress` valor para a VM.

```bash 
ssh <publicIpAddress>
```

## <a name="create-the-database"></a>Criar a base de dados

O software Oracle já está instalado na imagem do Marketplace. Crie uma base de dados de exemplo da seguinte forma. 

1.  Mudar para o *oracle* Superutilizador e inicializar o serviço de escuta para o registo:

    ```bash
    $ sudo su - oracle
    $ lsnrctl start
    ```

    O resultado é semelhante ao seguinte:

    ```bash
    Copyright (c) 1991, 2014, Oracle.  All rights reserved.

    Starting /u01/app/oracle/product/12.1.0/dbhome_1/bin/tnslsnr: please wait...

    TNSLSNR for Linux: Version 12.1.0.2.0 - Production
    Log messages written to /u01/app/oracle/diag/tnslsnr/myVM/listener/alert/log.xml
    Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=myVM.twltkue3xvsujaz1bvlrhfuiwf.dx.internal.cloudapp.net)(PORT=1521)))

    Connecting to (ADDRESS=(PROTOCOL=tcp)(HOST=)(PORT=1521))
    STATUS of the LISTENER
    ------------------------
    Alias                     LISTENER
    Version                   TNSLSNR for Linux: Version 12.1.0.2.0 - Production
    Start Date                23-MAR-2017 15:32:08
    Uptime                    0 days 0 hr. 0 min. 0 sec
    Trace Level               off
    Security                  ON: Local OS Authentication
    SNMP                      OFF
    Listener Log File         /u01/app/oracle/diag/tnslsnr/myVM/listener/alert/log.xml
    Listening Endpoints Summary...
    (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=myVM.twltkue3xvsujaz1bvlrhfuiwf.dx.internal.cloudapp.net)(PORT=1521)))
    The listener supports no services
    The command completed successfully
    ```

2.  Crie a base de dados:

    ```bash
    dbca -silent \
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

    Demora alguns minutos para criar a base de dados.

3. Definir variáveis de Oracle

Antes de ligar, tem de definir duas variáveis de ambiente: *ORACLE_HOME* e *ORACLE_SID*.

```bash
ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME
ORACLE_SID=cdb1; export ORACLE_SID
```
Também pode adicionar as variáveis ORACLE_HOME e ORACLE_SID para o ficheiro .bashrc. Isto seria guardar as variáveis de ambiente para inícios de sessão futuras. Confirme que foram adicionadas as seguintes instruções para o `~/.bashrc` ficheiro utilizando o editor à sua escolha.

```bash
# Add ORACLE_HOME. 
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1 
# Add ORACLE_SID. 
export ORACLE_SID=cdb1 
```

## <a name="oracle-em-express-connectivity"></a>Conectividade de IT rápida Oracle

Para obter uma ferramenta de gestão GUI que pode utilizar para explorar a base de dados, defina Oracle It Express. Para ligar a Oracle It Express, tem de configurar primeiro a porta no Oracle. 

1. Ligar à base de dados utilizando sqlplus:

    ```bash
    sqlplus / as sysdba
    ```

2. Assim que estiver ligado, defina a porta 5502 para IT rápida

    ```bash
    exec DBMS_XDB_CONFIG.SETHTTPSPORT(5502);
    ```

3. Abra o contentor PDB1 se não estiver já está aberto, mas, primeiro verifique o estado:

    ```bash
    select con_id, name, open_mode from v$pdbs;
    ```

    O resultado é semelhante ao seguinte:

    ```bash
      CON_ID NAME                           OPEN_MODE 
      ----------- ------------------------- ---------- 
      2           PDB$SEED                  READ ONLY 
      3           PDB1                      MOUNT
    ```

4. Se o OPEN_MODE para `PDB1` não é de leitura de escrita, em seguida, execute os comandos seguinte para abrir PDB1:

   ```bash
    alter session set container=pdb1;
    alter database open;
   ```

É necessário para o tipo `quit` para terminar a sessão de sqlplus e tipo `exit` a fim de sessão do utilizador oracle.

## <a name="automate-database-startup-and-shutdown"></a>Automatizar a base de dados arranque e encerramento

A base de dados Oracle por predefinição não iniciar automaticamente quando reiniciar a VM. Para configurar a base de dados Oracle para iniciar automaticamente, primeiro inicie sessão como raiz. Em seguida, criar e atualizar alguns ficheiros de sistema.

1. Iniciar sessão como raiz
    ```bash
    sudo su -
    ```

2.  Utilizando o seu editor favorito, edite o ficheiro `/etc/oratab` e alterar a predefinição `N` para `Y`:

    ```bash
    cdb1:/u01/app/oracle/product/12.1.0/dbhome_1:Y
    ```

3.  Crie um ficheiro denominado `/etc/init.d/dbora` e cole o seguinte conteúdo:

    ```
    #!/bin/sh
    # chkconfig: 345 99 10
    # Description: Oracle auto start-stop script.
    #
    # Set ORA_HOME to be equivalent to $ORACLE_HOME.
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle

    case "$1" in
    'start')
        # Start the Oracle databases:
        # The following command assumes that the Oracle sign-in
        # will not prompt the user for any values.
        # Remove "&" if you don't want startup as a background process.
        su - $ORA_OWNER -c "$ORA_HOME/bin/dbstart $ORA_HOME" &
        touch /var/lock/subsys/dbora
        ;;

    'stop')
        # Stop the Oracle databases:
        # The following command assumes that the Oracle sign-in
        # will not prompt the user for any values.
        su - $ORA_OWNER -c "$ORA_HOME/bin/dbshut $ORA_HOME" &
        rm -f /var/lock/subsys/dbora
        ;;
    esac
    ```

4.  Alterar permissões em ficheiros com *chmod* da seguinte forma:

    ```bash
    chgrp dba /etc/init.d/dbora
    chmod 750 /etc/init.d/dbora
    ```

5.  Crie ligações simbólicas de arranque e encerramento da seguinte forma:

    ```bash
    ln -s /etc/init.d/dbora /etc/rc.d/rc0.d/K01dbora
    ln -s /etc/init.d/dbora /etc/rc.d/rc3.d/S99dbora
    ln -s /etc/init.d/dbora /etc/rc.d/rc5.d/S99dbora
    ```

6.  Para testar as suas alterações, reinicie a VM:

    ```bash
    reboot
    ```

## <a name="open-ports-for-connectivity"></a>Portas abertas para conectividade

A tarefa final consiste em configurar alguns pontos finais externos. Para configurar o grupo de segurança de rede do Azure que protege a VM, sair primeiro a sua sessão SSH na VM (deve ter sido kicked fora do SSH ao ser reiniciado no passo anterior). 

1.  Para abrir o ponto final que utilizar para aceder à base de dados Oracle remotamente, crie uma regra de grupo de segurança de rede com [criar regras de nsg de rede az](/cli/azure/network/nsg/rule#create) da seguinte forma: 

    ```azurecli-interactive
    az network nsg rule create \
        --resource-group myResourceGroup\
        --nsg-name myVmNSG \
        --name allow-oracle \
        --protocol tcp \
        --priority 1001 \
        --destination-port-range 1521
    ```

2.  Para abrir o ponto final que utilizar para aceder remotamente Oracle It Express, crie uma regra de grupo de segurança de rede com [criar regras de nsg de rede az](/cli/azure/network/nsg/rule#create) da seguinte forma:

    ```azurecli-interactive
    az network nsg rule create \
        --resource-group myResourceGroup \
        --nsg-name myVmNSG \
        --name allow-oracle-EM \
        --protocol tcp \
        --priority 1002 \
        --destination-port-range 5502
    ```

3. Se necessário, obter o endereço IP público da sua VM com [mostrar de ip público de rede az](/cli/azure/network/public-ip#show) da seguinte forma:

    ```azurecli-interactive
    az network public-ip show \
        --resource-group myResourceGroup \
        --name myVMPublicIP \
        --query [ipAddress] \
        --output tsv
    ```

4.  Ligar-se EM Express a partir do seu browser. Certifique-se de que o browser é compatível com rápida IT (é necessária a instalação Flash): 

    ```
    https://<VM ip address or hostname>:5502/em
    ```

Pode iniciar sessão utilizando o **SYS** conta e verifique o **como sysdba** caixa de verificação. Utilize a palavra-passe **OraPasswd1** que definiu durante a instalação. 

![Captura de ecrã da página de início de sessão Oracle OEM Express](./media/oracle-quick-start/oracle_oem_express_login.png)

## <a name="clean-up-resources"></a>Limpar recursos

Depois de terminar a explorar a sua primeira base de dados Oracle no Azure e a VM já não é necessário, pode utilizar o [eliminação do grupo de az](/cli/azure/group#delete) comando para remover o grupo de recursos, VM, e todos os recursos relacionados.

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre outros [soluções Oracle no Azure](oracle-considerations.md). 

Repita o [instalar e configurar Oracle automatizada gestão de armazenamento](configure-oracle-asm.md) tutorial.