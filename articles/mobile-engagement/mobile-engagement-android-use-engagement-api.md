---
title: Como utilizar o Engagement API no Android
description: SDK Android mais recente - como utilizar o Engagement API no Android
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 09b62659-82ae-4a55-8784-fca0b6b22eaf
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: na
ms.topic: article
ms.date: 07/25/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: d353cd2fe47c54a0282cc5bb1b22b4a56e0cd82c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-the-engagement-api-on-android"></a>Como utilizar o Engagement API no Android
Este documento é um suplemento para o documento [opções avançadas de relatórios para o Android SDK do Mobile Engagement](mobile-engagement-android-advanced-reporting.md). Fornece na profundidade obter detalhes sobre como utilizar a API de envolvimento para comunicar as estatísticas da aplicação.

Tenha em atenção que se pretender que apenas o Engagement comunicar da sua aplicação sessões, atividades, as falhas e obter informações técnicas, em seguida, a forma mais simples consiste em efetuar todas as suas `Activity` classes secundárias herdarem correspondente `EngagementActivity` classe.

Se pretender obter mais informações, por exemplo, se precisar de comunicar eventos específicos de aplicação, erros e tarefas, ou se tiver de comunicar as atividades da aplicação de forma diferente que aquele implementado no `EngagementActivity` classes, em seguida, tem de utilizar a API do Engagement.

O Engagement API é fornecida pelo `EngagementAgent` classe. Uma instância desta classe pode ser obtida ao chamar o `EngagementAgent.getInstance(Context)` método estático (tenha em atenção que o `EngagementAgent` objeto devolvido é um singleton).

## <a name="engagement-concepts"></a>Conceitos de envolvimento
As seguintes partes refinar comuns [conceitos do Mobile Engagement](mobile-engagement-concepts.md), para a plataforma Android.

### <a name="session-and-activity"></a>`Session` e `Activity`
Se o utilizador mantém-se mais do que alguns segundos de inatividade entre duas *atividades*, em seguida, a sequência de *atividades* é dividida em duas distintos *sessões*. Estes alguns segundos são denominados "tempo limite da sessão".

Um *atividade* é normalmente associadas um ecrã da aplicação, que é diga a *atividade* inicia quando o ecrã é apresentado e para quando o ecrã é fechado: for este o caso quando o Engagement SDK é integrado utilizando o `EngagementActivity` classes.

Mas *atividades* também pode ser controlado manualmente utilizando a API do Engagement. Isto permite a dividir um ecrã específico em várias partes de subchaves para obter mais detalhes sobre a utilização deste ecrã (por exemplo ao conhecido frequência e quanto as caixas de diálogo são utilizadas dentro este ecrã).

## <a name="reporting-activities"></a>Relatório de atividades
> [!IMPORTANT]
> Não precisa de atividades, como o relatório descrito nesta secção se estiver a utilizar o `EngagementActivity` classe e o respetivos variantes conforme explicado na como integrar o Engagement num documento do Android.
> 
> 

### <a name="user-starts-a-new-activity"></a>Utilizador inicia uma nova atividade
            EngagementAgent.getInstance(this).startActivity(this, "MyUserActivity", null);
            // Passing the current activity is required for Reach to display in-app notifications, passing null will postpone such announcements and polls.

Tem de chamar `startActivity()` cada vez que as alterações de atividade do utilizador. A primeira chamada para esta função inicia uma nova sessão de utilizador.

O melhor local para chamar esta função é em cada atividade `onResume` chamada de retorno.

### <a name="user-ends-his-current-activity"></a>Utilizador termina a atividade atual
            EngagementAgent.getInstance(this).endActivity();

Tem de chamar `endActivity()` , pelo menos, uma vez quando o utilizador conclui a última atividade. Informa o Engagement SDK que o utilizador está atualmente inativo, e que a sessão do utilizador tem de ser fechados uma vez o tempo limite da sessão irá expirar (se chamar `startActivity()` antes do tempo limite da sessão expira, a sessão é simplesmente retomada).

O melhor local para chamar esta função é em cada atividade `onPause` chamada de retorno.

## <a name="reporting-events"></a>Eventos de relatório
### <a name="session-events"></a>Eventos da sessão
Eventos da sessão, normalmente, são utilizados para comunicar as ações executadas por um utilizador durante a sua sessão.

**Exemplo sem dados adicionais:**

            public MyActivity extends EngagementActivity {
               [...]
               @Override
               public boolean onPrepareOptionsMenu(Menu menu) {
                  getEngagementAgent().sendSessionEvent("menu_shown", null);
               }
               [...]
            }

**Exemplo com dados adicionais:**

            public MyActivity extends EngagementActivity {
              [...]
              @Override
              public boolean onMenuItemSelected(int featureId, MenuItem item) {
                Bundle extras = new Bundle();
                extras.putInt("id", item.getItemId());
                getEngagementAgent().sendSessionEvent("menu_selected", extras);
              }
              [...]
            }

### <a name="standalone-events"></a>Eventos de autónomo
Contrária ao disposto eventos da sessão, podem ocorrer eventos de autónomo fora do contexto de uma sessão.

**Exemplo:**

Suponha que pretende ocorrer quando é acionado um recetor de difusão de eventos de relatório:

            /** Triggered by Intent.ACTION_BATTERY_LOW */
            public BatteryLowReceiver extends BroadcastReceiver {
              [...]
              @Override
              public void onReceive(Context context, Intent intent) {
                EngagementAgent.getInstance(context).sendEvent("battery_low", null);
              }
              [...]
            }

## <a name="reporting-errors"></a>Relatório de erros
### <a name="session-errors"></a>Erros de sessão
Erros de sessão, normalmente, são utilizados para comunicar os erros afetar o utilizador durante a sua sessão.

**Exemplo:**

            /** The user has entered invalid data in a form */
            public MyActivity extends EngagementActivity {
              [...]
              public void onMyFormSubmitted(MyForm form) {
                [...]
                /* The user has entered an invalid email address */
                getEngagementAgent().sendSessionError("sign_up_email", null);
                [...]
              }
              [...]
            }

### <a name="standalone-errors"></a>Erros de autónomo
Contrária ao disposto erros de sessão, podem ocorrer erros de autónomo fora do contexto de uma sessão.

**Exemplo:**

O exemplo seguinte mostra como um erro de relatórios sempre que a memória fica baixa no telemóvel enquanto o processo de aplicação está em execução.

            public MyApplication extends EngagementApplication {

              @Override
              protected void onApplicationProcessLowMemory() {
                EngagementAgent.getInstance(this).sendError("low_memory", null);
              }
            }

## <a name="reporting-jobs"></a>Tarefas de relatório
### <a name="example"></a>Exemplo
Suponha que pretende comunicar a duração do processo de início de sessão:

            [...]
            public void signIn(Context context, ...) {

              /* We need an Android context to call the Engagement API, if you are extending Activity, Service, you can pass "this" */
              EngagementAgent engagementAgent = EngagementAgent.getInstance(context);

              /* Report sign in job has started */
              engagementAgent.startJob("sign_in", null);

              [... sign in ...]

              /* Report sign in job is now ended */
              engagementAgent.endJob("sign_in");
            }
            [...]

### <a name="report-errors-during-a-job"></a>Relatório de erros durante a uma tarefa
Erros podem estar relacionado com uma tarefa em execução em vez de a ser relacionado à sessão do utilizador atual.

**Exemplo:**

Suponha que pretende um erro durante a iniciar sessão no processo de relatório:

[...] signIn void público (contexto contexto,...) {

              /* We need an Android context to call the Engagement API, if you are extending Activity, Service, you can pass "this" */
              EngagementAgent engagementAgent = EngagementAgent.getInstance(context);

              /* Report sign in job has been started */
              engagementAgent.startJob("sign_in", null);

              /* Try to sign in */
              while(true)
                try {
                  trySignin();
                  break;
                }
                catch(Exception e) {
                  /* Report the error to Engagement */
                  engagementAgent.sendJobError("sign_in_error", "sign_in", null);

                  /* Retry after a moment */
                  sleep(2000);
                }
              [...]
              /* Report sign in job is now ended */
              engagementAgent.endJob("sign_in");
            }
            [...]

### <a name="reporting-events-during-a-job"></a>Eventos de relatório durante uma tarefa
Eventos podem estar relacionado com uma tarefa em execução em vez de a ser relacionado à sessão do utilizador atual.

**Exemplo:**

Suponha que temos uma rede social e utilizamos uma tarefa de relatório o total de tempo durante os quais o utilizador está ligado ao servidor. O utilizador pode permaneça ligado em segundo plano, mesmo quando ele está a utilizar outra aplicação ou quando o telefone está suspenso, pelo que não existe nenhuma sessão.

O utilizador pode receber mensagens dos seus amigos, este é um evento de tarefa.

            [...]
            public void signin(Context context, ...) {
              [...Sign in code...]
              EngagementAgent.getInstance(context).startJob("connection", null);
            }
            [...]
            public void signout(Context context) {
              [...Sign out code...]
              EngagementAgent.getInstance(context).endJob("connection");
            }
            [...]
            public void onMessageReceived(Context context) {
              [...Notify in status bar...]
              EngagementAgent.getInstance(context).sendJobEvent("message_received", "connection", null);
            }
            [...]

## <a name="extra-parameters"></a>Parâmetros adicionais
Dados arbitrários podem ser anexados a eventos, erros, atividades e tarefas.

Estes dados podem ser estruturados, utiliza a classe de pacote do Android (na realidade, funciona como parâmetros adicionais no Android pendentes). Tenha em atenção que um grupo pode conter matrizes ou outro instâncias de pacote.

> [!IMPORTANT]
> Se o put nos parâmetros parcelable ou serializáveis, certifique-se os seus `toString()` método é implementado para devolver uma cadeia legível por humanos. Classes serializáveis que contêm campos não transitórios que não são serializáveis tornará falhas Android quando irá chamar`bundle.putSerializable("key",value);`
> 
> [!WARNING]
> Não são suportadas matrizes dispersas nos parâmetros adicionais, ou seja, não irá ser serializada como uma matriz. Deverá convertê-los em matrizes padrão antes de a utilizar nos parâmetros adicionais.
> 
> 

### <a name="example"></a>Exemplo
            Bundle extras = new Bundle();
            extras.putString("video_id", 123);
            extras.putString("ref_click", "http://foobar.com/blog");
            EngagementAgent.getInstance(context).sendEvent("video_clicked", extras);

### <a name="limits"></a>Limites
#### <a name="keys"></a>Chaves
Cada chave no `Bundle` tem de coincidir com a seguinte expressão regular:

`^[a-zA-Z][a-zA-Z_0-9]*`

Significa que as chaves tem de começar com, pelo menos, uma letra, seguido de letras, dígitos ou carateres de sublinhado (\_).

#### <a name="size"></a>Tamanho
Extras estão limitados a **1024** carateres por chamada (uma vez codificados em JSON pelo serviço Engagement).

No exemplo anterior, o JSON enviado para o servidor é 58 carateres de comprimento:

            {"ref_click":"http:\/\/foobar.com\/blog","video_id":"123"}

## <a name="reporting-application-information"></a>Informações de relatórios de aplicações
Pode comunicar manualmente controlo informações (ou quaisquer outras informações específicas da aplicação) utilizando o `sendAppInfo()` função.

Tenha em atenção que estas informações podem ser enviadas de forma incremental: o valor mais recente para uma determinada chave será mantido para um determinado dispositivo.

Como extras de eventos, a classe do pacote é utilizada para separar as informações da aplicação, tenha em atenção que as matrizes ou pacotes secundárias serão tratados como cadeias simples (com a serialização do JSON).

### <a name="example"></a>Exemplo
Eis um exemplo de código para enviar género de utilizador e a data de nascimento:

            Bundle appInfo = new Bundle();
            appInfo.putString("status", "premium");
            appInfo.putString("expiration", "2016-12-07"); // December 7th 2016
            EngagementAgent.getInstance(context).sendAppInfo(appInfo);

### <a name="limits"></a>Limites
#### <a name="keys"></a>Chaves
Cada chave no `Bundle` tem de coincidir com a seguinte expressão regular:

`^[a-zA-Z][a-zA-Z_0-9]*`

Significa que as chaves tem de começar com, pelo menos, uma letra, seguido de letras, dígitos ou carateres de sublinhado (\_).

#### <a name="size"></a>Tamanho
Informações sobre a aplicação está limitado a **1024** carateres por chamada (uma vez codificados em JSON pelo serviço Engagement).

No exemplo anterior, o JSON enviado para o servidor é 44 carateres de comprimento:

            {"expiration":"2016-12-07","status":"premium"}
