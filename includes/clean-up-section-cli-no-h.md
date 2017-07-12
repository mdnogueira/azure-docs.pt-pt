Nos passos anteriores, criou os recursos do Azure num grupo de recursos. Se provavelmente não necessitar desses recursos no futuro, pode eliminá-los ao eliminar o grupo de recursos.
 
1. Execute o seguinte comando para verificar se o grupo de recursos não contém quaisquer recursos que pretenda guardar:

  ```azurecli
  az group show --name myResourceGroup
  ```

2. Se os recursos apresentados forem todos os que pretende eliminar, execute o seguinte comando:
 
  ```azurecli
  az group delete --name myResourceGroup
  ```
