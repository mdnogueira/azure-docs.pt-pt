---
title: "Sincronização do Azure AD Connect: referência de funções | Microsoft Docs"
description: "Referência de expressões de aprovisionamento declarativas na sincronização do Azure AD Connect."
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: 4f525ca0-be0e-4a2e-8da1-09b6b567ed5f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: 0f556c786fdfdf775e00e68740351e7630e0e002
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-connect-sync-functions-reference"></a>Sincronização do Azure AD Connect: referência de funções
No Azure AD Connect, as funções são utilizadas para manipular um valor de atributo durante a sincronização.  
A sintaxe das funções é expresso utilizando o seguinte formato:  
`<output type> FunctionName(<input type> <position name>, ..)`

Se uma função está sobrecarregada e aceita vários sintaxes, todas as sintaxes válidos são listados.  
As funções são do tipo seguro e, certifique-se de que o tipo transmitido corresponde ao tipo documentado.  
Se o tipo não corresponde, é emitido um erro.

Os tipos são expressas com a seguinte sintaxe:

* **Reciclagem** – binário
* **bool** – booleano
* **DT** – data/hora UTC
* **Enum** – enumeração de constantes conhecidas
* **EXP** – expressão, que é esperada avaliar como um valor booleano
* **mvbin** – binário com múltiplos valores
* **mvstr** – cadeia com múltiplos valores
* **mvref** – referência com múltiplos valores
* **Núm** – numérico
* **Ref** – referência
* **str** – cadeia
* **var** – uma variante de (quase) de qualquer outro tipo
* **void** – não devolve um valor

As funções com os tipos de **mvbin**, **mvstr**, e **mvref** só pode ser utilizado em atributos com múltiplos valores. Funciona com **bin**, **str**, e **ref** escolar nos atributos de valor único e com múltiplos valores.

## <a name="functions-reference"></a>Referência das Funções
| Lista de funções |  |  |  |  |
| --- | --- | --- | --- | --- | --- |
| **Certificado** | | | | |
| [CertExtensionOids](#certextensionoids) |[CertFormat](#certformat) |[CertFriendlyName](#certfriendlyname) |[CertHashString](#certhashstring) | |
| [CertIssuer](#certissuer) |[CertIssuerDN](#certissuerdn) |[CertIssuerOid](#certissueroid) |[CertKeyAlgorithm](#certkeyalgorithm) | |
| [CertKeyAlgorithmParams](#certkeyalgorithmparams) |[CertNameInfo](#certnameinfo) |[CertNotAfter](#certnotafter) |[CertNotBefore](#certnotbefore) | |
| [CertPublicKeyOid](#certpublickeyoid) |[CertPublicKeyParametersOid](#certpublickeyparametersoid) |[CertSerialNumber](#certserialnumber) |[CertSignatureAlgorithmOid](#certsignaturealgorithmoid) | |
| [CertSubject](#certsubject) |[CertSubjectNameDN](#certsubjectnamedn) |[CertSubjectNameOid](#certsubjectnameoid) |[CertThumbprint](#certthumbprint) | |
[CertVersion](#certversion) |[IsCert](#iscert) | | | |
| **Conversão** | | | | |
| [CBool](#cbool) |[CDate](#cdate) |[CGuid](#cguid) |[ConvertFromBase64](#convertfrombase64) | |
| [ConvertToBase64](#converttobase64) |[ConvertFromUTF8Hex](#convertfromutf8hex) |[ConvertToUTF8Hex](#converttoutf8hex) |[CNum](#cnum) | |
| [CRef](#cref) |[CStr](#cstr) |[StringFromGuid](#StringFromGuid) |[StringFromSid](#stringfromsid) | |
| **Data / hora** | | | | |
| [DateAdd](#dateadd) |[DateFromNum](#datefromnum) |[FormatDateTime](#formatdatetime) |[Agora](#now) | |
| [NumFromDate](#numfromdate) | | | | |
| **Diretório** | | | | |
| [DNComponent](#dncomponent) |[DNComponentRev](#dncomponentrev) |[EscapeDNComponent](#escapedncomponent) | | |
| **Avaliação** | | | | |
| [IsBitSet](#isbitset) |[IsDate](#isdate) |[IsEmpty](#isempty) |[IsGuid](#isguid) | |
| [IsNull](#isnull) |[IsNullOrEmpty](#isnullorempty) |[IsNumeric](#isnumeric) |[IsPresent](#ispresent) | |
| [IsString](#isstring) | | | | |
| **Bibliotecas** | | | | |
| [BitAnd](#bitand) |[BitOr](#bitor) |[RandomNum](#randomnum) | | |
| **Com múltiplos valores** | | | | |
| [Contém](#contains) |[Contagem](#count) |[Item](#item) |[ItemOrNull](#itemornull) | |
| [Associar](#join) |[RemoveDuplicates](#removeduplicates) |[Divisão](#split) | | |
| **Fluxo de programa** | | | | |
| [Erro](#error) |[IIF](#iif) |[Selecionar](#select) |[Comutador](#switch) | |
| [Onde](#where) |[Com o](#with) | | | |
| **Texto** | | | | |
| [GUID](#guid) |[InStr](#instr) |[InStrRev](#instrrev) |[LCase](#lcase) | |
| [À esquerda](#left) |[Len](#len) |[LTrim](#ltrim) |[Mid](#mid) | |
| [Padleft do modelo](#padleft) |[PadRight](#padright) |[PCase](#pcase) |[Substituir](#replace) | |
| [ReplaceChars](#replacechars) |[À direita](#right) |[RTrim](#rtrim) |[Cortar](#trim) | |
| [UCase](#ucase) |[Word](#word) | | | |

- - -
### <a name="bitand"></a>BitAnd
**Descrição:**  
A função de BitAnd define do bits especificada num valor.

**Sintaxe:**  
`num BitAnd(num value1, num value2)`

* Valor1, valor2: os valores numéricos que devem ser AND'ed em conjunto

**Comentários:**  
Esta função converte os dois parâmetros de representação binária e define um pouco para:

* 0 - se um ou ambos bits correspondente na *máscara* e *sinalizador* são 0
* 1 - se o bits correspondente são 1.

Por outras palavras, devolve 0 em todos os casos, exceto quando o bits correspondente de ambos os parâmetros são 1.

**Exemplo:**  
`BitAnd(&HF, &HF7)`  
Devolve 7 porque hexadecimal "F" e "F7" avaliar a este valor.

- - -
### <a name="bitor"></a>BitOr
**Descrição:**  
A função de BitOr define do bits especificada num valor.

**Sintaxe:**  
`num BitOr(num value1, num value2)`

* Valor1, valor2: os valores numéricos que devem ser OR'ed em conjunto

**Comentários:**  
Esta função converte os dois parâmetros de representação binária e define um pouco 1 se uma ou ambas bits correspondente na máscara e o sinalizador 1 e 0 se o bits correspondente são 0. Por outras palavras, devolve 1 em todos os casos, exceto em que o bits correspondente de ambos os parâmetros são 0.

- - -
### <a name="cbool"></a>CBool
**Descrição:**  
A função de CBool devolve um valor boleano baseado na expressão avaliada

**Sintaxe:**  
`bool CBool(exp Expression)`

**Comentários:**  
Se a expressão é avaliada para um valor diferente de zero, em seguida, CBool devolve True, caso contrário, devolve False.

**Exemplo:**  
`CBool([attrib1] = [attrib2])`  

Atributos de devolve True se ambos têm o mesmo valor.

- - -
### <a name="cdate"></a>CDate
**Descrição:**  
A função de CDate devolve um valor de DateTime de UTC de uma cadeia. DateTime não é um tipo de atributo nativo sincronizado, mas é utilizada por algumas funções.

**Sintaxe:**  
`dt CDate(str value)`

* Valor: Uma cadeia com uma data, hora e, opcionalmente, fuso horário

**Comentários:**  
A cadeia devolvida é sempre em UTC.

**Exemplo:**  
`CDate([employeeStartTime])`  
Devolve que um DateTime com base no empregado a hora de início

`CDate("2013-01-10 4:00 PM -8")`  
Devolve um DateTime que representa "2013-01-11 12:00:00"








- - -
### <a name="certextensionoids"></a>CertExtensionOids
**Descrição:**  
Devolve os valores de Oid de todas as extensões crítico de um objeto de certificado.

**Sintaxe:**  
`mvstr CertExtensionOids(binary certificateRawData)`  
*   certificateRawData: representação de matriz de bytes de um certificado x. 509. A matriz de bytes pode ser binary (DER) codificado ou dados de x. 509 com codificação Base64.

- - -
### <a name="certformat"></a>CertFormat
**Descrição:**  
Devolve o nome do formato deste certificado 509v3.

**Sintaxe:**  
`str CertFormat(binary certificateRawData)`  
*   certificateRawData: representação de matriz de bytes de um certificado x. 509. A matriz de bytes pode ser binary (DER) codificado ou dados de x. 509 com codificação Base64.

- - -
### <a name="certfriendlyname"></a>CertFriendlyName
**Descrição:**  
Devolve o alias associado um certificado.

**Sintaxe:**  
`str CertFriendlyName(binary certificateRawData)`  
*   certificateRawData: representação de matriz de bytes de um certificado x. 509. A matriz de bytes pode ser binary (DER) codificado ou dados de x. 509 com codificação Base64.

- - -
### <a name="certhashstring"></a>CertHashString
**Descrição:**  
Devolve o valor de hash SHA1 do certificado 509v3 como uma cadeia hexadecimal.

**Sintaxe:**  
`str CertHashString(binary certificateRawData)`  
*   certificateRawData: representação de matriz de bytes de um certificado x. 509. A matriz de bytes pode ser binary (DER) codificado ou dados de x. 509 com codificação Base64.

- - -
### <a name="certissuer"></a>CertIssuer
**Descrição:**  
Devolve o nome de autoridade de certificação que emitiu o certificado de 509v3.

**Sintaxe:**  
`str CertIssuer(binary certificateRawData)`  
*   certificateRawData: representação de matriz de bytes de um certificado x. 509. A matriz de bytes pode ser binary (DER) codificado ou dados de x. 509 com codificação Base64.

- - -
### <a name="certissuerdn"></a>CertIssuerDN
**Descrição:**  
Devolve o nome distinto do emissor do certificado.

**Sintaxe:**  
`str CertIssuerDN(binary certificateRawData)`  
*   certificateRawData: representação de matriz de bytes de um certificado x. 509. A matriz de bytes pode ser binary (DER) codificado ou dados de x. 509 com codificação Base64.

- - -
### <a name="certissueroid"></a>CertIssuerOid
**Descrição:**  
Devolve o Oid do emissor do certificado.

**Sintaxe:**  
`str CertIssuerOid(binary certificateRawData)`  
*   certificateRawData: representação de matriz de bytes de um certificado x. 509. A matriz de bytes pode ser binary (DER) codificado ou dados de x. 509 com codificação Base64.

- - -
### <a name="certkeyalgorithm"></a>CertKeyAlgorithm
**Descrição:**  
Devolve as informações de algoritmo de chave para este certificado 509v3 como uma cadeia.

**Sintaxe:**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData: representação de matriz de bytes de um certificado x. 509. A matriz de bytes pode ser binary (DER) codificado ou dados de x. 509 com codificação Base64.

- - -
### <a name="certkeyalgorithmparams"></a>CertKeyAlgorithmParams
**Descrição:**  
Devolve os parâmetros do algoritmo de chave para o certificado de 509v3 como uma cadeia hexadecimal.

**Sintaxe:**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData: representação de matriz de bytes de um certificado x. 509. A matriz de bytes pode ser binary (DER) codificado ou dados de x. 509 com codificação Base64.

- - -
### <a name="certnameinfo"></a>CertNameInfo
**Descrição:**  
Devolve o assunto e um emissor nomes de um certificado.

**Sintaxe:**  
`str CertNameInfo(binary certificateRawData, str x509NameType, bool includesIssuerName)`  
*   certificateRawData: representação de matriz de bytes de um certificado x. 509. A matriz de bytes pode ser binary (DER) codificado ou dados de x. 509 com codificação Base64.
*   X509NameType: O valor de X509NameType para o assunto.
*   includesIssuerName: true para incluir o nome do emissor; caso contrário, FALSO.

- - -
### <a name="certnotafter"></a>CertNotAfter
**Descrição:**  
Devolve a data na hora local após o qual um certificado já não é válido.

**Sintaxe:**  
`dt CertNotAfter(binary certificateRawData)`  
*   certificateRawData: representação de matriz de bytes de um certificado x. 509. A matriz de bytes pode ser binary (DER) codificado ou dados de x. 509 com codificação Base64.

- - -
### <a name="certnotbefore"></a>CertNotBefore
**Descrição:**  
Devolve a data na hora local em que um certificado passa a ser válido.

**Sintaxe:**  
`dt CertNotBefore(binary certificateRawData)`  
*   certificateRawData: representação de matriz de bytes de um certificado x. 509. A matriz de bytes pode ser binary (DER) codificado ou dados de x. 509 com codificação Base64.

- - -
### <a name="certpublickeyoid"></a>CertPublicKeyOid
**Descrição:**  
Devolve o Oid da chave pública do certificado 509v3.

**Sintaxe:**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData: representação de matriz de bytes de um certificado x. 509. A matriz de bytes pode ser binary (DER) codificado ou dados de x. 509 com codificação Base64.

- - -
### <a name="certpublickeyparametersoid"></a>CertPublicKeyParametersOid
**Descrição:**  
Devolve o Oid dos parâmetros de chaves públicos do certificado 509v3.

**Sintaxe:**  
`str CertPublicKeyParametersOid(binary certificateRawData)`  
*   certificateRawData: representação de matriz de bytes de um certificado x. 509. A matriz de bytes pode ser binary (DER) codificado ou dados de x. 509 com codificação Base64.

- - -
### <a name="certserialnumber"></a>CertSerialNumber
**Descrição:**  
Devolve o número de série do certificado 509v3.

**Sintaxe:**  
`str CertSerialNumber(binary certificateRawData)`  
*   certificateRawData: representação de matriz de bytes de um certificado x. 509. A matriz de bytes pode ser binary (DER) codificado ou dados de x. 509 com codificação Base64.

- - -
### <a name="certsignaturealgorithmoid"></a>CertSignatureAlgorithmOid
**Descrição:**  
Devolve o Oid do algoritmo utilizado para criar a assinatura de um certificado.

**Sintaxe:**  
`str CertSignatureAlgorithmOid(binary certificateRawData)`  
*   certificateRawData: representação de matriz de bytes de um certificado x. 509. A matriz de bytes pode ser binary (DER) codificado ou dados de x. 509 com codificação Base64.

- - -
### <a name="certsubject"></a>CertSubject
**Descrição:**  
Obtém o nome do requerente único de um certificado.

**Sintaxe:**  
`str CertSubject(binary certificateRawData)`  
*   certificateRawData: representação de matriz de bytes de um certificado x. 509. A matriz de bytes pode ser binary (DER) codificado ou dados de x. 509 com codificação Base64.

- - -
### <a name="certsubjectnamedn"></a>CertSubjectNameDN
**Descrição:**  
Devolve o nome do requerente único de um certificado.

**Sintaxe:**  
`str CertSubjectNameDN(binary certificateRawData)`  
*   certificateRawData: representação de matriz de bytes de um certificado x. 509. A matriz de bytes pode ser binary (DER) codificado ou dados de x. 509 com codificação Base64.

- - -
### <a name="certsubjectnameoid"></a>CertSubjectNameOid
**Descrição:**  
Devolve o Oid com o nome do requerente de um certificado.

**Sintaxe:**  
`str CertSubjectNameOid(binary certificateRawData)`  
*   certificateRawData: representação de matriz de bytes de um certificado x. 509. A matriz de bytes pode ser binary (DER) codificado ou dados de x. 509 com codificação Base64.

- - -
### <a name="certthumbprint"></a>CertThumbprint
**Descrição:**  
Devolve o thumbprint de um certificado.

**Sintaxe:**  
`str CertThumbprint(binary certificateRawData)`  
*   certificateRawData: representação de matriz de bytes de um certificado x. 509. A matriz de bytes pode ser binary (DER) codificado ou dados de x. 509 com codificação Base64.

- - -
### <a name="certversion"></a>CertVersion
**Descrição:**  
Devolve a versão do formato x. 509 de um certificado.

**Sintaxe:**  
`str CertThumbprint(binary certificateRawData)`  
*   certificateRawData: representação de matriz de bytes de um certificado x. 509. A matriz de bytes pode ser binary (DER) codificado ou dados de x. 509 com codificação Base64.

- - -
### <a name="cguid"></a>CGuid
**Descrição:**  
A função de CGuid converte a representação de cadeia de um GUID respetiva representação binária.

**Sintaxe:**  
`bin CGuid(str GUID)`

* Uma cadeia formatada neste padrão: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx ou {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}

- - -
### <a name="contains"></a>Contains
**Descrição:**  
A função de contém localiza uma cadeia dentro de um atributo com múltiplos valor

**Sintaxe:**  
`num Contains (mvstring attribute, str search)`-maiúsculas e minúsculas  
`num Contains (mvstring attribute, str search, enum Casetype)`  
`num Contains (mvref attribute, str search)`-maiúsculas e minúsculas

* atributo: o atributo com múltiplos valor para procurar.
* pesquisa: cadeia encontrar no atributo.
* Casetype: CaseInsensitive ou CaseSensitive.

Devolve o índice no atributo com múltiplos valor em que a cadeia foi encontrada. 0 é devolvido se a cadeia não foi encontrada.

**Comentários:**  
Para atributos com múltiplos valores de cadeia, a pesquisa localiza subcadeias os valores.  
Para atributos de referência, a cadeia a procurar têm de corresponder exatamente o valor para ser considerado uma correspondência.

**Exemplo:**  
`IIF(Contains([proxyAddresses],"SMTP:")>0,[proxyAddresses],Error("No primary SMTP address found."))`  
Se o atributo de proxyAddresses tem um endereço de correio eletrónico principal (indicado em maiúsculas "SMTP:"), em seguida, devolver o atributo /proxyaddress, caso contrário, devolve um erro.

- - -
### <a name="convertfrombase64"></a>ConvertFromBase64
**Descrição:**  
A função de ConvertFromBase64 converte o valor de codificado em base64 especificado para uma cadeia regular.

**Sintaxe:**  
`str ConvertFromBase64(str source)`-parte do princípio de Unicode para codificar  
`str ConvertFromBase64(str source, enum Encoding)`

* origem: cadeia de codificada em Base64  
* Codificação: UTF8 de Unicode, ASCII,

**Exemplo**  
`ConvertFromBase64("SABlAGwAbABvACAAdwBvAHIAbABkACEA")`  
`ConvertFromBase64("SGVsbG8gd29ybGQh", UTF8)`

Ambos os exemplos de retorno "*Olá, mundo!*"

- - -
### <a name="convertfromutf8hex"></a>ConvertFromUTF8Hex
**Descrição:**  
A função de ConvertFromUTF8Hex converte o valor hexadecimal UTF8 codificado especificado numa cadeia.

**Sintaxe:**  
`str ConvertFromUTF8Hex(str source)`

* origem: sting codificado de 2 bytes UTF8

**Comentários:**  
A diferença entre a esta função e ConvertFromBase64([],UTF8) em que o resultado é amigável para o atributo DN.  
Este formato é utilizado pelo Azure Active Directory como DN.

**Exemplo:**  
`ConvertFromUTF8Hex("48656C6C6F20776F726C6421")`  
Devolve "*Olá, mundo!*"

- - -
### <a name="converttobase64"></a>ConvertToBase64
**Descrição:**  
A função de ConvertToBase64 converte uma cadeia de uma cadeia de base64 Unicode.  
Converte o valor de uma matriz de números inteiros respetiva representação de cadeia equivalente está codificada com base 64 dígitos.

**Sintaxe:**  
`str ConvertToBase64(str source)`

**Exemplo:**  
`ConvertToBase64("Hello world!")`  
Devolve "SABlAGwAbABvACAAdwBvAHIAbABkACEA"

- - -
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**Descrição:**  
A função de ConvertToUTF8Hex converte uma cadeia para um valor hexadecimal UTF8 codificado.

**Sintaxe:**  
`str ConvertToUTF8Hex(str source)`

**Comentários:**  
O formato de saída desta função é utilizado pelo Azure Active Directory como formato do atributo de DN.

**Exemplo:**  
`ConvertToUTF8Hex("Hello world!")`  
Devolve 48656C6C6F20776F726C6421

- - -
### <a name="count"></a>Contagem
**Descrição:**  
A função Count devolve o número de elementos num atributo com múltiplos valor

**Sintaxe:**  
`num Count(mvstr attribute)`

- - -
### <a name="cnum"></a>CNum
**Descrição:**  
A função de CNum utiliza uma cadeia e devolve um tipo de dados numérico.

**Sintaxe:**  
`num CNum(str value)`

- - -
### <a name="cref"></a>CRef
**Descrição:**  
Converte uma cadeia para um atributo de referência

**Sintaxe:**  
`ref CRef(str value)`

**Exemplo:**  
`CRef("CN=LC Services,CN=Microsoft,CN=lcspool01,CN=Pools,CN=RTC Service," & %Forest.LDAP%)`

- - -
### <a name="cstr"></a>CStr
**Descrição:**  
A função de CStr converte um tipo de dados de cadeia.

**Sintaxe:**  
`str CStr(num value)`  
`str CStr(ref value)`  
`str CStr(bool value)`  

* valor: pode ser um valor numérico, um atributo de referência ou um booleano.

**Exemplo:**  
`CStr([dn])`  
Devolver "cn = Joe, dc = contoso, dc = com"

- - -
### <a name="dateadd"></a>DateAdd
**Descrição:**  
Devolve uma data que contenha uma data à qual foi adicionado um intervalo de tempo especificado.

**Sintaxe:**  
`dt DateAdd(str interval, num value, dt date)`

* intervalo: cadeia de expressão que é o intervalo de tempo que pretende adicionar. A cadeia tem de ter um dos seguintes valores:
  * AAAA ano
  * q trimestre
  * m mês
  * y dia do ano
  * d dia
  * w dia da semana
  * ww semana
  * Hora de h
  * n minuto
  * s segundo
* valor: O número de unidades que pretende adicionar. Pode ser positivo (para obter as datas no futuro) ou negativo (para obter as datas anteriormente).
* Data: DateTime, que representa a data em que o intervalo é adicionado.

**Exemplo:**  
`DateAdd("m", 3, CDate("2001-01-01"))`  
Adiciona 3 meses e devolve um valor DateTime que representa "2001-04-01".

- - -
### <a name="datefromnum"></a>DateFromNum
**Descrição:**  
Converte de função DateFromNum formatar um valor de data do AD para um tipo DateTime.

**Sintaxe:**  
`dt DateFromNum(num value)`

**Exemplo:**  
`DateFromNum([lastLogonTimestamp])`  
`DateFromNum(129699324000000000)`  
Devolve um valor DateTime que representa 2012-01-01 23:00:00

- - -
### <a name="dncomponent"></a>DNComponent
**Descrição:**  
A função de DNComponent devolve o valor de um componente DN especificado que vai da esquerda.

**Sintaxe:**  
`str DNComponent(ref dn, num ComponentNumber)`

* DN: o atributo de referência para interpretar
* ComponentNumber: O componente no DN a devolver

**Exemplo:**  
`DNComponent([dn],1)`  
Se for dn "cn = Joe, UO =...," devolve Joe

- - -
### <a name="dncomponentrev"></a>DNComponentRev
**Descrição:**  
A função de DNComponentRev devolve o valor de um componente DN especificado que vai da direita (fim).

**Sintaxe:**  
`str DNComponentRev(ref dn, num ComponentNumber)`  
`str DNComponentRev(ref dn, num ComponentNumber, enum Options)`

* DN: o atributo de referência para interpretar
* ComponentNumber - o componente no DN a devolver
* Opções: DC – ignorar todos os componentes com "dc ="

**Exemplo:**  
Se for dn "cn = Joe, UO = Atlanta, UO = DG, UO = US, dc = contoso, dc = com", em seguida,  
`DNComponentRev([dn],3)`  
`DNComponentRev([dn],1,"DC")`  
Ambos devolvem-nos.

- - -
### <a name="error"></a>Erro
**Descrição:**  
A função de erro é utilizada para devolver um erro personalizado.

**Sintaxe:**  
`void Error(str ErrorMessage)`

**Exemplo:**  
`IIF(IsPresent([accountName]),[accountName],Error("AccountName is required"))`  
Se o atributo accountName não estiver presente, gerar um erro no objeto.

- - -
### <a name="escapedncomponent"></a>EscapeDNComponent
**Descrição:**  
A função de EscapeDNComponent assume um componente de um DN e escapes-la para que podem ser representado no LDAP.

**Sintaxe:**  
`str EscapeDNComponent(str value)`

**Exemplo:**  
`EscapeDNComponent("cn=" & [displayName]) & "," & %ForestLDAP%)`  
Certifica-se do que objeto pode ser criado num diretório LDAP, mesmo que o atributo displayName tem carateres tem escape no LDAP.

- - -
### <a name="formatdatetime"></a>FormatDateTime
**Descrição:**  
A função de FormatDateTime é utilizada para formatar um DateTime numa cadeia com um formato especificado

**Sintaxe:**  
`str FormatDateTime(dt value, str format)`

* valor: um valor no formato DateTime
* formato: uma cadeia representando o formato a converter.

**Comentários:**  
Os valores possíveis para o formato podem ser encontrados aqui: [definidas pelo utilizador (formato função) de formatos de data/hora](http://msdn2.microsoft.com/library/73ctwf33\(VS.90\).aspx)

**Exemplo:**  

`FormatDateTime(CDate("12/25/2007"),"yyyy-mm-dd")`  
Resultados no "2007-12-25".

`FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")`  
Pode resultar em "20140905081453.0Z"

- - -
### <a name="guid"></a>GUID
**Descrição:**  
A função GUID gera um novo GUID aleatório

**Sintaxe:**  
`str GUID()`

- - -
### <a name="iif"></a>IIF
**Descrição:**  
A função ' IIF devolve um de um conjunto de valores possíveis com base numa condição especificada.

**Sintaxe:**  
`var IIF(exp condition, var valueIfTrue, var valueIfFalse)`

* condição: qualquer valor ou expressão que pode ser avaliado como true ou false.
* valueIfTrue: se a condição for avaliada como true, o valor devolvido.
* valueIfFalse: se a condição for avaliada como false, o valor devolvido.

**Exemplo:**  
`IIF([employeeType]="Intern","t-" & [alias],[alias])`  
 Se o utilizador for um estagiário, devolve o alias de um utilizador com "t-" adicionado no início do mesmo pessoa devolve o alias do utilizador conforme está.

- - -
### <a name="instr"></a>InStr
**Descrição:**  
A função de InStr localiza a primeira ocorrência de uma subcadeia numa cadeia

**Sintaxe:**  

`num InStr(str stringcheck, str stringmatch)`  
`num InStr(str stringcheck, str stringmatch, num start)`  
`num InStr(str stringcheck, str stringmatch, num start , enum compare)`

* stringcheck: cadeia a ser procurado
* stringmatch: cadeia ser encontrado
* iniciar: Iniciar posição para determinar a subcadeia
* comparar: vbTextCompare ou vbBinaryCompare

**Comentários:**  
Devolve a posição em que a subcadeia encontrada ou 0 se não for encontrado.

**Exemplo:**  
`InStr("The quick brown fox","quick")`  
Evalues 5

`InStr("repEated","e",3,vbBinaryCompare)`  
Avalia a 7

- - -
### <a name="instrrev"></a>InStrRev
**Descrição:**  
A função de InStrRev localiza a última ocorrência de uma subcadeia numa cadeia

**Sintaxe:**  
`num InstrRev(str stringcheck, str stringmatch)`  
`num InstrRev(str stringcheck, str stringmatch, num start)`  
`num InstrRev(str stringcheck, str stringmatch, num start, enum compare)`

* stringcheck: cadeia a ser procurado
* stringmatch: cadeia ser encontrado
* iniciar: Iniciar posição para determinar a subcadeia
* comparar: vbTextCompare ou vbBinaryCompare

**Comentários:**  
Devolve a posição em que a subcadeia encontrada ou 0 se não for encontrado.

**Exemplo:**  
`InStrRev("abbcdbbbef","bb")`  
Devolve 7

- - -
### <a name="isbitset"></a>IsBitSet
**Descrição:**  
A função IsBitSet testes se um bit está definida ou não

**Sintaxe:**  
`bool IsBitSet(num value, num flag)`

* valor: um valor numérico evaluated.flag: um valor numérico que tem o bit a ser avaliada

**Exemplo:**  
`IsBitSet(&HF,4)`  
Devolve VERDADEIRO se porque bits "4" está definida no valor hexadecimal "F"

- - -
### <a name="isdate"></a>IsDate
**Descrição:**  
Se a expressão pode ser avalia como um tipo DateTime, em seguida, a função de IsDate avalia como verdadeiro.

**Sintaxe:**  
`bool IsDate(var Expression)`

**Comentários:**  
Utilizado para determinar se CDate() pode ser efetuada com êxito.

- - -
### <a name="iscert"></a>IsCert
**Descrição:**  
Devolve true se os dados não processados podem ser serializados no objeto de certificado .NET X509Certificate2.

**Sintaxe:**  
`bool CertThumbprint(binary certificateRawData)`  
*   certificateRawData: representação de matriz de bytes de um certificado x. 509. A matriz de bytes pode ser binary (DER) codificado ou dados de x. 509 com codificação Base64.
- - -
### <a name="isempty"></a>IsEmpty
**Descrição:**  
Se o atributo está presente no CS ou MV mas for avaliada como uma cadeia vazia, a função de IsEmpty avalia como verdadeiro.

**Sintaxe:**  
`bool IsEmpty(var Expression)`

- - -
### <a name="isguid"></a>IsGuid
**Descrição:**  
Se a cadeia pode ser convertida num GUID, em seguida, a função de IsGuid avaliada como true.

**Sintaxe:**  
`bool IsGuid(str GUID)`

**Comentários:**  
Um GUID está definido como uma cadeia de seguir uma destes padrões: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx ou {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}

Utilizado para determinar se CGuid() pode ser efetuada com êxito.

**Exemplo:**  
`IIF(IsGuid([strAttribute]),CGuid([strAttribute]),NULL)`  
Se o StrAttribute tem um formato GUID, devolve uma representação binária, caso contrário, devolve um valor nulo.

- - -
### <a name="isnull"></a>IsNull
**Descrição:**  
Se a expressão é avaliada como nulo, a função de IsNull devolve true.

**Sintaxe:**  
`bool IsNull(var Expression)`

**Comentários:**  
Para um atributo, um valor nulo é expresso pela ausência do atributo.

**Exemplo:**  
`IsNull([displayName])`  
Devolve True se o atributo não está presente no CS ou MV.

- - -
### <a name="isnullorempty"></a>IsNullOrEmpty
**Descrição:**  
Se a expressão é nulo ou uma cadeia vazia, a função de IsNullOrEmpty devolve true.

**Sintaxe:**  
`bool IsNullOrEmpty(var Expression)`

**Comentários:**  
Para um atributo, isto seria avaliar como VERDADEIRO se o atributo está ausente ou estiver presente, mas é uma cadeia vazia.  
O inverso desta função é denominado IsPresent.

**Exemplo:**  
`IsNullOrEmpty([displayName])`  
Devolve True se o atributo não está presente ou é uma cadeia vazia na CS ou MV.

- - -
### <a name="isnumeric"></a>IsNumeric
**Descrição:**  
A função de IsNumeric devolve um valor de Booleano indicando se uma expressão pode ser avaliada como um tipo de número.

**Sintaxe:**  
`bool IsNumeric(var Expression)`

**Comentários:**  
Utilizado para determinar se CNum() pode ser efetuada com êxito ao analisar a expressão.

- - -
### <a name="isstring"></a>IsString
**Descrição:**  
Se a expressão pode ser avaliada como um tipo de cadeia, a função de IsString avalia como verdadeiro.

**Sintaxe:**  
`bool IsString(var expression)`

**Comentários:**  
Utilizado para determinar se CStr() pode ser efetuada com êxito ao analisar a expressão.

- - -
### <a name="ispresent"></a>IsPresent
**Descrição:**  
Se a expressão é avaliada para uma cadeia que não seja nulo e não está vazia, a função de IsPresent devolve true.

**Sintaxe:**  
`bool IsPresent(var expression)`

**Comentários:**  
O inverso desta função é denominado IsNullOrEmpty.

**Exemplo:**  
`Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])`

- - -
### <a name="item"></a>Item
**Descrição:**  
A função Item devolve um item de um cadeia/atributos com múltiplos valores.

**Sintaxe:**  
`var Item(mvstr attribute, num index)`

* atributo: atributo com múltiplos valor
* o índice: índice para um item na cadeia com múltiplos valor.

**Comentários:**  
A função Item é útil em conjunto com a função de contém desde a última função devolve o índice para um item no atributo com múltiplos valor.

Emitir um erro se o índice está fora dos limites.

**Exemplo:**  
`Mid(Item([proxyAddress],Contains([proxyAddress], "SMTP:")),6)`  
Devolve o endereço de correio eletrónico principal.

- - -
### <a name="itemornull"></a>ItemOrNull
**Descrição:**  
A função de ItemOrNull devolve um item de um cadeia/atributos com múltiplos valores.

**Sintaxe:**  
`var ItemOrNull(mvstr attribute, num index)`

* atributo: atributo com múltiplos valor
* o índice: índice para um item na cadeia com múltiplos valor.

**Comentários:**  
A função de ItemOrNull é útil em conjunto com a função de contém desde a última função devolve o índice para um item no atributo com múltiplos valor.

Se o índice está fora dos limites, em seguida, devolve um valor nulo.

- - -
### <a name="join"></a>Associar
**Descrição:**  
A função de associação tem uma cadeia com múltiplos valor e devolve uma cadeia de valor único com separador especificado inserido entre cada item.

**Sintaxe:**  
`str Join(mvstr attribute)`  
`str Join(mvstr attribute, str Delimiter)`

* atributo: atributo com múltiplos valor que contém as cadeias para ser associado.
* delimitador: qualquer cadeia, utilizada para separar subcadeias na cadeia devolvida. Se for omitido, o caráter de espaço ("") é utilizado. Se o delimitador é uma cadeia de comprimento zero ("") ou nada, todos os itens na lista são concatenados com nenhuma delimitadores.

**Observações**  
Existe paridade entre as funções de associação e divisão. A função de associação tem uma matriz de cadeias e associa-los através de uma cadeia de delimitador para devolver uma cadeia única. A função de divisão assume uma cadeia e separa o delimitador, para devolver uma matriz de cadeias. No entanto, uma principal diferença é que a associação pode concatenar cadeias com qualquer cadeia delimitador, divisão só pode separar cadeias utilizando um único caráter delimitador.

**Exemplo:**  
`Join([proxyAddresses],",")`  
Devolver: "SMTP:john.doe@contoso.com,smtp:jd@contoso.com"

- - -
### <a name="lcase"></a>LCase
**Descrição:**  
A função de LCase converte todos os carateres existentes numa cadeia caso inferior.

**Sintaxe:**  
`str LCase(str value)`

**Exemplo:**  
`LCase("TeSt")`  
Devolve "teste".

- - -
### <a name="left"></a>À esquerda
**Descrição:**  
A função à esquerda devolve um número especificado de carateres do lado esquerdo de uma cadeia.

**Sintaxe:**  
`str Left(str string, num NumChars)`

* cadeia: a cadeia a devolver carateres do
* NumChars: um número que identifica o número de carateres a devolver a partir do início (esquerdo) de cadeia

**Comentários:**  
Uma cadeia que contém os carateres numChars primeiro na cadeia:

* Se numChars = 0, devolver uma cadeia vazia.
* Se numChars < 0, devolver a cadeia de entrada.
* Se a cadeia é nula, devolva uma cadeia vazia.

Se a cadeia contém menos carateres do que o numChars especificado no número, é devolvida uma cadeia idêntica à cadeia (o que é, que contém todos os caracteres no parâmetro 1).

**Exemplo:**  
`Left("John Doe", 3)`  
Devolve "Joh".

- - -
### <a name="len"></a>Len
**Descrição:**  
A função de Len devolve o número de carateres existentes numa cadeia.

**Sintaxe:**  
`num Len(str value)`

**Exemplo:**  
`Len("John Doe")`  
Devolve 8

- - -
### <a name="ltrim"></a>LTrim
**Descrição:**  
A função de LTrim remove espaços em branco à esquerda de uma cadeia.

**Sintaxe:**  
`str LTrim(str value)`

**Exemplo:**  
`LTrim(" Test ")`  
Devolve "teste"

- - -
### <a name="mid"></a>Mid
**Descrição:**  
A função de Mid devolve um número especificado de carateres da posição especificada numa cadeia.

**Sintaxe:**  
`str Mid(str string, num start, num NumChars)`

* cadeia: a cadeia a devolver carateres do
* iniciar: um número que identifica a iniciar a posição na cadeia para devolver carateres do
* NumChars: um número que identifica o número de carateres a devolver a partir da posição na cadeia

**Comentários:**  
Devolva a partir do início da posição na cadeia de carateres de numChars.  
Uma cadeia contendo numChars carateres da posição de início na cadeia:

* Se numChars = 0, devolver uma cadeia vazia.
* Se numChars < 0, devolver a cadeia de entrada.
* Se iniciar > o comprimento da cadeia, cadeia de entrada de retorno.
* Se iniciar < = 0, devolve a cadeia de entrada.
* Se a cadeia é nula, devolva uma cadeia vazia.

Se não existirem carateres numChar restante na cadeia de início de posição, como o número de carateres como possíveis é devolvido.

**Exemplo:**  
`Mid("John Doe", 3, 5)`  
Devolve "hn efetue".

`Mid("John Doe", 6, 999)`  
Devolve "Silva"

- - -
### <a name="now"></a>Agora
**Descrição:**  
A função agora devolve um valor de DateTime especificando a data e hora atuais, de acordo com a data do sistema e a hora do seu computador.

**Sintaxe:**  
`dt Now()`

- - -
### <a name="numfromdate"></a>NumFromDate
**Descrição:**  
A função de NumFromDate devolve uma data no formato de data do AD.

**Sintaxe:**  
`num NumFromDate(dt value)`

**Exemplo:**  
`NumFromDate(CDate("2012-01-01 23:00:00"))`  
Devolve 129699324000000000

- - -
### <a name="padleft"></a>Padleft do modelo
**Descrição:**  
Os padleft modelo função left-pads uma cadeia para um comprimento especificado com um caráter de preenchimento fornecido.

**Sintaxe:**  
`str PadLeft(str string, num length, str padCharacter)`

* cadeia: a cadeia a preencher.
* comprimento: um número inteiro que representa o comprimento pretendido da cadeia.
* padCharacter: uma cadeia consistindo num caráter plica para utilizar como o caráter de preenchimento

**Comentários:**

* Se o comprimento da cadeia é inferior ao comprimento, padCharacter repetidamente é acrescentada ao início (esquerda) do cadeia até ter um comprimento igual ao comprimento.
* padCharacter pode ser um caráter de espaço, mas não pode ser um valor nulo.
* Se o comprimento da cadeia de é igual ou superior ao comprimento, é devolveu uma cadeia inalterada.
* Se a cadeia tem um comprimento maior que ou igual ao comprimento, é devolvida uma cadeia idêntica à cadeia.
* Se o comprimento da cadeia é inferior ao comprimento, é devolvida uma nova cadeia do comprimento pretendida cadeia que contém será preenchida com um padCharacter.
* Se a cadeia for nula, a função devolve uma cadeia vazia.

**Exemplo:**  
`PadLeft("User", 10, "0")`  
Devolve "000000User".

- - -
### <a name="padright"></a>PadRight
**Descrição:**  
Os PadRight função direito-pads uma cadeia para um comprimento especificado com um caráter de preenchimento fornecido.

**Sintaxe:**  
`str PadRight(str string, num length, str padCharacter)`

* cadeia: a cadeia a preencher.
* comprimento: um número inteiro que representa o comprimento pretendido da cadeia.
* padCharacter: uma cadeia consistindo num caráter plica para utilizar como o caráter de preenchimento

**Comentários:**

* Se o comprimento da cadeia é inferior ao comprimento, em seguida, padCharacter é repetidamente acrescentado ao fim (direito) de cadeia até ter um comprimento igual ao comprimento.
* padCharacter pode ser um caráter de espaço, mas não pode ser um valor nulo.
* Se o comprimento da cadeia de é igual ou superior ao comprimento, é devolveu uma cadeia inalterada.
* Se a cadeia tem um comprimento maior que ou igual ao comprimento, é devolvida uma cadeia idêntica à cadeia.
* Se o comprimento da cadeia é inferior ao comprimento, é devolvida uma nova cadeia do comprimento pretendida cadeia que contém será preenchida com um padCharacter.
* Se a cadeia for nula, a função devolve uma cadeia vazia.

**Exemplo:**  
`PadRight("User", 10, "0")`  
Devolve "User000000".

- - -
### <a name="pcase"></a>PCase
**Descrição:**  
A função de PCase converte o primeiro caráter de cada palavra espaço delimitada por numa cadeia de maiúsculas e todos os outros carateres são convertidos em maiúsculas e minúsculas inferior.

**Sintaxe:**  
`String PCase(string)`

**Comentários:**

* Esta função não fornece maiúsculas e minúsculas adequada para converter uma palavra que é completamente em maiúsculas, tais como um acrónimo.

**Exemplo:**  
`PCase("TEsT")`  
Devolve o "Teste".

`PCase(LCase("TEST"))`  
Devolve "teste"

- - -
### <a name="randomnum"></a>RandomNum
**Descrição:**  
A função de RandomNum devolve um número aleatório entre um intervalo especificado.

**Sintaxe:**  
`num RandomNum(num start, num end)`

* iniciar: um número que identifica o limite inferior do valor aleatório para gerar
* fim: um número que identifica o limite superior do valor aleatório para gerar

**Exemplo:**  
`Random(100,999)`  
Pode devolver 734.

- - -
### <a name="removeduplicates"></a>RemoveDuplicates
**Descrição:**  
A função de RemoveDuplicates assume uma cadeia com múltiplos valor e certifique-se de que cada valor é exclusivo.

**Sintaxe:**  
`mvstr RemoveDuplicates(mvstr attribute)`

**Exemplo:**  
`RemoveDuplicates([proxyAddresses])`  
Devolve um atributo /proxyaddress sanitized onde foram removidos todos os valores duplicados.

- - -
### <a name="replace"></a>Substituir
**Descrição:**  
A função de substituição substitui todas as ocorrências de uma cadeia de outra cadeia.

**Sintaxe:**  
`str Replace(str string, str OldValue, str NewValue)`

* cadeia: uma cadeia para substituir valores.
* OldValue: A cadeia para procurar e substituir.
* NewValue: A cadeia para substituir a.

**Comentários:**  
A função reconhece os monikers especiais seguintes:

* \n – nova linha
* \r – avanço devolver
* \t – separador

**Exemplo:**  
`Replace([address],"\r\n",", ")`  
Substitui CRLF com uma vírgula e um espaço e podem levar à "Um Microsoft forma, Redmond, WA, EUA"

- - -
### <a name="replacechars"></a>ReplaceChars
**Descrição:**  
A função de ReplaceChars substitui todas as ocorrências de foram encontrados na cadeia de ReplacePattern caracteres.

**Sintaxe:**  
`str ReplaceChars(str string, str ReplacePattern)`

* cadeia: uma cadeia para substituir carateres.
* ReplacePattern: uma cadeia contendo um dicionário com carateres para substituir.

O formato é {source1}: {target1}, {source2}: {target2}, {sourceN}, {targetN} em que a origem é o caráter para localizar e a cadeia de substituir de destino.

**Comentários:**

* A função aceita cada ocorrência dos origens definidas e substitui-los com os destinos.
* A origem tem de ser exactamente um carácter (unicode).
* A origem não pode estar vazio nem ser maior do que um caráter (erro de análise).
* O destino pode ter vários carateres, por exemplo, ö:oe, β:ss.
* O destino pode ser vazio que indica que o caráter deve ser removido.
* A origem é maiúsculas e minúsculas e têm de ser uma correspondência exata.
* (Vírgula) e: (dois pontos) são carateres reservados e não pode ser substituído utilizando esta função.
* Os espaços e outros carateres brancos na cadeia de ReplacePattern são ignorados.

**Exemplo:**  
`%ReplaceString% = ’:,Å:A,Ä:A,Ö:O,å:a,ä:a,ö,o`

`ReplaceChars("Räksmörgås",%ReplaceString%)`  
Devolve Raksmorgas

`ReplaceChars("O’Neil",%ReplaceString%)`  
Devolve "ONeil", a único marcas de escala não está definida para ser removido.

- - -
### <a name="right"></a>À direita
**Descrição:**  
A função à direita devolve um número especificado de carateres à direita (fim) de uma cadeia.

**Sintaxe:**  
`str Right(str string, num NumChars)`

* cadeia: a cadeia a devolver carateres do
* NumChars: um número que identifica o número de carateres a devolver a partir de fim (direito) da cadeia

**Comentários:**  
NumChars carateres são devolvidos da última posição da cadeia.

Uma cadeia que contém os carateres numChars último na cadeia:

* Se numChars = 0, devolver uma cadeia vazia.
* Se numChars < 0, devolver a cadeia de entrada.
* Se a cadeia é nula, devolva uma cadeia vazia.

Se a cadeia contém menos carateres do que o NumChars especificado no número, é devolvida uma cadeia idêntica à cadeia.

**Exemplo:**  
`Right("John Doe", 3)`  
Devolve "Silva".

- - -
### <a name="rtrim"></a>RTrim
**Descrição:**  
A função de RTrim remove espaços em branco à direita de uma cadeia.

**Sintaxe:**  
`str RTrim(str value)`

**Exemplo:**  
`RTrim(" Test ")`  
Devolve o "Teste".

- - -
### <a name="select"></a>Selecione
**Descrição:**  
Processo de todos os valores de um atributo com múltiplos valores (ou saída de uma expressão) com base na função especificada.

**Sintaxe:**  
`mvattr Select(variable item, mvattr attribute, func function)`  
`mvattr Select(variable item, exp expression, func function)`

* item: representa um elemento no atributo com múltiplos valor
* atributo: o atributo com múltiplos valor
* expressão: uma expressão que devolve uma coleção de valores
* condição: qualquer função que pode processar um item no atributo

**Exemplos:**  
`Select($item,[otherPhone],Replace($item,"-",""))`  
Devolva todos os valores otherPhone o atributo com múltiplos valores depois de ter sido removido hífenes (-).

- - -
### <a name="split"></a>Divisão
**Descrição:**  
A função de divisão demora uma cadeia separada por com um delimitador e torna uma cadeia com múltiplos valor.

**Sintaxe:**  
`mvstr Split(str value, str delimiter)`  
`mvstr Split(str value, str delimiter, num limit)`

* valor: a cadeia com um caráter delimitador para separar.
* delimitador: único caráter a ser utilizado como o delimitador.
* limite: o número máximo de valores que podem devolver.

**Exemplo:**  
`Split("SMTP:john.doe@contoso.com,smtp:jd@contoso.com",",")`  
Devolve uma cadeia com múltiplos valor com 2 elementos útil para o atributo /proxyaddress.

- - -
### <a name="stringfromguid"></a>StringFromGuid
**Descrição:**  
A função de StringFromGuid assume um GUID binário e converte-o numa cadeia

**Sintaxe:**  
`str StringFromGuid(bin GUID)`

- - -
### <a name="stringfromsid"></a>StringFromSid
**Descrição:**  
A função de StringFromSid converte uma matriz de bytes que contém um identificador de segurança para uma cadeia.

**Sintaxe:**  
`str StringFromSid(bin ObjectSID)`  

- - -
### <a name="switch"></a>Comutador
**Descrição:**  
A função de comutador é utilizada para devolver um valor único, com base nas condições avaliadas.

**Sintaxe:**  
`var Switch(exp expr1, var value1[, exp expr2, var value … [, exp expr, var valueN]])`

* expr: expressão Variant que pretende avaliar.
* valor: valor a ser devolvido se a expressão correspondente for VERDADEIRO.

**Comentários:**  
A lista de argumentos da função de comutador é constituído por pares de valores e expressões. As expressões são avaliadas da esquerda para a direita e é devolvido o valor associado a primeira expressão a avaliar como verdadeiro. Se as partes corretamente não estão emparelhadas, ocorre um erro de tempo de execução.

Por exemplo, se expr1 for True, o comutador devolve value1. Se expr-1 é False, mas expr-2 é verdadeiro, o comutador devolve o valor 2 e assim sucessivamente.

Comutador devolve um nada se:

* Nenhuma das expressões é verdadeiro.
* A primeira expressão True tem um valor correspondente, que é Null.

Comutador avalia todas as expressões, apesar de devolve apenas uma delas. Por este motivo, deve procurar indesejáveis efeitos secundários. Por exemplo, se resulta da avaliação de uma expressão numa divisão por zero erro, ocorre um erro.

Valor também pode ser a função de erro, o que devolva uma cadeia personalizada.

**Exemplo:**  
`Switch([city] = "London", "English", [city] = "Rome", "Italian", [city] = "Paris", "French", True, Error("Unknown city"))`  
Devolve o idioma ditas algumas cidades principais, caso contrário, devolve um erro.

- - -
### <a name="trim"></a>Cortar
**Descrição:**  
A função de compactação remove espaços em branco de uma cadeia de direita e à esquerda.

**Sintaxe:**  
`str Trim(str value)`  

**Exemplo:**  
`Trim(" Test ")`  
Devolve o "Teste".

`Trim([proxyAddresses])`  
Remove espaços para cada valor de atributo /proxyaddress à esquerda e.

- - -
### <a name="ucase"></a>UCase
**Descrição:**  
A função de UCase converte todos os carateres existentes numa cadeia de maiúsculas.

**Sintaxe:**  
`str UCase(str string)`

**Exemplo:**  
`UCase("TeSt")`  
Devolve "Teste".

- - -
### <a name="where"></a>onde

**Descrição:**  
Devolve um subconjunto dos valores de um atributo com múltiplos valores (ou saída de uma expressão) com base numa condição específica.

**Sintaxe:**  
`mvattr Where(variable item, mvattr attribute, exp condition)`  
`mvattr Where(variable item, exp expression, exp condition)`  
* item: representa um elemento no atributo com múltiplos valor
* atributo: o atributo com múltiplos valor
* condição: uma expressão que pode ser avaliada como true ou false
* expressão: uma expressão que devolve uma coleção de valores

**Exemplo:**  
`Where($item,[userCertificate],CertNotAfter($item)>Now())`  
Devolva os valores de certificado userCertificate o atributo com múltiplos valores que não estão expirados.

- - -
### <a name="with"></a>com o
**Descrição:**  
A função de With fornece uma forma para simplificar a uma expressão complexa, utilizando uma variável para representar um subexpression que aparece um ou mais vezes na expressão complexa.

**Sintaxe:**
`With(var variable, exp subExpression, exp complexExpression)`  
* variável: representa o subexpression.
* subExpression: subexpression representado pela variável.
* complexExpression: uma expressão complexa.

**Exemplo:**  
`With($unExpiredCerts,Where($item,[userCertificate],CertNotAfter($item)>Now()),IIF(Count($unExpiredCerts)>0,$unExpiredCerts,NULL))`  
É funcionalmente equivalente à:  
`IIF (Count(Where($item,[userCertificate],CertNotAfter($item)>Now()))>0, Where($item,[userCertificate],CertNotAfter($item)>Now()),NULL)`  
Que devolve apenas os valores de certificado não expiradas no atributo userCertificate.


- - -
### <a name="word"></a>Word
**Descrição:**  
A função do Word devolve uma palavra contida uma cadeia, com base nos parâmetros que descrevem os delimitadores a utilizar e o número de palavras para devolver.

**Sintaxe:**  
`str Word(str string, num WordNumber, str delimiters)`

* cadeia: a cadeia para devolver uma palavra de.
* WordNumber: deverá devolver um número que identifica o número da palavra.
* os delimitadores: uma cadeia representando o delimiter(s) que deve ser utilizado para identificar as palavras

**Comentários:**  
Cada cadeia de carateres na cadeia separados por um dos carateres no delimitadores são identificadas como palavras:

* Se number < 1, devolve uma cadeia vazia.
* Se a cadeia é nula, devolve uma cadeia vazia.

Se a cadeia contém menos palavras número ou cadeia não contém quaisquer palavras identificadas por delimitadores, é devolvida uma cadeia vazia.

**Exemplo:**  
`Word("The quick brown fox",3," ")`  
Devolve "castanha"

`Word("This,string!has&many separators",3,",!&#")`  
Devolvam "tem"

## <a name="additional-resources"></a>Recursos Adicionais
* [Compreender as expressões do aprovisionamento declarativas](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md)
* [Sincronização do Azure AD Connect: Personalizar as opções de sincronização](active-directory-aadconnectsync-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](active-directory-aadconnect.md)
