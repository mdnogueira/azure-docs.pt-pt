---
title: "Assistente de cópia do Azure Data Factory | Microsoft Docs"
description: "Saiba mais sobre como utilizar o Assistente de cópia de Azure do Data Factory para copiar dados de origens de dados suportadas para sinks."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 0974eb40-db98-4149-a50d-48db46817076
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: spelluru
robots: noindex
ms.openlocfilehash: 3f0b296b0bfb762607fdb4b42151b77a9e388a18
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2017
---
# <a name="azure-data-factory-copy-wizard"></a>Assistente de cópia de Factory de dados do Azure
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory, que está geralmente disponível (GA). 

O Assistente de cópia do Azure Data Factory facilita o processo de ingestão relacionados dados, que é normalmente um primeiro passo para um cenário de integração de dados ponto-a-ponto. Ao percorrer o Assistente de cópia de fábrica de dados do Azure, não terá de compreender as definições de JSON para serviços ligados, conjuntos de dados e pipelines. O assistente cria automaticamente um pipeline para copiar dados de origem de dados selecionada para o destino selecionado. Além disso, o Assistente de cópia ajuda-o a validar os dados que está a ser ingeridos no momento da criação. Isto poupa tempo, especialmente quando está ingestão de dados pela primeira vez da origem de dados. Para iniciar o Assistente de cópia, clique em de **copiar dados** mosaico na home page da fábrica de dados.

![Assistente de Cópia](./media/data-factory-copy-wizard/copy-data-wizard.png)

## <a name="designed-for-big-data"></a>Concebido para macrodados
Este assistente permite-lhe mover facilmente dados de uma ampla variedade de origens para destinos em minutos. Depois de passar através do assistente, um pipeline com uma atividade de cópia é criado automaticamente para si, juntamente com dependentes entidades do Data Factory (serviços ligados e conjuntos de dados). Não existem passos adicionais são necessários para criar o pipeline.   

![Selecione a origem de dados](./media/data-factory-copy-wizard/select-data-source-page.png)

> [!NOTE]
> Para obter instruções passo a passo Criar um exemplo de pipeline para copiar dados de um Azure blob para uma tabela de base de dados do Azure SQL, consulte o [tutorial do Assistente para copiar](data-factory-copy-data-wizard-tutorial.md).
>
>

O assistente foi concebido com macrodados em mente desde o início, com suporte para diversos dados e os tipos de objeto. Pode criar pipelines de fábrica de dados que passam centenas de pastas, ficheiros ou tabelas. O assistente suporta pré-visualização de dados automática, captura de esquema e mapeamento e filtragem de dados.

## <a name="automatic-data-preview"></a>Pré-visualização de dados automática
Pode pré-visualizar a parte dos dados da origem de dados selecionada para validar se os dados são o que pretende copiar. Além disso, se a origem de dados num ficheiro de texto, o Assistente para copiar analisa o ficheiro de texto para saber os delimitadores de linha e coluna e esquema automaticamente.

![Definições do formato de ficheiro](./media/data-factory-copy-wizard/file-format-settings.png)

## <a name="schema-capture-and-mapping"></a>Captura de esquema e mapeamento
O esquema de dados de entrada pode não corresponder o esquema de dados de saída em alguns casos. Neste cenário, é necessário mapear colunas de esquema de origem para colunas de esquema de destino.

> [!TIP]
> Quando copiar dados do SQL Server ou SQL Database do Azure para o Azure SQL Data Warehouse, se a tabela não existe no arquivo de destino, o Data Factory suporta a criação de tabela automática utilizando o esquema da origem. Saiba mais de [mover dados para e do armazém de dados SQL do Azure utilizando o Azure Data Factory](./data-factory-azure-sql-data-warehouse-connector.md).
>

Utilize na lista pendente para selecionar uma coluna do esquema de origem para mapear para uma coluna do esquema de destino. O Assistente de cópia tenta compreender o padrão para mapeamento de colunas. Aplica-se o mesmo padrão para o resto das colunas, para que não é necessário selecionar cada uma das colunas individualmente para concluir o mapeamento de esquema. Se preferir, pode substituir estes mapeamentos utilizando na lista pendente para mapear as colunas um por um. O padrão torna-se mais exato como mapear mais colunas. O Assistente para copiar constantemente atualiza o padrão e, em última análise atinge o padrão de direita para o mapeamento de colunas que pretende alcançar.     

![Mapeamento de esquema](./media/data-factory-copy-wizard/schema-mapping.png)

## <a name="filtering-data"></a>Filtragem de dados
Pode filtrar os dados de origem para selecionar apenas os dados que tem de ser copiados para o arquivo de dados do sink. Filtragem reduz o volume dos dados a ser copiado para o arquivo de dados do sink e, por conseguinte, melhora o débito da operação de cópia. Fornece uma forma flexível de dados de filtro na base de dados relacional utilizando a linguagem de consulta SQL ou ficheiros numa pasta BLOBs do Azure utilizando [funções de Data Factory e variáveis](data-factory-functions-variables.md).   

### <a name="filtering-of-data-in-a-database"></a>Filtragem de dados numa base de dados
A seguinte captura de ecrã mostra uma consulta do SQL Server utilizando o `Text.Format` função e `WindowStart` variável.

![Validar expressões](./media/data-factory-copy-wizard/validate-expressions.png)

### <a name="filtering-of-data-in-an-azure-blob-folder"></a>Filtragem de dados numa pasta BLOBs do Azure
Pode utilizar variáveis no caminho da pasta para copiar dados a partir de uma pasta que é determinada em runtime com base no [variáveis do sistema](data-factory-functions-variables.md#data-factory-system-variables). As variáveis suportadas são: **{year}**, **{month}**, **{day}**, **{hora}**, **{minuto}**e  **{personalizado}** . Por exemplo: inputfolder / {year} / {month} / {day}.

Suponha que tem de entrada pastas no seguinte formato:

    2016/03/01/01
    2016/03/01/02
    2016/03/01/03
    ...

Clique em de **procurar** botão para **ficheiro ou pasta**, navegue para uma destas pastas (por exemplo, 2016->-03 -> 01 -> 02) e clique em **escolha**. Deverá ver `2016/03/01/02` na caixa de texto. Agora, substitua **2016** com **{year}**, **03** com **{month}**, **01** com **{day}** , e **02** com **{hora}**e prima a **separador** chave. Deverá ver na lista pendente para selecionar o formato para estas quatro variáveis:

![Utilizar variáveis de sistema](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)   

Conforme mostrado na captura de ecrã seguinte, também pode utilizar um **personalizado** variável e quaisquer [suportado cadeias de formato](https://msdn.microsoft.com/library/8kb3ddd4.aspx). Para selecionar uma pasta com essa estrutura, utilize o **procurar** botão primeiro. Em seguida, substituir um valor com **{personalizado}**e prima a **separador** chave para ver a caixa de texto, onde pode introduzir a cadeia de formato.     

![Utilizar a variável personalizada](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)

## <a name="scheduling-options"></a>Opções de agendamento
Pode executar a operação de cópia uma vez ou com base numa agenda (hora a hora, diariamente, e assim sucessivamente). Ambas estas opções podem ser utilizadas para o volume dos conectores em ambientes, incluindo no local, de nuvem e de cópia de ambiente de trabalho local.

Uma operação de cópia de uso individual permite que o movimento de dados de uma origem para um destino apenas uma vez. Aplica-se aos dados de qualquer dimensão e qualquer formato suportado. A cópia agendada permite-lhe copiar dados de uma periodicidade prevista. Pode utilizar as definições avançadas (como tentativas, o tempo limite e alertas) para configurar a cópia agendada.

![Propriedades de agendamento](./media/data-factory-copy-wizard/scheduling-properties.png)

## <a name="next-steps"></a>Passos seguintes
Para instruções rápidas de utilizar o Assistente de cópia do Data Factory para criar um pipeline com atividade de cópia, consulte [Tutorial: criar um pipeline com o Assistente para copiar](data-factory-copy-data-wizard-tutorial.md).
