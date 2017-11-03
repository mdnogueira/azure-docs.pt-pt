## <a name="clean-up-deployment"></a>Limpar a implementação 

Depois de executar o script de exemplo, o comando de siga pode ser utilizado para remover o grupo de recursos, a instância da Cache de Redis do Azure e a quaisquer recursos relacionados no grupo de recursos.

```azurecli
az group delete --name contosoGroup
```