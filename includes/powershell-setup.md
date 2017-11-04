---
services: virtual-machines
title: Configurar o PowerShell
author: JoeDavies-MSFT
solutions: 
manager: timlt
editor: tysonn
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 05/12/2015
ms.author: rasquill
ms.openlocfilehash: 19c704d965ff3e2fc9ac8c5b623aeb386cb0b974
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
## <a name="setting-up-powershell"></a>Configurar o PowerShell
Antes de poder utilizar o Azure PowerShell, siga estes passos.

### <a name="verify-powershell-versions"></a>Verificar se versões do PowerShell
Antes de poder utilizar o Windows PowerShell, tem de ter o Windows PowerShell, versão 3.0 ou 4.0. Para encontrar a versão do Windows PowerShell, escreva este comando numa linha de comandos do Windows PowerShell.

    $PSVersionTable

Deverá ver algo semelhante ao seguinte.

    Name                           Value
    ----                           -----
    PSVersion                      3.0
    WSManStackVersion              3.0
    SerializationVersion           1.1.0.1
    CLRVersion                     4.0.30319.18444
    BuildVersion                   6.2.9200.16481
    PSCompatibleVersions           {1.0, 2.0, 3.0}
    PSRemotingProtocolVersion      2.2

Certifique-se de que o valor de **PSVersion** é 3.0 ou 4.0. Para instalar uma versão compatível, consulte [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) ou [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855).

Também deverá ter o Azure PowerShell versão 0.8.0 ou posterior. Pode verificar a versão do Azure PowerShell que tenha instalado com este comando na linha de comandos do Azure PowerShell.

    Get-Module azure | format-table version

Deverá ver algo semelhante ao seguinte.

    Version
    -------
    0.8.16.1

Para obter instruções e uma hiperligação para a versão mais recente, consulte [como instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs).

### <a name="set-your-azure-account-and-subscription"></a>Definir a conta e a subscrição do Azure
Se ainda não tiver uma subscrição do Azure, pode ativar o [benefícios de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se um [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).

Abra uma linha de comandos do Azure PowerShell e inicie sessão no Azure com este comando.

    Add-AzureAccount

Se tiver várias subscrições do Azure, pode listar as subscrições do Azure com este comando.

    Get-AzureSubscription

Receberá os tipos de informações seguintes:

    SubscriptionId            : fd22919d-eaca-4f2b-841a-e4ac6770g92e
    SubscriptionName          : Visual Studio Ultimate with MSDN
    Environment               : AzureCloud
    SupportedModes            : AzureServiceManagement,AzureResourceManager
    DefaultAccount            : johndoe@contoso.com
    Accounts                  : {johndoe@contoso.com}
    IsDefault                 : True
    IsCurrent                 : True
    CurrentStorageAccountName : 
    TenantId                  : 32fa88b4-86f1-419f-93ab-2d7ce016dba7

Pode definir a subscrição do Azure atual, executando estes comandos na linha de comandos do Azure PowerShell. Substituir tudo dentro de aspas, incluindo os < e > carateres, com o nome correto.

    $subscr="<SubscriptionName from the display of Get-AzureSubscription>"
    Select-AzureSubscription -SubscriptionName $subscr -Current    

Para obter mais informações sobre as subscrições do Azure e as contas, consulte [como: ligar à sua subscrição](/powershell/azureps-cmdlets-docs#Connect).

