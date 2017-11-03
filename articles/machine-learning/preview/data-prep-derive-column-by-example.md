---
title: "Derivar coluna ao utilizar o Azure Machine Learning Workbench de transformação de exemplo"
description: "O documento de referência para a transformação 'Deriva coluna por exemplo'"
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
ms.openlocfilehash: a02f5e827345a1d28f01d691e1b6fbccfc03ae8a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="derive-column-by-example-transformation"></a>Derivar coluna, transformação de exemplo

O **derivar coluna, por exemplo** transformação permite aos utilizadores criar um derivativo de exemplos de fornecido pelo utilizador do resultado derivado a utilizar uma ou mais colunas existentes. O derivativo pode ser qualquer combinação da cadeia suportada, a data e a número transformações. 

Os seguintes cadeia, a data e o número de transformações são suportadas:

**Transformações de cadeia:** 

Subcadeia incluindo extração inteligente de número e datas, concatenação, manipulação de maiúsculas e minúsculas, mapeamento de valores constantes.

**Transformações de datas:** 

Alterar o formato de data, extrair partes da data, hora mapeamento para intervalos de tempo de binários.

As transformações data são bastante genéricas com algumas limitações importantes:
* Timezones não são suportadas.
* Alguns formatos comuns que não são suportados:
    * ISO 8601 semana do formato de ano (por exemplo "2009-W53-7") 
    * Tempo de época UNIX.
* Todos os formatos são sensíveis a maiúsculas (as notavelmente "4 am" não é reconhecida como uma hora apesar de ser "4 AM").

**Número de transformações de:** 

Arredondar piso, limite a Discretização, preenchimento com zeros ou espaço, uma divisão ou multiplicação por uma potência de 1000.

**Transformações compostas:** 

Qualquer combinação de cadeia, número ou transformações de data.

## <a name="how-to-use-this-transformation"></a>Como utilizar esta transformação

Para efetuar esta transformação, siga estes passos:
1. Selecione uma ou mais colunas que pretende deriva de valor. 
2. Selecione **derivar coluna, por exemplo** do **transforma** menu. Em alternativa, clique no cabeçalho de qualquer uma das colunas selecionadas e selecione **derivar coluna, por exemplo** no menu de contexto. Abre o Editor de transformação e uma nova coluna é adicionada junto a coluna da direita mais selecionada. Colunas selecionadas podem ser identificadas pelas caixas de verificação dos cabeçalhos das colunas. Adição e remoção das colunas da seleção podem ser feitas utilizando as caixas de verificação dos cabeçalhos das colunas.
3. Escreva um exemplo do *saída* contra uma linha e prima introduza. Neste momento, o Workbench analisa a coluna de entrada, bem como o resultado fornecido para sintetizar um programa que pode transformar as entradas fornecidas para saída. O programa sintetizado é executado relativamente a todas as linhas na grelha de dados. Nos casos ambíguo e complicados, vários exemplos podem ser necessárias. Dependendo se está no modo básico ou avançado, vários exemplos podem ser fornecidos de formas diferentes.
4. Reveja os resultados e clique em **OK** para aceitar a transformação.

### <a name="transform-editor-basic-mode"></a>Transformar o editor: modo básico

O modo básico fornece um inline experiência na grelha de dados de edição. Pode fornecer exemplos de saída ao navegar para a célula de interesse e escrever o valor. 

O workbench analyses os dados e tenta identificar os casos de limite devem ser revistos pelo utilizador. Enquanto os dados está a ser analisados, **analisar dados** é apresentado no cabeçalho do Editor de transformação. Um a análise estiver concluído, quer **sem sugestões** ou, **linha sugerida seguinte de revisão** é apresentado no cabeçalho. Pode navegar através de cenários de limite, clicando no **linha sugerida seguinte de revisão**. No caso do valor incorreto para uma linha, deve chave no valor de correto como exemplo adicional. 

### <a name="transform-editor-advanced-mode"></a>Transformar o editor: modo avançado

Modo avançado fornece uma experiência mais rica para efetuar a derivação de colunas por exemplo. Todos os exemplos são apresentados num único local. Também pode rever todos os cenários de limite num único local ao clicar no **Mostrar sugestões de exemplos**. 

O modo avançado, pode adicionar qualquer linha como uma linha de exemplo, fazendo duplo clique na linha na grelha. Um uma linha é copiado como uma linha de exemplo, também pode editar os dados nas colunas de origem para se um exemplo sintético. Ao fazê-lo, pode adicionar casos que não estão atualmente presentes nos dados de exemplo.

Utilizador pode alternar entre o **modo básico** e **modo avançado** clicando nas ligações do Editor de transformação.

### <a name="editing-existing-transformation"></a>Editar transformação existente

Um utilizador pode editar uma existente **derivar coluna por exemplo** transformar selecionando **editar** opção do passo de transformação. Clicar no **editar** abre o Editor de transformação de mensagens em fila no **modo avançado**, e são apresentados todos os exemplos que foram fornecidos durante a criação de transformação.

## <a name="examples-of-string-transformations-by-example"></a>Exemplos de transformações de cadeia, por exemplo


>[!NOTE] 
>Os valores no **negrito** representam os exemplos que foram fornecidos para concluir a transformação no conjunto de dados indicado.


### <a name="s1-extracting-file-names-from-file-paths"></a>S1. Extrair os nomes de ficheiros de caminhos de ficheiro

Número de exemplos que são necessários para este cenário: 2

|Input|Saída|
|:-----|:-----|
|C:\Python35\Tools\pynche\TypeinViewer.PY|**TypeinViewer.py**|
|C:\Python35\Tools\pynche\webcolors.txt|webcolors.txt|
|C:\Python35\Tools\pynche\websafe.txt|websafe.txt|
|C:\Python35\Tools\pynche\X\rgb.txt|RGB.txt|
|C:\Python35\Tools\pynche\X\xlicense.txt|xlicense.txt|
|C:\Python35\Tools\Scripts\2to3.PY|2to3.PY|
|C:\Python35\Tools\Scripts\analyze_dxp.PY|**analyze_dxp.PY**|
|C:\Python35\Tools\Scripts\byext.PY|byext.PY|
|C:\Python35\Tools\Scripts\byteyears.PY|byteyears.PY|
|C:\Python35\Tools\Scripts\checkappend.PY|checkappend.PY|

### <a name="s2-case-manipulation-during-string-extraction"></a>S2. Manipulação de maiúsculas e durante a extração de cadeia

Número de exemplos que são necessários para este cenário: 3

|Input|Saída|
|:-----|:-----|
|REINDEER Cionar & fim de mensagens não;  NOVO HANOVER; Estação 332; 2015-12-10 @ 17:10:52;|**Novo Hanover**|
|CAMINHO de BRIAR & WHITEMARSH LN;  HATFIELD TOWNSHIP; Estação 345; 2015-12-10 @ 17:29:21;|Hatfield Township|
|HAWS AVE; NORRISTOWN; 2015-12-10 @ 14:39:21-estação: STA27;|**Norristown**|
|Santa AIRY & SWEDE ST;  NORRISTOWN; Estação 308A; 2015-12-10 @ 16:47:36;|**Norristown**|
|CHERRYWOOD Cionar & fim de mensagens não;  INFERIOR POTTSGROVE; Estação 329; 2015-12-10 @ 16:56:52;|Pottsgrove inferior|
|CANNON AVE & TIR 9TH ST;  LANSDALE; Estação 345; 2015-12-10 @ 15:39:04;|Lansdale|
|LAUREL AVE & OAKDALE AVE;  HORSHAM; Estação 352; 2015-12-10 @ 16:46:48;|Horsham|
|COLLEGEVILLE RD & LYWISKI RD;  SKIPPACK; Estação 336; 2015-12-10 @ 16:17:05;|Skippack|
|Pa & antigo SUMNEYTOWN PIKE; principal  INFERIOR SALFORD; Estação 344; 2015-12-10 @ 16:51:42;|Salford inferior|
|BLUEROUTE & PREPARAÇÃO I476 NB PARA CHEMICAL RD; PLYMOUTH; 2015-12-10 @ 17:35:41;|Plymouth|
|RT202 PKWY & KNAPP RD; MONTGOMERY; 2015-12-10 @ 17:33:50;|Montgomery|
|BROOK RD & COLWELL LN; PLYMOUTH; 2015-12-10 @ 16:32:10;|Plymouth|

### <a name="s3-date-format-manipulation-during-string-extraction"></a>S3. Manipulação de formato de data durante a extração de cadeia

Número de exemplos que são necessários para este cenário: 1

|Input|Saída|
|:-----|:-----|
|MONTGOMERY AVE & WOODSIDE RD;  INFERIOR MERION; Estação 313; 2015-12-11 @ 04:11:35;|**12 de Novembro de 2015 4 AM**|
|DREYCOTT LN & TIR LANCASTER AVE;  INFERIOR MERION; Estação 313; 2015-12-11 @ 01:29:52;|12 de Novembro de 2015 1: 00|
|I LEVERING MILL RD & CONSHOHOCKEN ESTADO RD; INFERIOR MERION; 2015-12-11 @ 07:29:58;|12 de Novembro de 2015 7 AM|
|PENN VALLEY RD & MANOR RD;  INFERIOR MERION; Estação 313; 2015-12-10 @ 20:53:30;|12 de FPO de 2015 8 PM|
|BELMONT AVE & OVERHILL RD; INFERIOR MERION; 2015-12-10 @ 23:02:27;|12 de FPO de 2015 23: 00|
|W MONTGOMERY AVE & PENNSWOOD RD; INFERIOR MERION; 2015-12-10 @ 19:25:22;|12 de FPO de 2015 as 19: 00|
|ROSEMONT AVE & fim de mensagens não;  INFERIOR MERION; Estação 313; 2015-12-10 @ 18:43:07;|12 de FPO de 2015 18: 00|
|AVIGNON DR & fim de mensagens não; INFERIOR MERION; 2015-12-10 @ 20:01:29-estação: STA24;|12 de FPO de 2015 8 PM|

### <a name="s4-concatenating-strings"></a>S4. Concatenar cadeias

Número de exemplos que são necessários para este cenário: 1

>[!NOTE] 
>Neste exemplo, · caráter especial representa espaços na coluna de saída.

|Nome Próprio|Média inicial|Apelido|Saída|
|:-----|:-----|:-----|:-----|
|Laquanda||Lohmann|Laquanda·· Lohmann|
|Claudio|A|Chew|**Claudio· A· Chew**|
|Joana o Sarah|S|Santos|Sarah Jane· S· Santos|
|Brandi||Blumenthal|Brandi·· Blumenthal|
|Jesusita|R|Journey|Jesusita· R· Journey|
|Hermina||Hults|Hermina·· Hults|
|Anne Marie|W|Jones|Anne Marie· W· Jones|
|Rico||Ropp|Rico·· Ropp|
|Maio de Lauren||Fullmer|Lauren May·· Fullmer|
|Marc|T|Maine|Marc· T· Maine|
|Angie||Adelman|Angie·· Adelman|
|O João Paul||Santos|O João Paul·· Santos|
|Song|W|Staller|Song· W· Staller|
|Jill||Jefferies|Jill·· Jefferies|
|Tolerância Ruby|M|Simmons|Ruby Grace· M· Simmons|

### <a name="s5-generating-initials"></a>S5. Gerar iniciais

Número de exemplos que são necessários para este cenário: 2

|Nome completo|Saída|
|:-----|:-----|
|Laquanda Lohmann|**L.L.**|
|Claudio Chew|C.C.|
|A Joana o Sarah Santos|S.S.|
|Brandi Blumenthal|B.B.|
|Jesusita Journey|J.J.|
|Hermina Hults|H.H.|
|Anne Marie Jones|A.J.|
|Rico Ropp|R.R.|
|Maio de Lauren Fullmer|L.F.|
|Marc Maine|M.M.|
|Angie Adelman|A.A.|
|Paul João Silva|**J.S.**|
|Song Staller|S.S.|
|Jill Jefferies|J.J.|
|Simmons tolerância Ruby|R.S.|


### <a name="s6-mapping-constant-values"></a>S6. Valores de constantes de mapeamento

Número de exemplos que são necessários para este cenário: 3

|Sexo administrativo|Saída|
|:-----|:-----:|
|Male|**0**|
|Female|**1**|
|Desconhecido|**2**|
|Female|1|
|Female|1|
|Male|0|
|Desconhecido|2|
|Male|0|
|Female|1|

## <a name="examples-of-number-transformations-by-example"></a>Exemplos de transformações de números, por exemplo

>[!NOTE] 
>Os valores no **negrito** representam os exemplos que foram fornecidos para concluir a transformação no conjunto de dados indicado.


### <a name="n1-rounding-to-nearest-10"></a>N1. Arredondamento para 10 mais próximo

Número de exemplos que são necessários para este cenário: 1

|Input|Saída|
|-----:|-----:|
|112|**110**|
|117|120|
|11112|11110|
|11119|11120|
|548|550|

### <a name="n2-rounding-down-to-nearest-10"></a>N2. Arredondamento até mais próximo 10

Número de exemplos que são necessários para este cenário: 2

|Input|Saída|
|-----:|-----:|
|112|**110**|
|117|**110**|
|11112|11110|
|11119|11110|
|548|540|

### <a name="n3-rounding-to-nearest-005"></a>N3. Arredondamento para 0,05 mais próximo

Número de exemplos que são necessários para este cenário: 2

|Input|Saída|
|-----:|-----:|
|-75.5812935|**-75.60**|
|-75.2646799|-75.25|
|-75.3519752|-75.35|
|-75.343513|**-75.35**|
|-75.6033497|-75.60|
|-75.283245|-75.30|

### <a name="n4-binning"></a>N4. A discretização

Número de exemplos que são necessários para este cenário: 1

|Input|Saída|
|-----:|:-----:|
|20.16|**20-25**|
|14.32|10-15|
|5.44|5-10|
|3.84|0-5|
|3.73|0-5|
|7.36|5-10|

### <a name="n5-scaling-by-1000"></a>N5. Dimensionamento, 1000

Número de exemplos que são necessários para este cenário: 1

|Input|Saída|
|-----:|-----:|
|-243|**-243000**|
|-12.5|-12500|
|-2345.23292|-2345232.92|
|-1202.3433|-1202343.3|
|1202.3433|1202343.3|

### <a name="n6-padding"></a>N6. Preenchimento

Número de exemplos que são necessários para este cenário: 1

|Código|Saída|
|-----:|-----:|
|5828|**05828**|
|44130|44130|
|49007|49007|
|29682|29682|
|4759|04759|
|10029|10029|
|7204|07204|

## <a name="examples-of-date-transformations-by-example"></a>Exemplos de transformações de data, por exemplo

>[!NOTE] 
>Os valores no **negrito** representam os exemplos que foram fornecidos para concluir a transformação no conjunto de dados indicado.


### <a name="d1-extracting-date-parts"></a>D1. Extrair partes da data

Estas partes da data foram extraídos utilizando transformações por exemplo diferentes no mesmo conjunto de dados. Cadeias de negrito representam os exemplos que foram fornecidos na respetiva transformação correspondentes.

|DateTime|dia da semana|Data|Mês|ano|Hora|Minuto|Segundo|
|-----:|-----:|-----:|-----:|-----:|-----:|-----:|-----:|
|2031 de Janeiro de 31 05:54 em: 18|**Sab**|**31**|**Janeiro**|**2031**|**5**|**54**|**18**|
|17-Jan-1990 13:32:01|WED|17|Janeiro|1990|13|32|01|
|14-Fev-2034 05:36:07|TUE|14|Fev|2034|5|36|07|
|Mar-14-2002 13:16:16|Qui|14|Mar|2002|13|16|16|
|Jan-21-1985 05:44:43|MON|21|Janeiro|1985|5|44|**43**|
|16-Aug-1985 01:11:56|Sab|16|Aug|1985|1|11|56|
|20-Dec-2033 18:36:29|TUE|20|DEC|2033|18|36|29|
|16-Jul-1984 10:21:59|MON|16|Jul|1984|10|21|59|
|13-Janeiro de 2038 10:59:36|WED|13|Janeiro|2038|10|59|36|
|Aug-14-1982 15:13:54 em|DOM|14|Aug|1982|15|13|54|
|22-Novembro-2030 08:18:08|Sab|22|Novembro|2030|8|18|08|
|FPO-21-1997 08:42:58|TUE|21|FPO|1997|8|42|58|
|2006 de Novembro de 28 14:19:15|TUE|28|Novembro|2006|14|19|15|
|29-Apr-2031 04:59:45.|TUE|29|Apr|2031|4|59|45|
|29-Jan-2032 02:38:36|Qui|29|Janeiro|2032|2|38|36|
|2028 de Maio de 11 15:31:52|Qui|11|Pode|2028|15|31|52|
|15-Jul-1977 12:45:39|Sab|15|Jul|1977|12|45|39|
|27-Jan-2029 05:55:41|DOM|27|Janeiro|2029|5|55|41|
|Mar-03-2024 10:17:49|Sun|3|Mar|2024|10|17|49|
|00:23:13 de 14-Apr-2010|WED|14|Apr|2010|0|23|13|

### <a name="d2-formatting-dates"></a>D2. Formatar datas

Estes formattings data foram efetuadas utilizando transformações por exemplo diferentes no mesmo conjunto de dados. Cadeias de negrito representam os exemplos que foram fornecidos na respetiva transformação correspondentes.

|DateTime|Format1|Format2|Format3|Format4|Format5|
|-----:|-----:|-----:|-----:|-----:|-----:|
|2031 de Janeiro de 31 05:54 em: 18|**1/31/2031**|**Sexta-feira, 31 de Janeiro de 2031**|**01312031 5:54**|**1/31/2031 5:54 EM AM**|**Q1 2031**|
|17-Jan-1990 13:32:01|1/17/1990|Quarta-feira, 17 de Janeiro de 1990|01171990 13:32|17/1/1990 1:32 PM|Q1 1990|
|14-Fev-2034 05:36:07|2/14/2034|Terça-feira, 14 de Fevereiro de 2034|02142034 5:36|14/2/2034 5:36 AM|Q1 2034
|Mar-14-2002 13:16:16|3/14/2002|Quinta-feira, 14 de Março de 2002|03142002 13:16|3/14/2002 1:16 PM|Q1 2002
|Jan-21-1985 05:44:43|1/21/1985|Segunda-feira, 21 de Janeiro de 1985|01211985 5:44|21/1/1985 5:44 AM|Q1 1985
|16-Aug-1985 01:11:56|8/16/1985|Sexta-feira, 16 de Agosto de 1985|08161985 1:11|8/16/1985 1:11:00|Q3 1985
|20-Dec-2033 18:36:29|12/20/2033|Terça-feira, 20 de Dezembro de 2033|12202033 18:36|20/12/2033 6:36 PM|Q4 2033
|16-Jul-1984 10:21:59|7/16/1984|Segunda, 16 de Julho de 1984|07161984 10:21|16/7/1984 10:21 AM|Q3 1984
|13-Janeiro de 2038 10:59:36|1/13/2038|Quarta-feira, 13 de Janeiro de 2038|01132038 10:59|13/1/2038 10:59 AM|Q1 2038
|Aug-14-1982 15:13:54 em|8/14/1982|Sábado, 14 de Agosto de 1982|08141982 15:13|8/14/1982 ÀS 15:13|Q3 1982
|22-Novembro-2030 08:18:08|11/22/2030|Sexta-feira, 22 de Novembro de 2030|11222030 8:18|22/11/2030 8:18 AM|Q4 2030
|FPO-21-1997 08:42:58|10/21/1997|Terça-feira, 21 de Outubro de 1997|10211997 8:42|21/10/1997 8:42 AM|Q4 1997
|2006 de Novembro de 28 14:19:15|11/28/2006|Terça-feira, 28 de Novembro de 2006|11282006 14:19|11/28/2006 2:19 PM|Q4 2006
|29-Apr-2031 04:59:45.|4/29/2031|Terça-feira, 29 de Abril de 2031|04292031 4:59|4/29/2031 4:59 AM|Q2 2031
|29-Jan-2032 02:38:36|1/29/2032|Quinta-feira, 29 de Janeiro de 2032|01292032 2:38|29/1/2032 2:38 AM|Q1 2032
|2028 de Maio de 11 15:31:52|5/11/2028|Quinta-feira, 11 de Maio de 2028|05112028 15:31|11/5/2028 ÀS 15:31|Q2 2028
|15-Jul-1977 12:45:39|7/15/1977|Sexta-feira, 15 de Julho de 1977|07151977 12:45|15/7/1977 12:45 PM|Q3 1977
|27-Jan-2029 05:55:41|1/27/2029|Sábado, 27 de Janeiro de 2029|01272029 5:55|27/1/2029 5:55 AM|Q1 2029
|Mar-03-2024 10:17:49|3/3/2024|Domingo, 3 de Março de 2024|03032024 10:17|3/3/2024 10:17 AM|Q1 2024
|00:23:13 de 14-Apr-2010|4/14/2010|Quarta-feira, 14 de Abril de 2010|04142010 0:23|4/14/2010 12:23:00|Q2 2010


### <a name="d3-mapping-time-to-time-periods"></a>D3. Períodos de hora a hora de mapeamento

Estes DateTime para mapeamentos de período foram efetuadas utilizando transformações por exemplo diferentes no mesmo conjunto de dados. Cadeias de negrito representam os exemplos que foram fornecidos na respetiva transformação correspondentes.

|DateTime|Period(seconds)|Period(minutes)|Período (duas horas)|Período (30 minutos)|
|-----:|-----:|-----:|-----:|-----:|
|2031 de Janeiro de 31 05:54 em: 18|**0-20**|**45-60**|**5 AM - 7 AM**|**5:30-6:00**|
|17-Jan-1990 13:32:01|**0-20**|30-45|ÀS 1 PM - 15|13:30-14:00|
|14-Fev-2034 05:36:07|0-20|30-45|5 AM - 7 AM|5:30-6:00|
|Mar-14-2002 13:16:16|0-20|15-30|ÀS 1 PM - 15|13:00-13:30|
|Jan-21-1985 05:44:43|40-60|30-45|5 AM - 7 AM|5:30-6:00|
|16-Aug-1985 01:11:56|40-60|0-15|1: 00 - 3 AM|1:00-1:30|
|20-Dec-2033 18:36:29|20-40|30-45|AS 17: 00 - 7 PM|18:30-19:00|
|16-Jul-1984 10:21:59|40-60|15-30|09: 00 - 11 AM|10:00-10:30|
|13-Janeiro de 2038 10:59:36|20-40|45-60|09: 00 - 11 AM|10:30-11:00|
|Aug-14-1982 15:13:54 em|40-60|0-15|3 PM - AS 17: 00|15:00-15:30|
|22-Novembro-2030 08:18:08|0-20|15-30|7 AM - 09: 00|8:00-8:30|
|FPO-21-1997 08:42:58|40-60|30-45|7 AM - 09: 00|8:30-9:00|
|2006 de Novembro de 28 14:19:15|0-20|15-30|ÀS 1 PM - 15|14:00-14:30|
|29-Apr-2031 04:59:45.|40-60|45-60|AM DE 3-5 AM|4:30-5:00|
|29-Jan-2032 02:38:36|20-40|30-45|1: 00 - 3 AM|2:30-3:00|
|2028 de Maio de 11 15:31:52|40-60|30-45|3 PM - AS 17: 00|15:30-16:00|
|15-Jul-1977 12:45:39|20-40|45-60|11 AM - 1 PM|12:30-13:00|
|27-Jan-2029 05:55:41|40-60|45-60|5 AM - 7 AM|5:30-6:00|
|Mar-03-2024 10:17:49|40-60|15-30|09: 00 - 11 AM|10:00-10:30|
|00:23:13 de 14-Apr-2010|0-20|15-30|23: 00 - 1: 00|0:00-0:30|

## <a name="examples-of-composite-transformations-by-example"></a>Exemplos de transformações compostos por exemplo

|tripduration|StartTime|iniciar o id de estação|iniciar a latitude de estação|iniciar a longitude de estação|UserType|Coluna|
|-----:|-----:|-----:|-----:|-----:|-----:|-----:|
|61|2016-01-08 16:09:32|107|42.3625|-71.08822|Subscritor|**Um subscritor selecionado uma bicicleta de estação 107, lat/longa (42.363,-71.088), no 08 de Janeiro de 2016 em cerca de 4 PM. A duração da viagem foi 61 minutos**|
|61|2016-01-17 09:28:10|74|42.373268|-71.118579|Cliente|Um cliente selecionado uma bicicleta de estação 74, lat/longa (42.373,-71.119), no 17 de Janeiro de 2016 em cerca de 09: 00. A duração da viagem foi 61 minutos|
|62|2016-01-25 08:10:26|176|42.386748020450561|-71.119018793106079|Subscritor|Um subscritor selecionado uma bicicleta de estação 176, lat/longa (42.387,-71.119), em 25 de Janeiro de 2016 em cerca de 8 AM. A duração da viagem foi 62 minutos|
|63|2016-01-08 10:10:29|107|42.3625|-71.08822|Subscritor|Um subscritor selecionado uma bicicleta de estação 107, lat/longa (42.363,-71.088), no 08 de Janeiro de 2016 em cerca de 10 AM. A duração da viagem foi 63 minutos|
|64|2016-01-15 19:42:08|68|42.36507|-71.1031|Subscritor|Um subscritor selecionado uma bicicleta de estação 68, lat/longa (42.365,-71.103), em 15 de Janeiro de 2016 em cerca de 7 PM. A duração da viagem foi 64 minutos|
|64|2016-01-22 18:16:13|115|42.387995|-71.119084|Subscritor|Um subscritor selecionado uma bicicleta de estação 115, lat/longa (42.388,-71.119), no 22 de Janeiro de 2016 em cerca de 18: 00. A duração da viagem foi 64 minutos|
|68|2016-01-18 09:51:52|178|42.359573201090441|-71.101294755935669|Subscritor|Um subscritor selecionado uma bicicleta de estação 178, lat/longa (42.360,-71.101), no 18 de Janeiro de 2016 em cerca de 09: 00. A duração da viagem foi 68 minutos|
|69|2016-01-14 08:57:55|176|42.386748020450561|-71.119018793106079|Subscritor|Um subscritor selecionado uma bicicleta de estação 176, lat/longa (42.387,-71.119), no 14 de Janeiro de 2016 em cerca de 8 AM. A duração da viagem foi 69 minutos|
|69|2016-01-13 22:12:55|141|42.363560158429884|-71.08216792345047|Subscritor|Um subscritor selecionado uma bicicleta de estação 141, lat/longa (42.364,-71.082), em 13 de Janeiro de 2016 em cerca de 10 PM. A duração da viagem foi 69 minutos|
|69|2016-01-15 08:13:09|176|42.386748020450561|-71.119018793106079|Subscritor|Um subscritor selecionado uma bicicleta de estação 176, lat/longa (42.387,-71.119), em 15 de Janeiro de 2016 em cerca de 8 AM. A duração da viagem foi 69 minutos|


## <a name="technical-notes"></a>Notas técnicas

### <a name="conditional-transformations"></a>Transformações condicionais
Em alguns casos, não é possível encontrar uma transformação único que satisfaça os exemplos indicados. Nestes casos, derivar coluna, por exemplo transformar tenta agrupar as entradas com base num padrão de algumas e saiba transformação separada para cada grupo. Chamamos a isto **transformação condicional**. **Transformação condicional** é tentada apenas para transformações com uma única coluna de entrada. 

### <a name="reference"></a>Referência
Podem encontrar mais informações sobre a transformação de cadeia por tecnologia de exemplo no [esta publicação](https://www.microsoft.com/en-us/research/publication/automating-string-processing-spreadsheets-using-input-output-examples/).
