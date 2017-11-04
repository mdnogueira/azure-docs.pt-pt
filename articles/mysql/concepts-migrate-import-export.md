---
title: Importar e exportar na base de dados do Azure para MySQL | Microsoft Docs
description: Este artigo explica formas comuns de importar e exportar bases de dados na base de dados do Azure para MySQL, utilizando ferramentas como o MySQL Workbench.
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 11/02/2017
ms.openlocfilehash: 36ffa7082ce60093cbd90d0c12187e28f517646d
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2017
---
# <a name="migrate-your-mysql-database-by-using-import-and-export"></a>Migrar a base de dados MySQL, utilizando a importação e exportação
Este artigo explica duas abordagens comuns para importar e exportar dados para uma base de dados do Azure para o servidor de MySQL utilizando MySQL Workbench. 

## <a name="before-you-begin"></a>Antes de começar
Para seguir este guia de procedimentos, tem de:
- Uma base de dados do Azure para o servidor de MySQL, seguindo [criar uma base de dados do Azure para o servidor de MySQL utilizando o portal do Azure](quickstart-create-mysql-server-database-using-azure-portal.md).
- MySQL Workbench [transferido](https://dev.mysql.com/downloads/workbench/), ou outra ferramenta MySQL para importar e exportar.

## <a name="use-common-tools"></a>Utilizar ferramentas comuns
Utilize ferramentas comuns, como o Workbench do MySQL, Toad ou Navicat remotamente ligar e importar ou exportar dados na base de dados do Azure para MySQL. 

Utilize estas ferramentas no seu computador cliente com uma ligação à Internet para ligar à base de dados do Azure para MySQL. Utilizar uma ligação encriptada por SSL para melhores práticas de segurança, conforme descrito em [conectividade de configurar o SSL na base de dados do Azure para MySQL](concepts-ssl-connection-security.md).

Não é necessário mover a importar e exportar ficheiros de qualquer localização nuvem especiais ao migrar a base de dados do Azure para MySQL. 

## <a name="create-a-database-on-the-azure-database-for-mysql-server"></a>Criar uma base de dados na base de dados do Azure para o servidor de MySQL
Crie uma base de dados vazio na base de dados do Azure para o servidor de MySQL onde pretende migrar os dados. Utilize uma ferramenta como o Workbench do MySQL, Toad ou Navicat para criar a base de dados. A base de dados pode ter o mesmo nome que a base de dados que contém os dados capturados, ou pode criar uma base de dados com um nome diferente.

Para obter ligado, localize as informações de ligação no **propriedades** painel na base de dados do Azure para MySQL.

![Localizar as informações de ligação no portal do Azure](./media/concepts-migrate-import-export/1_server-properties-name-login.png)

Adicione as informações de ligação à MySQL Workbench.

![Cadeia de ligação do MySQL Workbench](./media/concepts-migrate-import-export/2_setup-new-connection.png)

## <a name="determine-when-to-use-import-and-export-techniques-instead-of-a-dump-and-restore"></a>Determinar quando deve utilizar Importar e exportar técnicas em vez de uma captura e restaurar
Utilize ferramentas de MySQL para importar e exportar bases de dados na base de dados do Azure MySQL nos seguintes cenários. Noutros cenários, poderá beneficiar da utilização de [informação do Estado e restaurar](concepts-migrate-dump-restore.md) abordagem em vez disso. 

- Quando tem de escolher seletivamente alguns tabelas para importar a partir de uma base de dados MySQL existente na base de dados do Azure MySQL, é melhor utilizar a importação e exportação técnica.  Ao fazê-lo, pode omitir quaisquer tabelas desnecessárias da migração para poupar tempo e recursos. Por exemplo, utilizar o `--include-tables` ou `--exclude-tables` mudar com [mysqlpump](https://dev.mysql.com/doc/refman/5.7/en/mysqlpump.html#option_mysqlpump_include-tables) e `--tables` mudar com [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html#option_mysqldump_tables).
- Quando estiver a mover os objetos de base de dados diferente de tabelas, crie explicitamente esses objetos. Inclua restrições (chave primária, chave externa, índices), vistas, funções, procedimentos, acionadores e quaisquer outros objetos de base de dados que pretende migrar.
- Quando estiver a migrar dados a partir de origens de dados externos que não seja uma base de dados MySQL, criar ficheiros simples e importá-los utilizando [mysqlimport](https://dev.mysql.com/doc/refman/5.7/en/mysqlimport.html).

Certifique-se de que todas as tabelas na base de dados utilizam o motor de armazenamento InnoDB quando estiver a carregar os dados na base de dados do Azure para MySQL. Base de dados do Azure para MySQL suporta apenas InnoDB motor de armazenamento, pelo que não suporta os motores de armazenamento alternativo. Se as tabelas necessitam de motores de armazenamento alternativo, lembre-se de que convertê-los para utilizar o formato do motor de InnoDB antes da migração para a base de dados do Azure para MySQL. 

Por exemplo, se tiver uma WordPress ou uma aplicação web que utiliza o motor de MyISAM, comece por converter as tabelas ao migrar os dados em tabelas de InnoDB. Em seguida, restaure a base de dados do Azure para MySQL. Utilize a cláusula `ENGINE=INNODB` para definir o motor para criar uma tabela e, em seguida, transferir os dados na tabela compatível antes da migração. 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```

## <a name="performance-recommendations-for-import-and-export"></a>Recomendações de desempenho para importação e exportação
-   Crie os índices em cluster e as chaves primárias antes de carregar dados. Carregar os dados por ordem de chave primária. 
-   Atraso de criação de índices secundários até depois de dados foi carregada. Crie todos os índices secundários após o carregamento. 
-   Desative as restrições de chave externa antes de carregar. Desativar verificações de chaves externas proporciona ganhos de desempenho significativas. Ative as restrições e verificar os dados após o carregamento para garantir a integridade referencial.
-   Carregar os dados em paralelo. Evite demasiada paralelismo que iria fazer com que atingiu o limite de um recurso e monitorizar recursos utilizando as métricas disponíveis no portal do Azure. 
-   Utilize as tabelas particionadas quando for adequado.

## <a name="import-and-export-by-using-mysql-workbench"></a>Importar e exportar utilizando MySQL Workbench
Existem duas formas de exportação e importação de dados MySQL Workbench. Cada capacidade serve um objetivo de diferentes. 

### <a name="table-data-export-and-import-wizards-from-the-object-browsers-context-menu"></a>Os dados da tabela de exportação e importação assistentes a partir do menu de contexto do browser de objeto
![Assistentes de MySQL Workbench no menu de contexto do browser de objeto](./media/concepts-migrate-import-export/p1.png)

Os assistentes para dados da tabela suportam a importação e exportação operações utilizando ficheiros CSV e JSON. Incluem várias opções de configuração, tais como os separadores, seleção de coluna e seleção de codificação. Pode efetuar cada assistente contra locais ou remotamente ligados servidores MySQL. Inclui a ação de importação de tabela, coluna e mapeamento de tipos. 

Pode aceder a estes assistentes a partir do menu de contexto do browser de objeto clicando com uma tabela. Em seguida, escolha o **Assistente de exportação de dados em tabela** ou **Assistente de importação de dados de tabela**. 

#### <a name="table-data-export-wizard"></a>Assistente de exportação de dados de tabela
O exemplo seguinte exporta a tabela para um ficheiro CSV: 
1. Clique com botão direito a tabela da base de dados para ser exportada. 
2. Selecione **Assistente de exportação de dados de tabela**. Selecione as colunas a ser exportado, linha desvio (se aplicável) e contagem (se aplicável). 
3. No **selecionar dados para a exportação de** página, clique em **seguinte**. Selecione o caminho do ficheiro, o tipo de ficheiro CSV ou JSON. Selecione também o separador de linha, o método de envolvente cadeias e como separador de campo. 
4. No **localização do ficheiro de saída selecione** página, clique em **seguinte**. 
5. No **exportar dados** página, clique em **seguinte**.

#### <a name="table-data-import-wizard"></a>Assistente de importação de dados de tabela
O exemplo seguinte importa a tabela a partir de um ficheiro CSV:
1. Clique com botão direito a tabela da base de dados a serem importados. 
2. Procure e selecione o ficheiro CSV para importar e, em seguida, clique em **seguinte**. 
3. Selecione a tabela de destino (nova ou existente) e selecione ou desmarque o **tabela Truncate antes da importação** caixa de verificação. Clique em **Seguinte**.
4. Selecione a codificação e as colunas a ser importados e, em seguida, clique em **seguinte**. 
5. No **importar dados** página, clique em **seguinte**. O assistente importa os dados em conformidade.

### <a name="sql-data-export-and-import-wizards-from-the-navigator-pane"></a>Dados do SQL Server exportar e importar assistentes a partir do painel de navegador
Utilize um Assistente para exportar ou importar SQL gerado a partir do MySQL Workbench ou gerada do comando mysqldump. Aceder a estes assistentes do **navegador** painel ou selecionando **servidor** no menu principal. Em seguida, selecione **exportar dados** ou **importação de dados**. 

#### <a name="data-export"></a>Exportação de dados
![Exportação de dados MySQL Workbench através do painel de navegador](./media/concepts-migrate-import-export/p2.png)

Pode utilizar o **exportar dados** separador para exportar os dados MySQL. 
1. Selecione cada esquema que pretende exportar, opcionalmente, escolha objetos/tabelas de esquema específicos de cada esquema e gerar a exportação. Opções de configuração incluem a exportação para uma pasta do projeto ou ficheiro autónomo do SQL Server, informação do Estado armazenadas rotinas e eventos ou ignorar os dados da tabela. 
 
   Em alternativa, utilize **exportar um conjunto de resultados** para exportar um resultado específico definido no editor de SQL Server para outro formato, tais como CSV, JSON, HTML e XML. 
3. Selecione os objetos de base de dados para exportar e configurar as opções relacionadas.
4. Clique em **atualizar** ao carregar os objetos atuais.
5. Opcionalmente, abra o **opções avançadas** separador para refinar a operação de exportação. Por exemplo, adicione as bloqueios de tabela, utilize substituir em vez de instruções insert e aspas identificadores com backtick carateres.
6. Clique em **iniciar exportar** para iniciar o processo de exportação.


#### <a name="data-import"></a>Importação de dados
![Importação de dados MySQL Workbench utilizando navegador de gestão](./media/concepts-migrate-import-export/p3.png)

Pode utilizar o **de importação de dados** separador para importar ou restaurar exportar dados da operação de exportação de dados ou do comando mysqldump. 
1. Escolha a pasta do projeto ou o ficheiro autónomo do SQL Server, escolha o esquema para importar para ou escolha **novo** para definir um esquema de novo. 
2. Clique em **iniciar Importar** para iniciar o processo de importação.

## <a name="next-steps"></a>Passos seguintes
Como outra abordagem de migração, leia [migrar a base de dados MySQL utilizar informação do Estado e o restauro na base de dados do Azure para MySQL](concepts-migrate-dump-restore.md). 
