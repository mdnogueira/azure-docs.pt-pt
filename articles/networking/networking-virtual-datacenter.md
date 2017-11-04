---
title: Centro de dados do Microsoft Azure Virtual | Microsoft Docs
description: Aprenda a criar o seu centro de dados virtual no Azure
services: networking
author: tracsman
manager: rossort
tags: azure-resource-manager
ms.service: virtual-network
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/26/2017
ms.author: jonor
ms.openlocfilehash: 7dcc6b77bde8b8a7b485525105c1a07c53301f8e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="microsoft-azure-virtual-data-center"></a>Centro de dados Virtual do Microsoft Azure
**Microsoft Azure**: agilizar, poupar dinheiro, integrar aplicações no local e dados

## <a name="overview"></a>Descrição geral
Migrar aplicações no local para o Azure, mesmo sem quaisquer alterações significativas (uma abordagem conhecida como "de comparação de precisão e deslocar"), fornece às organizações as vantagens de uma infraestrutura segura e económico. No entanto, para efetuar a maioria da agilidade possíveis com informática em nuvem, as empresas devem evoluir as arquiteturas para tirar partido das capacidades do Azure. Microsoft Azure fornece serviços de hiper escala e infraestrutura, as capacidades de nível empresarial e fiabilidade e muitas opções para conectividade híbrida. Os clientes podem optar por aceder a estes serviços em nuvem através da Internet ou com o ExpressRoute do Azure, que fornece a conectividade de rede privada. A plataforma Microsoft Azure permite aos clientes expandir a sua infraestrutura para a nuvem e criar arquiteturas de várias camadas de forma totalmente integrada. Além disso, os parceiros da Microsoft fornecem capacidades de avançada por oferta de serviços de segurança e de aplicações virtuais que estão otimizadas para ser executada no Azure.

Este artigo fornece uma descrição geral de padrões e estruturas que podem ser utilizadas para resolver as preocupações de escala, desempenho e segurança da arquitetura muitos que os clientes enfrentam quando pensar sobre como mover en masse para a nuvem. Uma descrição geral sobre como ajustar o diferentes funções de TI organizacionais para a gestão e governação do sistema também são abordados com ênfase aos requisitos de segurança e a otimização de custos.

## <a name="what-is-a-virtual-data-center"></a>O que é um centro de dados do Virtual?
Nos dias iniciais, as soluções de nuvem foram concebidas para anfitrião único relativamente isolado, aplicações, no espetro público. Esta abordagem funcionado bem para alguns anos. No entanto, como as vantagens da nuvem soluções ficaram aparente e várias cargas de trabalho em grande escala foram alojadas na nuvem, segurança, fiabilidade e desempenho, de endereçamento e de custo preocupações de implementações de uma ou mais regiões ficaram vitais ao longo do ciclo de vida do serviço em nuvem.

O diagrama de implementação de nuvem seguinte ilustra alguns exemplos de intervalos de segurança (caixa vermelha) e espaço para aplicações virtuais de rede de otimização em cargas de trabalho (caixa amarela).

[![0]][0]

O Centro de dados do Virtual (vDC) nasceu desta necessidade para dimensionamento para suportar cargas de trabalho empresariais e a necessidade de lidar com problemas introduzidos quando o suporte de aplicações em grande escala na nuvem pública.

Um vDC não é apenas as cargas de trabalho aplicações na nuvem, mas também da rede, segurança, gestão e infraestrutura (por exemplo, DNS e serviços de diretório). Normalmente, também fornece uma ligação privada para um centro de dados ou de rede no local. Como mover mais cargas de trabalho para o Azure, é importante pensar sobre a infraestrutura de suporte e os objetos que estas cargas de trabalho são colocadas em. Ter em consideração cuidadosamente como são estruturados recursos pode evitar a proliferação de centenas de "Ilhas de carga de trabalho" que tem de ser geridas separadamente com o fluxo de dados independentes, modelos de segurança e desafios de conformidade.

Um centro de dados do Virtual é, essencialmente, uma coleção de entidades separadas, relacionadas, mas com infraestrutura, funcionalidades e funções de suporte comuns. Ao visualizar as cargas de trabalho como um vDC integrada, que pode pensa custo reduzido devido a economias de escala, segurança otimizada através da centralização de fluxo de componentes e os dados, juntamente com as operações mais fácil, gestão e conformidade auditorias simples e.

> [!NOTE]
> É importante compreender que é o vDC **não** um produto Azure discreto, mas a combinação de várias funcionalidades e capacidades para satisfazer os seus requisitos exatos. vDC é uma forma de ter em consideração as cargas de trabalho e a utilização do Azure para maximizar os recursos e capacidades na nuvem. O DC virtual, por conseguinte, é uma abordagem modular sobre como criar cópias de segurança serviços de TI no Azure, respecting organizacionais funções e responsabilidades.

O vDC pode ajudar as empresas obter cargas de trabalho e aplicações no Azure para os seguintes cenários:

-   Alojar várias cargas de trabalho relacionadas
-   Migrar cargas de trabalho de um ambiente no local para o Azure
-   Implementar requisitos de acesso e segurança partilhada ou centralizada em cargas de trabalho
-   A combinação de DevOps e centralizada TI adequadamente para uma grande empresa

A chave para desbloquear as vantagens de vDC, é uma topologia centralizada (hub e spokes realizar) com uma combinação de funcionalidades do Azure: [Azure VNet][VNet], [NSGs] [ NSG], [O VNet Peering][VNetPeering], [rotas definidas pelo utilizador (UDR)][UDR]e identidade do Azure com [Base de função (RBAC) do controlo de acesso][RBAC].

## <a name="who-needs-a-virtual-data-center"></a>Quem deverá Virtual Centro de dados?
Qualquer cliente do Azure que tem de mover mais do que algumas das cargas de trabalho no Azure podem beneficiar de pensar sobre a utilização de recursos comuns. Consoante a importância, o mesmo única aplicações podem beneficiar da utilização de padrões e componentes utilizados para criar um vDC.

Se a sua organização tem uma segurança de TI, rede, centralizada, e/ou equipa/departamento de conformidade, um vDC pode ajudar a impor a política pontos, segregação dos duty e garantir a uniformidade dos componentes comuns subjacentes ao transmitir as equipas das aplicações a maior quantidade liberdade e controlo, conforme adequado para os seus requisitos.

As organizações que procura DevOps podem utilizar os conceitos de vDC para fornecer pockets autorizados dos recursos do Azure e certifique-se de que têm controlo total dentro desse grupo (a subscrição ou grupo de recursos numa subscrição comum), mas a rede e limites de segurança permanecem em conformidade, tal como definido por uma política centralizada numa hub VNet e o grupo de recursos.

## <a name="considerations-on-implementing-a-virtual-data-center"></a>Considerações sobre na implementação de um centro de dados Virtual
Ao conceber uma vDC, existem vários problemas pivotal a ter em consideração:

-   Serviços de diretório e identidade
-   Infraestrutura de segurança
-   Conectividade para a nuvem
-   Conectividade na nuvem

##### <a name="identity-and-directory-service"></a>*Serviço de diretório e identidade*
Serviços de identidade e de diretório são um aspeto chave dos dados de todos os centros de ambos no local e na nuvem. Identidade está relacionado com todos os aspetos de acesso e autorização para serviços de vDC. Para ajudar a garantir que apenas utilizadores autorizados e processos de aceder à sua conta do Azure e recursos, o Azure utiliza vários tipos de credenciais para autenticação. Estas incluem certificados, assinaturas digitais, as chaves criptográficas e palavras-passe (para aceder ao mesmo a conta do Azure). [*Multi-factor Authentication do Azure* (MFA)] [ MFA] é uma camada adicional de segurança para aceder aos serviços do Azure. Azure MFA fornece autenticação forte com uma gama de opções de verificação fácil — chamada telefónica, mensagem de texto ou notificação de aplicação móvel — e permitir que os clientes escolher o método que preferem.

Qualquer grande empresa tem de definir um processo de gestão de identidade que descreve a gestão de identidades individuais, os respetivos autenticação, autorização, funções e privilégios dentro ou entre o vDC. Os objetivos deste processo devem ser para aumentar a produtividade e segurança ao diminuir o custo, tempo de inatividade e as tarefas manuais repetitivas.

Organizações/empresariais pode exigir uma mistura demanding de serviços para a linha de-de-empresas diferentes (LOBs) e os funcionários têm frequentemente diferentes funções quando relacionados com projetos diferentes. Um vDC requer cooperação boa entre equipas diferentes, cada um com as definições de função específico, para obter os sistemas com boa governação. A matriz das responsabilidades, acesso e direitos pode ser extremamente complexa. Gestão de identidade no vDC é implementado através de [ *do Azure Active Directory* (AAD)] [ AAD] e controlo de acesso baseado em funções (RBAC).

Um serviço de diretório é uma infraestrutura de informações partilhadas para localizar, gerir, administrar e organizar os itens de uso corrente e recursos de rede. Estes recursos podem incluir volumes, pastas, ficheiros, impressoras, utilizadores, grupos, dispositivos e outros objetos. Cada recurso na rede é considerado um objeto pelo servidor de diretório. Informações sobre um recurso são armazenadas como uma coleção de atributos associados esse recurso ou o objeto.

Todos os serviços do Microsoft online negócio baseiam-se no Azure Active Directory (AAD) para início de sessão e outra identidade necessidades. O Azure Active Directory é uma solução em cloud de gestão de acessos e identidades altamente disponível e completa que combina serviços de diretório centrais, governação de identidade avançada e gestão de acesso da aplicação. AAD pode ser integrado no local do Active Directory para ativar o início de sessão para todos os baseado na nuvem e alojada localmente (no local) aplicações. Os atributos de utilizador do Active Directory no local podem ser sincronizados automaticamente para o AAD.

Um administrador global único não é necessário atribuir a todas as permissões num vDC. Em vez disso, cada departamento específico (ou grupo de utilizadores ou serviços no serviço de diretório) pode ter as permissões necessárias para gerir os seus próprios recursos dentro de um vDC. Structuring permissões requer balanceamento. Demasiados permissões podem impedir a eficiência de desempenho e poucos ou permissões soltas podem aumentar riscos de segurança. Azure baseada em funções controlo de acesso (RBAC) ajuda a resolver este problema, gestão de acesso detalhada para os recursos de vDC da oferta.

##### <a name="security-infrastructure"></a>*Infraestrutura de segurança*
Infraestrutura de segurança, no contexto de um vDC, principalmente relacionadas com a segregação de tráfego no segmento de rede virtual específico de vDC e, como controlar a entrada e de saída flui em toda a vDC. Azure baseia-se na arquitetura de multi-inquilino que impede o tráfego não autorizado e não intencional entre implementações, utiliza o isolamento de rede Virtual (VNet), acesso (ACL) de listas de controlo, carregar balanceadores e filtros IP, juntamente com as políticas de fluxo de tráfego. Tradução de endereços de rede (NAT) separa o tráfego de rede interno do tráfego externo.

Os recursos de infraestrutura do Azure aloca recursos de infraestrutura para cargas de trabalho de inquilino e gere as comunicações de e para máquinas virtuais (VMs). O hipervisor do Azure impõe a memória e o processo de separação entre as VMs e tráfego aos inquilinos de SO convidado de rede de rotas de forma segura.

##### <a name="connectivity-to-the-cloud"></a>*Conectividade para a nuvem*
O vDC necessita de conectividade com redes externas para oferecer serviços aos clientes, parceiros e/ou utilizadores internos. Isto significa que, normalmente, não apenas à Internet, mas também para centros de dados e de redes no local de conectividade.

Os clientes podem criar as suas políticas de segurança para controlar o que e como vDC específico alojado serviços são acessíveis para/a partir da Internet com os dispositivos de rede Virtual (inspeção de tráfego e filtragem) e encaminhamento rede filtragem (encaminhamento definido pelo utilizador e grupos de segurança de rede) e políticas personalizadas.

As empresas, muitas vezes, tem de ligar vDCs centros de dados no local ou outros recursos. A conectividade entre redes do Azure e no local, por conseguinte, é um aspeto fundamental ao conceber uma arquitetura efetiva. As empresas têm duas formas diferentes de criar um interconnection entre vDC e no local no Azure: trânsito através da Internet e/ou privadas ligações diretas.

Um [ **VPN de Site para Site do Azure** ] [ VPN] é um serviço de interligação através da Internet entre redes no local e o vDC estabelecida através de ligações encriptadas seguras (túneis IPsec/IKE). Ligação de Site para Site do Azure é flexível e rápido criar e não requer qualquer aprovisionamento adicional, como todas as ligações ligarem através da internet.

[**ExpressRoute** ] [ ExR] é um serviço de conectividade do Azure que permite-lhe criar ligações privadas entre vDC e as redes no local. As ligações do ExpressRoute não passam para a Internet pública e oferecem uma maior segurança, fiabilidade e velocidades superiores (até 10 Gbps), juntamente com a latência consistente. O ExpressRoute é muito útil para vDCs, como os clientes podem obter as vantagens de regras de compatibilidade associadas ligações privadas do ExpressRoute.

Implementar ligações ExpressRoute envolve envolvimento com um fornecedor de serviços ExpressRoute. Para clientes que necessita para começar rapidamente, é também comum inicialmente utilizar VPN Site a Site para estabelecer conectividade entre o vDC e recursos no local e, em seguida, migra para a ligação do ExpressRoute.

##### <a name="connectivity-within-the-cloud"></a>*Conectividade na nuvem*
[As VNets] [ VNet] e [VNet Peering] [ VNetPeering] são os serviços de conectividade de rede básico dentro de um vDC. Um limite de isolamento de recursos de vDC natural garantias de uma VNet e o VNet peering permite intercommunication entre VNets diferentes na mesma região do Azure. Controlo de tráfego no interior de uma VNet e entre VNets tem de corresponder a um conjunto de regras de segurança especificado através de listas de controlo de acesso ([grupo de segurança de rede][NSG]), [aparelhos de rede Virtual][NVA]e tabelas de encaminhamento personalizadas ([UDR][UDR]).

## <a name="virtual-data-center-overview"></a>Descrição geral do Centro de dados virtual

### <a name="topology"></a>Topologia
Modelo hub- and spokes realizar expandido o Centro de dados do Virtual numa única região do Azure

[![1]][1]

O hub é a zona central que controla e inspeciona o tráfego de entrada e/ou saída entre diferentes zonas: Internet, no local e a spokes a realizar. A topologia hub- and -spoke proporciona o departamento de TI uma forma eficaz de impor políticas de segurança numa localização central, ao reduzir a possibilidade de uma configuração incorreta e exposição.

O hub contém os componentes de serviço comum consumidos por spokes a realizar. Seguem-se alguns exemplos típicos de serviços centrais comuns:

-   A infraestrutura do Active Directory do Windows (com o serviço ADFS relacionado) necessária para a autenticação de utilizador de terceiros aceder a partir de redes não fidedignas antes de obter acesso para as cargas de trabalho no spoke
-   Um serviço DNS para resolver a nomenclatura para a carga de trabalho spokes realizar, para acesso recursos no local e na Internet
-   Uma infraestrutura PKI, para implementar o início de sessão único em cargas de trabalho
-   Controlo de fluxo (TCP/UDP) entre o spokes realizar e a Internet
-   Controlo de fluxo entre os spoke e no local
-   Se assim o desejar, fluxo de controlo entre um spoke e outra

O vDC reduz o custo global, utilizando a infraestrutura de hub partilhado entre vários spokes a realizar.

A função de cada spoke pode ser a diferentes tipos de anfitrião das cargas de trabalho. O spokes realizar também pode fornecer uma abordagem modular para implementações repetíveis (por exemplo, desenvolvimento e teste, testes de aceitação de utilizadores, pré-produção e de produção) das cargas de trabalho mesmas. Também pode ser utilizado o spokes realizar segregar e ativar a diferentes grupos na sua organização (por exemplo, grupos de DevOps). Dentro de um spoke, é possível implementar uma carga de trabalho básica ou complexas multicamadas cargas de trabalho com controlo de tráfego entre as camadas.

##### <a name="subscription-limits-and-multiple-hubs"></a>Limites de subscrição e hubs de vários
No Azure, cada componente, independentemente do tipo, é implementado numa subscrição do Azure. O isolamento de componentes do Azure em diferentes subscrições do Azure pode satisfazer os requisitos de LOBs diferentes, como a definição de cópia de segurança diferenciadas níveis de acesso e autorização.

Um único vDC pode aumentar verticalmente ao elevado número de spokes realizar, embora, tal como acontece com cada sistema de TI, existem limites de plataformas. A implementação de hub está vinculada a uma subscrição do Azure específica, que tem restrições e limites (por exemplo, um número máximo de peerings de VNet - consulte [subscrição do Azure e limites de serviço, quotas e restrições] [ Limits] para obter detalhes). Em casos onde os limites podem ser um problema, pode dimensionar a arquitetura até ainda mais ao alargar o modelo a partir de um único hub-spokes realizar para um cluster de concentrador hub- and spokes a realizar. Os hubs de vários num ou mais regiões do Azure podem ser interligados utilizando Express Route ou VPN de site para site.

[![2]][2]

A introdução dos hubs vários aumenta o esforço de custos e gestão do sistema e só deverá ser seria justificada por escalabilidade (exemplos: limites de sistema ou redundância) e replicação regional (exemplos: recuperação de desastre ou de desempenho do utilizador final). Em cenários que requerem vários hubs, todos os hubs deve esforçar-nos oferecer o mesmo conjunto de serviços para facilitar a operacional.

##### <a name="interconnection-between-spokes"></a>Interconnection entre spokes realizar
Dentro de um único spoke, é possível implementar cargas de trabalho de várias camadas complexas. Configurações de várias camadas podem ser implementadas através de sub-redes (um para cada camada) na mesma VNet e filtragem fluxos utilizando NSGs.

Por outro lado, um arquiteto de sistemas pode querer implementar uma carga de trabalho de várias camada em várias VNets. Spokes realizar utilizando VNet peering, pode ligar a outros spokes realizar no mesmo hub ou hubs diferentes. Um exemplo deste cenário típico é o caso em que servidores de processamento de aplicação estão num spoke (VNet), enquanto a base de dados é implementado num spoke diferente (VNet). Neste caso, é fácil interligar-se a spokes realizar com o VNet peering e, deste modo, evite transiting através do hub. Deve ser efetuada uma revisão de segurança e arquitetura cuidada para garantir que ignorando do hub não ignorar ou pontos que só podem existir num hub de auditoria de segurança importante.

[![3]][3]

Também pode ser interligada spokes realizar para um spoke que age como um concentrador. Esta abordagem cria uma hierarquia de dois níveis: spoke no nível superior (nível 0) se tornar o hub de inferior spokes realizar (nível 1) da hierarquia. Tem do spokes realizar de vDC reencaminhar o tráfego para o concentrador central para aceder à rede no local ou à internet. Uma arquitetura com dois níveis do hub apresenta encaminhamentos complexos que remove as vantagens de uma relação de concentrador hub-spoke simples.

Embora o Azure permite topologias complexas, um dos princípios de núcleos do conceito de vDC é repetibilidade e na simplicidade. Para minimizar o esforço de gestão, a conceção de concentrador hub-spoke simples é a arquitetura de referência de vDC recomendada.

### <a name="components"></a>Componentes
Um centro de dados do Virtual é constituído por quatro tipos de componente básico: **infraestrutura**, **redes de perímetro**, **cargas de trabalho**, e **monitorização**.

Cada tipo de componente é composta por várias funcionalidades do Azure e recursos. O vDC é constituído por instâncias de vários tipos de componentes e vários variações do mesmo tipo de componente. Por exemplo, pode ter várias instâncias de carga de trabalho diferentes, logicamente separados, que representam aplicações diferentes. Pode utilizar estes tipos de diferentes componentes e as instâncias para em última análise de compilação de vDC.

[![4]][4]

A arquitetura de alto nível anterior de um vDC mostra os tipos de diferentes componentes utilizados em zonas diferentes da topologia hub-spokes a realizar. O diagrama mostra várias partes da arquitetura de componentes de infraestrutura.

Acesso uma boa prática (para um DC no local ou vDC) direitos e privilégios devem ser baseado no grupo. Lidar com a grupos, em vez de utilizadores individuais ajuda a manter as políticas de acesso consistente entre equipas e ajuda na minimizando a erros de configuração. Atribuir e remover utilizadores e de grupos adequados ajuda a manter os privilégios de um utilizador específico atualizado.

Cada grupo de função deve ter um prefixo exclusivo nos respetivos nomes, facilitando a identificar o grupo está associado que carga de trabalho. Por exemplo, uma carga de trabalho que aloja um serviço de autenticação pode ter grupos com o nome *AuthServiceNetOps, AuthServiceSecOps, AuthServiceDevOps e AuthServiceInfraOps.* Da mesma forma para centralizada funções, ou funções não relacionado com um serviço específico, foi de ser precedido pela "Corp" *CorpNetOps* por exemplo.

Muitas organizações utilizam uma variação dos seguintes grupos para fornecer uma repartição das funções principais:

-   O *grupo central de TI (rede empresarial)* tem os direitos de propriedade para controlar os componentes de infraestrutura (por exemplo, rede e segurança) e, por conseguinte, tem de ter a função de Contribuidor na subscrição (e o controlo do hub) e a rede direitos de contribuinte em spokes a realizar. Organização de grandes dimensões divide frequentemente cópias de segurança estas responsabilidades de gestão entre várias equipas, tais como; um grupo de operações de rede (CorpNetOps) (com o foco exclusivo em redes) e um grupo de operações de segurança (CorpSecOps) (responsável pela política de segurança e de firewall). Neste caso específico, dois grupos diferentes tem de ser criado para a atribuição de uma destas funções personalizadas.
-   O *dev & grupo de teste (AppDevOps)* tem a responsabilidade de implementar cargas de trabalho (aplicações ou serviços). Este grupo de leva a função de contribuinte de Máquina Virtual para implementações IaaS de e/ou um ou funções mais contribuinte de PaaS (consulte [funções incorporadas para controlo de acesso em funções do Azure][Roles]). Opcionalmente, a equipa de desenvolvimento e teste poderá ter de têm visibilidade em políticas de segurança (NSGs) e políticas de encaminhamento (UDR) dentro do hub ou um spoke específico. Por conseguinte, para além das funções de contribuinte para cargas de trabalho, este grupo também terá da função de leitor de rede.
-   O *operação e manutenção de grupo (CorpInfraOps ou AppInfraOps)* responsável por gerir cargas de trabalho na produção. Este grupo tem de ser um contribuinte de subscrição nas cargas de trabalho em quaisquer subscrições de produção. Algumas organizações poderão avaliar também se necessitam de um grupo de equipa da suporte adicional escalamento, com a função de contribuinte de subscrição na produção e na subscrição concentrador central, para corrigir possíveis problemas de configuração no ambiente de produção.

Um vDC está estruturada para que os grupos criados para os grupos centrais de TI gerir o hub tem grupos correspondentes ao nível da carga de trabalho. Além de gerir recursos de hub apenas os grupos de TI centrais conseguirão controlar o acesso externo e permissões de nível superior na subscrição. No entanto, os grupos de carga de trabalho conseguirão controlar permissões da respetiva VNet independentemente em departamentos de TI centrais e de recursos.

O vDC tem de ser particionados em segurança alojar vários projetos em linha-de-empresas diferentes (LOBs). Todos os projetos requerem diferentes ambientes isolados (Dev UAT, produção). As subscrições do Azure separadas para cada um destes ambientes de fornecer isolamento natural.

[![5]][5]

O diagrama anterior mostra a relação entre projetos da organização, os utilizadores, grupos e ambientes onde os componentes do Azure estão implementados.

Normalmente, no departamento de TI um ambiente (ou camada) é um sistema em que várias aplicações estão implementadas e executadas. As grandes empresas utilizam um ambiente de desenvolvimento (onde as alterações originalmente efetuadas e testadas) e um ambiente de produção (o que os utilizadores finais utilizam). Esses ambientes são separados, muitas vezes, com vários ambientes entre-las para permitir a implementação faseada (implementação) de teste, teste e a reversão em caso de problemas. Arquiteturas de implementação variam significativamente, mas normalmente, o processo básico de começando desenvolvimento (desenvolvimento) e termina em produção (PROD) ainda é seguido.

Uma arquitetura comuns para estes tipos de ambientes de múltiplas camadas é constituído por DevOps (desenvolvimento e teste), UAT (teste) e ambientes de produção. As organizações podem tirar partido único ou vários inquilinos do Azure AD para definir o acesso e direitos a estes ambientes. O diagrama anterior mostra um cenário em que dois diferentes inquilinos do Azure AD são utilizados: um para DevOps e UAT e outra exclusivamente para produção.

A presença do Azure AD diferentes inquilinos imponha a separação entre ambientes. O mesmo grupo de utilizadores (por exemplo, TI Central) tem de autenticar utilizando outro URI para aceder a um inquilino diferente do AD modificar as funções ou permissões de ambientes de DevOps ou de produção de um projeto. A presença de autenticação de utilizador diferentes para aceder a diferentes ambientes reduz possíveis falhas e outros problemas causados por erros humanos.

#### <a name="component-type-infrastructure"></a>Tipo de componente: infraestrutura
Este tipo de componente está onde reside a maior parte da infraestrutura de suporte. Também é onde o centralizada departamento de TI segurança, e/ou as equipas de conformidade gastam a maior parte do respetivo tempo.

[![6]][6]

Componentes de infraestrutura de fornecem um interconnection entre os diferentes componentes de um vDC e estão presentes no hub e a spokes a realizar. A responsabilidade para gerir e manter os componentes de infraestrutura, normalmente, é atribuída para o Centro IT e/ou equipa de segurança.

Uma das tarefas principais a equipa de infraestrutura de TI é garantir a consistência de esquemas de endereço IP em toda a empresa. Espaço atribuído para as necessidades de vDC para estar consistente e não sobrepostas com endereços IP privados atribuídos nas redes no local de endereços de IP privado.

Enquanto NAT nos routers de limite no local ou em ambientes do Azure pode evitar conflitos de endereços IP, adiciona complicações para os componentes de infraestrutura. Simplicidade de gestão é um dos principais objetivos de vDC, pelo que a utilização de NAT para processar as questões de IP não é uma solução recomendada.

Componentes de infraestrutura contenham as seguintes funcionalidades:

-   [**Serviços de identidade e de diretório**][AAD]. Acesso para cada tipo de recurso no Azure é controlado por uma identidade armazenada no serviço de diretório. O serviço de diretório armazena não apenas a lista de utilizadores, mas também os direitos de acesso a recursos numa subscrição do Azure específica. Estes serviços podem existir apenas na nuvem ou podem ser sincronizadas com a identidade no local, armazenada no Active Directory.
-   [**Rede virtual**][VPN]. Redes virtuais são um dos componentes principais de um vDC e permitem-lhe criar um limite de isolamento de tráfego na plataforma do Azure. Uma rede Virtual é composta por um único ou vários segmentos de rede virtual, cada um com um prefixo de rede IP específico (uma sub-rede). A rede Virtual define uma área de perímetro internos onde as máquinas virtuais de IaaS e PaaS serviços podem estabelecer comunicações privadas. VMs (e os serviços de PaaS) de uma rede virtual não pode comunicar diretamente com as VMs (e os serviços de PaaS) numa rede virtual diferente, mesmo se ambas as redes virtuais são criadas ao mesmo cliente, na mesma subscrição. Isolamento é uma propriedade crítica que garante a VMs de cliente e comunicação permanece privada dentro de uma rede virtual.
-   [**UDR**][UDR]. O tráfego numa rede Virtual é encaminhado por predefinição com base na tabela de encaminhamento do sistema. Uma rota definir utilizador é uma tabela de encaminhamento personalizada que os administradores de rede pode associar a um ou mais sub-redes para substituir o comportamento da tabela de encaminhamento do sistema e a definir um caminho de comunicação dentro de uma rede virtual. A presença de UDRs garante que esse tráfego de saída do trânsito spoke através de VMs personalizadas específicas e/ou os dispositivos de rede Virtual e Balanceadores de carga presente no hub e no spokes a realizar.
-   [**NSG**][NSG]. Um grupo de segurança de rede é uma lista de regras de segurança que atuam como tráfego de filtragem de portas de origens de IP, IP de destino, protocolos, portas de origem de IP e IP de destino. O NSG pode ser aplicado a uma sub-rede, um cartão de Virtual NIC associado uma VM do Azure, ou ambos. Os NSGs são essenciais para implementar um controlo de fluxo correto no hub e no spokes a realizar. O nível de segurança proporcionada pelo NSG é uma função de que portas o abrir e para que fim. Os clientes devem aplicar filtros adicionais-VM com firewalls baseada no anfitrião, tais como IPtables ou a Firewall do Windows.
-   **DNS**. A resolução do nome de recursos em VNets de um vDC é fornecida através de DNS. O âmbito da resolução do nome de DNS predefinido está limitado para a VNet. Normalmente, um serviço DNS personalizado tem de ser implementado no hub como parte dos serviços comuns, mas os consumidores de serviços DNS principais residirem no spoke. Se necessário, os clientes podem criar uma estrutura hierárquica de DNS com a delegação de zonas DNS para a spokes a realizar.
-   [* * Subscrição] [ SubMgmt] e [gestão de grupo de recursos][RGMgmt]* *. Uma subscrição define um limite de natural para criar vários grupos de recursos no Azure. Recursos numa subscrição são assembled em conjunto nos contentores lógicos com o nome de grupos de recursos. O grupo de recurso representa um grupo lógico para organizar os recursos de um vDC.
-   [**RBAC**][RBAC]. Através do RBAC, é possível papel organizacional do mapa, juntamente com direitos de acesso a recursos específicos do Azure, permitindo-lhe restringir os utilizadores apenas para um determinado subconjunto de ações. Utilizar o RBAC, pode conceder acesso ao atribuir a função adequada para os utilizadores, grupos e aplicações no âmbito relevante. O âmbito de uma atribuição de função pode ser uma subscrição do Azure, um grupo de recursos ou um único recurso. RBAC permite a herança de permissões. Uma função atribuída a um âmbito principal também concede acesso a subordinados contidos. Utilizar o RBAC, pode segregar funções e conceder apenas a quantidade de acesso aos utilizadores que precisam para desempenhar as suas funções. Por exemplo, utilize o RBAC para permitir que um empregado gerir máquinas virtuais numa subscrição, enquanto outro pode gerir bds SQL dentro da mesma subscrição.
-   [**O VNet Peering**][VNetPeering]. A funcionalidade fundamental utilizada para criar a infraestrutura de uma vDC é VNet Peering, um mecanismo que liga as duas redes virtuais (VNets) na mesma região através da rede do Centro de dados do Azure.

#### <a name="component-type-perimeter-networks"></a>Tipo de componente: Redes de perímetro
[Rede de perímetro] [ DMZ] componentes (também conhecido como uma rede DMZ) permitem-lhe fornecer conectividade de rede com no local ou com redes de centros de dados físicos, juntamente com qualquer conectividade para e da Internet. Também é onde equipas da rede e segurança das prováveis gastam a maior parte do respetivo tempo.

Os pacotes recebidos devem fluir através de aplicações de segurança no hub, como a firewall, IDS e IPS, antes de chegar aos servidores back-end no spokes a realizar. Pacotes de vinculado à Internet das cargas de trabalho também devem fluir através de aplicações de segurança na rede de perímetro para a imposição de política, inspeção e fins de auditorias, antes de deixarem a rede.

Componentes de rede de perímetro fornecem as seguintes funcionalidades:

-   [Redes virtuais][VNet], [UDR][UDR], [NSG][NSG]
-   [Dispositivo de rede Virtual][NVA]
-   [O Balanceador de carga][ALB]
-   [Gateway de aplicação][AppGW] / [WAF][WAF]
-   [IPs públicos][PIP]

Normalmente, o Centro IT e equipas de segurança têm responsabilidade de definição de requisitos e as operações das redes de perímetro.

[![7]][7]

O diagrama anterior mostra a imposição de dois perimeters com acesso à internet e uma rede no local, ambos residente no hub. Num hub único, a rede de perímetro à internet pode aumentar verticalmente para suportar grandes quantidades de LOBs, utilizando vários farms de Firewalls de aplicação Web (WAFs) e/ou as firewalls.

[**Redes virtuais** ] [ VNet] hub baseia-se normalmente uma vnet com várias sub-redes para alojar o tipo de diferentes dos serviços de filtragem e inspecionar o tráfego de ou à internet através de NVAs, WAFs e Gateways de aplicação do Azure.

[**UDR** ] [ UDR] utilizando UDR, os clientes podem implementar firewalls, IDS/IPS e outras aplicações virtuais e encaminhar o tráfego de rede através destas aplicações de segurança para a imposição de política de limite de segurança, auditoria e inspeção. UDRs podem ser criados no concentrador e spokes a realizar para garantir que o tráfego transits através da VMs personalizadas específicas, os dispositivos de rede Virtual e Balanceadores de carga utilizados pelo vDC. Para garantir que o tráfego gerado a partir de VMs residente no trânsito spoke para as aplicações virtuais corretas, uma UDR tem de ser definidas as sub-redes de spoke ao definir o endereço IP Front-end do Balanceador de carga interno como o next-hop. O Balanceador de carga interno distribui o tráfego interno para aplicações virtuais (conjunto de back-end de Balanceador de carga).

[![8]][8]

[**Aplicações virtuais de rede** ] [ NVA] no concentrador, a rede de perímetro com acesso à internet normalmente é gerida através de um farm de firewalls e/ou de Firewalls de aplicação Web (WAFs).

LOBs diferentes utilizam frequentemente muitas aplicações web, e estas aplicações têm tendência de sofrer de várias vulnerabilidades e explorações potenciais. As Firewalls de aplicações Web são um breed especial de produto utilizado para detetar ataques contra aplicações web (HTTP/HTTPS) em profundidade mais que uma firewall genérica. Comparado com a tecnologia de firewall tradição, WAFs tem um conjunto de funcionalidades específicas para proteger os servidores web interno contra ameaças.

Um farm de firewall é o grupo de firewalls trabalhar em conjunto com a mesma administração comuns, com um conjunto de regras de segurança para proteger as cargas de trabalho alojadas no spokes de realizar, controlar o acesso a locais e redes. Um farm de firewall tem inferior especializada software comparado com uma WAF, mas tem um âmbito de aplicação abrangente para filtrar e inspecionar qualquer tipo de tráfego de saída e entrada. Normalmente, farms de firewall são implementados no Azure através da rede Virtual aparelhos (NVAs), que estão disponíveis no Azure marketplace.

É recomendado utilizar um conjunto de NVAs para tráfego com origem na Internet, e outra para o tráfego com origem no local. A utilização de apenas um conjunto de NVAs para ambas é um risco de segurança, como fornece não perímetro de segurança entre os dois conjuntos de tráfego de rede. Utilizar NVAs separados reduz a complexidade das regras de segurança de verificação e torna-se a limpar as regras que correspondem ao que pedido de rede de entrada.

Mais grandes empresas gerir vários domínios. O DNS do Azure pode ser utilizado para alojar os registos DNS para um determinado domínio. Como exemplo, o endereço de IP Virtual (VIP) do Balanceador de carga externo do Azure (ou os WAFs) pode ser registado no registo de um registo de DNS do Azure.

[**Azure Load Balancer** ] [ ALB] Balanceador de carga do Azure oferece uma elevada disponibilidade do serviço de camada 4 (TCP, UDP), que pode distribuir o tráfego de entrada entre instâncias de serviço definido um conjunto com balanceamento de carga. O tráfego enviado para o Balanceador de carga a partir de pontos finais de front-end (pontos finais IP públicos ou pontos finais IP privados) pode ser redistribuído com ou sem tradução de endereços para um conjunto de conjunto de endereços IP back-end (exemplos que está a ser; Os dispositivos de rede Virtual ou VMs).

Balanceador de carga do Azure pode sonda o estado de funcionamento, bem como as várias instâncias de servidor e, quando uma sonda não consegue responder o Balanceador de carga para envio de tráfego para a instância de mau estado de funcionamento. Um vDC, temos a presença de um balanceador de carga externo no hub (por exemplo, a equilibrar o tráfego para NVAs) e na spokes a realizar (para efetuar tarefas, como o tráfego entre VMs diferentes de uma aplicação com várias camadas de balanceamento).

[**Gateway de aplicação** ] [ AppGW] Gateway de aplicação do Microsoft Azure é uma aplicação virtual dedicada, fornecendo o controlador de entrega de aplicações (ADC) como um serviço, oferece várias camada 7 balanceamento de carga capacidades para a sua aplicação. Permite-lhe otimizar a produtividade do web farm ao descarregar a terminação SSL exigente em termos de CPU para o gateway de aplicação. Proporciona também outras capacidades de encaminhamento de camada 7, incluindo a distribuição round robin de tráfego de entrada, a afinidade de sessão com base em cookies, o encaminhamento baseado no caminho do URL e a capacidade de alojar vários Web sites atrás de um Gateway de Aplicação individual. Também é fornecida uma firewall de aplicações Web (WAF) como parte do SKU da WAF do gateway de aplicação. Este SKU fornece proteção para as aplicações web de vulnerabilidades web e explorações comuns. O Gateway de Aplicação pode ser configurado como um gateway com acesso à Internet, gateway só interno ou uma combinação de ambos. 

[**Os IPs públicos** ] [ PIP] das funcionalidades do Azure algumas permitem-lhe associar pontos finais de serviço para um endereço IP público que permite ao seu recurso a partir da internet. Este ponto final utiliza a tradução de endereços de rede (NAT) para encaminhar o tráfego para o endereço interno e a porta na rede virtual do Azure. Este caminho é a forma primária para o tráfego externo passar para a rede virtual. Os endereços IP públicos podem ser configurados para determinar qual o tráfego é transmitido e como e onde é traduzido na rede virtual.

#### <a name="component-type-monitoring"></a>Tipo de componente: monitorização
Componentes de monitorização fornecem visibilidade e alertas de todos os outros tipos de componentes. Todas as equipas devem ter acesso a monitorização para os componentes e serviços têm acesso. Se tiver um equipas de suporte técnico ou operações de ajuda centralizada, seria precisam de se ter integrado acesso aos dados fornecidos por estes componentes.

Ofertas do Azure diferentes tipos de registo e monitorização dos serviços para controlar o comportamento do Azure recursos alojados. Governação e o controlo de cargas de trabalho no Azure é com base não apenas na recolher dados de registo, mas também a capacidade de ações de Acionador com base em eventos comunicados específicos.

Existem dois tipos principais de registos no Azure:

-   [**Registos de atividade** ] [ ActLog] (denominados também como "Registo operacional") fornecem informações aprofundadas as operações que foram executadas no recursos na subscrição do Azure. Estes registos de eventos de controlo plane para as suas subscrições de relatório. Todos os recursos do Azure produz os registos de auditoria.

-   [**Os registos de diagnóstico do Azure** ] [ DiagLog] são registos gerados por um recurso que fornecem dados avançados, frequentes sobre o funcionamento desse recurso. O conteúdo estes registos varia consoante o tipo de recurso.

[![9]][9]

Um vDC, é extremamente importante controlar os registos de NSGs, particularmente estas informações:

-   [**Registos de eventos**][NSGLog]: fornece informações sobre as regras do NSG são aplicadas para as VMs e as funções de instância com base no endereço MAC.
-   [**Os registos do contador**][NSGLog]: controla a quantidade de vezes que cada regra NSG foi executada para negar ou permitir o tráfego.

Todos os registos podem ser armazenados em contas do Storage do Azure para fins de cópia de segurança, de análise estático ou de auditoria. Quando os registos são armazenados uma conta de armazenamento do Azure, os clientes podem utilizar diferentes tipos de arquiteturas de obter, preparação, analisar e visualizar estes dados para comunicar o estado e estado de funcionamento dos recursos de nuvem.

As grandes empresas, devem já ter adquirido uma estrutura padrão para monitorização sistemas no local e podem expandir esse framework para integrar os registos gerados por implementações de nuvem. Para as organizações que pretendem manter todos os registo na nuvem, [Microsoft Operations Management Suite (OMS)] [ OMS] é uma escolha ideal. Quando o OMS estiver implementado como um serviço baseado na nuvem, pode colocá-lo em execução de imediato com o mínimo investimento em termos de serviços de infraestrutura. OMS também pode integrar com componentes do System Center, tais como o System Center Operations Manager para expandir os investimentos de gestão para a nuvem.

Análise de registos do OMS é um componente do framework OMS para ajudar a recolher, correlacionar, procurar e atuar sobre dados de registo e de desempenho gerados por sistemas operativos, aplicações, componentes de infraestrutura de nuvem. Proporciona aos clientes as informações operacionais em tempo real utilizando a pesquisa integrada e dashboards personalizados para analisar todos os registos em todas as suas cargas de trabalho uma vDC.

#### <a name="component-type-workloads"></a>Tipo de componente: cargas de trabalho
Componentes de carga de trabalho são onde residem os seus serviços e aplicações reais. Também é onde suas equipas de desenvolvimento de aplicações gastam a maior parte do respetivo tempo.

As possibilidades de carga de trabalho são verdadeiramente endless. Seguem-se alguns dos tipos possíveis de carga de trabalho:

**Aplicações de LOB interno**

Aplicações de linha de negócio são aplicações de computador críticas para a operação em curso de uma empresa. Aplicações de LOB tem alguns características comuns:

-   **Interativo**. Aplicações de LOB são interativas por natureza: dados são introduzidos e resultado/relatórios são devolvidos.
-   **Baseados nos dados**. Aplicações de LOB são dados que consomem muita com acesso frequente para as bases de dados ou outro armazenamento.
-   **Integrada**. LOB aplicações oferta integração com outros sistemas dentro ou fora da organização.

**Cliente web sites (com acesso à Internet ou interno à)** maioria das aplicações que interagem com a Internet são web sites. O Azure oferece a capacidade para executar um web site numa VM do IaaS ou a partir de um [Web Apps do Azure] [ WebApps] (PaaS) do site. As aplicações Web do Azure suporta a integração com as VNets que permitir a implementação das aplicações Web no spoke de um vDC. Com a integração de VNET, não precisa de expor um ponto final de Internet para as suas aplicações, mas pode utilizar o recursos não internet encaminhável endereços privados da sua VNet privada em vez disso.

**Análise/dados de grande** quando dados tem de aumentar verticalmente para um volume muito grande, bases de dados poderão não dimensionar corretamente. Tecnologia de Hadoop oferece um sistema para executar consultas distribuídas em paralelo em grande número de nós. Os clientes têm a opção para executar cargas de trabalho de dados em VMs do IaaS ou PaaS ([HDInsight][HDI]). HDInsight suporta a implementação para uma VNet com base na localização, pode ser implementada para um cluster de um spoke de vDC.

**Eventos e mensagens**
[Event Hubs do Azure] [ EventHubs] é um serviço de ingestão de telemetria de hiper escala que recolhe, transforma e armazena milhões de eventos. Como uma plataforma de transmissão em fluxo distribuída, oferece baixa latência e retenção de tempo configurável, permitindo-lhe incorporar quantidades enormes de telemetria no Azure e ler dados a partir de várias aplicações. Com os Event Hubs, um fluxo único pode suportar pipelines em tempo real e baseada no batch.

Uma nuvem de elevada disponibilidade fiável mensagens do serviço entre aplicações e serviços, podem ser implementadas através de [Service Bus do Azure] [ ServiceBus] que oferece assíncrono as mensagens mediadas entre cliente e servidor, juntamente com estruturados as capacidades primeiro na primeira out (FIFO) e publicação de mensagens.

[![10]][10]

### <a name="multiple-vdc"></a>Vários vDC
Até ao momento, este artigo tem concentra-se num único vDC, que descreve a arquitetura que contribuir para um vDC resiliente e componentes básicos. Funcionalidades do Azure, tais como a carga do Azure balanceador, NVAs, conjuntos de disponibilidade, conjuntos de dimensionamento, juntamente com outros mecanismos contribuem para um sistema que lhe permitem criar sólidas níveis de SLA nos serviços de produção.

No entanto, um único vDC está alojado numa única região e é vulnerável a principais, que pode afetar nessa região completa. Os clientes que pretendem alcançar a elevada SLAs tem proteger os serviços através de implementações do mesmo projeto no vDCs dois (ou mais), colocados em regiões diferentes.

Para além das preocupações de SLA, existem vários cenários comuns onde a implementação de vários vDCs faz sentido:

-   Presença de regionais/Global
-   Recuperação Após Desastre
-   Mecanismo de divert tráfego entre DC

#### <a name="regionalglobal-presence"></a>Presença de regionais/Global
Os centros de dados do Azure estão presentes em várias regiões em todo o mundo. Quando selecionar múltiplos centros de dados do Azure, os clientes tem de considerar dois fatores relacionados com a: as distâncias geográficas e latência. Os clientes têm de avaliar geográfica distância entre o vDCs e a distância entre o vDC e os utilizadores finais, para oferecer a melhor experiência de utilizador.

A região do Azure onde estão alojadas vDCs também tem de estar em conformidade com os requisitos de regulamentação estabelecidos pelo qualquer jurisdiction legal sob as quais funciona a sua organização.

#### <a name="disaster-recovery"></a>Recuperação Após Desastre
A implementação de um plano de recuperação de desastre é vivamente relacionado com o tipo de carga de trabalho pretendido e a capacidade para sincronizar o estado da carga de trabalho entre vDCs diferentes. Idealmente, a maioria dos clientes pretendem sincronizar os dados de aplicação entre implementações em execução em dois vDCs diferentes para implementar um mecanismo de rápido ativação pós-falha. A maioria das aplicações são sensíveis à latência e que pode fazer com que potenciais tempo limite e atraso na sincronização de dados.

Sincronização ou heartbeat monitorização de aplicações no vDCs diferentes necessita de comunicação entre elas. Dois vDCs em regiões diferentes podem ser ligados através de:

-   ExpressRoute privada peering quando vDC centros estão ligados ao mesmo circuito do ExpressRoute
-   vários circuitos ExpressRoute ligado através da sua empresa principal e o mesh vDC ligado dos circuitos ExpressRoute
-   Ligações de VPN de site a Site entre os hubs de vDC em cada região do Azure

Normalmente, a ligação do ExpressRoute é o mecanismo de preferido devido a largura de banda superior e latência consistente quando transiting através do principal do Microsoft.

Não há nenhum receitas mágica para validar uma aplicação distribuída entre dois (ou mais) vDCs diferentes localizados em regiões diferentes. Os clientes devem executar testes de qualificação de rede para verificar a latência e largura de banda das ligações e de destino se a replicação de dados assíncrona ou síncrona é adequada e que o objetivo de tempo de ideal de recuperação (RTO) pode ser para as cargas de trabalho.

#### <a name="mechanism-to-divert-traffic-between-dc"></a>Mecanismo de divert tráfego entre DC
Uma técnica eficaz para divert de entrada de tráfego num DC baseia-se no DNS. [Traffic Manager do Azure] [ TM] utiliza o mecanismo de sistema de nomes de domínio (DNS) para direcionar o tráfego do utilizador final para o ponto de final público mais adequado num vDC específico. Através de sondas, Gestor de tráfego verifica periodicamente o estado de funcionamento do serviço de pontos finais públicos no vDCs diferentes e, em caso de falha desses pontos finais, encaminha automaticamente para o secundário vDC.

Gestor de tráfego funciona em pontos finais públicos do Azure e pode ser utilizado, por exemplo, para o tráfego para VMs do Azure e as aplicações Web no vDC adequada de controlo/divert. Gestor de tráfego é resiliente, mesmo face a uma falha de região do Azure completo e pode controlar a distribuição de tráfego do utilizador para pontos finais de serviço no vDCs diferentes com base em vários critérios (por exemplo, falha de um serviço num vDC específico, ou de selecionar o vDC com a menor latência de rede para o cliente).

### <a name="conclusion"></a>Conclusão
O Centro de dados do Virtual é uma abordagem para migração do Centro de dados para a nuvem que utiliza uma combinação de funcionalidades e capacidades para criar uma arquitetura dimensionável no Azure que maximiza a utilização de recursos de nuvem, reduzindo os custos e simplificar a governação de sistema. O conceito de vDC baseiam-se uma topologia hub-spokes realizar, fornecendo serviços partilhados comuns no hub e permitindo que as aplicações/cargas de trabalho específicas no spokes a realizar. Um vDC corresponde à estrutura das funções de empresa, onde diferentes departamentos (Central de TI, DevOps, operação e manutenção) funcionam em conjunto, cada um com uma lista de funções e as tarefas específica. Um vDC satisfaça os requisitos para uma migração "De comparação de precisão e Shift", mas também fornece muitas vantagens para implementações de nuvem nativo.

## <a name="references"></a>Referências
As seguintes funcionalidades foram abordadas neste documento. Clique nas hiperligações para saber mais.

| | | |
|-|-|-|
|Funcionalidades de rede|Balanceamento de Carga|Conectividade|
|[Redes virtuais do Azure][VNet]</br>[Grupos de segurança de rede][NSG]</br>[Registos de NSG][NSGLog]</br>[Encaminhamento definido pelo utilizador][UDR]</br>[Dispositivos de rede Virtual][NVA]</br>[Endereços IP públicos][PIP]|[Balanceador de carga do Azure (L3)][ALB]</br>[Gateway de aplicação (L7)][AppGW]</br>[Firewall de aplicações Web][WAF]</br>[Traffic Manager do Azure][TM] |[O VNet Peering][VNetPeering]</br>[Rede privada virtual][VPN]</br>[ExpressRoute][ExR]
|Identidade</br>|Monitorização</br>|Melhores práticas</br>|
|[Azure Active Directory][AAD]</br>[Autenticação Multifator][MFA]</br>[Controlos de acesso de Base de função][RBAC]</br>[Funções predefinidas do AAD][Roles] |[Registos de atividade][ActLog]</br>[Registos de diagnóstico][DiagLog]</br>[Microsoft Operations Management Suite][OMS]</br> |[Melhores práticas de redes de perímetro][DMZ]</br>[Gestão de subscrição][SubMgmt]</br>[Gestão de grupo de recursos][RGMgmt]</br>[Limites de subscrição do Azure][Limits] |
|Outros serviços do Azure|
|[Aplicações Web do Azure][WebApps]</br>[HDInsights (Hadoop)][HDI]</br>[Hubs de Eventos][EventHubs]</br>[Service Bus][ServiceBus]|



## <a name="next-steps"></a>Passos Seguintes
 - Explorar [VNet Peering][VNetPeering], a tecnologia de underpinning para estruturas de vDC hub- and -spoke
 - Implementar [AAD] [ AAD] para começar com [RBAC] [ RBAC] exploração
 - Desenvolver um modelo de gestão de subscrição e dos recursos e o RBAC para satisfazer os requisitos, a estrutura do modelo e as políticas da sua organização. Está a planear a atividade mais importante. Muito práticas, como planear reorganizations, fusões, novas linhas de produto, etc.

<!--Image References-->
[0]: ./media/networking-virtual-datacenter/redundant-equipment.png "exemplos de sobreposição de componente" 
[1]: ./media/networking-virtual-datacenter/vdc-high-level.png "exemplo de nível superior de vDC hub- and -spoke"
[2]: ./media/networking-virtual-datacenter/hub-spokes-cluster.png "cluster dos hubs e spokes realizar"
[3]: ./media/networking-virtual-datacenter/spoke-to-spoke.png "spoke-para-spoke"
[4]: ./media/networking-virtual-datacenter/vdc-block-level-diagram.png "diagrama de nível de bloco de vDC"
[5]: ./media/networking-virtual-datacenter/users-groups-subsciptions.png "utilizadores, grupos, subscrições e projetos"
[6]: ./media/networking-virtual-datacenter/infrastructure-high-level.png "diagrama da infraestrutura de alto nível"
[7]: ./media/networking-virtual-datacenter/highlevel-perimeter-networks.png "diagrama da infraestrutura de alto nível"
[8]: ./media/networking-virtual-datacenter/vnet-peering-perimeter-neworks.png "redes de perímetro e de VNet Peering"
[9]: ./media/networking-virtual-datacenter/high-level-diagram-monitoring.png "diagrama de alto nível para monitorização"
[10]: ./media/networking-virtual-datacenter/high-level-workloads.png "diagrama de alto nível para carga de trabalho"

<!--Link References-->
[Limits]: https://docs.microsoft.com/azure/azure-subscription-service-limits
[Roles]: https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles
[VNet]: https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview
[NSG]: https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg 
[VNetPeering]: https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview 
[UDR]: https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview 
[RBAC]: https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is
[MFA]: https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication
[AAD]: https://docs.microsoft.com/azure/active-directory/active-directory-whatis
[VPN]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways 
[ExR]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction 
[NVA]: https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha
[SubMgmt]: https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-subscription-governance 
[RGMgmt]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview
[DMZ]: https://docs.microsoft.com/azure/best-practices-network-security
[ALB]: https://docs.microsoft.com/azure/load-balancer/load-balancer-overview
[PIP]: https://docs.microsoft.com/azure/virtual-network/resource-groups-networking#public-ip-address
[AppGW]: https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction
[WAF]: https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview
[ActLog]: https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs 
[DiagLog]: https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs
[NSGLog]: https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log
[OMS]: https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview
[WebApps]: https://docs.microsoft.com/azure/app-service/
[HDI]: https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-introduction
[EventHubs]: https://docs.microsoft.com/azure/event-hubs/event-hubs-what-is-event-hubs 
[ServiceBus]: https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-overview
[TM]: https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview
