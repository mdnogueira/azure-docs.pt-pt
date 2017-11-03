---
title: "Modificar a configuração de dispositivos de série 8000 do StorSimple | Microsoft Docs"
description: "Descreve como utilizar o serviço do Gestor de dispositivos do StorSimple para reconfigurar um dispositivo StorSimple que já tenha sido implementado."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/28/2017
ms.author: alkohli
ms.openlocfilehash: 13ff24c24a881297775fa5f65821e53ceb83c351
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-device-manager-service-to-modify-your-storsimple-device-configuration"></a>Utilizar o serviço do Gestor de dispositivos do StorSimple para modificar a configuração do dispositivo StorSimple

## <a name="overview"></a>Descrição geral

O portal do Azure **definições do dispositivo** secção o **definições** painel contém todos os parâmetros de dispositivo que pode reconfigurar num dispositivo StorSimple que é gerido por um serviço do Gestor de dispositivos do StorSimple. Este tutorial explica como pode utilizar o **definições** painel para realizar as seguintes tarefas ao nível do dispositivo:

* Modificar o nome amigável do dispositivo
* Modificar as definições de hora do dispositivo
* Atribuir um DNS secundário
* Modificar as interfaces de rede
* Comutação ou reatribuir IPs

## <a name="modify-device-friendly-name"></a>Modificar o nome amigável do dispositivo

Pode utilizar o portal do Azure para alterar o nome do dispositivo e atribua-lhe um nome amigável exclusivo à sua escolha. Utilize o **definições gerais** painel no seu dispositivo para modificar o nome amigável do dispositivo. O nome amigável pode conter quaisquer carateres e pode ter um máximo de 64 carateres.

> [!NOTE] 
> Só é possível modificar o nome do dispositivo no portal do Azure antes da configuração do dispositivo foi concluída. Depois de concluída a configuração mínima do dispositivo, não é possível alterar o nome do dispositivo.

![Nome de dispositivo em geral definições](./media/storsimple-8000-modify-device-config/modify-general-settings3.png)

Um dispositivo StorSimple que está ligado ao serviço do Gestor de dispositivos do StorSimple é atribuído um nome predefinido. O nome predefinido normalmente reflete o número de série do dispositivo. Por exemplo, um nome de dispositivo predefinido que é de 15 carateres de comprimento, tais como 8600-SHX0991003G44HT, indica o seguinte:

* **8600** – indica o modelo do dispositivo.
* **SHX** – indica o site de fabrico.
* **0991003** -indica um produto específico.
* **G44HT**-os últimos 5 dígitos são aumentados para criar os números de série exclusivos. Não pode ser um conjunto sequencial.

## <a name="modify-device-description"></a>Modificar a descrição do dispositivo

Utilize o **definições gerais** painel no seu dispositivo para modificar a descrição do dispositivo.

![Descrição do dispositivo nas definições gerais](./media/storsimple-8000-modify-device-config/modify-general-settings4.png)

Uma descrição do dispositivo, normalmente, ajuda a identificar o proprietário e a localização física do dispositivo. O campo de descrição tem de conter menos de 256 carateres.

## <a name="modify-time-settings"></a>Modificar as definições de hora

O dispositivo terá de sincronizar a hora para autenticar com o fornecedor de serviços de armazenamento na nuvem. Utilize o **definições gerais** painel no seu dispositivo para modificar as definições de tempo do dispositivo.

![Descrição do dispositivo nas definições gerais](./media/storsimple-8000-modify-device-config/modify-general-settings2.png)

 Selecione o seu fuso horário da lista pendente. Pode especificar até dois servidores de protocolo NTP (Network Time):

 - **Servidor NTP primário** -a configuração é necessária e é especificada quando utilizar o Windows PowerShell para StorSimple para configurar o seu dispositivo. Pode especificar a predefinição do Windows Server **time.windows.com** como servidor NTP. Pode ver a configuração do servidor NTP primária através do portal do Azure, mas tem de utilizar a interface do Windows PowerShell para alterá-la. Utilize o `Set-HcsNTPClientServerAddress` cmdlet para modificar o servidor NTP primário do seu dispositivo. Para obter mais informações, visite synxtax cmdlet [Set-HcsNTPClientServerAddress] (https://technet.microsoft.com/library/dn688138.aspx).

- **Servidor NTP secundário** -a configuração é opcional. Pode utilizar o portal para configurar um servidor NTP secundário.

Quando configurar o servidor NTP, certifique-se de que a sua rede permite que o tráfego NTP passe do seu centro de dados para a Internet. Quando especificar um servidor NTP público, tem de se certificar de que as firewalls de rede e outros dispositivos de segurança estão configurados para permitir tráfego NTP percorrer de e para a rede externa. Não é permitido o tráfego NTP bidirecional, tem de utilizar um servidor NTP interno (um controlador de domínio do Windows fornece esta função). Se o dispositivo não é possível sincronizar a hora, poderá não conseguir comunicar com o fornecedor de armazenamento em nuvem.

Para obter uma lista de servidores NTP públicos, vá para o [NTP servidores Web](http://support.ntp.org/bin/view/Servers/WebHome).

### <a name="what-happens-if-the-device-is-deployed-in-a-different-time-zone"></a>O que acontece se o dispositivo for implementado num fuso horário diferente?

Se o dispositivo for implementado num fuso horário diferente, irá alterar o fuso horário do dispositivo. Uma vez que todas as políticas de cópia de segurança utilizam o fuso horário do dispositivo, as políticas de cópia de segurança serão ajustado automaticamente de acordo com o novo fuso horário. Não é necessária nenhuma intervenção do utilizador.

## <a name="modify-dns-settings"></a>Modificar as definições de DNS

Um servidor DNS é utilizado quando o dispositivo tenta comunicar com o fornecedor de serviços de armazenamento na nuvem. Utilize o **as definições de rede** painel no seu dispositivo para ver e modificar as definições de DNS configuradas. 

![Definições de DNS nas definições de rede](./media/storsimple-8000-modify-device-config/modify-network-settings1.png)

Para elevada disponibilidade, são necessários para configurar o site primário e os servidores DNS secundários durante a implementação de dispositivos inicial.

**Servidor DNS primário** -utilizar o Windows PowerShell para StorSimple para especificar o servidor DNS primário primeiro durante a configuração inicial. Pode reconfigurar o servidor DNS primário apenas através da interface do Windows PowerShell. Utilize o `Set-HcsDNSClientServerAddress` cmdlet para modificar o servidor DNS primário do seu dispositivo. Para obter mais informações, aceda a synxtax para [conjunto HcsDNSClientServerAddress](https://technet.microsoft.com/library/dn688138.aspx) cmdlet.

**Servidor DNS secundário** - para modificar o servidor DNS secundário, utilize o `Set-HcsDNSClientServerAddress` cmdlet na interface do Windows PowerShell do dispositivo ou **as definições de rede** painel do dispositivo StorSimple no portal do Azure.

Para modificar o servidor DNS secundário no portal do Azure, execute os seguintes passos.

1. Aceda ao seu serviço Gestor de Dispositivos do StorSimple. Na lista de dispositivos, selecione e clique em seu dispositivo.

2. No **definições** painel, aceda a **definições do dispositivo > rede**. Isto abre-se a **as definições de rede** painel. Clique em **as definições de DNS** mosaico. Modificar o endereço IP de servidor DNS secundário.

    ![Modificar adderss IP de servidor DNS secundário](./media/storsimple-8000-modify-device-config/modify-secondary-dns1.png)

4. Na barra de comandos, clique em **guardar** e quando for pedida a confirmação, clique em **OK**.

    ![Guarde e confirmar as alterações](./media/storsimple-8000-modify-device-config/modify-secondary-dns-2.png)



## <a name="modify-network-interfaces"></a>Modificar as interfaces de rede

O dispositivo tem seis interfaces de rede de dispositivo, quatro que são de 1 GbE e dois dos que são 10 GbE. Estas interfaces estão identificadas como dados 0-5 de dados. DADOS 0, 1 de dados, dados 4 e 5 de dados é de 1 GbE, enquanto os dados 2 e dados 3 são as interfaces de rede de 10 GbE.

Utilize o **as definições de rede** painel para configurar cada uma das interfaces a ser utilizado.

![Configurar as interfaces de rede através de definições de rede](./media/storsimple-8000-modify-device-config/modify-network-settings3.png)

Para garantir a elevada disponibilidade, recomendamos que tem pelo menos duas interfaces de iSCSI e duas interfaces de rede com capacidade de nuvem no seu dispositivo. É recomendado, mas não necessitam de que as interfaces não utilizadas ser desativada.

Para cada interface de rede, são apresentados os seguintes parâmetros:

* **Velocidade** – não é um parâmetro configurável pelo utilizador. DATA 0, 1 de dados, dados 4 e 5 de dados encontram-se sempre de 1 GbE, enquanto que os dados 2 e dados 3 são 10 interfaces de GbE.
  
  > [!NOTE]
  > Velocidade e duplex são sempre auto-negociado. Frames Jumbo não é suportados.
  
* **Estado da interface** – uma interface pode ser ativada ou desativada. Se estiver ativada, o dispositivo irá tentar utilizar a interface. Recomendamos que apenas as interfaces que estão ligadas à rede e utilizadas esteja ativada. Desative as interfaces que não está a utilizar.
* **Tipo de interface** – este parâmetro permite-lhe isolar o tráfego iSCSI do tráfego de armazenamento na nuvem. Este parâmetro pode ser um dos seguintes:
  
  * **Nuvem ativada** – quando ativada, o dispositivo irá utilizar esta interface para comunicar com a nuvem.
  * **iSCSI ativado** – quando ativada, o dispositivo irá utilizar esta interface para comunicar com o anfitrião de iSCSI.
    
    Recomendamos que isole o tráfego iSCSI do tráfego de armazenamento na nuvem. Tenha também em atenção que se o anfitrião estiver dentro da mesma sub-rede que o seu dispositivo, não terá de atribuir um gateway; No entanto, se o anfitrião estiver numa sub-rede diferente, que o seu dispositivo, terá de atribuir um gateway.
* **Endereço IP** – quando configurar qualquer uma das interfaces de rede, tem de configurar um IP virtual (VIP). Isto pode ser IPv4 ou IPv6 ou ambos. Famílias de endereços IPv4 e IPv6 são suportadas para as interfaces de rede do dispositivo. Ao utilizar o IPv4, especifique um endereço IP de 32 bits (*xxx.xxx.xxx.xxx*) na notação decimal de ponto. Ao utilizar o IPv6, basta fornecer um prefixo de 4 dígitos e um endereço de 128 bits será gerado automaticamente para a interface de rede do dispositivo com base nesse prefixo.
* **Sub-rede** -Isto refere-se para a máscara de sub-rede e é configurada através da interface do Windows PowerShell.
* **Gateway** – este é o gateway predefinido que deve ser utilizado por esta interface quando tenta comunicar com os nós que não estão no mesmo espaço de endereços IP (sub-rede). O gateway predefinido deve estar no mesmo espaço de endereços (sub-rede) como a interface de endereço IP, conforme determinado pela máscara de sub-rede.
* **Corrigido endereço IP** – este campo está disponível apenas ao configurar os dados 0 interface. Para operações, tais como atualizações ou de resolução de problemas do dispositivo, poderá ter de ligar diretamente ao controlador de dispositivo. O endereço IP fixo pode ser utilizado para aceder a Active Directory e o controlador passivo no seu dispositivo.

> [!NOTE]
> * Para garantir o funcionamento correto, certifique-se a velocidade da interface e duplex no comutador que cada interface de dispositivo está ligado a. Interfaces de comutador ou deve negociar com ou ser configuradas para Gigabit Ethernet (1000 Mbps) e ser full-duplex. Interfaces operativo velocidades mais lento ou no half-duplex resultará em problemas de desempenho.
> * Para minimizar as interrupções e tempo de inatividade, recomendamos que ative portfast em cada uma das portas de comutador que a interface de rede iSCSI do seu dispositivo irá ligar. Isto irá garantir que a conectividade de rede pode ser estabelecida rapidamente em caso de uma ativação pós-falha.

### <a name="configure-data-0"></a>Configurar dados 0

DATA 0 é ativado para a nuvem por predefinição. Quando configurar dados 0, também tem de configurar dois endereços IP fixos, um para cada controlador. Estes endereços IP fixos podem ser utilizados para aceder diretamente a controladores de dispositivo e são úteis quando instalar atualizações no dispositivo, para recolha de lixo funcionar corretamente ou ao aceder aos controladores para fins de resolução de problemas.

Pode reconfigurar os controladores IP fixos através do painel de 0 definições de dados.

![Configure a interface de rede - DATA 0](./media/storsimple-8000-modify-device-config/modify-network-settings2.png)

> [!NOTE]
> Os endereços IP fixos para o controlador são utilizados para manutenção de atualizações para o dispositivo e para algoritmos de recuperação de espaço (recolha de lixo) funcionem corretamente. Por conseguinte, os IPs fixos têm de ser encaminháveis e conseguir estabelecer uma ligação à Internet.

### <a name="configure-data-1---data-5"></a>Configurar dados de 1 - 5 de dados

Para dados 1 - interfaces de rede 5 de dados, pode configurar as definições de rede conforme mostrado na captura de ecrã seguinte:

![Configurar as interfaces de rede 1 de dados - 5 de dados](./media/storsimple-8000-modify-device-config/modify-network-settings4.png)


## <a name="swap-or-reassign-ips"></a>Comutação ou reatribuir IPs

Atualmente, se nenhuma interface de rede no controlador de é atribuído um VIP que está a ser utilizado (ao mesmo dispositivo ou outro dispositivo na rede), em seguida, o controlador de efetuar a ativação pós-falha. Se trocar ou reatribuir VIPs para uma interface de rede do dispositivo, tem de seguir um procedimento adequado, pode criar uma situação de IP duplicada.

Execute os seguintes passos para trocar ou reatribuir VIPs para qualquer uma das interfaces de rede:

#### <a name="to-reassign-ips"></a>Para reatribuir os IPs

1. Limpe o endereço IP para ambas as interfaces.
2. Depois dos endereços IP são eliminados, atribua novos endereços IP para as respetivas interfaces.

## <a name="next-steps"></a>Passos seguintes

* Saiba como [configurar o MPIO para o dispositivo StorSimple](storsimple-8000-configure-mpio-windows-server.md).
* Saiba como [utilizar o serviço do Gestor de dispositivos do StorSimple para administrar o dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

