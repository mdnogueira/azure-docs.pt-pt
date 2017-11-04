## <a name="set-up-azure-powershell-for-azure-dns"></a>Configurar o Azure PowerShell para o DNS do Azure

### <a name="before-you-begin"></a>Antes de começar

Antes de iniciar a configuração, verifique se tem os seguintes itens.

* Uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, pode ativar os [Benefícios de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se numa [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Tem de instalar a versão mais recente dos cmdlets do PowerShell do Azure Resource Manager. Para obter mais informações, veja [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azureps-cmdlets-docs).

### <a name="sign-in-to-your-azure-account"></a>Inicie sessão na sua conta do Azure

Abra a consola do PowerShell e ligue-se à sua conta. Para obter mais informações, consulte [utilizando o PowerShell com o Resource Manager](../articles/azure-resource-manager/powershell-azure-resource-manager.md).

```powershell
Login-AzureRmAccount
```

### <a name="select-the-subscription"></a>Selecionar a subscrição
 
Verifique as subscrições da conta.

```powershell
Get-AzureRmSubscription
```

Escolha qual das suas subscrições do Azure utilizar.

```powershell
Select-AzureRmSubscription -SubscriptionName "your_subscription_name"
```

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

O Azure Resource Manager requer que todos os grupos de recursos especifiquem uma localização, que é utilizada como a localização predefinida para os recursos nesse grupo de recursos. No entanto, uma vez que todos os recursos DNS são globais, não regionais, a opção de localização do grupo de recursos não tem impacto no DNS do Azure.

Pode ignorar este passo se estiver a utilizar um grupo de recursos existente.

```powershell
New-AzureRmResourceGroup -Name MyAzureResourceGroup -location "West US"
```

### <a name="register-resource-provider"></a>Registar o fornecedor de recursos

O serviço do DNS do Azure é gerido pelo fornecedor de recursos Microsoft.Network. A subscrição do Azure tem de estar registada para utilizar este fornecedor de recursos antes de poder utilizar o DNS do Azure. Esta é uma operação única para cada subscrição.

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
```