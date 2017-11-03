---
title: "Azure AD Connect: Noções sobre aprovisionamento declarativo | Microsoft Docs"
description: "Explica o modelo de configuração aprovisionamento declarativo no Azure AD Connect."
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: cfbb870d-be7d-47b3-ba01-9e78121f0067
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: 7497ec2ca658c3790227c56ef1755d9a1cb74e0a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-connect-sync-understanding-declarative-provisioning"></a>Sincronização do Azure AD Connect: Noções sobre o aprovisionamento declarativo
Este tópico explica o modelo de configuração no Azure AD Connect. O modelo é denominado aprovisionamento declarativo e permite-lhe efetuar uma alteração com facilidade de configuração. Inúmeros aspetos descritos neste tópico estão avançados e não é necessário para a maioria dos cenários de cliente.

## <a name="overview"></a>Descrição geral
Aprovisionamento declarativo está a processar objetos feitos um diretório de ligação de origem e determina como o objeto e os atributos devem ser transformados de uma origem para um destino. Um objeto é processado num pipeline sincronização e o pipeline é o mesmo para regras de entrada e saídas. Uma regra de entrada é de um espaço de conector para o metaverso e uma regra de saída é de metaverso um espaço de conector.

![Pipeline de sincronização](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/sync1.png)  

O pipeline tem vários módulos diferentes. Cada um deles é responsável por um conceito na sincronização de objetos.

![Pipeline de sincronização](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/pipeline.png)  

* Origem, o objeto de origem
* [Âmbito](#scope), localiza todas as regras de sincronização que se encontrem no âmbito
* [Associar](#join), determina a relação entre o metaverso e espaço de conector
* [Transformar](#transform), Calculates como atributos devem ser transformados e fluxo
* [Precedência](#precedence), resolve em conflito contribuições de atributo
* Destino, o objeto de destino

## <a name="scope"></a>Âmbito
O módulo de âmbito está a avaliar um objeto e determina as regras que estejam no âmbito e devem ser incluídas no processamento. Consoante os valores de atributos do objeto, regras de sincronização diferentes são avaliadas para estar no âmbito. Por exemplo, um utilizador desativado com nenhuma caixa de correio do Exchange tem regras diferentes do que um utilizador ativado com uma caixa de correio.  
![Âmbito](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/scope1.png)  

O âmbito é definido como grupos e cláusulas. As cláusulas estão dentro de um grupo. É utilizado um e lógica entre todas as cláusulas num grupo. Por exemplo, (departamento de TI e país de = = Dinamarca). É utilizado ou entre grupos.

![Âmbito](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/scope2.png)  
O âmbito nesta imagem deve ser lido como (departamento de TI e país de = = Dinamarca) ou (país = Suécia). Se o grupo de 1 ou 2 for avaliado como true, a regra se encontra no âmbito.

O módulo de âmbito suporta as seguintes operações.

| Operação | Descrição |
| --- | --- |
| IGUAL, NOTEQUAL |Comparar uma cadeia que avalia se o valor for igual ao valor no atributo. Para atributos com múltiplos valores, consulte ISIN e ISNOTIN. |
| LESSTHAN, LESSTHAN_OR_EQUAL |Comparar uma cadeia que avalia se o valor for less than do valor do atributo. |
| CONTÉM, NOTCONTAINS |Comparar uma cadeia que avalia se o valor pode ser encontrado algures no valor no atributo. |
| STARTSWITH, NOTSTARTSWITH |Comparar uma cadeia que avalia se o valor está no início do valor do atributo. |
| ENDSWITH, NOTENDSWITH |Comparar uma cadeia que avalia se o valor está no final do valor do atributo. |
| GREATERTHAN, GREATERTHAN_OR_EQUAL |Comparar uma cadeia que avalia se o valor é superior do valor do atributo. |
| ISNULL, ISNOTNULL |Avalia se o atributo está ausente do objeto. O atributo não está presente e, por conseguinte, null, em seguida, a regra estiver no âmbito. |
| ISIN, ISNOTIN |Avalia se o valor estiver presente no atributo definido. Esta operação é a variação de igual e NOTEQUAL com múltiplos valor. O atributo deve para ser um atributo com múltiplos valor e se o valor pode ser encontrado em nenhum dos valores de atributo, em seguida, a regra se encontra no âmbito. |
| ISBITSET, ISNOTBITSET |Avalia se um determinado bit estiver definido. Por exemplo, pode ser utilizado para avaliar o bits na userAccountControl para ver se um utilizador está ativado ou desativado. |
| ISMEMBEROF, ISNOTMEMBEROF |O valor deve conter um DN a um grupo no espaço de conector. Se o objeto é um membro do grupo especificado, a regra está no âmbito. |

## <a name="join"></a>Associar
O módulo de associação no pipeline de sincronização é responsável por localizar a relação entre o objeto de origem e de um objeto no destino. Uma regra de entrada, esta relação seria um objeto num espaço de conector localizar uma relação a um objeto no metaverso.  
![Associar entre cs e mv](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/join1.png)  
O objetivo é para ver se houver um objeto já no metaverso, criado por outro conector, que deve ser associado. Por exemplo, uma floresta de recursos de conta de utilizador da floresta de contas deve ser associado com o utilizador da floresta de recursos.

Associações são utilizadas principalmente nas regras de entrada para associar os objetos de espaço de conector em conjunto para o mesmo objeto de metaverso.

As associações são definidas como um ou mais grupos. Dentro de um grupo, terá de cláusulas. É utilizado um e lógica entre todas as cláusulas num grupo. É utilizado ou entre grupos. Os grupos são processados ordem da parte superior da parte inferior. Quando um grupo tiver exatamente uma correspondência com um objeto no destino, não existem outras regras de associação são avaliadas. Se zero ou mais do que é possível localizar um objeto, continua o processamento ao seguinte grupo de regras. Por este motivo, as regras devem ser criado pela ordem dos mais explícito primeiro e mais difusa no final.  
![Definição de associação](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/join2.png)  
As associações nesta imagem são processadas da parte superior para parte inferior. Primeiro, o pipeline de sincronização verá se houver uma correspondência no campo IDdeEmpregado. Caso contrário, a segunda regra vê se o nome da conta pode ser utilizado para associar os objetos em conjunto. Se isto não for uma correspondência ou, a regra de terceira e final é uma correspondência mais difusa utilizando o nome do utilizador.

Se todas as regras de associação foram avaliadas e existir não exatamente uma correspondência, o **tipo de ligação** no **Descrição** página é utilizada. Se esta opção estiver definida como **aprovisionar**, em seguida, é criado um novo objeto no destino.  
![Aprovisionar ou uma associação](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/join3.png)  

Um objeto deve ter apenas uma regra de sincronização único com regras de associação no âmbito. Se existirem várias regras de Sincronização quais está definida a associação, ocorre um erro. Precedência não é utilizada para resolver conflitos de associação. Um objeto tem de ter uma regra de associação no âmbito de atributos para o fluxo com a mesma direção de entrada/saída. Se precisar de atributos de fluxo de entrada e saída para o mesmo objeto, tem de ter uma entrada e uma regra de sincronização de saída com associação.

Associação de saída tem um comportamento especial quando tentar aprovisionar um objeto para um espaço de conector de destino. O atributo de DN é utilizado para experimentar primeiro uma associação inversa. Se já existir um objeto no espaço de conector de destino com o mesmo DN, os objetos associados.

O módulo de associação é avaliado apenas uma vez quando uma nova regra de sincronização é fornecido no âmbito. Quando tiver associado um objeto, não é disjoining, mesmo se os critérios de associação já não é satisfeita. Se pretender disjoin um objeto, a regra de sincronização que os objetos associados a um fique fora do âmbito.

### <a name="metaverse-delete"></a>Eliminação de Metaverso
Um objeto de metaverso permanece desde que não há uma regra de sincronização no âmbito com **tipo de ligação** definido como **aprovisionar** ou **StickyJoin**. Um StickyJoin é utilizado quando um conector não é permitido para aprovisionar um novo objeto de metaverso, mas quando foi associado, tem de ser eliminado na origem antes do objeto de metaverso é eliminado.

Quando é eliminado um objeto de metaverso, todos os objetos associados uma regra de sincronização de saída marcado para **aprovisionar** estão marcados para um eliminar.

## <a name="transformations"></a>Transformações
As transformações são utilizadas para definir como atributos devem fluxo da origem de destino. Os fluxos podem ter um dos seguintes **fluxo tipos**: direta, constante ou expressão. Um valor de atributo como flui de um fluxo direto,-é com nenhuma transformações adicionais. Um valor constante define o valor especificado. Uma expressão utiliza o idioma de expressão de aprovisionamento declarativo para expressar a forma como deve ser a transformação. Os detalhes para o idioma de expressão podem ser encontrados no [compreender o idioma de expressão de aprovisionamento declarativo](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) tópico.

![Aprovisionar ou uma associação](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/transformations1.png)  

O **aplicar uma vez** caixa de verificação define que o atributo só deve ser definido quando o objecto for criado inicialmente. Por exemplo, esta configuração pode ser utilizada para definir uma palavra-passe inicial para um novo objeto de utilizador.

### <a name="merging-attribute-values"></a>A intercalação de valores de atributo
Os fluxos de atributos não há uma definição para determinar se os atributos com múltiplos valores devem ser intercalados de vários conectores diferentes. O valor predefinido é **atualização**, que indica que a regra de sincronização com precedência mais elevada deve win.

![Tipos de intercalação](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/mergetype.png)  

Há também **intercalar** e **MergeCaseInsensitive**. Estas opções permitem-lhe os valores de diferentes origens de intercalação. Por exemplo, pode ser utilizado para intercalar o atributo de membro ou proxyAddresses de várias florestas diferentes. Quando utilizar esta opção, todas as regras de sincronização no âmbito de um objeto tem de utilizar o mesmo tipo de intercalação. Não é possível definir **atualização** de um conector e **intercalar** de outro. Se tentar, receberá um erro.

A diferença entre **intercalar** e **MergeCaseInsensitive** é como processar os valores de atributo duplicado. O motor de sincronização certifica-se de valores duplicados não são inseridos o atributo de destino. Com **MergeCaseInsensitive**, valores com apenas uma diferença de duplicados, no caso de não vai estar presente. Por exemplo, não verá ambos "SMTP:bob@contoso.com"e"smtp:bob@contoso.com" no atributo de destino. **Intercalar** é apenas observar os valores exatos e vários valores onde existe apenas uma diferença nos casos poderão estar presente.

A opção **substituir** é o mesmo que **atualização**, mas não está a ser utilizada.

### <a name="control-the-attribute-flow-process"></a>Controlar o processo de fluxo de atributos
Quando várias regras de sincronização de entrada estão configuradas para contribuir para o mesmo atributo de metaverso, precedência é utilizada para determinar o winner. A regra de sincronização com precedência mais elevada (menor valor numérico) vai para contribuir com o valor. O mesmo acontece para regras de saída. A sincronização com o wins mais elevados de precedência da regra e contribuir o valor para o diretório ligado.

Em alguns casos, em vez de contribuir um valor, a regra de sincronização deve determinar a forma como devem comportar-se outras regras. Existem algumas especial os literais que são utilizados para este cenário.

Para regras de sincronização de entrada, o literal **nulo** pode ser utilizado para indicar que o fluxo não tem um valor para contribuir. Outra regra com precedência inferior pode contribuir um valor. Se nenhuma regra contribuíram um valor, o atributo do metaverso é removido. Para uma regra de saída, se **nulo** é o valor final depois de todas as regras de sincronização tem sido processadas, em seguida, o valor é removido no diretório ligado.

O literal **AuthoritativeNull** é semelhante à **nulo** , mas com a diferença que não existem regras de precedência inferiores podem contribuir um valor.

Também pode utilizar um fluxo de atributos **IgnoreThisFlow**. É semelhante a valor nulo na medida em que indica que não há nada a contribuir. A diferença é que não remove um valor já existente no destino. É como o fluxo de atributos nunca foi não existe.

Segue-se um exemplo:

No *Out AD - híbrida do Exchange do utilizador* pode encontrar o fluxo seguinte:  
`IIF([cloudSOAExchMailbox] = True,[cloudMSExchSafeSendersHash],IgnoreThisFlow)`  
Esta expressão deve ser lido como: se a caixa de correio do utilizador estiver localizada no Azure AD, em seguida, fluxo de atributos do Azure AD para AD. Caso contrário, não fluxo nada para o Active Directory. Neste caso, este seria manter o valor existente no AD.

### <a name="importedvalue"></a>ImportedValue
A função ImportedValue é diferente de todas as outras funções, uma vez que o nome de atributo tem de ser colocado entre aspas, em vez de parênteses Retos:  
`ImportedValue("proxyAddresses")`.

Normalmente durante a sincronização um atributo utiliza o valor esperado, mesmo se ainda não foram exportado ainda ou foi recebido um erro durante a exportação ("parte superior do Torre"). Uma sincronização de entrada parte do princípio de que um atributo que ainda não chegaram, eventualmente, um diretório ligado atinja o. Em alguns casos, é importante sincronizar apenas um valor que foi confirmado pelo diretório ligado ("holograma e diferenças importar Torre").

Um exemplo desta função pode ser encontrado na regra de sincronização de out-of-box *do AD – utilizador comuns do Exchange*. No Exchange híbrida, o valor adicionado pelo Exchange online apenas deve ser sincronizado quando confirmado de que o valor foi exportado com êxito:  
`proxyAddresses` <- `RemoveDuplicates(Trim(ImportedValue("proxyAddresses")))`

## <a name="precedence"></a>Precedência
Quando tentarem várias regras de sincronização contribuir com o mesmo valor de atributo para o destino, o valor de prioridade é utilizado para determinar o winner. A regra com precedência mais elevada, mais baixa valor numérico, vai para contribuir com o atributo num conflito.

![Tipos de intercalação](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/precedence1.png)  

Esta ordem pode ser utilizado para definir o modo mais precisos fluxos de atributos para um pequeno subconjunto de objetos. Por exemplo, fora-de-caixa-regras Certifique-se de que atributos a partir de uma conta ativada (**utilizador AccountEnabled**) têm precedência de outras contas.

Precedência pode ser definida entre os conectores. Que permite que os conectores com dados melhor para contribuir com valores pela primeira vez.

### <a name="multiple-objects-from-the-same-connector-space"></a>Vários objetos do mesmo espaço de conector
Se tiver vários objetos no mesmo espaço de conector associado ao mesmo objeto de metaverso, tem de ser ajustada precedência. Se vários objetos no âmbito da mesma regra de sincronização, o motor de sincronização não é capaz de determinar precedência. É ambígua o objeto de origem deve contribuir o valor para o metaverso. Esta configuração é comunicada como ambígua, mesmo se os atributos da origem de terem o mesmo valor.  
![Vários objetos associados ao mesmo objeto de mv](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/multiple1.png)  

Para este cenário, terá de alterar o âmbito das regras de sincronização para que os objetos de origem tem regras de sincronização diferentes no âmbito. Que permite-lhe definir a precedência diferentes.  
![Vários objetos associados ao mesmo objeto de mv](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/multiple2.png)  

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre o idioma de expressão no [expressões compreender de aprovisionamento declarativo](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md).
* Consulte como declarativa de aprovisionamento é utilizado out-of-box no [entender a configuração predefinida](active-directory-aadconnectsync-understanding-default-configuration.md).
* Ver como efetuar uma alteração prática utilizando o aprovisionamento declarativo no [como efetuar uma alteração para a configuração predefinida](active-directory-aadconnectsync-change-the-configuration.md).
* Continuar a ler a forma como os utilizadores e contactos trabalham em conjunto [entender utilizadores e contactos](active-directory-aadconnectsync-understanding-users-and-contacts.md).

**Tópicos de descrição geral**

* [Sincronização do Azure AD Connect: Noções e personalizar a sincronização](active-directory-aadconnectsync-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](active-directory-aadconnect.md)

**Tópicos de referência**

* [Sincronização do Azure AD Connect: referência de funções](active-directory-aadconnectsync-functions-reference.md)
