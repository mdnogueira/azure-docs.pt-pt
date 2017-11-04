---
title: "Ligar a pilha do Azure ao Azure através da VPN"
description: "Como ligar redes virtuais na pilha do Azure para redes virtuais no Azure através da VPN."
services: azure-stack
documentationcenter: 
author: ScottNapolitan
manager: 
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: victorh
ms.openlocfilehash: c06eb0bb44bdfeab956e9b5051786b5bc631acf5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="connect-azure-stack-to-azure-using-vpn"></a>Ligar a pilha do Azure ao Azure através da VPN

*Aplica-se a: Azure pilha integrado sistemas*

Este artigo mostra como criar uma VPN de site a site para ligar uma rede virtual na pilha do Azure a uma rede virtual no Azure.

### <a name="connection-diagram"></a>Diagrama de ligação
O diagrama seguinte mostra que a configuração da ligação deve ter o seguinte aspeto quando tiver terminado:

![Configuração da ligação VPN site a site](media/azure-stack-connect-vpn/image2.png)

### <a name="before-you-begin"></a>Antes de começar
Para concluir a configuração da ligação, certifique-se de que tem os seguintes itens antes de começar:

* Uma pilha do Azure integrada uma implementação de sistemas (com vários nós) que estejam ligada diretamente à Internet. Isto significa que o intervalo de endereço IP público externo tem de ser diretamente acessível a partir da Internet pública.
* Uma subscrição do Azure válida.  Se não tiver uma subscrição do Azure, pode criar um [aqui a conta do Azure gratuita](https://azure.microsoft.com/free/?b=17.06).

## <a name="network-example-values-table"></a>Tabela de valores de exemplo de rede
A tabela de valores de exemplo de rede mostra os valores de exemplo que são utilizados neste artigo. Pode utilizar estes valores ou possa consultá-las para compreender melhor os exemplos neste artigo.

**Tabela de valores de exemplo de rede**
|   |Azure Stack|Azure|
|---------|---------|---------|
|Nome da rede virtual     |Azs VNet|AzureVNet |
|Espaço de endereços de rede virtual |10.1.0.0/16|10.100.0.0/16|
|Nome da sub-rede     |FrontEnd|FrontEnd|
|Intervalo de endereços da sub-rede|10.1.0.0/24 |10.100.0.0/24 |
|Sub-rede de gateway     |10.1.1.0/24|10.100.1.0/24|

## <a name="create-the-network-resources-in-azure"></a>Criar os recursos de rede no Azure

Primeiro de criar os recursos de rede do Azure. As instruções seguintes mostram como criar os recursos utilizando o [portal do Azure](http://portal.azure.com/).

### <a name="create-the-virtual-network-and-vm-subnet"></a>Criar a rede virtual e a sub-rede VM

1. Iniciar sessão para o [portal do Azure](http://portal.azure.com/) utilizando a sua conta do Azure.
2. No portal de utilizador, selecione **novo**.
3. Aceda a **Marketplace**e, em seguida, selecione **redes**.
4. Selecione **rede Virtual**.
5. Utilize as informações da tabela de configuração de rede para identificar os valores para o Azure **nome**, **espaço de endereços**, **nome de sub-rede**, e **endereço de sub-rede intervalo**.
6. Para **grupo de recursos**, crie um novo grupo de recursos ou, se já tiver uma, selecione **utilizar existente**.
7. Selecione o **localização** da sua VNet.  Se estiver a utilizar os valores de exemplo, selecione **EUA Leste** ou utilize outra localização, se preferir.
8. Selecione **Afixar ao dashboard**.
9. Selecione **Criar**.

### <a name="create-the-gateway-subnet"></a>Criar a Sub-rede de Gateway
1. Abra o recurso de rede Virtual que criou (**AzureVNet**) partir do dashboard.
2. No **definições** secção, selecione **sub-redes**.
3. Selecione **sub-rede do Gateway** para adicionar uma sub-rede de gateway para a rede virtual.
4. O nome da sub-rede está predefinido como **GatewaySubnet**.
   As sub-redes de gateway são especiais e têm de ter este nome específico para funcionarem corretamente.
5. No **intervalo de endereços** campo, certifique-se de que o endereço é **10.100.0.0/24**.
6. Selecione **OK** para criar a sub-rede do gateway.

### <a name="create-the-virtual-network-gateway"></a>Criar o gateway de rede virtual
1. No portal do Azure, selecione **novo**.  
2. Aceda a **Marketplace**e, em seguida, selecione **redes**.
3. Na lista de recursos de rede, selecione **gateway de rede Virtual**.
4. No **nome**, tipo **Azure-GW**.
5. Para escolher uma rede virtual, selecione **rede Virtual**. Em seguida, selecione **AzureVnet** da lista.
6. Selecione **endereço IP público**. Quando o **escolher endereço IP público** secção se abre, selecione **criar nova**.
7. No **nome**, tipo **GW-Azure-PiP**e, em seguida, selecione **OK**.
8. Por predefinição, para **tipo de VPN**, **baseado na rota** está selecionada.
    Manter o **baseado na rota** tipo de VPN.
9. Certifique-se de que a **Subscrição** e a **Localização** estão corretas. Pode afixar o recurso ao dashboard. Selecione **Criar**.

### <a name="create-the-local-network-gateway-resource"></a>Criar o recurso de gateway de rede local

1. No portal do Azure, selecione **novo**. 
4. Aceda a **Marketplace**e, em seguida, selecione **redes**.
5. Na lista de recursos, selecione **gateway de rede Local**.
6. No **nome**, tipo **Azs-GW**.
7. No **endereço IP**, escreva o endereço IP público para o Azure pilha de Gateway de rede Virtual que esteja listado anteriormente na tabela de configuração de rede.
8. No **espaço de endereços**, a partir de pilha do Azure, escreva o **10.0.10.0/23** espaço de endereços **AzureVNet**.
9. Certifique-se de que o **subscrição**, **grupo de recursos**, e **localização** estão corretos e, em seguida, selecione **criar**.

## <a name="create-the-connection"></a>Criar a ligação
1. No portal de utilizador, selecione **novo**. 
2. Aceda a **Marketplace**e, em seguida, selecione **redes**.
3. Na lista de recursos, selecione **ligação**.
4. No **básico** secção de definições para o **tipo de ligação**, escolha **Site a site (IPSec)**.
5. Selecione o **subscrição**, **grupo de recursos**, e **localização**e, em seguida, selecione **OK**.
6. No **definições** secção, selecione **gateway de rede Virtual**e, em seguida, selecione **Azure-GW**.
7. Selecione **gateway de rede Local**e, em seguida, selecione **Azs-GW**.
8. No **nome da ligação**, tipo **Azure Azs**.
9. No **chave partilhada (PSK)**, tipo **12345**. Se escolher um valor diferente, lembre-se de que as TI *tem* corresponde ao valor para a chave partilhada que criou na outra extremidade da ligação. Selecione **OK**.
10. Reveja o **resumo** secção e, em seguida, selecione **OK**.

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual
Criar uma máquina virtual no Azure agora e colocá-la na sua sub-rede VM na sua rede virtual.

1. No portal do Azure, selecione **novo**.
2. Aceda a **Marketplace**e, em seguida, selecione **computação**.
3. Na lista de imagens da máquina virtual, selecione o **Windows Server 2016 Datacenter Eval** imagem.
4. No **Noções básicas** secção, para **nome**, tipo **AzureVM**.
5. Escreva um nome de utilizador válido e palavra-passe. Pode utilizar esta conta para iniciar sessão para a máquina virtual depois de criado.
6. Forneça um **subscrição**, **grupo de recursos**, e **localização**e, em seguida, selecione **OK**.
7. No **tamanho** secção, selecione um tamanho de máquina virtual para esta instância e, em seguida, selecione **selecione**.
8. No **definições** secção, pode aceitar as predefinições. Certifique-se de que o **AzureVnet** rede virtual está selecionada e certifique-se de que a sub-rede está definida como **10.0.20.0/24**. Selecione **OK**.
9. Reveja as definições no **resumo** secção e, em seguida, selecione **OK**.

## <a name="create-the-network-resources-in-azure-stack"></a>Criar os recursos de rede na pilha do Azure
Em seguida, criar os recursos de rede na pilha do Azure.

### <a name="sign-in-as-a-user"></a>Inicie sessão como um utilizador
Um administrador de serviço pode iniciar sessão como um utilizador para testar a planos, ofertas e subscrições que poderão utilizar os seus utilizadores. Se ainda não tiver um, [criar uma conta de utilizador](azure-stack-add-new-user-aad.md) antes de iniciar sessão.

### <a name="create-the-virtual-network-and-vm-subnet"></a>Criar a rede virtual e a sub-rede VM
1. Utilize uma conta de utilizador para iniciar sessão no portal de utilizador.
2. No portal de utilizador, selecione **novo**.

    ![Criar nova rede virtual](media/azure-stack-create-vpn-connection-one-node-tp2/image3.png)

3. Aceda a **Marketplace**e, em seguida, selecione **redes**.
4. Selecione **rede Virtual**.
5. Para **nome**, **espaço de endereços**, **nome de sub-rede**, e **intervalo de endereços da sub-rede**, utilize os valores da tabela de configuração de rede.
6. No **subscrição**, é apresentada a subscrição que criou anteriormente.
7. Para **grupo de recursos**, pode criar um grupo de recursos ou se já tiver uma, selecione **utilizar existente**.
8. Verifique a localização predefinida.
9. Selecione **Afixar ao dashboard**.
10. Selecione **Criar**.

### <a name="create-the-gateway-subnet"></a>Criar a sub-rede de gateway
1. No dashboard, abra o recurso de rede virtual Azs VNet que criou.
2. No **definições** secção, selecione **sub-redes**.
3. Para adicionar uma sub-rede de gateway para a rede virtual, selecione **sub-rede do Gateway**.
   
    ![Adicionar sub-rede do gateway](media/azure-stack-create-vpn-connection-one-node-tp2/image4.png)

4. Por predefinição, o nome de sub-rede está definido como **GatewaySubnet**.
   As sub-redes de gateway são especiais. Para funcionar corretamente, terão de utilizar o *GatewaySubnet* nome.
5. No **intervalo de endereços**, certifique-se de que o endereço está **10.1.1.0/24**.
6. Selecione **OK** para criar a sub-rede do gateway.

### <a name="create-the-virtual-network-gateway"></a>Criar o gateway de rede virtual
1. No portal do Azure pilha, selecione **novo**. 
2. Aceda a **Marketplace**e, em seguida, selecione **redes**.
3. Na lista de recursos de rede, selecione **gateway de rede Virtual**.
4. No **nome**, tipo **Azs-GW**.
5. Selecione o **rede Virtual** item para escolher uma rede virtual.
   Selecione **Azs VNet** da lista.
6. Selecione o **endereço IP público** item de menu. Quando o **escolher endereço IP público** secção se abre, selecione **criar nova**.
7. No **nome**, tipo **Azs-GW-PiP**e, em seguida, selecione **OK**.
8.  Por predefinição, para **tipo de VPN**, **baseado na rota** está selecionada.
    Manter o **baseado na rota** tipo de VPN.
9. Certifique-se de que a **Subscrição** e a **Localização** estão corretas. Pode afixar o recurso ao dashboard. Selecione **Criar**.

### <a name="create-the-local-network-gateway"></a>Criar o gateway de rede local
A noção de um *gateway de rede local* na pilha do Azure é um pouco diferente numa implementação do Azure.

Numa implementação do Azure, um gateway de rede local representa um dispositivo físico no local (a localização do utilizador), que utiliza para ligar a um gateway de rede virtual no Azure. Na pilha do Azure, ambas as extremidades da ligação são gateways de rede virtual!

Uma forma de pensar nesta mais genericamente é que o recurso de gateway de rede local indica sempre o gateway remoto no final da ligação. 

### <a name="create-the-local-network-gateway-resource"></a>Criar o recurso de gateway de rede local
1. Inicie sessão no portal do Azure pilha.
2. No portal de utilizador, selecione **novo**.
3. Aceda a **Marketplace**e, em seguida, selecione **redes**.
4. Na lista de recursos, selecione **gateway de rede local**.
5. No **nome**, tipo **Azure-GW**.
6. No **endereço IP**, escreva o endereço IP público para o gateway de rede virtual no Azure **GW-Azure-PiP**. Este endereço é apresentado anteriormente na tabela de configuração de rede.
7. No **espaço de endereços**, para o espaço de endereços da VNET do Azure que criou, escreva **10.0.20.0/23**.
8. Certifique-se de que o **subscrição**, **grupo de recursos**, e **localização** estão corretos e, em seguida, selecione **criar**.

### <a name="create-the-connection"></a>Criar a ligação
1. No portal de utilizador, selecione **novo**.
2. Aceda a **Marketplace**e, em seguida, selecione **redes**.
3. Na lista de recursos, selecione **ligação**.
4. No **Noções básicas** secção de definições para o **tipo de ligação**, selecione **Site a site (IPSec)**.
5. Selecione o **subscrição**, **grupo de recursos**, e **localização**e, em seguida, selecione **OK**.
6. No **definições** secção, selecione **gateway de rede Virtual**e, em seguida, selecione **Azs-GW**.
7. Selecione **gateway de rede Local**e, em seguida, selecione **Azure-GW**.
8. No **nome da ligação**, tipo **Azs Azure**.
9. No **chave partilhada (PSK)**, tipo **12345**e, em seguida, selecione **OK**.
10. No **resumo** secção, selecione **OK**.

### <a name="create-a-vm"></a>Criar uma VM
Para validar os dados que circulam através da ligação VPN, terá de criar máquinas virtuais em cada extremidade para enviar e receber dados através do túnel VPN. 

1. No portal do Azure, selecione **novo**.
2. Aceda a **Marketplace**e, em seguida, selecione **computação**.
3. Na lista de imagens da máquina virtual, selecione o **Windows Server 2016 Datacenter Eval** imagem.
4. No **Noções básicas** na secção **nome**, tipo **Azs VM**.
5. Escreva um nome de utilizador válido e palavra-passe. Pode utilizar esta conta para iniciar sessão para a VM, depois de criado.
6. Forneça um **subscrição**, **grupo de recursos**, e **localização**e, em seguida, selecione **OK**.
7. No **tamanho** secção, para esta instância, selecione um tamanho de máquina virtual e, em seguida, selecione **selecione**.
8. No **definições** secção, aceite as predefinições. Certifique-se de que o **Azs VNet** rede virtual está selecionada. Certifique-se de que a sub-rede está definida como **10.1.0.0/24**. Em seguida, selecione **OK**.
9. No **resumo** secção, reveja as definições e, em seguida, selecione **OK**.


## <a name="test-the-connection"></a>Testar a ligação
Agora que é estabelecida a ligação de site a site, deve validar que pode obter o tráfego que flui através do mesmo. Para validar, iniciar sessão para uma das máquinas virtuais que criou na pilha do Azure. Em seguida, executar um ping a máquina virtual que criou no Azure. 

Para se certificar de que enviar o tráfego através da ligação site a site, executar um ping o endereço IP direto (DIP) da máquina virtual na sub-rede remota, não o VIP. Para tal, localize o endereço na outra extremidade da ligação. Guarde o endereço para utilização posterior.

### <a name="sign-in-to-the-user-vm-in-azure-stack"></a>Iniciar sessão para o utilizador VM na pilha do Azure
1. Inicie sessão no portal do Azure pilha.
2. Na barra de navegação esquerdo, selecione **máquinas virtuais**.
3. Na lista de VMs, localizar **Azs VM** que criou anteriormente e, em seguida, selecioná-lo.
4. Na secção para a máquina virtual, clique em **Connect**e, em seguida, abra o ficheiro de Azs VM.rdp.
   
     ![Botão de ligar](media/azure-stack-create-vpn-connection-one-node-tp2/image17.png)
5. Inicie sessão com a conta que configurou quando criou a máquina virtual.
6. Abra uma **do Windows PowerShell** janela.
7. Escreva **ipconfig /all**.
8. O resultado, localize o **endereço IPv4**e, em seguida, guarde o endereço para utilização posterior. Este é o endereço que vai enviar um ping a partir do Azure. No ambiente de exemplo, o endereço é **10.0.10.4**, mas no seu ambiente poderá ser diferente. Este deve coincidir com o **10.0.10.0/24** sub-rede que criou anteriormente.
9. Para criar uma regra de firewall que permite que a máquina virtual responder a pings, execute o seguinte comando do PowerShell:

   ```powershell
   New-NetFirewallRule `
    –DisplayName “Allow ICMPv4-In” `
    –Protocol ICMPv4
   ```

### <a name="sign-in-to-the-tenant-vm-in-azure"></a>Inicie sessão no inquilino do VM no Azure
1. Inicie sessão no Portal do Azure.
2. Na barra de navegação esquerdo, clique em **máquinas virtuais**.
3. Na lista de máquinas virtuais, localizar **VM do Azure** que criou anteriormente e, em seguida, selecioná-lo.
4. Na secção para a máquina virtual, clique em **Connect**.
5. Inicie sessão com a conta que configurou quando criou a máquina virtual.
6. Abra uma **do Windows PowerShell** janela.
7. Escreva **ipconfig /all**.
8. Deverá ver um endereço de IPv4 que se encontrem dentro **10.0.20.0/24**. No ambiente de exemplo, o endereço é **10.0.20.4**, mas o seu endereço de poderão ser diferente.
9. Para criar uma regra de firewall que permite que a máquina virtual responder a pings, execute o seguinte comando do PowerShell:

   ```powershell
   New-NetFirewallRule `
    –DisplayName “Allow ICMPv4-In” `
    –Protocol ICMPv4
   ```

10. Da máquina virtual no Azure, executar um ping a máquina virtual na pilha do Azure, através do túnel. Para tal, ping DIP que registou Azs VM.
   No ambiente de exemplo, isto é **10.0.10.4**, mas certifique-se enviar um ping o endereço que anotou no laboratório. Deverá ver um resultado semelhante à captura de ecrã seguinte:
   
    ![Ping enviado com êxito](media/azure-stack-create-vpn-connection-one-node-tp2/image19b.png)
11. Uma resposta da máquina virtual remota indica um teste com êxito! Pode fechar a janela de máquina virtual. Para testar a ligação, pode tentar outros tipos de transferências de dados, como uma cópia de ficheiros.

### <a name="viewing-data-transfer-statistics-through-the-gateway-connection"></a>Ver estatísticas de transferência de dados através da ligação de gateway
Se pretender saber a quantidade de dados atravessa a ligação de site a site, esta informação está disponível na **ligação** secção. Este teste também é outra forma de verificar que o ping que acabámos de enviar, na verdade, Ocorreu um erro através da ligação VPN.

1. Enquanto tem sessão iniciada para a máquina virtual de utilizador na pilha do Azure, utilize a sua conta de utilizador para iniciar sessão no portal de utilizador.
2. Aceda a **todos os recursos**e, em seguida, selecione o **Azs Azure** ligação. **Ligações** aparece.
4. No **ligação** secção, as estatísticas de **dados** e **a dados extra** aparecer. Na captura de ecrã seguinte, os números de grandes dimensões são atribuída ao utilizador a transferência de ficheiro adicionais. Deverá ver alguns valores diferentes de zero não existe.
   
    ![Dados de entrada e saída](media/azure-stack-connect-vpn/Connection.png)

## <a name="next-steps"></a>Passos seguintes

[Implementar aplicações do Azure e Azure pilha](azure-stack-solution-pipeline.md)