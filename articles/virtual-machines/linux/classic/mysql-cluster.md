---
title: Clusterize MySQL com conjuntos com balanceamento de carga | Microsoft Docs
description: "Configurar uma balanceamento de carga, de elevada disponibilidade cluster Linux MySQL criado com o modelo de implementação clássico no Azure"
services: virtual-machines-linux
documentationcenter: 
author: bureado
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 6c413a16-e9b5-4ffe-a8a3-ae67046bbdf3
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/14/2015
ms.author: jparrel
ms.openlocfilehash: be178a59f7c3558cc0fef89e5529893f160c649b
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2017
---
# <a name="use-load-balanced-sets-to-clusterize-mysql-on-linux"></a>Utilizar conjuntos com balanceamento de carga para clusterize MySQL no Linux
> [!IMPORTANT]
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [do Azure Resource Manager](../../../resource-manager-deployment-model.md) e clássico. Este artigo cobre a utilização do modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager. A [modelo do Resource Manager](https://azure.microsoft.com/documentation/templates/mysql-replication/) está disponível se tiver de implementar um cluster MySQL.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

Este artigo explicar e ilustra diferentes abordagens disponíveis para implementar elevados serviços baseados em Linux no Microsoft Azure, a explorar a elevada disponibilidade do servidor de MySQL como um manual. Um vídeo que ilustra esta abordagem não está disponível na [Channel 9](http://channel9.msdn.com/Blogs/Open/Load-balancing-highly-available-Linux-services-on-Windows-Azure-OpenLDAP-and-MySQL).

Iremos irá descrevem uma solução de elevada disponibilidade de MySQL nada partilhado, dois nós, mestre única com base em DRBD, Corosync e Pacemaker. Apenas um nó executa MySQL cada vez. Leitura e escrita a partir do recurso DRBD também estão limitado a apenas um nó de cada vez.

Não é necessário para uma solução de VIP como LVS, porque irá utilizar conjuntos com balanceamento de carga no Microsoft Azure para fornecer a deteção de funcionalidades e o ponto final de round robin, a remoção e a recuperação correto de VIP. O VIP é um endereço IPv4 globalmente encaminhável atribuído pelo Microsoft Azure quando cria pela primeira vez o serviço em nuvem.

Existem outras arquiteturas possíveis para MySQL, incluindo NBD Cluster, Percona, Galera e várias soluções de middleware, incluindo, pelo menos, um disponível como uma VM na [VM depósito](http://vmdepot.msopentech.com). Desde que estas soluções podem replicar em unicast vs. multicast ou difusão e não pode confiar no armazenamento partilhado ou de várias interfaces de rede, os cenários devem ser fácil de implementar no Microsoft Azure.

Estes clustering arquiteturas podem ser expandidos para outros produtos como PostgreSQL e OpenLDAP de forma semelhante. Por exemplo, este procedimento balanceamento de carga com nada partilhado com êxito foi testado com multi mestre OpenLDAP e pode vê-lo no nosso blogue Channel 9.

## <a name="get-ready"></a>Prepare-se
Terá dos seguintes recursos e capacidades:

  - Uma conta do Microsoft Azure com uma subscrição válida, é possível criar, pelo menos, duas VMs (XS foi utilizado neste exemplo)
  - Uma rede e uma sub-rede
  - Um grupo de afinidade
  - Um conjunto de disponibilidade
  - A capacidade de criar o VHD na mesma região que o serviço em nuvem e anexe-os para as VMs do Linux

### <a name="tested-environment"></a>Ambiente testada
* Ubuntu 13.10
  * DRBD
  * Servidor de MySQL
  * Corosync e Pacemaker

### <a name="affinity-group"></a>Grupo de afinidade
Criar um grupo de afinidade para a solução ao iniciar sessão no portal clássico do Azure, selecionar **definições**e criar um grupo de afinidade. Recursos alocados criados posteriormente serão atribuídos a este grupo de afinidade.

### <a name="networks"></a>Redes
É criada uma nova rede e uma sub-rede é criada dentro da rede. Este exemplo utiliza uma rede 10.10.10.0/24 com /24 apenas uma sub-rede no interior.

### <a name="virtual-machines"></a>Máquinas virtuais
O primeiro 13.10 VM com Ubuntu é criada utilizando uma imagem de Endorsed Ubuntu Galeria e é denominada `hadb01`. É criado um novo serviço em nuvem no processo, designado hadb. Este nome ilustra a natureza partilhada, balanceamento de carga com o serviço será quando são adicionados mais recursos. A criação de `hadb01` está concluída e uneventful através do portal. Um ponto final de SSH é automaticamente criado e a nova rede está selecionada. Agora, pode criar um conjunto de disponibilidade para as VMs.

Depois de criar a primeira VM (tecnicamente, quando é criado o serviço em nuvem), criar a VM segundo, `hadb02`. Para a segunda VM, utilize Ubuntu 13.10 VM na galeria do através do portal, mas um serviço em nuvem existente, `hadb.cloudapp.net`, em vez de criar um novo. O conjunto de disponibilidade e da rede deve estar selecionado automaticamente. Será possível criar um ponto final SSH, demasiado.

Depois de criar ambas as VMs, tome nota da porta SSH para `hadb01` (TCP 22) e `hadb02` (automaticamente atribuído pelo Azure).

### <a name="attached-storage"></a>Armazenamento ligado
Anexe um novo disco para ambas as VMs e criar discos de 5 GB no processo. Os discos estão alojados no contentor de VHD em utilização para os discos de principais do sistema operativo. Depois de discos são criados e ligados, não é necessário reiniciar o Linux porque o kernel irá ver o novo dispositivo. Este dispositivo é normalmente `/dev/sdc`. Verifique `dmesg` para a saída.

Em cada VM, crie uma partição utilizando `cfdisk` (primária, Linux e partição) e escrever a nova tabela de partição. Não crie um sistema de ficheiros nesta partição.

## <a name="set-up-the-cluster"></a>Configurar o cluster
Utilize APT para instalar Corosync, Pacemaker e DRBD em ambas as VMs Ubuntu. Para fazê-lo com `apt-get`, execute o seguinte código:

    sudo apt-get install corosync pacemaker drbd8-utils.

Não instale o MySQL neste momento. Debian e Ubuntu scripts de instalação irão inicializar um diretório de dados MySQL no `/var/lib/mysql`, porque o diretório tiver sido substituído por um sistema de ficheiros DRBD, precisa de instalar o MySQL mais tarde.

Certifique-se (utilizando `/sbin/ifconfig`) que ambas as VMs estão a utilizar endereços da sub-rede 10.10.10.0/24 e que estes podem enviar ping a si por nome. Também pode utilizar `ssh-keygen` e `ssh-copy-id` para se certificar de que ambas as VMs podem comunicar através de SSH sem exigir uma palavra-passe.

### <a name="set-up-drbd"></a>Configurar DRBD
Crie um recurso DRBD que utiliza o subjacente `/dev/sdc1` partição para produzir um `/dev/drbd1` recursos que podem ser formatado utilizando ext3 e utilizado em nós principais e secundários.

1. Abra `/etc/drbd.d/r0.res` e copie a seguinte definição de recurso em ambas as VMs:

        resource r0 {
          on `hadb01` {
            device  /dev/drbd1;
            disk   /dev/sdc1;
            address  10.10.10.4:7789;
            meta-disk internal;
          }
          on `hadb02` {
            device  /dev/drbd1;
            disk   /dev/sdc1;
            address  10.10.10.5:7789;
            meta-disk internal;
          }
        }

2. Inicializar o recurso utilizando `drbdadm` em ambas as VMs:

        sudo drbdadm -c /etc/drbd.conf role r0
        sudo drbdadm up r0

3. Na VM principal (`hadb01`), forçar a propriedade (principal) do recurso DRBD:

        sudo drbdadm primary --force r0

Se examinar o conteúdo da/proc/drbd (`sudo cat /proc/drbd`) em ambas as VMs, deverá ver `Primary/Secondary` no `hadb01` e `Secondary/Primary` no `hadb02`, consistente com a solução neste momento. O disco de 5 GB é sincronizado através da rede 10.10.10.0/24, sem encargos aos clientes.

Depois do disco está sincronizado, pode criar o sistema de ficheiros no `hadb01`. Para fins de teste, utilizámos ext2, mas o código seguinte, irá criar um sistema de ficheiros ext3:

    mkfs.ext3 /dev/drbd1

### <a name="mount-the-drbd-resource"></a>O recurso DRBD de montagem
Agora, está pronto para montar os recursos DRBD no `hadb01`. Utilize debian e derivados `/var/lib/mysql` como diretório de dados do MySQL. Uma vez que ainda não instalou o MySQL, criar o diretório e Monte o recurso DRBD. Para executar esta opção, execute o seguinte código no `hadb01`:

    sudo mkdir /var/lib/mysql
    sudo mount /dev/drbd1 /var/lib/mysql

## <a name="set-up-mysql"></a>Configurar o MySQL
Agora está pronto para instalar o MySQL no `hadb01`:

    sudo apt-get install mysql-server

Para `hadb02`, tem duas opções. Pode instalar o mysql-servidor, que irá criar /var/lib/mysql, preencha-o com um novo diretório de dados e, em seguida, remova o conteúdo. Para executar esta opção, execute o seguinte código no `hadb02`:

    sudo apt-get install mysql-server
    sudo service mysql stop
    sudo rm –rf /var/lib/mysql/*

A segunda opção é a ativação pós-falha `hadb02` e, em seguida, instale o mysql-servidor não existe. Scripts de instalação vai reparar que a instalação existente e não o touch.

Execute o seguinte código no `hadb01`:

    sudo drbdadm secondary –force r0

Execute o seguinte código no `hadb02`:

    sudo drbdadm primary –force r0
    sudo apt-get install mysql-server

Se não tiver intenção de ativação pós-falha DRBD agora, a primeira opção é mais fácil embora possivelmente menos elegante. Depois de configurar esta opção, pode começar a trabalhar na base de dados MySQL. Execute o seguinte código no `hadb02` (ou qualquer um dos servidores estiver ativo, de acordo com DRBD):

    mysql –u root –p
    CREATE DATABASE azureha;
    CREATE TABLE things ( id SERIAL, name VARCHAR(255) );
    INSERT INTO things VALUES (1, "Yet another entity");
    GRANT ALL ON things.\* TO root;

> [!WARNING]
> Esta última instrução eficazmente desativa a autenticação para o utilizador raiz nesta tabela. Isto deve ser substituído pelo seu nível de produção conceder instruções e está incluído apenas para fins de ilustrativa.

Se pretender efetuar consultas fora as VMs (que é o objetivo deste guia), terá também de ativar redes para MySQL. Em ambas as VMs, abra `/etc/mysql/my.cnf` e aceda a `bind-address`. Altere o endereço de 127.0.0.1 como 0.0.0.0. Depois de guardar o ficheiro, emitir uma `sudo service mysql restart` no servidor primário atual.

### <a name="create-the-mysql-load-balanced-set"></a>Criar o conjunto com balanceamento de carga do MySQL
Voltar para o portal, aceda a `hadb01`e escolha **pontos finais**. Para criar um ponto final, escolha o MySQL (3306 de TCP) na lista pendente e selecione **conjunto com balanceamento de carga novo criar**. O ponto final com balanceamento de carga de nomes `lb-mysql`. Definir **tempo** para 5 segundos, mínimo.

Depois de criar o ponto final, aceda a `hadb02`, escolha **pontos finais**e criar um ponto final. Escolha `lb-mysql`e, em seguida, selecione o MySQL na lista pendente. Também pode utilizar a CLI do Azure para este passo.

Agora, tem tudo o que precisa para a operação manual do cluster.

### <a name="test-the-load-balanced-set"></a>Testar o conjunto com balanceamento de carga
Testes podem ser executados a partir de uma máquina externa utilizando qualquer cliente MySQL, ou através da utilização de determinadas aplicações, como phpMyAdmin a ser executado como um site do Azure. Neste caso, utilizou ferramenta da linha de comandos do MySQL na outra caixa de Linux:

    mysql azureha –u root –h hadb.cloudapp.net –e "select * from things;"

### <a name="manually-failing-over"></a>Efetuar manualmente a ativação pós-falha
Pode simular as ativações pós-falha por encerrar MySQL, mudar principal do DRBD e iniciar novamente o MySQL.

Para efetuar esta tarefa, execute o seguinte código no hadb01:

    service mysql stop && umount /var/lib/mysql ; drbdadm secondary r0

Em seguida, no hadb02:

    drbdadm primary r0 ; mount /dev/drbd1 /var/lib/mysql && service mysql start

Após a ativação pós-falha manualmente, pode repetir a consulta remota e deve funciona perfeitamente.

## <a name="set-up-corosync"></a>Configurar Corosync
Corosync é a infraestrutura de cluster subjacente necessária para Pacemaker funcione. Para o Heartbeat (e outras metodologias como Ultramonkey) Corosync é uma divisão de funcionalidades CRM, enquanto Pacemaker permanece mais semelhante para o Heartbeat da funcionalidade.

A restrição principal para Corosync no Azure é que Corosync prefers multicast através de difusão através de comunicações unicast, mas o funcionamento em rede do Microsoft Azure só suporta unicast.

Felizmente, Corosync tem um modo unicast de trabalho. A restrição apenas real é que uma vez todos os nós não estão a comunicar entre si próprios, tem de definir os nós nos seus ficheiros de configuração, incluindo os respetivos endereços IP. Podemos utilizar os ficheiros de exemplo Corosync para Unicast e alteração de atribuição de endereço, listas de nó e diretórios de registo (utiliza Ubuntu `/var/log/corosync` enquanto a utilização de ficheiros de exemplo `/var/log/cluster`) e ativar as ferramentas de quórum.

> [!NOTE]
> Utilize o seguinte `transport: udpu` diretiva e os endereços IP manualmente definidos para ambos os nós.

Execute o seguinte código no `/etc/corosync/corosync.conf` para ambos os nós:

    totem {
      version: 2
      crypto_cipher: none
      crypto_hash: none
      interface {
        ringnumber: 0
        bindnetaddr: 10.10.10.0
        mcastport: 5405
        ttl: 1
      }
      transport: udpu
    }

    logging {
      fileline: off
      to_logfile: yes
      to_syslog: yes
      logfile: /var/log/corosync/corosync.log
      debug: off
      timestamp: on
      logger_subsys {
        subsys: QUORUM
        debug: off
        }
      }

    nodelist {
      node {
        ring0_addr: 10.10.10.4
        nodeid: 1
      }

      node {
        ring0_addr: 10.10.10.5
        nodeid: 2
      }
    }

    quorum {
      provider: corosync_votequorum
    }

Copie este ficheiro de configuração em ambas as VMs e iniciar Corosync em ambos os nós:

    sudo service start corosync

Pouco tempo depois de iniciar o serviço, o cluster deve ser estabelecido no anel atual e deve ser constituted quórum. Iremos pode verificar esta funcionalidade, revendo os registos ou executando o seguinte código:

    sudo corosync-quorumtool –l

Irá ver o resultado semelhante para a imagem seguinte:

![saída de exemplo do corosync quorumtool - l](./media/mysql-cluster/image001.png)

## <a name="set-up-pacemaker"></a>Configurar Pacemaker
Pacemaker utiliza o cluster para monitorizar recursos, definir quando primaries forem abaixo e mudar esses recursos para bases de dados secundárias. Recursos podem ser definidos a partir de um conjunto de scripts disponíveis ou de scripts de (como o init) LSB, entre outras opções.

Queremos Pacemaker para "proprietário" o recurso DRBD, o ponto de montagem e o serviço de MySQL. Se Pacemaker pode ativar e desativar DRBD, montar e desmontar e, em seguida, iniciar e parar MySQL na ordem correta quando ocorre algo incorreto com o site primário, a configuração está concluída.

Quando instalar pela primeira vez Pacemaker, a configuração deve ser suficiente, simple algo como:

    node $id="1" hadb01
      attributes standby="off"
    node $id="2" hadb02
      attributes standby="off"

1. Verifique a configuração executando `sudo crm configure show`.
2. Em seguida, crie um ficheiro (como `/tmp/cluster.conf`) com os seguintes recursos:

        primitive drbd_mysql ocf:linbit:drbd \
              params drbd_resource="r0" \
              op monitor interval="29s" role="Master" \
              op monitor interval="31s" role="Slave"

        ms ms_drbd_mysql drbd_mysql \
              meta master-max="1" master-node-max="1" \
                clone-max="2" clone-node-max="1" \
                notify="true"

        primitive fs_mysql ocf:heartbeat:Filesystem \
              params device="/dev/drbd/by-res/r0" \
              directory="/var/lib/mysql" fstype="ext3"

        primitive mysqld lsb:mysql

        group mysql fs_mysql mysqld

        colocation mysql_on_drbd \
               inf: mysql ms_drbd_mysql:Master

        order mysql_after_drbd \
               inf: ms_drbd_mysql:promote mysql:start

        property stonith-enabled=false

        property no-quorum-policy=ignore

3. Carregue o ficheiro para a configuração. Apenas terá de fazê-lo no nó.

        sudo crm configure
          load update /tmp/cluster.conf
          commit
          exit

4. Certifique-se de que Pacemaker inicia no arranque em ambos os nós:

        sudo update-rc.d pacemaker defaults

5. Ao utilizar `sudo crm_mon –L`, certifique-se de que um dos seus nós tornou-se o mestre de para o cluster e está a executar todos os recursos. Pode utilizar a montagem e ps para verificar que os recursos estão em execução.

A seguinte captura de ecrã mostra `crm_mon` com um nó parado (saída selecionando Ctrl + C):

![nó crm_mon parado](./media/mysql-cluster/image002.png)

Nesta captura de ecrã mostra os nós, um nó principal e subordinado de um:

![principal/subordinado crm_mon](./media/mysql-cluster/image003.png)

## <a name="testing"></a>Testes
Está pronto para uma simulação de ativação pós-falha automática. Existem duas formas para efetuar este procedimento: flexível e de disco rígido.

A forma de forma recuperável utiliza a função de encerramento do cluster: ``crm_standby -U `uname -n` -v on``. Se utilizar isto no mestre de, o serviço secundário assume. Lembre-se definir esta opção novamente para desativado. Se não o fizer, crm_mon irá mostrar um nó em modo de espera.

A forma de disco rígida está a encerrar VM principal (hadb01) através do portal ou alterando o runlevel na VM (ou seja, halt, encerramento). Isto ajuda a Corosync e Pacemaker por sinalização que o mestre do ficar inativo. Pode testar esta (útil para janelas de manutenção), mas pode também forçar o cenário por freezing a VM.

## <a name="stonith"></a>STONITH
Deve ser possível emitir um encerramento VM através da CLI do Azure in lieu of um script STONITH que controla um dispositivo físico. Pode utilizar `/usr/lib/stonith/plugins/external/ssh` como uma base e ativar STONITH na configuração do cluster. CLI do Azure deve ser globalmente instalado e as definições de publicação e perfil devem ser carregados para o utilizador do cluster.

Código de exemplo para o recurso está disponível no [GitHub](https://github.com/bureado/aztonith). Alterar a configuração do cluster, adicionando o seguinte procedimento para `sudo crm configure`:

    primitive st-azure stonith:external/azure \
      params hostlist="hadb01 hadb02" \
      clone fencing st-azure \
      property stonith-enabled=true \
      commit

> [!NOTE]
> O script não executar verificações para cima/para baixo. O recurso SSH original tinha 15 de ping de verificações, mas o tempo de recuperação para uma VM do Azure pode ser mais variável.

## <a name="limitations"></a>Limitações
As seguintes limitações aplicam-se:

* O script de recursos DRBD linbit gere DRBD como um recurso no utiliza Pacemaker `drbdadm down` quando encerrar um nó, mesmo se o nó for apenas no modo de espera. Isto não é ideal porque o serviço secundário será não ser a sincronizar o recurso DRBD enquanto obtém de mestre de operações de escrita. Se o mestre não graciously falhar, o serviço secundário pode assumir um estado anterior do sistema de ficheiros. Existem duas formas possíveis de resolver isto:
  * Impor uma `drbdadm up r0` em todos os nós de cluster através de um local watchdog do (não clusterized)
  * Editar o script DRBD linbit, certificando-se de que `down` não for chamado`/usr/lib/ocf/resource.d/linbit/drbd`
* O Balanceador de carga necessita, pelo menos, cinco segundos de responder, para que as aplicações devem ter em consideração o cluster e ser mais tolerante a tempo limite. Outras arquiteturas, tal como na aplicação filas e middlewares de consulta, também podem ajudar.
* Otimização de MySQL é necessário para garantir que a escrever é feito a um ritmo gerível e caches são libertados para o disco com uma frequência de possíveis para minimizar a perda de memória.
* Escrever o desempenho está dependente na VM interligar-se no comutador virtual porque este é o mecanismo utilizado pelo DRBD para replicar o dispositivo.
