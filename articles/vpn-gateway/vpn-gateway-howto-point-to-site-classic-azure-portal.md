---
title: "Ligar um computador a uma rede virtual do Azure através de Ponto a Site: Portal do Azure: clássico | Microsoft Docs"
description: "Crie uma ligação de gateway de VPN Ponto a Site com o portal do Azure para se ligar à sua Rede Virtual do Azure clássica em segurança."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 65e14579-86cf-4d29-a6ac-547ccbd743bd
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/08/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: d6e58625e7f90e6290268b55e488540436d3a4da
ms.lasthandoff: 03/11/2017


---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-the-azure-portal-classic"></a>Configurar uma ligação Ponto a Site a uma VNet com o portal do Azure (clássico)
> [!div class="op_single_selector"]
> * [Resource Manager - Portal do Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [Resource Manager – PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Clássica – Portal do Azure](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
>
>

Uma configuração Ponto a Site (P2S) permite-lhe criar uma ligação segura a partir de um computador cliente individual para a sua rede virtual. Uma P2S é uma ligação VPN através de SSTP (Secure Socket Tunneling Protocol). As ligações Ponto a Site são úteis quando quer ligar a VNet a partir de uma localização remota, por exemplo, quando está em casa ou numa conferência ou quando tem apenas alguns clientes que precisam de se ligar a uma rede virtual. As ligações P2S não precisam de nenhum dispositivo VPN ou endereço IP destinado ao público. O utilizador estabelece a ligação VPN a partir do computador cliente.

Este artigo explica-lhe como criar uma VNet com uma ligação Ponto a Site no modelo de implementação clássica através do portal do Azure. Para obter mais informações sobre ligações de Ponto a Site, consulte [Point-to-Site FAQ (FAQ sobre Ponto a Site)](#faq) no final deste artigo.


### <a name="deployment-models-and-methods-for-p2s-connections"></a>Modelos e métodos de implementação para ligações P2S
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

A tabela seguinte mostra os dois modelos de implementação e métodos de implementação disponíveis para configurações P2S. Quando estiver disponível um artigo com passos de configuração, criamos uma ligação direta para o mesmo nesta tabela.

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="basic-workflow"></a>Fluxo de trabalho básico
![Diagrama Ponto a Site](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/point-to-site-connection-diagram.png)

As secções seguintes orientam-no ao longo dos passos para criar uma ligação Ponto a Site segura a uma rede virtual.

1. Criar uma rede virtual e um gateway de VPN
2. Gerar certificados
3. Carregar o ficheiro .cer
4. Gerar o pacote de configuração do cliente VPN
5. Configurar o computador cliente
6. Ligar ao Azure

### <a name="example-settings"></a>Definições de exemplo
Pode utilizar as definições de exemplo seguintes:

* **Nome: VNet1**
* **Espaço de endereços: 192.168.0.0/16**<br>Para este exemplo, utilizamos apenas um espaço de endereços. Pode ter mais do que um espaço de endereços para a sua VNet.
* **Nome da sub-rede: FrontEnd**
* **Intervalo de endereços da sub-rede: 192.168.1.0/24**
* **Subscrição:** se tiver várias subscrições, verifique se tem a correta.
* **Grupo de Recursos: TestRG**
* **Localização: E.U.A. Leste**
* **Tipo de ligação: Ponto a site**
* **Espaço de Endereços do Cliente: 172.16.201.0/24**. Os clientes VPN que se ligam à VNet através desta ligação Ponto a Site recebem um endereço IP a partir do conjunto especificado.
* **GatewaySubnet: 192.168.200.0/24**. A sub-rede do Gateway tem de utilizar o nome "GatewaySubnet".
* **Tamanho:** selecione o SKU do gateway que pretende utilizar.
* **Tipo de Encaminhamento: Dinâmico**



## <a name="vnetvpn"></a>Secção 1 - Criar uma rede virtual e um gateway de VPN

Antes de começar, verifique se tem uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, pode ativar os [Benefícios de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) ou inscrever-se numa [conta gratuita](https://azure.microsoft.com/pricing/free-trial).
### <a name="createvnet"></a>Parte 1: Criar uma rede virtual
Se ainda não tiver uma rede virtual, crie uma. As capturas de ecrã são disponibilizadas como exemplos. Não se esqueça de substituir os valores pelos seus. Para criar uma VNet com o portal do Azure, siga os passos abaixo:

1. Num browser, navegue para o [Portal do Azure](http://portal.azure.com) e, se necessário, inicie sessão com a sua conta do Azure.
2. Clique em **Novo**. No campo **Procurar no Marketplace**, escreva "Rede Virtual". Localize **Rede Virtual** na lista devolvida e clique para abrir o painel **Rede Virtual**.

    ![Painel Procurar rede virtual](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/newvnetportal700.png)
3. Perto da parte inferior do painel Rede Virtual, na lista **Selecionar um modelo de implementação**, selecione **Clássica** e clique em **Criar**.

    ![Selecionar modelo de implementação](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/selectmodel.png)
4. No painel **Criar rede virtual**, configure as definições da VNet. Neste painel, deverá adicionar o seu primeiro espaço de endereços e um único intervalo de endereços de sub-rede. Uma vez concluída a criação da VNet, pode voltar atrás e adicionar sub-redes e espaços de endereços adicionais.

    ![Painel Criar rede virtual](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/vnet125.png)
5. Verifique se a **Subscrição** está correta. Pode utilizar o menu pendente para mudar de subscrição.
6. Clique em **Grupo de recursos** e selecione um grupo de recursos existente ou crie um novo escrevendo um nome para o novo grupo de recursos. Se estiver a criar um novo grupo, dê-lhe um nome de acordo com os valores de configuração planeados. Para obter mais informações sobre os grupos de recursos, veja [Descrição Geral do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md#resource-groups).
7. Em seguida, selecione as definições **Localização** para a sua VNet. A localização irá determinar onde irão residir os recursos que são implementados nesta VNet.
8. Selecione **Afixar ao dashboard** se pretender conseguir localizar mais facilmente as suas VNet no dashboard e clique em **Criar**.

    ![Afixar ao dashboard](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/pintodashboard150.png)
9. Depois de clicar em Criar, verá um mosaico no dashboard que irá refletir o progresso da VNet. O mosaico muda enquanto a VNet está a ser criada.

    ![Mosaico Criar rede virtual](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deploying150.png)
10. Depois de criar a rede virtual, pode adicionar o endereço IP de um servidor DNS, de modo a lidar com a resolução de nomes. Abra as definições da rede virtual, clique em Servidores DNS e adicione o endereço IP do servidor DNS que quer utilizar. Esta definição não cria um servidor DNS novo. Certifique-se de que adiciona um servidor DNS com o qual os seus recursos possam comunicar.

Assim que criar a rede virtual, verá **Criado** listado no **Estado** na página de redes do Portal Clássico do Azure.

### <a name="gateway"></a>Parte 2: Criar a sub-rede de gateway e um gateway de encaminhamento dinâmico
Neste passo, vai criar uma sub-rede de gateway e um gateway de encaminhamento dinâmico. No portal do Azure para o modelo de implementação clássica, pode utilizar os mesmos painéis de configuração para criar a sub-rede de gateway e o gateway.

1. No portal, navegue para a rede virtual para a qual pretende criar um gateway.
2. No painel da rede virtual, no painel **Descrição Geral**, na secção de ligações VPN, clique em **Gateway**.

    ![Clique para criar um gateway](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/beforegw125.png)
3. No painel **Nova ligação de VPN**, selecione **Ponto a Site**.

    ![Tipo de ligação Ponto a Site](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/newvpnconnect.png)
4. Em **Espaço de Endereços do Cliente**, adicione o intervalo de endereços IP. Este é o intervalo a partir do qual os clientes VPN receberão um endereço IP quando ligam. Elimine o intervalo preenchido automaticamente e adicione o seu.

    ![Espaço de endereços do cliente](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientaddress.png)
5. Selecione a caixa de verificação **Criar gateway de imediato**.

    ![Criar gateway de imediato](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/creategwimm.png)
6. Clique em **Configuração do gateway opcional** para abrir o painel **Configuração do gateway**.

    ![Clique em configuração do gateway opcional](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/optsubnet125.png)
7. Clique em **Definições necessárias da configuração da sub-rede** para adicionar a **sub-rede de gateway**. Embora seja possível criar uma sub-rede de gateway tão pequena como /29, recomendamos que crie uma sub-rede maior que inclui mais endereços selecionando pelo menos /28 ou /27. Desta forma, se quiser ter mais configurações no futuro, haverá endereços suficientes para as acomodar.

   > [!IMPORTANT]
   > Ao trabalhar com sub-redes de gateway, evite associar um grupo de segurança de rede (NSG) às mesmas. A associação de um grupo de segurança de rede a esta sub-rede poderá fazer com que o gateway de VPN deixe de funcionar conforme esperado. Para obter mais informações sobre os grupos de segurança de rede, veja [O que é um grupo de segurança de rede (NSG)?](../virtual-network/virtual-networks-nsg.md)
   >
   >

    ![Adicionar GatewaySubnet](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsubnet125.png)
8. Selecione o **Tamanho** do gateway. Este é o SKU de gateway que vai utilizar para criar o gateway de rede virtual. No portal, o SKU Predefinido é **Básico**. Para obter mais informações sobre os SKUs de gateway, veja [About VPN Gateway Settings (Acerca das Definições do Gateway de VPN)](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

    ![tamanho do gateway](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsize125.png)
9. Selecione o **Tipo de Encaminhamento** do gateway. As configurações P2S requerem o tipo de encaminhamento **Dinâmico**. Clique em **OK** depois de concluir a configuração deste painel.

    ![Configurar o tipo de encaminhamento](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/routingtype125.png)
10. No painel **Nova Ligação de VPN**, clique em **OK**, na parte inferior, para começar a criar o gateway de rede virtual. Esta ação pode demorar até 45 minutos a concluir.

## <a name="generatecerts"></a>Secção 2 - Criar certificados
O Azure utiliza os certificados para autenticar os clientes VPN em VPNs Ponto a Site. Depois de criar o certificado de raiz, pode exportar os dados do certificado público (não a chave privada) como ficheiro .cer X.509 com codificação Base-64. Em seguida, carregue os dados do certificado público do certificado de raiz para o Azure.

Cada computador cliente que se ligue uma VNet por Ponto a Site tem de ter um certificado de cliente instalado. O certificado de cliente é gerado a partir do certificado de raiz e instalado em cada computador cliente. Se não estiver instalado um certificado de cliente válido e o cliente tentar estabelecer ligação com a VNet, a autenticação falhará.

### <a name="cer"></a>Parte 1: Obter a chave pública (.cer) para o certificado de raiz

####<a name="enterprise-certificate"></a>Certificado da empresa
 
Se estiver a utilizar uma solução empresarial, pode servir-se da sua cadeia de certificados existente. Obtenha o ficheiro .cer para o certificado de raiz que pretende utilizar.

####<a name="self-signed-root-certificate"></a>Certificado de raiz autoassinado

Se não estiver a utilizar uma solução de certificados de empresa, tem de criar um certificado de raiz autoassinado. Para criar um certificado autoassinado que contenha os campos necessários para autenticação P2S, pode utilizar o PowerShell. [Criar um certificado autoassinado para ligações Ponto a Site com o PowerShell](vpn-gateway-certificates-point-to-site.md) descreve os passos para criar um certificado de raiz autoassinado.

> [!NOTE]
> Anteriormente, makecert era o método recomendado para criar certificados de raiz autoassinados e gerar certificados de cliente para ligações Ponto a Site. Pode agora utilizar o PowerShell para criar esses certificados. Uma vantagem de utilizar o PowerShell é a capacidade de criar certificados SHA-2. Veja [Criar um certificado autoassinado para ligações Ponto a Site com o PowerShell](vpn-gateway-certificates-point-to-site.md) para os valores necessários.
>
>

#### <a name="to-export-the-public-key-for-a-self-signed-root-certificate"></a>Para exportar a chave pública para um certificado de raiz autoassinado

As ligações Ponto a Site requerem o carregamento da chave pública (.cer) para o Azure. Os passos seguintes ajudam-no a exportar o ficheiro .cer para o certificado de raiz autoassinado.

1. Para obter um ficheiro .cer a partir do certificado, abra **certmgr.msc**. Localize o certificado de raiz autoassinado, normalmente em ''Certificates - Current User\Personal\Certificates'', e clique com o botão direito do rato. Clique em **Todas as Tarefas** e, em seguida, clique em **Exportar**. Esta ação abre o **Assistente para Exportar Certificados**.
2. No Assistente, clique em **Seguinte**. Selecione **Não, não exportar a chave privada** e, em seguida, clique em **Seguinte**.
3. Na página **Formato do Ficheiro de Exportação**, selecione **X.509 codificado com Base-64 (.CER).** e, em seguida, clique em **Seguinte**. 
4. Em **Ficheiro a Exportar**, **Navegue** até à localização para a qual pretende exportar o certificado. Em **Nome do ficheiro**, atribua um nome ao ficheiro de certificado. Clique depois em **Seguinte**.
5. Clique em **Concluir** para exportar o certificado. Verá **A exportação foi concluída com êxito**. Clique em **OK** para fechar o assistente.

### <a name="genclientcert"></a>Parte 2: Gerar um certificado de cliente

Pode optar por gerar um certificado exclusivo para cada cliente que irá ligar ou pode utilizar o mesmo certificado em vários clientes. A vantagem de gerar certificados de cliente únicos é a capacidade de revogar um certificado único se for necessário. Caso contrário, se toda a gente estiver a utilizar o mesmo certificado de cliente e precisar de revogar o certificado para um cliente, terá de gerar e instalar novos certificados para todos os clientes que utilizam o certificado para autenticação.

####<a name="enterprise-certificate"></a>Certificado da empresa
- Se estiver a utilizar uma solução de certificado da empresa, gere um certificado de cliente com o formato do valor de nome comum “name@yourdomain.com”, em vez do formato “nome de domínio/nome de utilizador”.
- Certifique-se de que o certificado de cliente que emitiu é baseado no modelo de certificado "Utilizador" que tem a "Autenticação de Cliente" como o primeiro item na lista de utilização, em vez do Início de Sessão de Smart Card, etc. Pode verificar o certificado ao clicar duas vezes no certificado de cliente e visualizar **Detalhes > Utilização Avançada da Chaves**.

####<a name="self-signed-root-certificate"></a>Certificado de raiz autoassinado 
Se estiver a utilizar um certificado de raiz autoassinado, veja [Gerar um certificado de cliente com o PowerShell](vpn-gateway-certificates-point-to-site.md#clientcert) para obter os passos para gerar um certificado de cliente compatível com Ligações Ponto a Site.

### <a name="exportclientcert"></a>Parte 3: Exportar o certificado de cliente
Se gerar um certificado de cliente a partir de um certificado de raiz autoassinado com as instruções do [PowerShell](vpn-gateway-certificates-point-to-site.md#clientcert), este é instalado automaticamente no computador que utilizou para gerá-lo. Se quiser instalar um certificado de cliente noutro computador cliente, tem de exportá-lo.

1. Para exportar um certificado de cliente, abra **certmgr.msc**. Clique com o botão direito do rato no certificado de cliente que pretende exportar, clique em **todas as tarefas** e, em seguida, em **exportar**. Esta ação abre o **Assistente para Exportar Certificados**.
2. No Assistente, clique em **Seguinte**, selecione **Sim, exportar a chave privada** e, em seguida, clique em **Seguinte**.
3. Na página **Exportar Formato de Ficheiro**, pode deixar as predefinições selecionadas. Clique depois em **Seguinte**. 
4. Na página **Segurança**, tem de proteger a chave privada. Se optar por utilizar uma palavra-passe, certifique-se de que regista ou memoriza a palavra-passe que define para este certificado. Clique depois em **Seguinte**.
5. Em **Ficheiro a Exportar**, **Navegue** até à localização para a qual pretende exportar o certificado. Em **Nome do ficheiro**, atribua um nome ao ficheiro de certificado. Clique depois em **Seguinte**.
6. Clique em **Concluir** para exportar o certificado.

## <a name="upload"></a>Secção 3 – Carregar o ficheiro .cer do certificado de raiz
Depois de o gateway ter sido criado, pode carregar o ficheiro .cer de um certificado de raiz fidedigno para o Azure. Pode carregar ficheiros de um máximo de 20 certificados de raiz. Não carregue a chave privada do certificado de raiz para o Azure. Após o carregamento do ficheiro .cer, o Azure utiliza-lo para autenticar os clientes que se ligam à rede virtual.

1. Na secção **Ligações VPN** do painel da VNet, clique na imagem **clientes** para abrir o painel **Ligação VPN Ponto a Site**.

    ![Clientes](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png)
2. No painel **Ligação Ponto a Site**, clique em **Gerir certificados** para abrir o painel **Certificados**.<br>

    ![Painel Certificados](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png)<br><br>
3. No painel **Certificados**, clique em **Carregar** para abrir o painel **Carregar certificado**.<br>

    ![Painel Carregar certificados](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/uploadcerts.png)<br>
4. Clique na imagem de pasta para procurar o ficheiro .cer. Selecione o ficheiro e clique em **OK**. Atualize a página para ver o certificado carregado no painel **Certificados**.

    ![Carregar certificado](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/upload.png)<br>

## <a name="vpnclientconfig"></a>Secção 4 - Gerar o pacote de configuração do cliente VPN
Para ligar à rede virtual, também tem de configurar um cliente VPN. O computador cliente precisa de um certificado de cliente e do pacote de configuração adequado do cliente VPN para se ligar.

O pacote de clientes VPN contém informações de configuração para configurar o software de cliente VPN incorporado no Windows. O pacote não instala software adicional. As definições são específicas para a rede virtual à qual se quer ligar. Para consultar a lista de sistemas operativos cliente que são suportados, consulte [Point-to-Site connections FAQ (FAQ das ligações de Ponto a Site)](#faq) no final deste artigo.

### <a name="to-generate-the-vpn-client-configuration-package"></a>Para gerar o pacote de configuração do cliente VPN
1. No portal do Azure, no painel **Descrição Geral** da VNet, em **Ligações VPN**, clique na imagem de cliente para abrir o painel **Ligação VPN Ponto a Site**.
2. Na parte superior do painel **Ligação VPN Ponto a Site**, clique no pacote de transferência que corresponde ao sistema operativo cliente no qual será instalado:

   * Para clientes de 64 bits, selecione **Cliente VPN (64 bit)s**.
   * Para clientes de 32 bits, selecione **Cliente VPN (32 bit)s**.

     ![Transferir o pacote de configuração do cliente VPN](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/dlclient.png)<br>
3. Verá uma mensagem a dizer que o Azure está a gerar o pacote de configuração do cliente VPN da rede virtual. Ao fim de alguns minutos, o pacote é gerado e verá uma mensagem no computador local a dizer que o pacote foi transferido. Guarde o ficheiro do pacote de configuração. Vai instalá-lo em cada computador cliente que se vai ligar à rede virtual através de P2S.

## <a name="clientconfiguration"></a>Secção 5 - Configurar o computador cliente
### <a name="part-1-install-an-exported-client-certificate"></a>Parte 1: Instalar um certificado de cliente exportado

Se quiser criar uma ligação P2S a partir de um computador cliente sem ser o utilizado para gerar os certificados de cliente, tem de instalar um certificado de cliente. Ao instalar um certificado de cliente, vai precisar da palavra-passe que foi criada quando o certificado de cliente foi exportado.

1. Localize e copie o ficheiro *.pfx* para o computador cliente. No computador cliente, faça duplo clique no ficheiro *.pfx* para o instalar. Deixe **Localização do Arquivo** como **Utilizador Atual** e, em seguida, clique em **Seguinte**.
2. Na página **Ficheiro a importar**, não efetue nenhuma alteração. Clique em **Seguinte**.
3. Na página **Proteção por chave privada**, introduza a palavra-passe para o certificado, se utilizou um, ou verifique se o principal de segurança que está a instalar o certificado está correto e, em seguida, clique em **Seguinte**.
4. Na página **Arquivo de Certificados**, mantenha a localização predefinida e, em seguida, clique em **Seguinte**.
5. Clique em **Concluir**. Em **Aviso de Segurança** para a instalação do certificado, clique em **Sim**. Pode sentir-se à vontade para clicar em ''Sim'', uma vez que gerou o certificado. O certificado foi agora importado com êxito.

### <a name="part-2-install-the-vpn-client-configuration-package"></a>Parte 2: Instalar o pacote de configuração do cliente VPN
Pode utilizar o mesmo pacote de configuração do cliente VPN em cada computador cliente, desde que a versão corresponda à arquitetura do cliente.

1. Copie o ficheiro de configuração localmente para o computador que pretende ligar à rede virtual. 
2. Faça duplo clique no ficheiro .exe para instalar o pacote no computador cliente. O pacote de configuração não está assinado porque foi criado por si. Isto significa que poderá ver um aviso. Se obtiver um pop-up SmartScreen do Windows, clique em **Mais informações** (no lado esquerdo) e, em seguida, em **Executar mesmo assim** para instalar o pacote.
3. No computador cliente, navegue para **Definições de Rede** e clique em **VPN**. Verá a ligação listada. Mostra o nome da rede virtual à qual se vai ligar e terá um aspeto semelhante a:

    ![cliente de VPN](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/vpn.png)

## <a name="connect"></a>Secção 6 – Ligar ao Azure
### <a name="connect-to-your-vnet"></a>Ligar à VNet
1. Para se ligar à sua VNet, no computador cliente, navegue até às ligações VPN e localize a ligação VPN que criou. Tem o mesmo nome da sua rede virtual. Clique em **Ligar**. Poderá aparecer uma mensagem pop-up que se refere à utilização do certificado. Se isto ocorrer, clique em **Continuar** para utilizar privilégios elevados.
2. Na página de estado da **Ligação**, clique em **Ligar** para iniciar a ligação. Se vir um ecrã **Selecionar Certificado**, verifique se o certificado de cliente apresentado é aquele que pretende utilizar para se ligar. Se não for, utilize a seta para baixo para selecionar o certificado correto e clique em **OK**.

    ![Ligação de cliente VPN](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientconnect.png)
3. A ligação deve ser agora estabelecida.

    ![Ligação estabelecida](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/connected.png)

> [!NOTE]
> Se estiver a utilizar um certificado que foi emitido através de uma solução de AC Empresarial e se deparou com problemas de autenticação, verifique a ordem de autenticação do certificado de cliente. Pode verificar a ordem da lista de autenticação ao clicar duas vezes no certificado de cliente e aceder a **Detalhes > Utilização Avançada de Chaves**. Certifique-se de que a lista apresenta "Autenticação de Cliente" como o primeiro item. Caso contrário, terá de emitir um certificado de cliente com base no modelo de Utilizador que tenha a Autenticação de Cliente como o primeiro item na lista. 
>
>

### <a name="verify-the-vpn-connection"></a>Verificar a ligação VPN
1. Para verificar se a ligação VPN está ativa, abra uma linha de comandos elevada e execute *ipconfig/all*.
2. Veja os resultados. Repare que o endereço IP que recebeu é um dos endereços dentro do intervalo de endereços de conetividade Ponto a Site que especificou quando criou a VNet. Os resultados deverão ser algo semelhante a isto:

Exemplo:

    PPP adapter VNet1:
        Connection-specific DNS Suffix .:
        Description.....................: VNet1
        Physical Address................:
        DHCP Enabled....................: No
        Autoconfiguration Enabled.......: Yes
        IPv4 Address....................: 192.168.130.2(Preferred)
        Subnet Mask.....................: 255.255.255.255
        Default Gateway.................:
        NetBIOS over Tcpip..............: Enabled

## <a name="add"></a>Adicionar ou remover certificados de raiz fidedigna

Pode adicionar e remover certificados de raiz fidedigna do Azure. Quando remove um certificado fidedigno, os certificados de cliente que foram gerados a partir do certificado de raiz já não poderão ser ligados ao Azure via Ponto a Site. Se pretender que os clientes estabeleçam ligação, terá de instalar um novo certificado de cliente que seja gerado a partir de um certificado considerado fidedigno no Azure.

### <a name="to-add-a-trusted-root-certificate"></a>Para adicionar um certificado de raiz fidedigna

Pode adicionar até 20 ficheiros .cer de certificado de raiz fidedigna ao Azure. Para obter instruções, veja a [Secção 3 - carregar o ficheiro .cer do certificado de raiz](#upload).

### <a name="to-remove-a-trusted-root-certificate"></a>Para remover um certificado de raiz fidedigna


1. Na secção **Ligações VPN** do painel da VNet, clique na imagem **clientes** para abrir o painel **Ligação VPN Ponto a Site**.

    ![Clientes](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png)
2. No painel **Ligação Ponto a Site**, clique em **Gerir certificados** para abrir o painel **Certificados**.<br>

    ![Painel Certificados](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png)<br><br>
3. No painel **Certificados**, clique nas reticências junto ao certificado que pretende remover e, em seguida, clique em **Eliminar**.

     ![Eliminar certificado de raiz](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deleteroot.png)<br>


## <a name="revokeclient"></a>Revogar um certificado de cliente
Pode revogar certificados de cliente. A lista de revogação de certificado permite-lhe negar seletivamente a conectividade de Ponto a Site, com base em certificados de cliente individuais. Isto é diferente da remoção de um certificado de raiz fidedigna. Se remover um certificado de raiz .cer fidedigno do Azure, revoga o acesso a todos os certificados de cliente gerados/assinados pelo certificado de raiz revogado. Ao revogar um certificado de cliente, em vez do certificado de raiz, permite que os outros certificados que foram gerados a partir do certificado de raiz continuem a ser utilizados para autenticação da ligação Ponto a Site.

A prática comum é utilizar o certificado de raiz para gerir o acesso nos níveis de equipa ou organização e utilizar certificados de cliente revogados para controlo de acesso detalhado dos utilizadores individuais.

### <a name="to-revoke-a-client-certificate"></a>Para revogar um certificado de cliente

Pode revogar um certificado de cliente, ao adicionar o thumbprint à lista de revogação.

1. Obtenha o thumbprint do certificado de cliente. Para obter mais informações, veja [Como: Obter o Thumbprint de um Certificado](https://msdn.microsoft.com/library/ms734695.aspx).
2. Copie as informações para um editor de texto e remova todos os espaços, para que seja uma cadeia contínua.
3. Navegue para o painel **“nome de rede virtual clássica” > ligação VPN Ponto a Site > Certificados** e clique em **Lista de revogação** para abrir o painel Lista de revogação. 
4. No painel **Lista de revogação**, clique em **+ Adicionar certificado** para abrir o painel **Adicionar certificado à lista de revogação**.
5. No painel **Adicionar certificado à lista de revogação**, cole o thumbprint do certificado como uma linha contínua de texto, sem espaços. Clique em **OK** na parte inferior do painel.
6. Depois de concluída a atualização, o certificado já não pode ser utilizado para ligar. Os clientes que tentarem ligar com este certificado irão receber uma mensagem a indicar que o certificado já não é válido.


## <a name="faq"></a>FAQ Ponto a Site

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-point-to-site-faq-include.md)]

## <a name="next-steps"></a>Passos seguintes
Assim que a ligação estiver concluída, pode adicionar máquinas virtuais às redes virtuais. Para obter mais informações, veja [Máquinas Virtuais](https://docs.microsoft.com/azure/#pivot=services&panel=Compute). Para compreender melhor o funcionamento em rede e as máquinas virtuais, veja [Descrição geral da rede VM do Azure e Linux](../virtual-machines/virtual-machines-linux-azure-vm-network-overview.md).

