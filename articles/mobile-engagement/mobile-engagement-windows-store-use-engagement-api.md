---
title: Como utilizar o Engagement API no Windows Universal
description: Como utilizar o Engagement API no Windows Universal
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: bb501fca-9cfe-4495-81df-b5efd6e0137b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 75fc134a5535e6113331470cf61df9c06eb8e2ab
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-the-engagement-api-on-windows-universal"></a>Como utilizar o Engagement API no Windows Universal
Este documento é um suplemento para o documento [como integrar o Engagement no Windows Universal](mobile-engagement-windows-store-integrate-engagement.md): fornece na profundidade obter detalhes sobre como utilizar a API de envolvimento para comunicar as estatísticas da aplicação.

Tenha em atenção que se pretender que apenas o Engagement comunicar da sua aplicação sessões, atividades, as falhas e obter informações técnicas, em seguida, a forma mais simples consiste em efetuar todas as suas `Page` classes secundárias herdarem do `EngagementPage` classe.

Se pretender obter mais informações, por exemplo, se precisar de comunicar eventos específicos de aplicação, erros e tarefas, ou se tiver de comunicar as atividades da aplicação de forma diferente que aquele implementado no `EngagementPage` classes, em seguida, tem de utilizar a API do Engagement.

O Engagement API é fornecida pelo `EngagementAgent` classe. É possível aceder a esses métodos através de `EngagementAgent.Instance`.

Mesmo que o módulo de agente não foi inicializado, cada chamada à API do é deferida e será executada novamente quando o agente está disponível.

## <a name="engagement-concepts"></a>Conceitos de envolvimento
As seguintes partes refinar comuns [conceitos do Mobile Engagement](mobile-engagement-concepts.md) para a plataforma Universal do Windows.

### <a name="session-and-activity"></a>`Session` e `Activity`
Um *atividade* é normalmente associadas uma página da aplicação, que é diga a *atividade* inicia quando a página é apresentada e para quando a página é fechada: for este o caso quando o Engagement SDK é integrado utilizando o `EngagementPage` classe.

Mas *atividades* também pode ser controlado manualmente utilizando a API do Engagement. Isto permite-lhe dividir uma determinada página em várias partes de subchaves para obter mais detalhes sobre a utilização desta página (por exemplo, para saber a frequência e quanto caixas de diálogo são utilizadas dentro desta página).

## <a name="reporting-activities"></a>Relatório de atividades
### <a name="user-starts-a-new-activity"></a>Utilizador inicia uma nova atividade
#### <a name="reference"></a>Referência
            void StartActivity(string name, Dictionary<object, object> extras = null)

Tem de chamar `StartActivity()` cada vez que as alterações de atividade do utilizador. A primeira chamada para esta função inicia uma nova sessão de utilizador.

> [!IMPORTANT]
> O SDK automaticamente chama o método de EndActivity quando a aplicação está fechada. Assim, é altamente recomendado para chamar o método de StartActivity sempre que a atividade das alterações de utilizador e nunca chamar o método de EndActivity, uma vez que chamar este método força a sessão atual para terminado.
> 
> 

#### <a name="example"></a>Exemplo
            EngagementAgent.Instance.StartActivity("main", new Dictionary<object, object>() {{"example", "data"}});

### <a name="user-ends-his-current-activity"></a>Utilizador termina a atividade atual
#### <a name="reference"></a>Referência
            void EndActivity()

Isto termina a sessão e a atividade. Não deve chamar este método, a menos que conhece a que está a fazer.

#### <a name="example"></a>Exemplo
            EngagementAgent.Instance.EndActivity();

## <a name="reporting-jobs"></a>Tarefas de relatório
### <a name="start-a-job"></a>Iniciar uma tarefa
#### <a name="reference"></a>Referência
            void StartJob(string name, Dictionary<object, object> extras = null)

Pode utilizar a tarefa para controlar tarefas certains durante um período de tempo.

#### <a name="example"></a>Exemplo
            // An upload begins...

            // Set the extras
            var extras = new Dictionary<object, object>();
            extras.Add("title", "avatar");
            extras.Add("type", "image");

            EngagementAgent.Instance.StartJob("uploadData", extras);

### <a name="end-a-job"></a>Terminar uma tarefa
#### <a name="reference"></a>Referência
            void EndJob(string name)

Assim que uma tarefa controlada por uma tarefa foi terminada, deverá chamar o método de EndJob para esta tarefa, fornecendo o nome da tarefa.

#### <a name="example"></a>Exemplo
            // In the previous section, we started an upload tracking with a job
            // Then, the upload ends

            EngagementAgent.Instance.EndJob("uploadData");

## <a name="reporting-events"></a>Eventos de relatório
Há três tipos de eventos:

* Eventos de autónomo
* Eventos da sessão
* Eventos de tarefas

### <a name="standalone-events"></a>Eventos de autónomo
#### <a name="reference"></a>Referência
            void SendEvent(string name, Dictionary<object, object> extras = null)

Podem ocorrer eventos de autónomo fora do contexto de uma sessão.

#### <a name="example"></a>Exemplo
            EngagementAgent.Instance.SendEvent("event", extra);

### <a name="session-events"></a>Eventos da sessão
#### <a name="reference"></a>Referência
            void SendSessionEvent(string name, Dictionary<object, object> extras = null)

Eventos da sessão, normalmente, são utilizados para comunicar as ações executadas por um utilizador durante a sua sessão.

#### <a name="example"></a>Exemplo
**Sem dados:**

            EngagementAgent.Instance.SendSessionEvent("sessionEvent");

            // or

            EngagementAgent.Instance.SendSessionEvent("sessionEvent", null);

**Com os dados:**

            Dictionary<object, object> extras = new Dictionary<object,object>();
            extras.Add("name", "data");
            EngagementAgent.Instance.SendSessionEvent("sessionEvent", extras);

### <a name="job-events"></a>Eventos de tarefas
#### <a name="reference"></a>Referência
            void SendJobEvent(string eventName, string jobName, Dictionary<object, object> extras = null)

Eventos de tarefas normalmente, são utilizados para comunicar as ações executadas por um utilizador durante uma tarefa.

#### <a name="example"></a>Exemplo
            EngagementAgent.Instance.SendJobEvent("eventName", "jobName", extras);

## <a name="reporting-errors"></a>Relatório de erros
Existem três tipos de erros:

* Erros de autónomo
* Erros de sessão
* Erros de tarefas

### <a name="standalone-errors"></a>Erros de autónomo
#### <a name="reference"></a>Referência
            void SendError(string name, Dictionary<object, object> extras = null)

Contrária ao disposto erros de sessão, podem ocorrer erros de autónomo fora do contexto de uma sessão.

#### <a name="example"></a>Exemplo
            EngagementAgent.Instance.SendError("errorName", extras);

### <a name="session-errors"></a>Erros de sessão
#### <a name="reference"></a>Referência
            void SendSessionError(string name, Dictionary<object, object> extras = null)

Erros de sessão, normalmente, são utilizados para comunicar os erros afetar o utilizador durante a sua sessão.

#### <a name="example"></a>Exemplo
            EngagementAgent.Instance.SendSessionError("errorName", extra);

### <a name="job-errors"></a>Erros de tarefas
#### <a name="reference"></a>Referência
            void SendJobError(string errorName, string jobName, Dictionary<object, object> extras = null)

Erros podem estar relacionado com uma tarefa em execução em vez de a ser relacionado à sessão do utilizador atual.

#### <a name="example"></a>Exemplo
            EngagementAgent.Instance.SendJobError("errorName", "jobname", extra);

## <a name="reporting-crashes"></a>Relatório de falhas
O agente fornece dois métodos para lidar com falhas.

### <a name="send-an-exception"></a>Enviar uma exceção
#### <a name="reference"></a>Referência
            void SendCrash(Exception e, bool terminateSession = false)

#### <a name="example"></a>Exemplo
Pode enviar uma exceção em qualquer altura ao chamar:

            EngagementAgent.Instance.SendCrash(aCatchedException);

Também pode utilizar um parâmetro opcional para terminar a sessão de envolvimento ao mesmo tempo que o envio de falha. Para fazê-lo, chame:

            EngagementAgent.Instance.SendCrash(new Exception("example"), terminateSession: true);

Se, fazê-lo, a sessão e as tarefas serão fechadas depois de envio de falha.

### <a name="send-an-unhandled-exception"></a>Enviar uma exceção não processada
#### <a name="reference"></a>Referência
            void SendCrash(Exception e)

Engagement também fornece um método para enviar exceções não processadas se tiver **DESATIVADO** Engagement automático **falhas** Reporting Services. Isto é especialmente útil quando utilizada dentro de processador de eventos de UnhandledException de aplicação.

Este método irá **sempre** terminar a sessão de envolvimento e tarefas depois de a ser chamado.

#### <a name="example"></a>Exemplo
Pode utilizá-lo para implementar o suas próprias processador UnhandledExceptionEventArgs. Por exemplo, adicione o `Current_UnhandledException` método o `App.xaml.cs` ficheiro:

            // In your App.xaml.cs file

            // Code to execute on Unhandled Exceptions
            void Current_UnhandledException(object sender, UnhandledExceptionEventArgs e)
            {
               EngagementAgent.Instance.SendCrash(e.Exception,false);
            }

No App.xaml.cs na "App() pública {}" Adicione:

            Application.Current.UnhandledException += Current_UnhandledException;

## <a name="device-id"></a>Id de dispositivo
            String EngagementAgent.Instance.GetDeviceId()

Pode obter o id de dispositivo do engagement ao chamar este método.

## <a name="extras-parameters"></a>Parâmetros de Extras
Dados arbitrários podem ser anexados a um evento, um erro, uma atividade ou uma tarefa. Estes dados podem ser estruturados utilizando um dicionário. As chaves e valores podem ser de qualquer tipo.

Dados extras são serializados se pretender que a introduzir o seu próprio tipo na extras terá de adicionar um contrato de dados para este tipo.

### <a name="example"></a>Exemplo
Iremos criar uma nova classe "Pessoa".

            using System.Runtime.Serialization;

            namespace Microsoft.Azure.Engagement
            {
              [DataContract]
              public class Person
              {
                public Person(string name, int age)
                {
                  Age = age;
                  Name = name;
                }

                // Properties

                [DataMember]
                public int Age
                {
                  get;
                  set;
                }

                [DataMember]
                public string Name
                {
                  get;
                  set; 
                }
              }
            }

Em seguida, iremos adicionar um `Person` instância para uma adicionais.

            Person person = new Person("Engagement Haddock", 51);
            var extras = new Dictionary<object, object>();
            extras.Add("people", person);

            EngagementAgent.Instance.SendEvent("Event", extras);

> [!WARNING]
> Se o put outros tipos de objetos, certificar-se de que o método ToString () é implementado para devolver uma cadeia legível humana.
> 
> 

### <a name="limits"></a>Limites
#### <a name="keys"></a>Chaves
Cada chave no objeto tem de corresponder a seguinte expressão regular:

`^[a-zA-Z][a-zA-Z_0-9]*$`

Significa que as chaves tem de começar com, pelo menos, uma letra, seguido de letras, dígitos ou carateres de sublinhado (\_).

#### <a name="size"></a>Tamanho
Extras estão limitados a **1024** carateres por chamada.

## <a name="reporting-application-information"></a>Informações de relatórios de aplicações
### <a name="reference"></a>Referência
            void SendAppInfo(Dictionary<object, object> appInfos)

Função de controlo de informações (ou quaisquer outras informações específicas da aplicação) utilizando o SendAppInfo() manualmente pode comunicar.

Tenha em atenção que estes dados podem ser enviados de forma incremental: o valor mais recente para uma determinada chave será mantido para um determinado dispositivo. Como extras de evento, utilizar um dicionário\<objeto, objeto\> ligar dados.

### <a name="example"></a>Exemplo
            Dictionary<object, object> appInfo = new Dictionary<object, object>()
              {
                {"birthdate", "1983-12-07"},
                {"gender", "female"}
              };

            EngagementAgent.Instance.SendAppInfo(appInfo);

### <a name="limits"></a>Limites
#### <a name="keys"></a>Chaves
Cada chave no objeto tem de corresponder a seguinte expressão regular:

`^[a-zA-Z][a-zA-Z_0-9]*$`

Significa que as chaves tem de começar com, pelo menos, uma letra, seguido de letras, dígitos ou carateres de sublinhado (\_).

#### <a name="size"></a>Tamanho
Informações sobre a aplicação está limitado a **1024** carateres por chamada.

No exemplo anterior, o JSON enviado para o servidor é 44 carateres de comprimento:

            {"birthdate":"1983-12-07","gender":"female"}

## <a name="logging"></a>Registo
### <a name="enable-logging"></a>Ativar o registo
O SDK pode ser configurado para produzir os registos de teste na consola do IDE.
Estes registos não estão ativados por predefinição. Para personalizar esta, atualize a propriedade `EngagementAgent.Instance.TestLogEnabled` para um valor disponível no `EngagementTestLogLevel` enumeração, para a instância:

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

