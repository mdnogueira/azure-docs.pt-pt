---
title: "Acerca das ligações VPN do Azure ponto a Site | Microsoft Docs"
description: "Este artigo ajuda-o a compreender as ligações ponto a Site e ajuda-o a decidir o tipo de autenticação de gateway de P2S VPN a utilizar."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 010/19/2017
ms.author: cherylmc
ms.openlocfilehash: 0e31d58de113f737a48b6d3091650226f04ec69a
ms.sourcegitcommit: 963e0a2171c32903617d883bb1130c7c9189d730
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2017
---
# <a name="about-point-to-site-vpn"></a>Sobre VPN ponto a Site

Uma ligação de gateway de VPN Ponto a Site (P2S) permite-lhe criar uma ligação segura à sua rede virtual a partir de um computador cliente individual. É estabelecida uma ligação P2S ao iniciá-la a partir do computador cliente. Esta solução é útil para as pessoas que trabalham à distância que queiram ligar às VNets do Azure a partir de uma localização remota, como, por exemplo, a partir de casa ou de uma conferência. Uma VPN P2S também é uma solução útil para utilizar em vez de uma VPN S2S, quando são poucos os clientes que precisam de ligar a uma VNet. Este artigo aplica-se ao modelo de implementação clássica e do Resource Manager.

## <a name="protocol"></a>O protocolo utilizar P2S?

VPN ponto a site pode utilizar um dos seguintes protocolos:

* Secure Socket Tunneling Protocol (SSTP), um protocolo proprietário de VPN baseada em SSL. Uma solução de SSL VPN pode penetrarem firewalls, uma vez que a maioria das firewalls abra a porta TCP 443, que utiliza SSL. SSTP só é suportada em dispositivos Windows. Azure suporta todas as versões do Windows que tenham o SSTP (Windows 7 e posterior).

* VPN IKEv2, uma solução de VPN IPsec baseada em normas. A VPN IKEv2 pode ser utilizada para ligar a partir de dispositivos Mac (versões de OSX 10.11 e superiores).

Se tiver um ambiente de cliente mistos consiste em dispositivos Windows e Mac, configure o SSTP e IKEv2.

>[!NOTE]
>IKEv2 para P2S está atualmente em pré-visualização e está disponível para o modelo de implementação Resource Manager.
>

## <a name="authentication"></a>Como é que os clientes P2S VPN são são autenticados?

Antes de Azure aceita uma ligação P2S VPN, o utilizador tem de ser autenticada pela primeira vez. Existem dois mecanismos para autenticar um utilizador ao ligar o Azure oferece.

### <a name="authenticate-using-native-azure-certificate-authentication"></a>Autenticar através da autenticação do certificado Azure nativo

Quando utilizar a autenticação do certificado Azure nativo, um certificado de cliente que está presente no dispositivo é utilizado para autenticar o utilizador ao ligar. Certificados de cliente são gerados a partir de um certificado de raiz fidedigna e, em seguida, instalados em cada computador cliente. Pode utilizar um certificado de raiz que foi gerado utilizando uma solução de empresa, ou pode gerar um certificado autoassinado.

A validação do certificado de cliente é efetuada pelo gateway VPN e ocorre durante o estabelecimento da ligação P2S VPN. O certificado de raiz é necessário para a validação e tem de ser carregado para o Azure. 

### <a name="authenticate-using-active-directory-ad-domain-server"></a>Autenticar com o servidor de domínio do Active Directory (AD)

Autenticação de domínio do AD permite aos utilizadores ligar ao Azure através das respetivas credenciais de domínio de organização. Requer um servidor RADIUS que se integra com o servidor do AD. As organizações também podem tirar partido as suas implementações de RADIUS existente.   
 O servidor RADIUS pode ser implementado no local ou na sua VNET do Azure. Durante a autenticação, o Gateway de VPN do Azure funciona como um pass-through e reencaminha as mensagens de autenticação entre o servidor RADIUS e o dispositivo de ligação. Por isso, reachability de Gateway para o servidor RADIUS é importante. Se o servidor RADIUS está presente no local, é necessária para reachability uma ligação VPN S2S do Azure para o site no local.  
 O servidor RADIUS também pode integrar com serviços de certificados do AD. Isto permite-lhe utilizar o servidor RADIUS e a implementação de certificados de empresa para autenticação de certificados P2S como alternativa para a autenticação de certificado do Azure. A vantagem é que não é necessário carregar certificados de raiz e certificados revogados para o Azure.

Um servidor RADIUS também pode integrar com outros sistemas de identidade externas. Esta ação abre a cópia de segurança muitos de opções de autenticação para P2S VPN, incluindo as opções de multifator.

>[!NOTE]
>Autenticação RADIUS para P2S está atualmente em pré-visualização.
>

! [ponto a site]] (./media/point-to-site-about/p2s.png "Ponto a Site")

### <a name="configuration-requirements-for-client-devices"></a>Requisitos de configuração para dispositivos cliente

Os utilizadores utilizam os clientes VPN nativos em dispositivos Windows e Mac para P2S. O Azure oferece um cliente VPN zip configuração ficheiro que contém as definições necessárias por estes clientes nativos para ligar ao Azure.

  * Para dispositivos Windows, a configuração de cliente VPN é composta por um pacote de instalador que os utilizadores instalam nos seus dispositivos.
  * Para dispositivos Mac, é composto por ficheiro mobileconfig instalado pelos utilizadores nos respetivos dispositivos.

O ficheiro zip também fornece os valores de algumas das definições importantes no lado do Azure que pode utilizar para criar o seu próprio perfil para estes dispositivos. Alguns dos valores incluem o endereço de gateway de VPN, tipos de túnel configurado, as rotas e o certificado de raiz para validação do gateway.

### <a name="which-gateway-skus-support-p2s-vpn"></a>Que P2S de suporte de SKUs de Gateway VPN?

[!INCLUDE [p2s-skus](../../includes/vpn-gateway-table-point-to-site-skus-include.md)]

* A Referência de Débito de Agregação baseia-se em medidas de vários túneis agregados através de um único gateway. Não se trata de um débito garantido devido a condições de tráfego de internet e os comportamentos de aplicação.
* Obter informações sobre preços, podem ser encontrado na página de preços 
* Pode encontrar informações de SLA (contrato de nível de serviço) na página de SLA.

## <a name="faqcert"></a>FAQ para autenticação de certificados do Azure nativo

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="faqradius"></a>Perguntas mais frequentes sobre autenticação RADIUS

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>Passos Seguintes

[Configurar ligações P2S - autenticação RADIUS](point-to-site-how-to-radius-ps.md)

[Configurar ligações P2S - autenticação de certificados nativo do Azure](vpn-gateway-howto-point-to-site-rm-ps.md)
