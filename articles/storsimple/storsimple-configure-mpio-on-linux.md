---
title: "Configurar o MPIO num anfitrião Linux do StorSimple | Microsoft Docs"
description: "Configurar o MPIO no StorSimple ligado ao anfitrião Linux em execução CentOS 6.6"
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: tysonn
ms.assetid: ca289eed-12b7-4e2e-9117-adf7e2034f2f
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/01/2016
ms.author: alkohli
ms.openlocfilehash: add539351066f9ff94febeebfd5334773b360e8f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-mpio-on-a-storsimple-host-running-centos"></a>Configurar o MPIO num anfitrião StorSimple com CentOS
Este artigo explica os passos necessários para configurar Multipathing e/s (MPIO) no seu servidor de anfitrião do Centos 6.6. O servidor anfitrião está ligado ao dispositivo Microsoft Azure StorSimple para elevada disponibilidade através de iniciadores iSCSI. Descreve em detalhe a deteção automática de dispositivos multipath e o programa de configuração específico apenas para os volumes do StorSimple.

Este procedimento é aplicável a todos os modelos de dispositivos de série 8000 do StorSimple.

> [!NOTE]
> Este procedimento não pode ser utilizado para um dispositivo virtual StorSimple. Para obter mais informações, consulte como configurar os servidores de anfitrião para o dispositivo virtual.
> 
> 

## <a name="about-multipathing"></a>Sobre multipathing
A funcionalidade de multipathing permite-lhe configurar vários caminhos de e/s entre um servidor de anfitrião e um dispositivo de armazenamento. Estes caminhos de e/s são físicas ligações de SAN que podem incluir cabos separados, comutadores, interfaces de rede e controladores. Multipathing agrega os caminhos de e/s, para configurar um novo dispositivo que está associado a todos os caminhos agregada.

O objetivo de multipathing é dois-subconjuntos de validação:

* **Elevada disponibilidade**: Fornece um caminho alternativo se falhar qualquer elemento do caminho e/s (por exemplo, um cabo, comutador, interface de rede ou controlador).
* **Balanceamento de carga**: dependendo da configuração do seu dispositivo de armazenamento, pode melhorar o desempenho ao detetar cargas nos caminhos de e/s e dinamicamente reequilíbrio essas cargas.

### <a name="about-multipathing-components"></a>Sobre os componentes de multipathing
Multipathing no Linux é constituído por componentes de kernel e componentes de espaço do utilizador como apresentadas abaixo.

* **Kernel**: O componente principal é o *mapeador de dispositivo* que reroutes e/s e suporta a ativação pós-falha para os caminhos e os grupos de caminho.

* **Espaço de utilizador**: estes são *multipath ferramentas* que gerir dispositivos de multipathed ao que dá instruções o módulo de multipath mapeador de dispositivo para o que fazer. As ferramentas é composto por:
   
   * **Multipath**: lista e configura os dispositivos multipathed.
   * **Multipathd**: daemon que executa o multipath e monitoriza os caminhos.
   * **Nome de Devmap**: Fornece um nome de dispositivo significativo para udev para devmaps.
   * **Kpartx**: mapeia linear devmaps para partições de dispositivo para tornar o multipath maps partições.
   * **Multipath.Conf**: ficheiro de configuração para o daemon de multipath que é utilizado para substituir a tabela de configuração incorporada.

### <a name="about-the-multipathconf-configuration-file"></a>Sobre o ficheiro de configuração multipath.conf
O ficheiro de configuração `/etc/multipath.conf` efetua muitas das funcionalidades de multipathing configurável pelo utilizador. O `multipath` comando e o daemon de kernel `multipathd` utilizar as informações localizadas neste ficheiro. O ficheiro é consulted apenas durante a configuração de dispositivos multipath. Certifique-se de que todas as alterações são efetuadas antes de executar o `multipath` comando. Se modificar o ficheiro posteriormente, terá de parar e iniciar multipathd novamente para que as alterações surtam efeito.

O multipath.conf tem cinco secções:

- **Predefinições ao nível do sistema** *(por predefinição)*: pode substituir as predefinições ao nível do sistema.
- **Blacklisted dispositivos** *(blacklist)*: pode especificar a lista de dispositivos que não deve ser controlada pelo mapeador de dispositivo.
- **Lista de proibições exceções** *(blacklist_exceptions)*: pode identificar dispositivos específicos deve ser tratado como dispositivos multipath, mesmo se listados no bloqueados.
- **As definições específicas do controlador de armazenamento** *(dispositivos)*: pode especificar definições de configuração que serão aplicadas a dispositivos que tem informações de fabricante e produto.
- **As definições específicas do dispositivo** *(multipaths)*: pode utilizar esta secção para otimizar as definições de configuração para os LUNs individuais.

## <a name="configure-multipathing-on-storsimple-connected-to-linux-host"></a>Configurar multipathing no StorSimple ligado ao anfitrião Linux
Um dispositivo StorSimple ligado ao anfitrião Linux pode ser configurado para balanceamento de carga e de elevada disponibilidade. Por exemplo, se o anfitrião do Linux tem duas interfaces de rede ligadas à SAN e o dispositivo tem duas interfaces de rede ligadas à SAN, de modo a que estas interfaces estão na mesma sub-rede, em seguida, existirá 4 caminhos disponível. No entanto, se cada interface de dados na interface de dispositivo e o anfitrião estiver numa sub-rede IP diferentes (e não encaminhável), em seguida, apenas 2 caminhos estará disponíveis. Pode configurar multipathing automaticamente detetar todos os caminhos disponíveis, escolher um algoritmo de balanceamento de carga para esses caminhos, aplicar definições de configuração específicos para volumes de só de StorSimple e, em seguida, ative e certifique-se de multipathing.

O procedimento seguinte descreve como configurar multipathing quando um dispositivo StorSimple com duas interfaces de rede está ligado a um anfitrião com duas interfaces de rede.

## <a name="prerequisites"></a>Pré-requisitos
Esta secção fornece detalhes sobre os pré-requisitos de configuração para o servidor do CentOS e o dispositivo StorSimple.

### <a name="on-centos-host"></a>No anfitrião do CentOS
1. Certifique-se de que o anfitrião do CentOS tem 2 interfaces de rede ativadas. Escreva:
   
    `ifconfig`
   
    O exemplo seguinte mostra a saída quando duas interfaces de rede (`eth0` e `eth1`) estão presentes no anfitrião.
   
        [root@centosSS ~]# ifconfig
        eth0  Link encap:Ethernet  HWaddr 00:15:5D:A2:33:41  
          inet addr:10.126.162.65  Bcast:10.126.163.255  Mask:255.255.252.0
          inet6 addr: 2001:4898:4010:3012:215:5dff:fea2:3341/64 Scope:Global
          inet6 addr: fe80::215:5dff:fea2:3341/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
         RX packets:36536 errors:0 dropped:0 overruns:0 frame:0
          TX packets:6312 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:13994127 (13.3 MiB)  TX bytes:645654 (630.5 KiB)
   
        eth1  Link encap:Ethernet  HWaddr 00:15:5D:A2:33:42  
          inet addr:10.126.162.66  Bcast:10.126.163.255  Mask:255.255.252.0
          inet6 addr: 2001:4898:4010:3012:215:5dff:fea2:3342/64 Scope:Global
          inet6 addr: fe80::215:5dff:fea2:3342/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:25962 errors:0 dropped:0 overruns:0 frame:0
          TX packets:11 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:2597350 (2.4 MiB)  TX bytes:754 (754.0 b)
   
        loLink encap:Local Loopback  
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:12 errors:0 dropped:0 overruns:0 frame:0
          TX packets:12 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0
          RX bytes:720 (720.0 b)  TX bytes:720 (720.0 b)
2. Instalar *utils de iniciador de iSCSI* no seu servidor CentOS. Execute os seguintes passos para instalar *utils de iniciador de iSCSI*.
   
   1. Inicie sessão como `root` no seu anfitrião CentOS.
   2. Instalar o *utils de iniciador de iSCSI*. Escreva:
      
       `yum install iscsi-initiator-utils`
   3. Depois do *utils de iniciador de iSCSI* com êxito é instalado, inicie o serviço de iSCSI. Escreva:
      
       `service iscsid start`
      
       Em ocasiões, `iscsid` , na verdade, não poderá iniciar e o `--force` opção pode ser necessária
   4. Para garantir que o iniciador iSCSI está ativado durante o tempo de arranque, utilize o `chkconfig` comando para ativar o serviço.
      
       `chkconfig iscsi on`
   5. Para verificar que a configuração foi corretamente, execute o comando:
      
       `chkconfig --list | grep iscsi`
      
       É apresentada abaixo uma saída de exemplo.
      
           iscsi   0:off   1:off   2:on3:on4:on5:on6:off
           iscsid  0:off   1:off   2:on3:on4:on5:on6:off
      
       O exemplo acima, pode ver que o seu ambiente de iSCSI será executada no momento do arranque em execução níveis 2, 3, 4 e 5.
3. Instalar *multipath de mapeador de dispositivo*. Escreva:
   
    `yum install device-mapper-multipath`
   
    A instalação será iniciada. Tipo **Y** continue após a um pedido de confirmação.

### <a name="on-storsimple-device"></a>No dispositivo StorSimple
Deve ter o dispositivo StorSimple:

* Um mínimo de duas interfaces ativadas para iSCSI. Para verificar que duas interfaces de rede estão preparados para iSCSI no dispositivo StorSimple, execute os seguintes passos no portal clássico do Azure para o dispositivo StorSimple:
  
  1. Inicie sessão no portal clássico para o dispositivo StorSimple.
  2. Selecione o serviço StorSimple Manager, clique em **dispositivos** e selecione o dispositivo StorSimple específico. Clique em **configurar** e verifique as definições de interface de rede. Uma captura de ecrã com duas interfaces de rede preparada para iSCSI é mostrada abaixo. Aqui dados 2 e dados 3, ambos os 10 GbE interfaces estão ativadas para iSCSI.
     
      ![Configuração do MPIO StorsSimple dados 2](./media/storsimple-configure-mpio-on-linux/IC761347.png)
     
      ![MPIO StorSimple dados 3 configuração](./media/storsimple-configure-mpio-on-linux/IC761348.png)
     
      No **configurar** página
     
     1. Certifique-se de que ambas as interfaces de rede estão preparados para iSCSI. O **iSCSI ativado** campo deve ser definido como **Sim**.
     2. Certifique-se de que as interfaces de rede têm a mesma velocidade, ambos devem ser de 1 GbE ou de 10 GbE.
     3. Tenha em atenção os endereços IPv4 das interfaces preparada para iSCSI e guardar para utilização posterior no anfitrião.
* As interfaces do iSCSI no dispositivo StorSimple devem ser acessíveis a partir do servidor do CentOS.
      Para verificar isto, tem de fornecer os endereços IP das interfaces de rede com o iSCSI da StorSimple no seu servidor de anfitrião. Os comandos utilizados e o resultado correspondente com DATA2 (10.126.162.25) e DATA3 (10.126.162.26) é mostrado abaixo:
  
        [root@centosSS ~]# iscsiadm -m discovery -t sendtargets -p 10.126.162.25:3260
        10.126.162.25:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g44mt-target
        10.126.162.26:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g44mt-target

### <a name="hardware-configuration"></a>Configuração de hardware
Recomendamos que o se ligar duas interfaces de rede do iSCSI no caminhos separados para redundância. A figura abaixo mostra a configuração de hardware recomendadas para elevada disponibilidade e multipathing balanceamento de carga para o servidor de CentOS e dispositivo StorSimple.

![Configuração de hardware do MPIO para StorSimple para o anfitrião Linux](./media/storsimple-configure-mpio-on-linux/MPIOHardwareConfigurationStorSimpleToLinuxHost2M.png)

Conforme mostrado na figura anterior:

* O dispositivo StorSimple é uma configuração de ativo-passivo com dois controladores.
* Dois comutadores de SAN estão ligados aos controladores de dispositivo.
* Dois iniciadores iSCSI estiverem ativados no dispositivo StorSimple.
* Duas interfaces de rede estão ativadas no anfitrião do CentOS.

A configuração de acima irá produzir 4 caminhos separados entre o dispositivo e o anfitrião se as interfaces de anfitrião e os dados são encaminháveis.

> [!IMPORTANT]
> * Recomendamos que não misture de 1 GbE e interfaces de rede GbE 10 para multipathing. Quando utilizar duas interfaces de rede, ambas as interfaces devem ser do tipo idêntico.
> * No dispositivo StorSimple, DATA0, DATA1, DATA4 e DATA5 são interfaces do 1 GbE enquanto DATA2 DATA3, sendo as interfaces de rede de 10 GbE. |
> 
> 

## <a name="configuration-steps"></a>Passos de configuração
Os passos de configuração para multipathing envolvem configurar os caminhos disponíveis para a deteção automática, especificar o algoritmo de balanceamento de carga a utilizar, permitindo multipathing e, finalmente, verificar a configuração. Cada um destes passos é abordada em detalhe nas secções seguintes.

### <a name="step-1-configure-multipathing-for-automatic-discovery"></a>Passo 1: Configurar multipathing para a deteção automática
Os dispositivos suportados pelo multipath podem ser detetados e automaticamente configurados.

1. Inicializar `/etc/multipath.conf` ficheiro. Escreva:
   
     `mpathconf --enable`
   
    O comando acima irá criar um `sample/etc/multipath.conf` ficheiro.
2. Inicie o serviço multipath. Escreva:
   
    `service multipathd start`
   
    Verá a seguinte saída:
   
    `Starting multipathd daemon:`
3. Ative a deteção automática de multipaths. Escreva:
   
    `mpathconf --find_multipaths y`
   
    Isto irá modificar a secção de predefinições do seu `multipath.conf` conforme mostrado abaixo:
   
        defaults {
        find_multipaths yes
        user_friendly_names yes
        path_grouping_policy multibus
        }

### <a name="step-2-configure-multipathing-for-storsimple-volumes"></a>Passo 2: Configurar multipathing para volumes do StorSimple
Por predefinição, todos os dispositivos são preto listado no ficheiro multipath.conf e irão ser ignorados. Terá de criar blacklist exceções para permitir multipathing de volumes a partir de dispositivos StorSimple.

1. Editar o `/etc/mulitpath.conf` ficheiro. Escreva:
   
    `vi /etc/multipath.conf`
2. Localize a secção de blacklist_exceptions no ficheiro multipath.conf. O dispositivo StorSimple tem de estar listado como uma exceção de blacklist nesta secção. Pode anule os comentários linhas relevantes neste ficheiro de modificá-lo como mostrado abaixo (utilize o modelo específico do dispositivo que está a utilizar):
   
        blacklist_exceptions {
            device {
                       vendor  "MSFT"
                       product "STORSIMPLE 8100*"
            }
            device {
                       vendor  "MSFT"
                       product "STORSIMPLE 8600*"
            }
           }

### <a name="step-3-configure-round-robin-multipathing"></a>Passo 3: Configurar multipathing round robin
Este algoritmo de balanceamento de carga utiliza todos os multipaths disponíveis ao controlador ativo de forma equilibrada, round robin.

1. Editar o `/etc/multipath.conf` ficheiro. Escreva:
   
    `vi /etc/multipath.conf`
2. Sob o `defaults` secção, defina o `path_grouping_policy` para `multibus`. O `path_grouping_policy` Especifica o caminho predefinido da política a aplicar ao multipaths não especificados de agrupamento. A secção de predefinições procurará conforme mostrado abaixo.
   
        defaults {
                user_friendly_names yes
                path_grouping_policy multibus
        }

> [!NOTE]
> Os valores mais comuns de `path_grouping_policy` incluem:
> 
> * ativação pós-falha = 1 caminho por grupo de prioridade
> * multibus = todos os caminhos válidos no grupo de 1 prioridade
> 
> 

### <a name="step-4-enable-multipathing"></a>Passo 4: Ativar multipathing
1. Reinicie o `multipathd` daemon. Escreva:
   
    `service multipathd restart`
2. O resultado será conforme mostrado abaixo:
   
        [root@centosSS ~]# service multipathd start
        Starting multipathd daemon:  [OK]

### <a name="step-5-verify-multipathing"></a>Passo 5: Verificar multipathing
1. Primeiro certifique-se de que o iSCSI é estabelecida ligação com o dispositivo StorSimple da seguinte forma:
   
   a. Detete o dispositivo StorSimple. Escreva:
      
    ```
    iscsiadm -m discovery -t sendtargets -p  <IP address of network interface on the device>:<iSCSI port on StorSimple device>
    ```
    
    O resultado quando o endereço IP para DATA0 é 10.126.162.25 e porta 3260 é aberta no dispositivo StorSimple para tráfego de saída iSCSI é conforme mostrado abaixo:
    
    ```
    10.126.162.25:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target
    10.126.162.26:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target
    ```

    Copie o IQN do dispositivo StorSimple, `iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target`, da saída anterior.

   b. Ligar ao dispositivo utilizar o IQN de destino. O dispositivo StorSimple é o destino de iSCSI aqui. Escreva:

    ```
    iscsiadm -m node --login -T <IQN of iSCSI target>
    ```

    O exemplo seguinte mostra a saída com um destino IQN do `iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target`. O resultado indica que que tenha ligado com êxito ao duas interfaces de rede com o iSCSI no seu dispositivo.

    ```
    Logging in to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] (multiple)
    Logging in to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] (multiple)
    Logging in to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] (multiple)
    Logging in to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] (multiple)
    Login to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] successful.
    Login to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] successful.
    Login to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] successful.
    Login to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] successful.
    ```

    Se vir a interface de anfitrião apenas um e dois caminhos aqui, em seguida, tem de ativar ambas as interfaces no anfitrião para iSCSI. Pode seguir o [instruções na documentação de Linux detalhadas](https://access.redhat.com/documentation/Red_Hat_Enterprise_Linux/5/html/Online_Storage_Reconfiguration_Guide/iscsioffloadmain.html).

2. Um volume está exposto ao servidor do CentOS do dispositivo StorSimple. Para obter mais informações, consulte [passo 6: criar um volume](storsimple-deployment-walkthrough.md#step-6-create-a-volume) através do portal clássico do Azure no dispositivo StorSimple.

3. Certifique-se os caminhos disponíveis. Escreva:

      ```
      multipath –l
      ```

      O exemplo seguinte mostra a saída para duas interfaces de rede num dispositivo StorSimple ligado a uma interface de rede de anfitrião único com dois caminhos disponíveis.

        ```
        mpathb (36486fd20cc081f8dcd3fccb992d45a68) dm-3 MSFT,STORSIMPLE 8100
        size=100G features='0' hwhandler='0' wp=rw
        `-+- policy='round-robin 0' prio=0 status=active
        |- 7:0:0:1 sdc 8:32 active undef running
        `- 6:0:0:1 sdd 8:48 active undef running
        ```

        The following example shows the output for two network interfaces on a StorSimple device connected to two host network interfaces with four available paths.

        ```
        mpathb (36486fd27a23feba1b096226f11420f6b) dm-2 MSFT,STORSIMPLE 8100
        size=100G features='0' hwhandler='0' wp=rw
        `-+- policy='round-robin 0' prio=0 status=active
        |- 17:0:0:0 sdb 8:16 active undef running
        |- 15:0:0:0 sdd 8:48 active undef running
        |- 14:0:0:0 sdc 8:32 active undef running
        `- 16:0:0:0 sde 8:64 active undef running
        ```

        After the paths are configured, refer to the specific instructions on your host operating system (Centos 6.6) to mount and format this volume.

## <a name="troubleshoot-multipathing"></a>Resolver problemas de multipathing
Esta secção fornece algumas sugestões úteis caso se depare com problemas durante a configuração de multipathing.

Q. Não posso ver as alterações no `multipath.conf` ficheiros a colocar em vigor.

A. Se tiver efetuado alterações para o `multipath.conf` ficheiros, terá de reiniciar o serviço de multipathing. Escreva o seguinte comando:

    service multipathd restart

Q. Posso ter ativada a duas interfaces de rede no dispositivo StorSimple e duas interfaces de rede no anfitrião. Quando posso listar os caminhos disponíveis, posso ver apenas dois caminhos. Deve ver quatro caminhos disponíveis.

A. Certifique-se de que os dois caminhos estão na mesma sub-rede e encaminhável. Se as interfaces de rede estão em diferentes vLANs e não encaminhável, verá apenas dois caminhos. Uma forma de verificar esta situação é certificar-se de que pode chegar ambas as interfaces de anfitrião a partir de uma interface de rede no dispositivo StorSimple. Terá [contacte a Microsoft Support](storsimple-contact-microsoft-support.md) como esta verificação só pode ser feita através de uma sessão de suporte.

Q. Quando posso listar os caminhos disponíveis, não vejo qualquer saída.

A. Normalmente, não existem quaisquer caminhos multipathed sugere um problema com o daemon de multipathing e é provável que qualquer problema aqui reside no `multipath.conf` ficheiro.

Seria também vale a verificar se pode ver, na verdade, alguns discos depois de ligar para o destino, conforme sem resposta das listagens multipath também pode significar que não tem quaisquer discos.

* Utilize o seguinte comando para voltar a analisar o barramento SCSI:
  
    `$ rescan-scsi-bus.sh `(parte do pacote de sg3_utils)
* Escreva os seguintes comandos:
  
    `$ dmesg | grep sd*`
     
     Ou
  
    `$ fdisk –l`
  
    Estes irão devolver detalhes dos discos adicionados recentemente.
* Para determinar se deve ser um disco do StorSimple, utilize os seguintes comandos:
  
    `cat /sys/block/<DISK>/device/model`
  
    Esta ação irá devolver uma cadeia, o que irá determinar se é um disco do StorSimple.

A menos provável, mas é possível causa também pode ser iscsid obsoleto pid. Utilize o seguinte comando para terminar a sessão das sessões iSCSI:

    iscsiadm -m node --logout -p <Target_IP>

Repita este comando para todas as interfaces de rede ligada do destino iSCSI, o que é o dispositivo StorSimple. Depois de ter a sessão terminada de todas as sessões de iSCSI, utilize o destino de iSCSI IQN para restabelecer a sessão de iSCSI. Escreva o seguinte comando:

    iscsiadm -m node --login -T <TARGET_IQN>


Q. Não sou tem a certeza se o dispositivo está na lista de permissões.

A. Para verificar se o dispositivo está na lista de permissões, utilize o seguinte comando interativo resolução de problemas:

    multipathd –k
    multipathd> show devices
    available block devices:
    ram0 devnode blacklisted, unmonitored
    ram1 devnode blacklisted, unmonitored
    ram2 devnode blacklisted, unmonitored
    ram3 devnode blacklisted, unmonitored
    ram4 devnode blacklisted, unmonitored
    ram5 devnode blacklisted, unmonitored
    ram6 devnode blacklisted, unmonitored
    ram7 devnode blacklisted, unmonitored
    ram8 devnode blacklisted, unmonitored
    ram9 devnode blacklisted, unmonitored
    ram10 devnode blacklisted, unmonitored
    ram11 devnode blacklisted, unmonitored
    ram12 devnode blacklisted, unmonitored
    ram13 devnode blacklisted, unmonitored
    ram14 devnode blacklisted, unmonitored
    ram15 devnode blacklisted, unmonitored
    loop0 devnode blacklisted, unmonitored
    loop1 devnode blacklisted, unmonitored
    loop2 devnode blacklisted, unmonitored
    loop3 devnode blacklisted, unmonitored
    loop4 devnode blacklisted, unmonitored
    loop5 devnode blacklisted, unmonitored
    loop6 devnode blacklisted, unmonitored
    loop7 devnode blacklisted, unmonitored
    sr0 devnode blacklisted, unmonitored
    sda devnode whitelisted, monitored
    dm-0 devnode blacklisted, unmonitored
    dm-1 devnode blacklisted, unmonitored
    dm-2 devnode blacklisted, unmonitored
    sdb devnode whitelisted, monitored
    sdc devnode whitelisted, monitored
    dm-3 devnode blacklisted, unmonitored


Para obter mais informações, aceda a [utilizar a resolução de problemas de comando interativo para multipathing](http://www.centos.org/docs/5/html/5.1/DM_Multipath/multipath_config_confirm.html).

## <a name="list-of-useful-commands"></a>Lista de comandos útil
| Tipo | Comando | Descrição |
| --- | --- | --- |
| **iSCSI** |`service iscsid start` |Iniciar o serviço iSCSI |
| &nbsp; |`service iscsid stop` |Parar o serviço iSCSI |
| &nbsp; |`service iscsid restart` |Reinicie o serviço iSCSI |
| &nbsp; |`iscsiadm -m discovery -t sendtargets -p <TARGET_IP>` |Detetar os destinos disponíveis no endereço especificado |
| &nbsp; |`iscsiadm -m node --login -T <TARGET_IQN>` |Inicie sessão no destino iSCSI |
| &nbsp; |`iscsiadm -m node --logout -p <Target_IP>` |Terminar sessão de destino iSCSI |
| &nbsp; |`cat /etc/iscsi/initiatorname.iscsi` |Nome do iniciador iSCSI de impressão |
| &nbsp; |`iscsiadm –m session –s <sessionid> -P 3` |Verifique o estado da sessão de iSCSI e volume detetados no anfitrião |
| &nbsp; |`iscsi –m session` |Mostra todas as sessões de iSCSI estabelecidas entre o anfitrião e o dispositivo StorSimple |
|  | | |
| **Multipathing** |`service multipathd start` |Iniciar o daemon de multipath |
| &nbsp; |`service multipathd stop` |Parar multipath daemon |
| &nbsp; |`service multipathd restart` |Reinicie o daemon de multipath |
| &nbsp; |`chkconfig multipathd on` </br> OU </br> `mpathconf –with_chkconfig y` |Ativar o daemon de multipath iniciar no momento do arranque |
| &nbsp; |`multipathd –k` |Inicie a consola interativa para resolução de problemas |
| &nbsp; |`multipath –l` |Lista de ligações multipath e dispositivos |
| &nbsp; |`mpathconf --enable` |Criar um ficheiro de exemplo mulitpath.conf no`/etc/mulitpath.conf` |
|  | | |

## <a name="next-steps"></a>Passos seguintes
Como estiver a configurar o MPIO num anfitrião Linux, também poderá ter de consultar os seguintes documentos CentoS 6.6:

* [Configurar o MPIO em CentOS](http://www.centos.org/docs/5/html/5.1/DM_Multipath/setup_procedure.html)
* [Guia de preparação do Linux](http://linux-training.be/files/books/LinuxAdm.pdf)

