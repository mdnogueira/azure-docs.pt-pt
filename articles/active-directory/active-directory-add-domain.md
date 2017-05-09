---
title: "Adicionar o seu nome de domínio personalizado ao Azure Active Directory | Microsoft Docs"
description: "Como adicionar nomes de domínio da sua empresa ao Azure Active Directory e como verificar o nome de domínio."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 35a6e20a-9907-432b-9d36-16b916a5c249
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/08/2016
ms.author: curtand;jeffsta
ms.translationtype: Human Translation
ms.sourcegitcommit: f1f3d6fa1ba29b0d43d29c7d9cccfc430fdd6f1e
ms.openlocfilehash: 9803b4e41223bc979e4c0b0a4f22586a441c28df
ms.contentlocale: pt-pt
ms.lasthandoff: 02/22/2017


---
# <a name="add-a-custom-domain-name-to-azure-active-directory"></a>Adicionar um nome de domínio personalizado ao Azure Active Directory
> [!div class="op_single_selector"]
> * [Portal do Azure](active-directory-domains-add-azure-portal.md)
> * [Portal Clássico do Azure](active-directory-add-domain.md)
> 
> 

Já tem um ou mais nomes de domínio que a sua organização utiliza para fins comerciais e os seus utilizadores iniciam sessão na rede da sua empresa utilizando o nome de domínio da sua empresa. Agora que está a utilizar o Azure Active Directory (Azure AD), pode também adicionar ao Azure AD o nome de domínio da sua empresa. Isto permite-lhe atribuir nomes de utilizador no diretório com que os seus utilizadores estão familiarizados, como “alice@contoso.com”. O processo é simples:

1. Adicione o nome de domínio personalizado ao seu diretório
2. Adicione uma entrada DNS do nome de domínio à entidade registo do nome de domínio
3. Verifique o nome de domínio personalizado no Azure AD

> [!NOTE]
> Se planear configurar o seu nome de domínio personalizado para ser utilizado com Serviços de Federação do Active Directory (AD FS) ou um serviço de tokens de segurança (STS) diferente na sua rede empresarial, siga as instruções em [Adicionar e configurar um domínio para a Federação com o Azure Active Directory](active-directory-add-domain-federated.md). Isto é útil se pretender sincronizar os utilizadores a partir do diretório da sua empresa para o Azure AD, e [sincronização de hash de palavra-passe](active-directory-aadconnectsync-implement-password-synchronization.md) não cumprir os requisitos.
> 
> 

## <a name="add-a-custom-domain-name-to-your-directory"></a>Adicionar um nome de domínio personalizado ao seu diretório
1. Inicie sessão no [Portal Clássico do Azure](https://manage.windowsazure.com/) com uma conta de utilizador que seja um administrador global do seu diretório do Azure AD.
2. Em **Active Directory**, abra o seu diretório e selecione o separador **Domínios**.
3. Na barra de comandos, selecione **Adicionar**. Introduza o nome do seu domínio personalizado, por exemplo, "contoso.com". Não se esqueça de incluir a extensão .com, .net ou outra extensão de nível superior e deixe a caixa de verificação "início de sessão único" (federação) desmarcada.
4. Selecione **Adicionar**.
5. Na segunda página do assistente Adicionar domínio, obtenha a entrada DNS que o Azure AD irá utilizar para verificar se a sua organização é proprietária do nome de domínio personalizado.

Agora que já adicionou o nome de domínio, o Azure AD tem de verificar que a sua organização é proprietária desse nome de domínio. Para que o Azure AD possa executar esta verificação, terá de adicionar uma entrada DNS no ficheiro de zona DNS para o nome de domínio. Esta tarefa é executada no Web site da entidade de registo de nome de domínio para o nome de domínio.

## <a name="add-the-dns-entry-at-the-domain-name-registrar-for-the-domain"></a>Adicione a entrada DNS na entidade de registo do nome de domínio para o domínio
O passo seguinte para utilizar o seu nome de domínio personalizado com o Azure AD é atualizar o ficheiro de zona DNS para o domínio. Isto permite ao Azure AD verificar se a sua organização é proprietária do nome de domínio personalizado.

1. Inicie sessão na entidade de registo de nome de domínio para o domínio. Se não tiver acesso para atualizar a entrada DNS, peça à pessoa ou equipa na sua organização que tenham este acesso para concluir o passo 2 e para informá-lo quando for concluído.
2. Atualize o ficheiro de zona DNS para o domínio ao adicionar a entrada DNS fornecida pelo Azure AD. Esta entrada DNS permite ao Azure AD verificar a sua propriedade do domínio. A entrada DNS não irá alterar quaisquer comportamentos, como o encaminhamento de correio ou o alojamento web.

Para obter ajuda quando adicionar a entrada DNS, leia o artigo [Instruções para adicionar uma entrada DNS em entidades de registo DNS populares](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)

## <a name="verify-the-domain-name-with-azure-ad"></a>Verificar o nome de domínio com o Azure AD
Depois de adicionar a entrada DNS, está pronto para verificar o nome de domínio com o Azure AD.

Se ainda tiver o assistente **Adicionar domínio** aberto, selecione **Verificar** na terceira página do assistente. Quando seleciona **Verificar**, o Azure AD irá procurar a entrada DNS no ficheiro de zona DNS do domínio. O Azure AD só pode verificar o nome de domínio depois de os registos DNS terem sido propagados. Esta propagação frequentemente demora apenas alguns segundos, mas, por vezes, pode demorar uma hora ou mais. Se a verificação não funcionar à primeira, tente de novo mais tarde.

Se o assistente **Adicionar domínio** já não estiver aberto, pode verificar o domínio no [Portal Clássico do Azure](https://manage.windowsazure.com/):

1. Inicie sessão com uma conta de utilizador que seja um administrador global do seu diretório do Azure AD.
2. Abra o seu diretório e selecione o separador **Domínios**.
3. Selecione o nome de domínio que pretende verificar e selecione **Verificar** na barra de comando.
4. Selecione **Verificar** na caixa de diálogo para concluir a verificação.

Já pode [atribuir nomes de utilizador que incluem o seu nome de domínio personalizado](active-directory-add-domain-add-users.md).

## <a name="troubleshooting"></a>Resolução de problemas
Se não conseguir verificar um nome de domínio personalizado, tente o seguinte procedimento. Vamos começar com as mais comuns e prosseguir para as menos comuns.

1. **Aguarde uma hora**. Os registos DNS têm de ser propagados antes de o Azure AD poder verificar o domínio. Esta ação pode demorar uma hora ou mais.
2. **Certifique-se de que o registo DNS foi introduzido e que está correto**. Conclua este passo no site da entidade de registo de nome do domínio. O Azure AD não consegue verificar o nome de domínio se a entrada DNS não estiver presente no ficheiro de zona DNS ou se não for uma correspondência exata com a entrada DNS que o Azure AD lhe forneceu. Se não tiver acesso para atualizar os registos DNS do domínio na entidade de registo de nome de domínio, partilhe a entrada DNS com a pessoa ou equipa na sua organização que tenham este acesso e peça-lhes para adicionar a entrada DNS.
3. **Elimine o nome de domínio a partir de outro diretório no Azure AD**. Um nome de domínio só pode ser verificado num único diretório. Se um nome de domínio foi verificado anteriormente noutro diretório, tem de ser eliminado aí antes de poder ser verificado no seu novo diretório. Para saber mais sobre como eliminar nomes de domínio, veja o artigo [Gerir nomes de domínio personalizados](active-directory-add-manage-domain-names.md).

## <a name="add-more-custom-domain-names"></a>Adicionar mais nomes de domínio personalizados
Se a sua organização utilizar vários nomes de domínio personalizados, como “contoso.com” e “contosobank.com”, pode adicioná-los até um máximo de 900 nomes de domínio. Utilize os mesmos passos neste artigo para adicionar cada um dos seus nomes de domínio.

## <a name="next-steps"></a>Passos seguintes
* [Atribuir nomes de utilizador que incluem o seu nome de domínio personalizado](active-directory-add-domain-add-users.md)
* [Gerir nomes de domínio personalizado](active-directory-add-manage-domain-names.md)
* [Saber mais acerca de conceitos de gestão de domínios no Azure AD](active-directory-add-domain-concepts.md)
* [Mostrar a imagem corporativa da sua empresa quando os seus utilizadores iniciam sessão](active-directory-add-company-branding.md)
* [Utilizar o PowerShell para gerir os nomes de domínio no Azure AD](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)


