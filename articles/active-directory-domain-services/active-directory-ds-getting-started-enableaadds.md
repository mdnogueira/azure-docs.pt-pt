---
title: 'Serviços de Domínio do Azure AD: Ativar os Serviços de Domínio do Azure AD | Microsoft Docs'
description: Introdução aos Serviços de Domínio do Azure Active Directory
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand

ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/21/2016
ms.author: maheshu

---
# Ativar o Azure AD Domain Services
## Tarefa 3: Ativar os Serviços de Domínio do Azure AD
Nesta tarefa, vai ativar o Azure AD Domain Services no seu diretório. Execute os seguintes passos de configuração para ativar o Azure AD Domain Services no seu diretório.

1. Navegue para o **Portal Clássico do Azure** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).
2. Selecione o nó **Active Directory** no painel da esquerda.
3. Selecione o inquilino do Azure AD (diretório) para o qual gostaria de ativar os Serviços de Domínio do Azure AD.
   
    ![Selecionar o Azure AD Directory](./media/active-directory-domain-services-getting-started/select-aad-directory.png)
4. Clique no separador **Configurar**.
   
    ![Separador Configurar do diretório](./media/active-directory-domain-services-getting-started/configure-tab.png)
5. Desloque-se para baixo para uma secção intitulada **serviços de domínio**.
   
    ![Secção de configuração dos Serviços de Domínio](./media/active-directory-domain-services-getting-started/domain-services-configuration.png)
6. Ativar/desativar a opção intitulada **Ativar os serviços de domínio para este diretório** para **SIM**. Vai reparar que aparecem na página mais algumas opções de configuração do Azure AD Domain Services.
   
    ![Ativar os Serviços de Domínio](./media/active-directory-domain-services-getting-started/enable-domain-services.png)
   
   > [!NOTE]
   > Quando ativar o Azure AD Domain Services para o seu inquilino, o Azure AD gera e armazena os hashes de credencial de Kerberos e NTLM que são necessários para autenticar os utilizadores.
   > 
   > 
7. Especifique o **Nome de domínio DNS dos serviços de domínio**.
   
   * O nome de domínio predefinido do diretório (ou seja, que termina com o sufixo de domínio **. onmicrosoft.com**) é selecionado por predefinição.
   * A lista contém todos os domínios que foram configurados para o diretório do Azure AD – incluindo domínios verificados e não verificados que configura no separador 'Domínios'.
   * Além disso, também pode adicionar um nome de domínio personalizado a esta lista escrevendo o mesmo. Neste exemplo, introduzimos um nome de domínio personalizado 'contoso100.com'
     
     > [!WARNING]
     > Confirme que o prefixo de domínio do nome de domínio que especificar (por exemplo, “contoso100” no nome de domínio “contoso100.com”) tem menos de 15 carateres. Não é possível criar um domínio de Serviços de Domínio do Azure AD com um prefixo de domínio com mais de 15 carateres.
     > 
     > 
8. O passo seguinte consiste em selecionar uma rede virtual na qual gostaria de disponibilizar os Serviços de Domínio do Azure AD. Selecione a rede virtual que criou na lista pendente com o nome **Ligar serviços de domínio a esta rede virtual**.
   
   * Certifique-se de que a rede virtual que especificou pertence a uma região do Azure suportada pelos Serviços de Domínio do Azure AD.
   * Veja a página [Serviços do Azure por região](https://azure.microsoft.com/regions/#services/) para saber em que regiões do Azure está disponível o Azure AD Domain Services.
   * As redes virtuais pertencentes a uma região que não suporte o Azure AD Domain Services não aparecem na lista pendente.
   * Da mesma forma, as redes virtuais que foram criadas com o Azure Resource Manager não aparecem na lista pendente. O Azure AD Domain Services não suporta redes virtuais baseadas no ARM atualmente.
9. Certifique-se de que o nome de domínio DNS que escolheu para o domínio gerido ainda não existe na rede virtual. Em particular, verifique se:
   
   * Já tem um domínio com o mesmo nome de domínio DNS na rede virtual.
   * A rede virtual que selecionou tem uma ligação VPN à sua rede no local e se tem um domínio com o mesmo nome de domínio DNS na sua rede no local.
   * Tem um serviço em nuvem existente com este nome na rede virtual.
10. Para ativar o Azure AD Domain Services, clique em **Guardar** no painel da tarefa, na parte inferior da página.
11. A página apresenta o estado “Pendente...” enquanto os Serviços de Domínio do Azure AD estiverem a ser ativados para o seu diretório.
    
    ![Ativar os Serviços de Domínio - estado pendente](./media/active-directory-domain-services-getting-started/enable-domain-services-pendingstate.png)
    
    > [!NOTE]
    > Os Serviços de Domínio do Azure AD fornecem elevada disponibilidade para o seu domínio gerido. Depois de ativar o Azure AD Domain Services, repare que os endereços IP nos quais o Domain Services estão disponíveis na rede virtual aparecem um a um. O segundo endereço IP é apresentado em breve, assim que o serviço ativa a elevada disponibilidade para o seu domínio. Quando a elevada disponibilidade está configurada e ativa para o seu domínio, deverá ver dois endereços IP na secção **serviços de domínio** do separador **Configurar**.
    > 
    > 
12. Depois de cerca de 20 a 30 minutos, verá o primeiro endereço IP no qual o Domain Services está disponível na sua rede virtual no campo **Endereço IP**, na página **Configurar**.
    
    ![Serviços de Domínio ativados - primeiro IP aprovisionado](./media/active-directory-domain-services-getting-started/domain-services-enabled-firstdc-available.png)
13. Quando a elevada disponibilidade estiver operacional no seu domínio, verá dois endereços IP apresentados na página. Estes são os endereços IP nos quais o Azure AD Domain Services está disponível na sua rede virtual selecionada. Tome nota dos endereços IP, de modo a que possa atualizar as definições de DNS da sua rede virtual. Este passo ativa máquinas virtuais na rede virtual para ligar ao domínio para operações como associação a um domínio.
    
    ![Serviços de Domínio ativados – ambos os IPs aprovisionados](./media/active-directory-domain-services-getting-started/domain-services-enabled-bothdcs-available.png)

> [!NOTE]
> Consoante o tamanho do seu inquilino do Azure AD (número de utilizadores, grupos, etc.), demora algum tempo para que os conteúdos do inquilino fiquem disponíveis no Azure AD Domain Services. Este processo de sincronização ocorre em segundo plano. Em inquilinos grandes com dezenas de milhares de objetos, pode demorar um dia ou dois para que todos os utilizadores, associações a grupos e credenciais fiquem disponíveis no Azure AD Domain Services.
> 
> 

<br>

## Tarefa 4 - Atualizar as definições de DNS para a Azure Virtual Network
A próxima tarefa de configuração é [atualizar as definições de DNS para a Azure Virtual Network](active-directory-ds-getting-started-dns.md).

<!--HONumber=Sep16_HO4-->


