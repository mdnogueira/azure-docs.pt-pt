
## <a name="start-your-powershell-session"></a>Iniciar a sessão do PowerShell
Primeiro, deve ter a versão mais recente do Azure PowerShell instalada e em execução. Para informações detalhadas, consulte [Como instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs).

> [!NOTE]
> Muitas funcionalidades novas da Base de Dados SQL só são suportadas quando utilizar o [Modelo de implementação Azure Resource Manager](../articles/azure-resource-manager/resource-group-overview.md). Assim, os exemplos utilizam os [Cmdlets PowerShell da Base de Dados do Azure](https://msdn.microsoft.com/library/azure/mt574084\(v=azure.300\).aspx) para o Resource Manager. Os [Cmdlets de Gestão de Serviço da Base de Dados SQL do Azure](https://msdn.microsoft.com/library/azure/dn546723\(v=azure.300\).aspx) do modelo de implementação (clássico) de gestão de serviço são suportados para compatibilidade com versões anteriores, mas recomendamos que utilize os cmdlets Resource Manager.
> 
> 

Execute o cmdlet [**Add-AzureRmAccount**](https://msdn.microsoft.com/library/azure/mt619267\(v=azure.300\).aspx) para visualizar um ecrã de início de sessão para introduzir as suas credenciais. Utilize as mesmas credenciais que utiliza para iniciar sessão no Portal do Azure.

    Add-AzureRmAccount

Se tiver várias subscrições, utilize o cmdlet [**Set-AzureRmContext**](https://msdn.microsoft.com/library/azure/mt619263\(v=azure.300\).aspx) para selecionar a subscrição que a sessão do PowerShell deve utilizar. Para ver que subscrição a sessão atual do PowerShell está a utilizar, execute [**Get-AzureRmContext**](https://msdn.microsoft.com/library/azure/mt619265\(v=azure.300\).aspx). Para ver todas as subscrições, execute [**Get-AzureRmSubscription**](https://msdn.microsoft.com/library/azure/mt619284\(v=azure.300\).aspx).

    Set-AzureRmContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'


<!--HONumber=Dec16_HO5-->


