---
title: "Dividir coluna por utilizar o Azure Machine Learning Workbench de transformação de exemplo"
description: "O documento de referência para a transformação 'Dividir coluna por exemplo'"
services: machine-learning
author: ranvijaykumar
ms.author: ranku
manager: mwinkle
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, reference
ms.topic: article
ms.date: 09/14/2017
ms.openlocfilehash: 013c99045621e4651a44ab99c9f695fff6004654
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="split-column-by-example-transformation"></a>Coluna de divisão por transformação de exemplo
Esta transformação divide predictively o conteúdo de uma coluna nos limites significativos sem exigir a intervenção do utilizador. O algoritmo de divisão seleciona os limites depois de analisar o conteúdo da coluna. Estes limites pode ser definidos por
* Um delimitador fixo,
* Vários delimitadores arbitrários apresentação em particular contextos ou,
* Padrões de dados ou a determinados tipos de entidade

Os utilizadores também podem controlar o divisor comportamento o modo avançado, onde pode especificar os delimitadores ou ao fornecer exemplos de divisão pretendido.

Em teoria, também é possível efetuar operações de divisão no Workbench utilizando uma série de *derivar coluna, por exemplo* transformações. No entanto, se existirem várias colunas, efetuar a derivação de cada um desses individualmente mesmo abordagem por exemplo a utilizar pode ser muito demorada. Divisão preditiva permite dividir fácil sem o necessidade de fornecer exemplos para cada uma das colunas do utilizador. 

## <a name="how-to-perform-this-transformation"></a>Como efetuar esta transformação

1. Selecione a coluna que pretende dividir. 
2. Selecione **dividir coluna por exemplo** do **transforma** menu. Ou, faça duplo clique no cabeçalho da coluna selecionada e selecione **dividir coluna por exemplo** no menu de contexto. Abre o Editor de transformação e são adicionadas colunas novas junto a coluna selecionada. Neste momento, o Workbench analisa a coluna de entrada, determina os limites de divisão e synthesizes um programa para dividir a coluna, tal como apresentado na grelha. O programa sintetizado é executado relativamente a todas as linhas da coluna. Os delimitadores, se aplicável, são excluídos do resultado final.
3. Pode clicar no **modo avançado** para melhorar o controlo sobre a transformação de divisão. 
4. Reveja os resultados e clique em **OK** para aceitar a transformação. 

A transformação visa para produzir o mesmo número de colunas resultantes para todas as linhas. Se qualquer linha não pode ser dividida em determinado limites, produz *nulo* para todas as colunas por predefinição. Este comportamento pode ser alterado no **modo avançado**.

### <a name="transform-editor-advanced-mode"></a>Transformar o editor: modo avançado
**Modo avançado** fornece uma experiência mais rica para dividir colunas. 

Selecionar **manter o delimitador colunas** inclui os delimitadores o resultado final. Os delimitadores são excluídos por predefinição.

Especificar **delimitadores** substitui a lógica de seleção automática delimitador. Vários delimitadores, um em cada linha, podem ser especificados como **delimitadores**. Todas essas carateres são utilizados como delimitadores para dividir a coluna.

Por vezes, divisão de um valor num determinado limites produz um número diferente de colunas do que a maioria das outras pessoas. Nesses casos, **preencher direção** é utilizado para decidir a ordem pela qual as colunas devem ser preenchidas.

Clicar no **Mostrar sugestões de exemplos** apresenta as linhas representativos para o utilizador devem fornecer um exemplo de divisão. Utilizador pode clicar no **segurança** seta para a direita da linha sugerida para promover a linha como exemplo. 

O utilizador pode **eliminar a coluna** ou **novas colunas de inserção** ao clicar no cabeçalho de **exemplos tabela**.

Utilizador pode copiar e colar os valores de uma célula para outro para fornecer um exemplo de divisão.

Utilizador pode alternar entre o **modo básico** e **modo avançado** clicando nas ligações do Editor de transformação.

### <a name="editing-an-existing-transformation"></a>Editar uma transformação existente

Um utilizador pode editar uma existente **dividir coluna por exemplo** transformar selecionando **editar** opção do passo de transformação. Clicar no **editar** abre o Editor de transformação de mensagens em fila no **modo avançado**, e são apresentados todos os exemplos que foram fornecidos durante a criação de transformação.

## <a name="examples-of-splitting-on-a-fixed-single-character-delimiter"></a>Exemplos de divisão de um fixo, caráter delimitador

É comum de campos de dados ser separados por um único delimitador fixo como uma vírgula num formato CSV. A transformação de divisão tenta inferir estas delimitadores automaticamente. Por exemplo, no cenário seguinte-la automaticamente infere a "." como um delimitador.

### <a name="splitting-ip-addresses"></a>Endereços IP de dividir

Os valores na primeira coluna predictively estão divididos em quatro colunas.

|IP|IP_1|IP_2|IP_3|IP_4|
|:-----|:-----|:-----|:-----|:-----|
|192.168.0.102|192|168|0|102|
|192.138.0.101|192|138|0|101|
|192.168.0.102|192|168|0|102|
|192.158.1.202|192|158|1|202|
|192.168.0.102|192|168|0|102|
|192.169.1.102|192|169|1|102|

## <a name="examples-of-splitting-on-multiple-delimiters-within-particular-contexts"></a>Exemplos de divisão em vários delimitadores dentro de determinada contextos

Os dados do utilizador podem incluir muitos delimitadores diferentes separar campos diferentes. Além disso, apenas algumas ocorrências de uma cadeia delimitador podem ser um delimitador, mas não todas. Por exemplo, no caso de seguinte o conjunto de delimitadores necessário é "-",","e":" para produzir o resultado pretendido. No entanto, não cada ocorrência da ":" deve ser um ponto de divisão, uma vez que não queremos divide o tempo, mas mantenha-a numa única coluna. A transformação de divisão infere delimitadores dentro os contextos na qual ocorrem em dados de entrada em vez de quaisquer possíveis ocorrência do delimitador. A transformação também está ciente dos tipos de dados comuns, tais como as datas e horas.   

### <a name="splitting-store-opening-timings"></a>Dividir as temporizações de abertura do arquivo

Os valores a seguir *temporizações* coluna predictively obter dividida em nove colunas mostradas na tabela sob a mesma.

|Temporizações|
|:-----|
|Segunda - sexta: 7:00 am - 18:00:00, Sábado: 09:00:00 - as 17:00:00, Domingo: fechado|
|Segunda - sexta: 09:00:00 - 18:00:00, Sábado: 4 horas da manhã - 4:00 pm, Domingo: fechado|
|Segunda - sexta: 8:30 am - 7:00 pm, Sábado: 3 horas da manhã - 2:30 da tarde, Domingo: fechado|
|Segunda - sexta: 8 horas da manhã - 18:00:00, Sábado: 2 horas da manhã - às 15:00, Domingo: fechado|
|Segunda - sexta: 4 horas da manhã - 7:00 pm, Sábado: 09:00:00 - 4:00 pm, Domingo: fechado|
|Segunda - sexta: 8:30 am - 4:30 pm, Sábado: 09:00:00 - as 17:00:00, Domingo: fechado|
|Segunda - sexta: 5:30 am - 18:30:00, Sábado: 5 horas da manhã - 4:00 pm, Domingo: fechado|
|Segunda - sexta: 8:30 am - 8:30 da tarde, Sábado: 6:00:00 - as 17:00:00, Domingo: fechado|
|Segunda - sexta: 8 horas da manhã - 9:00 pm, Sábado: 09:00:00 - 8:00 pm, Domingo: fechado|
|Segunda - sexta: 10 horas da manhã - 9:30 da tarde, Sábado: 9:30 am - às 15:00, Domingo: fechado|

|Timings_1|Timings_2|Timings_3|Timings_4|Timings_5|Timings_6|Timings_7|Timings_8|Timings_9|
|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|
|Segunda-feira|Sexta-feira|7 horas da manhã|18:00:00|Sábado|09:00|17:00:00|Domingo|Fechado|
|Segunda-feira|Sexta-feira|09:00|18:00:00|Sábado|4 horas da manhã|4:00 pm|Domingo|Fechado|
|Segunda-feira|Sexta-feira|8:30 am|19:00:00|Sábado|3 horas da manhã|2:30 pm|Domingo|Fechado|
|Segunda-feira|Sexta-feira|8 horas da manhã|18:00:00|Sábado|2 horas da manhã|3:00 pm|Domingo|Fechado|
|Segunda-feira|Sexta-feira|4 horas da manhã|19:00:00|Sábado|09:00|4:00 pm|Domingo|Fechado|
|Segunda-feira|Sexta-feira|8:30 am|4:30 pm|Sábado|09:00|17:00:00|Domingo|Fechado|
|Segunda-feira|Sexta-feira|5:30 am|18:30:00|Sábado|5 horas da manhã|4:00 pm|Domingo|Fechado|
|Segunda-feira|Sexta-feira|8:30 am|8:30 pm|Sábado|6:00:00|17:00:00|Domingo|Fechado|
|Segunda-feira|Sexta-feira|8 horas da manhã|9:00 pm|Sábado|09:00|8:00 pm|Domingo|Fechado|
|Segunda-feira|Sexta-feira|10 horas da manhã|9:30 pm|Sábado|09:30:00|3:00 pm|Domingo|Fechado|

### <a name="splitting-iis-log"></a>Divisor registo do IIS

Segue-se outro exemplo de vários delimitadores arbitrários. Este exemplo também inclui um delimitador contextual "/", que não devem ser divididos dentro os URLs ou caminhos de ficheiros. É tedious efetuar esta divisão utiliza muitos *derivar coluna, por exemplo* transformações e dar exemplos para cada campo. A transformação de divisão a utilizar, pode efetuar divisão preditiva sem fornecer qualquer exemplos.

|logtext|
|:-----|
|192.128.138.20 – [FPO/16/2016 16:22:33-0200] "GET /images/picture.gif HTTP/1.1" 234 343 www.yahoo.com "http://www.example.com/" "Mozilla/4.0 (compatível; MSIE 4)""-"|
|10.128.72.213 – [FPO/17/2016 12:43:12 +0300] "GET /news/stuff.html HTTP/1.1" 200 www.aol.com 6233 "http://www.sample.com/" "Mozilla/5.0 (MSIE)" "-"|
|192.165.71.165 – [Novembro/12/2016 14:22:44-0500] "GET /sample.ico HTTP/1.1" 342 7342 www.facebook.com "-" "Mozilla/5.0 (Windows; U; Windows NT 5.1; Rv:1.7.3) ""-"|
|10.166.64.165 – [Novembro/23/2016 01:52:45-0800] "GET /style.css HTTP/1.1" 200 2552 www.google.com "http://www.test.com/index.html" "Mozilla/5.0 (Windows)" "-"|
|192.167.1.193 – [Jan/16/2017 22:34:56 +0200] "GET /js/ads.js HTTP/1.1" 200 23462 www.microsoft.com "http://www.illustration.com/index.html" "Mozilla/5.0 (Windows)" "-"|
|192.147.76.193 – [Jan/28/2017 26:36:16 +0800] "GET /search.php HTTP/1.1" 400 1777 www.bing.com "-" "Mozilla/4.0 (compatível; MSIE 6.0; Windows NT 5.1)""-"|
|192.166.64.165 – [Mar/23/2017 01:55:25-0800] "GET /style.css HTTP/1.1" 200 2552 www.google.com "http://www.test.com/index.html" "Mozilla/5.0 (Windows)" "-"|
|11.167.1.193 – [Apr/16/2017 11:34:36 +0200] "GET /js/ads.js HTTP/1.1" 200 23462 www.microsoft.com "http://www.illustration.com/index.html" "Mozilla/5.0 (Windows)" "-"|

Obtém dividida em:

|logtext_1|logtext_2|logtext_3|logtext_4|logtext_5|logtext_6|logtext_7|logtext_8|logtext_9|logtext_10|logtext_11|logtext_12|logtext_13|logtext_14|logtext_15|
|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|
|192.128.138.20|FPO/16/2016|16:22:33|-0200|INTRODUÇÃO|Images/Picture.gif|HTTP|1.1|234|343|www.yahoo.com|http://www.example.com/|Mozilla|4.0|compatível; MSIE 4|
|10.128.72.213|17/FPO/2016|12:43:12|+0300|INTRODUÇÃO|News/Stuff.HTML|HTTP|1.1|200|6233|www.AOL.com|http://www.Sample.com/|Mozilla|5.0|MSIE|
|192.165.71.165|Novembro/12/2016|14:22:44|-0500|INTRODUÇÃO|Sample.ico|HTTP|1.1|342|7342|www.Facebook.com|-|Mozilla|5.0|Windows; U; Windows NT 5.1; Rv:1.7.3|
|10.166.64.165|Novembro/23/2016|01:52:45|-0800|INTRODUÇÃO|style.css|HTTP|1.1|200|2552|www.google.com|http://www.test.com/index.HTML|Mozilla|5.0|Windows|
|192.167.1.193|Jan/16/2017|22:34:56|+0200|INTRODUÇÃO|js/ADs.js|HTTP|1.1|200|23462|www.microsoft.com|http://www.Illustration.com/index.HTML|Mozilla|5.0|Windows|
|192.147.76.193|Jan/28/2017|26:36:16|+0800|INTRODUÇÃO|Search.php|HTTP|1.1|400|1777|www.Bing.com|-|Mozilla|4.0|compatível; MSIE 6.0; Windows NT 5.1|
|192.166.64.165|Mar/23/2017|01:55:25|-0800|INTRODUÇÃO|style.css|HTTP|1.1|200|2552|www.google.com|http://www.test.com/index.HTML|Mozilla|5.0|Windows|
|11.167.1.193|Apr/16/2017|11:34:36|+0200|INTRODUÇÃO|js/ADs.js|HTTP|1.1|200|23462|www.microsoft.com|http://www.Illustration.com/index.HTML|Mozilla|5.0|Windows|

## <a name="examples-of-splitting-without-delimiters"></a>Exemplos de divisão sem delimitadores
Em alguns casos, não existem nenhum delimitadores reais e campos de dados poderão ocorrer contiguamente junto entre si. Neste caso, a transformação de divisão Deteta automaticamente padrões nos dados para inferir provavelmente pontos de divisão. Por exemplo, no cenário seguinte queremos separar a quantidade do tipo de moeda e divisão infere automaticamente os limites entre os dados numéricos e não numérico como ponto de divisão.

### <a name="splitting-amount-with-currency-symbol"></a>Dividir a quantidade com o símbolo de moeda

|Montante|Amount_1|Amount_2|
|:-----|:-----|:-----|
|\$14|$|14|
|£9|£|9|
|\$34|$|34|
|€ 18|€ |18|
|\$42|$|42|
|\$7|$|7|
|£42|£|42|
|\$16|$|16|
|€ 16|€ |16|
|\$15|$|15|
|\$16|$|16|
|€ 64|€ |64|

No exemplo seguinte, gostaríamos de separar os valores de ponderação das unidades de medida. Novamente a inferência de divisão Deteta automaticamente os limites significativo e prefers-lo através de outros delimitadores possíveis, tais como a "." carateres. 

### <a name="splitting-weights-with-units"></a>Dividir ponderações com unidades

|Peso|Weight_1|Weight_2|
|:-----|:-----|:-----|
|2.27KG|2.27|KG|
|1L|1|L|
|2.5 KG|2.5|KG|
|2KG|2|KG|
|1.7KGA|1.7|KGA|
|3KG|3|KG|
|2KG|2|KG|
|125G|125|G|
|500G|500|G|
|1.5KGA|1.5|KGA|

## <a name="technical-notes"></a>Notas técnicas

A funcionalidade de transformação de divisão baseia-se no **preditiva programa Synthesis** técnica. Nesta técnica, programas de transformação de dados adquiridos automaticamente com base nos dados de entrada. Os programas estão synthesized num idioma específicas do domínio. O DSL baseia delimitadores e campos que ocorrem em particular contextos de expressão regular. Obter mais informações sobre esta tecnologia podem ser encontradas num [recente publicação sobre este tópico](https://www.microsoft.com/en-us/research/publication/automated-data-extraction-using-predictive-program-synthesis/). 
