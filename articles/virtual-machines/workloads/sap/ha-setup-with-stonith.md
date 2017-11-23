---
title: "Elevada disponibilidade que configurar com STONITH para SAP HANA no Azure (instâncias de grande) | Microsoft Docs"
description: "Estabelecer elevada disponibilidade para SAP HANA no Azure (instâncias de grande) no SUSE utilizando o STONITH"
services: virtual-machines-linux
documentationcenter: 
author: saghorpa
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/21/2017
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d710fe24673c6ddc581d36e4f0cacdb750ff74f9
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2017
---
# <a name="high-availability-set-up-in-suse-using-the-stonith"></a>Elevada disponibilidade que configurar no SUSE utilizando o STONITH
Este documento fornece instruções passo a passo detalhadas para configurar a elevada disponibilidade no sistema operativo SUSE utilizando o dispositivo STONITH.

**Exclusão de responsabilidade:** *neste guia é derivado testando o conjunto de cópias de segurança no ambiente do Microsoft HANA grande instâncias, que funciona com êxito. Como a equipa de gestão de serviço da Microsoft para instâncias de grande HANA não suporta o sistema operativo, poderá ter de contactar o SUSE para resolução de problemas ou esclarecimentos na camada do sistema operativo. Equipa de gestão do serviço Microsoft configurar dispositivo STONITH e totalmente suporta e pode ser envolvida para resolução de problemas para problemas de STONITH de dispositivos.*
## <a name="overview"></a>Descrição geral
Para configurar a elevada disponibilidade utilizando SUSE clustering, tem de cumprir os seguintes pré-requisitos.
### <a name="pre-requisites"></a>Pré-requisitos
- Instâncias de grande HANA são aprovisionadas
- Sistema operativo está registado
- Servidores de instâncias de grande HANA estão ligados ao servidor SMT obter patches/pacotes
- Sistema operativo têm patches mais recentes instalados
- Configurar a NTP (hora server)
- Leia e compreenda a versão mais recente da documentação no SUSE no HA configurar

### <a name="set-up-details"></a>Configurar detalhes
- Neste guia, utilizámos o seguinte conjunto de cópias de segurança:
- Sistema operativo: SLES 12 SP1 para SAP
- Instâncias de grande HANA: 2xS192 (quatro sockets, 2 TB)
- Versão HANA: HANA 2.0 SP1
- Os nomes de servidor: sapprdhdb95 (Nó1) e sapprdhdb96 (Nó2)
- Dispositivo STONITH: dispositivo STONITH baseado em iSCSI
- NTP configurar num de nós de instância grande HANA

Quando configurar HANA instâncias grande com HSR, pode pedir a equipa de gestão de serviço da Microsoft para configurar STONITH. Se já for um cliente existente que tenha instâncias grande HANA aprovisionado e tem necessidade de dispositivo STONITH estar configurada para os painéis existentes, tem de fornecer as seguintes informações para a equipa de gestão de serviço da Microsoft no formulário de pedido de serviço (SRF). Pode pedir formulário SRF através do Gestor de conta Technical ou seu contacto da Microsoft para a integração de instância grande HANA. Os novos clientes podem pedir dispositivo STONITH no momento de aprovisionamento. As entradas estão disponíveis no formulário de pedido de aprovisionamento.

- Endereço de IP do servidor e o nome de servidor (por exemplo, 10.35.0.1, myhanaserver1)
- Localização (por exemplo, EUA Leste)
- Nome do cliente (por exemplo, a Microsoft)
- SID - HANA identificador de sistema (por exemplo, H11)

Quando o dispositivo STONITH estiver configurado, a equipa de gestão de serviço da Microsoft forneça o nome do dispositivo SBD e configurar o endereço IP do armazenamento iSCSI, que pode utilizar para configurar STONITH. 

Para configurar o HA ponto a ponto utilizando STONITH, os seguintes passos tem de ser seguida:

1.  Identificar o dispositivo SBD
2.  Inicializar o dispositivo SBD
3.  Configurar o Cluster
4.  Configurar o Watchdog Softdog do
5.  Associe o nó ao cluster
6.  Validar o cluster
7.  Configurar os recursos para o cluster
8.  Teste o processo de ativação pós-falha

## <a name="1---identify-the-sbd-device"></a>1.   Identificar o dispositivo SBD
Esta secção descreve sobre como determinar se o dispositivo SBD para o conjunto de cópias de segurança equipa de gestão do Microsoft service tiver configurado o STONITH. **Esta secção aplica-se apenas ao cliente existente**. Se for um cliente novo, a equipa de gestão do serviço Microsoft fornecer SBD nome do dispositivo por si e pode ignorar esta secção.

1.1 modificar */etc/iscsi/initiatorname.isci* para 
``` 
iqn.1996-04.de.suse:01:<Tenant><Location><SID><NodeNumber> 
```

Gestão de serviço da Microsoft fornecem esta cadeia. Modificar o ficheiro no **ambos** nós, no entanto o número de nós é diferente em cada nó.

![initiatorname.png](media/HowToHLI/HASetupWithStonith/initiatorname.png)

1.2 modificar */etc/iscsi/iscsid.conf*: definir *node.session.timeo.replacement_timeout=5* e *node.startup = automática*. Modificar o ficheiro no **ambos** os nós.

1.3 executar o comando de deteção, mostra quatro sessões. Executá-la em ambos os nós.

```
iscsiadm -m discovery -t st -p <IP address provided by Service Management>:3260
```

![iSCSIadmDiscovery.png](media/HowToHLI/HASetupWithStonith/iSCSIadmDiscovery.png)

1.4 executar o comando para iniciar sessão no dispositivo de iSCSI, mostra quatro sessões. Execute-o no **ambos** os nós.

```
iscsiadm -m node -l
```
![iSCSIadmLogin.png](media/HowToHLI/HASetupWithStonith/iSCSIadmLogin.png)

1.5 executar o script de nova análise: *reanálise-scsi-bus.sh*.  Este script mostra os discos novo criados para si.  Executá-la em ambos os nós. Deverá ver um número LUN que é maior que zero (por exemplo: 1, 2 etc.)

```
rescan-scsi-bus.sh
```
![rescanscsibus.png](media/HowToHLI/HASetupWithStonith/rescanscsibus.png)

1.6 para obter o nome de dispositivo que execute o comando *fdisk – l*. Executá-la em ambos os nós. Escolha o dispositivo com o tamanho de **178 MiB**.

```
  fdisk –l
```

![Fdisk l.png](media/HowToHLI/HASetupWithStonith/fdisk-l.png)

## <a name="2---initialize-the-sbd-device"></a>2.   Inicializar o dispositivo SBD

2.1 inicializar o dispositivo SBD no **ambos** nós

```
sbd -d <SBD Device Name> create
```
![sbdcreate.png](media/HowToHLI/HASetupWithStonith/sbdcreate.png)

2.2 verificar que foi escrito para o dispositivo. Fazê-lo **ambos** nós

```
sbd -d <SBD Device Name> dump
```

## <a name="3---configuring-the-cluster"></a>3.   Configurar o Cluster
Esta secção descreve os passos para configurar o cluster SUSE HA.
### <a name="31-package-installation"></a>3.1 instalação do pacote de
3.1.1 Verifique que ha_sles e padrões de documento SAPHanaSR estão instalados. Se não estiver instalado, instale-os. Instalá-lo no **ambos** os nós.
```
zypper in -t pattern ha_sles
zypper in SAPHanaSR SAPHanaSR-doc
```
![zypperpatternha_sles.PNG](media/HowToHLI/HASetupWithStonith/zypperpatternha_sles.png)
![zypperpatternSAPHANASR doc.png](media/HowToHLI/HASetupWithStonith/zypperpatternSAPHANASR-doc.png)

### <a name="32-setting-up-the-cluster"></a>3.2 como configurar o cluster
3.2.1 pode utilizar *ha-cluster-init* comando ou utilize o Assistente de yast2 para configurar o cluster. Neste caso, utilizámos o Assistente de yast2. Execute este passo **apenas no nó principal**.

Siga yast2 > elevada disponibilidade > Cluster ![center.png de controlo de yast](media/HowToHLI/HASetupWithStonith/yast-control-center.png)
![yast-hawk-install.png](media/HowToHLI/HASetupWithStonith/yast-hawk-install.png)

Clique em **Cancelar** como já temos o pacote de halk2 instalado.

![yast-hawk-continue.png](media/HowToHLI/HASetupWithStonith/yast-hawk-continue.png)

Clique em **continuar**

Valor esperado = número de nós implementados (neste caso, 2) ![yast-Cluster-Security.png](media/HowToHLI/HASetupWithStonith/yast-Cluster-Security.png) clique **seguinte**
![yast-cluster-configurar-csync2.png](media/HowToHLI/HASetupWithStonith/yast-cluster-configure-csync2.png) adicionar nomes de nó e, em seguida, clique em "adicionam ficheiros sugeridos"

Clique em "Ativar csync2"

Clique em "Gerar pré Shared chaves", mostra abaixo pop-up

![yast-chave-file.png](media/HowToHLI/HASetupWithStonith/yast-key-file.png)

Clique em **OK**

A autenticação é executada com os endereços IP e pré shared chaves no Csync2. O ficheiro de chave é gerado com csync2 -k /etc/csync2/key_hagroup. Manualmente o ficheiro key_hagroup deve ser copiado para todos os membros do cluster depois de criado. **Certifique-se para copiar o ficheiro de nó de 1 para Nó2**.

![yast-cluster-conntrackd.png](media/HowToHLI/HASetupWithStonith/yast-cluster-conntrackd.png)

Clique em **seguinte**
![yast-cluster-service.png](media/HowToHLI/HASetupWithStonith/yast-cluster-service.png)

A opção predefinida, Booting foi desativado, alterá-la como "ativado" para que pacemaker é iniciado no arranque. Pode efetuar a escolha com base no seu conjunto de cópias de segurança requisitos.
Clique em **seguinte** e a configuração de cluster está concluída.

## <a name="4---setting-up-the-softdog-watchdog"></a>4.   Configurar o Watchdog Softdog do
Esta secção descreve a configuração de watchdog do (softdog).

4.1 Adicione a seguinte linha para */etc/init.d/boot.local* no **ambos** os nós.
```
modprobe softdog
```
![modprobe softdog.png](media/HowToHLI/HASetupWithStonith/modprobe-softdog.png)

4.2 atualizar o ficheiro */etc/sysconfig/sbd* no **ambos** os nós da seguinte forma:
```
SBD_DEVICE="<SBD Device Name>"
```
![SBD device.png](media/HowToHLI/HASetupWithStonith/sbd-device.png)

4.3 carregar o módulo de kernel no **ambos** os nós, executando o seguinte comando
```
modprobe softdog
```
![modprobe-softdog-command.png](media/HowToHLI/HASetupWithStonith/modprobe-softdog-command.png)

4.4 Verifique e certifique-se de que softdog está em execução como o seguinte procedimento no **ambos** nós:
```
lsmod | grep dog
```
![lsmod-grep-dog.png](media/HowToHLI/HASetupWithStonith/lsmod-grep-dog.png)

4.5 iniciar o dispositivo SBD **ambos** nós
```
/usr/share/sbd/sbd.sh start
```
![SBD-ostrar-start.png](media/HowToHLI/HASetupWithStonith/sbd-sh-start.png)

4.6 testar o daemon SBD **ambos** os nós. Pode ver duas entradas depois de configurar no **ambos** nós
```
sbd -d <SBD Device Name> list
```
![SBD list.png](media/HowToHLI/HASetupWithStonith/sbd-list.png)

4.7 enviar uma mensagem de teste **um** os nós
```
sbd  -d <SBD Device Name> message <node2> <message>
```
![SBD list.png](media/HowToHLI/HASetupWithStonith/sbd-list.png)

4.8 no **segundo** (Nó2), pode verificar o estado de mensagem de nó
```
sbd  -d <SBD Device Name> list
```
![SBD-lista-message.png](media/HowToHLI/HASetupWithStonith/sbd-list-message.png)

4.9 para adotar a configuração de sbd, atualize o ficheiro */etc/sysconfig/sbd* seguinte forma. Atualizar o ficheiro no **ambos** nós
```
SBD_DEVICE=" <SBD Device Name>" 
SBD_WATCHDOG="yes" 
SBD_PACEMAKER="yes" 
SBD_STARTMODE="clean" 
SBD_OPTS=""
```
4.10 iniciar o serviço de pacemaker no **nó principal** (Nó1)
```
systemctl start pacemaker
```
![início pacemaker.png](media/HowToHLI/HASetupWithStonith/start-pacemaker.png)

Se o serviço de pacemaker *falhar*, consulte *cenário 5: falha do serviço de Pacemaker*

## <a name="5---joining-the-cluster"></a>5.   Associar o cluster
Esta secção descreve sobre como associar o nó ao cluster.

### <a name="51-add-the-node"></a>5.1 adicionar o nó
Execute o seguinte comando **Nó2** para permitir que Nó2 aderirem ao cluster.
```
ha-cluster-join
```
Se receber um *erro* durante a associar o cluster, consulte *cenário 6: não é possível para aderirem ao cluster do nó 2*.

## <a name="6---validating-the-cluster"></a>6.   Validar o cluster

### <a name="61-start-the-cluster-service"></a>6.1 iniciar o serviço de cluster
Para verificar e, opcionalmente, início do cluster pela primeira vez no **ambos** nós.
```
systemctl status pacemaker
systemctl start pacemaker
```
![Estado de systemctl-pacemaker.png](media/HowToHLI/HASetupWithStonith/systemctl-status-pacemaker.png)
### <a name="62-monitor-the-status"></a>6.2 monitorizar o Estado
Execute o comando *crm_mon* para garantir **ambos** os nós estão online. Pode executá-la no **qualquer um de nós** do cluster
```
crm_mon
```
![CRM-mon.png](media/HowToHLI/HASetupWithStonith/crm-mon.png) pode também iniciar sessão hawk para verificar o estado de cluster *https://<node IP>: 7630*. O utilizador predefinido é hacluster e a palavra-passe é linux. Se necessário, pode alterar a palavra-passe utilizando *passwd* comando.

## <a name="7-configure-cluster-properties-and-resources"></a>7. Configurar propriedades de Cluster e recursos 
Esta secção descreve os passos para configurar os recursos de cluster.
Neste exemplo, configuramos o seguinte recurso, o resto pode ser configurado (se necessário) ao consultar o guia SUSE HA. Executar a configuração no **um de nós** apenas. Fazer no nó principal.

- O arranque de configuração do cluster
- Dispositivo STONITH
- O endereço Virtual IP


### <a name="71-cluster-bootstrap-and-more"></a>7.1 arranque de cluster e muito mais
Adicione o arranque de configuração do cluster. Criar o ficheiro e adicione o texto da seguinte forma:
```
sapprdhdb95:~ # vi crm-bs.txt
# enter the following to crm-bs.txt
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
```
Adicione a configuração para o cluster.
```
crm configure load update crm-bs.txt
```
![crmbs.png CRM configurar](media/HowToHLI/HASetupWithStonith/crm-configure-crmbs.png)

### <a name="72-stonith-device"></a>7.2 dispositivo STONITH
Adicione recurso STONITH. Criar o ficheiro e adicione o texto da seguinte forma.
```
# vi crm-sbd.txt
# enter the following to crm-sbd.txt
primitive stonith-sbd stonith:external/sbd \
params pcmk_delay_max="15" \
op monitor interval="15" timeout="15"
```
Adicione a configuração para o cluster.
```
crm configure load update crm-sbd.txt
```

### <a name="73-the-virtual-ip-address"></a>7.3 o endereço IP virtual
Adicione o recurso IP virtual. Criar o ficheiro e adicione o texto, tal como indicado abaixo.
```
# vi crm-vip.txt
primitive rsc_ip_HA1_HDB10 ocf:heartbeat:IPaddr2 \
operations $id="rsc_ip_HA1_HDB10-operations" \
op monitor interval="10s" timeout="20s" \
params ip="10.35.0.197"
```
Adicione a configuração para o cluster.
```
crm configure load update crm-vip.txt
```

### <a name="74-validate-the-resources"></a>7.4 validar os recursos

Quando executa o comando *crm_mon*, pode ver os dois recursos existe.
![crm_mon_command.png](media/HowToHLI/HASetupWithStonith/crm_mon_command.png)

Além disso, pode ver o estado em *https://<node IP address>: 7630/cib/live/Estado*

![Estado de hawlk-page.png](media/HowToHLI/HASetupWithStonith/hawlk-status-page.png)

## <a name="8-testing-the-failover-process"></a>8. O processo de ativação pós-falha de teste
Para testar o processo de ativação pós-falha, pare o serviço de pacemaker no Nó1 e a ativação pós-falha de recursos para Nó2.
```
Service pacemaker stop
```
Agora, pare o serviço de pacemaker no **Nó2** e recursos para ativação pós-falha **Nó1**

**Antes da ativação pós-falha**
![antes failover.png](media/HowToHLI/HASetupWithStonith/Before-failover.png)
**após a ativação pós-falha**
![após failover.png](media/HowToHLI/HASetupWithStonith/after-failover.png)
 ![ CRM mon após failover.png](media/HowToHLI/HASetupWithStonith/crm-mon-after-failover.png)


## <a name="9-troubleshooting"></a>9. Resolução de problemas
Esta secção descreve alguns cenários de falha, que podem ser encontrados durante a configuração. Poderá não necessariamente enfrentam estes problemas.

### <a name="scenario-1-cluster-node-not-online"></a>Cenário 1: Cluster de nó não online
Se qualquer um de nós não for apresentada online no Gestor de clusters, pode experimentar a seguir para colocá-lo online.

Iniciar o serviço iSCSI
```
service iscsid start
```

E agora, deve conseguir iniciar sessão para esse nó iSCSI
```
iscsiadm -m node -l
```
O resultado esperado parece seguinte
```
sapprdhdb45:~ # iscsiadm -m node -l
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.11,3260] (multiple)
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.12,3260] (multiple)
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.22,3260] (multiple)
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.21,3260] (multiple)
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.11,3260] successful.
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.12,3260] successful.
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.22,3260] successful.
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.21,3260] successful.
```
### <a name="scenario-2-yast2-does-not-show-graphical-view"></a>Cenário 2: yast2 não mostrar vista gráfica
Utilizámos o ecrã yast2 gráficas para configurar o cluster de elevada disponibilidade neste documento. Se yast2 não abrir com a janela de gráfica, conforme mostrado e throw erro Qt, efetue os passos seguintes. Se abre-se com a janela de gráfica, pode ignorar os passos.

**Erro**

![yast2-qt-gui-error.png](media/HowToHLI/HASetupWithStonith/yast2-qt-gui-error.png)

**Resultado esperado**

![controlo de yast-center.png](media/HowToHLI/HASetupWithStonith/yast-control-center.png)

Se o yast2 não for aberto com a vista gráfica, siga os seguintes passos.

Instale os pacotes necessários. Tem de ter sessão iniciada como utilizador "raiz" e ter SMT configurado para transferência/instalar os pacotes.

Para instalar os pacotes, utilize yast > Software > Gestão de Software > dependências > opção "Instalação recomendada pacotes...". Captura de ecrã seguinte ilustra os ecrãs esperados.
>[!NOTE]
>Tem de efetuar os passos em ambos os nós, para que possam aceder a vista gráfica yast2 de ambos os nós.

![yast sofwaremanagement.png](media/HowToHLI/HASetupWithStonith/yast-sofwaremanagement.png)

Em dependências, selecione "Instalar recomendado Packages" ![yast dependencies.png](media/HowToHLI/HASetupWithStonith/yast-dependencies.png)

Reveja as alterações e clique em OK

![yast](media/HowToHLI/HASetupWithStonith/yast-automatic-changes.png)

Pacote de instalação continua ![installation.png yast efetuar](media/HowToHLI/HASetupWithStonith/yast-performing-installation.png)

Clique em seguinte

![yast-instalação-report.png](media/HowToHLI/HASetupWithStonith/yast-installation-report.png)

Clique em Concluir

Também terá de instalar os pacotes libqt4 e libyui qt.
```
zypper -n install libqt4
```
![zypper-install-libqt4.png](media/HowToHLI/HASetupWithStonith/zypper-install-libqt4.png)
```
zypper -n install libyui-qt
```
![zypper-install-ligyui.png](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui.png)
![zypper-install-ligyui_part2.png](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui_part2.png) Yast2 deve ser capaz de abrir a vista gráfica agora como mostrados aqui.
![controlo de yast2-center.png](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

### <a name="scenario-3-yast2-does-not-high-availability-option"></a>Cenário 3: yast2 não opção de elevada disponibilidade
Para a opção de elevada disponibilidade para estar visível no Centro de controlo de yast2, terá de instalar os pacotes adicionais.

Utilizar Yast2 > Software > Gestão de Software > selecione os seguintes padrões

- Servidor de SAP HANA base
- Ferramentas e compilador de C/C++
- Elevada disponibilidade
- Servidor de aplicações SAP base

O ecrã seguinte mostra os passos para instalar os padrões.

Utilizar yast2 > Software > Gestão de Software

![controlo de yast2-center.png](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

Selecione os padrões

![yast pattern1.png](media/HowToHLI/HASetupWithStonith/yast-pattern1.png)
![yast pattern2.png](media/HowToHLI/HASetupWithStonith/yast-pattern2.png)

Clique em **aceitar**

![packages.png yast alterado](media/HowToHLI/HASetupWithStonith/yast-changed-packages.png)

Clique em **continuar**

![installation.png yast2 efetuar](media/HowToHLI/HASetupWithStonith/yast2-performing-installation.png)

Clique em **seguinte** quando a instalação estiver concluída

![yast2-instalação-report.png](media/HowToHLI/HASetupWithStonith/yast2-installation-report.png)

### <a name="scenario-4-hana-installation-fails-with-gcc-assemblies-error"></a>Cenário 4: Instalação HANA falha com o erro de assemblagens do gcc
A instalação de HANA falha com o erro seguinte.

![Hana-instalação-error.png](media/HowToHLI/HASetupWithStonith/Hana-installation-error.png)

Para corrigir o problema, tem de instalar bibliotecas (libgcc_sl e libstdc + + 6) como seguinte.

![zypper-install-lib.png](media/HowToHLI/HASetupWithStonith/zypper-install-lib.png)

### <a name="scenario-5-pacemaker-service-fails"></a>Cenário 5: Falha de serviço Pacemaker

Ocorreu o seguinte problema durante o início do serviço pacemaker.

```
sapprdhdb95:/ # systemctl start pacemaker
A dependency job for pacemaker.service failed. See 'journalctl -xn' for details.
```
```
sapprdhdb95:/ # journalctl -xn
-- Logs begin at Thu 2017-09-28 09:28:14 EDT, end at Thu 2017-09-28 21:48:27 EDT. --
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync configuration map
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync configuration ser
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync cluster closed pr
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync cluster quorum se
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync profile loading s
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [MAIN  ] Corosync Cluster Engine exiting normally
Sep 28 21:48:27 sapprdhdb95 systemd[1]: Dependency failed for Pacemaker High Availability Cluster Manager
-- Subject: Unit pacemaker.service has failed
-- Defined-By: systemd
-- Support: http://lists.freedesktop.org/mailman/listinfo/systemd-devel
--
-- Unit pacemaker.service has failed.
--
-- The result is dependency.
```
```
sapprdhdb95:/ # tail -f /var/log/messages
2017-09-28T18:44:29.675814-04:00 sapprdhdb95 corosync[57600]:   [QB    ] withdrawing server sockets
2017-09-28T18:44:29.676023-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync cluster closed process group service v1.01
2017-09-28T18:44:29.725885-04:00 sapprdhdb95 corosync[57600]:   [QB    ] withdrawing server sockets
2017-09-28T18:44:29.726069-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync cluster quorum service v0.1
2017-09-28T18:44:29.726164-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync profile loading service
2017-09-28T18:44:29.776349-04:00 sapprdhdb95 corosync[57600]:   [MAIN  ] Corosync Cluster Engine exiting normally
2017-09-28T18:44:29.778177-04:00 sapprdhdb95 systemd[1]: Dependency failed for Pacemaker High Availability Cluster Manager.
2017-09-28T18:44:40.141030-04:00 sapprdhdb95 systemd[1]: [/usr/lib/systemd/system/fstrim.timer:8] Unknown lvalue 'Persistent' in section 'Timer'
2017-09-28T18:45:01.275038-04:00 sapprdhdb95 cron[57995]: pam_unix(crond:session): session opened for user root by (uid=0)
2017-09-28T18:45:01.308066-04:00 sapprdhdb95 CRON[57995]: pam_unix(crond:session): session closed for user root
```

Para o corrigir, elimine a seguinte linha do ficheiro */usr/lib/systemd/system/fstrim.timer*

```
Persistent=true
```

![Persistent.png](media/HowToHLI/HASetupWithStonith/Persistent.png)

### <a name="scenario-6-node-2-unable-to-join-the-cluster"></a>Cenário 6: Nó 2 não é possível para aderirem ao cluster

Quando associar o Nó2 ao existente de cluster utilizando *ha cluster-associação* comando, o seguinte erro ocorreu.

```
ERROR: Can’t retrieve SSH keys from <Primary Node>
```

![ha-cluster-associação-error.png](media/HowToHLI/HASetupWithStonith/ha-cluster-join-error.png)

Para corrigir, execute o seguinte em ambos os nós

```
ssh-keygen -q -f /root/.ssh/id_rsa -C 'Cluster Internal' -N ''
cat /root/.ssh/id_rsa.pub >> /root/.ssh/authorized_keys
```

![SSH-keygen-Nó1. PNG](media/HowToHLI/HASetupWithStonith/ssh-keygen-node1.PNG)

![SSH-keygen-Nó2. PNG](media/HowToHLI/HASetupWithStonith/ssh-keygen-node2.PNG)

Após a correção anterior, Nó2 devem obter adicionados ao cluster

![ha-cluster-associação-fix.png](media/HowToHLI/HASetupWithStonith/ha-cluster-join-fix.png)

## <a name="10-general-documentation"></a>10. Documentação geral
Pode encontrar mais informações sobre o SUSE HA configurar nos seguintes artigos: 

- [SAP HANA SR desempenho otimizado do cenário](https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf )
- [Com base em armazenamento fencing](https://www.suse.com/documentation/sle-ha-2/book_sleha/data/sec_ha_storage_protect_fencing.html)
- [Blogue - utilizando o Cluster de Pacemaker para SAP HANA - parte 1](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-1-basics/)
- [Blogue - utilizando o Cluster de Pacemaker para SAP HANA partes 2](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-2-failure-of-both-nodes/)