## <a name="deploy-template-from-cloud-shell"></a>Implementar o modelo a partir do Cloud Shell

Pode utilizar o [Cloud Shell](../articles/cloud-shell/overview.md) para implementar o modelo. No entanto, primeiro tem de carregar o modelo na partilha de ficheiros do Cloud Shell. Se não utilizou o Cloud Shell, consulte a [Descrição Geral do Azure Cloud Shell](../articles/cloud-shell/overview.md) para obter informações sobre como o configurar.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

1. Selecione o grupo de recursos do Cloud Shell. O padrão de nome é `cloud-shell-storage-<region>`.

   ![Selecionar grupo de recursos](./media/resource-manager-cloud-shell-deploy/select-cs-resource-group.png)

1. Selecione a conta de armazenamento para o Cloud Shell.

   ![Selecionar a conta de armazenamento](./media/resource-manager-cloud-shell-deploy/select-storage.png)

1. Selecione **Ficheiros**.

   ![Selecionar ficheiros](./media/resource-manager-cloud-shell-deploy/select-files.png)

1. Selecione a partilha de ficheiros para o Cloud Shell. O padrão de nome é `cs-<user>-<domain>-com-<uniqueGuid>`.

   ![Selecionar a partilha de ficheiros](./media/resource-manager-cloud-shell-deploy/select-file-share.png)

1. Selecione **Adicionar diretório**.

   ![Adicionar diretório](./media/resource-manager-cloud-shell-deploy/select-add-directory.png)

1. Atribua-lhe o nome **templates** e selecione **OK**.

   ![Atribuir um nome ao diretório](./media/resource-manager-cloud-shell-deploy/name-templates.png)

1. Selecione o novo diretório.

   ![Selecionar o diretório](./media/resource-manager-cloud-shell-deploy/select-templates.png)

1. Selecione **Upload**.

   ![Selecionar a operação carregar](./media/resource-manager-cloud-shell-deploy/select-upload.png)

1. Localize e carregue o modelo.

   ![Carregar o ficheiro](./media/resource-manager-cloud-shell-deploy/upload-files.png)

1. Abra o prompt.

   ![Abrir o Cloud Shell](./media/resource-manager-cloud-shell-deploy/start-cloud-shell.png)
