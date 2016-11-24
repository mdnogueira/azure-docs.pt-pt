---
title: Proteger recursos da cloud com o MFA do Azure e AD FS
description: "Esta é a página do Multi-Factor Authentication do Azure que descreve como começar a utilizar o MFA do Azure e o AD FS na nuvem."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 0927fc67-8090-4fdd-913a-b3cfed3fbe77
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/14/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 830eb6627cae71f358b9790791b1d86f7c82c566
ms.openlocfilehash: 4a2d271be7fbd0d27163ead8f8eb2c05a43f7fbc


---
# <a name="securing-cloud-resources-with-azure-multi-factor-authentication-and-ad-fs"></a>Proteger recursos da nuvem com o Multi-Factor Authentication do Azure e o AD FS
Se a sua organização estiver federada no Azure Active Directory, utilize o Multi-Factor Authentication do Azure ou os Serviços de Federação do Active Directory (AD FS) para proteger os recursos acedidos pelo Azure AD. Utilize os procedimentos seguintes para proteger os recursos do Azure Active Directory com o Multi-Factor Authentication do Azure ou os Serviços de Federação do Active Directory (AD FS).

## <a name="secure-azure-ad-resources-using-ad-fs"></a>Proteger recursos do Azure AD com o AD FS
Para proteger o seu recurso da cloud, ative primeiro uma conta para os utilizadores e, em seguida, configure uma regra de afirmações. Siga este procedimento para percorrer os passos:

1. Utilize os passos descritos em [Ativar o Multi-Factor Authentication para os utilizadores](multi-factor-authentication-get-started-cloud.md#turn-on-two-step-verification-for-users) para ativar uma conta.
2. Inicie a consola de Gestão do AD FS.
   ![Cloud](./media/multi-factor-authentication-get-started-adfs-cloud/adfs1.png)
3. Navegue até **Confianças de Entidade Confiadora** e clique com o botão direito do rato na Confiança da Entidade Confiadora. Selecione **Editar Regras de Afirmação...**
4. Clique em **Adicionar Regra...**
5. No menu pendente, selecione **Enviar Afirmações Utilizando uma Regra Personalizada** e clique em **Seguinte**.
6. Introduza um nome para a regra de afirmação.
7. Em Regra personalizada, adicione o texto seguinte:

    ```
    => issue(Type = "http://schemas.microsoft.com/claims/authnmethodsreferences", Value = "http://schemas.microsoft.com/claims/multipleauthn");
    ```

    Afirmação correspondente:

    ```
    <saml:Attribute AttributeName="authnmethodsreferences" AttributeNamespace="http://schemas.microsoft.com/claims">
    <saml:AttributeValue>http://schemas.microsoft.com/claims/multipleauthn</saml:AttributeValue>
    </saml:Attribute>
    ```
8. Clique em **OK** e em **Concluir**. Feche a consola de Gestão do AD FS.

Em seguida, os utilizadores podem concluir o início de sessão utilizando o método no local (por exemplo, um smart card).

## <a name="trusted-ips-for-federated-users"></a>IPs Fidedignos para utilizadores federados
Os IPs Fidedignos permitem aos administradores ignorar a verificação em dois passos para endereços IP específicos ou para os utilizadores federados que tenham pedidos com origem na sua própria intranet. As secções seguintes descrevem como configurar IPs Fidedignos do Multi-Factor Authentication do Azure com utilizadores federados e ignorar a verificação em dois passos quando um pedido tem origem na intranet dos utilizadores federados. Isto é feito ao configurar o AD FS para utilizar uma passagem ou filtrar um modelo de afirmação de entrada com o tipo de afirmação Dentro da Rede da Empresa.

Este exemplo utiliza o Office 365 para as Confianças de Entidades Confiadoras.

### <a name="configure-the-ad-fs-claims-rules"></a>Configurar regras de afirmações do AD FS
A primeira coisa a fazer é configurar as afirmações do AD FS. Vamos criar duas regras de afirmações, uma para o tipo de afirmação Dentro da Rede da Empresa e uma adicional para manter os utilizadores com sessão iniciada.

1. Abra a Gestão do AD FS.
2. À esquerda, selecione **Confianças de Entidades Confiadoras**.
3. Clique com o botão direito do rato na **Plataforma de Identidade do Microsoft Office 365** e selecione **Editar Regras de Afirmação**
   ![Cloud](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip1.png)
4. Em Regras de Transformação da Emissão, clique em **Adicionar Regra.**
   ![Cloud](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip2.png)
5. No Assistente para Adicionar Regra de Afirmação de Transformação, selecione **Passar ou Filtrar uma Afirmação de Entrada** no menu pendente e clique em **Seguinte**.
   ![Cloud](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip3.png)
6. Na caixa junto ao nome da regra de afirmação, atribua um nome à regra. Por exemplo: InsideCorpNet.
7. No menu pendente, junto ao tipo de afirmação de entrada, selecione **Dentro da Rede da Empresa**.
   ![Cloud](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip4.png)
8. Clique em **Concluir**.
9. Em Regras de Transformação da Emissão, clique em **Adicionar Regra**.
10. No Assistente para Adicionar Regra de Afirmação de Transformação, selecione **Enviar Afirmações Utilizando uma Regra Personalizada** no menu pendente e clique em **Seguinte**.
11. Na caixa em Nome da regra de afirmação, introduza *Manter Utilizadores com Sessão Iniciada*.
12. Na caixa Regra personalizada, introduza:

        c:[Type == "http://schemas.microsoft.com/2014/03/psso"]
            => issue(claim = c);
    ![Nuvem](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip5.png)
13. Clique em **Concluir**.
14. Clique em **Aplicar**.
15. Clique em **OK**.
16. Feche a Gestão do AD FS.

### <a name="configure-azure-multi-factor-authentication-trusted-ips-with-federated-users"></a>Configurar IPs Fidedignos do Multi-Factor Authentication do Azure com Utilizadores Federados
Agora que as afirmações estão implementadas, podemos configurar os IPs fidedignos.

1. Inicie sessão no [Portal Clássico do Azure](https://manage.windowsazure.com).
2. No lado esquerdo, clique em **Active Directory**.
3. Sob Diretório, selecione o diretório onde pretende configurar IPs fidedignos.
4. No Diretório que selecionou, clique em **Configurar**.
5. Na secção Multi-Factor Authentication, clique em **Gerir definições do serviço**.
6. Na página Definições de Serviço, em IPs Fidedignos, selecione **Ignorar autenticação multifator para pedidos de utilizadores federados na minha intranet.**
   ![Cloud](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip6.png)
7. Clique em **Guardar**.
8. Depois de terem sido aplicadas as atualizações, clique em **Fechar**.

Já está! Neste momento, os utilizadores federados do Office 365 apenas têm de utilizar o MFA quando uma afirmação tiver origem fora da intranet da empresa.



<!--HONumber=Nov16_HO2-->


