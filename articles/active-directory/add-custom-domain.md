---
title: "Adicionar um domínio personalizado ao Azure AD | Microsoft Docs"
description: "Explica como adicionar um domínio personalizado no Azure Active Directory."
services: active-directory
author: curtand
manager: femila
ms.assetid: 0a90c3c5-4e0e-43bd-a606-6ee00f163038
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: curtand
ms.reviewer: jsnow
ms.custom: it-pro
ms.openlocfilehash: 2ea119b56f467f57b5929dec05bfd645028578ef
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="quickstart-add-a-custom-domain-name-to-azure-active-directory"></a>Guia de introdução: Adicionar um nome de domínio personalizado ao Azure Active Directory

Cada diretório do Azure AD é fornecido com um nome de domínio inicial sob a forma de *domainname*. c o m. O nome de domínio inicial não pode ser alterado ou eliminado, mas pode adicionar o seu nome de domínio empresarial para o Azure AD também. Por exemplo, a organização tem provavelmente outros nomes de domínio utilizados para fazer negócios e os utilizadores que inicie sessão com o nome de domínio empresarial. Adicionar nomes de domínio personalizado ao Azure AD permite-lhe atribuir nomes de utilizador no diretório que estão familiarizados aos seus utilizadores, tais como 'alice@contoso.com.' em vez de ' alice @*<domain name>*. c o m ". O processo é simples:

1. Adicione o nome de domínio personalizado ao seu diretório
2. Adicione uma entrada DNS do nome de domínio à entidade registo do nome de domínio
3. Verifique o nome de domínio personalizado no Azure AD

## <a name="add-the-custom-domain-name-to-your-directory"></a>Adicione o nome de domínio personalizado ao seu diretório
1. Iniciar sessão para o [portal do Azure](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) com uma conta que seja um administrador global do diretório.
2. No lado esquerdo, selecione **nomes de domínio**.
3. No  ***nome do diretório* -nomes de domínio**, selecione **adicionar**.
   
   ![Selecione o comando Adicionar](./media/active-directory-domains-add-azure-portal/add-command.png)
5. No **nome de domínio**, introduza o nome do seu domínio personalizado na caixa, tais como "contoso.com" e, em seguida, selecione **Adicionar domínio**. Certifique-se de que inclui .com .net ou outra extensão de nível superior.
6. No ***domainname*** (ou seja, o novo nome de domínio é o título), reunir as informações de entrada DNS a utilizar mais tarde para verificar o nome de domínio personalizado no Azure AD.
   
   ![obter as informações de entrada DNS](./media/active-directory-domains-add-azure-portal/get-dns-info.png)

> [!TIP]
> Se planeia federar no local do Windows Server AD com o Azure AD, em seguida, tem de selecionar o **tenciono configurar este domínio para o início de sessão único com o meu Active Directory local** caixa de verificação ao executar a ferramenta Azure AD Connect Sincronize os diretórios. Também tem de registar o mesmo nome de domínio que seleciona para a Federação com o seu diretório no local no **domínio do Azure AD** passo no assistente. Pode ver como funciona esse passo no assistente [nestas instruções](./connect/active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation). Se não tiver a ferramenta Azure AD Connect, pode [transferi-lo aqui](http://go.microsoft.com/fwlink/?LinkId=615771).

## <a name="add-a-dns-entry-for-the-domain-name-at-the-domain-name-registrar"></a>Adicione uma entrada DNS do nome de domínio à entidade registo do nome de domínio
O passo seguinte para utilizar o seu nome de domínio personalizado com o Azure AD é atualizar o ficheiro de zona DNS para o domínio. Azure AD, em seguida, pode verificar que a sua organização é proprietária do nome de domínio personalizado. Pode utilizar [DNS do Azure](https://docs.microsoft.com/azure/dns/dns-getstarted-portal) para os registos DNS do Azure/Office 365/externa no Azure, ou adicione a entrada DNS em [uma entidade de registo DNS diferente](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/).

1. Inicie sessão na entidade de registo de nome de domínio para o domínio. Se não tiver acesso para atualizar a entrada DNS, peça à pessoa ou equipa na sua organização que tenham este acesso para concluir o passo 2 e para informá-lo quando for concluído.
2. Atualize o ficheiro de zona DNS para o domínio ao adicionar a entrada DNS fornecida pelo Azure AD. A entrada DNS não irá alterar quaisquer comportamentos, como o encaminhamento de correio ou o alojamento web.

## <a name="verify-the-custom-domain-name-in-azure-ad"></a>Verifique o nome de domínio personalizado no Azure AD
Depois de adicionar a entrada DNS, está pronto para verificar o nome de domínio com o Azure AD. Um nome de domínio pode ser verificado apenas depois dos registos DNS terem sido propagados. Esta propagação frequentemente demora apenas alguns segundos, mas, por vezes, pode demorar uma hora ou mais. Se a verificação não funcionar à primeira, tente de novo mais tarde.

1. Inicie sessão no [do Azure AD](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) com uma conta que seja um administrador global do diretório.
2. No lado esquerdo, selecione **nomes de domínio**.
3. No  ***nome do diretório* -nomes de domínio**, selecione o **Adicionar nome de domínio** comando. 
  ![Selecione o comando Adicionar](./media/active-directory-domains-add-azure-portal/add-command.png)
3. No  ***nome do diretório* -nomes de domínio**, selecione o nome de domínio não verificado que pretende verificar.
4. No ***domainname*** (ou seja, o nome de domínio selecionado é o título), selecione **verifique** para concluir a verificação.

Já pode [atribuir nomes de utilizador que incluem o seu nome de domínio personalizado](active-directory-users-create-azure-portal.md). Pode criar contas de utilizador baseadas na nuvem ou atualização anteriormente sincronizado informações de conta de utilizador no local, utilizando o seu nome de domínio personalizado. Também pode alterar utilizador sincronizado conta de domínio sufixo informações utilizando [Microsoft PowerShell](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains) ou [Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations).

> [!TIP]
> Pode adicionar a cópia de segurança para um máximo de 900 nomes de domínio gerido. Se pretender configurar todos os seus domínios para federação no local com o Active Directory, pode adicionar cópias de segurança para um máximo de 450 nomes de domínio em cada diretório. Para obter mais informações, consulte [Federated e nomes de domínio geridos](https://docs.microsoft.com/azure/active-directory/active-directory-add-domain-concepts#federated-and-managed-domain-names).

## <a name="troubleshooting"></a>Resolução de problemas
Se não conseguir verificar um nome de domínio personalizado, tente os seguintes passos de resolução de problemas:

1. **Aguarde uma hora**. Registos DNS devem ser propagados antes do Azure AD pode verificar o domínio. Este processo pode demorar uma hora ou mais.
2. **Certifique-se de que o registo DNS foi introduzido e que está correto**. Conclua este passo no site da entidade de registo de nome do domínio. Azure AD não é possível verificar o nome de domínio se 
  * A entrada DNS não está presente no ficheiro de zona DNS
  * Não é uma correspondência exata com a entrada DNS do Azure AD lhe forneceu. 
  
  Se não tiver acesso para atualizar os registos DNS do domínio na entidade de registo de nome de domínio, partilhe a entrada DNS com a pessoa ou equipa na sua organização que tenham este acesso e peça-lhes para adicionar a entrada DNS.
3. **Elimine o nome de domínio a partir de outro diretório no Azure AD**. Um nome de domínio só pode ser verificado num único diretório. Se um nome de domínio é verificado atualmente num diretório diferente, não é possível verificá-lo no seu novo diretório até serem eliminada dos outros. Para saber mais sobre como eliminar nomes de domínio, veja o artigo [Gerir nomes de domínio personalizados](active-directory-domains-manage-azure-portal.md).    

## <a name="add-more-custom-domain-names"></a>Adicionar mais nomes de domínio personalizados
Se a organização utilizar vários nomes de domínio personalizado, como "contoso.com" e "contosobank.com", pode adicioná-los, até um máximo de 900 nomes de domínio. Os passos neste artigo podem ajudar a adicionar cada um dos nomes de domínio.

### <a name="learn-more"></a>Saiba mais
[Descrição geral conceptual dos nomes de domínio personalizados no Azure AD](active-directory-add-domain-concepts.md)

[Gerir nomes de domínio personalizado](active-directory-domains-manage-azure-portal.md)

## <a name="next-steps"></a>Passos seguintes
Este guia de introdução, aprendeu como adicionar um domínio personalizado ao Azure AD. 

Pode utilizar a hiperligação seguinte para adicionar um novo domínio personalizado no Azure AD a partir do portal do Azure.

> [!div class="nextstepaction"]
> [Adicionar um domínio personalizado](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/QuickStart) 