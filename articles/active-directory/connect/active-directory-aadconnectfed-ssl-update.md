---
title: "O Azure AD Connect: Atualizar o certificado SSL para um farm de serviços de Federação do Active Directory (AD FS) | Microsoft Docs"
description: Este documento detalhes os passos para atualizar o certificado SSL de um farm do AD FS utilizando o Azure AD Connect.
services: active-directory
keywords: "do Azure ad connect, atualização de ssl do AD FS, atualização de certificados do AD FS, certificado do adfs de alteração, novo certificado do AD FS, certificado do adfs, atualização adfs certificado ssl, adfs do certificado de ssl de atualização, configure o certificado de ssl do AD FS, adfs, ssl, certificado, o serviço de adfs certificado de comunicação, Federação de atualização, configurar a Federação, aad connect"
authors: anandyadavmsft
manager: femila
editor: billmath
ms.assetid: 7c781f61-848a-48ad-9863-eb29da78f53c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: anandy
ms.openlocfilehash: 87807a203d71b3abfe3e93132eb7d0b82b14b4ee
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="update-the-ssl-certificate-for-an-active-directory-federation-services-ad-fs-farm"></a>Atualizar o certificado SSL para um farm de serviços de Federação do Active Directory (AD FS)

## <a name="overview"></a>Descrição geral
Este artigo descreve como pode utilizar o Azure AD Connect para atualizar o certificado SSL para um farm de serviços de Federação do Active Directory (AD FS). Pode utilizar a ferramenta Azure AD Connect para atualizar facilmente o certificado SSL para o farm do AD FS, mesmo que o utilizador iniciar sessão no método selecionado não é do AD FS.

Pode executar toda a operação de atualização do certificado SSL para o farm do AD FS em todos os servidores de Proxy de aplicações Web (WAP) em três passos simples e Federação:

![Três passos](./media/active-directory-aadconnectfed-ssl-update/threesteps.png)


>[!NOTE]
>Para saber mais sobre os certificados que são utilizados pelo AD FS, consulte [certificados compreender utilizados pelo AD FS](https://technet.microsoft.com/library/cc730660.aspx).

## <a name="prerequisites"></a>Pré-requisitos

* **Farm do AD FS**: Certifique-se de que o farm do AD FS é baseado no Windows Server 2012 R2 ou posterior.
* **O Azure AD Connect**: Certifique-se de que a versão do Azure AD Connect 1.1.443.0 ou posterior. Irá utilizar a tarefa **atualização AD certificado SSL FS**.

![Tarefa de atualização de SSL](./media/active-directory-aadconnectfed-ssl-update/updatessltask.png)

## <a name="step-1-provide-ad-fs-farm-information"></a>Passo 1: Fornecer informações de farm do AD FS

O Azure AD Connect tenta obter informações sobre o farm do AD FS automaticamente pelo:
1. Consultar as informações de farm do AD FS (Windows Server 2016 ou posterior).
2. As informações de referência da execução anterior, que são armazenadas localmente com o Azure AD Connect.

Pode modificar a lista de servidores que são apresentados ao adicionar ou remover servidores para refletir a atual configuração do farm do AD FS. Assim que as informações do servidor for fornecidas, o Azure AD Connect apresenta a conectividade e o estado atual do certificado SSL.

![Informações do servidor do AD FS](./media/active-directory-aadconnectfed-ssl-update/adfsserverinfo.png)

Se a lista contém um servidor que já não faz parte do farm do AD FS, clique em **remover** ao eliminar o servidor da lista de servidores no farm do AD FS.

![Servidor offline na lista](./media/active-directory-aadconnectfed-ssl-update/offlineserverlist.png)

>[!NOTE]
> Remover um servidor da lista de servidores para um farm do AD FS no Azure AD Connect é uma operação local e atualiza as informações para o farm do AD FS que o Azure AD Connect mantém localmente. O Azure AD Connect não modificar a configuração do AD FS de modo a refletir a alteração.    

## <a name="step-2-provide-a-new-ssl-certificate"></a>Passo 2: Fornecer um novo certificado SSL

Depois de ter confirmado as informações sobre os servidores do farm do AD FS, o Azure AD Connect pede-lhe para o novo certificado SSL. Forneça um certificado PFX protegido por palavra-passe para continuar a instalação.

![Certificado SSL](./media/active-directory-aadconnectfed-ssl-update/certificate.png)

Depois de fornecer o certificado, o Azure AD Connect passa através de uma série de pré-requisitos. Verificar o certificado para se certificar de que o certificado está correto para o farm do AD FS:

-   O nome de requerente alternativo/nome do requerente do certificado é é o mesmo que o nome do serviço de Federação ou é um certificado de caráter universal.
-   O certificado é válido durante mais de 30 dias.
-   A cadeia de fidedignidade do certificado é válida.
-   O certificado é protegido de palavra-passe.

## <a name="step-3-select-servers-for-the-update"></a>Passo 3: Selecione servidores para a atualização

No próximo passo, selecione os servidores que têm de ter o certificado SSL atualizado. Servidores que estejam offline não podem ser selecionados para a atualização.

![Selecione os servidores ao atualizar](./media/active-directory-aadconnectfed-ssl-update/selectservers.png)

Depois de concluir a configuração, o Azure AD Connect apresenta a mensagem que indica o estado da atualização e fornece uma opção para verificar o AD FS início de sessão.

![Conclusão da configuração](./media/active-directory-aadconnectfed-ssl-update/configurecomplete.png)   

## <a name="faqs"></a>Perguntas mais frequentes

* **O que deve ser o nome do requerente do certificado para o novo certificado SSL do AD FS?**

    O Azure AD Connect verifica se o nome de requerente alternativo/nome de requerente do certificado contém o nome do serviço de Federação. Por exemplo, se o nome do serviço de Federação for fs.contoso.com, o nome de requerente alternativo/nome do requerente tem de ser fs.contoso.com.  Certificados de caráter universal também são aceites.

* **Por que razão estou posso pedido as credenciais novamente na página do servidor do WAP?**

    Se as credenciais que fornece para se ligar aos servidores do AD FS também não tem o privilégio para gerir os servidores do WAP, em seguida, o Azure AD Connect pede-lhe as credenciais que têm privilégios administrativos nos servidores do WAP.

* **O servidor é mostrado como offline. O que devo fazer?**

    O Azure AD Connect não pode efetuar qualquer operação se o servidor estiver offline. Se o servidor fizer parte do farm do AD FS, em seguida, verifique a conectividade ao servidor. Após ter resolvido o problema, prima o ícone de atualização para atualizar o estado do assistente. Se o servidor fazia parte do farm anteriormente, mas agora já não existe, clique em **remover** eliminá-la na lista de servidores que o Azure AD Connect mantém. Remover o servidor da lista no Azure AD Connect não altera a configuração do AD FS. Se estiver a utilizar o AD FS no Windows Server 2016 ou posterior, os permanecem de servidor nas definições de configuração e será apresentado novamente da próxima vez que a tarefa é executada.

* **Pode atualizar um subconjunto dos meus servidores do farm com o novo certificado SSL?**

    Sim. É sempre possível executar a tarefa **atualizar o certificado de SSL** novamente para atualizar os restantes servidores. No **selecione os servidores para SSL certificado atualização** página, pode ordenar a lista de servidores no **data de expiração de SSL** facilmente aceder aos servidores que não são ainda atualizados.

* **Posso removido do servidor na execução anterior, mas ser continuam a ser apresentado como offline e listadas na página servidores do AD FS. Por que motivo é o servidor de offline ainda existe mesmo depois de posso removido?**

    Remover o servidor da lista no Azure AD Connect não removê-lo na configuração do AD FS. O Azure AD Connect referencia o AD FS (Windows Server 2016 ou posterior) para qualquer informação sobre o farm. Se o servidor ainda está presente na configuração do AD FS, serão apresentado na lista.  

## <a name="next-steps"></a>Passos seguintes

- [Azure AD Connect e a federação](active-directory-aadconnectfed-whatis.md)
- [Gestão de serviços de Federação do Active Directory e personalização com o Azure AD Connect](active-directory-aadconnect-federation-management.md)
