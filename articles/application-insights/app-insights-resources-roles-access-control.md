---
title: "Controlam o acesso, funções e recursos no Azure Application Insights | Microsoft Docs"
description: "Os proprietários, contribuintes e leitores das informações da sua organização."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: 49f736a5-67fe-4cc6-b1ef-51b993fb39bd
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/17/2017
ms.author: mbullwin
ms.openlocfilehash: e3aa1d432cf2c510c09b13d4f07833edcb1e9b53
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="resources-roles-and-access-control-in-application-insights"></a>Recursos, funções e controlo de acesso no Application Insights
Pode controlar quem tem ler e atualizar o acesso aos seus dados no Azure [Application Insights][start], utilizando [controlo de acesso baseado em funções no Microsoft Azure](../active-directory/role-based-access-control-configure.md).

> [!IMPORTANT]
> Atribuir acesso para os utilizadores a **grupo de recursos ou subscrição** para que o recurso de aplicação pertence - não no recurso de si próprio. Atribuir o **contribuinte de componentes do Application Insights** função. Isto garante uniform controlo de acesso para testes web e alertas, juntamente com o seu recurso de aplicação. [Saiba mais](#access).
> 
> 

## <a name="resources-groups-and-subscriptions"></a>Recursos, grupos e subscrições
Primeiro, algumas definições:

* **Recurso** – uma instância de um serviço do Microsoft Azure. O recurso do Application Insights recolhe, analisa e apresenta os dados de telemetria enviados a partir da sua aplicação.  Outros tipos de recursos do Azure incluem as web apps, bases de dados e VMs.
  
    Para ver os recursos, abra o [Portal do Azure][portal], iniciar sessão e, em todos os recursos. Para localizar um recurso, escreva a parte do respetivo nome no campo de filtro.
  
    ![Lista de recursos do Azure](./media/app-insights-resources-roles-access-control/10-browse.png)

<a name="resource-group"></a>

* [**Grupo de recursos** ] [ group] -cada recurso pertence a um grupo. Um grupo é uma forma conveniente de gerir recursos relacionados, especialmente para controlo de acesso. Por exemplo, um grupo de recursos, pode colocar uma aplicação Web, um recurso do Application Insights para monitorizar a aplicação e um recurso de armazenamento para manter os dados exportados.

    ![Escolha procurar, grupos de recursos, em seguida, escolha um grupo](./media/app-insights-resources-roles-access-control/11-group.png)

* [**Subscrição** ](https://manage.windowsazure.com) - para utilizar o Application Insights ou outros recursos do Azure, pode iniciar sessão para uma subscrição do Azure. Cada grupo de recursos pertence a uma subscrição do Azure, onde escolher o seu pacote de preços e, se se tratar de uma subscrição de organização, escolha os membros e as respetivas permissões de acesso.
* [**Conta Microsoft** ] [ account] -o nome de utilizador e palavra-passe que utiliza para iniciar sessão Microsoft Azure subscrições, XBox Live, Outlook.com e outros serviços Microsoft.

## <a name="access"></a>Controlo de acesso no grupo de recursos
É importante compreender que, para além do recurso que criou para a sua aplicação, também existem recursos ocultos separados para os alertas e testes web. Estão ligados ao mesmo [grupo de recursos](#resource-group) que a aplicação. Poderá também colocados outros serviços do Azure no mesmo, tais como Web sites ou de armazenamento.

![Recursos no Application Insights](./media/app-insights-resources-roles-access-control/00-resources.png)

Para controlar o acesso a estes recursos, por conseguinte, é recomendado para:

* Controlar o acesso à **grupo de recursos ou subscrição** nível.
* Atribuir o **contribuinte de componentes do Application Insights** função para os utilizadores. Isto permite-lhes para editar os testes web, alertas e recursos do Application Insights, sem fornecer acesso a outros serviços no grupo.

## <a name="to-provide-access-to-another-user"></a>Para fornecer acesso a outro utilizador
Tem de ter direitos de proprietário para a subscrição ou o grupo de recursos.

O utilizador tem de ter um [Account Microsoft][account], ou acesso ao respetivo [Account institucional do Microsoft](../active-directory/sign-up-organization.md). Pode fornecer acesso indivíduos bem como para os grupos de utilizadores definidos no Azure Active Directory.

#### <a name="navigate-to-the-resource-group"></a>Navegue para o grupo de recursos
Adicione o utilizador não existe.

![No painel de recursos da sua aplicação, abra o Essentials, abra o grupo de recursos e selecione existe/utilizadores de definições. Clique em Adicionar.](./media/app-insights-resources-roles-access-control/01-add-user.png)

Ou foi aceda a cópia de segurança outro nível e adicionar o utilizador à subscrição.

#### <a name="select-a-role"></a>Selecione uma função
![Selecione uma função para o novo utilizador](./media/app-insights-resources-roles-access-control/03-role.png)

| Função | No grupo de recursos |
| --- | --- |
| Proprietário |Pode alterar tudo, incluindo o acesso de utilizador |
| Contribuinte |Pode editar qualquer coisa, incluindo todos os recursos |
| Contribuinte de componentes de informações de aplicação |Pode editar os recursos do Application Insights, testes web e alertas |
| Leitor |Pode ver, mas não altera nada |

'Editar' inclui a criação, eliminação e a atualização:

* Recursos
* Testes Web
* Alertas
* Exportação contínua

#### <a name="select-the-user"></a>Selecione o utilizador

Se o utilizador que pretende não se encontra no diretório, pode convidar qualquer pessoa com uma conta Microsoft.
(Se utilizarem serviços como o Outlook.com, OneDrive, Windows Phone ou XBox Live, têm uma conta Microsoft.)

## <a name="related-content"></a>Conteúdo relacionado

* [Controlo de acesso no Azure baseado em funções](../active-directory/role-based-access-control-configure.md)

<!--Link references-->

[account]: https://account.microsoft.com
[group]: ../azure-resource-manager/resource-group-overview.md
[portal]: https://portal.azure.com/
[start]: app-insights-overview.md
