---
title: "Versões na base de dados do Azure para PostgreSQL | Microsoft Docs"
description: "Descreve as versões suportadas na base de dados do Azure para PostgreSQL."
services: postgresql
author: kamathsun
ms.author: sukamat
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 11/03/2017
ms.openlocfilehash: 646e95f5c3c7bc9bb175a5532435b28bb998b49b
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2017
---
# <a name="supported-postgresql-database-versions"></a>Versões de base de dados PostgreSQL suportados
Microsoft visa suportar n-2 versões do motor PostgreSQL a base de dados do Azure PostgreSQL serviço, que significa que a versão principal atualmente lançada (n) e as dois principais as versões anteriores (-2).

Base de dados do Azure para PostgreSQL atualmente suporta as seguintes versões:

## <a name="postgresql-version-962"></a>Versão PostgreSQL 9.6.2
Consulte o [PostgreSQL documentação](https://www.postgresql.org/docs/9.6/static/release-9-6-2.html) para saber mais sobre os melhoramentos e correções PostgreSQL 9.6.2.

## <a name="postgresql-version-957"></a>Versão PostgreSQL 9.5.7
Consulte o [PostgreSQL documentação](https://www.postgresql.org/docs/9.5/static/release-9-5-7.html) para saber mais sobre os melhoramentos e correções no PostgreSQL 9.5.7.

## <a name="managing-updates-and-upgrades"></a>Gestão de atualizações e melhoramentos
Base de dados do Azure para PostgreSQL gere automaticamente a aplicação de patches para atualizações de versões de secundárias. Atualmente, em pré-visualização pública, atualização de versão principal não é suportada. Por exemplo, a atualização do PostgreSQL 9.5 para PostgreSQL 9.6 não é suportada.

## <a name="next-steps"></a>Passos seguintes
Para informações sobre o suporte de extensões de PostgreSQL diferentes, consulte [PostgreSQL extensões](concepts-extensions.md)
