---
title: "Integração do Android SDK do Azure Mobile Engagement"
description: "Mais recentes atualizações e procedimentos para Android SDK do Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 585341f9-3f39-459a-af42-864e400a0128
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 07/17/2017
ms.author: piyushjo
ms.openlocfilehash: c179c39a43da0aa35e945acceacbf27fe8e328f3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="release-notes"></a>Notas de versão

## <a name="431-07172017"></a>4.3.1 (07/17/2017)
* Corrigir falhas que raramente pode ter ocorrido ao chamar `EngagementAgentUtils.isInDedicatedEngagementProcess`, que também é utilizado pelo `EngagementApplication` classe.

## <a name="430-06272017"></a>4.3.0 (06/27/2017)
* Suporte Android 8 (versões anteriores do SDK não funcionará no Android 8).
* Não existem mais dependência na biblioteca de suporte.
* Remover `EngagementFragmentActivity` classe.
* Devido a [limites de execução em segundo plano](https://developer.android.com/preview/features/background.html) no Android 8, os registos em segundo plano podem sofrer um atraso de até que o utilizador interage com o dispositivo, este irá ter um impacto no campanha Push **entregues** e **notificação do sistema apresentada** estatísticas a ser atrasadas se o dispositivo foi suspenso (a notificação irá ainda ser apresentada, serão anel e Vibre em tempo real sem problemas).
* Devido a [limites de localização em segundo plano](https://developer.android.com/preview/features/background-location-limits.html), o localização em segundo plano não será atualizada frequentemente no Android 8 em tempo real.

## <a name="424-03302017"></a>4.2.4 (03/30/2017)
* Corrija cores de texto de notificação na aplicação no Android 7 ser as mesmas versões mais antigas de Android.

## <a name="423-08102016"></a>4.2.3 (08/10/2016)
* Não existem mais bloqueio de Wi-Fi.
* Corrigir um impasse ao chamar getDeviceId antes init (erros introduzido no 4.2.0).

## <a name="422-05172016"></a>4.2.2 (05/17/2016)
* Melhoramentos de estabilidade.

## <a name="421-05102016"></a>4.2.1 (05/10/2016)
* Segurança: desative o acesso web a vista ficheiro local.
* Segurança: remover `EngagementPreferenceActivity` classe que expande obsoleta e não seguros `PreferenceActivity` classe.
* Segurança: alcance atividades agora estão documentadas utilizar `exported="false"`, este sinalizador também pode ser utilizado em versões anteriores do SDK.

## <a name="420-03112016"></a>4.2.0 (03/11/2016)
* O SDK agora está licenciado sob MIT.
* Permite a especificação de um identificador de dispositivo personalizada em vez de inicialização do SDK.

## <a name="415-02012016"></a>4.1.5 (02/01/2016)
* Melhoramentos de estabilidade.

## <a name="414-01262016"></a>4.1.4 (01/26/2016)
* Melhoramentos de estabilidade.

## <a name="413-1292015"></a>4.1.3 (12/9/2015)
* Melhoramentos de estabilidade.

## <a name="412-11252015"></a>4.1.2 (11/25/2015)
* Melhoramentos de estabilidade.

## <a name="411-11042015"></a>4.1.1 (11/04/2015)
* Melhoramentos de estabilidade.

## <a name="410-08252015"></a>4.1.0 (08/25/2015)
* Identificador de novo modelo de permissão para Android M.
* Agora pode configurar funcionalidades de localização em tempo de execução em vez de utilizar `AndroidManifest.xml`.
* Corrija um erro de permissão: Se utilizar `ACCESS_FINE_LOCATION`, em seguida, `ACCESS_COARSE_LOCATION` não é necessária já.
* Melhoramentos de estabilidade.

## <a name="400-07062015"></a>4.0.0 (07/06/2015)
* Alterações do protocolo interno para tornar mais fiável push e de análise.
* Push nativo (GCM/ADM) agora também é utilizado para em notificações da aplicação, de modo tem de configurar as credenciais de push nativo para qualquer tipo de campanha push.
* Corrija a notificação de visão: fossem apresentados 10s apenas após a ser instalada.
* Corrija a partir de um erro de vista web: clicar numa ligação foi também executar o URL de ação predefinida.
* Corrigir falhas raro relacionadas com a gestão de armazenamento local.
* Corrija a gestão de cadeia de configuração dinâmica.
* Atualize o EULA.

## <a name="300-02172015"></a>3.0.0 (02/17/2015)
* Versão inicial do Azure Mobile Engagement
* configuração de appId é substituída por uma configuração de cadeia de ligação.
* Remover API para enviar e receber mensagens de com XMPP arbitrários de entidades de com XMPP arbitrários.
* Remover API para enviar e receber mensagens entre dispositivos.
* Melhoramentos de segurança.
* Controlo do Google Play e SmartAd removido.

