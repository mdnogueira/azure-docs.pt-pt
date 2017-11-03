---
title: Como utilizar a API Management do Azure com redes virtuais internas | Microsoft Docs
description: Saiba como configurar a API Management do Azure numa rede virtual interna
services: api-management
documentationcenter: 
author: vladvino
manager: kjoshi
editor: 
ms.assetid: dac28ccf-2550-45a5-89cf-192d87369bc3
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.author: apimpm
ms.openlocfilehash: 2a496059d1959a6c9e762e70dfbeff9bf961c4d4
ms.sourcegitcommit: 1131386137462a8a959abb0f8822d1b329a4e474
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2017
---
# <a name="using-azure-api-management-service-with-an-internal-virtual-network"></a>Utilizar o serviço de API Management do Azure com uma rede virtual interna
Com redes virtuais do Azure, a API Management do Azure pode gerir APIs não está acessíveis na internet. Um número de tecnologias VPN está disponível para efetuar a ligação. API Management podem ser implementada em dois modos principais dentro de uma rede virtual:
* Externo
* Interno


Quando a API Management implementa no modo de rede virtual interna, todos os serviço pontos finais (gateway, portal do programador, portal do publicador, gestão direta e Git) apenas estão visíveis no interior de uma rede virtual que controlam o acesso ao. Nenhum dos pontos finais do serviço estão registados no servidor DNS público.

Utilizando a gestão de API no modo interno, que pode realizar os seguintes cenários:
* Certifique-APIs alojadas no seu centro de dados privado acessível de forma segura por terceiros fora do através de ligações de VPN do Azure ExpressRoute ou de site para site.
* Ative cenários de nuvem híbrida através de exposição das suas APIs baseado na nuvem e APIs no local através de um gateway comuns.
* Gerir as suas APIs alojados em várias localizações geográficas através da utilização de um ponto final de gateway único. 


## <a name="prerequisites"></a>Pré-requisitos

Para efetuar os passos descritos neste artigo, tem de ter:

+ **Uma subscrição do Azure Active Directory**.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ **Uma instância de API Management do Azure**. Para obter mais informações, consulte [criar uma instância de API Management do Azure](get-started-create-service-instance.md).

## <a name="enable-vpn"></a>Criar uma API Management numa rede virtual interna
O serviço de API Management numa rede virtual interna está alojado por trás de um balanceador de carga interno (ILB).

### <a name="enable-a-virtual-network-connection-using-the-azure-portal"></a>Ativar uma ligação de rede virtual com o portal do Azure

1. Navegue até à sua instância de API Management do Azure no [portal do Azure](https://portal.azure.com/).
2. Selecione **domínios personalizados e SSL**.
3. Configure a instância de API Management para ser implementada dentro da rede virtual.

    ![Menu para configurar uma API Management do Azure numa rede virtual interna][api-management-using-internal-vnet-menu]

4. Selecione **Guardar**.

Depois da implementação for bem sucedida, deverá ver o endereço IP virtual interno do seu serviço no dashboard.

![Dashboard de gestão de API com uma rede virtual interna configurada][api-management-internal-vnet-dashboard]

### <a name="enable-a-virtual-network-connection-by-using-powershell-cmdlets"></a>Ativar uma ligação de rede virtual utilizando cmdlets do PowerShell
Também pode ativar a conectividade de rede virtual utilizando cmdlets do PowerShell.

* Criar um serviço de API Management dentro de uma rede virtual: Utilize o cmdlet [New-AzureRmApiManagement](/powershell/module/azurerm.apimanagement/new-azurermapimanagement) para criar um serviço de API Management do Azure dentro de uma rede virtual e configurá-lo para utilizar o tipo de rede virtual interna.

* Implementar um serviço de API Management existente no interior de uma rede virtual: Utilize o cmdlet [atualização AzureRmApiManagementDeployment](/powershell/module/azurerm.apimanagement/update-azurermapimanagementdeployment) mover um serviço de API Management existente no interior de uma rede virtual e configurá-lo para utilizar o interno tipo de rede virtual.

## <a name="apim-dns-configuration"></a>Configuração de DNS
Quando a API Management está no modo de rede virtual externa, o DNS é gerido pelo Azure. Para o modo de rede virtual interna, tem de gerir os seus próprios encaminhamento.

> [!NOTE]
> Serviço de API Management escutar para pedidos provenientes de endereços IP. Apenas responde a pedidos para o nome de anfitrião configuradas nos respetivos pontos finais de serviço. Estes pontos finais incluem gateway, o portal do programador, o portal do publicador, o ponto final de gestão direta e o Git.

### <a name="access-on-default-host-names"></a>Acesso em nomes de anfitrião predefinido
Quando cria um serviço de API Management, com o nome "contoso", por exemplo, os seguintes pontos finais de serviço estão configurados por predefinição:

   * Gateway ou proxy: contoso.azure api.net

   * Portal do publicador e o portal do programador: contoso.portal.azure api.net

   * Ponto final de gestão direta: contoso.management.azure api.net

   * Git: contoso.scm.azure-api.net

Para aceder a estes pontos finais de serviço de API Management, pode criar uma máquina virtual numa sub-rede ligada à rede virtual em que a API Management está implementada. Pressupondo que o endereço IP virtual interno para o seu serviço é 10.0.0.5, é possível mapear o ficheiro de anfitriões % SystemDrive%\drivers\etc\hosts, da seguinte forma:

   * 10.0.0.5 contoso.azure-api.net

   * 10.0.0.5 contoso.portal.azure-api.net

   * 10.0.0.5 contoso.management.azure-api.net

   * 10.0.0.5 contoso.scm.azure-api.net

Em seguida, pode aceder a todos os pontos finais do serviço da máquina virtual que criou. Se utilizar um servidor DNS personalizado numa rede virtual, também pode criar registos A DNS e aceder a estes pontos finais a partir de qualquer lugar na sua rede virtual. 

### <a name="access-on-custom-domain-names"></a>Acesso em nomes de domínio personalizados

   1. Se não quiser aceder ao serviço de gestão de API com os nomes de anfitrião predefinido, que pode configurar os nomes de domínio personalizado para todos os seus pontos finais de serviço conforme mostrado na imagem seguinte: 

   ![Configurar um domínio personalizado para gestão de API][api-management-custom-domain-name]

   2. Em seguida, pode criar registos no seu servidor DNS para aceder os pontos finais que só estão acessíveis a partir de dentro da sua rede virtual.

## <a name="routing"></a> Encaminhamento
+ Um balanceamento de carga virtual endereço IP privado de intervalo de sub-rede será reservado e utilizado para aceder os API Management pontos finais do serviço de dentro da vnet.
+ Também será possível reservar um endereço IP público com balanceamento de carga (VIP) para fornecer acesso ao ponto final do serviço de gestão apenas através da porta 3443.
+ Um endereço IP de um intervalo de sub-rede IP (DIP) será utilizado para aceder a recursos dentro da vnet e um endereço IP público (VIP) será utilizado para aceder a recursos fora da vnet.
+ Público com balanceamento de carga e endereços IP privados podem ser encontrados no painel de descrição geral/Essentials no portal do Azure.

## <a name="related-content"></a>Relacionados com o conteúdo
Para obter mais informações, consulte os artigos seguintes:
* [Problemas comuns de configuração de rede ao configurar a API Management do Azure numa rede virtual][Common network configuration problems]
* [Perguntas mais frequentes de rede virtual](../virtual-network/virtual-networks-faq.md)
* [Criar um registo no DNS](https://msdn.microsoft.com/en-us/library/bb727018.aspx)

[api-management-using-internal-vnet-menu]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-menu.png
[api-management-internal-vnet-dashboard]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-dashboard.png
[api-management-custom-domain-name]: ./media/api-management-using-with-internal-vnet/api-management-custom-domain-name.png


[Create API Management service]: api-management-get-started.md#create-service-instance
[Common network configuration problems]: api-management-using-with-vnet.md#network-configuration-issues

