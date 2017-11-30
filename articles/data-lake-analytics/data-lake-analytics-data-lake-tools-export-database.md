---
title: Como exportar bases de dados do U-SQL com o Azure Data Lake Tools para Visual Studio | Microsoft Docs
description: "Saiba como utilizar o Azure Data Lake Tools para Visual Studio para exportar a base de dados do U-SQL e importá-la à conta local, ao mesmo tempo."
services: data-lake-analytics
documentationcenter: 
author: yanancai
manager: 
editor: 
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/27/2017
ms.author: yanacai
ms.openlocfilehash: 9f11e07f7fbaf2b708d6fbc8a746238745132bda
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2017
---
# <a name="how-to-export-u-sql-database"></a>Como exportar a base de dados do U-SQL

Neste documento, irá aprender a utilizar [ferramentas do Azure Data Lake para Visual Studio](http://aka.ms/adltoolsvs) para exportar a base de dados do U-SQL como um script único do U-SQL e de recursos transferidos. Importar a base de dados exportado para a conta local também é suportado no mesmo processo.

Os clientes, normalmente, mantêm vários ambientes de desenvolvimento, teste e produção. Nestes ambientes alojados na conta local no computador local de programadores e conta do Azure Data Lake Analytics no Azure. Quando desenvolver e otimização de consultas de U-SQL em ambientes de desenvolvimento e teste, é comum que os programadores necessitam recriá-tudo na base de dados de produção. **Assistente para exportar a base de dados** ajuda a acelerar este processo. Utilizando o assistente, os programadores podem clonar o ambiente de base de dados existente e dados de exemplo para outras contas de Azure Data Lake Analytics.

## <a name="export-steps"></a>Passos de exportação

### <a name="step-1-right-click-the-database-in-server-explorer-and-click-export"></a>Passo 1: A base de dados no Explorador de servidores com o botão direito e clique em "Exportar..."

Todas as contas de Azure Data Lake Analytics tem permissão para estão listados no Explorador de servidores. Expanda um contém a base de dados que pretende exportar e faça duplo clique na base de dados para escolher **exportar...** . Se não encontrar o menu de contexto, terá de [a ferramenta de atualização para a versão lasted](http://aka.ms/adltoolsvs).

![Base de dados de exportação de ferramentas do Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-export-database/export-database.png)

### <a name="step-2-configure-the-objects-you-want-to-export"></a>Passo 2: Configurar os objetos que pretende exportar

Por vezes, uma base de dados é grande, mas precisa apenas numa pequena parte do mesmo e, em seguida, pode configurar o subconjunto de objetos que pretende exportar no Assistente de exportação. Tenha em atenção que a ação de exportação é concluída ao executar uma tarefa de U-SQL e, por conseguinte, exportar a partir da conta do Azure implica alguns custos.

![Assistente de base de dados do Data Lake Analytics ferramentas exportar](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard.png)

### <a name="step-3-check-the-objects-list-and-more-configurations"></a>Passo 3: Verificar a lista de objetos e configurações mais

Neste passo, pode verificar duplo os objetos selecionados na parte superior da caixa de diálogo. Se existirem alguns erros, pode clicar em anterior para voltar atrás e configurar os objetos que pretende exportar de novo.

Também pode efetuar outras configurações sobre o destino de exportação, as descrições destas configurações estão listadas na tabela abaixo:

|Configuração|Descrição|
|-------------|-----------|
|Nome de destino|Este nome indica onde pretende guardar os recursos de base de dados exportada, como as assemblagens, ficheiros adicionais e dados de exemplo. Será criada uma pasta com este nome na sua pasta de raiz de dados local.|
|Diretório do projeto|Este caminho define onde pretende guardar o script U-SQL exportado, que inclui todas as definições de objetos de base de dados.|
|Apenas o esquema|A seleção desta opção resulta em apenas definições de base de dados e recursos (como assemblagens e ficheiros adicionais) que está a ser exportado.|
|Esquema e de dados|A seleção desta opção resulta em definições de base de dados, recursos e os dados para ser exportada. As linhas N superiores de tabelas são exportadas.|
|Importar automaticamente a base de dados Local|A verificar esta configuração significa que a base de dados exportada será importada para a base de dados local automaticamente depois de concluída a exportar.|

![Configuração de Assistente de base de dados do Data Lake Analytics ferramentas exportação](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-configuration.png)

### <a name="step-4-check-the-export-results"></a>Passo 4: Verificar os resultados de exportação

Depois de todas estas definições e progresso de exportação, pode encontrar os resultados exportados a partir da janela de registo no assistente. Através do registo marcado pelo retângulo vermelho na captura de ecrã blow, pode encontrar a localização dos exportado U-SQL script e de base de dados de recursos, incluindo as assemblagens, ficheiros adicionais e dados de exemplo.

![Data Lake Analytics ferramentas exportação da base de dados assistente foi concluído](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-completed.png)

## <a name="how-to-import-the-exported-database-to-local-account"></a>Como importar a base de dados exportada à conta local

A forma mais conveniente para fazer este importar está a verificar **importação automaticamente da base de dados Local** durante o progresso exportar no passo 3. Se esqueceu a para o fazer, pode localizar o script U-SQL exportado através do registo de exportar e execute o script U-SQL localmente para importar a base de dados para a conta local.

## <a name="how-to-import-the-exported-database-to-azure-data-lake-analytics-account"></a>Como importar a base de dados exportado para a conta do Azure Data Lake Analytics

Para importar a base de dados para outra conta do Azure Data Lake Analytics, precisa de dois passos:

1. Carregar os recursos exportados, incluindo as assemblagens, ficheiros adicionais e dados de exemplo para a conta do Azure Data Lake Store predefinida da conta que pretende importar para o Azure Data Lake Analytics. Pode localizar a pasta de recurso exportado na pasta raiz de dados local e carregar toda a pasta para a raiz da conta de armazenamento predefinido.
2. O script U-SQL exportado para a conta do Azure Data Lake Analytics que pretende importar base de dados depois de concluir o carregamento de envio.

## <a name="known-limitation"></a>Limitação conhecida

Atualmente, se tiver selecionado **esquema e dados** no assistente, a ferramenta é executada uma tarefa de U-SQL para exportar os dados armazenados nas tabelas. É por esse motivo os processo de exportação de dados foi lenta e implicar custos. 

## <a name="next-steps"></a>Passos seguintes

* [Compreender a base de dados do U-SQL](https://msdn.microsoft.com/library/azure/mt621299.aspx) 
* [como testar e depurar tarefas U-SQL através da utilização de execução local e o SDK do Azure Data Lake U-SQL](data-lake-analytics-data-lake-tools-local-run.md)


