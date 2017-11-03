---
title: "Considerações sobre planeamento para a pilha do Azure integrado sistemas | Microsoft Docs"
description: "Saiba o que pode fazer agora a planear e preparar para a pilha do Azure de vários nós."
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: 90f8fa1a-cace-4bfa-852b-5abe2b307615
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ms.author: twooley
ms.openlocfilehash: 8484f7947f23a00c05b34babf13cd75f9d227740
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2017
---
# <a name="planning-considerations-for-azure-stack-integrated-systems"></a>Considerações sobre planeamento para a pilha do Azure integrado sistemas

*Aplica-se a: Azure pilha integrado sistemas*

Se estiver interessado num sistema pilha do Azure integrado, poderá ser útil compreender algumas das principais considerações sobre planeamento em torno da implementação e como o sistema se enquadra no seu centro de dados. Este tópico fornece uma descrição geral de alto nível destas considerações.

Se optar por adquirir um sistema integrado, o seu fornecedor de hardware de fabricante de equipamento original (OEM) ajuda a ajudá-lo através de grande parte do processo de planeamento em mais detalhe. Também irá efetuar a implementação real.

## <a name="management-considerations"></a>Considerações sobre gestão

Pilha do Azure é um sistema selado, em que a infraestrutura é bloqueada de um permissões e a perspetiva de rede. Listas de controlo de acesso (ACL) de rede são aplicadas para bloquear todo o tráfego de entrada não autorizado e todas as comunicações desnecessárias entre os componentes de infraestrutura. Isto torna difícil para os utilizadores não autorizados a aceder ao sistema.

Para operações e gestão diária, há sem acesso de administrador sem restrições para a infraestrutura. Os operadores do Azure da pilha tem de gerir o sistema através do portal de administrador ou através do Azure Resource Manager (através do PowerShell ou a API REST). Não há nenhum acesso ao sistema por outras ferramentas de gestão, tais como o Gestor de Hyper-V ou o Gestor de clusters de ativação pós-falha. Para ajudar a proteger o sistema, o software de terceiros (por exemplo, agentes) não pode ser instalado no interior de componentes da infraestrutura de pilha do Azure. Interoperabilidade com o software de gestão e segurança externas ocorre através do PowerShell ou a API REST.

Quando um nível mais elevado de acesso necessária para a resolução de problemas que não estão resolvidos passos mediation alerta, tem de trabalhar com o suporte. Através do suporte, há um método para fornecer acesso de administrador total temporário para o sistema para executar operações mais avançadas. 

## <a name="deploy-connected-or-disconnected"></a>Implementar ligado ou desligado
 
Pode optar por implementar pilha do Azure ligado à internet (e para o Azure) ou desligada. Para obter a vantagem da maioria da pilha do Azure, incluindo cenários híbridos entre pilha do Azure e o Azure, seria aconselhável implementar ligado ao Azure. A tabela seguinte ajuda resumem as principais diferenças entre os modos de implementação.

| Área | Modo ligado | Modo desligado |
| -------- | ------------- | ----------|
| Fornecedor de identidade | Azure Active Directory (Azure AD) ou serviços de Federação do Active Directory (AD FS) | Apenas AD FS. |
| Sindicação do Marketplace | Transferir itens diretamente a partir do Azure Marketplace para o mercado na pilha do Azure | Requer gestão manual do marketplace na pilha do Azure |
| Modelo de licenciamento | Pay-como-utiliza ou com base na capacidade | Com base na capacidade apenas|
| Patch e atualização  | Transferir pacotes de atualização diretamente a pilha do Azure | Necessita de suporte de dados amovível e um dispositivo ligado separado |
| | | |

Não é possível alterar o modo de implementação mais tarde sem reimplementação completa do sistema.

## <a name="identity-considerations"></a>Considerações de identidade

### <a name="choose-identity-provider"></a>Escolha o fornecedor de identidade

Terá de considerar o fornecedor de identidade que pretende utilizar para a implementação de pilha do Azure, Azure AD ou AD FS. Não é possível comutar a fornecedores de identidade após a implementação sem reimplementação completa do sistema.

A opção do fornecedor de identidade não tem efeito em máquinas virtuais de inquilino, o sistema de identidade e contas utilizarem, se possam juntar-se um domínio do Active Directory, etc. Este é separada.

**Razões para considerar utilizar o Azure AD**

- Já tiver investimentos existentes no Azure AD (como o Azure ou do Office 365).
- Pretende utilizar a mesma identidade em nuvens do Azure e pilha do Azure.
- Pretende suportar cenários de vários inquilinos, onde pode alojar organizações diferentes na mesma instância de pilha do Azure.
- Pretende utilizar a gestão de diretório baseado em REST através do Azure AD Graph aprovisionar utilizadores, grupos, etc. através de APIs.

> [!NOTE]
> Não é possível ligar uma implementação de pilha do Azure para uma instância do Azure AD e uma instância existente do AD FS ao mesmo tempo. Se implementar com o Azure AD e gostaria de utilizar uma instância existente do AD FS, podem federar no local instância do AD FS com o Azure AD.

**Razões para considerar a utilização do AD FS**

- Não existe nenhum ou apenas a conectividade à internet parcial.
- Existem requisitos de regulamentação/soberania dos.
- Pretende utilizar o seu próprio sistema de identidade (por exemplo, o Active Directory empresarial) para contas de utilizador e de operador. Para tal, podem federar com uma instância do AD FS existente (no Windows Server 2012, 2012 R2 ou 2016) que é copiado em segurança pelo Active Directory.
- Tiver não investimentos do Azure e não pretender utilizar o Azure AD.

> [!NOTE]
> Podem federar pilha do Azure apenas com outra instância do AD FS que é copiado em segurança pelo Active Directory. Não são suportados outros fornecedores de identidade. Se tiver outros fornecedores de identidade que pretende utilizar com a pilha do Azure, considere a utilização em vez disso, a implementação do Azure baseada no AD.

### <a name="ad-fs-and-graph-integration"></a>Integração do AD FS e o gráfico

Se optar por implementar pilha do Azure através do AD FS como o fornecedor de identidade, tem de integrar a instância do AD FS na pilha do Azure com uma instância existente do AD FS através de uma fidedignidade de Federação. Isto permite que as identidades na floresta do Active Directory existente para autenticar com recursos na pilha do Azure.

Também pode integrar o serviço do gráfico na pilha do Azure com o Active Directory existente. Isto permite-lhe gerir baseada em funções controlo de acesso (RBAC) na pilha do Azure. Quando o acesso a um recurso é delegado, a conta de utilizador na floresta do Active Directory existente, utilizando o protocolo LDAP procura o componente de gráfico.

O diagrama seguinte mostra o fluxo de tráfego do AD FS e do Graph integrado.
![Diagrama que mostra o fluxo de tráfego do AD FS e o gráfico](media/azure-stack-planning-considerations/ADFSIntegration.PNG)

## <a name="licensing-model"></a>Modelo de licenciamento

Tem de decidir o modelo de licenciamento que pretende utilizar. Para uma implementação ligada, pode escolher pay-como-utiliza ou com base na capacidade de licenciamento. Pay-como-utiliza requer uma ligação para o Azure para reportar a utilização, que, em seguida, é faturada através de comércio do Azure. Apenas capacidade com base no licenciamento é suportada se implementar desligado da internet. Para obter mais informações sobre os modelos de licenciamento, consulte [Microsoft Azure pilha empacotamento e preços](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf).

## <a name="naming-decisions"></a>Decisões de nomenclatura

Tem de pensar sobre a forma como pretende planear o seu espaço de nomes pilha do Azure, especialmente o nome de região e nome de domínio externo. O nome de domínio completamente qualificado (FQDN) da sua implementação de pilha do Azure para pontos finais de destinado ao público é a combinação destes dois nomes, &lt; *região*&gt;&lt;*external_FQDN*  &gt;, por exemplo, *east.cloud.fabrikam.com*. Neste exemplo, os portais de pilha do Azure seria disponíveis nos seguintes URLs:

- https://portal.East.cloud.Fabrikam.com
- https://adminportal.East.cloud.Fabrikam.com

A tabela seguinte resume estas decisões de nomenclatura de domínio.

| Nome | Descrição | 
| -------- | ------------- | 
|nome de região | O nome da sua região do Azure pilha primeiro. Este nome é utilizado como parte do FQDN para os públicos endereços IP virtuais (VIPs) que gere a pilha do Azure. Normalmente, o nome da região é um identificador de localização física, tais como uma localização de centro de dados. | 
| Nome de domínio externo | O nome da zona de sistema de nomes de domínio (DNS) para pontos finais com acesso externo VIPs. Utilizado no FQDN para estes VIPs públicos. | 
| Nome de domínio (interno) privada | O nome do domínio (e zona DNS interna) criado na pilha do Azure para a gestão de infraestrutura. 
| | |

## <a name="certificate-requirements"></a>Requisitos de certificado

Para a implementação, terá de fornecer certificados de Secure Sockets Layer (SSL) para pontos finais de destinado ao público. Um nível elevado, certificados têm os seguintes requisitos:

> [!IMPORTANT]
> As informações de certificado neste artigo são fornecidas apenas como orientação geral. Antes de adquirir todos os certificados para a pilha do Azure, trabalhar com o seu parceiro de hardware do OEM. Os utilizadores deverão fornecer orientações de certificado e os requisitos mais detalhadas.

- Pode utilizar um certificado de caráter universal único ou pode utilizar um conjunto de certificados dedicados e utilizar carateres universais apenas para pontos finais, como o armazenamento e o Cofre de chaves.
- Certificados devem ser emitidos por uma autoridade de certificação fidedigna pública (AC) ou uma AC gerida pelo cliente.
 
A tabela seguinte mostra os serviços e o número de pontos finais de destinado ao público que exigem certificados para a implementação inicial de pilha do Azure. 

| Utilizado para | Ponto Final 
| -------- | ------------- | 
| O Azure Resource Manager (administrador) | adminmanagment. [Região]. [external_domain] |
| O Azure Resource Manager (utilizador) | gestão. [Região]. [external_domain] |
| Portal (administrador) | adminportal. [Região]. [external_domain] |
| Portal (utilizador) | Portal. [Região]. [external_domain] |
| Cofre de chaves (utilizador) | &#42;. cofre. [Região]. [external_domain] |
| Cofre de chaves (administrador) | &#42;. adminvault. [Região]. [external_domain] |
| Armazenamento | &#42;. blob. [Região]. [external_domain]<br>&#42;. tabela. [Região]. [external_domain]<br>&#42;. fila. [Região]. [external_domain]  |
| Gráfico * * | gráfico. [Região]. [external_domain] |
| AD FS * * | AD FS. [Região]. [external_domain] |
| | |

* * Certificados para pontos finais do gráfico e o AD FS só são necessárias para implementações do AD FS.

Se pretender utilizar um certificado de caráter universal único, precisa de um total de seis alternativa os nomes do requerente (SANs) para a implementação inicial de pilha do Azure. Estes SANs são: 

- &#42;. [Região]. [external_domain]
- &#42;. cofre. [Região]. [external_domain]
- &#42;. adminvault. [Região]. [external_domain]
- &#42;. blob. [Região]. [external_domain]
- &#42;. tabela. [Região]. [external_domain] 
- &#42;. fila. [Região]. [external_domain]

## <a name="time-synchronization"></a>Sincronização de hora

Tem de sincronizar o servidor de tempo de pilha do Azure com um servidor de hora externa que é resolvível através do endereço IP. É necessário para uma implementação desligada um servidor de tempo na rede empresarial.

## <a name="network-connectivity"></a>Conectividade de rede

### <a name="dns-integration"></a>Integração do DNS

Para resolver nomes DNS externos da pilha do Azure (por exemplo, www.bing.com), terá de fornecer os servidores DNS que pilha do Azure pode utilizar para reencaminhar pedidos DNS para o qual não é autoritativa pilha do Azure. Como entradas de implementação, tem de fornecer, pelo menos, dois servidores a utilizar como reencaminhadores de DNS para tolerância a falhas.

Para resolver nomes DNS de pontos finais de pilha do Azure da pilha fora de Azure (por exemplo, a partir de floresta empresarial), tem de integrar os servidores DNS que alojam a zona DNS externa para a pilha do Azure com os servidores DNS que alojam a zona de principal que pretende utilizar. Isto requer a resolução do nome DNS entre a pilha do Azure e zonas DNS existentes no Centro de dados. Para tal, deverá utilizar métodos, tais como a delegação de reencaminhamento ou zona condicional. Recomendamos que o se tem controlo direto sobre os servidores DNS que alojam a zona principal para o Azure pilha externo espaço de nomes DNS de reencaminhamento condicional. (Se a sua zona DNS de pilha do Azure externa é apresentado como um domínio subordinado do seu nome de domínio de empresa (por exemplo, azurestack.contoso.com e contoso.com), tem de utilizar a delegação de zona em vez disso.

### <a name="network-infrastructure"></a>Infraestrutura de rede

A infraestrutura de rede para a pilha do Azure é composta por várias redes lógicas que estão configuradas nos comutadores. O diagrama seguinte mostra estas redes lógicas e a forma integrar com o top-of-rack (TOR), controlador de gestão de placa base (BMC) e limite comutadores (rede de cliente).

![Ligações de diagrama e o comutador de rede lógica](media/azure-stack-planning-considerations/NetworkDiagram.png)

A tabela seguinte mostra as redes lógicas e os intervalos de sub-rede IPv4 associados que terá de planear.

| Rede lógica | Descrição | Tamanho | 
| -------- | ------------- | ------------ | 
| VIP público | Endereços IP públicos para um pequeno conjunto de serviços de pilha do Azure, com o resto utilizado pelas máquinas virtuais de inquilino. A infraestrutura de pilha do Azure utiliza 32 endereços desta rede. Se planeia utilizar o serviço de aplicações e os fornecedores de recursos do SQL Server, esta opção utiliza mais de 7. | / 26 (62 anfitriões) - /22 (1022 anfitriões)<br><br>Recomendado = /24 (254 anfitriões) | 
| Infraestrutura de comutador | Endereços IP-to-Point para efeitos de encaminhamento, dedicados mudar interfaces de gestão e os endereços de loopback atribuídos ao comutador. | /26 | 
| Infraestrutura | Utilizado para componentes internos de pilha do Azure para comunicar. | /24 |
| Privado | Utilizado para a rede de armazenamento e VIPs privadas. | /24 | 
| BMC | Utilizado para comunicar com os BMCs nos anfitriões físicos. | /27 | 
| | | |

### <a name="uplink-to-border-switches"></a>Uplink aos comutadores de limite

Irá precisar de um uplink configurado para os comutadores de limite no seu centro de dados. Pode encaminhar este tráfego de camada 3 de comutadores (TOR) top-of-rack que fazem parte de um sistema de pilha do Azure integrado utilizando um dos seguintes métodos:

- Protocolo Border Gateway Protocol (BGP) 
- encaminhamento estático

Recomendamos BGP porque permitem que a atualização automática de rotas que são publicadas pelo Software carga balanceamento Multiplexer (SLB MUX) para redes externas. Isto é importante se adicionar intervalos de endereços IP públicos após a implementação. Se o fizer BGP peering de TOR muda para o comutador de agregação que estão ligados os comutadores TOR à, intervalos de endereço IP público que adicionar pós-implementação são automaticamente anunciados no comutador de agregação sem uma intervenção manual. Se utilizar o encaminhamento estático, tem de atualizar manualmente as rotas de intervalos de nova sempre que adicionar um bloco de endereços IP público.

### <a name="proxy-server"></a>Servidor proxy

Pilha do Azure suporta apenas servidores de proxy transparente. Um proxy transparente interceta pedidos na camada de rede, sem necessidade de qualquer configuração de cliente especiais.

### <a name="publish-azure-stack-services"></a>Publicar os serviços de pilha do Azure

Terá de disponibilizar os serviços do Azure pilha aos utilizadores da pilha fora do Azure. Pilha do Azure configura vários pontos finais para as respetivas funções de infraestrutura. Estes pontos finais são atribuídos VIPs do conjunto de endereço IP público. Uma entrada DNS é criada para cada ponto final na zona DNS externa, que foi especificada no momento da implementação. Por exemplo, o portal de utilizador está atribuído a entrada de anfitrião DNS de "portal. <*região*>. <*external_FQDN*>." 

#### <a name="ports-and-urls"></a>URLs e portas

Para tornar os serviços de pilha do Azure (por exemplo, portais do Azure Resource Manager, DNS, etc.) disponível para redes externas, tem de permitir tráfego de entrada para estes pontos finais para URLs, portas e protocolos específicos.
 
Numa implementação em que um uplink de proxy transparentes para um servidor proxy tradicionais, tem de permitir URLs e portas específicas para comunicação de saída. Estes incluem portas e URLs para identidade, sindicação do marketplace, patch e atualização, registo e dados de utilização.

Para obter mais informações, consulte:
- [Protocolos e portas de entrada](https://docs.microsoft.com/azure/azure-stack/azure-stack-integrate-endpoints#ports-and-protocols-inbound)
- [URLs e portas de saída](https://docs.microsoft.com/azure/azure-stack/azure-stack-integrate-endpoints#ports-and-urls-outbound)

### <a name="connect-azure-stack-to-azure"></a>Ligar a pilha do Azure para o Azure

Para cenários de nuvem híbrida, terá de planear a forma como pretende ligar pilha do Azure para o Azure. Existem dois métodos suportados para ligar redes virtuais na pilha do Azure para redes virtuais no Azure: 
- **Site-site**. Uma ligação de rede privada virtual (VPN) através de IPsec (IKE v1 e IKE v2). Este tipo de ligação requer um dispositivo VPN ou de encaminhamento e serviço de acesso remoto (RRAS). Para obter mais informações sobre gateways de VPN no Azure, consulte [sobre o Gateway de VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). A comunicação por este túnel é encriptada e é segura. No entanto, a largura de banda é limitada pelo débito máximo do túnel (100-200 Mbps).
- **NAT de saída**. Por predefinição, todas as máquinas virtuais na pilha do Azure irá ter conetividade para redes externas através de NAT de saída. Cada rede virtual que é criado na pilha do Azure obtém um endereço IP público atribuído. Se a máquina virtual diretamente é atribuída um endereço IP público, ou se encontrar atrás de um balanceador de carga com um endereço IP público, terá acesso de saída através de NAT de saída com o VIP da rede virtual. Isto funciona apenas para comunicação que é iniciada pela máquina virtual e destinada a redes externas (internet ou da intranet). Não pode ser utilizado para comunicar com a máquina virtual a partir de fora.

#### <a name="hybrid-connectivity-options"></a>Opções de conectividade híbrida

Para conectividade híbrida, é importante considerar o tipo de implementação que pretende oferecer e onde irá ser implementada. Terá de considerar a necessidade isolar o tráfego de rede por inquilino e se tem uma implementação de intranet ou à internet.

- **Pilha do Azure de inquilino único**. Uma implementação de pilha do Azure que procura, pelo menos de uma perspetiva de rede, como se fosse um inquilino. Podem existir que várias subscrições de inquilino, mas como qualquer serviço de intranet, todo o tráfego percorre as mesmas redes. Tráfego de rede a partir de uma subscrição percorre a mesma ligação de rede como outra subscrição e não tem de ser isolada através de um túnel encriptado.

- **Pilha do Azure multi-inquilino**. Uma implementação de pilha do Azure onde o tráfego de cada subscrição de inquilino que está vinculado a redes externas ao Azure pilha deve ser isolado do tráfego de rede dos outros inquilinos.
 
- **Implementação de intranet**. Uma implementação de pilha do Azure que se encontre numa intranet empresarial, normalmente, num espaço de endereços IP privados e atrás de firewalls de um ou mais. Os endereços IP públicos não são verdadeiramente públicos, como não pode ser encaminhados diretamente através da internet pública.

- **Implementação de Internet**. Uma implementação de pilha do Azure que está ligada ao público os endereços IP público encaminháveis para a internet internet e utiliza para o intervalo de VIP público. A implementação ainda pode manter-se protegido por uma firewall, mas o intervalo de VIP público é diretamente acessível a partir da internet e o Azure público.
 
A tabela seguinte resume os cenários de conectividade híbrida, com o profissionais de TI, contras e casos de utilização.

| Cenário | Método de conectividade | Profissionais de TI | Contras | Boa para |
| -- | -- | --| -- | --|
| Único inquilino do Azure pilha, implementação de intranet | NAT de saída | Uma melhor largura de banda para transferências mais rápidas. Simples de implementar; Não existem gateways necessários. | Tráfego não encriptado; sem isolamento ou a encriptação para além de TOR. | Implementações em empresas onde todos os inquilinos são igualmente fidedignos.<br><br>Empresas que têm um circuito ExpressRoute do Azure para o Azure. |
| Pilha de Azure multi-inquilino, implementação de intranet | VPN de site a site | Tráfego de inquilino VNet para o destino é seguro. | Largura de banda é limitada pelo túnel VPN de site para site.<br><br>Necessita de um gateway de rede virtual e um dispositivo VPN na rede de destino. | Implementações em empresas onde algumas tráfego de inquilino tem de estar protegidas de outros inquilinos. |
| Único inquilino do Azure pilha, implementação de internet | NAT de saída | Uma melhor largura de banda para transferências mais rápidas. | Tráfego não encriptado; sem isolamento ou a encriptação para além de TOR. | Alojamento de cenários em que o inquilino obtém as seus próprios implementação da pilha do Azure e um circuito dedicado para o ambiente de pilha do Azure. Por exemplo, ExpressRoute e de mudança de etiqueta Multiprotocol (MPLS).
| Pilha de Azure multi-inquilino, implementação de internet | VPN de site a site | Tráfego de inquilino VNet para o destino é seguro. | Largura de banda é limitada pelo túnel VPN de site para site.<br><br>Necessita de um gateway de rede virtual e um dispositivo VPN na rede de destino. | Cenários em que pretende oferecem uma nuvem de multi-inquilino o fornecedor de alojamento, em que os inquilinos não confiam entre si e o tráfego tem de estar encriptado.
|  |  |  |  |  |

#### <a name="using-expressroute"></a>Com o ExpressRoute

Pode ligar a pilha do Azure ao Azure através de [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) para intranet de inquilino único e cenários de multi-inquilinos. Irá precisar de um circuito ExpressRoute aprovisionado através de [um fornecedor de conectividade](https://docs.microsoft.com/azure/expressroute/expressroute-locations).

O diagrama seguinte mostra o ExpressRoute para um cenário de inquilino único (em que "Ligação de cliente" é o circuito de ExpressRoute).

![Cenário de ExpressRoute único inquilino do diagrama que mostra](media/azure-stack-planning-considerations/ExpressRouteSingleTenant.PNG)

O diagrama seguinte mostra o ExpressRoute para um cenário de multi-inquilino.

![Cenário de ExpressRoute do diagrama que mostra multi-inquilino](media/azure-stack-planning-considerations/ExpressRouteMultiTenant.PNG)

## <a name="external-monitoring"></a>Monitorização externo
Para obter uma vista única de todos os alertas de implementação da pilha do Azure e dispositivos e integrar alertas IT service gestão fluxos de trabalho existentes para emissão de permissões, pode integrar pilha do Azure com o Centro de dados externo soluções de monitorização.

Incluído com a solução de pilha do Azure, o anfitrião de ciclo de vida de hardware é um computador fora da pilha do Azure que executa as ferramentas de gestão do fornecedor fornecidos pelo OEM para hardware. Pode utilizar estas ferramentas ou outras soluções que diretamente integram com soluções de monitorização existentes no seu centro de dados.

A tabela seguinte resume a lista de opções disponíveis atualmente.

| Área | Solução de monitorização externa |
| -- | -- |
| Software de pilha do Azure | - [Pacote de gestão de pilha do Azure para o Operations Manager](https://azure.microsoft.com/blog/management-pack-for-microsoft-azure-stack-now-available/)<br>- [Plug-in da Nagios](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details)<br>-Baseado em REST chamadas de API | 
| Servidores físicos (BMCs através de IPMI) | -Pacote de gestão do fornecedor do operations Manager<br>-Solução de fornecido de fornecedor de hardware do OEM<br>-Fornecedor de hardware Nagios plug-ins | OEM suportado parceiro (incluída) de solução de monitorização | 
| Dispositivos de rede (SNMP) | -Deteção de dispositivos de rede do operations Manager<br>-Solução de fornecido de fornecedor de hardware do OEM<br>-Comutador da Nagios Plug-in |
| Monitorização de estado de funcionamento de subscrição de inquilino | - [Pacote de gestão do System Center para o Windows Azure](https://www.microsoft.com/download/details.aspx?id=50013) | 
|  |  | 

Tenha em atenção os seguintes requisitos:
- A solução que utiliza tem de ser sem agente. Não é possível instalar agentes de terceiros dentro de componentes de pilha do Azure. 
- Se pretender utilizar o System Center Operations Manager, necessita do Operations Manager 2012 R2 ou o Operations Manager 2016.

## <a name="backup-and-disaster-recovery"></a>Cópia de segurança e recuperação após desastre

Planear a cópia de segurança e recuperação após desastre envolve a planear para ambos os Azure pilha infraestrutura subjacente que aloja máquinas virtuais de IaaS e PaaS serviços e para aplicações de inquilino e os dados. Terá de planear estas separadamente.

### <a name="protect-infrastructure-components"></a>Proteger os componentes de infraestrutura

Pilha do Azure efetua cópias de segurança de componentes de infraestrutura a uma partilha que especificar.

- Irá precisar de uma partilha de ficheiros SMB externa no servidor de ficheiros baseado no Windows existente ou um dispositivo de terceiros.
- Deve utilizar esta mesma partilha para a cópia de segurança de comutadores de rede e o anfitrião de ciclo de vida de hardware. O fornecedor do hardware OEM irão ajudá-lo a fornecer orientações para cópia de segurança e restauro destes componentes, como estes são externos à pilha do Azure. Está responsável pela execução de fluxos de trabalho de cópia de segurança com base na recomendação do fornecedor OEM.

Se ocorrer a perda catastrófica de dados, pode utilizar a cópia de segurança da infraestrutura para dados de implementação de reseed como recursos federados (em implementações desligados), implementação entradas e identificadores, contas de serviço, certificado de raiz AC, ofertas, e planos as subscrições, quotas, atribuições de política e a função RBAC e segredos do Cofre de chaves.
 
### <a name="protect-tenant-applications-on-iaas-virtual-machines"></a>Proteger aplicações de inquilino em máquinas de virtuais do IaaS

Pilha do Azure não fazer uma cópia de segurança inquilino aplicações e dados. Tem de planear para proteção de recuperação após desastre e de cópia de segurança para um destino externo à pilha do Azure. Proteção de inquilino é uma atividade condicionada por inquilino. Para máquinas virtuais de IaaS, os inquilinos podem utilizar tecnologias no convidado para proteger ficheiros, dados de aplicação e pastas do Estado do sistema. No entanto, como um fornecedor de serviço ou enterprise, pode pretender oferecer uma solução de cópia de segurança e recuperação no mesmo datacenter ou externamente numa nuvem.

Para fazer uma cópia de segurança das máquinas virtuais de Linux ou IaaS do Windows, tem de utilizar produtos de cópia de segurança com acesso ao sistema operativo convidado para proteger o ficheiro, pasta, estado do sistema operativo e dados da aplicação. Pode utilizar a cópia de segurança do Azure, System Center Data Center Protection Manager, ou suportada produtos de terceiros.

Para replicar dados para uma localização secundária e orquestrar ativação pós-falha de aplicação, se ocorrer um desastre, pode utilizar o Azure Site Recovery ou suportados produtos de terceiros. (No lançamento inicial dos sistemas integrados, do Azure Site Recovery não suporta a reativação pós-falha. No entanto, pode conseguir reativação pós-falha através de um processo manual.) Além disso, as aplicações que suportem replicação nativa (como o Microsoft SQL Server) podem replicar dados para outra localização onde a aplicação está em execução.

> [!IMPORTANT]
> Na versão inicial dos sistemas integrados, estamos vai suportar tecnologias de proteção que funcionam ao nível do convidado da máquina virtual IaaS. Não é possível instalar os agentes em servidores de infraestrutura subjacente.

## <a name="next-steps"></a>Passos seguintes

- Para obter informações sobre casos de utilização, aquisição, parceiros e fornecedores de hardware do OEM, consulte o [Azure pilha](https://azure.microsoft.com/overview/azure-stack/) página de produto.
- Para obter informações sobre o plano e a georreplicação disponibilidade pilha do Azure integrados sistemas, consulte o documento técnico: [pilha do Azure: uma extensão do Azure](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/). 
