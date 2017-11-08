---
title: "Descrição geral de referência de base de dados SQL do Azure"
description: "Este tópico descreve o Benchmark de base de dados SQL do Azure utilizado para medir o desempenho da SQL Database do Azure."
services: sql-database
documentationcenter: na
author: jan-eng
manager: jhubbard
editor: monicar
ms.assetid: e26f8a66-2c12-49d7-8297-45b4d48a5c01
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: On Demand
ms.date: 06/21/2016
ms.author: janeng
ms.openlocfilehash: fb8a5f205ddc143dc47349829048f46f88963d05
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2017
---
# <a name="azure-sql-database-benchmark-overview"></a>Descrição geral de referência de base de dados SQL do Azure
## <a name="overview"></a>Descrição geral
Base de dados de SQL do Microsoft Azure oferece três [escalões de serviço](sql-database-service-tiers.md) com vários níveis de desempenho. Cada nível de desempenho fornece um conjunto crescente de recursos ou "power", foi concebida para fornecer o débito velocidades cada vez.

É importante ser capaz de quantificar como potência crescente de cada nível de desempenho traduz para desempenho de base de dados maior. Para fazer este Microsoft desenvolveu o Benchmark de base de dados de SQL do Azure (ASDB). O benchmark exercises uma mistura de operações básicas encontrado em todas as cargas de trabalho OLTP. Iremos medir o débito obtido para bases de dados em execução em cada nível de desempenho.

Os recursos e a potência de cada nível de desempenho e o escalão de serviço são expressas em termos de [unidades de transação de base de dados (DTUs)](sql-database-what-is-a-dtu.md). As DTUs proporcionam uma forma de descrever a capacidade relativa de um nível de desempenho baseado numa medida combinada de CPU, memória e leem e escrevem taxas oferecidos por cada nível de desempenho. Duplicando a classificação de DTU de uma base de dados equivale a duplicando a potência de base de dados. O benchmark permite-nos avaliar o impacto no desempenho da base de dados do poder aumentar oferecido por cada nível de desempenho por exercising operações de base de dados real, ao aumentar o tamanho de base de dados, o número de utilizadores, e transação classifica proportion para o recursos fornecidos para a base de dados.

Por expressa o débito de camada de serviço básico utilizando transações por hora, a camada de serviço Standard com transações por minuto e o escalão de serviço Premium utilizando transações por segundo, este torna mais fácil relacionar rapidamente o desempenho possibilidade de cada camada de serviços para os requisitos de uma aplicação.

## <a name="correlating-benchmark-results-to-real-world-database-performance"></a>Correlacionar os resultados do benchmark para desempenho de base de dados do mundo real
É importante compreender que ASDB, como todos os testes de desempenho, é representativo e facto apenas. As taxas de transacção conseguidas com a aplicação do benchmark não poderá ser os mesmos que os que pode ser conseguido com outras aplicações. O benchmark é composta por uma coleção de transação de diferentes tipos de executam em relação a um esquema que contém uma variedade de tipos de dados e tabelas. Enquanto o benchmark exercises as mesmas operações básicas que são comuns a todas as cargas de trabalho OLTP, não representa qualquer classe específica da aplicação ou a base de dados. O objetivo do benchmark consiste em fornecer um guia razoável para o desempenho relativo de uma base de dados que pode esperar quando aumentar ou reduzir verticalmente entre níveis de desempenho. Na realidade, as bases de dados têm tamanhos diferentes e a complexidade, encontrarem mixes diferentes das cargas de trabalho em responderá formas diferentes. Por exemplo, uma aplicação de e/s intensivas pode atingir os limiares de e/s mais cedo, ou uma aplicação intensivas em CPU pode atingir os limites de CPU mais cedo. Não há nenhuma garantia que sejam dimensionadas de qualquer base de dados específica da mesma forma como o benchmark em aumento de carga.

O benchmark e respetivos metodologia são descritos mais detalhadamente abaixo.

## <a name="benchmark-summary"></a>Resumo do benchmark
ASDB mede o desempenho de uma combinação de operações de base de dados básica que ocorrem com maior frequência em cargas de trabalho (OLTP) de processamento de transações online. Embora o benchmark concebido com em mente, o esquema de base de dados, a população de dados de informática na cloud e transações foram concebidas para ser amplamente representativa dos elementos básicos normalmente utilizados em cargas de trabalho OLTP.

## <a name="schema"></a>Esquema
O esquema foi concebido para ter suficiente variedade e complexidade para suportar uma vasta gama de operações. O benchmark é executada relativamente a uma base de dados composto seis tabelas. As tabelas enquadram-se em três categorias: tamanho fixo, dimensionamento e a crescer. Existem duas tabelas de tamanho fixo; três tabelas dimensionamento; e uma tabela crescente. Tabelas de tamanho fixo de ter um número constante de linhas. Tabelas de dimensionamento tem uma cardinalidade que é proporcional ao desempenho de base de dados, mas não irá alterar durante o benchmark. A tabela crescente é um tamanho adequado, como uma tabela de dimensionamento do carregamento inicial, mas, em seguida, as alterações de cardinalidade durante a execução do benchmark como linhas estão inseridas e eliminadas.

O esquema inclui uma mistura de tipos de dados, incluindo o número inteiro, numérico, caráter e data/hora. O esquema inclui as chaves primária e secundária, mas não quaisquer chaves externas - ou seja, existem não existem restrições de integridade referencial entre as tabelas.

Um programa de geração de dados gera os dados para a base de dados inicial. Dados numéricos e de número inteiro são gerados com várias estratégias. Em alguns casos, os valores são distribuídos aleatoriamente ao longo de um intervalo. Noutros casos, um conjunto de valores é aleatoriamente permuted para se certificar de que é mantida uma distribuição específico. Campos de texto são gerados a partir de uma lista de palavras para produzir os dados de procura realistas ponderada.

A base de dados é dimensionada com base no "fator de dimensionamento". O factor de dimensionamento (abreviado como SF) determina a cardinalidade do dimensionamento e tabelas a crescer. Conforme descrito abaixo na secção utilizadores e Pacing, o tamanho de base de dados, número de utilizadores e o máximo desempenho todas Dimensionar in proportion to entre si.

## <a name="transactions"></a>Transações
A carga de trabalho consiste em tipos de transação nove, conforme mostrado na tabela abaixo. Cada transação foi concebida para realçar um conjunto específico de características de sistema na base de dados motor e sistema de hardware, com alto contraste a partir de outras transações. Esta abordagem torna mais fácil avaliar o impacto de diferentes componentes para o desempenho global. Por exemplo, a transação "Leitura pesada" produz um número significativo de operações de leitura do disco.

| Tipo de transação | Descrição |
| --- | --- |
| Ler Lite |SELECIONAR; dentro da memória; só de leitura |
| Média de leitura |SELECIONAR; sobretudo em memória; só de leitura |
| Pesado de leitura |SELECIONAR; não sobretudo em memória; só de leitura |
| Atualização Lite |ATUALIZAR; dentro da memória; leitura-escrita |
| Atualizar pesado |ATUALIZAR; não sobretudo em memória; leitura-escrita |
| INSERT Lite |INSERIR; dentro da memória; leitura-escrita |
| Inserir pesado |INSERIR; não sobretudo em memória; leitura-escrita |
| Eliminar |ELIMINAR; combinação de dentro da memória e não em memória; leitura-escrita |
| CPU pesado |SELECIONAR; dentro da memória; relativamente CPU sobrecarga; só de leitura |

## <a name="workload-mix"></a>Combinação de carga de trabalho
Transações são selecionadas aleatoriamente entre uma distribuição ponderada com a seguinte combinação geral. A mistura geral tem um rácio de leitura/escrita de aproximadamente 2:1.

| Tipo de transação | % de combinação |
| --- | --- |
| Ler Lite |35 |
| Média de leitura |20 |
| Pesado de leitura |5 |
| Atualização Lite |20 |
| Atualizar pesado |3 |
| INSERT Lite |3 |
| Inserir pesado |2 |
| Eliminar |2 |
| CPU pesado |10 |

## <a name="users-and-pacing"></a>Os utilizadores e o ritmo de processamento
A carga de trabalho do benchmark é suscitada pelo departamento de uma ferramenta que submete transações através de um conjunto de ligações para simular o comportamento de um número de utilizadores em simultâneo. Embora todas as ligações e transações máquina gerada, de simplicidade denominamos estas ligações "utilizadores". Apesar de cada utilizador funciona independentemente todos os outros utilizadores, todos os utilizadores executam o mesmo ciclo de passos abaixo:

1. Estabelece uma ligação de base de dados.
2. Repita até assinalado para sair:
   * Selecione uma transação aleatória, (a partir de uma distribuição ponderada).
   * Executar a transação selecionada e medir o tempo de resposta.
   * Aguarde um atraso de ritmo de processamento.
3. Feche a ligação de base de dados.
4. Saída.

O ritmo de processamento atraso (no passo 2c) é aleatória, selecionado, mas com uma distribuição que tenha uma média de segundo 1.0. Deste modo, cada utilizador pode, em média, gerar no máximo uma transação por segundo.

## <a name="scaling-rules"></a>Regras de dimensionamento
O número de utilizadores é determinado pelo tamanho da base de dados (em unidades de fator de dimensionamento). Não há um utilizador a cada cinco unidades de fator de dimensionamento. Devido a atraso ritmo de processamento, um utilizador pode gerar um máximo de uma transação por segundo, em média.

Por exemplo, um-fator de dimensionamento de 500 (SF = 500) base de dados terá 100 utilizadores e pode conseguir uma velocidade máxima de 100 TPS. A unidade um TPS superior taxa requer uma base de dados maior e mais utilizadores.

A tabela abaixo mostra o número de utilizadores que efetivamente constante para cada nível de desempenho e o escalão de serviço.

| Camada de serviço (nível de desempenho) | Utilizadores | Tamanho da Base de Dados |
| --- | --- | --- |
| Básica |5 |720 MB |
| Padrão (S0) |10 |1 GB |
| Padrão (S1) |20 |2.1 GB |
| Padrão (S2) |50 |7.1 GB |
| Premium (P1) |100 |14 GB |
| Premium (P2) |200 |28 GB |
| Premium (P6) |800 |114 GB |

## <a name="measurement-duration"></a>Duração de medida
Um benchmark válido executar requer uma duração de medição do Estado de repouso de, pelo menos, uma hora.

## <a name="metrics"></a>Métricas
As métricas chave no benchmark são débito e a resposta de tempo.

* Débito é a medida de desempenho essenciais no benchmark. Débito é reportado no transações por unidade-de-time, todos os tipos de transação de contagem.
* Tempo de resposta é uma medida de previsibilidade quanto de desempenho. A restrição de tempo de resposta varia de acordo com a classe de serviço, com classes superiores de serviço ter um requisito de tempo de resposta mais rigorosos, conforme mostrado abaixo.

| Classe de serviço | Medida de débito | Requisito de tempo de resposta |
| --- | --- | --- |
| Premium |Transações por segundo |percentil 95th em segundos 0.5 |
| Standard |Transações por minuto |percentil 90th em segundos 1.0 |
| Básica |Transações por hora |percentil 80th em segundos 2.0 |

## <a name="conclusion"></a>Conclusão
O Benchmark de base de dados SQL do Azure mede o desempenho relativo de base de dados do SQL do Azure em execução em toda a gama de escalões de serviço disponível e níveis de desempenho. O benchmark exercises uma mistura de operações de base de dados básica que ocorrem com maior frequência em cargas de trabalho (OLTP) de processamento de transações online. Ao medir o desempenho real, o benchmark fornece uma avaliação do impacto mais significativa no débito de alterar o nível de desempenho, que é possível ao listar apenas os recursos fornecidos por cada nível, tais como CPU, velocidade, tamanho de memória e IOPS. No futuro, iremos irá continuar a evoluir benchmark para alargar o respetivo âmbito e expanda os dados fornecidos.

## <a name="resources"></a>Recursos
[Introdução à base de dados SQL](sql-database-technical-overview.md)

[Camadas de serviços e níveis de desempenho](sql-database-service-tiers.md)

[Guia de desempenho das bases de dados](sql-database-performance-guidance.md)
