<properties 
   pageTitle="Conhecer o Data Lake Analytics e U-SQL através dos tutoriais interativos do Portal do Azure | Azure" 
   description="Início rápido com aprendizagem do Data Lake Analytics e U-SQL. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="02/11/2016"
   ms.author="edmaca"/>


# Utilizar tutoriais interativos do Azure Data Lake Analytics

O Portal do Azure fornece um tutorial interativo para começar a utilizar o Data Lake Analytics . Este artigo mostra como seguir o tutorial para analisar os registos de sites.


>[AZURE.NOTE] Se pretender percorrer o mesmo tutorial com o Visual Studio, veja [Analisar os registos de sites com o Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
>Serão adicionados tutoriais mais interativos ao portal.


Para outros tutoriais, veja:

- [Introdução ao Data Lake Analytics com o Portal do Azure](data-lake-analytics-get-started-portal.md)
- [Introdução ao Data Lake Analytics com o Azure PowerShell](data-lake-analytics-get-started-powershell.md)
- [Introdução ao Data Lake Analytics com o SDK .NET](data-lake-analytics-get-started-net-sdk.md)
- [Desenvolver scripts U-SQL com as Ferramentas do Data Lake para Visual Studio | Azure](data-lake-analytics-data-lake-tools-get-started.md) 

**Pré-requisitos**

Antes de começar este tutorial, tem de ter o seguinte:

- **Conta do Data Lake Analytics**.  Veja [Introdução à Análise do Azure Data Lake com o Portal do Azure](data-lake-analytics-get-started-portal.md).

##Criar conta do Data Lake Analytics  

Tem de ter uma conta do Data Lake Analytics antes de poder executar quaisquer tarefas.

Cada conta do Data Lake Analytics tem uma dependência de conta do [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md).  Esta conta é referida como a conta predefinida do Data Lake Store.  Pode criar a conta do Data Lake Store previamente ou quando criar a conta do Data Lake Analytics . Neste tutorial, irá criar a conta do Data Lake Store com a conta do Data Lake Analytics 

**Para criar uma conta do Data Lake Analytics **

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/signin/index/?Microsoft_Azure_Kona=true&Microsoft_Azure_DataLake=true&hubsExtension_ItemHideKey=AzureDataLake_BigStorage%2cAzureKona_BigCompute).
2. Clique em **Microsoft Azure** no canto superior esquerdo para abrir o StartBoard.
3. Clique no mosaico **Mercado**.  
3. Escreva **Análise do Azure Data Lake** in na caixa de pesquisa no painel **Tudo** e prima **ENTER**. Deverá ver **Azure Data Lake Analytics** na lista.
4. Clique em **Azure Data Lake Analytics** na lista.
5. Clique em **Criar** na parte inferior do painel.
6. Escreva ou selecione o seguinte:

    ![Painel do portal da Análise do Azure Data Lake](./media/data-lake-analytics-get-started-portal/data-lake-analytics-portal-create-adla.png)

    - **Nome**: atribua um nome à conta da Análise.
    - **Data Lake Store**: cada conta do Data Lake Analytics tem uma conta do Data Lake Store dependente. A conta do Data Lake Analytics e a conta do Data Lake Store dependente têm de estar localizadas no centro de dados do Azure. Siga as instruções para criar uma nova conta do Data Lake Store ou selecione uma conta existente.
    - **Subscrição**: selecione a subscrição do Azure utilizada para a conta da Análise.
    - **Grupo de Recursos**. Selecione um Grupo de Recursos do Azure existente ou crie um novo. As aplicações são geralmente constituídas por diversos componentes, por exemplo, uma aplicação Web, base de dados, servidor de base de dados, armazenamento e serviços de terceiros. O Azure Resource Manager (ARM) permite trabalhar com os recursos na sua aplicação como um grupo, referido como um Grupo de Recursos do Azure. Pode implementar, atualizar, monitorizar ou eliminar todos os recursos da sua aplicação numa operação única e coordenada. Utiliza um modelo para a implementação e esse modelo pode funcionar para ambientes diferentes, como de teste e produção. Pode clarificar a faturação da sua organização ao visualizar os custos agregados do grupo inteiro. Para obter mais informações, veja [Descrição geral do Azure Resource Manager](resource-group-overview.md). 
    - **Localização**. Selecione um centro de dados do Azure para a conta do Data Lake Analytics. 
7. Selecione **Afixar ao StartBoard**. Esta ação é necessária para seguir este tutorial.
8. Clique em **Criar**. Esta ação leva-o para o StartBoard do portal. Um novo mosaico é adicionado à home page com a etiqueta que indica "A implementar a Análise do Azure Data Lake". A criação de uma conta da Data Lake Analytics demora alguns instantes. Quando a conta estiver criada, o portal abre a conta num novo painel.

    ![Painel do portal da Análise do Azure Data Lake](./media/data-lake-analytics-get-started-portal/data-lake-analytics-portal-blade.png)

##Executar o tutorial interativo da Website Log Analytics

**Para abrir o tutorial interativo do Website Log Analytics**

1. A partir do Portal, clique em **Microsoft Azure** no menu à esquerda para abrir o StartBoard.
2. Clique no mosaico associado à sua conta da Data Lake Analytics .
3. Clique em **Explorar tutoriais interativos** a partir da barra **Essentials**.

    ![Tutoriais interativos da Análise do Azure Data Lake](./media/data-lake-analytics-use-interactive-tutorials/data-lake-analytics-explore-interactive-tutorials.png)

4. Se vir um aviso laranja a indicar "Exemplos não configurados, clique em …", clique em **Copiar Dados de Exemplo** para copiar os dados de exemplo para a conta do Data Lake Store predefinida. O tutorial interativo precisa dos dados para ser executado.
5. A partir do painel **Tutoriais Interativos**, clique em **Website Log Analytics**. O portal abre o tutorial num novo painel do portal.
5. Clique em **1 Introdução** e, em seguida, siga as instruções

##Veja também

- [Descrição geral do Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
- [Introdução ao Data Lake Analytics com o Portal do Azure](data-lake-analytics-get-started-portal.md)
- [Introdução ao Data Lake Analytics com o Azure PowerShell](data-lake-analytics-get-started-powershell.md)
- [Desenvolver scripts U-SQL com as Ferramentas do Data Lake para Visual Studio | Azure](data-lake-analytics-data-lake-tools-get-started.md)
- [Analisar registos de sites com o Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md)



<!--HONumber=Jun16_HO2-->


