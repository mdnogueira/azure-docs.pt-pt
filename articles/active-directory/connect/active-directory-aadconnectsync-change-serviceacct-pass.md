---
title: "Sincronização do Azure AD Connect: alterar a conta de serviço do Azure AD Connect sincronização | Microsoft Docs"
description: "Este documento de tópico descreve a chave de encriptação e como abandoná-lo Depois da palavra-passe é alterada."
services: active-directory
keywords: "Conta de serviço de sincronização do Azure AD, a palavra-passe"
documentationcenter: 
author: cychua
manager: femila
editor: 
ms.assetid: 76b19162-8b16-4960-9e22-bd64e6675ecc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: bf6234d0810f870909957ee1c1e33c225a4922b9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="changing-the-azure-ad-connect-sync-service-account-password"></a>Alterar a palavra-passe da conta de serviço sincronização do Azure AD Connect
Se alterar a palavra-passe da conta de serviço sincronização do Azure AD Connect, o serviço de sincronização não será possível iniciar corretamente até ter abandonado a chave de encriptação e a reinicialização da palavra-passe da conta de serviço sincronização do Azure AD Connect. 

O Azure AD Connect, como parte dos serviços de sincronização utiliza uma chave de encriptação para armazenar as palavras-passe das contas de serviço do AD DS e AD do Azure.  Estas contas são encriptadas antes que estão armazenados na base de dados. 

A chave de encriptação utilizada é protegida por [Windows Data Protection (DPAPI)](https://msdn.microsoft.com/library/ms995355.aspx). DPAPI protege a encriptação chaves utilizando o **palavra-passe da conta de serviço de sincronização do Azure AD Connect**. 

Se precisar de alterar a palavra-passe da conta de serviço pode utilizar os procedimentos [abandonar a chave de encriptação do Azure AD Connect sincronização](#abandoning-the-azure-ad-connect-sync-encryption-key) para realizar esta tarefa.  Estes procedimentos também devem ser utilizados se precisar de abandonar a chave de encriptação por qualquer motivo.

##<a name="issues-that-arise-from-changing-the-password"></a>Problemas que resultam de alterar a palavra-passe
Existem dois aspetos que precisam de ser efetuada quando alterar a palavra-passe da conta de serviço.

Primeiro, precisa de alterar a palavra-passe com o Gestor de controlo do serviço do Windows.  Até que este problema seja resolvido, verá os seguintes erros:


- Se tentar iniciar o serviço de sincronização no Gestor de controlo de serviço do Windows, receberá o erro "**Windows não conseguiu iniciar o serviço do Microsoft Azure AD Sync no computador Local**". **Erro 1069: O serviço não foram iniciados devido a uma falha de início de sessão.** "
- No Visualizador de eventos do Windows, o registo de eventos do sistema contém um erro com **7038 de ID de evento** e a mensagem "**ADSync o serviço não conseguiu iniciar sessão, tal como acontece com a palavra-passe atualmente configurada devido ao seguinte erro: O nome de utilizador ou palavra-passe está incorreto.**"

Segundo, sob condições específicas, se a palavra-passe for atualizada, o serviço de sincronização pode já não é obter a chave de encriptação através do DPAPI. Sem a chave de encriptação, o serviço de sincronização não é possível desencriptar as palavras-passe necessárias para sincronizar do local AD e o Azure AD.
Verá erros, tais como:

- Em Gestor de controlo de serviço de Windows, se tentar iniciar o serviço de sincronização e não é possível obter a chave de encriptação, falhar com o erro "* * Windows não conseguiu iniciar a sincronização do Microsoft Azure AD no computador Local. Para obter mais informações, consulte o registo de eventos do sistema. Se se tratar de um serviço de terceiros, contacte o fornecedor de serviço e consulte o código de erro específicas do serviço * *-21451857952 ***. "
- No Visualizador de eventos do Windows, o registo de eventos da aplicação contém um erro com **6028 de ID de evento** e mensagem de erro *"**a chave de encriptação do servidor não pode ser acedida.* *"*

Para garantir que não receberá estes erros, siga os procedimentos [abandonar a chave de encriptação do Azure AD Connect sincronização](#abandoning-the-azure-ad-connect-sync-encryption-key) quando alterar a palavra-passe.
 
## <a name="abandoning-the-azure-ad-connect-sync-encryption-key"></a>Abandonar a chave de encriptação do Azure AD Connect sincronização
>[!IMPORTANT]
>Os procedimentos seguintes aplicam-se apenas ao Azure AD Connect compilação 1.1.443.0 ou anterior.

Utilize os procedimentos seguintes para abandonar a chave de encriptação.

### <a name="what-to-do-if-you-need-to-abandon-the-encryption-key"></a>O que fazer se tiver de abandonar a chave de encriptação

Se precisar de abandonar a chave de encriptação, utilize os procedimentos seguintes para realizar esta tarefa.

1. [Abandonar a chave de encriptação existente](#abandon-the-existing-encryption-key)

2. [Forneça a palavra-passe da conta do AD DS](#provide-the-password-of-the-ad-ds-account)

3. [Reinicializar a palavra-passe da conta de sincronização do Azure AD](#reinitialize-the-password-of-the-azure-ad-sync-account)

4. [Iniciar o serviço de sincronização](#start-the-synchronization-service)

#### <a name="abandon-the-existing-encryption-key"></a>Abandonar a chave de encriptação existente
Abandone a chave de encriptação existente para que essa nova chave de encriptação pode ser criado:

1. Inicie sessão no seu servidor AD de ligar do Azure como administrador.

2. Inicie uma nova sessão do PowerShell.

3. Navegue até à pasta:`$env:Program Files\Microsoft Azure AD Sync\bin\`

4. Execute o comando:`./miiskmu.exe /a`

![Utilitário de chave de encriptação de sincronização do Azure AD Connect](media/active-directory-aadconnectsync-encryption-key/key5.png)

#### <a name="provide-the-password-of-the-ad-ds-account"></a>Forneça a palavra-passe da conta do AD DS
Como as palavras-passe existentes armazenadas no interior da base de dados já não podem ser desencriptadas, terá de fornecer o serviço de sincronização com a palavra-passe da conta do AD DS. O serviço de sincronização encripta as palavras-passe utilizando a nova chave de encriptação:

1. Inicie o Synchronization Service Manager (serviço de sincronização do início →).
</br>![Gestor do serviço de sincronização](./media/active-directory-aadconnectsync-service-manager-ui/startmenu.png)  
2. Vá para o **conectores** separador.
3. Selecione o **conector AD** que corresponde ao seu local AD. Se tiver mais do que um conector do AD, repita os passos seguintes para cada um deles.
4. Em **ações**, selecione **propriedades**.
5. Na caixa de diálogo de pop-up, selecione **ligar à floresta do Active Directory**:
6. Introduza a palavra-passe da conta do AD DS no **palavra-passe** caixa de texto. Se não souber a palavra-passe, tem de defini-lo para um valor conhecido antes de executar este passo.
7. Clique em **OK** para guardar a nova palavra-passe e fechar a caixa de diálogo de pop-up.
![Utilitário de chave de encriptação de sincronização do Azure AD Connect](media/active-directory-aadconnectsync-encryption-key/key6.png)

#### <a name="reinitialize-the-password-of-the-azure-ad-sync-account"></a>Reinicializar a palavra-passe da conta de sincronização do Azure AD
Diretamente não é possível fornecer a palavra-passe da conta de serviço do Azure AD para o serviço de sincronização. Em vez disso, tem de utilizar o cmdlet **adicionar ADSyncAADServiceAccount** reinicializar a conta de serviço do Azure AD. O cmdlet repõe a palavra-passe de conta e torna a mesma disponível para o serviço de sincronização:

1. Inicie uma nova sessão PowerShell no servidor do Azure AD Connect.
2. Execute o cmdlet `Add-ADSyncAADServiceAccount`.
3. Na caixa de diálogo de pop-up, forneça as credenciais de Administrador Global do Azure AD para o seu inquilino do Azure AD.
![Utilitário de chave de encriptação de sincronização do Azure AD Connect](media/active-directory-aadconnectsync-encryption-key/key7.png)
4. Se for bem sucedida, verá a linha de comandos do PowerShell.

#### <a name="start-the-synchronization-service"></a>Iniciar o serviço de sincronização
Agora que o serviço de sincronização tem acesso para a chave de encriptação e todas as palavras-passe que é necessário, pode reiniciar o serviço do Windows serviço Gestor de controlo:


1. Aceda ao Gestor de controlo de serviço do Windows (início → serviços).
2. Selecione **Microsoft Azure AD Sync** e clique em reiniciar.

## <a name="next-steps"></a>Passos seguintes
**Tópicos de descrição geral**

* [Sincronização do Azure AD Connect: Noções e personalizar a sincronização](active-directory-aadconnectsync-whatis.md)

* [Integrar as identidades no local ao Azure Active Directory](active-directory-aadconnect.md)
