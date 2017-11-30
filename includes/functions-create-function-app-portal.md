1. Clique em de **novo** botão encontrado no canto superior esquerdo do portal do Azure, em seguida, selecione **computação** > **aplicação de função**. 

    ![Criar uma aplicação de função no portal do Azure](./media/functions-create-function-app-portal/function-app-create-flow.png)

2. Utilize as definições de aplicação de função especificado na tabela abaixo a imagem.

    ![Definir novas definições de aplicação de função](./media/functions-create-function-app-portal/function-app-create-flow2.png)

    | Definição      | Valor sugerido  | Descrição                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome da aplicação**  | Nome globalmente exclusivo | Nome que identifica a sua aplicação Function App nova. Carateres válidos são `a-z`, `0-9`, e `-`.  | 
    | **Subscrição** | A sua subscrição | A subscrição sob a qual esta nova aplicação de função será criada. | 
    | **[Grupo de Recursos](../articles/azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | Nome do grupo de recursos novo no qual a aplicação Function App vai ser criada. | 
    | **SO** | Windows | Sem servidor de alojamento está atualmente disponível apenas quando em execução no Windows. Para o alojamento de Linux, consulte [criar a sua primeira função em execução no Linux utilizando a CLI do Azure](../articles/azure-functions/functions-create-first-azure-function-azure-cli-linux.md). |
    | **[Plano de alojamento](../articles/azure-functions/functions-scale.md)** |   Plano de consumo | O plano de alojamento que define a forma como os recursos são alocados à sua aplicação Function App. No **Plano de Consumo** predefinido, os recursos são adicionados dinamicamente, conforme necessário por parte das suas funções. Neste [sem servidor](https://azure.microsoft.com/overview/serverless-computing/) de alojamento, apenas paga a hora de executam as suas funções.   |
    | **Localização** | Europa Ocidental | Escolha uma localização perto de si ou de outros serviços aos quais as suas funções vão aceder. |
    | **[Conta de armazenamento](../articles/storage/common/storage-create-storage-account.md#create-a-storage-account)** |  Nome globalmente exclusivo |  O nome da conta de armazenamento nova utilizada pela sua aplicação Function App. Os nomes das contas do Storage devem ter entre 3 e 24 carateres de comprimento e apenas podem conter números e letras minúsculas. Também pode utilizar uma conta já existente. |

1. Clique em **Criar** para aprovisionar e implementar a nova aplicação de função.
