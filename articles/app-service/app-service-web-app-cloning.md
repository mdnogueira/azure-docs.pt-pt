---
title: "Clonagem de aplicação de Web com o PowerShell"
description: "Saiba como clonar das suas aplicações Web novas aplicações da Web através do PowerShell."
services: app-service\web
documentationcenter: 
author: ahmedelnably
manager: stefsch
editor: 
ms.assetid: f9a5cfa1-fbb0-41e6-95d1-75d457347a35
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/13/2016
ms.author: aelnably
ms.openlocfilehash: dc252903571857b5fc89d1d9a2c63cd6b44e9021
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-app-service-app-cloning-using-powershell"></a>Aplicação do App Service do Azure através do PowerShell de clonagem
Com o lançamento do Microsoft Azure PowerShell versão 1.1.0 foi adicionada uma nova opção para New-AzureRMWebApp seria conceder ao utilizador a capacidade de clone de uma aplicação Web existente para uma aplicação recentemente criada numa região diferente ou na mesma região. Isto permitirá aos clientes implementar com um número de aplicações em diferentes regiões forma rápida e fácil.

Aplicação da clonagem está atualmente só é suportada para planos de serviço de aplicações de escalão premium. A nova funcionalidade utiliza as mesmas limitações, como a funcionalidade de cópia de segurança do Web Apps, consulte [cópia de segurança numa aplicação web no App Service do Azure](web-sites-backup.md).

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="cloning-an-existing-app"></a>A clonagem de uma aplicação existente
Cenário: Uma aplicação web existente no Sul Central-na região, o utilizador pretende clonar o conteúdo para uma nova aplicação web na região Norte Central-nos. Isto pode ser conseguido utilizando a versão do Gestor de recursos do Azure do cmdlet do PowerShell para criar uma nova aplicação web com a opção - SourceWebApp.

Saber o nome do grupo de recursos que contém a aplicação web de origem, podemos utilizar o seguinte comando do PowerShell para obter informações sobre a aplicação de web de origem (neste caso, denominado webapp de origem):

    $srcapp = Get-AzureRmWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp

Para criar um novo plano do App Service, podemos utilizar comandos de AzureRmAppServicePlan novo como no exemplo seguinte

    New-AzureRmAppServicePlan -Location "South Central US" -ResourceGroupName DestinationAzureResourceGroup -Name NewAppServicePlan -Tier Premium

Utilizando o comando de novo AzureRmWebApp, pode criar a nova aplicação web na região Norte Central-nos e associá-lo para um escalão premium existente plano do App Service, além pode utilizar o mesmo grupo de recursos que a aplicação web de origem ou definir um novo grupo de recursos, demonstra o seguinte:

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp

A clonagem de uma aplicação web existente, incluindo a implementação de todos os associado ranhuras, o utilizador terá de utilizar o parâmetro IncludeSourceWebAppSlots, o comando do PowerShell seguinte demonstra a utilização desse parâmetro com o comando de novo AzureRmWebApp:

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -IncludeSourceWebAppSlots

A clonagem de uma aplicação web existente na mesma região, o utilizador terá de criar um novo grupo de recursos e um novo serviço de aplicações do plano na mesma região e, em seguida, utilizar o seguinte comando do PowerShell para clonar a aplicação web

    $destapp = New-AzureRmWebApp -ResourceGroupName NewAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan NewAppServicePlan -SourceWebApp $srcap

## <a name="cloning-an-existing-app-to-an-app-service-environment"></a>A clonagem de uma aplicação existente para um ambiente de serviço de aplicações
Cenário: Uma aplicação web existente no Sul Central-na região, o utilizador pretende clonar o conteúdo para uma nova aplicação web para existente aplicação serviço de ambiente (ASE).

Saber o nome do grupo de recursos que contém a aplicação web de origem, podemos utilizar o seguinte comando do PowerShell para obter informações sobre a aplicação de web de origem (neste caso, denominado webapp de origem):

    $srcapp = Get-AzureRmWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp

Saber o nome do ASE e o nome do grupo de recursos que o ASE pertence, o utilizador pode utilizar o comando de novo AzureRmWebApp para criar a nova aplicação web no ASE existente, demonstra o seguinte:

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -ASEName DestinationASE -ASEResourceGroupName DestinationASEResourceGroupName -SourceWebApp $srcapp

O parâmetro de localização não é necessário devido à razão legado, mas no caso de criação de uma aplicação está num ASE serão ignorada. 

## <a name="cloning-an-existing-app-slot"></a>A clonagem de uma ranhura de aplicação existente
Cenário: O utilizador pretende clonar uma ranhura de aplicação Web existente ou uma nova aplicação Web ou uma nova ranhura de aplicação Web. A nova aplicação Web pode ser na mesma região que a ranhura de aplicação Web original ou numa região diferente.

Saber o nome do grupo de recursos que contém a aplicação web de origem, podemos utilizar o seguinte comando do PowerShell para obter informações da origem ranhura de aplicação web (neste caso, denominadas origem webappslot) associadas a webapp de origem de aplicação Web:

    $srcappslot = Get-AzureRmWebAppSlot -ResourceGroupName SourceAzureResourceGroup -Name source-webapp -Slot source-webappslot

O seguinte mostra como criar um clone da aplicação de web de origem para uma nova aplicação web:

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcappslot

## <a name="configuring-traffic-manager-while-cloning-a-app"></a>Configurar o Gestor de tráfego durante a clonagem de uma aplicação
Criar aplicações web de multirregião e configurar o Gestor de tráfego do Azure para encaminhar o tráfego para todas estas aplicações web, é um cenário importante n a assegurar que aplicações de clientes são de elevada disponibilidade, quando a clonagem de uma aplicação web existente tem a opção Ligar ambas as aplicações web para um novo perfil de Gestor de tráfego ou uma existente-tenha em atenção que apenas o Azure Resource Manager é suportada a versão do Gestor de tráfego.

### <a name="creating-a-new-traffic-manager-profile-while-cloning-a-app"></a>Criar um novo perfil de Gestor de tráfego durante a clonagem de uma aplicação
Cenário: O utilizador pretende que a clonagem de uma aplicação web noutra região, ao configurar um perfil do Gestor de tráfego do Azure Resource Manager que incluem ambas as aplicações web. O seguinte mostra como criar um clone da aplicação de web de origem para uma nova aplicação web ao configurar um novo perfil de Gestor de tráfego:

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileName newTrafficManagerProfile

### <a name="adding-new-cloned-web-app-to-an-existing-traffic-manager-profile"></a>Adicionar novos clonar a aplicação Web para um perfil do Traffic Manager existente
Cenário: O utilizador já tem um perfil do Gestor de tráfego do Azure Resource Manager que ele pretende adicionar ambas as aplicações web como pontos finais. Para fazer, pelo que, primeiro é preciso Monte o id de perfil de Gestor de tráfego existente, é necessário o id de subscrição, nome do grupo de recursos e o nome de perfil de Gestor de tráfego existente.

    $TMProfileID = "/subscriptions/<Your subscription ID goes here>/resourceGroups/<Your resource group name goes here>/providers/Microsoft.TrafficManagerProfiles/ExistingTrafficManagerProfileName"

Depois de ter o id do tráfego do Configuration Manager, o seguinte mostra como criar um clone da aplicação de web de origem para uma nova aplicação web ao adicioná-las para um perfil do Traffic Manager existente:

    $destapp = New-AzureRmWebApp -ResourceGroupName <Resource group name> -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileId $TMProfileID

## <a name="current-restrictions"></a>Restrições atuais
Esta funcionalidade está atualmente em pré-visualização, estamos a trabalhar para adicionar novas capacidades ao longo do tempo, a lista seguinte são as restrições conhecidas na versão atual da clonagem de aplicação:

* As definições de dimensionamento automático não são Clonadas
* Definições da agenda de cópia de segurança não são Clonadas
* Definições da VNET não são Clonadas
* Informações de aplicação não são automaticamente configuradas na aplicação de web de destino
* As definições de autenticação fácil não são Clonadas
* Extensão do kudu não são Clonadas
* Regras de sugestão não são Clonadas
* Conteúdo da base de dados não são Clonadas

### <a name="references"></a>Referências
* [A clonagem de aplicação Web](app-service-web-app-cloning.md)
* [Cópia de segurança de uma aplicação web no App Service do Azure](web-sites-backup.md)
* [Suporte de Gestor de recursos do Azure para a pré-visualização do Traffic Manager do Azure](../traffic-manager/traffic-manager-powershell-arm.md)
* [Introdução ao Ambiente do Serviço de Aplicações](environment/intro.md)
* [Utilizar o Azure PowerShell com o Azure Resource Manager](../azure-resource-manager/powershell-azure-resource-manager.md)

