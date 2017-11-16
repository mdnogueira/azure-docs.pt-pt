---
title: "Níveis de consistência na base de dados do Azure Cosmos | Microsoft Docs"
description: "BD do Azure do Cosmos tem cinco níveis de consistência para ajudar a balancear eventual consistência, disponibilidade e a latência compromissos."
keywords: "consistência eventual, azure cosmos db, do azure, do Microsoft azure"
services: cosmos-db
author: mimig1
manager: jhubbard
editor: cgronlun
documentationcenter: 
ms.assetid: 3fe51cfa-a889-4a4a-b320-16bf871fe74c
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: mimig
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 303a36fc966cd92399de92b4d52f75c114b75781
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="tunable-data-consistency-levels-in-azure-cosmos-db"></a>Níveis de consistência sincronizáveis dados na base de dados do Azure Cosmos
BD do Azure do Cosmos foi concebido partir do zero cópias de segurança com distribuição global em mente para cada modelo de dados. Foi concebido para oferecer vários modelos de simples de consistência bem definidos e de garantias de latência baixa previsível. Atualmente, a base de dados do Azure Cosmos fornece cinco níveis de consistência: forte, consistência vinculada, sessão, prefixo consistente e eventual. Consistência vinculada, sessão, prefixo consistente e eventual são designados "modelos de consistência simples" que fornecem o menor consistência que segura, que é o modelo a maioria das consistente altamente disponível. 

Besides o **forte** e **consistência eventual** modelos oferecidas normalmente pelas bases de dados distribuídas, BD do Cosmos Azure oferece três modelos de consistência cuidadosamente codified e operacionalizado mais:  **tem um vínculo vinculada**, **sessão**, e **prefixo consistente**. A utilidade dos cada um destes níveis de consistência foi validada contra casos de utilização do mundo real. Coletivamente estes níveis de cinco consistência permitem-lhe efetuar compromissos bem reasoned entre consistência, disponibilidade e a latência. 

## <a name="distributed-databases-and-consistency"></a>Bases de dados distribuídas e consistência
Bases de dados distribuídas comerciais enquadram-se em duas categorias: bases de dados que oferece opções de consistência provable bem definidos e as bases de dados que oferecem duas opções de programação para Alpine (fortes vs. a consistência eventual). 

A primeira opção sobrecarrega os programadores de aplicações com a minúcia dos seus protocolos de replicação e exige que haja compromissos difíceis entre consistência, disponibilidade, latência e débito. A segunda opção obriga a que se tenha de escolher entre dois extremos. Não obstante a abundância de pesquisa e propostas para mais de 50 modelos de consistência, a comunidade da base de dados distribuída não conseguiu comercializar níveis de consistência para além da consistência forte e da consistência eventual. BD do cosmos permite aos programadores escolher entre cinco modelos de consistência bem definidos ao longo do espetro consistência – forte, obsoletismo, [sessão](http://dl.acm.org/citation.cfm?id=383631), prefixo consistente e eventual. 

![O Azure Cosmos DB proporciona vários modelos de consistência (flexíveis) bem definidos à escolha](./media/consistency-levels/five-consistency-levels.png)

A tabela seguinte ilustra as garantias específicas proporcionadas por cada nível de consistência.
 
**Níveis de Consistência e garantias**

| Nível de Consistência | Garantias |
| --- | --- |
| Forte | Linearizability. Leituras garantidas para devolver a versão mais recente de um item.|
| Estagnação Limitada | Prefixo Consistente. Tempo de desfasamento de leituras em escritas por prefixos k ou intervalo t |
| Sessão   | Prefixo Consistente. Leituras monotónicas, escritas monotónicas, leitura das próprias escritas, escrita de acordo com leituras |
| Prefixo Consistente | As atualizações devolvidas são alguns prefixos de todas as atualizações, sem intervalos |
| Eventual  | Leituras fora de ordem |

Pode configurar o nível predefinido de consistência na sua conta do Cosmos DB (e, mais tarde, substituir a consistência num pedido de leitura específico). Internamente, o nível de consistência predefinida aplica-se aos dados dentro de conjuntos de partição, o que poderão span regiões. 73 cerca de % de consistência de sessão de utilização do Azure Cosmos DB inquilinos e 20% preferem obsoletismo. % De 3 aproximadamente dos clientes de base de dados do Azure Cosmos experimentar vários níveis de consistência inicialmente antes settling numa escolha específica de consistência para a respetiva aplicação. Apenas 2% de inquilinos do Azure Cosmos DB substituir níveis de consistência numa base por pedido. 

Na base de dados do Cosmos, leituras servido em sessão, prefixo consistente e a consistência eventual duas vezes são como cheap como leituras com consistência forte ou vinculada vinculada. BD do cosmos tem SLAs abrangentes, incluindo garantias de consistência, juntamente com a disponibilidade, débito e latência de líderes da indústria. BD do Azure do Cosmos emprega um [verificador linearizability](http://dl.acm.org/citation.cfm?id=1806634), que funciona continuamente ao longo de telemetria de serviço e reporta abertamente quaisquer violações de consistência para si. Para obsoletismo, base de dados do Azure Cosmos monitoriza e reporta quaisquer violações para limites de k e t. Para todos os cinco níveis de consistência simples, base de dados do Azure Cosmos também comunica o [probabilistically tem um vínculo métrica vinculada](http://dl.acm.org/citation.cfm?id=2212359) diretamente a si.  

## <a name="service-level-agreements"></a>Contratos de nível de serviço

BD do Azure do Cosmos oferece abrangente 99,99% [SLAs](https://azure.microsoft.com/support/legal/sla/cosmos-db/) que o débito de garantia, consistência, disponibilidade e latência de base de dados do Azure Cosmos da base de dados contas no âmbito de uma única região do Azure configurada com qualquer um da consistência cinco níveis ou contas de base de dados várias regiões do Azure, configuradas com qualquer um dos quatro níveis de consistência simples de expansão. Além disso, é independente da escolha de um nível de consistência, base de dados do Azure Cosmos oferece um SLA de 99.999% de disponibilidade de leitura para contas de base de dados expansão dois ou mais regiões do Azure.

## <a name="scope-of-consistency"></a>Âmbito de consistência
A granularidade de consistência é confinada para um pedido de utilizador único. Um pedido de escrita pode corresponder a um inserir, substituir, upsert ou eliminar a transação. Tal como acontece com escritas, também tem um âmbito uma transação de leitura/consulta a um pedido de utilizador único. O utilizador poderá ser necessário paginação através de um grande conjunto de resultados, a expansão de várias partições, mas cada ler transação está no âmbito de uma única página e servida a partir de uma única partição.

## <a name="consistency-levels"></a>Níveis de consistência
Pode configurar um nível de consistência predefinida na sua conta de base de dados que se aplica a todas as coleções (e as bases de dados) na sua conta de base de dados do Cosmos. Por predefinição, todas as operações de leitura e consultas emitidas relativamente aos recursos definida pelo utilizador utilizam o nível de consistência predefinido, especificado na conta de base de dados. Pode reduzir o nível de consistência de uma utilização de pedido de leitura/consulta específicas em cada uma das APIs suportados. Existem cinco tipos dos níveis de consistência suportados pelo protocolo de replicação de base de dados do Azure Cosmos que fornecem um compromisso claro entre garantias de consistência específico e o desempenho, conforme descrito nesta secção.

**Forte**: 

* A consistência forte oferece um [linearizability](https://aphyr.com/posts/313-strong-consistency-models) garantir com leituras garantidas para devolver a versão mais recente de um item. 
* A consistência forte garante que uma escrita apenas seja visível depois de ser consolidada de forma durável pelo quórum maioria das réplicas. Uma operação de escrita é colocada forma síncrona consolidada de forma durável pela principal e o quórum de secundárias ou foi abortada. Uma leitura é sempre confirmada pela maioria de quórum de leitura, um cliente nunca pode ver uma escrita não consolidada ou parcial e é sempre garantido para ler mais recente confirmada de escrita. 
* As contas de base de dados do Cosmos do Azure que estão configuradas para utilizar a consistência forte não é possível associar mais do que uma região do Azure com a conta de base de dados do Azure Cosmos.  
* O custo de uma operação de leitura (em termos de [unidades de pedido](request-units.md) consumido) com consistência forte é superior a sessão e eventual, mas o mesmo que obsoletismo.

**Tem um vínculo vinculada**: 

* Tem um vínculo vinculada a consistência garante que as leituras poderão ficar mais lentos durante escritas no máximo *K* versões ou prefixos de um item ou *t* intervalo de tempo. 
* Por conseguinte, quando escolher consistência vinculada, "vinculada" pode ser configurada de duas formas: número de versões *K* do item através do qual as leituras ficar mais lentos durante as escritas e o intervalo de tempo *t* 
* Tem um vínculo vinculada ofertas global ordem total, exceto "janela vinculada". As garantias de leitura monotonic existem numa região dentro e fora da "vinculada janela." 
* Obsoletismo fornece uma mais forte garantia de consistência de sessão, consistente prefixo ou consistência eventual. Para aplicações distribuídas globalmente, recomendamos que utilize obsoletismo para cenários onde pretende tenham consistência forte, mas também 99,99% de disponibilidade e a latência baixa.   
* As contas de base de dados do Cosmos do Azure que estão configuradas com consistência de obsoletismo podem associar a respetiva conta de base de dados do Azure Cosmos qualquer número de regiões do Azure. 
* O custo de uma operação de leitura (em termos de RUs consumido) com obsoletismo é superior a sessão e a consistência eventual, mas o mesmo que a consistência forte.

**Sessão**: 

* Ao contrário dos modelos de consistência global oferecidos pelos níveis de consistência forte e vinculada vinculada a consistência de sessão tem um âmbito a uma sessão de cliente. 
* A consistência de sessão é ideal para todos os cenários em que uma sessão de dispositivo ou utilizador está envolvida uma vez que esta situação garante monotonic leituras, escritas monotonic e leitura garante que os seus próprios escritas (RYW). 
* A consistência de sessão fornece a consistência previsível para uma sessão e débito de leitura máximo oferecendo a mais baixa latência escritas e leituras. 
* As contas de base de dados do Cosmos do Azure que estão configuradas com consistência de sessão podem associar a respetiva conta de base de dados do Azure Cosmos qualquer número de regiões do Azure. 
* O custo de uma operação de leitura (em termos de RUs consumido) com o nível de consistência de sessão está vinculada menos segura e vinculada, mas a consistência eventual mais.

<a id="consistent-prefix"></a>
**Prefixo consistente**: 

* Prefixo consistente garante que na ausência de mais escritas, as réplicas dentro do grupo de eventualmente convergir. 
* Prefixo consistente garante que leituras nunca veem escritas fora de ordem. Se escritas foram executadas na ordem `A, B, C`, em seguida, um cliente, este verá a `A`, `A,B`, ou `A,B,C`, mas nunca fora de ordem como `A,C` ou `B,A,C`.
* As contas de base de dados do Cosmos do Azure que estão configuradas com consistência de prefixo consistentes podem associar a respetiva conta de base de dados do Azure Cosmos qualquer número de regiões do Azure. 

**Eventual**: 

* A consistência eventual garante que na ausência de mais escritas, as réplicas dentro do grupo de eventualmente convergir. 
* A consistência eventual é a forma mais fraca de consistência em que um cliente pode obter os valores que são mais antigos do que aqueles que tinha vistos anteriormente.
* A consistência eventual fornece a consistência de leitura mais fraca, mas assegura a latência mais baixa para leituras e escritas.
* As contas de base de dados do Cosmos do Azure que estão configuradas com a consistência eventual podem associar a respetiva conta de base de dados do Azure Cosmos qualquer número de regiões do Azure. 
* O custo de uma operação de leitura (em termos de RUs consumido) com a consistência eventual nível é a mais baixa de todos os níveis de consistência da base de dados do Azure Cosmos.

## <a name="configuring-the-default-consistency-level"></a>Configurar o nível de consistência predefinida
1. No [portal do Azure](https://portal.azure.com/), no Jumpbar, clique em **Azure Cosmos DB**.
2. No **Azure Cosmos DB** página, selecione a conta de base de dados para modificar.
3. Na página de conta, clique em **predefinido consistência**.
4. No **consistência predefinida** página, selecione o novo nível de consistência e clique em **guardar**.
   
    ![Realce o ícone de definições e a entrada de consistência predefinida de captura de ecrã](./media/consistency-levels/database-consistency-level-1.png)

## <a name="consistency-levels-for-queries"></a>Níveis de consistência para consultas
Por predefinição, para obter recursos definida pelo utilizador, o nível de consistência para consultas é o mesmo que o nível de consistência para leituras. Por predefinição, o índice é atualizado em sincronia cada inserir, substituir ou eliminar de um item no contentor de BD do Cosmos. Isto permite que as consultas que respeite o mesmo nível de consistência da leituras de ponto. Enquanto a base de dados do Azure Cosmos é escrita otimizada e suporta volumes constante de escritas, índice síncrona e de manutenção que serve consultas consistentes, pode configurar determinadas coleções para atualizar o respetivo índice lenta. Ainda mais lento de indexação boosts o desempenho de escrita e é ideal para cenários de ingestão em massa, quando uma carga de trabalho é principalmente leitura extremamente encriptados.  

| Modo de indexação | Lê | Consultas |
| --- | --- | --- |
| Consistente (predefinição) |Selecione a partir de vinculada forte e vinculada, sessão, prefixo consistente ou eventual |Selecione a partir de vinculada forte e vinculada, sessão, ou eventual |
| Lento |Selecione a partir de vinculada forte e vinculada, sessão, prefixo consistente ou eventual |Eventual |
| Nenhuma |Selecione a partir de vinculada forte e vinculada, sessão, prefixo consistente ou eventual |Não aplicável |

Como com pedidos de leitura, pode reduzir o nível de consistência de um pedido de consulta específicas em cada API.

## <a name="next-steps"></a>Passos seguintes
Se gostaria de fazer ler mais sobre os níveis de consistência e fala, recomendamos os seguintes recursos:

* Tiago Doug. Consistência de dados replicadas explicado através de baseball (vídeo).   
  [https://www.YouTube.com/Watch?v=gluIh8zd26I](https://www.youtube.com/watch?v=gluIh8zd26I)
* Tiago Doug. Consistência de dados replicadas explicado através de baseball.   
  [http://Research.microsoft.com/pubs/157411/ConsistencyAndBaseballReport.pdf](http://research.microsoft.com/pubs/157411/ConsistencyAndBaseballReport.pdf)
* Tiago Doug. Garantias de sessão para os dados replicados consistentes Weakly.   
  [http://DL.ACM.org/CITATION.cfm?ID=383631](http://dl.acm.org/citation.cfm?id=383631)
* Daniel Abadi. Consistência fala moderna design de sistemas de base de dados distribuída: CAP é apenas uma parte do bloco ".   
  [http://Computer.org/CSDL/mags/co/2012/02/mco2012020037-Abs.HTML](http://computer.org/csdl/mags/co/2012/02/mco2012020037-abs.html)
* Peter Bailis Shivaram Venkataraman, Miguel J. Franklin, Joseph M. Hellerstein, Stoica período. Probabilistic tem um vínculo vinculada (. PBS) para práticas Quorums parciais.   
  [http://VLDB.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf](http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
* Werner Vogels. Eventual consistente - Revisitado.    
  [http://allthingsdistributed.com/2008/12/eventually_consistent.HTML](http://allthingsdistributed.com/2008/12/eventually_consistent.html)
* Moni Naor, Avishai Wool, a carga, capacidade e disponibilidade dos sistemas de quórum, SIAM diário de alterações em informática, v.27 n.2, p.423-447, Abril de 1998.
  [http://epubs.siam.org/DOI/Abs/10.1137/S0097539795281232](http://epubs.siam.org/doi/abs/10.1137/S0097539795281232)
* Sebastian Burckhardt, Chris Dern, Macanal Musuvathi, Roy Tan, Line-up: um linearizability completa e automática verificador, Proceedings do conferência ACM SIGPLAN 2010 no idioma de conceção e implementação, 05 10 de Junho de 2010, Toronto, Ontario, Canadá de programação [doi > 10.1145/1806596.1806634] [http://dl.acm.org/citation.cfm?id=1806634](http://dl.acm.org/citation.cfm?id=1806634)
* Peter Bailis, Shivaram Venkataraman, Miguel J. Franklin, Joseph M. Hellerstein, período Stoica, Probabilistically tem um vínculo vinculada para práticas quorums parciais, Proceedings do Endowment VLDB, v.5 n.8, p.776-787, Abril de 2012 [http://dl.acm.org/citation.cfm?id=2212359](http://dl.acm.org/citation.cfm?id=2212359)
