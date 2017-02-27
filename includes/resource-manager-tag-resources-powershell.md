A versão 3.0 do módulo AzureRm.Resources incluía alterações significativas na forma como trabalha com etiquetas. Antes de continuar, verifique a sua versão:

```powershell
Get-Module -ListAvailable -Name AzureRm.Resources | Select Version
```

Se os resultados mostrarem a versão 3.0 ou posterior, os exemplos deste tópico funcionam no seu ambiente. Se a sua versão não é a 3.0 ou posterior, utilize a Galeria do PowerShell ou o Instalador de Plataforma Web para [atualizar a versão](/powershell/azureps-cmdlets-docs/), antes de continuar o tópico.

```powershell
Version
-------
3.5.0
```

Sempre que aplica etiquetas a um recurso ou grupo de recursos, substitui as etiquetas existentes nesse recurso ou grupo de recursos. Por conseguinte, tem de utilizar uma abordagem diferente, que tenha em conta se o recurso ou grupo de recursos tem etiquetas existentes que queira preservar. Para adicionar etiquetas a:

* um grupo de recursos sem etiquetas.

  ```powershell
  Set-AzureRmResourceGroup -Name TagTestGroup -Tag @{ Dept="IT"; Environment="Test" }
  ```

* um grupo de recursos com etiquetas.

  ```powershell
  $tags = (Get-AzureRmResourceGroup -Name TagTestGroup).Tags
  $tags += @{Status="Approved"}
  Set-AzureRmResourceGroup -Tag $tags -Name TagTestGroup
  ```

* um recurso sem etiquetas.

  ```powershell
  Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test" } -ResourceName storageexample -ResourceGroupName TagTestGroup -ResourceType Microsoft.Storage/storageAccounts
  ```

* um recurso com etiquetas.

  ```powershell
  $tags = (Get-AzureRmResource -ResourceName storageexample -ResourceGroupName TagTestGroup).Tags
  $tags += @{Status="Approved"}
  Set-AzureRmResource -Tag $tags -ResourceName storageexample -ResourceGroupName TagTestGroup -ResourceType Microsoft.Storage/storageAccounts
  ```

Para aplicar todas as etiquetas de um grupo de recursos para todos os respetivos recursos, **sem reter etiquetas existentes nos recursos**, utilize o script seguinte:

```powershell
$groups = Get-AzureRmResourceGroup
foreach ($g in $groups) 
{
    Find-AzureRmResource -ResourceGroupNameEquals $g.ResourceGroupName | ForEach-Object {Set-AzureRmResource -ResourceId $_.ResourceId -Tag $g.Tags -Force } 
}
```

Para aplicar todas as etiquetas de um grupo de recursos para todos os respetivos recursos, **retendo etiquetas existentes nos recursos que não são duplicados**, utilize o script seguinte:

```powershell
$groups = Get-AzureRmResourceGroup
foreach ($g in $groups) 
{
    if ($g.Tags -ne $null) {
        $resources = Find-AzureRmResource -ResourceGroupNameEquals $g.ResourceGroupName 
        foreach ($r in $resources)
        {
            $resourcetags = (Get-AzureRmResource -ResourceId $r.ResourceId).Tags
            foreach ($key in $g.Tags.Keys)
            {
                if ($resourcetags.ContainsKey($key)) { $resourcetags.Remove($key) }
            }
            $resourcetags += $g.Tags
            Set-AzureRmResource -Tag $resourcetags -ResourceId $r.ResourceId -Force
        }
    }
}
```

Para remover todas as etiquetas, transmita uma tabela hash vazia.

```powershell
Set-AzureRmResourceGroup -Tag @{} -Name TagTestGgroup
```

Para obter os grupos de recursos com uma etiqueta específica, utilize o cmdlet `Find-AzureRmResourceGroup`.

```powershell
(Find-AzureRmResourceGroup -Tag @{ Dept="Finance" }).Name 
```

Para obter todos os recursos com uma etiqueta e um valor específicos, utilize o cmdlet `Find-AzureRmResource`.

```powershell
(Find-AzureRmResource -TagName Dept -TagValue Finance).Name
```



<!--HONumber=Feb17_HO3-->


