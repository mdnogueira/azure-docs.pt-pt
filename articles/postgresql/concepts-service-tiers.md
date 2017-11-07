---
title: "Escalões de preço na base de dados do Azure para PostgreSQL"
description: "Escalões de preço na base de dados do Azure para PostgreSQL"
services: postgresql
author: kamathsun
ms.author: sukamat
manager: jhubbard
editor: jasonwhowell
ms.custom: mvc
ms.service: postgresql
ms.topic: article
ms.date: 11/03/2017
ms.openlocfilehash: 2c0ed6b58fe3e354da3cf58cd0c504d72bb0f421
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2017
---
# <a name="azure-database-for-postgresql-options-and-performance-understand-whats-available-in-each-pricing-tier"></a>Base de dados do Azure para PostgreSQL opções e desempenho: compreender o que está disponível em cada escalão de preço
Quando cria uma base de dados do Azure para o servidor de PostgreSQL, decida três escolhas principais para configurar os recursos alocados para esse servidor. Estas opções afetam o desempenho e a escala do servidor.
- Escalão de preço
- Unidades de Computação
- Armazenamento (GB)

Cada escalão de preço tem um intervalo de níveis de desempenho (unidades de computação) à sua escolha, dependendo dos requisitos de cargas de trabalho. Níveis de desempenho superiores fornecem recursos adicionais para o servidor foi concebido para fornecer um maior débito. Pode alterar o nível de desempenho do servidor dentro de um escalão de preço virtualmente sem período de indisponibilidade de aplicações.

> [!IMPORTANT]
> Enquanto o serviço está em pré-visualização pública, não há um contrato de nível de serviço (SLA) garantida.

Dentro de uma base de dados do Azure para o servidor de PostgreSQL, pode ter uma ou várias bases de dados. Pode optar por criar uma base de dados individual por servidor para que a base de dados a utilizar todos os recursos de servidor ou criar várias bases de dados para partilhar os recursos do servidor. 

## <a name="choose-a-pricing-tier"></a>Escolha um escalão de preço
Enquanto na pré-visualização, base de dados do Azure para PostgreSQL oferece dois escalões de preços: básico e padrão. Escalão Premium ainda não está disponível, mas estará disponível em breve. 

A tabela seguinte fornece exemplos dos escalões de preços melhor adequados para cargas de trabalho de aplicação diferente.

| Escalão de preço | Cargas de trabalho de destino |
| :----------- | :----------------|
| Básica | Mais adequada para pequenas cargas de trabalho que necessitam de armazenamento e computação escalável sem garantir de IOPS. Os exemplos incluem servidores utilizados para programação ou testes ou dimensionamento pequeno raramente utilizadas aplicações. |
| Standard | A opção para a nuvem aplicações que precisam de IOPS garantir com débito elevado. Os exemplos incluem web ou aplicações analíticas. |
| Premium | Melhor adequados para cargas de trabalho que necessitam de latência baixa para transações e/s. Fornece a melhor suporte para vários utilizadores em simultâneo. Aplicável a bases de dados que suportam aplicações fundamentais.<br />O escalão de preço Premium não está disponível na pré-visualização. |

Para escolher um escalão de preço, primeiro comece por determinar se a carga de trabalho tem uma garantia IOPS. Se assim for, utilize o escalão de preço padrão.

| **Funcionalidades do escalão de preço** | **Básica** | **Standard** |
| :------------------------ | :-------- | :----------- |
| Unidades de processamento máximo | 100 | 800 | 
| Máximo de armazenamento total | 1 TB | 1 TB | 
| Garantia IOPS de armazenamento | N/D | Sim | 
| IOPS máximo de armazenamento | N/D | 3000 | 
| Período de retenção de cópias de segurança de base de dados | 7 dias | 35 dias | 

Durante o período de tempo de pré-visualização, não é possível alterar o escalão de preço assim que o servidor é criado. No futuro, será possível atualizar ou mudar de um servidor de um escalão de preço para outro escalão.

## <a name="understand-the-price"></a>Compreender o preço
Quando cria uma nova base de dados do Azure para PostgreSQL no interior de [portal do Azure](https://portal.azure.com/#create/Microsoft.PostgreSQLServer), clique em de **escalão de preço** página e o custo mensal é apresentado com base nas opções que selecionou. Se não tiver uma subscrição do Azure, utilize a Calculadora de preços do Azure para obter um preço estimado. Visite o [Calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/) Web site, em seguida, clique em **adicionar itens**, expanda o **bases de dados** categoria e escolha **base de dados do Azure para PostgreSQL**  para personalizar as opções.

## <a name="choose-a-performance-level-compute-units"></a>Escolha o nível de desempenho (computação unidades)
Depois de ter determinado o escalão de preço da base de dados do Azure para o servidor de PostgreSQL, está pronto para determinar o nível de desempenho ao selecionar o número de unidades de computação for necessário. Um ponto de partida é 200 ou 400 unidades de computação para aplicações que necessitam de maior simultaneidade de utilizador para os seus web ou cargas de trabalho analíticas e ajustar incrementalmente conforme necessário. 

Computação que unidades são uma medida de débito de processamento de CPU que fique disponível para uma única base de dados do Azure para o servidor de PostgreSQL. Uma unidade de computação é uma medida combinada de CPU e memória recursos.  Para obter mais informações, consulte [explicar unidades computação](concepts-compute-unit-and-storage.md).

### <a name="basic-pricing-tier-performance-levels"></a>Básico preço camada níveis de desempenho:

| **Nível de desempenho** | **50** | **100** |
| :-------------------- | :----- | :------ |
| Unidades de computação máx. | 50 | 100 |
| Tamanho de armazenamento incluídos | 50 GB | 50 GB |
| Tamanho máximo de armazenamento de servidor\* | 1 TB | 1 TB |

### <a name="standard-pricing-tier-performance-levels"></a>Standard preço camada níveis de desempenho:

| **Nível de desempenho** | **100** | **200** | **400** | **800** |
| :-------------------- | :------ | :------ | :------ | :------ |
| Unidades de computação máx. | 100 | 200 | 400 | 800 |
| Tamanho de armazenamento incluídos e IOPS aprovisionado | 125 GB,<br/> 375 IOPS | 125 GB,<br/> 375 IOPS | 125 GB,<br/> 375 IOPS | 125 GB,<br/> 375 IOPS |
| Tamanho máximo de armazenamento de servidor\* | 1 TB | 1 TB | 1 TB | 1 TB |
| Servidor de máx. aprovisionado IOPS | 3,000 IOPS | 3,000 IOPS | 3,000 IOPS | 3,000 IOPS |
| Servidor de máx. aprovisionado IOPS por GB | Corrigido 3 IOPS por GB | Corrigido 3 IOPS por GB | Corrigido 3 IOPS por GB | Corrigido 3 IOPS por GB |

\*Tamanho máximo de armazenamento de servidor refere-se ao tamanho máximo de armazenamento aprovisionada para o servidor.

## <a name="storage"></a>Armazenamento 
A configuração de armazenamento define a quantidade de capacidade de armazenamento disponível para uma base de dados do Azure para o servidor de PostgreSQL. O armazenamento utilizado pelo serviço inclui os ficheiros de base de dados, os registos de transações e os registos do servidor PostgreSQL. Considere o tamanho de armazenamento necessário para alojar as bases de dados e os requisitos de desempenho (IOPS), ao selecionar a configuração de armazenamento.

Alguns capacidade de armazenamento está incluída no mínimo, com cada escalão de preço, descrito na tabela anterior, como "Tamanho de armazenamento incluídos". Capacidade de armazenamento adicionais pode ser adicionada quando o servidor é criado, em incrementos de 125 GB, até o armazenamento máxima permitida. A capacidade de armazenamento adicionais pode ser configurada independentemente da configuração de unidades de computação. As alterações de preços com base na quantidade de armazenamento selecionada.

A configuração de IOPS cada nível de desempenho relacionado com o escalão de preço e o tamanho de armazenamento escolhido. O escalão básico não fornece uma garantia IOPS. Dentro do escalão de preço padrão, o IOPS Dimensionar proporcionalmente tamanho máximo de armazenamento num rácio de 3:1 fixo. O armazenamento incluído de garantias de 125 GB para 375 aprovisionado IOPS, cada um com um tamanho de e/s de 256 KB. Pode optar por armazenamento adicional cópias de segurança 1 TB no máximo, para garantir 3,000 IOPS aprovisionados.

Monitorizar o gráfico de métricas no portal do Azure ou escrever comandos da CLI do Azure para medir o consumo de armazenamento e de IOPS. Uma métrica relevante para monitorizar é limite de armazenamento, a percentagem de armazenamento, armazenamento utilizado e percentagem de es.

>[!IMPORTANT]
> Enquanto na pré-visualização, selecione a quantidade de armazenamento no momento quando o servidor é criado. Ainda, o alterar o tamanho de armazenamento num servidor existente não é suportado. 

## <a name="scaling-a-server-up-or-down"></a>Dimensionamento de um servidor de cópia de segurança ou para baixo
Inicialmente, escolha o nível de desempenho e o escalão de preço ao criar a base de dados do Azure para PostgreSQL. Mais tarde, pode dimensionar as unidades de computação ou de encerramento dinamicamente, dentro do intervalo do mesmo escalão de preço. No portal do Azure, deslize as unidades de computação na página de escalão de preço do servidor ou, neste exemplo a seguir para o script: [monitorizar e dimensionar um único servidor PostgreSQL utilizando a CLI do Azure](scripts/sample-scale-server-up-or-down.md)

As unidades de computação de dimensionamento é feito independentemente do tamanho máximo de armazenamento que escolheu.

Nos bastidores, alterar o nível de desempenho de uma base de dados cria uma cópia do servidor original ao nível de desempenho do novo e, em seguida, passa ligações para o servidor copiado. Não se tenha perdido nenhum dado durante este processo. Durante o momento em breve quando o sistema passa para a nova cópia do servidor, as ligações à base de dados estão desativadas, para que alguns as transações em trânsito podem ser revertidas. Esta janela varia, mas em média 4 segundos e em mais do que 99% contra casos é inferior a 30 segundos. Se existir um grande número de transações em voo no momento em que as ligações são desativadas, esta janela pode demorar mais.

A duração do processo de escala completa depende do tamanho tanto escalão do servidor de preços antes e após a alteração. Por exemplo, um servidor que está a alterar unidades de computação dentro do escalão de preço padrão, deve ser concluído dentro de alguns minutos. Novas propriedades para o servidor não são aplicadas até que as alterações foram concluídas.

## <a name="next-steps"></a>Passos seguintes
- Para mais informações sobre unidades de computação, consulte [explicar unidades de computação](concepts-compute-unit-and-storage.md)
- Saiba como [monitorizar e dimensionar um único servidor PostgreSQL utilizando a CLI do Azure](scripts/sample-scale-server-up-or-down.md)
