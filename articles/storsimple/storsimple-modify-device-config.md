---
title: "Modificar a configuração do dispositivo StorSimple | Microsoft Docs"
description: "Descreve como utilizar o serviço StorSimple Manager para reconfigurar um dispositivo StorSimple que já tenha sido implementado."
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: bb679264-8d46-429c-9ef7-630dc3b4a423
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/19/2017
ms.author: v-sharos
ms.openlocfilehash: 5bfbeb038dbedae2bf77016abbc19458c3dc22c9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-manager-service-to-modify-your-storsimple-device-configuration"></a>Utilizar o serviço StorSimple Manager para modificar a configuração do dispositivo StorSimple
## <a name="overview"></a>Descrição geral
Portal clássico do Azure **configurar** página contém todos os parâmetros de dispositivo que pode reconfigurar num dispositivo StorSimple que é gerido por um serviço StorSimple Manager. Este tutorial explica como pode utilizar o **configurar** página para efetuar as seguintes tarefas ao nível do dispositivo:

* Modificar as definições do dispositivo 
* Modificar as definições de hora 
* Modificar as definições de DNS 
* Modificar as interfaces de rede
* Comutação ou reatribuir IPs

## <a name="modify-device-settings"></a>Modificar as definições do dispositivo
As definições do dispositivo incluem o nome amigável do dispositivo e a descrição do dispositivo.

> [!NOTE] 
> Não é possível modificar o nome de dispositivo no portal clássico do Azure. Não é suportado a mudar o nome do dispositivo.

Um dispositivo StorSimple que está ligado ao serviço StorSimple Manager é atribuído um nome predefinido. O nome predefinido normalmente reflete o número de série do dispositivo. Por exemplo, um nome de dispositivo predefinido que é de 15 carateres de comprimento, tais como 8600-SHX0991003G44HT, indica o seguinte:

* **8600** – indica o modelo do dispositivo.
* **SHX** – indica o site de fabrico.
* **0991003** -indica um produto específico.
* **G44HT**-os últimos 5 dígitos são aumentados para criar os números de série exclusivos. Não pode ser um conjunto sequencial.

Pode especificar uma descrição do dispositivo. Uma descrição do dispositivo, normalmente, ajuda a identificar o proprietário e a localização física do dispositivo. O campo de descrição tem de conter menos de 256 carateres.

## <a name="modify-time-settings"></a>Modificar as definições de hora
O dispositivo terá de sincronizar a hora para autenticar com o fornecedor de serviços de armazenamento na nuvem. Selecione o seu fuso horário da lista pendente e especificar até dois servidores do protocolo NTP (Network Time). O servidor NTP primário é necessário e é especificado quando utilizar o Windows PowerShell para StorSimple para configurar o seu dispositivo. Pode especificar a predefinição do Windows Server **time.windows.com** como servidor NTP. Pode ver a configuração do servidor NTP primária através do portal clássico do Azure, mas tem de utilizar a interface do Windows PowerShell para alterá-la.

A configuração do servidor NTP secundária é opcional. Pode utilizar o portal clássico para configurar um servidor NTP secundário. 

Quando configurar o servidor NTP, certifique-se de que a sua rede permite que o tráfego NTP passe do seu centro de dados para a Internet. Quando especificar um servidor NTP público, tem de se certificar de que as firewalls de rede e outros dispositivos de segurança estão configurados para permitir tráfego NTP percorrer de e para a rede externa. Não é permitido o tráfego NTP bidirecional, tem de utilizar um servidor NTP interno (um controlador de domínio do Windows fornece esta função). Se o dispositivo não é possível sincronizar a hora, poderá não conseguir comunicar com o fornecedor de armazenamento em nuvem.

Para obter uma lista de servidores NTP públicos, vá para o [NTP servidores Web](http://support.ntp.org/bin/view/Servers/WebHome). 

### <a name="what-happens-if-the-device-is-deployed-in-a-different-time-zone"></a>O que acontece se o dispositivo for implementado num fuso horário diferente?
Se o dispositivo for implementado num fuso horário diferente, irá alterar o fuso horário do dispositivo. Uma vez que todas as políticas de cópia de segurança utilizam o fuso horário do dispositivo, as políticas de cópia de segurança serão ajustado automaticamente de acordo com o novo fuso horário. Não é necessária nenhuma intervenção do utilizador.

## <a name="modify-dns-settings"></a>Modificar as definições de DNS
Um servidor DNS é utilizado quando o dispositivo tenta comunicar com o fornecedor de serviços de armazenamento na nuvem. Para elevada disponibilidade, são necessários para configurar o site primário e os servidores DNS secundários durante a implementação de dispositivos inicial. Para reconfigurar o servidor DNS primário, terá de utilizar a interface do Windows PowerShell no dispositivo StorSimple.

Para modificar o servidor DNS secundário, pode utilizar o portal clássico do Azure.

## <a name="modify-network-interfaces"></a>Modificar as interfaces de rede
O dispositivo tem seis interfaces de rede de dispositivo, quatro que são de 1 GbE e dois dos que são 10 GbE. Estas interfaces estão identificadas como dados 0-5 de dados. DADOS 0, 1 de dados, dados 4 e 5 de dados é de 1 GbE, enquanto os dados 2 e dados 3 são as interfaces de rede de 10 GbE.

Configurar **as definições da Interface de rede** para cada uma das interfaces a ser utilizado. Para garantir a elevada disponibilidade, recomendamos que tem pelo menos duas interfaces de iSCSI e duas interfaces de rede com capacidade de nuvem no seu dispositivo. É recomendado, mas não necessitam de que as interfaces não utilizadas ser desativada.

Quando configurar qualquer uma das interfaces de rede, tem de configurar um IP virtual (VIP).

DATA 0 é ativado para a nuvem por predefinição. Quando configurar dados 0, também tem de configurar dois endereços IP fixos, um para cada controlador. Estes endereços IP fixos podem ser utilizados para aceder diretamente a controladores de dispositivo e são úteis quando instalar atualizações no dispositivo ou ao aceder aos controladores para fins de resolução de problemas.

No StorSimple série 8000 atualização 1, a métrica de encaminhamento de dados 0 está definida para a mais baixa; Por conseguinte, se o dispositivo estiver em execução StorSimple 8000 série Update 1, todo o tráfego de nuvem será encaminhado através de dados 0. Tome nota deste se tiver mais de uma interface de rede de nuvem ativada no dispositivo StorSimple.

> [!NOTE]
> Os endereços IP fixos do controlador são utilizados para a manutenção de atualizações do dispositivo. Por conseguinte, os IPs fixos têm de ser encaminháveis e conseguir estabelecer uma ligação à Internet.
> 
> 

Para cada interface de rede, são apresentados os seguintes parâmetros:

* **Velocidade** – não é um parâmetro configurável pelo utilizador. DATA 0, 1 de dados, dados 4 e 5 de dados encontram-se sempre de 1 GbE, enquanto que os dados 2 e dados 3 são 10 interfaces de GbE.
  
  > [!NOTE]
  > Velocidade e duplex são sempre auto-negociado. Frames Jumbo não é suportados.
  > 
  > 
* **Estado da interface** – uma interface pode ser ativada ou desativada. Se estiver ativada, o dispositivo irá tentar utilizar a interface. Recomendamos que apenas as interfaces que estão ligadas à rede e utilizadas esteja ativada. Desative as interfaces que não está a utilizar.
* **Tipo de interface** – este parâmetro permite-lhe isolar o tráfego iSCSI do tráfego de armazenamento na nuvem. Este parâmetro pode ser um dos seguintes:
  
  * **Nuvem ativada** – quando ativada, o dispositivo irá utilizar esta interface para comunicar com a nuvem.
  * **iSCSI ativado** – quando ativada, o dispositivo irá utilizar esta interface para comunicar com o anfitrião de iSCSI.
    
    Recomendamos que isole o tráfego iSCSI do tráfego de armazenamento na nuvem. Tenha também em atenção que se o anfitrião estiver dentro da mesma sub-rede que o seu dispositivo, não terá de atribuir um gateway; No entanto, se o anfitrião estiver numa sub-rede diferente, que o seu dispositivo, terá de atribuir um gateway.
* **Endereço IP** – Isto pode ser IPv4 ou IPv6 ou ambos. Famílias de endereços IPv4 e IPv6 são suportadas para as interfaces de rede do dispositivo. Ao utilizar o IPv4, especifique um endereço IP de 32 bits (*xxx.xxx.xxx.xxx*) na notação decimal de ponto. Ao utilizar o IPv6, basta fornecer um prefixo de 4 dígitos e um endereço de 128 bits será gerado automaticamente para a interface de rede do dispositivo com base nesse prefixo.
* **Sub-rede** -Isto refere-se para a máscara de sub-rede e é configurada através da interface do Windows PowerShell.
* **Gateway** – este é o gateway predefinido que deve ser utilizado por esta interface quando tenta comunicar com os nós que não estão no mesmo espaço de endereços IP (sub-rede). O gateway predefinido deve estar no mesmo espaço de endereços (sub-rede) como a interface de endereço IP, conforme determinado pela máscara de sub-rede.
* **Corrigido endereço IP** – este campo está disponível apenas ao configurar os dados 0 interface. Para operações, tais como atualizações ou de resolução de problemas do dispositivo, poderá ter de ligar diretamente ao controlador de dispositivo. O endereço IP fixo pode ser utilizado para aceder a Active Directory e o controlador passivo no seu dispositivo.

Pode reconfigurar o controlador 0 e o controlador 1 através do portal clássico do Azure.

> [!NOTE]
> * Para garantir o funcionamento correto, certifique-se a velocidade da interface e duplex no comutador que cada interface de dispositivo está ligado a. Interfaces de comutador ou deve negociar com ou ser configuradas para Gigabit Ethernet (1000 Mbps) e ser full-duplex. Interfaces operativo velocidades mais lento ou no half-duplex resultará em problemas de desempenho.
> * Para minimizar as interrupções e tempo de inatividade, recomendamos que ative portfast em cada uma das portas de comutador que a interface de rede iSCSI do seu dispositivo irá ligar. Isto irá garantir que a conectividade de rede pode ser estabelecida rapidamente em caso de uma ativação pós-falha.
> 
> 

## <a name="swap-or-reassign-ips"></a>Comutação ou reatribuir IPs
Atualmente, se nenhuma interface de rede no controlador de é atribuído um VIP que está a ser utilizado (ao mesmo dispositivo ou outro dispositivo na rede), em seguida, o controlador de efetuar a ativação pós-falha. Por conseguinte, terá de seguir o procedimento adequado se são trocar VIPs para a interface de rede do dispositivo, porque irá criar uma situação de IP duplicada.

Execute os seguintes passos para trocar ou reatribuir VIPs para qualquer uma das interfaces de rede:

#### <a name="to-reassign-ips"></a>Para reatribuir os IPs
1. Limpe o endereço IP para ambas as interfaces.
2. Depois dos endereços IP são eliminados, atribua novos endereços IP para as respetivas interfaces.

## <a name="next-steps"></a>Passos seguintes
* Saiba como [configurar o MPIO para o dispositivo StorSimple](storsimple-configure-mpio-windows-server.md).
* Saiba como [utilizam o serviço StorSimple Manager para administrar o dispositivo StorSimple](storsimple-manager-service-administration.md).

