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
ms.date: 09/26/2017
ms.author: maheshu
ms.openlocfilehash: 245ad4948cf4b8c2d44a0dafb61923b0b4267856
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Configurar segura LDAP (LDAPS) para um domínio gerido dos serviços de domínio do Azure AD

## <a name="before-you-begin"></a>Antes de começar
Certifique-se de que concluiu [tarefa 2 - exportar o certificado LDAP seguro para um. Ficheiro PFX](active-directory-ds-admin-guide-configure-secure-ldap-export-pfx.md).


## <a name="task-3---enable-secure-ldap-for-the-managed-domain-using-the-azure-portal"></a>Tarefa 3 - ativar LDAP seguro para o domínio gerido utilizando o portal do Azure
Para ativar o LDAP segura, execute os seguintes passos de configuração:

1. Navegue para o  **[portal do Azure](https://portal.azure.com)**.

2. Procurar para serviços de domínio a **procurar recursos** caixa de pesquisa. Selecione **serviços de domínio do Azure AD** do resultado de pesquisa. O **serviços de domínio do Azure AD** página lista o seu domínio gerido.

    ![Localizar o domínio gerido que está a ser aprovisionado](./media/getting-started/domain-services-provisioning-state-find-resource.png)

2. Clique no nome do domínio gerido (por exemplo, ' contoso100.com') para ver mais detalhes sobre o domínio.

    ![Serviços de domínio - estado de aprovisionamento](./media/getting-started/domain-services-provisioning-state.png)

3. Clique em **LDAP seguro** no painel de navegação.

    ![Serviços de domínio - de página LDAP seguro](./media/active-directory-domain-services-admin-guide/secure-ldap-blade.png)

4. Por predefinição, o acesso LDAP seguro ao seu domínio gerido está desativado. Ativar/desativar **LDAP seguro** para **ativar**.

    ![Ativar LDAP seguro](./media/active-directory-domain-services-admin-guide/secure-ldap-blade-configure.png)
5. Por predefinição, o acesso LDAP seguro ao seu domínio gerido através da internet está desativado. Ativar/desativar **permitir acesso LDAP seguro através da internet** para **ativar**, se assim o desejar. 

    > [!TIP]
    > Se ativar o acesso LDAP seguro através da internet, é recomendável configurar um NSG para bloquear o acesso aos intervalos de endereços IP de origem necessários. Consulte as instruções para [bloqueio para baixo de acesso LDAPS ao seu domínio gerido através da internet](#task-5---lock-down-ldaps-access-to-your-managed-domain-over-the-internet).
    >

6. Clique em seguinte de ícone pasta **. Ficheiro PFX com certificado LDAP seguro**. Especifique o caminho para o ficheiro PFX com o certificado para proteger o acesso LDAP para o domínio gerido.

7. Especifique o **palavra-passe para desencriptar. Ficheiro PFX**. Forneça a mesma palavra-passe que utilizou quando exportar o certificado para o ficheiro PFX.

8. Quando tiver terminado, clique em de **guardar** botão.

9. Verá uma notificação que informa a que proteger o LDAP está a ser configurada para o domínio gerido. Até que esta operação esteja concluída, não é possível modificar outras definições para o domínio.

    ![Configuração de LDAP seguro para o domínio gerido](./media/active-directory-domain-services-admin-guide/secure-ldap-blade-configuring.png)

> [!NOTE]
> Demora cerca de 10 a 15 minutos para ativar o LDAP seguro para o seu domínio gerido. Se o certificado LDAP seguro fornecido não corresponde aos critérios necessários, LDAP seguro não está ativada para o seu diretório e verá uma falha. Por exemplo, o nome de domínio está incorreto, o certificado já expirou ou expira em breve. Neste caso, tente novamente com um certificado válido.
>
>

<br>

## <a name="task-4---configure-dns-to-access-the-managed-domain-from-the-internet"></a>Tarefa 4 - configurar o DNS para aceder ao domínio gerido através da internet
> [!NOTE]
> **Tarefas opcionais** - se de que não pretende aceder domínio gerido utilizando LDAPS através da internet, ignorar esta tarefa de configuração.
>
>

Antes de começar esta tarefa, certifique-se de que concluiu os passos descritos em [tarefa 3](#task-3---enable-secure-ldap-for-the-managed-domain-using-the-azure-portal-preview).

Assim que tiver ativado o acesso LDAP seguro através da internet para o seu domínio gerido, tem de atualizar o DNS para que os computadores cliente possam localizar este domínio gerido. No final da tarefa 3, um endereço IP externo é apresentado no **propriedades** separador **IP endereço para LDAPS acesso externo**.

Configure o fornecedor DNS externo para que o nome DNS do domínio gerido (por exemplo, ' ldaps.contoso100.com') aponta para este endereço IP externo. No nosso exemplo, temos de criar a entrada DNS seguinte:

    ldaps.contoso100.com  -> 52.165.38.113

Já está - agora está pronto para ligar ao domínio gerido utilizando o LDAP seguro através da internet.

> [!WARNING]
> Lembre-se de que os computadores cliente têm de confiar o emissor do certificado LDAPS para conseguir ligar com êxito ao domínio gerido utilizando LDAPS. Se estiver a utilizar uma autoridade de certificação publicamente fidedigna, não precisa de fazer nada, uma vez que os computadores cliente confiar estes emissores de certificados. Se estiver a utilizar um certificado autoassinado, instale a parte pública do certificado autoassinado para o arquivo de certificados fidedignos no computador cliente.
>
>


## <a name="task-5---lock-down-ldaps-access-to-your-managed-domain-over-the-internet"></a>Tarefa 5 – acesso LDAPS bloqueio pendente para o seu domínio gerido através da internet
> [!NOTE]
> **Tarefas opcionais** - se não tiver ativado o acesso LDAPS domínio gerido através da internet, ignorar esta tarefa de configuração.
>
>

Antes de começar esta tarefa, certifique-se de que concluiu os passos descritos em [tarefa 3](#task-3---enable-secure-ldap-for-the-managed-domain-using-the-azure-portal-preview).

A exposição do seu domínio gerido para o acesso LDAPS através da internet representa uma ameaça de segurança. O domínio gerido é acessível a partir da internet, a porta utilizada para o LDAP seguro (ou seja, porta 636). Por conseguinte, pode optar por restringir o acesso ao domínio gerido para os endereços IP conhecidos específicos. Para segurança melhorada, crie um grupo de segurança de rede (NSG) e associá-lo com a sub-rede onde tiver ativado os serviços de domínio do Azure AD.

A tabela seguinte ilustra um exemplo NSG, pode configurar, para bloquear o acesso LDAP seguro através da internet. O NSG contém um conjunto de regras que permitem acesso de entrada de LDAPS através da porta TCP 636 apenas a partir de um conjunto especificado de endereços IP. A regra predefinida do 'DenyAll' aplica-se a todos os outro o tráfego de entrada da internet. A regra NSG para permitir o acesso LDAPS através da internet a partir de endereços IP especificados tem uma prioridade mais alta do que a regra de DenyAll NSG.

![Exemplo NSG para proteger o acesso a LDAPS através da internet](./media/active-directory-domain-services-admin-guide/secure-ldap-sample-nsg.png)

**Obter mais informações** - [grupos de segurança de rede](../virtual-network/virtual-networks-nsg.md).

<br>

## <a name="related-content"></a>Conteúdo relacionado
* [Serviços de domínio do Azure AD - guia de introdução](active-directory-ds-getting-started.md)
* [Administrar um domínio gerido dos Serviços de Domínio do Azure AD](active-directory-ds-admin-guide-administer-domain.md)
* [Administrar a política de grupo num domínio gerido dos serviços de domínio do Azure AD](active-directory-ds-admin-guide-administer-group-policy.md)
* [Grupos de segurança de rede](../virtual-network/virtual-networks-nsg.md)
* [Criar um grupo de segurança de rede](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)
