---
title: "Configurar o ambiente de serviço de aplicações do Azure para ser forçada em túnel"
description: "Ativar o ambiente de serviço de aplicações funcionar quando o tráfego de saída foi forçado em túnel"
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
ms.openlocfilehash: f5f099042cefe666e22a9d561afeb4584db3d92c
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2017
---
# <a name="configure-your-app-service-environment-with-forced-tunneling"></a>Configurar o ambiente de serviço de aplicações com a imposição do túnel

O ambiente de serviço de aplicações é uma implementação do serviço de aplicações do Azure na instância de um cliente de rede Virtual do Azure. Muitos clientes configurar redes virtuais para as extensões de redes no local com VPNs ou Azure ExpressRoute ligações. Devido a políticas empresariais ou outras limitações de segurança, se configurarem rotas para enviar todas as tráfego de saída no local antes-pode aceder a à internet. Alterar o encaminhamento da rede virtual para que o tráfego de saída da rede virtual flui através da ligação VPN ou ExpressRoute para o local é denominado a imposição do túnel. 

Imposição do túnel pode causar problemas de um ambiente de serviço de aplicações. O ambiente de serviço de aplicações tem um número de dependências externas que são enumerado no [arquitetura de rede do ambiente de serviço de aplicações] [ network] documento. O ambiente de serviço de aplicações, por predefinição, requer que todas as comunicações de saída que atravessa o VIP que está aprovisionado com o ambiente de serviço de aplicações.

As rotas são um aspeto fundamental de que imposição do túnel é e saber como lidar com o mesmo. Numa rede virtual do Azure, o encaminhamento é efetuado com base na correspondência de prefixo (LPM) mais longo. Se existir mais do que uma rota com a mesma correspondência LPM, uma rota está selecionada com base na origem pela seguinte ordem:

* Rota definida pelo utilizador (UDR)
* Rota BGP (quando é utilizado o ExpressRoute)
* Rota de sistema

Para saber mais sobre o encaminhamento na rede virtual, leia [rotas definidas pelo utilizador e reencaminhamento IP][routes]. 

Se pretender que o ambiente de serviço de aplicações para funcionar numa rede virtual com a imposição de túnel, tem duas opções:

* Ative o ambiente de serviço de aplicações tenham acesso direto à internet.
* Altere o ponto final de saída para o ambiente de serviço de aplicações.

## <a name="enable-your-app-service-environment-to-have-direct-internet-access"></a>Ativar o ambiente de serviço de aplicações tenham acesso direto à internet

Para o ambiente de serviço de aplicações funcionar enquanto a rede virtual é configurada com uma ligação ExpressRoute, pode:

* Configure o ExpressRoute para anunciar 0.0.0.0/0. Por predefinição, este forçar túneis toda a saída de tráfego no local.
* Crie um UDR. Aplicá-la para a sub-rede que contém o ambiente de serviço de aplicações com um prefixo de endereço de 0.0.0.0/0 e um tipo de próximo salto de Internet.

Se efetuar estas duas alterações, o tráfego destinado de internet que origina a partir da sub-rede do ambiente de serviço de aplicações não está forçado para baixo a ligação do ExpressRoute e de ambiente de serviço de aplicações funciona.

> [!IMPORTANT]
> As rotas definidas num UDR tem de ser específicas o suficiente para têm precedência sobre qualquer rotas anunciadas na configuração do ExpressRoute. O exemplo anterior utiliza o intervalo de endereços abrangente 0.0.0.0/0. Que pode potencialmente seja acidentalmente substituído por anúncios de rota que utilizam os intervalos de endereços mais específicos.
>
> Ambientes de serviço de aplicações não são suportados com configurações de ExpressRoute que cross-anunciam rotas do caminho de peering de público para o caminho de peering de privada. Configurações de ExpressRoute com o peering público configurado recebem anúncios de rota da Microsoft. Os anúncios contêm um grande conjunto de intervalos de endereços IP do Microsoft Azure. Se os intervalos de endereços entre anunciados no caminho de peering de privada, todos os pacotes de rede de saída da sub-rede do ambiente de serviço de aplicações são force em túnel à infraestrutura de rede no local do cliente. Este fluxo de rede não é suportado por predefinição com ambientes do App Service. Uma solução para este problema está a parar as rotas entre publicidade do caminho de peering de público para o caminho de peering de privada. Outra solução é ativar o ambiente de serviço de aplicações funcionar numa configuração de imposição de túnel.

## <a name="change-the-egress-endpoint-for-your-app-service-environment"></a>Alterar o ponto final de saída para o ambiente de serviço de aplicações ##

Esta secção descreve como ativar um ambiente de serviço de aplicações a funcionar numa configuração de imposição de túnel, alterar o ponto final de saída utilizado pelo ambiente de serviço de aplicações. Se o tráfego de saída de ambiente de serviço de aplicações foi forçado em túnel a uma rede no local, terá de permitir o tráfego de que a origem de endereços IP que não seja o endereço VIP de ambiente de serviço de aplicações.

Um ambiente de serviço de aplicações não só tem dependências externas, mas também deve escutar para tráfego de entrada e responder a esse tráfego. Não é possível enviar o responde novamente a partir do outro endereço porque que quebras de TCP. Existem três passos necessários para alterar o ponto final de saída para o ambiente de serviço de aplicações:

1. Defina uma tabela de rota para se certificar de que o tráfego de entrada de gestão pode voltar atrás do mesmo endereço IP.

2. Adicione os endereços IP que estão a ser utilizada para a saída à firewall do ambiente de serviço de aplicações.

3. Defina as rotas para tráfego de saída do ambiente de serviço de aplicação para ser em túnel.

   ![Forçado o fluxo de túnel de rede][1]

Pode configurar o ambiente de serviço de aplicações com endereços de saída diferente depois do ambiente de serviço de aplicações já está a cópia de segurança e operacionais, ou pode ser definidos durante a implementação de ambiente de serviço de aplicações.

### <a name="change-the-egress-address-after-the-app-service-environment-is-operational"></a>Alterar o endereço de saída depois do ambiente de serviço de aplicações está operacional ###
1. Obter os endereços IP que pretende utilizar como saída de IPs para o ambiente de serviço de aplicações. Se estiver a fazer a imposição do túnel, estes endereços vêm da sua os NATs ou gateway IPs. Se pretender encaminhar o tráfego de saída do ambiente de serviço de aplicações através de uma NVA, o endereço de saída é o IP público a NVA.

2. Defina os endereços de saída nas suas informações de configuração do ambiente de serviço de aplicações. Aceda a resource.azure.com e aceda à subscrição /<subscription id>/resourceGroups/<ase resource group>/providers/Microsoft.Web/hostingEnvironments/<ase name>. Em seguida, pode ver o JSON que descreve o ambiente de serviço de aplicações. Certifique-se de que diz **leitura/escrita** na parte superior. Selecione **editar**. Desloque para baixo para a parte inferior e altere o **userWhitelistedIpRanges** valor a partir da **nulo** para algo semelhante ao seguinte. Utilize os endereços que pretende definir como o intervalo de endereços de saída. 

        "userWhitelistedIpRanges": ["11.22.33.44/32", "55.66.77.0/24"] 

   Selecione **colocar** na parte superior. Esta opção aciona uma operação de escala no seu ambiente de serviço de aplicações e ajusta a firewall.
 
3. Criar ou editar uma tabela de rota e preencher as regras para permitir o acesso dos endereços de gestão que mapeiam para a localização do ambiente de serviço de aplicações. Para obter os endereços de gestão, consulte [endereços de gestão do ambiente de serviço de aplicações][management].

4. Ajuste as rotas aplicadas à sub-rede de ambiente de serviço de aplicações com uma tabela de rota ou as rotas BGP. 

Se o ambiente de serviço de aplicações ficar responder a partir do portal, há um problema com as suas alterações. O problema poderá ser que a lista de endereços de saída era incompleta, o tráfego foi perdido ou o tráfego foi bloqueado. 

### <a name="create-a-new-app-service-environment-with-a-different-egress-address"></a>Criar um novo ambiente de serviço de aplicações com um endereço de saída diferente ###

Se a rede virtual já está configurada para forçar o túnel todo o tráfego, terá de efetuar passos adicionais para criar o ambiente de serviço de aplicações para que este pode pensar com êxito. Tem de ativar a utilização de outro ponto final de saída durante a criação do ambiente de serviço de aplicações. Para tal, tem de criar o ambiente de serviço de aplicações com um modelo que especifica os endereços de saída permitidos.

1. Obter os endereços IP a ser utilizado como os endereços de saída para o ambiente de serviço de aplicações.

2. Pré-crie a sub-rede a serem utilizadas pelo ambiente de serviço de aplicações. O ficheiro necessário para que pode configurar rotas e também porque o modelo requê-lo.

3. Crie uma tabela de rota com a gestão IPs que mapeiam para a localização do ambiente de serviço de aplicações. Atribua-o ao seu ambiente de serviço de aplicações.

4. Siga as indicações [criar um ambiente de serviço de aplicações com um modelo][template]. Obtenha o modelo apropriado.

5. Edite a secção "recursos" no ficheiro azuredeploy. JSON. Incluir uma linha para **userWhitelistedIpRanges** com os seus valores como esta:

       "userWhitelistedIpRanges":  ["11.22.33.44/32", "55.66.77.0/30"]

Se esta secção está corretamente configurada, o ambiente de serviço de aplicações deve começar com sem problemas. 


<!--IMAGES-->
[1]: ./media/forced-tunnel-support/forced-tunnel-flow.png

<!--Links-->
[management]: ./management-addresses.md
[network]: ./network-info.md
[routes]: ../../virtual-network/virtual-networks-udr-overview.md
[template]: ./create-from-template.md
