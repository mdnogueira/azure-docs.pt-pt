<properties
    pageTitle="Serviços de Domínio do Azure AD: Ativar os Serviços de Domínio do Azure AD | Microsoft Azure"
    description="Introdução aos Serviços de Domínio do Azure Active Directory (pré-visualização)"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/06/2016"
    ms.author="maheshu"/>


# Serviços de domínio do Azure AD *(pré-visualização)* – Ativar os Serviços de Domínio do Azure AD

## Tarefa 3: Ativar os Serviços de Domínio do Azure AD
Nesta tarefa irá ativar os Serviços de Domínio do Azure AD para o seu diretório. Execute os seguintes passos de configuração para poder ativar os Serviços de Domínio do Azure AD para o seu diretório.

1. Navegue para o **Portal Clássico do Azure** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).

2. Selecione o nó **Active Directory** no painel da esquerda.

3. Selecione o inquilino do Azure AD (diretório) para o qual gostaria de ativar os Serviços de Domínio do Azure AD.

    ![Selecionar o Azure AD Directory](./media/active-directory-domain-services-getting-started/select-aad-directory.png)

4. Clique no separador **Configurar**.

    ![Separador Configurar do diretório](./media/active-directory-domain-services-getting-started/configure-tab.png)

5. Desloque-se para baixo para uma secção intitulada **serviços de domínio**.

    ![Secção de configuração dos Serviços de Domínio](./media/active-directory-domain-services-getting-started/domain-services-configuration.png)

6. Ativar/desativar a opção intitulada **Ativar os serviços de domínio para este diretório** para **SIM**. Vai reparar que aparecem na página mais algumas opções de configuração de Serviços de Domínio do Azure AD a

    ![Ativar os Serviços de Domínio](./media/active-directory-domain-services-getting-started/enable-domain-services.png)

    > [AZURE.NOTE] Quando ativar os Serviços de Domínio do Azure AD para o seu inquilino, o Azure AD irá gerar e armazenar os hashes de credencial de Kerberos e NTLM que são necessários para autenticar utilizadores.

7. Especifique o **Nome de domínio DNS dos serviços de domínio**.

   - O nome de domínio predefinido do diretório (ou seja, que termina com o sufixo de domínio **. onmicrosoft.com**) será selecionado por predefinição.

   - A lista contém todos os domínios que foram configurados para o diretório do Azure AD – incluindo domínios verificados e não verificados que configura no separador 'Domínios'.

   - Além disso, também pode adicionar um nome de domínio personalizado a esta lista escrevendo o mesmo. Neste exemplo, introduzimos um nome de domínio personalizado 'contoso100.com'

     > [AZURE.WARNING] Certifique-se de que o prefixo de domínio com o nome de domínio especificado (ex. 'contoso100' no domínio 'contoso100.com') é inferior a 15 carateres. Não é possível criar um domínio de Serviços de Domínio do Azure AD com um prefixo de domínio com mais de 15 carateres.

8. O passo seguinte consiste em selecionar uma rede virtual na qual gostaria de disponibilizar os Serviços de Domínio do Azure AD. Selecione a rede virtual que acabou de criar na lista pendente intitulada **Ligar serviços de domínio a esta rede virtual**.

   - Certifique-se de que a rede virtual que especificou pertence a uma região do Azure suportada pelos Serviços de Domínio do Azure AD.

   - Consulte a página [Serviços do Azure por região](https://azure.microsoft.com/regions/#services/) para saber em que regiões do Azure estão disponíveis os Serviços de Domínio do Azure AD.

   - Tenha em atenção que as redes virtuais pertencentes a uma região que não suporte os Serviços de Domínio do Azure AD não irão aparecer na lista pendente.

   - Da mesma forma, as redes virtuais que foram criadas utilizando o Azure Resource Manager(redes virtuais baseadas em ARM) não serão apresentados na lista pendente. Isto acontece porque as redes virtuais baseadas em ARM não são atualmente suportadas pelos Serviços de Domínio do Azure AD.

9. Certifique-se de que o nome de domínio DNS que escolheu para o domínio gerido ainda não existe na rede virtual. Isto pode ocorrer em qualquer um dos seguintes cenários:

   - Se já tiver um domínio com o mesmo nome de domínio DNS na rede virtual.

   - Se a rede virtual que selecionou tiver uma ligação VPN à sua rede no local e se tiver um domínio com o mesmo nome de domínio DNS na sua rede no local.

   - Se tiver um serviço em nuvem existente com esse nome na rede virtual.

10. Quando terminar de selecionar as opções acima, clique em **Guardar** a partir do painel de tarefas na parte inferior da página para ativar os Serviços de Domínio do Azure AD.

11. A página mostrará um estado 'Pendente...' enquanto os Serviços de Domínio do Azure AD estiverem a ser ativados para o seu diretório.

    ![Ativar os Serviços de Domínio - estado pendente](./media/active-directory-domain-services-getting-started/enable-domain-services-pendingstate.png)

    > [AZURE.NOTE] Os Serviços de Domínio do Azure AD fornecem elevada disponibilidade para o seu domínio gerido. Quando ativar primeiro os Serviços de Domínio do Azure AD para o seu domínio, irá reparar que os endereços IP nos quais os Serviços de Domínio estão disponíveis na rede virtual aparecem um por um. O segundo endereço IP será apresentado em breve, assim que o serviço ativa a elevada disponibilidade para o seu domínio. Quando a elevada disponibilidade está configurada e ativa para o seu domínio, deverá ver dois endereços IP na secção **serviços de domínio** do separador **Configurar**.

12. Depois de cerca de 20 a 30 minutos, verá o primeiro endereço IP nos quais os Serviços de Domínio estão disponíveis na sua rede virtual no campo **endereço IP** na página **Configurar**.

    ![Serviços de Domínio ativados - primeiro IP aprovisionado](./media/active-directory-domain-services-getting-started/domain-services-enabled-firstdc-available.png)

13. Quando a elevada disponibilidade está operacional para o seu domínio, verá dois endereços IP apresentados na página. Estes são os endereços IP nos quais os Serviços de Domínio do Azure AD estarão disponíveis na sua rede virtual selecionada. Tome nota destes endereços IP, de modo a que possa atualizar as definições de DNS da sua rede virtual. Este passo ativa máquinas virtuais na rede virtual para ligar ao domínio para operações como associação a um domínio.

    ![Serviços de Domínio ativados – ambos os IPs aprovisionados](./media/active-directory-domain-services-getting-started/domain-services-enabled-bothdcs-available.png)

> [AZURE.NOTE] Consoante o tamanho do seu diretório do Azure AD (número de utilizadores, grupos, etc.), irá demorar algum tempo para que o conteúdo do diretório fique disponível nos Serviços de Domínio do Azure AD. Este processo de sincronização ocorre em segundo plano. Para grandes diretórios com dezenas de milhares de objetos, poderá demorar um dia ou dois para que todos os utilizadores, as associações a grupos e as credenciais sejam sincronizados e fiquem disponíveis nos Serviços de Domínio do Azure AD.

<br>

## Tarefa 4 - Atualizar as definições de DNS para a Azure Virtual Network
A próxima tarefa de configuração é [atualizar as definições de DNS para a Azure Virtual Network](active-directory-ds-getting-started-dns.md).



<!--HONumber=Sep16_HO3-->


