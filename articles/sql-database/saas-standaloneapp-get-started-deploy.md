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
ms.date: 11/29/2017
ms.author: genemi
ms.openlocfilehash: 164d98220f69a8c25d853baf1e07f9a11c200f16
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2017
---
# <a name="deploy-and-explore-a-standalone-single-tenant-application-that-uses-azure-sql-database"></a>Implementar e explorar uma aplicação de inquilino único autónoma que utiliza a SQL Database do Azure

Neste tutorial, implementar e explorar aplicação Wingtip pedidos de suporte de SaaS autónomo. A aplicação foi concebida para demonstram as funcionalidades da SQL Database do Azure que simplificam a ativar cenários de SaaS.

O padrão de aplicação autónoma implementa um grupo de recursos do Azure que contém uma aplicação de inquilino único e uma base de dados de inquilino único para cada inquilino.  Várias instâncias da aplicação podem ser aprovisionadas para fornecer uma solução multi-inquilino.

Neste tutorial, implementar grupos de recursos para vários inquilinos na sua subscrição do Azure.  Este padrão permite que os grupos de recursos ser implementada numa subscrição do Azure de um inquilino. O Azure tem programas parceiro que permitem estes grupos de recursos ser gerido por um fornecedor de serviço em nome do inquilino. O fornecedor de serviço é um administrador na subscrição do inquilino.

Na secção de implementação posterior, existem três azul **implementar no Azure** botões. Cada botão implementa uma instância diferente da aplicação. Cada instância personalizada para um inquilino específico. Quando cada botão é premido, a aplicação correspondente totalmente é implementada dentro de cinco minutos.  As aplicações são implementadas na sua subscrição do Azure.  Tem acesso total a explorar e trabalhar com os componentes de aplicações individuais.

Os scripts de código e gestão da origem de aplicação estão disponíveis no [WingtipTicketsSaaS StandaloneApp](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp) repositório do GitHub.


Neste tutorial, ficará a saber:

> [!div class="checklist"]
> * Como implementar a aplicação autónoma do Wingtip pedidos de suporte de SaaS.
> * Onde obter o código fonte da aplicação e os scripts de gestão.
> * Sobre os servidores e bases de dados que constituem a aplicação.

Tutoriais adicionais serão lançadas. Irá permitir-lhe explorar uma gama de cenários de gestão com base num padrão aplicação.   

## <a name="deploy-the-wingtip-tickets-saas-standalone-application"></a>Implementar a aplicação de SaaS autónomo Wingtip bilhetes

Implemente a aplicação para os inquilinos fornecidos três:

1. Clique em cada blue **implementar no Azure** botão para abrir o modelo de implementação no [portal do Azure](https://portal.azure.com). Cada modelo requer dois valores de parâmetro; um nome para um novo grupo de recursos e um nome de utilizador que distingue esta implementação de outras implementações da aplicação. O passo seguinte fornece detalhes para definir estes valores.<br><br>
    <a href="http://aka.ms/deploywingtipsa-contoso" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a>&nbsp; **Contoso Concert Hall**
<br><br>
    <a href="http://aka.ms/deploywingtipsa-dogwood" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a>&nbsp; **Dogwood Dojo**
<br><br>
    <a href="http://aka.ms/deploywingtipsa-fabrikam" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a>&nbsp; **Club Fabrikam Jazz**

2. Introduza os valores de parâmetros necessários para cada implementação.

    > [!IMPORTANT]
    > Alguns firewalls de autenticação e o servidor são intencionalmente protegidas para fins de demonstração. **Criar um novo grupo de recursos** para cada implementação de aplicação.  Não utilize um grupo de recursos existente. Não utilize a aplicação ou a quaisquer recursos cria, para produção. Elimine todos os grupos de recursos quando tiver terminado com as aplicações para parar a faturação relacionada.

    É melhor utilizar apenas letras minúsculas, números e hífenes nos nomes de recursos.
    * Para **grupo de recursos** - selecione **criar nova**e, em seguida, forneça uma minúscula **nome** para o grupo de recursos.
        * Recomendamos que acrescentar um traço, seguido pelo seu iniciais, seguidos de um dígito: por exemplo, *wingtip-sa-af1*.
        * Selecione um **localização** na lista pendente.

    * Para **utilizador** -que recomendamos que escolha um valor de utilizador curto, tais como as suas iniciais e um dígito: por exemplo, *af1*.


3. **Implemente a aplicação**.

    * Clique para aceitar os termos e condições.
    * Clique em **Comprar**.

4. Monitorizar o estado de implementação de todas as implementações de três clicando **notificações** (no ícone de campainha para a direita da caixa de pesquisa). Implementar a aplicação demora a cinco minutos.


## <a name="run-the-application"></a>Executar a aplicação

A aplicação showcases venues que alojam os eventos. Os tipos de venue incluem concert halls, jazz clubs e clubs desporto. Venues são os clientes da aplicação Wingtip pedidos de suporte. Em permissões Wingtip, venues estão registados como *inquilinos*. A ser um inquilino proporciona um venue uma forma fácil para eventos de lista e para vender permissões aos seus clientes. Cada venue obtém um site personalizado para listar os respetivos eventos e para vender pedidos de suporte. Cada inquilino seja isolado de outros inquilinos e é independente dos mesmos. Nos bastidores, a cada inquilino obtém uma instância de aplicacionais separados com a sua própria base de dados de SQL Server autónomo.

1. Abra a página de eventos para cada um dos três inquilinos no separadores de browser separados:

    - http://events.contosoconcerthall. &lt;Utilizador&gt;. trafficmanager.net
    - http://events.dogwooddojo. &lt;Utilizador&gt;. trafficmanager.net
    - http://events.fabrikamjazzclub. &lt;Utilizador&gt;. trafficmanager.net

    (No cada URL, substitua &lt;utilizador&gt; com o valor de utilizador da sua implementação.)

   ![Eventos](./media/saas-standaloneapp-get-started-deploy/fabrikam.png)

Para controlar a distribuição de pedidos recebidos, as utilizações de aplicação [ *Traffic Manager do Azure*](../traffic-manager/traffic-manager-overview.md). Cada instância da aplicação do inquilino específico inclui o nome de inquilino como parte do nome de domínio no URL. Todos os inquilinos URLs incluem específicos da sua **utilizador** valor. Os URLs siga o seguinte formato:
- http://events. &lt;venuename&gt;.&lt; UTILIZADOR&gt;. trafficmanager.net

Base de dados de cada inquilino **localização** está incluído nas definições da aplicação da aplicação implementada correspondente.

Num ambiente de produção, normalmente, cria um registo CNAME DNS para [ *apontar um domínio de internet da empresa* ](../traffic-manager/traffic-manager-point-internet-domain.md) para o URL do perfil do Gestor de tráfego.


## <a name="explore-the-servers-and-tenant-databases"></a>Explorar os servidores e bases de dados de inquilino

Vamos ver alguns dos recursos que foram implementados:

1. No [portal do Azure](http://portal.azure.com), navegue para a lista de grupos de recursos.
2. Consulte o **wingtip-sa-catálogo -&lt;utilizador&gt;**  grupo de recursos.
    - Neste grupo de recursos, o **catálogo-sa -&lt;utilizador&gt;**  servidor esteja implementado. O servidor contém o **tenantcatalog** base de dados.
    - Também deverá ver os grupos de recursos de três inquilino.
3. Abra o **wingtip-sa-fabrikam -&lt;utilizador&gt;**  grupo de recursos, que contém os recursos para a implementação da Fabrikam Jazz Club.  O **fabrikamjazzclub -&lt;utilizador&gt;**  servidor contém o **fabrikamjazzclub** base de dados.

Cada base de dados do inquilino é um DTU 50 *autónomo* base de dados.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber:

> [!div class="checklist"]
> * Como implementar a aplicação autónoma do Wingtip pedidos de suporte de SaaS.
> * Sobre os servidores e bases de dados que constituem a aplicação.
> * Como eliminar os recursos de exemplo para parar a faturação relacionada.


## <a name="additional-resources"></a>Recursos adicionais

<!--* Additional [tutorials that build on the Wingtip SaaS application](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* To learn about elastic pools, see [*What is an Azure SQL elastic pool*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool)
* To learn about elastic jobs, see [*Managing scaled-out cloud databases*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-jobs-overview)
-->

- Para saber mais sobre as aplicações de SaaS multi-inquilino, consulte [padrões para aplicações de SaaS multi-inquilino de conceção](saas-tenancy-app-design-patterns.md).
