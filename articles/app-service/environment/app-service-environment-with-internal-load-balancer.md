---
title: "Criar e utilizar um balanceador de carga interno com um ambiente de serviço de aplicações | Microsoft Docs"
description: Criar e utilizar ASE com um ILB
services: app-service
documentationcenter: 
author: ccompy
manager: stefsch
editor: 
ms.assetid: ad9a1e00-d5e5-413e-be47-e21e5b285dbf
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.openlocfilehash: c0f942cb11edc6d6212914b3134e25c3fbc3d3ab
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="using-an-internal-load-balancer-with-an-app-service-environment"></a>Utilizar um balanceador de carga interno com um ambiente de serviço de aplicações

> [!NOTE] 
> Este artigo é sobre o ambiente de serviço de aplicações v1. Há uma versão mais recente do ambiente de serviço de aplicações que são mais fáceis de utilizar e é executada na infraestrutura mais poderosa. Para saber mais sobre o novo início de versão com o [introdução ao ambiente de serviço de aplicações](intro.md).
>

A funcionalidade de ambiente de serviço de aplicações (ASE) é uma opção de serviço Premium do serviço de aplicações do Azure que fornece uma capacidade de configuração avançada que não está disponível nos carimbos de multi-inquilinos. A funcionalidade de ASE essencialmente implementa o App Service do Azure na sua Network(VNet) Virtual do Azure. Para obter uma maior compreensão das funcionalidades oferecidas pelas ambientes do App Service, leia o [o que é um ambiente de serviço de aplicações] [ WhatisASE] documentação. Se não souber as vantagens de funcionar numa leitura de VNet a [FAQ da rede Virtual do Azure][virtualnetwork]. 

## <a name="overview"></a>Descrição geral
ASE pode ser implementado com um ponto final acessível de internet ou com um endereço IP na sua VNet. Para definir o endereço IP para um endereço de VNet terá de implementar a sua ASE com um Balancer(ILB) de carga interno. Quando o ASE está configurado com um ILB é fornecer:

* os seus próprios domínio ou um subdomínio. Para facilitar, este documento assume subdomínio, mas pode configurá-la qualquer forma. 
* o certificado utilizado para HTTPS
* Gestão de DNS para o subdomínio. 

No return, pode fazer coisas como:

* aplicações de intranet do anfitrião, como de linha de aplicações empresariais, em segurança na nuvem que pode aceder através de um Site a Site ou a VPN do ExpressRoute
* anfitrião de aplicações na nuvem que não estão listadas em servidores DNS públicos
* criar aplicações de back-end de internet isolada que as aplicações de front-end podem integrar com segurança

#### <a name="disabled-functionality"></a>Funcionalidade desativada
Existem algumas coisas que não é possível fazer quando utilizar ILB ASE. Essas ações incluem:

* utilizar IPSSL
* atribuir endereços IP para aplicações específicas
* comprar e utilizar um certificado com uma aplicação através do portal. Obviamente ainda pode obter certificados diretamente com uma autoridade de certificação e utilizá-lo com as suas aplicações, não apenas através do portal do Azure.

## <a name="creating-an-ilb-ase"></a>Criar ASE do ILB
Criar ILB ASE não é muito diferente da criação ASE normalmente. Para ver um debate mais aprofundado sobre a criação de ASE ler [como criar um ambiente de serviço de aplicações][HowtoCreateASE]. O processo de criação ILB ASE é o mesmo entre criar uma VNet durante a criação de ASE ou selecionar uma VNet já existente. Para criar ILB ASE: 

1. No portal do Azure, selecione **novo -> Web + Mobile -> ambiente de serviço de aplicações**
2. Selecione a sua subscrição
3. Selecione ou crie um grupo de recursos
4. Selecione ou crie uma VNet
5. Criar uma sub-rede, se selecionar uma VNet
6. Selecione **Virtual/localização de rede-> Configuração de VNet** e defina o tipo de VIP para interno
7. Forneça o nome de subdomínio (este será o subdomínio utilizado para aplicações criadas neste ASE)
8. Selecione Ok e, em seguida, criar

![][1]

No painel de rede Virtual, não há uma opção de configuração de VNet. Este permite selecionar entre um VIP externo ou o VIP interno. A predefinição é externo. Se tiver definido como externo a ASE utilizará um VIP acessível da internet. Se selecionar interno, o ASE será configurado com um ILB um endereço IP na sua VNet. 

Depois de selecionar interno, a capacidade de adicionar mais endereços IP para o seu ASE é removida e em vez disso, tem de fornecer o subdomínio a ASE. Está num ASE com um VIP externo o nome do ASE é utilizado no subdomínio para aplicações criadas nesse ASE. Se o seu ASE foi chamado ***contosotest*** e a sua aplicação nesse ASE foi chamada ***mytest*** , em seguida, o subdomínio deverá estar no formato ***contosotest.p.azurewebsites.net*** e o URL para essa aplicação seria ***mytest.contosotest.p.azurewebsites.net***. Se definir o tipo de VIP para interno, o ASE nome não é utilizado no subdomínio para o ASE. Especifique o subdomínio explicitamente. Se não tiver sido o subdomínio ***contoso.corp.net*** e tiver efetuado uma aplicação desse ASE denominado ***timereporting*** , em seguida, o URL para essa aplicação seria ***timereporting.contoso.corp.net***.

## <a name="apps-in-an-ilb-ase"></a>Aplicações num ASE do ILB
Criar uma aplicação está num ILB ASE é igual a criar uma aplicação está num ASE normalmente. 

1. No portal do Azure, selecione **novo -> Web + Mobile -> Web** ou **Mobile** ou **aplicação API**
2. Introduza o nome da aplicação
3. Selecionar subscrição
4. Selecionar ou criar o grupo de recursos
5. Selecione ou crie Plan(ASP) do serviço de aplicações. Se criar um novo ASP, em seguida, selecione o seu ASE como a localização e selecione o conjunto de trabalho que pretende que o ASP a serem criadas. Quando cria o ASP, selecionar o ASE como a localização e o conjunto de trabalho. Quando especificar o nome da aplicação, verá que o subdomínio sob o nome da aplicação foi substituído do subdomínio para o seu ASE. 
6. Selecione criar. Deve selecionar o **afixar ao dashboard** caixa de verificação se pretender que a aplicação para apresentar no dashboard. 

![][2]

Sob o nome da aplicação o nome de subdomínio for atualizado para refletir o subdomínio do seu ASE. 

## <a name="post-ilb-ase-creation-validation"></a>Após a validação de criação do ILB ASE
É ligeiramente diferente do que o não - ILB ASE ILB ASE. Assinaladas como já tem de gerir os seus próprios DNS e também tem de fornecer o seu próprio certificado para ligações HTTPS. 

Depois de criar o ASE irá reparar que o subdomínio mostra o subdomínio especificado e não existe um novo item no **definição** menu chamado **ILB certificado**. O ASE é criado com um certificado autoassinado que torna mais fácil testar o HTTPS. O portal irá indicar que tem de fornecer o seu próprio certificado para HTTPS, mas esta é a unidade que tenha um certificado que fique com o subdomínio. 

![][3]

Se simplesmente está a tentar coisas e não souber como criar um certificado, pode utilizar a aplicação de consola do MMC do IIS para criar um certificado autoassinado. Quando for criado pode exportá-lo como um ficheiro. pfx e, em seguida, carregue-o na IU de certificado do ILB. Ao aceder a um site protegido com um certificado autoassinado, o seu browser irá dar-lhe um aviso se o site que está a aceder ao não é seguro devido a incapacidade para validar o certificado. Se quiser evitar este aviso é necessário um certificado assinado devidamente que corresponda ao seu subdomínio e tem uma cadeia de fidedignidade que é reconhecida pelo browser.

![][6]

Se pretender que o fluxo com os seus próprios certificados e testar o acesso HTTP e HTTPS para o seu ASE:

1. Aceda a IU ASE após a criação ASE **ASE -> Definições -> certificados do ILB**
2. Definir o certificado do ILB, selecionando o ficheiro pfx de certificado e fornecer a palavra-passe. Este passo demora um bocado para processar e será apresentada a mensagem está em curso uma operação de dimensionamento.
3. Obter o endereço do ILB para sua ASE (**ASE -> propriedades -> endereço IP Virtual**)
4. Criar uma aplicação web num ASE após a criação 
5. Criar uma VM, se não tiver um nessa VNET (não se encontra na mesma sub-rede que o quebra ASE ou coisas)
6. Definir o DNS para o subdomínio. Pode utilizar um caráter universal com o subdomínio no seu DNS ou se quiser fazer alguns testes simples, edite o ficheiro de anfitriões na sua VM ao definir o nome da aplicação web para o endereço IP de VIP. Se o seu ASE tinha o nome de subdomínio. ilbase.com e efetuadas mytestapp de aplicação web para que poderia ser resolvido em mytestapp.ilbase.com em seguida, definir que no seu ficheiro de anfitriões. (No Windows o ficheiro de anfitriões está no C:\Windows\System32\drivers\etc\)
7. Utilizar um browser nessa VM e aceda a http://mytestapp.ilbase.com (ou o que é o nome da aplicação web com o subdomínio)
8. Utilizar um browser nessa VM e aceda a https://mytestapp.ilbase.com que terá de aceitar a falta de segurança, se utilizar um certificado autoassinado. 

O endereço IP para o ILB está listado nas suas propriedades como o endereço IP Virtual

![][4]

## <a name="using-an-ilb-ase"></a>Utilizar ASE do ILB
#### <a name="network-security-groups"></a>Grupos de Segurança de Rede
ILB ASE permite isolamento de rede para as suas aplicações, como as aplicações não são acessíveis ou mesmo conhecidos através da internet. Este é excelente para alojar sites da intranet, tais como aplicações empresariais de linha de. Quando necessitar de restringir o acesso ao mesmo mais pode continuar a utilizar Groups(NSGs) de segurança de rede para controlar o acesso ao nível da rede. 

Se desejar utilizar os NSGs para restringir mais o acesso, em seguida, tem de certificar-se de que não a interromper a comunicação que precisa do ASE para funcionar. Apesar do acesso HTTP/HTTPS é apenas através do ILB utilizado pelo ASE o ASE ainda depende de recursos fora da VNet. Para ver o acesso de rede é ainda necessário procure as informações no documento no [controlar o tráfego de entrada para um ambiente de serviço de aplicações] [ ControlInbound] e o documento no [detalhes de configuração de rede para ambientes do App Service com o ExpressRoute][ExpressRoute]. 

Para configurar os seus NSGs precisa de saber o endereço IP que é utilizado pelo Azure para gerir a sua ASE. Esse endereço IP também é o endereço IP de saída da sua ASE se faz pedidos de internet. O endereço IP de saída para a sua ASE permanecerá estático para a vida do seu ASE. Se eliminar e recriar a ASE, irá obter um novo endereço IP. Para localizar este endereço IP, aceda a **definições -> propriedades** e localize o **saída endereço de IP**. 

![][5]

#### <a name="general-ilb-ase-management"></a>Gestão de geral ILB ASE
Gerir ILB ASE amplamente é igual a gerir ASE normalmente. Terá de aumentar verticalmente os conjuntos de trabalho para alojar mais instâncias ASP e dimensionar os servidores de Front-End para processar maiores quantidades de tráfego HTTP/HTTPS. Para informações gerais sobre como gerir a configuração de ASE, leia o documento no [configurar um ambiente de serviço de aplicações][ASEConfig]. 

Os itens de gestão adicionais são gestão de certificados e gestão de DNS. Terá de obter e carregar o certificado utilizado para HTTPS após a criação do ILB ASE e substitua-o antes de expirar. Porque o Azure pertence o domínio base podermos fornecer certificados para ASEs com um VIP externo. Uma vez que o subdomínio utilizado pelo ILB ASE pode ser qualquer coisa, é necessário fornecer o seu próprio certificado para HTTPS. 

#### <a name="dns-configuration"></a>Configuração de DNS
Quando utilizar um VIP externo, o DNS é gerido pelo Azure. Qualquer aplicação que criou no seu ASE é automaticamente adicionada ao DNS do Azure que é um DNS público. Está num ILB ASE tem de gerir os seus próprios DNS. Para um determinada subdomínio como contoso.corp.net tem de criar DNS A registos que apontem para o seu endereço ILB para:

    * 
    publicar *.SCM ftp 


## <a name="getting-started"></a>Introdução
Para começar com ambientes do App Service, consulte o artigo [introdução para ambientes do App Service][WhatisASE]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-environment-with-internal-load-balancer/ilbase-createilbase.png
[2]: ./media/app-service-environment-with-internal-load-balancer/ilbase-createapp.png
[3]: ./media/app-service-environment-with-internal-load-balancer/ilbase-newase.png
[4]: ./media/app-service-environment-with-internal-load-balancer/ilbase-vip.png
[5]: ./media/app-service-environment-with-internal-load-balancer/ilbase-externalvip.png
[6]: ./media/app-service-environment-with-internal-load-balancer/ilbase-ilbcertificate.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[ControlInbound]: app-service-app-service-environment-control-inbound-traffic.md
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[ASEAutoscale]: app-service-environment-auto-scale.md
[ExpressRoute]: app-service-app-service-environment-network-configuration-expressroute.md
[vnetnsgs]: http://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[ASEConfig]: app-service-web-configure-an-app-service-environment.md
