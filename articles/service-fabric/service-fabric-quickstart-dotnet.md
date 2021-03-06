---
title: "Criar uma aplicação .NET Service Fabric no Azure | Microsoft Docs"
description: "Crie uma aplicação .NET do Azure utilizando o exemplo de início rápido do Service Fabric."
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/02/2017
ms.author: mikhegn
ms.custom: mvc, devcenter
ms.openlocfilehash: 40b29ccb454caf5462807d6c24ca3f470865d368
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2017
---
# <a name="create-a-net-service-fabric-application-in-azure"></a>Criar uma aplicação .NET Service Fabric no Azure
O Azure Service Fabric é uma plataforma de sistemas distribuídos par implementar e gerir microsserviços e contentores dimensionáveis e fiáveis. 

Este guia de introdução mostra como implementar a sua primeira aplicação .NET para o Service Fabric. Quando tiver terminado, terá uma aplicação de voto com um web de ASP.NET Core front-end que guarda os resultados de votos no serviço de back-end com monitorização de estado no cluster.

![Captura de ecrã da aplicação](./media/service-fabric-quickstart-dotnet/application-screenshot.png)

Utilizar esta aplicação, a saber como:
> [!div class="checklist"]
> * Criar uma aplicação utilizando o .NET e do Service Fabric
> * Utilizar o ASP.NET core como uma web front-end
> * Armazenar dados da aplicação num serviço de monitorização de estado
> * Depurar a aplicação localmente
> * Implementar a aplicação para um cluster no Azure
> * A aplicação em vários nós de escalamento horizontal
> * Efetuar uma atualização sem interrupção da aplicação

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este guia de início rápido:
1. [Instalar Visual Studio 2017](https://www.visualstudio.com/) com o **programação do Azure** e **desenvolvimento ASP.NET e web** cargas de trabalho.
2. [Instale o Git](https://git-scm.com/).
3. [Instalar o SDK do Microsoft Azure Service Fabric](http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK)
4. Execute o seguinte comando para ativar o Visual Studio para implementar o cluster de Service Fabric local:
    ```powershell
    Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
    ```

## <a name="download-the-sample"></a>Transferir o exemplo
Na janela da linha de comandos, execute o seguinte comando para clonar o repositório da aplicação de exemplo para o computador local.
```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="run-the-application-locally"></a>Executar a aplicação localmente
Faça duplo clique no ícone de Visual Studio, no Menu Iniciar e escolha **executar como administrador**. Para ligar o depurador aos seus serviços, terá de executar o Visual Studio como administrador.

Abra o **Voting.sln** solução do Visual Studio do repositório que clonou.  

Por predefinição, a aplicação de voto estiver definida para escutar numa porta 8080.  A porta de aplicação está definida */VotingWeb/PackageRoot/ServiceManifest.xml* ficheiro.  Pode alterar a porta de aplicação, atualizando o **porta** atributo do **Endpoint** elemento.  Para implementar e executar a aplicação localmente, a porta de aplicação tem de ser aberta e disponível no seu computador.  Se alterar a porta de aplicação, substitua o novo valor de porta de aplicação para "8080" ao longo deste artigo.

Para implementar a aplicação, prima **F5**.

> [!NOTE]
> Na primeira vez, executar e implementar a aplicação, o Visual Studio cria um cluster local para a depuração. Esta operação pode demorar algum tempo. O estado da criação do cluster aparece na janela de saída do Visual Studio.  Na saída, verá a mensagem "O URL da aplicação não está definido ou não é um URL de HTTP/HTTPS para que o browser não será aberto na aplicação."  Esta mensagem não indicar um erro, mas que um browser será não auto-iniciar.

Quando a implementação estiver concluída, iniciar um browser e abra esta página: `http://localhost:8080` -web front-end da aplicação.

![Front-end da aplicação](./media/service-fabric-quickstart-dotnet/application-screenshot-new.png)

Agora pode adicionar um conjunto de opções de voto e começar a tirar votos. A aplicação é executada e armazena todos os dados no seu cluster do Service Fabric, sem a necessidade de uma base de dados separada.

## <a name="walk-through-the-voting-sample-application"></a>Percorrer a aplicação de exemplo voto
A aplicação de voto é composta por dois serviços:
- Serviço de front-end (VotingWeb) Web - web de ASP.NET Core um serviço front-end, o que funciona a página web e expõe web APIs para comunicar com o serviço de back-end.
- O serviço de back-end (VotingData)-serviço web de uma ASP.NET Core, que expõe uma API para armazenar os resultados de voto num dictionary fiável persistentes no disco.

![Diagrama de aplicação](./media/service-fabric-quickstart-dotnet/application-diagram.png)

Quando votar na aplicação ocorrem os seguintes eventos:
1. Um JavaScript envia o pedido de voto para a API web no serviço web de front-end como um pedido HTTP PUT.

2. O serviço web de front-end utiliza um proxy para localizar e reencaminhar um pedido de HTTP PUT de mensagens em fila para o serviço de back-end.

3. O serviço de back-end demora o pedido de entrada e armazena o resultado atualizado num dictionary fiável, que obtém replicado para múltiplos nós dentro do cluster e persistentes no disco. Dados da aplicação são armazenados no cluster, pelo que não é necessária nenhuma base de dados.

## <a name="debug-in-visual-studio"></a>Depuração no Visual Studio
Quando a depuração de aplicações no Visual Studio, está a utilizar um cluster de desenvolvimento do Service Fabric local. Tem a opção para ajustar a sua experiência de depuração para o seu cenário. Nesta aplicação, os dados são armazenados no serviço de back-end através de um dicionário fiável. Visual Studio remove a aplicação por predefinição, quando parar o depurador. Remover a aplicação faz com que os dados no serviço de back-end para também ser removido. Para manter os dados entre sessões de depuração, pode alterar o **modo de depuração da aplicação** como uma propriedade no **voto** projeto no Visual Studio.

Para ver o que acontece no código, conclua os seguintes passos:
1. Abra o **/VotingWeb/Controllers/VotesController.cs** de ficheiros e defina um ponto de interrupção na web da API **colocar** método (linha 47) - pode procurar o ficheiro no Explorador de soluções no Visual Studio.

2. Abra o **/VotingData/ControllersVoteDataController.cs** de ficheiros e defina um ponto de interrupção da API web **colocar** método (50 de linha).

3. Voltar para o browser e clique numa opção de voto ou adicionar uma nova opção de voto. Atingiu o primeiro ponto de interrupção no controlador de api do web frente-fim.
    - Este é onde o JavaScript no browser envia um pedido para o controlador da API web no serviço de front-end.
    
    ![Adicionar serviço de front-end de voto](./media/service-fabric-quickstart-dotnet/addvote-frontend.png)

    - Em primeiro lugar, construir o URL para o ReverseProxy para o nosso serviço de back-end **(1)**.
    - Em seguida, enviar o colocar pedido de HTTP para o ReverseProxy **(2)**.
    - Por último, regresse a resposta do serviço de back-end para o cliente **(3)**.

4. Prima **F5** para continuar
    - Está agora no ponto de interrupção no serviço de back-end.
    
    ![Adicionar serviço de Back-End de voto](./media/service-fabric-quickstart-dotnet/addvote-backend.png)

    - Na primeira linha no método **(1)** o `StateManager` obtém ou adiciona um dicionário fiável chamado `counts`.
    - Todas as interações com valores num dictionary fiável necessitam de uma transação, esta utilizando instrução **(2)** cria esse transação.
    - A transação, atualize o valor da chave relevante para a opção de voto e consolidar a operação **(3)**. Depois da consolidação método devolve, os dados é atualizado no dicionário e replicados para outros nós do cluster. Os dados estão agora armazenados em segurança no cluster e o serviço de back-end pode falhar relativamente aos outros nós, ainda que os dados disponíveis.
5. Prima **F5** para continuar

Para parar a sessão de depuração, prima **Shift + F5**.

## <a name="deploy-the-application-to-azure"></a>Implementar a aplicação no Azure
Para implementar a aplicação no Azure, terá de um cluster do Service Fabric que executa a aplicação. 

### <a name="join-a-party-cluster"></a>Aderir a um cluster de terceiros
Os party clusters são clusters do Service Fabric gratuitos, limitados temporalmente, alojados no Azure e executados pela equipa do Service Fabric, nos quais qualquer pessoa pode implementar aplicações e saber mais sobre a plataforma. 

A iniciar sessão e [aderir a um cluster do Windows](http://aka.ms/tryservicefabric). Lembre-se a **ponto final de ligação** valor, que é utilizado nos seguintes passos.

> [!Note]
> Por predefinição, o serviço de front-end da web está configurado para escutar na porta 8080 para o tráfego de entrada. A porta 8080 está aberta no Cluster de terceiros.  Se precisar de alterar a porta de aplicação, alterá-la a uma das portas que estão abertas no Cluster de terceiros.
>

### <a name="deploy-the-application-using-visual-studio"></a>Implementar a aplicação com o Visual Studio
Agora que a aplicação está pronta, pode implementá-la num cluster diretamente a partir do Visual Studio.

1. Clique com botão direito **voto** no Explorador de soluções e escolha **publicar**. É apresentada a caixa de diálogo Publicar.

    ![Caixa de diálogo Publicar](./media/service-fabric-quickstart-dotnet/publish-app.png)

2. Copiar o **ponto final de ligação** da página de cluster de terceiros para o **ponto final de ligação** campo e clique em **publicar**. Por exemplo, `winh1x87d1d.westus.cloudapp.azure.com:19000`.

    Cada aplicação no cluster tem de ter um nome exclusivo.  Clusters intervenientes são um ambiente público, partilhado no entanto e pode existir um conflito com uma aplicação existente.  Se houver um conflito de nomes, mudar o nome do projeto do Visual Studio e implemente novamente.

3. Abra um browser e escreva o endereço de cluster seguidos de ': 8080' para obter a aplicação no cluster - por exemplo, `http://winh1x87d1d.westus.cloudapp.azure.com:8080`. Deverá ver a aplicação em execução no cluster no Azure.

![Front-end da aplicação](./media/service-fabric-quickstart-dotnet/application-screenshot-new-azure.png)

## <a name="scale-applications-and-services-in-a-cluster"></a>Dimensionar aplicações e serviços num cluster
Serviços do Service Fabric facilmente podem ser ampliados através de um cluster para acomodar uma alteração da carga nos serviços. Para dimensionar um serviço, tem de alterar o número de instâncias em execução no cluster. Tem várias formas de dimensionamento os seus serviços, pode utilizar os scripts ou comandos do PowerShell ou a CLI de recursos de infraestrutura de serviço (sfctl). Neste exemplo, utilize o Service Fabric Explorer.

Explorador de recursos de infraestrutura de serviço é executado em todos os clusters de Service Fabric e podem ser acedido a partir de um browser, ao navegar para a porta de gestão de clusters HTTP (19080), por exemplo, `http://winh1x87d1d.westus.cloudapp.azure.com:19080`.

Para dimensionar o serviço de front-end da Web, execute os seguintes passos:

1. Abra o Service Fabric Explorer no seu cluster - por exemplo, `http://winh1x87d1d.westus.cloudapp.azure.com:19080`.
2. Clique no botão de reticências (reticências) junto a **recursos de infraestrutura: voto/VotingWeb** nó na treeview e escolha **escala serviço**.

    ![Service Fabric Explorer](./media/service-fabric-quickstart-dotnet/service-fabric-explorer-scale.png)

    Agora, pode optar por dimensionar o número de instâncias do serviço de front-end da Web.

3. Altere o número para **2** e clique em **Dimensionar Serviço**.
4. Clique no **fabric: / voto/VotingWeb** nó na vista de árvore e expanda o nó de partição (representado por um GUID).

    ![Escala de Explorador de recursos de infraestrutura do serviço](./media/service-fabric-quickstart-dotnet/service-fabric-explorer-scaled-service.png)

    Após um atraso, pode ver que o serviço tem duas instâncias.  Na vista de árvore, ver os nos quais as instâncias de executam.

Por esta tarefa de gestão simples, os recursos disponíveis duplicada para o serviço de front-end processar a carga de utilizador. É importante compreender que não precisa de várias instâncias de um serviço para o executar de forma fiável. Se um serviço falhar, o Service Fabric certifica-se de que uma nova instância de serviço é executado no cluster.

## <a name="perform-a-rolling-application-upgrade"></a>Efetuar uma atualização sem interrupção da aplicação
Quando implementar novas atualizações à sua aplicação, o Service Fabric lança a atualização de uma forma segura. A anular atualizações dá-lhe sem períodos de indisponibilidade durante a atualização, bem como a reversão automática devem ocorrer erros.

Para atualizar a aplicação, efetue o seguinte:

1. Abra o **/VotingWeb/Views/Home/Index.cshtml** ficheiro no Visual Studio.
2. Alterar o <h2> cabeçalho da página ao adicionar ou atualizar o texto. Por exemplo, altere o cabeçalho para "V2 de exemplo de voto de recursos de infraestrutura de serviço".
3. Guarde o ficheiro.
4. Clique com botão direito **voto** no Explorador de soluções e escolha **publicar**. É apresentada a caixa de diálogo Publicar.
5. Clique em de **manifesto versão** botão para alterar a versão do serviço e aplicação.
6. Alterar a versão do **código** elemento em **VotingWebPkg** para "2.0.0", por exemplo e clique em **guardar**.

    ![Caixa de diálogo de versão de alteração](./media/service-fabric-quickstart-dotnet/change-version.png)
7. No **publicar aplicação do serviço Fabric** caixa de diálogo, se a atualização a caixa de verificação da aplicação e clique em **publicar**.

    ![Caixa de diálogo Publicar atualizar a definição](./media/service-fabric-quickstart-dotnet/upgrade-app.png)
8. Abra o browser e navegue para o endereço do cluster na porta 19080 - por exemplo, `http://winh1x87d1d.westus.cloudapp.azure.com:19080`.
9. Clique em de **aplicações** nó na vista de árvore e, em seguida, **atualizações em curso** no painel da direita. Pode ver como a atualização se faz através de domínios de atualização do cluster, certificar-se de que cada domínio está em bom estado antes de avançar para o seguinte. Um domínio de atualização na barra de progresso é apresentada a verde quando o estado de funcionamento do domínio ter sido verificado.
    ![Vista de atualização no Service Fabric Explorer](./media/service-fabric-quickstart-dotnet/upgrading.png)

    Service Fabric faz com que as atualizações seguro ao aguardar dois minutos depois de atualizar o serviço em cada nó no cluster. Esperam que a atualização demore aproximadamente oito minutos completa.

10. Enquanto a atualização está em execução, pode continuar a utilizar a aplicação. Porque tem duas instâncias do serviço em execução no cluster, alguns dos seus pedidos poderão obter uma versão atualizada da aplicação, enquanto outros ainda podem obter a versão antiga.

## <a name="next-steps"></a>Passos seguintes
Neste início rápido, aprendeu a:

> [!div class="checklist"]
> * Criar uma aplicação utilizando o .NET e do Service Fabric
> * Utilizar o ASP.NET core como uma web front-end
> * Armazenar dados da aplicação num serviço de monitorização de estado
> * Depurar a aplicação localmente
> * Implementar a aplicação para um cluster no Azure
> * A aplicação em vários nós de escalamento horizontal
> * Efetuar uma atualização sem interrupção da aplicação

Para saber mais sobre o Service Fabric e .NET, observe neste tutorial:
> [!div class="nextstepaction"]
> [Aplicação .NET no Service Fabric](service-fabric-tutorial-create-dotnet-app.md)