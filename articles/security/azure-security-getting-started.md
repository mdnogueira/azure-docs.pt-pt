---
title: "Introdução à segurança do Microsoft Azure | Microsoft Docs"
description: "Este artigo fornece uma descrição geral das funcionalidades de segurança do Microsoft Azure e as considerações gerais para as organizações que estiver a migrar os seus recursos para um fornecedor de nuvem."
services: security
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: TomSh
ms.assetid: 8d8a0088-c85a-48e7-bd04-2bc7b78b0691
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: yurid
ms.openlocfilehash: e1ee07f2284df925b8bbd9050de7ae40fa66bf65
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2017
---
# <a name="getting-started-with-microsoft-azure-security"></a>Introdução à segurança do Microsoft Azure
Quando criar ou migrar os recursos de IT para um fornecedor de nuvem, são depender capacidades da organização para proteger as suas aplicações e dados com os serviços e os controlos fornecem para gerir a segurança dos seus recursos baseados na nuvem.

A infraestrutura do Azure foi concebida a partir da facilidade que as aplicações têm para alojar milhões de clientes em simultâneo e proporciona uma fundação fidedigna com a qual as empresas podem satisfazer as suas necessidades de segurança. Além disso, o Azure disponibiliza-lhe um vasto leque de opções de segurança configuráveis e a capacidade para controlá-las de modo a que possa personalizar a segurança e satisfazer os requisitos únicos das suas implementações.

Este artigo de descrição geral da segurança do Azure foca-se em:

* Serviços do Azure e funcionalidades que pode utilizar para ajudar a proteger os seus serviços e dados no Azure.
* Como o Microsoft protege a infraestrutura do Azure para ajudar a proteger os seus dados e aplicações.

## <a name="identity-and-access-management"></a>Gestão de identidades e acessos
Controlar o acesso à infraestrutura de TI, dados e aplicações é essencial. Microsoft Azure oferece estas capacidades por serviços como o Azure Active Directory (Azure AD), o Storage do Azure e o suporte para várias APIs e normas.

[Azure AD](../active-directory/active-directory-whatis.md) é um repositório de identidade e de motor que fornece autenticação, autorização e controlo de acesso para utilizadores da organização, grupos e objetos. O Azure AD também oferece aos programadores um método eficaz de integrar a gestão de identidades nas respetivas aplicações. Protocolos de norma da indústria, tais como [SAML 2.0](https://en.wikipedia.org/wiki/SAML_2.0), [WS-Federation](https://msdn.microsoft.com/library/bb498017.aspx), e [OpenID Connect](http://openid.net/connect/) tornar possível iniciar sessão em plataformas como .NET, Java, Node.js e PHP.

A Graph API baseada em REST permite que os programadores leiam e escrevam no diretório a partir de qualquer plataforma. Através do suporte para [OAuth 2.0](http://oauth.net/2/), os programadores podem criar móveis e aplicações web que se integram com a Microsoft e de terceiros web APIs e criar as suas próprias APIs de web segura. As bibliotecas cliente de código aberto estão disponíveis para .Net, Loja Windows, iOS e Android, estando em desenvolvimento bibliotecas adicionais.

### <a name="how-azure-enables-identity-and-access-management"></a>Como o Azure ativa a gestão de identidades e acessos
O Azure AD pode ser utilizado como um diretório em nuvem autónomo para a sua organização ou como uma solução integrada com o Active Directory existente no local. Algumas funcionalidades de integração incluem a sincronização de diretórios e o início de sessão único (SSO). Estes expandem o alcance das identidades no local existentes para a nuvem e melhoram a experiência de administrador e utilizador.

Outras funcionalidades para a gestão de identidades e acessos incluem:

* O Azure AD permite [SSO](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/) para aplicações SaaS, independentemente de onde estão alojadas. Algumas aplicações estão federadas com o Azure AD e outras utilizam SSO com palavra-passe. As aplicações federadas também podem suportar o aprovisionamento do utilizador e cofres de palavras-passe.
* O acesso aos dados do [Armazenamento do Azure](https://azure.microsoft.com/services/storage/) é controlado através da autenticação. Cada conta de armazenamento tem uma chave primária ([chave da conta de armazenamento](https://msdn.microsoft.com/library/azure/ee460785.aspx), ou SAK) e uma chave secreta secundária (a assinatura de acesso partilhado ou SAS).
* O Azure AD fornece identidade como um serviço através de Federação utilizando [serviços de Federação do Active Directory](../active-directory/fundamentals-identity.md), sincronização e a replicação de diretórios no local.
* [Multi-factor Authentication do Azure](../multi-factor-authentication/multi-factor-authentication.md) é o serviço de autenticação multifator que exige que os utilizadores verificar os inícios de sessão utilizando uma aplicação móvel, chamada telefónica ou mensagem de texto. Pode ser utilizado com o Azure AD para o ajudar a proteger recursos no local com o servidor multi-factor Authentication do Azure bem como com aplicações personalizadas e diretórios utilizando o SDK.
* [Serviços de domínio do Azure AD](https://azure.microsoft.com/services/active-directory-ds/) permite-lhe associar máquinas virtuais do Azure a um domínio sem implementar a política de controladores de domínio. Pode iniciar sessão para estas máquinas virtuais com as suas credenciais do Active Directory empresarias e administrar máquinas de virtuais associados a um domínio utilizando a política de grupo para impor as linhas de base de segurança em todas as suas máquinas virtuais do Azure.
* [O Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) fornece um serviço de gestão de identidade global altamente disponíveis para aplicações direcionadas para o consumidor preparada para centenas de milhões de identidades. Pode ser integrado entre plataformas móveis e Web. Os consumidores podem iniciar sessão todas as suas aplicações através de experiências personalizáveis através das respetivas contas de redes sociais existentes ou criação de novas credenciais.

## <a name="data-access-control-and-encryption"></a>Controlo e encriptação de acesso de dados
A Microsoft emprega os princípios de Separação de Funções e [Menor Privilégio](https://en.wikipedia.org/wiki/Principle_of_least_privilege) em todas as operações do Azure. O acesso a dados por parte das equipas suporte do Azure requer a sua permissão explícita, e é concedido numa base “just-in-time” que é iniciada e auditada, e revogada após a conclusão do compromisso.

O Azure também oferece várias capacidades para proteger dados em trânsito e rest. Isto inclui a encriptação de dados, ficheiros, aplicações, serviços, as comunicações e unidades. Pode encriptar informações antes de colocá-lo no Azure e também armazenar chaves nos seus centros de dados no local.

![Microsoft Antimalware no Azure](./media/azure-security-getting-started/sec-azgsfig1.PNG)

### <a name="azure-encryption-technologies"></a>Tecnologias de encriptação do Azure
Pode recolher detalhes sobre acesso administrativo para o seu ambiente de subscrição com os [Relatórios do Azure AD](../active-directory/active-directory-reporting-audit-events.md). Pode configurar [encriptação de unidade BitLocker](https://technet.microsoft.com/library/cc732774.aspx) em VHDs que contêm informações confidenciais no Azure.

Outras capacidades do Azure que irão ajudá-lo a manter os seus dados protegidos incluem:

* Os programadores de aplicações podem criar encriptação para as aplicações que implementam no Azure utilizando o Windows [CryptoAPI](https://msdn.microsoft.com/library/ms867086.aspx) e .NET Framework.
* Controla totalmente as chaves com a encriptação do lado do cliente para o Blob storage do Azure. O serviço de armazenamento nunca vê as chaves e não consegue desencriptar os dados.
* [O Azure Rights Management (Azure RMS)](https://technet.microsoft.com/library/jj585026.aspx) (com o [RMS SDK](https://msdn.microsoft.com/library/dn758244.aspx)) fornece o ficheiro e a prevenção de fuga de dados de encriptação e de nível de dados através da gestão de acesso baseado em políticas.
* Suporte do Azure [encriptação ao nível da tabela e ao nível da coluna (TDE/CLE)](http://blogs.msdn.com/b/sqlsecurity/archive/2015/05/12/recommendations-for-using-cell-level-encryption-in-azure-sql-database.aspx) em máquinas virtuais do SQL Server e suporta independente no local servidores de gestão de chaves nos centros de dados.
* As Chaves de Conta de Armazenamento, Assinaturas de Acesso Partilhado, certificados de gestão e outras chaves são exclusivas de cada inquilino do Azure.
* Azure [StorSimple](http://www.microsoft.com/server-cloud/products/storsimple/overview.aspx) armazenamento híbrida encripta os dados através de um par de chaves públicas/privadas 128 bits antes de o carregar para o armazenamento do Azure.
* Azure suporta e utiliza vários mecanismos de encriptação, incluindo SSL/TLS, IPsec e AES, consoante os tipos de dados, contentores e transportes.

## <a name="virtualization"></a>Virtualização
A plataforma Azure utiliza um ambiente virtualizado. Instâncias de utilizador funcionam como máquinas virtuais autónomas que não têm acesso a um servidor de anfitrião físico, e este isolamento é imposto utilizando físico [níveis de privilégio do processador (em anel-0/em anel-3)](https://en.wikipedia.org/wiki/Protection_ring).

O Ring 0 é mais privilegiado e o 3 é o menos. O SO convidado é executado no menor privilégio anel 1 e as aplicações são executadas no 3 anel com menos privilégios. Esta virtualização dos recursos físicos conduz a uma separação clara entre o SO convidado e o hipervisor, resultando na separação de segurança adicional entre os dois.

O hipervisor do Azure funciona como um kernel micro e passa todos os pedidos de acesso de hardware de máquinas virtuais convidadas para o anfitrião para processamento através da utilização de uma interface de memória partilhada chamada VMBus. Isto impede os utilizadores de obterem acesso de leitura/escrita/execução não processado ao sistema e atenua o risco de partilha de recursos do sistema.

![Microsoft Antimalware no Azure](./media/azure-security-getting-started/sec-azgsfig2.PNG)

### <a name="how-azure-implements-virtualization"></a>Como o Azure implementa a virtualização
Azure utiliza uma firewall de hipervisor (filtro de pacote), que é implementada no hipervisor e configurada por um agente de controlador de recursos de infraestrutura. Isto ajuda a proteger os inquilinos de acesso não autorizado. Por predefinição, todo o tráfego é bloqueado quando é criada uma máquina virtual e, em seguida, o agente de controlador de recursos de infraestrutura configura o filtro de pacotes para adicionar *regras e as exceções* para permitir tráfego autorizado.

Existem duas categorias de regras que são programadas aqui:

* **Regras de infraestrutura ou de configuração do computador**: por predefinição, todas as comunicações está bloqueada. Existem exceções para permitir que uma máquina virtual para enviar e receber tráfego DHCP e DNS. Máquinas virtuais também pode enviar o tráfego para a internet "pública" e enviar o tráfego para outras máquinas virtuais no cluster e o servidor de ativação de SO. Lista as máquinas virtuais de destinos permitidos de saída não inclui as sub-redes do router do Azure, Azure gestão back-end e outras propriedades de Microsoft.
* **Ficheiro de configuração de função**: Isto define a entrada acesso as listas de controlo (ACLs) com base no modelo de serviço do inquilino. Por exemplo, se um inquilino tem um front-end da Web na porta 80 numa determinada máquina virtual, em seguida, Azure abre-se a porta TCP 80 para todos os IPs se estiver a configurar um ponto final no [modelo de implementação clássica do Azure](../azure-resource-manager/resource-manager-deployment-model.md). Se a máquina virtual tiver uma função de fim ou trabalho anterior em execução, em seguida, abre a função de trabalho apenas para a máquina virtual no mesmo inquilino.

## <a name="isolation"></a>Isolamento
Outro requisito de segurança de nuvem importante é a separação, para impedir a transferência não intencional e não autorizados de informações entre implementações numa arquitetura multi-inquilino partilhada.

Implementa Azure [controlo de acesso de rede](https://azure.microsoft.com/blog/network-isolation-options-for-machines-in-windows-azure-virtual-networks/) e segregação isolação de VLAN, as ACLs, através do carregar balanceadores e filtros de IP. Restringe o tráfego externo entrada para as portas e protocolos em máquinas virtuais por si. Azure implementa rede filtragem para impedir a denuncia tráfego e restringir o tráfego de entrada e saído para componentes de plataforma fidedigna. As políticas de fluxo de tráfego são implementadas em dispositivos de proteção de limites que negam tráfego por predefinição.

![Microsoft Antimalware no Azure](./media/azure-security-getting-started/sec-azgsfig3.PNG)

A Tradução de Endereços de Rede (NAT) é utilizada para separar o tráfego de rede interno do tráfego externo. O tráfego interno não é encaminhável externamente. Os [Endereços IP virtuais](http://blogs.msdn.com/b/cloud_solution_architect/archive/2014/11/08/vips-dips-and-pips-in-microsoft-azure.aspx) que são encaminháveis externamente são convertidos em endereços [IP Dinâmicos Internos](http://blogs.msdn.com/b/cloud_solution_architect/archive/2014/11/08/vips-dips-and-pips-in-microsoft-azure.aspx) que só são encaminháveis dentro do Azure.

O tráfego externo para máquinas virtuais do Azure é com firewall através de ACLs nos routers, balanceadores de carga, e comutadores de camada 3. Apenas são permitidos protocolos conhecidos específicos. As ACLs são implementados para limitar o tráfego proveniente de máquinas virtuais convidadas a outras VLANs utilizadas para gestão. Além disso, o tráfego filtrado através de filtros IP no anfitrião de que SO mais limita o tráfego em ambas as camadas de ligação e a rede de dados.

### <a name="how-azure-implements-isolation"></a>Como o Azure implementa o isolamento
O controlador de recursos de infraestrutura do Azure é responsável pela alocação de recursos de infraestrutura para cargas de trabalho de inquilino e gere unidirecionais comunicações do anfitrião para máquinas virtuais. O hipervisor do Azure impõe a separação de processo e de memória entre máquinas virtuais e, em segurança encaminha o tráfego de rede aos inquilinos de SO convidado. Azure também implementa o isolamento de inquilinos, armazenamento e redes virtuais.

* Cada inquilino do Azure AD é logicamente isolado através da utilização de limites de segurança.
* As contas do storage do Azure são exclusivas para cada subscrição e acesso tem de ser autenticado utilizando uma chave de conta do storage.
* Redes virtuais são logicamente isoladas através de uma combinação de exclusivos endereços IP privados, as firewalls e ACLs de IP. Os balanceadores de carga encaminham o tráfego para os inquilinos adequados, com base nas definições de ponto final.

## <a name="virtual-networks-and-firewalls"></a>Redes virtuais e as firewalls
O [redes virtuais e distribuídas](http://download.microsoft.com/download/4/3/9/43902EC9-410E-4875-8800-0788BE146A3D/Windows%20Azure%20Network%20Security%20Whitepaper%20-%20FINAL.docx) na ajuda do Azure Certifique-se de que o tráfego de rede privada é logicamente isolado do tráfego nas outras redes virtuais do Azure.

![Microsoft Antimalware no Azure](./media/azure-security-getting-started/sec-azgsfig4.PNG)

A subscrição pode conter várias redes privadas isolados (e incluem firewall, o balanceamento de carga e a tradução de endereços de rede).

O Azure oferece três níveis primários de segregação de rede em cada cluster do Azure para segregar tráfego logicamente. [As redes locais virtuais](https://azure.microsoft.com/services/virtual-network/) (VLANs) são utilizadas para separar o tráfego de cliente do resto da rede do Azure. O acesso à rede do Azure a partir de fora do cluster é restringido através de balanceadores de carga.

Tráfego de rede de e para máquinas virtuais tem de passar através do comutador virtual de hipervisor. O componente de filtro IP na raiz SO isola a máquina virtual de raiz de máquinas virtuais convidadas e as máquinas virtuais de convidado entre si. Efetua a filtragem de tráfego para restringir a comunicação entre nós de um inquilino e a Internet pública (com base na configuração de serviço do cliente), segregating-los a partir de outros inquilinos.

O filtro IP ajuda a impedir que as máquinas virtuais de convidado de:

* A gerar a denuncia tráfego.
* Receber tráfego endereçado não aos mesmos.
* Instruir o tráfego para pontos finais de infraestrutura protegido.
* Tráfego de difusão enviar ou receber inadequada.

Pode colocar as máquinas virtuais no [redes virtuais do Azure](https://azure.microsoft.com/documentation/services/virtual-network/). Estas redes virtuais são semelhantes às redes que configura em ambientes no local, onde estão normalmente associadas a um comutador virtual. Máquinas virtuais ligadas à mesma rede virtual podem comunicar entre si sem configuração adicional. Também pode configurar sub-redes diferentes na sua rede virtual.

Pode utilizar as seguintes tecnologias de rede Virtual do Azure para o ajudar a comunicação segura na sua rede virtual:

* [**Grupos de segurança (NSGs) de rede**](../virtual-network/virtual-networks-nsg.md). Pode utilizar um NSG para controlar tráfego para uma ou mais instâncias de máquina virtual na sua rede virtual. Um NSG contém as regras de controlo de acesso que permitem ou negam tráfego com base na direção, protocolo, endereço e porta de origem e endereço e porta de destino do tráfego.
* [**Definido pelo utilizador encaminhamento**](../virtual-network/virtual-networks-udr-overview.md). Pode controlar o encaminhamento de pacotes através de uma aplicação virtual ao criar rotas definidas pelo utilizador que especificam o salto seguinte para os pacotes que fluem para uma sub-rede específica para ir para uma aplicação de segurança de rede virtual.
* [**Reencaminhamento IP**](../virtual-network/virtual-networks-udr-overview.md). Uma aplicação virtual de segurança de rede deve ser capaz de receber tráfego de entrada que não esteja endereçado a si mesma. Para permitir que uma máquina virtual receba tráfego endereçado a outros destinos, ativar o reencaminhamento IP para a máquina virtual.
* [**Túnel forçado**](../vpn-gateway/vpn-gateway-about-forced-tunneling.md). Permite imposição de túnel redirecionar ou "forçar" todo o tráfego vinculado à Internet gerado pela suas máquinas virtuais numa rede virtual de volta para a localização no local através de um túnel VPN de site a site para inspeção e auditoria
* [**ACLs de ponto final**](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Pode controlar que máquinas são permitidas ligações de entrada da Internet para uma máquina virtual na sua rede virtual por definir ACLs de ponto final.
* [**Soluções de segurança da rede de parceiros**](https://azure.microsoft.com/marketplace/). Existem várias soluções de segurança de rede de parceiros pode aceder a partir do Azure Marketplace.

### <a name="how-azure-implements-virtual-networks-and-firewalls"></a>Como o Azure implementa redes virtuais e as firewalls
Azure implementa firewalls de filtragem de pacotes em todas as máquinas de virtuais de anfitrião e convidado por predefinição. Imagens de sistema operativo Windows no Azure Marketplace tem também a Firewall do Windows ativada por predefinição. Balanceadores de carga no perímetro das comunicações de controlo de redes destinado ao público do Azure com base nas ACLs de IP gerida pelos administradores do cliente.

Se o Azure mover os dados de um cliente como parte das operações normais ou durante um desastre, isto é feito através de canais de comunicação privados e encriptados. Outras capacidades utilizadas pelo Azure para utilizar em redes virtuais e as firewalls são:

* **Firewall de anfitrião nativo**: Azure Service Fabric e Storage do Azure ser executados num SO nativo que não tenha nenhum hipervisor. Por conseguinte, a firewall do windows está configurada com dois conjuntos de regras que se anterior. O armazenamento é executado no modo nativo para otimizar o desempenho.
* **Firewall de anfitrião**: A firewall do anfitrião está a proteger o sistema operativo anfitrião que executa o hipervisor. As regras são programadas para permitir apenas o controlador de Service Fabric e ir caixas para falar com o anfitrião do SO numa porta específica. As outras exceções são permitir a resposta DHCP e Respostas DNS. Azure utiliza um ficheiro de configuração de máquina com o modelo de regras de firewall para o anfitrião do SO. O próprio anfitrião se encontra protegido contra ataques externo por uma firewall do Windows configurada para permitir a comunicação apenas a partir de origens conhecidas, autenticadas.
* **Firewall de convidado**: replica as regras no filtro de pacotes de comutador de máquina virtual, mas programados e outro software (por exemplo, a peça de Firewall do Windows do SO convidado). A firewall da máquina virtual de convidado pode ser configurada para restringir comunicações de ou para a máquina virtual convidada, mesmo que a comunicação é permitida por configurações, o filtro de IP do anfitrião. Por exemplo, pode optar por utilizar a firewall da máquina virtual de convidado para restringir a comunicação entre duas as suas VNets que tenham sido configurados para ligar a um do outro.
* **Firewall de armazenamento (FW)**: firewall front-end armazenamento filtros de tráfego seja apenas em portas 80/443 e outras portas utilitário necessário. A firewall no armazenamento de back-end restringe comunicações fique apenas a partir de servidores de front-end de armazenamento.
* **Gateway de rede virtual**: O [Gateway de rede Virtual do Azure](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md) funciona como o gateway entre locais ligar as cargas de trabalho na rede Virtual do Azure para os sites no local. É necessário para ligar a sites no local através de [túneis VPN de site para site IPsec](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md), ou através de [ExpressRoute](../expressroute/expressroute-introduction.md) circuitos. Para os túneis VPN IPsec/IKE, os gateways efetuar IKE handshakes e estabelecer túneis IPsec S2S VPN entre as redes virtuais e os sites no local. Gateways de rede virtual também terminam [VPNs ponto a site](../vpn-gateway/vpn-gateway-point-to-site-create.md).

## <a name="secure-remote-access"></a>Proteger o acesso remoto
Os dados armazenados na nuvem têm de ter ativadas salvaguardas suficientes para evitar vulnerabilidades e manter a confidencialidade e integridade em trânsito. Isto inclui controlos de rede que se juntam aos mecanismos auditáveis de gestão de identidades e acessos, baseados na política da organização.

A tecnologia de criptografia incorporada permite-lhe encriptar as comunicações dentro e entre as implementações, entre regiões do Azure e do Azure para datacenters no local. Acesso de administrador para máquinas virtuais através de [sessões de ambiente de trabalho remotas](../virtual-machines/windows/classic/connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json), [remota do Windows PowerShell](http://blogs.technet.com/b/heyscriptingguy/archive/2013/09/07/weekend-scripter-remoting-the-cloud-with-windows-azure-and-powershell.aspx), e o portal do Azure é sempre encriptado.

Para expandir o seu centro de dados no local para a nuvem em segurança, a Azure oferece [VPN site a site](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md) e [VPN ponto a site](../vpn-gateway/vpn-gateway-point-to-site-create.md), juntamente com hiperligações dedicadas com [ExpressRoute](../expressroute/expressroute-introduction.md) (as ligações a redes virtuais do Azure através de VPN são encriptadas).

### <a name="how-azure-implements-secure-remote-access"></a>Como o Azure implementa o acesso remoto seguro
As ligações ao portal do Azure têm sempre de ser autenticadas e requer SSL/TLS. Pode configurar certificados de gestão para ativar a gestão segura. Protocolos de segurança de norma da indústria, tais como [SSTP](https://technet.microsoft.com/magazine/2007.06.cableguy.aspx) e [IPsec](https://en.wikipedia.org/wiki/IPsec) são totalmente suportadas.

O [ExpressRoute do Azure](../expressroute/expressroute-introduction.md) permite-lhe criar ligações privadas entre os datacenters do Azure e a infraestrutura no local ou num ambiente de colocalização. As ligações do ExpressRoute não passam para a Internet pública. Oferecem mais fiabilidade, velocidades mais rápidas, latências inferiores e uma maior segurança ligações típicas baseado na Internet. Em alguns casos, a transferência de dados entre localizações no local e o Azure, utilizando as ligações do ExpressRoute pode também produzir benefícios de custos significativas.

## <a name="logging-and-monitoring"></a>Início de sessão e monitorização
O Azure oferece autenticado o registo de eventos relevantes de segurança que geram um registo de auditoria e é projetada seja resistente a adulteração. Isto inclui informações do sistema, tais como registos de eventos de segurança em máquinas virtuais de infraestrutura do Azure e o Azure AD. Monitorização de eventos de segurança inclui a recolha de eventos, tais como as alterações no DHCP ou DNS endereços IP do servidor; tentativa de acesso para as portas e protocolos ou endereços IP que estão bloqueados por predefinição; alterações nas definições de firewall ou política de segurança; criação de conta ou grupo; e processos inesperados ou instalação de controlador.

![Microsoft Antimalware no Azure](./media/azure-security-getting-started/sec-azgsfig5.PNG)

Os registos de auditoria que gravam o acesso e as atividades de utilizadores com privilégios, as tentativas de acesso autorizado e não autorizado, as exceções de sistema e os eventos de informações de segurança são retidos durante um determinado período de tempo. O período de retenção dos seus registos fica a seu critério porque a recolha e retenção de registos para os seus requisitos é configurada por si.

### <a name="how-azure-implements-logging-and-monitoring"></a>Como o Azure implementa o registo e a monitorização
O Azure implementa Agentes de Gestão (MA) e agentes do Monitor de Segurança do Azure (ASM) para cada nó de computação, armazenamento ou de recursos de infraestrutura sob gestão, quer sejam nativos ou virtuais. Cada Agente de Gestão está configurado para autenticar uma conta de armazenamento da equipa do serviço com um certificado obtido a partir do arquivo de certificados do Azure e para reencaminhar dados pré-configurados de diagnóstico e de eventos para a conta de armazenamento. Estes agentes não são implementados nas máquinas virtuais dos clientes.

Os administradores do Azure acedem a registos através de um portal Web para acesso autenticado e controlado aos registos. Um administrador pode analisar, filtrar e correlacionar os registos. As contas de armazenamento da equipa do serviço do Azure para registos estão protegidas contra acesso direto pelo administrador, para ajudar a evitar a adulteração de registos.

A Microsoft recolhe registos de dispositivos de rede utilizando o protocolo de Syslog e os servidores de anfitrião utilizando os serviços de recolha de auditorias (ACS) do Microsoft. Estes registos são colocados numa base de dados do registo de que são gerados alertas para eventos suspeitas. O administrador pode aceder a estes registos e analisá-los.

[Diagnósticos do Azure](https://msdn.microsoft.com/library/azure/gg433048.aspx) é uma funcionalidade do Azure que permite recolher dados de diagnóstico de uma aplicação em execução no Azure. Estes são dados de diagnóstico para a depuração e resolução de problemas, medir o desempenho, a utilização de recursos, análise de tráfego, o planeamento de capacidade de monitorização e auditoria. Depois de os dados de diagnóstico serem recolhidos, podem ser transferidos para uma conta de armazenamento do Azure, para persistência. Transferências: podem ser agendadas ou a pedido.

## <a name="threat-mitigation"></a>Mitigação de ameaça
Para além do isolamento, da encriptação e da filtragem, o Azure emprega vários mecanismos e processos de atenuação de ameaças para proteger a infraestrutura e os serviços. Estes incluem os controlos e tecnologias internos utilizados para detetar e remediar ameaças avançadas, como DDoS, escalamento de privilégios e o [OWASP Top-10](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project).

Os controlos de segurança e os processos de gestão de riscos implementados pela Microsoft para proteger a infraestrutura da nuvem reduzem o risco de incidentes de segurança. No caso de ocorre um incidente, a equipa de gestão de incidentes de segurança (SIM) dentro da equipa Microsoft Online Services de segurança e conformidade (OSSC) está pronta para responder a qualquer momento.

### <a name="how-azure-implements-threat-mitigation"></a>Como o Azure implementa a atenuação de ameaças
O Azure tem controlos de segurança no local para implementar mitigação de ameaças e também para ajudar a mitigar ameaças potenciais nos respetivos ambientes de clientes. A lista seguinte resume as capacidades de mitigação de ameaça oferecidas pelo Azure:

* [Antimalware do Azure](azure-security-antimalware.md) está ativada por predefinição em todos os servidores de infraestrutura. Opcionalmente, pode ativá-la no seus próprio máquinas virtuais.
* A Microsoft mantém a monitorização contínua de servidores, redes e aplicações para detetar ameaças e evitar explorações. Alertas automatizados notificam os administradores de comportamentos anómalos, permitindo-lhes tomar uma ação corretiva nas ameaças internas e externas.
* Pode implementar soluções de segurança de terceiros na suas subscrições, tais como firewalls de aplicação web de [Barracuda](https://techlib.barracuda.com/ng54/deployonazure).
* A abordagem da Microsoft para o teste de penetração inclui "[Red de agrupamento](http://download.microsoft.com/download/C/1/9/C1990DBA-502F-4C2A-848D-392B93D9B9C3/Microsoft_Enterprise_Cloud_Red_Teaming.pdf)," que envolve a profissionais de segurança do Microsoft atacar sistemas de produção em direto (não-cliente) no Azure para testar defesas contra mundo real, avançada, ameaças persistentes.
* Sistemas de implementação integrada gerem a distribuição e instalação de patches de segurança nas plataformas do Azure.

## <a name="next-steps"></a>Passos seguintes
[Centro de Fidedignidade do Azure](https://azure.microsoft.com/support/trust-center/)

[Blogue da Equipa de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/)

[Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx)

[Blogue do Active Directory](http://blogs.technet.com/b/ad/)
