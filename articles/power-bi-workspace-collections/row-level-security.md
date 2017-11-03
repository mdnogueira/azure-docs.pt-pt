---
title: "Segurança ao nível da linha com coleções de área de trabalho do Power BI"
description: "Detalhes sobre a segurança ao nível da linha com coleções de área de trabalho do Power BI"
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ROBOTS: NOINDEX
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: asaxton
ms.openlocfilehash: 8c3ce8bc69a098d3133f27a2604f9d564693ea54
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="row-level-security-with-power-bi-workspace-collections"></a>Segurança ao nível da linha com coleções de área de trabalho do Power BI

Segurança ao nível da linha (RLS) pode ser utilizada para restringir o acesso de utilizador para dados específicas dentro de um relatório ou conjunto de dados, permitindo que vários utilizadores diferente de utilizar o relatório mesmo ao todos os dados de diferentes ver. Coleções de área de trabalho do Power BI suporta conjuntos de dados configurados com RLS.

![Fluxo de segurança ao nível da linha em coleções de área de trabalho do Power BI](media/row-level-security/flow-1.png)

> [!IMPORTANT]
> As Coleções de Áreas de Trabalho do Power BI foram preteridas e estão disponíveis até junho de 2018 ou até quando indicar o contrato. Recomendamos que planeie a migração para o Power BI Embedded para evitar interrupções na sua aplicação. Para obter informações sobre como migrar os dados para o Power BI Embedded, veja [How to migrate Power BI Workspace Collections content to Power BI Embedded (Como migrar o conteúdo das Coleções de Áreas de Trabalho do Power BI para o Power BI Embedded)](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Para tirar partido do RLS, é importante que compreender três conceitos principais; Os utilizadores, funções e regras. Vamos um olhar cada:

**Os utilizadores** – estes são os utilizadores finais reais visualizar relatórios. Em coleções de área de trabalho do Power BI, os utilizadores são identificados pela propriedade de nome de utilizador de um Token de aplicação.

**Funções** – os utilizadores pertencem a funções. Uma função é um contentor para as regras e o nome pode ser algo como "Gestor de vendas" ou "Vendedor". Em coleções de área de trabalho do Power BI, os utilizadores são identificados pela propriedade funções num Token de aplicação.

**Regras** – funções têm regras e essas regras são os filtros reais que vão ser aplicadas aos dados. Isto pode ser tão simple como "país = EUA" ou algo muito mais dinâmica.

### <a name="example"></a>Exemplo

Para o resto deste artigo, iremos fornecer um exemplo de criação RLS e, em seguida, consumir que dentro de uma aplicação incorporada. O nosso exemplo utiliza o [exemplo de análise de revenda](http://go.microsoft.com/fwlink/?LinkID=780547) ficheiro PBIX.

![Relatório de vendas de exemplo](media/row-level-security/scenario-2.png)

Nosso exemplo de análise de revenda mostra vendas para todos os arquivos de uma cadeia de revenda específico. Sem RLS, independentemente da que regional manager inicia sessão e visualiza o relatório, verá os mesmos dados. Gestão sénior determinou cada manager regional só deverá ver de vendas para os arquivos de que eles geridos, e para fazer isto, podemos utilizar RLS.

RLS foi criado no Power BI Desktop. Quando o conjunto de dados e o relatório estiverem abertos, iremos pode mudar para vista de diagrama para ver o esquema:

![Diagrama de modelo no Power BI Desktop](media/row-level-security/diagram-view-3.png)

Seguem-se alguns aspetos a reparar com este esquema:

* Todas as medidas, como **vendas totais**, são armazenados no **vendas** tabela de factos.
* Existem quatro tabelas de dimensão relacionados adicionais: **Item**, **tempo**, **arquivo**, e **regional**.
* As setas nas linhas relação indicam que forma os filtros que possam circular de uma tabela para outro. Por exemplo, se um filtro está colocado **tempo [Date]**, no esquema atual apenas poderia filtrar para baixo os valores existentes no **vendas** tabela. Não existem outras tabelas seriam afetadas por este filtro, uma vez que todas as setas nas linhas relação ponto para a tabela de venda e não ausente.
* O **regional** tabela indica que o Gestor de destina-se cada regional:
  
  ![Linhas de tabela regional](media/row-level-security/district-table-4.png)

Com base neste esquema, se aplicar um filtro para o **regional Manager** coluna na tabela regional, e se esse filtro corresponder o utilizador visualizar o relatório, que filtrar também filtros para baixo a **arquivo** e  **Vendas** tabelas apenas para mostram dados para esse regional específico manager.

Eis como:

1. No separador de modelação, clique em **gerir funções**.  
   ![Gerir funções clique no botão no Friso de modelação](media/row-level-security/modeling-tab-5.png)
2. Criar uma nova função chamada **Manager**.  
   ![Criação de funções no Power BI Desktop](media/row-level-security/manager-role-6.png)
3. No **regional** tabela introduza a seguinte expressão DAX: **[regional Manager] = USERNAME()**  
   ![Expressão de filtro DAX para a tabela na função](media/row-level-security/manager-role-7.png)
4. Para se certificar de que as regras estiverem a funcionar, no **modelação** separador, clique em **vista como funções**e, em seguida, introduza o seguinte:  
   ![Ver como funções](media/row-level-security/view-as-roles-8.png)

   Os relatórios agora irão mostrar dados, como se tem sessão iniciada como **Andrew Ma**.

Aplicar o filtro, a forma como foi feito aqui, filtra baixo todos os registos no **regional**, **arquivo**, e **vendas** tabelas. No entanto, devido a direção do filtro BAS relações existentes entre **vendas** e **tempo**, **vendas** e **Item**e **Item** e **tempo** tabelas não serão filtradas para baixo.

![Vista de diagrama com relações realçado](media/row-level-security/diagram-view-9.png)

Que poderá ser ok para este requisito, no entanto, se não queremos gestores para ver os itens para o qual não têm qualquer vendas, iremos foi ativar bidirecional filtragem cruzada para a relação e o filtro de segurança em ambas as direções do fluxo. Isto pode ser feito ao editar a relação entre **vendas** e **Item**, como esta:

![Direção do filtro para a relação de se estender](media/row-level-security/edit-relationship-10.png)

Agora, os filtros podem também fluxo da tabela de vendas para o **Item** tabela:

![Ícone de direção de filtro de relação na vista de diagrama](media/row-level-security/diagram-view-11.png)

> [!NOTE]
> Se estiver a utilizar o modo DirectQuery para os seus dados, terá de ativar a filtragem cruzada bidirecional selecionando estas duas opções:

1. **Ficheiro** -> **opções e definições** -> **funcionalidades de pré-visualização** -> **Ativar filtragem cruzada em ambas as direções para DirectQuery**.
2. **Ficheiro** -> **opções e definições** -> **DirectQuery** -> **permitir sem restrições medidas no modo DirectQuery**.

Para obter mais informações sobre filtragem cruzada bidirecional, transfira o [bidirecional filtragem cruzada no SQL Server Analysis Services 2016 e o Power BI Desktop](http://download.microsoft.com/download/2/7/8/2782DF95-3E0D-40CD-BFC8-749A2882E109/Bidirectional cross-filtering in Analysis Services 2016 and Power BI.docx) documento.

Isto encapsula num wrapper se todo o trabalho que tem de ser feita no Power BI Desktop, mas não existe um trabalho de peça mais que tem de ser feito para tornar a RLS regras que definimos trabalho no Power BI Embedded. Os utilizadores são autenticados e autorizados pela sua aplicação e os tokens de aplicação são utilizados para conceder acesso desse utilizador para um relatório do Power BI Embedded específico. Power BI Embedded não tem quaisquer informações específicas sobre quem é o utilizador. Para RLS funcione, tem de passar algum contexto adicional como parte do seu token de aplicação:

* **nome de utilizador** (opcional) – utilizado com RLS esta é uma cadeia que pode ser utilizada para ajudar a identificar o utilizador ao aplicar regras RLS. Ver a utilizar a linha segurança ao nível com o Power BI Embedded
* **funções** – uma cadeia que contém as funções para selecionar ao aplicar regras de segurança de nível de linha. Se a transmitir mais de uma função, deve ser transmitidas como uma matriz de cadeia.

Criar o token ao utilizar o [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#Microsoft_PowerBI_Security_PowerBIToken_CreateReportEmbedToken_System_String_System_String_System_String_System_DateTime_System_String_System_Collections_Generic_IEnumerable_System_String__) método. Se a propriedade de nome de utilizador estiver presente, também tem de passar pelo menos um valor em funções.

Por exemplo, pode alterar o EmbedSample. Linha DashboardController 55 foi atualizada a partir do

    var embedToken = PowerBIToken.CreateReportEmbedToken(this.workspaceCollection, this.workspaceId, report.Id);

para

    var embedToken = PowerBIToken.CreateReportEmbedToken(this.workspaceCollection, this.workspaceId, report.Id, "Andrew Ma", ["Manager"]);'

O token de aplicação completa aspeto semelhante ao seguinte:

![Exemplo do JSON web token](media/row-level-security/app-token-string-12.png)

Agora, com todas as peças em conjunto, quando um utilizador inicia sessão no nossa aplicação para ver este relatório, este vê os dados que estão autorizados a ver, tal como definido pela nossa segurança ao nível da linha.

![Relatório apresentado na aplicação](media/row-level-security/dashboard-13.png)

## <a name="see-also"></a>Consultar também

[Segurança ao nível da linha (RLS) com energia](https://powerbi.microsoft.com/documentation/powerbi-admin-rls/)  
[Autenticação e autorização nas Coleções de Áreas de Trabalho do Power BI](app-token-flow.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[Exemplo de Incorporação de JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  

Mais perguntas? [Tente a Comunidade do Power BI](http://community.powerbi.com/)