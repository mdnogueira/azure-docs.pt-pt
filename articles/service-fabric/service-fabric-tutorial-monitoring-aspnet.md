---
title: "Monitorização e diagnóstico ASP.NET Core dos serviços de no Service Fabric do Azure | Microsoft Docs"
description: "Saiba como configurar a monitorização e diagnóstico para uma aplicação do Azure Service Fabric ASP.NET Core."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/14/2017
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: 68788efffd27edf2813cf455490b651c2c7106a8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-and-diagnose-an-aspnet-core-application-on-service-fabric"></a>Monitorizar e diagnosticar uma aplicação ASP.NET Core no Service Fabric
Este tutorial é parte quatro de uma série. Passa pelos passos para configurar a monitorização e diagnóstico para uma aplicação de ASP.NET Core em execução num cluster de Service Fabric com o Application Insights. Iremos irá recolher telemetria da aplicação desenvolvida na primeira parte do tutorial, [compilar uma aplicação .NET Service Fabric](service-fabric-tutorial-create-dotnet-app.md). 

Na parte quatro da série tutorial, saiba como:
> [!div class="checklist"]
> * Configurar o Application Insights para a sua aplicação
> * Recolher a telemetria de resposta para rastreio baseado em HTTP comunicação entre os serviços
> * Utilizar a funcionalidade de mapa de aplicação no Application Insights
> * Adicionar eventos personalizados utilizando a API do Application Insights

Este tutorial série, a saber como:
> [!div class="checklist"]
> * [Criar uma aplicação .NET Service Fabric](service-fabric-tutorial-create-dotnet-app.md)
> * [Implementar a aplicação para um cluster remoto](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * [Configurar CI/CD utilizando o Visual Studio Team Services](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * Configurar a monitorização e diagnóstico para a aplicação

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial:
- Se não tiver uma subscrição do Azure, crie um [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Instalar Visual Studio 2017](https://www.visualstudio.com/) e instalar o **programação do Azure** e **desenvolvimento ASP.NET e web** cargas de trabalho.
- [Instalar o SDK do Service Fabric](service-fabric-get-started.md)

## <a name="download-the-voting-sample-application"></a>Transferir a aplicação de exemplo de voto
Se não foi possível criar a aplicação de exemplo de voto [parte de um desta série tutorial](service-fabric-tutorial-create-dotnet-app.md), poderá transferi-lo. Numa janela de comandos ou terminal, execute o seguinte comando para clonar o repositório da aplicação de exemplo para o computador local.

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="set-up-an-application-insights-resource"></a>Configurar um recurso do Application Insights
O Application Insights está plataforma de gestão de desempenho de aplicações do Azure e Service Fabric recomendado plataforma para a aplicação de monitorização e diagnóstico. Para criar um recurso do Application Insights, navegue até à [portal do Azure](https://portal.azure.com). Clique em **novo** no menu de navegação esquerdo para abrir a cópia de segurança do Azure Marketplace. Clique em **monitorização + gestão** e, em seguida, **Application Insights**.

![Criar novo recurso de AI](./media/service-fabric-tutorial-monitoring-aspnet/new-ai-resource.png)

Agora tem de preencher as informações necessárias sobre os atributos do recurso a ser criado. Introduza um adequado *nome*, *grupo de recursos*, e *subscrição*. Definir o *localização* para onde pretende implementar o cluster do Service Fabric no futuro. Neste tutorial, iremos implementar a aplicação para um cluster local, por isso, o *localização* campo é irrelevantes. O *tipo de aplicação* deve ser deixada como "Aplicação web ASP.NET". 

![Atributos de recurso de AI](./media/service-fabric-tutorial-monitoring-aspnet/new-ai-resource-attrib.png)

Assim que tiver preenchidos as informações necessárias, clique em **criar** para aprovisionar o recurso - deve demorar sobre um minuto. 
<!-- When completed, navigate to the newly deployed resource, and find the "Instrumentation Key" (visible in the "Essentials" drop down section). Copy it to clipboard, since we will need it in the next step. -->

## <a name="add-application-insights-to-the-applications-services"></a>Adicionar o Application Insights para os serviços da aplicação

Inicie o Visual Studio 2017 com privilégios elevados. Pode fazê-lo ao clicar no ícone de Visual Studio, no Menu Iniciar e escolher **executar como administrador**. Clique em **ficheiro** > **abra** > **projeto/solução** e navegue para a aplicação de voto (a criado na parte do tutorial ou git clonado). Abra *Voting.sln* e se lhe for pedido para restaurar os pacotes de NuGet da aplicação, clique em **Sim**.

Siga estes passos para configurar o Application Insights para serviços VotingWeb e VotingData:
1. Clique com o botão direito no nome do serviço e clique em **configurar o Application Insights...** .

    ![Configurar AI](./media/service-fabric-tutorial-monitoring-aspnet/configure-ai.png)

2. Clique em **iniciar livre**.
3. Inicie sessão na sua conta (com o qual também configurar a subscrição do Azure) e selecione a subscrição na qual criou o recurso do Application Insights. Localizar o recurso em *recurso do Application Insights existente* na lista pendente "Recursos". Clique em **registar** para adicionar o Application Insights ao seu serviço.

    ![Registar AI](./media/service-fabric-tutorial-monitoring-aspnet/register-ai.png)

4. Clique em **concluir** assim que a caixa de diálogo que aparece concluir a ação.
    
Certifique-se efetuar os passos acima **ambos** dos serviços da aplicação para concluir a configuração do Application Insights para a aplicação. Mesmo recurso do Application Insights é utilizado para os serviços para ver a entrada e saída pedidos e comunicação entre os serviços.

## <a name="add-the-microsoftapplicationinsightsservicefabricnative-nuget-to-the-services"></a>Adicionar o Microsoft.ApplicationInsights.ServiceFabric.Native NuGet aos serviços

O Application Insights tem dois NuGets específicos de recursos de infraestrutura de serviço que podem ser utilizadas dependendo do cenário. Um é utilizado com serviços nativos do Service Fabric e os outros com contentores e executáveis de convidado. Neste caso, iremos utilizar o Microsoft.ApplicationInsights.ServiceFabric.Native NuGet tirar partido de compreensão de contexto de serviço que implica. Para ler mais sobre o Application Insights SDK e o NuGets específicas do Service Fabric, consulte [Microsoft Application Insights para o Service Fabric](https://github.com/Microsoft/ApplicationInsights-ServiceFabric/blob/develop/README.md). 

Eis os passos para configurar o NuGet:
1. Clique com o botão direito no **solução 'Votar'** na parte superior do Explorador de soluções e clique em **gerir pacotes NuGet para solução...** .
2. Clique em **procurar** no menu de navegação superior da janela de "NuGet - a solução" e verifique o **incluir pré-lançamento** caixa junto a barra de pesquisa.
3. Procurar `Microsoft.ApplicationInsights.ServiceFabric.Native` e clique no pacote NuGet adequado.
4. À direita, clique nas duas caixas de verificação junto aos dois serviços na aplicação, **VotingWeb** e **VotingData** e clique em **instalar**.
    ![Registo de AI concluído](./media/service-fabric-tutorial-monitoring-aspnet/aisdk-sf-nuget.png)
5. Clique em **OK** no *rever alterações* caixa de diálogo que aparece e aceita o *aceitação de licença*. Isto irá concluir a adição do NuGet para os serviços.
6. Agora tem de configurar o inicializador de telemetria nos serviços de dois. Para tal, abra cópias de segurança *VotingWeb.cs* e *VotingData.cs*. Para ambos os parâmetros, execute os seguintes dois passos:
    1. Adicione estes dois *utilizando* declarações na parte superior de cada  *\<ServiceName > CS*:
    
    ```csharp
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.ServiceFabric;
    ```
    
    2. O aninhada no *devolver* declaração do Estado de *CreateServiceInstanceListeners()* ou *CreateServiceReplicaListeners()*, em *ConfigureServices*  >  *serviços*, entre os dois serviços Singleton declarados, adicione: `.AddSingleton<ITelemetryInitializer>((serviceProvider) => FabricTelemetryInitializerExtension.CreateFabricTelemetryInitializer(serviceContext))`.
    Isto irá adicionar o *serviço contexto* para sua telemetria, permitindo-lhe compreender melhor a origem da sua telemetria no Application Insights. O aninhada *devolver* instrução no *VotingWeb.cs* deve ter este aspeto:
    
    ```csharp
    return new WebHostBuilder().UseWebListener()
        .ConfigureServices(
            services => services
                .AddSingleton<StatelessServiceContext>(serviceContext)
                .AddSingleton<ITelemetryInitializer>((serviceProvider) => FabricTelemetryInitializerExtension.CreateFabricTelemetryInitializer(serviceContext))
                .AddSingleton<HttpClient>())
        .UseContentRoot(Directory.GetCurrentDirectory())
        .UseStartup<Startup>()
        .UseApplicationInsights()
        .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
        .UseUrls(url)
        .Build();
    ```

    Da mesma forma, no *VotingData.cs*, deverá ter:

    ```csharp
    return new WebHostBuilder()
        .UseKestrel()
        .ConfigureServices(
            services => services
                .AddSingleton<StatefulServiceContext>(serviceContext)
                .AddSingleton<ITelemetryInitializer>((serviceProvider) => FabricTelemetryInitializerExtension.CreateFabricTelemetryInitializer(serviceContext))
                .AddSingleton<IReliableStateManager>(this.StateManager))
        .UseContentRoot(Directory.GetCurrentDirectory())
        .UseStartup<Startup>()
        .UseApplicationInsights()
        .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.UseUniqueServiceUrl)
        .UseUrls(url)
        .Build();
    ```

Neste momento, está pronto para implementar a aplicação. Clique em **iniciar** na parte superior (ou **F5**), e o Visual Studio irá criar a aplicação do pacote, configurar o seu cluster local e implementar a aplicação. 

Assim que a aplicação é feita implementação, vá ao longo para [localhost:8080](localhost:8080), onde deve ser capaz de ver a aplicação de página única de exemplo de voto. Votar para alguns itens diferentes da sua preferência criar alguns dados e telemetria de exemplo - I correu para desserts!

![Votos de exemplo de AI](./media/service-fabric-tutorial-monitoring-aspnet/vote-sample.png)

Não hesite *remover* algumas das opções de votos, bem como quando tiver terminado adicionar alguns votos.

## <a name="view-telemetry-and-the-app-map-in-application-insights"></a>Ver telemetria e o mapa de aplicação no Application Insights 

HEAD ativação pós-falha para o recurso do Application Insights no portal do Azure e, na barra de navegação esquerdo do recurso, clique em **pré-visualizações** em *configurar*. Ativar **no** o *mapa de aplicação de função Multi* na lista de pré-visualizações disponíveis.

![Ativar AI AppMap](./media/service-fabric-tutorial-monitoring-aspnet/ai-appmap-enable.png)

Clique em **descrição geral** para voltar para a página de destino do seu recurso. Em seguida, clique em **pesquisa** na parte superior para ver os rastreios futuras do. Demora alguns minutos para que os rastreios a aparecer no Application Insights. No caso de que não veja qualquer, aguarde um pouco e atingiu o **atualizar** botão na parte superior.
![Rastreios de Consulte de AI](./media/service-fabric-tutorial-monitoring-aspnet/ai-search.png)

Deslocamento para baixo no *pesquisa* janela mostrará a telemetria de entrada obter out of box com o Application Insights. Para cada ação demorou na aplicação de voto, deverá haver um pedido PUT saído de *VotingWeb* (colocar votos/Put [nome]), um pedido PUT recebido do *VotingData* (colocar VoteData/Put [nome ]), seguido de um par de pedidos GET para atualizar os dados que está a ser apresentados. Também será um rastreio de dependência de HTTP no localhost, uma vez que estes pedidos de HTTP. Eis um exemplo de que verá para como um voto é adicionado: ![rastreio de pedido de amostra de AI](./media/service-fabric-tutorial-monitoring-aspnet/sample-request.png)

Pode clicar dos rastreios para ver mais detalhes acerca do mesmo. Não há informações úteis sobre o pedido fornecido pelo Application Insights, incluindo o *tempo de resposta* e *URL do pedido*. Além disso, uma vez que adicionou do NuGet específico do Service Fabric, irá também obter dados sobre a sua aplicação no contexto de um cluster do Service Fabric no *dados personalizados* secção abaixo. Isto inclui o contexto de serviço, para que possa ver a *PartitionID* e *ReplicaId* da origem do pedido e uma melhor localizar problemas quando diagnosticar erros na sua aplicação.

![Detalhes de rastreio de AI](./media/service-fabric-tutorial-monitoring-aspnet/trace-details.png)

Além disso, uma vez que foi ativado o mapa de aplicação, o *descrição geral* página, ao clicar no **mapa de aplicação** ícone irá mostrar-lhe ambos os seus serviços ligados.

![Detalhes de rastreio de AI](./media/service-fabric-tutorial-monitoring-aspnet/app-map.png)

O mapa de aplicação pode ajudar a compreender a topologia de aplicação melhor, especialmente como começar a adicionar vários diferentes serviços que funcionam em conjunto. Também lhe dados básicos taxas de êxito de pedido e pode ajudar a diagnosticar pedidos falhados para compreender onde coisas poderão ter decorrido erradas. Para saber mais sobre como utilizar o mapa de aplicação, consulte [mapa de aplicação no Application Insights](../application-insights/app-insights-app-map.md).

## <a name="add-custom-instrumentation-to-your-application"></a>Adicionar instrumentação personalizada à sua aplicação

Apesar do Application Insights fornece uma grande quantidade de telemetria a Box, poderá querer adicionar mais instrumentação personalizada. Isto pode ser com base nas suas necessidades de negócio ou melhorar diagnóstico quando coisas correrem mal na sua aplicação. O Application Insights tem um API para inserção eventos personalizados e métricas, que pode ler mais sobre [aqui](../application-insights/app-insights-api-custom-events-metrics.md).

Vamos adicionar alguns eventos personalizados para *VoteDataController.cs* (em *VotingData* > *controladores*) para controlar quando votos estão a ser adicionado e eliminado do subjacente *votesDictionary*. 
1. Adicionar `using Microsoft.ApplicationInsights;` no fim das outras instruções de utilização.
2. Declarar uma nova *TelemetryClient* no início da classe, sob a criação do *IReliableStateManager*: `private TelemetryClient telemetry = new TelemetryClient();`.
3. No *Put()* de função, adicione um evento que confirma um voto foi adicionado. Adicionar `telemetry.TrackEvent($"Added a vote for {name}");` depois da transação foi concluída, com o botão direito antes do devolver *OkResult* instrução.
4. No *DELETE ()*, há um "se/else" baseada em condition que o *votesDictionary* contém votos para uma determinada opção voto. 
    1. Adicionar um evento que confirma a eliminação de um voto no *se* declaração, após o *await tx.CommitAsync()*:`telemetry.TrackEvent($"Deleted votes for {name}");`
    2. Adicionar um evento para mostrar que a eliminação não ocorrer *pessoa* declaração, antes da instrução de retorno:`telemetry.TrackEvent($"Unable to delete votes for {name}, voting option not found");`

Eis um exemplo de que o *Put()* e *DELETE ()* funções poderão aparentar ser depois de adicionar os eventos:

```csharp
// PUT api/VoteData/name
[HttpPut("{name}")]
public async Task<IActionResult> Put(string name)
{
    var votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

    using (ITransaction tx = this.stateManager.CreateTransaction())
    {
        await votesDictionary.AddOrUpdateAsync(tx, name, 1, (key, oldvalue) => oldvalue + 1);
        await tx.CommitAsync();
    }

    telemetry.TrackEvent($"Added a vote for {name}");
    return new OkResult();
}

// DELETE api/VoteData/name
[HttpDelete("{name}")]
public async Task<IActionResult> Delete(string name)
{
    var votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

    using (ITransaction tx = this.stateManager.CreateTransaction())
    {
        if (await votesDictionary.ContainsKeyAsync(tx, name))
        {
            await votesDictionary.TryRemoveAsync(tx, name);
            await tx.CommitAsync();
            telemetry.TrackEvent($"Deleted votes for {name}");
            return new OkResult();
        }
        else
        {
            telemetry.TrackEvent($"Unable to delete votes for {name}, voting option not found");
            return new NotFoundResult();
        }
    }
}
```

Depois de concluir a efetuar estas alterações, **iniciar** a aplicação, de modo a que cria e implementa a versão mais recente do mesmo. Depois de terminar a aplicação de implementação, vá ao longo para [localhost:8080](localhost:8080)e adicionar e eliminar algumas opções de votos. Em seguida, volte para o recurso do Application Insights para ver os rastreios para a execução mais recente (como anteriormente, rastreios podem demorar 1-2 minutos a aparecer no Application Insights). Para todos os os votos adicionado e eliminado, deverá ver agora um "personalizada eventos * juntamente com a resposta telemetria. 

![eventos personalizados](./media/service-fabric-tutorial-monitoring-aspnet/custom-events.png)

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, ficou a saber como:
> [!div class="checklist"]
> * Configurar o Application Insights para a sua aplicação
> * Recolher a telemetria de resposta para rastreio baseado em HTTP comunicação entre os serviços
> * Utilizar a funcionalidade de mapa de aplicação no Application Insights
> * Adicionar eventos personalizados utilizando a API do Application Insights

Agora que concluiu a configuração de monitorização e diagnóstico para a sua aplicação ASP.NET, experimente o seguinte:
- [Explorar a monitorização e diagnóstico no Service Fabric](service-fabric-diagnostics-overview.md)
- [Análise de eventos de Service Fabric com o Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md)
- Para saber mais sobre o Application Insights, consulte o artigo [documentação do Application Insights](https://docs.microsoft.com/en-us/azure/application-insights/)
