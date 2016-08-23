<properties
    pageTitle="Serviços de Domínio do Azure AD: Criar ou selecionar uma rede virtual | Microsoft Azure"
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

# Serviços de Domínio do Azure AD *(Pré-visualização)* – Criar ou selecionar uma rede virtual

## Diretrizes para selecionar uma Azure Virtual Network
Quando seleciona uma rede virtual para utilizar com os Serviços de Domínio do Azure AD, mantenha as seguintes diretrizes em mente:

- Certifique-se de que seleciona uma rede virtual numa região que é suportada pelos Serviços de Domínio do Azure AD. Veja a página [Serviços do Azure por região](https://azure.microsoft.com/regions/#services/) para saber em que regiões do Azure estão disponíveis os Serviços de Domínio do Azure AD.

- Se pretender utilizar uma rede virtual existente, certifique-se de que é uma rede virtual regional. Não é possível utilizar redes virtuais que utilizam o mecanismo de grupos de afinidade legado com os Serviços de Domínio do Azure AD. É necessário [migrar redes virtuais legadas para redes virtuais regionais](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).

- Se pretender utilizar uma rede virtual existente, certifique-se de que não existem servidores DNS personalizados configurados para a rede virtual. Os Serviços de Domínio do Azure AD não suportam servidores DNS personalizados/com proprietário.

- Se pretender utilizar uma rede virtual existente, certifique-se de que não tem um domínio existente com o mesmo nome de domínio disponível na rede virtual. Por exemplo, suponha que tem um domínio chamado 'contoso.com' já disponível na rede virtual selecionada. Posteriormente, tenta ativar um domínio gerido dos Serviços de Domínio do Azure AD com o mesmo nome de domínio (ou seja, 'contoso.com') nessa rede virtual. Irá ocorrer uma falha ao tentar ativar os Serviços de Domínio do Azure AD. Isto acontece devido a conflitos de nomes para o nome de domínio na rede virtual. Nesta situação, tem de utilizar um nome diferente para configurar o seu domínio gerido dos Serviços de Domínio do Azure AD. Em alternativa, pode anular o aprovisionamento do domínio existente e, em seguida, prosseguir para ativar os Serviços de Domínio do Azure AD.

- Selecione a rede virtual que atualmente aloja/irá alojar máquinas virtuais que necessitam de aceder aos Serviços de Domínio do Azure AD. Não será possível mover os Serviços de Domínio para uma rede virtual diferente depois de ter ativado o serviço.

- Os Serviços de Domínio do Azure AD não são suportados com redes virtuais criadas utilizando o Azure Resource Manager. Pode [ligar uma rede virtual clássica a uma rede virtual baseada em ARM](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md), para poder utilizar os Serviços de Domínio do Azure AD numa rede virtual criada com o Azure Resource Manager.


## Tarefa 2: Criar uma Azure Virtual Network
A próxima tarefa de configuração consiste em criar uma Azure Virtual Network na qual gostaria de ativar os Serviços de Domínio do Azure AD. Se já tiver uma rede virtual existente que prefira utilizar, pode ignorar este passo.

> [AZURE.NOTE] Certifique-se de que a Azure Virtual Network que cria ou opta por utilizar com os Serviços de Domínio do Azure AD pertence a uma região do Azure que seja suportado pelos Serviços de Domínio do Azure AD. Veja a página [Serviços do Azure por região](https://azure.microsoft.com/regions/#services/) para saber em que regiões do Azure estão disponíveis os Serviços de Domínio do Azure AD.

Precisa de tomar nota do nome da rede virtual para que possa selecionar a rede virtual correta quando ativar os Serviços de Domínio do Azure AD num passo subsequente da configuração.

Execute os seguintes passos de configuração para criar uma Azure Virtual Network na qual gostaria de ativar os Serviços de Domínio do Azure AD.

1. Navegue para o **Portal Clássico do Azure** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).

2. Selecione o nó **Redes** no painel da esquerda.

3. Clique em **NOVO** no painel de tarefas na parte inferior da página.

    ![Nó de redes virtuais](./media/active-directory-domain-services-getting-started/virtual-networks.png)

4. No nó **Serviços de Rede**, selecione **Virtual Network**.

5. Clique em **Criação Rápida** para criar uma rede virtual.

    ![Rede virtual – criação rápida](./media/active-directory-domain-services-getting-started/virtual-network-quickcreate.png)

6. Especifique um **Nome** para a sua rede virtual. Também pode optar por configurar o **Espaço de endereço** ou **Número máximo de VMs** para esta rede. Pode deixar a definição do servidor DNS definida como 'Nenhuma' por agora. Esta definição será atualizada após a ativação dos Serviços de Domínio do Azure AD.

7. Certifique-se de que seleciona uma região do Azure suportada no menu pendente **Localização**. Veja a página [Serviços do Azure por região](https://azure.microsoft.com/regions/#services/) para saber em que regiões do Azure estão disponíveis os Serviços de Domínio do Azure AD. Este é um passo importante. Se selecionar uma rede virtual numa região do Azure que não seja suportada pelos Serviços de Domínio do Azure AD, não será possível ativar o serviço nessa rede virtual.

8. Clique no botão **Criar uma Rede Virtual** para criar a rede virtual.

    ![Crie uma rede virtual para os Serviços de Domínio do Azure AD.](./media/active-directory-domain-services-getting-started/create-vnet.png)

<br>

## Tarefa 3 – Ativar os Serviços de Domínio do Azure AD
A próxima tarefa de configuração consiste em [ativar os Serviços de Domínio do Azure AD](active-directory-ds-getting-started-enableaadds.md).



<!--HONumber=Aug16_HO1-->


