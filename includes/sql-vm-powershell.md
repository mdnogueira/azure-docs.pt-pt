
## <a name="start-your-powershell-session"></a>Iniciar a sessão do PowerShell
Primeiro tem de ter a versão mais recente [Azure PowerShell](http://msdn.microsoft.com/library/mt619274.aspx) instalado e em execução. Para informações detalhadas, consulte [Como instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs).

> [!NOTE]
> Os exemplos de utilização deste tópico [modelo de implementação Azure Resource Manager](../articles/azure-resource-manager/resource-group-overview.md)assim, os exemplos utilizam o [cmdlets do Azure Resource Manager](http://msdn.microsoft.com/library/azure/mt125356.aspx). 
> 
> 

Execute o [ **Add-AzureRmAccount** ](http://msdn.microsoft.com/library/mt619267.aspx) cmdlet e será apresentada com um início de sessão no ecrã para introduzir as suas credenciais. Utilize as mesmas credenciais que utiliza para iniciar sessão no Portal do Azure.

    Add-AzureRmAccount

Se tiver várias subscrições, utilize o [ **Set-AzureRmContext** ](http://msdn.microsoft.com/library/mt619263.aspx) cmdlet para selecionar a subscrição a sessão do PowerShell deve utilizar. Para ver que subscrição a sessão atual do PowerShell está a utilizar, execute [**Get-AzureRmContext**](http://msdn.microsoft.com/library/mt619265.aspx). Para ver todas as subscrições, execute [**Get-AzureRmSubscription**](http://msdn.microsoft.com/library/mt619284.aspx).

    Set-AzureRmContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'

