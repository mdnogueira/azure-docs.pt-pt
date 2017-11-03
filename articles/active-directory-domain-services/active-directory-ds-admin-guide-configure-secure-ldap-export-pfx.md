---
title: "Configurar o LDAP seguro (LDAPS) nos serviços de domínio do Azure AD | Microsoft Docs"
description: "Configurar o LDAP seguro (LDAPS) para um domínio gerido dos serviços de domínio do Azure AD"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: c6da94b6-4328-4230-801a-4b646055d4d7
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: maheshu
ms.openlocfilehash: 5d46f376d46b8bbf3f93de57a7d4e31abdbcdb2f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Configurar segura LDAP (LDAPS) para um domínio gerido dos serviços de domínio do Azure AD

## <a name="before-you-begin"></a>Antes de começar
Certifique-se de que concluiu [tarefa 1 - obter um certificado para o LDAP seguro](active-directory-ds-admin-guide-configure-secure-ldap.md).


## <a name="task-2---export-the-secure-ldap-certificate-to-a-pfx-file"></a>Tarefa 2 - exportar o certificado LDAP seguro para um. Ficheiro PFX
Antes de começar esta tarefa, certifique-se de que o certificado LDAP seguro obter de uma autoridade de certificação pública ou tiver criado um certificado autoassinado.

Execute os seguintes passos, para exportar o certificado LDAPS para um. Ficheiro PFX.

1. Prima a **iniciar** botão e tipo **R**. No **executar** caixa de diálogo, escreva **mmc** e clique em **OK**.

    ![Inicie a consola do MMC](./media/active-directory-domain-services-admin-guide/secure-ldap-start-run.png)
2. No **controlo de conta de utilizador** linha de comandos, clique em **Sim** para iniciar MMC (Consola de gestão da Microsoft) como administrador.
3. Do **ficheiro** menu, clique em **Adicionar/Remover Snap-in...** .

    ![Adicionar snap-in à consola MMC](./media/active-directory-domain-services-admin-guide/secure-ldap-add-snapin.png)
4. No **adicionar ou Remover Snap-ins** caixa de diálogo, selecione o **certificados** snap-in e clique em de **adicionar >** botão.

    ![Adicionar snap-in Certificados à consola MMC](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-snapin.png)
5. No **snap-in de certificados** assistente, selecione **conta de computador** e clique em **seguinte**.

    ![Adicionar snap-in de certificados para conta de computador](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-computer-account.png)
6. No **selecionar computador** página, selecione **computador Local: (o computador onde esta consola está em execução no)** e clique em **concluir**.

    ![Adicionar snap-in Certificados - computador Selecione](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-local-computer.png)
7. No **adicionar ou Remover Snap-ins** caixa de diálogo, clique em **OK** para adicionar o snap-in de certificados a MMC.

    ![Adicionar snap-in de certificados para MMC - concluído](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-snapin-done.png)
8. Na janela do MMC, clique para expandir **raiz da consola**. Deverá ver o snap-in de certificados carregado. Clique em **certificados (computador Local)** para expandir. Clique para expandir o **pessoais** nó, seguido de **certificados** nó.

    ![Arquivo de certificados pessoais aberta](./media/active-directory-domain-services-admin-guide/secure-ldap-open-personal-store.png)
9. Deverá ver o certificado autoassinado que foi criada. Pode examinar as propriedades do certificado para garantir que o thumbprint corresponde ao que reportados do windows PowerShell quando criou o certificado.
10. Selecione o certificado autoassinado e **certo**. No menu de contexto, selecione **todas as tarefas** e selecione **exportar...** .

    ![Exportar certificado](./media/active-directory-domain-services-admin-guide/secure-ldap-export-cert.png)
11. No **Assistente para exportar certificados**, clique em **seguinte**.

    ![Assistente de exportação de certificados](./media/active-directory-domain-services-admin-guide/secure-ldap-export-cert-wizard.png)
12. No **exportar chave privada** página, selecione **Sim, exportar a chave privada**e clique em **seguinte**.

    ![Exportar chave privada do certificado](./media/active-directory-domain-services-admin-guide/secure-ldap-export-private-key.png)

    > [!WARNING]
    > TEM de exportar a chave privada, juntamente com o certificado. Se fornecer um PFX que contém a chave privada do certificado, ativar LDAP seguro para o seu domínio gerido irá falhar.
    >
    >
13. No **exportar formato de ficheiro** página, selecione **Personal Information Exchange - PKCS #12 (. PFX)** como o formato de ficheiro para o certificado exportado.

    ![Exportar formato de ficheiro de certificado](./media/active-directory-domain-services-admin-guide/secure-ldap-export-to-pfx.png)

    > [!NOTE]
    > Apenas o. Formato de ficheiro PFX é suportado. Não exportar o certificado para o. Formato de ficheiro CER.
    >
    >
14. No **segurança** página, selecione o **palavra-passe** opção e escreva uma palavra-passe para proteger o. Ficheiro PFX. Memorize esta palavra-passe, uma vez que é necessária a seguinte tarefa. Clique em **seguinte** para continuar.

    ![Palavra-passe para exportar certificados ](./media/active-directory-domain-services-admin-guide/secure-ldap-export-select-password.png)

    > [!NOTE]
    > Tome nota desta palavra-passe. Precisa ao ativar o LDAP seguro para este domínio gerido no [tarefa 3 - ativar LDAP seguro para o domínio gerido](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md)
    >
    >
15. No **ficheiro a exportar** página, especifique o nome de ficheiro e a localização onde pretende exportar o certificado.

    ![Caminho para exportar certificados](./media/active-directory-domain-services-admin-guide/secure-ldap-export-select-path.png)
16. Na página seguinte, clique em **concluir** para exportar o certificado para um ficheiro PFX. Deverá ver a caixa de diálogo de confirmação quando o certificado foi exportado.

    ![Exportar certificado concluído](./media/active-directory-domain-services-admin-guide/secure-ldap-exported-as-pfx.png)


## <a name="next-step"></a>Passo seguinte
[Tarefa 3 - ativar LDAP seguro para o domínio gerido](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md)
