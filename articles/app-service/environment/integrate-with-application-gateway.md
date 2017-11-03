---
title: "Integrar o ILB ASE com um Gateway de aplicação do Azure"
description: "Obter instruções sobre como integrar uma aplicação no seu ASE ILB com o Gateway de aplicação do Azure"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: a6a74f17-bb57-40dd-8113-a20b50ba3050
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: ccompy
ms.openlocfilehash: eedad8824add7fe425d34975dab640fbee82c2bc
ms.sourcegitcommit: b723436807176e17e54f226fe00e7e977aba36d5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2017
---
# <a name="integrating-your-ilb-ase-with-an-application-gateway"></a>Integrar o ILB ASE com um Gateway de aplicação #

O [ambiente de serviço de aplicações do Azure (ASE)](./intro.md) é uma implementação do serviço de aplicações do Azure na sub-rede da rede Virtual do Azure do cliente. Pode ser implementado com um ponto de final público ou privado para acesso à aplicação. A implementação de ASE com um ponto de final privado é denominada ILB ASE.  
O Gateway de aplicação do Azure é uma aplicação virtual que fornece balanceamento de carga de camada 7, a descarga de SSL e a proteção de WAF. Pode escutar um IP endereços e encaminhar tráfego público para o ponto final da aplicação. As informações seguintes descrevem como integrar uma WAF configurado o Gateway de aplicação com uma aplicação num ILB ASE.  

A integração do Gateway de aplicação com o ILB ASE é um nível de aplicação.  Quando configurar o Gateway de aplicação com o seu ASE do ILB, está a fazê-lo para aplicações específicas na sua ASE ILB. Isto permite que aplicações de multi-inquilinos seguras num único ASE do ILB de alojamento.  

![Gateway de aplicação que aponta para a aplicação num ASE do ILB][1]

Nestas instruções, irá:

* Criar um Gateway de aplicação
* configurar o Gateway de aplicação para apontar para uma aplicação no seu ASE do ILB
* Configure a sua aplicação que respeite o nome de domínio personalizado
* Editar o nome de anfitrião público do DNS que aponta para o Gateway de aplicação

Para integrar o Gateway de aplicação com o seu ASE do ILB, tem de:

* ASE do ILB
* uma aplicação em execução num ASE ILB
* um nome de domínio encaminhável de internet para ser utilizado com a sua aplicação num ASE ILB
* o endereço do ILB utilizado pelo seu ASE ILB (este é o portal de ASE em **definições -> endereços IP**)

    ![Endereços IP utilizados pelo seu ASE do ILB][9]
    
* um nome DNS público, que é utilizado mais tarde para apontar para o gateway de aplicação 

Para obter detalhes sobre como criar ILB ASE ler o documento [criar e utilizar ASE do ILB][ilbase]

Este guia pressupõe que pretende que um Gateway de aplicação na mesma rede Virtual do Azure e que o ASE está implementada para. Antes de iniciar a criação do Gateway de aplicação, escolha ou crie uma sub-rede que irá utilizar para alojar o Gateway de aplicação. Deve utilizar uma sub-rede de não um com o nome GatewaySubnet, ou a sub-rede utilizada pelo ASE ILB.
Se colocar o Gateway de aplicação no GatewaySubnet, em seguida, poderá criar um gateway de rede Virtual mais tarde. Poderá também não é possível colocá-la para a sub-rede utilizada pelo seu ASE do ILB, como o ASE é a única coisa que pode ser na respetiva sub-rede.

## <a name="steps-to-configure"></a>Passos para configurar ##

1. De dentro do portal do Azure, aceda a **novo > rede > Gateway de aplicação** 
    1. Fornece:
        1. Nome do Gateway de aplicação
        1. Selecione WAF
        1. Selecione a subscrição do mesma utilizada para a VNet ASE
        1. Crie ou selecione o grupo de recursos
        1. Selecione a localização onde a VNet ASE está no

    ![Novo Noções básicas do criação de gateway aplicação][2]   
    1. No conjunto de área de definições:
        1. A VNet ASE
        1. A sub-rede do Gateway de aplicação tem de ser implementado no. Fazer sem utilizar o GatewaySubnet impedirá a criação de gateways de VPN
        1. Selecione público
        1. Selecione um endereço IP público. Se não tiver uma, em seguida, criar um neste momento
        1. Configure para HTTP ou HTTPS. Se a configurar para HTTPS tem de fornecer um certificado PFX
        1. Selecionadas fireway definições da aplicação Web. Aqui pode ativar a firewall e também defini-lo para a deteção ou prevenção como ver ajustar.

    ![Novas definições de criação do gateway de aplicação][3]
    
    1. Reveja o resumo de secção, selecionar **Ok**. Pode demorar um pouco mais de 30 minutos para que o Gateway de aplicação para concluir a configuração.  

2. Após a conclusão da configuração do Gateway de aplicação, vá para o portal de Gateway de aplicação. Selecione **conjunto back-end**.  Adicione o endereço do ILB para ASE o ILB.

    ![Configurar o conjunto de back-end][4]

3. Depois do processamento ser concluído para configurar o conjunto de back-end, selecione **sondas de estado de funcionamento**. Crie uma sonda do Estado de funcionamento para o nome de domínio que pretende utilizar para a sua aplicação. 

    ![Configurar sondas do estado de funcionamento][5]
    
4. Depois do processamento ser concluído para configurar as sondas de estado de funcionamento, selecione **definições HTTP**.  Editar a definição existente não existe, selecione **sonda de utilização personalizada**e escolha a sonda que configurou.

    ![Configurar definições de HTTP][6]
    
5. Vá para o Gateway de aplicação **descrição geral**e copie o endereço IP público utilizado para o Gateway de aplicação.  Definir esse endereço IP como um registo a para o seu nome de domínio de aplicação ou utilize o nome DNS para esse endereço de um registo CNAME.  É mais fácil selecionar o endereço IP público e copie-o do endereço IP público da IU em vez de copiá-lo a partir da ligação na secção Descrição geral do Gateway de aplicação. 

    ![Portal de Gateway de aplicação][7]

6. Defina o nome de domínio personalizado para a sua aplicação no seu ASE ILB.  Aceda à sua aplicação no portal e, em selecione definições **domínios personalizados**

![Definir o nome de domínio personalizado da aplicação][8]

Não há informações sobre a configuração de nomes de domínio personalizados para as suas aplicações web aqui [definir nomes de domínio personalizado da sua aplicação web][custom-domain]. A diferença com uma aplicação num ILB ASE e esse documento é que não existe qualquer validação no nome de domínio.  Uma vez que é proprietário de DNS que gere os pontos finais de aplicação, pode colocar que pretende no mesmo. O nome de domínio personalizado que adicionar neste caso, não precisa de estar no seu DNS, mas ainda precisa de ser configurada com a sua aplicação. 

Após a conclusão da configuração e que tenham permitido um curto período de tempo para as alterações DNS se propague, em seguida, pode aceder à sua aplicação com o nome de domínio personalizado que criou. 


<!--IMAGES-->
[1]: ./media/integrate-with-application-gateway/appgw-highlevel.png
[2]: ./media/integrate-with-application-gateway/appgw-createbasics.png
[3]: ./media/integrate-with-application-gateway/appgw-createsettings.png
[4]: ./media/integrate-with-application-gateway/appgw-backendpool.png
[5]: ./media/integrate-with-application-gateway/appgw-healthprobe.png
[6]: ./media/integrate-with-application-gateway/appgw-httpsettings.png
[7]: ./media/integrate-with-application-gateway/appgw-publicip.png
[8]: ./media/integrate-with-application-gateway/appgw-customdomainname.png
[9]: ./media/integrate-with-application-gateway/appgw-iplist.png

<!--LINKS-->
[appgw]: http://docs.microsoft.com/azure/application-gateway/application-gateway-introduction
[custom-domain]: ../app-service-web-tutorial-custom-domain.md
[ilbase]: ./create-ilb-ase.md
