---
title: "Gestão de serviços de Federação do Active Directory e personalização com o Azure AD Connect | Microsoft Docs"
description: "Gestão do AD FS com o Azure AD Connect e personalização do AD FS início de sessão experiência de utilizador com o Azure AD Connect e PowerShell."
keywords: "AD FS, ADFS, do AD FS gestão, AAD Connect, ligar, início de sessão, do AD FS personalização, repare a Federação de confiança, Office 365, da entidade confiadora"
services: active-directory
documentationcenter: 
author: anandyadavmsft
manager: femila
editor: 
ms.assetid: 2593b6c6-dc3f-46ef-8e02-a8e2dc4e9fb9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: c9ff22c7b4793112da5211162ed9cd58ac86d238
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2017
---
# <a name="manage-and-customize-active-directory-federation-services-by-using-azure-ad-connect"></a>Gerir e personalizar os serviços de Federação do Active Directory utilizando o Azure AD Connect
Este artigo descreve como gerir e personalizar os serviços de Federação do Active Directory (AD FS), utilizando o Azure Active Directory (Azure AD) Connect. Também inclui outras tarefas comuns do AD FS que poderá ter de fazer para toda a configuração de um farm do AD FS.

| Tópico | O que abrange a |
|:--- |:--- |
| **Gerir o AD FS** | |
| [Reparar a confiança](#repairthetrust) |Como reparar a confiança de federação com o Office 365. |
| [Federar com o Azure AD utilizando o ID de início de sessão alternativo](#alternateid) | Configurar a Federação com o ID de início de sessão alternativo  |
| [Adicionar um servidor do AD FS](#addadfsserver) |Como expandir um farm do AD FS com um servidor do AD FS adicional. |
| [Adicionar um servidor de Proxy de aplicações Web do AD FS](#addwapserver) |Como expandir um farm do AD FS com um servidor de Proxy de aplicações Web (WAP) adicional. |
| [Adicionar um domínio federado](#addfeddomain) |Como adicionar um domínio federado. |
| [Atualizar o certificado SSL](active-directory-aadconnectfed-ssl-update.md)| Como atualizar o certificado SSL para um farm do AD FS. |
| **Personalizar o AD FS** | |
| [Adicionar um logótipo de empresa personalizado ou ilustração](#customlogo) |Como personalizar uma página de início de sessão do AD FS com um logótipo da empresa e a ilustração. |
| [Adicione uma descrição de início de sessão](#addsignindescription) |Como adicionar uma descrição da página de início de sessão. |
| [Modificar as regras de afirmações do AD FS](#modclaims) |Como modificar afirmações do AD FS para vários cenários de Federação. |

## <a name="manage-ad-fs"></a>Gerir o AD FS
Pode efetuar diversas tarefas relacionadas com FS do AD no Azure AD Connect com intervenção do utilizador mínima, utilizando o Assistente do Azure AD Connect. Depois de terminar a instalar o Azure AD Connect, executando o assistente, pode executar o Assistente para realizar tarefas adicionais.

## <a name="repairthetrust"></a>Reparar a confiança 
Pode utilizar o Azure AD Connect para verificar o estado de funcionamento atual do AD FS e do Azure AD confia e tome as ações adequadas para reparar a confiança. Siga estes passos para reparar o seu Azure AD e fidedignidade do AD FS.

1. Selecione **reparação AAD e ADFS confiar** da lista de tarefas adicionais.
   ![Reparar AAD e o ADFS confiar](media/active-directory-aadconnect-federation-management/RepairADTrust1.PNG)

2. No **ligar para o Azure AD** página, forneça as credenciais de administrador global para o Azure AD e clique em **seguinte**.
   ![Ligar ao Azure AD](media/active-directory-aadconnect-federation-management/RepairADTrust2.PNG)

3. No **credenciais de acesso remoto** página, introduza as credenciais de administrador do domínio.

   ![Credenciais de acesso remoto](media/active-directory-aadconnect-federation-management/RepairADTrust3.PNG)

    Depois de clicar em **seguinte**, Azure AD Connect verifica o estado de funcionamento do certificado e mostra os problemas.

    ![Estado de certificados](media/active-directory-aadconnect-federation-management/RepairADTrust4.PNG)

    O **pronto para configurar** página mostra a lista de ações que serão executadas para reparar a confiança.

    ![Preparado para configurar](media/active-directory-aadconnect-federation-management/RepairADTrust5.PNG)

4. Clique em **instalar** para reparar a confiança.

> [!NOTE]
> Azure AD Connect pode apenas reparação ou ATO em certificados autoassinados. O Azure AD Connect não é possível reparar a certificados de terceiros.

## <a name="alternateid"></a>Federar com o Azure AD utilizando AlternateID 
Recomenda-se que o Name(UPN) de Principal de utilizador no local e na nuvem Nome Principal de utilizador são mantidos o mesmo. Se o UPN no local utiliza um domínio não encaminháveis internos (por exemplo Contoso. local) ou não pode ser alterado devido a dependências de aplicações locais, recomendamos que configurar ID de início de sessão alternativo. ID de início de sessão alternativo permite-lhe configurar uma experiência de início de sessão onde os utilizadores podem iniciar sessão com um atributo que não seja o UPN, como o correio. A escolha de nome Principal de utilizador no Azure AD Connect por predefinição, o atributo userPrincipalName no Active Directory. Se escolher qualquer outro atributo para o nome Principal de utilizador e são Federação a utilizar o AD FS, em seguida, o Azure AD Connect irá configurar o AD FS para o ID de início de sessão alternativo. Um exemplo de escolher um atributo diferente para o nome do Principal de utilizador é mostrado abaixo:

![Seleção de atributo ID alternativa](media/active-directory-aadconnect-federation-management/attributeselection.png)

Configurar o ID de início de sessão alternativo para o AD FS consiste em dois passos principais:
1. **Configurar o conjunto correto de emissão de afirmações**: as regras de afirmação de emissão da entidade confiadora do Azure AD confiam são modificados para utilizar o atributo UserPrincipalName selecionado como o ID alternativo do utilizador.
2. **Ativar o ID de início de sessão alternativo na configuração do AD FS**: A configuração do AD FS é atualizada para que o AD FS pode procurar utilizadores nas florestas adequadas com o ID alternativo. Esta configuração é suportada para o AD FS no Windows Server 2012 R2 (com KB2919355) ou posterior. Se os servidores AD FS 2012 R2, o Azure AD Connect verifica a presença do KB necessário. Se não for detetado o KB, um aviso é apresentado após a conclusão da configuração, como mostrado abaixo:

    ![Aviso de em falta KB no 2012R2](media/active-directory-aadconnect-federation-management/kbwarning.png)

    Para resolver a configuração em caso de KB em falta, instale o necessário [KB2919355](http://go.microsoft.com/fwlink/?LinkID=396590) e, em seguida, repare a confiança utilizando [reparar AAD e o AD FS confiança](#repairthetrust).

> [!NOTE]
> Para obter mais informações sobre alternateID e os passos para configurar manualmente, leia o artigo [configurar ID de início de sessão alternativo](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/configuring-alternate-login-id)

## <a name="addadfsserver"></a>Adicionar um servidor do AD FS 

> [!NOTE]
> Para adicionar um servidor do AD FS, o Azure AD Connect necessita do certificado do PFX. Por conseguinte, pode efetuar esta operação apenas se tiver configurado o farm do AD FS utilizando o Azure AD Connect.

1. Selecione **implementar um servidor de Federação adicional**e clique em **seguinte**.

   ![Servidor de Federação adicional](media/active-directory-aadconnect-federation-management/AddNewADFSServer1.PNG)

2. No **ligar para o Azure AD** página, introduza as suas credenciais de administrador global para o Azure AD e clique em **seguinte**.

   ![Ligar ao Azure AD](media/active-directory-aadconnect-federation-management/AddNewADFSServer2.PNG)

3. Fornece credenciais de administrador de domínio.

   ![Credenciais de administrador de domínio](media/active-directory-aadconnect-federation-management/AddNewADFSServer3.PNG)

4. O Azure AD Connect pergunta da palavra-passe do ficheiro PFX fornecido ao configurar o seu novo farm do AD FS com o Azure AD Connect. Clique em **introduza a palavra-passe** para fornecer a palavra-passe para o ficheiro PFX.

   ![Palavra-passe de certificado](media/active-directory-aadconnect-federation-management/AddNewADFSServer4.PNG)

    ![Especificar certificado SSL](media/active-directory-aadconnect-federation-management/AddNewADFSServer5.PNG)

5. No **servidores do AD FS** página, introduza o nome do servidor ou endereço IP a ser adicionado ao farm do AD FS.

   ![Servidores do AD FS](media/active-directory-aadconnect-federation-management/AddNewADFSServer6.PNG)

6. Clique em **seguinte**e percorra o final **configurar** página. Depois do Azure AD Connect terminou a adicionar os servidores no farm do AD FS, terá a opção para verificar a conectividade.

   ![Preparado para configurar](media/active-directory-aadconnect-federation-management/AddNewADFSServer7.PNG)

    ![Instalação concluída](media/active-directory-aadconnect-federation-management/AddNewADFSServer8.PNG)

## <a name="addwapserver"></a>Adicionar um servidor do WAP do AD FS 

> [!NOTE]
> Para adicionar um servidor do WAP, o Azure AD Connect necessita do certificado do PFX. Por conseguinte, só pode efetuar esta operação se tiver configurado o farm do AD FS utilizando o Azure AD Connect.

1. Selecione **implementar o Proxy de aplicações Web** da lista de tarefas disponíveis.

   ![Implementar o Proxy de aplicações Web](media/active-directory-aadconnect-federation-management/WapServer1.PNG)

2. Forneça as credenciais de administrador global do Azure.

   ![Ligar ao Azure AD](media/active-directory-aadconnect-federation-management/wapserver2.PNG)

3. No **certificado SSL especificar** página, forneça a palavra-passe para o ficheiro PFX que indicou quando configurou o farm do AD FS com o Azure AD Connect.
   ![Palavra-passe do certificado](media/active-directory-aadconnect-federation-management/WapServer3.PNG)

    ![Especificar certificado SSL](media/active-directory-aadconnect-federation-management/WapServer4.PNG)

4. Adicione o servidor para ser adicionado como um servidor do WAP. Porque o servidor do WAP não pode ser associado ao domínio, o assistente pede-lhe credenciais administrativas para o servidor que está a ser adicionado.

   ![Credenciais administrativas do](media/active-directory-aadconnect-federation-management/WapServer5.PNG)

5. No **as credenciais de fidedignidade do Proxy** página, forneça as credenciais administrativas para configurar o proxy de confiam e acedam ao servidor primário do farm AD FS.

   ![Credenciais de confiança do proxy](media/active-directory-aadconnect-federation-management/WapServer6.PNG)

6. No **pronto para configurar** página, o assistente mostra a lista de ações que serão executados.

   ![Preparado para configurar](media/active-directory-aadconnect-federation-management/WapServer7.PNG)

7. Clique em **instalar** para concluir a configuração. Depois de concluída a configuração, o assistente fornece-lhe a opção para verificar a conectividade aos servidores. Clique em **verifique** para verificar a conectividade.

   ![Instalação concluída](media/active-directory-aadconnect-federation-management/WapServer8.PNG)

## <a name="addfeddomain"></a>Adicionar um domínio federado 

É fácil adicionar um domínio a ser Federado com o Azure AD através do Azure AD Connect. O Azure AD Connect adiciona o domínio para Federação e modifica as regras de afirmação para refletir corretamente o emissor quando tem vários domínios federados com o Azure AD.

1. Para adicionar um domínio federado, selecione a tarefa **adicionar outro domínio do Azure AD**.

   ![Adicionais domínio do Azure AD](media/active-directory-aadconnect-federation-management/AdditionalDomain1.PNG)

2. Na página seguinte do assistente, forneça as credenciais de administrador global para o Azure AD.

   ![Ligar ao Azure AD](media/active-directory-aadconnect-federation-management/AdditionalDomain2.PNG)

3. No **credenciais de acesso remoto** página, forneça as credenciais de administrador de domínio.

   ![Credenciais de acesso remoto](media/active-directory-aadconnect-federation-management/additionaldomain3.PNG)

4. Na página seguinte, o assistente fornece uma lista de domínios do Azure AD que podem federar o seu diretório no local com. Escolha o domínio a partir da lista.

   ![Domínio do Azure AD](media/active-directory-aadconnect-federation-management/AdditionalDomain4.PNG)

    Depois de escolher o domínio, o assistente fornece-lhe as informações adequadas sobre mais ações que o assistente executará e o impacto da configuração. Em alguns casos, se selecionar um domínio que ainda não está a ser verificado no Azure AD, o assistente fornece-lhe informações para ajudar a verificar o domínio. Consulte [adicionar o seu nome de domínio personalizado ao Azure Active Directory](../active-directory-domains-add-azure-portal.md) para obter mais detalhes.

5. Clique em **Seguinte**. O **pronto para configurar** página mostra a lista de ações que o Azure AD Connect irá realizar. Clique em **instalar** para concluir a configuração.

   ![Preparado para configurar](media/active-directory-aadconnect-federation-management/AdditionalDomain5.PNG)

> [!NOTE]
> Utilizadores do domínio federado adicionado têm de ser sincronizados antes de poder iniciar sessão com o Azure AD.

## <a name="ad-fs-customization"></a>Personalização do AD FS
As secções seguintes fornecem detalhes sobre algumas das tarefas comuns que poderá ter de efetuar quando personalizar a página de início de sessão do AD FS.

## <a name="customlogo"></a>Adicionar um logótipo de empresa personalizado ou ilustração 
Para alterar o logótipo da empresa que é apresentado no **início de sessão** página, utilize a sintaxe e o seguinte cmdlet do Windows PowerShell.

> [!NOTE]
> As dimensões recomendadas para o logótipo são 260 x 35 a 96 dpi com um tamanho de ficheiro não superior a 10 KB.

    Set-AdfsWebTheme -TargetName default -Logo @{path="c:\Contoso\logo.PNG"}

> [!NOTE]
> O *TargetName* é necessário o parâmetro. O tema predefinido que é fornecido com o AD FS com o nome predefinido.

## <a name="addsignindescription"></a>Adicione uma descrição de início de sessão 
Para adicionar uma descrição da página de início de sessão para o **página de início de sessão**, utilize a sintaxe e o seguinte cmdlet do Windows PowerShell.

    Set-AdfsGlobalWebContent -SignInPageDescriptionText "<p>Sign-in to Contoso requires device registration. Click <A href='http://fs1.contoso.com/deviceregistration/'>here</A> for more information.</p>"

## <a name="modclaims"></a>Modificar as regras de afirmações do AD FS 
AD FS suporta um idioma de afirmação avançado que pode utilizar para criar regras de afirmação personalizada. Para obter mais informações, consulte [a função da linguagem de regras de afirmação](https://technet.microsoft.com/library/dd807118.aspx).

As secções seguintes descrevem como pode escreve regras personalizadas para alguns cenários que se relacionam com o Azure AD e a Federação do AD FS.

### <a name="immutable-id-conditional-on-a-value-being-present-in-the-attribute"></a>ID imutável condicional num valor estar presente no atributo
O Azure AD Connect permite-lhe especificar um atributo a ser utilizado como uma âncora de origem quando objetos são sincronizados com o Azure AD. Se o valor do atributo personalizado não está vazio, poderá querer emitir uma afirmação de ID imutável.

Por exemplo, poderá selecionar **ms-ds-consistencyguid** como o atributo para a âncora de origem e o problema **ImmutableID** como **ms-ds-consistencyguid** caso o atributo tem um valor nele. Se não existir nenhum valor contra o atributo, emitir **objectGuid** como ID imutável. Pode criar o conjunto de regras de afirmação personalizada, tal como descrito na secção seguinte.

**Regra 1: Atributos de consulta**

    c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"]
    => add(store = "Active Directory", types = ("http://contoso.com/ws/2016/02/identity/claims/objectguid", "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"), query = "; objectGuid,ms-ds-consistencyguid;{0}", param = c.Value);

Esta regra, está a consultar os valores de **ms-ds-consistencyguid** e **objectGuid** para o utilizador do Active Directory. Altere o nome de arquivo para um nome de arquivo adequado na sua implementação do AD FS. Também alterar o tipo de afirmações para um adequado afirmações de tipo para a Federação, tal como definido para **objectGuid** e **ms-ds-consistencyguid**.

Além disso, ao utilizar **adicionar** e não **problema**, evitar a adição de um problema de saída para a entidade e pode utilizar os valores como valores intermédios. Irá emitir afirmações de uma regra posterior depois de estabelecer qual o valor a utilizar como o ID imutável.

**Regra 2: Verificar se o ms-ds-consistencyguid existe para o utilizador**

    NOT EXISTS([Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"])
    => add(Type = "urn:anandmsft:tmp/idflag", Value = "useguid");

Esta regra define um sinalizador temporário denominado **idflag** que está definido como **useguid** se não houver nenhuma **ms-ds-consistencyguid** preenchido para o utilizador. A lógica por trás este é o facto de que o AD FS não permite afirmações vazias. Para adicionar afirmações http://contoso.com/ws/2016/02/identity/claims/objectguid e http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid na regra 1, acaba por ficar com uma **msdsconsistencyguid** afirmação apenas se a valor é preenchido para o utilizador. Se não se encontra preenchida, o AD FS verá que terá um valor vazio e ignora-lo imediatamente. Todos os objetos terão **objectGuid**, por isso que afirmações serão sempre existe depois de executar a regra 1.

**A regra 3: Emitir ms-ds-consistencyguid como ID imutável se estiver presente**

    c:[Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"]
    => issue(Type = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier", Value = c.Value);

Esta é uma implícita **Exist** verificar. Se o valor de afirmação, em seguida, emite que como ID imutável. O exemplo anterior utiliza o **nameidentifier** de afirmação. Terá de alterar este para o tipo de afirmações adequado para o ID imutável no seu ambiente.

**Regra 4: Emitir objectGuid como ID imutável se ms-ds-consistencyGuid não estiver presente**

    c1:[Type == "urn:anandmsft:tmp/idflag", Value =~ "useguid"]
    && c2:[Type == "http://contoso.com/ws/2016/02/identity/claims/objectguid"]
    => issue(Type = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier", Value = c2.Value);

Esta regra, que está simplesmente a verificar o sinalizador temporário **idflag**. Decida se pretende emitir afirmações com base no respetivo valor.

> [!NOTE]
> A sequência destas regras é importante.

### <a name="sso-with-a-subdomain-upn"></a>SSO com um subdomínio UPN
Pode adicionar mais do que um domínio a ser federado através da utilização do Azure AD Connect, conforme descrito em [adicionar um novo domínio federado](active-directory-aadconnect-federation-management.md#addfeddomain). Tem de modificar a afirmação de nome principal (UPN) do utilizador para que o ID de emissor corresponde ao domínio de raiz e não o subdomínio, porque o domínio de raiz federado também abrange o subordinado.

Por predefinição, a regra de afirmação para o emissor ID está definida como:

    c:[Type
    == “http://schemas.xmlsoap.org/claims/UPN“]

    => issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(c.Value, “.+@(?<domain>.+)“, “http://${domain}/adfs/services/trust/“));

![Predefinição emissor afirmação ID IDE INome](media/active-directory-aadconnect-federation-management/issuer_id_default.png)

A regra predefinida simplesmente demora o sufixo UPN e utiliza-o na afirmação de ID de emissor. Por exemplo, o João é um utilizador na sub.contoso.com e contoso.com está federada com o Azure AD. O João entra john@sub.contoso.com como o nome de utilizador ao iniciar sessão com o Azure AD. A regra de afirmação de ID emissor predefinida no AD FS processa da seguinte forma:

    c:[Type
    == “http://schemas.xmlsoap.org/claims/UPN“]

    => issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(john@sub.contoso.com, “.+@(?<domain>.+)“, “http://${domain}/adfs/services/trust/“));

**Valor de afirmação:** http://sub.contoso.com/adfs/services/trust/

Para que apenas o domínio de raiz no valor de afirmação de emissor, altere a regra de afirmação para fazer corresponder o seguinte:

    c:[Type == “http://schemas.xmlsoap.org/claims/UPN“]

    => issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(c.Value, “^((.*)([.|@]))?(?<domain>[^.]*[.].*)$”, “http://${domain}/adfs/services/trust/“));

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre [opções de início de sessão do utilizador](active-directory-aadconnect-user-signin.md).
