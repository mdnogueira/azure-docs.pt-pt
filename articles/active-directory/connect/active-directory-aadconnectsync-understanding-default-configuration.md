---
title: "Sincronização do Azure AD Connect: entender a configuração predefinida | Microsoft Docs"
description: "Este artigo descreve a configuração predefinida na sincronização do Azure AD Connect."
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: ed876f22-6892-4b9d-acbe-6a2d112f1cd1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: 32a693c059a1b4261f33a3d6f50f397365e9dac4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-connect-sync-understanding-the-default-configuration"></a>Sincronização do Azure AD Connect: entender a configuração predefinida
Este artigo explica as regras de configuração de out-of-box. -Documentos as regras e como estas regras afetam a configuração. É também explica a configuração predefinida de sincronização do Azure AD Connect. O objetivo é que o leitor compreende a forma como o modelo de configuração com o nome de aprovisionamento declarativo, está a funcionar num exemplo do mundo real. Este artigo pressupõe que já tenha instalado e configurar a sincronização do Azure AD Connect utilizando o Assistente de instalação.

Para compreender os detalhes do modelo de configuração, leia [compreender de aprovisionamento declarativo](active-directory-aadconnectsync-understanding-declarative-provisioning.md).

## <a name="out-of-box-rules-from-on-premises-to-azure-ad"></a>Regras de Out-of-box no local ao Azure AD
As expressões seguintes podem ser encontradas na configuração de out-of-box.

### <a name="user-out-of-box-rules"></a>Regras de out-of-box do utilizador
Estas regras também se aplicam ao tipo de objeto iNetOrgPerson.

Um objeto de utilizador deve satisfazer as seguintes sejam sincronizados:

* Tem de ter um sourceAnchor.
* Depois do objeto foi criado no Azure AD, não é possível alterar a sourceAnchor. Se o valor for alterado no local, o objeto para sincronizar até que o sourceAnchor é alterado para o valor anterior.
* Tem de ter o atributo accountEnabled (userAccountControl) preenchido. Com um diretório de Active Directory no local, este atributo está sempre presente e preenchidos.

Os seguintes objetos de utilizador são **não** sincronizados para o Azure AD:

* `IsPresent([isCriticalSystemObject])`. Certifique-se a muitos objetos de out-of-box no Active Directory, tais como a conta de administrador incorporada, não estão sincronizadas.
* `IsPresent([sAMAccountName]) = False`. Certifique-se de que os objetos de utilizador com o atributo não sAMAccountName não estão sincronizados. Neste caso praticamente só iria acontecer num domínio atualizado a partir do NT4.
* `Left([sAMAccountName], 4) = "AAD_"`, `Left([sAMAccountName], 5) = "MSOL_"`. Não sincronize a conta de serviço utilizada pelo sincronização do Azure AD Connect e respetivas versões anteriores.
* Não sincronize contas do Exchange que não funcionarão no Exchange Online.
  * `[sAMAccountName] = "SUPPORT_388945a0"`
  * `Left([mailNickname], 14) = "SystemMailbox{"`
  * `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`
  * `(Left([sAMAccountName], 4) = "CAS_" && (InStr([sAMAccountName], "}")> 0))`
* Não sincronize objetos que não funcionarão no Exchange Online.
  `CBool(IIF(IsPresent([msExchRecipientTypeDetails]),BitAnd([msExchRecipientTypeDetails],&H21C07000) > 0,NULL))`  
  Esta máscara de bits (& H21C07000) seriam filtrar os seguintes objetos:
  * Pasta pública com capacidade de correio
  * Caixa de correio Attendant do sistema
  * Caixa de correio de base de dados caixa de correio (caixa de correio do sistema)
  * Grupo de segurança universal (não se aplicam para um utilizador, mas encontra-se por motivos de legado)
  * Grupo de não-Universal (não se aplicam para um utilizador, mas encontra-se por motivos de legado)
  * Plano de caixa de correio
  * Caixa de correio de deteção
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Não sincronize a quaisquer objetos de vítima de replicação.

As seguintes regras de atributo aplicam-se:

* `sourceAnchor <- IIF([msExchRecipientTypeDetails]=2,NULL,..)`. O atributo sourceAnchor não é contribuíram da caixa de correio ligada. Presume-se que se não foi encontrada uma caixa de correio ligada, a conta real é associado a um mais tarde.
* Exchange relacionadas com atributos só são sincronizados se o atributo **mailNickName** tem um valor.
* Quando existem várias florestas, os atributos são consumidos pela seguinte ordem:
  1. Atributos relacionados com o início de sessão (por exemplo userPrincipalName) são contribuíram da floresta com uma conta ativada.
  2. Os atributos que podem ser encontrados na GAL Exchange (lista de endereços Global) são contribuíram da floresta com uma caixa de correio do Exchange.
  3. Se nenhuma caixa de correio pode ser localizada, em seguida, estes atributos podem ser provenientes qualquer floresta.
  4. Exchange relacionados com os atributos (técnicos atributos não é visíveis na GAL) são contribuíram da floresta onde `mailNickname ISNOTNULL`.
  5. Se existirem várias florestas que satisfazem estas regras, a ordem de criação (data/hora) dos conectores (florestas) é utilizada para determinar qual floresta contribui os atributos.

### <a name="contact-out-of-box-rules"></a>Regras de out-of-box contactos
Um objeto de contacto deve satisfazer as seguintes sejam sincronizados:

* O contacto tem de ser com capacidade de correio. É verificá-lo com as seguintes regras:
  * `IsPresent([proxyAddresses]) = True)`. O atributo proxyAddresses tem de ser preenchido.
  * Um endereço de correio eletrónico principal pode ser encontrado o atributo proxyAddresses ou o atributo de correio. A presença de um @ é utilizado para verificar se o conteúdo é um endereço de e-mail. Estas duas regras tem de ser avaliada como True.
    * `(Contains([proxyAddresses], "SMTP:") > 0) && (InStr(Item([proxyAddresses], Contains([proxyAddresses], "SMTP:")), "@") > 0))`. Existe uma entrada com "SMTP:" e se existir, pode um @ ser encontrado na cadeia de?
    * `(IsPresent([mail]) = True && (InStr([mail], "@") > 0)`. É o atributo de correio preenchido e se for o caso, pode um @ ser encontrado na cadeia de?

Os seguintes objetos de contactos são **não** sincronizados para o Azure AD:

* `IsPresent([isCriticalSystemObject])`. Certifique-se de que não existem objetos de contactos marcados como críticos estão sincronizados. Não deve ser qualquer com uma configuração predefinida.
* `((InStr([displayName], "(MSOL)") > 0) && (CBool([msExchHideFromAddressLists])))`.
* `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`. Estes objetos não funcionam no Exchange Online.
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Não sincronize a quaisquer objetos de vítima de replicação.

### <a name="group-out-of-box-rules"></a>Grupo de regras de out-of-box
Um objeto de grupo deve satisfazer as seguintes sejam sincronizados:

* Tem de ter menos de 50 000 membros. Esta contagem é o número de membros do grupo local.
  * Se tiver mais membros antes de sincronização é iniciado pela primeira vez, o grupo não está sincronizado.
  * Se aumentar o número de membros do quando este foi inicialmente criado, em seguida, quando atinge 50.000 membros deixa de sincronizar até que a contagem de membros é inferior a 50 000 novamente.
  * Nota: A contagem de associação de 50 000 também é imposta pelo Azure AD. Não é possível sincronizar grupos com mais membros do mesmo modificar ou remover esta regra.
* Se o grupo for um **grupo de distribuição**, em seguida, também tem de ser correio ativado. Consulte [contacte regras de out-of-box](#contact-out-of-box-rules) para esta regra é aplicada.

Os seguintes objetos de grupo são **não** sincronizados para o Azure AD:

* `IsPresent([isCriticalSystemObject])`. Certifique-se a muitos objetos de out-of-box no Active Directory, tais como o grupo de Administradores incorporado, não estão sincronizadas.
* `[sAMAccountName] = "MSOL_AD_Sync_RichCoexistence"`. Grupo legado utilizado pelo DirSync.
* `BitAnd([msExchRecipientTypeDetails],&amp;H40000000)`. Grupo de função.
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Não sincronize a quaisquer objetos de vítima de replicação.

### <a name="foreignsecurityprincipal-out-of-box-rules"></a>Regras de out-of-box ForeignSecurityPrincipal
FSPs estão associados a "nenhum" (\*) objetos no metaverso. Na realidade, esta associação só acontece para utilizadores e grupos de segurança. Esta configuração assegura que as associações de múltiplas florestas são resolvidas e representadas corretamente no Azure AD.

### <a name="computer-out-of-box-rules"></a>Regras de out-of-box do computador
Um objeto de computador tem de satisfazer o seguinte para ser sincronizados:

* `userCertificate ISNOTNULL`. Apenas os computadores Windows 10 preencher este atributo. São sincronizados todos os objetos de computador com um valor neste atributo.

## <a name="understanding-the-out-of-box-rules-scenario"></a>Noções sobre o cenário de regras de out-of-box
Neste exemplo, estamos a utilizar uma implementação com uma floresta de contas (A), uma floresta de recursos (R) e um diretório do Azure AD.

![Imagem com a descrição do cenário](./media/active-directory-aadconnectsync-understanding-default-configuration/scenario.png)

Nesta configuração, presume-se que houver uma conta ativada na floresta de conta e uma conta desativada na floresta de recursos com uma caixa de correio ligada.

O nosso objetivo com a configuração predefinida é:

* Atributos relacionados com o início de sessão são sincronizados a partir da floresta com a conta ativada.
* Os atributos que podem ser encontrados da GAL (lista de endereços Global) são sincronizados a partir da floresta com a caixa de correio. Se nenhuma caixa de correio pode ser encontrada, é utilizada qualquer outra floresta.
* Se uma caixa de correio ligada for encontrada, a conta ativada ligada deve estar presente para o objeto seja exportado para o Azure AD.

### <a name="synchronization-rule-editor"></a>Editor de regras de sincronização
A configuração pode ser visualizada e alterada com a ferramenta de Editor de regras de sincronização (SRE) e um atalho para o mesmo pode ser encontrado no menu Iniciar.

![Ícone de Editor de regras de sincronização](./media/active-directory-aadconnectsync-understanding-default-configuration/sre.png)

O SRE é uma ferramenta do kit de recursos e é instalado com a sincronização do Azure AD Connect. Para conseguir iniciar, tem de ser um membro do grupo ADSyncAdmins. Quando é iniciado, verá algo semelhante ao seguinte:

![As regras de sincronização de entrada](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulesinbound.png)

Neste painel, pode ver todas as regras de sincronização criado para a sua configuração. Cada linha na tabela é uma regra de sincronização. Para a esquerda em tipos de regras, são apresentados dois tipos diferentes: entrada e saída. Entrada e saída é da vista do metaverso. Principalmente vai concentrar-se nas regras de entrada nesta descrição geral. A lista de regras de sincronização real depende o esquema detetado no AD. Na imagem acima, a floresta de contas (fabrikamonline.com) não tem quaisquer serviços, como o Exchange e o Lync, e não existem regras de sincronização foram criadas para estes serviços. No entanto, na floresta de recursos (res.fabrikamonline.com) localize as regras de sincronização para estes serviços. O conteúdo das regras é diferente consoante a versão detetada. Por exemplo, uma implementação com o Exchange 2013 existem mais fluxos de atributos configurados que no Exchange 2010/2007.

### <a name="synchronization-rule"></a>Regra de sincronização
Uma regra de sincronização é um objeto de configuração com um conjunto de atributos que circulam quando uma condição é satisfeita. Também é utilizado para descrever como um objeto num espaço de conector está relacionado com um objeto no metaverso, conhecido como **associação** ou **corresponder**. As regras de sincronização ter um valor de prioridade que indica como se relacionam entre si. Uma regra de sincronização com um valor numérico inferior tem uma precedência superior e num conflito de fluxo de atributos, precedência superior wins a resolução de conflito.

Por exemplo, observe a regra de sincronização **do AD – utilizador AccountEnabled**. Marcar esta linha no SRE e selecione **editar**.

Uma vez que esta regra é uma regra de out-of-box, receberá um aviso quando abrem a regra. Não deve efetuar qualquer [alterações às regras de out-of-box](active-directory-aadconnectsync-best-practices-changing-default-configuration.md), por isso, é-lhe perguntado quais são as intenções. Neste caso, apenas pretende ver a regra. Selecione **não**.

![Sincronização de regras de aviso](./media/active-directory-aadconnectsync-understanding-default-configuration/warningeditrule.png)

Uma regra de sincronização tem quatro secções de configuração: descrição, controlo de âmbito do filtro, regras de associação e transformações.

#### <a name="description"></a>Descrição
A primeira secção fornece informações básicas, tais como um nome e descrição.

![Separador de descrição do editor de regras de sincronização ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncruledescription.png)

Encontrará também informações sobre o sistema ligado esta regra está relacionado com a, que tipo de sistema ligado aplica-se e o tipo de objeto de metaverso de objeto. O tipo de objeto de metaverso é sempre, independentemente da pessoa quando o tipo de objeto de origem é um utilizador, iNetOrgPerson ou contacte. O tipo de objeto de metaverso nunca deve ser alterado pelo que é criado como um tipo genérico. O tipo de ligação pode ser definido para associação, StickyJoin ou o aprovisionar. Esta definição funciona em conjunto com a secção de regras de associação e é explicada mais tarde.

Também pode ver que esta regra de sincronização é utilizada para sincronização de palavra-passe. Se um utilizador estiver no âmbito para esta regra de sincronização, a palavra-passe está sincronizada no local à nuvem (partindo do princípio de que ativou a funcionalidade de sincronização de palavra-passe).

#### <a name="scoping-filter"></a>Filtro de âmbito
A secção de controlo de âmbito do filtro é utilizada para configurar quando deve ser aplicada uma regra de sincronização. Uma vez que o nome da regra de sincronização está a visualizar indica só devem ser aplicada para utilizadores ativados, o âmbito é configurado, por isso, o atributo de AD **userAccountControl** não pode ter o bit 2 definido. Quando o motor de sincronização localiza um utilizador no AD, aplica-se esta sincronização regra quando **userAccountControl** está definido como o valor decimal 512 (utilizador normal ativado). Não é aplicável a regra quando o utilizador tem **userAccountControl** definido como 514 (utilizador normal desativado).

![Separador âmbito no editor de regras de sincronização ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulescopingfilter.png)

O filtro de âmbito tem grupos e cláusulas que podem ser aninhadas. Devem ser satisfeitas todas as cláusulas dentro de um grupo para aplicar uma regra de sincronização. Quando vários grupos são definidos, pelo menos um grupo deve ser satisfeito para aplicar a regra. Ou seja, ou é avaliada entre grupos e uma lógica e é avaliada dentro de um grupo. Um exemplo desta configuração pode ser encontrado na regra de sincronização de saída **Out para o AAD – aderir a grupo**. Existem vários grupos de filtro de sincronização, por exemplo, um para grupos de segurança (`securityEnabled EQUAL True`) e outra para grupos de distribuição (`securityEnabled EQUAL False`).

![Separador âmbito no editor de regras de sincronização ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulescopingfilterout.png)

Esta regra é utilizada para definir os grupos que devem ser aprovisionados com o Azure AD. Grupos de distribuição tem de ser correio ativado sejam sincronizados com o Azure AD, mas para os grupos de segurança de uma mensagem de e-mail não é necessária.

#### <a name="join-rules"></a>Regras de associação
A secção terceira é utilizada para configurar a inter-relação entre objetos no espaço de conector para objetos no metaverso. A regra que tem em conta anteriormente não tem qualquer configuração para regras de associação, por isso, em vez disso, se pretender ver **do AD – associar utilizador**.

![Associar o separador regras no editor de regras de sincronização ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulejoinrules.png)

O conteúdo da regra de associação depende correspondente opção selecionada no Assistente de instalação. Para uma regra de entrada, a avaliação começa com um objeto no espaço de conector de origem e cada grupo de regras de associação é avaliado em sequência. Se um objeto de origem é avaliado para corresponder exatamente um objeto no metaverso utilizando uma das regras de associação, os objetos associados. Se todas as regras foram avaliadas e houver nenhuma correspondência, o tipo de ligação na página de descrição é utilizado. Se esta configuração é definida como **aprovisionar**, em seguida, é criado um novo objeto no destino, o metaverso. Para aprovisionar um novo objeto de metaverso é também conhecido como **projeto** um objeto para o metaverso.

As regras de associação só são avaliadas uma vez. Quando estão associados um objeto de espaço de conector e um objeto de metaverso, permanecem associados, desde que o âmbito da regra de sincronização está ainda satisfeito.

Quando avaliar as regras de sincronização, tem de ser apenas uma regra de sincronização com regras de associação definidas no âmbito. Se forem encontradas várias regras de sincronização com regras de associação para um objeto, será emitido um erro. Por este motivo, a melhor prática é ter apenas uma regra de sincronização com associação definida quando várias regras de sincronização estão no âmbito de um objeto. Na configuração de out-of-box para sincronização do Azure AD Connect, estas regras podem ser encontrada ao procurar o nome e localizar as que o word **associar** no fim do nome. Uma regra de sincronização sem quaisquer regras de associação definidas aplica-se os fluxos de atributos quando outra regra de sincronização Unidas os objetos ou o aprovisionar um novo objeto de destino.

Se observar a imagem acima, pode ver que a regra está a tentar associar **Sidobjeto** com **msExchMasterAccountSid** (Exchange) e **msRTCSIP-OriginatorSid** (Lync), que é o esperado numa topologia de floresta de recursos de conta. Localizar a regra mesma em todas as florestas. Pressuposto é que cada floresta pode ser uma conta ou o recurso de floresta. Esta configuração também funciona se tiver contas que residem numa única floresta e não têm de ser associado.

#### <a name="transformations"></a>Transformações
A secção de transformação define todos os fluxos de atributos que se aplicam ao objeto de destino quando os objetos associados e o filtro de âmbito é satisfeito. Retroceder para o **do AD – utilizador AccountEnabled** regra de sincronização, encontrará as transformações seguintes:

![Transformações de separador editor de regras de sincronização ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncruletransformations.png)

Colocar esta configuração no contexto, uma implementação de floresta de recursos de conta, espera-se para localizar uma conta ativada na floresta de conta e uma conta desativada na floresta de recursos com as definições do Exchange e o Lync. A regra de sincronização está a visualizar contém os atributos necessários para início de sessão e estes atributos devem fluxo da floresta onde existe uma conta ativada. Todos os fluxos de atributos são colocados em conjunto, uma regra de sincronização.

Uma transformação pode ter tipos diferentes: constante, direta e expressão.

* Um fluxo constante flui sempre um valor de codificado. No caso, sempre define o valor **verdadeiro** no atributo metaverso denominado **accountEnabled**.
* O valor do atributo de origem para o atributo de destino como flui sempre de um fluxo direto-é.
* O tipo de fluxo terceiro é expressão e permite configurações mais avançadas.

É o idioma de expressão VBA (Visual Basic para aplicações), por isso, as pessoas com a experiência do Microsoft Office ou VBScript irá reconhece o formato. Os atributos estão entre parêntesis Retos, [attributeName]. Nomes de função e os nomes de atributos são sensíveis a maiúsculas, mas o Editor de regras de sincronização avalia as expressões e forneça um aviso se a expressão não é válida. Todas as expressões são expressas numa linha única com funções aninhadas. Para mostrar a potência do idioma de configuração, eis o fluxo de pwdLastSet, mas com comentários adicionais inseridos:

```
// If-then-else
IIF(
// (The evaluation for IIF) Is the attribute pwdLastSet present in AD?
IsPresent([pwdLastSet]),
// (The True part of IIF) If it is, then from right to left, convert the AD time format to a .Net datetime, change it to the time format used by Azure AD, and finally convert it to a string.
CStr(FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")),
// (The False part of IIF) Nothing to contribute
NULL
)
```

Consulte [expressões compreender de aprovisionamento declarativo](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) para obter mais informações sobre o idioma de expressão para fluxos de atributos.

### <a name="precedence"></a>Precedência
Agora tem consultou algumas regras de sincronização individuais, mas as regras funcionam em conjunto na configuração. Em alguns casos, um valor de atributo é contribuíram a partir de várias regras de sincronização para o mesmo atributo de destino. Neste caso, precedência de atributo é utilizada para determinar qual atributo wins. Por exemplo, observe o atributo sourceAnchor. Este atributo é um atributo importante para conseguir iniciar sessão Azure AD. Pode encontrar um fluxo de atributos para este atributo na duas regras de sincronização diferentes, **do AD – utilizador AccountEnabled** e **do AD – utilizador comuns**. Devido a precedência da regra de sincronização, o atributo sourceAnchor é contribuíram da floresta com uma conta ativada primeiro quando existem vários objetos associados ao objeto de metaverso. Se não existem não existem contas ativadas, então, o motor de sincronização utiliza a regra de sincronização de catch-all **do AD – utilizador comuns**. Esta configuração assegura que, mesmo para as contas que estão desativadas, há ainda um sourceAnchor.

![As regras de sincronização de entrada](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulesinbound.png)

A precedência para as regras de sincronização está definida em grupos pelo Assistente de instalação. Todas as regras num grupo de ter o mesmo nome, mas estão ligados a diferentes diretórios ligados. O Assistente de instalação fornece a regra **do AD – associar utilizador** mais alta precedência e itera ligados de ativação pós-falha de todos os diretórios AD. Em seguida, prossegue com os grupos de regras por uma ordem predefinida seguintes. Dentro de um grupo, as regras são adicionadas pela ordem que foram adicionados os conectores no assistente. Se outro conector for adicionado através do assistente, as regras de sincronização são reordenadas e regras do novo conector são inseridas pela última vez em cada grupo.

### <a name="putting-it-all-together"></a>Passar todos os em conjunto
Sabemos agora suficiente sobre regras de sincronização para ser capaz de compreender como a configuração funciona com as regras de sincronização diferentes. Se observar os atributos que são contribuídos para o metaverso e um utilizador, as regras são aplicadas pela seguinte ordem:

| Nome | Comentário |
|:--- |:--- |
| Do AD – associação do utilizador |Regra para efetuar a adesão de objetos de espaço de conector com o metaverso. |
| Do AD – UserAccount ativada |Os atributos necessários para início de sessão para o Azure AD e o Office 365. Queremos estes atributos da conta ativada. |
| Do AD – comuns de utilizador do Exchange |Atributos encontrados na lista de endereços Global. Partimos do princípio que de qualidade de dados é melhor na floresta onde tem encontrámos caixa de correio do utilizador. |
| Do AD – comum do utilizador |Atributos encontrados na lista de endereços Global. No caso de não foi possível localizar uma caixa de correio, qualquer outro objeto associado ao pode contribuir o valor do atributo. |
| Do AD – Exchange do utilizador |Existe apenas se foi detetado o Exchange. Fluxos de todos os atributos do Exchange de infraestrutura. |
| Do AD – utilizador Lync |Existe apenas se foi detetado o Lync. Fluxos de todos os atributos de Lync de infraestrutura. |

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre o modelo de configuração [compreender de aprovisionamento declarativo](active-directory-aadconnectsync-understanding-declarative-provisioning.md).
* Saiba mais sobre o idioma de expressão no [expressões compreender de aprovisionamento declarativo](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md).
* Continuar a ler a forma como a configuração de out-of-box funciona em [entender utilizadores e contactos](active-directory-aadconnectsync-understanding-users-and-contacts.md)
* Ver como efetuar uma alteração prática utilizando o aprovisionamento declarativo no [como efetuar uma alteração para a configuração predefinida](active-directory-aadconnectsync-change-the-configuration.md).

**Tópicos de descrição geral**

* [Sincronização do Azure AD Connect: Noções e personalizar a sincronização](active-directory-aadconnectsync-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](active-directory-aadconnect.md)

