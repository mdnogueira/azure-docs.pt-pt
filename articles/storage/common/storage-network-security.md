---
title: "Configurar Firewalls de armazenamento do Azure e as redes virtuais (pré-visualização) | Microsoft Docs"
description: "Configure a segurança de rede em camadas para a sua conta de armazenamento."
services: storage
documentationcenter: 
author: cbrooksmsft
manager: cbrooks
editor: cbrooks
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 10/25/2017
ms.author: cbrooks
ms.openlocfilehash: 2e155231e430a8333095fdcd92a727a17c6d1e8c
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="configure-azure-storage-firewalls-and-virtual-networks-preview"></a>Configurar Firewalls de armazenamento do Azure e as redes virtuais (pré-visualização)
Armazenamento do Azure fornece um modelo de segurança por camadas que permite proteger as contas de armazenamento para um conjunto específico de redes permitidos.  Quando as regras de rede estiverem configuradas, apenas as aplicações permitidas redes podem aceder a uma conta de armazenamento.  Ao chamar a partir de uma rede permitida, as aplicações continuam a exigir autorização adequada (uma chave de acesso válido ou SAS token) para aceder à conta de armazenamento.

## <a name="preview-availability-and-support"></a>Disponibilidade de pré-visualização e suporte
Firewalls de armazenamento e redes virtuais estão em pré-visualização.  Esta capacidade está atualmente disponível para contas de armazenamento novo ou existente em todas as regiões de nuvem pública do Azure.

> [!NOTE]
> Cargas de trabalho de produção não são suportadas durante a pré-visualização.
>

## <a name="scenarios"></a>Cenários
As contas de armazenamento podem ser configuradas para negar o acesso ao tráfego de todas as redes (incluindo o tráfego de internet), por predefinição.  Acesso pode ser concedido a tráfego específico redes virtuais do Azure, permitindo-lhe criar um limite de rede segura para as suas aplicações.  Também pode ser concedido acesso à internet pública intervalos de endereços IP, ativação de ligações de clientes de internet ou local específicas.

Regras de rede são impostas em todos os protocolos de rede ao armazenamento do Azure, incluindo REST e SMB.  Portal do Azure, o Explorador de armazenamento, como o acesso aos seus dados a partir de ferramentas e AZCopy requerem regras de rede explícita conceder o acesso quando as regras de rede são no force.

Regras de rede podem ser aplicadas a contas do Storage existentes ou podem ser aplicadas durante a criação de novas contas de armazenamento.

Assim que são aplicadas as regras de rede, estes são impostas para todos os pedidos.  Os tokens SAS que concedam acesso a um serviço de endereço IP específico servem para **limite** o acesso do marcador de posição token, fazer, mas não conceder novo acesso para além de regras de rede configurados. 

O tráfego de disco da máquina virtual (incluindo montar e desmontar operações e e/s de disco) é **não** afetadas pelas regras de rede.  Acesso REST para blobs de páginas está protegido por regras de rede.

As contas de armazenamento clássicas **não** suportam as Firewalls e redes virtuais.

## <a name="change-the-default-network-access-rule"></a>Alterar a regra de acesso de rede predefinida
Por predefinição, as contas do storage aceitam ligações de clientes em qualquer rede.  Para limitar o acesso a redes selecionados, primeiro tem de alterar a ação predefinida.

> [!WARNING]
> Efetuar alterações às regras de rede pode afetar a capacidade das suas aplicações para estabelecer ligação ao armazenamento do Azure.  Definir a regra de rede predefinido para **negar** todos os blocos de acesso aos dados, a menos que as regras de rede específicas *conceder* acesso também são aplicadas.  É necessário conceder acesso a quaisquer redes permitidos através de regras de rede antes de alterar a regra predefinida para negar o acesso.
>

#### <a name="azure-portal"></a>Portal do Azure
1. Navegue para a conta de armazenamento que pretende proteger.  
> [!NOTE]
> Certifique-se a sua conta de armazenamento é de uma das regiões suportadas para a pré-visualização pública.
>

2. Clique no menu de definições denominado **Firewalls e redes virtuais**.
3. Negar o acesso por predefinição, optar por permitir acesso a partir de 'Selecionados redes'.  Para permitir tráfego de todas as redes, optar por permitir acesso a partir de 'Todas as redes'.
4. Clique em *guardar* para aplicar as suas alterações.

#### <a name="powershell"></a>PowerShell
1. Instalar a versão mais recente [Azure PowerShell](/powershell/azure/install-azurerm-ps) e [início de sessão](/powershell/azure/authenticate-azureps).

2. Apresenta o estado da regra predefinida para a conta de armazenamento.
```PowerShell
(Get-AzureRmStorageAccountNetworkRuleSet  -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").DefaultAction
``` 

3. Defina a regra predefinida para negar o acesso de rede por predefinição.  
```PowerShell
Update-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Deny
```    

4. Defina a regra predefinida para permitir o acesso de rede por predefinição.
```PowerShell
Update-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Allow
```    

#### <a name="cliv2"></a>CLIv2
1. [Instalar a CLI do Azure 2.0](/cli/azure/install-azure-cli) e [início de sessão](/cli/azure/authenticate-azure-cli).
2. Apresenta o estado da regra predefinida para a conta de armazenamento.
```azurecli
az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.defaultAction
```

3. Defina a regra predefinida para negar o acesso de rede por predefinição.  
```azurecli
az storage account update --name "mystorageaccount" --resource-group "myresourcegroup" --default-action Deny
```

4. Defina a regra predefinida para permitir o acesso de rede por predefinição.
```azurecli
az storage account update --name "mystorageaccount" --resource-group "myresourcegroup" --default-action Allow
```

## <a name="grant-access-from-a-virtual-network"></a>Conceder acesso a partir de uma rede virtual
As contas de armazenamento podem ser configuradas para permitir o acesso apenas a partir de redes virtuais do Azure específicas. 

Ao ativar uma [ponto final de serviço](/azure/virtual-network/virtual-network-service-endpoints-overview) para o armazenamento do Azure dentro da rede Virtual, o tráfego está a certificar-se uma rota ideal para o serviço de armazenamento do Azure. As identidades de rede virtual e a sub-rede também são transmitidas com cada pedido.  Os administradores, subsequentemente, podem configurar regras de rede para a conta de armazenamento que permitem pedidos a serem recebidos de sub-redes específicas na rede Virtual.  Os clientes concedido acesso via estas regras de rede têm de continuar para cumprir os requisitos de autorização da conta do Storage para aceder aos dados.

Cada conta de armazenamento pode suportar até 100 regras de rede Virtual que podem ser combinadas com [regras de rede IP](#grant-access-from-an-internet-ip-range).

### <a name="available-virtual-network-regions"></a>Regiões de rede Virtual
Em geral, os pontos finais de serviço funcionar entre redes virtuais e instâncias de serviço na mesma região do Azure.  Quando são utilizados pontos finais de serviço com o Storage do Azure, este âmbito é expandido para incluir o [região emparelhado](/azure/best-practices-availability-paired-regions).  Isto permite a continuidade durante uma ativação pós-falha, bem como seemless acesso a instâncias de armazenamento (RA-GRS) georreplicação-reduntant só de leitura.  Regras de rede que concedam acesso a partir de uma rede virtual para uma conta de armazenamento também conceder acesso para qualquer instância de RA-GRS.

Quando planear a recuperação após desastre durante uma falha regional, deve aprovisionar as redes virtuais na região emparelhada com antecedência. Pontos finais do serviço de armazenamento do Azure deve estar ativados e conceder acesso estas redes virtuais alternativo de regras de rede devem ser aplicadas às suas contas de armazenamento georredundante.

> [!NOTE]
> Pontos finais de serviço não se aplicam ao tráfego fora da região de rede Virtual e o par de região designado.  Regras de rede conceder acesso de redes virtuais para as contas de armazenamento só podem ser aplicadas para as redes virtuais na região primária de uma conta de armazenamento ou na região emparelhada designada.
>

### <a name="required-permissions"></a>Permissões necessárias
Para aplicar uma regra de rede Virtual a uma conta de armazenamento, o utilizador tem de ter permissão para *aderir ao serviço a uma sub-rede* para as sub-redes que está a ser adicionadas.  Esta permissão está incluído no *contribuinte de conta de armazenamento* função incorporada e podem ser adicionados à definição de função personalizada.

Conta de armazenamento e as redes virtuais concedido acesso **poderá** ser em subscrições diferentes, mas essas subscrições têm de fazer parte do mesmo inquilino do Azure Active Directory.

### <a name="managing-virtual-network-rules"></a>A gestão de regras de rede Virtual
Regras de rede virtuais para contas de armazenamento podem ser geridas através do portal do Azure, PowerShell ou CLIv2.

#### <a name="azure-portal"></a>Portal do Azure
1. Navegue para a conta de armazenamento que pretende proteger.  
2. Clique no menu de definições denominado **Firewalls e redes virtuais**.
3. Certifique-se de que tiver optado por permitir acesso a partir de 'Selecionados redes'.
4. Para conceder acesso a uma rede Virtual com uma nova regra de rede, em "Redes virtuais", clique em "Adicionar existente" para selecionar uma rede Virtual existente e sub-redes, em seguida, clique em *adicionar*.  Para criar uma nova rede Virtual e conceder acesso, clique em *adicionar novo*, forneça as informações necessárias para criar a nova rede Virtual e, em seguida, clique em *criar*.

> [!NOTE]
> Se um ponto final do serviço de armazenamento do Azure não tenha sido anteriormente configurado para a rede Virtual selecionada e a sub-redes, pode ser configurado como parte desta operação.
>

5. Para remover uma regra de rede ou uma sub-rede Virtual, clique em "…" para abrir o menu de contexto para a rede Virtual ou a sub-rede e clique em "Remover".
6. Clique em *guardar* para aplicar as suas alterações.

#### <a name="powershell"></a>PowerShell
1. Instalar a versão mais recente [Azure PowerShell](/powershell/azure/install-azurerm-ps) e [início de sessão](/powershell/azure/authenticate-azureps).
2. Lista regras de rede Virtual
```PowerShell
(Get-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").VirtualNetworkRules
```

3. Ativar o ponto final de serviço para o armazenamento de Azure de uma rede Virtual existente e a sub-rede
```PowerShell
Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet"  -AddressPrefix "10.1.1.0/24" -ServiceEndpoint "Microsoft.Storage" | Set-AzureRmVirtualNetwork
```

4. Adicione uma regra de rede para uma rede Virtual e a sub-rede.  
```PowerShell
$subnet = Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet"
Add-AzureRmStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id   
```    

5. Remova uma regra de rede para uma rede Virtual e a sub-rede.  
```PowerShell
$subnet = Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet"
Remove-AzureRmStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id   
```    

> [!IMPORTANT]
> Certifique-se para [definir a regra predefinida](#change-the-default-network-access-rule) para negar, ou as regras de rede não tem qualquer efeito.
>

#### <a name="cliv2"></a>CLIv2
1. [Instalar a CLI do Azure 2.0](/cli/azure/install-azure-cli) e [início de sessão](/cli/azure/authenticate-azure-cli).
2. Lista regras de rede Virtual
```azurecli
az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query virtualNetworkRules
```

2. Ativar o ponto final de serviço para o armazenamento de Azure de uma rede Virtual existente e a sub-rede
```azurecli
az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.Storage"
```

3. Adicione uma regra de rede para uma rede Virtual e a sub-rede.  
```azurecli
subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
```

4. Remova uma regra de rede para uma rede Virtual e a sub-rede. 
```azurecli
subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
```

> [!IMPORTANT]
> Certifique-se para [definir a regra predefinida](#change-the-default-network-access-rule) para negar, ou as regras de rede não tem qualquer efeito.
>

## <a name="grant-access-from-an-internet-ip-range"></a>Conceder acesso a partir de um internet intervalo de IP
As contas de armazenamento podem ser configuradas para permitir o acesso a partir da internet pública específica intervalos de endereços IP.  Esta configuração permite que os serviços baseados na internet específicos e no local redes para ser concedido acesso ao tráfego de internet geral é bloqueado.

Permitido intervalos de endereços de internet podem ser fornecidos utilizando [notação CIDR](https://tools.ietf.org/html/rfc4632) no formato *16.17.18.0/24* ou como IP individuais, como endereços *16.17.18.19* .

> [!NOTE]
> Intervalos de endereços pequeno com "/ 31" ou "/ 32" prefixo tamanhos não são suportados.  Estes intervalos devem ser configurados utilizando regras de endereços IP individuais.
>

Regras de rede IP só são permitidas para **internet pública** endereços IP.  Os intervalos de endereços IP reservados para redes privadas (conforme definido no RFC 1918) não são permitidos nas regras IP.  Redes privadas incluem endereços que comecem com *10.\** , *172.16.\** , e *192.168.\** .

Apenas os endereços IPV4 são suportados neste momento.

Cada conta de armazenamento pode suportar até 100 regras de rede IP que poderão ser combinadas com [regras de rede Virtual](#grant-access-from-a-virtual-network)

### <a name="configuring-access-from-on-premises-networks"></a>Configurar o acesso a partir de redes no local
Para conceder acesso a redes no local à sua conta de armazenamento com uma regra de rede IP, tem de identificar a internet com endereços IP utilizados pela sua rede.  Contacte o administrador de rede para obter ajuda.

Se a sua rede está ligada à utilização de rede do Azure [ExpressRoute](/azure/expressroute/expressroute-introduction), cada circuito está configurado com dois endereços IP públicos no Microsoft Edge que são utilizados para ligar ao Services da Microsoft, como o Storage do Azure utilizando [Peering público do azure](/azure/expressroute/expressroute-circuit-peerings#expressroute-routing-domains).  Para permitir a comunicação entre o seu circuito e Storage do Azure, tem de criar regras de rede IP para os endereços IP públicos do seu circuitos.  Para localizar endereços IP públicos do seu circuito de ExpressRoute, [abrir um pedido de suporte com o ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) através do portal do Azure.


### <a name="managing-ip-network-rules"></a>Gerir regras de rede IP
Regras de rede IP para as contas de armazenamento podem ser geridas através do portal do Azure, PowerShell ou CLIv2.

#### <a name="azure-portal"></a>Portal do Azure
1. Navegue para a conta de armazenamento que pretende proteger.  
2. Clique no menu de definições denominado **Firewalls e redes virtuais**.
3. Certifique-se de que tiver optado por permitir acesso a partir de 'Selecionados redes'.
4. Para conceder acesso a um internet intervalo de IP, introduza o endereço IP ou intervalo de endereços (no formato CIDR) na Firewall, intervalos de endereços.
5. Para remover uma regra de rede IP, clique em "…" para abrir o menu de contexto para a regra e clique em "Remover".
6. Clique em *guardar* para aplicar as suas alterações.

#### <a name="powershell"></a>PowerShell
1. Instalar a versão mais recente [Azure PowerShell](/powershell/azure/install-azurerm-ps) e [início de sessão](/powershell/azure/authenticate-azureps).
2. Lista regras de rede IP.
```PowerShell
(Get-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").IPRules
```

3. Adicione uma regra de rede para um endereço IP individual.  
```PowerShell
Add-AzureRMStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19" 
``` 

4. Adicione uma regra de rede para um intervalo de endereços IP.  
```PowerShell
Add-AzureRMStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24" 
```    

5. Remova uma regra de rede para um endereço IP individual. 
```PowerShell
Remove-AzureRMStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"  
```

6. Remova uma regra de rede para um intervalo de endereços IP.  
```PowerShell
Remove-AzureRMStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"  
```    

> [!IMPORTANT]
> Certifique-se para [definir a regra predefinida](#change-the-default-network-access-rule) para negar, ou as regras de rede não tem qualquer efeito.
>

#### <a name="cliv2"></a>CLIv2
1. [Instalar a CLI do Azure 2.0](/cli/azure/install-azure-cli) e [início de sessão](/cli/azure/authenticate-azure-cli).
2. Lista regras de rede IP
```azurecli
az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query ipRules
```

3. Adicione uma regra de rede para um endereço IP individual.
```azurecli
az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
```

4. Adicione uma regra de rede para um intervalo de endereços IP.  
```azurecli
az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
```

5. Remova uma regra de rede para um endereço IP individual.  
```azurecli
az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
```

6. Remova uma regra de rede para um intervalo de endereços IP.  
```azurecli
az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
```

> [!IMPORTANT]
> Certifique-se para [definir a regra predefinida](#change-the-default-network-access-rule) para negar, ou as regras de rede não tem qualquer efeito.
>

## <a name="exceptions"></a>Exceções
Enquanto as regras de rede podem ativar uma configuração de rede segura para a maioria dos cenários, existem alguns casos em que as exceções têm de ser concedidas para ativar a funcionalidade completa.  As contas de armazenamento podem ser configuradas com exceções nos serviços da Microsoft fidedignos e para aceder aos dados de análise de armazenamento.

### <a name="trusted-microsoft-services"></a>Serviços da Microsoft fidedigno
Alguns serviços da Microsoft que interagem com contas do Storage operam de redes que não não possível conceder acesso através de regras de rede. 

Para permitir que este tipo de serviço a funcionar conforme pretendido, é possível permitir o conjunto de serviços Microsoft fidedignos para ignorar as regras de rede. Estes serviços, em seguida, irão utilizar a autenticação forte para aceder à conta de armazenamento.

Quando a exceção "Fidedigna serviços da Microsoft" está ativada, os seguintes serviços (quando registado na sua subscrição) são concedidos o acesso à conta de armazenamento:

|Serviço|Nome do fornecedor de recursos|Objetivo|
|:------|:---------------------|:------|
|Azure DevTest Labs|Microsoft.DevTestLab|Instalação de Federação e de criação de imagem personalizada.  [Saiba mais](https://docs.microsoft.com/en-us/azure/devtest-lab/devtest-lab-overview).|
|Azure Event Grid|Microsoft.EventGrid|Ative a publicação de eventos de armazenamento de Blobs.  [Saiba mais](https://docs.microsoft.com/en-us/azure/event-grid/overview).|
|Azure Event Hubs|Microsoft.EventHub|Dados de arquivo com capturar os Hubs de eventos.  [Saiba mais](https://docs.microsoft.com/en-us/azure/event-hubs/event-hubs-capture-overview).|
|O Azure HDInsight|Microsoft.HDInsight|Aprovisionamento de cluster e a instalação.  [Saiba mais](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-use-blob-storage).|
|Redes do Azure|Microsoft.Networking|Armazenar e analisar registos de tráfego de rede.  [Saiba mais](https://docs.microsoft.com/en-us/azure/network-watcher/network-watcher-packet-capture-overview).|
|Azure SQL Data Warehouse|Microsoft.Sql|Importam e exportar dados.  [Saiba mais](https://docs.microsoft.com/en-us/azure/sql-data-warehouse/sql-data-warehouse-overview-load#load-from-azure-blob-storage).|
|Azure Backup|Microsoft.RecoveryServices|Cópia de segurança e restauro de discos não geridos.  [Saiba mais](https://docs.microsoft.com/en-us/azure/backup/backup-introduction-to-azure-backup).|
||||

### <a name="storage-analytics-data-access"></a>Acesso de dados de análise de armazenamento
Em alguns casos, acesso de leitura métricas e registos de diagnóstico é necessário a partir de fora do limite de rede.  Exceções para as regras de rede podem ser concedidas a permitir o acesso de leitura para armazenamento de ficheiros de registo de conta, tabelas de métricas ou ambos. [Saiba mais sobre como trabalhar com a análise de armazenamento.](/azure/storage/storage-analytics)

### <a name="managing-exceptions"></a>Gestão de exceções
Exceções de regras de rede podem ser geridas através do portal do Azure, o PowerShell ou a CLI do Azure v2.

#### <a name="azure-portal"></a>Portal do Azure
1. Navegue para a conta de armazenamento que pretende proteger.  
2. Clique no menu de definições denominado **Firewalls e redes virtuais**.
3. Certifique-se de que tiver optado por permitir acesso a partir de 'Selecionados redes'.
4. Em exceções, selecione as exceções que pretende conceder.
5. Clique em *guardar* para aplicar as suas alterações.

#### <a name="powershell"></a>PowerShell
1. Instalar a versão mais recente [Azure PowerShell](/powershell/azure/install-azurerm-ps) e [início de sessão](/powershell/azure/authenticate-azureps).
2. Apresente as exceções para as regras de rede de conta de armazenamento.
```PowerShell
(Get-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount").Bypass
```

3. Configure as exceções para as regras de rede de conta de armazenamento.
```PowerShell
Update-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount"  -Bypass AzureServices,Metrics,Logging
```

4. Remova as exceções para as regras de rede de conta de armazenamento.
```PowerShell
Update-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount"  -Bypass None
```

> [!IMPORTANT]
> Certifique-se para [definir a regra predefinida](#change-the-default-network-access-rule) para negar, ou remover exceções não tem qualquer efeito.
>

#### <a name="cliv2"></a>CLIv2
1. [Instalar a CLI do Azure 2.0](/cli/azure/install-azure-cli) e [início de sessão](/cli/azure/authenticate-azure-cli).
2. Apresente as exceções para as regras de rede de conta de armazenamento.
```azurecli
az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.bypass
```

3. Configure as exceções para as regras de rede de conta de armazenamento.
```azurecli
az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass Logging Metrics AzureServices
```

4. Remova as exceções para as regras de rede de conta de armazenamento.
```azurecli
az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass None
```

> [!IMPORTANT]
> Certifique-se para [definir a regra predefinida](#change-the-default-network-access-rule) para negar, ou remover exceções não tem qualquer efeito.
>

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre os pontos finais do serviço de rede do Azure no [pontos finais de serviço](/azure/virtual-network/virtual-network-service-endpoints-overview).

Dig mais aprofundada para segurança de armazenamento do Azure no [guia de segurança de armazenamento do Azure](storage-security-guide.md).
