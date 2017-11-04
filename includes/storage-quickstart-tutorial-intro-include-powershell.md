## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão na sua subscrição do Azure com o comando `Login-AzureRmAccount` e siga as instruções no ecrã.

```powershell
Login-AzureRmAccount
```

Se não souber qual localização que pretende utilizar, pode listar as localizações disponíveis. Depois de é apresentada a lista, localize aquela que pretende utilizar. Neste exemplo, vai utilizar **eastus**. Armazene-o numa variável e utilize a variável, pelo que pode alterá-la num único local.

```powershell
Get-AzureRmLocation | select Location 
$location = "eastus"
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos do Azure com [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos. 

```powershell
$resourceGroup = "myResourceGroup"
New-AzureRmResourceGroup -Name $resourceGroup -Location $location 
```

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Criar uma conta de armazenamento para fins gerais standard com através de replicação do LRS [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount), em seguida, obter o contexto da conta de armazenamento que define a conta de armazenamento a ser utilizado. Quando a atuar numa conta de armazenamento, referenciar o contexto em vez de repetidamente fornecer as credenciais. Este exemplo cria uma conta de armazenamento denominada *mystorageaccount* com a encriptação de blob e de armazenamento localmente redundante ativada.

```powershell
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name "mystorageaccount" `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage `
  -EnableEncryptionService Blob

$ctx = $storageAccount.Context
```
