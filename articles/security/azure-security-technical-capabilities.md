---
title: "As capacidades técnicas de segurança do Azure | Microsoft Docs"
description: "Saiba mais sobre serviços informáticos baseada na nuvem que incluem uma seleção grande de instâncias de computação e serviços que podem ser dimensionados acima e abaixo automaticamente para satisfazer as necessidades da sua aplicação ou a empresa."
services: security
documentationcenter: na
author: UnifyCloud
manager: swadhwa
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/01/2017
ms.author: TomSh
ms.openlocfilehash: 7288466cf31e180a16db18f8ddfe02ace3588a8d
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="azure-security-technical-capabilities"></a>Capacidades técnicas de segurança do Azure

Para ajudá-lo do Azure atual e potencial clientes compreenderem e utilizam as funcionalidades relacionadas com segurança vários disponíveis no e envolvente a plataforma do Azure, Microsoft desenvolveu uma série de documentos técnicos, descrições gerais de segurança, as melhores práticas, e Listas de verificação. Os tópicos em termos de volume e a profundidade de intervalo e são atualizados periodicamente. Este documento faz parte da série, conforme resumido na secção Abstrata abaixo. Mais informações sobre esta série de segurança do Azure podem ser encontradas em (URL).

## <a name="azure-platform"></a>Plataforma Azure

[Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/) é uma plataforma em nuvem é composta da infraestrutura e serviços de aplicações, com os serviços de dados integrada e análises avançadas e ferramentas de programador e serviços, alojado nos dados de nuvem pública da Microsoft centros. Os clientes utilizar o Azure para vários cenários, de computação básico, redes e armazenamento, para móveis e as diferentes capacidades e serviços de aplicação web, para cenários de nuvem completo, como a Internet das coisas, podem ser utilizados com tecnologias de código aberto e implementados como nuvem híbrida ou alojada num centro de dados de um cliente. O Azure oferece tecnologia de nuvem, como blocos modulares para ajudar a reduzir os custos, de empresas inovar rapidamente e gerir proativamente a sistemas. Quando criar ou migrar os recursos de IT para um fornecedor de nuvem, são depender capacidades da organização para proteger as suas aplicações e dados com os serviços e os controlos fornecem para gerir a segurança dos seus recursos baseados na nuvem.

Microsoft Azure é o único fornecedor de informática em nuvem que oferece uma plataforma de aplicações seguras e consistente e a infraestrutura-como-um-serviço para as equipas de trabalho dentro do respetivo skillsets de nuvem diferente e níveis de complexidade de projeto, com os serviços de dados integrada e análise desvendar intelligence a partir dos dados sempre que existir, entre a Microsoft e não Microsoft plataformas, abra estruturas e ferramentas, fornecer à escolha para integrar nuvem no local, bem como implementar serviços em nuvem do Azure nas instalações dos centros de dados. Como parte da nuvem da Microsoft fidedigna, os clientes baseiam-se no Azure para líder da indústria de segurança, fiabilidade, compatibilidade, privacidade e a rede grande de pessoas, parceiros e os processos para suportar as organizações na nuvem.

Com o Microsoft Azure, pode:

- Acelerar inovação com a nuvem.

- As decisões de negócio de energia & aplicações com o insights.

- Livremente a criar e implementar em qualquer lugar.

- Proteger os seus negócios.

## <a name="scope"></a>Âmbito

O ponto focal deste documento seja relativo a funcionalidades de segurança e a funcionalidade suportar os componentes de principais do Microsoft Azure, nomeadamente [armazenamento do Microsoft Azure](https://docs.microsoft.com/azure/storage/storage-introduction), [bases de dados do Microsoft Azure SQL](https://docs.microsoft.com/azure/sql-database/), [ Modelo de máquina virtual do Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/    )e a infraestrutura e ferramentas que gerem tudo. Foco este documento técnico no Microsoft Azure capacidades técnicas disponibilizadas como os clientes para satisfazer a respetiva função de proteger a segurança e privacidade dos respetivos dados.

A importância de compreender este modelo de responsabilidade partilhado é essencial para os clientes que estão a mover para a nuvem. Os fornecedores de nuvem oferecem considerável vantagens para os esforços de segurança e conformidade, mas as seguintes vantagens não absolve o cliente de proteger os seus utilizadores, aplicações e ofertas de serviço.

Para soluções de IaaS, o cliente é responsável ou tem uma responsabilidade para proteger e gerir o sistema operativo, a configuração de rede, aplicações, identidade, os clientes e dados partilhada.  Compilação de soluções de PaaS em implementações de IaaS, o cliente continua a ser responsável ou tem uma responsabilidade para proteger e gerir aplicações, identidade, os clientes e dados partilhada. Soluções de SaaS, Nonetheless, o cliente continua a ser responsável. Estes tem de garantir que os dados são classificados corretamente e partilham uma responsabilidade para gerir os seus utilizadores e dispositivos de ponto final.

Este documento não fornece cobertura de detalhado de qualquer um dos componentes de plataforma Microsoft Azure relacionados, tais como Azure Web Sites, Azure Active Directory, HDInsight, os Media Services e outros serviços que estão em camadas visível os componentes principais. Embora é fornecido um nível mínimo de informações gerais, leitores são considerados familiarizados com conceitos básicos do Azure, conforme descrito em outras referências a fornecidos pela Microsoft e incluído nas ligações fornecidas neste documento em branco.


## <a name="available-security-technical-capabilities-to-fulfil-user-customer-responsibility---big-picture"></a>Capacidades técnicas de segurança disponíveis para satisfazer responsabilidade do utilizador (cliente) - visão geral

Microsoft Azure fornece serviços que podem ajudar os clientes para satisfazer as necessidades de segurança, privacidade e conformidade. A imagem seguinte ajuda explicam vários serviços do Azure disponíveis para os utilizadores a criar uma infraestrutura de aplicação segura e compatível com base nas normas da indústria.

![Imagem de Big de capacidades técnicas de segurança disponíveis](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig1.png)

## <a name="manage-and-control-identity-and-user-access-protect"></a>Gerir e controlar o acesso de utilizador e de identidade (proteger)

Azure ajuda-o a proteger o negócio e as informações pessoais, permitindo gerir identidades de utilizador e as credenciais e controlar o acesso.

### <a name="azure-active-directory"></a>Do Azure Active Directory

Microsoft identidades e acessos soluções ajuda da gestão de IT proteger o acesso a aplicações e recursos em todo o datacenter empresarial e para a nuvem, permitindo níveis adicionais de validação, tais como autenticação multifator e políticas de acesso condicional. Monitorização de atividade suspeita através de segurança avançada, auditoria e relatórios de alerta ajuda a mitigar potenciais problemas de segurança. [Azure Active Directory Premium](https://docs.microsoft.com/azure/active-directory/active-directory-editions) fornece início de sessão único a milhares de nuvem aplicações (SaaS) e acesso a aplicações web é executado no local.

Vantagens de segurança do Azure Active Directory (AD) incluem a capacidade para:

- Criar e gerir uma identidade única para cada utilizador em toda a empresa híbrida, mantendo os utilizadores, grupos e dispositivos sincronizadas.

- Fornece acesso de início de sessão único para as aplicações, incluindo milhares de aplicações de SaaS previamente integradas.

- Ative a segurança de acesso da aplicação através da imposição baseada em regras multi-factor Authentication no local e as aplicações em nuvem.

- Aprovisionar o acesso remoto seguro a aplicações web no local, através do Proxy de aplicações do Azure AD.

[Portal do Azure active directory](http://aad.portal.azure.com/) está disponível uma parte no portal do azure. Este dashboard, pode obter uma descrição geral do Estado da sua organização e facilmente aprofundar gerir o diretório, utilizadores ou à aplicação o acesso.

![Do Azure Active Directory](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig2.png)

Seguintes são principais capacidades de gestão de identidade do Azure:

- Início de sessão único

- Multi-Factor Authentication

- Monitorização de segurança, alertas e relatórios com base na aprendizagem do computador

- Gestão de acesso e identidade do consumidor

- Registo de dispositivo

- Gestão de identidades privilegiadas

- Proteção de identidade

#### <a name="single-sign-on"></a>Início de sessão único

[Único início de sessão (SSO)](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/) significa que está a ser capaz de aceder a todas as aplicações e recursos que precisa para fins comerciais, ao iniciar sessão apenas uma vez com uma conta de utilizador único. Depois de iniciar sessão, pode aceder a todas as aplicações que precisar, sem ser necessário para autenticação (por exemplo, escreva uma palavra-passe) uma segunda vez.

Muitas organizações confiarem em software como um aplicações de serviço (SaaS), tais como o Office 365, a caixa e a Salesforce para a produtividade do utilizador final. Historicamente, equipa de TI necessário individualmente criar e atualizar as contas de utilizador em cada aplicação SaaS e os utilizadores tinham de memorizar uma palavra-passe para cada aplicação SaaS.

[Azure AD expande do Active Directory no local para a nuvem](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis), ativar que os utilizadores utilizem os seus conta organizacional principal não só para iniciar sessão para os respetivos dispositivos associados a um domínio e recursos da empresa, mas também todos os web e aplicações SaaS necessários para o trabalho.

Não apenas os utilizadores não é necessário gerir vários conjuntos de nomes de utilizador e palavras-passe, acesso de aplicação pode ser automaticamente aprovisionados ou anular o aprovisionamento com base nos grupos organizacionais e o respetivo estado como um empregado. [Azure AD apresenta os controlos de governação de acesso e segurança](https://docs.microsoft.com/azure/active-directory/active-directory-sso-integrate-saas-apps) que lhe permite gerir centralmente o acesso dos utilizadores em aplicações SaaS.

#### <a name="multi-factor-authentication"></a>Multi-Factor Authentication

[Azure multi-factor authentication (MFA)](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) é um método de autenticação que requer a utilização de mais do que um método de verificação e adiciona uma segunda camada crítica de segurança aos inícios de sessão de utilizador e de transações. [MFA ajuda a salvaguardar](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-how-it-works) acesso a dados e aplicações, cumprindo o pedido do utilizador para um processo de início de sessão simple. Fornece autenticação forte através de uma gama de opções de verificação — chamada telefónica, mensagem de texto ou aplicação móvel notificação ou da verificação de código e de terceiros OAuth tokens.

#### <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>Monitorização de segurança, alertas e relatórios com base na aprendizagem do computador

Monitorização de segurança e alertas e relatórios com base na aprendizagem de máquina identificam padrões de acesso inconsistentes podem ajudar a proteger a sua empresa. Pode utilizar o acesso do Azure Active Directory e os relatórios de utilização para ganhar visibilidade a integridade e a segurança do diretório da sua organização. Com esta informação, um administrador da directory melhor determinar onde pode ficar os possíveis riscos de segurança para que estes podem planear adequadamente a mitigar os riscos.

No portal do Azure ou através de [portal do Azure Active directory](http://aad.portal.azure.com/), [relatórios](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-guide) são categorizados das seguintes formas:

- Relatórios de anomalias – contém eventos que são identificadas como estando anómala de início de sessão. O nosso objetivo é fazer com que conhecimento essa atividade e permitem-lhe poder decidir sobre se um evento é suspeito.

- Relatórios de aplicação integrados – fornecem informações sobre a forma como as aplicações em nuvem estão a ser utilizadas na sua organização. Azure Active Directory oferece integração com milhares de aplicações em nuvem.

- Relatórios de erros indicar erros que podem ocorrer quando o aprovisionamento de contas em aplicações externas.

- Relatórios de utilizadores específicos – apresentam dispositivos/início de sessão nos dados de atividade para um utilizador específico.

- Os registos de atividade – conter um registo de eventos auditados todas as últimas 24 horas, últimos 7 dias ou últimos 30 dias e alterações ao grupo de atividade e atividade de registo e reposição de palavra-passe.

#### <a name="consumer-identity-and-access-management"></a>Gestão de acesso e identidade do consumidor

[O Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) é um serviço de gestão de identidade de elevada disponibilidade, global, para aplicações direcionadas para o consumidor preparada para centenas de milhões de identidades. Pode ser integrado entre plataformas móveis e Web. Os consumidores podem iniciar sessão em todas as suas aplicações através de experiências personalizáveis, utilizando as suas contas de redes sociais existentes ou criando novas credenciais.

Nos programadores da aplicação nos últimos, que pretendiam [inscrever-se e iniciar sessão em consumidores](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview) nas suas aplicações teriam de escrever o seu próprio código. E teriam de utilizar sistemas ou bases de dados no local para armazenar os nomes de utilizador e as palavras-passe. O Azure Active Directory B2C oferece a sua organização uma melhor forma de integrar a gestão de identidades de consumidor nas aplicações com a ajuda de uma plataforma segura, baseada em normas e um grande conjunto de políticas extensíveis.

Quando utiliza o Azure Active Directory B2C, os consumidores podem inscrever-se para as suas aplicações através das respetivas contas de redes sociais existentes (Facebook, Google, Amazon, LinkedIn) ou através da criação de novas credenciais (endereço de correio eletrónico e palavra-passe, ou nome de utilizador e palavra-passe).

Registo de dispositivo

[Registo de dispositivos do Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-device-registration-overview) é o alicerce da baseado nos dispositivos [acesso condicional](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-device-registration-overview) cenários. Quando um dispositivo é registado, o registo de dispositivos do Azure Active Directory fornece o dispositivo com uma identidade que é utilizado para autenticar o dispositivo quando o utilizador inicia sessão. O dispositivo autenticado e os atributos do dispositivo podem então ser utilizados para aplicar políticas de acesso condicional para aplicações alojadas na nuvem e no local.

Quando combinada com um [gestão de dispositivos móveis (MDM)](https://www.microsoft.com/itshowcase/Article/Content/588/Mobile-device-management-at-Microsoft) solução, como o Intune, os atributos do dispositivo no Azure Active Directory são atualizados com informações adicionais sobre o dispositivo. Isto permite criar regras de acesso condicional que impõem o acesso a partir de dispositivos para cumprir as normas de segurança e conformidade.

#### <a name="privileged-identity-management"></a>Gestão de identidades privilegiadas

[O Azure Active Directory (AD) Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) permite-lhe gere, controla e monitorize as identidades privilegiadas e aceder aos recursos no Azure AD, bem como outros serviços online da Microsoft, como o Office 365 ou o Microsoft Intune.

Por vezes, os utilizadores precisam realizar operações privilegiadas de recursos do Azure ou do Office 365 ou outras aplicações SaaS. Isto, muitas vezes, significa que as organizações têm atribuir-lhes acesso privilegiado permanente no Azure AD. Este é um risco de segurança crescente para recursos alojados na nuvem, porque as organizações não é possível monitorizar suficientemente que esses utilizadores estão a fazer com os respetivos privilégios de administrador. Além disso, se uma conta de utilizador com acesso privilegiado for comprometida, o que uma violação pode afetar a segurança de nuvem global. O Azure AD Privileged Identity Management ajuda a resolver este risco.

O Azure AD Privileged Identity Management permite-lhe:

- Veja os utilizadores que são administradores do Azure AD

- Ativar a pedido, "just in time" acesso administrativo ao Microsoft Online Services como o Office 365 e o Intune

- Obter relatórios sobre o histórico de acesso de administrador e as alterações em atribuições de administrador

- Obtenha alertas sobre o acesso a uma função com privilégios

#### <a name="identity-protection"></a>Proteção de identidade

[Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) é um serviço de segurança que fornece uma vista consolidada sobre eventos de risco e potenciais vulnerabilidades que afetam as identidades da organização. Proteção de identidade utiliza as capacidades de deteção de anomalias existente do Azure do Active Directory (disponíveis através de relatórios de atividade anómala do Azure AD) e introduz novos tipos de eventos de risco que consegue de detetar anomalias em tempo real.

## <a name="secured-resource-access-in-azure"></a>Acesso a recursos protegidos no Azure

Controlo de acesso no Azure inicia-se de uma perspetiva de faturação. O proprietário de uma conta do Azure, acedido, visitando o [Centro de contas do Azure](https://account.windowsazure.com/subscriptions), é o administrador de conta (AA). As subscrições são um contentor para faturação, mas também agir como um limite de segurança: cada subscrição tem um serviço de administrador (SA) que pode adicionar, remover e modificar recursos do Azure nessa subscrição utilizando o portal do Azure. A predefinição SA de uma nova subscrição é AA, mas o AA pode alterar a SA no Centro de contas do Azure.

![Acesso a recursos protegidos no Azure](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig3.png)

As subscrições também terem uma associação a um diretório. O diretório define um conjunto de utilizadores. Estes podem ser os utilizadores do trabalho ou escola que criou o diretório ou podem ser utilizadores externos (ou seja, Accounts Microsoft). Subscrições podem ser acedidas por um subconjunto dos utilizadores de diretório que atribuiu como administrador de serviço (SA) ou Coadministrador (AC); a única exceção é que, por motivos de legado, Accounts da Microsoft (anteriormente Windows Live ID) podem ser atribuídos como SA ou AC sem estar presente no diretório.

Segurança e orientado para empresas devem focar-se em fornecer aos funcionários as permissões exatas que precisam. Demasiados permissões podem expor uma conta para os atacantes. Permissões insuficientes significam que os funcionários não é possível obter o trabalho feito de forma eficiente. [Azure baseada em funções controlo de acesso (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is) ajuda a resolver este problema, oferecendo gestão de acesso detalhada para o Azure.

![Acesso a recursos protegidos ](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig4.png)

Utilizar o RBAC, pode segregar funções na sua equipa e conceder apenas a quantidade de acesso aos utilizadores que precisam para desempenhar as suas funções. Em vez de dar everybody sem restrições permissões na sua subscrição do Azure ou recursos, pode permitir que apenas determinadas ações. Por exemplo, utilize o RBAC para permitir que um empregado gerir máquinas virtuais numa subscrição, enquanto outro pode gerir bases de dados SQL dentro da mesma subscrição.

![Acesso a recursos protegidos na Azure(RBAC)](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig5.png)

## <a name="azure-data-security-and-encryption-protect"></a>Encriptação e de segurança dos dados do Azure (proteger)

Uma das chaves para proteção de dados na nuvem é contabilidade para os Estados possíveis em que os dados poderão ocorrer e que controlos estão disponíveis para esse Estado. Para dados do Azure segurança encriptação melhores práticas e recomendações ser à volta de Estados dos dados seguintes.

- Em rest: Isto inclui informações de todos os objetos de armazenamento, contentores e tipos de que existem estaticamente no suporte de dados físico, ser-torção ou optical disco.

- Em trânsito: Quando a dados está a ser transferidos entre componentes, localizações ou programas, tal como através da rede, entre um barramento de serviço (a partir de no local para a nuvem e vice-versa, incluindo as ligações híbridas, tais como o ExpressRoute), ou durante um processo de entrada/saída, é considerar como estando em movimento.

### <a name="encryption--rest"></a>Encriptação @ rest

Para alcançar a encriptação inativa, cada uma das seguintes opções:

Suporte, pelo menos, um dos modelos de encriptação recomendada detalhados na tabela seguinte para encriptar dados.

| Modelos de encriptação |  |  |  |
| ----------------  | ----------------- | ----------------- | --------------- |
| Encriptação do servidor | Encriptação do servidor | Encriptação do servidor | Encriptação de cliente
| Utilizando o serviço gerido chaves de encriptação do lado do servidor | Encriptação do lado do servidor utilizando as chaves de Customer-Managed no Cofre de chaves do Azure | Encriptação do lado do servidor utilizando as chaves de cliente gerido no local |
| • Os fornecedores de recursos do azure efetuarem as operações de encriptação e desencriptação <br> • Microsoft gere as chaves <br>• A funcionalidade completa de nuvem | • Os fornecedores de recursos do azure efetuarem as operações de encriptação e desencriptação<br>• Cliente controla chaves através do Cofre de chaves do Azure<br>• A funcionalidade completa de nuvem | • Os fornecedores de recursos do azure efetuarem as operações de encriptação e desencriptação <br>• Cliente controla chaves no local <br> • A funcionalidade completa de nuvem| • Serviços do azure não podem ver dados desencriptados <br>• Clientes manter chaves no local (ou noutras secure arquivos). As chaves não estão disponíveis aos serviços do Azure <br>• Reduzido a funcionalidade de nuvem|

### <a name="enabling-encryption-at-rest"></a>Ativar a encriptação de Inativos

**Identificar os dados de arquivos de todas as localizações**

É o objetivo de encriptação de Inativos para encriptar todos os dados. Se o fizer, elimina a possibilidade de em falta dados importantes ou todas as localizações persistentes. Enumerar todos os dados armazenados pela sua aplicação. 

> [!Note] 
> Não apenas "dados de aplicação" ou "PII', mas a conta de quaisquer dados relacionados com a aplicação, incluindo os metadados (subscrição mapeamentos informações de contrato, PII).

Considere que estiver a utilizar para armazenar dados de arquivos. Por exemplo:

- Armazenamento externo (por exemplo, SQL Azure Document DB, HDInsights, Data Lake, etc.)

- Armazenamento temporário (qualquer cache local que inclua dados de inquilino)

- Cache de memória (pode ser inserida o ficheiro de paginação.)

### <a name="leverage-the-existing-encryption-at-rest-support-in-azure"></a>Tirar partido da encriptação existente no suporte de rest no Azure

Para cada loja, utilizar, tirar partido da encriptação existente no suporte de Rest.

- Storage do Azure: Consulte [encriptação do serviço de armazenamento do Azure para dados Inativos](https://docs.microsoft.com/azure/storage/storage-service-encryption),

- SQL Azure: Consulte [a encriptação de dados transparente (TDE) SQL sempre encriptado](https://msdn.microsoft.com/library/mt163865.aspx)

- VM & Local de armazenamento de disco ([Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption))

Para armazenamento de disco da VM e Local utilizar Azure Disk Encryption se suportado:

IaaS

Devem utilizar serviços com as VMs de IaaS (Windows ou Linux) [Azure Disk Encryption](https://microsoft.sharepoint.com/teams/AzureSecurityCompliance/Security/SitePages/Azure%20Disk%20Encryption.aspx) para encriptar os volumes que contêm dados de cliente.

PaaS v2

Os serviços executados em PaaS v2 utilizar o Service Fabric pode utilizar encriptação de disco do Azure para o conjunto de dimensionamento da Máquina Virtual [VMSS] para encriptar as respetivas VMs de v2 PaaS.

PaaS v1

Atualmente, a Azure Disk Encryption não é suportada no PaaS v1. Por conseguinte, tem de utilizar encriptação de nível de aplicação para encriptar dados persistentes inativos.  Isto inclui, mas não está limitado a dados da aplicação, ficheiros temporários, os registos e informações de falhas.

A maioria dos serviços devem tentar tirar partido da encriptação de um fornecedor de recursos de armazenamento. Alguns serviços têm de fazer encriptação explícita, por exemplo, qualquer persistente material de chaves (certificados, raiz / mestre de chaves) deve ser armazenado no Cofre de chaves.

Se suportar a encriptação do lado do serviço com chaves gerida pelo cliente tem de haver uma forma do cliente obter a chave para nós. A forma recomendada e suportada fazê-lo através da integração com o Cofre de chaves do Azure (AKV). Neste caso, os clientes podem adicionar e gerir as respetivas chaves no Cofre de chaves do Azure. Um cliente pode aprender a utilizar AKV através de [introdução ao Cofre de chaves](http://go.microsoft.com/fwlink/?linkid=521402).

Para integrar com o Cofre de chaves do Azure, teria de adicionar código para pedir uma chave de AKV quando for necessário para desencriptação.

- Consulte [Cofre de chaves do Azure – passo](https://blogs.technet.microsoft.com/kv/2015/06/02/azure-key-vault-step-by-step/) para informações sobre como integrar com AKV.

Se suportar chaves de cliente gerido, tem de fornecer um UX para o cliente especificar que o Cofre de chaves (ou URI do Cofre de chave) para utilizar.

Como a encriptação de Inativos envolve a encriptação do anfitrião, os dados de infraestrutura e de inquilino, a perda das chaves devido uma falha de sistema ou atividade maliciosa pode significar todos os dados encriptados é perdido. Consequentemente, é essencial que a sua solução de Rest a encriptação tem uma história de recuperação de desastre abrangente sejam resiliente a falhas de sistema e a atividade maliciosa.

Serviços que implementam a encriptação de Inativos são normalmente ainda suscetíveis às chaves de encriptação ou a dados a ser deixados não encriptada na unidade de anfitrião (por exemplo, no ficheiro de página do SO anfitrião.) Por conseguinte, os serviços tem de garantir que o volume do anfitrião para os seus serviços é encriptado. Para facilitar esta computação equipa ativou a implementação de encriptação de anfitrião, que utiliza [Bitlocker](https://technet.microsoft.com/library/dn306081.aspx) NKP e extensões para o serviço de DCM e o agente para encriptar o volume anfitrião.

A maioria dos serviços são implementados em VMs do Azure standard. Devem obter desses serviços [anfitrião encriptação](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) automaticamente quando computação ativa o mesmo. Para serviços em execução na computação clusters geridos anfitrião encriptação é ativada automaticamente como Windows Server 2016 é implementado.

### <a name="encryption-in-transit"></a>Encriptação em trânsito

Proteger dados em trânsito deve fazer parte essencial da sua estratégia de proteção de dados. Uma vez que os dados são movidos anterior e descritos de diversas localizações, a recomendação geral é que utilize sempre protocolos SSL/TLS para trocar dados entre localizações diferentes. Em algumas circunstâncias, poderá pretender isolar o canal de comunicação completa entre o local e nuvem infraestrutura através da utilização de uma rede privada virtual (VPN).

Para dados mover entre a infraestrutura no local e o Azure, deve considerar as proteções de adequada, tais como HTTPS ou VPN.

Para organizações que precisam de proteger o acesso a partir de várias estações de trabalho localizados no local para o Azure, utilize [VPN de site para site do Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-site-to-site-create).

Para organizações que precisam de proteger o acesso a partir de uma estação de trabalho localizado no local para o Azure, utilize [ponto a Site VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-point-to-site-create).

Conjuntos de dados maiores podem ser movidos através de uma ligação WAN de alta velocidade dedicada, tais como [ExpressRoute](https://azure.microsoft.com/services/expressroute/). Se optar por utilizar o ExpressRoute, também pode encriptar os dados ao nível da aplicação utilizando [SSL/TLS](https://support.microsoft.com/kb/257591) ou outros protocolos para maior proteção.

Se interage com o Storage do Azure através do Portal do Azure, todas as transações ocorrem através de HTTPS. [API REST do Storage](https://msdn.microsoft.com/library/azure/dd179355.aspx) HTTPS também pode ser utilizado para interagir com a ativação pós-falha [Storage do Azure](https://azure.microsoft.com/services/storage/) e [SQL Database do Azure](https://azure.microsoft.com/services/sql-database/).

As organizações que falham para proteger dados em trânsito sejam mais suscetíveis de [ataques man-in-the-middle](https://technet.microsoft.com/library/gg195821.aspx), [escutas](https://technet.microsoft.com/library/gg195641.aspx)e hijacking de sessão. Estes ataques podem ser o primeiro passo na obtendo acesso aos dados confidenciais.

Pode saber mais sobre a opção de VPN do Azure ao ler o artigo [planeamento e design para o Gateway de VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design).

### <a name="enforce-file-level-data-encryption"></a>Impor a encriptação de dados ao nível do ficheiro

[O Azure RMS](https://technet.microsoft.com/library/jj585026.aspx) utiliza políticas de encriptação, identidade e autorização para ajudar a proteger os seus ficheiros e e-mail. O Azure RMS funciona em vários dispositivos — telemóveis, tablets e PCs ao proteger dentro da sua organização e fora da sua organização. Esta capacidade é possível porque o Azure RMS adiciona um nível de proteção permanece com os dados, mesmo quando sai dos limites da organização.

Quando utilizar o Azure RMS para proteger os seus ficheiros, estiver a utilizar a criptografia de norma da indústria com suporte completo de [FIPS 140-2](http://csrc.nist.gov/groups/STM/cmvp/standards.html). Ao tirar partido do Azure RMS para proteção de dados, tem a garantia que a proteção permanece com o ficheiro, mesmo que seja copiado para armazenamento que não se encontra sob o controlo de TI, tal como um serviço de armazenamento em nuvem. O mesmo ocorre para os ficheiros partilhados através de e-mail, o ficheiro está protegido como um anexo a uma mensagem de e-mail, com instruções sobre como abrir o anexo protegido.
Quando planear a adoção do Azure RMS, recomendamos o seguinte:

- Instalar o [aplicação de partilha RMS](https://technet.microsoft.com/library/dn339006.aspx). Esta aplicação integra-se com o Office aplicações através da instalação de um escritório suplemento para que os utilizadores possam facilmente proteger ficheiros diretamente.

- Configurar aplicações e serviços suportam o Azure RMS

- Criar [modelos personalizados](https://technet.microsoft.com/library/dn642472.aspx) que reflete os requisitos de negócio. Por exemplo: um modelo de dados secretos superiores que devem ser aplicados em todos os principal segredo relacionados com mensagens de correio eletrónico.

As organizações que são fracas no [classificação de dados](http://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) e proteção de ficheiros pode ser mais suscetível a fuga de dados. Sem proteção de ficheiros adequado, as organizações não conseguirão obter informações empresariais, monitorizar abusos e impedir o acesso malicioso a ficheiros.

> [!Note]
> Pode saber mais sobre o Azure RMS ao ler o artigo [introdução ao Azure Rights Management](https://technet.microsoft.com/library/jj585016.aspx).

## <a name="secure-your-application-protect"></a>Proteger a sua aplicação (proteger)
Enquanto é responsável por proteger a infraestrutura e plataforma em que a aplicação for executada no Azure, é da responsabilidade do cliente para proteger a sua própria aplicação. Por outras palavras, tem de desenvolver, implementar e gerir o seu código da aplicação e o conteúdo de forma segura. Sem isto, o código da aplicação ou o conteúdo pode ainda ser vulnerável a ameaças.

### <a name="web-application-firewall-waf"></a>Firewall de aplicações Web (WAF)
[Firewall de aplicações Web (WAF)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) é uma funcionalidade do [Gateway de aplicação](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) que fornece proteção centralizada das suas aplicações web de vulnerabilidades e explorações comuns.

A firewall de aplicações Web baseia-se nas regras dos [conjuntos de regras de núcleo OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 ou 2.2.9. Cada vez mais, as aplicações Web são alvo de ataques maliciosos que exploram vulnerabilidades conhecidas comuns. Destas vulnerabilidades, são frequentes os ataques de injeção de SQL, scripting entre sites, entre muitas outras. Impedir este tipo de ataques ao código das aplicações constitui um desafio e exige uma manutenção, correção e monitorização rigorosas em várias camadas da topologia da aplicação. Uma firewall de aplicações Web centralizada ajuda a simplificar em muito a gestão da segurança e confere aos administradores de aplicações uma maior garantia de proteção contra as ameaças ou intrusões. Uma solução WAF também pode reagir mais rapidamente a uma ameaça de segurança ao corrigir uma vulnerabilidade conhecida numa localização central, em vez de proteger cada uma das aplicações Web individualmente. Os gateways de aplicações existentes podem ser facilmente convertidos num gateway de aplicação com firewall de aplicações Web ativada.

Algumas das vulnerabilidades Web comuns contra as quais a firewall de aplicações Web protege incluem:

- Proteção contra injeção de SQL

- Proteção contra scripting entre sites

- Proteção contra Ataques Web comuns, como, por exemplo, injeção de comandos, contrabando de pedidos HTTP, divisão de respostas HTTP e ataques remotos de inclusão de ficheiros

- Proteção contra violações de protocolo HTTP

- Proteção contra anomalias de protocolo HTTP, como agente de utilizador de anfitrião e cabeçalhos de aceitação em falta

- Prevenção de contra bots, crawlers e scanners

- Deteção de aplicação configurações incorretas comuns (ou seja, o Apache, IIS, etc.)

> [!Note]
> Para uma lista de regras mais detalhada e os respetivos proteções, consulte o seguinte [principal conjuntos de regras](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview#core-rule-sets):

O Azure também oferece várias funcionalidades de fácil utilização para ajudar a proteger o tráfego de entrada e saído para a sua aplicação. Azure também ajuda os clientes proteger o respetivo código de aplicação, fornecendo externamente fornecidos funcionalidade para analisar a sua aplicação web quanto a vulnerabilidades.

- [Configurar a autenticação do Azure Active Directory para a sua aplicação](https://azure.microsoft.com/blog/azure-websites-authentication-authorization/)


- [Proteger o tráfego para a sua aplicação, permitindo Transport Layer Security (TLS/SSL) - HTTPS](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl)

    - [Forçar todo o tráfego de entrada através de ligação HTTPS](http://microsoftazurewebsitescheatsheet.info/)

  - [Ativar segurança de transporte Strict (HSTS)](http://microsoftazurewebsitescheatsheet.info/#enable-http-strict-transport-security-hsts)


- [Restringir o acesso à sua aplicação ao endereço IP do cliente](http://microsoftazurewebsitescheatsheet.info/#filtering-traffic-by-ip)

- [Restringir o acesso à sua aplicação ao comportamento do cliente - frequência de pedido e simultaneidade](http://microsoftazurewebsitescheatsheet.info/#dynamic-ip-restrictions)

- [Analisar o código de aplicação web quanto a vulnerabilidades utilizando a análise do Tinfoil Security](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/)

- [Configurar a autenticação mútua do TLS para exigir certificados de cliente para ligar à sua aplicação web](https://docs.microsoft.com/azure/app-service/app-service-web-configure-tls-mutual-auth)

- [Configurar um certificado de cliente para a utilização da sua aplicação para ligar de forma segura aos recursos externos](https://azure.microsoft.com/blog/using-certificates-in-azure-websites-applications/)

- [Remover os cabeçalhos de servidor padrão para evitar ferramentas a partir do fingerprinting a sua aplicação](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/)

- [Ligar a aplicação de forma segura com os recursos numa rede privada através da VPN ponto a Site](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)

- [Ligar a aplicação de forma segura com os recursos numa rede privada a utilizar ligações híbridas](https://docs.microsoft.com/azure/app-service/app-service-hybrid-connections)

App Service do Azure utiliza a mesma solução Antimalware utilizada por máquinas virtuais e serviços Cloud do Azure. Para obter mais informações sobre esta consulte a nossa [Antimalware documentação](https://docs.microsoft.com/azure/security/azure-security-antimalware).

## <a name="secure-your-network-protect"></a>Proteger a sua rede (proteger)
Microsoft Azure inclui uma infraestrutura robusta de rede para suportar a sua aplicação e os requisitos de conectividade do serviço. Conectividade de rede é possível entre os recursos localizados no Azure, no local e Azure alojadas recursos e de e para a Internet e o Azure.

O [infraestrutura de rede do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-networking-guidelines) permite-lhe ligar de forma segura a recursos do Azure uns aos outros com [redes virtuais (VNets)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview). Uma VNet é uma representação da sua própria rede na nuvem. Uma VNet é um isolamento lógico da rede em nuvem do Azure dedicado à sua subscrição. Pode ligar VNets para as suas redes no local.

![Proteger a sua rede (proteger)](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig6.png)

Se precisar de controlo de acesso de nível de rede básica (com base no endereço IP e os protocolos TCP ou UDP), em seguida, pode utilizar [grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg). Um grupo de segurança de rede (NSG) é um pacote de monitorização de estado básico filtragem firewall e permite-lhe controlar o acesso com base num [5 cadeias de identificação](https://www.techopedia.com/definition/28190/5-tuple).

Redes do Azure suporta a capacidade para personalizar o comportamento de encaminhamento de tráfego de rede nas redes virtuais do Azure. Pode fazê-lo através da configuração [rotas definidas pelo utilizador](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) no Azure.

[Imposição do túnel](https://www.petri.com/azure-forced-tunneling) é um mecanismo que pode utilizar para se certificar de que os seus serviços não são permitidos para iniciar uma ligação para dispositivos na Internet.

Suporte do Azure dedicada a conectividade da ligação WAN para a sua rede no local e uma rede Virtual do Azure com [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction). A ligação entre o Azure e o seu site utiliza uma ligação dedicada que passam para a Internet pública. Se a aplicação do Azure está em execução em vários datacenters, pode utilizar [Traffic Manager do Azure](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview) para pedidos de rota de utilizadores inteligentemente entre instâncias da aplicação. Também pode encaminhar o tráfego para serviços não em execução no Azure se que estejam acessíveis a partir da Internet.

## <a name="virtual-machine-security-protect"></a>Segurança de máquina virtual (proteger)

[Máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/) permite-lhe implementar uma vasta gama de soluções de informática de uma forma seja ágil. Com suporte do Microsoft Windows, Linux, Microsoft SQL Server, Oracle, IBM, SAP e Serviços BizTalk do Azure, pode implementar qualquer carga de trabalho, qualquer linguagem, em praticamente qualquer sistema operativo.

Com o Azure, pode utilizar [antimalware software](https://docs.microsoft.com/azure/security/azure-security-antimalware) dos fornecedores de segurança, tais como Microsoft, da Symantec, Micro de tendência e Kaspersky para proteger as máquinas virtuais de ficheiros maliciosos, adware e outras ameaças.

Antimalware da Microsoft para máquinas virtuais e serviços Cloud do Azure é uma funcionalidade de proteção em tempo real que ajuda a identificar e remover vírus, spyware e outro software malicioso. Antimalware da Microsoft fornece configuráveis alertas ao conhecido software malicioso ou indesejável, tentar instalar-se ou executar nos seus sistemas do Azure.

[Cópia de segurança do Azure](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup) é uma solução dimensionável, que protege os dados de aplicação com zero investimento de capital e mínimo operativo os custos. Os erros das aplicações podem danificar os dados e os erros humanos podem introduzir erros nas suas aplicações. Com a cópia de segurança do Azure, as máquinas virtuais que executam o Windows e Linux estão protegidas.

[O Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) ajuda a orquestrar a replicação, ativação pós-falha e recuperação de cargas de trabalho e aplicações para que estão disponíveis a partir de uma localização secundária se a sua localização principal ficar inativo.

## <a name="ensure-compliance-cloud-services-due-diligence-checklist-protect"></a>Certifique-se de conformidade: serviços devido a lista de verificação diligence em nuvem (proteger)

Microsoft desenvolvida [a Cloud Services devida Diligence lista de verificação de](https://aka.ms/cloudchecklist.download) para ajudar as organizações exercer diligence devida tenha em conta uma mudança para a nuvem. Fornece uma estrutura para uma organização de qualquer tamanho e tipo — privadas empresas e organizações de setor pública, incluindo government em todos os níveis e nonprofits — para identificar os seus próprios desempenho, serviço, gestão de dados e os objetivos de governação e requisitos. Isto permite-lhes para comparar as ofertas de fornecedores de serviços de nuvem diferente, basicamente que formam a base para um contrato de serviço em nuvem.

A lista de verificação fornece uma arquitetura alinhar a cláusula por cláusula com um novo internacional padrão para contratos de serviço de nuvem, ISO/IEC 19086. Esta norma oferece um conjunto unificado de considerações para as organizações ajudar a tomar decisões sobre a adoção de nuvem e criar um zero comuns para comparar as ofertas de serviços em nuvem.

A lista de verificação promove uma mudança exaustivamente vetted na nuvem, fornecendo uma abordagem consistente e repetida e documentação de orientação estruturada para escolher um fornecedor de serviços em nuvem.

A adoção de nuvem já não é simplesmente uma decisão de tecnologia. Porque os requisitos de lista de verificação touch em todos os aspetos de uma organização, servem para convene todas as chaves internos-decisores — o CIO e CISO, bem como informações legais, profissionais de gestão, aprovisionamento e a conformidade de risco. Isto aumenta a eficiência do zero as decisões relativas à raciocínio som, reduzindo assim a probabilidade de roadblocks imprevistos adoção e processos de tomada de decisão.

Além disso, a lista de verificação:

- Expõe tópicos de debate chave para decisores no início do processo de adoção de nuvem.

- Suporta o negócio detalhado em debates sobre regulamentos e os objetivos da organização para privacidade, informações de identificação pessoal (PII) e segurança dos dados.

- Ajuda as organizações a identificar problemas potenciais que podem afetar um projeto de nuvem.

- Fornece um conjunto de perguntas, consistente com o mesmos termos, definições, métricas e materiais a entregar para cada fornecedor, para simplificar o processo de comparação de ofertas de fornecedores de serviços de nuvem diferente.

## <a name="azure-infrastructure-and-application-security-validation-detect"></a>Azure validação de segurança da infraestrutura e da aplicação (detetar)

[Segurança operacionais do Azure](https://docs.microsoft.com/azure/security/azure-operational-security) refere-se os serviços, controlos e funcionalidades disponíveis para os utilizadores para proteger os seus dados, aplicações e outros recursos no Microsoft Azure.

![validação de segurança (detetar)](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig7.png)

Segurança operacionais do Azure é construída numa estrutura que incorpora o conhecimento adquirido através de uma várias funcionalidades que são exclusivas para a Microsoft, incluindo o Microsoft Security Development Lifecycle (SDL), o programa de Centre de resposta de segurança do Microsoft e a deteção de ameaças atuais.

### <a name="microsoft-operations-management-suiteoms"></a>Microsoft operations management suite(OMS)

[Microsoft Operations Management Suite (OMS)](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) é a solução de gestão de IT para a nuvem híbrida. Utilizado individualmente ou para expandir a implementação existente do System Center, OMS dá-lhe a flexibilidade máxima e o controlo de gestão baseado na nuvem da sua infraestrutura.

![Microsoft operations management suite(OMS)](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig8.png)

Com o OMS, pode gerir qualquer instância em nenhuma nuvem, incluindo no local, o Azure, AWS, Windows Server, o Linux, VMware e OpenStack, um custo mais baixo que soluções competitivos. Incorporado para o mundo primeiro de nuvem, o OMS oferece uma nova abordagem para gerir a sua empresa que é a forma mais rápida e mais económica para cumprir os novos desafios de negócio e acomodar novas cargas de trabalho, aplicações e ambientes de nuvem.

### <a name="log-analytics"></a>Log analytics

O [Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics) oferece serviços de monitorização para o OMS, ao recolher dados de recursos geridos num repositório central. Estes dados podem incluir dados de eventos ou de desempenho ou dados personalizados fornecidos através da API. Depois de recolhidos, os dados estão disponíveis para alertas, análises e exportação.

![Log analytics](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig9.png)

Este método permite-lhe consolidar os dados a partir de uma variedade de origens de, pelo que pode combinar dados a partir do seu serviços do Azure com existentes no local ambiente. Também separa claramente a recolha dos dados das ações tomadas em relação aos mesmos, para que todas as ações estejam disponíveis para todos os tipos de dados.

### <a name="azure-security-center"></a>Centro de segurança do Azure

O [Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro) ajuda-o a evitar, detetar e responder a ameaças com uma maior visibilidade e controlo sobre a segurança dos seus recursos Azure. Fornece gestão de políticas e monitorização de segurança integrada nas suas subscrições do Azure, ajuda a detetar ameaças que caso contrário podem passar despercebidas e funciona com um ecossistema abrangente de soluções de segurança.

O Centro de Segurança analisa o estado de segurança dos seus recursos Azure para identificar potenciais vulnerabilidades de segurança. Uma lista de recomendações orienta-o no processo de configuração de controlos necessários.

Os exemplos incluem:

- Aprovisionamento de antimalware para ajudar a identificar e remover o software malicioso

- Configurar regras para controlar tráfego para VMs e os grupos de segurança de rede

- Aprovisionamento de firewalls de aplicação Web para ajudar a proteger-se contra ataques que visam as suas Web Apps

- Implementação de atualizações do sistema em falta

- Abordagem de configurações de SO que não correspondam às linhas de base recomendadas

O Centro de Segurança recolhe, analisa e integra automaticamente os dados de registo a partir dos recursos do Azure, da rede e das soluções de parceiros, como os programas antimalware e as firewalls. Quando são detetadas ameaças, é criado um alerta de segurança. Os exemplos incluem a deteção de:

- VMs comprometidas a comunicar com endereços IP maliciosos conhecidos

- Software maligno avançado detetado ao utilizar a comunicação de erros do Windows

- Ataques de força bruta contra VMs

- Alertas de segurança a partir de programas antimalware e firewalls integrados

### <a name="azure-monitor"></a>Monitor do Azure

[Monitor do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview) fornece indicações para informações sobre tipos específicos de recursos. Oferece visualização, consulta, encaminhamento, alertas, escala automática e automatização nos dados da infraestrutura do Azure (registo da atividade) e cada individuais (registos de diagnóstico) de recursos do Azure.

Aplicações em nuvem são complexas com várias partes mover. A monitorização fornece dados para se certificar de que a aplicação permanece cópias de segurança e em execução em bom estado. Também o ajuda a stave desativar potenciais problemas ou resolver passado aqueles.

![Monitor do Azure](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig10.png) além disso, pode utilizar os dados de monitorização para obter conhecimentos aprofundados sobre a sua aplicação. Conhecimentos podem ajudar a melhorar o desempenho da aplicação ou maintainability ou automatizar ações que caso contrário necessitem intervenção manual.

Auditoria de segurança de rede é vital para detetar vulnerabilidades de rede e garantir a conformidade com a segurança de TI e o modelo de regulamentação de governação. Vista de grupo de segurança, pode obter o grupo de segurança de rede configurado e regras de segurança, bem como as regras de segurança eficaz. Com a lista de regras aplicadas, poderá determinar as portas que estão abertas e ss vulnerabilidade de rede.

### <a name="network-watcher"></a>Observador de rede

[Observador de rede](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-watcher) é um serviço regional que lhe permite monitorizar e diagnosticar condições a um nível de rede no, a e do Azure. Diagnóstico de rede e ferramentas de visualização disponíveis com o observador de rede ajudam-na compreender, diagnosticar e obter informações sobre a sua rede no Azure. Este serviço inclui a captura de pacotes, o salto seguinte, o fluxo IP verificar, a vista de grupo de segurança, os registos de fluxo NSG. A monitorização ao nível do cenário fornece uma vista de ponto a ponto dos recursos de rede, ao contrário de monitorização de recursos de rede individuais.

### <a name="storage-analytics"></a>Análise de armazenamento

[Análise de armazenamento](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) pode armazenar métricas que incluem dados de capacidade e estatísticas agregadas de transações sobre pedidos para um serviço de armazenamento. As transações são reportadas ao ambos o nível de operação de API, bem como a nível de serviço de armazenamento e a capacidade é comunicada a nível de serviço de armazenamento. Dados de métricas, podem ser utilizados para analisar a utilização do serviço de armazenamento, diagnosticar problemas com pedidos efetuados contra o serviço de armazenamento e para melhorar o desempenho das aplicações que utilizam um serviço.

### <a name="application-insights"></a>Informações de aplicação

[Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) é um serviço de gestão de desempenho de aplicações (APM) extensível para programadores de web em várias plataformas. Utilize-o para monitorizar a sua aplicação Web online. Além de detetar automaticamente anomalias de desempenho, Inclui as ferramentas de análise poderosas para ajudar a diagnosticar problemas e para saber o que fazem os utilizadores com a sua aplicação. Foi concebido para o ajudar a melhorar continuamente o desempenho e a usabilidade. Funciona para aplicações em diversas plataformas, incluindo .NET, Node.js e J2EE, alojadas no local ou na cloud. Integra-se com o processo de devOps e pontos de ligação para uma várias ferramentas de desenvolvimento.

Monitoriza:

- **Taxas de pedidos, tempos de resposta e taxas de falhas** - saiba quais são as páginas mais populares, em que alturas do dia e onde estão localizados os seus utilizadores. Veja que páginas têm o melhor desempenho. Se os tempos de resposta e as taxas de falhas aumentarem quando há mais pedidos, é possível que tenha um problema relativo a recursos.

- **Taxas de dependência, tempos de resposta e taxas de falhas** - veja se os serviços externos o estão a atrasar.

- **Exceções** - analisar as estatísticas agregadas, ou escolha instâncias específicas e explorar o rastreio da pilha e os pedidos relacionados. São reportadas exceções do servidor e do browser.

- **Vistas de página e desempenho de carga** - reportados pelos browsers dos utilizadores.

- **Chamadas AJAX de páginas web** -taxas, tempos de resposta e taxas de falha.

- **Contagens de sessões e de utilizador.**

- **Contadores de desempenho** dos computadores de servidor Windows ou Linux, como CPU, memória e utilização de rede.

- **Diagnóstico do anfitrião** do Docker ou do Azure.

- **Registos de rastreio de diagnóstico** da sua aplicação - para que possa correlacionar eventos de rastreio a pedidos.

- **Métricas e eventos personalizados** escrever pessoalmente o código de cliente ou servidor, para controlar eventos empresariais, tais como vendidos, ou jogos won.
Normalmente, a infraestrutura da sua aplicação é composta por vários componentes, como uma máquina virtual, uma conta do Storage e uma rede virtual, ou uma aplicação Web, uma base de dados, um servidor de base de dados e serviços de terceiros. Não vê estes componentes como entidades separadas. Em vez disso, vê-os como partes relacionadas e interdependentes de uma única entidade. Deve implementá-los, geri-los e monitorizá-los como um grupo. [O Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) permite-lhe trabalhar com os recursos na sua solução como um grupo.

Pode implementar, atualizar ou eliminar todos os recursos da sua solução numa operação única e coordenada. Utiliza um modelo para a implementação e esse modelo pode funcionar para ambientes diferentes, como de teste e produção. O Resource Manager fornece funcionalidades de segurança, auditoria e etiquetagem para o ajudar a gerir os recursos após a implementação.

**As vantagens de utilizar o Gestor de recursos**

O Resource Manager oferece várias vantagens:

- Pode implementar, gerir e monitorizar todos os recursos da sua solução como um grupo, em vez de os processar individualmente.

- Pode implementar repetidamente a solução durante todo o ciclo de vida de desenvolvimento e ter a confiança de que os recursos são implementados num estado consistente.

- Pode gerir a sua infraestrutura através de modelos declarativos em vez de scripts.

- Pode definir as dependências entre os recursos, para que sejam implementados na ordem correta.

- Pode aplicar o controlo de acesso a todos os serviços no seu grupo de recursos porque o Controlo de Acesso Baseado em Funções (RBAC) está integrado de forma nativa na plataforma de gestão.

- Pode aplicar etiquetas a recursos para organizar logicamente todos os recursos na sua subscrição.

- Pode clarificar a faturação da sua organização visualizando os custos de um grupo de recursos partilhando a mesma etiqueta.

> [!Note]
> O Resource Manager proporciona uma nova forma de implementar e gerir as suas soluções. Se utilizou o modelo de implementação e pretender anteriores para saber mais sobre as alterações, consulte [Gestor de recursos de compreender implementação clássica e](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model).

## <a name="next-steps"></a>Passos seguintes

Saber mais sobre a segurança ao ler alguns dos nossos tópicos de segurança aprofundados:

- [Auditoria e registo](https://www.microsoft.com/en-us/trustcenter/security/auditingandlogging)

- [Cibercrime](https://www.microsoft.com/en-us/trustcenter/security/cybercrime)

- [Conceção e segurança operacional](https://www.microsoft.com/en-us/trustcenter/security/designopsecurity)

- [Encriptação](https://www.microsoft.com/en-us/trustcenter/security/encryption)

- [Gestão de identidades e acesso](https://www.microsoft.com/en-us/trustcenter/security/identity)

- [Segurança da rede](https://www.microsoft.com/en-us/trustcenter/security/networksecurity)

- [Gestão de ameaças](https://www.microsoft.com/en-us/trustcenter/security/threatmanagement)
