---
title: "Utilize as tabelas de dados e de pesquisa de referência do Stream Analytics | Microsoft Docs"
description: "Utilizar dados de referência numa consulta do Stream Analytics"
keywords: "tabela de referência, os dados de referência"
services: stream-analytics
documentationcenter: 
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 06103be5-553a-4da1-8a8d-3be9ca2aff54
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: samacha
ms.openlocfilehash: 438ec565f3c6e06ab7ec92cf1bbfbdde88f99b6d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="using-reference-data-or-lookup-tables-in-a-stream-analytics-input-stream"></a>Utilizar as tabelas de dados ou de pesquisa de referência num fluxo de entrada do Stream Analytics
Dados de referência (também conhecido como uma tabela de pesquisa) são um conjunto de dados finito que é estático ou abrandamento alterar natureza, utilizada para efetuar uma pesquisa de ou para correlacionar com o fluxo de dados. Para tornar a utilização de dados de referência na sua tarefa do Azure Stream Analytics, normalmente, utilizará um [associação de dados de referência](https://msdn.microsoft.com/library/azure/dn949258.aspx) na sua consulta. Do Stream Analytics utiliza o Blob storage do Azure como a camada de armazenamento para dados de referência e com a referência do Azure Data Factory dados podem ser transformados e/ou copiados para o armazenamento de Blobs do Azure, para utilização como dados de referência, [qualquer número de baseado na nuvem e arquivos de dados no local](../data-factory/copy-activity-overview.md). Dados de referência são modelados como uma sequência de blobs (definido na configuração de entrada) por ordem de data/hora especificada no nome do blob ascendente. - **Apenas** suporta a adicionar ao fim da sequência com uma data/hora **maior** à especificada pela última blob na sequência.

Do Stream Analytics tem uma **limite de 100 MB por blob** mas tarefas podem processar vários blobs de referência utilizando o **padrão do caminho** propriedade.


## <a name="configuring-reference-data"></a>Configurar dados de referência
Para configurar os dados de referência, terá primeiro de criar uma entrada que é do tipo **dados de referência**. A tabela abaixo explica cada propriedade que terá de fornecer ao criar os dados de referência de entrada com a respetiva descrição:


<table>
<tbody>
<tr>
<td>Nome da propriedade</td>
<td>Descrição</td>
</tr>
<tr>
<td>Alias de entrada</td>
<td>Um nome amigável que será utilizado na consulta de tarefas para fazer referência a esta entrada.</td>
</tr>
<tr>
<td>Conta de Armazenamento</td>
<td>O nome da conta do storage onde estão localizados os blobs. Se estiver na mesma subscrição que a tarefa do Stream Analytics, pode selecionar da lista pendente.</td>
</tr>
<tr>
<td>Chave de conta de armazenamento</td>
<td>A chave secreta associada à conta de armazenamento. Este obtém preenchido automaticamente se a conta do storage na mesma subscrição que a tarefa de Stream Analytics.</td>
</tr>
<tr>
<td>Contentor de armazenamento</td>
<td>Contentores fornecem um agrupamento lógico blobs armazenados no Microsoft serviço Blob do Azure. Quando carregar um blob para o serviço Blob, tem de especificar um contentor para esse blob.</td>
</tr>
<tr>
<td>Padrão de caminho</td>
<td>O caminho utilizado para localizar os blobs no contentor especificado. No caminho, pode optar por especificar uma ou mais instâncias das 2 variáveis seguintes:<BR>{date}, {time}<BR>Exemplo 1: products/{date}/{time}/product-list.csv<BR>Exemplo 2: products/{date}/product-list.csv
</tr>
<tr>
<td>Formato de data [opcional]</td>
<td>Se tiver utilizado {date} dentro o padrão do caminho que especificou, pode selecionar o formato da data em que os blobs são organizados de lista pendente dos formatos suportados.<BR>Exemplo: DD/MM/DD, MM/DD/AAAA, etc.</td>
</tr>
<tr>
<td>Formato de hora [opcional]</td>
<td>Se tiver utilizado {time} dentro o padrão do caminho que especificou, pode selecionar o formato de hora em que os blobs são organizados de lista pendente dos formatos suportados.<BR>Exemplo: HH, HH/mm ou dd HH</td>
</tr>
<tr>
<td>Formato de serialização de eventos</td>
<td>Para se certificar de que as suas consultas funcionam do modo esperado, o Stream Analytics precisa de saber qual o formato de serialização está a utilizar para fluxos de dados recebidos. Para dados de referência, os formatos suportados são CSV e JSON.</td>
</tr>
<tr>
<td>Encoding</td>
<td>UTF-8 é o único formato de codificação suportado neste momento</td>
</tr>
</tbody>
</table>

## <a name="generating-reference-data-on-a-schedule"></a>Gerar dados de referência com base numa agenda
Se os dados de referência for um conjunto de dados de alteração lenta, em seguida, suporte para atualizar a referência de dados são ativados especificando um padrão de caminho na configuração de entrada utilizando {date} e {time} tokens de substituição. Do Stream Analytics seleciona as definições de dados de referência atualizados com base num padrão caminho. Por exemplo, um padrão de `sample/{date}/{time}/products.csv` com um formato de data do **"Aaaa-MM-DD"** e um formato de hora do **"Dd HH"** dá instruções ao Stream Analytics para recolher o blob atualizado `sample/2015-04-16/17-30/products.csv` nas 17:30:00 em 16th de Abril , Fuso horário UTC 2015.

> [!NOTE]
> Atualmente tarefas do Stream Analytics procure a atualização de blob apenas quando a hora do computador avança para o tempo codificado no nome do blob. Por exemplo, a tarefa irá procurar `sample/2015-04-16/17-30/products.csv` logo que possível, mas não anteriormente que 5:30 PM 16th de Abril de 2015 UTC tempo zona. Este irá *nunca* procure um blob com um período de tempo codificado anteriores à última que é detetado.
> 
> Por exemplo, Depois da tarefa localiza o blob `sample/2015-04-16/17-30/products.csv` ignorará quaisquer ficheiros com uma data codificado anteriores ao 5:30 da Tarde 16th de Abril de 2015, pelo que, se um enlace tardio chegar `sample/2015-04-16/17-25/products.csv` blob é criado no mesmo contentor a tarefa não utilizá-lo.
> 
> Da mesma forma se `sample/2015-04-16/17-30/products.csv` apenas é produzido nas 22:03:00 16th de Abril de 2015, mas não existem BLOBs com uma data anterior está presente no contentor, a tarefa irá utilizar este ficheiro começando as 22:03:00 16th de Abril de 2015 e utilizar os dados de referência anterior até.
> 
> Uma exceção é quando a tarefa tem de voltar a processar dados para trás no tempo ou quando a tarefa é o primeira foi iniciada. Hora de início da tarefa está à procura de blob mais recente produzido antes da tarefa iniciar o período de tempo especificado. Isto é feito para garantir que existe um **vazios** referenciar o conjunto de dados quando a tarefa é iniciada. Se não for encontrado, a tarefa apresenta o diagnóstico seguinte: `Initializing input without a valid reference data blob for UTC time <start time>`.
> 
> 

[O Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) podem ser utilizados para orquestrar a tarefa de criação os blobs atualizados necessários pelo Stream Analytics para atualizar as definições de dados de referência. Data Factory é um serviço de integração de dados baseado na nuvem que orquestra e automatiza o movimento e a transformação de dados. Fábrica de dados suporta [ligar a um grande número de nuvem com base e arquivos de dados no local](../data-factory/copy-activity-overview.md) e mover dados facilmente regularmente que especificar. Para obter mais informações e orientações passo a passo sobre como configurar um pipeline do Data Factory para gerar dados de referência para o Stream Analytics que é atualizada com base num agendamento predefinido, veja isto [GitHub exemplo](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ReferenceDataRefreshForASAJobs).

## <a name="tips-on-refreshing-your-reference-data"></a>Sugestões sobre como atualizar os dados de referência
1. Substituir blobs de dados de referência não irá causar Stream Analytics recarregar o blob e, em alguns casos, pode fazer com que a tarefa falhar. A forma recomendada para alterar os dados de referência está a adicionar um novo blob com o mesmo padrão de contentor e o caminho definido na entrada de tarefa e utilizar uma data/hora **maior** à especificada pela última blob na sequência.
2. Os blobs de dados de referência são **não** ordenada por hora do blob da "Última modificação", mas apenas pela hora e data especificada no blob nome utilizando {date} e {time} substituições.
3. Em ocasiões, alguns, uma tarefa tem de voltar atrás no tempo, por conseguinte blobs de dados de referência não tem de ser alterados ou eliminados.

## <a name="get-help"></a>Obter ajuda
Para mais assistência, tente ler o nosso [fórum do Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Passos seguintes
Agora já conhece o Stream Analytics, um serviço gerido para a transmissão da análise dos dados a partir da Internet das Coisas. Para obter mais informações sobre este serviço, consulte:

* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
