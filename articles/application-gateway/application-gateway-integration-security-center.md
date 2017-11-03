---
title: "Integração do Gateway de aplicação com o Centro de segurança do Azure | Microsoft Docs"
description: "Esta página fornece informações sobre como o Gateway de aplicação está integrado no Centro de segurança do Azure."
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: 
ms.assetid: e5ea5cf9-3b41-4b85-a12c-e758bff7f3ec
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: 
ms.workload: infrastructure-services
ms.date: 06/07/2017
ms.author: davidmu
ms.openlocfilehash: abed4095bcacac4ca3995acfaafae2f16d5f1532
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-integration-between-application-gateway-and-azure-security-center"></a>Descrição geral da integração entre o Gateway de aplicação e o Centro de segurança do Azure

Saiba como Gateway de aplicação e o Centro de segurança ajudam a proteger os recursos de aplicação web. Firewall de aplicação da web de gateway de aplicação (WAF) integra-se [Centro de segurança](../security-center/security-center-intro.md) para proporcionar uma visão totalmente integrada para evitar, detetar e responder a ameaças para aplicações web não protegido no seu ambiente.

## <a name="overview"></a>Descrição geral

Gateway de aplicação WAF é uma recomendação no Centro de segurança para proteger as aplicações web contra exploits e vulnerabilidades. Mostram recursos Web ativado que não estão protegidos pelo WAF no Centro de segurança como recomendações de elevada gravidade. Recomendações de firewalls de aplicação web são apresentadas no **descrição geral** página **aplicações**.

![integração com o Centro de segurança][1]

Ao clicar em qualquer recomendações sobre firewall de aplicações web abre um novo painel que mostra os detalhes da recomendação.

## <a name="add-a-web-application-firewall-to-an-existing-resource"></a>Adicionar uma firewall de aplicação web para um recurso existente

Navegue para **mais serviços** > **segurança + identidade** > **Centro de segurança** e o **Centro de segurança - descrição geral**  painel, clique em **aplicações**. No **Centro de segurança - aplicações** painel, a tabela contém uma lista de aplicações do Centro de segurança detetado na sua subscrição.

![aplicações Web][3]

Ao clicar numa aplicação web com um problema crítico, obter o **estado de funcionamento de segurança de aplicação** painel. Na imagem abaixo, a aplicação web que não está protegido por uma firewall de aplicação web. 

![recursos da Web não protegidos][2]

Clique em **adicionar uma firewall de aplicação web** em **recomendações** para abrir o **adicionar uma Firewall de aplicação Web** painel.

Se não tiver um Gateway de aplicação existente, ou para criar um novo, clique em **criar novo** e o **criar uma nova Firewall de aplicação Web** painel e clique em **Microsoft - aplicações Gateway**. Isto leva-o pelos passos para criar um gateway de aplicação. Neste momento, a aplicação web é adicionada como um recurso protegido, o Centro de segurança agora controla que este recurso é protegido por uma firewall de aplicação web. Isto não adicioná-lo como um membro do conjunto de back-end.

Se tiver um gateway de aplicação existente, pode escolhê-lo em **utilizar solução existente**

![painel Adicionar de firewall de aplicações Web][4]

Adicionar que uma aplicação web para um gateway de aplicação através do Centro de segurança não adiciona o recurso como um membro do conjunto de back-end, isto deve ser efetuado no recurso de gateway de aplicação diretamente.

## <a name="add-a-resource-to-an-existing-web-application-firewall"></a>Adicione um recurso a uma firewall de aplicação web existente

Navegue para **mais serviços** > **segurança + identidade** > **Centro de segurança** e o **Centro de segurança - descrição geral**  painel, clique em **soluções de parceiros**. Gateways de aplicação com suporte para o Centro de segurança existentes mostram o **soluções de parceiros** painel.

![soluções de parceiros][7]

Clique em **ligar aplicação** para abrir o **ligar aplicações** painel, aqui é-lhe dada as opções para selecionar as aplicações existentes. Escolher as aplicações para proteger e clique em **OK**. Isto não adiciona a aplicação web para o conjunto de back-end do gateway de aplicação. Isto define os recursos como um recurso protegido pelo centro de segurança podem controlá-lo. Para adicionar o recurso como um membro do conjunto de back-end, este tem de ser efetuada no gateway de aplicação, no painel atual pode clicar em **consola solução** para ir para o recurso de gateway de aplicação onde pode adicionar a aplicação web para o conjunto de back-end.

![aplicações de soluções de parceiros][6]

## <a name="finalize-configuration"></a>Finalizar a configuração

Centro de segurança monitoriza aplicações adicionadas a um gateway de aplicação como um recurso protegido.  Monitoriza o estado de funcionamento deste recurso e assegura que está protegida por um gateway de aplicação. O passo seguinte consiste em adicionar o IP privado, o IP público ou o NIC da sua máquina virtual para o conjunto de back-end do gateway de aplicação. Até que este procedimento é efetuado uma recomendação adicional de **finalizar proteção aplicação** é apresentada até que o recurso seja adicionado.

![painel Adicionar de firewall de aplicações Web][5]

## <a name="security-alerts"></a>Alertas de segurança

No Centro de segurança, navegue para **DETEÇÃO** > **alertas de segurança**.  Aqui poderá encontrar alertas de WAF para gateways de aplicação. Alertas são divididos por regra WAF.

![alertas de segurança][8]

Clicar uma regra irá fornecer uma lista de alertas para essa regra WAF específica. Cada alerta mostra detalhes adicionais sobre a localizar. Os detalhes de fornecer uma ligação para o gateway de aplicação.
 
![Detalhes do alerta][9]

## <a name="next-steps"></a>Passos seguintes

Para saber como ativar a firewall de aplicação web num gateway de aplicação existente, visite [criar ou atualizar um Gateway de aplicação do Azure com firewall de aplicações web](application-gateway-web-application-firewall-portal.md#add-web-application-firewall-to-an-existing-application-gateway)

[1]: ./media/application-gateway-integration-security-center/figure1.png
[2]: ./media/application-gateway-integration-security-center/figure2.png
[3]: ./media/application-gateway-integration-security-center/figure3.png
[4]: ./media/application-gateway-integration-security-center/figure4.png
[5]: ./media/application-gateway-integration-security-center/figure5.png
[6]: ./media/application-gateway-integration-security-center/figure6.png
[7]: ./media/application-gateway-integration-security-center/figure7.png
[8]: ./media/application-gateway-integration-security-center/securitycenter.png
[9]: ./media/application-gateway-integration-security-center/figure9.png