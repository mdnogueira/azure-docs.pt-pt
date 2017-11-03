---
title: "Explicar computação unidades de base de dados do Azure para MySQL | Microsoft Docs"
description: "BD do Azure para o MySQL: Este artigo explica os conceitos de unidades de computação e o que acontece quando a carga de trabalho atinge as unidades de computação máximo."
services: mysql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 09/29/2017
ms.openlocfilehash: 2c4894ae9a4235f9ced4a8d9b991238543646f53
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="explaining-compute-units-in-azure-database-for-mysql"></a>Explicar as unidades de computação na base de dados do Azure para MySQL
Este tópico explica o conceito de unidades de computação e o que acontece quando a carga de trabalho atinge as unidades de computação máximo.

## <a name="what-are-compute-units"></a>Quais são as unidades de computação?
Computação que unidades são uma medida de débito de processamento de CPU que fique disponível para uma única base de dados do Azure para o servidor de MySQL. Uma unidade de computação é uma medida combinada de CPU e memória recursos. Em geral, 50 unidades computação equacionar para metade um núcleo. 100 unidades de computação equacionar para um núcleo. 2.000 unidades computação equacionar para 20 núcleos de débito de processamento garantido disponível para o servidor.

A quantidade de memória por unidade de computação está otimizada para a base e padrão escalões de preço. Duplicando as unidades de computação ao aumentar o nível de desempenho equivale a duplicando o conjunto de recursos disponíveis para essa base de dados do Azure individual para MySQL.

Por exemplo, um unidades de computação de 800 padrão fornece mais de x 8 débito de CPU e memória, do que uma configuração de unidades padrão de computação de 100. No entanto, enquanto unidades padrão de computação de 100 fornecer o débito de CPU mesmo em comparação comparado unidades básicas de computação de 100, a quantidade de memória que está pré-configurada no escalão de preço padrão é duplo a quantidade de memória configurada para o escalão de preço básico. Por conseguinte, o escalão de preço padrão fornece um melhor desempenho da carga de trabalho e menor latência de transação do que o escalão de preço básico com o mesmo unidades computação selecionado.

## <a name="how-can-i-determine-the-number-of-compute-units-needed-for-my-workload"></a>Como posso determinar o número de unidades de computação necessária para a minha carga de trabalho?
Se pretender migrar um servidor de MySQL existente em execução no local ou numa máquina virtual, pode determinar o número de unidades de computação por estimar quantos núcleos de débito de processamento requer a sua carga de trabalho. 

Se o existente no local ou o servidor de máquina virtual está atualmente a utilizar 4 núcleos (sem contar hyperthread de CPU), comece por configurar 400 unidades de computação para a base de dados do Azure para o servidor de MySQL. Unidades de computação podem ser escaladas para cima ou para baixo dinamicamente consoante as suas necessidades de carga de trabalho e virtualmente sem período de indisponibilidade de aplicações. 

Monitorizar o gráfico de métricas no portal do Azure ou escrever comandos da CLI do Azure para medir unidades de computação. A métrica relevante para monitorizar está a percentagem de unidade de computação e o limite de unidade de computação.

>[!IMPORTANT]
> Se encontrar IOPS não é utilizado totalmente para o número máximo de armazenamento, considere a utilização de unidades de computação, bem como de monitorização. Aumentar as unidades de computação pode permitir para maior débito de e/s por lessening o estrangulamento do desempenho resultantes da limitado de CPU ou memória.

## <a name="what-happens-when-i-hit-my-maximum-compute-units"></a>O que acontece quando atinjo o meu unidades computação máximo?
Níveis de desempenho são calibrated e regidos para fornecer recursos para executar a carga de trabalho de base de dados até aos limites máximas para o nível do desempenho e o escalão de preço selecionado. 

Se a carga de trabalho atingir os limites máximos na computação unidades ou os limites de IOPS aprovisionados, pode continuar a utilizar os recursos no máximo permitido nível, mas as suas consultas são encontrar latências maiores. Estes limites resultam num slowdown na carga de trabalho em vez de erros, a menos que o slowdown torna-se, por isso, grave que consulta o tempo limite. 

Se a carga de trabalho atingir os limites no número de ligações máximos, são gerados erros explícitos. Para obter mais informações sobre limites de recursos, consulte [limitações na base de dados do Azure para MySQL](concepts-limits.md).

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre os escalões de preços, consulte [base de dados do Azure para MySQL escalões de preço](./concepts-service-tiers.md).
