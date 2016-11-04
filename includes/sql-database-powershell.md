
## Iniciar a sessão do PowerShell
Primeiro, tem de ter a versão mais recente do [Azure PowerShell](https://msdn.microsoft.com/library/mt619274.aspx) instalada e em execução. Para obter informações detalhadas, veja [Como instalar e configurar o Azure PowerShell](../articles/powershell-install-configure.md).

> [!NOTE]
> Muitas funcionalidades novas da Base de Dados SQL só são suportadas quando utilizar o [Modelo de implementação Azure Resource Manager](../articles/resource-group-overview.md). Assim, os exemplos utilizam os [Cmdlets PowerShell da Base de Dados do Azure](https://msdn.microsoft.com/library/azure/mt574084.aspx) para o Resource Manager. Os [Cmdlets da SQL Database do Azure (clássica)](https://msdn.microsoft.com/library/azure/dn546723.aspx) do modelo de implementação clássica existentes são suportados para compatibilidade com versões anteriores, mas recomendamos que utilize os cmdlets Resource Manager.
> 
> 

Execute o cmdlet [**Add-AzureRmAccount**](https://msdn.microsoft.com/library/mt619267.aspx) para visualizar um ecrã de início de sessão para introduzir as suas credenciais. Utilize as mesmas credenciais que utiliza para iniciar sessão no Portal do Azure.

    Add-AzureRmAccount

Se tiver várias subscrições, utilize o cmdlet [**Set-AzureRmContext**](https://msdn.microsoft.com/library/mt619263.aspx) para selecionar a subscrição que a sessão do PowerShell deve utilizar. Para ver que subscrição a sessão atual do PowerShell está a utilizar, execute [**Get-AzureRmContext**](https://msdn.microsoft.com/library/mt619265.aspx). Para ver todas as subscrições, execute [**Get-AzureRmSubscription**](https://msdn.microsoft.com/library/mt619284.aspx).

    Set-AzureRmContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'


<!--HONumber=Sep16_HO3-->


