---
title: "IOS do Mobile Engagement notas de versão do SDK do Azure | Microsoft Docs"
description: "Mais recentes atualizações e procedimentos para o SDK do iOS do Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: a43ff0f6-90d5-4b3c-8d7a-a1db21bc776b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 07/17/2017
ms.author: piyushjo
ms.openlocfilehash: 9bdaa57f9902373ccf796ff109332b64c66bf9e7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-mobile-engagement-ios-sdk-release-notes"></a>Notas de versão do iOS do Mobile Engagement SDK do Azure

## <a name="410-07172017"></a>4.1.0 (07/17/2017)
* Destaques fixos limpo em segundo plano.
* Avisos fixos no XCode 9 sobre APIs não chamadas na fila principal.
* Fixo uma fuga de memória em inquéritos de alcance.
* Remover o suporte para iOS 6. x. A partir desta versão do destino da implementação da aplicação tem de ser, pelo menos, o iOS 7.

## <a name="401-12132016"></a>4.0.1 (12/13/2016)
* Melhorado entrega de registo em segundo plano.

## <a name="400-09122016"></a>4.0.0 (09/12/2016)
* Notificação fixa não alvo de ação em dispositivos iOS 10.
* Despromover o XCode 7.

## <a name="324-06302016"></a>3.2.4 (06/30/2016)
* Agregação fixa entre registos técnicos e outros registos.

## <a name="323-06072016"></a>3.2.3 (06/07/2016)
* Corrigir os erros onde os comentários de entrega não foi reportado quando a aplicação está em segundo plano.
* Otimizado o envio de registos técnicos.

## <a name="322-04072016"></a>3.2.2 (04/07/2016)
* Erros fixo no cancelamento do pedido HTTP que por vezes, conduz a falhas.

## <a name="321-12112015"></a>3.2.1 (12/11/2015)
* Corrigido o atraso quando uma nova instância de aplicação é acionada por uma notificação com ligações avançadas

## <a name="320-10082015"></a>3.2.0 (10/08/2015)
* Ativar Bitcode no SDK para torná-lo a trabalhar com **Xcode 7**.
* Fixos erros relacionados com as notificações na aplicação.
* As notificações na aplicação efetuadas mais fiável em caso de bateria baixo e outros tais cenários.
* Remover os registos de consola adicionais gerados por 3rd biblioteca de terceiros.

## <a name="310-08262015"></a>3.1.0 (08/26/2015)
* Corrija os erros de compatibilidade do iOS 9 com uma biblioteca de terceiros. -Foi causar falhas durante o envio de consulta de resultados, informações sobre a aplicação ou dados adicionais.

## <a name="300-06192015"></a>3.0.0 (06/19/2015)
* O Mobile Engagement utiliza notificações Push automáticas.
* Remover o suporte para iOS 4. x. A partir desta versão do destino da implementação da aplicação tem de ser, pelo menos, iOS 6.

## <a name="220-05212015"></a>2.2.0 (05/21/2015)
* O id de dispositivo do Mobile Engagement para dispositivos < baseia-se agora o iOS 6 num GUID gerado no momento de instalação.

## <a name="210-04242015"></a>2.1.0 (04/24/2015)
* Compatibilidade Swift adicionada.
* Quando clica numa notificação, executar a ação que URL está agora direito após a aplicação está aberta.
* Ficheiro de cabeçalho em falta adicionado no pacote SDK.
* Corrigido um problema, quando o relatório de falhas de Mobile Engagement foi desativado.

## <a name="200-02172015"></a>2.0.0 (02/17/2015)
* Versão inicial do Azure Mobile Engagement
* configuração de appId/sdkKey é substituída por uma configuração de cadeia de ligação.
* Remover API para enviar e receber mensagens de com XMPP arbitrários de entidades de com XMPP arbitrários.
* Remover API para enviar e receber mensagens entre dispositivos.
* Melhoramentos de segurança.
* Controlo de SmartAd removido.
