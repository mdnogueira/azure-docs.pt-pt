---
title: "Introdução ao tabelas temporais na base de dados SQL do Azure | Microsoft Docs"
description: "Saiba como começar a utilizar tabelas temporais na SQL Database do Azure."
services: sql-database
documentationcenter: 
author: bonova
manager: jhubbard
editor: 
ms.assetid: c8c0f232-0751-4a7f-a36e-67a0b29fa1b8
ms.service: sql-database
ms.custom: develop databases
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: On Demand
ms.date: 01/10/2017
ms.author: bonova
ms.openlocfilehash: 58f97c142ba0b9282d8988fc1bc037b9c0c69986
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="getting-started-with-temporal-tables-in-azure-sql-database"></a>Introdução ao tabelas temporais na base de dados SQL do Azure
As tabelas temporais são uma nova funcionalidade de programação para da base de dados do SQL do Azure que lhe permite controlar e analisar o histórico de alterações nos seus dados, sem a necessidade de codificação personalizada completo. As tabelas temporais mantém os dados estritamente relacionados para o contexto de tempo para que os factos armazenados podem ser interpretados como válido apenas dentro do período específico. Esta propriedade de tabelas temporais permite eficiente analysis baseados no tempo e ao obter conhecimentos aprofundados sobre evolução de dados.

## <a name="temporal-scenario"></a>Cenário temporal
Este artigo ilustra os passos para utilizar as tabelas temporais num cenário de aplicação. Suponha que pretende controlar a atividade do utilizador num novo Web site que está a ser desenvolvido a partir do zero ou num Web site existente que pretende expandir a análise de atividade do utilizador. Neste exemplo simplificada, partimos do pressuposto que o número de páginas web visitados durante um período de tempo é um indicador de que tem de ser capturadas e monitorizados na base de dados do site que está alojado numa SQL Database do Azure. O objetivo da análise histórico da atividade do utilizador está a obter entradas redesenhar o Web site e fornecer a melhor experiência para os visitantes.

O modelo de base de dados para este cenário é muito simple - métrica de atividade do utilizador é representado com um campo de inteiros simples, **PageVisited**e é capturado, juntamente com informações básicas sobre o perfil de utilizador. Além disso, para análise de tempo com base, também manteria uma série de linhas para cada utilizador, onde cada linha representa o número de páginas de um determinado utilizador visitou dentro de um período de tempo específico.

![Esquema](./media/sql-database-temporal-tables/AzureTemporal1.png)

Felizmente, não é necessário colocar qualquer esforço na sua aplicação para manter estas informações de atividade. Com as tabelas temporais, este processo é um processo automatizado - dando-lhe total flexibilidade durante a conceção do Web site e mais tempo para se focarem na análise de dados própria. É a única coisa que precisa fazer garantir que **WebSiteInfo** tabela está configurada como [temporais com versão do sistema](https://msdn.microsoft.com/library/dn935015.aspx#Anchor_0). Os passos exatos para utilizar as tabelas temporais neste cenário são descritos abaixo.

## <a name="step-1-configure-tables-as-temporal"></a>Passo 1: Configurar tabelas temporais como
Dependendo se estiver a iniciar o desenvolvimento de novo ou atualizar a aplicação existente, terá de criar as tabelas temporais ou modificar já existentes ao adicionar atributos temporais. Em geral caso, o cenário pode ser uma mistura destas duas opções. Efetuar estas utilizando a ação [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) (SSMS), [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) (SSDT) ou qualquer outra ferramenta de desenvolvimento de Transact-SQL.

> [!IMPORTANT]
> É recomendado utilizar sempre a versão mais recente do Management Studio, para permanecer sincronizado com as atualizações do Microsoft Azure e da Base de Dados SQL. [Atualize o SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
> 
> 

### <a name="create-new-table"></a>Criar nova tabela
Utilize o item de menu de contexto "Nova tabela com versão do sistema" no SSMS Object Explorer para abrir o editor de consultas com um script de modelo de tabela temporal e, em seguida, utilize "Especifique valores para os parâmetros do modelo" (Ctrl + Shift + M) para povoar o modelo:

![SSMSNewTable](./media/sql-database-temporal-tables/AzureTemporal2.png)

No SSDT, escolha o modelo de "(com versão do sistema) de tabela Temporal" quando adicionar novos itens para o projeto de base de dados. Que irá abrir o estruturador de tabela e permitem-lhe especificar facilmente o esquema de tabela:

![SSDTNewTable](./media/sql-database-temporal-tables/AzureTemporal3.png)

Também pode criar tabela temporal especificando as instruções Transact-SQL diretamente, conforme mostrado no exemplo abaixo. Tenha em atenção que os elementos de cada tabela temporal obrigatórios estão a definição de período e a cláusula SYSTEM_VERSIONING com uma referência a outra tabela de utilizador que irá armazenar versões de linha histórico:

````
CREATE TABLE WebsiteUserInfo 
(  
    [UserID] int NOT NULL PRIMARY KEY CLUSTERED 
  , [UserName] nvarchar(100) NOT NULL
  , [PagesVisited] int NOT NULL 
  , [ValidFrom] datetime2 (0) GENERATED ALWAYS AS ROW START
  , [ValidTo] datetime2 (0) GENERATED ALWAYS AS ROW END
  , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo)
 )  
 WITH (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.WebsiteUserInfoHistory));
````

Ao criar a tabela temporal com versão do sistema, a tabela de histórico associada com a configuração predefinida é criada automaticamente. A tabela de histórico predefinida contém um índice de árvore B em cluster em colunas de período (fim, início) com compressão de página ativada. Esta configuração é o ideal para a maioria dos cenários em que são utilizadas as tabelas temporais, especialmente para [dados auditoria](https://msdn.microsoft.com/library/mt631669.aspx#Anchor_0). 

Neste caso específico, pretendemos para efetuar a análise de tendências baseados no tempo ao longo de um histórico de dados maior e com conjuntos de dados maiores, pelo que a opção de armazenamento para a tabela de histórico é um índice columnstore em cluster. Fornece um columnstore em cluster compressão muito bom e o desempenho de consultas analíticos. As tabelas temporais dão-lhe a flexibilidade para configurar os índices em tabelas de atuais e temporais completamente independente. 

> [!NOTE]
> Os índices Columnstore só estão disponíveis no escalão de serviço premium.
>

O script seguinte mostra como o índice de predefinição na tabela de histórico pode ser alterado para o columnstore em cluster:

````
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory
ON dbo.WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON); 
````

As tabelas temporais são representadas no Explorador de objetos com o ícone específico para fins de identificação mais fácil, enquanto a respetiva tabela de histórico é apresentada como um nó subordinado.

![AlterTable](./media/sql-database-temporal-tables/AzureTemporal4.png)

### <a name="alter-existing-table-to-temporal"></a>Alterar uma tabela existente para temporal
Vamos abrange o cenário alternativo no qual a tabela de WebsiteUserInfo já existe, mas não foi concebida para manter um histórico de alterações. Neste caso, pode simplesmente expandir da tabela existente para se tornarem temporais, conforme mostrado no exemplo seguinte:

````
ALTER TABLE WebsiteUserInfo 
ADD 
    ValidFrom datetime2 (0) GENERATED ALWAYS AS ROW START HIDDEN  
        constraint DF_ValidFrom DEFAULT DATEADD(SECOND, -1, SYSUTCDATETIME())
    , ValidTo datetime2 (0)  GENERATED ALWAYS AS ROW END HIDDEN   
        constraint DF_ValidTo DEFAULT '9999.12.31 23:59:59.99'
    , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo); 

ALTER TABLE WebsiteUserInfo  
SET (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.WebsiteUserInfoHistory));
GO

CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory
ON dbo.WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON); 
````

## <a name="step-2-run-your-workload-regularly"></a>Passo 2: Executar a carga de trabalho regularmente
A principal vantagem de tabelas temporais é que não é necessário alterar ou ajustar o seu Web site em qualquer forma de efetuar o registo de alterações. Depois de criado, tabelas temporais transparente manter versões de linha anterior sempre que efetuar alterações nos seus dados. 

Para poder tirar partido automática controlo de alterações para este cenário em particular, vamos apenas atualizar a coluna **PagesVisited** sempre quando o utilizador termina her/his sessão no Web site:

````
UPDATE WebsiteUserInfo  SET [PagesVisited] = 5 
WHERE [UserID] = 1;
````

É importante tenha em atenção que a consulta de atualização não precisa de saber o momento exato quando ocorreu a operação real nem a forma como os dados históricos serão mantidos para análise futura. Ambos os aspetos automaticamente são processados pela base de dados SQL do Azure. O diagrama seguinte ilustra como dados do histórico está a ser gerados em cada atualização.

![TemporalArchitecture](./media/sql-database-temporal-tables/AzureTemporal5.png)

## <a name="step-3-perform-historical-data-analysis"></a>Passo 3: Executar uma análise de dados históricos
Quando o sistema-versioning temporal está ativado, análise de dados históricos está agora apenas uma consulta na direção oposta ao. Neste artigo, fornecemos alguns exemplos que tratam cenários comuns de análise - para obter todos os detalhes, explore várias opções introduzidas com o [FOR SYSTEM_TIME](https://msdn.microsoft.com/library/dn935015.aspx#Anchor_3) cláusula.

Para ver os superiores 10 utilizadores ordenados pelo número de páginas web visitados a partir de uma hora há, execute esta consulta:

````
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
SELECT TOP 10 * FROM dbo.WebsiteUserInfo FOR SYSTEM_TIME AS OF @hourAgo
ORDER BY PagesVisited DESC
````

Pode facilmente modificar esta consulta para analisar as visitas de site a partir de um dia, há um mês há ou em qualquer momento no passado desejar.

Para executar análises estatísticas básicas para o dia anterior, utilize o seguinte exemplo:

````
DECLARE @twoDaysAgo datetime2 = DATEADD(DAY, -2, SYSUTCDATETIME());
DECLARE @aDayAgo datetime2 = DATEADD(DAY, -1, SYSUTCDATETIME());

SELECT UserID, SUM (PagesVisited) as TotalVisitedPages, AVG (PagesVisited) as AverageVisitedPages,
MAX (PagesVisited) AS MaxVisitedPages, MIN (PagesVisited) AS MinVisitedPages,
STDEV (PagesVisited) as StDevViistedPages
FROM dbo.WebsiteUserInfo 
FOR SYSTEM_TIME BETWEEN @twoDaysAgo AND @aDayAgo
GROUP BY UserId
````

Para procurar as atividades de um utilizador específico, num período de tempo, utilize a cláusula contidos em:

````
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
DECLARE @twoHoursAgo datetime2 = DATEADD(HOUR, -2, SYSUTCDATETIME());
SELECT * FROM dbo.WebsiteUserInfo 
FOR SYSTEM_TIME CONTAINED IN (@twoHoursAgo, @hourAgo)
WHERE [UserID] = 1;
````

Visualização de gráfico é especialmente útil para consultas temporais, que pode apresentar tendências e padrões de utilização um intuitiva forma muito facilmente:

![TemporalGraph](./media/sql-database-temporal-tables/AzureTemporal6.png)

## <a name="evolving-table-schema"></a>Evolução do esquema da tabela
Normalmente, terá de alterar o esquema de tabela temporal enquanto estão a fazer o desenvolvimento de aplicações. Para tal, basta executar regular ALTER TABLE instruções e a SQL Database do Azure irão adequadamente propagam alterações para a tabela de histórico. O script seguinte mostra como adicionar atributo adicionais para o controlo de:

````
/*Add new column for tracking source IP address*/
ALTER TABLE dbo.WebsiteUserInfo 
ADD  [IPAddress] varchar(128) NOT NULL CONSTRAINT DF_Address DEFAULT 'N/A';
````

Da mesma forma, pode alterar a definição de coluna enquanto a carga de trabalho está ativa:

````
/*Increase the length of name column*/
ALTER TABLE dbo.WebsiteUserInfo 
    ALTER COLUMN  UserName nvarchar(256) NOT NULL;
````

Por fim, pode remover uma coluna que não precisa de já.

````
/*Drop unnecessary column */
ALTER TABLE dbo.WebsiteUserInfo 
    DROP COLUMN TemporaryColumn; 
````

Em alternativa, utilize a versão mais recente [SSDT](https://msdn.microsoft.com/library/mt204009.aspx) para alterar o esquema de tabela temporal enquanto estiver ligado à base de dados (modo online) ou como parte do projeto de base de dados (modo offline).

## <a name="controlling-retention-of-historical-data"></a>Controlar a retenção de dados históricos
Com as tabelas temporais com versão do sistema, a tabela de histórico pode aumentar o tamanho da base de dados mais do que tabelas regulares. Uma tabela de histórico de grande e crescente pode tornar-se um problema ambos devido a custos de armazenamento puro, bem como impor um desempenho dedução dos impostos em consultas temporais. Por conseguinte, a desenvolver uma política de retenção de dados para a gestão de dados na tabela de histórico é um aspeto importante de planeamento e a gerir o ciclo de vida de cada tabela temporal. Base de dados SQL do Azure, tem as seguintes abordagens para a gestão de dados do histórico na tabela temporal:

* [A criação de partições de tabela](https://msdn.microsoft.com/library/mt637341.aspx#Anchor_2)
* [Script de limpeza personalizado](https://msdn.microsoft.com/library/mt637341.aspx#Anchor_3)

## <a name="next-steps"></a>Passos seguintes
Para obter informações detalhadas em tabelas temporais, veja [documentação MSDN](https://msdn.microsoft.com/library/dn935015.aspx).
Visite Channel 9 ouvi um [história de sucesso de temporal implemenation clientes reais](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions) e veja um [em direto demonstração temporal](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016).

