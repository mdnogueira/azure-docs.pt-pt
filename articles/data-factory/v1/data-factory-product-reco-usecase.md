---
title: "Caso de utilização de fábrica de dados - recomendações de produto"
description: "Saiba mais sobre um caso de utilização implementado através do Azure Data Factory, juntamente com outros serviços."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: 6f1523c7-46c3-4b8d-9ed6-b847ae5ec4ae
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2017
ms.author: shlo
robots: noindex
ms.openlocfilehash: e72dd426f7af3d1539aad6a3499d2ce5f792c152
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="use-case---product-recommendations"></a>Caso de Utilização - Recomendações do Produto
O Azure Data Factory é um dos muitos serviços utilizados para implementar o Cortana Intelligence Suite de Aceleradores de solução.  Consulte [Cortana Intelligence Suite](http://www.microsoft.com/cortanaanalytics) página para obter detalhes sobre este conjunto. Neste documento, vamos descrever um caso de utilização comuns que os utilizadores do Azure já resolvidos e implementadas utilizando o Azure Data Factory e outros serviços de componentes do Cortana Intelligence.

## <a name="scenario"></a>Cenário
Os retalhistas online pretendem normalmente entice respetivos clientes comprar produtos através da apresentação-los com os produtos que são mais provável estar interessado no e, por conseguinte, provavelmente, comprar. Para tal, retalhistas online tem de personalizar a experiência online do seu utilizador utilizando recomendações de produto personalizado para esse utilizador específico. Estas recomendações personalizadas estão a ser efetuadas com base nos respetivos históricos de compras de dados de comportamento, informações de produto, marcas recentemente introduzidas e dados de segmentação de produto e o cliente e o atual.  Além disso, podem fornecer as recomendações de produto do utilizador com base na análise do comportamento de utilização geral de todos os respetivos utilizadores combinadas.

O objetivo destas retalhistas é otimizar para conversões de clique para venda de utilizador e mais significativos beneficiar de receitas de vendas superiores.  Estes alcançarem esta conversão ao fornecer recomendações de produto contextuais, com base no comportamento com base nos interesses de cliente e ações. Neste caso de utilização, utilizamos retalhistas online como um exemplo de empresas que pretende otimizar para os seus clientes. No entanto, estes princípios aplicam-se para as empresas que pretender interagir com os seus clientes em torno da respetiva bens e serviços e melhorar a experiência de comprar dos seus clientes com recomendações de produto personalizado.

## <a name="challenges"></a>Desafios
Existem muitos desafios que enfrentam retalhistas online ao tentar implementar este tipo de caso de utilização. 

Em primeiro lugar, tem de ser ingeridos dados de formas e tamanhos diferentes de várias origens de dados, no local e na nuvem. Estes dados incluem dados de produto, dados de comportamento do histórico do cliente e dados de utilizador como o utilizador percorre o site de revenda online. 

Recomendações de produto do segundo, personalizado tem de ser razoável e com precisão calculadas e prever. Para além de produto, marca e dados de comportamento e o browser do cliente, retalhistas online também têm de incluir comentários de clientes no passado efetuar compras na fator na determinação das melhores recomendações de produto para o utilizador. 

Terceira, as recomendações tem de ser imediatamente deliverable ao utilizador para fornecer uma navegação totalmente integrada e a experiência de compra e fornecer as recomendações mais recentes e relevantes. 

Por fim, retalhistas tem de medir a eficácia do respetiva abordagem através do registo de segurança geral-vende cross-vende êxitos de vendas de conversão de clique e ajuste para as respetivas recomendações futuras.

## <a name="solution-overview"></a>Descrição geral da solução
Neste caso de utilização de exemplo foi resolvido e implementado por utilizadores real do Azure utilizando o Azure Data Factory e outros serviços de componentes do Cortana Intelligence, incluindo [HDInsight](https://azure.microsoft.com/services/hdinsight/) e [Power BI](https://powerbi.microsoft.com/).

O revendedor online utiliza um arquivo de Blob do Azure, um servidor SQL no local, BD SQL do Azure e um relacional data mart como as opções de armazenamento de dados em todo o fluxo de trabalho.  O arquivo de blob contém informações de cliente, dados de comportamento do cliente e dados de informações de produto. Os dados de informações de produto incluem informações de marca de produto e armazenada no local num armazém de dados SQL do catálogo de um produto. 

Todos os dados são combinados e sejam fornecidos um sistema de recomendação do produto para fornecer recomendações personalizadas com base nos interesses de cliente e ações, enquanto o utilizador percorre produtos no catálogo no Web site. Os clientes também veem os produtos que estão relacionados com o produto que estão a visualizar com base nos padrões de utilização de Web site geral que não estão relacionadas com qualquer um utilizador.

![Utilize o diagrama maiúsculas](./media/data-factory-product-reco-usecase/diagram-1.png)

Gigabytes web em bruto de ficheiros de registo são gerados diariamente a partir do site do revendedor online como semiestruturados ficheiros. Os ficheiros de registo web em bruto e as informações do catálogo de cliente e o produto é ingeridos regularmente para um Blob storage do Azure utilizando o movimento de dados globalmente implementado de fábrica de dados como um serviço. Os ficheiros de registo não processados para o dia são particionados (ano e mês) no armazenamento de BLOBs de armazenamento de longa duração.  [O Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) é utilizado para particionar os ficheiros de registo não processados no arquivo de blob e processar os registos transmissões em escala com scripts Hive e Pig. Web particionada os registos de dados é processada, em seguida, a extrair as entradas necessárias para uma sistema de recomendação para gerar as recomendações de produto personalizado de aprendizagem.

O sistema de recomendação utilizado para o machine learning neste exemplo é uma máquina de open source para aprender a plataforma de recomendação do [Apache Mahout](http://mahout.apache.org/).  Qualquer [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) ou pode ser aplicado modelo personalizado para o cenário.  O modelo de Mahout é utilizado para prever a semelhança entre itens no Web site com base nos padrões de utilização geral e para gerar recomendações personalizadas com base no utilizador individual.

Por fim, o conjunto de resultados de recomendações de produto personalizado é movido para um relacional data mart para consumo por Web site do revendedor.  O conjunto de resultados pode também ser acedido diretamente a partir do blob storage por outra aplicação ou foi movido para arquivos adicionais para outros consumidores e casos de utilização.

## <a name="benefits"></a>Benefícios
Otimizar a sua estratégia de recomendação do produto e alinhá-lo com os objetivos de negócio, a solução cumprido merchandising o revendedor online e os objetivos de marketing. Além disso, era possível operacionalizar e gerir o fluxo de trabalho de recomendação do produto de forma eficiente, fiável e económica. A abordagem efetuadas fácil para os mesmos atualizar o seu modelo e otimizar a respetiva eficácia com base em medidas de vendas êxitos de clique para conversão. Ao utilizar o Azure Data Factory, estes foram capazes de abandonar a respetiva gestão de recursos de nuvem manual demorado e dispendioso e mover para gestão de recursos de nuvem a pedido. Por conseguinte, era possível poupar tempo, dinheiro e reduzir o respectivo período para implementação da solução. Vistas de linhagem de dados e estado de funcionamento operacional do serviço ficaram visualizar e resolver problemas com a intuitiva fábrica de dados de monitorização e gestão da IU disponível a partir do portal do Azure. A solução agora pode ser agendada e gerida para que os dados concluídos fiável são produzidos e entregue aos utilizadores e dependências de processamento de dados e são geridas automaticamente sem qualquer intervenção humana.

Ao fornecer esta experiência compras personalizada, o revendedor online criado um cliente mais competitiva, interagir experiência e, por conseguinte, aumentar a satisfação do cliente de vendas e geral.

