---
title: "Explicar computação unidades de base de dados do Azure para PostgreSQL | Microsoft Docs"
description: "BD do Azure para PostgreSQL: Este artigo explica os conceitos de unidades de computação e o que acontece quando a carga de trabalho atinge as unidades de computação máximo."
services: postgresql
author: kamathsun
ms.author: sukamat
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 09/26/2017
ms.openlocfilehash: dbb9f733455fa0492358b24b178c8c637ff08c71
ms.sourcegitcommit: 3e3a5e01a5629e017de2289a6abebbb798cec736
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2017
---
# <a name="explaining-compute-units-in-azure-database-for-postgresql"></a>Explicar as unidades de computação na base de dados do Azure para PostgreSQL
Este tópico explica o conceito de unidades de computação e o que acontece quando a carga de trabalho atinge o nível máximo de unidades de computação.

## <a name="what-are-compute-units"></a>Quais são as unidades de computação?
Computação que unidades são uma medida de débito de processamento de CPU que fique disponível para uma única base de dados do Azure para o servidor de PostgreSQL. Uma unidade de computação é uma medida combinada de CPU e memória recursos. Em geral, 50 unidades computação equacionar para metade um núcleo. 100 unidades de computação equacionar para um núcleo. Unidades de computação de 2000 equacionar para 20 núcleos de débito de processamento garantido disponível para o servidor.

A quantidade de memória por unidade de computação está otimizada para a base e padrão escalões de preço. Duplicando as unidades de computação ao aumentar o nível de desempenho equivale a duplicando a quantidade de CPU e memória disponível para a base de dados do Azure único para PostgreSQL.

Por exemplo, um unidades de computação de 800 padrão fornece mais de x 8 débito de CPU e memória que uma configuração de unidades padrão de computação de 100. No entanto, enquanto unidades padrão de computação de 100 fornecer o mesmo débito de CPU, como sucede unidades básicas de computação de 100, a quantidade de memória que está pré-configurada no escalão de preço padrão é duplo a quantidade de memória configurada para o escalão de preço básico. Por conseguinte, o escalão de preço padrão fornece um melhor desempenho da carga de trabalho e menor latência de transação do que o escalão de preço básico com o mesmo unidades computação selecionado.

## <a name="how-can-i-determine-the-number-of-compute-units-needed-for-my-workload"></a>Como posso determinar o número de unidades de computação necessária para a minha carga de trabalho?
Se pretender para migrar um servidor PostgreSQL existente em execução no local ou numa máquina virtual, pode determinar o número de unidades de computação por estimar quantos núcleos de débito de processamento tem da carga de trabalho. 

Se o existente no local ou o servidor de máquina virtual está atualmente a utilizar 4 núcleos (sem contar hyperthread de CPU), comece por configurar 400 unidades de computação para a base de dados do Azure para o servidor de PostgreSQL. Unidades de computação podem ser dinamicamente escaladas para cima ou para baixo consoante as suas necessidades de carga de trabalho virtualmente sem período de indisponibilidade de aplicações. 

Monitorizar o gráfico de métricas no portal do Azure ou escrever comandos da CLI do Azure para medir unidades de computação. A métrica relevante para monitorizar está a percentagem de unidade de computação e o limite de unidade de computação.

>[!IMPORTANT]
> Se encontrar IOPS não é utilizado totalmente para o número máximo de armazenamento, considere a utilização de unidades de computação, bem como de monitorização. Aumentar as unidades de computação pode permitir para maior débito de e/s por lessening estrangulamento do desempenho devido a limitado CPU ou memória.

## <a name="what-happens-when-i-hit-my-maximum-compute-units"></a>O que acontece quando atinjo o meu unidades computação máximo?
Níveis de desempenho são calibrated e regidos para fornecer recursos para executar a carga de trabalho de base de dados até aos limites máximos para o nível do desempenho e o escalão de preço selecionado. 

Se a carga de trabalho atingir os limites em unidades de computação ou aprovisionado IOPS máximos, pode continuar a utilizar os recursos no máximo permitido nível, mas as suas consultas são provável que veja latências maiores. Estes limites não resultar em erros, mas em vez disso, um slowdown numa carga de trabalho, a menos que o slowdown torna-se, por isso, grave que consulta o tempo limite. 

Se a carga de trabalho atingir os limites no número de ligações máximos, são gerados erros explícitos. Para obter mais informações sobre limites de recursos, consulte [limitações na base de dados do Azure para PostgreSQL](concepts-limits.md).

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre os escalões de preços, consulte [base de dados do Azure para PostgreSQL escalões de preço](./concepts-service-tiers.md).
