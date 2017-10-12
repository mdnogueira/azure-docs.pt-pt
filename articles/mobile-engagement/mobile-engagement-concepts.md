---
title: Conceitos do Mobile Engagement | Microsoft Docs
description: Conceitos do Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 8d19abd1-0a6c-4772-9fa5-5e99980ac5da
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 8450651528007b4527366b89a6ad7615169f93c0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-mobile-engagement-concepts"></a>Conceitos do Azure Mobile Engagement
O Mobile Engagement define alguns conceitos comuns para todas as plataformas suportadas. Este artigo descreve brevemente esses conceitos.

Este artigo é um bom ponto de partida se não estiver familiarizado com o Mobile Engagement. Certifique-se também de que lê a documentação específica da plataforma que está a utilizar, uma vez que tal irá refinar os conceitos descritos neste artigo com mais detalhes e exemplos, bem como limitações possíveis.

## <a name="devices-and-users"></a>Dispositivos e utilizadores
O Mobile Engagement identifica os utilizadores através da geração de um identificador exclusivo para cada dispositivo. Este identificador é designado identificador do dispositivo (ou `deviceid`). Este é gerado de tal forma que todas as aplicações em execução no mesmo dispositivo partilham o mesmo identificador de dispositivo.

Implicitamente, tal significa que o Mobile Engagement considera que um dispositivo pertence a exatamente um utilizador e, assim, os utilizadores e dispositivos são conceitos equivalentes.

## <a name="sessions-and-activities"></a>Sessões e atividades
Uma sessão corresponde a uma utilização da aplicação realizada por um utilizador, desde a altura em que o utilizador começa a utilizá-la, até que a termina.

Uma atividade corresponde a uma utilização de uma determinada parte secundária da aplicação realizada por um utilizador (é normalmente um ecrã, mas pode ser qualquer coisa adequada à aplicação).

Um utilizador pode realizar apenas uma atividade de cada vez.

Uma atividade é identificada por um nome (limitado a 64 carateres) e, opcionalmente, pode incorporar alguns dados adicionais (com um limite de 1024 bytes).

As sessões são automaticamente calculadas a partir da sequência das atividades realizadas pelos utilizadores. Uma sessão é iniciada quando o utilizador começa a primeira atividade e termina quando este conclui a última atividade. Tal significa que uma sessão não precisa de ser explicitamente iniciada ou interrompida. Em vez disso, as atividades são explicitamente iniciadas ou interrompidas. Se nenhuma atividade for comunicada, nenhuma sessão será comunicada.

## <a name="events"></a>Eventos
Os eventos são utilizados para comunicar ações instantâneas (como um botão premido ou artigos lidos por utilizadores).

Um evento pode estar relacionado à sessão atual, a uma tarefa em execução ou pode ser um evento autónomo.

Um evento é identificado por um nome (limitado a 64 carateres) e, opcionalmente, pode incorporar alguns dados adicionais (com um limite de 1024 bytes).

## <a name="error"></a>Erro
Os erros são utilizados para comunicar problemas corretamente detetados pela aplicação (como ações de utilizador incorretas ou falhas de chamada à API).

Um erro pode estar relacionado à sessão atual, a uma tarefa em execução ou pode ser um erro autónomo.

Um erro é identificado por um nome (limitado a 64 carateres) e, opcionalmente, pode incorporar alguns dados adicionais (com um limite de 1024 bytes).

## <a name="job"></a>Tarefa
As tarefas são utilizadas para comunicar ações com uma duração (como a duração de chamadas à API, unidade de tempo dos anúncios, duração das tarefas em segundo plano ou duração das ações do utilizador).

Uma tarefa não está relacionada a uma sessão, porque pode ser realizada em segundo plano, sem qualquer interação do utilizador.

Uma tarefa é identificada por um nome (limitado a 64 carateres) e, opcionalmente, pode incorporar alguns dados adicionais (com um limite de 1024 bytes).

## <a name="crash"></a>Falha de sistema
As falhas de sistema são emitidas automaticamente pelo SDK do Mobile Engagement de forma a comunicar erros das aplicações provocados por problemas não detetados pela aplicação e que levam ao seu encerramento.

## <a name="application-information"></a>Informações da aplicação
As informações da aplicação são utilizadas para assinalar utilizadores, isto é, para associar alguns dados aos utilizadores de uma aplicação (isto é semelhante aos cookies da Web, à exceção de as informações da aplicação serem armazenadas no lado do servidor na plataforma Azure Mobile Engagement).

As informações da aplicação podem ser registadas utilizando a API do SDK do Mobile Engagement ou utilizando a API de Dispositivo da plataforma Mobile Engagement.

As informações da aplicação são um par chave/valor associado a um dispositivo. A chave é o nome das informações da aplicação (limitado a 64 letras ASCII [a-zA-Z], números [0-9] e carateres de sublinhado [_]). O valor (limitado a 1024 carateres) pode ser qualquer cadeia, número inteiro, data (aaaa-MM-dd) ou booleano (verdadeiro ou falso).

Quaisquer informações da aplicação podem ser associadas a um dispositivo, dentro dos limites definidos pelos termos dos preços do Mobile Engagement. Numa determinada chave, o Mobile Engagement mantém apenas um registo do conjunto de valor mais recente (nenhum histórico). Definir ou alterar o valor das informações da aplicação força o Mobile Engagement a reavaliar o conjunto de critérios da audiência nestas informações da aplicação (se aplicável), o que significa que as informações da aplicação podem ser utilizadas para acionar os pushes em tempo real.

## <a name="extra-data"></a>Dados adicionais
Os dados adicionais (ou extras) são alguns dados arbitrários que podem ser anexados a eventos, erros, atividades e tarefas.

Os extras estão estruturados do mesmo modo que os objetos JSON: são compostos por uma árvore de pares chave/valor. As chaves estão limitadas a 64 letras ASCII [a-zA-Z], números [0-9] e carateres de sublinhado [_]) e o tamanho total dos extras está limitado a 1024 carateres (uma vez codificados em JSON pelo SDK do Mobile Engagement).

A árvore completa de pares chave/valor está armazenada como um objeto JSON. No entanto, apenas o primeiro nível das chaves/valores está decomposto para ser diretamente acessível para algumas funções avançadas, tal como Segmentos (por exemplo, pode facilmente definir um segmento denominado “Fãs de Ficção Científica”, composto por todos os utilizadores que enviaram, pelo menos, 10 vezes o evento chamado “conteúdo_visualziado” com a chave adicional “tipo_conteúdo” definido para o valor “ficção científica” no último mês). Por conseguinte, é altamente recomendado enviar apenas extras constituídos por listas simples de pares chave/valor utilizando valores escalares (por exemplo, cadeias, datas, números inteiros ou booleano).

## <a name="next-steps"></a>Passos seguintes
* [Descrição geral do SDK do Universal do Windows para o Azure Mobile Engagement](mobile-engagement-windows-store-sdk-overview.md)
* [Descrição geral do SDK do Windows Phone Silverlight para o Azure Mobile Engagement](mobile-engagement-windows-phone-sdk-overview.md)
* [SDK do iOS do Azure Mobile Engagement](mobile-engagement-ios-sdk-overview.md)
* [SDK do Android do Azure Mobile Engagement](mobile-engagement-android-sdk-overview.md)

