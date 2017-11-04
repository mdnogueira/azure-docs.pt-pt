---
title: "Escrever expressões para mapeamentos de atributos no Azure Active Directory | Microsoft Docs"
description: "Saiba como utilizar os mapeamentos de expressão para transformar valores de atributo para um formato aceitável durante o aprovisionamento automatizado de objetos de aplicação SaaS no Azure Active Directory."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: b13c51cd-1bea-4e5e-9791-5d951a518943
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: markvi
ms.openlocfilehash: 2811b4d57f69425ef119c88f80b32d24c6c32195
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2017
---
# <a name="writing-expressions-for-attribute-mappings-in-azure-active-directory"></a>Escrever expressões para mapeamentos de atributos no Azure Active Directory
Quando configurar o aprovisionamento para uma aplicação SaaS, um dos tipos de mapeamentos de atributos que pode especificar é um mapeamento de expressão. Para estes, tem de escrever uma expressão de tipo de script que permite-lhe transformar dados dos utilizadores em formatos mais aceitáveis para a aplicação SaaS.

## <a name="syntax-overview"></a>Descrição geral da sintaxe
A sintaxe para expressões para mapeamentos de atributos é reminiscent do Visual Basic para funções de aplicações (VBA).

* A expressão completa tem de ser definido em termos de funções, que consistem em nome de um seguido de argumentos parênteses: <br>
  *FunctionName (<< argumento 1 >>, <<argument N>>)*
* Podem aninhar funções uns nos outros. Por exemplo: <br> *FunctionOne (FunctionTwo (<<argument1>>))*
* Pode passar três tipos diferentes de argumentos para funções:
  
  1. Atributos, que têm de estar entre quadrado entre parênteses Retos. Por exemplo: [attributeName]
  2. Constantes String, que devem estar entre aspas. Por exemplo: "Dos Estados Unidos"
  3. Outras funções. Por exemplo: FunctionOne (<<argument1>>, FunctionTwo (<<argument2>>))
* Para as constantes string, se precisar de uma barra invertida (\) ou aspas (") na cadeia de-tem de ser caráter de escape correto com o símbolo de barra invertida (\). Por exemplo: "o nome da empresa: \"Contoso\""

## <a name="list-of-functions"></a>Lista de funções
[Acrescentar](#append) &nbsp; &nbsp; &nbsp; &nbsp; [FormatDateTime](#formatdatetime) &nbsp; &nbsp; &nbsp; &nbsp; [associar](#join) &nbsp; &nbsp; &nbsp; &nbsp; [Mid](#mid) &nbsp; &nbsp; &nbsp; &nbsp; [não](#not) &nbsp; &nbsp; &nbsp; &nbsp; [Substituir](#replace) &nbsp; &nbsp; &nbsp; &nbsp; [StripSpaces](#stripspaces) &nbsp; &nbsp; &nbsp; &nbsp; [Comutador](#switch)

- - -
### <a name="append"></a>Acrescentar
**Função:**<br> Append(Source, suffix)

**Descrição:**<br> Cria um valor de cadeia de origem e acrescenta o sufixo ao fim do mesmo.

**Parâmetros:**<br> 

| Nome | Necessário / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **origem** |Necessário |Cadeia |Normalmente, o nome do atributo do objeto de origem |
| **sufixo** |Necessário |Cadeia |A cadeia que pretende anexar ao fim do valor de origem. |

- - -
### <a name="formatdatetime"></a>FormatDateTime
**Função:**<br> FormatDateTime (origem inputFormat, outputFormat)

**Descrição:**<br> Utiliza uma cadeia de um formato de data e converte-o num formato diferente.

**Parâmetros:**<br> 

| Nome | Necessário / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **origem** |Necessário |Cadeia |Normalmente, o nome do atributo do objeto de origem. |
| **inputFormat** |Necessário |Cadeia |Formato esperado do valor de origem. Para formatos suportados, consulte [http://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx](http://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx). |
| **outputFormat** |Necessário |Cadeia |Formato da data de saída. |

- - -
### <a name="join"></a>Associar
**Função:**<br> Associar (separador source1, source2,...)

**Descrição:**<br> JOIN() é semelhante à Append(), exceto que pode combinar vários **origem** cadeia os valores numa cadeia única, e cada valor ficarão separado por um **separador** cadeia.

Se um dos valores de origem é um atributo de valor múltiplo, em seguida, cada valor nesse atributo irá ser unidas, separados pelo valor de separação.

**Parâmetros:**<br> 

| Nome | Necessário / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **separador** |Necessário |Cadeia |Cadeia utilizada para separar os valores de origem quando estes são concatenados numa cadeia. Pode ser "" se o separador de não é necessário. |
| * * source1... sourceN * * |Necessário, variável-número de vezes |Cadeia |Cadeia de valores para ser unidas. |

- - -
### <a name="mid"></a>Mid
**Função:**<br> Mid (origem de início, comprimento)

**Descrição:**<br> Devolve uma subcadeia do valor de origem. Uma subcadeia é uma cadeia que contém apenas alguns dos carateres da cadeia de origem.

**Parâmetros:**<br> 

| Nome | Necessário / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **origem** |Necessário |Cadeia |Normalmente, o nome do atributo. |
| **start** |Necessário |número inteiro |O índice no **origem** cadeia onde deve começar a subcadeia. Primeiro carácter na cadeia de terão o índice de 1, o segundo caráter de tem índice 2 e assim sucessivamente. |
| **comprimento** |Necessário |número inteiro |Comprimento da subcadeia. Se o comprimento termina fora do **origem** cadeia, a função irá devolver a subcadeia do **iniciar** índice até o fim do **origem** cadeia. |

- - -
### <a name="not"></a>não
**Função:**<br> Not(Source)

**Descrição:**<br> Flips o valor de booleano o **origem**. Se **origem** valor é "*verdadeiro*", devolve "*falso*". Caso contrário, devolve "*verdadeiro*".

**Parâmetros:**<br> 

| Nome | Necessário / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **origem** |Necessário |Cadeia booleana |Era esperado **origem** os valores são "True" ou "False"... |

- - -
### <a name="replace"></a>Substituir
**Função:**<br> ObsoleteReplace (origem oldValue, regexPattern, regexGroupName, replacementValue, replacementAttributeName, modelo)

**Descrição:**<br>
Substitui os valores dentro de uma cadeia. Este funciona de forma diferente consoante os parâmetros fornecidos:

* Quando **oldValue** e **replacementValue** são fornecidos:
  
  * Substitui todas as ocorrências de oldValue na origem de com replacementValue
* Quando **oldValue** e **modelo** são fornecidos:
  
  * Substitui todas as ocorrências do **oldValue** no **modelo** com o **origem** valor
* Quando **oldValueRegexPattern**, **oldValueRegexGroupName**, **replacementValue** são fornecidos:
  
  * Substitui todos os valores correspondentes oldValueRegexPattern na cadeia de origem com replacementValue
* Quando **oldValueRegexPattern**, **oldValueRegexGroupName**, **replacementPropertyName** são fornecidos:
  
  * Se **origem** tem um valor, **origem** é devolvido
  * Se **origem** não tem nenhum valor, utiliza **oldValueRegexPattern** e **oldValueRegexGroupName** para extrair a propriedade com o valor de substituição  **replacementPropertyName**. Valor de substituição é devolvido como resultado

**Parâmetros:**<br> 

| Nome | Necessário / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **origem** |Necessário |Cadeia |Normalmente, o nome do atributo do objeto de origem. |
| **oldValue** |Opcional |Cadeia |Valor a ser substituído na **origem** ou **modelo**. |
| **regexPattern** |Opcional |Cadeia |Padrão de RegEx para o valor a ser substituído na **origem**. Ou, quando é utilizado replacementPropertyName, padrão, a extrair o valor da propriedade de substituição. |
| **regexGroupName** |Opcional |Cadeia |Nome do grupo no interior **regexPattern**. Apenas quando é utilizado replacementPropertyName, iremos irá extraia o valor deste grupo como replacementValue da propriedade de substituição. |
| **replacementValue** |Opcional |Cadeia |Novo valor para substituir antiga com. |
| **replacementAttributeName** |Opcional |Cadeia |Nome do atributo a ser utilizada para o valor de substituição, quando a origem não tem um valor. |
| **modelo** |Opcional |Cadeia |Quando **modelo** valor é fornecido, iremos irá procurar **oldValue** dentro do modelo e substitua-o com o valor de origem. |

- - -
### <a name="stripspaces"></a>StripSpaces
**Função:**<br> StripSpaces(source)

**Descrição:**<br> Remove a todo o espaço ("") carateres da cadeia de origem.

**Parâmetros:**<br> 

| Nome | Necessário / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **origem** |Necessário |Cadeia |**origem** valor para atualizar. |

- - -
### <a name="switch"></a>Comutador
**Função:**<br> Comutador (origem defaultValue, key1, value1, key2, value2,...)

**Descrição:**<br> Quando **origem** valor correspondências um **chave**, devolve **valor** para esse **chave**. Se **origem** valor não corresponde a quaisquer chaves, devolve **defaultValue**.  **Chave** e **valor** parâmetros tem sempre surgem em pares. A função espera sempre um número par de parâmetros.

**Parâmetros:**<br> 

| Nome | Necessário / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **origem** |Necessário |Cadeia |**Origem** valor para atualizar. |
| **defaultValue** |Opcional |Cadeia |Valor predefinido a ser utilizado quando a origem não corresponde a quaisquer chaves. Pode ser uma cadeia vazia (""). |
| **chave** |Necessário |Cadeia |**Chave** para comparar **origem** valor com. |
| **valor** |Necessário |Cadeia |Valor de substituição para o **origem** à chave. |

## <a name="examples"></a>Exemplos
### <a name="strip-known-domain-name"></a>Nome de domínio conhecidos de faixa
Terá de eliminar um nome de domínio conhecidos do e-mail de um utilizador para obter um nome de utilizador. <br>
Por exemplo, se o domínio "contoso.com", em seguida, pode utilizar a seguinte expressão:

**Expressão:** <br>
`Replace([mail], "@contoso.com", , ,"", ,)`

**Exemplo de entrada / saída:** <br>

* **ENTRADA** (mail): "john.doe@contoso.com"
* **SAÍDA**: "john.doe"

### <a name="append-constant-suffix-to-user-name"></a>Acrescentar sufixos constante ao nome de utilizador
Se estiver a utilizar uma Sandbox do Salesforce, poderá ter de acrescentar um sufixo adicional para todos os nomes de utilizador antes da sincronização-los.

**Expressão:** <br>
`Append([userPrincipalName], ".test"))`

**Entrada/saída de exemplo:** <br>

* **ENTRADA**: (userPrincipalName): "John.Doe@contoso.com"
* **SAÍDA**: "John.Doe@contoso.com.test"

### <a name="generate-user-alias-by-concatenating-parts-of-first-and-last-name"></a>Gerar o alias do utilizador por concatenar partes do nome próprio e apelido
Tem de gerar um utilizador alias, efetuando os primeiros 3 letras do nome próprio do utilizador e os primeiros 5 letras do apelido do utilizador.

**Expressão:** <br>
`Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**Entrada/saída de exemplo:** <br>

* **ENTRADA** (givenName): "João"
* **ENTRADA** (apelido): "Silva"
* **SAÍDA**: "JohDoe"

### <a name="output-date-as-a-string-in-a-certain-format"></a>Data de saída como uma cadeia com um formato de determinados
Pretende enviar datas a uma aplicação SaaS num formato de determinadas. <br>
Por exemplo, que pretende formatar datas para ServiceNow.

**Expressão:** <br>

`FormatDateTime([extensionAttribute1], "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**Entrada/saída de exemplo:**

* **ENTRADA** (extensionAttribute1): "20150123105347.1Z"
* **SAÍDA**: "2015-01-23"

### <a name="replace-a-value-based-on-predefined-set-of-options"></a>Substitua um valor com base num conjunto predefinido de opções
Tem de definir o fuso horário do utilizador com base no código de estado armazenado no Azure AD. <br>
Se o código de estado não corresponde a qualquer uma das opções predefinidas, utilize o valor predefinido de "Austrália/Sydney".

**Expressão:** <br>

`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**Entrada/saída de exemplo:**

* **ENTRADA** (estado): "QLD"
* **SAÍDA**: "Austrália/Brisbane"

## <a name="related-articles"></a>Artigos relacionados
* [Índice de Artigos da Gestão da Aplicação no Azure Active Directory](active-directory-apps-index.md)
* [Automatizar utilizador aprovisionamento/desaprovisionamento para aplicações SaaS](active-directory-saas-app-provisioning.md)
* [Personalizar os mapeamentos de atributos para o aprovisionamento de utilizador](active-directory-saas-customizing-attribute-mappings.md)
* [Filtros de âmbito para o aprovisionamento de utilizador](active-directory-saas-scoping-filters.md)
* [Utilizar o SCIM para ativar o aprovisionamento automático de utilizadores e grupos do Azure Active Directory a aplicações](active-directory-scim-provisioning.md)
* [Notificações de aprovisionamento de contas](active-directory-saas-account-provisioning-notifications.md)
* [Lista de tutoriais sobre como integrar aplicações SaaS](active-directory-saas-tutorial-list.md)

