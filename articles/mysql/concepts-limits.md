---
title: "Limitações na base de dados do Azure para MySQL | Microsoft Docs"
description: "Descreve as limitações de pré-visualização na base de dados do Azure para MySQL."
services: mysql
author: jasonh
ms.author: kamathsun
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 10/26/2017
ms.openlocfilehash: b3fba38cacf5b5abcdea7f0def8c1d39e653f0a8
ms.sourcegitcommit: 3e3a5e01a5629e017de2289a6abebbb798cec736
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2017
---
# <a name="limitations-in-azure-database-for-mysql-preview"></a>Limitações na base de dados do Azure para MySQL (pré-visualização)
A base de dados do Azure para o serviço de MySQL está em pré-visualização pública. As secções seguintes descrevem a capacidade e limites funcionais no serviço de base de dados. Consulte também [limitações gerais](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.6/en/limits.html) aplicável para o motor de base de dados MySQL.

## <a name="service-tier-maximums"></a>Valores máximos de camada de serviço
Base de dados do Azure para MySQL tem vários escalões de serviço à sua escolha durante a criação de um servidor. Para obter mais informações, consulte [compreender o que está disponível em cada camada de serviço](concepts-service-tiers.md).  

Não há um número máximo de ligações, unidades de computação e armazenamento em cada camada de serviço durante a pré-visualização, da seguinte forma: 

|                            |                   |
| :------------------------- | :---------------- |
| **Máx. ligações**        |                   |
| Unidades básicas de computação 50     | ligações de 50    |
| 100 unidades de computação básicas    | ligações de 100   |
| Unidades de 100 de computação padrão | ligações de 200   |
| Unidades padrão de computação de 200 | 400 ligações   |
| Unidades padrão de 400 de computação | 800 ligações   |
| Unidades padrão de 800 de computação | ligações de 1600  |
| **Unidades de computação máx.**      |                   |
| Camada de serviços básicos         | 100 unidades de computação |
| Camada de serviços padrão      | 800 unidades de computação |
| **Armazenamento máximo**            |                   |
| Camada de serviços básicos         | 1 TB              |
| Camada de serviços padrão      | 1 TB              |

Quando são atingidas demasiadas ligações, poderá receber o erro seguinte:
> Erro 1040 (08004): Demasiadas ligações

## <a name="preview-functional-limitations"></a>Limitações de pré-visualização funcionais

### <a name="scale-operations"></a>Operações de dimensionamento
- Dimensionamento dinâmico de servidores em escalões de serviço não é atualmente suportada. Ou seja, alternar entre escalões de serviço básico e padrão.
- Dinâmico a pedido aumento do armazenamento no servidor previamente criada não é atualmente suportado.
- Não é suportada a diminuir o tamanho de armazenamento do servidor.

### <a name="server-version-upgrades"></a>Atualização de versão do servidor
- Automatizar a migração entre versões do motor de base de dados principal não é atualmente suportada.

### <a name="subscription-management"></a>Gestão de subscrição
- Mover dinamicamente servidores previamente criadas na subscrição e grupo de recursos não é atualmente suportada.

### <a name="point-in-time-restore"></a>Restauro para um ponto anterior no tempo
- Não é permitida a restaurar para a camada de serviço diferente e/ou tamanho de unidades de computação e armazenamento.
- Restaurar um servidor de ignorados não é suportada.

## <a name="next-steps"></a>Passos seguintes
- [O que está disponível em cada camada de serviço](concepts-service-tiers.md)
- [Versões de base de dados MySQL suportadas](concepts-supported-versions.md)
