---
title: "Pesquisas de registo de expressões regulares na análise de registos do OMS | Microsoft Docs"
description: "Pode utilizar a palavra-chave de RegEx em procuras de registo de análise de registos ao filtro de resultados, de acordo com uma expressão regular.  Este artigo fornece a sintaxe para estas expressões com vários exemplos."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2017
ms.author: bwren
ms.openlocfilehash: 28b2402cefa38ef3bfca68f2ff70e56b649c72f5
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2017
---
# <a name="using-regular-expressions-to-filter-log-searches-in-log-analytics"></a>Utilizando expressões regulares para filtrar o registo de pesquisa na análise de registos

>[!NOTE]
> Este artigo descreve as expressões regulares, utilizando a linguagem de consulta legado na análise de registos.  Se a sua área de trabalho tiver sido atualizada para o [idioma de consulta de análise de registos nova](log-analytics-log-search-upgrade.md), em seguida, devem consultar a [expressões regulares na documentação do idioma](https://docs.loganalytics.io/docs/Language-Reference/References/Regular-Expressions-syntax).


[Inicie sessão pesquisas](log-analytics-log-searches.md) permitem-lhe extrair a informação do repositório de análise de registos.  [Expressões de filtro](log-analytics-search-reference.md#filter-expressions) permitem-lhe filtrar os resultados da pesquisa, de acordo com critérios específicos.  O **RegEx** palavra-chave permite-lhe especificar uma expressão regular para o filtro.  

Este artigo fornece detalhes sobre a sintaxe de expressão regular utilizada pela análise de registos.

> [!NOTE]
> Só pode utilizar o RegEx com campos pesquisáveis.  Para obter mais informações sobre campos pesquisáveis, consulte **tipos de campo** no [localizar dados através de pesquisas de registo na análise de registos](log-analytics-log-searches.md#use-additional-filters).


## <a name="regex-keyword"></a>Palavra-chave do RegEx

Utilize a sintaxe seguinte para utilizar o **RegEx** palavra-chave na pesquisa de registo.  Pode utilizar as outras secções neste artigo para determinar a sintaxe da própria expressão regular.

    field:Regex("Regular Expression")
    field=Regex("Regular Expression")

Por exemplo, para utilizar uma expressão regular para devolver os registos com um tipo de alerta *aviso* ou *erro*, teria de utilizar a pesquisa de registo seguinte.

    Type=Alert AlertSeverity=RegEx("Warning|Error")

## <a name="partial-matches"></a>Correspondências parciais
Tenha em atenção que a expressão regular tem de coincidir com o texto completo da propriedade.  Correspondências parciais não irão devolver quaisquer registos.  Por exemplo, se estava a tentar devolver registos a partir de um computador com o nome srv01.contoso.com, a pesquisa de registo seguinte seria **não** devolver quaisquer registos.

    Computer=RegEx("srv..")

Isto acontece porque a primeira parte do nome corresponde à expressão regular.  As procuras de registo de dois seguintes devolvam registos deste computador porque corresponderem o nome completo.

    Computer=RegEx("srv..@")
    Computer=RegEx("srv...contoso.com")

## <a name="characters"></a>Carateres
Especifique outros carateres.

| Caráter | Descrição | Exemplo | Correspondências de exemplo |
|:--|:--|:--|:--|
| A | Uma ocorrência do caráter. | Computer=RegEx("Srv01.contoso.com") | Srv01.contoso.com |
| . | Um único caráter. | Computer=RegEx("SRV...contoso.com") | Srv01.contoso.com<br>SRV02.contoso.com<br>srv03.contoso.com |
| um? | Zero ou uma ocorrência do caráter. | Computador = RegEx ("srv01?. contoso.com") | srv0.contoso.com<br>Srv01.contoso.com |
| um * | Zero ou mais ocorrências do caráter. | Computer=RegEx("Srv01*.contoso.com") | srv0.contoso.com<br>Srv01.contoso.com<br>srv011.contoso.com<br>srv0111.contoso.com |
| um + | Um ou mais ocorrências do caráter. | Computer=RegEx("Srv01+.contoso.com") | Srv01.contoso.com<br>srv011.contoso.com<br>srv0111.contoso.com |
| [*abc*] | Corresponder a um único caráter os parênteses | Computer=RegEx("srv0[123].contoso.com") | Srv01.contoso.com<br>SRV02.contoso.com<br>srv03.contoso.com |
| [*um*-*z*] | Corresponde a um único caráter no intervalo.  Pode incluir vários intervalos. | Computer=RegEx("srv0[1-3].contoso.com") | Srv01.contoso.com<br>SRV02.contoso.com<br>srv03.contoso.com |
| [^*abc*] | Nenhum dos carateres os parênteses | Computer=RegEx("srv0[^123].contoso.com") | srv05.contoso.com<br>SRV06.contoso.com<br>srv07.contoso.com |
| [^*um*-*z*] | Nenhum dos carateres no intervalo. | Computer=RegEx("srv0[^1-3].contoso.com") | srv05.contoso.com<br>SRV06.contoso.com<br>srv07.contoso.com |
| [*n*-*m*] | Corresponde a um intervalo de carateres numéricos. | Computer=RegEx("SRV[01-03].contoso.com") | Srv01.contoso.com<br>SRV02.contoso.com<br>srv03.contoso.com |
| @ | Qualquer cadeia de carateres. | Computador = RegEx ("srv@.contoso.com") | Srv01.contoso.com<br>SRV02.contoso.com<br>srv03.contoso.com |


## <a name="multiple-occurences-of-character"></a>Vários occurences do caráter
Especifique as múltiplas ocorrências de um determinado carateres.

| Caráter | Descrição | Exemplo | Correspondências de exemplo |
|:--|:--|:--|:--|
| {n} |  *n*ocorrências do caráter. | Computer=RegEx("BW-Win-sc01{3}.bwren.Lab") | BW-win-sc0111.bwren.lab |
| {n} |  *n*ou mais ocorrências do caráter. | Computer=RegEx("BW-Win-sc01{3,}.bwren.Lab") | BW-win-sc0111.bwren.lab<br>BW-win-sc01111.bwren.lab<br>BW-win-sc011111.bwren.lab<br>BW-win-sc0111111.bwren.lab |
| {n, m} |  *n*para *m* ocorrências do caráter. | Computer=RegEx("BW-Win-sc01{3,5}.bwren.Lab") | BW-win-sc0111.bwren.lab<br>BW-win-sc01111.bwren.lab<br>BW-win-sc011111.bwren.lab |


## <a name="logical-expressions"></a>Expressões lógicas
Selecione a partir de vários valores.

| Caráter | Descrição | Exemplo | Correspondências de exemplo |
|:--|:--|:--|:--|
| &#124; | OU lógico.  Devolve o resultado se corresponder a expressão. | Tipo = AlertSeverity alerta = RegEx ("aviso &#124; Erro") | Aviso<br>Erro |
| & | AND. lógica  Devolve o resultado se correspondem em ambas as expressões | EventData = regex ("(segurança.\* &. \*com êxito. \*)") | Segurança de auditoria com êxito |


## <a name="literals"></a>Literais
Converta os carateres especiais carateres literais.  Isto inclui carateres que fornecem a funcionalidade para as expressões regulares, tais como?-\*^\[\]{}\(\)+\|. &.

| Caráter | Descrição | Exemplo | Correspondências de exemplo |
|:--|:--|:--|:--|
| \\ | Converte um caráter especial um literal. | Status_CF =\\[erro\\] @<br>Status_CF = erro\\-@ | [Erro] Ficheiro não encontrado.<br>Ficheiro de erros não encontrado. |


## <a name="next-steps"></a>Passos Seguintes

* Familiarizar com [pesquisas de registo](log-analytics-log-searches.md) para ver e analisar dados no repositório de análise de registos.
