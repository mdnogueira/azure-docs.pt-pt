---
title: "Configurar o ambiente de serviço de aplicações do Azure para ser forçada em túnel"
description: "Ativar o ASE funcione ao tráfego de saída foi forçado em túnel"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 384cf393-5c63-4ffb-9eb2-bfd990bc7af1
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/10/2017
ms.author: ccompy
ms.openlocfilehash: cd89bd23074dec1de6fa0e8784d42a5c539938b1
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2017
---
# <a name="configure-your-app-service-environment-with-forced-tunneling"></a>Configurar o ambiente de serviço de aplicações com a imposição do túnel

O ambiente de serviço de aplicações (ASE) é uma implementação do serviço de aplicações do Azure numa rede Virtual de um cliente do Azure (VNet). Muitos clientes configurar as respetivas VNets ser extensões de redes no local com VPNs ou ExpressRoute ligações. Devido a políticas empresariais ou outras limitações de segurança, se configurarem rotas para enviar todas as tráfego de saída no local antes-pode aceder a à internet. Alterar o encaminhamento da VNet, para que o tráfego de saída de VNet flui através da ligação VPN ou ExpressRoute para o local é denominado a imposição do túnel.  

Imposição do túnel pode causar problemas de ASE. O ASE tem um número de dependências externas que são enumerado deste [arquitetura de rede ASE] [ network] documento. ASE, por predefinição, requer que todas as comunicações de saída que atravessa o VIP que está aprovisionado com o ASE.

As rotas são um aspeto fundamental forçado túnel Novidades e saber como lidar com o mesmo. Numa rede Virtual do Azure, o encaminhamento é efetuado com base em mais longo do prefixo da correspondência (LPM).  Se existir mais do que uma rota com a mesma correspondência LPM, em seguida, uma rota é selecionada com base na origem pela seguinte ordem:

1. Rota definida pelo utilizador
1. Rota BGP (quando é utilizado o ExpressRoute)
1. Rota de sistema

Para saber mais sobre o encaminhamento numa VNet, leia [definidas pelo utilizador rotas e reencaminhamento IP][routes]. 

Se pretender que o seu ASE operar num túnel forçado VNet, tem duas opções:

1. Ativar o ASE tenham acesso direto à internet
1. Alterar o ponto final de saída para a sua ASE

## <a name="enable-your-ase-to-have-direct-internet-access"></a>Ativar o ASE tenham acesso direto à internet

Para sua ASE funcionar enquanto a VNet está configurada com o ExpressRoute, pode:

* Configure o ExpressRoute para anunciar 0.0.0.0/0. Por predefinição, este forçar túneis toda a saída de tráfego no local.
* Crie um UDR. Aplicá-la para a sub-rede que contém o ASE com um prefixo de endereço de 0.0.0.0/0 e um tipo de próximo salto de Internet.

Se efetuar estas duas alterações, o tráfego destinado de internet que origina da sub-rede ASE não é forçado para baixo o ExpressRoute e a ASE funciona.

> [!IMPORTANT]
> As rotas definidas num UDR tem de ser específicas o suficiente para têm precedência sobre qualquer rotas anunciadas na configuração do ExpressRoute. O exemplo anterior utiliza o intervalo de endereços abrangente 0.0.0.0/0. Que pode potencialmente seja acidentalmente substituído por anúncios de rota que utilizam os intervalos de endereços mais específicos.
>
> ASEs não são suportadas com configurações de ExpressRoute que cross-anunciam rotas do caminho de peering de público para o caminho de peering de privada. Configurações de ExpressRoute com o peering público configurado recebem anúncios de rota da Microsoft. Os anúncios contêm um grande conjunto de intervalos de endereços IP do Microsoft Azure. Se os intervalos de endereços entre anunciados no caminho de peering de privada, todos os pacotes de rede de saída da sub-rede a ASE são force em túnel à infraestrutura de rede no local do cliente. Este fluxo de rede não é suportado por predefinição com ASEs. Uma solução para este problema está a parar as rotas entre publicidade do caminho de peering de público para o caminho de peering de privada.  A outra solução é ativar a sua ASE funcionar numa configuração de imposição de túnel.

## <a name="change-the-egress-endpoint-for-your-ase"></a>Alterar o ponto final de saída para a sua ASE ##

Esta secção descreve como ativar ASE a funcionar numa configuração de imposição de túnel, alterar o ponto final de saída utilizado pelo ASE. Se o tráfego de saída do ASE é forçado em túnel a uma rede no local, em seguida, terá de permitir o tráfego de que a origem de endereços IP que não seja o endereço ASE VIP.

ASE não só tem dependências externas, mas também deve escutar para tráfego de entrada para gerir o ASE. O ASE deve ser capaz de responder a esse tráfego e as respostas não não possível enviar novamente a partir do outro endereço que quebras de TCP.  Deste modo, existem três passos necessários para alterar o ponto final de saída para o ASE.

1. Defina uma tabela de rota para se certificar de que o tráfego de entrada de gestão pode voltar atrás do mesmo endereço IP
1. Adicionar que o IP endereços que a ser utilizada para a saída à ASE firewall
1. Definir as rotas para tráfego de saída do ASE para estar em túnel

![Forçado o fluxo de túnel de rede][1]

Pode configurar o ASE com endereços de saída diferente depois do ASE já está a cópia de segurança e operacionais ou pode ser definidos durante a implementação de ASE.  

### <a name="changing-the-egress-address-after-the-ase-is-operational"></a>Alterar o endereço de saída depois do ASE está operacional ###
1. Obter os endereços IP que pretende utilizar como saída de IPs para o seu ASE. Se está a fazer imposição do túnel, em seguida, isto seria o NATs ou gateway IPs.  Se pretender encaminhar o tráfego de saída ASE através de uma NVA, o endereço de saída seria o IP público a NVA.
2. Defina os endereços de saída nas suas informações de configuração ASE. Aceda a resource.azure.com e navegue para: subscrição /<subscription id>/resourceGroups/<ase resource group>/providers/Microsoft.Web/hostingEnvironments/<ase name> , em seguida, pode ver o json que descreve o ASE.  Certifique-se diz leitura/escrita na parte superior.  Clique em Editar desloque-se até à parte inferior e alterar userWhitelistedIpRanges do  

       "userWhitelistedIpRanges": null 
      
  para algo semelhante ao seguinte. Utilize os endereços que pretende definir como o intervalo de endereços de saída. 

      "userWhitelistedIpRanges": ["11.22.33.44/32", "55.66.77.0/24"] 

  Clique em PUT na parte superior. Isto aciona uma operação de escala no seu ASE e ajustar a firewall.
   
3. Criar ou editar uma tabela de rota e preencher as regras para permitir o acesso dos endereços de gestão que mapeiam para a localização de ASE.  Os endereços de gestão são aqui, [endereços de gestão do ambiente de serviço de aplicações][management] 

4. Ajuste as rotas aplicadas à sub-rede ASE com uma tabela de rota ou as rotas BGP.  

Se o ASE ficar não responde no portal, é um problema com as suas alterações.  Pode ser que a lista de endereços de saída era incompleta, o tráfego foi perdido ou o tráfego foi bloqueado.  

### <a name="create-a-new-ase-with-a-different-egress-address"></a>Criar um novo ASE com um endereço de saída diferente  ###

No caso da VNet já está configurada para forçar o túnel todo o tráfego, terá de efetuar alguns passos adicionais para criar sua ASE de forma a que este pode pensar com êxito. Isto significa que terá de ativar a utilização de outro ponto final de saída durante a criação de ASE.  Para tal, tem de criar o ASE com um modelo que especifica os endereços de saída permitidos.

1. Obter os endereços IP a ser utilizado como os endereços de saída para sua ASE.
1. Pré-crie a sub-rede para ser utilizado pelo ASE. Isto é necessário para permitem-lhe definir as rotas e também porque o modelo requê-lo.  
1. Criar uma tabela de rota com a gestão IPs que mapeia para a localização de ASE e atribuí-lo a sua ASE
1. Siga as indicações aqui, [criar ASE com um modelo][template]e obtenha o modelo apropriado
1. Edite a secção de "recursos" do ficheiro azuredeploy. JSON. Incluir uma linha para **userWhitelistedIpRanges** com os seus valores, como:

       "userWhitelistedIpRanges":  ["11.22.33.44/32", "55.66.77.0/30"]

Se este está configurado corretamente, deve começar a ASE sem problemas.  


<!--IMAGES-->
[1]: ./media/forced-tunnel-support/forced-tunnel-flow.png

<!--Links-->
[management]: ./management-addresses.md
[network]: ./network-info.md
[routes]: ../../virtual-network/virtual-networks-udr-overview.md
[template]: ./create-from-template.md
