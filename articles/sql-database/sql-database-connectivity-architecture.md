---
title: Arquitetura de conectividade da base de dados SQL do Azure | Microsoft Docs
description: Este documento explica a arquitetura da conectividade do Azure SQLDB no Azure ou a partir de fora do Azure.
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: monicar
ms.assetid: 
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: On Demand
ms.date: 06/05/2017
ms.author: carlrab
ms.openlocfilehash: eda6e19d27afbf07df853dd4cef5ece1a745034d
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2017
---
# <a name="azure-sql-database-connectivity-architecture"></a>Arquitetura de conectividade de base de dados SQL do Azure 

Este artigo explica a arquitetura de conectividade da SQL Database do Azure e explica a forma como os diferentes componentes funcionarem para direcionar o tráfego para a instância do SQL Database do Azure. Estes SQL Database do Azure conectividade componentes a função para direcionar o tráfego de rede para a base de dados do Azure com os clientes ligar a partir de dentro do Azure e com os clientes ligar a partir de fora do Azure. Este artigo também fornece exemplos de script para alterar a forma como ocorre a conectividade e as considerações relacionadas com a alterar as definições de conetividade de predefinição. 

## <a name="connectivity-architecture"></a>Arquitetura de conectividade

O diagrama seguinte fornece uma descrição geral de alto nível da arquitetura de conectividade do SQL Database do Azure.

![Descrição geral da arquitetura](./media/sql-database-connectivity-architecture/architecture-overview.png)


Os passos seguintes descrevem como é estabelecida uma ligação para uma base de dados SQL do Azure através do SQL Database do Azure software Balanceador de carga (SLB) e o gateway da SQL Database do Azure.

- Ligam clientes no Azure ou fora do Azure para SLB, que tem um endereço IP público e escuta na porta 1433.
- O SLB direciona o tráfego para o gateway da SQL Database do Azure.
- O gateway redireciona o tráfego para o middleware de proxy correta.
- O middleware de proxy redireciona o tráfego para a base de dados SQL do Azure adequada.

> [!IMPORTANT]
> Cada um destes componentes tem ataques denial of protection service (DDoS distribuídos) incorporada na rede e a camada de aplicação.
>

## <a name="connectivity-from-within-azure"></a>Conectividade no Azure

Se estiver a ligar no Azure, as suas ligações tem uma política de ligação do **redirecionar** por predefinição. Uma política do **redirecionar** significa que as ligações após a sessão TCP é estabelecida para a base de dados SQL do Azure, a sessão de cliente, em seguida, é redirecionado para o middleware de proxy com uma alteração de IP virtual de destino do que o gateway de SQL Database do Azure para que o middleware de proxy. Depois disso, todos os pacotes subsequentes fluxo diretamente através do middleware de proxy, ignorar o gateway da SQL Database do Azure. O diagrama seguinte ilustra o fluxo de tráfego.

![Descrição geral da arquitetura](./media/sql-database-connectivity-architecture/connectivity-from-within-azure.png)

## <a name="connectivity-from-outside-of-azure"></a>Conectividade de fora do Azure

Se estiver a ligar a partir de fora do Azure, as suas ligações tem uma política de ligação do **Proxy** por predefinição. Uma política do **Proxy** significa que a sessão TCP é estabelecida através do gateway da SQL Database do Azure e todos os pacotes subsequentes fluxo através do gateway. O diagrama seguinte ilustra o fluxo de tráfego.

![Descrição geral da arquitetura](./media/sql-database-connectivity-architecture/connectivity-from-outside-azure.png)

## <a name="azure-sql-database-gateway-ip-addresses"></a>Endereços de IP do gateway de base de dados SQL do Azure

Para ligar a uma base de dados SQL do Azure a partir dos recursos no local, terá de permitir tráfego de rede de saída para o gateway de SQL Database do Azure para a região do Azure. As suas ligações aceda apenas através do gateway ao ligar-se no modo de Proxy, que é a predefinição quando ligar a partir de recursos no local.

A tabela seguinte lista os IPs primário e secundário do gateway para todas as regiões de dados SQL Database do Azure. Para algumas regiões, existem dois endereços IP. Estas regiões, o endereço IP primário é o endereço IP atual do gateway e o segundo endereço IP é um endereço IP de ativação pós-falha. O endereço de ativação pós-falha é o endereço para o qual pode mover o servidor para manter a elevada disponibilidade do serviço. Para estas regiões, recomendamos que autorize saída para os endereços IP. O segundo endereço IP pertencente à Microsoft e não escutar todos os serviços até ser ativado pela base de dados do Azure SQL Server para aceitar ligações.

| Nome da Região | Endereço IP primário | Endereço IP secundário |
| --- | --- |--- |
| Leste da Austrália | 191.238.66.109 | 13.75.149.87 |
| Sudeste da Austrália | 191.239.192.109 | 13.73.109.251 |
| Sul do Brasil | 104.41.11.5 | |
| Canadá Central | 40.85.224.249 | |
| Leste do Canadá | 40.86.226.166 | |
| EUA Central | 23.99.160.139 | 13.67.215.62 |
| Ásia Oriental | 191.234.2.139 | 52.175.33.150 |
| EUA Leste 1 | 191.238.6.43 | 40.121.158.30 |
| EUA Leste 2 | 191.239.224.107 | 40.79.84.180 |
| Índia Central | 104.211.96.159  | |
| Índia do Sul | 104.211.224.146  | |
| Índia Ocidental | 104.211.160.80 | |
| Leste do Japão | 191.237.240.43 | 13.78.61.196 |
| Oeste do Japão | 191.238.68.11 | 104.214.148.156 |
| Coreia Central | 52.231.32.42 | |
| Coreia do Sul | 52.231.200.86 |  |
| EUA Centro-Norte | 23.98.55.75 | 23.96.178.199 |
| Europa do Norte | 191.235.193.75 | 40.113.93.91 |
| EUA Centro-Sul | 23.98.162.75 | 13.66.62.124 |
| Sudeste Asiático | 23.100.117.95 | 104.43.15.0 |
| Norte do Reino Unido | 13.87.97.210 | |
| Sul do RU 1 | 51.140.184.11 | |
| Sul do Reino Unido 2 | 13.87.34.7 | |
| Reino Unido Oeste | 51.141.8.11  | |
| EUA Centro-Oeste | 13.78.145.25 | |
| Europa Ocidental | 191.237.232.75 | 40.68.37.158 |
| EUA oeste 1 | 23.99.34.75 | 104.42.238.205 |
| EUA Oeste 2 | 13.66.226.202  | |
||||

## <a name="change-azure-sql-database-connection-policy"></a>Alterar a política de ligação de SQL Database do Azure

Para alterar a política de ligação de SQL Database do Azure para um servidor de base de dados do Azure SQL, utilize o [REST API](https://msdn.microsoft.com/library/azure/mt604439.aspx).

- Se a sua política de ligação é definida como **Proxy**, todos os pacotes de fluxo através do gateway da SQL Database do Azure de rede. Para esta definição, tem de permitir a saída para apenas o IP do gateway SQL Database do Azure. Utilizar uma definição de **Proxy** tem a latência mais que uma definição de **redirecionar**.
- Se a política de ligação é **redirecionar**, todos os pacotes fluxo diretamente para o middleware de proxy de rede. Para esta definição, tem de permitir a saída para vários IPs.

## <a name="script-to-change-connection-settings-via-powershell"></a>Script para alterar as definições de ligação através do PowerShell

> [!IMPORTANT]
> Este script requer o [módulo Azure PowerShell](/powershell/azure/install-azurerm-ps).
>

O script do PowerShell seguinte mostra como alterar a política de ligação.

```powershell
Add-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName <Subscription Name>

# Azure Active Directory ID
$tenantId = "<Azure Active Directory GUID>"
$authUrl = "https://login.microsoftonline.com/$tenantId"

# Subscription ID
$subscriptionId = "<Subscription GUID>"

# Create an App Registration in Azure Active Directory.  Ensure the application type is set to NATIVE
# Under Required Permissions, add the API:  Windows Azure Service Management API

# Specify the redirect URL for the app registration
$uri = "<NATIVE APP - REDIRECT URI>"

# Specify the application id for the app registration
$clientId = "<NATIVE APP - APPLICATION ID>"

# Logical SQL Server Name
$serverName = "<LOGICAL DATABASE SERVER - NAME>"

# Resource Group where the SQL Server is located
$resourceGroupName= "<LOGICAL DATABASE SERVER - RESOURCE GROUP NAME>"


# Login and acquire a bearer token
$AuthContext = [Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext]$authUrl
$result = $AuthContext.AcquireToken(
"https://management.core.windows.net/",
$clientId,
[Uri]$uri,
[Microsoft.IdentityModel.Clients.ActiveDirectory.PromptBehavior]::Auto
)

$authHeader = @{
'Content-Type'='application\json; '
'Authorization'=$result.CreateAuthorizationHeader()
}

#Get current connection Policy
Invoke-RestMethod -Uri "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/servers/$serverName/connectionPolicies/Default?api-version=2014-04-01-preview" -Method GET -Headers $authHeader

#Set connection policy to Proxy
$connectionType="Proxy" <#Redirect / Default are other options#>
$body = @{properties=@{connectionType=$connectionType}} | ConvertTo-Json

# Apply Changes
Invoke-RestMethod -Uri "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/servers/$serverName/connectionPolicies/Default?api-version=2014-04-01-preview" -Method PUT -Headers $authHeader -Body $body -ContentType "application/json"
```

## <a name="script-to-change-connection-settings-via-azure-cli-20"></a>Script para alterar as definições de ligação através do Azure CLI 2.0

> [!IMPORTANT]
> Este script requer o [Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest).
>

O script CLI seguinte mostra como alterar a política de ligação.

<pre>
# Get SQL Server ID
sqlserverid=$(az sql server show -n <b>sql-server-name</b> -g <b>sql-server-group</b> --query 'id' -o tsv)

# Set URI
id="$sqlserverid/connectionPolicies/Default"

# Get current connection policy 
az resource show --ids $id

# Update connection policy 
az resource update --ids $id --set properties.connectionType=Proxy

</pre>

## <a name="next-steps"></a>Passos seguintes

- Para obter informações sobre como alterar a política de ligação de SQL Database do Azure para um servidor de base de dados do Azure SQL, consulte [Create ou política de ligação de servidor de atualização utilizando a API REST](https://msdn.microsoft.com/library/azure/mt604439.aspx).
- Para informações sobre o comportamento de ligação de SQL Database do Azure para clientes que utilizam ADO.NET 4.5 ou uma versão posterior, consulte [portas para além de 1433 para ADO.NET 4.5](sql-database-develop-direct-route-ports-adonet-v12.md).
- Para informações de descrição geral do desenvolvimento de aplicações gerais, consulte [descrição geral do desenvolvimento de aplicações de base de dados do SQL Server](sql-database-develop-overview.md).
