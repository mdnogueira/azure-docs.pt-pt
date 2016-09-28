<properties
    pageTitle="Serviços de domínio do Azure AD: Atualizar as definições de DNS para a Azure Virtual Network | Microsoft Azure"
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


# Serviços de Domínio do Azure AD *(Pré-visualização)* – Atualizar as definições de DNS para a Azure Virtual Network

## Tarefa 4: Atualizar as definições de DNS para a Azure Virtual Network
Agora que ativou com êxito os Serviços de Domínio do Azure AD para o diretório, a próxima tarefa consiste em garantir que os computadores dentro da rede virtual podem ligar e consumir estes serviços. Para fazer isto, terá de atualizar as definições do servidor DNS da sua rede virtual para que apontem para os endereços IP para os quais os Serviços de Domínio do Azure AD estão disponíveis na rede virtual.

> [AZURE.NOTE] Tome nota dos endereços IP para os Serviços de Domínio do Azure AD apresentados no separador **Configurar** do seu diretório, depois de ter ativado os Serviços de Domínio do Azure AD para o diretório.

Execute os seguintes passos de configuração para atualizar a definição do servidor DNS para a rede virtual em que tiver ativado os Serviços de Domínio do Azure AD.

1. Navegue para o **Portal Clássico do Azure** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).

2. Selecione o nó **Redes** no painel da esquerda.

    ![Nó de redes virtuais](./media/active-directory-domain-services-getting-started/virtual-network-select.png)

3. No separador **Redes virtuais**, selecione a rede virtual em que ativou os Serviços de Domínio do Azure AD para ver as respetivas propriedades.

4. Clique no separador **Configurar**.

    ![Nó de redes virtuais](./media/active-directory-domain-services-getting-started/virtual-network-configure-tab.png)

5. Na secção **Servidores DNS**, introduza os endereços IP dos Serviços de Domínio do Azure AD.

6. Certifique-se de que introduz os endereços IP que foram apresentados na secção **Serviços de Domínio** no separador **Configurar** do seu diretório.

7. Clique em **Guardar** no painel de tarefas na parte inferior da página para guardar as definições do servidor DNS para esta rede virtual.

   ![Atualize as definições do servidor DNS para a rede virtual.](./media/active-directory-domain-services-getting-started/update-dns.png)

> [AZURE.NOTE] Depois de atualizar as definições do servidor DNS para a rede virtual, poderá demorar algum tempo para as máquinas virtuais na rede obterem a configuração de DNS atualizada. Se uma máquina virtual não consegue ligar ao domínio, pode esvaziar a cache DNS (ex. 'ipconfig /flushdns') na máquina virtual, para forçar uma atualização das definições de DNS na máquina virtual.


## Tarefa 5 – Ativar a sincronização de palavras-passe para os Serviços de Domínio do Azure AD
A próxima tarefa de configuração é [ativar a sincronização de palavras-passe para os Serviços de Domínio do Azure AD](active-directory-ds-getting-started-password-sync.md).



<!--HONumber=Sep16_HO3-->


