---
title: "Azure máquinas virtuais elevada disponibilidade para SAP NetWeaver no SUSE Linux Enterprise Server para SAP aplicações | Microsoft Docs"
description: "Guia de elevada disponibilidade para SAP NetWeaver no SUSE Linux Enterprise Server para aplicações SAP"
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: mssedusch
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/27/2017
ms.author: sedusch
ms.openlocfilehash: ed728011f2cb7b6108e19a916010fd5447c07093
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-suse-linux-enterprise-server-for-sap-applications"></a>Elevada disponibilidade para SAP NetWeaver em VMs do Azure no SUSE Linux Enterprise Server para aplicações SAP

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
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-hana-ha-guide]:https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md

Este artigo descreve como implementar as máquinas virtuais, configurar as máquinas virtuais, instale a estrutura de cluster e instalar um sistema de SAP NetWeaver 7.50 altamente disponível.
Nas configurações de exemplo, comandos de instalação etc. É utilizado o número de instância ASCS 00, o número de instância ERS 02 e SAP sistema ID NWS. Os nomes de recursos (por exemplo, as máquinas virtuais, redes virtuais) no exemplo partem do princípio de que utiliza o [convergido modelo] [ template-converged] com ID NWS para criar os recursos do sistema de SAP.

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
* [SAP HANA SR desempenho otimizado do cenário][suse-hana-ha-guide]  
  O guia contém todas as informações necessárias para configurar a replicação do sistema do SAP HANA no local. Utilize este guia como uma linha de base.
* [Altamente NFS armazenamento disponível com DRBD e Pacemaker] [ suse-drbd-guide] o guia contém informações de todas as necessárias para configurar um servidor NFS elevada. Utilize este guia como uma linha de base.


## <a name="overview"></a>Descrição geral

Para alcançar a elevada disponibilidade, SAP NetWeaver requer um servidor NFS. O servidor NFS está configurado num cluster separado e pode ser utilizado por vários sistemas SAP.

![Descrição geral do SAP NetWeaver elevada disponibilidade](./media/high-availability-guide-suse/img_001.png)

O servidor NFS, SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver ERS e a base de dados SAP HANA utilizam o nome de anfitrião virtual e endereços IP virtuais. No Azure, é necessário um balanceador de carga a utilizar um endereço IP virtual. A lista seguinte mostra a configuração de Balanceador de carga.

### <a name="nfs-server"></a>Servidor NFS
* Configuração de front-end
  * Endereço IP 10.0.0.4
* Configuração de back-end
  * Ligado a interfaces de rede principal de todas as máquinas virtuais que deve fazer parte do NFS cluster
* Porta da sonda
  * Porta 61000
* Regras de carga
  * 2049 TCP 
  * 2049 UDP

### <a name="ascs"></a>(A) SCS
* Configuração de front-end
  * Endereço IP 10.0.0.10
* Configuração de back-end
  * Ligado a interfaces de rede principal de todas as máquinas virtuais que deve fazer parte do (A) cluster SCS/ERS
* Porta da sonda
  * Porta 620**&lt;nr&gt;**
* Regras de carga
  * 32**&lt;nr&gt;**  TCP
  * 36**&lt;nr&gt;**  TCP
  * 39**&lt;nr&gt;**  TCP
  * 81**&lt;nr&gt;**  TCP
  * 5**&lt;nr&gt;**13 TCP
  * 5**&lt;nr&gt;**14 TCP
  * 5**&lt;nr&gt;**16 TCP

### <a name="ers"></a>ERS
* Configuração de front-end
  * Endereço IP 10.0.0.11
* Configuração de back-end
  * Ligado a interfaces de rede principal de todas as máquinas virtuais que deve fazer parte do (A) cluster SCS/ERS
* Porta da sonda
  * Porta 621**&lt;nr&gt;**
* Regras de carga
  * 33**&lt;nr&gt;**  TCP
  * 5**&lt;nr&gt;**13 TCP
  * 5**&lt;nr&gt;**14 TCP
  * 5**&lt;nr&gt;**16 TCP

### <a name="sap-hana"></a>SAP HANA
* Configuração de front-end
  * Endereço IP 10.0.0.12
* Configuração de back-end
  * Ligado a interfaces de rede principal de todas as máquinas virtuais que deve fazer parte do HANA cluster
* Porta da sonda
  * Porta 625**&lt;nr&gt;**
* Regras de carga
  * 3**&lt;nr&gt;**15 TCP
  * 3**&lt;nr&gt;**17 TCP

## <a name="setting-up-a-highly-available-nfs-server"></a>Configurar um servidor NFS elevada

### <a name="deploying-linux"></a>Implementar Linux

O Azure Marketplace contém uma imagem para o SUSE Linux Enterprise Server para SAP aplicações 12, que pode utilizar para implementar novas máquinas virtuais.
Pode utilizar um dos modelos de início rápido no github para implementar recursos todos requeridos. O modelo implementa as máquinas virtuais, o Balanceador de carga, etc de conjunto de disponibilidade. Siga estes passos para implementar o modelo:

1. Abra o [modelo de servidor de ficheiros SAP] [ template-file-server] no portal do Azure   
1. Introduza os seguintes parâmetros
   1. Prefixo de recursos  
      Introduza o prefixo que pretende utilizar. O valor é utilizado como um prefixo para os recursos que são implementados.
   2. Tipo de SO  
      Selecione um das distribuições de Linux. Para este exemplo, selecione o SLES 12
   3. Nome de utilizador de administrador e a palavra-passe de administrador  
      Um novo utilizador é criado que podem ser utilizadas para iniciar sessão na máquina.
   4. Id de sub-rede  
      O ID de sub-rede à qual as máquinas virtuais devem ser ligadas. Deixe em branco, se pretender criar uma nova rede virtual ou selecione a sub-rede da rede virtual VPN ou Expressroute para ligar a máquina virtual à sua rede no local. O ID de aspeto normalmente /subscriptions/{targetsubscriptionid}/resourcegroups/{targetresourcegroupname}**&lt;id de subscrição&gt;**/resourceGroups/**&lt;nome do grupo de recursos&gt;**/providers/Microsoft.Network/virtualNetworks/**&lt;nome da rede virtual&gt;**/subnets/**&lt;nome de sub-rede&gt;**

### <a name="installation"></a>Instalação

Os seguintes itens são o prefixo um **[A]** - aplicáveis a todos os nós, **[1]** - só é aplicável ao nó de 1 ou **[2]** - apenas aplicável a nó 2.

1. **[A]**  Atualizar SLES

   <pre><code>
   sudo zypper update
   </code></pre>

1. **[1]**  Ativar ssh acesso

   <pre><code>
   sudo ssh-keygen -tdsa
   
   # Enter file in which to save the key (/root/.ssh/id_dsa): -> ENTER
   # Enter passphrase (empty for no passphrase): -> ENTER
   # Enter same passphrase again: -> ENTER
   
   # copy the public key
   sudo cat /root/.ssh/id_dsa.pub
   </code></pre>

2. **[2]**  Ativar ssh acesso

   <pre><code>
   sudo ssh-keygen -tdsa

   # insert the public key you copied in the last step into the authorized keys file on the second server
   sudo vi /root/.ssh/authorized_keys
   
   # Enter file in which to save the key (/root/.ssh/id_dsa): -> ENTER
   # Enter passphrase (empty for no passphrase): -> ENTER
   # Enter same passphrase again: -> ENTER
   
   # copy the public key   
   sudo cat /root/.ssh/id_dsa.pub
   </code></pre>

1. **[1]**  Ativar ssh acesso

   <pre><code>
   # insert the public key you copied in the last step into the authorized keys file on the first server
   sudo vi /root/.ssh/authorized_keys
   </code></pre>

1. **[A]**  Extensão instalar HA
   
   <pre><code>
   sudo zypper install sle-ha-release fence-agents
   </code></pre>

1. **[A]**  Configurar a resolução de nome de anfitrião   

   Pode utilizar um servidor DNS ou modificar os /etc/hosts em todos os nós. Este exemplo mostra como utilizar o ficheiro /etc/hosts.
   Substituir o endereço IP e o nome de anfitrião nos seguintes comandos

   <pre><code>
   sudo vi /etc/hosts
   </code></pre>
   
   Insira as seguintes linhas ao /etc/hosts. Alterar o endereço IP e nome de anfitrião para corresponder ao seu ambiente   
   
   <pre><code>
   # IP address of the load balancer frontend configuration for NFS
   <b>10.0.0.4 nws-nfs</b>
   </code></pre>

1. **[1]**  Instalar o Cluster
   
   <pre><code>
   sudo ha-cluster-init
   
   # Do you want to continue anyway? [y/N] -> y
   # Network address to bind to (for example: 192.168.1.0) [10.79.227.0] -> ENTER
   # Multicast address (for example: 239.x.x.x) [239.174.218.125] -> ENTER
   # Multicast port [5405] -> ENTER
   # Do you wish to use SBD? [y/N] -> N
   # Do you wish to configure an administration IP? [y/N] -> N
   </code></pre>

1. **[2]**  Adicionar nó ao cluster
   
   <pre><code> 
   sudo ha-cluster-join

   # WARNING: NTP is not configured to start at system boot.
   # WARNING: No watchdog device found. If SBD is used, the cluster will be unable to start without a watchdog.
   # Do you want to continue anyway? [y/N] -> y
   # IP address or hostname of existing node (for example: 192.168.1.1) [] -> IP address of node 1 for example 10.0.0.10
   # /root/.ssh/id_dsa already exists - overwrite? [y/N] N
   </code></pre>

1. **[A]**  Alterar hacluster palavra-passe para a mesma palavra-passe

   <pre><code> 
   sudo passwd hacluster
   </code></pre>

1. **[A]**  Configurar corosync para utilizar outro transporte e adicionar nodelist. Cluster não irão funcionar em contrário.
   
   <pre><code> 
   sudo vi /etc/corosync/corosync.conf   
   </code></pre>

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
      # IP address of <b>prod-nfs-0</b>
      ring0_addr:10.0.0.5
     }
     node {
      # IP address of <b>prod-nfs-1</b>
      ring0_addr:10.0.0.6
     } 
   }</b>
   logging {
     [...]
   </code></pre>

   Em seguida, reinicie o serviço de corosync

   <pre><code>
   sudo service corosync restart
   </code></pre>

1. **[A]**  Instalar os componentes de drbd

   <pre><code>
   sudo zypper install drbd drbd-kmp-default drbd-utils
   </code></pre>

1. **[A]**  Criar uma partição para o dispositivo drbd

   <pre><code>
   sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/sdc'
   </code></pre>

1. **[A]**  Configurações LVM criar

   <pre><code>
   sudo pvcreate /dev/sdc1   
   sudo vgcreate vg_NFS /dev/sdc1
   sudo lvcreate -l 100%FREE -n <b>NWS</b> vg_NFS
   </code></pre>

1. **[A]**  Criar o dispositivo de drbd NFS

   <pre><code>
   sudo vi /etc/drbd.d/<b>NWS</b>_nfs.res
   </code></pre>

   Inserir a configuração para o novo dispositivo drbd e saída

   <pre><code>
   resource <b>NWS</b>_nfs {
      protocol     C;
      disk {
         on-io-error       pass_on;
      }
      on <b>prod-nfs-0</b> {
         address   <b>10.0.0.5</b>:7790;
         device    /dev/drbd0;
         disk      /dev/vg_NFS/NWS;
         meta-disk internal;
      }
      on <b>prod-nfs-1</b> {
         address   <b>10.0.0.6</b>:7790;
         device    /dev/drbd0;
         disk      /dev/vg_NFS/NWS;
         meta-disk internal;
      }
   }
   </code></pre>

   Criar o dispositivo drbd e inicie-o

   <pre><code>
   sudo drbdadm create-md <b>NWS</b>_nfs
   sudo drbdadm up <b>NWS</b>_nfs
   </code></pre>

1. **[1]**  Ignorar sincronização inicial

   <pre><code>
   sudo drbdadm new-current-uuid --clear-bitmap <b>NWS</b>_nfs
   </code></pre>

1. **[1]**  Defina o nó principal

   <pre><code>
   sudo drbdadm primary --force <b>NWS</b>_nfs
   </code></pre>

1. **[1]**  Aguarde até que são sincronizados os novos dispositivos drbd

   <pre><code>
   sudo cat /proc/drbd

   # version: 8.4.6 (api:1/proto:86-101)
   # GIT-hash: 833d830e0152d1e457fa7856e71e11248ccf3f70 build by abuild@sheep14, 2016-05-09 23:14:56
   # 0: cs:Connected ro:Primary/Secondary ds:UpToDate/UpToDate C r-----
   #    ns:0 nr:0 dw:0 dr:912 al:8 bm:0 lo:0 pe:0 ua:0 ap:0 ep:1 wo:f oos:0
   </code></pre>

1. **[1]**  Criar sistemas de ficheiros nos dispositivos drbd

   <pre><code>
   sudo mkfs.xfs /dev/drbd0
   </code></pre>


### <a name="configure-cluster-framework"></a>Configurar o Framework de Cluster

1. **[1]**  Alterar as predefinições

   <pre><code>
   sudo crm configure

   crm(live)configure# rsc_defaults resource-stickiness="1"

   crm(live)configure# commit
   crm(live)configure# exit
   </code></pre>

1. **[1]**  Adicionar o dispositivo de drbd NFS para a configuração de cluster

   <pre><code>
   sudo crm configure

   crm(live)configure# primitive drbd_<b>NWS</b>_nfs \
     ocf:linbit:drbd \
     params drbd_resource="<b>NWS</b>_nfs" \
     op monitor interval="15" role="Master" \
     op monitor interval="30" role="Slave"

   crm(live)configure# ms ms-drbd_<b>NWS</b>_nfs drbd_<b>NWS</b>_nfs \
     meta master-max="1" master-node-max="1" clone-max="2" \
     clone-node-max="1" notify="true" interleave="true"

   crm(live)configure# commit
   crm(live)configure# exit
   </code></pre>

1. **[1]**  Criar o servidor NFS

   <pre><code>
   sudo crm configure

   crm(live)configure# primitive nfsserver \
     systemd:nfs-server \
     op monitor interval="30s"

   crm(live)configure# clone cl-nfsserver nfsserver interleave="true"

   crm(live)configure# commit
   crm(live)configure# exit
   </code></pre>

1. **[1]**  Criar os recursos de sistema de ficheiros NFS

   <pre><code>
   sudo crm configure

   crm(live)configure# primitive fs_<b>NWS</b>_sapmnt \
     ocf:heartbeat:Filesystem \
     params device=/dev/drbd0 \
     directory=/srv/nfs/<b>NWS</b>  \
     fstype=xfs \
     op monitor interval="10s"

   crm(live)configure# group g-<b>NWS</b>_nfs fs_<b>NWS</b>_sapmnt

   crm(live)configure# order o-<b>NWS</b>_drbd_before_nfs inf: \
     ms-drbd_<b>NWS</b>_nfs:promote g-<b>NWS</b>_nfs:start
   
   crm(live)configure# colocation col-<b>NWS</b>_nfs_on_drbd inf: \
     g-<b>NWS</b>_nfs ms-drbd_<b>NWS</b>_nfs:Master

   crm(live)configure# commit
   crm(live)configure# exit
   </code></pre>

1. **[1]**  Criar exporta o NFS

   <pre><code>
   sudo mkdir /srv/nfs/<b>NWS</b>/sidsys
   sudo mkdir /srv/nfs/<b>NWS</b>/sapmntsid
   sudo mkdir /srv/nfs/<b>NWS</b>/trans

   sudo crm configure

   crm(live)configure# primitive exportfs_<b>NWS</b> \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NWS</b>" \
     options="rw,no_root_squash" \
     clientspec="*" fsid=0 \
     wait_for_leasetime_on_stop=true \
     op monitor interval="30s"

   crm(live)configure# modgroup g-<b>NWS</b>_nfs add exportfs_<b>NWS</b>

   crm(live)configure# commit
   crm(live)configure# exit
   </code></pre>

1. **[1]**  Criar um recurso de IP virtual e a sonda de estado de funcionamento para o Balanceador de carga interno

   <pre><code>
   sudo crm configure

   crm(live)configure# primitive vip_<b>NWS</b>_nfs IPaddr2 \
     params ip=<b>10.0.0.4</b> cidr_netmask=24 \
     op monitor interval=10 timeout=20

   crm(live)configure# primitive nc_<b>NWS</b>_nfs anything \
     params binfile="/usr/bin/nc" cmdline_options="-l -k 610<b>00</b>" \
     op monitor timeout=20s interval=10 depth=0

   crm(live)configure# modgroup g-<b>NWS</b>_nfs add nc_<b>NWS</b>_nfs
   crm(live)configure# modgroup g-<b>NWS</b>_nfs add vip_<b>NWS</b>_nfs

   crm(live)configure# commit
   crm(live)configure# exit
   </code></pre>

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

#### <a name="1-create-the-stonith-devices"></a>**[1]**  Criar os dispositivos STONITH

Depois de editar as permissões para as máquinas virtuais, pode configurar os dispositivos STONITH no cluster.

<pre><code>
sudo crm configure

# replace the bold string with your subscription id, resource group, tenant id, service principal id and password

crm(live)configure# primitive rsc_st_azure_1 stonith:fence_azure_arm \
   params subscriptionId="<b>subscription id</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant id</b>" login="<b>login id</b>" passwd="<b>password</b>"

crm(live)configure# primitive rsc_st_azure_2 stonith:fence_azure_arm \
   params subscriptionId="<b>subscription id</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant id</b>" login="<b>login id</b>" passwd="<b>password</b>"

crm(live)configure# colocation col_st_azure -2000: rsc_st_azure_1:Started rsc_st_azure_2:Started

crm(live)configure# commit
crm(live)configure# exit
</code></pre>

#### <a name="1-enable-the-use-of-a-stonith-device"></a>**[1]**  Ativar a utilização de um dispositivo STONITH

<pre><code>
sudo crm configure property stonith-enabled=true 
</code></pre>

## <a name="setting-up-ascs"></a>Configurar a (A) SCS

### <a name="deploying-linux"></a>Implementar Linux

O Azure Marketplace contém uma imagem para o SUSE Linux Enterprise Server para SAP aplicações 12, que pode utilizar para implementar novas máquinas virtuais. A imagem do marketplace contém o agente de recursos para SAP NetWeaver.

Pode utilizar um dos modelos de início rápido no github para implementar recursos todos requeridos. O modelo implementa as máquinas virtuais, o Balanceador de carga, etc de conjunto de disponibilidade. Siga estes passos para implementar o modelo:

1. Abra o [modelo ASCS/SCS várias SID] [ template-multisid-xscs] ou [convergido modelo] [ template-converged] do Azure portal ASCS/SCS o modelo apenas cria as regras de balanceamento de carga do SAP NetWeaver ASCS/SCS e instâncias ERS (apenas Linux), enquanto o modelo convergido também cria as regras de balanceamento de carga para uma base de dados (por exemplo o Microsoft SQL Server ou o SAP HANA). Se planear instalar um sistema NetWeaver SAP com base e também instalar a base de dados nas mesmas máquinas, utilize o [convergido modelo][template-converged].
1. Introduza os seguintes parâmetros
   1. Prefixo de recursos (apenas modelo ASCS/SCS várias SID)  
      Introduza o prefixo que pretende utilizar. O valor é utilizado como um prefixo para os recursos que são implementados.
   3. Id de sistema de SAP (apenas modelo convergido)  
      Introduza o Id de sistema SAP do sistema SAP que pretende instalar. O Id é utilizado como um prefixo para os recursos que são implementados.
   4. Tipo de pilha  
      Selecione o tipo de pilha do SAP NetWeaver
   5. Tipo de SO  
      Selecione um das distribuições de Linux. Para este exemplo, selecione o SLES 12 BYOS
   6. Tipo de base de dados  
      Selecione HANA
   7. Tamanho do sistema de SAP  
      A quantidade de SAPS fornece o novo sistema. Se não tiver a não certeza SAPS quantos requer que o sistema,. Peça ao seu parceiro de tecnologia de SAP ou integrador de sistema
   8. Disponibilidade do sistema  
      Selecione HA
   9. Nome de utilizador de administrador e a palavra-passe de administrador  
      Um novo utilizador é criado que podem ser utilizadas para iniciar sessão na máquina.
   10. Id de sub-rede  
   O ID de sub-rede à qual as máquinas virtuais devem ser ligadas.  Deixe em branco, se pretender criar uma nova rede virtual ou selecione a mesma sub-rede que utilizada ou criada como parte da implementação de servidor NFS. O ID de aspeto normalmente /subscriptions/{targetsubscriptionid}/resourcegroups/{targetresourcegroupname}**&lt;id de subscrição&gt;**/resourceGroups/**&lt;nome do grupo de recursos&gt;**/providers/Microsoft.Network/virtualNetworks/**&lt;nome da rede virtual&gt;**/subnets/**&lt;nome de sub-rede&gt;**

### <a name="installation"></a>Instalação

Os seguintes itens são o prefixo um **[A]** - aplicáveis a todos os nós, **[1]** - só é aplicável ao nó de 1 ou **[2]** - apenas aplicável a nó 2.

1. **[A]**  Atualizar SLES

   <pre><code>
   sudo zypper update
   </code></pre>

1. **[1]**  Ativar ssh acesso

   <pre><code>
   sudo ssh-keygen -tdsa
   
   # Enter file in which to save the key (/root/.ssh/id_dsa): -> ENTER
   # Enter passphrase (empty for no passphrase): -> ENTER
   # Enter same passphrase again: -> ENTER
   
   # copy the public key
   sudo cat /root/.ssh/id_dsa.pub
   </code></pre>

2. **[2]**  Ativar ssh acesso

   <pre><code>
   sudo ssh-keygen -tdsa

   # insert the public key you copied in the last step into the authorized keys file on the second server
   sudo vi /root/.ssh/authorized_keys
   
   # Enter file in which to save the key (/root/.ssh/id_dsa): -> ENTER
   # Enter passphrase (empty for no passphrase): -> ENTER
   # Enter same passphrase again: -> ENTER
   
   # copy the public key   
   sudo cat /root/.ssh/id_dsa.pub
   </code></pre>

1. **[1]**  Ativar ssh acesso

   <pre><code>
   # insert the public key you copied in the last step into the authorized keys file on the first server
   sudo vi /root/.ssh/authorized_keys
   </code></pre>

1. **[A]**  Extensão instalar HA
   
   <pre><code>
   sudo zypper install sle-ha-release fence-agents
   </code></pre>

1. **[A]**  Agentes de recursos de SAP da atualização  
   
   É necessário uma correção para o pacote de recursos agentes para utilizar a nova configuração, o que é descrita neste artigo. Pode verificar, se o patch já estiver instalado com o seguinte comando

   <pre><code>
   sudo grep 'parameter name="IS_ERS"' /usr/lib/ocf/resource.d/heartbeat/SAPInstance
   </code></pre>

   O resultado deve ser semelhante a

   <pre><code>
   &lt;parameter name="IS_ERS" unique="0" required="0"&gt;
   </code></pre>

   Se o comando grep não localize o parâmetro IS_ERS, terá de instalar o patch listado no [o SUSE página de transferência](https://download.suse.com/patch/finder/#bu=suse&familyId=&productId=&dateRange=&startDate=&endDate=&priority=&architecture=&keywords=resource-agents)

   <pre><code>
   # example for patch for SLES 12 SP1
   sudo zypper in -t patch SUSE-SLE-HA-12-SP1-2017-885=1
   # example for patch for SLES 12 SP2
   sudo zypper in -t patch SUSE-SLE-HA-12-SP2-2017-886=1
   </code></pre>

1. **[A]**  Configurar a resolução de nome de anfitrião   

   Pode utilizar um servidor DNS ou modificar os /etc/hosts em todos os nós. Este exemplo mostra como utilizar o ficheiro /etc/hosts.
   Substituir o endereço IP e o nome de anfitrião nos seguintes comandos

   <pre><code>
   sudo vi /etc/hosts
   </code></pre>
   
   Insira as seguintes linhas ao /etc/hosts. Alterar o endereço IP e nome de anfitrião para corresponder ao seu ambiente   
   
   <pre><code>
   # IP address of the load balancer frontend configuration for NFS
   <b>10.0.0.4 nws-nfs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS/SCS
   <b>10.0.0.10 nws-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ERS
   <b>10.0.0.11 nws-ers</b>
   # IP address of the load balancer frontend configuration for database
   <b>10.0.0.12 nws-db</b>
   </code></pre>

1. **[1]**  Instalar o Cluster
   
   <pre><code>
   sudo ha-cluster-init
   
   # Do you want to continue anyway? [y/N] -> y
   # Network address to bind to (for example: 192.168.1.0) [10.79.227.0] -> ENTER
   # Multicast address (for example: 239.x.x.x) [239.174.218.125] -> ENTER
   # Multicast port [5405] -> ENTER
   # Do you wish to use SBD? [y/N] -> N
   # Do you wish to configure an administration IP? [y/N] -> N
   </code></pre>

1. **[2]**  Adicionar nó ao cluster
   
   <pre><code> 
   sudo ha-cluster-join

   # WARNING: NTP is not configured to start at system boot.
   # WARNING: No watchdog device found. If SBD is used, the cluster will be unable to start without a watchdog.
   # Do you want to continue anyway? [y/N] -> y
   # IP address or hostname of existing node (for example: 192.168.1.1) [] -> IP address of node 1 for example 10.0.0.10
   # /root/.ssh/id_dsa already exists - overwrite? [y/N] N
   </code></pre>

1. **[A]**  Alterar hacluster palavra-passe para a mesma palavra-passe

   <pre><code> 
   sudo passwd hacluster
   </code></pre>

1. **[A]**  Configurar corosync para utilizar outro transporte e adicionar nodelist. Cluster não irão funcionar em contrário.
   
   <pre><code> 
   sudo vi /etc/corosync/corosync.conf   
   </code></pre>

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
      # IP address of <b>nws-cl-0</b>
      ring0_addr:     10.0.0.14
     }
     node {
      # IP address of <b>nws-cl-1</b>
      ring0_addr:     10.0.0.13
     } 
   }</b>
   logging {
     [...]
   </code></pre>

   Em seguida, reinicie o serviço de corosync

   <pre><code>
   sudo service corosync restart
   </code></pre>

1. **[A]**  Instalar os componentes de drbd

   <pre><code>
   sudo zypper install drbd drbd-kmp-default drbd-utils
   </code></pre>

1. **[A]**  Criar uma partição para o dispositivo drbd

   <pre><code>
   sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/sdc'
   </code></pre>

1. **[A]**  Configurações LVM criar

   <pre><code>
   sudo pvcreate /dev/sdc1   
   sudo vgcreate vg_<b>NWS</b> /dev/sdc1
   sudo lvcreate -l 50%FREE -n <b>NWS</b>_ASCS vg_<b>NWS</b>
   sudo lvcreate -l 50%FREE -n <b>NWS</b>_ERS vg_<b>NWS</b>
   </code></pre>

1. **[A]**  Criar o dispositivo de drbd SCS

   <pre><code>
   sudo vi /etc/drbd.d/<b>NWS</b>_ascs.res
   </code></pre>

   Inserir a configuração para o novo dispositivo drbd e saída

   <pre><code>
   resource <b>NWS</b>_ascs {
      protocol     C;
      disk {
         on-io-error       pass_on;
      }
      on <b>nws-cl-0</b> {
         address   <b>10.0.0.14</b>:7791;
         device    /dev/drbd0;
         disk      /dev/vg_NWS/NWS_ASCS;
         meta-disk internal;
      }
      on <b>nws-cl-1</b> {
         address   <b>10.0.0.13</b>:7791;
         device    /dev/drbd0;
         disk      /dev/vg_NWS/NWS_ASCS;
         meta-disk internal;
      }
   }
   </code></pre>

   Criar o dispositivo drbd e inicie-o

   <pre><code>
   sudo drbdadm create-md <b>NWS</b>_ascs
   sudo drbdadm up <b>NWS</b>_ascs
   </code></pre>

1. **[A]**  Criar o dispositivo de drbd ERS

   <pre><code>
   sudo vi /etc/drbd.d/<b>NWS</b>_ers.res
   </code></pre>

   Inserir a configuração para o novo dispositivo drbd e saída

   <pre><code>
   resource <b>NWS</b>_ers {
      protocol     C;
      disk {
         on-io-error       pass_on;
      }
      on <b>nws-cl-0</b> {
         address   <b>10.0.0.14</b>:7792;
         device    /dev/drbd1;
         disk      /dev/vg_NWS/NWS_ERS;
         meta-disk internal;
      }
      on <b>nws-cl-1</b> {
         address   <b>10.0.0.13</b>:7792;
         device    /dev/drbd1;
         disk      /dev/vg_NWS/NWS_ERS;
         meta-disk internal;
      }
   }
   </code></pre>

   Criar o dispositivo drbd e inicie-o

   <pre><code>
   sudo drbdadm create-md <b>NWS</b>_ers
   sudo drbdadm up <b>NWS</b>_ers
   </code></pre>

1. **[1]**  Ignorar sincronização inicial

   <pre><code>
   sudo drbdadm new-current-uuid --clear-bitmap <b>NWS</b>_ascs
   sudo drbdadm new-current-uuid --clear-bitmap <b>NWS</b>_ers
   </code></pre>

1. **[1]**  Defina o nó principal

   <pre><code>
   sudo drbdadm primary --force <b>NWS</b>_ascs
   sudo drbdadm primary --force <b>NWS</b>_ers
   </code></pre>

1. **[1]**  Aguarde até que são sincronizados os novos dispositivos drbd

   <pre><code>
   sudo cat /proc/drbd

   # version: 8.4.6 (api:1/proto:86-101)
   # GIT-hash: 833d830e0152d1e457fa7856e71e11248ccf3f70 build by abuild@sheep14, 2016-05-09 23:14:56
   # 0: cs:<b>Connected</b> ro:Primary/Secondary ds:<b>UpToDate/UpToDate</b> C r-----
   #     ns:93991268 nr:0 dw:93991268 dr:93944920 al:383 bm:0 lo:0 pe:0 ua:0 ap:0 ep:1 wo:f oos:0
   # 1: cs:<b>Connected</b> ro:Primary/Secondary ds:<b>UpToDate/UpToDate</b> C r-----
   #     ns:6047920 nr:0 dw:6047920 dr:6039112 al:34 bm:0 lo:0 pe:0 ua:0 ap:0 ep:1 wo:f oos:0
   # 2: cs:<b>Connected</b> ro:Primary/Secondary ds:<b>UpToDate/UpToDate</b> C r-----
   #     ns:5142732 nr:0 dw:5142732 dr:5133924 al:30 bm:0 lo:0 pe:0 ua:0 ap:0 ep:1 wo:f oos:0
   </code></pre>

1. **[1]**  Criar sistemas de ficheiros nos dispositivos drbd

   <pre><code>
   sudo mkfs.xfs /dev/drbd0
   sudo mkfs.xfs /dev/drbd1
   </code></pre>


### <a name="configure-cluster-framework"></a>Configurar o Framework de Cluster

**[1]**  Alterar as predefinições

   <pre><code>
   sudo crm configure

   crm(live)configure# rsc_defaults resource-stickiness="1"

   crm(live)configure# commit
   crm(live)configure# exit
   </code></pre>

## <a name="prepare-for-sap-netweaver-installation"></a>Preparar para a instalação do SAP NetWeaver

1. **[A]**  Criar os diretórios partilhados

   <pre><code>
   sudo mkdir -p /sapmnt/<b>NWS</b>
   sudo mkdir -p /usr/sap/trans
   sudo mkdir -p /usr/sap/<b>NWS</b>/SYS

   sudo chattr +i /sapmnt/<b>NWS</b>
   sudo chattr +i /usr/sap/trans
   sudo chattr +i /usr/sap/<b>NWS</b>/SYS
   </code></pre>

1. **[A]**  Configurar autofs
 
   <pre><code>
   sudo vi /etc/auto.master

   # Add the following line to the file, save and exit
   +auto.master
   /- /etc/auto.direct
   </code></pre>

   Criar um ficheiro com o

   <pre><code>
   sudo vi /etc/auto.direct

   # Add the following lines to the file, save and exit
   /sapmnt/<b>NWS</b> -nfsvers=4,nosymlink,sync <b>nws-nfs</b>:/sapmntsid
   /usr/sap/trans -nfsvers=4,nosymlink,sync <b>nws-nfs</b>:/trans
   /usr/sap/<b>NWS</b>/SYS -nfsvers=4,nosymlink,sync <b>nws-nfs</b>:/sidsys
   </code></pre>

   Reiniciar autofs montar novas partilhas

   <pre><code>
   sudo systemctl enable autofs
   sudo service autofs restart
   </code></pre>

1. **[A]**  Configurar TROCAR ficheiros
 
   <pre><code>
   sudo vi /etc/waagent.conf

   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=<b>y</b>

   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=<b>2000</b>
   </code></pre>

   Reinicie o agente para ativar a alteração

   <pre><code>
   sudo service waagent restart
   </code></pre>

### <a name="installing-sap-netweaver-ascsers"></a>Instalar o SAP NetWeaver ASCS/ERS

1. **[1]**  Criar um recurso de IP virtual e a sonda de estado de funcionamento para o Balanceador de carga interno

   <pre><code>
   sudo crm node standby <b>nws-cl-1</b>
   sudo crm configure

   crm(live)configure# primitive drbd_<b>NWS</b>_ASCS \
     ocf:linbit:drbd \
     params drbd_resource="<b>NWS</b>_ascs" \
     op monitor interval="15" role="Master" \
     op monitor interval="30" role="Slave"

   crm(live)configure# ms ms-drbd_<b>NWS</b>_ASCS drbd_<b>NWS</b>_ASCS \
     meta master-max="1" master-node-max="1" clone-max="2" \
     clone-node-max="1" notify="true"

   crm(live)configure# primitive fs_<b>NWS</b>_ASCS \
     ocf:heartbeat:Filesystem \
     params device=/dev/drbd0 \
     directory=/usr/sap/<b>NWS</b>/ASCS<b>00</b>  \
     fstype=xfs \
     op monitor interval="10s"

   crm(live)configure# primitive vip_<b>NWS</b>_ASCS IPaddr2 \
     params ip=<b>10.0.0.10</b> cidr_netmask=24 \
     op monitor interval=10 timeout=20

   crm(live)configure# primitive nc_<b>NWS</b>_ASCS anything \
     params binfile="/usr/bin/nc" cmdline_options="-l -k 620<b>00</b>" \
     op monitor timeout=20s interval=10 depth=0
   
   crm(live)configure# group g-<b>NWS</b>_ASCS nc_<b>NWS</b>_ASCS vip_<b>NWS</b>_ASCS fs_<b>NWS</b>_ASCS \
      meta resource-stickiness=3000

   crm(live)configure# order o-<b>NWS</b>_drbd_before_ASCS inf: \
     ms-drbd_<b>NWS</b>_ASCS:promote g-<b>NWS</b>_ASCS:start
   
   crm(live)configure# colocation col-<b>NWS</b>_ASCS_on_drbd inf: \
     ms-drbd_<b>NWS</b>_ASCS:Master g-<b>NWS</b>_ASCS
   
   crm(live)configure# commit
   crm(live)configure# exit
   </code></pre>

   Certifique-se de que o estado de cluster está ok e de que todos os recursos são iniciados. Não é importante no nó que os recursos estão em execução.

   <pre><code>
   sudo crm_mon -r

   # Node nws-cl-1: standby
   # <b>Online: [ nws-cl-0 ]</b>
   # 
   # Full list of resources:
   # 
   #  Master/Slave Set: ms-drbd_NWS_ASCS [drbd_NWS_ASCS]
   #      <b>Masters: [ nws-cl-0 ]</b>
   #      Stopped: [ nws-cl-1 ]
   #  Resource Group: g-NWS_ASCS
   #      nc_NWS_ASCS        (ocf::heartbeat:anything):      <b>Started nws-cl-0</b>
   #      vip_NWS_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started nws-cl-0</b>
   #      fs_NWS_ASCS        (ocf::heartbeat:Filesystem):    <b>Started nws-cl-0</b>
   </code></pre>

1. **[1]**  Instalar SAP NetWeaver ASCS  

   Instalar o SAP NetWeaver ASCS como raiz no primeiro nó utilizando um nome de anfitrião virtual que mapeia para o endereço IP da configuração de front-end de Balanceador de carga para o ASCS, por exemplo <b>nws ascs</b>, <b>10.0.0.10</b> e o número de instância que utilizou para a sonda do Balanceador de carga por exemplo <b>00</b>.

   Pode utilizar o parâmetro de sapinst SAPINST_REMOTE_ACCESS_USER para permitir que um utilizador não raiz ao ligar ao sapinst.

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

1. **[1]**  Criar um recurso de IP virtual e a sonda de estado de funcionamento para o Balanceador de carga interno

   <pre><code>
   sudo crm node standby <b>nws-cl-0</b>
   sudo crm node online <b>nws-cl-1</b>
   sudo crm configure

   crm(live)configure# primitive drbd_<b>NWS</b>_ERS \
     ocf:linbit:drbd \
     params drbd_resource="<b>NWS</b>_ers" \
     op monitor interval="15" role="Master" \
     op monitor interval="30" role="Slave"

   crm(live)configure# ms ms-drbd_<b>NWS</b>_ERS drbd_<b>NWS</b>_ERS \
     meta master-max="1" master-node-max="1" clone-max="2" \
     clone-node-max="1" notify="true"

   crm(live)configure# primitive fs_<b>NWS</b>_ERS \
     ocf:heartbeat:Filesystem \
     params device=/dev/drbd1 \
     directory=/usr/sap/<b>NWS</b>/ERS<b>02</b>  \
     fstype=xfs \
     op monitor interval="10s"

   crm(live)configure# primitive vip_<b>NWS</b>_ERS IPaddr2 \
     params ip=<b>10.0.0.11</b> cidr_netmask=24 \
     op monitor interval=10 timeout=20

   crm(live)configure# primitive nc_<b>NWS</b>_ERS anything \
    params binfile="/usr/bin/nc" cmdline_options="-l -k 621<b>02</b>" \
    op monitor timeout=20s interval=10 depth=0

   crm(live)configure# group g-<b>NWS</b>_ERS nc_<b>NWS</b>_ERS vip_<b>NWS</b>_ERS fs_<b>NWS</b>_ERS

   crm(live)configure# order o-<b>NWS</b>_drbd_before_ERS inf: \
     ms-drbd_<b>NWS</b>_ERS:promote g-<b>NWS</b>_ERS:start
   
   crm(live)configure# colocation col-<b>NWS</b>_ERS_on_drbd inf: \
     ms-drbd_<b>NWS</b>_ERS:Master g-<b>NWS</b>_ERS
   
   crm(live)configure# commit
   # WARNING: Resources nc_NWS_ASCS,nc_NWS_ERS,nc_NWS_nfs violate uniqueness for parameter "binfile": "/usr/bin/nc"
   # Do you still want to commit (y/n)? y

   crm(live)configure# exit
   
   </code></pre>
 
   Certifique-se de que o estado de cluster está ok e de que todos os recursos são iniciados. Não é importante no nó que os recursos estão em execução.

   <pre><code>
   sudo crm_mon -r

   # Node <b>nws-cl-0: standby</b>
   # <b>Online: [ nws-cl-1 ]</b>
   # 
   # Full list of resources:
   # 
   #  Master/Slave Set: ms-drbd_NWS_ASCS [drbd_NWS_ASCS]
   #      <b>Masters: [ nws-cl-1 ]</b>
   #      Stopped: [ nws-cl-0 ]
   #  Resource Group: g-NWS_ASCS
   #      nc_NWS_ASCS        (ocf::heartbeat:anything):      <b>Started nws-cl-1</b>
   #      vip_NWS_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started nws-cl-1</b>
   #      fs_NWS_ASCS        (ocf::heartbeat:Filesystem):    <b>Started nws-cl-1</b>
   #  Master/Slave Set: ms-drbd_NWS_ERS [drbd_NWS_ERS]
   #      <b>Masters: [ nws-cl-1 ]</b>
   #      Stopped: [ nws-cl-0 ]
   #  Resource Group: g-NWS_ERS
   #      nc_NWS_ERS (ocf::heartbeat:anything):      <b>Started nws-cl-1</b>
   #      vip_NWS_ERS        (ocf::heartbeat:IPaddr2):       <b>Started nws-cl-1</b>
   #      fs_NWS_ERS (ocf::heartbeat:Filesystem):    <b>Started nws-cl-1</b>
   </code></pre>

1. **[2]**  Instalar SAP NetWeaver ERS  

   Instalar o SAP NetWeaver ERS como raiz no nó segundo utilizando um nome de anfitrião virtual que mapeia para o endereço IP da configuração de front-end de Balanceador de carga para o ERS, por exemplo <b>nws ers</b>, <b>10.0.0.11</b> e o número de instância que utilizou para a sonda do Balanceador de carga por exemplo <b>02</b>.

   Pode utilizar o parâmetro de sapinst SAPINST_REMOTE_ACCESS_USER para permitir que um utilizador não raiz ao ligar ao sapinst.

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   > [!NOTE]
   > Utilize SWPM SP 20 LP 05 ou superior. Versões inferiores não corretamente as permissões e a instalação irá falhar.
   > 

1. **[1]**  Adapt ASCS/SCS e ERS instância perfis
 
   * Perfil ASCS/SCS

   <pre><code> 
   sudo vi /sapmnt/<b>NWS</b>/profile/<b>NWS</b>_<b>ASCS00</b>_<b>nws-ascs</b>

   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)

   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector

   # Add the keep alive parameter
   enque/encni/set_so_keepalive = true
   </code></pre>

   * Perfil ERS

   <pre><code> 
   sudo vi /sapmnt/<b>NWS</b>/profile/<b>NWS</b>_ERS<b>02</b>_<b>nws-ers</b>

   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   </code></pre>


1. **[A]**  Configurar Keep Alive

   A comunicação entre o servidor de aplicações do SAP NetWeaver e ASCS/SCS é encaminhada através de um balanceador de carga de software. O Balanceador de carga desliga ligações inativas após um limite de tempo configurável. Para evitar esta situação tem de definir um parâmetro no perfil do SAP NetWeaver ASCS/SCS e alterar as definições de sistema do Linux. Leia [1410736 de nota SAP] [ 1410736] para obter mais informações.
   
   O ASCS/SCS perfil parâmetro colocar/encni/set_so_keepalive já foi adicionado no último passo.

   <pre><code> 
   # Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=120
   </code></pre>

1. **[A]**  Configurar os utilizadores SAP após a instalação
 
   <pre><code>
   # Add sidadm to the haclient group
   sudo usermod -aG haclient <b>nws</b>adm   
   </code></pre>

1. **[1]**  Adicionar os serviços ASCS e ERS SAP para o ficheiro sapservice

   Adicione os ASCS service entrada para o segundo nó e copie a entrada de serviço ERS para o primeiro nó.

   <pre><code>
   cat /usr/sap/sapservices | grep ASCS<b>00</b> | sudo ssh <b>nws-cl-1</b> "cat >>/usr/sap/sapservices"
   sudo ssh <b>nws-cl-1</b> "cat /usr/sap/sapservices" | grep ERS<b>02</b> | sudo tee -a /usr/sap/sapservices
   </code></pre>

1. **[1]**  Criar os recursos de cluster do SAP

   <pre><code>
   sudo crm configure property maintenance-mode="true"

   sudo crm configure

   crm(live)configure# primitive rsc_sap_<b>NWS</b>_ASCS<b>00</b> SAPInstance \
    operations $id=rsc_sap_<b>NWS</b>_ASCS<b>00</b>-operations \
    op monitor interval=11 timeout=60 on_fail=restart \
    params InstanceName=<b>NWS</b>_ASCS<b>00</b>_<b>nws-ascs</b> START_PROFILE="/sapmnt/<b>NWS</b>/profile/<b>NWS</b>_ASCS<b>00</b>_<b>nws-ascs</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 failure-timeout=60 migration-threshold=1 priority=10

   crm(live)configure# primitive rsc_sap_<b>NWS</b>_ERS<b>02</b> SAPInstance \
    operations $id=rsc_sap_<b>NWS</b>_ERS<b>02</b>-operations \
    op monitor interval=11 timeout=60 on_fail=restart \
    params InstanceName=<b>NWS</b>_ERS<b>02</b>_<b>nws-ers</b> START_PROFILE="/sapmnt/<b>NWS</b>/profile/<b>NWS</b>_ERS<b>02</b>_<b>nws-ers</b>" AUTOMATIC_RECOVER=false IS_ERS=true \
    meta priority=1000

   crm(live)configure# modgroup g-<b>NWS</b>_ASCS add rsc_sap_<b>NWS</b>_ASCS<b>00</b>
   crm(live)configure# modgroup g-<b>NWS</b>_ERS add rsc_sap_<b>NWS</b>_ERS<b>02</b>

   crm(live)configure# colocation col_sap_<b>NWS</b>_no_both -5000: g-<b>NWS</b>_ERS g-<b>NWS</b>_ASCS
   crm(live)configure# location loc_sap_<b>NWS</b>_failover_to_ers rsc_sap_<b>NWS</b>_ASCS<b>00</b> rule 2000: runs_ers_<b>NWS</b> eq 1
   crm(live)configure# order ord_sap_<b>NWS</b>_first_start_ascs Optional: rsc_sap_<b>NWS</b>_ASCS<b>00</b>:start rsc_sap_<b>NWS</b>_ERS<b>02</b>:stop symmetrical=false

   crm(live)configure# commit
   crm(live)configure# exit

   sudo crm configure property maintenance-mode="false"
   sudo crm node online <b>nws-cl-0</b>
   </code></pre>

   Certifique-se de que o estado de cluster está ok e de que todos os recursos são iniciados. Não é importante no nó que os recursos estão em execução.

   <pre><code>
   sudo crm_mon -r

   # Online: <b>[ nws-cl-0 nws-cl-1 ]</b>
   # 
   # Full list of resources:
   # 
   #  Master/Slave Set: ms-drbd_NWS_ASCS [drbd_NWS_ASCS]
   #      <b>Masters: [ nws-cl-0 ]</b>
   #      <b>Slaves: [ nws-cl-1 ]</b>
   #  Resource Group: g-NWS_ASCS
   #      nc_NWS_ASCS        (ocf::heartbeat:anything):      <b>Started nws-cl-0</b>
   #      vip_NWS_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started nws-cl-0</b>
   #      fs_NWS_ASCS        (ocf::heartbeat:Filesystem):    <b>Started nws-cl-0</b>
   #      rsc_sap_NWS_ASCS00 (ocf::heartbeat:SAPInstance):   <b>Started nws-cl-0</b>
   #  Master/Slave Set: ms-drbd_NWS_ERS [drbd_NWS_ERS]
   #      <b>Masters: [ nws-cl-1 ]</b>
   #      <b>Slaves: [ nws-cl-0 ]</b>
   #  Resource Group: g-NWS_ERS
   #      nc_NWS_ERS (ocf::heartbeat:anything):      <b>Started nws-cl-1</b>
   #      vip_NWS_ERS        (ocf::heartbeat:IPaddr2):       <b>Started nws-cl-1</b>
   #      fs_NWS_ERS (ocf::heartbeat:Filesystem):    <b>Started nws-cl-1</b>
   #      rsc_sap_NWS_ERS02  (ocf::heartbeat:SAPInstance):   <b>Started nws-cl-1</b>
   </code></pre>

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

#### <a name="1-create-the-stonith-devices"></a>**[1]**  Criar os dispositivos STONITH

Depois de editar as permissões para as máquinas virtuais, pode configurar os dispositivos STONITH no cluster.

<pre><code>
sudo crm configure

# replace the bold string with your subscription id, resource group, tenant id, service principal id and password

crm(live)configure# primitive rsc_st_azure_1 stonith:fence_azure_arm \
   params subscriptionId="<b>subscription id</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant id</b>" login="<b>login id</b>" passwd="<b>password</b>"

crm(live)configure# primitive rsc_st_azure_2 stonith:fence_azure_arm \
   params subscriptionId="<b>subscription id</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant id</b>" login="<b>login id</b>" passwd="<b>password</b>"

crm(live)configure# colocation col_st_azure -2000: rsc_st_azure_1:Started rsc_st_azure_2:Started

crm(live)configure# commit
crm(live)configure# exit
</code></pre>

#### <a name="1-enable-the-use-of-a-stonith-device"></a>**[1]**  Ativar a utilização de um dispositivo STONITH

Ativar a utilização de um dispositivo STONITH

<pre><code>
sudo crm configure property stonith-enabled=true 
</code></pre>

## <a name="install-database"></a>Instalar a base de dados

Neste exemplo, uma replicação do sistema de SAP HANA está instalada e configurada. SAP HANA será executado no mesmo cluster como o SAP NetWeaver ASCS/SCS e ERS. Também pode instalar o SAP HANA num cluster dedicado. Consulte [disponibilidade elevada do SAP HANA em Azure Virtual Machines (VMs)] [ sap-hana-ha] para obter mais informações.

### <a name="prepare-for-sap-hana-installation"></a>Preparar para a instalação de SAP HANA

Em geral, recomendamos que utilize LVM para volumes que armazenam dados e ficheiros de registo. Para fins de teste, também pode optar por armazenar os dados e ficheiros diretamente num disco simples de registo.

1. **[A]**  LVM  
   O exemplo abaixo parte do princípio de que as máquinas virtuais tem quatro dados os discos ligados que devem ser utilizados para criar dois volumes.
   
   Crie volumes de físicos para todos os discos que pretende utilizar.
   
   <pre><code>
   sudo pvcreate /dev/sdd
   sudo pvcreate /dev/sde
   sudo pvcreate /dev/sdf
   sudo pvcreate /dev/sdg
   </code></pre>
   
   Criar um grupo de volume para os ficheiros de dados, um grupo de volume para os ficheiros de registo e outro para o diretório partilhado de SAP HANA
   
   <pre><code>
   sudo vgcreate vg_hana_data /dev/sdd /dev/sde
   sudo vgcreate vg_hana_log /dev/sdf
   sudo vgcreate vg_hana_shared /dev/sdg
   </code></pre>
   
   Criar os volumes lógicos
   
   <pre><code>
   sudo lvcreate -l 100%FREE -n hana_data vg_hana_data
   sudo lvcreate -l 100%FREE -n hana_log vg_hana_log
   sudo lvcreate -l 100%FREE -n hana_shared vg_hana_shared
   sudo mkfs.xfs /dev/vg_hana_data/hana_data
   sudo mkfs.xfs /dev/vg_hana_log/hana_log
   sudo mkfs.xfs /dev/vg_hana_shared/hana_shared
   </code></pre>
   
   Criar os diretórios de montagem e copie o UUID de todos os volumes lógicos
   
   <pre><code>
   sudo mkdir -p /hana/data
   sudo mkdir -p /hana/log
   sudo mkdir -p /hana/shared
   sudo chattr +i /hana/data
   sudo chattr +i /hana/log
   sudo chattr +i /hana/shared
   # write down the id of /dev/vg_hana_data/hana_data, /dev/vg_hana_log/hana_log and /dev/vg_hana_shared/hana_shared
   sudo blkid
   </code></pre>
   
   Criação de entradas de autofs para os volumes lógicas três
   
   <pre><code>
   sudo vi /etc/auto.direct
   </code></pre>
   
   Inserir a linha ao sudo vi /etc/auto.direct
   
   <pre><code>
   /hana/data -fstype=xfs :UUID=<b>&lt;UUID of /dev/vg_hana_data/hana_data&gt;</b>
   /hana/log -fstype=xfs :UUID=<b>&lt;UUID of /dev/vg_hana_log/hana_log&gt;</b>
   /hana/shared -fstype=xfs :UUID=<b>&lt;UUID of /dev/vg_hana_shared/hana_shared&gt;</b>
   </code></pre>
   
   Montar os novos volumes
   
   <pre><code>
   sudo service autofs restart 
   </code></pre>

1. **[A]**  Discos simples  

   Para pequenas ou demonstração sistemas, pode colocar os ficheiros de dados e de registo HANA num disco. Os seguintes comandos crie uma partição no /dev/sdc e formate-o com xfs.
   ```bash
   sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/sdd'
   sudo mkfs.xfs /dev/sdd1
   
   # write down the id of /dev/sdd1
   sudo /sbin/blkid
   sudo vi /etc/auto.direct
   ```
   
   Inserir a linha para /etc/auto.direct
   <pre><code>
   /hana -fstype=xfs :UUID=<b>&lt;UUID&gt;</b>
   </code></pre>
   
   Criar o diretório de destino e montar o disco.
   
   <pre><code>
   sudo mkdir /hana
   sudo chattr +i /hana
   sudo service autofs restart
   </code></pre>

### <a name="installing-sap-hana"></a>Instalar o SAP HANA

Os seguintes passos baseiam-se no capítulo 4 do [guia de SAP HANA SR desempenho otimizado cenário] [ suse-hana-ha-guide] para instalar a replicação do sistema de SAP HANA. Leia este programa antes de continuar a instalação.

1. **[A]**  Executar hdblcm a partir do HANA DVD
   
   <pre><code>
   sudo hdblcm --sid=<b>HDB</b> --number=<b>03</b> --action=install --batch --password=<b>&lt;password&gt;</b> --system_user_password=<b>&lt;password for system user&gt;</b>

   sudo /hana/shared/<b>HDB</b>/hdblcm/hdblcm --action=configure_internal_network --listen_interface=internal --internal_network=<b>10.0.0/24</b> --password=<b>&lt;password for system user&gt;</b> --batch
   </code></pre>

1. **[A]**  Atualizar o agente do anfitrião SAP

   Transferir o arquivo de agente do anfitrião SAP mais recente do [SAP Softwarecenter] [ sap-swcenter] e execute o seguinte comando para atualizar o agente. Substitua o caminho para o arquivo para apontar para o ficheiro que transferiu.
   <pre><code>
   sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive <b>&lt;path to SAP Host Agent SAR&gt;</b> 
   </code></pre>

1. **[1]**  Replicação criar HANA (como raiz)  

   Execute o seguinte comando. Certifique-se de que substitui a negrito cadeias (HANA sistema ID HDB e instância número 03) com os valores a instalação de SAP HANA.
   <pre><code>
   PATH="$PATH:/usr/sap/<b>HDB</b>/HDB<b>03</b>/exe"
   hdbsql -u system -i <b>03</b> 'CREATE USER <b>hdb</b>hasync PASSWORD "<b>passwd</b>"' 
   hdbsql -u system -i <b>03</b> 'GRANT DATA ADMIN TO <b>hdb</b>hasync' 
   hdbsql -u system -i <b>03</b> 'ALTER USER <b>hdb</b>hasync DISABLE PASSWORD LIFETIME' 
   </code></pre>

1. **[A]**  Criar keystore entrada (root)

   <pre><code>
   PATH="$PATH:/usr/sap/<b>HDB</b>/HDB<b>03</b>/exe"
   hdbuserstore SET <b>hdb</b>haloc localhost:3<b>03</b>15 <b>hdb</b>hasync <b>&lt;passwd&gt;</b>
   </code></pre>

1. **[1]**  Base de dados de cópia de segurança (como raiz)

   <pre><code>
   PATH="$PATH:/usr/sap/<b>HDB</b>/HDB<b>03</b>/exe"
   hdbsql -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')" 
   </code></pre>

1. **[1]**  Mudar para o utilizador de sapsid HANA e criar o site primário.

   <pre><code>
   su - <b>hdb</b>adm
   hdbnsutil -sr_enable –-name=<b>SITE1</b>
   </code></pre>

1. **[2]**  Mudar para o utilizador de sapsid HANA e criar o site secundário.

   <pre><code>
   su - <b>hdb</b>adm
   sapcontrol -nr <b>03</b> -function StopWait 600 10
   hdbnsutil -sr_register --remoteHost=<b>nws-cl-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
   </code></pre>

1. **[1]**  Recursos de cluster de SAP HANA criar

   Em primeiro lugar, crie a topologia.
   
   <pre><code>
   sudo crm configure

   # replace the bold string with your instance number and HANA system id
   
   crm(live)configure# primitive rsc_SAPHanaTopology_<b>HDB</b>_HDB<b>03</b>   ocf:suse:SAPHanaTopology \
     operations $id="rsc_sap2_<b>HDB</b>_HDB<b>03</b>-operations" \
     op monitor interval="10" timeout="600" \
     op start interval="0" timeout="600" \
     op stop interval="0" timeout="300" \
     params SID="<b>HDB</b>" InstanceNumber="<b>03</b>"
    
   crm(live)configure# clone cln_SAPHanaTopology_<b>HDB</b>_HDB<b>03</b> rsc_SAPHanaTopology_<b>HDB</b>_HDB<b>03</b> \
     meta is-managed="true" clone-node-max="1" target-role="Started" interleave="true"

   crm(live)configure# commit
   crm(live)configure# exit
   </code></pre>
   
   Em seguida, criar os recursos HANA
   
   <pre><code>
   sudo crm configure

   # replace the bold string with your instance number, HANA system id and the frontend IP address of the Azure load balancer. 
    
   crm(live)configure# primitive rsc_SAPHana_<b>HDB</b>_HDB<b>03</b> ocf:suse:SAPHana \
     operations $id="rsc_sap_<b>HDB</b>_HDB<b>03</b>-operations" \
     op start interval="0" timeout="3600" \
     op stop interval="0" timeout="3600" \
     op promote interval="0" timeout="3600" \
     op monitor interval="60" role="Master" timeout="700" \
     op monitor interval="61" role="Slave" timeout="700" \
     params SID="<b>HDB</b>" InstanceNumber="<b>03</b>" PREFER_SITE_TAKEOVER="true" \
     DUPLICATE_PRIMARY_TIMEOUT="7200" AUTOMATED_REGISTER="false"
    
   crm(live)configure# ms msl_SAPHana_<b>HDB</b>_HDB<b>03</b> rsc_SAPHana_<b>HDB</b>_HDB<b>03</b> \
     meta is-managed="true" notify="true" clone-max="2" clone-node-max="1" \
     target-role="Started" interleave="true"
    
   crm(live)configure# primitive rsc_ip_<b>HDB</b>_HDB<b>03</b> ocf:heartbeat:IPaddr2 \ 
     meta target-role="Started" is-managed="true" \ 
     operations $id="rsc_ip_<b>HDB</b>_HDB<b>03</b>-operations" \ 
     op monitor interval="10s" timeout="20s" \ 
     params ip="<b>10.0.0.12</b>" 

   crm(live)configure# primitive rsc_nc_<b>HDB</b>_HDB<b>03</b> anything \ 
     params binfile="/usr/bin/nc" cmdline_options="-l -k 625<b>03</b>" \ 
     op monitor timeout=20s interval=10 depth=0 

   crm(live)configure# group g_ip_<b>HDB</b>_HDB<b>03</b> rsc_ip_<b>HDB</b>_HDB<b>03</b> rsc_nc_<b>HDB</b>_HDB<b>03</b>
    
   crm(live)configure# colocation col_saphana_ip_<b>HDB</b>_HDB<b>03</b> 2000: g_ip_<b>HDB</b>_HDB<b>03</b>:Started \ 
     msl_SAPHana_<b>HDB</b>_HDB<b>03</b>:Master  

   crm(live)configure# order ord_SAPHana_<b>HDB</b>_HDB<b>03</b> 2000: cln_SAPHanaTopology_<b>HDB</b>_HDB<b>03</b> \ 
     msl_SAPHana_<b>HDB</b>_HDB<b>03</b>
    
   crm(live)configure# commit
   crm(live)configure# exit
   </code></pre>

   Certifique-se de que o estado de cluster está ok e de que todos os recursos são iniciados. Não é importante no nó que os recursos estão em execução.

   <pre><code>
   sudo crm_mon -r

   # <b>Online: [ nws-cl-0 nws-cl-1 ]</b>
   # 
   # Full list of resources:
   # 
   #  Master/Slave Set: ms-drbd_NWS_ASCS [drbd_NWS_ASCS]
   #      <b>Masters: [ nws-cl-1 ]</b>
   #      <b>Slaves: [ nws-cl-0 ]</b>
   #  Resource Group: g-NWS_ASCS
   #      nc_NWS_ASCS        (ocf::heartbeat:anything):      <b>Started nws-cl-1</b>
   #      vip_NWS_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started nws-cl-1</b>
   #      fs_NWS_ASCS        (ocf::heartbeat:Filesystem):    <b>Started nws-cl-1</b>
   #      rsc_sap_NWS_ASCS00 (ocf::heartbeat:SAPInstance):   <b>Started nws-cl-1</b>
   #  Master/Slave Set: ms-drbd_NWS_ERS [drbd_NWS_ERS]
   #      <b>Masters: [ nws-cl-0 ]</b>
   #      <b>Slaves: [ nws-cl-1 ]</b>
   #  Resource Group: g-NWS_ERS
   #      nc_NWS_ERS (ocf::heartbeat:anything):      <b>Started nws-cl-0</b>
   #      vip_NWS_ERS        (ocf::heartbeat:IPaddr2):       <b>Started nws-cl-0</b>
   #      fs_NWS_ERS (ocf::heartbeat:Filesystem):    <b>Started nws-cl-0</b>
   #      rsc_sap_NWS_ERS02  (ocf::heartbeat:SAPInstance):   <b>Started nws-cl-0</b>
   #  Clone Set: cln_SAPHanaTopology_HDB_HDB03 [rsc_SAPHanaTopology_HDB_HDB03]
   #      <b>Started: [ nws-cl-0 nws-cl-1 ]</b>
   #  Master/Slave Set: msl_SAPHana_HDB_HDB03 [rsc_SAPHana_HDB_HDB03]
   #      <b>Masters: [ nws-cl-0 ]</b>
   #      <b>Slaves: [ nws-cl-1 ]</b>
   #  Resource Group: g_ip_HDB_HDB03
   #      rsc_ip_HDB_HDB03   (ocf::heartbeat:IPaddr2):       <b>Started nws-cl-0</b>
   #      rsc_nc_HDB_HDB03   (ocf::heartbeat:anything):      <b>Started nws-cl-0</b>
   # rsc_st_azure_1  (stonith:fence_azure_arm):      <b>Started nws-cl-0</b>
   # rsc_st_azure_2  (stonith:fence_azure_arm):      <b>Started nws-cl-1</b>
   </code></pre>

1. **[1]**  Instalar a instância de base de dados SAP NetWeaver

   Instalar a instância de base de dados SAP NetWeaver como raiz com um nome de anfitrião virtual que mapeia para o endereço IP da configuração de front-end de Balanceador de carga para a base de dados, por exemplo <b>nws-db</b> e <b>10.0.0.12</b>.

   Pode utilizar o parâmetro de sapinst SAPINST_REMOTE_ACCESS_USER para permitir que um utilizador não raiz ao ligar ao sapinst.

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

## <a name="sap-netweaver-application-server-installation"></a>Instalação do servidor de aplicação do SAP NetWeaver

Siga estes passos para instalar um servidor de aplicações SAP. O passos abaixo partem do princípio de que instala o servidor de aplicações num servidor diferente do que os servidores ASCS/SCS e HANA. Caso contrário, alguns dos passos abaixo (como configurar a resolução de nome de anfitrião) não são necessários.

1. Configurar a resolução de nome de anfitrião    
   Pode utilizar um servidor DNS ou modificar os /etc/hosts em todos os nós. Este exemplo mostra como utilizar o ficheiro /etc/hosts.
   Substituir o endereço IP e o nome de anfitrião nos seguintes comandos
   ```bash
   sudo vi /etc/hosts
   ```
   Insira as seguintes linhas ao /etc/hosts. Alterar o endereço IP e nome de anfitrião para corresponder ao seu ambiente    
    
   <pre><code>
   # IP address of the load balancer frontend configuration for NFS
   <b>10.0.0.4 nws-nfs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS/SCS
   <b>10.0.0.10 nws-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ERS
   <b>10.0.0.11 nws-ers</b>
   # IP address of the load balancer frontend configuration for database
   <b>10.0.0.12 nws-db</b>
   # IP address of the application server
   <b>10.0.0.8 nws-di-0</b>
   </code></pre>

1. Criar o diretório de sapmnt

   <pre><code>
   sudo mkdir -p /sapmnt/<b>NWS</b>
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/<b>NWS</b>
   sudo chattr +i /usr/sap/trans
   </code></pre>

1. Configurar autofs
 
   <pre><code>
   sudo vi /etc/auto.master

   # Add the following line to the file, save and exit
   +auto.master
   /- /etc/auto.direct
   </code></pre>

   Criar um novo ficheiro com o

   <pre><code>
   sudo vi /etc/auto.direct

   # Add the following lines to the file, save and exit
   /sapmnt/<b>NWS</b> -nfsvers=4,nosymlink,sync <b>nws-nfs</b>:/sapmntsid
   /usr/sap/trans -nfsvers=4,nosymlink,sync <b>nws-nfs</b>:/trans
   </code></pre>

   Reiniciar autofs montar novas partilhas

   <pre><code>
   sudo systemctl enable autofs
   sudo service autofs restart
   </code></pre>

1. Configurar o ficheiro de comutação
 
   <pre><code>
   sudo vi /etc/waagent.conf

   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=<b>y</b>

   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=<b>2000</b>
   </code></pre>

   Reinicie o agente para ativar a alteração

   <pre><code>
   sudo service waagent restart
   </code></pre>

1. Instalar o servidor de aplicações do SAP NetWeaver

   Instale um servidor de aplicações do SAP NetWeaver primário ou adicional.

   Pode utilizar o parâmetro de sapinst SAPINST_REMOTE_ACCESS_USER para permitir que um utilizador não raiz ao ligar ao sapinst.

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

1. Atualizar o arquivo seguro de SAP HANA

   Atualize o arquivo seguro de SAP HANA para apontar para o nome virtual da configuração de replicação do sistema do SAP HANA.
   <pre><code>
   su - <b>nws</b>adm
   hdbuserstore SET DEFAULT <b>nws-db</b>:3<b>03</b>15 <b>SAPABAP1</b> <b>&lt;password of ABAP schema&gt;</b>
   </code></pre>

## <a name="next-steps"></a>Passos seguintes
* [Azure máquinas virtuais de planeamento e implementação de SAP][planning-guide]
* [Implementação de máquinas virtuais do Azure para SAP][deployment-guide]
* [Implementação de DBMS de máquinas virtuais do Azure para SAP][dbms-guide]
* Para saber como estabelecer elevada disponibilidade e o plano de recuperação de desastres do SAP HANA no Azure (instâncias de grande), consulte o artigo [SAP HANA (instâncias de grande) elevada disponibilidade e recuperação após desastre no Azure](hana-overview-high-availability-disaster-recovery.md).
* Para saber como estabelecer elevada disponibilidade e o plano de recuperação de desastres do SAP HANA em VMs do Azure, consulte o artigo [disponibilidade elevada do SAP HANA em Azure Virtual Machines (VMs)][sap-hana-ha]