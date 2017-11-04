---
title: Como utilizar o Engagement API no iOS
description: SDK - mais recente do iOS como utilizar o Engagement API no iOS
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 1fb4509e-3804-46c1-949f-1cf727f91f9f
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: na
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: a31424da98205e97bdf57010cccfd044360f03dd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-the-engagement-api-on-ios"></a>Como utilizar o Engagement API no iOS
Este documento é um suplemento para o documento como integrar o Engagement no iOS: fornece na profundidade obter detalhes sobre como utilizar a API de envolvimento para comunicar as estatísticas da aplicação.

Tenha em atenção que se pretender que apenas o Engagement comunicar da sua aplicação sessões, atividades, as falhas e obter informações técnicas, em seguida, a forma mais simples é certificar-todos os seus personalizada `UIViewController` objetos herdarem correspondente `EngagementViewController` classe.

Se pretender obter mais informações, por exemplo, se precisar de comunicar eventos específicos de aplicação, erros e tarefas, ou se tiver de comunicar as atividades da aplicação de forma diferente que aquele implementado no `EngagementViewController` classes, em seguida, tem de utilizar a API do Engagement.

O Engagement API é fornecida pelo `EngagementAgent` classe. Uma instância desta classe pode ser obtida ao chamar o `[EngagementAgent shared]` método estático (tenha em atenção que o `EngagementAgent` objeto devolvido é um singleton).

Antes de quaisquer chamadas de API, o `EngagementAgent` objeto tem de ser inicializado ao chamar o método`[EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];`

## <a name="engagement-concepts"></a>Conceitos de envolvimento
As seguintes partes refinar comuns [conceitos do Mobile Engagement](mobile-engagement-concepts.md) para a plataforma iOS.

### <a name="session-and-activity"></a>`Session` e `Activity`
Um *atividade* é normalmente associadas um ecrã da aplicação, que é diga a *atividade* inicia quando o ecrã é apresentado e para quando o ecrã é fechado: for este o caso quando o Engagement SDK é integrado utilizando o `EngagementViewController` classes.

Mas *atividades* também pode ser controlado manualmente utilizando a API do Engagement. Isto permite a dividir um ecrã específico em várias partes de subchaves para obter mais detalhes sobre a utilização deste ecrã (por exemplo ao conhecido frequência e quanto as caixas de diálogo são utilizadas dentro este ecrã).

## <a name="reporting-activities"></a>Relatório de atividades
### <a name="user-starts-a-new-activity"></a>Utilizador inicia uma nova atividade
            [[EngagementAgent shared] startActivity:@"MyUserActivity" extras:nil];

Tem de chamar `startActivity()` cada vez que as alterações de atividade do utilizador. A primeira chamada para esta função inicia uma nova sessão de utilizador.

### <a name="user-ends-his-current-activity"></a>Utilizador termina a atividade atual
            [[EngagementAgent shared] endActivity];

> [!WARNING]
> Deve **nunca** chamar esta função por si, exceto se pretender dividir uma utilização da sua aplicação para várias sessões: uma chamada para esta função seria terminar a sessão atual imediatamente, por isso, uma chamada subsequente para `startActivity()` seria iniciar uma sessão de novo. Esta função é chamada automaticamente pelo SDK quando a aplicação está fechada.
> 
> 

## <a name="reporting-events"></a>Eventos de relatório
### <a name="session-events"></a>Eventos da sessão
Eventos da sessão, normalmente, são utilizados para comunicar as ações executadas por um utilizador durante a sua sessão.

**Exemplo sem dados adicionais:**

    @implementation MyViewController {
       [...]
       - (void)willRotateToInterfaceOrientation:(UIInterfaceOrientation)toInterfaceOrientation duration:(NSTimeInterval)duration
       {
        [super willRotateToInterfaceOrientation:toInterfaceOrientation duration:duration];
            ...
        [[EngagementAgent shared] sendSessionEvent:@"will_rotate" extras:nil];
            ...
       }
       [...]
    }

**Exemplo com dados adicionais:**

    @implementation MyViewController {
       [...]
       - (void)willRotateToInterfaceOrientation:(UIInterfaceOrientation)toInterfaceOrientation duration:(NSTimeInterval)duration
       {
        [super willRotateToInterfaceOrientation:toInterfaceOrientation duration:duration];
            ...
        NSMutableDictionary* extras = [NSMutableDictionary dictionary];
        [extras setObject:[NSNumber numberWithInt:toInterfaceOrientation] forKey:@"to_orientation_id"];
        [extras setObject:[NSNumber numberWithDouble:duration] forKey:@"duration"];
        [[EngagementAgent shared] sendSessionEvent:@"will_rotate" extras:extras];
            ...
       }
       [...]
    }

### <a name="standalone-events"></a>Eventos de autónomo
Contrária ao disposto eventos da sessão, os eventos de autónomo podem ser utilizados fora do contexto de uma sessão.

**Exemplo:**

    [[EngagementAgent shared] sendEvent:@"received_notification" extras:nil];

## <a name="reporting-errors"></a>Relatório de erros
### <a name="session-errors"></a>Erros de sessão
Erros de sessão, normalmente, são utilizados para comunicar os erros afetar o utilizador durante a sua sessão.

**Exemplo:**

    /** The user has entered invalid data in a form */
    @implementation MyViewController {
      [...]
      -(void)onMyFormSubmitted:(MyForm*)form {
        [...]
        /* The user has entered an invalid email address */
        [[EngagementAgent shared] sendSessionError:@"sign_up_email" extras:nil]
        [...]
      }
      [...]
    }

### <a name="standalone-errors"></a>Erros de autónomo
Contrária ao disposto erros de sessão, erros autónomo podem ser utilizados fora do contexto de uma sessão.

**Exemplo:**

    [[EngagementAgent shared] sendError:@"something_failed" extras:nil];

## <a name="reporting-jobs"></a>Tarefas de relatório
**Exemplo:**

Suponha que pretende comunicar a duração do processo de início de sessão:

    [...]
    -(void)signIn
    {
      /* Start job */
      [[EngagementAgent shared] startJob:@"sign_in" extras:nil];

      [... sign in ...]

      /* End job */
      [[EngagementAgent shared] endJob:@"sign_in"];
    }
    [...]

### <a name="report-errors-during-a-job"></a>Relatório de erros durante a uma tarefa
Erros podem estar relacionado com uma tarefa em execução em vez de a ser relacionado à sessão do utilizador atual.

**Exemplo:**

Suponha que pretende comunicar um erro durante o processo de início de sessão:

    [...]
    -(void)signin
    {
      /* Start job */
      [[EngagementAgent shared] startJob:@"sign_in" extras:nil];

      BOOL success = NO;
      while (!success) {
        /* Try to sign in */
        NSError* error = nil;
        [self trySigin:&error];
        success = error == nil;

        /* If an error occured report it */
        if(!success)
        {
          [[EngagementAgent shared] sendJobError:@"sign_in_error"
                         jobName:@"sign_in"
                          extras:[NSDictionary dictionaryWithObject:[error localizedDescription] forKey:@"error"]];

          /* Retry after a moment */
          [NSThread sleepForTimeInterval:20];
        }
      }

      /* End job */
      [[EngagementAgent shared] endJob:@"sign_in"];
    };
    [...]

### <a name="events-during-a-job"></a>Eventos durante uma tarefa
Eventos podem estar relacionado com uma tarefa em execução em vez de a ser relacionado à sessão do utilizador atual.

**Exemplo:**

Suponha que temos uma rede social e utilizamos uma tarefa de relatório o total de tempo durante os quais o utilizador está ligado ao servidor. O utilizador pode receber mensagens dos seus amigos, este é um evento de tarefa.

    [...]
    - (void) signin
    {
      [...Sign in code...]
      [[EngagementAgent shared] startJob:@"connection" extras:nil];
    }
    [...]
    - (void) signout
    {
      [...Sign out code...]
      [[EngagementAgent shared] endJob:@"connection"];
    }
    [...]
    - (void) onMessageReceived
    {
      [...Notify user...]
      [[EngagementAgent shared] sendJobEvent:@"connection" jobName:@"message_received" extras:nil];
    }
    [...]

## <a name="extra-parameters"></a>Parâmetros adicionais
Dados arbitrários podem ser anexados a eventos, erros, atividades e tarefas.

Estes dados podem ser estruturados, utiliza a classe de NSDictionary do iOS.

Tenha em atenção que podem conter extras `arrays(NSArray, NSMutableArray)`, `numbers(NSNumber class)`, `strings(NSString, NSMutableString)`, `urls(NSURL)`, `data(NSData, NSMutableData)` ou outros `NSDictionary` instâncias.

> [!NOTE]
> O parâmetro extra é serializado no JSON. Se pretender passar a diferentes objetos que o descrito acima, tem de implementar o método seguinte na sua classe:
> 
> -(NSString*) JSONRepresentation;
> 
> O método deverá devolver uma representação JSON do seu objeto.
> 
> 

### <a name="example"></a>Exemplo
    NSMutableDictionary* extras = [NSMutableDictionary dictionaryWithCapacity:2];
    [extras setObject:[NSNumber numberWithInt:123] forKey:@"video_id"];
    [extras setObject:@"http://foobar.com/blog" forKey:@"ref_click"];
    [[EngagementAgent shared] sendEvent:@"video_clicked" extras:extras];

### <a name="limits"></a>Limites
#### <a name="keys"></a>Chaves
Cada chave no `NSDictionary` tem de coincidir com a seguinte expressão regular:

`^[a-zA-Z][a-zA-Z_0-9]*`

Significa que as chaves tem de começar com, pelo menos, uma letra, seguido de letras, dígitos ou carateres de sublinhado (\_).

#### <a name="size"></a>Tamanho
Extras estão limitados a **1024** carateres por chamada (uma vez codificados em JSON pelo agente Engagement).

No exemplo anterior, o JSON enviado para o servidor é 58 carateres de comprimento:

    {"ref_click":"http:\/\/foobar.com\/blog","video_id":"123"}

## <a name="reporting-application-information"></a>Informações de relatórios de aplicações
Pode comunicar manualmente controlo informações (ou quaisquer outras informações específicas da aplicação) utilizando o `sendAppInfo:` função.

Tenha em atenção que estas informações podem ser enviadas de forma incremental: o valor mais recente para uma determinada chave será mantido para um determinado dispositivo.

Como extras de evento, o `NSDictionary` classe é utilizada para separar as informações da aplicação, tenha em atenção que as matrizes ou dicionários secundárias serão tratados como cadeias simples (com a serialização do JSON).

**Exemplo:**

    NSMutableDictionary* appInfo = [NSMutableDictionary dictionaryWithCapacity:2];
    [appInfo setObject:@"female" forKey:@"gender"];
    [appInfo setObject:@"1983-12-07" forKey:@"birthdate"]; // December 7th 1983
    [[EngagementAgent shared] sendAppInfo:appInfo];

### <a name="limits"></a>Limites
#### <a name="keys"></a>Chaves
Cada chave no `NSDictionary` tem de coincidir com a seguinte expressão regular:

`^[a-zA-Z][a-zA-Z_0-9]*`

Significa que as chaves tem de começar com, pelo menos, uma letra, seguido de letras, dígitos ou carateres de sublinhado (\_).

#### <a name="size"></a>Tamanho
Informações sobre a aplicação está limitado a **1024** carateres por chamada (uma vez codificados em JSON pelo agente Engagement).

No exemplo anterior, o JSON enviado para o servidor é 44 carateres de comprimento:

    {"birthdate":"1983-12-07","gender":"female"}
