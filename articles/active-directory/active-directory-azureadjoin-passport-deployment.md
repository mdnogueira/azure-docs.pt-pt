---
title: "Ativar o Microsoft Windows Hello para empresas na sua organização | Microsoft Docs"
description: "Instruções de implementação para ativar o Microsoft Passport na sua organização."
services: active-directory
documentationcenter: 
keywords: "configurar o Microsoft Passport, Microsoft Windows Hello para implementação de negócio"
author: MarkusVi
manager: femila
tags: azure-classic-portal
ms.assetid: 7dbbe3c6-1cd7-429c-a9b2-115fcbc02416
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: markvi
ms.openlocfilehash: 8fd9f3a51651fc163abd79aaf21ec3dd92502bb5
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2017
---
# <a name="enable-microsoft-windows-hello-for-business-in-your-organization"></a>Ativar o Microsoft Windows Hello para empresas na sua organização
Depois de [ligar dispositivos associados a um domínio do Windows 10 com o Azure Active Directory](active-directory-azureadjoin-devices-group-policy.md), efetue o seguinte procedimento para ativar o Microsoft Windows Hello para empresas na sua organização:

1. Implementar o System Center Configuration Manager  
2. Configurar definições de política
3. Configurar o perfil de certificado  

## <a name="deploy-system-center-configuration-manager"></a>Implementar o System Center Configuration Manager
Para implementar certificados de utilizador com base no Windows Hello para chaves de negócio, precisa do seguinte:

* **System Center Configuration Manager Current Branch** -tem de instalar a versão 1606 ou melhor. Para obter mais informações, consulte o [documentação para o System Center Configuration Manager](https://technet.microsoft.com/library/mt346023.aspx) e [blogue da equipa do System Center Configuration Manager](http://blogs.technet.com/b/configmgrteam/archive/2015/09/23/now-available-update-for-system-center-config-manager-tp3.aspx).
* **Infraestrutura de chaves públicas (PKI)** - para ativar o Microsoft Windows Hello para empresas utilizando certificados de utilizador, tem de ter uma PKI no local. Se não tiver uma, ou se não pretender utilizá-lo para certificados de utilizador, pode implementar um novo controlador de domínio que tenha o Windows Server 2016 compilação 10551 (ou superior) instalado. Siga os passos para [instalar um controlador de domínio de réplica num domínio existente](https://technet.microsoft.com/library/jj574134.aspx) ou [instalar uma nova floresta do Active Directory, se estiver a criar um novo ambiente](https://technet.microsoft.com/library/jj574166). (Os ISOs estão disponíveis para transferência no [Signiant suporte Exchange](https://datatransfer.microsoft.com/signiant_media_exchange/spring/main?sdkAccessible=true).)

## <a name="configure-policy-settings"></a>Configurar definições de política
Para configurar o Microsoft Windows Hello para definições de política da empresa, tem duas opções:

* Política de grupo no Active Directory 
* O System Center Configuration Manager 

Política de grupo utilizando no Active Directory é o método recomendado para configurar o Microsoft Windows Hello para definições de política da empresa. 

Utilizar o System Center Configuration Manager é o método preferencial quando também o utilizar para implementar certificados. Este cenário:

* Garante a compatibilidade com os cenários de implementação mais recentes
* Necessita do lado do cliente Windows 10 versão 1607 ou superior.

### <a name="configure-microsoft-windows-hello-for-business-via-group-policy-in-active-directory"></a>Configurar o Microsoft Windows Hello para empresas através da política de grupo no Active Directory
**Passos**:

1. Abra o Gestor de servidor e navegue para **ferramentas** > **gestão de políticas de grupo**.
2. Gestão de políticas de grupo, navegue para o nó de domínio que corresponde ao domínio no qual pretende ativar a associação do Azure AD.
3. Clique com botão direito **objetos de política de grupo**e selecione **novo**. Atribua o objeto de política de grupo um nome, por exemplo, ativar o Windows Hello para empresas. Clique em **OK**.
4. Clique com o botão direito do novo objeto de política de grupo e, em seguida, selecione **editar**.
5. Navegue para **configuração do computador** > **políticas** > **modelos administrativos** > **Windows Componentes** > **Windows Hello para empresas**.
6. Clique com botão direito **ativar Windows Hello para empresas**e, em seguida, selecione **editar**.
7. Selecione o **ativado** botão de opção e, em seguida, clique em **aplicar**. Clique em **OK**.
8. Pode agora ligar o objeto de política de grupo para uma localização da sua preferência. Para ativar esta política para todos os dispositivos Windows 10 associados a um domínio na sua organização, ligue a política de grupo ao domínio. Por exemplo:
   * Uma unidade organizacional (UO) específica no Active Directory onde estarão localizados computadores associados a um domínio do Windows 10
   * Um grupo de segurança específicos que contenha computadores Windows 10 associados a um domínio que serão registados automaticamente com o Azure AD

### <a name="configure-windows-hello-for-business-using-system-center-configuration-manager"></a>Configurar o Windows Hello para empresas com o System Center Configuration Manager
**Passos**:

1. Abra o **System Center Configuration Manager**e, em seguida, navegue para **ativos e compatibilidade > definições de compatibilidade > acesso a recursos da empresa > Windows Hello para empresas perfis**.
   
    ![Configurar o Windows Hello para empresas](./media/active-directory-azureadjoin-passport-deployment/01.png)
2. Na barra de ferramentas na parte superior, clique em **criar Windows Hello para empresas perfil**.
   
    ![Configurar o Windows Hello para empresas](./media/active-directory-azureadjoin-passport-deployment/02.png)
3. No **geral** caixa de diálogo, execute os seguintes passos:
   
    ![Configurar o Windows Hello para empresas](./media/active-directory-azureadjoin-passport-deployment/03.png)
   
    a. No **nome** caixa de texto, escreva um nome para o seu perfil, por exemplo, **meu perfil WHfB**.
   
    b. Clique em **Seguinte**.
4. No **plataformas suportadas** caixa de diálogo, selecione as plataformas que serão aprovisionadas com este Windows perfil Hello para empresas e, em seguida, clique em **seguinte**.
   
    ![Configurar o Windows Hello para empresas](./media/active-directory-azureadjoin-passport-deployment/04.png)
5. No **definições** caixa de diálogo, execute os seguintes passos:
   
    ![Configurar o Windows Hello para empresas](./media/active-directory-azureadjoin-passport-deployment/05.png)
   
    a. Como **configurar o Windows Hello para empresas**, selecione **ativado**.
   
    b. Como **utilizar um Trusted Platform Module (TPM)**, selecione **necessário**. 
   
    c. Como **método de autenticação**, selecione **baseada em certificado**.
   
    d. Clique em **Seguinte**.
6. No **resumo** caixa de diálogo, clique em **seguinte**.
7. No **conclusão** caixa de diálogo, clique em **fechar**.
8. Na barra de ferramentas na parte superior, clique em **implementar**.
   
    ![Configurar o Windows Hello para empresas](./media/active-directory-azureadjoin-passport-deployment/06.png)

## <a name="configure-the-certificate-profile"></a>Configurar o perfil de certificado
Se estiver a utilizar autenticação baseada em certificado para autenticação no local, terá de configurar e implementar um perfil de certificado. Esta tarefa requer a configurar um servidor do NDES e a função de site do ponto de registo de certificados do System Center Configuration Manager. Para obter mais detalhes, consulte o [pré-requisitos para perfis de certificado no Configuration Manager](https://technet.microsoft.com/library/dn261205.aspx).

1. Abra o **System Center Configuration Manager**e, em seguida, navegue para **ativos e compatibilidade > definições de compatibilidade > acesso a recursos da empresa > perfis de certificado**.
2. Selecione um modelo que tenha de Smart Card início de sessão utilização alargada da chave (EKU).

No **inscrição SCEP** página do perfil de certificado, tem de escolher **instalar no Passport for Work, de outra forma falha** como o **Key Storage Provider**.

## <a name="next-steps"></a>Passos seguintes
* [Windows 10 para a empresa: formas de utilizar os dispositivos para o trabalho](active-directory-azureadjoin-windows10-devices-overview.md)
* [Aumentar as capacidades de nuvem para dispositivos com Windows 10 através da Associação do Azure Active Directory](active-directory-azureadjoin-user-upgrade.md)
* [Autenticar sem palavras-passe](active-directory-azureadjoin-passport.md)
* [Saiba mais sobre os cenários de utilização da Associação do Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Ligar a dispositivos associados a um domínio ao Azure AD para experiências com o Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configurar a Associação do Azure AD](active-directory-azureadjoin-setup.md)

