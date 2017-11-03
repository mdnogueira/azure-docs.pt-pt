---
title: "Azure AD Connect: Expressões de aprovisionamento declarativas | Microsoft Docs"
description: "Explica as expressões de aprovisionamento declarativas."
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: e3ea53c8-3801-4acf-a297-0fb9bb1bf11d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: e3a03a97b10e04fb85261620879b2102e1db8465
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-connect-sync-understanding-declarative-provisioning-expressions"></a>Sincronização do Azure AD Connect: Noções sobre expressões de aprovisionamento declarativo
Sincronização do Azure AD Connect baseia-se aprovisionamento declarativo introduzida pela primeira vez no Forefront Identity Manager 2010. Permite-lhe implementar a lógica de negócio de integração de identidade concluída sem a necessidade de escrever código compilado.

Uma parte essencial do aprovisionamento declarativo é o idioma de expressão utilizado em fluxos de atributos. O idioma utilizado é um subconjunto do Microsoft® Visual Basic® para aplicações (VBA). Neste idioma é utilizado no Microsoft Office e os utilizadores com a experiência de VBScript também reconhecê-lo. O idioma de expressão aprovisionamento declarativo estiver a utilizar apenas as funções e não é uma linguagem structured. Não são métodos ou instruções. As funções em vez disso, estão aninhadas a fluxo programa rápida.

Para obter mais detalhes, consulte [bem-vindo ao Visual Basic, de referência de linguagem de aplicações do Office 2013](https://msdn.microsoft.com/library/gg264383.aspx).

Os atributos são do tipo seguro. Uma função só aceita atributos do tipo correto. Também é maiúsculas e minúsculas. Os nomes de função e os nomes de atributo tem maiúsculas e minúsculas adequada ou emitido um erro.

## <a name="language-definitions-and-identifiers"></a>Definições de idioma e identificadores
* As funções de ter um nome seguido de argumentos entre parênteses Retos: FunctionName (argumento 1, o argumento N).
* Atributos são identificados por parênteses Retos: [attributeName]
* Os parâmetros são identificados por percentagem sinais: % ParameterName %
* As constantes String são rodeadas por aspas: por exemplo, "Contoso" (Nota: tem de utilizar as aspas direitas "" e não smart aspas "")
* Valores numéricos são expressos sem aspas e deve ser decimal. Valores hexadecimais têm o prefixo & H. Por exemplo, 98052 & HFF
* Os valores booleanos são expressas com constantes: True, False.
* Constantes incorporadas e literais são expressas com apenas os respetivos nomes: IgnoreThisFlow NULL, CRLF,

### <a name="functions"></a>Funções
Aprovisionamento declarativo utiliza muitas funções para ativar a possibilidade de transformar os valores de atributo. Estas funções podem ser aninhadas para que o resultado de uma função é transmitido para outra função.

`Function1(Function2(Function3()))`

A lista completa de funções pode ser encontrada no [funcionar referência](active-directory-aadconnectsync-functions-reference.md).

### <a name="parameters"></a>Parâmetros
Um parâmetro está definido por um conector ou por um administrador através do PowerShell. Os parâmetros normalmente contém valores que são diferentes do sistema para o sistema, por exemplo o nome do domínio, o utilizador está localizado na. Estes parâmetros podem ser utilizados em fluxos de atributos.

O conector do Active Directory fornecidos os seguintes parâmetros para regras de sincronização de entrada:

| Nome do Parâmetro | Comentário |
| --- | --- |
| Domain.Netbios |Formato de NetBIOS do domínio a ser importado, por exemplo FABRIKAMSALES |
| Domain.FQDN |Formato FQDN do domínio a ser importado, por exemplo sales.fabrikam.com |
| Domain.LDAP |Formato LDAP do domínio a ser importado, por exemplo, DC = vendas, DC = fabrikam, DC = com |
| Forest.Netbios |O nome da floresta a que está a ser importado, por exemplo FABRIKAMCORP formato NetBIOS |
| Forest.FQDN |O nome da floresta a que está a ser importado, por exemplo, fabrikam.com formato FQDN |
| Forest.LDAP |Formato LDAP, o nome de floresta a que está a ser importado, por exemplo, DC = fabrikam, DC = com |

O sistema fornece o seguinte parâmetro que é utilizado para obter o identificador do conector atualmente em execução:  
`Connector.ID`

Eis um exemplo que preenche o domínio de atributo do metaverso com o nome netbios do domínio onde o utilizador está localizado:  
`domain` <- `%Domain.Netbios%`

### <a name="operators"></a>Operadores
Podem ser utilizados os seguintes operadores:

* **Comparação**: <, < =, <>, =, >, > =
* **Mathematics**: +, -, \*, -
* **Cadeia**: & (concatenate)
* **Lógica**: & & (e) | | (ou)
* **Ordem de avaliação**:)

Os operadores são avaliados à esquerda para a direita e tem a mesma prioridade de avaliação. Ou seja, o \* (multiplicador) não é avaliado antes - (subtração). 2\*(5 + 3) não é igual a 2\*5 + 3. O (Retos) são utilizados para alterar a ordem de avaliação quando à esquerda para a direita de sequência de ordenação não é adequada.

## <a name="multi-valued-attributes"></a>Atributos com múltiplos valores
As funções podem operar em atributos de valor único e com múltiplos valores. Para atributos com múltiplos valores, a função funciona através de cada valor e aplica-se a mesma função a cada valor.

Por exemplo:  
`Trim([proxyAddresses])`Fazer uma limitação de cada valor de atributo /proxyaddress.  
`Word([proxyAddresses],1,"@") & "@contoso.com"`Para cada valor com um @-sign, substitua o domínio com @contoso.com.  
`IIF(InStr([proxyAddresses],"SIP:")=1,NULL,[proxyAddresses])`Procure o endereço SIP e removê-lo de entre os valores.

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre o modelo de configuração [compreender de aprovisionamento declarativo](active-directory-aadconnectsync-understanding-declarative-provisioning.md).
* Consulte como declarativa de aprovisionamento é utilizado out-of-box no [entender a configuração predefinida](active-directory-aadconnectsync-understanding-default-configuration.md).
* Ver como efetuar uma alteração prática utilizando o aprovisionamento declarativo no [como efetuar uma alteração para a configuração predefinida](active-directory-aadconnectsync-change-the-configuration.md).

**Tópicos de descrição geral**

* [Sincronização do Azure AD Connect: Noções e personalizar a sincronização](active-directory-aadconnectsync-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](active-directory-aadconnect.md)

**Tópicos de referência**

* [Sincronização do Azure AD Connect: referência de funções](active-directory-aadconnectsync-functions-reference.md)

