---
title: Proteger recursos da nuvem com o Multi-Factor Authentication do Azure e o AD FS
description: Esta é a página do Multi-Factor Authentication do Azure que descreve como começar a utilizar o MFA do Azure e o AD FS na nuvem.
services: multi-factor-authentication
documentationcenter: ''
author: kgremban
manager: femila
editor: curtland

ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/04/2016
ms.author: kgremban

---
# Proteger recursos da nuvem com o Multi-Factor Authentication do Azure e o AD FS
Se a sua organização estiver federada no Azure Active Directory e tiver recursos que sejam acedidos pelo Azure AD, pode utilizar o Multi-Factor Authentication do Azure ou os Serviços de Federação do Active Directory para proteger estes recursos. Utilize os procedimentos abaixo para proteger os recursos do Azure Active Directory com o Multi-Factor Authentication do Azure ou os Serviços de Federação do Active Directory.

## Para proteger os recursos do Azure AD utilizando o AD FS, efetue o seguinte:
1. Utilize os passos descritos em [Ativar autenticação multifator](multi-factor-authentication-get-started-cloud.md#turn-on-multi-factor-authentication-for-users) para os utilizadores ativarem uma conta.
2. Utilize o procedimento seguinte para configurar uma regra de afirmações:

![Nuvem](./media/multi-factor-authentication-get-started-adfs-cloud/adfs1.png)

* Inicie a consola de Gestão do AD FS.
* Navegue para Confianças de Entidades Confiadoras e clique com o botão direito do rato na Confiança da Entidade Confiadora. Selecione Editar Regras de Afirmação...
* Clique em Adicionar Regra...
* No menu pendente, selecione Enviar Afirmações Utilizando uma Regra Personalizada e clique em Seguinte.
* Introduza um nome para a regra de afirmação.
* Em Regra Rersonalizada, adicione o seguinte:

        => issue(Type = "http://schemas.microsoft.com/claims/authnmethodsreferences", Value = "http://schemas.microsoft.com/claims/multipleauthn");

    Afirmação correspondente:

        <saml:Attribute AttributeName="authnmethodsreferences" AttributeNamespace="http://schemas.microsoft.com/claims">
        <saml:AttributeValue>http://schemas.microsoft.com/claims/multipleauthn</saml:AttributeValue>
        </saml:Attribute>
* Clique em OK. Clique em Concluir. Feche a consola de Gestão do AD FS.

Em seguida, os utilizadores podem concluir o início de sessão utilizando o método no local (por exemplo, um smart card).

## IPs Fidedignos para utilizadores federados
Os IPs Fidedignos permitem aos administradores ignorar a autenticação multifator para o endereço IP específico ou para os utilizadores federados que tenham pedidos com origem na sua própria intranet. As secções seguintes descrevem como configurar IPs Fidedignos do Multi-Factor Authentication do Azure com utilizadores federados e ignorar a autenticação multifator, quando um pedido tem origem na intranet dos utilizadores federados.  Isto é feito ao configurar o AD FS para utilizar um pass-through ou filtrar um modelo de afirmação de entrada com o tipo de afirmação Dentro da Rede Empresarial.  Este exemplo utiliza o Office 365 para as Confianças de Entidades Confiadoras.

### Configurar regras de afirmações do AD FS
A primeira coisa a fazer é configurar as afirmações do AD FS. Vamos criar duas regras de afirmações, uma para o tipo de afirmação Dentro da Rede Empresarial e uma adicional para manter os utilizadores com sessão iniciada.

1. Abra a Gestão do AD FS.
2. À esquerda, selecione Confianças de Entidades Confiadoras.
3. No meio, clique com o botão direito do rato na Plataforma de Identidade do Microsoft Office 365 e selecione **Editar Regras de Afirmação…**
   ![Nuvem](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip1.png)
4. Em Regras de Transformação de Emissão, clique em **Adicionar Regra.**
   ![Nuvem](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip2.png)
5. No Assistente para Adicionar Regras de Afirmações de Transformação, selecione Pass-Through ou Filtrar uma Afirmação de Entrada no menu pendente e clique em Seguinte.
   ![Nuvem](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip3.png)
6. Na caixa junto ao nome da regra de afirmação, atribua um nome à regra. Por exemplo: InsideCorpNet.
7. No menu pendente, junto ao tipo de afirmação de entrada, selecione Dentro da Rede Empresarial.
   ![Nuvem](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip4.png)
8. Clique em Concluir.
9. Em Regras de Transformação de Emissão, clique em **Adicionar Regra.**
10. No Assistente para Adicionar Regras de Afirmações de Transformação, selecione Enviar Afirmações Utilizando uma Regra Personalizada no menu pendente e clique em Seguinte.
11. Na caixa em Nome da regra de afirmação, introduza Manter Utilizadores com Sessão Iniciada.
12. Na caixa Regra personalizada, introduza:
    
        c:[Type == "http://schemas.microsoft.com/2014/03/psso"]
            => issue(claim = c);
    ![Nuvem](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip5.png)
13. Clique em **Concluir**.
14. Clique em **Aplicar**.
15. Clique em **OK**.
16. Feche a Gestão do AD FS.

### Configurar IPs Fidedignos do Multi-Factor Authentication do Azure com Utilizadores Federados
Agora que as afirmações estão ativadas, vamos configurar IPs fidedignos.

1. Inicie sessão no Portal de Gestão do Azure.
2. No lado esquerdo, clique em Active Directory.
3. Em Diretório, clique no diretório no qual pretende configurar IPs Fidedignos.
4. No Diretório que selecionou, clique em Configurar.
5. Na secção Autenticação multifator, selecione Gerir definições do serviço.
6. Na página Definições do Serviço, em IPs Fidedignos, selecione **Para pedidos de utilizadores federados com origem na minha intranet.**
   ![Nuvem](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip6.png)
7. Clique em Guardar.
8. Depois de terem sido aplicadas as atualizações, clique em Fechar.

Já está! Neste momento, os utilizadores federados do Office 365 apenas têm de utilizar o MFA quando uma afirmação tiver origem fora da intranet da empresa.

<!--HONumber=Sep16_HO3-->


