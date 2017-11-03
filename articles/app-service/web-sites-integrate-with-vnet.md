---
title: "Integrar uma aplicação com uma Azure Virtual Network"
description: "Mostra como ligar uma aplicação no App Service do Azure a uma rede de virtual do Azure nova ou existente"
services: app-service
documentationcenter: 
author: ccompy
manager: erikre
editor: cephalin
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2017
ms.author: ccompy
ms.openlocfilehash: 72ff0c13319218f8ef91aff9208772fcb0fd9459
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Integrar a sua aplicação com uma Azure Virtual Network
Este documento descreve a funcionalidade de integração de rede virtual do App Service do Azure e mostra como configurá-lo com aplicações [App Service do Azure](http://go.microsoft.com/fwlink/?LinkId=529714). Se estiver familiarizado com o Azure redes virtuais (VNets), esta é uma funcionalidade que permite-lhe colocar muitos dos seus recursos do Azure numa rede routeable não internet que controla o acesso a. Estas redes, em seguida, podem ser ligadas a suas redes no local utilizando uma variedade de tecnologias VPN. Para saber mais sobre redes virtuais do Azure, comece com as informações aqui: [descrição geral de rede Virtual do Azure][VNETOverview]. 

O App Service do Azure tem duas formas. 

1. Os sistemas de multi-inquilinos que suportam a gama completa de planos de preços
2. A funcionalidade de premium ambiente de serviço de aplicações (ASE), que implementa na sua VNet. 

Este documento vai através da integração de VNet e não o ambiente de serviço de aplicações. Se pretender saber mais sobre a funcionalidade de ASE, comece por aqui a informação de: [introdução de ambiente de serviço de aplicações][ASEintro].

Integração de VNet permite o acesso de aplicação web a recursos na sua rede virtual, mas não concede acesso privado à sua aplicação web de rede virtual. Acesso de site privada refere-se para tornar a sua aplicação só é acessível a partir de uma rede privada, tal como a partir de dentro de uma rede virtual do Azure. Acesso de site privada só está disponível com ASE configurado com um interno carga balanceador (ILB). Para obter detalhes sobre como utilizar ILB ASE, comece com o artigo aqui: [criar e utilizar ILB ASE][ILBASE]. 

Um cenário comum, onde seria utilizar a integração de VNet é permitir o acesso da sua aplicação web a uma base de dados ou um serviço web em execução numa máquina virtual na sua rede virtual do Azure. Com a integração de VNet, não precisa de expor um ponto final público para aplicações na sua VM, mas pode utilizam os endereços de encaminháveis privados não internet em vez disso. 

A funcionalidade de integração da VNet:

* requer um Standard, Premium ou Isolated plano de preços 
* funciona com clássico ou VNet do Resource Manager 
* suporta TCP e UDP
* funciona com aplicações Web, móveis e API
* permite que uma aplicação para ligar a VNet apenas 1 numa altura
* permite até cinco VNets ser integrado num plano do App Service 
* permite que a mesma VNet a ser utilizado por várias aplicações num plano do App Service
* suporta um SLA de 99,9% devido ao SLA no Gateway de VNet

Existem algumas coisas que integração VNet não suporta, incluindo:

* montar a unidade
* Integração do AD 
* NetBios
* acesso de site privada

### <a name="getting-started"></a>Introdução
Eis algumas coisas a lembrar antes de ligar a sua aplicação web a uma rede virtual:

* Integração de VNet só funciona com aplicações num **padrão**, **Premium**, ou **Isolated** plano de preços. Se ativar a funcionalidade e, em seguida, dimensionar o seu plano do App Service para um plano de preços não suportado das suas aplicações perdem as suas ligações para as VNets que estão a utilizar. 
* Se a rede virtual de destino já existir, tem de ter VPN ponto a site ativada com um gateway de encaminhamento dinâmico antes de pode ser ligado a uma aplicação. Se o gateway está configurado com o encaminhamento estático, não é possível ativar o ponto a site rede privada Virtual (VPN).
* A VNet tem de ser na mesma subscrição do seu Plan(ASP) do serviço de aplicações. 
* As aplicações que se integram com uma utilização de VNet DNS especificado para essa VNet.
* Por predefinição é as suas aplicações integrating apenas encaminhar o tráfego para a VNet com base nas rotas que estão definidas na sua VNet. 

## <a name="enabling-vnet-integration"></a>Ativar a integração de VNet

Tem a opção para ligar a aplicação a uma rede virtual nova ou existente. Se criar uma nova rede como parte da sua integração, em seguida, para além de criar apenas a VNet, um gateway de encaminhamento dinâmico está pré-configurada para si e ponto a Site VPN estiver ativado. 

> [!NOTE]
> Configurar uma nova integração de rede virtual pode demorar alguns minutos. 
> 
> 

Para ativar a integração de VNet, abra a aplicação definições e, em seguida, selecione o funcionamento em rede. A IU que se abre oferece três opções de rede. Neste guia apenas será para integração de VNet apesar ligações híbridas e ambientes do App Service são abordados posteriormente neste documento. 

Se a aplicação é não o estão corretas preços plano, a IU permite-lhe dimensionar o seu plano para um plano de preços superior à sua escolha.

![][1]

### <a name="enabling-vnet-integration-with-a-pre-existing-vnet"></a>Ativar a integração de VNet com uma VNet já existente
A IU de integração de VNet permite-lhe selecionar numa lista das suas VNets. As VNets clássico indicam que estão, tais com a palavra "Clássico" parênteses junto ao nome da VNet. A lista é ordenada de forma a que as VNets do Resource Manager são apresentadas primeiro. Na imagem abaixo, pode ver o que pode ser selecionada apenas uma VNet. Existem várias razões que uma VNet pode ser a cinzento, incluindo:

* está a ser outra subscrição que a conta tem acesso para a VNet
* a VNet não tem ponto a Site ativada
* a VNet não tem um gateway de encaminhamento dinâmico

![][2]

Para ativar a integração clique na VNet que pretende integrar. Depois de selecionar a VNet, a aplicação é automaticamente reiniciada para que as alterações entrem em vigor. 

##### <a name="enable-point-to-site-in-a-classic-vnet"></a>Ativar ponto a Site de uma VNet clássica
Se a sua VNet não tem um gateway nem tem ponto a Site, em seguida, tem de configurar essa opção primeiro. Para fazê-lo para uma VNet clássica, vá para o [portal do Azure] [ AzurePortal] e trazer a lista de Virtual Networks(classic). Aqui, clique na rede que pretende integrar e clique na caixa de macrocomputação em Essentials chamado ligações VPN. Aqui, pode criar o ponto de VPN de site e, mesmo que crie um gateway. Depois de percorrer o ponto a site com a experiência de criação do gateway é cerca de 30 minutos antes de ser preparado. 

![][8]

##### <a name="enabling-point-to-site-in-a-resource-manager-vnet"></a>Ativar o ponto a Site de uma VNet do Resource Manager
Para configurar uma VNet do Resource Manager com um gateway e um ponto a Site, pode utilizar o PowerShell como documentadas aqui, [configurar uma ligação ponto a Site a uma rede virtual com o PowerShell] [ V2VNETP2S] ou utilizar o portal do Azure conforme documentado aqui [configurar uma ligação ponto a Site para uma VNet no portal do Azure][V2VNETPortal]. A IU para efetuar esta capacidade ainda não está disponível. Tenha em atenção que tem de criar os certificados para o ponto de configuração do Site. Isto é configurado automaticamente quando se liga a WebApp para a VNet. 

### <a name="creating-a-pre-configured-vnet"></a>Criar uma VNet pré-configurada
Se pretender criar uma nova VNet que está configurada com um gateway e ponto a Site, o serviço de aplicações da IU de rede tem a capacidade de fazê-lo, mas apenas para uma VNet do Resource Manager. Se pretender criar uma VNet clássica com um gateway e o ponto a Site, terá de fazê-lo manualmente através da interface de utilizador de rede. 

Para criar uma VNet do Resource Manager através da IU de integração da VNet, basta selecionar **criar nova rede Virtual** e forneça o:

* Nome da rede virtual
* Bloco de endereços de rede virtual
* Nome da sub-rede
* Bloco de endereços de sub-rede
* Bloco de endereços de gateway
* Bloco de endereços de ponto a Site

Se pretender que esta VNet para ligar a quaisquer outras redes, em seguida, deve evitar diretriz espaço de endereços IP que se sobreponha essas redes. 

> [!NOTE]
> Criação de VNet do Resource Manager com um gateway demora cerca de 30 minutos e atualmente não integrar a VNet com a sua aplicação. Depois de é criada a VNet com o gateway tem de voltar a sua aplicação IU de integração de VNet e selecione a sua nova VNet.
> 
> 

![][3]

Normalmente, as VNets do Azure são criadas dentro de endereços de rede privada. Por predefinição, a integração de VNet funcionalidade encaminha o tráfego destinado os intervalos de endereços IP para a VNet. Os intervalos de endereços IP privados são:

* 10.0.0.0/8 - Este é o mesmo 10.0.0.0 - 10.255.255.255
* 172.16.0.0/12 - este é o mesmo 172.16.0.0 - 172.31.255.255 
* 192.168.0.0/16 - este é o mesmo 192.168.0.0 - 192.168.255.255

O espaço de endereços VNet tem de ser especificado em notação CIDR. Se não estiver familiarizado com notação CIDR, é um método para especificar blocos de endereços com um endereço IP e um número inteiro que representa a máscara de rede. Como uma referência rápida, tenha em atenção que 10.1.0.0/24 seria 256 endereços e 10.1.0.0/25 seria 128 endereços. Um endereço IPv4 com uma /32 seria apenas 1 endereço. 

Se definir as informações do servidor DNS aqui, que é definida para a sua VNet. Após a criação da VNet pode editar estas informações a partir das experiências de utilizador da VNet. Se alterar o DNS da VNet, terá de efetuar uma operação de sincronização de rede.

Quando cria uma VNet clássica, utilizando a IU de integração da VNet, cria uma VNet no mesmo grupo de recursos que a sua aplicação. 

## <a name="how-the-system-works"></a>Como funciona o sistema
Nos bastidores, esta funcionalidade baseia-se em cima de tecnologia VPN de ponto a Site para ligar a aplicação para sua VNet. As aplicações no App Service do Azure têm uma arquitetura de sistema de multi-inquilino, o que sequência exclui aprovisionamento uma aplicação diretamente numa VNet, como é feito com máquinas virtuais. Ao criar a tecnologia de ponto a site limitamos acesso à rede para apenas a máquina virtual que aloja a aplicação. Acesso à rede é mais restrito nesses anfitriões de aplicação para que as suas aplicações só podem aceder as redes que configurou-lhes aceder. 

![][4]

Se ainda não configurou um servidor DNS com a sua rede virtual, a sua aplicação terá de utilizar endereços IP para aceder aos recursos na VNet. Ao utilizar endereços IP, lembre-se de que a principal vantagem desta funcionalidade é que permite-lhe utilizar os endereços privados dentro da sua rede privada. Se definir a aplicação até a utilização de endereços IP público para uma das suas VMs, em seguida, que não está a utilizar a funcionalidade de integração de VNet e está a comunicar através da internet.

## <a name="managing-the-vnet-integrations"></a>Gerir as integrações de VNet
A capacidade de ligar e desligar para uma VNet é um nível de aplicação. As operações que podem afetar a integração de VNet por várias aplicações são um nível de ASP. Na IU do que é apresentada ao nível da aplicação, pode obter os detalhes na sua VNet. A maioria dessas mesmas informações também é apresentado ao nível do ASP. 

![][5]

Na página Estado da funcionalidade de rede, pode ver se a aplicação está ligada à sua VNet. Se o gateway de VNet para baixo por qualquer motivo, em seguida, isto seria Mostrar ligado como não. 

As informações que tem agora disponíveis para si na aplicação que nível IU de integração de VNet é o mesmo que as informações de detalhe que aproveitar o ASP. Seguem-se esses itens:

* Nome da VNet - esta ligação abre a IU de rede virtual do Azure
* Localização - isto reflete a localização da sua VNet. É possível integrar uma VNet noutra localização.
* Estado de certificado - não existem certificados utilizados para proteger a ligação VPN entre a aplicação e a VNet. Isto reflete um teste para garantir que estão sincronizadas.
* Estado do gateway - devem gateways de estar inativo por qualquer motivo, em seguida, a aplicação não é possível aceder aos recursos na VNet. 
* Espaço de endereços da VNet - este é o espaço de endereços IP para a sua VNet. 
* Aponte para o espaço de endereços do Site - este é o ponto de espaço de endereços IP de site para a sua VNet. A aplicação mostra comunicação como proveniente de um dos IPs neste espaço de endereço. 
* Site ao espaço de endereços do site - pode utilizar VPNs de VPNs para ligar a VNet para os recursos no local ou para outra VNet. Deve ter configurado, em seguida, os intervalos de IP definidas com que a ligação de VPN mostra aqui.
* Servidores DNS, se tiver servidores DNS configurados com a VNet, em seguida, são listados aqui.
* IPs encaminhados para a VNet - daí são uma lista de endereços IP com a VNet encaminhamento definida para e essas Mostrar endereços aqui. 

A única operação que pode demorar na vista de aplicação de integração a VNet é desligue a VNet está actualmente ligado a sua aplicação. Para fazer isto basta clicar em Desligar na parte superior. Esta ação não altera a sua VNet. A VNet e a respetiva configuração, incluindo os gateways permanece inalterado. Se pretender, em seguida, elimine a sua VNet, terá de eliminar primeiro os recursos, incluindo os gateways. 

A vista do plano do App Service tem um número de operações adicionais. É também acedido forma diferente a partir da aplicação. Para alcançar a IU de redes de ASP basta abrir o seu ASP IU e desloque-se para baixo. Não há um elemento de IU chamado estado da funcionalidade de rede. Proporciona alguns detalhes secundárias à volta a integração de VNet. Clicar na IU deste abre a IU de estado da funcionalidade de rede. Se, em seguida, clicar em "Clique aqui para gerir", abre a IU apresenta uma lista de integrações de VNet neste ASP.

![][6]

A localização de ASP está boa lembrar-se ao observar as localizações das VNets que está a integrar. Quando a VNet é noutra localização são muito mais provável ver os problemas de latência. 

As VNets integrada é um lembrete em VNets quantos que as suas aplicações estão integradas com este ASP e quantos pode ter. 

Para ver detalhes adicionados cada vnet, basta clicar na VNet que está interessado. Além dos detalhes que foram anotou anteriormente, também pode ver uma lista de aplicações deste ASP que estão a utilizar essa VNet. 

Relativamente a ações existem duas ações primárias. A primeira é a capacidade de adicionar as rotas que a unidade de tráfego que sai a sua aplicação na sua VNet. A segunda ação é a capacidade para sincronizar informações de rede e de certificados.

![][7]

**Encaminhamento** conforme indicado anteriormente, as rotas que estão definidas na sua VNet são o que é utilizado para instruir o tráfego para a VNet a partir da sua aplicação. Existem algumas utilizações embora onde os clientes pretendem enviar tráfego de saída adicional a partir de uma aplicação para a VNet e para os mesmos esta capacidade é fornecido. O que acontece ao tráfego depois até como o cliente configura a sua VNet. 

**Certificados** o estado do certificado reflete uma verificação de que está a ser efetuada pelo serviço de aplicações para validar os certificados que estamos a utilizar para a ligação VPN estão ainda boas. Quando a integração de VNet ativada, em seguida, se esta for a primeira integração a essa VNet de todas as aplicações neste ASP, há um exchange necessário certificados para garantir a segurança da ligação. Juntamente com os certificados estamos a obter a configuração de DNS, as rotas e outras coisas semelhantes que descrevem a rede.
Se esses certificados ou informações de rede for alterado, em seguida, terá de clicar em "Sync rede". **Tenha em atenção**: quando clicar em "Sync rede", em seguida, o fazer com que um breve tempo de indisponibilidade na conectividade entre a sua aplicação e a sua VNet. Enquanto a aplicação não for reiniciada, a perda de conectividade pode fazer com que o site não funcione corretamente. 

## <a name="accessing-on-premises-resources"></a>Aceder a recursos no local
Uma das vantagens da funcionalidade de integração de VNet é que, se a VNet está ligada à sua rede no local com uma VPN de Site a Site, em seguida, as aplicações podem ter acesso aos seus recursos no local da sua aplicação. Para isto funcionar, embora poderá ter de atualizar o gateway de VPN no local com as rotas para o ponto de Site IP no intervalo. Quando a VPN de Site a Site é configurada pela primeira vez, em seguida, os scripts utilizados para configurar, devem configurar rotas, incluindo o seu ponto de VPN de Site. Se adicionar o ponto de VPN de Site depois de criar a VPN de Site a Site, em seguida, terá de atualizar manualmente as rotas. Detalhes sobre como fazê-lo variam por gateway e não são descritos aqui. 

> [!NOTE]
> A funcionalidade de integração de VNet integra a uma aplicação com uma VNet com um Gateway do ExpressRoute. Mesmo se o Gateway do ExpressRoute estiver configurado no [modo de coexistência] [ VPNERCoex] a integração de VNet não funciona. Se precisa de aceder a recursos através de uma ligação ExpressRoute, em seguida, pode utilizar um [ambiente de serviço de aplicações][ASE], que é executada na sua VNet.
> 
> 

## <a name="pricing-details"></a>Detalhes dos preços
Existem algumas preços nuances que deve ter em mente quando utilizar a funcionalidade de integração de VNet. Existem 3 custos relacionados com a utilização desta funcionalidade:

* ASP requisitos de escalão de preço
* Custos da transferência de dados
* Custos de Gateway de VPN.

Para as suas aplicações para poder utilizar esta funcionalidade, necessitam de ser num padrão ou plano de serviço de aplicações do Premium. Pode ver mais detalhes nesses custos aqui: [preços do App Service][ASPricing]. 

Devido a forma como ponto a Site VPNs são processados, terá sempre cobrada uma taxa para dados de saída através da ligação de VNet integração, mesmo que esteja a VNet no mesmo centro de dados. Para ver quais são os encargos, veja aqui: [detalhes de preços de transferência de dados][DataPricing]. 

O último item é o custo dos gateways de VNet. Se não precisa de gateways para autorize como VPNs de Site a Site, são pagar para gateways suportar a funcionalidade de integração de VNet. Não existem detalhes nesses custos aqui: [preços do VPN Gateway][VNETPricing]. 

## <a name="troubleshooting"></a>Resolução de problemas
Enquanto a funcionalidade é fácil de configurar, significa que não que a sua experiência será problema livre. Deve encontrar problemas de acesso à sua endpoint pretendido existe são alguns utilitários que pode utilizar para testar a conectividade a partir da consola de aplicação. Existem duas experiências de consola, que pode utilizar. Um é a partir da consola do Kudu e o outro é a consola que pode alcançar no portal do Azure. Para obter na consola do Kudu da sua aplicação, vá a ferramentas -> Kudu. Este é o mesmo que vai [sitename]. scm.azurewebsites.net. Uma vez que abre simplesmente aceda ao separador consola de depuração. Para obter a consola alojada portal do Azure, em seguida, da sua aplicação, vá a ferramentas -> consola. 

#### <a name="tools"></a>Ferramentas
O ping de ferramentas, nslookup e tracert não funcionará através da consola devido a restrições de segurança. Para preencher existe void foram duas ferramentas separadas adicionadas. Para testar a funcionalidade DNS adicionámos uma ferramenta com o nome nameresolver.exe. A sintaxe é:

    nameresolver.exe hostname [optional: DNS Server]

Pode utilizar nameresolver para verificar os nomes de anfitriões que depende da sua aplicação. Desta forma pode testar se tiver nada incorretamente configuradas com o DNS ou talvez não tem acesso ao seu servidor DNS.

A seguinte ferramenta permite-lhe testar a conectividade de TCP para uma combinação de porta e anfitrião. Esta ferramenta é chamada tcpping.exe e a sintaxe é:

    tcpping.exe hostname [optional: port]

O utilitário de tcpping indica se pode aceder uma porta e anfitrião específico. Esta operação só pode mostrar êxito se: existe uma aplicação à escuta na combinação de porta e anfitrião, e não existe acesso à rede da sua aplicação para a porta e o anfitrião especificado.

#### <a name="debugging-access-to-vnet-hosted-resources"></a>Acesso a VNet a depuração recursos alojados
Existem várias coisas que pode impedir que a aplicação a atingir uma porta e anfitrião específico. Na maioria das vezes, é um dos três coisas:

* **Há uma firewall da forma** se tiver uma firewall da forma, irá atingiu o tempo limite TCP. Neste caso, que é 21 segundos. Utilize a ferramenta de tcpping para testar a conectividade. Tempos limite TCP podem dever-se inúmeros aspetos, para além de firewalls, mas não existe a iniciar. 
* **DNS não está acessível** DNS o limite de tempo é de três segundos por servidor DNS. Se tiver dois servidores DNS, o tempo limite é 6 segundos. Utilize nameresolver para ver se o DNS está a funcionar. Lembre-se de que não é possível utilizar nslookup, que não utiliza o DNS a VNet está configurada com.
* **Intervalo de P2S IP inválido** o ponto de intervalo IP do site tem de ser nos intervalos IP privados RFC 1918 (10.0.0.0-10.255.255.255 / 172.16.0.0-172.31.255.255 / 192.168.0.0-192.168.255.255). Se o intervalo utiliza IPs fora que, em seguida, poderá não funcionam coisas. 

Se o problema não responder a esses itens, procure primeiro para as coisas simples, como: 

* O Gateway mostra como sendo a cópia de segurança no portal?
* Certificados Mostrar como estando em sincronização?
* Qualquer pessoa alterar a configuração de rede sem fazer um "rede sincronização" os ASPs afetados? 

Se o gateway estiver em baixo, em seguida, colocá-lo uma cópia de segurança. Se os certificados estão fora de sincronização, em seguida, vá para a vista ASP da integração de VNet e prima "Sync rede". Se suspeitar que tiver ocorrido uma alteração efetuada à sua configuração de VNet e não sincronização faria com sua ASPs, em seguida, vá para a vista ASP da integração de VNet e prima "Sync rede" apenas como um lembrete, isto faz com que um breve tempo de indisponibilidade com a ligação de VNet e as suas aplicações. 

Se tudo o que é adequado, em seguida, são necessárias aprofundar um pouco:

* Existem todas as outras aplicações utilizando a integração de VNet para aceder aos recursos na mesma VNet? 
* Pode ir para a consola de aplicação e utilizar tcpping para aceder a quaisquer outros recursos na sua VNet? 

Se qualquer uma das respostas anteriores forem verdadeira, em seguida, a integração de VNet é adequada e o problema é algures pessoa. Trata-se em que obtém ser mais um desafio porque não existe nenhuma forma simple para ver o motivo pelo qual não é possível contactar uma anfitrião: porta. Algumas das causas incluem:

* tiver uma firewall cópias de segurança no seu anfitrião impedir o acesso à porta de aplicação do ponto de para o intervalo IP do site. Cruzamento entre sub-redes, muitas vezes, precisa de acesso de público.
* o anfitrião de destino está inativo
* a aplicação está inativo
* teve o IP ou nome de anfitrião errado
* a aplicação está a escutar numa porta diferente que é esperado. Pode verificar isto vai no anfitrião e utilizando "netstat - aon" da linha de comandos. Isto mostra que processo está a escutar na porta que ID. 
* os grupos de segurança de rede estão configurados de forma a que possam impedem o acesso ao seu anfitrião de aplicações e a porta do ponto de para o intervalo IP do site

Lembre-se de que não souber que IP no seu ponto ao intervalo de IP do Site que a aplicação utilizará, por isso terá de permitir o acesso a partir do intervalo completo. 

Passos de depuração adicionais incluem:

* Inicie sessão no outra VM na sua VNet e tentam aceder a recursos do anfitrião: porta partir daí. Existem algumas utilitários de ping TCP que pode utilizar para esta finalidade ou mesmo pode utilizar o telnet se necessário. O objetivo aqui é simplesmente para determinar se conectividade existe este outros VM. 
* trazer uma aplicação no outro acesso VM e de teste para esse anfitrião e a porta da consola da sua aplicação

#### <a name="on-premises-resources"></a>Recursos no local ####
Se a aplicação não é possível aceder a recursos no local, em seguida, a primeira coisa que deverá verificar é se pode aceder a um recurso na sua VNet. Se o que funciona, tente aceder a aplicações no local de uma VM na VNet. Pode utilizar o telnet ou um utilitário de ping TCP. Se a VM não é possível aceder o recursos no local, em seguida, certifique-se que a ligação de VPN de Site a Site está a funcionar. Se está a funcionar, em seguida, verifique as mesmas coisas que anotou anteriormente, bem como a configuração do gateway no local e o estado. 

Agora se a sua VNet alojada VM pode alcançar o sistema local, mas a aplicação não é possível, em seguida, o motivo é, provavelmente, uma das seguintes opções:

* as rotas não estão configuradas com o ponto para intervalos IP do site no seu gateway no local
* os grupos de segurança de rede estão a bloquear o acesso para o ponto de intervalo de IP do Site
* das firewalls no local estão a bloquear o tráfego do ponto de para o intervalo de IP do Site
* tiver uma Route(UDR) do utilizador definida na sua VNet, que impede que o ponto de para o tráfego do Site com base em atingir a sua rede no local

## <a name="hybrid-connections-and-app-service-environments"></a>As ligações híbridas e ambientes de serviço de aplicações
Existem três funcionalidades que permitem o acesso aos recursos da VNet alojado. São:

* Integração de VNet
* Ligações Híbridas
* Ambientes do App Service

As ligações híbridas requer a instalar um agente de reencaminhamento chamado Manager(HCM) de ligação híbrida na sua rede. O HCM tem de ser capaz de ligar para o Azure e também a sua aplicação. Esta solução é especialmente excelente de uma rede remota, tais como a sua rede no local ou mesmo outra nuvem alojado rede porque que não é necessário um ponto final acessível de internet. O HCM é executado apenas no Windows e pode ter até cinco instâncias em execução para fornecer elevada disponibilidade. Ligações híbridas suporta apenas TCP apesar e cada ponto final HC tem de corresponder a uma combinação de anfitrião: porta específica. 

A funcionalidade de ambiente de serviço de aplicações permite-lhe executar uma instância do serviço de aplicações do Azure na sua VNet. Isto permite aos seus recursos de acesso de aplicações na sua VNet sem quaisquer passos adicionais. São algumas das outras vantagens de um ambiente de serviço de aplicações que pode utilizar trabalhadores Dv2 com base com 14 GB de RAM. Outra vantagem é que pode dimensionar o sistema para satisfazer as necessidades. Ao contrário dos ambientes de multi-inquilinos onde o ASP está limitado a 20 instâncias num ASE pode dimensionar até 100 instâncias ASP. Uma das ações que obtém com ASE não com a integração de VNet é que um ambiente de serviço de aplicações pode trabalhar com uma VPN do ExpressRoute. 

Enquanto existir que algumas utilizam sobreposição maiúsculas, nenhuma destas funcionalidades pode substituir qualquer um dos outros. Saber que o recurso a usar está associado às suas necessidades. Por exemplo:

* Se for um programador e simplesmente querer executar um site no Azure e que o acesso a base de dados na estação de trabalho em seu suporte técnico, a mais fácil coisa a utilizar é ligações híbridas. 
* Se uma organização grande que pretende colocar um grande número de propriedades web público na nuvem e geri-los na sua própria rede, em seguida, pretende aceder com o ambiente de serviço de aplicações. 
* Se tiver um número de serviço de aplicações alojadas aplicações e simplesmente pretendem aceder a recursos na sua VNet, em seguida, integração de VNet é a forma de aceder. 

Para além de casos de utilização, existem algumas simplicidade relacionadas com aspetos. Se a sua VNet já está ligada à sua rede no local, em seguida, utilizando a integração de VNet ou de um ambiente de serviço de aplicações é uma forma fácil para consumir recursos no local. Por outro lado, se a sua VNet não está ligada à sua rede no local, em seguida, é muito mais overhead para configurar uma VPN de site para site com a VNet comparada com a instalação de HCM. 

Para além das diferenças funcionais, não existe são também preços diferenças. A funcionalidade de ambiente de serviço de aplicações é um Premium oferta de serviço oferece, mas mais rede as possibilidades de configuração para além de outras funcionalidades excelentes. Integração de VNet pode ser utilizada com Standard ou Premium ASPs e é perfeita para consumir forma segura a recursos na sua VNet a partir do multi-inquilino do serviço de aplicações. As ligações híbridas atualmente depende de uma conta, que níveis que começam com livre e, em seguida, obter progressivamente mais dispendioso de preços com base no que precisa de BizTalk. Quando se trata de trabalhar em redes com muitos apesar, não há nenhum outras funcionalidades, como ligações híbridas, o qual pode permitir-lhe aceder a recursos em redes separadas bem mais de 100. 

<!--Image references-->
[1]: ./media/web-sites-integrate-with-vnet/vnetint-upgradeplan.png
[2]: ./media/web-sites-integrate-with-vnet/vnetint-existingvnet.png
[3]: ./media/web-sites-integrate-with-vnet/vnetint-createvnet.png
[4]: ./media/web-sites-integrate-with-vnet/vnetint-howitworks.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-appmanage.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-aspmanage.png
[7]: ./media/web-sites-integrate-with-vnet/vnetint-aspmanagedetail.png
[8]: ./media/web-sites-integrate-with-vnet/vnetint-vnetp2s.png

<!--Links-->
[VNETOverview]: http://azure.microsoft.com/documentation/articles/virtual-networks-overview/ 
[AzurePortal]: http://portal.azure.com/
[ASPricing]: http://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: http://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: http://azure.microsoft.com/pricing/details/data-transfers/
[V2VNETP2S]: http://azure.microsoft.com/documentation/articles/vpn-gateway-howto-point-to-site-rm-ps/
[ASEintro]: environment/intro.md
[ILBASE]: environment/create-ilb-ase.md
[V2VNETPortal]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md
[VPNERCoex]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[ASE]: environment/intro.md
