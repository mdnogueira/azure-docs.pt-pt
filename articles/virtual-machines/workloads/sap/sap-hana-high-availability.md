---
title: "Elevada disponibilidade de SAP HANA em máquinas de virtuais (VMs) do Azure | Microsoft Docs"
description: "Estabelecer uma elevada disponibilidade de SAP HANA em máquinas de virtuais (VMs) do Azure."
services: virtual-machines-linux
documentationcenter: 
author: MSSedusch
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/25/2017
ms.author: sedusch
ms.openlocfilehash: 951150e621d21037b0adde7287b9f985290d8d11
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="high-availability-of-sap-hana-on-azure-virtual-machines-vms"></a>Elevada disponibilidade de SAP HANA em máquinas de virtuais (VMs) do Azure

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351

[hana-ha-guide-replication]:sap-hana-high-availability.md#14c19f65-b5aa-4856-9594-b81c7e4df73d
[hana-ha-guide-shared-storage]:sap-hana-high-availability.md#498de331-fa04-490b-997c-b078de457c9d

[suse-hana-ha-guide]:https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf
[sap-swcenter]:https://launchpad.support.sap.com/#/softwarecenter
[template-multisid-db]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged%2Fazuredeploy.json

No local, pode utilizar a replicação do sistema HANA ou utilizar o armazenamento partilhado para estabelecer a elevada disponibilidade para SAP HANA.
Estamos atualmente apenas suporta definição a replicação do sistema HANA no Azure. SAP HANA replicação é composta por um nó principal e um nó, pelo menos, um subordinado. As alterações dos dados no nó principal são replicadas para os nós secundários de forma síncrona ou assíncrona.

Este artigo descreve como implementar as máquinas virtuais, configurar as máquinas virtuais, instale a estrutura de cluster, instalar e configurar a replicação do sistema de SAP HANA.
As configurações de exemplo, o número de instância de etc. 03 os comandos de instalação e HANA sistema ID HDB é utilizado.

Leia primeiro as seguintes notas de SAP e papers

* A nota [1928533], que tem:
  * Lista de tamanhos de VM do Azure que são suportados para a implementação de software do SAP
  * Informações de capacidade importantes para tamanhos de VM do Azure
  * Software SAP suportado e o sistema operativo (SO) e combinações de base de dados
  * Versão necessária de kernel SAP para o Windows e Linux no Microsoft Azure
* A nota [2015553] apresenta uma lista de pré-requisitos suportado de SAP SAP para implementações de software no Azure.
* A nota [2205917] tem definições de SO para SUSE Linux Enterprise Server para aplicações SAP recomendadas
* A nota [1944799] tem SAP HANA diretrizes para SUSE Linux Enterprise Server para aplicações SAP
* A nota [2178632] tem informações detalhadas sobre todas as monitorizações métricas que relatados para SAP no Azure.
* A nota [2191498] tem a versão necessária do agente de anfitrião do SAP para Linux no Azure.
* A nota [2243692] tem informações sobre o licenciamento SAP no Linux no Azure.
* A nota [1984787] tem informações gerais sobre o SUSE Linux Enterprise Server 12.
* A nota [1999351] tem informações adicionais de resolução de problemas para o Azure avançada extensão de monitorização para SAP.
* [WIKI do SAP Comunidade](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) tem todas as necessárias SAP notas para Linux.
* [Azure máquinas virtuais de planeamento e implementação de SAP no Linux][planning-guide]
* [Implementação de máquinas virtuais do Azure para SAP no Linux (Este artigo)][deployment-guide]
* [Implementação de DBMS de máquinas virtuais do Azure para SAP no Linux][dbms-guide]
* [Cenário de com otimização de desempenho do SAP HANA SR] [ suse-hana-ha-guide] o guia contém informações de todas as necessárias para configurar a replicação do sistema do SAP HANA no local. Utilize este guia como uma linha de base.

## <a name="deploying-linux"></a>Implementar Linux

O agente de recursos para SAP HANA está incluído no SUSE Linux Enterprise Server para aplicações SAP.
O Azure Marketplace contém uma imagem para o SUSE Linux Enterprise Server para SAP aplicações 12 com BYOS (traga a sua subscrição própria) que pode utilizar para implementar novas máquinas virtuais.

### <a name="manual-deployment"></a>Implementação manual

1. Criar um Grupo de Recursos
1. Criar uma rede Virtual
1. Criar duas contas de armazenamento
1. Criar um conjunto de disponibilidade  
   Domínio de atualização máximo do conjunto
1. Criar um balanceador de carga (interno)  
   Selecione a VNET do passo acima
1. Criar Máquina Virtual 1  
   https://portal.Azure.com/#CREATE/SUSE-byos.sles-for-SAP-byos12-SP1  
   SLES para SAP aplicações 12 SP1 (BYOS)  
   Selecione a conta de armazenamento 1  
   Selecione o conjunto de disponibilidade  
1. Criar Máquina Virtual 2  
   https://portal.Azure.com/#CREATE/SUSE-byos.sles-for-SAP-byos12-SP1  
   SLES para SAP aplicações 12 SP1 (BYOS)  
   Selecione a conta de armazenamento 2   
   Selecione o conjunto de disponibilidade  
1. Adicionar discos de dados
1. Configurar o Balanceador de carga
    1. Criar um conjunto IP de front-end
        1. Abra o Balanceador de carga, selecione o conjunto de IP de front-end e clique em Adicionar
        1. Introduza o nome do novo conjunto IP Front-end (por exemplo hana-front-end)
       1. Clique em OK
        1. Depois de criar o novo conjunto IP de front-end, anote o endereço IP
    1. Criar um conjunto de back-end
        1. Abra o Balanceador de carga, selecione os conjuntos de back-end e clique em Adicionar
        1. Introduza o nome do novo conjunto de back-end (por exemplo hana-back-end)
        1. Clique em Adicionar uma máquina virtual
        1. Selecione o conjunto de disponibilidade que criou anteriormente
        1. Selecione as máquinas virtuais do cluster de SAP HANA
        1. Clique em OK
    1. Criar uma sonda do Estado de funcionamento
       1. Abra o Balanceador de carga, selecione sondas de estado de funcionamento e clique em Adicionar
        1. Introduza o nome da sonda de estado de funcionamento novo (por exemplo hana-hp)
        1. Selecione TCP como protocolo, porta 625**03**, mantenha o intervalo de 5 e limiar de mau estado de funcionamento 2
        1. Clique em OK
    1. Criar regras de balanceamento de carga
        1. Abra o Balanceador de carga, selecione as regras de balanceamento de carga e clique em Adicionar
        1. Introduza o nome da nova regra de Balanceador de carga (por exemplo hana-lb-3**03**15)
        1. Selecione o endereço IP de front-end, conjunto back-end e estado de funcionamento sonda que criou anteriormente (por exemplo hana-front-end)
        1. Manter o protocolo TCP, introduza a porta 3**03**15
        1. Aumentar o tempo limite de inatividade e 30 minutos
       1. **Certifique-se de ativar o IP flutuante**
        1. Clique em OK
        1. Repita os passos acima para a porta 3**03**17

### <a name="deploy-with-template"></a>Implementar com modelo
Pode utilizar um dos modelos de início rápido no github para implementar recursos todos requeridos. O modelo implementa as máquinas virtuais, o Balanceador de carga, etc de conjunto de disponibilidade. Siga estes passos para implementar o modelo:

1. Abra o [modelo de base de dados] [ template-multisid-db] ou o [convergido modelo] [ template-converged] no Portal do Azure o modelo de base de dados apenas cria as regras de balanceamento de carga para uma base de dados, enquanto o modelo convergido também cria as regras de balanceamento de carga para um ASCS/SCS e instância ERS (apenas Linux). Se planear instalar um sistema NetWeaver SAP com base e também instalar a instância ASCS/SCS nas mesmas máquinas, utilize o [convergido modelo][template-converged].
1. Introduza os seguintes parâmetros
    1. Id de sistema do SAP  
       Introduza o Id de sistema SAP do sistema SAP que pretende instalar. O Id será utilizado como um prefixo para os recursos que são implementados.
    1. Tipo de pilha (apenas é aplicável se utilizar o modelo convergido)  
       Selecione o tipo de pilha do SAP NetWeaver
    1. Tipo de SO  
       Selecione um das distribuições de Linux. Para este exemplo, selecione o SLES 12 BYOS
    1. Tipo de base de dados  
       Selecione HANA
    1. Tamanho do sistema de SAP  
       A quantidade de SAPS irá fornecer o novo sistema. Se não tiver a não certeza SAPS quantos necessitarão de sistema,. Peça ao seu parceiro de tecnologia de SAP ou integrador de sistema
    1. Disponibilidade do sistema  
       Selecione HA
    1. Nome de utilizador de administrador e a palavra-passe de administrador  
       Um novo utilizador é criado que podem ser utilizadas para iniciar sessão na máquina.
    1. Sub-rede nova ou existente  
       Determina se devem ser criadas uma nova rede virtual e uma sub-rede ou se deve ser utilizada uma sub-rede existente. Se já tiver uma rede virtual que está ligada à sua rede no local, selecione existente.
    1. Id de sub-rede  
    O ID de sub-rede à qual as máquinas virtuais devem ser ligadas. Selecione a sub-rede da rede virtual VPN ou Expressroute para ligar a máquina virtual à sua rede no local. O ID de aspeto normalmente /subscriptions/{targetsubscriptionid}/resourcegroups/{targetresourcegroupname}`<subscription id`> /resourceGroups/`<resource group name`> /providers/Microsoft.Network/virtualNetworks/`<virtual network name`> /subnets/`<subnet name`>

## <a name="setting-up-linux-ha"></a>Configurar HA do Linux

Os seguintes itens são o prefixo ou [A] - aplicáveis a todos os nós, [1] - apenas aplicáveis a nó 1 ou [2] - apenas aplicável a nó 2.

1. [A] SLES para SAP BYOS só - SLES registar para poder utilizar os repositórios do
1. [A] SLES para SAP BYOS só - adicionar o módulo de nuvem pública
1. [A] atualização SLES
    ```bash
    sudo zypper update

    ```

1. [1] ativar ssh acesso
    ```bash
    sudo ssh-keygen -tdsa
    
    # Enter file in which to save the key (/root/.ssh/id_dsa): -> ENTER
    # Enter passphrase (empty for no passphrase): -> ENTER
    # Enter same passphrase again: -> ENTER
    
    # copy the public key
    sudo cat /root/.ssh/id_dsa.pub
    ```

2. [2] ativar ssh acesso
    ```bash
    sudo ssh-keygen -tdsa

    # insert the public key you copied in the last step into the authorized keys file on the second server
    sudo vi /root/.ssh/authorized_keys
    
    # Enter file in which to save the key (/root/.ssh/id_dsa): -> ENTER
    # Enter passphrase (empty for no passphrase): -> ENTER
    # Enter same passphrase again: -> ENTER
    
    # copy the public key    
    sudo cat /root/.ssh/id_dsa.pub
    ```

1. [1] ativar ssh acesso
    ```bash
    # insert the public key you copied in the last step into the authorized keys file on the first server
    sudo vi /root/.ssh/authorized_keys
    
    ```

1. [A] Instalar extensão do HA
    ```bash
    sudo zypper install sle-ha-release fence-agents
    
    ```

1. [A] esquema de disco de configuração de
    1. LVM  
    Normalmente, recomendamos que utilize LVM para volumes que armazenam dados e ficheiros de registo. O exemplo abaixo parte do princípio de que as máquinas virtuais tem quatro dados os discos ligados que devem ser utilizados para criar dois volumes.
        * Crie volumes de físicos para todos os discos que pretende utilizar.
    <pre><code>
    sudo pvcreate /dev/sdc
    sudo pvcreate /dev/sdd
    sudo pvcreate /dev/sde
    sudo pvcreate /dev/sdf
    </code></pre>
        * Criar um grupo de volume para os ficheiros de dados, um grupo de volume para os ficheiros de registo e outro para o diretório partilhado de SAP HANA
    <pre><code>
    sudo vgcreate vg_hana_data /dev/sdc /dev/sdd
    sudo vgcreate vg_hana_log /dev/sde
    sudo vgcreate vg_hana_shared /dev/sdf
    </code></pre>
        * Criar os volumes lógicos
    <pre><code>
    sudo lvcreate -l 100%FREE -n hana_data vg_hana_data
    sudo lvcreate -l 100%FREE -n hana_log vg_hana_log
    sudo lvcreate -l 100%FREE -n hana_shared vg_hana_shared
    sudo mkfs.xfs /dev/vg_hana_data/hana_data
    sudo mkfs.xfs /dev/vg_hana_log/hana_log
    sudo mkfs.xfs /dev/vg_hana_shared/hana_shared
    </code></pre>
        * Criar os diretórios de montagem e copie o UUID de todos os volumes lógicos
    <pre><code>
    sudo mkdir -p /hana/data
    sudo mkdir -p /hana/log
    sudo mkdir -p /hana/shared
    # write down the id of /dev/vg_hana_data/hana_data, /dev/vg_hana_log/hana_log and /dev/vg_hana_shared/hana_shared
    sudo blkid
    </code></pre>
        * Criação de entradas de fstab para os volumes lógicas três
    <pre><code>
    sudo vi /etc/fstab
    </code></pre>
    Inserir a linha para /etc/fstab
    <pre><code>
    /dev/disk/by-uuid/<b>&lt;UUID of /dev/vg_hana_data/hana_data&gt;</b> /hana/data xfs  defaults,nofail  0  2
    /dev/disk/by-uuid/<b>&lt;UUID of /dev/vg_hana_log/hana_log&gt;</b> /hana/log xfs  defaults,nofail  0  2
    /dev/disk/by-uuid/<b>&lt;UUID of /dev/vg_hana_shared/hana_shared&gt;</b> /hana/shared xfs  defaults,nofail  0  2
    </code></pre>
        * Montar os novos volumes
    <pre><code>
    sudo mount -a
    </code></pre>
    1. Discos simples  
       Para pequenas ou demonstração sistemas, pode colocar os ficheiros de dados e de registo HANA num disco. Os seguintes comandos crie uma partição no /dev/sdc e formate-o com xfs.
    ```bash
    sudo fdisk /dev/sdc
    sudo mkfs.xfs /dev/sdc1
    
    # <a name="write-down-the-id-of-devsdc1"></a>Anote o id de /dev/sdc1
    sudo /sbin/Service blkid sudo vi etc/fstab
    ```

    Insert this line to /etc/fstab
    <pre><code>
    /dev/disk/by-uuid/<b>&lt;UUID&gt;</b> /hana xfs  defaults,nofail  0  2
    </code></pre>

    Create the target directory and mount the disk.

    ```bash
    sudo mkdir /hana
    sudo mount -a
    ```

1. [A] resolução de nomes de anfitrião de o programa de configuração para todos os anfitriões  
    Pode utilizar um servidor DNS ou modificar os /etc/hosts em todos os nós. Este exemplo mostra como utilizar o ficheiro /etc/hosts.
   Substituir o endereço IP e o nome de anfitrião nos seguintes comandos
    ```bash
    sudo vi /etc/hosts
    ```
    Insira as seguintes linhas ao /etc/hosts. Alterar o endereço IP e nome de anfitrião para corresponder ao seu ambiente    
    
    <pre><code>
    <b>&lt;IP address of host 1&gt; &lt;hostname of host 1&gt;</b>
    <b>&lt;IP address of host 2&gt; &lt;hostname of host 2&gt;</b>
    </code></pre>

1. [1] instalar o Cluster
    ```bash
    sudo ha-cluster-init
    
    # Do you want to continue anyway? [y/N] -> y
    # Network address to bind to (e.g.: 192.168.1.0) [10.79.227.0] -> ENTER
    # Multicast address (e.g.: 239.x.x.x) [239.174.218.125] -> ENTER
    # Multicast port [5405] -> ENTER
    # Do you wish to use SBD? [y/N] -> N
    # Do you wish to configure an administration IP? [y/N] -> N
    ```
        
1. [2] adicionar nó ao cluster
    ```bash
    sudo ha-cluster-join
        
    # WARNING: NTP is not configured to start at system boot.
    # WARNING: No watchdog device found. If SBD is used, the cluster will be unable to start without a watchdog.
    # Do you want to continue anyway? [y/N] -> y
    # IP address or hostname of existing node (e.g.: 192.168.1.1) [] -> IP address of node 1 e.g. 10.0.0.5
    # /root/.ssh/id_dsa already exists - overwrite? [y/N] N
    ```

1. [A] palavra-passe hacluster de alteração para a mesma palavra-passe
    ```bash
    sudo passwd hacluster
    
    ```

1. [A] configure corosync para utilizar outro transporte e adicionar nodelist. Cluster não irão funcionar em contrário.
    ```bash
    sudo vi /etc/corosync/corosync.conf    
    
    ```

    Adicione o seguinte conteúdo de negrito para o ficheiro.
    
    <pre><code> 
    [...]
      interface { 
          [...] 
      }
      <b>transport:      udpu</b>
    } 
    <b>nodelist {
      node {
        ring0_addr:     < ip address of node 1 >
      }
      node {
        ring0_addr:     < ip address of node 2 > 
      } 
    }</b>
    logging {
      [...]
    </code></pre>

    Em seguida, reinicie o serviço de corosync

    ```bash
    sudo service corosync restart
    
    ```

1. [A] instalar pacotes HANA HA  
    ```bash
    sudo zypper install SAPHanaSR
    
    ```

## <a name="installing-sap-hana"></a>Instalar o SAP HANA

Siga o capítulo 4 do [guia de SAP HANA SR desempenho otimizado cenário] [ suse-hana-ha-guide] para instalar a replicação do sistema de SAP HANA.

1. [A] executar hdblcm a partir do HANA DVD
    * Escolha a instalação -> 1
    * Selecione os componentes adicionais para instalação-1 >
    * Introduza o caminho de instalação [hana/partilhado]: -> ENTER
    * Introduza o nome de anfitrião Local [.]: -> ENTER
    * Pretende adicionar a anfitriões adicionais para o sistema? (s/n) [n]: -> ENTER
    * Introduza o ID de sistema do SAP HANA:<SID of HANA e.g. HDB>
    * Introduza o número de instância [00]:   
  Número de instância HANA. Utilizar 03 se utilizou o modelo do Azure ou seguido de exemplo acima
    * Selecionar o modo de base de dados / introduza índice [1]: -> ENTER
    * Selecionar a utilização do sistema / introduza índice [4]:  
  Selecione o sistema de utilização
    * Introduza a localização de Volumes de dados [/ dados/hana/HDB]: -> ENTER
    * Introduza a localização de Volumes de registo [/ registo/hana/HDB]: -> ENTER
    * Restringir a atribuição de memória máxima? [n]: -> ENTER
    * Introduza o nome de anfitrião de certificado para o anfitrião "..." [...]: -> ENTER
    * Introduza o utilizador de agente de anfitrião do SAP (sapadm) palavra-passe:
    * Confirme o SAP anfitrião de agente de utilizador (sapadm) palavra-passe:
    * Introduza o administrador de sistema (hdbadm) palavra-passe:
    * Confirme (hdbadm) palavra-passe de administrador de sistema:
    * Introduza o diretório de raiz de administrador de sistema [/ usr/sap/HDB/home]: -> ENTER
    * Introduza a Shell de início de sessão de administrador de sistema [/ bin/ostrar]: -> ENTER
    * Introduza o ID de utilizador de administrador de sistema [1001]: -> ENTER
    * Introduza ID do grupo de utilizadores (sapsys) [79]: -> ENTER
    * Introduza a palavra-passe de utilizador (sistema) de base de dados:
    * Confirme a palavra-passe de utilizador (sistema) de base de dados:
    * Reiniciar o sistema após o reinício do computador? [n]: -> ENTER
    * Pretende continuar? (s/n):  
  O resumo de validação e introduza s para continuar
1. [A] atualizar o agente de anfitrião do SAP  
  Transferir o arquivo de agente do anfitrião SAP mais recente do [SAP Softwarecenter] [ sap-swcenter] e execute o seguinte comando para atualizar o agente. Substitua o caminho para o arquivo para apontar para o ficheiro que transferiu.
    ```bash
    sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive <path to SAP Host Agent SAR>
    ```

1. [1] criar replicação HANA (como raiz)  
    Execute o seguinte comando. Certifique-se de que substitui a negrito cadeias (HANA sistema ID HDB e instância número 03) com os valores a instalação de SAP HANA.
    <pre><code>
    PATH="$PATH:/usr/sap/<b>HDB</b>/HDB<b>03</b>/exe"
    hdbsql -u system -i <b>03</b> 'CREATE USER <b>hdb</b>hasync PASSWORD "<b>passwd</b>"' 
    hdbsql -u system -i <b>03</b> 'GRANT DATA ADMIN TO <b>hdb</b>hasync' 
    hdbsql -u system -i <b>03</b> 'ALTER USER <b>hdb</b>hasync DISABLE PASSWORD LIFETIME' 
    </code></pre>

1. [A] Crie a entrada de keystore (como raiz)
    <pre><code>
    PATH="$PATH:/usr/sap/<b>HDB</b>/HDB<b>03</b>/exe"
    hdbuserstore SET <b>hdb</b>haloc localhost:3<b>03</b>15 <b>hdb</b>hasync <b>passwd</b>
    </code></pre>
1. [1] base de dados cópia de segurança (como raiz)
    <pre><code>
    PATH="$PATH:/usr/sap/<b>HDB</b>/HDB<b>03</b>/exe"
    hdbsql -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')" 
    </code></pre>
1. [1] mude para o utilizador sapsid (por exemplo hdbadm) e criar o site primário.
    <pre><code>
    su - <b>hdb</b>adm
    hdbnsutil -sr_enable –-name=<b>SITE1</b>
    </code></pre>
1. [2] mude para o utilizador sapsid (por exemplo hdbadm) e criar o site secundário.
    <pre><code>
    su - <b>hdb</b>adm
    sapcontrol -nr <b>03</b> -function StopWait 600 10
    hdbnsutil -sr_register --remoteHost=<b>saphanavm1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
    </code></pre>

## <a name="configure-cluster-framework"></a>Configurar o Framework de Cluster

Alterar as predefinições

<pre>
sudo vi crm-defaults.txt
# enter the following to crm-defaults.txt
<code>
property $id="cib-bootstrap-options" \
  no-quorum-policy="ignore" \
  stonith-enabled="true" \
  stonith-action="reboot" \
  stonith-timeout="150s"
rsc_defaults $id="rsc-options" \
  resource-stickiness="1000" \
  migration-threshold="5000"
op_defaults $id="op-options" \
  timeout="600"
</code>

# <a name="now-we-load-the-file-to-the-cluster"></a>agora podemos carregar o ficheiro para o cluster
sudo crm configurar carga atualização crm-defaults.txt
</pre>

### <a name="create-stonith-device"></a>Criar dispositivo STONITH

O dispositivo STONITH utiliza um Principal de serviço para autorizar contra o Microsoft Azure. Siga estes passos para criar um Principal de serviço.

1. Aceda a <https://portal.azure.com>
1. Abra o painel do Azure Active Directory  
   Aceda às propriedades e anotar o ID de diretório. Este é o **id do inquilino**.
1. Clique em registos de aplicação
1. Clique em Adicionar
1. Introduza um nome, selecione o tipo de aplicação "Aplicação Web/API", introduza um URL sign-on (por exemplo http://localhost) e clique em criar
1. O URL de início de sessão não é utilizado e pode ser qualquer URL válido
1. Selecione a nova aplicação e clique em chaves no separador Definições
1. Introduza uma descrição para uma nova chave, selecione "Nunca expira" e clique em Guardar
1. Anote o valor. É utilizado como o **palavra-passe** para o Principal de serviço
1. Anote o ID da aplicação. É utilizado como o nome de utilizador (**id de início de sessão** nos passos abaixo) do Principal de serviço

O Principal de serviço não tem permissões para aceder aos seus recursos do Azure por predefinição. Terá de conceder as permissões do Principal de serviço para iniciar e parar (desalocar) todas as máquinas virtuais do cluster.

1. Aceda a https://portal.azure.com
1. Abra o painel de recursos de todos os
1. Selecione a máquina virtual
1. Clique em controlo de acesso (IAM)
1. Clique em Adicionar
1. Selecione a função de proprietário
1. Introduza o nome da aplicação que criou acima
1. Clique em OK

Depois de editar as permissões para as máquinas virtuais, pode configurar os dispositivos STONITH no cluster.

<pre>
sudo vi crm-fencing.txt
# enter the following to crm-fencing.txt
# replace the bold string with your subscription id, resource group, tenant id, service principal id and password
<code>
primitive rsc_st_azure_1 stonith:fence_azure_arm \
    params subscriptionId="<b>subscription id</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant id</b>" login="<b>login id</b>" passwd="<b>password</b>"

primitive rsc_st_azure_2 stonith:fence_azure_arm \
    params subscriptionId="<b>subscription id</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant id</b>" login="<b>login id</b>" passwd="<b>password</b>"

colocation col_st_azure -2000: rsc_st_azure_1:Started rsc_st_azure_2:Started
</code>

# <a name="now-we-load-the-file-to-the-cluster"></a>agora podemos carregar o ficheiro para o cluster
sudo crm configurar carga atualização crm-fencing.txt
</pre>

### <a name="create-sap-hana-resources"></a>Criar recursos de SAP HANA

<pre>
sudo vi crm-saphanatop.txt
# enter the following to crm-saphana.txt
# replace the bold string with your instance number and HANA system id
<code>
primitive rsc_SAPHanaTopology_<b>HDB</b>_HDB<b>03</b> ocf:suse:SAPHanaTopology \
    operations $id="rsc_sap2_<b>HDB</b>_HDB<b>03</b>-operations" \
    op monitor interval="10" timeout="600" \
    op start interval="0" timeout="600" \
    op stop interval="0" timeout="300" \
    params SID="<b>HDB</b>" InstanceNumber="<b>03</b>"

clone cln_SAPHanaTopology_<b>HDB</b>_HDB<b>03</b> rsc_SAPHanaTopology_<b>HDB</b>_HDB<b>03</b> \
    meta is-managed="true" clone-node-max="1" target-role="Started" interleave="true"
</code>

# <a name="now-we-load-the-file-to-the-cluster"></a>agora podemos carregar o ficheiro para o cluster
sudo crm configurar carga atualização crm-saphanatop.txt
</pre>

<pre>
sudo vi crm-saphana.txt
# enter the following to crm-saphana.txt
# replace the bold string with your instance number, HANA system id and the frontend IP address of the Azure load balancer. 
<code>
primitive rsc_SAPHana_<b>HDB</b>_HDB<b>03</b> ocf:suse:SAPHana \
    operations $id="rsc_sap_<b>HDB</b>_HDB<b>03</b>-operations" \
    op start interval="0" timeout="3600" \
    op stop interval="0" timeout="3600" \
    op promote interval="0" timeout="3600" \
    op monitor interval="60" role="Master" timeout="700" \
    op monitor interval="61" role="Slave" timeout="700" \
    params SID="<b>HDB</b>" InstanceNumber="<b>03</b>" PREFER_SITE_TAKEOVER="true" \
    DUPLICATE_PRIMARY_TIMEOUT="7200" AUTOMATED_REGISTER="false"

ms msl_SAPHana_<b>HDB</b>_HDB<b>03</b> rsc_SAPHana_<b>HDB</b>_HDB<b>03</b> \
    meta is-managed="true" notify="true" clone-max="2" clone-node-max="1" \
    target-role="Started" interleave="true"

primitive rsc_ip_<b>HDB</b>_HDB<b>03</b> ocf:heartbeat:IPaddr2 \ 
    meta target-role="Started" is-managed="true" \ 
    operations $id="rsc_ip_<b>HDB</b>_HDB<b>03</b>-operations" \ 
    op monitor interval="10s" timeout="20s" \ 
    params ip="<b>10.0.0.21</b>" 
primitive rsc_nc_<b>HDB</b>_HDB<b>03</b> anything \ 
    params binfile="/usr/bin/nc" cmdline_options="-l -k 625<b>03</b>" \ 
    op monitor timeout=20s interval=10 depth=0 
group g_ip_<b>HDB</b>_HDB<b>03</b> rsc_ip_<b>HDB</b>_HDB<b>03</b> rsc_nc_<b>HDB</b>_HDB<b>03</b>
 
colocation col_saphana_ip_<b>HDB</b>_HDB<b>03</b> 2000: g_ip_<b>HDB</b>_HDB<b>03</b>:Started \ 
    msl_SAPHana_<b>HDB</b>_HDB<b>03</b>:Master  
order ord_SAPHana_<b>HDB</b>_HDB<b>03</b> 2000: cln_SAPHanaTopology_<b>HDB</b>_HDB<b>03</b> \ 
    msl_SAPHana_<b>HDB</b>_HDB<b>03</b>
</code>

# <a name="now-we-load-the-file-to-the-cluster"></a>agora podemos carregar o ficheiro para o cluster
sudo crm configurar carga atualização crm-saphana.txt
</pre>

### <a name="test-cluster-setup"></a>Configuração do cluster de teste
O capítulo seguinte descrevem como pode testar a configuração. Cada teste assume que são raiz e o mestre de SAP HANA está em execução no saphanavm1 a máquina virtual.

#### <a name="fencing-test"></a>Teste fencing

Pode testar a configuração do agente fencing ao desativar a interface de rede no nó saphanavm1.

<pre><code>
sudo ifdown eth0
</code></pre>

A máquina virtual deve agora obter reiniciada ou parada consoante a configuração de cluster.
Se definir a ação de stonith para desativado, a máquina virtual será parada e os recursos são migrados para a máquina virtual em execução.

Depois de iniciar a máquina virtual novamente, o recurso de SAP HANA irão falhar ao iniciar como secundário se definir AUTOMATED_REGISTER = "false". Neste caso, terá de configurar a instância HANA como secundário executando o seguinte comando:

<pre><code>
su - <b>hdb</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>saphanavm2</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# switch back to root and cleanup the failed state
exit
crm resource cleanup msl_SAPHana_<b>HDB</b>_HDB<b>03</b> <b>saphanavm1</b>
</code></pre>

#### <a name="testing-a-manual-failover"></a>Testar uma ativação pós-falha manual

Pode testar uma ativação pós-falha manual, parando o serviço de pacemaker no nó saphanavm1.
<pre><code>
service pacemaker stop
</code></pre>

Após a ativação pós-falha, pode iniciar o serviço novamente. O recurso de SAP HANA saphanavm1 irão falhar ao iniciar como secundário se definir AUTOMATED_REGISTER = "false". Neste caso, terá de configurar a instância HANA como secundário executando o seguinte comando:

<pre><code>
service pacemaker start
su - <b>hdb</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>saphanavm2</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b> 


# switch back to root and cleanup the failed state
exit
crm resource cleanup msl_SAPHana_<b>HDB</b>_HDB<b>03</b> <b>saphanavm1</b>
</code></pre>

#### <a name="testing-a-migration"></a>Uma migração de teste

Pode migrar o nó mestre de SAP HANA executando o seguinte comando
<pre><code>
crm resource migrate msl_SAPHana_<b>HDB</b>_HDB<b>03</b> <b>saphanavm2</b>
crm resource migrate g_ip_<b>HDB</b>_HDB<b>03</b> <b>saphanavm2</b>
</code></pre>

Isto deve migrar o nó mestre de SAP HANA e o grupo que contém o endereço IP virtual para saphanavm2.
O recurso de SAP HANA saphanavm1 irão falhar ao iniciar como secundário se definir AUTOMATED_REGISTER = "false". Neste caso, terá de configurar a instância HANA como secundário executando o seguinte comando:

<pre><code>
su - <b>hdb</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>saphanavm2</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b> 
</code></pre>

A migração cria contraints de localização que precisam de ser eliminadas novamente.

<pre><code>
crm configure edited

# delete location contraints that are named like the following contraint. You should have two contraints, one for the SAP HANA resource and one for the IP address group.
location cli-prefer-g_ip_<b>HDB</b>_HDB<b>03</b> g_ip_<b>HDB</b>_HDB<b>03</b> role=Started inf: <b>saphanavm2</b>
</code></pre>

Também terá de limpar o estado do recurso de nó secundário

<pre><code>
# switch back to root and cleanup the failed state
exit
crm resource cleanup msl_SAPHana_<b>HDB</b>_HDB<b>03</b> <b>saphanavm1</b>
</code></pre>

## <a name="next-steps"></a>Passos seguintes
* [Azure máquinas virtuais de planeamento e implementação de SAP][planning-guide]
* [Implementação de máquinas virtuais do Azure para SAP][deployment-guide]
* [Implementação de DBMS de máquinas virtuais do Azure para SAP][dbms-guide]
* Para saber como estabelecer elevada disponibilidade e o plano de recuperação de desastres do SAP HANA no Azure (instâncias de grande), consulte o artigo [SAP HANA (instâncias de grande) elevada disponibilidade e recuperação após desastre no Azure](hana-overview-high-availability-disaster-recovery.md). 
