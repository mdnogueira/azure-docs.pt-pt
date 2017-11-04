---
title: Instalar o dispositivo 8600 do Microsoft Azure StorSimple | Microsoft Docs
description: Descreve como descompactar, montar em bastidor e instalar o seu dispositivo StorSimple 8600 antes de implementar e configurar o software.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: 3d82ba5f-3e34-40dc-9c33-50f952bc6be8
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 10/24/2016
ms.author: alkohli
ms.openlocfilehash: 309ceba2d65c0745ba1acac698acb62526ab8078
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="unpack-rack-mount-and-cable-your-storsimple-8600-device"></a>Descompactar, montar em bastidor e instalar os cabos do dispositivo StorSimple 8600
## <a name="overview"></a>Descrição geral
Os cabos do 8600 Microsoft Azure StorSimple é um dispositivo duplo bastidor e é composta por um site primário e um bastidor EBOD. Este tutorial explica como descompactar, montar em bastidor e hardware do dispositivo de cabo do 8600 StorSimple antes de configurar o software do StorSimple.

## <a name="unpack-your-storsimple-8600-device"></a>Descompacte o dispositivo StorSimple 8600
Os passos seguintes fornecem instruções detalhadas, desmarque no descompactar o seu dispositivo de armazenamento StorSimple 8600. Este dispositivo é incluído nas dois caixas, outra para o bastidor primário e outra para o bastidor EBOD. Estas duas caixas, em seguida, são colocadas numa única caixa.

### <a name="prepare-to-unpack-your-device"></a>Preparar para descompactar o seu dispositivo
Antes de Descompacte o seu dispositivo, reveja as seguintes informações.

![Ícone de aviso](./media/storsimple-safety/IC740879.png)![ícone de ponderação pesada](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png) **aviso!**

1. Certifique-se de que tem duas pessoas disponíveis para gerir a ponderação do dispositivo se são processamento manualmente. Um bastidor totalmente configurado pode pesar até 32 kg (70 lbs.).
2. A caixa de uma superfície simples, nível local.

Em seguida, conclua os seguintes passos para descompactar o seu dispositivo.

#### <a name="to-unpack-your-device"></a>Para descompactar o seu dispositivo
1. Inspecione a caixa e foam o empacotamento crushes, cuts, máximo danos ou quaisquer outros danos óbvios. Se a caixa ou empacotamento gravemente está danificado, abre a caixa. . [Contacte a Microsoft Support](storsimple-contact-microsoft-support.md) para o ajudar a avaliar se o dispositivo está no bom trabalho ordem.
2. Abrir a caixa de externa e, em seguida, remova as duas caixas correspondente a principal e inclusões EBOD. Agora pode descompactar o servidor primário e inclusões EBOD. A figura seguinte mostra a vista descompactada de uma das caixas.
   
    ![Descompacte o seu dispositivo de armazenamento](./media/storsimple-8600-hardware-installation/HCSUnpackyour4Udevice.png)
   
    **Vista descompactada do seu dispositivo de armazenamento**
   
   | Etiqueta | Descrição |
   | --- | --- |
   |   1 |Caixa de mais |
   |   2 |Cabos SAS (no tabuleiro acessórios e cabos) |
   |   3 |Foam inferior |
   |   4 |Dispositivo |
   |   5 |Foam superior |
   |   6 |Caixa acessórios |
3. Após unpacking as duas caixas, certifique-se de que tem:
   
   * 1 inclusão principal (o bastidor primário e de inclusão EBOD são nas duas caixas separadas)
   * Inclusão EBOD 1
   * 4 cabos, 2 em cada caixa de energia
   * 2 cabos SAS (para ligar a inclusão principal ao bastidor EBOD)
   * 1 cruzado cabo de Ethernet
   * 2 cabos de consola de série
   * 1 USB de série conversor para acesso de série
   * 4 QSFP-para-SFP + adaptadores para utilização com as interfaces de rede de 10 GbE
   * 2 em Bastidor kits de montagem (o 4 rails de lado com montar hardware, 2 para o bastidor primário e de inclusão EBOD), 1 em cada caixa
   * Obter a documentação de introdução
     
     Se não recebeu qualquer um dos itens listados acima, [contacte a Microsoft Support](storsimple-contact-microsoft-support.md).  

O passo seguinte é montar em Bastidor seu dispositivo.

## <a name="rack-mount-your-storsimple-8600-device"></a>Montar em Bastidor o dispositivo StorSimple 8600
Siga os passos seguintes para instalar o seu dispositivo de armazenamento StorSimple 8600 num bastidor 19 polegadas padrão com front-end e rear publicações. Este dispositivo é fornecida com dois inclusões: um bastidor primário e um bastidor EBOD. Ambos têm de ser eletrodomésticos montados.

A instalação é composta por vários passos, cada um dos quais é explicada nos procedimentos seguintes.

> [!IMPORTANT]
> Dispositivos StorSimple tem de ser eletrodomésticos montados para um funcionamento correto.
> 
> 

### <a name="site-preparation"></a>Preparação do site
As inclusões tem de ser instaladas num bastidor 19 polegadas padrão que tenha o front-end e rear publicações. Utilize o procedimento seguinte para preparar a instalação de bastidor.

#### <a name="to-prepare-the-site-for-rack-installation"></a>Para preparar o site para a instalação de bastidor
1. Certifique-se de que o servidor primário e inclusões EBOD resting em segurança numa superfície de trabalho simples, estável e nível (ou semelhante).
2. Certifique-se de que o site onde pretende configurar tem padrão AC energia de uma origem independente ou um bastidor de unidade de distribuição de energia (PDU) com um de alimentação ininterrupta (UPS).
3. Certifique-se que ranhura de (2U de X 2) um 4U disponível no bastidor em que pretende montar as inclusões.

![Ícone de aviso](./media/storsimple-safety/IC740879.png)![ícone de ponderação pesada](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png) **aviso!**

 Certifique-se de que tem duas pessoas disponíveis para gerir a ponderação se estiver a processar a configuração do dispositivo manualmente. Um bastidor totalmente configurado pode pesar até 32 kg (70 lbs.).

### <a name="rack-prerequisites"></a>Pré-requisitos de bastidor
As inclusões foram concebidas para instalação num bastidor 19 polegadas padrão cab com:

* Profundidade mínima de polegadas 27.84 de bastidor post para o post
* Ponderação máxima de 32 kg para o dispositivo
* Pressão de back-máximo de 5 Pascal (0.5 mm máximo medidor)

### <a name="rack-mounting-rail-kit"></a>Kit de lado de montagem de bastidor
Um conjunto de montar rails será fornecido para utilização com o ficheiro CAB 19 polegadas bastidor. Os rails foi testados para lidar com a importância de inclusão máximo. Estes rails também irão permitir a instalação de múltiplas inclusões sem perda de espaço no bastidor. Instale o bastidor EBOD primeiro.

#### <a name="to-install-the-ebod-enclosure-on-the-rails"></a>Para instalar o bastidor EBOD nos rails
1. Execute este passo apenas se rails internas não estão instalados no seu dispositivo. Normalmente, os rails internas são instalados na fábrica de. Se rails não estiver instalados, instale os lado esquerdo e direito lado slides para as margens do chassis de inclusão. Se anexar utilizando seis screws métricas em cada lado. Para ajudar com orientação, slides a lado são marcados **LH – frente** e **RH – frente**, e o end que está affixed para rear da inclusão tem um fim tapered.
   
    ![Anexar slides lado chassis de inclusão](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoEnclosureChassis.png)
   
    **Anexar slides lado para as margens do bastidor**
   
   | Etiqueta | Descrição |
   | --- | --- |
   |  1 |M, 3, 4 screws head do botão |
   |  2 |Slides de chassis |
2. Anexe o lado esquerdo e assemblagens lado direito para os membros de vertical cab bastidor. Retos estão marcados **LH**, **RH**, e **este lado segurança** para ajudá-lo durante sentido correcto.
3. Localize os pins de lado de frente e rear da assemblagem lado. Expanda o lado para caber entre as mensagens de bastidor e inserir os pins de front-end e holes de membro vertical rear bastidor post. Lembre-se de que a assemblagem lado é nível.
4. Proteger a assemblagem lado bastidor membros verticais utilizando dois as métricas screws fornecidos. Utilize um screw o front-end e um o rear.
5. Repita estes passos para a assemblagem de lado.
   
     ![Anexar slides lado bastidor Cab](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoRackCabinet.png)
   
    **Anexar assemblagens lado bastidor**
   
   | Etiqueta | Descrição |
   | --- | --- |
   |   1 |Clamping screw |
   |   2 |Bastidor quadrada hole de front-post screw |
   |   3 |À esquerda pins de localização de lado de front- |
   |   4 |Clamping screw |
   |   5 |Pins de localização do lado esquerdo de rear |

### <a name="mounting-the-ebod-enclosure-in-the-rack"></a>Montar a inclusão EBOD no bastidor
Utilizando os rails bastidor que apenas foram instaladas, execute os seguintes passos para montar a inclusão EBOD no bastidor.

#### <a name="to-mount-the-ebod-enclosure"></a>Para montar a inclusão EBOD
1. Com um assistente a inclusão de comparação de precisão e alinhá-lo com os rails bastidor.
2. Cuidadosamente inserir a inclusão de rails e, em seguida, enviá-lo completamente no bastidor Cab.
   
    ![A inserir o dispositivo no bastidor](./media/storsimple-8600-hardware-installation/HCSInsertingDeviceintheRack.png)
   
    **Montar a inclusão no bastidor**
3. Remova os caps flange esquerda e direita front ao extrair os caps livres. As funcionalidade caps flange simplesmente snap para os flanges.
4. Proteja o bastidor em Bastidor instalando um screw Phillips head fornecido através de cada flange, esquerda e da direita.
5. Instale as funcionalidade caps flange ao premi-los numa posição e ajuste-los no local.
   
     ![Instalar a funcionalidade caps de flange](./media/storsimple-8600-hardware-installation/HCSInstallingFlangeCaps.png)
   
    **Instalar as funcionalidade caps flange**
   
   | Etiqueta | Descrição |
   | --- | --- |
   |   1 |Screw fastening de inclusão |

### <a name="mounting-the-primary-enclosure-in-the-rack"></a>Montar a inclusão principal no bastidor
Depois de concluída a montar a inclusão EBOD, terá de montar a inclusão principal, os mesmos passos a seguir.

> [!NOTE]
> * É possível ter alguns ranhuras vazias no bastidor entre a inclusão principal e a inclusão EBOD.
> * Utilize o cabo SAS fornecido milhão 2 para ligar a inclusão principal ao bastidor EBOD.
> * Não existem sem restrições no posicionamento da unidade principal da unidade de EBOD relativo. Por conseguinte, o bastidor primário pode ser colocado na ranhura de superior e a inclusão EBOD abaixo — ou vice-versa.
> 
> 

O próximo passo é instalar os cabos do dispositivo de alimentação, rede e acesso de série.

## <a name="cable-your-storsimple-8600-device"></a>Instalar os cabos do dispositivo StorSimple 8600
Os procedimentos seguintes explicam como instalar os cabos do dispositivo StorSimple 8600 de alimentação, rede e ligações de série.

### <a name="prerequisites"></a>Pré-requisitos
Antes de começar a instalar os cabos do dispositivo, terá de:

* A inclusão principal e a inclusão EBOD completamente descompactado
* 4 cabos de energia (2 cada para o site primário e o bastidor EBOD) fornecido com o seu dispositivo
* 2 cabos SAS fornecidos com o dispositivo para ligar a inclusão EBOD ao bastidor primário
* Acesso às unidades de distribuição do 2 energia (PDUs) (recomendado)
* Cabos de rede
* Fornecido cabos de série
* Conversor de USB de série com o controlador adequado instalado no seu PC (se necessário)
* Fornecido 4 QSFP-para-SFP + adaptadores para utilização com as interfaces de rede de 10 GbE
* [Hardware suportado para as interfaces de rede GbE 10 no seu dispositivo StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)

### <a name="sas-and-power-cabling"></a>SAS e de cabos de energia
O dispositivo tem um bastidor primário e um bastidor EBOD. Isto requer que as unidades ser instalou os cabos em conjunto para conectividade Serial Attached SCSI (SAS) e de energia.

Quando configurar este dispositivo pela primeira vez, execute os passos para SAS cablagem primeiro e, em seguida, conclua os passos para power cablagem.

[!INCLUDE [storsimple-cable-8600-for-SAS](../../includes/storsimple-sas-cable-8600.md)]

[!INCLUDE [storsimple-cable-8600-for-power](../../includes/storsimple-cable-8600-for-power.md)]

### <a name="network-cabling"></a>Cabos de rede
O seu dispositivo é uma configuração de modo de espera ativo: em qualquer momento, um módulo de controlador está ativo e processar todas as operações de disco e rede enquanto o módulo de controlador está no modo de espera. Se ocorrer uma falha de controlador, o controlador de reserva dinâmica ativa imediatamente e continua a todas as operações de disco e de rede.

Para suportar este controlador redundante ativação pós-falha, terá de instalar a rede de dispositivos, conforme mostrado nos passos seguintes.

#### <a name="to-cable-for-network-connection"></a>A cabo da ligação de rede
1. O dispositivo tem seis interfaces de rede em cada controlador: quatro 1 Gbps e 10 de duas portas Gbps Ethernet. Consulte a ilustração seguinte para identificar as portas de dados no backplane do seu dispositivo.
   
     ![Backplane do dispositivo 8600](./media/storsimple-8600-hardware-installation/HCSBackplaneof2UDevicewithPortsLabeled.jpg)
   
    **Efetue uma cópia do seu dispositivo que mostra as portas de dados**
   
   | Etiqueta | Descrição |
   | --- | --- |
   |   0,1,4,5 |Interfaces de rede de 1 GbE |
   |   2,3 |Interfaces de rede de 10 GbE |
   |   6 |Portas série |
2. Consulte o diagrama a seguir para cabos de rede. (A configuração de rede mínimas é apresentada por linhas azuis sólidas. Para elevada disponibilidade e desempenho, necessária configuração adicional é apresentada por linhas ponteada.)

![Instalar os cabos do dispositivo de rede 4U](./media/storsimple-8600-hardware-installation/HCSCableYour4UDeviceforNetwork.png)

**Cablagem para o seu dispositivo de rede**

| Etiqueta | Descrição |
| --- | --- |
| A |LAN com acesso à Internet |
| B |Controlador 0 |
| C |PCM 0 |
| D |Controlador 1 |
| I |PCM 1 |
| F |Controlador EBOD 0 |
| G |Controlador EBOD 1 |
| H, POSSO |Anfitriões (por exemplo, servidores de ficheiros) |
| 0-5 |Interfaces de rede |
| 6 |Inclusão principal |
| 7 |Inclusão EBOD |

Quando os cabos do dispositivo, a configuração mínima requer:

* Pelo menos duas interfaces de rede ligados em cada controlador com um para acesso à nuvem e outro para iSCSI. Os dados de 0 a porta é automaticamente ativada e configurada através da consola de série do dispositivo. Para além dos dados 0, outra porta de dados também tem de ser configurado através do portal clássico do Azure. Neste caso, ligar dados 0 porta para a principal LAN (rede com acesso à Internet). As portas de dados podem ser ligadas ao segmento de LAN SAN/iSCSI (VLAN) da rede, consoante a função pretendida.
* Interfaces idênticas em cada controlador ligadas à mesma rede para garantir a disponibilidade, se ocorrer uma ativação pós-falha de controlador. Por exemplo, se optar por ligar dados 0 e 3 de dados para um dos controladores de, terá de ligar a dados correspondentes 0 e dados 3 no outro controlador de.

Tenha em consideração para elevada disponibilidade e desempenho:

* Sempre que possível, configure um par de interface de rede para acesso à nuvem (de 1 GbE) e outro par de iSCSI (10 GbE recomendado) em cada controlador.
* Sempre que possível, ligar a interfaces de rede de cada controlador a comutadores diferentes dois para garantir a disponibilidade contra uma falha de comutador. A figura abaixo mostra a dois 10 rede GbE das interfaces, dados 2 e dados 3, de cada controlador ligada a comutadores diferentes dois. Para obter mais informações, consulte o **interfaces de rede** sob o [requisitos de elevada disponibilidade para o dispositivo StorSimple](storsimple-system-requirements.md#high-availability-requirements-for-storsimple).

> [!NOTE]
> Se utilizar SFP + transcetores com as interfaces de rede 10 GbE, utilize o QSFP fornecido-SFP + adaptadores. Para obter mais informações, aceda a [hardware suportado para as interfaces de rede GbE 10 no seu dispositivo StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).
> 
> 

### <a name="serial-port-cabling"></a>Cablagem de porta série
Execute os seguintes passos para instalar a porta série.

#### <a name="to-cable-for-serial-connection"></a>A cabo da ligação de série
1. O dispositivo tem uma porta série em cada controlador de que é identificado por um ícone de chave inglesa. Para localizar as portas série, veja a ilustração que mostra os dados de portas na parte do seu dispositivo.
2. Identifique o controlador de Active Directory no seu backplane do dispositivo. Um LED azul intermitente indica que o controlador de Active Directory.
3. Utilize o cabo série fornecido (se necessário, o conversor de USB de série para o seu computador portátil) e ligar a consola ou computador (com emulação do terminal para o dispositivo) à porta de série do controlador ativo.
4. Instale os controladores USB de série (fornecidos com o dispositivo) no seu computador.
5. Configure a ligação de série da seguinte forma:
   
   * 115,200 transmissão.
   * bits de 8 dados
   * bits de 1 paragem
   * Não existem paridade
   * Fluxo de controlo definido como **None**
6. Certifique-se de que a ligação está a funcionar, premindo Enter na consola. Um menu da consola de série deve aparecer.

> [!NOTE]
> **Gestão de KVM:** quando o dispositivo é instalado num centro de dados remoto ou uma sala de computador com acesso limitado, certifique-se de que as ligações de série para ambos os controladores são sempre ligadas a um comutador de consola de série ou equipamento semelhante. Isto permite o controlo remoto de fora de banda e as operações de suporte em caso de interrupção de rede ou falhas inesperadas.
> 
> 

Concluiu o dispositivo de alimentação, acesso à rede e ligação série de cablagem. O passo seguinte consiste em configurar o software no seu dispositivo.

## <a name="next-steps"></a>Passos seguintes
Agora, está pronto para [implementar e configurar o dispositivo StorSimple no local](storsimple-deployment-walkthrough-u2.md).

