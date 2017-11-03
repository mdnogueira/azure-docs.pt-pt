---
title: Instalar o dispositivo 8100 do Microsoft Azure StorSimple | Microsoft Docs
description: Descreve como descompactar, montar em bastidor e instalar o seu dispositivo StorSimple 8100 antes de implementar e configurar o software.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: 6098a01e-c031-488a-a8d7-0b607ce665e1
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 08/17/2016
ms.author: alkohli
ms.openlocfilehash: 102dffcd73f3d3b9362d7b2853faa060e9c645dd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="unpack-rack-mount-and-cable-your-storsimple-8100-device"></a>Descompactar, montar em bastidor e instalar os cabos do dispositivo StorSimple 8100
## <a name="overview"></a>Descrição geral
Os cabos do 8100 Microsoft Azure StorSimple é um bastidor único, o dispositivo eletrodomésticos montados. Este tutorial explica como descompactar, montar em bastidor e hardware do dispositivo de cabo do 8100 StorSimple antes de configurar e implementar o dispositivo StorSimple.

## <a name="unpack-your-storsimple-8100-device"></a>Descompacte o seu dispositivo 8100 do StorSimple
Os passos seguintes fornecem instruções claras e detalhadas sobre como Descompacte o seu dispositivo de armazenamento StorSimple 8100. Este dispositivo está incluído numa única caixa.

### <a name="prepare-to-unpack-your-device"></a>Preparar para descompactar o seu dispositivo
Antes de Descompacte o seu dispositivo, reveja as seguintes informações.

![Ícone de aviso](./media/storsimple-safety/IC740879.png)![ícone de ponderação pesada](./media/storsimple-8100-hardware-installation/HCS_HeavyWeight_Icon.png) **aviso!**

1. Certifique-se de que tem duas pessoas disponíveis para gerir o peso do bastidor se são processamento manualmente. Um bastidor totalmente configurado pode pesar até 32 kg (70 lbs.).
2. A caixa de uma superfície simples, nível local.

Em seguida, conclua os seguintes passos para descompactar o seu dispositivo.

#### <a name="to-unpack-your-device"></a>Para descompactar o seu dispositivo
1. Inspecione a caixa e foam o empacotamento crushes, cuts, máximo danos ou quaisquer outros danos óbvios. Se a caixa ou empacotamento gravemente está danificado, abre a caixa. . [Contacte a Microsoft Support](storsimple-contact-microsoft-support.md) para o ajudar a avaliar se o dispositivo está no bom trabalho ordem.
2. Descompacte a caixa. A imagem seguinte mostra a vista descompactada do dispositivo StorSimple.
   
     ![Descompacte o seu dispositivo de armazenamento](./media/storsimple-8100-hardware-installation/HCSUnpackyour2Udevice.png)
   
    **Vista descompactada do seu dispositivo de armazenamento**
   
   | Etiqueta | Descrição |
   | --- | --- |
   |   1 |Caixa de mais |
   |   2 |Foam inferior |
   |   3 |Dispositivo |
   |   4 |Foam superior |
   |   5 |Caixa acessórios |
3. Após unpacking caixa, certifique-se de que tem:
   
   * dispositivo de inclusão único 1
   * 2 cabos de energia
   * 1 cruzado cabo de Ethernet
   * 2 cabos de consola de série
   * 1 USB de série conversor para acesso de série
   * 1 screwdriver de T10 prova de adulteração
   * 4 QSFP-para-SFP + adaptadores para utilização com as interfaces de rede de 10 GbE
   * 1. o bastidor-montagem kit (2 rails lado com montar hardware)
   * Obter a documentação de introdução
     
     Se não recebeu qualquer um dos itens listados acima, [contacte a Microsoft Support](storsimple-contact-microsoft-support.md).

O passo seguinte é montar em Bastidor seu dispositivo.

## <a name="rack-mount-your-storsimple-8100-device"></a>Montar em Bastidor dispositivo 8100 do StorSimple
Siga os passos seguintes para instalar o seu dispositivo de armazenamento StorSimple 8100 num bastidor 19 polegadas padrão com front-end e rear publicações. O dispositivo StorSimple 8100 tem um bastidor primário único.

A instalação é composta por vários passos, cada um dos quais é explicada nos procedimentos seguintes.

> [!IMPORTANT]
> Dispositivos StorSimple tem de ser eletrodomésticos montados para um funcionamento correto.
> 
> 

### <a name="prepare-the-site"></a>Preparar o site
O dispositivo tem de ser instalado num bastidor 19 polegadas padrão que tenha o front-end e rear publicações. Utilize o procedimento seguinte para preparar a instalação de bastidor.

#### <a name="to-prepare-the-site-for-rack-installation"></a>Para preparar o site para a instalação de bastidor
1. Certifique-se de que o dispositivo em segurança assenta num trabalho simples, estável e nível superfície (ou semelhante).
2. Certifique-se de que o site onde pretende configurar tem padrão AC energia de uma origem independente ou uma unidade de distribuição de energia (PDU) de bastidor com uma de alimentação ininterrupta (UPS).
3. Certifique-se de que um espaço de 2U está disponível no bastidor em que pretende montar o dispositivo.

![Ícone de aviso](./media/storsimple-safety/IC740879.png)![ícone de ponderação pesada](./media/storsimple-8100-hardware-installation/HCS_HeavyWeight_Icon.png) **aviso!**

Certifique-se de que tem duas pessoas disponíveis para gerir a ponderação se estiver a processar a configuração do dispositivo manualmente. Um bastidor totalmente configurado pode pesar até 32 kg (70 lbs.).

### <a name="rack-prerequisites"></a>Pré-requisitos de bastidor
A inclusão de 8100 foi concebida para instalação num bastidor 19 polegadas padrão cab com:

* Profundidade mínima de polegadas 27.84 de bastidor post para o post.
* Ponderação máxima de 32 kg para o dispositivo
* Pressão de back-máximo de 5 Pascal (0.5 mm máximo medidor).

### <a name="rack-mounting-rail-kit"></a>Kit de lado de montagem de bastidor
Um conjunto de montar rails é fornecido para utilização com o ficheiro CAB 19 polegadas bastidor. Os rails foi testados para lidar com a importância de inclusão máximo. Estes rails também irão permitir a instalação de múltiplas inclusões sem qualquer perda de espaço no bastidor.

#### <a name="to-install-the-device-on-the-rails"></a>Para instalar o dispositivo nos rails
1. Execute este passo apenas se rails internas não estão instalados no seu dispositivo. Normalmente, os rails internas são instalados na fábrica de. Se rails não estiver instalados, instale os lado esquerdo e direito lado slides para as margens do chassis de inclusão. Se anexar utilizando seis screws métricas em cada lado. Para ajudar com orientação, slides a lado são marcados **LH – frente** e **RH – frente**, e o end que está affixed para rear da inclusão tem um fim tapered.<br/>
   
    ![Anexar slides lado chassis de inclusão](./media/storsimple-8100-hardware-installation/HCSAttachingRailSlidestoEnclosureChassis.png)

    **Anexar slides lado interna para as margens do bastidor**
   
    Etiqueta | Descrição
    ----- | -----------
    1     | M, 3, 4 screws head do botão
    2     | Slides de chassis

2. Anexe o lado esquerdo externo e assemblagens lado direito externa para os membros de vertical cab bastidor. Retos estão marcados **LH**, **RH**, e **este lado segurança** para ajudá-lo através da orientação do correta.
3. Localize os pins de lado de frente e rear da assemblagem lado. Expanda o lado para caber entre as mensagens de bastidor e inserir os pins de front-end e bastidor rear post membro vertical holes. Lembre-se de que a assemblagem lado é nível.
4. Utilize dois os screws métricas fornecidos para proteger a assemblagem lado bastidor membros verticais. Utilize um screw o front-end e um o rear.
5. Repita estes passos para a assemblagem de lado.<br/>
   
     ![Anexar slides lado bastidor Cab](./media/storsimple-8100-hardware-installation/HCSAttachingRailSlidestoRackCabinet.png)
   
    **Anexar assemblagens lado externa ao bastidor**
   
   | Etiqueta | Descrição |
   | --- | --- |
   |   1 |Clamping screw |
   |   2 |Bastidor quadrada hole de front-post screw |
   |   3 |Pins de front-localização do lado esquerdo |
   |   4 |Clamping screw |
   |   5 |Pins de localização rear do lado esquerdo |

### <a name="mounting-the-device-in-the-rack"></a>Ao instalar o dispositivo no bastidor
Utilizando os rails bastidor que apenas foram instaladas, execute os seguintes passos para montar o dispositivo no bastidor.

#### <a name="to-mount-the-device"></a>Para montar o dispositivo
1. Com um assistente a inclusão de comparação de precisão e alinhá-lo com os rails bastidor.
2. Cuidadosamente inserir o dispositivo as rails e, em seguida, enviar o dispositivo completamente para o bastidor Cab.<br/>
   
    ![A inserir o dispositivo no bastidor](./media/storsimple-8100-hardware-installation/HCSInsertingDeviceintheRack.png)
   
    **Ao instalar o dispositivo no bastidor**
3. Remova os caps flange esquerda e direita front ao extrair os caps livres. As funcionalidade caps flange simplesmente snap para os flanges.
4. Proteja o bastidor no bastidor instalando um screw Phillips head fornecido através de cada flange, esquerda e da direita.
5. Instale as funcionalidade caps flange ao premi-los numa posição e ajuste-los no local.<br/>
   
     ![Instalar a funcionalidade caps de flange](./media/storsimple-8100-hardware-installation/HCSInstallingFlangeCaps.png)
   
    **Instalar as funcionalidade caps flange**
   
   | Etiqueta | Descrição |
   | --- | --- |
   |   1 |Screw fastening de inclusão |

O próximo passo é instalar os cabos do dispositivo de alimentação, rede e acesso de série.

## <a name="cable-your-storsimple-8100-device"></a>Instalar os cabos do dispositivo StorSimple 8100
Os procedimentos seguintes explicam como instalar os cabos do dispositivo StorSimple 8100 de alimentação, rede e ligações de série.

### <a name="prerequisites"></a>Pré-requisitos
Antes de começar a cablagem do seu dispositivo, terá de:

* O bastidor e de dispositivos de armazenamento, completamente descompactado, montado.
* 2 cabos de energia fornecido com o seu dispositivo
* Acesso a 2 unidades de distribuição de energia (recomendado).
* Cabos de rede
* Fornecido cabos de série
* Conversor USB de série com o controlador adequado instalado no seu PC (se necessário)
* Fornecido 4 QSFP-para-SFP + adaptadores para utilização com as interfaces de rede de 10 GbE
* [Hardware suportado para as interfaces de rede GbE 10 no seu dispositivo StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)

### <a name="power-cabling"></a>Cablagem de energia
O dispositivo inclui redundante energia e arrefecimento módulos (PCMs). Tanto PCMs tem de ser instaladas e ligadas a fontes de alimentação diferentes para garantir a elevada disponibilidade.

Execute os seguintes passos para instalar os cabos do dispositivo de energia.

[!INCLUDE [storsimple-cable-8100-for-power](../../includes/storsimple-cable-8100-for-power.md)]

### <a name="network-cabling"></a>Cabos de rede
O dispositivo é uma configuração de modo de espera ativo: em qualquer momento, um módulo de controlador está ativo e processar todas as operações de disco e rede enquanto o módulo de controlador está no modo de espera. Se um controlador falhar, o controlador de reserva dinâmica está ativado imediatamente e continua a todas as operações de rede e disco.

Para suportar este controlador redundante ativação pós-falha, terá de instalar a sua rede de dispositivo, tal como descrito nos passos seguintes.

#### <a name="to-cable-for-network-connection"></a>A cabo da ligação de rede
1. O dispositivo tem seis interfaces de rede em cada controlador: quatro 1 Gbps, e 10 de duas portas de Gbps Ethernet. Identifica as várias portas de dados no backplane do seu dispositivo.
   
    ![Backplane do dispositivo 8100](./media/storsimple-8100-hardware-installation/HCSBackplaneof2UDevicewithPortsLabeled.jpg)
   
    **Atrás do dispositivo que mostra as portas de dados**
   
   | Etiqueta | Descrição |
   | --- | --- |
   |   0,1,4,5 |Interfaces de rede de 1 GbE |
   |   2,3 |Interfaces de rede de 10 GbE |
   |   6 |Portas série |
2. Consulte o diagrama a seguir para cabos de rede. (A configuração de rede mínimas é apresentada por linhas azuis sólidas. Configuração adicional necessária para elevada disponibilidade e desempenho é apresentada por linhas ponteada.)

    ![Instalar os cabos do dispositivo de rede 2U](./media/storsimple-8100-hardware-installation/HCSCableYour2UDeviceforNetwork.png)

    **Cablagem para o seu dispositivo de rede**

   |Etiqueta | Descrição |
   |----- | ----------- |
   | A    | LAN com acesso à Internet |
   | B    | Controlador 0 |
   | C    | PCM 0 |
   | D    | Controlador 1 |
   | I    | PCM 1 |
   | F, G | Anfitriões |
   | 0-5  | Interfaces de rede |



Quando os cabos do dispositivo, a configuração mínima requer:

* Pelo menos duas interfaces de rede ligados em cada controlador com um para acesso à nuvem e outro para iSCSI. Os dados de 0 a porta é automaticamente ativada e configurada através da consola de série do dispositivo. Para além dos dados 0, outra porta de dados também tem de ser configurado através do portal clássico do Azure. Neste caso, ligar dados 0 porta para a principal LAN (rede com acesso à Internet). As portas de dados podem ser ligadas ao segmento de LAN SAN/iSCSI (VLAN) da rede, consoante a função pretendida.
* Interfaces idênticas em cada controlador ligadas à mesma rede para garantir a disponibilidade, se ocorrer uma ativação pós-falha de controlador. Por exemplo, se optar por ligar dados 0 e 3 de dados para um dos controladores de, terá de ligar a dados correspondentes 0 e dados 3 no outro controlador de.

Tenha em consideração para elevada disponibilidade e desempenho:

* Sempre que possível, configure um par de interface de rede para acesso à nuvem (de 1 GbE) e outro par de iSCSI (10 GbE recomendado) em cada controlador.
* Sempre que possível, ligar a interfaces de rede de cada controlador a comutadores diferentes dois para garantir a disponibilidade contra uma falha de comutador. A figura abaixo mostra a dois 10 rede GbE das interfaces, dados 2 e dados 3, de cada controlador ligada a comutadores diferentes dois.

Para obter mais informações, consulte o **interfaces de rede** sob o [requisitos de elevada disponibilidade para o dispositivo StorSimple](storsimple-system-requirements.md#high-availability-requirements-for-storsimple).

> [!NOTE]
> Se estiver a utilizar SFP + transcetores com as interfaces de rede 10 GbE, utilize o QSFP fornecido-SFP + adaptadores. Para obter mais informações, aceda a [hardware suportado para as interfaces de rede GbE 10 no seu dispositivo StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).
> 
> 

### <a name="serial-port-cabling"></a>Cablagem de porta série
Execute os seguintes passos para instalar a porta série.

#### <a name="to-cable-for-serial-connection"></a>A cabo da ligação de série
1. O dispositivo tem uma porta série em cada controlador de que é identificado por um ícone de chave inglesa. Veja a ilustração no [cabos de rede](#network-cabling) secção para localizar as portas série em backplane do seu dispositivo.
2. Identifique o controlador de Active Directory no seu backplane do dispositivo. Um LED azul intermitente indica que o controlador de Active Directory.
3. Utilize os cabos de série fornecidos (se necessário, o conversor de USB de série para o seu computador portátil) e ligar a consola ou computador (com emulação do terminal para o dispositivo) à porta de série do controlador ativo.
4. Instale os controladores USB de série (fornecidos com o dispositivo) no seu computador.
5. Configure a ligação de série da seguinte forma: transmissão 115,200, bits de 8 dados, bits de 1 paragem, não paridade e controlo de fluxo definida como None.
6. Certifique-se de que a ligação está a funcionar, premindo Enter na consola. Um menu da consola de série deve aparecer.

> [!NOTE]
> **Gestão de KVM**: quando o dispositivo é instalado num centro de dados remoto ou uma sala de computador com acesso limitado, certifique-se de que as ligações de série para ambos os controladores são sempre ligadas a um comutador de consola de série ou equipamento semelhante. Isto permite que as operações de suporte e controlo remoto de fora de banda se existem interrupções de rede ou falhas inesperadas.
> 
> 

O dispositivo está agora instalou os cabos de alimentação, acesso à rede e conectividade de série. O passo seguinte consiste em configurar o software e implementar o seu dispositivo.

## <a name="next-steps"></a>Passos seguintes
Saiba como [implementar e configurar o dispositivo StorSimple no local](storsimple-deployment-walkthrough-u2.md).

