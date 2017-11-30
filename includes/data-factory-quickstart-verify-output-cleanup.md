## <a name="verify-the-output"></a>Verificar a saída
O pipeline cria automaticamente a pasta de saída no contentor de blobs adftutorial. Em seguida, copia o ficheiro emp.txt da pasta de entrada para a pasta de saída. 

1. No portal do Azure, na página do contentor **adftutorial**, clique em **Atualizar** para ver a pasta de saída. 
    
    ![Atualizar](media/data-factory-quickstart-verify-output-cleanup/output-refresh.png)
2. Clique em **saída** na lista de pastas. 
2. Confirme se o ficheiro **emp.txt** foi copiado para a pasta de saída. 

    ![Atualizar](media/data-factory-quickstart-verify-output-cleanup/output-file.png)

## <a name="clean-up-resources"></a>Limpar recursos
Pode limpar os recursos que criou no Guia de Introdução de duas formas. Pode eliminar o [grupo de recursos do Azure](../articles/azure-resource-manager/resource-group-overview.md), que inclui todos os recursos no grupo de recursos. Se quiser manter os outros recursos intactos, elimine apenas a fábrica de dados que criou neste tutorial.

A eliminação de um grupo de recursos dita a eliminação de todos os recursos, incluindo as fábricas de dados nele incluídas. Execute o seguinte comando para eliminar todo o grupo de recursos: 
```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourcegroupname
```

Se quiser eliminar apenas a fábrica de dados, e não o grupo de recursos completo, execute o seguinte comando: 

```powershell
Remove-AzureRmDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```