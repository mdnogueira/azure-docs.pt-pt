### <a name="prerequisites"></a>Pré-requisitos
* Uma conta do Azure; Pode criar um [conta gratuita](https://azure.microsoft.com/free)
* Um [conta do Blob Storage do Azure](../articles/storage/common/storage-create-storage-account.md) , incluindo o nome da conta de armazenamento e a sua chave de acesso. Esta informação é listada nas propriedades da conta do storage no portal do Azure. Leia mais sobre [Storage do Azure](../articles/storage/common/storage-introduction.md).

Antes de utilizar a sua conta do Blob Storage do Azure numa aplicação lógica, ligar à sua conta do Blob Storage do Azure. Pode fazê-facilmente na sua aplicação lógica no portal do Azure.  

Ligar à sua conta do Blob Storage do Azure utilizando os seguintes passos:  

1. Crie uma aplicação lógica. No designer Logic Apps, adicione um acionador e, em seguida, adicionar uma ação. Selecione **Mostrar Microsoft APIs geridas** na lista pendente lista e, em seguida, introduza "blob" na caixa de pesquisa. Selecione uma das ações:  
   
    ![Passo de criação de ligação de armazenamento de Blobs do Azure](./media/connectors-create-api-azureblobstorage/azureblobstorage-1.png)  
2. Se ainda não criou anteriormente todas as ligações ao storage do Azure, é-lhe pedida os detalhes da ligação:   
   
    ![Passo de criação de ligação de armazenamento de Blobs do Azure](./media/connectors-create-api-azureblobstorage/connection-details.png)  
3. Introduza os detalhes da conta de armazenamento. Propriedades com um asterisco são necessárias.
   
   | Propriedade | Detalhes |
   | --- | --- |
   | Nome da ligação * |Introduza um nome para a sua ligação. |
   | Nome da conta de armazenamento do Azure * |Introduza o nome de conta de armazenamento. O nome da conta de armazenamento é apresentado nas propriedades de armazenamento no portal do Azure. |
   | Chave de acesso de conta do Storage do Azure * |Introduza a chave de conta de armazenamento. As chaves de acesso são apresentadas nas propriedades de armazenamento no portal do Azure. |
   
    Estas credenciais são utilizadas para autorizar a aplicação lógica para estabelecer ligação e aceder aos seus dados. 
4. Selecione **Criar**.
5. Tenha em atenção de que a ligação foi criada. Agora, continue com os outros passos na sua aplicação lógica: 
   
    ![Passo de criação de ligação de armazenamento de Blobs do Azure](./media/connectors-create-api-azureblobstorage/azureblobstorage-3.png)  

