---
title: Web do Azure Mobile Engagement APIs do SDK | Microsoft Docs
description: "As mais recentes atualizações e procedimentos para o SDK Web do Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 8a87d5ac-d8b7-4a0d-bdee-414dbcc561b2
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: web
ms.devlang: js
ms.topic: article
ms.date: 06/07/2016
ms.author: piyushjo
ms.openlocfilehash: 54c22ce6a03e382b1bbde102bccc97deec249b30
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-azure-mobile-engagement-api-in-a-web-application"></a>Utilize a API do Azure Mobile Engagement numa aplicação web
Este documento é um complemento ao documento que indica como a [integrar o Mobile Engagement numa aplicação web](mobile-engagement-web-integrate-engagement.md). Fornece detalhes aprofundados sobre como utilizar a API do Azure Mobile Engagement para comunicar as estatísticas da aplicação.

A API do Mobile Engagement é fornecida pelo `engagement.agent` objeto. A predefinição de alias é SDK Web do Azure Mobile Engagement `engagement`. Pode redefinir este alias da configuração do SDK.

## <a name="mobile-engagement-concepts"></a>Conceitos do Mobile Engagement
As seguintes partes refinar comuns [conceitos do Mobile Engagement](mobile-engagement-concepts.md) para a plataforma web.

### <a name="session-and-activity"></a>`Session` e `Activity`
Se o utilizador permanece inativo durante mais do que alguns segundos, entre duas atividades, a sequência do utilizador de atividades é dividida em duas sessões distintas. Estes alguns segundos são denominados o tempo limite da sessão.

Se a aplicação web não declara o fim das atividades de utilizador por si só (chamando a `engagement.agent.endActivity` função), o servidor de Mobile Engagement expira automaticamente a sessão do utilizador dentro de três minutos depois de fechar a página da aplicação. Isto denomina-se o tempo de limite de sessão do servidor.

### `Crash`
Relatórios automatizados de exceções de JavaScript não identificadas não estão criados por predefinição. No entanto, pode reportar falhas manualmente utilizando o `sendCrash` funcionar (consulte a secção sobre relatórios de falhas).

## <a name="reporting-activities"></a>Relatório de atividades
Atividade do utilizador de relatórios incluem quando um utilizador inicia uma nova atividade e quando o utilizador termina a atividade atual.

### <a name="user-starts-a-new-activity"></a>Utilizador inicia uma nova atividade
    engagement.agent.startActivity("MyUserActivity");

Tem de chamar `startActivity()` cada atividade de utilizador de hora é alterado. A primeira chamada para esta função inicia uma nova sessão de utilizador.

### <a name="user-ends-the-current-activity"></a>Utilizador termina a atividade atual
    engagement.agent.endActivity();

Tem de chamar `endActivity()` , pelo menos, uma vez quando o utilizador terminar, a última atividade dos mesmos. Informa o SDK do Mobile Engagement Web que o utilizador está atualmente inativo, e que a sessão do utilizador tem de ser fechada depois do tempo limite da sessão expira. Se chamar `startActivity()` antes do tempo limite da sessão expira, a sessão é simplesmente retomada.

Porque não existe nenhuma chamada fiável para que a janela do navegador é fechada, muitas vezes é difícil ou impossível detetar o fim das atividades de utilizador dentro de um ambiente de web. É por esse motivo o servidor de Mobile Engagement expira automaticamente a sessão do utilizador dentro de três minutos depois de fechar a página da aplicação.

## <a name="reporting-events"></a>Eventos de relatório
Eventos de relatório abrange eventos da sessão e eventos de autónomo.

### <a name="session-events"></a>Eventos da sessão
Normalmente, os eventos da sessão são utilizados para comunicar as ações executadas por um utilizador durante a sessão do utilizador.

**Exemplo sem dados adicionais:**

    loginButton.onclick = function() {
      engagement.agent.sendSessionEvent('login');
      // [...]
    }

**Exemplo com dados adicionais:**

    loginButton.onclick = function() {
      engagement.agent.sendSessionEvent('login', {user: 'alice'});
      // [...]
    }

### <a name="standalone-events"></a>Eventos de autónomo
Ao contrário de eventos da sessão, podem ocorrer eventos de autónomo fora do contexto de uma sessão.

Para tal, utilize ``engagement.agent.sendEvent`` em vez de ``engagement.agent.sendSessionEvent``.

## <a name="reporting-errors"></a>Relatório de erros
Relatório de erros abrange erros de sessão e erros de autónomo.

### <a name="session-errors"></a>Erros de sessão
Normalmente, os erros de sessão são utilizados para comunicar os erros que tem um impacto no utilizador durante a sessão do utilizador.

**Exemplo sem dados adicionais:**

    var validateForm = function() {
      // [...]
      if (password.length < 6) {
        engagement.agent.sendSessionError('password_too_short');
      }
      // [...]
    }

**Exemplo com dados adicionais:**

    var validateForm = function() {
      // [...]
      if (password.length < 6) {
        engagement.agent.sendSessionError('password_too_short', {length: 4});
      }
      // [...]
    }

### <a name="standalone-errors"></a>Erros de autónomo
Ao contrário dos erros de sessão, podem ocorrer erros de autónomo fora do contexto de uma sessão.

Para tal, utilize `engagement.agent.sendError` em vez de `engagement.agent.sendSessionError`.

## <a name="reporting-jobs"></a>Tarefas de relatório
Elaboração de relatórios em tarefas bastidores erros e eventos que ocorrem durante uma tarefa de relatórios e relatórios de falhas.

**Exemplo:**

Se pretender monitorizar um pedido de AJAX, utilizaria o seguinte:

    // [...]
    xhr.onreadystatechange = function() {
      if (xhr.readyState == 4) {
      // [...]
        engagement.agent.endJob('publish');
      }
    }
    engagement.agent.startJob('publish');
    xhr.send();
    // [...]

### <a name="reporting-errors-during-a-job"></a>Relatório de erros durante a uma tarefa
Erros podem estar relacionado com uma tarefa em execução em vez de à sessão do utilizador atual.

**Exemplo:**

Se pretender reportar um erro se um pedido de AJAX falhar:

    // [...]
    xhr.onreadystatechange = function() {
      if (xhr.readyState == 4) {
        // [...]
        if (xhr.status == 0 || xhr.status >= 400) {
          engagement.agent.sendJobError('publish_xhr', 'publish', {status: xhr.status, statusText: xhr.statusText});
        }
        engagement.agent.endJob('publish');
      }
    }
    engagement.agent.startJob('publish');
    xhr.send();
    // [...]

### <a name="reporting-events-during-a-job"></a>Eventos de relatório durante uma tarefa
Eventos podem estar relacionado com a uma tarefa em execução em vez da sessão do utilizador atual, thanks o `engagement.agent.sendJobEvent` função.

Esta função funciona exatamente como `engagement.agent.sendJobError`.

### <a name="reporting-crashes"></a>Relatório de falhas
Utilize o `sendCrash` função para o relatório de falhas manualmente.

O `crashid` argumento é uma cadeia que identifica o tipo de falha.
O `crash` argumento é, normalmente, o rastreio da pilha da falha como uma cadeia.

    engagement.agent.sendCrash(crashid, crash);

## <a name="extra-parameters"></a>Parâmetros adicionais
Pode anexar dados arbitrários para um evento, o erro, a atividade ou a tarefa.

Os dados podem ter qualquer objeto JSON (mas não uma matriz nem um tipo primitivo).

**Exemplo:**

    var extras = {"video_id": 123, "ref_click": "http://foobar.com/blog"};
    engagement.agent.sendEvent("video_clicked", extras);

### <a name="limits"></a>Limites
Os limites que se aplicam aos parâmetros adicionais são nas áreas de expressões regulares para chaves, tipos de valor e tamanho.

#### <a name="keys"></a>Chaves
Cada chave no objeto tem de corresponder a seguinte expressão regular:

    ^[a-zA-Z][a-zA-Z_0-9]*

Isto significa que as chaves tem de começar com, pelo menos, uma letra, seguido de letras, dígitos ou carateres de sublinhado (\_).

#### <a name="values"></a>Valores
Os valores estão limitados a cadeia, o número e tipos booleanos.

#### <a name="size"></a>Tamanho
Extras estão limitados a 1024 caracteres por chamada (após o SDK do Mobile Engagement Web codifica no JSON).

## <a name="reporting-application-information"></a>Informações de relatórios de aplicações
Manualmente pode comunicar controlo informações (ou outras informações específicas da aplicação), utilizando o `sendAppInfo()` função.

Tenha em atenção que estas informações podem ser enviadas de forma incremental. O valor mais recente para uma chave específica será mantido para um dispositivo específico.

Como extras de eventos, pode utilizar qualquer objeto JSON de separar as informações da aplicação. Tenha em atenção que as matrizes ou objetos secundárias são tratados como cadeias simples (com a serialização do JSON).

**Exemplo:**

Eis um exemplo de código para enviar sexo do utilizador e a data de nascimento:

    var appInfos = {"birthdate":"1983-12-07","gender":"female"};
    engagement.agent.sendAppInfo(appInfos);

### <a name="limits"></a>Limites
Os limites que se aplicam a informação da aplicação são nas áreas de expressões regulares para chaves e tamanho.

#### <a name="keys"></a>Chaves
Cada chave no objeto tem de corresponder a seguinte expressão regular:

    ^[a-zA-Z][a-zA-Z_0-9]*

Isto significa que as chaves tem de começar com, pelo menos, uma letra, seguido de letras, dígitos ou carateres de sublinhado (\_).

#### <a name="size"></a>Tamanho
Informações sobre a aplicação está limitado a 1024 caracteres por chamada (após o SDK do Mobile Engagement Web codifica no JSON).

No exemplo anterior, o JSON enviado para o servidor é 44 carateres de comprimento:

    {"birthdate":"1983-12-07","gender":"female"}
