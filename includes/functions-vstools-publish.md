1. No **Explorador de Soluções**, clique com o botão direito do rato no projeto e selecione **Publicar**. Escolha **Criar Novo** e, em seguida, clique em **Publicar**. 

    ![Publicar cria uma nova aplicação de funções](./media/functions-vstools-publish/functions-vstools-publish-new-function-app.png)

2. Se ainda não tiver associado o Visual Studio à sua conta do Azure, clique em **Adicionar uma conta...**.  

3. Na caixa de diálogo **Criar Serviço de Aplicações**, utilize as definições do **Sistema Anfitrião** conforme especificado na tabela seguinte: 

    ![Tempo de execução local do Azure](./media/functions-vstools-publish/functions-vstools-publish.png)

    | Definição      | Valor sugerido  | Descrição                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome da Aplicação** | Nome globalmente exclusivo | Nome que identifica exclusivamente a sua nova aplicação de funções. |
    | **Subscrição** | Escolher a sua subscrição | A subscrição do Azure que deve utilizar. |
    | **[Grupo de Recursos](../articles/azure-resource-manager/resource-group-overview.md)** | myResourceGroup |  Nome do grupo de recursos no qual a sua aplicação de funções será criada. |
    | **[Plano do Serviço de Aplicações](../articles/azure-functions/functions-scale.md)** | Plano de consumo | Confirme que escolhe o **Consumo** em **Tamanho** quando criar um novo plano.  |
    | **[Conta de armazenamento](../articles/storage/storage-create-storage-account.md#create-a-storage-account)** | Nome globalmente exclusivo | Utilize uma conta de armazenamento existente ou crie uma nova.   |

4. Clique em **Criar** para criar uma aplicação de funções no Azure com essas definições. Quando o aprovisionamento estiver concluído, aponte o valor **URL do Site**, que é o endereço da sua aplicação de função no Azure. 

    ![Tempo de execução local do Azure](./media/functions-vstools-publish/functions-vstools-publish-profile.png)
