---
title: 'Azure Active Directory Domain Services: Ativar o Azure Active Directory Domain Services | Microsoft Docs'
description: "Introdução aos Serviços de Domínio do Azure Active Directory"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: c659da59-f4b5-4edd-b702-1727a8ccb36f
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/06/2017
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: e5f1fe51d8931985fa55b2d8c0a3fd25bb93f20f
ms.lasthandoff: 04/12/2017


---
# <a name="enable-azure-active-directory-domain-services"></a>Ativar o Azure Active Directory Domain Services
## <a name="task-3-enable-azure-active-directory-domain-services"></a>Tarefa 3: Ativar o Azure Active Directory Domain Services
Nesta tarefa, ativa o Azure Active Directory Domain Services (Azure AD DS) no seu diretório da seguinte forma:

1. Aceda ao [portal clássico do Azure](https://manage.windowsazure.com).
2. No painel do lado esquerdo, selecione o botão **Active Directory**.
3. Selecione o inquilino do Azure Active Directory (diretório) para o qual gostaria de ativar os Azure AD DS.

    ![Selecionar o Azure AD Directory](./media/active-directory-domain-services-getting-started/select-aad-directory.png)
4. Na página **diretório de pré-visualização**, clique no separador **Configurar**.

    ![Separador Configurar do diretório](./media/active-directory-domain-services-getting-started/configure-tab.png)
5. Em **serviços de domínio**, altere a opção **Ativar os serviços de domínio para este diretório** para **Sim**.  
    As opções de configuração adicionais do Azure Active Directory Domain Services são apresentadas na página.

    ![Ativar os Serviços de Domínio](./media/active-directory-domain-services-getting-started/enable-domain-services.png)

   > [!NOTE]
   > Quando ativar o Azure Active Directory Domain Services para o seu inquilino, o Azure AD gera e armazena os hashes de credencial de Kerberos e NTLM que são necessários para autenticar os utilizadores.
   >
   >
6. Especifique o **Nome de domínio DNS dos serviços de domínio**.

   * O nome de domínio predefinido do diretório (com o sufixo **.onmicrosoft.com**) é selecionado por predefinição.

   * A lista contém todos os domínios que foram configurados para o diretório do Azure AD, incluindo domínios verificados e não verificados que configura no separador **Domínios**.

   * Também pode introduzir um nome de domínio personalizado. Neste exemplo, o nome de domínio personalizado é *contoso100.com*.

     > [!WARNING]
     > O prefixo do seu nome de domínio especificado (por exemplo, *contoso100* no nome de domínio *contoso100.com*) tem de conter 15 ou menos carateres. Não pode criar um domínio do Azure Active Directory Domain Services com um prefixo que contenha mais do que 15 carateres.
     >
     >
7. Certifique-se de que o nome de domínio DNS que escolheu para o domínio gerido ainda não existe na rede virtual. Especificamente, verifique se:

   * Já tem um domínio com o mesmo nome de domínio DNS na rede virtual.

   * A rede virtual que selecionou tem uma ligação VPN à sua rede no local e se tem um domínio com o mesmo nome de domínio DNS na sua rede no local.

   * Tem um serviço cloud existente com este nome na rede virtual.
8. Selecione uma rede virtual, no qual pretende que o Azure Active Directory Domain Services esteja disponível. Selecione a rede virtual e a sub-rede dedicada criadas na lista pendente **Ligar serviços de domínio a esta rede virtual**. Além disso, considere o seguinte:

   * Certifique-se de que a rede virtual que especificou pertence a uma região do Azure suportada pelo Azure Active Directory Domain Services. Para verificar em que regiões do Azure está disponível o Azure Active Directory Domain Services, consulte [Azure services by region (Serviços do Azure por região)](https://azure.microsoft.com/regions/#services/).

   * As redes virtuais que pertencem a uma região onde o Azure Active Directory Domain Services não é suportado não aparecem na lista pendente.

   * Utilize uma sub-rede dedicada na rede virtual para o Azure Active Directory Domain Services. *Não* selecionou a sub-rede do gateway. Veja [considerações de redes](active-directory-ds-networking.md).

   * Da mesma forma, as redes virtuais que foram criadas com o Azure Resource Manager não aparecem na lista pendente. O Azure Active Directory Domain Services não suporta redes virtuais baseadas no ARM atualmente.
9. Para ativar o Azure Active Directory Domain Services, no painel da tarefa na parte inferior da página, clique em **Guardar**. 
    * Enquanto o Azure Active Directory Domain Services está a ser ativado para o seu diretório, a página apresenta o estado *Pendente*.

        ![Ativar a janela dos Serviços de Domínio](./media/active-directory-domain-services-getting-started/enable-domain-services-pendingstate.png)

        > [!NOTE]
        > O Azure Active Directory Domain Services fornece elevada disponibilidade para o seu domínio gerido. Depois de ativar o Azure Active Directory Domain Services, repare que os endereços IP nos quais os serviços de domínio estão disponíveis na rede virtual são apresentados um a um. O segundo endereço IP é apresentado rapidamente depois do primeiro, assim que o serviço ativa a elevada disponibilidade para o seu domínio. Quando a elevada disponibilidade está configurada e ativa para o seu domínio, deverá ver dois endereços IP na secção **serviços de domínio** do separador **Configurar**.
        >
        >
    * Depois de cerca de 20 a 30 minutos, o primeiro endereço IP no qual os serviços de domínios estão disponíveis na sua rede virtual no campo **Endereço IP**, na página **Configurar**.

        ![Janela dos Serviços de Domínio apresenta primeiro IP aprovisionado](./media/active-directory-domain-services-getting-started/domain-services-enabled-firstdc-available.png)
    * Quando a elevada disponibilidade estiver operacional no seu domínio, são apresentados dois endereços IP na página. O domínio gerido está disponível na rede virtual selecionada nestes dois endereços IP. 
    
10. Tome nota dos dois endereços IP, de modo a que possa atualizar as definições de DNS da sua rede virtual. Ao fazê-lo ativa máquinas virtuais na rede virtual para ligar ao domínio para operações como associação a um domínio.

    ![Janela dos Serviços de Domínio apresenta ambos os IPs aprovisionados](./media/active-directory-domain-services-getting-started/domain-services-enabled-bothdcs-available.png)

> [!NOTE]
> Dependendo do tamanho do inquilino do Azure AD (por exemplo, o número de utilizadores ou grupos), a sincronização para o seu domínio gerido é demorada. Este processo de sincronização ocorre em segundo plano. Em inquilinos grandes com dezenas de milhares de objetos, pode demorar um dia ou dois para que todos os utilizadores, associações a grupos e credenciais a sincronizar.
>
>

## <a name="next-steps"></a>Passos seguintes
Tarefa 4: [Atualizar as definições de DNS para a rede virtual do Azure](active-directory-ds-getting-started-dns.md)

