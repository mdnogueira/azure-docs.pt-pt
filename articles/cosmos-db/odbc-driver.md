---
title: "Ligue à BD do Cosmos do Azure utilizando as ferramentas de análise de BI | Microsoft Docs"
description: "Saiba como utilizar o controlador ODBC de BD do Cosmos do Azure para criar tabelas e vistas que normalizado dados podem ser visualizados no software de análise do BI e os dados."
keywords: ODBC, o controlador odbc
services: cosmos-db
author: mimig1
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 9967f4e5-4b71-4cd7-8324-221a8c789e6b
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: rest-api
ms.topic: article
ms.date: 05/24/2017
ms.author: mimig
ms.openlocfilehash: 2df792c00b7a789dbefa64bfe0245f1ad73c3faa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="connect-to-azure-cosmos-db-using-bi-analytics-tools-with-the-odbc-driver"></a>Ligue à BD do Cosmos do Azure utilizando as ferramentas de análise de BI com o controlador ODBC

O controlador ODBC de BD do Cosmos Azure permite-lhe ligar à base de dados do Cosmos do Azure utilizando as ferramentas de análise de BI como o SQL Server Integration Services, o Power BI Desktop e o Tableau, para que possa analisar e criar visualizações de dados da sua base de dados do Azure Cosmos dessas soluções.

O controlador ODBC de BD do Azure Cosmos é ODBC 3.8 em conformidade e suporta ANSI SQL-92 sintaxe. O controlador oferece funcionalidades avançadas para o ajudar a renormalize dados na base de dados do Azure Cosmos. Utilizar o controlador, pode representar dados na base de dados do Azure Cosmos como tabelas e vistas. O controlador permite-lhe efetuar operações de SQL contra as tabelas e vistas de grupo, incluindo por consultas, insere, atualizações e elimina.

## <a name="why-do-i-need-to-normalize-my-data"></a>Por que motivo é necessário normalizar os meus dados?
BD do Cosmos do Azure é uma base de dados sem esquemas, pelo que permite um desenvolvimento rápido de aplicações, permitindo aplicações iterar o respetivo modelo de dados no momento e não confine-los para um esquema strict. Uma única base de dados de base de dados do Azure Cosmos pode conter documentos JSON de várias estruturas. Esta é uma grande para o desenvolvimento da aplicação rápida, mas quando pretende analisar e criar relatórios de dados utilizando ferramentas de BI e de análise de dados, os dados, muitas vezes, tem de ser simplificado e segue um esquema específico.

Este é onde o controlador ODBC é apresentada no. Ao utilizar o controlador ODBC, pode agora renormalized dados na base de dados do Azure Cosmos em tabelas e vistas de ajuste para as suas necessidades de analíticas e de relatórios de dados. Os esquemas renormalized não tem nenhum impacto nos dados subjacentes e não confine aos programadores-las cumprir, basta permitem-lhe tirar partido das ferramentas compatível com ODBC para aceder aos dados. Agora, por isso, a base de dados de base de dados do Azure Cosmos não só será um favorito para a sua equipa de desenvolvimento, mas os analistas de dados serão o adoram demasiado.

Permite agora começar com o controlador ODBC.

## <a id="install"></a>Passo 1: Instalar o controlador ODBC do Azure Cosmos DB

1. Transfira os controladores para o seu ambiente:

    * [Microsoft Azure Cosmos DB ODBC 64-bit.msi](https://aka.ms/documentdb-odbc-64x64) para Windows de 64 bits
    * [Microsoft Azure Cosmos DB ODBC 32 x 64-bit.msi](https://aka.ms/documentdb-odbc-32x64) de 32 bits em Windows de 64 bits
    * [Microsoft Azure Cosmos DB ODBC 32-bit.msi](https://aka.ms/documentdb-odbc-32x32) para Windows de 32 bits

    Execute o ficheiro msi localmente, que inicia o **instalação de controlador Assistente do Microsoft Azure Cosmos DB ODBC**. 
2. Conclua o Assistente de instalação utilizando a predefinição de entrada para instalar o controlador ODBC.
3. Abra o **administrador da origem de dados de ODBC** aplicação no seu computador, pode fazer isto escrevendo **origens de dados de ODBC** nas janelas de caixa de pesquisa. 
    Pode confirmar o controlador foi instalado, clicando a **controladores** separador e assegurando **o controlador de ODBC do Microsoft Azure Cosmos DB** está listado.

    ![Administrador de origem de dados ODBC do Azure Cosmos DB](./media/odbc-driver/odbc-driver.png)

## <a id="connect"></a>Passo 2: Ligar à base de dados do Azure Cosmos DB

1. Depois de [instalar o controlador ODBC de BD do Azure Cosmos](#install), no **administrador de origem de dados de ODBC** janela, clique em **adicionar**. Pode criar um utilizador ou sistema DSN. Neste exemplo, estamos a criar um DSN de utilizador.
2. No **criar uma nova origem de dados** janela, selecione **o controlador de ODBC do Microsoft Azure Cosmos DB**e, em seguida, clique em **concluir**.
3. No **configuração SDN de controladores ODBC Azure Cosmos DB** janela, preencha o seguinte: 

    ![Janela de programa de configuração do Cosmos DB ODBC controlador DSN do Azure](./media/odbc-driver/odbc-driver-dsn-setup.png)
    - **Nome da origem de dados**: O seus próprios nome amigável para o DSN de ODBC. Este nome é exclusivo para a sua conta de base de dados do Azure Cosmos, por isso, nome adequadamente se tiver várias contas.
    - **Descrição**: uma breve descrição da origem de dados.
    - **Anfitrião**: URI para a sua conta de base de dados do Azure Cosmos. Pode obter esta no painel chaves de BD do Cosmos do Azure no portal do Azure, conforme mostrado na captura de ecrã seguinte. 
    - **Aceder à chave**: A chave primária ou secundária, leitura e escrita ou só de leitura do painel chaves de BD do Cosmos do Azure no portal do Azure, como mostrado na captura de ecrã seguinte. Recomendamos que utilize a chave apenas de leitura se for utilizado o DSN para processamento de dados só de leitura e de relatórios.
    ![Painel de chaves de BD do Cosmos do Azure](./media/odbc-driver/odbc-driver-keys.png)
    - **Encriptar a chave de acesso de**: selecione a melhor opção com base nos utilizadores desta máquina. 
4. Clique em de **teste** botão para se certificar de que pode ligar à sua conta de base de dados do Azure Cosmos. 
5. Clique em **opções avançadas** e defina os seguintes valores:
    - **Consultar consistência**: selecione a [nível de consistência](consistency-levels.md) para as operações. A predefinição é de sessão.
    - **Número de tentativas**: introduza o número de vezes para tentar uma operação se o pedido inicial não for concluída devido à limitação do serviço.
    - **Ficheiro de esquema**: tiver um número de opções aqui.
        - Por predefinição, deixar esta entrada é (em branco), o controlador analisa os dados de página primeiro para todas as coleções determinar o esquema de cada coleção. Isto é conhecido como mapeamento de coleção. Sem um ficheiro de esquema definido, o controlador tem de executar a análise para cada sessão de controladores e pode resultar num início de tempo de uma aplicação utilizando o DSN superior. Recomendamos que associa sempre um ficheiro de esquema para um DSN.
        - Se já tiver um ficheiro de esquema (possivelmente uma que criou utilizando o [Editor de esquema](#schema-editor)), pode clicar em **procurar**, navegue para o ficheiro, clique em **guardar**e, em seguida, clique em **OK**.
        - Se pretender criar um novo esquema, clique em **OK**e, em seguida, clique em **Editor de esquema** na janela principal. Em seguida, avance para o [Editor de esquema](#schema-editor) informações. Após criar o novo ficheiro de esquema, não se esqueça de voltar para o **opções avançadas** janela incluir o ficheiro de esquema recentemente criado.

6. Depois de concluir e fechar o **configuração DSN de controladores ODBC Azure Cosmos DB** janela, o novo utilizador DSN é adicionada ao separador DSN de utilizador.

    ![Novo Azure Cosmos DB DSN ODBC no separador DSN de utilizador](./media/odbc-driver/odbc-driver-user-dsn.png)

## <a id="#collection-mapping"></a>Passo 3: Criar uma definição de esquema utilizando o método de mapeamento de coleção

Existem dois tipos de métodos de amostragem que pode utilizar: **mapeamento de coleção** ou **tabela delimitadores**. Uma sessão de amostragem pode utilizar ambos os métodos de amostragem, mas cada coleção só pode utilizar um método de amostragem específico. Os passos abaixo criar um esquema para os dados numa ou mais coleções utilizando o método de mapeamento de coleção. Este método de amostragem obtém os dados na página de uma coleção para determinar a estrutura dos dados. -Transposes uma coleção a uma tabela do lado do ODBC. Este método de amostragem é rápido e eficiente quando os dados de uma coleção são homogéneo. Se uma coleção contém heterogéneos tipo de dados, recomendamos que utilize o [delimitadores de tabela de mapeamento método](#table-mapping) como fornece um método de amostragem mais robusto para determinar as estruturas de dados na coleção. 

1. Depois de concluir os passos 1 a 4 no [ligar à base de dados do Azure Cosmos DB](#connect), clique em **Editor de esquema** no **configuração DSN de controladores ODBC Azure Cosmos DB** janela.

    ![Botão de editor do esquema na janela do Azure Cosmos DB ODBC controlador DSN programa de configuração](./media/odbc-driver/odbc-driver-schema-editor.png)
2. No **Editor de esquema** janela, clique em **criar novo**.
    O **gerar esquema** janela apresenta todas as coleções na conta de base de dados do Azure Cosmos. 
3. Selecione um ou mais coleções de exemplo e, em seguida, clique em **exemplo**. 
4. No **vista estrutura** separador a base de dados, esquema, tabela e são representados. Na vista de tabela, a análise apresenta o conjunto de propriedades associadas com os nomes de coluna (nome do SQL Server, o nome de origem, etc.).
    Para cada coluna, pode modificar o nome SQL da coluna, o tipo SQL, o comprimento SQL (se aplicável), dimensionamento (se aplicável), precisão (se aplicável) e Nullable.
    - Pode definir **Ocultar coluna** para **verdadeiro** se de que pretende excluir essa coluna de resultados da consulta. Colunas marcado Ocultar coluna = true não são devolvidos para a seleção e a projeção, embora ainda sejam parte do esquema. Por exemplo, pode ocultar todas as propriedades do sistema necessário de base de dados do Azure Cosmos começados por "_".
    - O **id** coluna é o campo apenas que não é possível ocultar porque é utilizado como a chave primária no esquema normalizado. 
5. Quando tiver concluído a definir o esquema, clique em **ficheiro** | **guardar**, navegue para o diretório para guardar o esquema e, em seguida, clique em **guardar**.

    Se no futuro que pretende utilizar este esquema com um DSN, abra a janela de configuração do Azure Cosmos DB ODBC controlador DSN (através de ODBC dados origem administrador), clique em Opções avançadas e, em seguida, na caixa de ficheiro de esquema, navegue para o esquema guardado. Guardar um ficheiro de esquema para um DSN existente modifica a ligação de DSN ao âmbito para os dados e a estrutura definido pelo esquema.

## <a id="table-mapping"></a>Passo 4: Criar uma definição de esquema utilizando os delimitadores de tabela de mapeamento de método

Existem dois tipos de métodos de amostragem que pode utilizar: **mapeamento de coleção** ou **tabela delimitadores**. Uma sessão de amostragem pode utilizar ambos os métodos de amostragem, mas cada coleção só pode utilizar um método de amostragem específico. 

Os seguintes passos criar um esquema para os dados numa ou mais coleções utilizando o **tabela delimitadores** mapeamento de método. Recomendamos que utilize este método de amostragem quando as coleções contêm heterogéneo tipo de dados. Pode utilizar este método para definir o âmbito de amostragem para um conjunto de atributos e os respetivos valores correspondentes. Por exemplo, se um documento contém uma propriedade "Type", pode definir o âmbito de amostragem para os valores desta propriedade. O resultado final a amostragem seria um conjunto de tabelas para cada um dos valores para o tipo que especificou. Por exemplo, digite = carro irá produzir uma tabela de carro ao tipo = Plane produziria uma tabela de Plane.

1. Depois de concluir os passos 1 a 4 no [ligar à base de dados do Azure Cosmos DB](#connect), clique em **Editor de esquema** na janela do Azure Cosmos DB ODBC controlador DSN a configuração.
2. No **Editor de esquema** janela, clique em **criar novo**.
    O **gerar esquema** janela apresenta todas as coleções na conta de base de dados do Azure Cosmos. 
3. Selecione uma coleção no **vista de exemplo** separador o **mapeamento definição** coluna para a coleção, clique em **editar**. Em seguida, no **mapeamento definição** janela, selecione **delimitadores de tabela** método. Em seguida, faça o seguinte:

    a. No **atributos** caixa, escreva o nome de uma propriedade de delimitador. Esta é uma propriedade do documento que pretende definir o âmbito de amostragem para, por exemplo, cidade e prima enter. 

    b. Se pretender apenas o âmbito de amostragem para determinados valores para o atributo introduziu apenas, selecione o atributo na caixa de seleção, em seguida, introduza um valor no **valor** caixa, por exemplo, Seattle e prima introduza. Pode continuar a adicionar vários valores de atributos. Certifique-se apenas de que o atributo correto está selecionado quando está a introduzir valores.

    Por exemplo, se incluir um **atributos** valor da cidade e pretende limitar a tabela para incluir apenas as linhas com um valor de cidade de Nova Iorque e Dubai, introduziria cidade na caixa de atributos e Nova Iorque e, em seguida, Dubai no **Valores** caixa.
4. Clique em **OK**. 
5. Depois de concluir as definições de mapeamento para as coleções que pretende, além de exemplo a **Editor de esquema** janela, clique em **exemplo**.
     Para cada coluna, pode modificar o nome SQL da coluna, o tipo SQL, o comprimento SQL (se aplicável), dimensionamento (se aplicável), precisão (se aplicável) e Nullable.
    - Pode definir **Ocultar coluna** para **verdadeiro** se de que pretende excluir essa coluna de resultados da consulta. Colunas marcado Ocultar coluna = true não são devolvidos para a seleção e a projeção, embora ainda sejam parte do esquema. Por exemplo, pode ocultar todas as propriedades de sistema necessário de base de dados do Azure Cosmos começados por "_".
    - O **id** coluna é o campo apenas que não é possível ocultar porque é utilizado como a chave primária no esquema normalizado. 
6. Quando tiver concluído a definir o esquema, clique em **ficheiro** | **guardar**, navegue para o diretório para guardar o esquema e, em seguida, clique em **guardar**.
7. Volta a **configuração DSN de controladores ODBC Azure Cosmos DB** janela, clique em * * Opções avançadas *. Em seguida, no **ficheiro de esquema** caixa, navegue para o ficheiro de esquema guardada e clique em **OK**. Clique em **OK** novamente para guardar o DSN. Isto poupa o esquema que criou para o DSN. 

## <a name="optional-creating-views"></a>(Opcional) Criar vistas
Pode definir e criar vistas como parte do processo de amostragem. Estas vistas são equivalentes às vistas do SQL Server. São só de leitura e são âmbito as seleções e projeções de SQL de BD do Azure Cosmos definido. 

Para criar uma vista para os seus dados, no **Editor de esquema** janela, no **vista definições** coluna, clique em **adicionar** na linha da coleção da amostra. Em seguida, no **vista definições** janela, efetue o seguinte procedimento:
1. Clique em **novo**, introduza um nome para a vista, por exemplo, EmployeesfromSeattleView e, em seguida, clique em **OK**.
2. No **Editar vista** janela, introduza uma consulta de base de dados do Azure Cosmos. Tem de ser uma consulta de base de dados SQL do Azure Cosmos, por exemplo`SELECT c.City, c.EmployeeName, c.Level, c.Age, c.Gender, c.Manager FROM c WHERE c.City = “Seattle”`e, em seguida, clique em **OK**.

Pode criar uma várias vistas que pretender. Quando tiver terminado definir as vistas, pode, em seguida, apresentar exemplos de dados. 

## <a name="step-5-view-your-data-in-bi-tools-such-as-power-bi-desktop"></a>Passo 5: Ver os seus dados nas ferramentas de BI como o Power BI Desktop

Pode utilizar o novo DSN ligar DocumentADB com quaisquer ferramentas compatível com ODBC - este passo simplesmente mostra como ligar ao Power BI Desktop e criar uma visualização do Power BI.

1. Abra o ambiente de trabalho do Power BI.
2. Clique em **obter dados**.
3. No **obter dados** janela, clique em **outros** | **ODBC** | **Connect**.
4. No **de ODBC** janela, selecione a origem de dados de nomes que criou e, em seguida, clique em **OK**. Pode deixar o **opções avançadas** entradas em branco.
5. No **aceder a uma origem de dados com um controlador ODBC** janela, selecione **predefinido ou personalizado** e, em seguida, clique em **Connect**. Não é necessário incluir o **propriedades de cadeia de ligação de credencial**.
6. No **navegador** janela, no painel esquerdo, expanda a base de dados, o esquema e, em seguida, selecione a tabela. O painel de resultados inclui os dados utilizando o esquema que criou.
7. Para visualizar os dados no Power BI desktop, marque a caixa à frente do nome de tabela e, em seguida, clique em **carga**.
8. No Power BI Desktop, da extremidade esquerda, selecione o separador de dados ![Separador de dados no Power BI Desktop](./media/odbc-driver/odbc-driver-data-tab.png) para confirmar os dados foi importada.
9. Agora, pode criar os visuais que utilizam o Power BI ao clicar no separador de relatório ![separador de relatório no Power BI Desktop](./media/odbc-driver/odbc-driver-report-tab.png), clicando em **novo Visual**e, em seguida, personalizar o mosaico. Para obter mais informações sobre a criação de visualizações no Power BI Desktop, consulte [tipos de visualização no Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-visualization-types-for-reports-and-q-and-a/).

## <a name="troubleshooting"></a>Resolução de problemas

Se receber o erro seguinte, certifique-se a **anfitrião** e **chave de acesso** valores que copiou do portal do Azure no [passo 2](#connect) estão corretas e tente novamente. Utilize os botões de cópia para a direita do **anfitrião** e **chave de acesso** valores no portal do Azure para copiar o erro de valores livre.

    [HY000]: [Microsoft][Azure Cosmos DB] (401) HTTP 401 Authentication Error: {"code":"Unauthorized","message":"The input authorization token can't serve the request. Please check that the expected payload is built as per the protocol, and check the key being used. Server used the following payload to sign: 'get\ndbs\n\nfri, 20 jan 2017 03:43:55 gmt\n\n'\r\nActivityId: 9acb3c0d-cb31-4b78-ac0a-413c8d33e373"}`

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a BD do Cosmos do Azure, consulte [o que é a base de dados do Azure Cosmos?](introduction.md).
