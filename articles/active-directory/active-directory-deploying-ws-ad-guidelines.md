---
title: "Diretrizes para a implementação do Windows Server Active Directory em máquinas virtuais do Azure | Microsoft Docs"
description: "Se souber como implementar os serviços de domínio do AD e serviços de Federação do AD no local, saiba como funcionam em máquinas virtuais do Azure."
services: active-directory
documentationcenter: 
author: femila
manager: femila
editor: 
ms.assetid: 04df4c46-e6b6-4754-960a-57b823d617fa
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/26/2017
ms.author: femila
ms.openlocfilehash: 342d9e2787add3d04f1b744152e135db98848179
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="guidelines-for-deploying-windows-server-active-directory-on-azure-virtual-machines"></a>Diretrizes para implementar o Windows Server Active Directory em máquinas virtuais do Azure
Este artigo explica as diferenças importantes entre implementar Windows Server Active Directory Domain Services (AD DS) e serviços de Federação do Active Directory (AD FS) no local em comparação com a implementação dos mesmos em máquinas virtuais do Microsoft Azure.

## <a name="scope-and-audience"></a>Âmbito e público-alvo
O artigo destina-se os já teve com a implementação do Active Directory no local. Aborda as diferenças entre a implementação do Active Directory em redes virtuais do Microsoft Azure máquinas virtuais/Azure e implementações de Active Directory tradicional no local. Máquinas virtuais do Azure e redes virtuais do Azure são parte de um infraestrutura-como-um-serviço (IaaS) oferta para as organizações a tirar partido dos recursos de computação na nuvem.

Para aqueles que não estiver familiarizado com a implementação do AD, consulte o [guia de implementação do AD DS](https://technet.microsoft.com/library/cc753963) ou [planear a implementação do AD FS](https://technet.microsoft.com/library/dn151324.aspx) conforme adequado.

Este artigo assume que o leitor está familiarizado com os seguintes conceitos:

* Gestão e implementação do Windows Server AD DS
* Implementação e configuração de DNS para suportar uma infraestrutura do Windows Server AD DS
* Gestão e implementação do Windows Server AD FS
* Implementar, configurar e gerir aplicações de terceiros (serviços web e Web sites) entidades confiadoras que podem consumir tokens do Windows Server AD FS
* Conceitos gerais máquina virtual, tais como configurar uma máquina virtual, discos virtuais e redes virtuais

Este artigo realça os requisitos para um cenário de implementação híbrida no qual a Windows Server AD DS ou AD FS estão implementado parcialmente no local e parcialmente implementados em máquinas virtuais do Azure. O documento abrange primeiro as diferenças entre em execução no AD DS e AD FS do Windows Server em virtual machines do Azure versus no local e pontos de decisão importantes que afetam a estrutura e implementação críticas. O resto do documento explica as diretrizes para cada um dos pontos de decisão em mais detalhe e como aplicar as diretrizes para vários cenários de implementação.

Este artigo não aborda a configuração de [do Azure Active Directory](http://azure.microsoft.com/services/active-directory/), que é um serviço baseado em REST que fornece capacidades de controlo de acesso e gestão de identidade para aplicações em nuvem. Azure Active Directory (Azure AD) e o Windows Server AD DS, no entanto, concebidas para trabalhar em conjunto para fornecer uma solução de gestão de identidades e acessos de TI de híbridos hoje ambientes e aplicações modernas. Para ajudar a compreender as diferenças e as relações entre o Windows Server AD DS e AD do Azure, considere o seguinte:

1. Poderá executar o Windows Server AD DS na nuvem em máquinas virtuais do Azure quando estiver a utilizar o Azure para expandir o seu centro de dados no local para a nuvem.
2. Pode utilizar o Azure AD para lhe conceder a utilizadores-início de sessão único às aplicações de Software-como-um-serviço (SaaS). Microsoft Office 365 utiliza esta tecnologia, por exemplo, e aplicações em execução no Azure ou outras plataformas de cloud também podem utilizá-la.
3. Pode utilizar o Azure AD (o serviço de controlo de acesso) para permitir que os utilizadores iniciam sessão utilizando as identidades de Facebook, Google, Microsoft e outros fornecedores de identidade para aplicações que estão alojadas na nuvem ou no local.

Para mais informações sobre estas diferenças, consulte [Azure Identity](fundamentals-identity.md).

## <a name="related-resources"></a>Recursos relacionados
Pode transferir e executar o [avaliação de preparação de Máquina Virtual do Azure](https://www.microsoft.com/download/details.aspx?id=40898). A avaliação será automaticamente inspecionar o seu ambiente no local e gerar um relatório personalizado com base na documentação de orientação encontrada neste tópico para o ajudar a migrar o ambiente para o Azure.

Recomendamos que reveja também primeiro os tutoriais, manuais e vídeos que abrangem os seguintes tópicos:

* [Configurar uma rede Virtual apenas na nuvem no Portal do Azure](../virtual-network/virtual-networks-create-vnet-arm-pportal.md)
* [Configurar uma VPN de Site para Site no Portal do Azure](../vpn-gateway/vpn-gateway-site-to-site-create.md)
* [Instalar uma nova floresta do Active Directory numa rede virtual do Azure](active-directory-new-forest-virtual-machine.md)
* [Instalar um controlador de domínio do Active Directory de réplica no Azure](active-directory-install-replica-active-directory-domain-controller.md)
* [Microsoft Azure IT Pro IaaS: Noções básicas de (01) Máquina Virtual](https://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)
* [Microsoft Azure IT Pro IaaS: (05) criar redes virtuais e conectividade em vários locais](https://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)

## <a name="introduction"></a>Introdução
Os requisitos fundamentais para implementar o Windows Server Active Directory em máquinas virtuais do Azure diferirem muito pouco de implementar a política em máquinas virtuais no local (e, para alguns extensão, as máquinas físicas). Por exemplo, no caso do Windows Server AD DS, se os controladores de domínio (DCs) que implementa em máquinas virtuais do Azure são réplicas no existente no local floresta/domínio empresarial, em seguida, a implementação do Azure grande parte pode ser tratada da mesma forma como pode trate a outro site do Windows Server Active Directory adicional. Ou seja, sub-redes tem de ser definidas no Windows Server AD DS, um site criado, as sub-redes ligados a esse site e ligados para outros sites utilizando ligações de site adequadas. No entanto, existem algumas diferenças que são comuns a todas as implementações do Azure e alguns que variar de acordo com o cenário de implementação específicos. Diferenças fundamentais dois descritas abaixo:

### <a name="azure-virtual-machines-may-need-connectivity-to-the-on-premises-corporate-network"></a>Máquinas virtuais do Azure poderá ter conectividade à rede empresarial no local.
Ligar máquinas virtuais do Azure numa rede empresarial no local requer a rede virtual do Azure, que inclui um site para site ou ponto a site virtual privada (VPN) de rede componente capaz de forma totalmente integrada ligar máquinas virtuais do Azure e máquinas no local. Este componente VPN também pode ativar computadores membros do domínio no local aceder a um domínio do Windows Server Active Directory cujos controladores de domínio estão alojados exclusivamente em máquinas virtuais do Azure. É importante ter em atenção, no entanto, que se falhar a VPN, autenticação e outras operações que dependem do Windows Server Active Directory também falharão. Enquanto os utilizadores podem ser capazes de início de sessão com existentes em cache as credenciais, todos os ponto-a-ponto ou as tentativas de autenticação de cliente-servidor para o qual bilhetes ainda ser emitidos ou ficou obsoleta irão falhar.

Consulte [rede Virtual](http://azure.microsoft.com/documentation/services/virtual-network/) para uma demonstração em vídeo e uma lista de tutoriais passo a passo, incluindo [configurar uma VPN de Site a Site no portal do Azure](../vpn-gateway/vpn-gateway-site-to-site-create.md).

> [!NOTE]
> Também pode implementar o Windows Server Active Directory numa rede virtual do Azure que não tenha conectividade com uma rede no local. As orientações neste tópico, no entanto, partem do princípio de que uma rede virtual do Azure é utilizada porque fornece capacidades que são essenciais para o Windows Server de endereçamento IP.
> 
> 

### <a name="static-ip-addresses-must-be-configured-with-azure-powershell"></a>Endereços IP estáticos tem de ser configurados com o Azure PowerShell.
Endereços dinâmicos são alocados por predefinição, mas utilizam o cmdlet Set-AzureStaticVNetIP para atribuir um endereço IP estático em vez disso. Que define um endereço IP estático que persistirá através de autorrecuperação do serviço e VM encerramento/reinício. Para obter mais informações, consulte [endereço interno de IP estático para máquinas virtuais](http://azure.microsoft.com/blog/static-internal-ip-address-for-virtual-machines/).

## <a name="BKMK_Glossary"></a>Termos e definições
Segue-se uma lista não exaustiva dos termos de várias tecnologias do Azure que irão ser referenciados neste artigo.

* **Máquinas virtuais do Azure**: IaaS a oferta no Azure que permite aos clientes implementar com VMs em execução praticamente qualquer tradicionalmente no local carga de trabalho do servidor.
* **Rede virtual do Azure**: O serviço de rede do Azure que permite aos clientes criar e gerir redes virtuais no Azure e ligá-las em segurança para os seus próprios no local infraestrutura de rede através da utilização de uma rede privada virtual (VPN).
* **Endereço IP virtual**: um endereço IP para a internet que não está vinculado a uma placa de interface de rede ou computador específica. Serviços cloud estão atribuídos um endereço IP virtual para a receção de tráfego de rede que é redirecionado para uma VM do Azure. Um endereço IP virtual é uma propriedade de um serviço em nuvem que pode conter um ou mais máquinas virtuais do Azure. Note também que uma rede virtual do Azure pode conter um ou mais serviços em nuvem. Endereços IP virtuais fornecem capacidades de balanceamento de carga nativas.
* **Endereço IP dinâmico**: Este é o endereço IP que é interno apenas. Este deve ser configurado como um endereço IP estático (utilizando o cmdlet Set-AzureStaticVNetIP) para as VMs que alojam as funções de servidor DC/DNS.
* **Autorrecuperação do serviço**: O processo no qual Azure automaticamente devolve um serviço para um Estado de execução novamente depois de detetar que o serviço falhou. Serviço de recuperação é um dos aspetos do Azure que suporta a disponibilidade e resiliência. Enquanto pouco provável, o resultado seguir um serviço autorrecuperação do incidente para um controlador de domínio em execução numa VM é semelhante a um reinício não planeado, mas tem alguns de atribuição de efeitos secundários:
  
  * O adaptador de rede virtual da VM será alterado
  * O endereço MAC do adaptador de rede virtual será alterado
  * O ID de processador/CPU da VM será alterado
  * A configuração de IP do adaptador de rede virtual não será alterado desde que a VM está ligada a uma rede virtual e endereço IP da VM é estático.
  
  Nenhum dos seguintes comportamentos afeta o Windows Server Active Directory porque não tem nenhum dependência no endereço MAC ou ID de processador/CPU e todas as implementações do Windows Server Active Directory no Azure são recomendadas para ser executada numa rede virtual do Azure, conforme descrito acima.

## <a name="is-it-safe-to-virtualize-windows-server-active-directory-domain-controllers"></a>É seguro virtualizar controladores de domínio do Windows Server Active Directory?
Implementar Windows Server Active Directory os controladores de domínio em máquinas virtuais do Azure está sujeita às diretrizes mesmas como DC no local a executar numa máquina virtual. Executar controladores de domínio virtualizados é uma prática de segurança, desde que as diretrizes para a cópia de segurança e restaurar os controladores de domínio são seguidas. Para obter mais informações sobre as restrições e diretrizes para a execução de controladores de domínio virtualizados, consulte [executar controladores de domínio no Hyper-V](https://technet.microsoft.com/library/dd363553).

Hipervisores fornecem ou trivialize tecnologias que podem causar problemas muitos sistemas distribuídos, incluindo o Windows Server Active Directory. Por exemplo, num servidor físico, pode clonar um disco ou utilizar métodos não suportados para reverter o estado de um servidor, incluindo a utilização SANs e assim sucessivamente, mas fazer num servidor físico é muito mais difícil de restaurar um instantâneo de máquina virtual num hipervisor. O Azure disponibiliza a funcionalidade que pode resultar na mesma condição indesejável. Por exemplo, não deve copiar ficheiros VHD de controladores de domínio em vez de efetuar cópias de segurança regulares, porque o restauro pode resultar numa situação semelhante para utilizar funcionalidades de restauro do instantâneo.

Essas reverte introduzir bolhas USN que podem levar a Estados permanentemente divergent entre os controladores de domínio. Que pode causar problemas, tais como:

* Objetos remanescentes
* Palavras-passe inconsistentes
* Valores de atributo inconsistente
* Correspondência de esquema se o mestre de esquema é revertido

Para obter mais informações sobre como os controladores de domínio são afetados, consulte [USN e reversão de USN](https://technet.microsoft.com/library/virtual_active_directory_domain_controller_virtualization_hyperv.aspx#usn_and_usn_rollback).

Começando com o Windows Server 2012, [proteções adicionais incorporadas AD DS](https://technet.microsoft.com/library/hh831734.aspx). Estas salvaguardas de ajudam a proteger os controladores de domínio virtualizados em relação a problemas acima mencionados, desde que a plataforma subjacente do hipervisor suporta VM-GenerationID. Azure suporta VM-GenerationID, o que significa que os controladores de domínio que executam o Windows Server 2012 ou posterior no Azure máquinas virtuais têm as proteções adicionais.

> [!NOTE]
> Deve encerrar e reiniciar uma VM que executa a função de controlador de domínio no Azure no sistema operativo convidado em vez de utilizar o **Encerrar** opção na porta do Azure. Hoje em dia, utilizando o portal para encerrar uma VM faz com que a VM ser desalocada. Uma VM desalocada tem a vantagem de não incorrer em custos, mas também redefine o VM-Generation ID, que é desejável para um controlador de domínio. Quando o VM-Generation ID é reposto, o invocationID da base de dados do AD DS também é reposto, o conjunto de RID é rejeitado e SYSVOL está marcado como não autoritativo. Para obter mais informações, consulte [introdução à virtualização de serviços de domínio do Active Directory (AD DS)](https://technet.microsoft.com/library/hh831734.aspx) e [virtualizar o DFSR com segurança](http://blogs.technet.com/b/filecab/archive/2013/04/05/safely-virtualizing-dfsr.aspx).
> 
> 

## <a name="why-deploy-windows-server-ad-ds-on-azure-virtual-machines"></a>Por que motivo implementar o Windows Server AD DS em máquinas de virtuais do Azure?
Muitos cenários de implementação do Windows Server AD DS são ideais para a implementação que as VMs no Azure. Por exemplo, suponha que tem uma empresa na Europa que tem de autenticar os utilizadores numa localização remota na Ásia. A empresa não implementou anteriormente Windows Server Active Directory DCs na Ásia devido ao custo para implementar conhecimentos-las e limitado para gerir a pós-implementação de servidores. Como resultado, os pedidos de autenticação de Ásia são servidos pela DCs na Europa com resultados inferior ao ideal. Neste caso, pode implementar um controlador de domínio numa VM que especificou tem de ser executado dentro do datacenter do Azure na Ásia. Anexar essa DC a uma rede virtual do Azure que está ligada diretamente à localização remota melhorará o desempenho de autenticação.

Azure é também adequado como um substitute para sites de (DR) de recuperação de desastres; caso contrário dispendiosos. O é relativamente baixo custo de alojar um pequeno número de controladores de domínio e uma única rede virtual no Azure representa uma boa alternativa.

Por fim, poderá implementar uma aplicação de rede no Azure, como o SharePoint, o que exige o Windows Server Active Directory, mas não tem nenhum dependência em rede no local ou o Windows Server Active Directory empresarial. Neste caso, implementar uma floresta isolada no Azure para satisfazer o SharePoint requisitos do servidor é o ideal. Novamente, a implementação de aplicações de rede que necessitam de conectividade à rede no local e o Active Directory empresarial também é suportada.

> [!NOTE]
> Uma vez que fornece uma ligação de camada 3, o componente VPN que fornece conectividade entre uma Azure virtual network e uma rede no local também pode ativar servidores membro que são executados no local para tirar partido dos DCs que são executados como máquinas virtuais do Azure no virtual do Azure rede. Mas se a VPN estiver disponível, a comunicação entre computadores no local e os controladores de domínio baseados no Azure não irão funcionar, resultando num autenticação e vários outros erros.  
> 
> 

## <a name="contrasts-between-deploying-windows-server-active-directory-domain-controllers-on-azure-virtual-machines-versus-on-premises"></a>Difere entre implementar controladores de domínio do Active Directory do Windows Server em Azure Virtual Machines versus no local
* Para qualquer cenário de implementação do Windows Server Active Directory que inclui mais de uma única VM, é necessário utilizar uma Azure virtual network consistência do endereço IP. Tenha em atenção que este guia pressupõe que os DC estão em execução numa rede virtual do Azure.
* Tal como acontece com os controladores de domínio no local, são recomendados endereços IP estáticos. Um endereço IP estático só pode ser configurado utilizando o Azure PowerShell. Consulte [estático um endereço IP para VMs](http://azure.microsoft.com/blog/static-internal-ip-address-for-virtual-machines/) para obter mais detalhes. Se tiver a monitorização de sistemas ou outras soluções que verifique a existência de configuração do endereço IP estático no sistema operativo convidado, pode atribuir o mesmo endereço IP estático para as propriedades do adaptador de rede da VM. Mas tenha em atenção de que o adaptador de rede será eliminado se a VM sofre autorrecuperação do serviço ou é encerrada no portal do e tem o respetivo endereço desalocada. Nesse caso, o endereço IP estático do convidado terão de ser reposto.
* Implementar as VMs numa rede virtual não implica (ou exigir) conectividade a uma rede no local; a rede virtual permite simplesmente essa possibilidade. Tem de criar uma rede virtual para privada comunicação entre o Azure e a sua rede no local. Tem de implementar um ponto final de VPN na rede no local. A VPN é aberta a partir do Azure para a rede no local. Para obter mais informações, consulte [descrição geral de rede Virtual](../virtual-network/virtual-networks-overview.md) e [configurar uma VPN de Site a Site no Portal do Azure](../vpn-gateway/vpn-gateway-site-to-site-create.md).

> [!NOTE]
> Uma opção para [criar uma VPN ponto a site](../vpn-gateway/vpn-gateway-point-to-site-create.md) está disponível para ligar os computadores baseados em Windows individuais diretamente a uma rede virtual do Azure.
> 
> 

* Independentemente de se criar virtual de rede ou não, Azure cobra o tráfego de saída, mas não de entrada. Várias opções de design do Windows Server Active Directory podem afetar quanto tráfego de saída é gerado por uma implementação. Por exemplo, implementação de um controlador de domínio só de leitura (RODC) limita o tráfego de saída porque não é replicado saída. Mas a decisão de implementar um RODC tem de ser weighed contra a necessidade de executar operações de escrita contra o DC e o [compatibilidade](https://technet.microsoft.com/library/cc755190) que aplicações e serviços no site têm com RODCs. Para obter mais informações sobre os encargos de tráfego, consulte [do Azure de preços em rapidamente](http://azure.microsoft.com/pricing/).
* Enquanto tiver concluído controla que servidor recursos a utilizar para VMs no local, por exemplo, RAM, tamanho do disco, e assim sucessivamente, no Azure tem de selecionar numa lista de tamanhos de servidor pré-configurada. Para um controlador de domínio, um disco de dados é necessária para além do disco do sistema operativo para armazenar a base de dados do Windows Server Active Directory.

## <a name="can-you-deploy-windows-server-ad-fs-on-azure-virtual-machines"></a>Pode implementar o Windows Server AD FS em máquinas virtuais do Azure?
Sim, pode implementar o Windows Server AD FS em máquinas virtuais do Azure e o [melhores práticas para implementação do AD FS](https://technet.microsoft.com/library/dn151324.aspx) no local aplicam-se igualmente a implementação do AD FS no Azure. Mas algumas das melhores práticas, tais como balanceamento de carga e tecnologias se para além dos que do AD FS oferece próprio necessitam de elevada disponibilidade. Tem de ser fornecidos pela infraestrutura subjacente. Vamos rever algumas dessas melhores práticas e ver como pode ser conseguidos através da utilização de VMs do Azure e uma rede virtual do Azure.

1. **Nunca expor os servidores de serviço (STS) do token de segurança diretamente à Internet.**
   
    Isto é importante porque o STS emite tokens de segurança. Como resultado, os servidores de STS, tais como servidores do AD FS devem ser tratados com o mesmo nível de proteção como um controlador de domínio. Se um STS for comprometida, os utilizadores mal intencionados têm a capacidade para emitir tokens de acesso, potencialmente, que contêm afirmações à sua escolha para aplicações de entidades confiadoras e outros servidores de STS confiadoras organizações.
2. **Implemente controladores de domínio do Active Directory para todos os domínios de utilizador na mesma rede que os servidores do AD FS.**
   
    Servidores do AD FS utilizam serviços de domínio do Active Directory para autenticar os utilizadores. Recomenda-se para implementar controladores de domínio na mesma rede que os servidores do AD FS. Isto fornece continuidade do negócio, no caso da ligação entre a rede do Azure e a sua rede no local está quebrada e permite a menor latência e um melhor desempenho para inícios de sessão.
3. **Implemente vários nós do AD FS para elevada disponibilidade e o balanceamento de carga.**
   
    Na maioria dos casos, a falha de uma aplicação que permite que o AD FS é inaceitável porque o missão as aplicações que necessitam de tokens de segurança são muitas vezes críticos. Como resultado, e porque do AD FS agora reside no caminho de crítico para aceder a aplicações fundamentais, o serviço AD FS tem de ser elevado através de vários proxies AD FS e servidores do AD FS. Para alcançar a distribuição de pedidos, balanceadores de carga, normalmente, são implementados à frente de Proxies do AD FS e os servidores do AD FS.
4. **Implemente um ou mais nós de Proxy de aplicações Web para acesso à internet.**
   
    Quando os utilizadores precisam de aceder a aplicações protegidas pelo serviço do AD FS, o serviço AD FS tem de estar disponível na internet. Isto é conseguido ao implementar o serviço de Proxy de aplicações Web. É vivamente recomendado para implementar mais do que um nó para fins de elevada disponibilidade e o balanceamento de carga.
5. **Restringir o acesso de nós de Proxy de aplicações Web a recursos da rede interna.**
   
    Para permitir que os utilizadores externos do AD FS de acesso a partir da internet, terá de implementar nós de Proxy de aplicações Web (ou Proxy do AD FS em versões anteriores do Windows Server). Os nós de proxy de aplicação Web são expostos diretamente à Internet. Não são necessárias para ser associado a um domínio e precisam apenas de aceder aos servidores do AD FS através das portas TCP 443 e 80. É vivamente recomendado que a comunicação para todos os outros computadores (especialmente controladores de domínio) é bloqueada.
   
    Isto é normalmente conseguido no local através de uma rede de Perímetro. As firewalls utilizam um modo de lista branca de funcionamento para restringir o tráfego do DMZ para a rede no local (ou seja, apenas o tráfego de endereços IP especificados e através de portas especificadas é permitido e todos os outros tráfego está bloqueado).

O diagrama seguinte mostra um tradicional implementação do AD FS no local.

![Diagrama de uma implementação de serviços de Federação do Active Directory tradicional no local](media/active-directory-deploying-ws-ad-guidelines/ADFS_onprem.png)

No entanto, porque o Azure não fornece nativo, capacidade de firewall completo, outras opções tem de ser utilizadas para restringir o tráfego. A tabela seguinte mostra cada opção e os respetivos vantagens e desvantagens.

| Opção | Partido | Desvantagens |
| --- | --- | --- |
| [ACLs de rede do Azure](../virtual-network/virtual-networks-acl.md) |Configuração inicial menos dispendiosa e mais simples |Configuração de ACL de rede adicionais necessários se VMs novas são adicionadas à implementação |
| [Firewall de barracuda NG](https://www.barracuda.com/products/ngfirewall) |Modo de lista branca da operação e não necessita de nenhuma configuração de ACL de rede |Aumento do custo e a complexidade da configuração inicial |

Neste caso, os passos de alto nível para implementar o AD FS são os seguintes:

1. Criar uma rede virtual com uma conectividade entre instalações, através de uma VPN ou [ExpressRoute](http://azure.microsoft.com/services/expressroute/).
2. Implemente controladores de domínio na rede virtual. Este passo é opcional mas recomendado.
3. Implemente servidores de AD FS associados a um domínio na rede virtual.
4. Criar um [conjunto com balanceamento de carga interno](http://azure.microsoft.com/blog/internal-load-balancing/) que inclui os servidores do AD FS e utiliza um novo endereço IP privado dentro da rede virtual (um endereço IP dinâmico).
   
   1. Atualize o DNS para criar o FQDN para apontar para o endereço IP privado (dinâmico) do conjunto com balanceamento de carga interno.
5. Crie um serviço em nuvem (ou uma rede virtual independente) para os nós de Proxy de aplicações Web.
6. Implementar os nós de Proxy de aplicações Web no serviço em nuvem ou de rede virtual
   
   1. Crie um conjunto com balanceamento de carga externo, que inclui os nós de Proxy de aplicações Web.
   2. Atualize o nome DNS externo (FQDN) para apontar para o nuvem serviço endereço IP público (o endereço IP virtual).
   3. Configure proxies do AD FS para utilizar o FQDN que corresponde ao conjunto com balanceamento de carga interno para os servidores do AD FS.
   4. Atualize baseada em afirmações web sites para utilizar o FQDN externo para o seu fornecedor de afirmações.
7. Restringir o acesso entre o Proxy de aplicações Web para qualquer computador na rede virtual do AD FS.

Para restringir o tráfego, o conjunto com balanceamento de carga para o Balanceador de carga interno do Azure tem de ser configurado para o tráfego apenas para as portas TCP 80 e 443 e todo o tráfego restante para o endereço IP dinâmico interno do conjunto com balanceamento de carga é ignorado.

![Diagrama de ACL de rede do ADFS com TCP 443 + 80 permitido.](media/active-directory-deploying-ws-ad-guidelines/ADFS_ACLs.png)

O tráfego para servidores do AD FS poderia apenas pelas seguintes origens:

* O Balanceador de carga interno do Azure.
* O endereço IP de um administrador na rede no local.

> [!WARNING]
> A estrutura tem de impedir que nós de Proxy de aplicações Web de chegar aos quaisquer localizações na rede no local ou de outras VMs na rede virtual do Azure. Que pode ser feito ao configurar regras de firewall na aplicação no local para ligações Expressroute ou o dispositivo VPN para ligações de VPN de site para site.
> 
> 

Uma desvantagem para esta opção é a necessidade de configurar a ACL de rede para vários dispositivos, incluindo o Balanceador de carga interno, servidores do AD FS e outros servidores que são adicionados à rede virtual. Se qualquer dispositivo é adicionado à implementação sem configurar ACLs de rede para restringir o tráfego para o mesmo, a implementação completa pode estar em risco. Se alguma vez alterar os endereços IP de nós de Proxy de aplicações Web, tem de ser reposta a ACL de rede (que significa que o proxies deve ser configurado para utilizar [endereços IP dinâmicos estáticos](http://azure.microsoft.com/blog/static-internal-ip-address-for-virtual-machines/)).

![AD FS no Azure com a rede ACLS.](media/active-directory-deploying-ws-ad-guidelines/ADFS_Azure.png)

Outra opção consiste em utilizar o [Barracuda NG Firewall](https://www.barracuda.com/products/ngfirewall) dispositivo para controlar o tráfego entre servidores de proxy do AD FS e os servidores AD FS. Esta opção está em conformidade com as melhores práticas de segurança e de elevada disponibilidade e requer menos administração após a configuração inicial, uma vez que a aplicação de Firewall de NG Barracuda fornece um modo de lista branca de administração de firewall e pode ser instalado diretamente numa rede virtual do Azure. Que elimina a necessidade de configurar ACLs de rede sempre que é adicionado um novo servidor para a implementação. Mas, esta opção adiciona a complexidade da implementação inicial e o custo.

Neste caso, as duas redes virtuais são implementadas em vez de um. Receberá um telefonema nosso-los VNet1 e VNet2. VNet1 contém os proxies e VNet2 contém os STSs e a ligação de rede à rede empresarial. VNet1, por conseguinte, é fisicamente (se virtualmente) isolada de VNet2 e, por sua vez, da rede empresarial. VNet1, em seguida, está ligado para a VNet2 utilizando uma tecnologia de túnel especial conhecida como transporte independentes rede arquitetura (TINA). O túnel TINA está ligado a cada uma das redes virtuais através de uma firewall Barracuda NG — um Barracuda em cada uma das redes virtuais.  Para elevada disponibilidade, recomenda-se que implemente dois Barracudas em cada rede virtual; uma ativa, o outro passivo. Oferecem extremamente firewalling avançadas que permitem-nos imitar o funcionamento do DMZ tradicional no local no Azure.

![AD FS no Azure com firewall.](media/active-directory-deploying-ws-ad-guidelines/ADFS_Azure_firewall.png)

Para obter mais informações, consulte [do AD FS: expandir uma aplicação de front-end com suporte para afirmações no local para a Internet](#BKMK_CloudOnlyFed).

### <a name="an-alternative-to-ad-fs-deployment-if-the-goal-is-office-365-sso-alone"></a>Uma alternativa à implementação do AD FS, se o objetivo é o Office 365 SSO individualmente
Há outra alternativa para implementar o AD Folders FS se o seu objetivo é apenas para ativar o início de sessão para o Office 365. Nesse caso, basta pode implementar o DirSync com a palavra-passe sincronização no local e alcançar o resultado final mesmo com a complexidade da implementação mínima porque esta abordagem necessitam de AD FS ou o Azure.

A tabela seguinte compara como os processos de início de sessão funcionam com e sem a implementar o AD FS.

| Office 365 único início de sessão através do AD FS e o DirSync | Office 365 mesmo início de sessão utilizando o DirSync + sincronização de palavra-passe |
| --- | --- |
| 1. O utilizador inicia sessão a uma rede empresarial e é autenticado para o Windows Server Active Directory. |1. O utilizador inicia sessão a uma rede empresarial e é autenticado para o Windows Server Active Directory. |
| 2. O utilizador tentar aceder ao Office 365 (sou @contoso.com). |2. O utilizador tentar aceder ao Office 365 (sou @contoso.com). |
| 3. Office 365 redireciona o utilizador para o Azure AD. |3. Office 365 redireciona o utilizador para o Azure AD. |
| 4. Uma vez que o Azure AD não consegue autenticar o utilizador e compreende que há uma relação de confiança com o AD FS no local, o utilizador será redirecionado para o AD FS. |4. Azure AD não pode aceitar permissões Kerberos diretamente e pelo que solicita que o utilizador introduza as credenciais não existe nenhuma relação de confiança. |
| 5. O utilizador envia um pedido de suporte do Kerberos para o AD FS STS. |5. O utilizador introduz a mesma palavra-passe no local e do Azure AD valida-los contra o nome de utilizador e palavra-passe que foi sincronizado pelo DirSync. |
| 6. AD FS transforma a permissão de Kerberos para as formato/afirmações token necessárias e redireciona o utilizador para o Azure AD. |6. Azure AD redireciona o utilizador para Office 365. |
| 7. O utilizador autentica para o Azure AD (transformação outra ocorre). |7. O utilizador pode iniciar sessão no Office 365 e o OWA utilizando o token do Azure AD. |
| 8. Azure AD redireciona o utilizador para Office 365. | |
| 9. O utilizador tem sessão iniciada automaticamente Office 365. | |

No Office 365 com o DirSync com cenário de sincronização de palavra-passe (nenhum AD FS), o início de sessão único, é substituído por "mesmo início de sessão" onde "mesmo" simplesmente significa que os utilizadores tem de reintroduzir as suas credenciais no local mesmo ao aceder ao Office 365. Tenha em atenção que estes dados podem ser memorizados pelo browser do utilizador para ajudar a reduzir os pedidos subsequentes.

### <a name="additional-food-for-thought"></a>Profundamente de for prato adicional
* Se implementar um proxy do AD FS numa máquina virtual do Azure, é necessária conectividade com os servidores do AD FS. Que se encontrem no local, é recomendado que tiram partido a conectividade VPN de site para site fornecida através da rede virtual para permitir que os nós de Proxy de aplicações Web comunicar com os seus servidores do AD FS.
* Se implementar um servidor do AD FS numa máquina virtual do Azure, é necessária e também pode exigir o ExpressRoute ou de uma ligação de VPN de site a site entre conectividade para controladores de domínio do Active Directory do Windows Server, arquivos de atributos e bases de dados de configuração a Azure virtual network e a rede no local.
* Os encargos são aplicados a todo o tráfego de máquinas virtuais do Azure (tráfego de saída). Se o custo é o fator despertar, é recomendado para implementar os nós de Proxy de aplicações Web no Azure, mantendo o AD FS servidores no local. Se os servidores AD FS são implementados em máquinas virtuais do Azure, bem como, serão incorrer tarifas de custos adicionais para autenticar utilizadores no local. Tráfego de saída implica um custo independentemente se pretende ou não está a atravessar o ExpressRoute ou a ligação do VPN site a site.
* Se optar por utilizar as capacidades de elevada disponibilidade dos servidores AD FS de balanceamento de carga do servidor nativo do Azure, tenha em atenção que o balanceamento de carga fornece sondas que são utilizadas para determinar o estado de funcionamento das máquinas virtuais no âmbito do serviço de nuvem. No caso de máquinas virtuais do Azure (por oposição a funções web ou trabalho), uma sonda personalizada tem de ser utilizada uma vez que o agente que responde às sondas predefinido não está presente em máquinas virtuais do Azure. De simplicidade, poderá utilizar uma sonda TCP personalizada — Isto requer apenas que ser estabelecida com êxito uma ligação de TCP (um segmento de TCP SIN enviados e respondeu com um segmento de TCP SIN ACK) para determinar o estado de funcionamento da máquina virtual. Pode configurar a sonda personalizada para utilizar qualquer porta TCP para o qual as máquinas virtuais são escuta ativa.

> [!NOTE]
> As máquinas que necessitam para expor o mesmo conjunto de portas diretamente à Internet (por exemplo, a porta 80 e 443) não é possível partilhar o mesmo serviço de nuvem. Recomenda-se, por conseguinte, a criação de uma nuvem dedicada serviço para os servidores do Windows Server AD FS para evitar potenciais sobreposições entre os requisitos de porta para uma aplicação e do Windows Server AD FS.
> 
> 

## <a name="deployment-scenarios"></a>Cenários de implementação
A secção seguinte descreve os cenários de implementação commonplace para desenhar a atenção para considerações importantes sobre o que deve ser colocada na conta. Cada cenário tem ligações para obter mais detalhes sobre as decisões e fatores a considerar.

1. [AD DS: Implementar uma aplicação compatível com o AD DS com sem necessidade de conectividade de rede empresarial](#BKMK_CloudOnly)
   
    Por exemplo, um serviço do SharePoint para a Internet é implementado numa máquina virtual do Azure. A aplicação não tem dependências nos recursos de rede empresarial. A aplicação necessita do Windows Server AD DS mas não necessita que o AD DS empresarial do Windows Server.
2. [AD FS: Expandir uma aplicação de front-end com suporte para afirmações no local para a Internet](#BKMK_CloudOnlyFed)
   
    Por exemplo, uma aplicação com suporte para afirmações, que foi implementado com êxito no local e utilizadas pelos utilizadores empresariais tem de se tornar acessível a partir da Internet. A aplicação tem de ser acedido diretamente através da Internet por parceiros de negócios com as suas próprias identidades empresariais e pelos utilizadores empresariais existentes.
3. [AD DS: Implementar uma aplicação do Windows Server AD DS com suporte para que necessita de conectividade à rede empresarial](#BKMK_HybridExt)
   
    Por exemplo, uma aplicação com suporte para o LDAP que suporta a autenticação integrada do Windows e utiliza o Windows Server AD DS como um repositório de dados de configuração e o perfil de utilizador é implementada numa máquina virtual do Azure. É necessário para a aplicação para tirar partido existente empresarial do Windows Server AD DS e forneça o início de sessão único. A aplicação não é compatível com afirmações.

### <a name="BKMK_CloudOnly"></a>1. AD DS: Implementar uma aplicação compatível com o AD DS com sem necessidade de conectividade de rede empresarial
![Implementação de AD DS apenas na nuvem](media/active-directory-deploying-ws-ad-guidelines/ADDS_cloud.png)
**figura 1**

#### <a name="description"></a>Descrição
SharePoint está implementado numa máquina virtual do Azure e a aplicação não tem dependências nos recursos de rede empresarial. A aplicação necessita do Windows Server AD DS, mas *não* requerem o DS do Windows Server AD empresarial. Não existem Kerberos ou fidedignidades federadas são necessárias porque os utilizadores Self-aprovisionados através da aplicação para o domínio do Windows Server AD DS também está alojado na nuvem em máquinas virtuais do Azure.

#### <a name="scenario-considerations-and-how-technology-areas-apply-to-the-scenario"></a>Considerações de cenário e como aplicam a áreas de tecnologia para o cenário
* [Topologia de rede](#BKMK_NetworkTopology): criar uma Azure virtual network sem conectividade em vários locais (também conhecido como conetividade site a site).
* [Configuração de implementação de DC](#BKMK_DeploymentConfig): implementar um novo controlador de domínio numa floresta nova, domínio único, Windows Server Active Directory. Isto deve ser implementado juntamente com o servidor de DNS do Windows.
* [Topologia de site do Windows Server Active Directory](#BKMK_ADSiteTopology): utilizar o site do Windows Server Active Directory predefinido (todos os computadores terão no Default-First-Site-Name).
* [Endereçamento IP e DNS](#BKMK_IPAddressDNS):
  
  * Defina um endereço IP estático para o DC utilizando o cmdlet Set-AzureStaticVNetIP Azure PowerShell.
  * Instalar e configurar o DNS do Windows Server no domínio controlador (es) no Azure.
  * Configure as propriedades de rede virtual com o nome e endereço IP da VM que aloja as funções de servidor DC e DNS.
* [Catálogo global](#BKMK_GC): O primeiro DC na floresta tem de ser um servidor de catálogo global. Os controladores de domínio adicionais também devem ser configurados como GCs porque numa floresta único domínio, o catálogo global não necessita de qualquer trabalho adicional de DC.
* [A colocação da base de dados do Windows Server AD DS e SYSVOL](#BKMK_PlaceDB): adicionar um disco de dados para os controladores de domínio em execução como VMs do Azure para armazenar a base de dados do Windows Server Active Directory, os registos e SYSVOL.
* [Cópia de segurança e restaurar](#BKMK_BUR): determinar onde pretende armazenar cópias de segurança de estado de sistema. Se necessário, adicione outro disco de dados para a VM de DC para armazenar as cópias de segurança.

### <a name="BKMK_CloudOnlyFed"></a>2 AD FS: expandir uma aplicação de front-end com suporte para afirmações no local para a Internet
![Federação com a conectividade entre instalações,](media/active-directory-deploying-ws-ad-guidelines/Federation_xprem.png)
**figura 2**

#### <a name="description"></a>Descrição
Necessita de uma aplicação com suporte para afirmações, que foi implementado com êxito no local e utilizadas pelos utilizadores empresariais fique acessível diretamente a partir da Internet. A aplicação funciona como front-end web a uma base de dados do SQL Server em que armazena os dados. Os servidores do SQL Server utilizados pela aplicação também estão localizados na rede empresarial. Um balanceador de carga e dois STSs de FS do Windows Server AD tem sido implementado no local para fornecer acesso aos utilizadores empresariais. A aplicação agora tem-se para além disso, ser acedido diretamente através da Internet por parceiros de negócios com as suas próprias identidades empresariais e pelos utilizadores empresariais existentes.

Num esforço para simplificar e satisfazer as necessidades de implementação e configuração deste novo requisito, é decidido que dois adicionais frontends e web dois servidores de proxy do Windows Server AD FS ser instalado em máquinas virtuais do Azure. Todos os quatro VMs ficará expostas diretamente à Internet e receberá conectividade à rede no local, utilizar a capacidade de VPN de site para site da rede Virtual do Azure.

#### <a name="scenario-considerations-and-how-technology-areas-apply-to-the-scenario"></a>Considerações de cenário e como aplicam a áreas de tecnologia para o cenário
* [Topologia de rede](#BKMK_NetworkTopology): criar uma Azure virtual network e [configurar a conectividade entre instalações,](../vpn-gateway/vpn-gateway-site-to-site-create.md).
  
  > [!NOTE]
  > Para cada um dos certificados do Windows Server AD FS, certifique-se de que o URL definido no modelo de certificado e os certificados resultantes pode ser acedido por instâncias de FS do Windows Server AD em execução no Azure. Esta operação pode requerer a conectividade entre instalações, para partes da sua infraestrutura PKI. Para o exemplo se o ponto final de CRL é baseado em LDAP e está lojado exclusivamente no local, em seguida, em vários locais terão conectividade. Se isto não é desejável, poderá ser necessário utilizar certificados emitidos por uma AC cuja CRL está acessível através da Internet.
  > 
  > 
* [Configuração de serviços cloud](#BKMK_CloudSvcConfig): Certifique-se de que tem dois serviços em nuvem numa ordem fornecem dois com balanceamento de carga endereços IP virtuais. Endereço IP virtual o serviço em nuvem primeiro será direcionado para duas do Windows Server AD FS proxy VMs as portas 80 e 443. O proxy do Windows Server AD FS VMs será configurado para apontar para o endereço IP do Balanceador de carga no local que fronts STSs de FS do Windows Server AD. Endereço IP virtual o serviço em nuvem segundo será direcionado para as duas VMs em execução o front-end da web novamente as portas 80 e 443. Configure uma sonda personalizada para garantir que o Balanceador de carga apenas direciona o tráfego para funcionar do Windows Server AD FS proxy e web front-end VMs.
* [Configuração do servidor de Federação](#BKMK_FedSrvConfig): configurar o Windows Server AD FS como um servidor de Federação (STS) para gerar tokens de segurança para a floresta do Windows Server Active Directory criados na nuvem. Definir relações de confiança do fornecedor de afirmações com os parceiros diferentes que pretender aceitar as identidades de Federação e configure as relações de confiança de entidade confiadora terceiros com as diferentes aplicações que pretende gerar tokens para.
  
    Na maioria dos cenários, os servidores de proxy do Windows Server AD FS são implementadas numa capacidade de acesso à Internet por motivos de segurança enquanto os seus homólogos de Federação do Windows Server AD FS permanecerem isolados do Conectividade Internet direta. Independentemente do seu cenário de implementação, tem de configurar o seu serviço em nuvem com um endereço IP virtual que irá fornecer um endereço IP e porta que é capaz de balanceamento de carga entre as duas instâncias do Windows Server AD FS STS ou instâncias de proxy publicamente expostos.
* [Configuração de elevada disponibilidade do Windows Server AD FS](#BKMK_ADFSHighAvail): é recomendável implementar um farm do Windows Server AD FS com, pelo menos, dois servidores de ativação pós-falha e balanceamento de carga. Poderá pretender considerar utilizar a base de dados interna do Windows (WID) para dados de configuração do Windows Server AD FS e utilizar a capacidade de balanceamento de carga interno do Azure para distribuir pedidos recebidos por todos os servidores no farm.

Para obter mais informações, consulte o [guia de implementação do AD DS](https://technet.microsoft.com/library/cc753963).

### <a name="BKMK_HybridExt"></a>3. AD DS: Implementar uma aplicação do Windows Server AD DS com suporte para que necessita de conectividade à rede empresarial
![Implementação de AD DS em vários locais](media/active-directory-deploying-ws-ad-guidelines/ADDS_xprem.png)
**figura 3**

#### <a name="description"></a>Descrição
Uma aplicação com suporte para o LDAP é implementada numa máquina virtual do Azure. Este suporta a autenticação integrada do Windows e utiliza o Windows Server AD DS como um repositório para dados de perfil de configuração e o utilizador. O objetivo é para a aplicação para tirar partido o empresarial existente de Windows Server AD DS e forneça o início de sessão único. A aplicação não é compatível com afirmações. Os utilizadores também necessitam de aceder à aplicação diretamente a partir da Internet. Para otimizar o desempenho e custo, é decidido que os dois controladores de domínio adicional que fazem parte do domínio empresarial ser implementados em conjunto com a aplicação no Azure.

#### <a name="scenario-considerations-and-how-technology-areas-apply-to-the-scenario"></a>Considerações de cenário e como aplicam a áreas de tecnologia para o cenário
* [Topologia de rede](#BKMK_NetworkTopology): criar uma rede virtual do Azure com [conectividade em vários locais](../vpn-gateway/vpn-gateway-site-to-site-create.md).
* [Método de instalação](#BKMK_InstallMethod): implementar a réplica DCs de domínio do Windows Server Active Directory empresarial. Para um DC de réplica, pode instalar o Windows Server AD DS na VM e, opcionalmente, utilize a funcionalidade de instalar do suporte de dados (IFM) para reduzir a quantidade de dados tem de ser replicadas para o novo controlador de domínio durante a instalação. Para um tutorial, consulte [instalar um controlador de domínio do Active Directory de réplica no Azure](active-directory-install-replica-active-directory-domain-controller.md). Mesmo se utilizar IFM, poderá ser mais eficiente para compilar a virtual DC no local e mover o disco rígido Virtual completo (VHD) para a nuvem em vez de replicar o Windows Server AD DS durante a instalação. Para segurança, recomenda-se que elimina o VHD da rede no local, assim que foi copiado para o Azure.
* [Topologia de site do Windows Server Active Directory](#BKMK_ADSiteTopology): criar um novo site do Azure nos serviços e locais do Active Directory. Crie um objeto de sub-rede do Windows Server Active Directory para representar a Azure virtual network e adicionar a sub-rede para o site. Crie uma nova ligação entre locais que inclui o novo site do Azure e o site no qual a ponto final de VPN de rede virtual do Azure está localizada para controlar e otimizar o tráfego do Windows Server Active Directory para e do Azure.
* [Endereçamento IP e DNS](#BKMK_IPAddressDNS):
  
  * Defina um endereço IP estático para o DC utilizando o cmdlet Set-AzureStaticVNetIP Azure PowerShell.
  * Instalar e configurar o DNS do Windows Server no domínio controlador (es) no Azure.
  * Configure as propriedades de rede virtual com o nome e endereço IP da VM que aloja as funções de servidor DC e DNS.
* [DCs geo-distribuição](#BKMK_DistributedDCs): configurar as redes virtuais adicionais conforme necessário. Se a sua topologia de site do Active Directory requer DCs em localizações geográficas que correspondem aos diferentes regiões do Azure que pretende criar sites do Active Directory em conformidade.
* [Os controladores de domínio só de leitura](#BKMK_RODC): poderá implementar um RODC no site do Azure, consoante os requisitos para executar a escrever operações contra o DC e a compatibilidade das aplicações e serviços no site com RODCs. Para obter mais informações sobre a compatibilidade de aplicações, consulte o [guia de compatibilidade da aplicação de controladores de domínio só de leitura](https://technet.microsoft.com/library/cc755190).
* [Catálogo global](#BKMK_GC): GCs são necessárias para pedidos de início de sessão do serviço em multidomain florestas. Se o utilizador não implementa um GC no site do Azure, será cobrado os custos de tráfego de saída como pedidos de autenticação provocar consultas GCs em outros sites. Para minimizar o tráfego de que, pode ativar o caching de adesão ao grupo universal para o site nos serviços e locais do Active Directory do Azure.
  
    Se implementar um GC, configure ligações de site e os custos de ligações de site para que não seja preferencial como um DC de origem por outros GCs que necessita de replicar as partições de domínio parcial mesmo GC no site do Azure.
* [A colocação da base de dados do Windows Server AD DS e SYSVOL](#BKMK_PlaceDB): adicionar um disco de dados para os controladores de domínio em execução em VMs do Azure para armazenar a base de dados do Windows Server Active Directory, os registos e SYSVOL.
* [Cópia de segurança e restaurar](#BKMK_BUR): determinar onde pretende armazenar cópias de segurança de estado de sistema. Se necessário, adicione outro disco de dados para a VM de DC para armazenar as cópias de segurança.

## <a name="deployment-decisions-and-factors"></a>Decisões de implementação e fatores
Esta tabela resume as áreas de tecnologia do Windows Server Active Directory que são afetadas os cenários anteriores e as decisões correspondentes a ter em consideração com ligações para mais detalhes abaixo. Algumas áreas de tecnologia podem não ser aplicáveis a todos os cenários de implementação, e algumas áreas de tecnologia podem ser mais vitais para um cenário de implementação do que outras áreas de tecnologia.

Por exemplo, se implementar um DC de réplica numa rede virtual e a floresta tem apenas um único domínio, em seguida, escolha implementar um servidor de catálogo global, caso em que não será possível crítica para o cenário de implementação porque não criará qualquer replicação adicional requisitos. Por outro lado, se a floresta tem vários domínios, em seguida, a decisão de implementar um catálogo global numa rede virtual pode afetar largura de banda disponível, desempenho, autenticação, pesquisas de diretório e assim sucessivamente.

| Área de tecnologia do Windows Server Active Directory | Decisões | Fatores |
| --- | --- | --- |
| [Topologia de rede](#BKMK_NetworkTopology) |Criar uma rede virtual? |<li>Requisitos para aceder aos recursos da rede empresarial</li> <li>Autenticação</li> <li>Gestão de contas</li> |
| [Configuração de implementação de DC](#BKMK_DeploymentConfig) |<li>Implementar uma floresta separada sem qualquer confianças?</li> <li>Implementar uma nova floresta com Federação?</li> <li>Implementar uma nova floresta com fidedignidade de floresta do Windows Server Active Directory ou Kerberos?</li> <li>Expandir floresta Corp implementando um DC de réplica?</li> <li>Expandir floresta Corp ao implementar um novo domínio subordinado ou árvore de domínio?</li> |<li>Segurança</li> <li>Conformidade</li> <li>Custo</li> <li>Resiliência e tolerância a falhas</li> <li>Compatibilidade aplicacional</li> |
| [Topologia de site do Windows Server Active Directory](#BKMK_ADSiteTopology) |Como pode configurar sub-redes, sites e ligações de site com a rede Virtual do Azure para otimizar o tráfego e minimizar os custos? |<li>Definições de sub-rede e o site</li> <li>Propriedades de ligação do site e a notificação de alteração</li> <li>Compressão de replicação</li> |
| [Endereçamento IP e DNS](#BKMK_IPAddressDNS) |Como configurar endereços IP e resolução de nomes? |<li>Utilize o cmdlet Set-AzureStaticVNetIP de utilização para atribuir um endereço IP estático</li> <li>Instalar o servidor de DNS do Windows Server e configurar as propriedades de rede virtual com o nome e endereço IP da VM que aloja as funções de servidor DNS e DC</li> |
| [DCs geo-distribuição](#BKMK_DistributedDCs) |Como se repliquem para os controladores de domínio em separado redes virtuais? |Se a sua topologia de site do Active Directory requer DCs em localizações geográficas que corresponde a diferentes regiões do Azure que pretende criar sites do Active Directory em conformidade. [Configurar a rede virtual para conectividade de rede virtual](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md) para replicar entre controladores de domínio em redes virtuais separados. |
| [Controladores de domínio só de leitura](#BKMK_RODC) |Utilizar os controladores de domínio só de leitura ou graváveis? |<li>Filtrar atributos HBI/PII</li> <li>Segredos do filtro</li> <li>Tráfego de saída de limite</li> |
| [Catálogo global](#BKMK_GC) |Instalar o catálogo global? |<li>Para a floresta de domínio único, fazer todas as GCs de DCs</li> <li>Para uma floresta multidomain, GCs são necessários para autenticação</li> |
| [Método de instalação](#BKMK_InstallMethod) |Como instalar o DC no Azure? |O: <li>Instalar o AD DS utilizando o Windows PowerShell ou Dcpromo</li> <li>Mova o VHD de um DC virtual no local</li> |
| [Colocação da base de dados do Windows Server AD DS e SYSVOL](#BKMK_PlaceDB) |Onde pretende armazenar o Windows Server AD DS base de dados, os registos e SYSVOL? |Altere os valores predefinidos de Dcpromo.exe. Estes ficheiros críticos do Active Directory *tem* ser colocadas em discos de dados do Azure em vez de discos de sistema operativo que implementam a colocação em cache de escrita. |
| [Cópia de Segurança e Restauro](#BKMK_BUR) |Como proteger e recuperar dados? |Criar cópias de segurança do Estado de sistema |
| [Configuração do servidor de Federação](#BKMK_FedSrvConfig) |<li>Implementar uma nova floresta com Federação na nuvem?</li> <li>Implementar o AD FS no local e expor um proxy na nuvem?</li> |<li>Segurança</li> <li>Conformidade</li> <li>Custo</li> <li>Acesso a aplicações de parceiros de negócios</li> |
| [Configuração de serviços cloud](#BKMK_CloudSvcConfig) |Um serviço em nuvem implicitamente é implementado na primeira vez que criar uma máquina virtual. É necessário implementar serviços na nuvem adicional? |<li>Uma VM ou VMs requerem exposição direta à Internet?</li> <li> O serviço precisa balanceamento de carga?</li> |
| [Requisitos do servidor de Federação para público e privado (IP dinâmico vs virtual IP) de endereçamento de IP](#BKMK_FedReqVIPDIP) |<li>A instância do Windows Server AD FS tem de ser aceder diretamente a partir da Internet?</li> <li>A aplicação que está a ser implementada na nuvem requerem o seu próprio endereço IP para a Internet e a porta?</li> |Criar um serviço em nuvem para cada endereço IP virtual que é necessário para a implementação |
| [Configuração de elevada disponibilidade do Windows Server AD FS](#BKMK_ADFSHighAvail) |<li>Quantos nós no meu farm de servidores do Windows Server AD FS?</li> <li>Quantos nós para implementar o meu farm de proxies do Windows Server AD FS?</li> |Tolerância de resiliência e falhas |

### <a name="BKMK_NetworkTopology"></a>Topologia de rede
Para cumprir os requisitos de DNS do Windows Server AD DS e consistência de endereço IP, é necessário criar primeiro um [rede virtual do Azure](../virtual-network/virtual-networks-overview.md) e associe as máquinas virtuais. Durante a respetiva criação, tem de decidir se pretende optar por alargar a conectividade à sua rede empresarial no local, que liga Transparente máquinas virtuais do Azure para máquinas no local — isto é conseguido utilizando tecnologias tradicionais de VPN e requer que um ponto final de VPN ser expostos na margem da rede empresarial. Ou seja, a VPN é iniciada a partir do Azure à rede da empresa, não vice-versa-se.

Tenha em atenção que, quando expandir uma rede virtual à sua rede no local para além dos encargos padrão que se aplicam a cada VM são aplicáveis encargos adicionais. Mais concretamente, existem encargos de tempo de CPU do gateway de rede Virtual do Azure e o tráfego de saída gerados por cada VM que comunica com máquinas no local através da VPN. Para mais informações sobre os encargos de tráfego de rede, consulte [do Azure de preços em rapidamente](http://azure.microsoft.com/pricing/).

### <a name="BKMK_DeploymentConfig"></a>Configuração de implementação de DC
A forma de configurar o DC depende dos requisitos para o serviço que pretende executar no Azure. Por exemplo, poderá implementar uma nova floresta, isolada da sua própria floresta empresarial, para testar um prova de conceito, uma nova aplicação ou algum outro projeto de curto prazo que requer os serviços de diretório, mas não específicos acesso a recursos empresariais internos.

Como uma vantagem, uma floresta isolado que DC não é replicado no local DCs, resultando em tráfego de rede de saída inferior gerado pelo sistema, diretamente a reduzir os custos. Para mais informações sobre os encargos de tráfego de rede, consulte [do Azure de preços em rapidamente](http://azure.microsoft.com/pricing/).

Outro exemplo, suponha que tem os requisitos de privacidade para um serviço, mas o serviço depende de acesso à sua interno Windows Server Active Directory. Se tiver permissão para alojar dados para o serviço na nuvem, poderá implementar um novo domínio subordinado para a floresta interna no Azure. Neste caso, pode implementar um controlador de domínio para o novo domínio subordinado (sem o catálogo global para ajudar as questões de privacidade do endereço). Neste cenário, juntamente com uma réplica de implementação de DC, necessita de uma rede virtual para conectividade com os controladores de domínio no local.

Se criar uma nova floresta, escolha se pretende utilizar [confianças do Active Directory](https://technet.microsoft.com/library/cc771397) ou [confianças de Federação](https://technet.microsoft.com/library/dd807036). Equilibrar os requisitos de ditado pelo compatibilidade, segurança, conformidade, o custo e resiliência. Por exemplo, para tirar partido das [autenticação seletiva](https://technet.microsoft.com/library/cc755844) pode optar por utilizar implementar uma nova floresta no Azure e criar uma confiança do Windows Server Active Directory entre a floresta no local e a floresta de nuvem. No entanto, se a aplicação tiver suporte para afirmações, poderá implementar confianças de Federação em vez de fidedignidades de floresta do Active Directory. Outro fator será o custo para replicar os dados mais ao alargar o local no Windows Server Active Directory para a nuvem ou gerar tráfego de saída mais como resultado da autenticação e a carga de consulta.

Requisitos para a tolerância de disponibilidade e falhas também afetam a escolha. Por exemplo, se a ligação for interrompida, a aplicações tirar partido de uma fidedignidade de Kerberos ou Federação confiam são todos provavelmente inteiramente quebrados, a menos que implementou a infraestrutura suficiente no Azure. Configurações de implementação alternativos, tais como o DC de réplica (gravável ou RODCs) aumenta a probabilidade de conseguir tolerar falhas de ligação.

### <a name="BKMK_ADSiteTopology"></a>Topologia de site do Windows Server Active Directory
Tem de definir corretamente locais e ligações de site para otimizar o tráfego e minimizar os custos. Sites, ligações de sites e sub-redes afetam a topologia de replicação entre controladores de domínio e o fluxo de tráfego de autenticação. Considere os seguintes encargos de tráfego e, em seguida, implementar e configurar os controladores de domínio, de acordo com os requisitos do seu cenário de implementação:

* Há uma taxa nominal por hora para o próprio gateway:
  
  * Pode ser iniciado e parado como julgar
  * Se parado, as VMs do Azure estão isoladas da rede empresarial
* Tráfego de entrada é gratuito
* Tráfego de saída é cobrado, em conformidade com [do Azure de preços em rapidamente](http://azure.microsoft.com/pricing/). Pode otimizar a propriedades de ligação de site entre sites no local e os sites de nuvem da seguinte forma:
  
  * Se estiver a utilizar várias redes virtuais, configure as ligações de site e os seus custos adequadamente para impedir que o Windows Server AD DS atribuir prioridades a sites do Azure através de um que pode fornecer os mesmos níveis de serviço, sem encargos. Também poderá considerar a desativar a Bridge todos os sites a opção de ligação (BASL) (que está ativada por predefinição). Isto garante que apenas diretamente ligados sites replicam entre si. DCs em sites ligados transitively deixam de ser capazes de replicar diretamente entre si, mas tem de ser replicada através de um site comum ou sites. Se os sites intermediário ficam indisponíveis por algum motivo, não irá ocorrer a replicação entre controladores de domínio em sites transitively ligados, mesmo que esteja disponível a conectividade entre os sites. Por fim, onde secções do comportamento de que a replicação transitiva permanecem desejável, crie um site pontes de ligação que contêm as ligações de site adequadas e a sites, tal como no local, os sites de rede empresarial.
  * [Configurar os custos de ligação de site](https://technet.microsoft.com/library/cc794882) corretamente para evitar o tráfego indesejado. Por exemplo, se **tente o seguinte Site mais próximo** definição estiver ativada, certifique-se os sites de rede virtual não estão a próxima closest ao aumentar os custos associados do objeto de ligação de site que liga o site do Azure para a rede empresarial.
  * Configurar a ligação entre locais [intervalos](https://technet.microsoft.com/library/cc794878) e [agendas](https://technet.microsoft.com/library/cc816906) , de acordo com requisitos de consistência e a taxa de alterações de objetos. Alinhe a agenda de replicação com tolerância a latência. DCs replicar apenas o último Estado de um valor, para que diminuir o intervalo de replicação pode reduzir os custos se houver taxa de alteração de um objeto suficiente.
* Se minimizar os custos é uma prioridade, certifique-se de que a replicação está agendada e a notificação de alteração não está ativada. Esta é a configuração predefinida quando replicar entre sites. Isto não é importante se estiver a implementar um RODC numa rede virtual porque o RODC não irá replicar as alterações de saída. Mas, se implementar um controlador de domínio gravável, certifique-se de que a ligação de site não está configurada para replicar as atualizações com frequência desnecessária. Se implementar um servidor de catálogo global (GC), certifique-se de que todos os sites que contém um GC replicam partições de domínio de uma origem de DC num site que está ligado com uma ligação de site ou ligações de site que tenham um custo mais baixo que o CG no site do Azure.
* É possível ainda reduzir o tráfego de rede gerado pela replicação entre sites alterando o algoritmo de compressão de replicação. O algoritmo de compressão é controlado pelo algoritmo de compressão de HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NTDS\Parameters\Replicator de entrada de registo REG_DWORD. O valor predefinido é 3, que está correlacionada com o algoritmo de Xpress comprimir. Pode alterar o valor para 2, que altera o algoritmo para MSZip. Na maioria dos casos, isto irá aumentar a compressão, mas isto é feito em detrimento da utilização da CPU. Para obter mais informações, consulte [Active Directory, como funciona a topologia de replicação](https://technet.microsoft.com/library/cc755994).

### <a name="BKMK_IPAddressDNS"></a>Endereçamento IP e DNS
Máquinas virtuais do Azure são alocadas "endereços de nos concessionados DHCP" por predefinição. Porque persistirem endereços dinâmicos da rede virtual do Azure com uma máquina virtual para a duração da máquina virtual, são cumpridos os requisitos do Windows Server AD DS.

Como resultado, quando utilizar um endereço dinâmico no Azure, está em efeito utilizando um endereço IP estático, porque é encaminhável para o período da concessão, e o período da concessão é igual a duração do serviço de nuvem.

No entanto, o endereço dinâmico é desalocado se a VM está a encerrar. Para impedir que o endereço IP a ser desalocada, pode [utilizar Set-AzureStaticVNetIP para atribuir um endereço IP estático](http://social.technet.microsoft.com/wiki/contents/articles/23447.how-to-assign-a-private-static-ip-to-an-azure-vm.aspx).

Resolução de nomes, implementar as suas próprias (ou tirar partido do seu existente) infraestrutura de servidor DNS; DNS fornecidos pelo Azure não satisfazer as necessidades de resolução de nome avançadas do Windows Server AD DS. Por exemplo, não suportam registos de SRV dinâmicos e assim sucessivamente. Resolução de nomes é um item de configuração críticas para os controladores de domínio e clientes associados a um domínio. Os controladores de domínio tem de ser capazes de registar registos de recursos e resolver os registos de recursos de outros DC.
Por motivos de desempenho e tolerância a falhas, é ideal para instalar o serviço DNS do Windows Server no DC em execução no Azure. Em seguida, configure as propriedades de rede virtual do Azure com o nome e endereço IP do servidor DNS. Quando iniciar as outras VMs na rede virtual, as respetivas definições de resolução DNS cliente serão configuradas com o servidor DNS como parte da atribuição de endereço IP dinâmico.

> [!NOTE]
> Não é possível associar computadores no local para um domínio do Active Directory do Windows Server AD DS que está alojado no Azure diretamente através da Internet. Os requisitos de porta para o Active Directory e a operação de associação de domínio apresente impractical para diretamente expõem as portas necessárias e, em efeito, um controlador de domínio completo da Internet.
> 
> 

VMs registar o respetivo nome DNS automaticamente no arranque, ou quando existe uma alteração de nome.

Para obter mais informações sobre este exemplo e outro exemplo que mostra como aprovisionar a VM primeiro e instalar o AD DS no mesmo, consulte [instalar uma nova floresta do Active Directory no Microsoft Azure](active-directory-new-forest-virtual-machine.md). Para obter mais informações sobre como utilizar o Windows PowerShell, consulte [instalar o Azure PowerShell](/powershell/azureps-cmdlets-docs) e [Cmdlets de gestão do Azure](/powershell/module/azurerm.compute/#virtual_machines).

### <a name="BKMK_DistributedDCs"></a>DCs geo-distribuição
Quando alojam vários controladores de domínio em redes virtuais diferentes, o Azure oferece vantagens:

* Vários site-tolerância a falhas
* Proximidade física aos escritórios (menor latência de)

Para obter informações sobre como configurar a comunicação direta entre redes virtuais, consulte [configurar a rede virtual para conectividade de rede virtual](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

### <a name="BKMK_RODC"></a>Controladores de domínio só de leitura
Tem de escolher se pretende implementar controladores de domínio só de leitura ou graváveis. Poderá ser inclined implementar RODCs porque não terão o controlo físico por cima deles, mas foram concebidos RODCs implementados em localizações onde a segurança física está em risco, como sucursais.

Azure não apresenta o risco de segurança físico de uma sucursal, mas ainda podem provar a RODCs para ser mais económico porque as funcionalidades que fornecem são ideais para estes ambientes, apesar de, por motivos de muito diferentes. Por exemplo, os RODCs não têm nenhuma replicação de saída e estão capazes de preencher seletivamente segredos (palavras-passe). No downside, falta destes segredos poderão necessitar de tráfego de saída a pedido para validá-los como um utilizador ou computador efetua a autenticação. Mas segredos podem ser seletivamente pré-preenchida e colocadas em cache.

RODCs proporcionam uma vantagem adicional no e em torno preocupações HBI e PII porque pode adicionar atributos que contêm dados confidenciais para o RODC filtrado atributo definido (FAS). Os FAS é um conjunto de atributos não são replicadas RODCs personalizável. Pode utilizar os FAS como salvaguarda, no caso de não são permitidas ou não pretender armazenar PII ou HBI no Azure. Para obter mais informações, consulte [RODC filtrado de atributos definido [(https://technet.microsoft.com/library/cc753459)].

Certifique-se de que as aplicações serão compatíveis com RODCs que planeia utilizar. Muitas aplicações preparados no Windows Server Active Directory funcionam bem com RODCs, mas algumas aplicações podem efetuar inefficiently ou falhar se não têm acesso a um controlador de domínio gravável. Para obter mais informações, consulte [guia de compatibilidade da aplicação de controladores de domínio só de leitura](https://technet.microsoft.com/library/cc755190).

### <a name="BKMK_GC"></a>Catálogo global
Tem de escolher se pretende instalar um catálogo global (GC). Numa floresta único domínio, deve configurar todos os DCs como servidores de catálogo global. Não tal irá aumentar os custos, porque não será nenhum tráfego de replicação adicionais.

Numa floresta multidomain, GCs são necessários para expandir as associações de grupo Universal durante o processo de autenticação. Se o utilizador não implementa um GC, cargas de trabalho na rede virtual que autenticarem face a um controlador de domínio no Azure indiretamente irão gerar tráfego de autenticação de saída para consultar as GCs no local durante cada tentativa de autenticação.

Os custos associados GCs são menos previsível porque estes alojam todos os domínios (na parte). Se a carga de trabalho aloja um serviço de acesso à Internet e autentica utilizadores em relação do Windows Server AD DS, os custos pode ser completamente imprevisíveis. Para ajudar a reduzir as consultas de GC fora do site na nuvem durante a autenticação, pode [ativar o Caching de adesão ao grupo Universal](https://technet.microsoft.com/library/cc816928).

### <a name="BKMK_InstallMethod"></a>Método de instalação
Tem de escolher como instalar os controladores de domínio na rede virtual:

* Promova os controladores de domínio novo. Para obter mais informações, consulte [instalar uma nova floresta do Active Directory numa rede virtual do Azure](active-directory-new-forest-virtual-machine.md).
* Mova o VHD de um DC virtual no local para a nuvem. Neste caso, tem de garantir que o DC virtual no local é "movido," não "copiados" ou "clonado".

Utilize máquinas virtuais apenas do Azure para controladores de domínio (por oposição a Azure "web" ou "trabalho" função VMs). São duráveis e características de durabilidade do Estado para um controlador de domínio é necessária. Máquinas virtuais do Azure foram concebidas para cargas de trabalho, tais como os controladores de domínio.

Utiliza SYSPREP para implementar ou clonar os controladores de domínio. A capacidade de clonar controladores de domínio está apenas disponível a partir do Windows Server 2012. A funcionalidade de clonagem requer o suporte para VMGenerationID no hipervisor subjacente. Hyper-V no Windows Server 2012 e o Azure redes virtuais ambos suportam VMGenerationID, tal como fornecedores de software de Virtualização de terceiros.

### <a name="BKMK_PlaceDB"></a>Colocação da base de dados do Windows Server AD DS e SYSVOL
Selecione onde localizar a base de dados do Windows Server AD DS, os registos e SYSVOL. Tem de ser implementados em discos de dados do Azure.

> [!NOTE]
> Discos de dados do Azure estão restritos 1 TB.
> 
> 

Unidades de disco de dados não não operações de escrita para a cache por predefinição. Unidades de disco de dados que estão anexadas a uma VM utiliza através de escrita a colocação em cache. Escrita através da colocação em cache faz com que se a operação de escrita está empenhada em durável storage do Azure antes da transação está concluída da perspetiva de sistema de operativo da VM. Fornece uma durabilidade, em detrimento escritas ligeiramente mais lentas.

Isto é importante para o Windows Server AD DS, porque a cache de write-behind disco invalida pressupostos sólidos realizados pelo DC. Windows Server AD DS tentativas desativar a colocação em cache de escrita, mas é até o disco do sistema de e/s que respeite-lo. Falha ao desativar a colocação em cache de escrita pode, em determinadas circunstâncias, dar origem a reversão do USN resultavam remanescentes objetos e outros problemas.

Como melhor prática para controladores de domínio virtuais, efetue o seguinte:

* Configure a definição de preferência de Cache do anfitrião no disco de dados do Azure para nenhum. Isto impede que os problemas com a colocação em cache de escrita para operações do AD DS.
* Armazenar a base de dados, os registos e SYSVOL no mesmo disco de dados ou os discos de dados separada. Normalmente, trata-se de um disco separado do disco utilizado para o sistema operativo. A chave takeaway é que a base de dados do Windows Server AD DS e SYSVOL não devem ser armazenados num tipo de disco do sistema de operativo do Azure. Por predefinição, o processo de instalação do AD DS instala estes componentes em % systemroot % pasta, o que não é recomendada para o Azure.

### <a name="BKMK_BUR"></a>Cópia de segurança e restauro
Tenha em atenção que é e não é suportado para cópia de segurança e restauro de um DC em geral e mais especificamente, os valores em execução numa VM. Consulte [cópia de segurança e restauro considerações para controladores de domínio virtualizados](https://technet.microsoft.com/library/virtual_active_directory_domain_controller_virtualization_hyperv#backup_and_restore_considerations_for_virtualized_domain_controllers).

Crie cópias de segurança do Estado de sistema através da utilização de apenas software cópia de segurança que seja especificamente em consideração os requisitos de cópia de segurança do Windows Server AD DS, tais como a cópia de segurança do Windows Server.

Não copie ou clonar os ficheiros VHD de controladores de domínio em vez de efetuar cópias de segurança regulares. Deve um restauro nunca ser necessário, se o fizer, utilizar VHDs clonados ou copiados sem o Windows Server 2012 e um hipervisor suportado introduz bolhas USN.

### <a name="BKMK_FedSrvConfig"></a>Configuração do servidor de Federação
A configuração dos servidores de Federação do Windows Server AD FS (STSs) depende em parte se as aplicações que pretende implementar no Azure devem ter acesso a recursos na sua rede no local.

Se as aplicações cumpram os seguintes critérios, pode implementar as aplicações de forma isolada da sua rede no local.

* Se aceitarem tokens de segurança SAML
* São exposable à Internet
* Não acedam a recursos no local

Neste caso, configure o Windows Server AD FS STSs da seguinte forma:

1. Configure uma floresta de domínio único isolada no Azure.
2. Fornece acesso federado para a floresta ao configurar um farm de servidores de Federação do Windows Server AD FS.
3. Configure o Windows Server AD FS (farm de servidores de Federação e farm de proxies de servidor de Federação) na floresta no local.
4. Estabelece uma relação de fidedignidade de Federação entre o local e instâncias do Azure do Windows Server AD FS.

Por outro lado, se as aplicações requerem acesso aos recursos no local, pode configurar o Windows Server AD FS com a aplicação no Azure da seguinte forma:

1. Configure a conectividade entre redes no local e o Azure.
2. Configure um farm de servidores de Federação do Windows Server AD FS na floresta no local.
3. Configure um farm de proxies de servidor de Federação do Windows Server AD FS no Azure.

Esta configuração tem a vantagem de reduzir a exposição de recursos no local, semelhantes à configuração do Windows Server AD FS com aplicações numa rede de perímetro.

Tenha em atenção que o cenário, pode estabelecer relações de fidedignidades com mais fornecedores de identidade, se necessitar de colaboração empresa-empresa.

### <a name="BKMK_CloudSvcConfig"></a>Configuração de serviços cloud
Serviços cloud são necessários se pretende expor uma VM diretamente à Internet ou se quiser expor uma aplicação de balanceamento de carga para a Internet. Isto é possível porque cada serviço em nuvem oferece um único endereço IP virtual configurável.

### <a name="BKMK_FedReqVIPDIP"></a>Requisitos do servidor de Federação para público e privado (IP dinâmico vs virtual IP) de endereçamento de IP
Cada máquina virtual do Azure, receber um endereço IP dinâmico. Um endereço IP dinâmico é um endereço privado acessível apenas dentro do Azure. Na maioria dos casos, no entanto, será necessário configurar um endereço IP virtual para as implementações do Windows Server AD FS. O IP virtual é necessário expõem pontos finais do Windows Server AD FS à Internet e será utilizado por clientes e os parceiros federados para autenticação e a gestão contínua. Um endereço IP virtual é uma propriedade de um serviço em nuvem que contém um ou mais máquinas virtuais do Azure. Se a aplicação com suporte para afirmações implementada no Azure e do Windows Server AD FS para a Internet e partilha de portas comuns, cada requer um endereço IP virtual do seu próprio e, por conseguinte, será necessário criar um serviço em nuvem para a aplicação e um segundo para o Windows Server AD FS.

Existência de definições do endereço IP virtual de termos e endereço IP dinâmico, consulte [termos e definições](#BKMK_Glossary).

### <a name="BKMK_ADFSHighAvail"></a>Configuração de elevada disponibilidade do Windows Server AD FS
Embora seja possível implementar os serviços de Federação autónomo do Windows Server AD FS, recomenda-se para implementar um farm de, pelo menos, dois nós para AD FS STS e proxies para ambientes de produção.

Consulte [considerações sobre a topologia do AD FS 2.0 implementação](https://technet.microsoft.com/library/gg982489) no [AD FS 2.0 Design guia](https://technet.microsoft.com/library/dd807036) decidir que opções de configuração de implementação melhor se adequarem às suas necessidades particulares.

> [!NOTE]
> Para obter balanceamento de carga para pontos finais de FS do Windows Server AD no Azure, configure todos os membros do farm do Windows Server AD FS no mesmo serviço de nuvem e utilize a capacidade de balanceamento de carga do Azure para HTTP (predefinição 80) e portas HTTPS (predefinição 443). Para obter mais informações, consulte [sonda do Balanceador de carga do Azure](https://msdn.microsoft.com/library/azure/jj151530).
> Windows Server rede balanceamento de carga (NLB) não é suportada no Azure.
> 
> 

