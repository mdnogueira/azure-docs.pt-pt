---
title: Tutorial de SaaS multi-inquilino - SQL Database do Azure | Microsoft Docs
description: "Implementar e explorar uma aplicação SaaS autónomo único inquilino, que utiliza a SQL Database do Azure."
keywords: tutorial de base de dados sql
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: sstein
ms.openlocfilehash: 2ca290dfcb23215ffd727500e76076ae8b14fa6f
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="deploy-and-explore-a-standalone-single-tenant-application-that-uses-azure-sql-database"></a>Implementar e explorar uma aplicação de inquilino único autónoma que utiliza a SQL Database do Azure

Neste tutorial, implementar e explorar aplicação Wingtip pedidos de suporte de SaaS autónomo. A aplicação foi concebida para demonstram as funcionalidades da SQL Database do Azure que simplificam a ativar cenários de SaaS.

O padrão de aplicação autónoma implementa um grupo de recursos do Azure que contém uma aplicação de inquilino único e uma base de dados de inquilino único para cada inquilino.  Várias instâncias da aplicação podem ser aprovisionadas para fornecer uma solução multi-inquilino.

Enquanto este tutorial irá implementar grupos de recursos para vários inquilinos na sua subscrição do Azure, este padrão permite que os grupos de recursos ser implementada numa subscrição do Azure de um inquilino.  O Azure tem programas parceiro que permitem estes grupos de recursos para serem geridos por um fornecedor de serviço em nome do inquilino como um administrador na subscrição do inquilino.

Na secção de implementação abaixo, existem três implementar no Azure botões, cada um dos quais implementa uma instância diferente da aplicação personalizada para um inquilino específico. Quando cada botão é premido a aplicação correspondente é totalmente implementada cinco minutos mais tarde.  As aplicações são implementadas na sua subscrição do Azure.  Tem acesso total a explorar e trabalhar com os componentes de aplicações individuais.

Os scripts de código e gestão da origem de aplicação estão disponíveis no [WingtipTicketsSaaS StandaloneApp](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp) repositório do GitHub.


Neste tutorial, ficará a saber:

> [!div class="checklist"]

> * Como implementar a aplicação autónoma do Wingtip pedidos de suporte de SaaS
> * Onde obter o código fonte da aplicação e os scripts de gestão
> * Sobre os servidores e bases de dados que constituem a aplicação

Tutoriais adicionais serão lançadas due é claro que permite-lhe explorar uma gama de cenários de gestão com base num padrão aplicação.   

## <a name="deploy-the-wingtip-tickets-saas-standalone-application"></a>Implementar a aplicação de SaaS autónomo Wingtip bilhetes

Implemente a aplicação para os inquilinos fornecidos três:

1. Clique em cada **implementar no Azure** botão para abrir o modelo de implementação no portal do Azure. Cada modelo requer dois valores de parâmetro; um nome para um novo grupo de recursos e um nome de utilizador que distingue esta implementação de outras implementações da aplicação. O passo seguinte fornece detalhes para definir estes valores.<br><br>
    <a href="http://aka.ms/deploywingtipsa-contoso" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a>     &nbsp;&nbsp;**Contoso Concert Hall**
<br><br>
    <a href="http://aka.ms/deploywingtipsa-dogwood" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a>&nbsp; &nbsp; **Dogwood Dojo**
<br><br>
    <a href="http://aka.ms/deploywingtipsa-fabrikam" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a>    &nbsp;&nbsp;**Fabrikam Jazz Club**

2. Introduza os valores de parâmetros necessários para cada implementação.

    > [!IMPORTANT]
    > Algumas autenticações e firewalls do servidor são intencionalmente não seguras para fins de demonstração. **Criar um novo grupo de recursos** para cada implementação de aplicação.  Não utilize um grupo de recursos existente. Não utilize a aplicação ou a quaisquer recursos cria, para produção. Elimine todos os grupos de recursos quando tiver terminado com as aplicações para parar a faturação relacionada.

    É melhor utilizar apenas letras minúsculas, números e hífenes nos nomes de recursos.
    * Para **grupo de recursos** - selecione Criar nova e, em seguida, forneça um nome para o grupo de recursos (sensível às maiúsculas e).
        * Recomendamos que todas as letras em seu nome de grupo de recursos estar em minúsculas.
        * Recomendamos que acrescentar um traço, seguido pelo seu iniciais, seguidos de um dígito: por exemplo, _wingtip-sa-af1_.
        * Selecione uma localização da lista pendente.

    * Para **utilizador** -que recomendamos que escolha um valor curto do utilizador, tais como as suas iniciais e um dígito: por exemplo, _af1_.


1. **Implemente a aplicação**.

    * Clique para aceitar os termos e condições.
    * Clique em **Comprar**.

1. Monitorizar o estado de implementação de todas as implementações de três clicando **notificações** (no ícone de campainha à direita da caixa de pesquisa). Implementar a aplicação demora cerca de cinco minutos.


## <a name="run-the-application"></a>Executar a aplicação

A aplicação showcases venues, tais como concert halls, jazz clubs e clubs desporto, que alojam os eventos. Venues registar-se como clientes (ou inquilinos) Wingtip permissões, para uma forma fácil de listar eventos e propor pedidos de suporte. Cada venue obtém um web site personalizado para gerir e listar os respetivos eventos e propor bilhetes, independentes e isolados de outros inquilinos. Nos bastidores, a cada inquilino obtém uma instância da aplicação separado e a base de dados do SQL Server autónomo.

1. Abra a página de eventos para cada um dos três inquilinos no separadores de browser separados:

    http://events.contosoconcerthall. &lt;Utilizador&gt;. trafficmanager.net <br>
    http://events.dogwooddojo. &lt;Utilizador&gt;. trafficmanager.net<br>
    http://events.fabrikamjazzclub. &lt;Utilizador&gt;. trafficmanager.net

    (substituir &lt;utilizador&gt; com o valor de utilizador da sua implementação).

   ![Eventos](./media/saas-standaloneapp-get-started-deploy/fabrikam.png)


Para controlar a distribuição de pedidos recebidos, as utilizações de aplicação [ *Traffic Manager do Azure*](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview). Cada aplicação específica do inquilino inclui o nome de inquilino como parte do nome de domínio no URL. Todos os inquilinos URLs incluem específicos da sua *utilizador* valor e segue este formato: http://events.&lt; venuename&gt;.&lt; UTILIZADOR&gt;. trafficmanager.net. Localização da base de dados de cada inquilino está incluída nas definições da aplicação da aplicação implementada correspondente.

Num ambiente de produção, normalmente criaria um registo CNAME DNS para [ *apontar um domínio de internet da empresa* ](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-point-internet-domain) para o URL do perfil do Gestor de tráfego.


## <a name="explore-the-servers-and-tenant-databases"></a>Explorar os servidores e bases de dados de inquilino

Vamos ver alguns dos recursos que foram implementados:

1. No [portal do Azure](http://portal.azure.com), navegue para a lista de grupos de recursos.  Deverá ver o **wingtip-sa-catálogo -&lt;utilizador&gt;**  grupo de recursos no qual **catálogo-sa -&lt;utilizador&gt;**  é implementado um servidor com o **tenantcatalog** base de dados. Também deverá ver os grupos de recursos de três inquilino.

1. Abra o **wingtip-sa-fabrikam -&lt;utilizador&gt;**  grupo de recursos, que contém os recursos para a implementação da Fabrikam Jazz Club.  O **fabrikamjazzclub -&lt;utilizador&gt;**  servidor contém o **fabrikamjazzclub** base de dados.


Cada base de dados do inquilino é um DTU 50 _autónomo_ base de dados.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber:

> [!div class="checklist"]

> * Como implementar a aplicação autónoma do Wingtip pedidos de suporte de SaaS
> * Sobre os servidores e bases de dados que constituem a aplicação
> * Como eliminar os recursos de exemplo para parar a faturação relacionada


## <a name="additional-resources"></a>Recursos adicionais

<!--* Additional [tutorials that build on the Wingtip SaaS application](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* To learn about elastic pools, see [*What is an Azure SQL elastic pool*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool)
* To learn about elastic jobs, see [*Managing scaled-out cloud databases*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-jobs-overview) -->
* Para saber mais sobre as aplicações SaaS multi-inquilino, veja [*Design patterns for multi-tenant SaaS applications (Padrões de conceção para aplicações SaaS multi-inquilino)*](saas-tenancy-app-design-patterns.md)
