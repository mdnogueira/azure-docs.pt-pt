---
title: "Utilizar relatórios de gestão de custo de gestão de custo do Azure | Microsoft Docs"
description: "Este artigo descreve como utilizar vários relatórios de gestão de custos no portal do Cloudyn."
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 11/29/2017
ms.topic: article
ms.service: cost-management
manager: carmonm
ms.custom: 
ms.openlocfilehash: 92bb4f2a6458057613bdbcb749026781111a778d
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2017
---
# <a name="use-cost-management-reports"></a>Utilizar relatórios de gestão de custos

Este artigo descreve como utilizar vários relatórios de gestão de custos no portal do Cloudyn. A maioria dos relatórios de Cloudyn são intuitivas e tem um aspeto e funcionalidade uniforme. Para obter uma descrição geral sobre Cloudyn relatórios, consulte [compreender custo relatórios](understading-cost-reports.md). O artigo também descreve várias opções e os campos utilizados na maioria dos relatórios.

## <a name="cost-analysis-reports"></a>Relatórios de análise de custos

Relatórios de análise de custos apresentam dados de faturação da sua fornecedores de nuvem. Utilizar os relatórios, pode agrupar e pormenorize vários segmentos de dados descritos no ficheiro de faturação. Os relatórios de ativar a navegação granulares custo em dados de faturação não processados dos fornecedores de nuvem.

Relatórios de análise de custos não pelas quais agrupar os custos de etiquetas. Relatórios baseados na tag só estão disponível nos relatórios de alocação de custo definidos depois de criar um modelo de custo utilizando 360 de alocação de custo.

### <a name="actual-cost-analysis"></a>Análise de Custo Real

O relatório de análise de custos reais mostra os contribuintes de custo principal, incluindo os custos em curso e taxas de uso individual.

 Utilize o relatório de análise de custos reais para:

- Analisar e monitorizar os custos reais despendidos durante um período de tempo especificado
- Agendar um alerta do limiar
- Analisar os custos de análise de custos e encargos

#### <a name="to-use-the-actual-cost-analysis-report"></a>Para utilizar o relatório de análise de custo real

No mínimo, execute os seguintes passos. Também pode utilizar outras opções e campos.

1. Selecione um intervalo de datas.
2. Selecione um filtro.

Pode faça duplo clique de resultados do relatório para ver os pormenores-los e ver informações mais detalhadas.

![Exemplo de relatório de análise de custo real](./media/use-reports/actual-cost-analysis.png)

### <a name="actual-cost-over-time"></a>Custo real ao longo do tempo

Relatório custo real ao longo do tempo é um relatório de análise de custos padrão distribuir custo através de uma resolução de tempo definido. Este relatório mostra os gastos com ao longo do tempo para que possa observar as tendências e detetar irregularities gastos. Este relatório mostra os contribuintes do custo principal, incluindo os custos em curso e as taxas de uma única instância reservado despendidos que está a ser durante um período de tempo selecionado.

Utilize o relatório de custo real ao longo do tempo para:

- Consulte as tendências de custo ao longo do tempo.
- Localize irregularities custo.
- Localize todas as perguntas relacionadas com o custo relacionadas com o Amazon Web Services.

#### <a name="to-use-the-actual-cost-over-time-report"></a>Para utilizar o relatório de custo real ao longo do tempo:

No mínimo, execute os seguintes passos. Também pode utilizar outras opções e campos.

- Selecione um intervalo de datas.

Por exemplo, pode selecionar grupos para ver os seus custos ao longo do tempo. E, em seguida, adicione filtros para restringir os resultados.

![Exemplo de relatório custo ao longo do tempo real](./media/use-reports/actual-cost-over-time.png)



### <a name="amortized-cost-reports"></a>Relatórios de custo amortized

Este conjunto de custo amortized relatórios mostra linearized não-utilização baseia taxas de serviço ou os custos de pagar única e distribuir os seus custos ao longo do tempo uniformemente durante o respetivo tempo de vida.

Por exemplo, a única taxas poderão incluir:

- Anual taxas de suporte
- Taxas de componente de segurança anual
- Taxas de compra de instâncias reservadas
- Alguns itens do Azure Marketplace

No ficheiro de faturação, taxas de uso individual são caracterizadas quando o início de consumo do serviço e as datas de fim ou timestamp, têm valores iguais. Cloudyn, em seguida, reconhece-taxas como única que podem ser amortized. Não não possível amortized outros serviços com base no consumo com os custos de utilização a pedido.

Para ilustrar os custos amortized, reveja a imagem de exemplo seguinte de um relatório de real custo ao longo do tempo. No exemplo, mostra um pico de pedidos de custo em 23 de Agosto. -Pode parecer uma anomalias em comparação comparada a tendência do custo diário habitual. Análise da causa raiz e navegação dados identificado este custo como uma anual AWS serviço APN reserva, que é uma taxa única adquiridas e cobrados nesse dia. Pode ver como este custo é amortized na secção seguinte.

![Exemplo de relatório custo ao longo do tempo real, que mostra o custo de uso individual](./media/use-reports/actual-amort-example.png)

#### <a name="to-use-the-amortized-cost-over-time-report"></a>Para utilizar o relatório de Amortized custo ao longo do tempo:

No mínimo, execute os seguintes passos. Também pode utilizar outras opções e campos.

1. Selecione um intervalo de datas.
2. Selecione um serviço e um fornecedor.

As devidas-reencaminhar o exemplo anterior, pode ver que o custo de uso individual está agora amortized na imagem seguinte:

![Exemplo de relatório do amortized custo ao longo do tempo](./media/use-reports/amort-cost-over-time.png)

A imagem anterior mostra o custo amortized o custo de reserva APN ao longo do tempo. Este relatório mostra o amortization taxa única e o custo APN como uma compra de reserva anuais. O custo APN é distribuído uniformemente diariamente como 1/365th do custo prévio de reserva.

## <a name="cost-allocation-analysis-reports"></a>Relatórios de análise de alocação de custos

Relatórios de análise de alocação de custo estão disponíveis depois de criar um modelo de custo utilizando 360 de alocação de custo. Cloudyn processa dados de custo/faturação e corresponde aos dados para os dados de utilização e etiqueta das suas contas de nuvem. Para fazer corresponder os dados, Cloudyn requer acesso aos seus dados de utilização. Contas que estão em falta credenciais, são identificados como recursos não categorizados.

### <a name="cost-analysis-report"></a>Relatório de análise de custos

O relatório de análise de custos fornece informações sobre o consumo de nuvem e de gastos durante um período de tempo selecionado. As políticas definidas no Gestor de atribuição de custo são utilizadas no relatório de análise de custos.

Como é que o Cloudyn calcular este relatório?

Cloudyn garante alocação mantém a integridade de cada conta ligada ao aplicar afinidade de conta. A afinidade assegura uma conta que não utiliza um serviço específico não tem quaisquer custos deste serviço atribuído ao mesmo. Os custos acumulados nessa conta permanecem nessa conta e não são calculados através das políticas de alocação. Por exemplo, poderá ter cinco contas ligadas. Se apenas três delas utilizam serviços de armazenamento, o custo dos serviços de armazenamento só está alocado em tags nas contas de três.

 Utilize o relatório de análise de custos para:

- Apresente uma vista agregada da implementação completa para um intervalo de tempo específico.
- Ver os custos, categorias de etiqueta com base nas políticas criadas no modelo de custo.

#### <a name="to-use-the-cost-analysis-report"></a>Para utilizar o relatório de análise de custos:

1. Selecione um intervalo de datas.
2. Adicione etiquetas, conforme necessário.
3. Adicione grupos.
4. Escolha um modelo de custo que criou anteriormente.

A imagem seguinte mostra um exemplo de relatório de análise de custos no formato sunburst. Os anéis de mostram grupos. A coroa exterior mostra serviço e círculo interno mostra unidade.

![Exemplo de relatório de análise de custos](./media/use-reports/cost-analysis01.png)



Eis o exemplo dessas mesmas informações numa vista de tabela.

![Exemplo de relatório de análise de custos](./media/use-reports/cost-analysis02.png)



### <a name="cost-over-time-report"></a>Relatório custo ao longo do tempo

O relatório de custo ao longo do tempo apresenta gastos ao longo do tempo para que possa observar as tendências e detetar irregularities na sua implementação. Essencialmente mostra os custos distribuídos por um período de tempo definido. O relatório inclui os contribuintes do custo principal, incluindo os custos em curso e as taxas de uma única instância reservado despendidos que está a ser durante um período de tempo selecionado. As políticas definidas no Gestor de custo de 360° podem ser utilizadas neste relatório.

Utilize o relatório de custo ao longo do tempo para:

- Ver as alterações ao longo do tempo e que influências alterar um dia (ou intervalo de datas) para o seguinte.
- Analise os custos ao longo do tempo de uma instância específica.
- Compreender porquê Ocorreu um aumento do custo de uma instância específica.

#### <a name="to-use-the-cost-over-time-report"></a>Para utilizar o relatório de custo ao longo do tempo:

1. Selecione um intervalo de datas.
2. Adicione etiquetas, conforme necessário.
3. Adicione grupos.
4. Escolha um modelo de custo que criou anteriormente.
5. Selecione os custos reais ou amortized os custos.
6. Escolha se pretende aplicar regras de atribuição a vista não processada de vista de dados de faturação ou para recalculada custo pela vista Cloudyn.

Eis um exemplo do relatório.

![Exemplo de custo ao longo do tempo](./media/use-reports/cost-over-time.png)



## <a name="next-steps"></a>Passos seguintes

- Se ainda não tiver concluído o primeiro tutorial para a gestão de custo, leia-lo no [rever os custos de utilização e](tutorial-review-usage.md).
