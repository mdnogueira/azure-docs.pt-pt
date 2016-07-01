<properties
   pageTitle="Expectativas de pré-visualização do SQL Data Warehouse | Microsoft Azure"
   description="Resumo das funcionalidades de pré-visualização pública e os nossos objetivos de disponibilidade geral do SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="happynicolle"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/05/2016"
   ms.author="nicw;barbkess;sonyama"/>


# Expectativas de pré-visualização do SQL Data Warehouse

Este artigo descreve as capacidades de pré-visualização do SQL Data Warehouse e os nossos objetivos para o serviço de disponibilidade geral (DG). Iremos atualizar esta informação continuamente, à medida que melhoramos as capacidades de pré-visualização pública.

Os nossos objetivos para o SQL Data Warehouse:

- Desempenho previsível e escalabilidade linear até petabytes de dados
- Fiabilidade elevada para todas as operações de armazém de dados
- Pouco tempo desde o carregamentos dos dados até obtenção de informações de dados em dados relacionais e não relacionais

Iremos trabalhar continuamente para atingirmos estes objetivos durante a pré-visualização do SQL Data Warehouse.

## Desempenho previsível e dimensionável

O Azure SQL Data Warehouse introduz Unidades de Armazém de Dados (DWUs) como uma forma de medir os recursos informáticos (CPU, memória, E/S de armazenamento) disponíveis para o armazém de dados. O aumento do número de DWUs aumenta os recursos. À medida que o número de DWUs aumenta, o SQL Data Warehouse executa operações em paralelo (por exemplo, carregamento ou consulta de dados) através de recursos mais distribuídos. Isto reduz a latência e melhora o desempenho.

Qualquer armazém de dados tem duas métricas de desempenho fundamentais:

- Taxa de carregamento. O número de registos que podem ser carregados para o armazém de dados por segundo. Especificamente,,medimos o número de registos que podem ser importados, através do PolyBase, a partir do Armazenamento de Blobs do Azure para uma tabela com um Índice de Arquivo de Colunas Agrupadas.
- Taxa de análise. O número de registos que podem ser obtidos sequencialmente do armazém de dados por segundo. Especificamente, medimos o número de registos devolvidos por uma consulta num índice de arquivo de colunas agrupadas.

Estamos a medir alguns melhoramentos de desempenho importantes e, em breve, iremos partilhar as taxas esperadas. Durante a pré-visualização, oferecemos melhoramentos contínuos (por exemplo, aumentando a compressão e a colocação em cache) para aumentar as taxas e garantir a previsibilidade do dimensionamento.  

## Proteção de Dados

O SQL Data Warehouse armazena todos os dados no armazenamento do Azure, utilizando o armazenamento localmente redundante. Várias cópias síncronas dos dados são mantidas no centro de dados local para garantir a proteção transparente de dados, em caso de falhas localizadas. 

## Cópias de segurança

O Azure SQL Data Warehouse cria cópias de segurança de todos os dados pelo menos a cada 8 horas, utilizando Instantâneos de Armazenamento do Azure. Estes instantâneos são mantidos durante 7 dias. Isto permite o restauro dos dados para, pelo menos, 21 pontos no tempo nos últimos 7 dias, até à altura do último instantâneo. Pode restaurar dados a partir de um instantâneo, utilizando o PowerShell ou APIs REST.

## Fiabilidade de consulta

O SQL Data Warehouse está incorporado numa arquitetura de processamento paralelo em massa (MPP). O SQL Data Warehouse deteta automaticamente e efetua a migração de falhas de nós de Computação e de Controlo. No entanto, uma operação (por exemplo, carregamento de dados ou consulta) poderá falhar devido a uma falha ou migração de nós. Durante a pré-visualização, estamos a efetuar melhoramentos contínuos para concluir as operações com êxito, apesar de falhas de nó.


## Atualizações e período de indisponibilidade

Periodicamente, o SQL Data Warehouse será atualizado para adicionar novas funcionalidades e instalar correções críticas.  Estas atualizações podem ser incómodas e, de momento, as atualizações não são efetuadas com base numa agenda previsível.  Se achar que este processo é demasiado incómodo, aconselhamo-lo a [criar um pedido de suporte][] para que o possamos ajudar neste processo.


## Passos seguintes

[Introdução][] à pré-visualização pública.

<!--Image references-->

<!--Article references-->
[criar um pedido de suporte]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Introdução]: ./sql-data-warehouse-get-started-provision.md

<!--MSDN references-->

<!--Other Web references-->



<!--HONumber=Jun16_HO2-->


