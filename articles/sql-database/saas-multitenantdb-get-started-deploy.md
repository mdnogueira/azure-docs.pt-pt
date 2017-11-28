---
title: "Implementar uma aplicação SaaS a base de dados do multi-inquilino que utiliza a SQL Database do Azure | Microsoft Docs"
description: "Implementar e explorar em partição horizontal Wingtip pedidos da base de dados do multi-inquilino aplicação SaaS, que demonstra padrões de SaaS ao utilizar a SQL Database do Azure."
keywords: tutorial de base de dados sql
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: billgib;MightyPen
ms.service: sql-database
ms.custom: scale out apps
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: sstein
ms.openlocfilehash: 1ef4355f7234bc6a534d21a57fa52b480983b99b
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="deploy-and-explore-a-sharded-multi-tenant-application-that-uses-azure-sql-database"></a>Implementar e explorar uma a aplicação multi-inquilino que utiliza a SQL Database do Azure

Neste tutorial, implementar e explorar uma aplicação de base de dados do multi-inquilino de SaaS de exemplo denominada Wingtip pedidos de suporte. A aplicação de Wingtip foi concebida para demonstram as funcionalidades da SQL Database do Azure que simplificam a implementação de cenários de SaaS.

Esta implementação do Wingtips utiliza um padrão da base de dados do multi-inquilino. É a fragmentação pelo identificador de inquilino. Dados do inquilino são distribuídos para uma determinada base de dados, de acordo com os valores de identificador de inquilino. Independentemente quantos inquilinos qualquer base de dados fornecido contém, todas as bases de dados são o multi-inquilinos na medida em que os esquemas de tabela incluem um identificador de inquilino. 

Neste padrão de base de dados permite-lhe armazenar um ou mais inquilinos em cada partição horizontal ou a base de dados. Pode otimizar o custo mais baixo, fazendo com que cada base de dados ser partilhado por vários inquilinos. Ou pode otimizar para isolamento, fazendo com que cada base de dados armazenar apenas um inquilino. A opção de otimização pode ser efetuada separadamente para cada inquilino específico. Pode ser efetuada à sua escolha, quando o inquilino é armazenado pela primeira vez, ou pode mudar de ideias mais tarde. A aplicação foi concebida para funcionar bem qualquer forma.

#### <a name="app-deploys-quickly"></a>Aplicação implementa rapidamente

A secção de implementação que se segue fornece o **implementar no Azure** botão. Quando o botão é premido, a aplicação de Wingtip totalmente é implementada um cinco minutos mais tarde. A aplicação de Wingtip é executado na nuvem do Azure e utiliza a SQL Database do Azure. Wingtip é implementada a sua subscrição do Azure. Tem acesso total ao trabalhar com os componentes de aplicações individuais.

A aplicação é implementada com os dados para os três inquilinos de exemplo. Os inquilinos são armazenados em conjunto numa base de dados do multi-inquilino.

Qualquer pessoa pode transferir o código de origem do c# e do PowerShell para bilhetes de Wingtip de [nosso repositório do GitHub][link-github-wingtip-multitenantdb-55g].

#### <a name="learn-in-this-tutorial"></a>Saiba neste tutorial

> [!div class="checklist"]

> - Como implementar a aplicação Wingtip SaaS.
> - Onde obter o código fonte da aplicação e os scripts de gestão.
> - Sobre os servidores e bases de dados que constituem a aplicação.
> - Como os inquilinos estão mapeados para os seus dados com o *catálogo*.
> - Como aprovisionar um novo inquilino.
> - Como monitorizar a atividade de inquilino na aplicação.

Uma série de tutoriais relacionados está disponível que tirar partido desta implementação inicial. Os tutoriais explorar padrões de conceção e gestão de intervalo de SaaS. Quando completar os tutoriais, são encorajados a passo através de scripts fornecidos para ver como os diferentes padrões de SaaS são implementados.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, confirme que conclui os pré-requisitos seguintes:

- O Azure PowerShell mais recente está instalado. Para obter mais informações, consulte [introdução ao Azure PowerShell][link-azure-get-started-powershell-41q].

## <a name="deploy-the-wingtip-tickets-app"></a>Implementar o Wingtip pedidos de aplicação

1. Clique em seguintes **implementar no Azure** botão.
    - É aberto o portal do Azure com o modelo de implementação Wingtip bilhetes SaaS. O modelo requer dois valores de parâmetro: um nome para um novo grupo de recursos e um valor de 'user' que distingue esta implementação de outras implementações da aplicação. O passo seguinte fornece detalhes para definir estes valores de parâmetro.
        - Lembre-se de que tenha em atenção os valores exatos a que utilizar, porque irá precisar deles mais tarde para um ficheiro de configuração.

    [![Botão para implementar no Azure.][image-deploy-to-azure-blue-48d]][link-aka-ms-deploywtp-mtapp-52k]

2. Introduza os valores de parâmetros necessários para a implementação.

    > [!IMPORTANT]
    > Alguns autenticação e as definições de firewall do servidor, são intencionalmente inseguras para facilitar a demonstração. Escolha **criar um novo grupo de recursos**e não utilizam grupos de recursos, servidores ou conjuntos existente. Não utilize a aplicação ou a quaisquer recursos cria, para produção. Elimine este grupo de recursos quando tiver terminado com a aplicação para parar a faturação relacionada.

    É melhor utilizar apenas letras minúsculas, números e hífenes nos nomes de recursos.

    - Para **grupo de recursos** - selecione **criar nova**e, em seguida, forneça um **nome** para o grupo de recursos (sensível às maiúsculas e).
        - Recomendamos que todas as letras em seu nome de grupo de recursos estar em minúsculas.
        - Recomendamos que acrescentar um traço, seguido pelo seu iniciais, seguidos de um dígito: por exemplo, *wingtip af1*.
        - Selecione um **localização** na lista pendente.
    - Para **utilizador** -que recomendamos que escolha um curto **utilizador** valor, tal como as suas iniciais e um dígito: por exemplo, *af1*.

3. **Implemente a aplicação**.

    - Clique para aceitar os termos e condições.
    - Clique em **Comprar**.

4. Monitorizar o estado de implementação, clicando em **notificações**, que é o ícone de campainha para a direita da caixa de pesquisa. Implementar a aplicação de Wingtip demora cerca de cinco minutos.

   ![implementação concluída com êxito](media/saas-multitenantdb-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-management-scripts"></a>Transferir e desbloquear os scripts de gestão

Enquanto a aplicação está a implementar, transfira os scripts de código e gestão da origem de aplicação.

> [!IMPORTANT]
> Conteúdo executável (scripts, dlls) pode estar bloqueado pelo Windows quando zip ficheiros são transferidos a partir de uma origem externa e extraiu. Quando extrair os scripts a partir de um ficheiro zip, utilize os seguintes passos para desbloquear o ficheiro. zip antes de a extrair. Ao desbloquear o ficheiro. zip, certifique-se que os scripts estão autorizados a executar.

1. Navegue até à [o repositório do GitHub WingtipTicketsSaaS MultiTenantDb](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb).
2. Clique em **clonar ou transferir**.
3. Clique em **transferir ZIP** e guarde o ficheiro.
4. Clique com botão direito do **WingtipTicketsSaaS-MultiTenantDb-master.zip** do ficheiro e selecione **propriedades**.
5. No **geral** separador, selecione **desbloqueio**e clique em **aplicar**.
6. Clique em **OK**.
7. Extraia os ficheiros.

Os scripts localizados no *... \\Mestre de MultiTenantDb WingtipTicketsSaaS\\Learning módulos\\*  pasta.

## <a name="update-the-configuration-file-for-this-deployment"></a>Atualizar o ficheiro de configuração para esta implementação

Antes de executar quaisquer scripts, defina o *grupo de recursos* e *utilizador* valores na **UserConfig.psm1**. Defina estas variáveis com os mesmos valores que definiu durante a implementação.

1. Abra ...\\Módulos de Aprendizagem\\*UserConfig.psm1* no *ISE do PowerShell*.
2. Atualização *ResourceGroupName* e *nome* com os valores específicos para a sua implementação (em linhas 10 e 11 apenas).
3. Guarde as alterações.

Os valores definidos neste ficheiro são utilizados por todos os scripts, pelo que é importante estão corretas. Se voltar a implementar a aplicação, certifique-se de que selecciona um valor de utilizador e grupo de recursos diferente. Em seguida, atualize o UserConfig com os novos valores.

## <a name="run-the-application"></a>Executar a aplicação

A aplicação apresenta os locais, como salas de espetáculos, clubes de jazz, clubes desportivos, onde os eventos são realizados. Venues registar-se como clientes (ou inquilinos) da plataforma Wingtip, para uma forma fácil de listar eventos e propor pedidos de suporte. Cada local obtém uma aplicação Web personalizada para gerir e listar os seus eventos e vender bilhetes de forma independente e isolada dos outros inquilinos. Nos bastidores, os dados de cada inquilino são armazenados por predefinição, numa base de dados a multi-inquilino.

Um centro **Hub de eventos** fornece uma lista de ligações para os inquilinos na sua implementação específica.

1. Abra o *Hub de eventos* no seu browser:
    - http://events.Wingtip. &lt;Utilizador&gt;. trafficmanager.net &nbsp; *(substituir pelo valor de utilizador da sua implementação.)*

    ![hub de eventos](media/saas-multitenantdb-get-started-deploy/events-hub.png)

2. Clique em **Fabrikam Jazz Club** no *Hub de Eventos*.

   ![Eventos](./media/saas-multitenantdb-get-started-deploy/fabrikam.png)

Para controlar a distribuição de pedidos recebidos, as utilizações de aplicação [Traffic Manager do Azure](../traffic-manager/traffic-manager-overview.md). As páginas de eventos, que são específicas do inquilino, incluem o nome de inquilino no URL. Os URLs também incluem o valor de utilizador específico e seguem este formato:

- http://events.Wingtip. &lt;Utilizador&gt;.trafficmanager.net/*fabrikamjazzclub*
 
A aplicação de eventos analisa o nome de inquilino a partir do URL e a codifica para criar uma chave para aceder a um catálogo utilizando [gestão de mapa de partições horizontais](sql-database-elastic-scale-shard-map-management.md). O catálogo mapeia a chave para a localização do inquilino da base de dados. O **Hub de eventos** apresenta uma lista de todos os inquilinos estão registados no catálogo. O **Hub de eventos** utiliza metadados expandido no catálogo para obter o nome do inquilino associado a cada mapeamento para construir os URLs.

Num ambiente de produção, normalmente criaria um registo CNAME DNS para [apontar um domínio de internet da empresa](../traffic-manager/traffic-manager-point-internet-domain.md) para o perfil do Gestor de tráfego.

## <a name="start-generating-load-on-the-tenant-databases"></a>Começar a gerar carga nas bases de dados de inquilinos

Agora que a aplicação é implementada, vamos colocá-la para funcionar! O *demonstração LoadGenerator* script do PowerShell inicia uma carga de trabalho em execução para cada inquilino. A carga do mundo real em muitas aplicações de SaaS é normalmente esporádicas e imprevisíveis. Para simular este tipo de carga, o gerador de dimensões produz uma carga distribuída a todos os inquilinos. A carga inclui bursts aleatório em cada inquilino ocorrer em intervalos aleatório. Demora alguns minutos até o padrão de carga ideia, pelo que é melhor permitir que o gerador de execução para, pelo menos, três ou quatro minutos antes da carga de monitorização.

1. No *ISE do PowerShell*, abra a... \\Learning módulos\\utilitários\\*demonstração LoadGenerator.ps1* script.
2. Prima **F5** para executar o script e iniciar o gerador de carga (deixe os valores predefinidos do parâmetro por agora).

> [!IMPORTANT]
> O *demonstração LoadGenerator.ps1* script abre-se a outra sessão de PowerShell onde é executado o gerador de carga. O gerador de carga executa nesta sessão como uma tarefa de primeiro plano invoca tarefas de geração de carregamento em segundo plano, um para cada inquilino.

Depois de inicia a tarefa de primeiro plano, permanece no Estado invocar a tarefa. A tarefa inicia tarefas em segundo plano adicionais para os novos inquilinos subsequentemente aprovisionados.

Fechar a sessão do PowerShell deixa de todas as tarefas.

Pode querer reiniciar a sessão do gerador de carga a utilizar valores de parâmetro diferentes. Em caso afirmativo, feche o PowerShell sessão geração e, em seguida, execute novamente o *demonstração LoadGenerator.ps1*.

## <a name="provision-a-new-tenant-into-the-sharded-database"></a>Aprovisionar um novo inquilino na base de dados em partição horizontal

A implementação inicial inclui três inquilinos de exemplo no *Tenants1* base de dados. Vamos criar outro inquilino para ver como este problema afeta a aplicação implementada. Neste passo, criar rapidamente um novo inquilino.

1. Abra... \\Learning Modules\Provision e catálogo\\*demonstração ProvisionTenants.ps1* no *ISE do PowerShell*.
2. Prima **F5** para executar o script (mantenha os valores predefinidos por agora).

   > [!NOTE]
   > Utilizam scripts de SaaS de bilhetes Wingtip muitos *$PSScriptRoot* para permitir a navegação de pastas, para invocar outros scripts ou para importar módulos. Esta variável é avaliada apenas quando o script é executado como um todo, premindo **F5**.  Realce e em execução uma seleção (**F8**) pode resultar em erros, por isso, prima **F5** quando executar scripts.

É adicionado ao novo inquilino Racing Maple vermelho para o *Tenants1* da base de dados e registadas no catálogo. Ao novo inquilino da permissão a vender *eventos* site abre no browser:

![Novo inquilino](./media/saas-multitenantdb-get-started-deploy/red-maple-racing.png)

Atualize o *Hub de eventos* e ao novo inquilino é apresentado na lista.

## <a name="provision-a-new-tenant-in-its-own-database"></a>Aprovisionar um novo inquilino na sua própria base de dados

O modelo de multi-inquilino em partição horizontal permite-lhe escolher se aprovisionar um novo inquilino numa base de dados que contém outros inquilinos, ou para aprovisionar o inquilino na base de dados do seu próprio. Um inquilino na sua própria beneficia de ter os respetivos dados isolados entre os dados de outros inquilinos. O isolamento permite-lhe gerir o desempenho desse inquilino independentemente outros inquilinos. Além disso, é mais fácil restaurar os dados para um período de tempo anterior para o inquilino isolado. Pode optar por colocar os clientes de avaliação gratuita ou regulares numa base de dados multi-inquilino e os clientes de premium em bases de dados individuais. Se criar muitas bases de dados que cada uma contém apenas um inquilino, pode geri-los todos os coletivamente num conjunto elástico para otimizar os custos de recursos.  

Agora vamos aprovisionar outro inquilino, desta vez na sua própria base de dados.

1. Em... \\Learning módulos\\aprovisionar e catálogo\*ProvisionTenants.ps1* demonstração, modificar *$TenantName* para **Salix Salsa**, *$VenueType*  para **dance** e *$Scenario* para **2**.

2. Prima **F5** para executar novamente o script.
    - Este prima F5 Aprovisiona ao novo inquilino na base de dados separada. A base de dados e de inquilino são registadas no catálogo. Em seguida, o browser abre-se para a página de eventos do inquilino.

   ![Página de eventos de Salix Salsa](./media/saas-multitenantdb-get-started-deploy/salix-salsa.png)

   - Desloque-se na parte inferior da página. Não existe na faixa da, pode ver o nome de base de dados na qual os dados de inquilino estão armazenados.

3. Atualizar o Hub de eventos e os dois novos inquilinos aparece na lista.



## <a name="explore-the-servers-and-tenant-databases"></a>Explorar os servidores e bases de dados de inquilino

Agora vamos ver alguns dos recursos que foram implementados:

1. No [portal do Azure](http://portal.azure.com), navegue para a lista de grupos de recursos. Abra o grupo de recursos que criou quando implementou a aplicação.

   ![grupo de recursos](./media/saas-multitenantdb-get-started-deploy/resource-group.png)

2. Clique em **catálogo mt&lt;utilizador&gt;**  servidor. O servidor de catálogo contém duas bases de dados com o nome *tenantcatalog* e *basetenantdb*. O *basetenantdb* base de dados é uma base de dados do modelo em branco. É copiado para criar uma nova base de dados de inquilino, se utilizado para vários inquilinos ou apenas um inquilino.

   ![servidor de catálogo](./media/saas-multitenantdb-get-started-deploy/catalog-server.png)

3. Volte ao grupo de recursos e selecione o *tenants1 mt* servidor que contém as bases de dados do inquilino.
    - A base de dados tenants1 é uma base de dados do multi-inquilino na qual os três inquilinos originais, mais inquilino primeiro adicionadas, é armazenado. Este é configurado como uma base de dados padrão de DTU 50.
    - O **salixsalsa** base de dados contém o venue de dance Salix Salsa como respetivo inquilino apenas. Este é configurado como uma base de dados de edição Standard com 50 DTUs por predefinição.

   ![servidor de inquilinos](./media/saas-multitenantdb-get-started-deploy/tenants-server.png)


## <a name="monitor-the-performance-of-the-database"></a>Monitorizar o desempenho da base de dados

Se o gerador de carga tem estado em execução durante vários minutos, telemetria suficiente está disponível para começar a procurar algumas das capacidades incorporadas no portal de monitorização de base de dados.

1. Navegue para o **tenants1 mt&lt;utilizador&gt;**  servidor e clique em **tenants1** para ver a utilização de recursos para a base de dados tem quatro inquilinos na mesma. Cada inquilino está sujeita a sobrecarga esporádicas do gerador de dimensões de carga:

   ![monitorizar tenants1](./media/saas-multitenantdb-get-started-deploy/monitor-tenants1.png)

   O gráfico de utilização da DTU corretamente ilustra como uma base de dados do multi-inquilino pode suportar uma carga de trabalho imprevisível em vários inquilinos. Neste caso, o gerador de carga é aplicar uma carga de aproximadamente 30 DTUs esporádicas a cada inquilino. Este carregamento equivale a 60% de utilização de uma base de dados DTU 50. Picos que excedem os 60% são o resultado de carga a ser aplicada a mais do que um inquilino ao mesmo tempo.

2. Navegue para o **tenants1 mt&lt;utilizador&gt;**  servidor e clique em de **salixsalsa** base de dados para ver a utilização de recursos nesta base de dados que contém apenas um inquilino.

   ![salixsalsa base de dados](./media/saas-multitenantdb-get-started-deploy/monitor-salix.png)

O gerador de carga é aplicar uma carga semelhante a cada inquilino, independentemente da base de dados que cada inquilino está a ser. Com apenas um inquilino no **salixsalsa** base de dados, pode ver que a base de dados pode suportar um muito superior de carga que a base de dados com vários inquilinos. 

> [!NOTE]
> O criado pelo gerador de dimensões de carga são para fins de ilustração apenas.  Os recursos atribuídos a multi-inquilinos e de inquilino único bases de dados e os números de inquilinos que pode alojar uma base de dados do multi-inquilino, dependem os padrões de carga de trabalho real na sua aplicação.


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber:

> [!div class="checklist"]

> - Como implementar a aplicação de base de dados do Wingtip bilhetes SaaS multi-inquilino
> - Sobre os servidores e bases de dados que constituem a aplicação
> - Como os inquilinos são mapeados para os dados com o *catálogo*
> - Como aprovisionar novos inquilinos, numa base de dados do multi-inquilino e da base de dados único inquilino.
> - Como ver a utilização do conjunto para monitorizar a atividade dos inquilinos
> - Como eliminar os recursos de exemplo para parar a faturação relacionada

Experimente agora o [tutorial de aprovisionamento e o catálogo](sql-database-saas-tutorial-provision-and-catalog.md).



## <a name="additional-resources"></a>Recursos adicionais

- Para saber mais sobre as aplicações SaaS multi-inquilino, veja [*Design patterns for multi-tenant SaaS applications (Padrões de conceção para aplicações SaaS multi-inquilino)*](https://docs.microsoft.com/azure/sql-database/sql-database-design-patterns-multi-tenancy-saas-applications)








<!--  Link references.

A [series of related tutorials] is available that build upon this initial deployment.
[link-wtp-overivew-jumpto-saas-tutorials-97j]: saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials

-->

[link-aka-ms-deploywtp-mtapp-52k]: http://aka.ms/deploywtp-mtapp


[link-azure-get-started-powershell-41q]: https://docs.microsoft.com/powershell/azure/get-started-azureps

[link-github-wingtip-multitenantdb-55g]: https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB/





<!--  Image references.

[image-deploy-to-azure-blue-48d]: http://aka.ms/deploywtp-mtapp "Button for Deploy to Azure."

-->

[image-deploy-to-azure-blue-48d]: media/saas-multitenantdb-get-started-deploy/deploy.png

