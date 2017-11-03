---
title: Como trabalhar com as origens de dados 'macrodados' | Microsoft Docs
description: "Artigo procedimentos realce padrões para utilizar o catálogo de dados do Azure com origens de dados 'macrodados', incluindo o Blob Storage do Azure, Azure Data Lake e HDFS do Hadoop."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: 626d1568-0780-4726-bad1-9c5000c6b31a
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 08/15/2017
ms.author: maroche
ms.openlocfilehash: 001d80ce42f0e87276e59d70dffb75eb561d96cd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-work-with-big-data-sources-in-azure-data-catalog"></a>Como trabalhar com as origens de dados de grande no catálogo de dados do Azure
## <a name="introduction"></a>Introdução
**Catálogo de dados do Microsoft Azure** é um serviço em nuvem completamente gerido que funciona como um sistema de registo e de deteção de origens de dados empresariais. É tudo sobre Ajuda pessoas detetar, compreender e utilizar origens de dados e organizações de ajuda para obter o valor mais do que as respetivas origens de dados existente, incluindo macrodados.

**Catálogo de dados do Azure** suporta o registo de blobs de armazenamento do blogue do Azure e diretórios, bem como os ficheiros do HDFS do Hadoop e diretórios. A natureza semiestruturada destas origens de dados fornece uma enorme flexibilidade. No entanto, para obter o maior valor de registo-los com **catálogo de dados do Azure**, os utilizadores tem de considerar como estão organizadas de origens de dados.

## <a name="directories-as-logical-data-sets"></a>Diretórios como lógicos conjuntos de dados
É um padrão comum para organizar as origens de dados de grande tratar diretórios como lógicos conjuntos de dados. Diretórios de nível superior são utilizados para definir um conjunto de dados, enquanto as partições de definir subpastas e os ficheiros contêm armazenam dados propriamente ditos.

Um exemplo deste padrão de pode ser:

    \vehicle_maintenance_events
        \2013
        \2014
        \2015
            \01
                \2015-01-trailer01.csv
                \2015-01-trailer92.csv
                \2015-01-canister9635.csv
                ...
    \location_tracking_events
        \2013
        ...

Neste exemplo, vehicle_maintenance_events e location_tracking_events representam lógicos conjuntos de dados. Cada uma destas pastas contém ficheiros de dados que são organizados por ano e mês em subpastas. Cada uma destas pastas, potencialmente, pode conter centenas ou milhares de ficheiros.

Neste padrão, registar ficheiros individuais com **catálogo de dados do Azure** provavelmente não fazer sentido. Em vez disso, registe os diretórios que representam os conjuntos de dados ser significativo para os utilizadores a trabalhar com dados.

## <a name="reference-data-files"></a>Ficheiros de dados de referência
É um padrão complementares armazenar conjuntos de dados de referência como ficheiros individuais. Estes conjuntos de dados poderá considerar como o lado 'pequeno' de macrodados e, muitas vezes, são semelhantes às dimensões de um modelo de dados analíticos. Ficheiros de dados de referência contém registos que são utilizados para fornecer contexto para o volume dos ficheiros de dados armazenadas noutro local no arquivo de macrodados.

Um exemplo deste padrão de pode ser:

    \vehicles.csv
    \maintenance_facilities.csv
    \maintenance_types.csv

Quando um analista ou dados scientist está a trabalhar com os dados contidos em estruturas de diretório maior, os dados destes ficheiros de referência podem ser utilizados para fornecer informações mais detalhadas para entidades que são designados apenas por nome ou ID do conjunto de dados maiores.

Neste padrão, faz sentido para registar os ficheiros de dados de referência individuais com **catálogo de dados do Azure**. Cada ficheiro representa um conjunto de dados, e cada um deles pode ser anotado detetado individualmente.

## <a name="alternate-patterns"></a>Padrões alternativos
Os padrões descritos na secção anterior estão apenas duas formas possíveis que um arquivo de macrodados pode ser organizado, mas cada implementação é diferente. Independentemente da forma como são estruturadas as origens de dados, quando registar macrodados origens com **catálogo de dados do Azure**, concentre-se no registo de ficheiros e diretórios que representam os conjuntos de dados do valor para outras pessoas dentro da sua organização. Registar todos os ficheiros e diretórios pode clutter o catálogo, tornando mais difícil para os utilizadores que precisam de encontrar.

## <a name="summary"></a>Resumo
Registar origens de dados com **catálogo de dados do Azure** se tornarem fáceis de detetar e compreender. Ao registar e anotar os ficheiros de macrodados e diretórios que representam os conjuntos de dados lógicos, pode ajudar os utilizadores a localizar e utilizar as origens de dados grande que precisam.
