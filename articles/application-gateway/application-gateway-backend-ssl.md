---
title: "Ativar a Política de SSL e o SSL de ponta a ponta no Gateway de Aplicação | Microsoft Docs"
description: "Esta página fornece uma descrição geral do suporte de SSL de ponta a ponta do Gateway de Aplicação."
documentationcenter: na
services: application-gateway
author: amsriva
manager: rossort
editor: amsriva
ms.assetid: 3976399b-25ad-45eb-8eb3-fdb736a598c5
ms.service: application-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 12/12/2016
ms.author: amsriva
translationtype: Human Translation
ms.sourcegitcommit: 5009b13cec57e6974f71610c84fdaad837085df0
ms.openlocfilehash: 5f81d8146f8000e73a2eb578ff2371a62c8875e9
ms.lasthandoff: 02/28/2017


---
# <a name="overview-of-end-to-end-ssl-and-ssl-policy-on-application-gateway"></a>Descrição geral de SSL e da Política SSL de ponto a ponto no Gateway de Aplicação

O Gateway de Aplicação suporta a terminação de SSL no gateway, após o qual o tráfego normalmente flui desencriptado para os servidores de back-end. Esta funcionalidade permite que os servidores Web estejam livres de sobrecarga de encriptação/desencriptação dispendiosa. No entanto, para alguns clientes, a comunicação sem encriptação para os servidores de back-end não é uma opção aceitável. Esta comunicação desencriptada pode dever-se aos requisitos de segurança/conformidade ou a aplicação pode aceitar apenas a ligação segura. Para essas aplicações, o gateway de aplicação suporta agora a encriptação SSL de ponta a ponta.

## <a name="overview"></a>Descrição geral

O SSL de ponto a ponto permite-lhe transmitir dados confidenciais de forma segura para o back-end encriptado e tirar partido dos benefícios das funcionalidades de balanceamento de carga da Camada 7 que o gateway de aplicação proporciona. Algumas destas funcionalidades são a afinidade do cookie, encaminhamento baseado em URL, suporte para encaminhamento baseado em sites ou capacidade para injetar cabeçalhos Encaminhados para X-*.

Quando configurado com o modo de comunicação SSL de ponta a ponta, o gateway de aplicação termina sessões de SSL de utilizador no gateway e desencripta o tráfego de utilizador. Em seguida, aplica as regras configuradas para selecionar uma instância de conjunto de back-end adequada para encaminhar o tráfego. O gateway de aplicação, em seguida, inicia uma nova ligação SSL ao servidor de back-end e encripta novamente os dados com o certificado de chave pública do servidor de back-end antes de transmitir o pedido para o back-end. O SSL de ponta a ponta é ativado ao configurar a definição do protocolo no BackendHTTPSetting como Https que, em seguida, é aplicado a um conjunto de back-end. Cada servidor de back-end no conjunto de back-end com SSL de ponta a ponta ativado deve ser configurado com um certificado, para permitir a comunicação segura.

![cenário de SSL ponto a ponto][1]

Neste exemplo, os pedidos que utilizam o TLS1.2 são encaminhados para servidores de back-end no Pool1 através do SSL ponto a ponto.

## <a name="end-to-end-ssl-and-whitelisting-of-certificates"></a>SSL ponto a ponto e lista de certificados permitidos

O gateway de aplicação comunica apenas com instâncias de back-end conhecidas que colocaram o respetivo certificado na lista de permissões com o gateway de aplicação. Para ativar a lista de certificados permitidos, tem de carregar a chave pública dos certificados de servidor de back-end para o gateway de aplicação (e não o certificado de raiz). Apenas são permitidas ligações a back-ends conhecidos e na lista de permissões. Os restantes back-ends resultam num erro de gateway. Os certificados autoassinados são apenas para fins de teste e não são recomendados para cargas de trabalho de produção. Esses certificados têm de estar na lista de permissões com o gateway de aplicação, conforme descrito nos passos anteriores, para poderem ser utilizados.

## <a name="application-gateway-ssl-policy"></a>Política SSL do Gateway de Aplicação

O gateway de aplicação suporta políticas de negociação SSL configuráveis pelo utilizador, o que permite aos clientes um maior controlo sobre ligações SSL no gateway de aplicação.

1. Por predefinição, o SSL 2.0 e 3.0 estão desativados para todos os Gateways de Aplicação. Não são configuráveis em qualquer altura.
2. A definição da política SSL dá-lhe a opção de desativar qualquer um dos seguintes três protocolos – **TLSv1\_0**, **TLSv1\_1**, **TLSv1\_2**.
3. Se não for definida uma política SSL, as três (TLSv1\_0, TLSv1\_1, TLSv1_2) serão ativadas.

## <a name="next-steps"></a>Passos seguintes

Após a aprendizagem sobre SSL de ponto a ponto e a política de SSL, aceda a [ativar SSL de ponta a ponta no gateway de aplicação](application-gateway-end-to-end-ssl-powershell.md), para criar um gateway de aplicação com SSL de ponto a ponto.

<!--Image references-->

[1]: ./media/application-gateway-backend-ssl/scenario.png

