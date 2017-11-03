---
title: "O Azure AD Connect: Utilizador início de sessão | Microsoft Docs"
description: "Azure AD Connect-sessão do utilizador para definições personalizadas."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: curtand
ms.assetid: 547b118e-7282-4c7f-be87-c035561001df
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: billmath
ms.openlocfilehash: 1d580ae43925bfb2cbe0fd9461cfb7e207fa56ec
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-connect-user-sign-in-options"></a>Opções do Azure AD Connect utilizador iniciar sessão
Ligar do Azure Active Directory (Azure AD) permite aos utilizadores iniciar sessão recursos de nuvem e no local utilizando as mesmas palavras-passe. Este artigo descreve conceitos chave para cada modelo de identidade para o ajudar a escolher a identidade que pretende utilizar para iniciar sessão com o Azure AD.

Se já estiver familiarizado com o modelo de identidade do Azure AD e pretender obter mais informações sobre um método específico, consulte a hiperligação adequada:

* [Sincronização de hash de palavra-passe](#password-synchronization) com [totalmente integrada-início de sessão único (SSO)](active-directory-aadconnect-sso.md)
* [A autenticação pass-through](active-directory-aadconnect-pass-through-authentication.md) com [totalmente integrada-início de sessão único (SSO)](active-directory-aadconnect-sso.md)
* [SSO Federado (com serviços de Federação do Active Directory (AD FS))](#federation-that-uses-a-new-or-existing-farm-with-ad-fs-in-windows-server-2012-r2)

## <a name="choosing-the-user-sign-in-method-for-your-organization"></a>Escolher o método de início de sessão do utilizador para a sua organização
Na maioria das organizações que apenas pretende ativar o utilizador iniciar sessão no Office 365, aplicações SaaS e outros recursos do Azure baseada no AD, recomendamos que a opção de sincronização de hash de palavra-passe predefinida. Algumas organizações, no entanto, tem um motivo específico que não estão capazes de utilizar esta opção. Que possam escolher qualquer uma federado início de sessão opção, tais como o AD FS ou a autenticação pass-through. Pode utilizar a seguinte tabela para o ajudar a tornar a escolha certa.

Preciso de | PHS com o SSO| PTA com o SSO| AD FS |
 --- | --- | --- | --- |
Sincronizar automaticamente o novo utilizador, contacte e contas de grupo no Active Directory no local para a nuvem.|x|x|x|
Configure os meus inquilinos para cenários de híbridos do Office 365.|x|x|x|
Ative os meus utilizadores iniciar sessão e aceder aos serviços de nuvem utilizando a palavra-passe no local.|x|x|x|
Implemente o início de sessão único utilizando as credenciais da empresa.|x|x|x|
Certifique-se de que nenhuma palavra-passe é armazenados na nuvem.||x *|x|
Ative soluções de autenticação multifator no local.|||x|

* Através de um agente simples.

### <a name="password-hash-synchronization"></a>Sincronização de hash de palavra-passe
Com a sincronização de hash de palavra-passe, hashes de palavras-passe do utilizador são sincronizados a partir do Active Directory no local ao Azure AD. Quando as palavras-passe são alteradas ou repor no local, os hashes de palavra-passe nova são sincronizados com o Azure AD imediatamente, para que os utilizadores podem utilizar sempre a mesma palavra-passe para recursos de nuvem e de recursos no local. As palavras-passe nunca são enviadas para o Azure AD ou armazenadas no Azure AD em texto não encriptado. Pode utilizar a sincronização de hash de palavra-passe, juntamente com a repetição de escrita de palavras-passe para permitir self-service reposição palavra-passe no Azure AD.

Além disso, pode ativar [SSO totalmente integrado](active-directory-aadconnect-sso.md) para os utilizadores em computadores associados a um domínio que estão na rede empresarial. Com início de sessão, os utilizadores ativados só tem de introduzir um nome de utilizador para ajudá-los em segurança aceder a recursos de nuvem.

![Sincronização de hash de palavra-passe](./media/active-directory-aadconnect-user-signin/passwordhash.png)

Para obter mais informações, consulte o [sincronização de hash de palavra-passe](active-directory-aadconnectsync-implement-password-synchronization.md) artigo.

### <a name="pass-through-authentication"></a>Autenticação pass-through
Com a autenticação pass-through, palavra-passe do utilizador é validado com o controlador de Active Directory no local. A palavra-passe não tem de estar presente no Azure AD em qualquer outra forma. Isto permite às políticas no local, tais como restrições de hora de início de sessão, a ser avaliada durante a autenticação na nuvem de serviços.

A autenticação pass-through utiliza um agente simple num computador associado a um domínio Windows Server 2012 R2 no ambiente no local. Este agente escuta pedidos de validação da palavra-passe. Não requer nenhuma porta de entrada para ser aberto à Internet.

Além disso, também pode ativar início de sessão para os utilizadores em computadores associados a um domínio que estão na rede empresarial. Com início de sessão, os utilizadores ativados só tem de introduzir um nome de utilizador para ajudá-los em segurança aceder a recursos de nuvem.
![Autenticação pass-through](./media/active-directory-aadconnect-user-signin/pta.png)

Para obter mais informações, consulte:
- [Autenticação pass-through](active-directory-aadconnect-pass-through-authentication.md)
- [Início de sessão único](active-directory-aadconnect-sso.md)

### <a name="federation-that-uses-a-new-or-existing-farm-with-ad-fs-in-windows-server-2012-r2"></a>Federação que utiliza um farm de novo ou existente com o AD FS no Windows Server 2012 R2
Com o início de sessão federado, os seus utilizadores podem iniciar sessão serviços do Azure baseada no AD com as respetivas palavras-passe no local. Enquanto estiverem na rede empresarial, mesmo não tem de introduzir as palavras-passe. Ao utilizar a opção de federação com o AD FS, pode implementar um farm de novo ou existente com o AD FS no Windows Server 2012 R2. Se optar por especificar um farm existente, o Azure AD Connect configura a confiança entre o seu farm e o Azure AD para que os utilizadores podem iniciar sessão.

<center>![Federação com o AD FS no Windows Server 2012 R2](./media/active-directory-aadconnect-user-signin/federatedsignin.png)</center>

#### <a name="deploy-federation-with-ad-fs-in-windows-server-2012-r2"></a>Implementar a Federação com o AD FS no Windows Server 2012 R2

Se estiver a implementar um novo farm, tem de:

* Um servidor do Windows Server 2012 R2 para o servidor de Federação.
* Um servidor do Windows Server 2012 R2 para o Proxy de aplicações Web.
* Um ficheiro. pfx com um certificado SSL para o nome do serviço de Federação pretendido. Por exemplo: fs.contoso.com.

Se estiver a implementar um novo farm ou utilizar um farm existente, precisa de:

* Credenciais de administrador local nos servidores de Federação.
* Credenciais de administrador local em todos os servidores de grupo de trabalho (não associados a um domínio) que pretende implementar a função de Proxy de aplicações Web no.
* O computador que execute o assistente em para conseguirem estabelecer ligação a quaisquer outras máquinas que pretende instalar o AD FS ou o Proxy de aplicações Web em utilizando a gestão remota do Windows.

Para obter mais informações, consulte [configurar o SSO com o AD FS](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs).

#### <a name="sign-in-by-using-an-earlier-version-of-ad-fs-or-a-third-party-solution"></a>Inicie sessão com uma versão anterior do AD FS ou uma solução de terceiros
Se já tiver configurado nuvem início de sessão utilizando uma versão anterior do AD FS (por exemplo, o AD FS 2.0) ou um fornecedor de Federação de terceiros, pode optar por ignorar a configuração de sessão do utilizador através do Azure AD Connect. Isto permitirá ao obter a sincronização mais recente e outras funcionalidades do Azure AD Connect ao ainda utilizar a solução existente para início de sessão.

Para obter mais informações, consulte o [lista de compatibilidades de Federação de terceiros do Azure AD](active-directory-aadconnect-federation-compatibility.md).


## <a name="user-sign-in-and-user-principal-name"></a>Sessão do utilizador e nome principal de utilizador
### <a name="understanding-user-principal-name"></a>Nome principal de utilizador de compreender
No Active Directory, o sufixo de nome principal (UPN) de utilizador predefinido é o nome DNS do domínio onde a conta de utilizador foi criada. Na maioria dos casos, este é o nome de domínio que está registado como o domínio empresarial na Internet. No entanto, pode adicionar mais sufixos UPN através da utilização de domínios do Active Directory e as confianças de entidades.

O UPN do utilizador tem o formato username@domain. Por exemplo, para um domínio do Active Directory com o nome "contoso.com", um utilizador com o nome João pode ter o UPN "john@contoso.com". O UPN do utilizador é baseado no RFC 822. Embora o e-mail e UPN partilham o mesmo formato, o valor de UPN de um utilizador poderá ou poderá não ser igual ao endereço de e-mail do utilizador.

### <a name="user-principal-name-in-azure-ad"></a>Nome principal de utilizador no Azure AD
O Assistente do Azure AD Connect utiliza o atributo userPrincipalName ou permite-lhe especificar o atributo (por uma instalação personalizada) a ser utilizada no local como nome principal de utilizador no Azure AD. Este é o valor que é utilizado para iniciar sessão com o Azure AD. Se o valor do atributo userPrincipalName não corresponde a um domínio verificado no Azure AD, em seguida, do Azure AD substitui-lo com uma predefinição. onmicrosoft.com valor.

Cada diretório no Azure Active Directory é fornecido com um nome de domínio incorporada, com o formato contoso.onmicrosoft.com, que permite a começar com o Azure ou outros serviços Microsoft. Pode melhorar e simplificar a experiência de início de sessão através da utilização de domínios personalizados. Para obter informações sobre nomes de domínio personalizados no Azure AD e como verificar um domínio, consulte [adicionar o seu nome de domínio personalizado ao Azure Active Directory](../add-custom-domain.md#add-the-custom-domain-name-to-your-directory).

## <a name="azure-ad-sign-in-configuration"></a>Configuração do início de sessão do Azure AD
### <a name="azure-ad-sign-in-configuration-with-azure-ad-connect"></a>Configuração do Azure AD início de sessão com o Azure AD Connect
A experiência de início de sessão do Azure AD depende se do Azure AD pode fazer corresponder o sufixo de nome principal de utilizador de um utilizador que está a ser sincronizado a um dos domínios personalizados que são verificados no diretório do Azure AD. O Azure AD Connect fornece ajuda ao configurar definições do Azure AD início de sessão, para que a experiência de início de sessão do utilizador na nuvem é semelhante à experiência no local.

O Azure AD Connect apresenta uma lista de sufixos UPN, que são definidos para os domínios e tenta corresponder a um domínio personalizado no Azure AD. Em seguida, ajuda a com a ação adequada, que necessita de seguimento.
A página de início de sessão do Azure AD apresenta uma lista de sufixos UPN, que são definidos para o Active Directory no local e apresenta o estado correspondente em relação a cada sufixo. Os valores de estado podem ser um dos seguintes:

| Estado | Descrição | É necessária uma ação |
|:--- |:--- |:--- |
| Verificado |O Azure AD Connect foi encontrada que uma correspondência de domínio verificado no Azure AD. Todos os utilizadores para este domínio podem iniciar sessão utilizando as suas credenciais no local. |É necessária nenhuma ação. |
| Não verificado |O Azure AD Connect foi encontrado um domínio personalizado correspondente no Azure AD, mas não é verificado. O sufixo UPN dos utilizadores deste domínio será alterado para a predefinição. sufixo onmicrosoft.com no após a sincronização, se o domínio não está a ser verificado. | [Verifique o domínio personalizado no Azure AD.](../add-custom-domain.md#verify-the-custom-domain-name-in-azure-ad) |
| Não foi adicionada |O Azure AD Connect não foi encontrado um domínio personalizado que corresponded para o sufixo UPN. O sufixo UPN dos utilizadores deste domínio será alterado para a predefinição. sufixo onmicrosoft.com no se o domínio não está adicionado e verificado no Azure. | [Adicionar e verificar um domínio personalizado que corresponde ao sufixo UPN.](../add-custom-domain.md) |

A página de início de sessão do Azure AD apresenta uma lista de sufixos UPN, que são definidos para Active Directory no local e o domínio personalizado correspondente no Azure AD com o estado atual de verificação. Uma instalação personalizada, pode agora selecionar o atributo para o nome principal de utilizador no **do Azure AD início de sessão** página.

![Azure página de início de sessão do AD](./media/active-directory-aadconnect-user-signin/custom_azure_sign_in.png)

Pode clicar no botão de atualização para obter novamente o estado mais recente dos domínios personalizados do Azure AD.

### <a name="selecting-the-attribute-for-the-user-principal-name-in-azure-ad"></a>Selecionar o atributo para o nome principal de utilizador no Azure AD
O atributo userPrincipalName é o atributo de que os utilizadores utilizam quando iniciam sessão no Azure AD e o Office 365. Deve verificar os domínios (também conhecido como sufixos UPN) que são utilizados no Azure AD antes dos utilizadores estão sincronizados.

Recomendamos vivamente que mantenha o atributo userPrincipalName predefinido. Se este atributo é nonroutable e não pode ser verificado, em seguida, é possível selecionar outro atributo (e-mail, por exemplo) como atributo que contém o ID de início de sessão. Isto é conhecido como o ID alternativo. O valor do atributo ID alternativo tem de seguir o padrão de RFC 822. Pode utilizar um ID alternativo com palavra-passe SSO e SSO de federação como solução de início de sessão.

> [!NOTE]
> Utilizar um ID alternativo não é compatível com todas as cargas de trabalho do Office 365. Para obter mais informações, consulte [configurar ID de início de sessão alternativo](https://technet.microsoft.com/library/dn659436.aspx).
>
>

#### <a name="different-custom-domain-states-and-their-effect-on-the-azure-sign-in-experience"></a>Estados de outro domínio personalizado e respetivos efeitos sobre a experiência de início de sessão do Azure
É muito importante compreender a relação entre os Estados de domínio personalizado no diretório do Azure AD e os sufixos UPN que estão definidas no local. Vamos percorrer as possíveis Azure início de sessão experiências diferentes quando estiver a configurar sincronização utilizando o Azure AD Connect.

Para obter as informações seguintes, vamos supor que estamos preocupados com contoso.com de sufixo UPN, o que é utilizado no diretório no local como parte de UPN – por exemplo user@contoso.com.

###### <a name="express-settingspassword-hash-synchronization"></a>Expressar a sincronização de hash de definições/palavra-passe
| Estado | Efeito na experiência de utilizador, o início de sessão do Azure |
|:---:|:--- |
| Não foi adicionada |Neste caso, nenhum domínio personalizado para contoso.com foi adicionado no diretório do Azure AD. Os utilizadores que possuem UPN no local com o sufixo @contoso.com não será possível utilizar o UPN no local para iniciar sessão no Azure. Em vez disso, terá a utilizar um UPN novo que é fornecido aos mesmos pelo Azure AD, adicionando o sufixo do diretório do Azure AD de predefinição. Por exemplo, se estiver a sincronizar os utilizadores azurecontoso.onmicrosoft.com de diretório do Azure AD, em seguida, o utilizador no local user@contoso.com terá um UPN de user@azurecontoso.onmicrosoft.com. |
| Não verificado |Neste caso, temos contoso.com um domínio personalizado que é adicionado no diretório do Azure AD. No entanto, ainda não é verificado. Se prosseguir com a sincronizar os utilizadores sem verificar o domínio, em seguida, os utilizadores serão atribuídos um UPN novo pelo Azure AD, tal como no cenário "Não adicionar". |
| Verificado |Neste caso, temos contoso.com um domínio personalizado que já tenha adicionado e verificados no Azure AD para o sufixo UPN. Os utilizadores serão capazes de utilizar o nome principal de utilizador respetivo no local, por exemplo user@contoso.com, para iniciar sessão no Azure depois de que está a ser sincronizadas com o Azure AD. |

###### <a name="ad-fs-federation"></a>Federação do AD FS
Não é possível criar uma federação com a predefinição. domínio onmicrosoft.com no Azure AD ou um domínio personalizado não verificado no Azure AD. Quando estiver a executar o Assistente do Azure AD Connect, se selecionar um domínio não verificado para criar a Federação com, em seguida, o Azure AD Connect irá pedir-lhe com os registos necessários para ser criados em que o DNS está alojado para o domínio. Para obter mais informações, consulte [verificar o domínio do Azure AD selecionado para Federação](active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation).

Se tiver selecionado a opção de início de sessão de utilizador **federação com o AD FS**, em seguida, tem de ter um domínio personalizado para continuar a criação de Federação no Azure AD. Na nossa debate, significa que temos uma contoso.com de domínio personalizado adicionado no diretório do Azure AD.

| Estado | Efeito no utilizador experiência de início de sessão do Azure |
|:---:|:--- |
| Não foi adicionada |Neste caso, o Azure AD Connect não foi encontrado um domínio personalizado correspondente para a contoso.com de sufixo UPN no diretório do Azure AD. Tem de adicionar um domínio personalizado contoso.com se precisar de utilizadores para iniciar sessão através da utilização do AD FS com o UPN no local (como user@contoso.com). |
| Não verificado |Neste caso, o Azure AD Connect irá pedir-lhe com adequado detalhes sobre como pode verificar o seu domínio posterior. |
| Verificado |Neste caso, pode aceder diretamente com a configuração sem qualquer ação adicional. |

## <a name="changing-the-user-sign-in-method"></a>Alterar o método de início de sessão do utilizador
Pode alterar o método de início de sessão do utilizador de Federação, a sincronização de hash de palavra-passe ou a autenticação pass-through, utilizando as tarefas que estão disponíveis no Azure AD Connect após a configuração inicial do Azure AD Connect com o assistente. Execute novamente o Assistente do Azure AD Connect, e verá uma lista de tarefas que pode realizar. Selecione **alterar utilizador início de sessão** da lista de tarefas.

![Alterar a sessão do utilizador](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

Na página seguinte, está a pedido para fornecer as credenciais para o Azure AD.

![Ligar ao Azure AD](./media/active-directory-aadconnect-user-signin/changeusersignin2.png)

No **sessão do utilizador** página, selecione o utilizador pretendido início de sessão.

![Ligar ao Azure AD](./media/active-directory-aadconnect-user-signin/changeusersignin2a.png)

> [!NOTE]
> Se um comutador temporário apenas está a efetuar a sincronização de hash de palavra-passe, em seguida, selecione o **não converter as contas de utilizador** caixa de verificação. A opção de verificação não irá converter a cada utilizador federado e pode demorar várias horas.
>
>

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [integrar as identidades no local ao Azure Active Directory](active-directory-aadconnect.md).
- Saiba mais sobre [conceitos de design do Azure AD Connect](active-directory-aadconnect-design-concepts.md).
