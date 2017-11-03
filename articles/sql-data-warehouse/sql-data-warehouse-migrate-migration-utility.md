---
title: "Migrar: Do armazém de dados utilitário de migração | Microsoft Docs"
description: "Migre para o armazém de dados do SQL Server."
services: sql-data-warehouse
documentationcenter: NA
author: sqlmojo
manager: jhubbard
editor: 
ms.assetid: 4d7ad981-ef31-4513-b337-50bdc4709c09
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: migrate
ms.date: 10/31/2016
ms.author: joeyong;barbkess
ms.openlocfilehash: 2466e823c448ada4dc7bc5769b1b7f10bbb5dc7d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="data-warehouse-migration-utility-preview"></a>Utilitário de migração do armazém de dados (pré-visualização)
> [!div class="op_single_selector"]
> * [Transferir o utilitário de migração][Download Migration Utility]
> 
> 

O utilitário de migração do Data Warehouse é uma ferramenta concebida para migrar o esquema e dados do SQL Server e SQL Database do Azure ao Azure SQL Data Warehouse. Durante a migração de esquema, a ferramenta automaticamente mapeia o esquema correspondente a partir da origem para destino. Depois do esquema tiver sido migrado, as ferramentas proporciona uma opção de mover os dados com scripts gerados automaticamente.

Além da migração de esquema e de dados, esta ferramenta dá-lhe a opção para gerar relatórios de compatibilidade que resumem incompatibilidades entre as instâncias de origem e de destino que possam impedir a migração simplificada.

## <a name="get-started"></a>Introdução
Como um pré-requisito de instalação, terá do utilitário BCP de linha de comandos para executar scripts de migração e Office para ver o relatório de compatibilidade. Depois de iniciar o executável que é transferido lhe-á para aceitar o EULA uma padrão para a ferramenta irá ser instalada.

Além disso, para executar a migração Utiliy, terá da na base de dados que pretender para migrar as seguintes permissões: criar a base de dados, alterar qualquer base de dados ou definição de qualquer vista.

### <a name="launching-the-tool-and-connecting"></a>Iniciar a ferramenta e ligar
Inicie a ferramenta clicando no ícone de ambiente de trabalho que aparece pós-instalação. Depois de abrir a ferramenta, será solicitado com uma página de ligação inicial, onde pode escolher a origem e destino para a ferramenta de migração. Neste momento, suportamos do SQL Server e SQL Database do Azure como origens e do armazém de dados do SQL Server como um destino. Depois de selecionar esta será pedido para ligar ao seu servidor de origem ao preencher no nome do servidor e autenticação e, em seguida, clicando em "Ligar".

Após a autenticação, a ferramenta apresentará uma lista de bases de dados que estão presentes no servidor que está ligado ao. Pode começar a migração, selecionando uma base de dados que pretende migrar e, em seguida, clicando em 'Migrar selecionado'.

## <a name="migration-report"></a>Relatório de migração
Selecionar 'Verificar compatibilidade de base de dados' na ferramenta irá gerar um relatório resumir todos os incompatibilidades de objeto na base de dados pedido para migrar. Uma lista mais ampla de algumas das funcionalidades do SQL Server que não está presente no SQL Data Warehouse pode ser encontrada na nossa [documentação de migração][migration documentation]. Depois do relatório é gerado conseguirá guardar e abrir o relatório no Excel.

Tenha em atenção que ao gerar o esquema de migração, a maioria dos problemas identificados como 'Object' será ajustada para permitir a migração imediata desses dados. Reveja as alterações para se certificar de que pretende efetuar ajustes adicionais antes de aplicar o esquema.

## <a name="migrate-schema"></a>Migrar esquema
Depois de ligar, selecionar 'Schema migrar' irá gerar um script de migração de esquema para as tabelas selecionadas. Portas este script a estrutura da tabela, dados incompatíveis maps tipos formulários mais compatível e cria as credenciais de segurança e de esquema se esta situação é indicada pelo utilizador nas definições de migração. Este código pode ser executado contra a instância de armazém de dados do SQL Server visada, guardados num ficheiro, copiado para a sua área de transferência ou mesmo editá-lo na linha antes de efetuar qualquer ação adicional.  

Como mencionado acima, quando migrar revisão do esquema a migração, as alterações que a ferramenta efetuou para garantir que que totalmente compreende-las.  

## <a name="migrate-data"></a>Migrar dados
Ao clicar na opção 'Migrar dados' pode gerar scripts BCP que irão mover os dados pela primeira vez para ficheiros simples no seu servidor, e, em seguida, diretamente para o SQL Data Warehouse. Recomendamos que este processo para pequenas quantidades de dados e, como tentativas de mover não são incorporados e falhas podem ocorrer se houver uma perda de ligação de rede. Para executar este, terá de ter o utilitário BCP de linha de comandos instalado e o esquema para os dados já deverá ter sido criado.

Depois de ter preenchidos parâmetros acima simplesmente tem de clicar em execução de migração e será gerado um conjunto de dois pacotes para a localização especificada. Execute o ficheiro de exportação para exportar os dados da sua origem de migração para ficheiros simples e execute o ficheiro de importação para importar os seus dados para o SQL Data Warehouse.

## <a name="next-steps"></a>Passos seguintes
Agora que tenha migrado alguns dados, consulte como [desenvolver][develop].

<!--Image references-->

<!--Article references-->
[migration documentation]: sql-data-warehouse-overview-migrate.md
[develop]: sql-data-warehouse-overview-develop.md

<!--Other Web references--> 
[Download Migration Utility]: https://migrhoststorage.blob.core.windows.net/sqldwsample/DataWarehouseMigrationUtility.zip
