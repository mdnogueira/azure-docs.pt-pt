---
title: Executar um cluster de MariaDB (MySQL) no Azure | Microsoft Docs
description: "Criar um MariaDB + Galera MySQL cluster em máquinas virtuais do Azure"
services: virtual-machines-linux
documentationcenter: 
author: sabbour
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: d0d21937-7aac-4222-8255-2fdc4f2ea65b
ms.service: virtual-machines-linux
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/15/2015
ms.author: asabbour
ms.openlocfilehash: 53e9bf18b26338212411ea7c4f260eb308486738
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="mariadb-mysql-cluster-azure-tutorial"></a>Cluster MariaDB (MySQL): tutorial do Azure
> [!IMPORTANT]
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [do Azure Resource Manager](../../../resource-manager-deployment-model.md) e clássico. Este artigo abrange o modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo do Azure Resource Manager.

> [!NOTE]
> Cluster de MariaDB Enterprise está agora disponível no Azure Marketplace. A nova oferta irão implementar automaticamente um cluster de MariaDB Galera no Azure Resource Manager. Deve utilizar a nova oferta de [Azure Marketplace](https://azure.microsoft.com/en-us/marketplace/partners/mariadb/cluster-maxscale/).
>
>

Este artigo mostra como criar um mestre Multi [Galera](http://galeracluster.com/products/) cluster de [MariaDBs](https://mariadb.org/en/about/) (um substituto drop-in robusta, escalável e fiável para MySQL) para trabalhar num ambiente altamente disponível em máquinas virtuais do Azure.

## <a name="architecture-overview"></a>Descrição geral da arquitetura
Este artigo descreve como concluir os seguintes passos:

- Crie um cluster de três nós.
- Separe os discos de dados a partir do disco do SO.
- Crie os discos de dados na definição de RAID-0/repartidos para aumentar o IOPS.
- Utilize o Azure Load Balancer para equilibrar a carga para os três nós.
- Para minimizar o trabalho repetitivos, criar uma imagem VM contém MariaDB + Galera e utilizá-la para criar o cluster VMs.

![Arquitetura do sistema](./media/mariadb-mysql-cluster/Setup.png)

> [!NOTE]
> Este tópico utiliza o [CLI do Azure](../../../cli-install-nodejs.md) ferramentas, por isso certifique-se de que transferi-los e ligue-o à sua subscrição do Azure, de acordo com as instruções. Se precisar de uma referência para os comandos disponíveis na CLI do Azure, consulte o [referência de comandos da CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2). Também terá [criar uma chave SSH para a autenticação] e tome nota da localização de ficheiro. pem.
>
>

## <a name="create-the-template"></a>Criar o modelo
### <a name="infrastructure"></a>Infraestrutura
1. Crie um grupo de afinidade para conter os recursos em conjunto.

        azure account affinity-group create mariadbcluster --location "North Europe" --label "MariaDB Cluster"
2. Crie uma rede virtual

        azure network vnet create --address-space 10.0.0.0 --cidr 8 --subnet-name mariadb --subnet-start-ip 10.0.0.0 --subnet-cidr 24 --affinity-group mariadbcluster mariadbvnet
3. Crie uma conta de armazenamento para alojar todos os nossos discos. Não deve colocar mais de 40 discos muito utilizados na mesma conta do storage para evitar atingir o limite de conta de armazenamento IOPS 20.000. Neste caso, está também abaixo esse limite, pelo que irá armazenar tudo na mesma conta de simplicidade.

        azure storage account create mariadbstorage --label mariadbstorage --affinity-group mariadbcluster
4. Localize o nome da imagem de máquina virtual do CentOS 7.

        azure vm image list | findstr CentOS
   O resultado será semelhante ao seguinte `5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20140926`.

   Utilize esse nome no passo seguinte.
5. Criar o modelo VM e substitua /path/to/key.pem com o caminho onde armazenou a chave SSH. pem gerado.

        azure vm create --virtual-network-name mariadbvnet --subnet-names mariadb --blob-url "http://mariadbstorage.blob.core.windows.net/vhds/mariadbhatemplate-os.vhd"  --vm-size Medium --ssh 22 --ssh-cert "/path/to/key.pem" --no-ssh-password mariadbtemplate 5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20140926 azureuser
6. Anexe quatro discos de dados de 500 GB para a VM para utilização na configuração do RAID.

        FOR /L %d IN (1,1,4) DO azure vm disk attach-new mariadbhatemplate 512 http://mariadbstorage.blob.core.windows.net/vhds/mariadbhatemplate-data-%d.vhd
7. Utilizar o SSH para iniciar sessão para o modelo de VM que criou no mariadbhatemplate.cloudapp.net:22 e ligar utilizando a chave privada.

### <a name="software"></a>Software
1. Obter a raiz.

        sudo su

2. Instale o suporte RAID:

    a. Instale mdadm.

              yum install mdadm

    b. Crie a configuração de RAID0/stripe com um sistema de ficheiros EXT4.

              mdadm --create --verbose /dev/md0 --level=stripe --raid-devices=4 /dev/sdc /dev/sdd /dev/sde /dev/sdf
              mdadm --detail --scan >> /etc/mdadm.conf
              mkfs -t ext4 /dev/md0
    c. Crie o diretório de ponto de montagem.

              mkdir /mnt/data
    d. Obter o UUID do dispositivo RAID recentemente criado.

              blkid | grep /dev/md0
    e. Edite /etc/fstab.

              vi /etc/fstab
    f. Adicionar o dispositivo para ativar a montagem automática no reinício, substituindo o UUID com o valor obtido a partir do anterior **blkid** comando.

              UUID=<UUID FROM PREVIOUS>   /mnt/data ext4   defaults,noatime   1 2
    g. Monte a nova partição.

              mount /mnt/data

3. Instale MariaDB.

    a. Crie o ficheiro MariaDB.repo.

                vi /etc/yum.repos.d/MariaDB.repo

    b. Preencha o ficheiro do repositório com o seguinte conteúdo:

              [mariadb]
              name = MariaDB
              baseurl = http://yum.mariadb.org/10.0/centos7-amd64
              gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
              gpgcheck=1
    c. Para evitar conflitos, remova sufixo existente e mariadb libs.

           yum remove postfix mariadb-libs-*
    d. Instale MariaDB com Galera.

           yum install MariaDB-Galera-server MariaDB-client galera

4. Mova o diretório de dados MySQL no dispositivo de bloco RAID.

    a. Copie o atual diretório de MySQL para a nova localização e remover o diretório antigo.

           cp -avr /var/lib/mysql /mnt/data  
           rm -rf /var/lib/mysql
    b. Defina as permissões para o novo diretório em conformidade.

           chown -R mysql:mysql /mnt/data && chmod -R 755 /mnt/data/

    c. Crie um symlink que aponta o diretório antigo para a nova localização na partição de RAID.

           ln -s /mnt/data/mysql /var/lib/mysql

5. Porque [SELinux interfere com as operações de cluster](http://galeracluster.com/documentation-webpages/configuration.html#selinux), é necessário desativá-los para a sessão atual. Editar `/etc/selinux/config` desativá-los para reinícios subsequentes.

            setenforce 0

            then editing `/etc/selinux/config` to set `SELINUX=permissive`
6. Valide executa MySQL.

   a. Inicie o MySQL.

           service mysql start
   b. Proteger a instalação do MySQL, defina a palavra-passe de raiz, remover utilizadores anónimos para desativar o início de sessão remoto raiz e remover a base de dados de teste.

           mysql_secure_installation
   c. Crie um utilizador na base de dados para operações de cluster e, opcionalmente, para as suas aplicações.

           mysql -u root -p
           GRANT ALL PRIVILEGES ON *.* TO 'cluster'@'%' IDENTIFIED BY 'p@ssw0rd' WITH GRANT OPTION; FLUSH PRIVILEGES;
           exit

   d. Pare MySQL.

            service mysql stop
7. Crie um marcador de posição de configuração.

   a. Edite a configuração de MySQL para criar um marcador de posição para as definições do cluster. Não substituir o  **`<Variables>`**  ou anule o comentário agora. Que irá ocorrer depois de criar uma VM a partir deste modelo.

            vi /etc/my.cnf.d/server.cnf
   b. Editar o  **[galera]**  secção e desmarcá-la a.

   c. Editar o **[mariadb]** secção.

           wsrep_provider=/usr/lib64/galera/libgalera_smm.so
           binlog_format=ROW
           wsrep_sst_method=rsync
           bind-address=0.0.0.0 # When set to 0.0.0.0, the server listens to remote connections
           default_storage_engine=InnoDB
           innodb_autoinc_lock_mode=2

           wsrep_sst_auth=cluster:p@ssw0rd # CHANGE: Username and password you created for the SST cluster MySQL user
           #wsrep_cluster_name='mariadbcluster' # CHANGE: Uncomment and set your desired cluster name
           #wsrep_cluster_address="gcomm://mariadb1,mariadb2,mariadb3" # CHANGE: Uncomment and Add all your servers
           #wsrep_node_address='<ServerIP>' # CHANGE: Uncomment and set IP address of this server
           #wsrep_node_name='<NodeName>' # CHANGE: Uncomment and set the node name of this server
8. Abra as portas necessárias na firewall utilizando FirewallD em CentOS 7.

   * MySQL:`firewall-cmd --zone=public --add-port=3306/tcp --permanent`
   * GALERA:`firewall-cmd --zone=public --add-port=4567/tcp --permanent`
   * GALERA IST:`firewall-cmd --zone=public --add-port=4568/tcp --permanent`
   * RSYNC:`firewall-cmd --zone=public --add-port=4444/tcp --permanent`
   * Recarregar a firewall:`firewall-cmd --reload`

9. Otimize o sistema para o desempenho. Para obter mais informações, consulte [estratégia de otimização do desempenho](optimize-mysql.md).

   a. Edite o ficheiro de configuração MySQL novamente.

            vi /etc/my.cnf.d/server.cnf
   b. Editar o **[mariadb]** secção e acrescentar o seguinte conteúdo:

   > [!NOTE]
   > Recomendamos que innodb\_memória intermédia\_pool_size é 70 por cento da memória da VM. Neste exemplo, ter sido definido no 2.45 GB para a média de VM do Azure com 3.5 GB de RAM.
   >
   >

           innodb_buffer_pool_size = 2508M # The buffer pool contains buffered data and the index. This is usually set to 70 percent of physical memory.
           innodb_log_file_size = 512M #  Redo logs ensure that write operations are fast, reliable, and recoverable after a crash
           max_connections = 5000 # A larger value will give the server more time to recycle idled connections
           innodb_file_per_table = 1 # Speed up the table space transmission and optimize the debris management performance
           innodb_log_buffer_size = 128M # The log buffer allows transactions to run without having to flush the log to disk before the transactions commit
           innodb_flush_log_at_trx_commit = 2 # The setting of 2 enables the most data integrity and is suitable for Master in MySQL cluster
           query_cache_size = 0
10. Parar MySQL, desativar o serviço de MySQL no arranque para evitar perturbar o cluster, ao adicionar um nó e retirar o aprovisionamento da máquina.

        service mysql stop
        chkconfig mysql off
        waagent -deprovision
11. Capture a VM através do portal. (Atualmente, [emitir #1268 nas ferramentas do CLI do Azure](https://github.com/Azure/azure-xplat-cli/issues/1268) descreve o facto de que as imagens capturadas pelas ferramentas da CLI do Azure não capturar os discos de dados anexados.)

    a. Encerre a máquina através do portal.

    b. Clique em **capturar** e especifique o nome de imagem como **imagem de galera mariadb**. Forneça uma descrição e selecione "Executei waagent."
      
      ![Capturar a máquina virtual](./media/mariadb-mysql-cluster/Capture2.PNG)

## <a name="create-the-cluster"></a>Criar o cluster
Crie três VMs com o modelo criado e, em seguida, configurar e iniciar o cluster.

1. Crie primeira CentOS 7 VM da imagem de imagem de galera mariadb que criou, fornecendo as seguintes informações:

 - Nome da rede virtual: mariadbvnet
 - Sub-rede: mariadb
 - Tamanho do computador: médio
 - Nome do serviço de nuvem: mariadbha (ou qualquer nome que pretende ser acedido através de mariadbha.cloudapp.net)
 - Nome do computador: mariadb1
 - Nome de utilizador: azureuser
 - Acesso SSH: ativado
 - Transmitir o ficheiro. pem de certificado SSH e substituir /path/to/key.pem com o caminho onde armazenou a chave SSH. pem gerado.

   > [!NOTE]
   > Os seguintes comandos estão divididos através de várias linhas para efeitos de clareza, mas deve introduzir cada como uma linha.
   >
   >
        azure vm create
        --virtual-network-name mariadbvnet
        --subnet-names mariadb
        --availability-set clusteravset
        --vm-size Medium
        --ssh-cert "/path/to/key.pem"
        --no-ssh-password
        --ssh 22
        --vm-name mariadb1
        mariadbha mariadb-galera-image azureuser
2. Crie duas máquinas virtuais mais ao ligá-las para o serviço de nuvem mariadbha. Altere o nome da VM e a porta SSH para uma porta exclusiva não em conflito com outras VMs no mesmo serviço em nuvem.

        azure vm create
        --virtual-network-name mariadbvnet
        --subnet-names mariadb
        --availability-set clusteravset
        --vm-size Medium
        --ssh-cert "/path/to/key.pem"
        --no-ssh-password
        --ssh 23
        --vm-name mariadb2
        --connect mariadbha mariadb-galera-image azureuser
  Para MariaDB3:

        azure vm create
        --virtual-network-name mariadbvnet
        --subnet-names mariadb
        --availability-set clusteravset
        --vm-size Medium
        --ssh-cert "/path/to/key.pem"
        --no-ssh-password
        --ssh 24
        --vm-name mariadb3
        --connect mariadbha mariadb-galera-image azureuser
3. Terá de obter um endereço IP de cada uma das três VMs para o passo seguinte:

    ![Obter o endereço IP](./media/mariadb-mysql-cluster/IP.png)
4. Utilize o SSH para iniciar sessão para as três VMs e edite o ficheiro de configuração em cada um deles.

        sudo vi /etc/my.cnf.d/server.cnf

    Anule o comentário  **`wsrep_cluster_name`**  e  **`wsrep_cluster_address`**  removendo a  **#**  no início da linha.
    Além disso, substitua  **`<ServerIP>`**  no  **`wsrep_node_address`**  e  **`<NodeName>`**  no  **`wsrep_node_name`**  com endereço IP da VM e name, respetivamente, e anule os comentários, bem como estas linhas.
5. Início do cluster no MariaDB1 e permitir que o mesmo são executados no arranque.

        sudo service mysql bootstrap
        chkconfig mysql on
6. Iniciar o MySQL MariaDB2 e MariaDB3 e permitir que o mesmo são executados no arranque.

        sudo service mysql start
        chkconfig mysql on

## <a name="load-balance-the-cluster"></a>O cluster de balanceamento de carga
Quando tiver criado as VMs em cluster, adicionou-los para um conjunto de disponibilidade chamado clusteravset para se certificar de que colocam em diferentes domínios de falhas e de atualização e esse Azure nunca efetua a manutenção em todas as máquinas em simultâneo. Esta configuração cumpre os requisitos para suportar o contrato de nível de serviço do Azure (SLA).

Agora utilize o Balanceador de carga do Azure para balancear pedidos entre os três nós.

Execute os seguintes comandos no seu computador, utilizando a CLI do Azure.

A estrutura de parâmetros de comando é:`azure vm endpoint create-multiple <MachineName> <PublicPort>:<VMPort>:<Protocol>:<EnableDirectServerReturn>:<Load Balanced Set Name>:<ProbeProtocol>:<ProbePort>`

    azure vm endpoint create-multiple mariadb1 3306:3306:tcp:false:MySQL:tcp:3306
    azure vm endpoint create-multiple mariadb2 3306:3306:tcp:false:MySQL:tcp:3306
    azure vm endpoint create-multiple mariadb3 3306:3306:tcp:false:MySQL:tcp:3306

A CLI define o intervalo de sonda do Balanceador de carga para 15 segundos, que pode ser um pouco demasiado longos. Alterá-la no portal em **pontos finais** para qualquer uma das VMs.

![Edite o ponto final](./media/mariadb-mysql-cluster/Endpoint.PNG)

Selecione **reconfigurar o conjunto com balanceamento de carga**.

![Reconfigure o conjunto com balanceamento de carga](./media/mariadb-mysql-cluster/Endpoint2.PNG)

Alteração **sonda intervalo** para 5 segundos e guarde as alterações.

![Intervalo de sonda de alteração](./media/mariadb-mysql-cluster/Endpoint3.PNG)

## <a name="validate-the-cluster"></a>Validar o cluster
O trabalho está concluído. O cluster deve ser agora acessível em `mariadbha.cloudapp.net:3306`, que chega a carga balanceador e encaminhar pedidos entre as três VMs facilmente e eficiente.

Utilize o cliente MySQL favorito para ligar ou ligar a partir de uma das VMs para verificar se este cluster está a funcionar.

     mysql -u cluster -h mariadbha.cloudapp.net -p

Em seguida, crie uma base de dados e preenchê-lo com alguns dados.

    CREATE DATABASE TestDB;
    USE TestDB;
    CREATE TABLE TestTable (id INT NOT NULL AUTO_INCREMENT PRIMARY KEY, value VARCHAR(255));
    INSERT INTO TestTable (value)  VALUES ('Value1');
    INSERT INTO TestTable (value)  VALUES ('Value2');
    SELECT * FROM TestTable;

A base de dados que criou devolve a tabela seguinte:

    +----+--------+
    | id | value  |
    +----+--------+
    |  1 | Value1 |
    |  4 | Value2 |
    +----+--------+
    2 rows in set (0.00 sec)

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Passos seguintes
Neste artigo, criou um nó de três MariaDB + Galera cluster altamente disponível no Azure virtual máquinas em execução CentOS 7. As VMs têm balanceada com o Azure Load Balancer de carga.

Pode querer ver [outra forma de cluster MySQL no Linux](mysql-cluster.md) e formas de [otimizar e testar o desempenho de MySQL em VMs do Linux do Azure](optimize-mysql.md).

<!--Anchors-->
[Architecture overview]:#architecture-overview
[Creating the template]:#creating-the-template
[Creating the cluster]:#creating-the-cluster
[Load balancing the cluster]:#load-balancing-the-cluster
[Validating the cluster]:#validating-the-cluster
[Next steps]:#next-steps

<!--Image references-->

<!--Link references-->
[Galera]:http://galeracluster.com/products/
[MariaDBs]:https://mariadb.org/en/about/
[criar uma chave SSH para a autenticação]:http://www.jeff.wilcox.name/2013/06/secure-linux-vms-with-ssh-certificates/
[issue #1268 in the Azure CLI]:https://github.com/Azure/azure-xplat-cli/issues/1268
