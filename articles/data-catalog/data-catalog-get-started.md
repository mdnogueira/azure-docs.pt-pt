<properties
   pageTitle="Catálogo de Dados do Azure - introdução ao Catálogo de Dados | Microsoft Azure"
   description="Tutorial ponto-a-ponto que apresenta os cenários e as capacidades do Catálogo de Dados do Azure."
   documentationCenter=""
   services="data-catalog"
   authors="steelanddata"
   manager=""
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="05/06/2016"
   ms.author="maroche"/>

# Introdução ao Catálogo de Dados do Azure

Este artigo é uma descrição geral ponto-a-ponto dos cenários e das capacidades do **Catálogo de Dados do Azure**. Depois de se inscrever no serviço, siga estes passos para criar um Catálogo de Dados e registar, anotar e detetar origens de dados.

## Pré-requisitos do tutorial

Antes de começar este tutorial, tem de ter o seguinte:

- **Uma subscrição do Azure** - Se não tiver uma subscrição, pode criar uma conta de avaliação gratuita em apenas alguns minutos. Veja [Avaliação Gratuita](https://azure.microsoft.com/pricing/free-trial/) para obter detalhes.
- **Azure Active Directory** - O Catálogo de Dados do Azure utiliza o [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) para a gestão de identidades e acessos.
- **Origens de dados** -O Catálogo de Dados do Azure tem capacidades para detetar origens de dados. Este tutorial utiliza a base de dados de exemplo Adventure Works, mas pode utilizar qualquer origem de dados suportada se preferir trabalhar com dados que lhe são familiares e relevantes para a sua função. Para obter uma lista de origens de dados suportadas, veja [Origens de dados suportadas](data-catalog-dsr.md).

> [AZURE.NOTE] Para obter mais informações sobre subscrições do Azure e do Azure Active Directory, veja [Pré-requisitos do Catálogo de Dados do Azure](data-catalog-prerequisites.md).

Vamos começar a instalar a base de dados de exemplo Adventure Works.

## Exercício 1: Instalar a base de dados de exemplo Adventure Works

Neste exercício, instala o exemplo Adventure Works para o Motor de Base de Dados do SQL Server que é utilizado nos exercícios que se seguem.

### Instalar a base de dados OLTP de 2014 do Adventure Works

A base de dados Adventure Works suporta cenários de processamento de transações online padrão para um fabricante de bicicletas fictício (Adventure Works Cycles) que inclui Produtos, Vendas e Compras. Neste tutorial, vai registar informações sobre os produtos no **Catálogo de Dados do Azure**.

Eis como instalar a base de dados de exemplo Adventure Works.

Para instalar a base de dados de exemplo Adventure Works, pode restaurar uma cópia de segurança AdventureWorks2014 localizada em [Adventure Works 2014 Full Database Backup.zip](https://msftdbprodsamples.codeplex.com/downloads/get/880661), no CodePlex. Uma forma de restaurar a base de dados é executar um script T-SQL no SQL Server Management Studio.

**Instalar a base de dados de exemplo Adventure Works com um script T-SQL**

1.  Crie uma pasta denominada C:\DataCatalog_GetStarted. Se utilizar outro nome de pasta, certifique-se de que altera o caminho do script T-SQL abaixo.
2.  Transfira [Adventure Works 2014 Full Database Backup.zip](https://msftdbprodsamples.codeplex.com/downloads/get/880661).
3.  Extraia Adventure Works 2014 Full Database Backup.zip para C:\DataCatalog_GetStarted. O script abaixo pressupõe que o ficheiro de cópia de segurança está localizado em C:\DataCatalog_GetStarted\Adventure Works 2014 Full Database Backup\AdventureWorks2014.bak.
4.  No **SQL Server Management Studio**, na barra de ferramentas **Standard**, clique em **Nova Consulta**.
5.  Execute o seguinte código T-SQL na janela da consulta.

**Execute este script para instalar a base de dados do Adventure Works 2014**

    USE [master]
    GO
    -- NOTE: This script is for sample purposes only. The default backup file path for this script is C:\DataCatalog_GetStarted. To run this script, create the default file path or change the file path, and copy AdventureWorks2014.bak into the path.

    -- IMPORTANT: In a production application, restore a SQL database to the data folder for your SQL Server instance.

    RESTORE DATABASE AdventureWorks2014
        -- AdventureWorks2014.bak file location
        FROM disk = 'C:\DataCatalog_GetStarted\Adventure Works 2014 Full Database Backup\AdventureWorks2014.bak'

        -- AdventureWorks2014.mdf database location
        WITH MOVE 'AdventureWorks2014_data' TO 'C:\DataCatalog_GetStarted\AdventureWorks2014.mdf',

        -- AdventureWorks2014.ldf log location
        MOVE 'AdventureWorks2014_Log' TO 'C:\DataCatalog_GetStarted\AdventureWorks2014.ldf'
    ,REPLACE
    GO

Como alternativa à execução de um script T-SQL, pode restaurar a base de dados com o SQL Server Management Studio. Veja [Restaurar uma Cópia de Segurança da Base de Dados (SQL Server Management Studio)](http://msdn.microsoft.com/library/ms177429.aspx).

Neste exercício, instalou a base de dados de exemplo Adventure Works que é utilizada nos restantes exercícios. No exercício seguinte, irá aprender a registar origens de dados do **Catálogo de Dados do Azure** a partir de tabelas na base de dados de exemplo Adventure Works.

## Exercício 2: Registar origens de dados

Neste exercício, irá utilizar a ferramenta de registo do **Catálogo de Dados do Azure** para registar origens de dados do Adventure Works no catálogo. O registo é o processo de extrair metadados estruturais chave – por exemplo, nomes, tipos e localizações – a partir da origem de dados e dos recursos que contém e copiar esses metadados para o catálogo. As origens de dados e os respetivos dados permanecem onde estão, mas os metadados são utilizados pelo catálogo para torná-los mais facilmente detetáveis e compreensíveis.

### Eis como registar uma origem de dados

1.  Aceda a https://azure.microsoft.com/services/data-catalog e clique em **Começar agora**.
2.  Inicie sessão no portal do **Catálogo de Dados do Azure** e clique em **Publicar dados**.

    ![](media/data-catalog-get-started/data-catalog-publish-data.png)

3.  Clique em **Iniciar Aplicação**.

    ![](media/data-catalog-get-started/data-catalog-launch-application.png)

4. Na página de **Boas-Vindas**, clique em **Sessão**, e introduza as suas credenciais.
5. Na página **Catálogo de Dados do Microsoft Azure**, faça duplo clique em **SQL Server** ou clique em ** SQL Server** e em **Seguinte**.

    ![](media/data-catalog-get-started/data-catalog-data-sources.png)

6.  Introduza as propriedades de ligação do SQL Server para o AdventureWorks2014 (veja o exemplo abaixo) e clique em **CONNECT**.

    ![](media/data-catalog-get-started/data-catalog-sql-server-connection.png)

7.  A página seguinte é onde regista os metadados da sua origem de dados. Neste exemplo, regista objetos de **Produção/Produto** do espaço de nomes AdventureWorks Production. Eis como fazê-lo:

    a. Na árvore **Hierarquia do Servidor**, clique em **Produção**.

    ![](media/data-catalog-get-started/data-catalog-server-hierarchy.png)

    b. Ctrl+clique em Product, ProductCategory, ProductDescription e ProductPhoto.

    c. Clique na seta selecionada mover (**>**). Isto irá mover todos os objetos Produto selecionados para a lista **Objetos a registar**.

    ![](media/data-catalog-get-started/data-catalog-available-objects.png)

    d. Em **Adicionar etiquetas**, introduza a descrição e a fotografia. Isto irá adicionar etiquetas de pesquisa a estes recursos de dados. As etiquetas são uma excelente forma de ajudar os utilizadores a localizar uma origem de dados registada.

    ![](media/data-catalog-get-started/data-catalog-objects-register.png)

    e.  **Opcional**: pode **Incluir uma Pré-visualização** e **Adicionar um especialista de origem de dados**.

    f.  Clique em **REGISTER**. O **Catálogo de Dados do Azure** regista os objetos selecionados. Neste exercício, os objetos selecionados de Adventure Works estão registados.

    g.  Para ver os objetos da origem de dados registados, clique em **Ver Portal**. No portal do **Catálogo de Dados do Azure**, pode ver objetos de origem de dados em **Mosaicos** ou numa **Lista**.

    ![](media/data-catalog-get-started/data-catalog-view-portal.png)

Neste exercício, registou objetos da base de dados de exemplo Adventure Works, para que estes possam ser facilmente detetados pelos utilizadores na sua organização.
No exercício seguinte, irá aprender a detetar recursos de dados registados.

## Exercício 3: Detetar Recursos de Dados Registados

Neste exercício, vai utilizar o portal do **Catálogo de Dados do Azure** para detetar recursos de dados registados e ver os seus metadados. O **Catálogo de Dados do Azure** fornece várias ferramentas para detetar recursos de dados, incluindo a pesquisa de palavra-chave simples, filtros interativos e uma sintaxe de pesquisa avançada para utilizadores avançados.

### Eis como detetar recursos de dados registados

O **Catálogo de Dados do Azure** tem uma sintaxe de pesquisa simples, mas poderosa, que permite construir facilmente as consultas que respondem às necessidades dos utilizadores de dados. Para obter detalhes sobre a pesquisa do **Catálogo de Dados do Azure**, veja [Referência de sintaxe da Pesquisa do Catálogo de Dados](https://msdn.microsoft.com/library/azure/mt267594.aspx).

O **Catálogo de Dados do Azure** tem as seguintes opções de pesquisa:

- Pesquisa de palavra-chave
- Filtro
- Pesquisa avançada

Também pode refinar os recursos de dados que quer ver. O **Catálogo de Dados do Azure** tem as seguintes opções de vista:

- Ver propriedades
- Ver colunas
- Ver pré-visualização

Neste exemplo, irá utilizar uma pesquisa de palavra-chave. A pesquisa do **Catálogo de Dados do Azure** tem várias técnicas de consulta. Neste exemplo, vai utilizar uma consulta de pesquisa **Agrupamento**.

**Técnicas de Consulta**

|Técnica|Utilização|Exemplo
|---|---|---
|Controlo do Âmbito da Propriedade|Devolver apenas origens de dados em que o termo de pesquisa tem correspondência na propriedade especificada|name:product
|Operadores Lógicos|Ampliar ou reduzir uma pesquisa com operações booleanas, conforme descrito na secção Operadores Booleanos nesta página|finanças NÃO empresariais
|Agrupar com Parênteses|Utilizar parênteses para agrupar partes da consulta para alcançar isolamento lógico, especialmente em conjunto com operadores booleanos|name:product E (tags:illustration OU tags:photo)
|Operadores de Comparação|Utilizar comparações além das de igualdade para propriedades que têm tipos de dados numéricos e de data|creationTime:>11/05/14

Neste exemplo, irá fazer uma pesquisa de **Agrupamento** para recursos de dados cujo nome é igual ao produto e as etiquetas são iguais às ilustrações ou em que as etiquetas são iguais à fotografia.

1. Aceda a https://azure.microsoft.com/services/data-catalog, clique em **Começar** e inicie sessão no portal do **Catálogo de Dados do Azure**.
2. Na caixa **Pesquisar Catálogo de Dados**, introduza uma consulta de **Agrupamento**: (**tags:description OU tags:photo**).
3. Clique no ícone de pesquisa ou prima Enter. O **Catálogo de Dados do Azure** irá apresentar recursos de dados para esta consulta de pesquisa.

    ![](media/data-catalog-get-started/data-catalog-search-box.png)

Neste exercício, utilizou o portal do **Catálogo de Dados do Azure** para detetar e ver recursos de dados da Adventure Works registados com o catálogo.

<a name="annotating"/>
## Exercício 4: Anotar origens de dados registadas

Neste exercício, vai utilizar o portal do **Catálogo de Dados do Azure** para anotar recursos de dados que foram anteriormente registados no catálogo. As anotações que fornecer irão complementar e melhorar os metadados estruturais extraídos da origem de dados durante o registo, e farão com que os recursos de dados sejam muito fáceis de detetar e compreender. Uma vez que cada utilizador do **Catálogo de Dados** pode fornecer as suas própria anotações, é fácil para todos os utilizadores partilharem a perspetiva dos dados.

### Eis como pode anotar recursos de dados

1. Aceda a https://azure.microsoft.com/services/data-catalog, clique em **Começar** e inicie sessão no portal do **Catálogo de Dados do Azure**.
2. Clique em **Detetar**.
3. Escolha um ou mais **Recursos de Dados**. Neste exemplo, escolha **ProductPhoto**, e introduza "Fotografias de produto para os materiais de marketing".
4. Introduza uma **Descrição** que irá ajudar os outros a detetar e compreender porquê e como utilizar os recursos de dados selecionados. Por exemplo, introduza "Imagens de produto". Também pode adicionar mais etiquetas e ver as colunas.
5. Agora pode tentar pesquisar e filtrar para detetar recursos de dados com os metadados descritivos que adicionou ao catálogo.

    ![](media/data-catalog-get-started/data-catalog-annotate.png)

Neste exercício, adicionou informações descritivas aos recursos de dados registados, de modo que os utilizadores do catálogo possam detetar origens de dados utilizando termos que compreendem.

> [AZURE.NOTE] A Edição Standard do Catálogo de Dados inclui um glossário comercial que permite aos administradores do catálogo definir uma taxonomia comercial central. Os utilizadores do catálogo podem assim anotar recursos de dados com os termos do glossário. Para mais informações, veja [Como configurar o Glossário Comercial para Etiquetagem Regida](data-catalog-how-to-business-glossary.md)  

## Exercício 5: Fazer crowdsourcing de metadados

Neste exercício, vai trabalhar com outro utilizador para adicionar metadados a recursos de dados no catálogo. A abordagem crowdsourcing do **Catálogo de Dados do Azure** para anotações permite que qualquer utilizador adicione etiquetas, descrições e outros metadados, de modo que qualquer utilizador com uma perspetiva de um recurso de dados e da sua utilização possa capturar e disponibilizar essa perspetiva para outros utilizadores.

> [AZURE.NOTE] Se não tiver outro utilizador com o qual trabalhar neste tutorial, não se preocupe! Qualquer utilizador que aceda ao catálogo de dados pode adicionar a sua própria perspetiva quando ele optar por fazê-lo. Esta abordagem de crowdsourcing aos metadados permite que o conteúdo do catálogo e a riqueza de metadados do catálogo aumentem ao longo do tempo.

### Eis como pode fazer crowdsourcing metadados sobre recursos de dados

Peça a um colega para repetir o exercício [Anotar Origens de Dados Registadas](#annotating) acima. Depois de os seus colegas adicionarem descrições a um recurso de dados, tais como ProductPhoto, verá vários anotações.

![](media/data-catalog-get-started/data-catalog-crowdsource.png)

Neste exercício, explorou as capacidades do **Catálogo de Dados do Azure** para metadados em crowdsourcing, em que qualquer utilizador do catálogo pode anotar os recursos de dados por ele detetados.

## Exercício 6: Ligar a origens de dados

Neste exercício, vai utilizar o portal do **Catálogo de Dados do Azure** para ligar a uma origem de dados com o Microsoft Excel.

> [AZURE.NOTE] É importante lembrar-se que o **Catálogo de Dados do Azure** não concede acesso à origem de dados real – apenas facilita a deteção e compreensão da mesma pelos utilizadores. Quando os utilizadores estabelecem ligação a uma origem de dados, a aplicação de cliente que escolherem utilizará as respetivas credenciais do Windows ou irá solicitar-lhes credenciais, conforme necessário. Se não tiver sido concedido anteriormente ao utilizador o acesso à base de dados, tem de ser-lhe concedido o acesso para que possa ligar.

### Eis como ligar a uma origem de dados a partir do Excel

1. Aceda a https://azure.microsoft.com/services/data-catalog, clique em **Começar** e inicie sessão no portal do **Catálogo de Dados do Azure**.
2. Clique em **Detetar**.
3. Escolha um recurso de dados. Neste exemplo, escolha ProductCategory.
4. Escolha **Abrir Em** > **Excel**.

    ![](media/data-catalog-get-started/data-catalog-connect1.png)

5. Na janela **Aviso de Segurança do Microsoft Excel**, clique em **Ativar**.
6. Abra o ficheiro **ProductCategory.odc**.
7. A origem de dados é aberta no Excel.

    ![](media/data-catalog-get-started/data-catalog-connect2.png)

Neste exercício, ligou a origens de dados detetadas com o **Catálogo de Dados do Azure**. O portal do **Catálogo de Dados do Azure** permite que os utilizadores liguem diretamente com as aplicações de cliente integradas no respetivo menu **Abrir em...** e permite aos utilizadores ligar através de qualquer aplicação que escolherem, com as informações de localização de ligação incluídas nos metadados do recurso.

## Exercício 7: Remover metadados da origem de dados

Neste exercício, vai utilizar o portal do **Catálogo de Dados do Azure** para remover dados de pré-visualização de recursos de dados registados e eliminar recursos de dados do catálogo.

> [AZURE.NOTE] O comportamento predefinido do catálogo é permitir que qualquer utilizador registe uma origem de dados e permitir que qualquer utilizador possa eliminar qualquer recurso de dados que foi registado. As capacidades de gestão incluídas na **Edição Standard Edition do Catálogo de Dados do Azure** fornecem opções adicionais para a propriedade de recursos, restringindo quem pode detetar e eliminar recursos.

No **Catálogo de Dados do Azure**, pode eliminar um recurso individual ou eliminar várias recursos.

### Eis como eliminar vários recursos de dados

1. Aceda a https://azure.microsoft.com/services/data-catalog, clique em **Começar** e inicie sessão no portal do **Catálogo de Dados do Azure**.
2. Clique em **Detetar**.
3. Escolha um ou mais recursos de dados.
4. Clique em **Eliminar**.

Neste exercício, removeu recursos de dados registados do catálogo.

## Exercício 8: gerir origens de dados registadas

Neste exercício, vai utilizar as capacidades de gestão do **Catálogo de Dados do Azure** para obter a propriedade de recursos de dados e para controlar o que os utilizadores podem detetar e como os utilizadores gerem esses recursos.

> [AZURE.NOTE] As funcionalidades de gestão descritas este exercício apenas estão disponíveis na **Edição Standard do Catálogo de Dados do Azure** e não na **Edição Gratuita**.
No **Catálogo de Dados do Azure**, pode obter a propriedade de recursos de dados, adicionar co-proprietários de recursos de dados e definir a visibilidade dos recursos de dados.

### Eis como obter a propriedade de recursos de dados e restringir a visibilidade

1. Aceda a https://azure.microsoft.com/services/data-catalog, clique em **Começar** e inicie sessão no portal do **Catálogo de Dados do Azure**.
2. Clique em **Detetar**.
3. Escolha um ou mais recursos de dados.
4. No painel **Propriedades**, secção **Gestão**, clique em **Obter Propriedade**.
5. Para restringir a visibilidade, clique em **Proprietários e Estes Utilizadores**.

    ![](media/data-catalog-get-started/data-catalog-ownership.png)

Neste exercício, explorou as capacidades de gestão do **Catálogo de Dados do Azure** e restringiu a visibilidade dos recursos de dados selecionados.

## Resumo

Neste tutorial, explorou funcionalidades essenciais do **Catálogo de Dados do Azure**, incluindo registar, anotar, detetar e gerir origens de dados empresariais. Agora que concluiu o tutorial, está na altura de começar. Pode começar hoje, registando as origens de dados de que você e a sua equipa dependem e convidando colegas para utilizarem o catálogo.



<!--HONumber=Jun16_HO2-->


