Os exemplos neste artigo requerem versão 3.0 ou posterior do Azure PowerShell. Se não tiver versão 3.0 ou posterior, [atualizar a versão](/powershell/azureps-cmdlets-docs/) utilizando galeria do PowerShell ou instalador de plataforma Web.

Para ver as etiquetas existentes de um *grupo de recursos*, utilize:

```powershell
(Get-AzureRmResourceGroup -Name examplegroup).Tags
```

Este script devolve o formato seguinte:

```powershell
Name                           Value
----                           -----
Dept                           IT
Environment                    Test
```

Para ver as etiquetas existentes de um *recurso que tem um ID de recurso específico*, utilize:

```powershell
(Get-AzureRmResource -ResourceId {resource-id}).Tags
```

Ou para ver as etiquetas existentes de um *recurso que tem um nome e grupo de recurso específicos*, utilize:

```powershell
(Get-AzureRmResource -ResourceName examplevnet -ResourceGroupName examplegroup).Tags
```

Para obter *grupos de recursos com uma etiqueta específica*, utilize:

```powershell
(Find-AzureRmResourceGroup -Tag @{ Dept="Finance" }).Name
```

Para obter *recursos com uma etiqueta específica*, utilize:

```powershell
(Find-AzureRmResource -TagName Dept -TagValue Finance).Name
```

Sempre que aplicar etiquetas a um recurso ou grupo de recursos, as etiquetas existentes nesse recurso ou grupo de recursos são substituídas. Por conseguinte, tem de utilizar uma abordagem diferente, que tenha em conta se o recurso ou grupo de recursos tem etiquetas existentes.

Para adicionar etiquetas a um *grupo de recursos que não tenha etiquetas*, utilize:

```powershell
Set-AzureRmResourceGroup -Name examplegroup -Tag @{ Dept="IT"; Environment="Test" }
```

Para adicionar etiquetas a um *grupo de recursos que tenha etiquetas existentes*, obtenha-as, adicione a etiqueta nova e volte a aplicar as etiquetas:

```powershell
$tags = (Get-AzureRmResourceGroup -Name examplegroup).Tags
$tags += @{Status="Approved"}
Set-AzureRmResourceGroup -Tag $tags -Name examplegroup
```

Para adicionar etiquetas a um *recurso que não tenha etiquetas*, utilize:

```powershell
$r = Get-AzureRmResource -ResourceName examplevnet -ResourceGroupName examplegroup
Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test" } -ResourceId $r.ResourceId -Force
```

Para adicionar etiquetas a um *recurso que tenha etiquetas*, utilize:

```powershell
$r = Get-AzureRmResource -ResourceName examplevnet -ResourceGroupName examplegroup
$r.tags += @{Status="Approved"}
Set-AzureRmResource -Tag $r.Tags -ResourceId $r.ResourceId -Force
```

Para aplicar todas as etiquetas de um grupo de recursos para todos os respetivos recursos, *sem reter etiquetas existentes nos recursos*, utilize o script seguinte:

```powershell
$groups = Get-AzureRmResourceGroup
foreach ($g in $groups)
{
    Find-AzureRmResource -ResourceGroupNameEquals $g.ResourceGroupName | ForEach-Object {Set-AzureRmResource -ResourceId $_.ResourceId -Tag $g.Tags -Force }
}
```

Para aplicar todas as etiquetas de um grupo de recursos para todos os respetivos recursos, *retendo etiquetas existentes nos recursos que não são duplicados*, utilize o script seguinte:

```powershell
$group = Get-AzureRmResourceGroup "examplegroup"
if ($group.Tags -ne $null) {
    $resources = $group | Find-AzureRmResource
    foreach ($r in $resources)
    {
        $resourcetags = (Get-AzureRmResource -ResourceId $r.ResourceId).Tags
        foreach ($key in $group.Tags.Keys)
        {
            if (($resourcetags) -AND ($resourcetags.ContainsKey($key))) { $resourcetags.Remove($key) }
        }
        $resourcetags += $group.Tags
        Set-AzureRmResource -Tag $resourcetags -ResourceId $r.ResourceId -Force
    }
}
```

Para remover todas as etiquetas, transmita uma tabela hash vazia:

```powershell
Set-AzureRmResourceGroup -Tag @{} -Name examplegroup
```
