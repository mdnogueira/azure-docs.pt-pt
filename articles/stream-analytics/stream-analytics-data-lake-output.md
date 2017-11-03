---
title: "Saída de arquivo Data Lake do Stream Analytics | Microsoft Docs"
description: "Configuração de autenticação e autorização de um Azure Data Lake Store numa tarefa do Stream Analytics"
keywords: 
services: stream-analytics
documentationcenter: 
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: ea5baafa-0054-4c70-973a-6a3a8c6eaffc
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/28/2017
ms.author: samacha
ms.openlocfilehash: 3d867df3ef875d5cc41de418c3d1d269ff751fda
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="stream-analytics-data-lake-store-output"></a>Saída de Stream Analytics Data Lake Store
Tarefas do Stream Analytics suportam vários métodos de saída, a ser um um [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/). O Azure Data Lake Store é um repositório de hiper escala a nível da empresa para cargas de trabalho de análise de macrodados. Arquivo data Lake permite-lhe armazenar dados de qualquer tamanho, tipo e velocidade de ingestão para análises exploratórias e operacionais.

## <a name="authorize-a-data-lake-store-account"></a>Autorizar a conta do Data Lake Store
1. Se a Data Lake Store é selecionada como uma saída no portal do Azure, será solicitado para autorizar a utilização do Data Lake Store existente ou para pedir acesso para o Data Lake Store através do Portal clássico.
   
   ![](media/stream-analytics-data-lake-output/stream-analytics-data-lake-output-authorization.png)  
   
2. Se já tiver acesso ao Data Lake Store, clique em "Autorizar agora" e por um breve período de tempo a página irá aparecer que indica "Redirecting para autorização". A página será automaticamente encerrado e será apresentada com a página que lhe permita configurar a saída do Data Lake Store.

Caso não tenha efetuado cópias de segurança para o Data Lake Store, pode seguir a hiperligação "Inscrever-me agora" para iniciar o pedido ou seguir o [obter instruções de introdução](../data-lake-store/data-lake-store-get-started-portal.md).

## <a name="configure-the-data-lake-store-output-properties"></a>Configurar as propriedades de saída do Data Lake Store
Depois de ter a conta de Data Lake Store autenticada, pode configurar as propriedades para a saída do Data Lake Store. A tabela abaixo é a lista de nomes de propriedade e a respetiva descrição para configurar a saída do Data Lake Store.

<table>
<tbody>
<tr>
<td><B>NOME DA PROPRIEDADE</B></td>
<td><B>DESCRIÇÃO</B></td>
</tr>
<tr>
<td>Alias de saída</td>
<td>Este é um nome amigável utilizado nas consultas para direcionar o resultado de consulta para este arquivo Data Lake.</td>
</tr>
<tr>
<td>Conta do Data Lake Store</td>
<td>O nome da conta do storage onde está a enviar o resultado. Será apresentada uma lista de contas de Data Lake Store, que o utilizador com sessão iniciada tem acesso.</td>
</tr>
<tr>
<td>Caminho do prefixo padrão [<I>opcional</I>]</td>
<td>O caminho do ficheiro utilizado para escrever ficheiros dentro do arquivo de conta especificado do Data Lake. <BR>{date}, {time}<BR>Exemplo 1: pasta1/logs / {date} / {time}<BR>Exemplo 2: pasta1/logs / {date}</td>
</tr>
<tr>
<td>Formato de data [<I>opcional</I>]</td>
<td>Se o token de data é utilizado no caminho de prefixo, pode selecionar o formato da data em que os ficheiros estão organizados. Exemplo: DD/MM/DD</td>
</tr>
<tr>
<td>Formato de hora [<I>opcional</I>]</td>
<td>Se o token de tempo é utilizado no caminho de prefixo, especifique o formato de hora em que os ficheiros estão organizados. Atualmente, o único valor suportado é HH.</td>
</tr>
<tr>
<td>Formato de serialização de eventos</td>
<td>Formato de serialização para dados de saída. JSON, CSV e Avro são suportados.</td>
</tr>
<tr>
<td>Encoding</td>
<td>Se o formato CSV ou JSON, uma codificação tem de ser especificada. UTF-8 é o único formato de codificação suportado neste momento.</td>
</tr>
<tr>
<td>Delimitador</td>
<td>Só é aplicável a serialização de CSV. Stream Analytics suporta um número de delimitadores comuns para serializar os dados CSV. Os valores suportados são vírgula, ponto e vírgula, espaço, separador e barra vertical.</td>
</tr>
<tr>
<td>formato</td>
<td>Só é aplicável a serialização do JSON. Separadas por linhas Especifica que a saída será formatada, fazendo com que cada objeto JSON separado por uma nova linha. A matriz Especifica que a saída será formatada como uma matriz de objetos JSON.</td>
</tr>
</tbody>
</table>

## <a name="renew-data-lake-store-authorization"></a>Renovar autorização do arquivo Data Lake
Atualmente, não há uma limitação em que o token de autenticação tem de ser atualizados manualmente todos os 90 dias para todas as tarefas com a saída do Data Lake Store. Também terá de voltar autenticar a sua conta do Data Lake Store, se tiver alterado a palavra-passe, uma vez que a tarefa foi criada ou pela última vez autenticada. Um sintoma de que este problema é um erro nos registos de operação com a indicação de necessidade de nova autorização e nenhum resultado da tarefa.

Para resolver este problema, parar a tarefa em execução e avance para a saída do Data Lake Store. Clique na ligação "Renovar autorização" e por um breve período de tempo a página irá aparecer que indica "Redirecting para autorização..". A página será fechada automaticamente e se tiver êxito, irá indicar "Autorização tem foi renovada com êxito". Pode, em seguida, clique em "Guardar" na parte inferior da página, é necessário e avançar ao reiniciar a tarefa da hora da última paragem para evitar a perda de dados.

![](media/stream-analytics-data-lake-output/stream-analytics-data-lake-output-renew-authorization.png)

