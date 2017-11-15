---
title: "Criar um tráfego de rede de rota a rota definida pelo utilizador através de uma aplicação virtual de rede - portal do Azure | Microsoft Docs"
description: "Saiba como criar uma rota definida pelo utilizador para substituir predefinição do Azure encaminhamento por encaminhamento de tráfego de rede através de uma aplicação virtual de rede."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/16/2017
ms.author: jdial
ms.openlocfilehash: 0319029277091611673f15c94604604850cbfcbe
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2017
---
# <a name="create-a-user-defined-route---azure-portal"></a>Criar uma rota definida pelo utilizador - portal do Azure

Neste tutorial, saiba como criar rotas definidas pelo utilizador para encaminhar o tráfego entre duas [rede virtual](virtual-networks-overview.md) sub-redes através de uma aplicação virtual de rede. Uma aplicação virtual de rede é uma máquina virtual que executa uma aplicação de rede, tais como uma firewall. Para obter mais informações sobre aplicações virtuais de rede previamente configuradas que pode implementar numa rede virtual do Azure, consulte o [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances).

Quando criar sub-redes na rede virtual, o Azure cria predefinido [rotas de sistema](virtual-networks-udr-overview.md#system-routes) que permitem que os recursos em todas as sub-redes para comunicar entre si, conforme mostrado na imagem seguinte:

![Rotas predefinidas](./media/create-user-defined-route/default-routes.png)

Neste tutorial, crie uma rede virtual com o público, privado e sub-redes da rede de Perímetro, conforme mostrado na imagem que se segue. Normalmente, os servidores web poderão ser implementados para uma sub-rede pública e uma aplicação ou um servidor de base de dados poderá ser implementada para uma sub-rede privada. Criar uma máquina virtual para agir como uma aplicação virtual de rede na sub-rede DMZ e, opcionalmente, criar uma máquina virtual em cada sub-rede que comunicam através da aplicação virtual de rede. Todo o tráfego entre as sub-redes públicas e privadas é encaminhado através de aplicação, conforme mostrado na imagem seguinte:

![Rotas definidas pelo utilizador](./media/create-user-defined-route/user-defined-routes.png)

Este artigo fornece os passos para criar uma rota definida pelo utilizador através do modelo de implementação Resource Manager, que é o modelo de implementação, que recomendamos a utilização quando criar rotas definidas pelo utilizador. Se precisar de criar uma rota definida pelo utilizador (clássica), consulte [criar uma rota definida pelo utilizador (clássica)](virtual-network-create-udr-classic-ps.md). Se não estiver familiarizado com os modelos de implementação do Azure, consulte [modelos de implementação do Azure compreender](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Para saber mais sobre as rotas definidas pelo utilizador, consulte o artigo [descrição geral de rotas definidas pelo utilizador](virtual-networks-udr-overview.md#user-defined).

## <a name="create-routes-and-network-virtual-appliance"></a>Criar rotas e dispositivo de rede virtual

1. **Pré-requisitos**: criar uma rede virtual com duas sub-redes, efetuando os passos em [criar uma rede virtual](#create-a-virtual-network).
2. Depois de criar a rede virtual, num browser da Internet, vá para o [portal do Azure](https://portal.azure.com). Inicie sessão com a sua [conta do Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account).
3. No **procurar recursos** na parte superior do portal, introduza *myResourceGroup*. Clique em **myResourceGroup** quando for apresentada nos resultados da pesquisa. O grupo de recursos foi criado como parte dos pré-requisitos.
4. Clique em **myVnet**, conforme mostrado na imagem seguinte:

    ![Definições da interface de rede](./media/create-user-defined-route/virtual-network.png)

5. Crie uma sub-rede para a aplicação virtual de rede:
 
    - Em **myVnet**, clique em **sub-redes** em **definições** no lado esquerdo.
    - Clique em **+ sub-rede**, conforme mostrado na imagem seguinte:

        ![Sub-redes](./media/create-user-defined-route/subnets.png) 
    - Introduza os seguintes valores no **adicionar sub-rede**, em seguida, clique em **OK**:

        |Definição|Valor|
        |-----|-----|
        |Nome|Rede de Perímetro|
        |Intervalo de endereços (bloco CIDR)|10.0.2.0/24|

6. Crie uma máquina de virtual da aplicação virtual de rede:

    - No lado esquerdo do portal, clique em **+ novo**, em seguida, clique em **computação**e, em seguida, clique em **Datacenter do Windows Server 2016** ou **Ubuntu Server 16.04 LTS**.
    - Introduza os seguintes valores **Noções básicas** painel apresentado, clique em **OK**.

        |Definição|Valor|
        |---|---|
        |Nome|myVm Nva|
        |Nome de utilizador|azureuser|
        |Palavra-passe e Confirmar palavra-passe|Uma palavra-passe da sua escolha|
        |Subscrição|Selecione a sua subscrição|
        |Grupo de recursos|Clique em **utilizar existente**, em seguida, selecione **myResourceGroup**|
        |Localização|EUA Leste|
    - No **escolher um tamanho** painel apresentado, clique em **DS1_V2 padrão**, em seguida, clique em **selecione**.
    - No **definições** painel apresentado, clique em **rede Virtual**. Clique em **myVnet** no **escolha de rede virtual** painel que aparece.
    - No **definições** painel, clique em **sub-rede**. Clique em **DMZ** no **escolha subrede** painel que aparece. 
    - Deixe as predefinições para as restantes definições e clique em **OK**.
    - Clique em **criar** no **criar** painel que aparece. Implementação da máquina virtual demora alguns minutos.

    > [!NOTE]
    > Para além de criar a máquina virtual, do portal do Azure cria um endereço IP público e atribui-o para a máquina virtual, por predefinição. Se foram a implementar a máquina virtual num ambiente de produção, pode optar por não atribuir um endereço IP público para a máquina virtual. Em vez disso, podem aceder a aplicação virtual de rede a partir de outras máquinas virtuais dentro da rede virtual. Para saber mais sobre os endereços IP públicos, consulte o artigo [gerir um endereço IP público](virtual-network-public-ip-address.md).

7. Atribua um endereço IP privado estático e ativar o IP para a interface de rede de reencaminhamento portal criado no passo anterior. 
    - No **procurar recursos** na parte superior da página, introduza *myVm Nva*.
    - Clique em **myVm Nva** quando for apresentada nos resultados da pesquisa.
    - Clique em **redes** em **definições** no lado esquerdo.
    - Clique no nome da interface de rede em **interfaces de rede myVm-Nva -**. O nome é **myvm nva***X*, onde *X* é um número atribuído pelo portal.
    - Clique em **configurações de IP** em **definições** para a interface de rede, conforme mostrado na imagem seguinte:

        ![Definições da interface de rede](./media/create-user-defined-route/network-interface-settings.png)
        
    - Clique em **ativado** para o **reencaminhamento IP** definição, em seguida, clique em **guardar**. Reencaminhamento IP tem de estar ativado para cada interface de rede que recebe o tráfego não resolvido para um endereço IP atribuído ao mesmo. Ativar o reencaminhamento IP desativa a verificação de origem/destino do Azure para a interface de rede.
    - Clique em **ipconfig1** na lista de configurações de IP.
    - Clique em **estático** para **atribuição** do endereço IP privado em **ipconfig1**, conforme mostrado na imagem seguinte:

        ![Configuração de IP](./media/create-user-defined-route/ip-configuration.png)
    - Como é mostrado na imagem anterior, introduza *10.0.2.4* em **endereço IP** no **as definições de endereço IP privado**. Atribuir um endereço IP estático para a interface de rede assegura que o endereço de não altera de vida da máquina virtual que a interface de rede está ligada a. O endereço introduzido não está atualmente atribuído a outro recurso na sub-rede DMZ que está a ser a interface de rede. 
    - Para guardar a configuração, clique em **guardar** em **ipconfig1**. Não feche a caixa de ipconfig1 até receber uma notificação no portal do que a interface de rede é guardada.
 
8. Crie duas tabelas de rota. As tabelas de rotas contenham zero ou mais rotas:

    - No lado esquerdo do portal, clique em **+ novo** > **redes** > **tabela de rotas**.
    - Introduza os seguintes valores no **criar uma tabela de rota**e, em seguida, clique em **criar**:

        |Definição|Valor|
        |---|---|
        |Nome|myRouteTable público|
        |Subscrição|Selecione a sua subscrição|
        |Grupo de recursos|Selecione **utilizar existente**, em seguida, selecione **myResourceGroup**|
        |Localização|EUA Leste|
    
    - Concluir os substeps anteriores do passo 8 novamente, mas a tabela de rotas de nome *myRouteTable privada*.
9. Adicionar rotas para o *myRouteTable público* tabela de rotas e associar a tabela de rotas para o *pública* sub-rede:

    - No **procurar recursos** na parte superior do portal, introduza *myRouteTable público*. Clique em **myRouteTable público** quando for apresentada nos resultados da pesquisa.
    - Em **myRouteTable público**, clique em **rotas** na lista de **definições**.
    - Clique em **+ adicionar** em **público myRouteTable - rotas**.
    -  Por predefinição, o tráfego de rotas do Azure entre todas as sub-redes dentro de uma rede virtual. Criar uma rota para alterar a predefinição do Azure encaminhamento para que o tráfego do *pública* sub-rede é encaminhada através de NVA, em vez de diretamente para o *privada* sub-rede. Introduza os seguintes valores **adicionar rota** painel que aparece e, em seguida, clique em **OK**:

        |Definição|Valor|Explicação|
        |---|---|---|
        |Nome da rota|ToPrivateSubnet|Esta rota direciona o tráfego para a sub-rede privada, através da aplicação virtual de rede.|
        |Prefixo de endereço|10.0.1.0/24| Qualquer tráfego enviado para os endereços dentro este prefixo de endereço (10.0.1.0 - 10.0.1.254) é enviada para a aplicação virtual de rede.|
        |Tipo de salto seguinte| Selecione **aplicação Virtual**||
        |Endereço do próximo salto|10.0.2.4| O endereço IP privado estático da interface de rede ligado ao dispositivo virtual de rede. O tipo de salto apenas pode especificar um endereço para for **aplicação Virtual**.|

    - Em **myRouteTable público**, clique em **sub-redes** em **definições**. 
    - Clique em **+ associar** em **público myRouteTable - sub-redes**.
    - Clique em **rede Virtual** em **associar sub-rede**, em seguida, clique em **myVnet**.
    - Clique em **sub-rede** em **associar sub-rede**, em seguida, clique em **pública** em **escolha sub-rede**. 
    - Para guardar a configuração, clique em **OK** em **associar sub-rede**. Uma sub-rede pode ter zero ou uma tabela de rotas associada ao mesmo.
10. Passo completo 9 novamente procurar **myRouteTable privada**, criar uma rota com as seguintes definições, em seguida, associar a tabela de rotas para o **privada** sub-rede do **myVnet** rede virtual:

    |Definição|Valor|Explicação|
    |---|---|---|
    |Nome da rota|ToPublicSubnet|Esta rota direciona o tráfego para a sub-rede pública, através da aplicação virtual de rede.|
    |Prefixo de endereço|10.0.0.0/24| Qualquer tráfego enviado para os endereços dentro este prefixo de endereço (10.0.0.0 - 10.0.1.254) é enviada para a aplicação virtual de rede.|
    |Tipo de salto seguinte| Selecione **aplicação Virtual**||
    |Endereço do próximo salto|10.0.2.4||

    Fluxos de tráfego de rede entre os recursos nas sub-redes privadas e públicas através da aplicação virtual de rede. 
11. **Opcional:** criar uma máquina virtual nas sub-redes pública e privada e confirme que a comunicação entre as máquinas virtuais é encaminhada através de aplicação virtual de rede, efetuando os passos em [validarEncaminhamento](#validate-routing). 
12. **Opcional**: a eliminar os recursos que criou neste tutorial, seguindo os passos no [eliminar recursos](#delete-resources).

## <a name="validate-routing"></a>Validar o encaminhamento

1. Se ainda não o fez, concluir os passos no [criar rotas e dispositivo de rede virtual](#create-routes-and-network-virtual-appliance).
2. Clique em de **experimente** botão na caixa de que se segue, que abre o Shell de nuvem do Azure. Se lhe for solicitado, inicie sessão no Azure com o [conta do Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Se não tiver uma conta do Azure, pode inscrever-se para obter uma [avaliação gratuita](https://azure.microsoft.com/offers/ms-azr-0044p). A Shell de nuvem do Azure é uma shell de deteção livre com a interface de linha de comandos do Azure pré-instalado. 

    Os scripts seguintes criar duas máquinas virtuais, um no *pública* sub-rede e um no *privada* sub-rede. Os scripts também ativar o IP de reencaminhamento para a interface de rede no sistema operativo da NVA para ativar o sistema operativo encaminhar o tráfego através da interface de rede. Uma produção NVA normalmente inspeciona o tráfego antes de encaminhamento, mas neste tutorial, a NVA simple encaminha apenas o tráfego sem inspecioná-lo. 

    Clique em de **cópia** clique no botão no **Linux** ou **Windows** scripts que sigam e colagem o conteúdo do script para um editor de texto. Alterar a palavra-passe para o *adminPassword* variável, em seguida, cole o script para a Shell de nuvem do Azure. Execute o script para o sistema operativo que selecionou quando criou a aplicação virtual de rede no passo 6 do [criar rotas e dispositivo de rede virtual](#create-routes-and-network-virtual-appliance). 

    **Linux**

    ```azurecli-interactive
    #!/bin/bash

    #Set variables used in the script.
    rgName="myResourceGroup"
    location="eastus"
    adminPassword=ChangeToYourPassword
    
    # Create a virtual machine in the Public subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Public \
      --image UbuntuLTS \
      --vnet-name myVnet \
      --subnet Public \
      --public-ip-address myPublicIp-Public \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Create a virtual machine in the Private subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Private \
      --image UbuntuLTS \
      --vnet-name myVnet \
      --subnet Private \
      --public-ip-address myPublicIp-Private \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Enable IP forwarding for the network interface in the NVA virtual machine's operating system.    
    az vm extension set \
      --resource-group $rgName \
      --vm-name myVm-Nva \
      --name customScript \
      --publisher Microsoft.Azure.Extensions \
      --settings '{"commandToExecute":"sudo sysctl -w net.ipv4.ip_forward=1"}'
    ```

    **Windows**

    ```azurecli-interactive

    #!/bin/bash
    #Set variables used in the script.
    rgName="myResourceGroup"
    location="eastus"
    adminPassword=ChangeToYourPassword
    
    # Create a virtual machine in the Public subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Public \
      --image win2016datacenter \
      --vnet-name myVnet \
      --subnet Public \
      --public-ip-address myPublicIp-Public \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Allow pings through the Windows Firewall.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Public \
      --resource-group $rgName \
      --settings '{"commandToExecute":"netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow"}'

    # Create a virtual machine in the Private subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Private \
      --image win2016datacenter \
      --vnet-name myVnet \
      --subnet Private \
      --public-ip-address myPublicIp-Private \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Allow pings through the Windows Firewall.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Private \
      --resource-group $rgName \
      --settings '{"commandToExecute":"netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow"}'

    # Enable IP forwarding for the network interface in the NVA virtual machine's operating system.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Nva \
      --resource-group $rgName \
      --settings '{"commandToExecute":"powershell.exe Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1"}'

    # Restart the NVA virtual machine.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Nva \
      --resource-group $rgName \
      --settings '{"commandToExecute":"powershell.exe Restart-Computer -ComputerName myVm-Nva -Force"}'
    ```

3. Valide a comunicação entre as máquinas virtuais nas sub-redes pública e privada. 

    - Abra um [SSH](../virtual-machines/linux/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-vm) (Linux) ou [ambiente de trabalho remoto](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-vm) ligação (Windows) para o endereço IP público do *myVm público* máquina virtual.
    - Numa linha de comandos no *myVm público* máquina virtual, introduza `ping myVm-Private`. Receber respostas porque a NVA encaminha o tráfego do público para a sub-rede privada.
    - Do *myVm público* máquina virtual, execute uma rota de rastreio entre as máquinas virtuais nas sub-redes públicas e privadas. Introduza o comando adequado que se segue, consoante o sistema operativo instalado nas máquinas virtuais nas sub-redes pública e privada:
        - **Windows**: a partir de uma linha de comandos, execute o `tracert myvm-private` comando.
        - **Ubuntu**: executar o `tracepath myvm-private` comando.
      Tráfego atravessa 10.0.2.4 (a NVA) antes de atingir 10.0.1.4 (a máquina virtual na sub-rede privada). 
    - Concluir os passos anteriores, ligando-se para o *myVm privada* máquina virtual e efetuando o ping a *myVm público* máquina virtual. A rota de rastreio mostra comunicação estiverem em deslocação através de 10.0.2.4 antes de atingir 10.0.0.4 (a máquina virtual na sub-rede pública).

      > [!NOTE]
      > Os passos anteriores permitem-lhe confirmar o encaminhamento entre os endereços IP privados do Azure. Se pretender direta ou proxy, o tráfego para o IP público endereços através de uma aplicação virtual de rede:
      > - Tem de fornecer o dispositivo de tradução de endereços de rede ou a capacidade de proxy. Se tradução de endereços de rede, o dispositivo tem traduzir a origem para a sua própria de endereço IP e, em seguida, reencaminhe esse pedido para o endereço IP público. Se o dispositivo tem endereços de rede traduzido o endereço de origem ou é a funcionalidade de proxy, Azure traduz os endereços IP privados a rede aplicação virtual para um endereço IP público. Para obter mais informações sobre os diferentes métodos Azure utiliza a tradução de endereços IP privados para endereços IP públicos, consulte [Noções sobre ligações de saída](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
      > - Uma rota adicional na tabela de rota, tais como prefixo: 0.0.0.0/0, o tipo de próximo salto VirtualAppliance e o IP do próximo salto endereços 10.0.2.4 (no script de exemplo anterior).
      >
    - **Opcionalmente,**: para validar o salto seguinte entre as duas máquinas virtuais no Azure, utilize a capacidade de salto seguinte do observador de rede do Azure. Antes de utilizar o observador de rede, deve primeiro [criar uma instância de observador de rede de Azure](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para a região que pretende utilizá-la no. Neste tutorial, é utilizada a região EUA Leste. Assim que tiver ativada uma instância de observador de rede para a região, introduza o seguinte comando para ver as informações de salto seguintes entre as máquinas virtuais nas sub-redes pública e privada:
     
        ```azurecli-interactive
        az network watcher show-next-hop --resource-group myResourceGroup --vm myVm-Public --source-ip 10.0.0.4 --dest-ip 10.0.1.4
        ```

       Devolve o resultado *10.0.2.4* como o **o nextHopIpAddress** e *VirtualAppliance* como o **nextHopType**. 

> [!NOTE]
> Para ilustrar os conceitos neste tutorial, endereços IP públicos são atribuídos a máquinas virtuais a pública e privada sub-redes e todos os acessos de porta de rede está ativado no Azure para máquinas virtuais. Ao criar máquinas virtuais para utilização em produção, não poderá atribuir endereços IP públicos aos mesmos e pode filtrar o tráfego de rede para a sub-rede privada ao implementar uma aplicação virtual de rede existentes à frente dele ou através da atribuição de um grupo de segurança de rede para as sub-redes, interface de rede, ou ambos. Para obter mais informações sobre grupos de segurança de rede, consulte [grupos de segurança de rede](virtual-networks-nsg.md).

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

Este tutorial requer uma rede virtual existente com duas sub-redes. Clique em de **experimente** botão na caixa que se segue, para criar rapidamente uma rede virtual. Ao clicar no **experimente** botão abre a [Shell de nuvem do Azure](../cloud-shell/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Embora a Shell de nuvem com o PowerShell ou numa shell de deteção, nesta secção, a shell de deteção é utilizada para criar a rede virtual. A shell de deteção tem interface de linha de comandos do Azure instalada. Se lhe for pedido, a Shell de nuvem, inicie sessão no Azure com o [conta do Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Se não tiver uma conta do Azure, pode inscrever-se para obter uma [avaliação gratuita](https://azure.microsoft.com/offers/ms-azr-0044p). Para criar a rede virtual que utilizou neste tutorial, clique o **cópia** botão na caixa seguinte, em seguida, cole o script de Shell de nuvem do Azure:

```azurecli-interactive
#!/bin/bash

#Set variables used in the script.
rgName="myResourceGroup"
location="eastus"

# Create a resource group.
az group create \
  --name $rgName \
  --location $location

# Create a virtual network with one subnet named Public.
az network vnet create \
  --name myVnet \
  --resource-group $rgName \
  --address-prefixes 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24

# Create an additional subnet named Private in the virtual network.
az network vnet subnet create \
  --name Private \
  --address-prefix 10.0.1.0/24 \
  --vnet-name myVnet \
  --resource-group $rgName
```

Para obter mais informações sobre como utilizar o portal, PowerShell ou um modelo Azure Resource Manager para criar uma rede virtual, consulte o artigo [criar uma rede virtual](virtual-networks-create-vnet-arm-pportal.md).

## <a name="delete-resources"></a>Eliminar recursos

Quando concluir este tutorial, pode querer eliminar os recursos que criou, para que não implicar custos de utilização. Também eliminar um grupo de recursos elimina todos os recursos que estão no grupo de recursos. Com o portal aberto, conclua os seguintes passos:

1. Na caixa de pesquisa de portal, introduza **myResourceGroup**. Nos resultados da pesquisa, clique em **myResourceGroup**.
2. No **myResourceGroup** painel, clique em de **eliminar** ícone.
3. Para confirmar a eliminação no **tipo o nome de grupo de recursos** box, introduza **myResourceGroup**e, em seguida, clique em **eliminar**.

## <a name="next-steps"></a>Passos seguintes

- Criar um [aplicação virtual de rede altamente disponível](/azure/architecture/reference-architectures/dmz/nva-ha?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Os dispositivos de rede virtuais têm, muitas vezes, várias interfaces de rede e endereços IP atribuídos aos mesmos. Saiba como [adicionar interfaces de rede para uma máquina virtual existente](virtual-network-network-interface-vm.md#vm-add-nic) e [adicionar endereços IP para uma interface de rede existente](virtual-network-network-interface-addresses.md#add-ip-addresses). Apesar de todos os tamanhos de máquina virtual podem ter, pelo menos, duas interfaces de rede ligados aos mesmos, cada tamanho da máquina virtual suporta um número máximo de interfaces de rede. Para saber como várias interfaces de rede virtuais de cada computador tamanho suporta, consulte [Windows](../virtual-machines/windows/sizes.md?toc=%2Fazure%2Fvirtual-network%2Ftoc.json) e [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) tamanhos de máquina virtual. 
- Criar uma rota definida pelo utilizador para forçar o túnel tráfego no local através de um [ligação de VPN de site para site](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
