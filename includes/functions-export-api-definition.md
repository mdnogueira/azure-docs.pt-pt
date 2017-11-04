## <a name="export-an-api-definition"></a>Exportar uma definição de API
Tiver uma definição de OpenAPI para a sua função de [criar uma definição de OpenAPI para uma função](../articles/azure-functions/functions-openapi-definition.md). O próximo passo neste processo é não exportar a definição da API para que PowerApps e Microsoft Flow podem utilizá-lo numa API personalizada.

> [!IMPORTANT]
> Lembre-se de que esta deve ser iniciada do Azure com as mesmas credenciais que utiliza para a sua PowerApps e Microsoft Flow inquilinos. Isto permite que o Azure para criar a API personalizada e disponibilizá-lo para PowerApps e Flow Microsoft.

1. No [portal do Azure](https://portal.azure.com), clique no nome de aplicação de função (como **demonstração de função energia**) > **funcionalidades da plataforma** > **definição da API** .

    ![Definição da API](media/functions-export-api-definition/api-definition.png)

1. Clique em **exportar para PowerApps + fluxo**.

    ![Origem de definição de API](media/functions-export-api-definition/export-api-1.png)

1. No painel da direita, utilize as definições conforme especificado na tabela.

    |Definição|Descrição|
    |--------|------------|
    |**Modo de exportação**|Selecione **Express** para gerar automaticamente a API personalizada. Selecionar **Manual** definição exporta a API, mas, em seguida, tem de o importar para PowerApps e Microsoft Flow manualmente. Para obter mais informações, consulte [exportar PowerApps e Microsoft Flow](../articles/azure-functions/app-service-export-api-to-powerapps-and-flow.md).|
    |**Ambiente**|Selecione o ambiente em que a API personalizada deve ser guardado. Para obter mais informações, consulte [descrição geral de ambientes (PowerApps)](https://powerapps.microsoft.com/tutorials/environments-overview/) ou [descrição geral de ambientes (Microsoft Flow)](https://us.flow.microsoft.com/documentation/environments-overview-admin/).|
    |**Nome da API personalizada**|Introduza um nome, como `Turbine Repair`.|
    |**Nome da chave de API**|Introduza o nome da aplicação e fluxo construtores deverá ver na IU de API personalizada. Tenha em atenção que o exemplo inclui informações úteis.|
 
    ![Exportar para as PowerApps e Microsoft Flow](media/functions-export-api-definition/export-api-2.png)

1. Clique em **OK**. A API personalizada é agora criado e adicionada ao ambiente que especificou.