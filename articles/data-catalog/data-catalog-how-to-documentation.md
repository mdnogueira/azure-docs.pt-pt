---
title: Como documentar origens de dados | Microsoft Docs
description: "Artigo procedimentos realce como documentar recursos de dados no catálogo de dados do Azure."
services: data-catalog
documentationcenter: 
author: spelluru
manager: NA
editor: 
tags: 
ms.assetid: 053b1701-b848-4ada-b726-6f485caa9961
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 11/01/2017
ms.author: spelluru
ms.openlocfilehash: ea17dc0914966b9c3ca611c3d359635216ecf1ca
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="document-data-sources"></a>Documentar origens de dados
## <a name="introduction"></a>Introdução
**Catálogo de dados do Microsoft Azure** é um serviço em nuvem completamente gerido que funciona como um sistema de registo e de deteção de origens de dados empresariais. Por outras palavras, **catálogo de dados do Azure** está tudo sobre ajudando as pessoas detetar, *compreender*, utilize origens de dados e que ajuda as organizações para rentabilizar o valor a partir dos respetivos dados existentes.

Quando uma origem de dados é registada com **catálogo de dados do Azure**, os metadados é copiado e o serviço indexa, mas o bloco não termina não existe. **Catálogo de dados do Azure** também permite que os utilizadores forneçam os seus próprios documentação completa sobre que possa descrever a utilização e cenários comuns para a origem de dados.

No [como anotar origens de dados](data-catalog-how-to-annotate.md), saiba o que os especialistas que conhece a origem de dados podem anotá-lo com as etiquetas e uma descrição. O **catálogo de dados do Azure** portal inclui um editor de texto formatado para que os utilizadores podem totalmente documentar recursos de dados e contentores. O editor de inclui parágrafo formatação, tais como cabeçalhos, formatação de texto, apresenta uma lista com marcas, listas numeradas e tabelas.

As etiquetas e descrições são ótimos para anotações simples. No entanto, para ajudar a compreender melhor a utilização de uma origem de dados e cenários de negócios para uma origem de dados de consumidores de dados, um especialista pode fornecer documentação completa, detalhada. É fácil de uma origem de dados de documento. Selecione um recurso de dados ou o contentor e escolha **documentação**.

![](media/data-catalog-documentation/data-catalog-documentation.png)

## <a name="documenting-data-assets"></a>Documentar recursos de dados
A vantagem de **catálogo de dados do Azure** documentação permite-lhe utilizar o catálogo de dados como um repositório de conteúdo para criar uma narrativa completa dos seus recursos de dados. Pode explorar o conteúdo de detalhado que descreve a contentores e tabelas. Se já tiver conteúdo no repositório de conteúdo outro, por exemplo, SharePoint ou uma partilha de ficheiros, pode adicionar as ligações de documentação do recurso para fazer referência a este conteúdo existente. Esta funcionalidade torna mais detetável os documentos existentes.

> [!NOTE]
> Documentação não está incluída num índice de pesquisa.
>
>

![](media/data-catalog-documentation/data-catalog-documentation2.png)

O nível de documentação pode variar entre que descreve as características e o valor de um contentor de elemento de dados para uma descrição detalhada do esquema da tabela num contentor. O nível de documentação fornecida deve conduzido pelos necessidades comerciais. Mas em geral, seguem-se alguns os profissionais de TI e contras de documentar recursos de dados:

* Apenas um contentor de documentos: todo o conteúdo está no local, mas poderá não dispõem de detalhes necessários para os utilizadores tomar uma decisão informada.
* Apenas as tabelas de documentos: conteúdo é específico para esse objeto, mas os utilizadores têm de vários locais para documentos.
* Documente contentores e tabelas: abordagem mais abrangente, mas pode introduzir mais manutenção dos documentos.

## <a name="summary"></a>Resumo
Documentar origens de dados com **catálogo de dados do Azure** pode criar uma narrativa sobre os recursos de dados como muito mais detalhadamente conforme necessário.  Ao utilizar ligações, pode associar o conteúdo armazenado num repositório de conteúdo existente, que reúne o docs existentes e recursos de dados. Assim que os utilizadores detetar recursos de dados adequada, estes podem ter um conjunto completo de documentação.
