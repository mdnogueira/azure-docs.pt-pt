---
title: "Do Azure Mobile Engagement Push/alcance - guia de resolução de problemas"
description: "Resolução de problemas de interação e a notificação do utilizador no Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: erikre
editor: 
ms.assetid: 3f1886b7-1fdd-47f4-b6b0-d79f158d5ef3
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: ef6f34404b97a6972fc136262920a1bdbc4117b0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshooting-guide-for-push-and-reach-issues"></a>Guia de resolução de problemas Push e alcance
Seguem-se possíveis problemas que pode surgir com a forma como o Azure Mobile Engagement envia informações aos seus utilizadores.

## <a name="push-failures"></a>Falhas de push
### <a name="issue"></a>Problema
* Pushes não funcionam (na aplicação, fora da aplicação, ou ambos).

### <a name="causes"></a>Causas
* Muitas vezes uma push falha é uma indicação de que Azure Mobile Engagement, alcance ou outra funcionalidade avançada do Azure Mobile Engagement não estiver corretamente integrada ou que uma atualização é necessário no SDK para corrigir um problema conhecido com uma nova plataforma de dispositivo ou de SO.
* Teste apenas um push na aplicação e apenas um push de saída da aplicação para determinar se algo é um problema na aplicação ou de aplicação.
* Teste a partir da IU e a API como um passo de resolução de problemas para ver as informações de erro adicionais estão disponíveis ambos os locais.
* Fora da aplicação pushes poderá não funcionam, a menos que o Azure Mobile Engagement e alcance estão integradas no SDK.
* Pushes não funcionará se certificados não são válidos ou estão a utilizar PROD vs. DEV corretamente (apenas iOS). (**Nota:** "fora da aplicação" notificações push podem não ser entregues para iOS, se tiver o (desenvolvimento) de desenvolvimento e versões de produção (PROD) da sua aplicação instalada no mesmo dispositivo, uma vez que o token de segurança associados com o seu certificado poderão ser invalidadas pela Apple. Para resolver este problema, desinstale ambas as versões de desenvolvimento e PROD da sua aplicação e volte a instalar o apenas a uma versão no seu dispositivo.)
* Fora de push da aplicação contagens são processadas forma diferente em plataformas diferentes (iOS mostra menos informações ao Android se estiverem desativados pushes nativos num dispositivo, a API pode fornecer mais informações do que a IU no estatísticas de push).
* Fora da aplicação podem ser bloqueados pushes por clientes ao nível do SO (iOS e Android).
* Fora da aplicação pushes serão apresentadas como desativada na IU do Azure Mobile Engagement, se não estiverem integrados corretamente, mas poderão falhar automaticamente a partir da API de.
* Na aplicação pushes poderá não funcionam, a menos que o Azure Mobile Engagement e alcance estão integradas no SDK.
* Pushes GCM e ADM não funcionará, a menos que o Azure Mobile Engagement e o servidor específico estão integrados no SDK (apenas Android).
* Na aplicação e fora da aplicação de pushes devem ser testadas separadamente para determinar se é um problema de Push ou alcance.
* Na aplicação pushes requerem que a aplicação ser aberta para que sejam recebidas.
* Na aplicação pushes são, muitas vezes, a configuração para ser filtrados por uma tag de informações de aplicação opção ou recusa.
* Se utilizar uma categoria personalizada no alcance para apresentação de notificações na aplicação, tem de seguir o ciclo de vida correto da notificação; caso contrário, a notificação não pode ser limpa quando o utilizador dispensá-lo.
* Se iniciar uma campanha com sem data de fim e um dispositivo recebe a aplicação na notificação mas não não apresentar este ainda, o será de utilizador ainda receber a notificação da próxima vez que iniciarem sessão na aplicação, mesmo se manualmente de terminar a campanha.
* Para problemas com a API de Push, confirme que é realmente necessário utilizar a API de Push em vez da API de alcance (uma vez que a API de alcance é utilizada mais frequentemente) e que não são confusa os parâmetros "payload" e "notifier".
* Teste a sua campanha push com ambos os um dispositivo ligado através de Wi-Fi e 3G para eliminar a ligação de rede como uma origem dos problemas possíveis.

## <a name="push-testing"></a>Push de teste
### <a name="issue"></a>Problema
* Pushes podem ser enviadas para um dispositivo específico, com base num ID de dispositivo.

### <a name="causes"></a>Causas
* Dispositivos de teste são a configuração de forma diferente para cada plataforma, mas a causar um evento na sua aplicação num dispositivo de teste e procurando o ID do dispositivo no portal do devem funcionar para localizar o ID do dispositivo para todas as plataformas.
* Dispositivos de teste funcionam de forma diferente com IDFA vs. IDFV (apenas iOS).

## <a name="push-customization"></a>Personalização de push
### <a name="issue"></a>Problema
* Avançadas push conteúdo item não funcionará (destaque, anel, Vibre, imagem, etc.).
* As ligações de pushes não funcionam (fora da aplicação, na aplicação, para um Web site, para uma localização na aplicação).
* Estatísticas de push mostram que um push não foi enviado para como muitas pessoas, tal como era esperado (demasiados ou insuficiente).
* Push duplicado e recebido duas vezes.
* Não é possível registar o dispositivo de teste para o Azure Mobile Engagement Pushes (com a sua própria aplicação Prod ou DEV).

### <a name="causes"></a>Causas
* Para ligar a uma localização específica na aplicação requer "categorias" (apenas Android).
* Os esquemas de ligação avançados para redirecionar os utilizadores para uma localização alternativa depois de clicar numa notificação push tem de ser criado no e geridos pela sua aplicação e o SO do dispositivo não ao Mobile Engagement diretamente. (**Nota:** fora da aplicação notificações não é possível ligar diretamente em localizações de aplicação com o iOS como estes requisitos podem com Android.)
* Servidores de imagem externos têm de ser capazes de utilizar HTTP "GET" e "HEAD" para visão pushes funcione (apenas Android).
* No seu código, pode desativar o agente do Azure Mobile Engagement quando é aberto o teclado e ter o seu código reativar o agente do Azure Mobile Engagement depois de fechar o teclado para que o teclado não irá afetar o aspeto da sua notificação (apenas iOS).
* Alguns itens não funcionam no simulações de teste, mas apenas as campanhas reais (de destaque, anel, Vibre, imagem, etc.).
* Não existem dados do lado do servidor são registados quando utilizar o botão pushes de "teste". Os dados são registados apenas para as campanhas real push.
* Para ajudar a isolar o problema, resolver problemas com: testar, simular e uma campanha real, uma vez que cada funcionam forma ligeiramente diferente.
* O período de tempo que a "aplicação" e campanhas de "sempre" estejam agendadas para execução pode que tenha efeito números de entrega, uma vez que só são entregues a uma campanha para os utilizadores que são "na aplicação", enquanto a campanha é executada (e os utilizadores que possuem as definições de dispositivo definidas para receber notificações "fora da aplicação").
* As diferenças entre a forma como o Android e iOS processar fora de notificações da aplicação dificulta diretamente comparar as estatísticas de push entre a versão do Android e iOS da sua aplicação. Android fornece mais informações de notificação de nível de SO que iOS. Android relatórios quando uma notificação nativa é recebida, clicada ou eliminada no Centro de notificações, mas o iOS não comunica estas informações, a menos que a notificação é clicada. 
* O principal motivo números "premidos" são diferentes que diferente de "entregues" números de campanhas de alcance que "na aplicação" e "fora da aplicação" notificações são contadas diferente. "Na aplicação" notificações são processadas pelo Mobile Engagement, mas "fora da aplicação" notificações são processadas pelo centro de notificações no SO do dispositivo.

## <a name="push-targeting"></a>Filtragem de push
### <a name="issue"></a>Problema
* Incorporada na filtragem não funciona conforme esperado.
* Filtragem de Tag as informações da aplicação não funciona conforme esperado.
* Filtragem de Geolocalização não funciona conforme esperado.
* As opções de idioma não funcionam conforme esperado.

### <a name="causes"></a>Causas
* Certifique-se de que carregou etiquetas de informações da aplicação através da IU do Azure Mobile Engagement ou a API.
* Limitação de velocidade de push ou a quota de push ao nível da aplicação ou limitar o público-alvo ao nível da campanha pode impedir que uma pessoa de receber um push específico, mesmo que cumprem os critérios de filtragem. 
* A definição de um "idioma" é diferente de filtragem com base num país ou região, que também é diferente de filtragem com base no geolocalização com base numa localização de telefone ou a localização de GPS.
* A mensagem "idioma predefinido" é enviada para qualquer cliente que não tenha o respetivo dispositivo definido como um dos idiomas alternativos que especificar.

## <a name="push-scheduling"></a>Push de agendamento
### <a name="issue"></a>Problema
* Agendamento de push não funciona conforme esperado (enviado demasiado antecipada ou atrasada).

### <a name="causes"></a>Causas
* Fusos horários pode problemas com o agendamento, especialmente quando se utilizam o fuso horário dos utilizadores finais.
* Funcionalidades avançadas de push podem atrasar pushes.
* Filtragem baseada no telefone definições (em vez de etiquetas de informações de aplicação) podem atrasar pushes uma vez que o Azure Mobile Engagement pode ter que solicitar dados em tempo real do telefone antes de enviar um push.
* As campanhas criadas sem uma data de fim armazenam push localmente no dispositivo e mostram-lo da próxima vez que abrir a aplicação, mesmo que a campanha manualmente seja terminada.
* Iniciar mais de uma campanha ao mesmo tempo, pode demorar mais tempo para verificar a sua base de utilizadores (tentar apenas iniciar uma campanha de cada vez com um máximo de quatro, também destino apenas para os utilizadores do Active Directory para que utilizadores antigos não tem de ser analisados).
* Se utilizar a opção "Ignorar audiência; push será enviado aos utilizadores através da API" na secção "Campanha" de uma campanha de alcance, a campanha não enviará automaticamente, terá de enviá-lo manualmente através da API de alcance.
* Se utilizar uma categoria personalizada no alcance para apresentação de notificações na aplicação, tem de seguir o ciclo de vida correto de uma notificação, caso contrário, a notificação não pode ser limpa quando o utilizador dispensá-lo.

