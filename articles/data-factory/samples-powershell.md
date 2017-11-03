---
title: Exemplos do PowerShell do Azure do Azure Data Factory | Microsoft Docs
description: "Exemplos do PowerShell do Azure - Scripts para o ajudar a criar e gerir as fábricas de dados."
services: data-factory
author: spelluru
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2017
ms.author: spelluru
ms.openlocfilehash: f223cd8efbff77b02598293afd3b30a662d3abf3
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="azure-powershell-samples-for-azure-data-factory"></a>Exemplos do PowerShell do Azure para o Azure Data Factory

A tabela seguinte inclui ligações para os scripts de Azure PowerShell de exemplo para o Azure Data Factory.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em pré-visualização. Se estiver a utilizar a versão 1 do serviço do Data Factory, o que é geralmente disponível (DG), consulte [exemplos para a fábrica de dados version1](v1/data-factory-samples.md).

| |  |
|---|---|
|**Copiar os dados**||
|[Copiar os blobs a partir de uma pasta para outra pasta um armazenamento de Blobs do Azure](scripts/copy-azure-blob-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Este script do PowerShell copia blobs a partir de uma pasta no armazenamento de Blobs do Azure para outra pasta no armazenamento de Blobs do mesmo. |
|[Copiar dados do SQL Server no local para o Blob Storage do Azure](scripts/hybrid-copy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Este script do PowerShell copia dados a partir de uma base de dados do SQL Server no local para um armazenamento de Blobs do Azure. |
|[Cópia em massa](scripts/bulk-copy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Este script do PowerShell de exemplo copia dados a partir de várias tabelas na base de dados do SQL do Azure para um armazém de dados SQL do Azure. |
|[Cópia incremental](scripts/incremental-copy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Este script do PowerShell de exemplo carrega registos apenas novos ou atualizados a partir de um arquivo de dados de origem para um arquivo de dados dependente após a cópia inicial completa dos dados de origem para o sink. |
|**Transformar dados**||
|[Transformar dados através de um cluster do Spark](scripts/transform-data-spark-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Este script do PowerShell transforma dados através da execução de um programa num cluster do Spark. |
|**Comparação de precisão e shift pacotes SSIS para o Azure**||
|[Criar o tempo de execução de integração de SSIS do Azure](scripts/deploy-azure-ssis-integration-runtime-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Este PowerShell script aprovisiona um SSIS Azure integração runtime executado pacotes do SQL Server Integration Services (SSIS) no Azure. |



