---
title: "Adicionar o seu nome de domínio personalizado e configurar o seu início de sessão federado no Azure Active Directory | Microsoft Docs"
description: "Como adicionar nomes de domínio da sua empresa ao Azure Active Directory e como configurar o início de sessão federado entre o Azure Active Directory e a sua solução de federação no local."
services: active-directory
documentationcenter: 
author: jeffsta
manager: femila
editor: 
ms.assetid: 27126c7e-e6d6-4ef3-a4fb-f5f0706e749d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/04/2016
ms.author: curtand;jeffsta
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 77d34a6ebce12465a15de158b9160a03db58c48e


---
# <a name="add-your-custom-domain-name-to-azure-active-directory"></a>Adicionar o seu nome de domínio personalizado ao Azure Active Directory
Pode configurar um nome de domínio personalizado, como "contoso.com", para que os utilizadores em contoso.com possam ter uma experiência de início de sessão único federado a partir da sua rede empresarial. Se já tiver Serviços de Federação do Active Directory (AD FS) ou um servidor de federação diferente em execução na sua rede empresarial, pode configurar o Azure AD para utilizar o seu nome de domínio personalizado utilizando a ferramenta do Azure AD Connect. Também pode utilizar o Azure AD Connect para implementar um novo ambiente AD FS e configurá-lo para o início de sessão único federado para o Azure AD.

Se não tiver e não planear implementar o AD FS ou outro servidor de federação, siga estas instruções: [Adicionar um nome de domínio personalizado ao Azure Active Directory](active-directory-add-domain.md).

## <a name="add-a-custom-domain-name-to-your-directory"></a>Adicionar um nome de domínio personalizado ao seu diretório
1. Inicie sessão no [Portal Clássico do Azure](https://manage.windowsazure.com/) com uma conta de utilizador que seja um administrador global do seu diretório do Azure AD.
2. Em **Active Directory**, abra o seu diretório e selecione o separador **Domínios**.
3. Na barra de comandos, selecione **Adicionar** e, em seguida, introduza o nome do seu domínio personalizado, como “contoso.com”. Certifique-se de que inclui .com .net ou outra extensão de nível superior.
4. Selecione a caixa de verificação **Tenciono configurar este domínio para início de sessão único com o meu Active Directory local**.
5. Selecione **Adicionar**.

Execute a ferramenta Azure AD Connect para obter a entrada DNS que o Azure AD irá utilizar para verificar o domínio. Verá a entrada DNS no passo **Domínio do Azure AD** no assistente. Pode ver como funciona esse passo no assistente [nestas instruções](connect/active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation). Se não tiver a ferramenta Azure AD Connect, pode [transferi-lo aqui](http://go.microsoft.com/fwlink/?LinkId=615771).

## <a name="add-the-dns-entry-at-the-domain-name-registrar-for-the-domain"></a>Adicione a entrada DNS na entidade de registo do nome de domínio para o domínio
O passo seguinte para utilizar o seu nome de domínio personalizado com o Azure AD é atualizar o ficheiro de zona DNS para o domínio. Isto permite ao Azure AD verificar se a sua organização é proprietária do nome de domínio personalizado.

1. Inicie sessão no site da entidade de registo do nome de domínio para o nome de domínio. Se não tiver acesso para o fazer, peça à pessoa ou equipa na sua organização que tenham este acesso para concluir o passo 2 e para informá-lo quando for concluído.
2. Atualize o ficheiro de zona DNS para o domínio ao adicionar a entrada DNS fornecida pelo Azure AD. Esta entrada DNS permite ao Azure AD verificar a sua propriedade do domínio. A entrada DNS não irá alterar quaisquer comportamentos, como o encaminhamento de correio ou o alojamento web.

Para obter ajuda com este passo, leia o artigo [Instruções para adicionar uma entrada DNS em entidades de registo DNS populares](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)

## <a name="verify-the-domain-name-with-azure-ad"></a>Verificar o nome de domínio com o Azure AD
Depois de adicionar a entrada DNS, está pronto para verificar o nome de domínio com o Azure AD.

Para verificar o domínio, selecione **Seguinte** no passo **Domínio do Azure AD** do assistente do Azure AD Connect. O Azure AD irá procurar a entrada DNS no ficheiro de zona DNS do domínio. O Azure AD só verifica o nome de domínio depois de os registos DNS terem sido propagados. A propagação frequentemente demora apenas alguns segundos, mas por vezes, pode demorar uma hora ou mais. Se a verificação não funcionar à primeira, tente de novo mais tarde.

Em seguida, prossiga com os restantes passos do assistente do Azure AD Connect. Isto irá sincronizar os utilizadores a partir do seu Windows Server AD para o Azure AD. Os utilizadores sincronizados no domínio que configurou para a federação poderão obter uma experiência de início de sessão único federado a partir da sua rede empresarial para o Azure AD.

## <a name="troubleshooting"></a>Resolução de problemas
Se não conseguir verificar um nome de domínio personalizado, tente o seguinte procedimento. Vamos começar com as mais comuns e prosseguir para as menos comuns.

1. **Aguarde uma hora**. Os registos DNS têm de ser propagados antes de o Azure AD poder verificar o domínio. Esta ação pode demorar uma hora ou mais.
2. **Certifique-se de que o registo DNS foi introduzido e que está correto**. Conclua este passo no site da entidade de registo de nome do domínio. O Azure AD não consegue verificar o nome de domínio se a entrada DNS não estiver presente no ficheiro de zona DNS ou se não for uma correspondência exata com a entrada DNS que o Azure AD lhe forneceu. Se não tiver acesso para atualizar os registos DNS do domínio na entidade de registo de nome de domínio, partilhe a entrada DNS com a pessoa ou equipa na sua organização que tenham este acesso e peça-lhes para adicionar a entrada DNS.
3. **Elimine o nome de domínio a partir de outro diretório no Azure AD**. Um nome de domínio só pode ser verificado num único diretório. Se um nome de domínio foi verificado anteriormente noutro diretório, tem de ser eliminado aí antes de poder ser verificado no seu novo diretório. Para saber mais sobre como eliminar nomes de domínio, veja o artigo [Gerir nomes de domínio personalizados](active-directory-add-manage-domain-names.md).

## <a name="add-more-custom-domain-names"></a>Adicionar mais nomes de domínio personalizados
Se a sua organização utilizar vários nomes de domínio personalizados, como “contoso.com” e “contosobank.com”, pode adicioná-los até um máximo de 900 nomes de domínio. Utilize os mesmos passos neste artigo para adicionar cada um dos seus nomes de domínio.

## <a name="next-steps"></a>Passos seguintes
* [Gerir nomes de domínio personalizado](active-directory-add-manage-domain-names.md)
* [Saber mais acerca de conceitos de gestão de domínios no Azure AD](active-directory-add-domain-concepts.md)
* [Mostrar a imagem corporativa da sua empresa quando os seus utilizadores iniciam sessão](active-directory-add-company-branding.md)
* [Utilizar o PowerShell para gerir os nomes de domínio no Azure AD](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)




<!--HONumber=Dec16_HO1-->


