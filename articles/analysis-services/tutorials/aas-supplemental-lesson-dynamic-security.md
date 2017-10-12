---
title: "Lição suplementar do tutorial do Azure Analysis Services: Segurança dinâmica | Microsoft Docs"
description: "Descreve como utilizar a segurança dinâmica com filtros de linha no tutorial do Azure Analysis Services."
services: analysis-services
documentationcenter: 
author: Minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 05/26/2017
ms.author: owend
ms.openlocfilehash: b258c18fde15014192e8f604a4e8b3842c3e52c9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="supplemental-lesson---dynamic-security"></a>Lição suplementar - Segurança dinâmica

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

Nesta lição suplementar, vai criar uma função adicional que implementa a segurança dinâmica. A segurança dinâmica proporciona segurança ao nível da linha com base no nome de utilizador ou no id de início de sessão do utilizador com sessão atualmente iniciada. 
  
Para implementar a segurança dinâmica, vai adicionar uma tabela ao seu modelo que contém os nomes de utilizador dos utilizadores que se podem ligar ao modelo e procurar objetos e dados do mesmo. O contexto do modelo que vai criar neste tutorial é a Adventure Works. Contudo, para concluir esta lição, tem de adicionar uma tabela que contenha utilizadores do seu próprio domínio. Não são necessárias as palavras-passe dos nomes de utilizador que vão ser adicionados. Para criar a tabela EmployeeSecurity, com uma pequena amostra dos utilizadores do seu próprio domínio, utilize a funcionalidade Colar e cole os dados dos colaboradores numa folha de cálculo do Excel. Num cenário do mundo real, a tabela com os nomes de utilizador terá, geralmente, como origem de dados uma base de dados real, como, por exemplo, uma tabela DimEmployee real.  
  
Para implementar a segurança dinâmica, utilize duas funções DAX: a [Função USERNAME (DAX)](http://msdn.microsoft.com/22dddc4b-1648-4c89-8c93-f1151162b93f) e a [Função LOOKUPVALUE (DAX)](http://msdn.microsoft.com/73a51c4d-131c-4c33-a139-b1342d10caab). Estas funções, aplicadas a uma fórmula de filtro de linha, são definidas numa função nova. Ao utilizar a função LOOKUPVALUE, a fórmula especifica um valor da tabela EmployeeSecurity. Depois, transmite este valor para a função USERNAME, que especifica que o nome de utilizador do utilizador com sessão iniciada pertence a esta função. O utilizador pode, então, procurar apenas os dados especificados pelos filtros de linha da função. Neste cenário, vai especificar que os colaboradores do departamento de vendas só podem procurar dados de vendas da Internet relativos às regiões de vendas de que são membros.  
  
As tarefas que são exclusivas deste cenário de modelo em tabela da Adventure Works, mas que não se aplicam necessariamente a um cenário do mundo real, são identificadas como tal. Cada tarefa inclui informações adicionais que descrevem o propósito da mesma.  
  
Tempo estimado para concluir esta lição: **30 minutos**  
  
## <a name="prerequisites"></a>Pré-requisitos  
Este tópico de lição suplementar faz parte de um tutorial de modelação em tabela, que deve ser concluído por ordem. Antes de executar as tarefas nesta lição suplementar, deverá ter concluído todas as lições anteriores.  
  
## <a name="add-the-dimsalesterritory-table-to-the-aw-internet-sales-tabular-model-project"></a>Adicionar a tabela DimSalesTerritory ao Projeto de Modelo em Tabela AW Internet Sales  
Para implementar a segurança dinâmica neste cenário da Adventure Works, tem de acrescentar duas tabelas adicionais ao seu modelo. A primeira tabela a adicionar é DimSalesTerritory (como Sales Territory) a partir da mesma base de dados AdventureWorksDW. Posteriormente, vai aplicar um filtro de linha à tabela SalesTerritory que define os dados concretos que o utilizador com sessão iniciada pode procurar.  
  
#### <a name="to-add-the-dimsalesterritory-table"></a>Para adicionar a tabela DimSalesTerritory  
  
1.  No Explorador de Modelos em Tabela > **Origens de Dados**, clique com o botão direito do rato na sua ligação e clique em **Importar Tabelas Novas**.  

    Se for apresentada a caixa de diálogo Credenciais de Representação, escreva as credenciais de representação que utilizou na Lição 2: Adicionar Dados.
  
2.  No Navegador, selecione a tabela **DimSalesTerritory** e clique em **OK**.    
  
3.  No Editor de Consultas, clique na consulta **DimSalesTerritory** e remova a coluna **SalesTerritoryAlternateKey**.  
  
7.  Clique em **importar**.  
  
    A tabela nova é adicionada à área de trabalho do modelo. Os objetos e os dados da tabela de origem DimSalesTerritory são, então, importados para o Modelo em Tabela AW Internet Sales.  
  
9. Após a importação bem-sucedida da tabela, clique em **Fechar**.  

## <a name="add-a-table-with-user-name-data"></a>Adicionar tabela com dados de nome de utilizador  
A tabela DimEmployee da base de dados de exemplo AdventureWorksDW contém utilizadores do domínio AdventureWorks. Estes nomes de utilizador não existem no seu ambiente. Tem de criar uma tabela no seu ambiente que contenha uma pequena amostra (pelo menos, três) de utilizadores reais da sua organização. Depois, adicione-os como membros à função nova. As palavras-passe dos nomes de utilizador de exemplo não são necessárias, mas precisa de nomes de utilizador do Windows reais do seu domínio.  
  
#### <a name="to-add-an-employeesecurity-table"></a>Para adicionar uma tabela EmployeeSecurity  
  
1.  Abra o Microsoft Excel e crie uma folha de cálculo.  
  
2.  Copie a tabela seguinte, incluindo a linha de cabeçalho, e cole-a na folha de cálculo.  

    ```
      |EmployeeId|SalesTerritoryId|FirstName|LastName|LoginId|  
      |---------------|----------------------|--------------|-------------|------------|  
      |1|2|<user first name>|<user last name>|\<domain\username>|  
      |1|3|<user first name>|<user last name>|\<domain\username>|  
      |2|4|<user first name>|<user last name>|\<domain\username>|  
      |3|5|<user first name>|<user last name>|\<domain\username>|  
    ```

3.  Substitua o nome próprio, o apelido e o domínio/nome de utilizador pelos nomes e ids de início de sessão de três utilizadores da sua organização. Coloque o mesmo utilizador nas duas primeiras linhas de EmployeeId 1, que mostra que pertence a mais de uma região de vendas. Deixe os campos EmployeeId e SalesTerritoryId como estão.  
  
4.  Salve a folha de cálculo como **SampleEmployee**.  
  
5.  Na folha de cálculo, selecione todas as células que tenham dados de colaboradores, incluindo os cabeçalhos, clique com o botão direito do rato nos dados selecionados e clique em **Copiar**.  
  
6.  No SSDT, clique no menu **Editar** e clique em **Colar**.  
  
    Se Colar estiver a cinzento, clique em qualquer coluna de qualquer tabela na janela do estruturador de modelos e repita.  
  
7.  Na caixa de diálogo **Pré-visualização da colagem**, em **Nome da Tabela**, escreva **EmployeeSecurity**.  
  
8.  Em **Dados a colar**, verifique que estão incluídos todos os dados de utilizador e cabeçalhos da folha de cálculo SampleEmployee.  
  
9. Verifique se **Utilizar primeira linha como cabeçalhos de coluna** está marcado e clique em **OK**.  
  
    É criada uma tabela nova, chamada EmployeeSecurity, com os dados dos colaboradores copiados a partir da folha de cálculo SampleEmployee.  
  
## <a name="create-relationships-between-factinternetsales-dimgeography-and-dimsalesterritory-table"></a>Criar relações entre as tabelas FactInternetSales, DimGeography e DimSalesTerritory  
As tabelas FactInternetSales, DimGeography e DimSalesTerritory têm uma coluna em comum, SalesTerritoryId. A coluna SalesTerritoryId na tabela DimSalesTerritory contém valores com um Id diferente para cada região de vendas.  
  
#### <a name="to-create-relationships-between-the-factinternetsales-dimgeography-and-the-dimsalesterritory-table"></a>Para criar relações entre as tabelas FactInternetSales, DimGeography e DimSalesTerritory  
  
1.  Na Vista de Diagrama, na tabela **DimGeography**, clique sem soltar na coluna **SalesTerritoryId**, arraste o cursor para a coluna **SalesTerritoryId** da tabela **DimSalesTerritory** e largue.  
  
2.  Na tabela **FactInternetSales**, clique sem soltar na coluna **SalesTerritoryId**, arraste o cursor para a coluna **SalesTerritoryId** da tabela **DimSalesTerritory** e largue.  
  
    Repare que a propriedade Active desta relação é Falso, o que significa que está inativa. A tabela FactInternetSales já tem outra relação ativa.  
  
## <a name="hide-the-employeesecurity-table-from-client-applications"></a>Ocultar a tabela EmployeeSecurity de aplicações cliente  
Nesta tarefa, vai ocultar a tabela EmployeeSecurity, impedindo-a de aparecer na lista de campos de uma aplicação cliente. Tenha em conta que a ocultação de tabelas não as protege. Os utilizadores continuam a poder consultar os dados da tabela EmployeeSecurity, se souberem como fazê-lo. Para proteger os dados da tabela, impedindo os utilizadores de consultá-los, vai aplicar um filtro numa tarefa posterior.  
  
#### <a name="to-hide-the-employeesecurity-table-from-client-applications"></a>Para ocultar a tabela EmployeeSecurity de aplicações cliente  
  
-   No estruturador de modelos, na Vista de Diagrama, clique com o botão direito do rato no cabeçalho da tabela **Employee** e clique em **Ocultar das Ferramentas de Cliente**.  
  
## <a name="create-a-sales-employees-by-territory-user-role"></a>Criar uma função de utilizador Colaboradores de Vendas por Região  
Nesta tarefa, vai criar uma função de utilizador. Esta função inclui um filtro de linha que define que linhas da tabela DimSalesTerritory estão visíveis para os utilizadores. O filtro é depois aplicado na direção de relação um para muitos a todas as outras tabelas relacionadas com DimSalesTerritory. Também pode aplicar um filtro que impeça que toda a tabela EmployeeSecurity seja consultável por qualquer utilizador que seja membro da função.  
  
> [!NOTE]  
> A função Colaboradores de Vendas por Região criada nesta lição só permite que os membros procurem (ou consultem) os dados de vendas relativos às regiões de vendas a que pertencem. Se adicionar um utilizador como membro da função Colaboradores de Vendas por Região que também seja membro de uma função criada em [Lição 11: Criar Funções](../tutorials/aas-lesson-11-create-roles.md), obtém uma combinação de permissões. Quando os utilizadores são membros de várias funções, as permissões e os filtros de linha definidos para cada função são cumulativos. Isto significa que o utilizador tem as permissões mais amplas determinadas pela combinação das funções.  
  
#### <a name="to-create-a-sales-employees-by-territory-user-role"></a>Para criar uma função de utilizador Colaboradores de Vendas por Região  
  
1.  No SSDT, clique no menu **Modelo** e clique em **Funções**.  
  
2.  No **Gestor de Funções**, clique em **Nova**.  
  
    É adicionada à lista uma função nova com a permissão Nenhuma.  
  
3.  Clique na função nova e, na coluna **Nome**, mude o nome para **Colaboradores de Vendas por Território**.  
  
4.  Na coluna **Permissões**, clique na lista pendente e selecione a permissão **Leitura**.  
  
5.  Clique no separador **Membros** e em **Adicionar**.  
  
6.  Na caixa de diálogo **Selecionar Utilizador ou Grupo**, em **Introduzir o nome do objeto a selecionar**, escreva o primeiro nome de utilizador de exemplo que utilizou para criar a tabela EmployeeSecurity. Clique em **Verificar Nomes** para verificar se o nome de utilizador é válido e clique em **OK**.  
  
    Repita este passo, adicionando os outros nomes de utilizador de exemplo que utilizou quando criou a tabela EmployeeSecurity.  
  
7.  Clique no separador **Filtros de Linha**.  
  
8.  Na tabela **EmployeeSecurity**, na coluna **Filtro DAX**, escreva a fórmula seguinte:  
  
    ```
      =FALSE()  
    ```
  
    Esta fórmula especifica que todas as colunas são resolvidas para a condição booleana “false”. Nenhuma coluna da tabela EmployeeSecurity pode ser consultável por membros da função de utilizador Colaboradores de Vendas por Região.  
  
9. Na tabela **DimSalesTerritory**, escreva a fórmula seguinte:  

    ```  
    ='Sales Territory'[Sales Territory Id]=LOOKUPVALUE('Employee Security'[Sales Territory Id], 
      'Employee Security'[Login Id], USERNAME(), 
      'Employee Security'[Sales Territory Id], 
      'Sales Territory'[Sales Territory Id]) 
    ```
  
    Nesta fórmula, a função LOOKUPVALUE devolve todos os valores da coluna DimEmployeeSecurity [SalesTerritoryId], em que o EmployeeSecurity [LoginId] é o mesmo que o nome de utilizador do Windows com sessão iniciada atualmente e o EmployeeSecurity [SalesTerritoryId] é o mesmo que o DimSalesTerritory [SalesTerritoryId].  
  
    O conjunto de IDs de regiões de vendas que LOOKUPVALUE devolve é então utilizado para limitar as linhas que são mostradas na tabela DimSalesTerritory. Só são apresentadas as linhas em que o SalesTerritoryID da linha está no conjunto de IDs que a função LOOKUPVALUE devolve.  
  
10. No Gestor de Funções, clique em **Ok**.  
  
## <a name="test-the-sales-employees-by-territory-user-role"></a>Testar a Função de Utilizador Colaboradores de Vendas por Região  
Nesta tarefa, vai utilizar a funcionalidade Analisar no Excel no SSDT para testar a eficácia da função de utilizador Colaboradores de Vendas por Região. Especifique um dos nomes de utilizador que adicionou à tabela EmployeeSecurity e como membro desta função. Este nome é depois utilizado como o nome de utilizador efetivo na ligação criada entre o Excel e o modelo.  
  
#### <a name="to-test-the-sales-employees-by-territory-user-role"></a>Para testar a função de utilizador Colaboradores de Vendas por Região  
  
1.  No SSDT, clique no menu **Modelo** e clique em **Analisar no Excel**.  
  
2.  Na caixa de diálogo **Analisar no Excel**, em **Especificar o nome de utilizador ou função a utilizar para ligar ao modelo**, selecione **Outro Utilizador do Windows** e clique em **Procurar**.  
  
3.  Na caixa de diálogo **Selecionar Utilizador ou Grupo**, em **Introduzir o nome do objeto a selecionar**, escreva um nome de utilizador que tenha incluído na tabela EmployeeSecurity e clique em **Verificar Nomes**.  
  
4.  Clique em **Ok** para fechar a caixa de diálogo **Selecionar Utilizador ou Grupo** e clique em **Ok** para fechar a caixa de diálogo **Analisar no Excel**.  
  
    O Excel abre-se com um livro novo. É criada automaticamente uma Tabela Dinâmica. Os Campos da Lista Dinâmica incluem a maioria dos campos de dados disponíveis no seu modelo novo.  
  
    Repare que a tabela EmployeeSecurity não está visível na lista Campos da Tabela Dinâmica. Tal deve-se a tê-la ocultado das ferramentas de cliente numa tarefa anterior.  
  
5.  Na lista **Campos**, em **∑ Internet Sales** (measures), selecione a medida **InternetTotalSales**. A medida é introduzida nos campos **Valores**,  
  
6.  Selecione a coluna **SalesTerritoryId** a partir da tabela **DimSalesTerritory**. A coluna é introduzida nos campos **Etiquetas de Linhas**.  
  
    Repare que só aparecem números de vendas na Internet relativos à região à qual o nome de utilizador efetivo que utilizou pertence. Se selecionar outra coluna, como City na tabela DimGeography, como um campo Etiqueta de Linhas, só são apresentadas cidades da região de vendas à qual o utilizador efetivo pertence.  
  
    Este utilizador não pode procurar nem consultar dados de vendas na Internet relativos a regiões das quais não faz parte. Esta limitação deve-se ao facto de o filtro de linha definido para a tabela DimSalesTerritory, na função de utilizador Colaboradores de Vendas por Região, proteger os dados de todas as informações relacionadas com outras regiões de vendas.  
  
## <a name="see-also"></a>Veja Também  
[Função USERNAME (DAX)](https://msdn.microsoft.com/library/hh230954.aspx)  
[Função LOOKUPVALUE (DAX)](https://msdn.microsoft.com/library/gg492170.aspx)  
[Função CUSTOMDATA (DAX)](https://msdn.microsoft.com/library/hh213140.aspx)  