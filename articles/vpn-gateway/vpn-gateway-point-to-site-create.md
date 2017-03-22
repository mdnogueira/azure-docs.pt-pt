---
title: "Ligar um computador a uma rede virtual do Azure através de Ponto a Site: portal clássico | Microsoft Docs"
description: "Crie uma ligação de gateway de VPN Ponto a Site com o portal do Azure para se ligar à sua Rede Virtual do Azure clássica em segurança com o portal clássico."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 4f5668a5-9b3d-4d60-88bb-5d16524068e0
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/08/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: ffd5690853a7344c7122940ad9676d1903ba2d4a
ms.lasthandoff: 03/09/2017


---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-the-classic-portal-classic"></a>Configurar uma ligação Ponto a Site a uma VNet com o portal clássico (clássico)
> [!div class="op_single_selector"]
> * [Resource Manager - Portal do Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [Resource Manager – PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Clássica – Portal do Azure](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
> * [Clássica – Portal Clássico](vpn-gateway-point-to-site-create.md)
> 
> 

Uma configuração Ponto a Site (P2S) permite-lhe criar uma ligação segura a partir de um computador cliente individual para a sua rede virtual. Uma P2S é uma ligação VPN através de SSTP (Secure Socket Tunneling Protocol). As ligações Ponto a Site são úteis quando quer ligar a VNet a partir de uma localização remota, por exemplo, quando está em casa ou numa conferência ou quando tem apenas alguns clientes que precisam de se ligar a uma rede virtual. As ligações P2S não precisam de nenhum dispositivo VPN ou endereço IP destinado ao público. O utilizador estabelece a ligação VPN a partir do computador cliente.

Este artigo explica-lhe como criar uma VNet com uma ligação Ponto a Site no modelo de implementação clássica através do portal clássico. Para obter mais informações sobre ligações de Ponto a Site, consulte [Point-to-Site FAQ (FAQ sobre Ponto a Site)](#faq) no final deste artigo.



### <a name="deployment-models-and-methods-for-p2s-connections"></a>Modelos e métodos de implementação para ligações P2S
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

A tabela seguinte mostra os dois modelos de implementação e métodos de implementação disponíveis para configurações P2S. Quando estiver disponível um artigo com passos de configuração, criamos uma ligação direta para o mesmo nesta tabela.

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="basic-workflow"></a>Fluxo de trabalho básico
![Diagrama Ponto a Site](./media/vpn-gateway-point-to-site-create/p2sclassic.png "ponto a site")

Os seguintes passos explicam como criar uma ligação Ponto a Site segura para uma rede virtual. A configuração de uma ligação Ponto a Site é apresentada em quatro secções. A ordem na qual configurou cada uma das seguintes secções é importante. Não ignore os passos ou passe à frente.

* **Secção 1** Criar uma rede virtual e gateway de VPN.
* **Secção 2** Criar os certificados utilizados para a autenticação e a carregá-los.
* **Secção 3** Exportar e instalar os certificados de cliente.
* **Secção 4** Configurar o cliente VPN.



## <a name="vnetvpn"></a>Secção 1 - Criar uma rede virtual e um gateway de VPN

Antes de começar, verifique se tem uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, pode ativar os [Benefícios de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) ou inscrever-se numa [conta gratuita](https://azure.microsoft.com/pricing/free-trial).

### <a name="part-1-create-a-virtual-network"></a>Parte 1: criar uma rede virtual
1. Inicie sessão no [Portal Clássico do Azure](https://manage.windowsazure.com). Estes passos utilizam o portal clássico e não o portal do Azure. Atualmente, não é possível criar uma ligação P2S através do portal do Azure.
2. No canto inferior esquerdo do ecrã, clique em **Novo**. No painel de navegação, clique em **Serviços de Rede** e em **Rede Virtual**. Clique em **Criação Personalizada** para iniciar o assistente de configuração.
3. Na página **Detalhes da Rede Virtual**, introduza as seguintes informações e clique na seta seguinte na parte inferior direita.
   
   * **Nome**: atribua um nome à rede virtual. Por exemplo, “VNet1”. Este é o nome que deverá mencionar quando implementar VMs nesta VNet.
   * **Localização**: a localização está diretamente relacionada com a localização física (região) onde pretende que os recursos (VMs) residam. Por exemplo, se pretender que as VMs que implementa nesta rede virtual estejam fisicamente localizadas nos EUA Leste, selecione essa localização. Não é possível alterar a região associada à rede virtual depois de a criar.
4. Na página **Servidores DNS e Conetividade VPN**, introduza as seguintes informações e clique na seta seguinte na parte inferior direita.
   
   * **Servidores DNS**: introduza o endereço IP e o nome do servidor DNS ou selecione um servidor DNS anteriormente registado no menu de atalho. Esta definição não cria um servidor DNS. Permite-lhe especificar os servidores DNS que pretende utilizar para a resolução de nomes desta rede virtual. Se pretender utilizar o serviço de resolução de nomes predefinido do Azure, deixe esta secção em branco.
   * **Configurar VPN Ponto a Site**: selecione a caixa de verificação.
5. Na página **Conetividade Ponto a Site**, especifique o intervalo de endereços IP a partir do qual os clientes VPN receberão um endereço IP quando estiverem ligados. Existem algumas regras relativamente aos intervalos de endereços que pode especificar. É importante verificar se o intervalo que especificou não se sobrepõe a qualquer um dos intervalos localizados na sua rede no local.
6. Introduza as seguintes informações e clique na seta seguinte.
   
   * **Espaço de Endereços**: inclua o IP Inicial e o CIDR (Contagem de Endereços).
   * **Adicionar espaço de endereços**: adicione o espaço de endereços apenas se for preciso para o design de redes.
7. Na página **Espaços de Endereços da Rede Virtual**, especifique o intervalo de endereços que pretende utilizar para a rede virtual. Estes são os endereços IP dinâmicos (DIPS) que serão atribuídos às VMs e a outras instâncias de função que implementar nesta rede virtual.<br><br>É especialmente importante selecionar um intervalo que não se sobreponha a nenhum intervalo utilizado para a rede no local. Terá de trabalhar em coordenação com o administrador de rede, que poderá ter de extrair um intervalo de endereços IP do seu espaço de endereços da rede no local para a utilização na sua rede virtual.
8. Introduza as seguintes informações e clique na marca de verificação para começar a criar a rede virtual.
   
   * **Espaço de Endereços**: adicione o intervalo de endereços IP interno que pretende utilizar para esta rede virtual, incluindo o IP Inicial e a Contagem. É importante selecionar um intervalo que não se sobreponha a nenhum intervalo que seja utilizado para a rede no local. 
   * **Adicionar sub-rede**: não são necessárias sub-redes adicionais, mas pode querer criar uma sub-rede separada para as VMs com DIPS estáticos. Em alternativa, pode ter as VMs numa sub-rede separada das outras instâncias de função.
   * **Adicionar sub-rede do gateway**: a sub-rede do gateway é necessária para uma VPN Ponto a Site. Clique para adicionar a sub-rede do gateway. A sub-rede do gateway é utilizada apenas para o gateway de rede virtual.
9. Assim que criar a rede virtual, verá **Criado** listado no **Estado** na página de redes do Portal Clássico do Azure. Após criar a rede virtual, pode criar o gateway de encaminhamento dinâmico.

### <a name="part-2-create-a-dynamic-routing-gateway"></a>Parte 2: criar um gateway de encaminhamento dinâmico
O tipo de gateway tem de ser configurado como dinâmico. Os gateways de encaminhamento estático não funcionam com esta funcionalidade.

1. No Portal Clássico do Azure, na página **Redes**, clique na rede virtual que criou e navegue até à página **Dashboard**.
2. Na parte inferior da página **Dashboard**, clique em **Criar Gateway**. É apresentada uma mensagem a perguntar **Pretende criar um gateway para a rede virtual “VNet1”**. Clique em **Sim** para começar a criar o gateway. A criação do gateway pode demorar até 45 minutos.

## <a name="generate"></a>Secção 2 - Gerar e carregar certificados
O Azure utiliza os certificados para autenticar os clientes VPN em VPNs Ponto a Site. Depois de criar o certificado de raiz, pode exportar os dados do certificado público (não a chave privada) como ficheiro .cer X.509 com codificação Base-64. Em seguida, carregue os dados do certificado público do certificado de raiz para o Azure.

Cada computador cliente que se ligue uma VNet por Ponto a Site tem de ter um certificado de cliente instalado. O certificado de cliente é gerado a partir do certificado de raiz e instalado em cada computador cliente. Se não estiver instalado um certificado de cliente válido e o cliente tentar estabelecer ligação com a VNet, a autenticação falhará.

Nesta secção irá efetuar o seguinte:

* Obtenha o ficheiro .cer para um certificado de raiz. Pode ser um certificado de raiz autoassinado ou pode utilizar o seu sistema de certificado de empresa.
* Carregue o ficheiro .cer para o Azure.
* Gere certificados de cliente.

### <a name="root"></a>Parte 1: Obter o ficheiro .cer para o certificado de raiz


Se estiver a utilizar uma solução empresarial, pode servir-se da sua cadeia de certificados existente. Obtenha o ficheiro .cer para o certificado de raiz que pretende utilizar.


Se não estiver a utilizar uma solução de certificados de empresa, tem de criar um certificado de raiz autoassinado. Para criar um certificado de raiz autoassinado que contenha os campos necessários para autenticação P2S, pode utilizar o PowerShell. [Criar um certificado de raiz autoassinado para ligações Ponto a Site com o PowerShell](vpn-gateway-certificates-point-to-site.md) descreve os passos para criar um certificado de raiz autoassinado.

> [!NOTE]
> Anteriormente, makecert era o método recomendado para criar certificados de raiz autoassinados e gerar certificados de cliente para ligações Ponto a Site. Pode agora utilizar o PowerShell para criar esses certificados. Uma vantagem de utilizar o PowerShell é a capacidade de criar certificados SHA-2. Veja [Criar um certificado de raiz autoassinado para ligações Ponto a Site com o PowerShell](vpn-gateway-certificates-point-to-site.md) para os valores necessários.
>
>


#### <a name="to-export-the-public-key-for-a-self-signed-root-certificate"></a>Para exportar a chave pública para um certificado de raiz autoassinado.

As ligações Ponto a Site requerem o carregamento da chave pública (.cer) para o Azure. Os passos seguintes ajudam-no a exportar o ficheiro .cer para o certificado de raiz autoassinado.

1. Para obter um ficheiro .cer a partir do certificado, abra **certmgr.msc**. Localize o certificado de raiz autoassinado, normalmente em ''Certificates - Current User\Personal\Certificates'', e clique com o botão direito do rato. Clique em **Todas as Tarefas** e, em seguida, clique em **Exportar**. Esta ação abre o **Assistente para Exportar Certificados**.
2. No Assistente, clique em **Seguinte**. Selecione **Não, não exportar a chave privada** e, em seguida, clique em **Seguinte**.
3. Na página **Formato do Ficheiro de Exportação**, selecione **X.509 codificado com Base-64 (.CER).** e, em seguida, clique em **Seguinte**. 
4. Em **Ficheiro a Exportar**, **Navegue** até à localização para a qual pretende exportar o certificado. Em **Nome do ficheiro**, atribua um nome ao ficheiro de certificado. Clique depois em **Seguinte**.
5. Clique em **Concluir** para exportar o certificado. Verá **A exportação foi concluída com êxito**. Clique em **OK** para fechar o assistente.

### <a name="upload"></a>Parte 2: Carregar o ficheiro .cer do certificado de raiz para o portal clássico do Azure

Adicionar um certificado fidedigno ao Azure. Quando adiciona um ficheiro com codificação x.509 Base64 (.cer) ao Azure, está a dizer ao Azure para confiar no certificado de raiz que o ficheiro representa. Pode carregar ficheiros de um máximo de 20 certificados de raiz. Não carregue a chave privada do certificado de raiz para o Azure. Após o carregamento do ficheiro .cer, o Azure utiliza-lo para autenticar os clientes que se ligam à rede virtual.

1. No Portal Clássico do Azure, na página **Certificados** da rede virtual, clique em **Carregar um certificado de raiz**.
2. Na página **Carregar Certificado**, procure o certificado de raiz do ficheiro .cer e clique na marca de verificação.

### <a name="createclientcert"></a>Parte 3: Gerar um certificado de cliente
Pode optar por gerar um certificado exclusivo para cada cliente que irá ligar ou pode utilizar o mesmo certificado em vários clientes. A vantagem de gerar certificados de cliente únicos é a capacidade de revogar um certificado único se for necessário. Caso contrário, se toda a gente estiver a utilizar o mesmo certificado de cliente e precisar de revogar o certificado para um cliente, terá de gerar e instalar novos certificados para todos os clientes que utilizam o certificado para autenticação.

####<a name="enterprise-certificate"></a>Certificado da empresa
- Se estiver a utilizar uma solução de certificado da empresa, gere um certificado de cliente com o formato do valor de nome comum “name@yourdomain.com”, em vez do formato “nome de domínio/nome de utilizador”.
- Certifique-se de que o certificado de cliente que emitiu é baseado no modelo de certificado "Utilizador" que tem a "Autenticação de Cliente" como o primeiro item na lista de utilização, em vez do Início de Sessão de Smart Card, etc. Pode verificar o certificado ao clicar duas vezes no certificado de cliente e visualizar **Detalhes > Utilização Avançada da Chaves**.

####<a name="self-signed-root-root-certificate"></a>Certificado de raiz autoassinado 
Se estiver a utilizar um certificado de raiz autoassinado, veja [Gerar um certificado de cliente com o PowerShell](vpn-gateway-certificates-point-to-site.md#clientcert) para obter os passos para gerar um certificado de cliente compatível com Ligações Ponto a Site.


## <a name="installclientcert"></a>Secção 3 - Exportar e instalar o certificado de cliente

Depois de gerar um certificado de cliente, tem de exportar o certificado como um ficheiro .pfx e, em seguida, instalá-lo no computador cliente. Cada computador cliente tem de ter um certificado de cliente para se autenticar. Pode automatizar a instalação do certificado de cliente ou pode instalá-lo manualmente. Os passos seguintes descrevem como exportar e instalar manualmente o certificado de cliente.

### <a name="export-the-client-certificate"></a>Exportar o certificado de cliente

1. Para exportar um certificado de cliente, abra **certmgr.msc**. Clique com o botão direito do rato no certificado de cliente que pretende exportar, clique em **todas as tarefas** e, em seguida, em **exportar**. Esta ação abre o **Assistente para Exportar Certificados**.
2. No Assistente, clique em **Seguinte**, selecione **Sim, exportar a chave privada** e, em seguida, clique em **Seguinte**.
3. Na página **Exportar Formato de Ficheiro**, pode deixar as predefinições selecionadas. Clique depois em **Seguinte**. 
4. Na página **Segurança**, tem de proteger a chave privada. Se optar por utilizar uma palavra-passe, certifique-se de que regista ou memoriza a palavra-passe que define para este certificado. Clique depois em **Seguinte**.
5. Em **Ficheiro a Exportar**, **Navegue** até à localização para a qual pretende exportar o certificado. Em **Nome do ficheiro**, atribua um nome ao ficheiro de certificado. Clique depois em **Seguinte**.
6. Clique em **Concluir** para exportar o certificado.

### <a name="install-the-client-certificate"></a>Instalar o certificado de cliente

Ao instalar o certificado de cliente, vai precisar da palavra-passe que foi criada quando o certificado de cliente foi exportado.

1. Localize e copie o ficheiro *.pfx* para o computador cliente. No computador cliente, faça duplo clique no ficheiro *.pfx* para o instalar. Deixe **Localização do Arquivo** como **Utilizador Atual** e, em seguida, clique em **Seguinte**.
2. Na página **Ficheiro a importar**, não efetue nenhuma alteração. Clique em **Seguinte**.
3. Na página **Proteção por chave privada**, introduza a palavra-passe para o certificado, se utilizou um, ou verifique se o principal de segurança que está a instalar o certificado está correto e, em seguida, clique em **Seguinte**.
4. Na página **Arquivo de Certificados**, mantenha a localização predefinida e, em seguida, clique em **Seguinte**.
5. Clique em **Concluir**. Em **Aviso de Segurança** para a instalação do certificado, clique em **Sim**. Pode sentir-se à vontade para clicar em ''Sim'', uma vez que gerou o certificado. O certificado foi agora importado com êxito.

## <a name="vpnclientconfig"></a>Secção 4 - Configurar o cliente VPN
Para ligar à rede virtual, também tem de configurar um cliente VPN. O cliente precisa de um certificado de cliente e do pacote de configuração adequado do cliente VPN para se ligar com êxito. Para configurar um cliente VPN, execute os passos seguintes, por ordem.

### <a name="part-1-create-the-vpn-client-configuration-package"></a>Parte 1: criar o pacote de configuração do cliente VPN
1. No portal clássico do Azure, na página do **Dashboard** da sua rede virtual, navegue para o menu de leitura rápida no canto direito. O pacote de clientes VPN contém informações de configuração para configurar o software de cliente VPN incorporado no Windows. O pacote não instala software adicional. As definições são específicas para a rede virtual à qual se quer ligar. Para consultar a lista de sistemas operativos cliente que são suportados, consulte [Point-to-Site connections FAQ (FAQ das ligações de Ponto a Site)](#faq) no final deste artigo.<br><br>Selecione o pacote de transferência que corresponde ao sistema operativo cliente no qual será instalado:
   
   * Para clientes de 32 bits, selecione **Transferir o Pacote de VPN Cliente de 32 bits**.
   * Para clientes de 64 bits, selecione **Transferir o Pacote de VPN Cliente de 64 bits**.
2. A criação do pacote de clientes demora alguns minutos. Quando o pacote estiver concluído, pode descarregar o ficheiro. O ficheiro *.exe* que transferir pode ser armazenado com segurança no computador local.
3. Depois de gerar e transferir o pacote de clientes VPN do Portal Clássico do Azure, pode instalá-lo no computador cliente a partir do qual pretende ligar-se à rede virtual. Se planear instalar o pacote de clientes VPN em vários computadores cliente, verifique se cada um também tem um certificado de cliente instalado.

### <a name="part-2-install-the-vpn-configuration-package-on-the-client"></a>Parte 2: instalar o pacote de configuração de VPN no cliente
1. Copie o ficheiro de configuração localmente para o computador que pretende ligar à rede virtual e faça duplo clique no ficheiro .exe. 
2. Depois de instalar o pacote, pode iniciar a ligação VPN. O pacote de configuração não está assinado pela Microsoft. Poderá pretender assinar o pacote utilizando o serviço de assinatura da sua organização ou o pacote pode ser assinado por si através da [SignTool](http://go.microsoft.com/fwlink/p/?LinkId=699327). Pode utilizar o pacote sem assinar. No entanto, se o pacote não for assinado, é apresentado um aviso quando instalar o pacote.
3. No computador cliente, navegue para **Definições de Rede** e clique em **VPN**. Verá a ligação listada. Mostra o nome da rede virtual à qual se vai ligar e terá um aspeto semelhante a: 
   
    ![cliente VPN](./media/vpn-gateway-point-to-site-create/vpn.png "cliente VPN")

### <a name="part-3-connect-to-azure"></a>Parte 3: ligar ao Azure
1. Para se ligar à sua VNet, no computador cliente, navegue até às ligações VPN e localize a ligação VPN que criou. Tem o mesmo nome da sua rede virtual. Clique em **Ligar**. Poderá aparecer uma mensagem pop-up que se refere à utilização do certificado. Se isto ocorrer, clique em **Continuar** para utilizar privilégios elevados. 
2. Na página de estado da **Ligação**, clique em **Ligar** para iniciar a ligação. Se vir um ecrã **Selecionar Certificado**, verifique se o certificado de cliente apresentado é aquele que pretende utilizar para se ligar. Se não for, utilize a seta para baixo para selecionar o certificado correto e clique em **OK**.
   
    ![cliente VPN 2](./media/vpn-gateway-point-to-site-create/clientconnect.png "ligação de cliente VPN")
3. A ligação deve ser agora estabelecida.
   
    ![cliente VPN 3](./media/vpn-gateway-point-to-site-create/connected.png "ligação de cliente VPN 2")

> [!NOTE]
> Se estiver a utilizar um certificado que foi emitido através de uma solução de AC Empresarial e se deparou com problemas de autenticação, verifique a ordem de autenticação do certificado de cliente. Pode verificar a ordem da lista de autenticação ao clicar duas vezes no certificado de cliente e aceder a **Detalhes > Utilização Avançada de Chaves**. Certifique-se de que a lista apresenta "Autenticação de Cliente" como o primeiro item. Caso contrário, terá de emitir um certificado de cliente com base no modelo de Utilizador que tenha a Autenticação de Cliente como o primeiro item na lista. 
>
>

### <a name="part-4-verify-the-vpn-connection"></a>Parte 4: verificar a ligação VPN
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

## <a name="faq"></a>FAQ Ponto a Site

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-point-to-site-faq-include.md)]

## <a name="next-steps"></a>Passos seguintes

Assim que a ligação estiver concluída, pode adicionar máquinas virtuais às redes virtuais. Para obter mais informações, veja [Máquinas Virtuais](https://docs.microsoft.com/azure/#pivot=services&panel=Compute). Para compreender melhor o funcionamento em rede e as máquinas virtuais, veja [Descrição geral da rede VM do Azure e Linux](../virtual-machines/virtual-machines-linux-azure-vm-network-overview.md).


