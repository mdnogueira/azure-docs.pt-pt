---
title: "Do Azure Mobile Engagement guia - o serviço de resolução de problemas"
description: "Guias do Azure Mobile Engagement de resolução de problemas"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: erikre
editor: 
ms.assetid: 8b4275da-c0b4-4690-824a-48e9d7a1fc6e
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: f13fd0540b783120014b3a8d4e41f78808c7fade
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshooting-guide-for-service-issues"></a>Guia de resolução de problemas para problemas de serviço
Seguem-se possíveis problemas que pode surgir com a forma como o Azure Mobile Engagement é executado.

## <a name="service-outages"></a>Falhas de serviço
### <a name="issue"></a>Problema
* Problemas que parecem ter sido causado por falhas no serviço do Azure Mobile Engagement.

### <a name="causes"></a>Causas
* Problemas que parecem ter sido causado por falhas no serviço do Azure Mobile Engagement podem ser causados por vários problemas diferentes:
  * Problemas isolados que originalmente aparecem systemic para todas as do Azure Mobile Engagement
  * Problemas conhecidos causados por falhas de servidor (nem sempre mostra no estado do servidor):
  * Agendamento atrasos, erros de filtragem, problemas de atualização do destaque, pare de estatísticas recolher, deixa de Push funcionar, deixarem de funcionar, novas aplicações ou utilizadores da API não é possível criar, erros DNS e erros de tempo limite da IU, API ou aplicações num dispositivo.
  * Falhas de dependência de nuvem [estado do serviço do Azure](http://status.azure.com/)
  * Falhas de dependência de serviços (PNS) de notificação de push
  * Falhas de loja de aplicações

1) Para testar se o problema está systemic pode testar a mesma função a partir de outro

* O Azure Mobile Engagement aplicação integrada
* Dispositivos de teste
* Versão de SO do dispositivo de teste
* Campanha
* Conta de utilizador de administrador
* Browser (, Firefox, Chrome, etc.)
* Computador

2) Para testar se o problema afeta apenas a IU ou a API:

* Teste a mesma função da IU do Azure Mobile Engagement e da API do Azure Mobile Engagement.

3) Para testar se o problema com a sua rede de telefone de rede móvel:

* Teste enquanto estiver ligado à Internet através de Wi-Fi e enquanto estiver ligado através da rede de rede móvel phone 3g.
* Certifique-se de que a firewall não está a bloquear qualquer um dos endereços de IP do Azure Mobile Engagement ou portas.

4) Para testar se o problema com o seu dispositivo:

* Teste se o dispositivo é capaz de ligar ao Azure Mobile Engagement com outra aplicação integrada do Azure Mobile Engagement.
* Teste pode gerar eventos, tarefas e as falhas de telemóvel que pode ser visto na IU do Azure Mobile Engagement. 
* Testar se pode enviar notificações push a partir da IU do Azure Mobile Engagement para o seu dispositivo com base no respetivo ID de dispositivo. 

5) Para testar se o problema com a sua aplicação:

* Instalar e testar a aplicação de um emulador em vez da partir de um dispositivo físico:

6) Para testar se com as atualizações do SO para o utilizador final dispositivos, o que exige uma atualização SDK para resolver o problema:

* Teste a aplicação em vários dispositivos com versões diferentes do SO.
* Certifique-se de que está a utilizar a versão mais recente do SDK.

## <a name="connectivity-and-incorrect-information-issues"></a>Problemas de informações incorretas e conectividade
### <a name="issue"></a>Problema
* Problemas de registo para a IU do Azure Mobile Engagement.
* Erros de ligação da API do Azure Mobile Engagement.
* Problemas de carregamento de etiquetas de informações da aplicação através da API do dispositivo.
* Problemas de transferir os registos ou dados exportados a partir do Azure Mobile Engagement.
* Informações incorretas mostradas na IU do Azure Mobile Engagement.
* Informações incorretas apresentadas nos registos do Azure Mobile Engagement.

### <a name="causes"></a>Causas
* Confirme a que sua conta de utilizador tem permissões suficientes para efetuar a tarefa.
* Confirme se o problema não está isolado para um computador ou a sua rede local.
* Certifique-se que o serviço do Azure Mobile Engagement não apresentou falhas.
* Confirme que os ficheiros de aplicação informações Tag siga todas estas regras:
  * Utilize apenas o conjunto de caracteres UTF8 (o conjunto de carateres ANSI não é suportado).
  * Utilize uma vírgula "," como o caráter de separação (pode abrir um pedido para pedido para alterar o caráter de separação. csv a partir de uma vírgula de serviço "," para outra caráter, tal como um ponto e vírgula ";").
  * Utilize todas as minúsculas para os valores booleanos "true" e "false".
  * Utilize um ficheiro que é inferior ao tamanho máximo do ficheiro de 35MB.

