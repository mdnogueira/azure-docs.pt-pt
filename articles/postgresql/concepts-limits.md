---
title: "Limitações na base de dados do Azure para PostgreSQL | Microsoft Docs"
description: "Descreve as limitações na base de dados do Azure para PostgreSQL."
services: postgresql
author: kamathsun
ms.author: sukamat
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.custom: mvc
ms.topic: article
ms.date: 11/03/2017
ms.openlocfilehash: dbb88e033d5be73b7b069d69c095d8df2c1faf1b
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="limitations-in-azure-database-for-postgresql"></a>Limitações na base de dados do Azure para PostgreSQL
A base de dados do Azure para o serviço de PostgreSQL está em pré-visualização pública. As secções seguintes descrevem a capacidade e limites funcionais no serviço de base de dados.

## <a name="service-tier-maximums"></a>Valores máximos de camada de serviço
Base de dados do Azure para PostgreSQL tem vários escalões de serviço, que pode escolher durante a criação de um servidor. Para obter mais informações, consulte [compreender o que está disponível em cada camada de serviço](concepts-service-tiers.md).  

Não há um número máximo de ligações, unidades de computação e armazenamento em cada camada de serviço durante a pré-visualização do serviço, da seguinte forma: 

| | |
| :------------------------- | :---------------- |
| **Máx. ligações**        |                   |
| Unidades básicas de computação 50     | ligações de 50    |
| 100 unidades de computação básicas    | ligações de 100   |
| Unidades de 100 de computação padrão | ligações de 200   |
| Unidades padrão de computação de 200 | 300 ligações   |
| Unidades padrão de 400 de computação | 400 ligações   |
| Unidades padrão de 800 de computação | ligações de 500   |
| **Unidades de computação máx.**      |                   |
| Camada de serviços básicos         | 100 unidades de computação |
| Camada de serviços padrão      | 800 unidades de computação |
| **Armazenamento máximo**            |                   |
| Camada de serviços básicos         | 1 TB              |
| Camada de serviços padrão      | 1 TB              |

Quando são atingidas demasiadas ligações, poderá receber o erro seguinte:
> FATAL: Lamentamos, mas já demasiados clientes

## <a name="preview-functional-limitations"></a>Limitações de pré-visualização funcionais
### <a name="scale-operations"></a>Operações de dimensionamento
1.  Dimensionamento dinâmico de servidores em escalões de serviço não é atualmente suportada. Ou seja, alternar entre escalões de serviço básico e padrão.
2.  Dinâmico a pedido aumento do armazenamento no servidor previamente criada não é atualmente suportado.
3.  Não é suportada a diminuir o tamanho de armazenamento do servidor.

### <a name="server-version-upgrades"></a>Atualização de versão do servidor
- Automatizar a migração entre versões do motor de base de dados principal não é atualmente suportada.

### <a name="subscription-management"></a>Gestão de subscrição
- Mover dinamicamente servidores previamente criadas na subscrição e grupo de recursos não é atualmente suportada.

### <a name="point-in-time-restore"></a>Restauro para um ponto anterior no tempo
1.  Não é permitida a restaurar para a camada de serviço diferente e/ou tamanho de unidades de computação e armazenamento.
2.  Restaurar um servidor de ignorados não é suportada.

## <a name="next-steps"></a>Passos seguintes
- Compreender [que está disponível em cada escalão de preço](concepts-service-tiers.md)
- Compreender [versões de base de dados PostgreSQL](concepts-supported-versions.md)
- Reveja [como fazer cópias de segurança e restaurar um servidor na base de dados do Azure para PostgreSQL no portal do Azure](howto-restore-server-portal.md)
