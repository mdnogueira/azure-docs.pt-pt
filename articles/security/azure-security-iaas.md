---
title: "Segurança melhores práticas para IaaS cargas de trabalho no Azure | Microsoft Docs"
description: " A migração de cargas de trabalho IaaS do Azure coloca oportunidades para reevaluate nosso estruturas "
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 02c5b7d2-a77f-4e7f-9a1e-40247c57e7e2
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: barclayn
ms.openlocfilehash: 376a3e47e5099aa4d74732e0b6ed14ed9af14091
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2017
---
# <a name="security-best-practices-for-iaas-workloads-in-azure"></a>Melhores práticas de segurança para cargas de trabalho IaaS no Azure

Como iniciados pensar sobre como mover as cargas de trabalho para a infraestrutura do Azure como um serviço (IaaS), provavelmente realizados que algumas considerações são familiares. Já poderá ter experiência proteger ambientes virtuais. Quando mover para o IaaS do Azure, pode aplicar o conhecimentos proteger ambientes virtuais e utilizar um novo conjunto de opções para ajudar a proteger os ativos.

Vamos começar por indicar que deve não Esperamos colocar recursos no local como um para um para o Azure. Novos desafios e as novas opções de colocar uma oportunidade para reevaluate existente deigns, ferramentas e processos.

A responsabilidade de segurança baseia-se no tipo de serviço em nuvem. O gráfico seguinte resume o equilíbrio de responsabilidade para Microsoft e:


![Áreas de responsabilidade](./media/azure-security-iaas/sec-cloudstack-new.png)


Iremos falar sobre algumas das opções disponíveis no Azure que pode ajudar a satisfazer os requisitos de segurança da sua organização. Tenha em atenção que os requisitos de segurança podem variar para diferentes tipos de cargas de trabalho. Não destas melhores práticas por si só, proteger os sistemas. Como tudo o resto em segurança, tem de escolher as opções apropriadas e ver como as soluções podem complementar entre si através do preenchimento de intervalos.

## <a name="use-privileged-access-workstations"></a>Utilizar acesso privilegiado estações de trabalho

As organizações, muitas vezes, enquadram-se prey para cyberattacks porque administradores efetuar ações durante a utilização de contas com direitos elevados. Normalmente, isto não é feito como mas porque a configuração existente e processos permitem-lo. A maioria destes utilizadores compreende o risco destas ações de um ponto de vista conceptual, mas ainda optar por fazê-los.

Fazer coisas como a verificação do e-mail e navegação na Internet parecem inofensivo suficiente. Mas poderá expor o contas elevadas para comprometer por atores maliciosos. Navegação atividades, especialmente crafted e-mails ou outras técnicas pode ser utilizado para obter acesso para a sua empresa. Recomendamos vivamente a utilização de estações de trabalho de gestão seguro (SAWs) para realizar todas as tarefas de administração do Azure. SAWs são uma forma de reduzir a exposição acidental comprometimento.

Estações de trabalho acesso privilegiado (PAWs) fornecem um sistema operativo dedicado para tarefas confidenciais que se encontra protegido contra ataques de Internet e vetores de ameaças. Separar estas tarefas confidenciais e as contas da utilização diária estações de trabalho e dispositivos fornece proteção forte. Esta separação limita o impacto de ataques de phishing e e das aplicações SO vulnerabilidades, vários ataques de representação, ataques de roubo de credenciais. (batimentos de tecla registo, de passagem do Hash e passagem da permissão)

A abordagem PAW é uma extensão com a prática recomendada e bem estabelecida para utilizar uma conta de administrador individualmente atribuída. A conta administrativa está separada de uma conta de utilizador padrão. Um PAW fornece uma estação de trabalho fidedigna para essas contas confidenciais.

Para obter mais orientações de informações e a implementação, consulte [estações de trabalho de acesso privilegiado](https://technet.microsoft.com/windows-server-docs/security/securing-privileged-access/privileged-access-workstations).

## <a name="use-multi-factor-authentication"></a>Utilize a autenticação Multifator

No passado, o perímetro de rede foi utilizado para controlar o acesso a dados empresariais. No mundo de nuvem primeiro, mobile primeiro, a identidade é a plane de controlo: utilizá-lo para controlar o acesso aos serviços de IaaS a partir de qualquer dispositivo. Pode também utilizado para obter visibilidade e conhecimentos aprofundados em onde e como os seus dados está a ser utilizados. Proteger a identidade digital dos utilizadores do Azure é fundamental para proteger as suas subscrições do roubo de identidade e outros cybercrimes.

Um dos passos mais vantajoso que pode tomar para proteger uma conta consiste em ativar a autenticação de dois fatores. A autenticação de dois fatores é uma forma de autenticar utilizando algo para além de uma palavra-passe. Ajuda a mitigar o risco de acesso por alguém que gere para obter a palavra-passe de alguém de outra pessoa.

[Multi-factor Authentication do Azure](../multi-factor-authentication/multi-factor-authentication.md) ajuda a salvaguardar o acesso a dados e aplicações cumprindo o pedido do utilizador para um processo de início de sessão simple. Fornece autenticação forte com uma variedade de opções de verificação fácil--chamada telefónica, mensagem de texto ou notificação de aplicação móvel. Os utilizadores escolher o método preferem.

A forma mais fácil de utilizar o multi-factor Authentication é a aplicação móvel Microsoft Authenticator que pode ser utilizada em dispositivos móveis com o Windows, iOS e Android. Com a versão mais recente do Windows 10 e a integração do Active Directory no local com o Azure Active Directory (Azure AD), [Windows Hello para empresas](../active-directory/active-directory-azureadjoin-passport-deployment.md) pode ser utilizado para totalmente integrada-início de sessão único aos recursos do Azure. Neste caso, o dispositivo Windows 10 é utilizado como segundo fator de autenticação.

Para contas que gerem a sua subscrição do Azure e para as contas que podem iniciar sessão para máquinas virtuais, utilizar o multi-factor Authentication dá-lhe um muito maior nível de segurança que utilizar apenas uma palavra-passe. Outras formas de autenticação de dois fatores poderão funcionar apenas bem, mas poderá ser complicada-los a implementar se não estiverem já em produção.

Captura de ecrã seguinte mostra algumas das opções disponíveis para o Azure multi-factor Authentication:

![Opções de autenticação Multifator](./media/azure-security-iaas/mfa-options.png)

## <a name="limit-and-constrain-administrative-access"></a>Limite e restringir o acesso administrativo

Proteger as contas que podem gerir a sua subscrição do Azure é extremamente importante. O compromisso de qualquer um dessas contas negates o valor de todos os outros passos que pode tomar para garantir a confidencialidade e integridade dos dados. Como recentemente ilustradas pelo [Edward Snowden](https://en.wikipedia.org/wiki/Edward_Snowden) ataques internos representam uma ameaça enorme para a segurança global de qualquer organização.

Avalie indivíduos para direitos administrativos pelos seguintes critérios de semelhantes a estas:

- Se efetuar tarefas que necessitam de privilégios administrativos?
- Como muitas vezes, as tarefas são executadas?
- Existe um motivo específico por que razão as tarefas não podem ser efetuadas por outro administrador em nome daqueles?

Documente todas as outras abordagens alternativas conhecidas para conceder o privilégio e por que motivo cada não é aceitável.

A utilização de administração just-in-time impede a existência desnecessária de contas com direitos elevados durante períodos quando esses direitos não são necessários. Contas de tem direitos elevados durante um período de tempo limitado, para que os administradores podem fazer as respetivas tarefas. Em seguida, esses direitos são removidos no final de um shift ou quando uma tarefa está concluída.

Pode utilizar [Privileged Identity Management](../active-directory/active-directory-privileged-identity-management-configure.md) para gerir, monitorizar e controlo de acesso na sua organização. Ajuda a ter em mente das ações que demoram indivíduos na sua organização. Implica igualmente administração just-in-time para o Azure AD, introduzindo o conceito de admins elegível. Estes são indivíduos que tenham contas com o potencial de possuir direitos de administrador. Estes tipos de utilizadores podem aceder através de um processo de ativação e de ter atribuídos direitos de administrador para um período de tempo limitado.


## <a name="use-devtest-labs"></a>Utilizar DevTest Labs

Utilizar o Azure para os ambientes de desenvolvimento e laboratórios permite às organizações ganhar agilidade ao desenvolvimento e teste adotando ausente atrasos que apresenta o aprovisionamento de hardware. Infelizmente, falta de familiaridade com o Azure ou um desejo para ajudar a agilizar a adoção poderá originar o administrador para ser excessivamente permissiva com atribuição de direitos. Este risco inadvertidamente poderá expor da organização a ataques internos. Alguns utilizadores poderão ser concedidos acesso muito mais do que devem ter.

O [Azure DevTest Labs](../devtest-lab/devtest-lab-overview.md) service utiliza [controlo de acesso em funções do Azure](../active-directory/role-based-access-control-what-is.md) (RBAC). Ao utilizar o RBAC, pode segregar funções na sua equipa em funções conceder apenas o nível de acesso necessário para os utilizadores para as respetivas tarefas. RBAC inclui funções predefinidas (proprietário, utilizador do laboratório e contribuinte). Mesmo pode utilizar estas funções para atribuir direitos para parceiros externos e simplificar significativamente a colaboração.

Uma vez que DevTest Labs utiliza RBAC, é possível criar adicionais, [funções personalizadas](../devtest-lab/devtest-lab-grant-user-permissions-to-specific-lab-policies.md). DevTest Labs não só simplifica a gestão de permissões, simplifica o processo de obtenção ambientes aprovisionados. Também o ajuda a lidar com outros típicos desafios de equipas que estão a funcionar em ambientes de desenvolvimento e teste. É necessária alguma preparação, mas a longo prazo, irá facilitar as coisas para a sua equipa.

Azure DevTest Labs funcionalidades incluem:

- Controlo administrativo sobre as opções disponíveis para os utilizadores. O administrador pode gerir centralmente as coisas como permitidos tamanhos de VM, o número máximo de VMs e quando são iniciadas VMs e encerramento.
- Automatização da criação do ambiente de laboratório.
- Controlo de custos.
- Distribuição simplificada de VMs para o trabalho de colaboração temporário.
- Self-Service que permite aos utilizadores aprovisionar os laboratórios utilizando os modelos.
- Gerir e limitação de consumo.

![Utilizar DevTest Labs para criar um laboratório](./media/azure-security-iaas/devtestlabs.png)

É associado com a utilização de DevTest Labs sem custos adicionais. A criação de laboratórios, políticas, modelos e artefactos é gratuita. Paga apenas os recursos do Azure utilizados em sua laboratórios, tais como máquinas virtuais, contas de armazenamento e redes virtuais.



## <a name="control-and-limit-endpoint-access"></a>Acesso de ponto final de controlo e o limite

Alojamento laboratórios ou sistemas de produção no Azure, significa que os sistemas têm de ser acessível a partir da Internet. Por predefinição, uma nova máquina virtual do Windows com a porta RDP acessível a partir da Internet e uma máquina virtual Linux tem de abrir a porta de SSH. Colocar os passos para pontos finais de limite exposto é necessário para minimizar o risco de acesso não autorizado.

As tecnologias no Azure podem ajudar a limitar o acesso a esses pontos finais administrativas. No Azure, pode utilizar [grupos de segurança de rede](../virtual-network/virtual-networks-nsg.md) (NSGs). Quando utilizar o Azure Resource Manager para a implementação, os NSGs limitam o acesso a partir de todas as redes para apenas os pontos finais de gestão (RDP ou SSH). Quando pensar NSGs, considere os ACLs do router. Pode utilizá-los para controlar totalmente a comunicação de rede entre vários segmentos das redes do Azure. Isto é semelhante à criação de redes em redes de perímetro ou outras redes isoladas. Estes não inspecionar o tráfego, mas podem ajudar a com a segmentação de rede.


No Azure, pode configurar um [VPN site a site](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) da sua rede no local. Uma VPN de site para site expande a sua rede no local para a nuvem. Isto dá-lhe outra oportunidade para utilizar os NSGs, porque também pode modificar os NSGs para não permitir acesso a partir de qualquer local à rede local. Em seguida, pode exigir que a administração é feita através da primeira ligação à rede através de VPN do Azure.

A opção de VPN de site para site poderá ser mais apelativo nos casos em que estão a alojar os sistemas de produção que estão estreitamente integrados com os recursos no local no Azure.

Em alternativa, pode utilizar o [ponto a site](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md) opção em situações em que pretende gerir sistemas que não precisam de acesso a recursos no local. Podem ser isolados esses sistemas na sua própria rede virtual do Azure. Os administradores podem VPN para o Azure alojada ambiente da sua estação de trabalho administrativa.

>[!NOTE]
>Pode utilizar qualquer uma das opções de VPN para reconfigurar as ACLs no NSGs para não permitir o acesso aos pontos finais de gestão da Internet.

É outra opção vale considerar um [Gateway de ambiente de trabalho remoto](../multi-factor-authentication/multi-factor-authentication-get-started-server-rdg.md) implementação. Pode utilizar esta implementação para ligar em segurança para servidores de ambiente de trabalho remoto através de HTTPS, ao aplicar controlos mais detalhados para essas ligações.

Funcionalidades que iriam tem acesso a incluir:

- Opções de administrador para limitar as ligações aos pedidos de sistemas específicos.
- Autenticação de smart card ou Azure multi-factor Authentication.
- Controlo sobre os sistemas que alguém pode ligar ao através do gateway.
- Controlo sobre o redirecionamento de dispositivo e o disco.

## <a name="use-a-key-management-solution"></a>Utilizar uma solução de gestão de chaves

A gestão de chaves segura é essencial para proteger os dados na cloud. Com [Cofre de chaves do Azure](../key-vault/key-vault-whatis.md), pode armazenar com segurança as chaves de encriptação e pequenos segredos como palavras-passe em módulos de segurança de hardware (HSMs). Para maior segurança, pode importar ou gerar chaves nos HSMs.

Processos de Microsoft as chaves de FIPS 140-2 de nível 2 HSMs validados (hardware e firmware). Auditoria e de monitor de utilização da chave com o registo do Azure: encaminhar os registos no sistema do Azure ou as informações de segurança e Event Management (SIEM) para adicional análise e deteção de ameaças.

Qualquer pessoa com uma subscrição do Azure pode criar e utilizar cofres de chaves. Embora a chave de cofre beneficiar os programadores e administradores de segurança, pode ser implementado e gerido por um administrador que é responsável pela gestão de serviços do Azure numa organização.


## <a name="encrypt-virtual-disks-and-disk-storage"></a>Encriptar discos virtuais e armazenamento em disco

[Azure Disk Encryption](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0) endereços a ameaça de roubo de dados ou exposição contra acesso não autorizado que é conseguido ao mover um disco. O disco pode ser ligado a outro sistema como uma forma de ignorar a outros controlos de segurança. Disco utiliza encriptação [BitLocker](https://technet.microsoft.com/library/hh831713) no Windows e do DM-Crypt no Linux para encriptar o sistema operativo e unidades de dados. Encriptação de disco do Azure integra-se com o Cofre de chaves para controlar e gerir as chaves de encriptação. Está disponível para standard VMs e as VMs com o armazenamento premium.

Para obter mais informações, consulte [Azure Disk Encryption no Windows e as VMs de IaaS Linux](azure-security-disk-encryption.md).

[Encriptação do serviço de armazenamento do Azure](../storage/common/storage-service-encryption.md) ajuda a proteger os seus dados inativos. Está ativada a nível da conta de armazenamento. Encripta dados à medida que é escrito nos nossos centros de dados e são desencriptado automaticamente aceder aos mesmos. Suporta os seguintes cenários:

- Acrescentar de encriptação de blobs de blocos, blobs e blobs de páginas
- Encriptação de arquivado VHDs e modelos colocados no Azure no local
- Encriptação de subjacentes SO e discos de dados para as VMs de IaaS que criou ao utilizar os seus VHDs

Antes de continuar com a encriptação de armazenamento do Azure, lembre-se de que duas limitações:

- Não está disponível em contas de armazenamento clássicas.
- Encripta apenas os dados escritos depois de encriptação está ativada.

## <a name="use-a-centralized-security-management-system"></a>Utilizar um sistema de gestão de segurança centralizada

Os servidores têm de ser monitorizados para aplicação de patches, configuração, eventos e as atividades que podem ser considerados como estando preocupações de segurança. Para resolver esses problemas, pode utilizar [Centro de segurança](https://azure.microsoft.com/services/security-center/) e [Operations Management Suite segurança e conformidade](https://azure.microsoft.com/services/security-center/). Ambas estas opções ponha a configuração no sistema operativo. Também fornecem monitorização da configuração da infraestrutura subjacente, como a configuração de rede e a utilização de aplicação virtual.

## <a name="manage-operating-systems"></a>Gerir sistemas operativos

Numa implementação IaaS, é ainda responsáveis pela gestão de sistemas que implementar, tal como qualquer outro servidor ou estação de trabalho no seu ambiente. Aplicação de patches, proteção, atribuições de direitos e todas as outras atividades relacionadas com a manutenção do seu sistema ainda são da responsabilidade do cliente. Para os sistemas que estão totalmente integrados com os recursos no local, pode querer utilizar as ferramentas e procedimentos que estiver a utilizar no local para coisas, como antivírus, antimalware, a aplicação de patches e cópia de segurança do mesmo.

### <a name="harden-systems"></a>Proteger os sistemas
Deve ser protegidas todas as máquinas virtuais no IaaS do Azure para que estes expõem apenas pontos finais de serviço que são necessários para as aplicações que são instaladas. Para máquinas virtuais do Windows, siga as recomendações que as linhas de base para a Microsoft publica o [Security Compliance Manager](https://technet.microsoft.com/solutionaccelerators/cc835245.aspx) solução.

Gestor de conformidade de segurança é uma ferramenta livre. Pode utilizá-lo para configurar e gerir os seus ambientes de trabalho, o Centro de dados tradicional e a nuvem privada e pública utilizando a política de grupo e o System Center Configuration Manager rapidamente.

Gestor de conformidade de segurança fornece pronto para implementar políticas e pacotes de configuração de gestão de configuração pretendida são testados. Estas linhas de base baseiam-se no [orientações de segurança do Microsoft](https://technet.microsoft.com/en-us/library/cc184906.aspx) recomendações e do setor melhores práticas. Ajudam a gerir a que se desviam configuração, os requisitos de conformidade de endereço e reduzir a ameaças de segurança.

Pode utilizar o Gestor de conformidade de segurança para importar a configuração atual dos seus computadores ao utilizar dois métodos diferentes. Em primeiro lugar, pode importar políticas de grupo baseada no Active Directory. Segundo, pode importar a configuração de "mestre dourada" computador de referência utilizando o [LocalGPO ferramenta](https://blogs.technet.microsoft.com/secguide/2016/01/21/lgpo-exe-local-group-policy-object-utility-v1-0/) fazer cópias de segurança na política de grupo local. Em seguida, pode importar a política de grupo local para o Gestor de conformidade de segurança.

Comparar as normas para melhores práticas industriais, personalize-os e criar novas políticas e pacotes de configuração de gestão de configuração pretendida. Linhas de base foram publicadas para todos os sistemas operativos suportados, incluindo o Windows 10 aniversário da Update e no Windows Server 2016.


### <a name="install-and-manage-antimalware"></a>Instalar e gerir antimalware

Para ambientes que estão alojados em separado do seu ambiente de produção, pode utilizar uma extensão de antimalware para ajudar a proteger as máquinas virtuais e serviços em nuvem. Integra-se com [Centro de segurança do Azure](../security-center/security-center-intro.md).


[Microsoft Antimalware](azure-security-antimalware.md) inclui funcionalidades como a proteção em tempo real a análise agendada, remediação do software maligno, assinatura, as atualizações, atualizações de motores amostras de relatórios, recolha de eventos de exclusão e [desuportedoPowerShell](https://msdn.microsoft.com/library/dn771715.aspx).

![Antimalware do Azure](./media/azure-security-iaas/azantimalware.png)

### <a name="install-the-latest-security-updates"></a>Instalar as atualizações de segurança mais recentes
Algumas das cargas de trabalho primeiro que os clientes mover para o Azure são laboratórios e sistemas de acesso externo. Se as máquinas de virtuais alojado no Azure alojar aplicações ou serviços que têm de estar acessíveis à Internet, ser vigilant sobre a aplicação de patches. Aplicar o patch para além do sistema operativo. Vulnerabilidades não corrigidas em aplicações de terceiros também podem levar a problemas que podem ser evitados se a gestão de patch boa estiver no local.

### <a name="deploy-and-test-a-backup-solution"></a>Implementar e testar uma solução de cópia de segurança

Tal como atualizações de segurança, uma cópia de segurança tem de ser tratados da mesma forma que lidar com qualquer outra operação. Isto é verdadeiro dos sistemas que fazem parte do seu ambiente de produção expandir para a nuvem. Sistemas de teste e desenvolvimento tem de seguir as estratégias de cópia de segurança que fornecem capacidades de restauro que são semelhantes às que os utilizadores têm ampliado habituados a, com base na sua experiência com ambientes no local.

Cargas de trabalho de produção movidas para o Azure, devem integrar com soluções de cópia de segurança existentes sempre que possível. Em alternativa, pode utilizar [cópia de segurança do Azure](../backup/backup-azure-arm-vms.md) para ajudar a resolver os requisitos de cópia de segurança.


## <a name="monitor"></a>Monitorizar

[Centro de segurança](../security-center/security-center-intro.md) fornece avaliação em curso do Estado de segurança dos seus recursos do Azure para identificar potenciais vulnerabilidades de segurança. Uma lista de recomendações orienta-o no processo de configuração de controlos necessários.

Os exemplos incluem:

- Aprovisionamento de antimalware para ajudar a identificar e remover software malicioso.
- Configurar grupos de segurança de rede e regras para controlar tráfego para máquinas virtuais.
- Aprovisionamento de firewalls de aplicação web para ajudar a proteger contra ataques em que o destino-as suas aplicações web.
- Implementar atualizações do sistema em falta.
- Endereçamento as configurações do SO que não correspondam a linhas de base recomendadas.

A imagem seguinte mostra algumas das opções que pode ativar no Centro de segurança.

![Políticas do Centro de segurança do Azure](./media/azure-security-iaas/security-center-policies.png)

[Operations Management Suite](../operations-management-suite/operations-management-suite-overview.md) é uma Microsoft baseado na nuvem IT solução de gestão que o ajuda a gerir e proteger no local e a infraestrutura de nuvem. Porque o Operations Management Suite é implementado como um serviço baseado na nuvem, podem ser implementado rapidamente e com o mínimo investimento em recursos de infraestrutura.

Novas funcionalidades são fornecidas automaticamente, poupando de manutenção em curso e atualizar os custos. Operations Management Suite também se integra com o System Center Operations Manager. Tem de diferentes componentes para o ajudar a gerir melhor as cargas de trabalho do Azure, incluindo um [segurança e conformidade](../operations-management-suite/oms-security-getting-started.md) módulo.

Pode utilizar as funcionalidades de segurança e conformidade no Operations Management Suite para ver informações sobre os seus recursos. As informações são organizadas em quatro categorias principais:

- **Domínios de segurança**: explorar ainda mais os registos de segurança ao longo do tempo. Aceder a avaliação de malware, atualizar a avaliação, as informações de segurança de rede, informações de identidade e acesso e computadores com eventos de segurança. Tire partido de um acesso rápido ao dashboard do Centro de segurança do Azure.
- **Problemas relevantes**: identificar rapidamente o número de problemas de Active Directory e a gravidade destes problemas.
- **As deteções (pré-visualização)**: identificar ataque padrões por visualizar alertas de segurança, como se acontecer relativamente aos seus recursos.
- **Ameaça intelligence**: identificar ataque padrões ao visualizar o número total de servidores com tráfego IP malicioso de saída, o tipo de ameaça malicioso e um mapa que mostra onde são feitos destes IPs.
- **Consultas de segurança comuns**: ver uma lista de consultas de segurança mais comuns que pode utilizar para monitorizar o seu ambiente. Quando clica destas consultas, o **pesquisa** painel abre-se e mostra os resultados para essa consulta.

A seguinte captura de ecrã mostra um exemplo das informações que pode apresentar Operations Management Suite.

![Linhas de base de segurança do Operations Management Suite](./media/azure-security-iaas/oms-security-baseline.png)

## <a name="next-steps"></a>Passos seguintes

* [Blogue da Equipa de Segurança do Azure](https://blogs.msdn.microsoft.com/azuresecurity/)
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx)
* [Padrões e melhores práticas de segurança do Azure](security-best-practices-and-patterns.md)
