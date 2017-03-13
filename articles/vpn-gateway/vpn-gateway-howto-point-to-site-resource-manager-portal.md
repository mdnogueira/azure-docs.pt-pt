---
title: "Ligar um computador a uma rede virtual do Azure através de Ponto a Site: Portal | Microsoft Docs"
description: "Crie uma ligação de gateway de VPN Ponto a Site com o Resource Manager e o portal do Azure para se ligar à sua Rede Virtual do Azure em segurança."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: a15ad327-e236-461f-a18e-6dbedbf74943
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/02/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: cea53acc33347b9e6178645f225770936788f807
ms.openlocfilehash: bc3d0252b3aef6a62c863b79839557e165d9f121
ms.lasthandoff: 03/03/2017


---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-the-azure-portal"></a>Configurar uma ligação Ponto a Site a uma VNet com o portal do Azure
> [!div class="op_single_selector"]
> * [Resource Manager - Portal do Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [Resource Manager – PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Clássica – Portal do Azure](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
> 
> 

Uma configuração Ponto a Site (P2S) permite-lhe criar uma ligação segura a partir de um computador cliente individual para a sua rede virtual. Uma ligação P2S é útil quando pretende ligar a VNet a partir de uma localização remota, por exemplo, quando está em casa ou numa conferência ou quando tem apenas alguns clientes que precisam de se ligar a uma rede virtual. 

As ligações Ponto a Site não precisam de nenhum dispositivo VPN ou endereço IP destinado ao público para funcionar. É estabelecida uma ligação VPN ao iniciar a ligação a partir do computador cliente. Para obter mais informações sobre ligações de Ponto a Site, consulte [Point-to-Site FAQ (FAQ sobre Ponto a Site)](#faq) no final deste artigo.

Este artigo explica-lhe como criar uma VNet com uma ligação Ponto a Site através do portal do Azure. Estes passos aplicam-se ao modelo de implementação clássica e do Resource Manager.

### <a name="deployment-models-and-methods-for-p2s-connections"></a>Modelos e métodos de implementação para ligações P2S
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

A tabela seguinte mostra os dois modelos de implementação e métodos de implementação disponíveis para configurações P2S. Quando estiver disponível um artigo com passos de configuração, criamos uma ligação direta para o mesmo nesta tabela.

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="basic-workflow"></a>Fluxo de trabalho básico
![Diagrama Ponto a Site](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/point-to-site-connection-diagram.png)

### <a name="example"></a>Valores de exemplo
* **Nome: VNet1**
* **Espaço de endereços: 192.168.0.0/16**<br>Para este exemplo, utilizamos apenas um espaço de endereços. Pode ter mais do que um espaço de endereços para a sua VNet.
* **Nome da sub-rede: FrontEnd**
* **Intervalo de endereços da sub-rede: 192.168.1.0/24**
* **Subscrição:** se tiver várias subscrições, verifique se tem a correta.
* **Grupo de Recursos: TestRG**
* **Localização: E.U.A. Leste**
* **GatewaySubnet: 192.168.200.0/24**
* **Nome do gateway de rede virtual: VNet1GW**
* **Tipo de Gateway: VPN**
* **Tipo de VPN: baseado na rota**
* **Endereço IP público: VNet1GWpip**
* **Tipo de ligação: Ponto a site**
* **Conjunto de endereços de cliente: 172.16.201.0/24**<br>Os clientes VPN que se ligam à VNet através desta ligação Ponto a Site recebem um endereço IP a partir do conjunto de endereços de cliente especificado.

## <a name="before-beginning"></a>Antes de começar
* Verifique se tem uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, pode ativar os [Benefícios de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) ou inscrever-se numa [conta gratuita](https://azure.microsoft.com/pricing/free-trial).

## <a name="createvnet"></a>Parte 1: criar uma rede virtual
Se estiver a criar esta configuração como um exercício, pode ver os [valores de exemplo](#example).

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="address"></a>Parte 2: Especificar espaços de endereços e sub-redes
Pode adicionar sub-redes e espaços de endereços adicionais à VNet depois de ter sido criada.

[!INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)]

## <a name="gatewaysubnet"></a>Parte 3: Adicionar uma sub-rede do gateway

Antes de ligar a rede virtual a um gateway, primeiro tem de criar a sub-rede do gateway para a rede virtual à qual pretende ligar. Os serviços do gateway utilizam os endereços IP especificados na sub-rede de gateway. Se possível, crie uma sub-rede do gateway com um bloco CIDR de /28 ou /27, para fornecer endereços IP suficientes para contemplar requisitos de configuração futuros adicionais.

As capturas de ecrã nesta secção são fornecidas como um exemplo de referência. Certifique-se de que utiliza o intervalo de endereços GatewaySubnet correspondente aos valores necessários para a sua configuração.

###<a name="to-create-a-gateway-subnet"></a>Para criar uma sub-rede do gateway

[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

## <a name="dns"></a>Parte 4: Especificar um servidor DNS (opcional)
[!INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="creategw"></a>Parte 5: Criar um gateway de rede virtual
As ligações ponto a site exigem as seguintes definições:

* Tipo de gateway: VPN
* Tipo de VPN: baseado na rota

### <a name="to-create-a-virtual-network-gateway"></a>Para criar um gateway de rede virtual
[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="generatecert"></a>Parte 6: Gerar certificados
O Azure utiliza os certificados para autenticar os clientes VPN em VPNs Ponto a Site. Exporte os dados do certificado público (e não a chave privada) como um ficheiro .cer X.509 codificado com Base-64 de um certificado de raiz gerado por uma solução de certificados de empresa ou um certificado de raiz autoassinado. Em seguida, importe os dados do certificado público a partir do certificado de raiz para o Azure. Além disso, precisa de gerar um certificado de cliente a partir do certificado de raiz para os clientes. Cada cliente que pretenda ligar-se à rede virtual através de uma ligação de P2S tem de ter instalado um certificado de cliente que foi gerado a partir do certificado de raiz.

### <a name="getcer"></a>Passo 1: Obter o ficheiro .cer para o certificado de raiz

Se estiver a utilizar uma solução empresarial, pode servir-se da sua cadeia de certificados existente. Obtenha o ficheiro .cer para o certificado de raiz que pretende utilizar.

Se não estiver a utilizar uma solução de certificados de empresa, tem de criar um certificado de raiz autoassinado. Para criar um certificado autoassinado que contenha os campos necessários para autenticação P2S, utilize o makecert. [Criar um certificado de raiz autoassinado para ligações P2S](vpn-gateway-certificates-point-to-site.md) descreve os passos para criar um certificado de raiz autoassinado. Estamos cientes de que o makecert foi preterido mas, de momento, é a solução suportada.

>[!NOTE]
>Embora seja possível utilizar o PowerShell para criar certificados autoassinados, o certificado gerado com o PowerShell não contém os campos necessários para autenticação Ponto a Site.
>


#### <a name="to-obtain-the-cer-file-from-a-self-signed-root-certificate"></a>Para obter o ficheiro .cer a partir de um certificado de raiz autoassinado

1. Para obter um ficheiro .cer a partir de um certificado de raiz autoassinado, abra **certmgr.msc** e localize o certificado de raiz que criou. Normalmente, o certificado está localizado em “'Certificates-Current User/ Personal/Certificates” e tem o nome que lhe atribuiu quando o criou. Clique no certificado de raiz autoassinado com o botão direito, clique em **todas as tarefas** e, em seguida, clique em **exportar**. Esta ação abre o **Assistente para Exportar Certificados**.
2. No Assistente, clique em **Seguinte**, selecione **Não, não exportar a chave privada** e, em seguida, clique em **Seguinte**.
3. Na página **Formato do Ficheiro de Exportação**, selecione **X.509 codificado com Base-64 (*.CER).** Clique depois em **Seguinte**.
4. Em **Ficheiro a Exportar**, **Navegue** até à localização para a qual pretende exportar o certificado. Em **Nome do ficheiro**, atribua um nome ao ficheiro de certificado. Clique depois em **Seguinte**.
5. Clique em **Concluir** para exportar o certificado.

### <a name="generateclientcert"></a>Passo 2: Gerar um certificado de cliente
Pode optar por gerar um certificado exclusivo para cada cliente que irá ligar à rede virtual, ou pode utilizar o mesmo certificado em vários clientes. A vantagem de gerar certificados de cliente únicos é a capacidade de revogar um certificado único se for necessário. Caso contrário, se toda a gente estiver a utilizar o mesmo certificado de cliente e precisar de revogar o certificado para um cliente, terá de gerar e instalar novos certificados para todos os clientes que utilizam o certificado para autenticação.

####<a name="enterprise-certificate"></a>Certificado da empresa
- Se estiver a utilizar uma solução de certificado da empresa, gere um certificado de cliente com o formato do valor de nome comum “name@yourdomain.com”, em vez do formato “nome de domínio/nome de utilizador”.
- Certifique-se de que o certificado de cliente que emitiu é baseado no modelo de certificado "Utilizador" que tem a "Autenticação de Cliente" como o primeiro item na lista de utilização, em vez do Início de Sessão de Smart Card, etc. Pode verificar o certificado ao clicar duas vezes no certificado de cliente e visualizar **Detalhes > Utilização Avançada da Chaves**.

####<a name="self-signed-certificate"></a>Certificado autoassinado 
Se estiver a utilizar um certificado de raiz autoassinado, consulte [Trabalhar com certificados de raiz autoassinados para configurações de Ponto a Site](vpn-gateway-certificates-point-to-site.md), para gerar um certificado de cliente.

### <a name="exportclientcert"></a>Passo 3: Exportar o certificado de cliente
É necessário um certificado de cliente para a autenticação. Após a gerar o certificado de cliente, exporte-o. O certificado de cliente que exportar será instalado posteriormente em cada computador cliente.

1. Para exportar um certificado de cliente, pode utilizar *certmgr.msc*. Clique com o botão direito do rato no certificado de cliente que pretende exportar, clique em **todas as tarefas** e, em seguida, em **exportar**.
2. Exporte o certificado de cliente com a chave privada. Este é um ficheiro *.pfx*. Certifique-se de que regista ou memoriza a palavra-passe (chave) que define para este certificado.

## <a name="addresspool"></a>Parte 7: Adicionar o conjunto de endereços de cliente
1. Quando o gateway de rede virtual tiver sido criado, navegue para a secção **Definições** do painel do gateway de rede virtual. Na secção **Definições**, clique em **Configuração ponto a site** para abrir o painel **Configuração**.
   
    ![Painel Ponto a Site](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configuration.png)
2. **Conjunto de endereços** é o conjunto de endereços IP a partir do qual os clientes que se ligam irão receber um endereço IP. Adicione o conjunto de endereços e, em seguida, clique em **Guardar**.
   
    ![Conjunto de endereços de cliente](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/ipaddresspool.png)

## <a name="uploadfile"></a>Parte 8: Carregar o ficheiro .cer do certificado de raiz
Depois de o gateway ter sido criado, pode carregar o ficheiro .cer de um certificado de raiz fidedigno para o Azure. Pode carregar ficheiros de um máximo de 20 certificados de raiz. Não carregue a chave privada do certificado de raiz para o Azure. Após o carregamento do ficheiro .cer, o Azure utiliza-lo para autenticar os clientes que se ligam à rede virtual.

1. Os certificados são adicionados no painel **Configuração ponto a site** na secção **Certificado de raiz**.  
2. Certifique-se de que exportou o certificado de raiz como um ficheiro X.509 codificado com Base-64 (.cer). Tem de exportá-lo neste formato para poder abrir o certificado com o editor de texto.
3. Abra o certificado com um editor de texto, como o Bloco de Notas. Copie apenas a secção seguinte como uma linha contínua:
   
    ![Dados de certificado](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/copycert.png)

    > [!NOTE]
    > Quando copiar os dados de certificado, certifique-se de que copia o texto como uma linha contínua sem símbolos de retorno ou avanços de linha. Poderá ter de modificar a sua vista no editor de texto para "Mostrar Símbolo/Mostrar todos os carateres" para ver os símbolos de retorno ou avanços de linha.                                                                                                                                                                            
    >
    >

4. Cole os dados de certificado no campo **Dados de Certificado Público**. **Nomeie** o certificado e, em seguida, clique em **Guardar**. Pode adicionar até 20 certificados de raiz fidedigna.
   
    ![Carregamento de certificados](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/rootcertupload.png)

## <a name="clientconfig"></a>Parte 9: Transferir e instalar o pacote de configuração do cliente VPN
Os clientes que se liguem ao Azure com P2S têm de ter um certificado de cliente e um pacote de instalação de cliente de VPN instalados. Os pacotes de configuração do cliente VPN estão disponíveis para clientes Windows. 

O pacote de clientes VPN contém informações para configurar o software de cliente VPN incorporado no Windows. A configuração é específica da VPN à qual se quer ligar. O pacote não instala software adicional.

Pode utilizar o mesmo pacote de configuração do cliente VPN em cada computador cliente, desde que a versão corresponda à arquitetura do cliente.

### <a name="step-1---download-the-client-configuration-package"></a>Passo 1 - transferir o pacote de configuração do cliente

1. No painel **Configuração ponto a site**, clique em **Transferir cliente VPN** para abrir o painel **Transferir cliente VPN**. Demora um minuto ou dois para o pacote ser gerado.
   
    ![transferência do cliente VPN 1](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/downloadvpnclient1.png)
2. Selecione o pacote correto para o cliente e, em seguida, clique em **Transferir**. Guarde o ficheiro do pacote de configuração. Vai instalá-lo em cada computador cliente que se vai ligar à rede virtual.

    ![transferência do cliente VPN 2](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/client.png)

   * Para clientes de 64 bits, selecione **AMD64**.
   * Para clientes de 32 bits, selecione **x86**.

### <a name="step-2---install-the-client-configuration-package"></a>Passo 2 - instalar o pacote de configuração do cliente

1. Copie o ficheiro de configuração localmente para o computador que pretende ligar à rede virtual. 
2. Faça duplo clique no ficheiro .exe para instalar o pacote no computador cliente. Como o pacote de configuração foi criado por si, o mesmo não está assinado. Isto significa que poderá ver um aviso. Se obtiver um pop-up SmartScreen do Windows, clique em **Mais informações** (no lado esquerdo) e, em seguida, em **Executar mesmo assim** para instalar o pacote.
3. No computador cliente, navegue para **Definições de Rede** e clique em **VPN**. Verá a ligação listada. Mostra o nome da rede virtual à qual se vai ligar e terá um aspeto semelhante a:
3. Instale o pacote no computador cliente. Se obtiver um pop-up SmartScreen do Windows, clique em **Mais informações** (no lado esquerdo) e, em seguida, em **Executar mesmo assim** para instalar o pacote.
4. No computador cliente, navegue para **Definições de Rede** e clique em **VPN**. Verá a ligação listada. Mostrará o nome da rede virtual à qual se vai ligar e terá um aspeto semelhante a este exemplo: 
   
    ![cliente de VPN](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/vpn.png)


## <a name="installclientcert"></a>Parte 10: Instalar o certificado de cliente
Cada computador cliente tem de ter um certificado de cliente para se autenticar. Ao instalar o certificado de cliente, vai precisar da palavra-passe que foi criada quando o certificado de cliente foi exportado.

1. Copie o ficheiro .pfx para o computador cliente.
2. Faça duplo clique no ficheiro .pfx para o instalar. Não modifique a localização da instalação.

## <a name="connect"></a>Parte 11: Ligar ao Azure
1. Para se ligar à sua VNet, no computador cliente, navegue até às ligações VPN e localize a ligação VPN que criou. Tem o mesmo nome da sua rede virtual. Clique em **Ligar**. Poderá aparecer uma mensagem pop-up que se refere à utilização do certificado. Se isto ocorrer, clique em **Continuar** para utilizar privilégios elevados. 
2. Na página de estado da **Ligação**, clique em **Ligar** para iniciar a ligação. Se vir um ecrã **Selecionar Certificado**, verifique se o certificado de cliente apresentado é aquele que pretende utilizar para se ligar. Se não for, utilize a seta para baixo para selecionar o certificado correto e clique em **OK**.
   
    ![Ligação de cliente VPN ao Azure](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/clientconnect.png)

    
3. A ligação deve ser agora estabelecida.
   
    ![Cliente VPN ligado ao Azure](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/connected.png)
                                                                                                                                                                           

> [!NOTE]
> Se estiver a utilizar um certificado que foi emitido através de uma solução de AC Empresarial e se deparou com problemas de autenticação, verifique a ordem de autenticação do certificado de cliente. Pode verificar a ordem da lista de autenticação ao clicar duas vezes no certificado de cliente e aceder a **Detalhes > Utilização Avançada de Chaves**. Certifique-se de que a lista apresenta "Autenticação de Cliente" como o primeiro item. Caso contrário, terá de emitir um certificado de cliente com base no modelo de Utilizador que tenha a Autenticação de Cliente como o primeiro item na lista. 
>
>

## <a name="verify"></a>Parte 12: Verificar a ligação
1. Para verificar se a ligação VPN está ativa, abra uma linha de comandos elevada e execute *ipconfig/all*.
2. Veja os resultados. Repare que o endereço IP que recebeu é um dos endereços dentro do Conjunto de Endereços de Cliente de VPN de Ponto a Site que especificou na configuração. Os resultados deverão ser algo semelhante a isto:
   
        PPP adapter VNet1:
            Connection-specific DNS Suffix .:
            Description.....................: VNet1
            Physical Address................:
            DHCP Enabled....................: No
            Autoconfiguration Enabled.......: Yes
            IPv4 Address....................: 172.16.201.3(Preferred)
            Subnet Mask.....................: 255.255.255.255
            Default Gateway.................:
            NetBIOS over Tcpip..............: Enabled

## <a name="add"></a>Adicionar ou remover certificados de raiz fidedigna
Pode adicionar e remover certificados de raiz fidedigna do Azure. Quando remove um certificado fidedigno, os certificados de cliente que foram gerados a partir do certificado de raiz já não poderão ser ligados ao Azure via Ponto a Site. Se pretender que os clientes estabeleçam ligação, terá de instalar um novo certificado de cliente que seja gerado a partir de um certificado considerado fidedigno no Azure.

### <a name="to-add-a-trusted-root-certificate"></a>Para adicionar um certificado de raiz fidedigna

Pode adicionar até 20 ficheiros .cer de certificado de raiz fidedigna ao Azure. Para obter instruções, veja a secção [Carregar um certificado de raiz fidedigna](#uploadfile) neste artigo.

### <a name="to-remove-a-trusted-root-certificate"></a>Para remover um certificado de raiz fidedigna

1. Para remover um certificado de raiz fidedigna, navegue para o painel **Configuração Ponto a Site** do gateway de rede virtual.
2. Na secção **Certificado de raiz** do painel, localize o certificado que pretende remover.
3. Clique nas reticências junto ao certificado e, em seguida, clique em “Remover”.

## <a name="revokeclient"></a>Revogar um certificado de cliente
Pode revogar certificados de cliente. A lista de revogação de certificado permite-lhe negar seletivamente a conectividade de Ponto a Site, com base em certificados de cliente individuais. Isto é diferente da remoção de um certificado de raiz fidedigna. Se remover um certificado de raiz .cer fidedigno do Azure, revoga o acesso a todos os certificados de cliente gerados/assinados pelo certificado de raiz revogado. Ao revogar um certificado de cliente, em vez do certificado de raiz, permite que os outros certificados que foram gerados a partir do certificado de raiz continuem a ser utilizados para autenticação da ligação Ponto a Site.

A prática comum é utilizar o certificado de raiz para gerir o acesso nos níveis de equipa ou organização e utilizar certificados de cliente revogados para controlo de acesso detalhado dos utilizadores individuais.

### <a name="to-revoke-a-client-certificate"></a>Para revogar um certificado de cliente

Pode revogar um certificado de cliente, ao adicionar o thumbprint à lista de revogação.

1. Obtenha o thumbprint do certificado de cliente. Para obter mais informações, veja [Como obter o Thumbprint de um Certificado](https://msdn.microsoft.com/library/ms734695.aspx).
2. Copie as informações para um editor de texto e remova todos os espaços, para que seja uma cadeia contínua.
3. Navegue para o painel **Configuração Ponto a Site** do gateway de rede virtual. Este é o painel que utilizou para [carregar um certificado de raiz fidedigna](#uploadfile).
4. Na secção **Certificados revogados**, introduza um nome amigável para o certificado (não tem de ser o CN do certificado).
5. Copie e cole a cadeia de thumbprint para o campo **Thumbprint**.
6. O thumbprint será validado e adicionado automaticamente à lista de revogação. Verá uma mensagem no ecrã a indicar que a lista está a atualizar. 
7. Depois de concluída a atualização, o certificado já não pode ser utilizado para ligar. Os clientes que tentarem ligar com este certificado irão receber uma mensagem a indicar que o certificado já não é válido.

## <a name="faq"></a>FAQ Ponto a Site

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-point-to-site-faq-include.md)]

## <a name="next-steps"></a>Passos seguintes
Assim que a ligação estiver concluída, pode adicionar máquinas virtuais às redes virtuais. Para obter mais informações, veja [Máquinas Virtuais](https://docs.microsoft.com/azure/#pivot=services&panel=Compute).


