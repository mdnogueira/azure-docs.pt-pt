---
title: "Isolamento na nuvem pública do Azure | Microsoft Docs"
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
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: a153d70e077ad63a042e76d0c4ae40e3cc067a2a
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2017
---
# <a name="isolation-in-the-azure-public-cloud"></a>Isolamento na nuvem pública do Azure
##  <a name="introduction"></a>Introdução
### <a name="overview"></a>Descrição geral
Para ajudá-lo do Azure atual e potencial clientes compreenderem e utilizam as funcionalidades relacionadas com segurança vários disponíveis no e envolvente a plataforma do Azure, Microsoft desenvolveu uma série de documentos técnicos, descrições gerais de segurança, as melhores práticas e listas de verificação.
Os tópicos em termos de volume e a profundidade de intervalo e são atualizados periodicamente. Este documento faz parte da série, conforme resumido da seguinte secção abstracta.

### <a name="azure-platform"></a>Plataforma do Azure
Azure é uma plataforma de serviço de nuvem aberta e flexível que suporte a seleção mais amplas de sistemas operativos, programação idiomas, estruturas, ferramentas, bases de dados e dispositivos. Pode, por exemplo:
- Execute os contentores de Linux com a integração do Docker;
- Criar aplicações com JavaScript, Python, .NET, PHP, Java e Node.js; e
- Compilação back-ends para iOS, Android e Windows dispositivos.

Microsoft Azure suporta as mesmas tecnologias milhões de programadores e profissionais de TI já dependem e fidedignidade.

Quando criar ou migrar os recursos IT à, um fornecedor de serviços de nuvem pública, é depender capacidades da organização para proteger as suas aplicações e dados com os serviços e os controlos fornecem para gerir a segurança dos seus recursos baseados na nuvem.

A infraestrutura do Azure foi concebida a partir da facilidade que as aplicações têm para alojar milhões de clientes em simultâneo e proporciona uma fundação fidedigna com a qual as empresas podem satisfazer as suas necessidades de segurança. Além disso, o Azure disponibiliza-lhe um vasto leque de opções de segurança configuráveis e a capacidade para controlá-las de modo a que possa personalizar a segurança e satisfazer os requisitos únicos das suas implementações. Este documento ajuda-o a cumpra estes requisitos.

### <a name="abstract"></a>Abstrato

Microsoft Azure permite-lhe executar aplicações e máquinas virtuais (VMs) na infraestrutura física partilhada. Uma das motivações económico prime para as aplicações em execução num ambiente de nuvem é a capacidade de distribuir o custo de recursos partilhados entre vários clientes. Esta prática de vários inquilinos melhora a eficiência ao multiplexação recursos entre diferentes clientes em custos baixos. Infelizmente, também introduz o risco de partilhar servidores físicos e outros recursos de infraestrutura para executar as suas aplicações confidenciais e VMs que poderão pertencer a um utilizador arbitrário e potencialmente malicioso.

Este artigo descreve como o Microsoft Azure fornece isolamento contra utilizadores maliciosos e não malicioso e funciona como um guia de arquitetura soluções de nuvem por oferta de várias opções de isolamento para arquitetos de TI. Este documento técnico centra-se na tecnologia de plataforma do Azure e controlos de segurança orientado para o cliente e não tenta SLAs de endereço, considerações de prática de DevOps e modelos de preços.

## <a name="tenant-level-isolation"></a>Isolamento de nível de inquilino
Uma das principais vantagens da nuvem informática é o conceito de uma infraestrutura partilhada, comum em clientes em simultâneo, originando economias de escala simples e. Este conceito é designado por vários inquilinos. A Microsoft trabalha continuamente para se certificar de que a arquitetura de multi-inquilino do Microsoft Azure de nuvem suporta segurança, confidencialidade, privacidade, integridade e normas de disponibilidade.

Na área de trabalho ativada na nuvem, um inquilino pode ser definido com um cliente ou uma organização que possui e gere uma instância específica do referido serviço em nuvem. Com a plataforma de identidade fornecida pelo Microsoft Azure, um inquilino é simplesmente uma instância dedicada do Azure Active Directory (Azure AD) que a sua organização recebe e possui quando se inscreve num serviço em nuvem da Microsoft.

Cada diretório do Azure AD é distinto e separado de outros diretórios do Azure AD. Tal como um edifício de escritórios empresariais é um recurso seguro específico apenas para a sua organização, um diretório do Azure AD também foi concebido para ser um recurso com utilização segura apenas para a sua organização. A arquitetura do Azure AD impede que os dados do cliente e as informações de identidade se misturem. Isto significa que os utilizadores e administradores de um diretório do Azure AD não podem, acidental ou intencionalmente, aceder aos dados de outro diretório.

### <a name="azure-tenancy"></a>Inquilinos do Azure
Inquilinos do Azure (subscrição do Azure) refere-se para uma relação de "cliente/billing" e um único [inquilino](https://docs.microsoft.com/azure/active-directory/develop/active-directory-howto-tenant) no [do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis). Isolamento de nível de inquilino no Microsoft Azure é conseguido utilizando o Azure Active Directory e [baseada em funções controlos](https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is) oferecidas pelo-lo. Cada subscrição do Azure está associada um diretório do Azure Active Directory (AD).

Os utilizadores, grupos e a esse diretório de aplicações podem gerir os recursos na subscrição do Azure. Pode atribuir estes direitos de acesso utilizando o portal do Azure, as ferramentas da linha de comandos do Azure e APIs de gestão do Azure. Um inquilino do Azure AD é logicamente isolado com limites de segurança para que nenhum cliente possa aceder ou comprometer conjunta inquilinos, de forma maliciosa ou acidentalmente. Do Azure AD é executado em servidores de "" bare-metal isoladas num segmento de rede segregated, onde filtragem de pacotes ao nível do anfitrião e a Firewall do Windows bloqueiam o tráfego e ligações indesejáveis.

- Acesso a dados no Azure AD requer autenticação do utilizador através de um serviço de token de segurança (STS). Informações sobre a existência do utilizador, estado ativado e função são utilizadas pelo sistema autorização para determinar se o acesso solicitado para o inquilino de destino está autorizado para este utilizador nesta sessão.

![Inquilinos do Azure](./media/azure-isolation/azure-isolation-fig1.png)


- Os inquilinos são contentores discretos e não existe nenhuma relação entre estas.

- Sem acesso através de inquilinos, a menos que o administrador de inquilinos concede-lo através de Federação ou o aprovisionar contas de utilizador de outros inquilinos.

- Acesso físico aos servidores que compõem o serviço do Azure AD e acesso direto aos sistemas de back-end do Azure AD, é restritos.

- Sem acesso a recursos físicos ou localizações de ter dos utilizadores do AD do Azure e, por conseguinte, não é possível ignorar as lógicas RBAC política verificações indicadas a seguir.

Para obter um diagnóstico e necessidades de manutenção, um modelo operacional que utiliza um sistema de elevação de privilégios just-in-time é necessário e utilizado. O Azure AD Privileged Identity Management (PIM) apresenta o conceito de um administrador elegível. [Administradores elegível](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) deve ser a utilizadores que necessitam de privilégios de acesso agora e, em seguida, mas não todos os dias. A função está inativa até que o utilizador precisa de acesso, em seguida, concluir um processo de ativação e tornar-se um administrador do Active Directory para um período de tempo predefinido.

![Azure AD Privileged Identity Management](./media/azure-isolation/azure-isolation-fig2.png)

Azure Active Directory aloja cada inquilino no seu próprio contentor protegido, com as políticas e permissões de e para dentro do contentor unicamente propriedade e são geridos pelo inquilino.

O conceito de contentores de inquilino está profundamente ingrained no serviço de diretórios em todas as camadas de portais para armazenamento persistente.

Mesmo quando os metadados de vários inquilinos do Azure Active Directory são armazenados no mesmo disco físico, não há nenhuma relação entre os contentores que não seja de que é definido pelo serviço de diretório, por sua vez, é ditado pelo administrador de inquilinos.

### <a name="azure-role-based-access-control-rbac"></a>Controlo de acesso do Azure baseada em funções (RBAC)
[Azure baseada em funções controlo de acesso (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is) ajuda-o a partilhar vários componentes disponíveis dentro de uma subscrição do Azure ao fornecer gestão de acesso detalhada para o Azure. RBAC do Azure permite-lhe segregar funções na sua organização e conceder acesso com base nos quais os utilizadores precisam desempenhar as suas funções. Em vez de dar everybody sem restrições permissões numa subscrição do Azure ou recursos, pode permitir que apenas determinadas ações.

RBAC do Azure tem três funções básicas que se aplicam a todos os tipos de recursos:

- **Proprietário** tem acesso total a todos os recursos, incluindo o direito para delegar o acesso a outras pessoas.

- **Contribuidor** pode criar e gerir todos os tipos de recursos do Azure, mas não é possível conceder acesso a outras pessoas.

- **Leitor** pode ver os recursos do Azure existentes.

![Controlo de Acesso Baseado em Funções do Azure](./media/azure-isolation/azure-isolation-fig3.png)

O resto das funções do RBAC do Azure permite a gestão de recursos do Azure específicos. Por exemplo, a função de contribuinte de Máquina Virtual permite ao utilizador criar e gerir máquinas virtuais. Se não atribuir-lhes acesso para a rede Virtual do Azure ou a sub-rede que a máquina virtual estabelece ligação ao.

[Funções incorporadas do RBAC](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) lista as funções disponíveis no Azure. Especifica as operações e o âmbito de cada função incorporada concede a utilizadores. Se estiver à procura para definir as suas próprias funções para o controlo ainda mais, consulte como criar [funções personalizadas no Azure RBAC](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles).

Algumas outras funcionalidades do Azure Active Directory incluem:
- Azure AD permite SSO para aplicações SaaS, independentemente de onde estão alojadas. Algumas aplicações estão federadas com o Azure AD e outras utilizam SSO com palavra-passe. Aplicações federadas também podem suportar o aprovisionamento de utilizador e [cofres de palavras de palavra-passe](https://www.techopedia.com/definition/31415/password-vault).

- O acesso aos dados do [Armazenamento do Azure](https://azure.microsoft.com/services/storage/) é controlado através da autenticação. Cada conta de armazenamento tem uma chave primária ([chave da conta de armazenamento](https://docs.microsoft.com/azure/storage/storage-create-storage-account), ou SAK) e uma chave secreta secundária (a assinatura de acesso partilhado ou SAS).

- O Azure AD fornece identidade como um serviço através de Federação utilizando [serviços de Federação do Active Directory](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-azure-adfs), sincronização e a replicação de diretórios no local.

- [Multi-factor Authentication do Azure](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) é o serviço de autenticação multifator que exige que os utilizadores verificar os inícios de sessão utilizando uma aplicação móvel, chamada telefónica ou mensagem de texto. Pode ser utilizado com o Azure AD para o ajudar a proteger recursos no local com o servidor multi-factor Authentication do Azure bem como com aplicações personalizadas e diretórios utilizando o SDK.

- [Serviços de domínio do Azure AD](https://azure.microsoft.com/services/active-directory-ds/) permite-lhe associar máquinas virtuais do Azure a um domínio do Active Directory sem implementar a política de controladores de domínio. Pode iniciar sessão para estas máquinas virtuais com as suas credenciais do Active Directory empresarias e administrar máquinas de virtuais associados a um domínio utilizando a política de grupo para impor as linhas de base de segurança em todas as suas máquinas virtuais do Azure.

- [O Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) fornece um serviço de gestão de identidade global altamente disponíveis para aplicações direcionadas para o consumidor preparada para centenas de milhões de identidades. Pode ser integrado entre plataformas móveis e Web. Os consumidores podem iniciar sessão todas as suas aplicações através de experiências personalizáveis através das respetivas contas de redes sociais existentes ou criar as credenciais.

### <a name="isolation-from-microsoft-administrators--data-deletion"></a>Isolamento de administradores do Microsoft & eliminação de dados
Microsoft demora medidas seguras para proteger os seus dados contra acesso inapropriado ou utilizar por pessoas não autorizadas. Estes controlos e os processos operacionais são apoiados pelo [termos de serviço Online](http://aka.ms/Online-Services-Terms), que oferecem compromissos contractual que controlam o acesso aos seus dados.

-   Engenheiros de Microsoft não têm acesso de predefinição aos seus dados na nuvem. Em vez disso, estes são concedidos acesso, em supervisão de gestão, apenas quando necessário. Esse acesso é cuidadosamente controlado com sessão iniciado e revogado quando já não é necessário.

-   Microsoft poderá contratamos outras empresas para fornecerem serviços limitados em seu nome. Podem aceder a dados do cliente apenas para fornecer que os serviços para os quais, podemos ter recrutados-los para fornecer a subcontratados e mesmas proibidas de utilizar para qualquer outra finalidade. Além disso, contractually estão vinculados a manter a confidencialidade das informações dos nossos clientes.

Serviços empresariais com certificações auditadas, tais como a norma ISO/IEC 27001 são verificados regularmente pela Microsoft e auditoria autorizada firms, que efetuam as auditorias de exemplo atestem que o acesso, apenas para fins de legítima de negócios. Pode aceder sempre os seus próprios dados de cliente em qualquer altura e por qualquer motivo.

Se eliminar quaisquer dados, o Microsoft Azure elimina os dados, incluindo quaisquer cópias em cache ou cópia de segurança. Para os serviços no âmbito, que irá ocorrer a eliminação no prazo de 90 dias após o fim do período de retenção. (No âmbito de serviços são definidos na secção termos de processamento de dados do nosso [termos de serviço Online](http://aka.ms/Online-Services-Terms).)

Se uma unidade de disco utilizada para armazenamento sofrerá de uma falha de hardware, é segura [apagar ou destruição](https://www.microsoft.com/trustcenter/Privacy/You-own-your-data) antes Microsoft devolve-lo para o fabricante de substituição ou a reparação. Os dados na unidade são substituídos para se certificar de que não não possível recuperar os dados por qualquer meio.

## <a name="compute-isolation"></a>Isolamento de computação
O Microsoft Azure oferece vários baseado na nuvem informáticos serviços que incluem uma seleção grande de instâncias de computação e serviços que podem ser dimensionados acima e abaixo automaticamente para satisfazer as necessidades da sua aplicação ou a empresa. Estas instâncias de computação e o serviço oferecem isolamento em vários níveis para proteger os dados sem comprometer a flexibilidade na configuração esse pedido de clientes.

### <a name="hyper-v--root-os-isolation-between-root-vm--guest-vms"></a>Isolamento de SO de Hyper-V & raiz entre raiz VM & VMs de convidado
Plataforma de computação do Azure é baseada em Virtualização da máquina – significado que todo o código de cliente seja executado numa máquina virtual de Hyper-V. Em cada nó do Azure (ou ponto final da rede), não há um hipervisor que é executado diretamente através do hardware e divide um nó num número de variável de convidado máquinas virtuais (VMs).


![Isolamento de SO de Hyper-V & raiz entre raiz VM & VMs de convidado](./media/azure-isolation/azure-isolation-fig4.jpg)


Cada nó tem também uma especial raiz VM, que é executado o sistema operativo anfitrião. Um limite crítico é o isolamento de VM a partir de VMs de convidado e as VMs de convidado entre si, geridos pelo hipervisor e a raiz de SO de raiz. O emparelhamento do hipervisor/root SO tira partido decades da Microsoft, de experiência de segurança do sistema operativo e a configuração mais recente do Hyper-V da Microsoft, para proporcionar o isolamento seguro de VMs de convidado.

A plataforma Azure utiliza um ambiente virtualizado. Instâncias de utilizador funcionam como máquinas virtuais autónomas que não têm acesso a um servidor de anfitrião físico, e este isolamento é imposto através da utilização de níveis de privilégio (em anel-0/em anel-3) de processador físico.

O Ring 0 é mais privilegiado e o 3 é o menos. O SO convidado é executado no menor privilégio anel 1 e as aplicações são executadas no 3 anel com menos privilégios. Esta virtualização dos recursos físicos conduz a uma separação clara entre o SO convidado e o hipervisor, resultando na separação de segurança adicional entre os dois.

O hipervisor do Azure funciona como um kernel micro e passa todos os pedidos de acesso de hardware de máquinas virtuais convidadas para o anfitrião para processamento através da utilização de uma interface de memória partilhada chamada VMBus. Isto impede os utilizadores de obterem acesso de leitura/escrita/execução não processado ao sistema e atenua o risco de partilha de recursos do sistema.

### <a name="advanced-vm-placement-algorithm--protection-from-side-channel-attacks"></a>Algoritmo de colocação de VM avançadas e de proteção contra ataques de canal do lado
Qualquer ataque cross-VM envolve dois passos: Colocação de uma VM adversário controlada no mesmo anfitrião, como uma vítima VMs e, em seguida, ser, o limite de isolamento para roubar informações confidenciais vítima ou afetar o desempenho para greed ou vandalism. Microsoft Azure fornece proteção em ambos os passos utilizando um algoritmo de colocação de VM e proteção a partir de todos os ataques de canal de lado conhecidos, incluindo VMs de vizinho inúteis avançadas.

### <a name="the-azure-fabric-controller"></a>O controlador de recursos de infraestrutura do Azure
O controlador de recursos de infraestrutura do Azure é responsável pela alocação de recursos de infraestrutura para cargas de trabalho de inquilino e gere unidirecionais comunicações do anfitrião para máquinas virtuais. O algoritmo de placing da VM do controlador de recursos de infraestrutura do Azure é altamente sofisticadas e praticamente impossíveis prever como ao nível do anfitrião físico.

![O controlador de recursos de infraestrutura do Azure](./media/azure-isolation/azure-isolation-fig5.png)

O hipervisor do Azure impõe a separação de processo e de memória entre máquinas virtuais e, em segurança encaminha o tráfego de rede aos inquilinos de SO convidado. Isto elimina a possibilidade de e lado canal ataque ao nível da VM.

No Azure, a VM de raiz é especial: executa um protegida sistema operativo denominado o SO de raiz que aloja um agente de recursos de infraestrutura (FA). FAs são utilizadas por sua vez para gerir agentes convidados (GA) dentro do convidado sos em VMs do cliente. FAs também gerir os nós de armazenamento.

A coleção de hipervisor do Azure, SO/FA de raiz e cliente VMs/petróleo é composto por um nó de computação. FAs são geridos por um controlador de recursos de infraestrutura (FC), que exista fora de nós de computação e armazenamento (os clusters de computação e armazenamento são geridos pelo FCs separado). Se um cliente de atualizações de ficheiro de configuração da respetiva aplicação enquanto estiver em execução, de FC comunica com FA, que, em seguida, contacta gás, notificar que a aplicação da alteração de configuração. Em caso de falha de hardware de FC automaticamente irá localizar hardware disponíveis e reiniciar a VM não existe.

![Controlador de recursos do Azure](./media/azure-isolation/azure-isolation-fig6.jpg)

A comunicação de um controlador de recursos de infraestrutura para um agente é unidirecional. O agente implementa um serviço protegido por SSL que apenas responde aos pedidos a partir do controlador. -Não é possível iniciar ligações para o controlador ou outros nós internos com privilégios. O FC processa todas as respostas de como se estivessem não fidedignos.


![Controlador de recursos de infraestrutura](./media/azure-isolation/azure-isolation-fig7.png)

Expande o isolamento da VM raiz de VMs de convidado e as VMs de convidado entre si. Também estão isolados de nós de armazenamento para uma maior proteção nós de computação.


O hipervisor e o anfitrião de que SO fornece pacotes de rede - os filtros para ajudar a garantir que máquinas virtuais não fidedignas não é possível gerar a denuncia tráfego ou receba tráfego endereçado não aos mesmos, direcionam o tráfego para pontos finais de infraestrutura protegido ou enviar/recebem o tráfego de difusão inadequado.


### <a name="additional-rules-configured-by-fabric-controller-agent-to-isolate-vm"></a>Regras adicionais configuradas pelo agente de controlador de recursos de infraestrutura para isolar VM
Por predefinição, todo o tráfego é bloqueado quando é criada uma máquina virtual e, em seguida, o agente de controlador de recursos de infraestrutura configura o filtro de pacotes para adicionar regras e as exceções para permitir tráfego autorizado.

Existem duas categorias de regras que são programadas:

-   **Regras de infraestrutura ou de configuração do computador:** por predefinição, todas as comunicações está bloqueada. Existem exceções para permitir que uma máquina virtual para enviar e receber tráfego DHCP e DNS. Máquinas virtuais também pode enviar o tráfego para a internet "pública" e enviar o tráfego para outras máquinas virtuais na mesma rede Virtual do Azure e o servidor de ativação de SO. Lista as máquinas virtuais de destinos permitidos de saída não inclui sub-redes do router do Azure, a gestão do Azure e outras propriedades de Microsoft.

-   **Ficheiro de configuração de função:** Isto define a entrada acesso as listas de controlo (ACLs) com base no modelo de serviço do inquilino.

### <a name="vlan-isolation"></a>Isolamento de VLAN
Existem três VLANs em cada cluster:

![Isolamento de VLAN](./media/azure-isolation/azure-isolation-fig8.jpg)


-   A VLAN principal – interconnects nós não fidedignos do cliente

-   A VLAN de FC – contém fidedigna FCs e sistemas de suporte

-   O dispositivo VLAN – contém rede fidedigna e outros dispositivos de infraestrutura

Comunicação é permitida da FC VLAN à VLAN principal, mas não pode ser iniciada a partir de VLAN principal para a VLAN de FC. Comunicação é também bloqueada de VLAN principal para o dispositivo VLAN. Isto garante que, mesmo se um nó com o código de cliente for comprometido, não é possível ataques nós em FC ou dispositivo VLANs.

## <a name="storage-isolation"></a>Isolamento de armazenamento
### <a name="logical-isolation-between-compute-and-storage"></a>Isolamento lógico entre computação e armazenamento
Como parte da respetiva estrutura fundamental, o Microsoft Azure separa computação baseadas em VMS do armazenamento. Esta separação permite que computação e armazenamento para dimensionamento independente, tornando mais fácil fornecer vários inquilinos e isolamento.

Por conseguinte, armazenamento do Azure é executado em hardware separado sem conectividade de rede para computação do Azure, exceto logicamente. [Isto](https://msenterprise.global.ssl.fastly.net/vnext/PDFs/A01_AzureSecurityWhitepaper20160415c.pdf) significa que quando um disco virtual é criado, espaço em disco não está alocado para a capacidade de toda. Em vez disso, é criar uma tabela que mapeie endereços no disco virtual para áreas no disco físico e essa tabela está inicialmente vazia. **Na primeira vez que um cliente escreve dados do disco virtual, seja atribuído espaço no disco físico, e um ponteiro para a mesma é colocado na tabela.**
### <a name="isolation-using-storage-access-control"></a>Controlo de acesso de armazenamento através de isolamento
**Controlo de acesso no armazenamento do Azure** tem um modelo de controlo de acesso simples. Cada subscrição do Azure, pode criar uma ou mais contas de armazenamento. Cada conta de armazenamento tem uma chave secreta única que é utilizada para controlar o acesso a todos os dados nessa conta de armazenamento.

![Controlo de acesso de armazenamento através de isolamento](./media/azure-isolation/azure-isolation-fig9.png)

**Acesso aos dados de armazenamento do Azure (incluindo tabelas)** pode ser controlado através de um [SAS (assinatura de acesso partilhado)](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) token, que concede acesso de âmbito. O SAS é criado através de um modelo de consulta (URL), assinado com o [SAK (chave de conta do Storage)](https://msdn.microsoft.com/library/azure/ee460785.aspx). Que [assinado URL](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) pode ser especificado para outro processo (que é, delegado), que pode, em seguida, preencha os detalhes da consulta e efetuar o pedido de serviço de armazenamento. Uma SAS permite-lhe de conceder acesso baseados no tempo para os clientes sem revelando a chave secreta a conta de armazenamento.

O SAS significa que podemos pode conceder que um cliente limitada permissões, para objetos na nossa conta de armazenamento para um determinado período de tempo e com um conjunto especificado de permissões. Iremos pode conceder estas permissões limitadas sem ter de partilhar as chaves de acesso da conta.

### <a name="ip-level-storage-isolation"></a>Isolamento de armazenamento ao nível do IP
Pode estabelecer as firewalls e definir um intervalo de endereços IP para os seus clientes fidedignos. Com um intervalo de endereços IP, apenas os clientes que têm um endereço IP no intervalo definido podem ligar ao [Storage do Azure](https://docs.microsoft.com/azure/storage/storage-security-guide).

Os dados de armazenamento IP podem ser protegidos contra utilizadores não autorizados através de um mecanismo de rede que é utilizada para atribuir um túnel dedicado ou dedicado de tráfego para o armazenamento IP.

### <a name="encryption"></a>Encriptação
O Azure disponibiliza os seguintes tipos de encriptação para proteger os dados:
-   Encriptação em trânsito

-   Encriptação inativa

#### <a name="encryption-in-transit"></a>Encriptação em trânsito
A encriptação em trânsito é um mecanismo de proteção de dados quando transmitido através de redes. Com o Storage do Azure, pode proteger dados utilizando:

-   [Encriptação de nível de transporte](https://docs.microsoft.com/azure/storage/storage-security-guide#encryption-in-transit), por exemplo, HTTPS quando a transferência de dados ou a sair do armazenamento do Azure.

-   [Ligar a encriptação](../storage/common/storage-security-guide.md#using-encryption-during-transit-with-azure-file-shares), tais como a encriptação SMB 3.0 para partilhas de ficheiros do Azure.

-   [Encriptação do lado do cliente](https://docs.microsoft.com/azure/storage/storage-security-guide#using-client-side-encryption-to-secure-data-that-you-send-to-storage), para encriptar os dados antes de é transferido para o armazenamento e para desencriptar os dados depois de serem transferidos fora do armazenamento.

#### <a name="encryption-at-rest"></a>Encriptação de Inativos
Para muitas organizações, [encriptação de dados Inativos](https://blogs.microsoft.com/cybertrust/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) é um passo obrigatório para soberania dos dados, privacidade e conformidade de dados. Existem três funcionalidades do Azure que fornecem encriptação de dados "Inativos":

-   [Encriptação do serviço de armazenamento](https://docs.microsoft.com/azure/storage/storage-security-guide#encryption-at-rest) permite-lhe solicitar que o serviço de armazenamento encriptar dados automaticamente quando escrita ao Storage do Azure.

-   [Encriptação do lado do cliente](https://docs.microsoft.com/azure/storage/storage-security-guide#client-side-encryption) também fornece a funcionalidade de encriptação de inativos.

-   [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) permite-lhe encriptar os discos de SO e discos de dados utilizados pela máquina virtual IaaS.

#### <a name="azure-disk-encryption"></a>Azure Disk Encryption
[Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) para máquinas virtuais (VMs) ajuda-o a segurança organizacional do endereço e requisitos de conformidade ao encriptar os discos da VM (incluindo discos de dados e de arranque) com as chaves e as políticas de controlo no [Cofre de chaves do Azure](https://azure.microsoft.com/services/key-vault/).

A solução de encriptação de disco para o Windows é baseada no [a encriptação de unidade BitLocker do Microsoft](https://technet.microsoft.com/library/cc732774.aspx), e a solução de Linux baseia-se no [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt).

A solução suporta os seguintes cenários para VMs de IaaS quando são ativados no Microsoft Azure:
-   Integração com o Cofre de chaves do Azure

-   O escalão Standard VMs: A, D, DS, G, GS e assim sucessivamente, VMs de IaaS de série

-   Ativar a encriptação em Windows e as VMs de IaaS Linux

-   A desativação da encriptação nos dados e SO unidades para as VMs de IaaS Windows

-   A desativação da encriptação em unidades de dados para as VMs de IaaS Linux

-   Ativar a encriptação em VMs de IaaS que estão a executar SO de cliente do Windows

-   Ativar a encriptação em volumes com caminhos de montagem

-   Ativar a encriptação em VMs do Linux que estão configurados com o disco striping (RAID) utilizando [mdadm](https://en.wikipedia.org/wiki/Mdadm)

-   Ativar a encriptação em VMs do Linux utilizando [LVM (Gestor de Volume lógico)](https://msdn.microsoft.com/library/windows/desktop/bb540532) para discos de dados

-   Ativar a encriptação em VMs do Windows que são configuradas através da utilização de espaços de armazenamento

-   Todas as regiões públicas do Azure são suportadas

A solução não suporta os seguintes cenários, funcionalidades e tecnologias na versão:

-   Escalão básico VMs do IaaS

-   A desativação da encriptação numa unidade do SO para as VMs de IaaS Linux

-   VMs de IaaS que são criados utilizando o método de criação de VM clássico

-   Integração com o serviço de gestão de chaves no local

-   Ficheiros do Azure (sistema de ficheiros partilhados), o sistema de ficheiros de rede (NFS), volumes dinâmicos e VMs do Windows que estão configurados com sistemas RAID baseados em software

## <a name="sql-azure-database-isolation"></a>Isolamento do SQL Server da base de dados do Azure
A Base de Dados SQL é um serviço de bases de dados relacionais na cloud da Microsoft baseado no motor Microsoft SQL Server líder de mercado e com capacidade para processar cargas de trabalho críticas para missões. Base de dados SQL proporciona isolamento previsível dados ao nível da conta, geografia / região com base e em redes — tudo com quase sem administração.

### <a name="sql-azure-application-model"></a>Modelo de aplicação do Azure SQL Server

[Microsoft SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-get-started) base de dados é um serviço de base de dados relacional baseado na nuvem baseado nas tecnologias de SQL Server. Fornece um serviço de base de dados de disponibilidade elevada, dimensionável e de multi-inquilino alojado pela Microsoft na nuvem.

De uma perspetiva de aplicação do SQL Azure fornece a hierarquia seguinte: cada nível tem um-para-muitos contenção de níveis abaixo.

![Modelo de aplicação do Azure SQL Server](./media/azure-isolation/azure-isolation-fig10.png)

A conta e subscrição são conceitos de plataforma do Microsoft Azure para associar a faturação e de gestão.

Lógicas servidores e bases de dados são conceitos específicos do SQL do Azure e são geridos utilizando o SQL Azure, fornecida OData e TSQL das interfaces ou através do portal do Azure de SQL que integrado no portal do Azure.

Servidores do Azure do SQL Server não são física ou instâncias de VM, em vez disso, são coleções de bases de dados, a partilha de políticas de gestão e segurança, que são armazenadas no, por isso, chamado "mestre lógico" base de dados.

![SQL Azure](./media/azure-isolation/azure-isolation-fig11.png)

Bases de dados mestras lógicas incluem:

-   Inícios de sessão do SQL Server utilizados para ligar ao servidor

-   Regras da firewall

Faturação relacionados com a utilização informações de e para o Azure SQL Server não são garantidos que as bases de dados do mesmo servidor lógico de estar na mesma instância física num cluster do SQL Azure, em vez disso, as aplicações têm de fornecer o nome de base de dados de destino quando a ligação.

Numa perspetiva de cliente, um servidor lógico é criado numa região georreplicação gráfica enquanto a criação do servidor real acontece dos clusters na região.

### <a name="isolation-through-network-topology"></a>Isolamento através da topologia de rede

Quando um servidor lógico é criado e registado do respetivo nome DNS, os nomes do DNS aponta para o endereço "Gateway VIP", por isso, chamado no Centro de dados específica em que o servidor foi colocado.

Atrás de VIP (endereço IP virtual), temos de uma coleção de serviços sem monitorização de estado do gateway. Em geral, os gateways obterem envolvidos quando há coordenação necessária entre várias origens de dados (base de dados mestra, base de dados do utilizador, etc.). Serviços de gateway implementam o seguinte:
-   **Funcionalidade de proxy de ligação TDS.** Isto inclui localizar a base de dados do utilizador no cluster de back-end, implementar a sequência de início de sessão e, em seguida, reencaminhar os pacotes TDS para o back-end e back.

-   **Gestão de base de dados.** Isto inclui a implementação de uma coleção de fluxos de trabalho para efetuar operações de base de dados CREATE/ALTER/DROP. As operações de base de dados podem ser invocadas por pacotes de interceção de TDS ou APIs de OData explícita.

-   Operações de CREATE/ALTER/DROP. o início de sessão/utilizador

-   Operações de gestão do servidor lógico através de OData API

![Isolamento através da topologia de rede](./media/azure-isolation/azure-isolation-fig12.png)

A camada atrás os gateways é chamada "back-end". Este é onde todos os dados são armazenados de forma altamente disponível. Cada conjunto de dados possui correspondências é denominado pertence a uma "partição" ou "failover unit", o cada um deles ter, pelo menos, três réplicas. As réplicas são armazenadas e replicadas pelo motor de SQL Server e geridas por um sistema de ativação pós-falha, muitas vezes, referido como "infraestrutura".

Geralmente, o sistema de back-end não comunicar com outros sistemas de saída como precaução de segurança. Isto está reservado para os sistemas na camada de front-end (gateway). As máquinas de camada de gateway limitada privilégios nos computadores back-end para minimizar a superfície de ataque como um mecanismo de defesa em profundidade.

### <a name="isolation-by-machine-function-and-access"></a>Isolamento através da função de máquina e acesso
SQL Azure (é composto pelos serviços em execução em funções de computador diferente. SQL Azure está dividido em "backend", base de dados de nuvem e "" front-end (/ gestão de Gateway) ambientes, com o geral princípio de que apenas de tráfego para o back-end e não terminar. O ambiente de front-end pode comunicar ao mundo externo de outros serviços e, em geral, apenas limitou as permissões no back-end (suficiente para chamar os pontos de entrada tem de invocar).

## <a name="networking-isolation"></a>Isolamento de rede
Implementação do Azure tem várias camadas de isolamento de rede. O diagrama seguinte mostra várias camadas de isolamento de rede que Azure fornece aos clientes. Estas camadas são nativo na própria plataforma do Azure e funcionalidades do cliente. Entrada da Internet e DDoS do Azure fornece isolamento contra ataques em grande escala contra do Azure. A próxima camada de isolamento é definida pelo cliente endereços IP públicos (pontos finais), que são utilizados para determinar qual o tráfego pode passar o serviço em nuvem para a rede virtual. Nativo virtual do Azure isolamento de rede assegura isolamento completado de todas as outras redes e de que o tráfego apenas flui através dos métodos e caminhos de utilizador configurado. Estes caminhos e os métodos são a próxima camada, onde os NSGs, UDR e os dispositivos de rede virtual podem ser utilizados para criar limites de isolamento para proteger as implementações de aplicações na rede protegida.

![Isolamento de rede](./media/azure-isolation/azure-isolation-fig13.png)

**Isolamento de tráfego:** A [rede virtual](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) é o limite de isolamento de tráfego na plataforma do Azure. Máquinas virtuais (VMs) numa rede virtual não pode comunicar diretamente para as VMs numa rede virtual diferente, mesmo se ambas as redes virtuais são criadas ao mesmo cliente. Isolamento é uma propriedade crítica que garante a VMs de cliente e comunicação permanece privada dentro de uma rede virtual.

[Sub-rede](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview#subnets) oferece uma camada adicional de isolamento na rede virtual com base no intervalo de IP. Endereços IP na rede virtual, pode dividir uma rede virtual em várias sub-redes para a organização e de segurança. As VMs e as instâncias de função de PaaS implementadas em sub-redes (nas mesmas ou em diferentes) dentro de uma VNet podem comunicar entre si sem qualquer configuração adicional. Também pode configurar [o grupo de segurança de rede (NSGs)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview#network-security-groups-nsg) para permitir ou negar o tráfego de rede para uma instância VM com base em regras configuradas na lista de controlo de acesso (ACL) de NSG. Os NSGs podem ser associados a sub-redes ou a instâncias de VM individuais dentro dessa sub-rede. Quando um NSG é associado a uma sub-rede, as regras da ACL são aplicadas a todas as instâncias de VM nessa sub-rede.

## <a name="next-steps"></a>Passos Seguintes

- [Opções de isolamento de rede para máquinas no Windows redes virtuais do Azure](https://azure.microsoft.com/blog/network-isolation-options-for-machines-in-windows-azure-virtual-networks/)

Isto inclui o cenário de front-end e back-end clássico onde máquinas numa rede de back-end específico ou sub-rede podem permitir apenas determinados clientes ou outros computadores ligar a um ponto final de determinado com base numa lista branca de endereços IP.

- [Isolamento de computação](https://msenterprise.global.ssl.fastly.net/vnext/PDFs/A01_AzureSecurityWhitepaper20160415c.pdf)

O Microsoft Azure oferece um vários baseado na nuvem informáticos serviços que incluem uma seleção grande de instâncias de computação e serviços que podem ser dimensionados acima e abaixo automaticamente para satisfazer as necessidades da sua aplicação ou a empresa.

- [Isolamento de armazenamento](https://msenterprise.global.ssl.fastly.net/vnext/PDFs/A01_AzureSecurityWhitepaper20160415c.pdf)

Microsoft Azure separa o cálculo de cliente baseada na VM do armazenamento. Esta separação permite que computação e armazenamento para dimensionamento independente, tornando mais fácil fornecer vários inquilinos e isolamento. Por conseguinte, armazenamento do Azure é executado em hardware separado sem conectividade de rede para computação do Azure, exceto logicamente. Todos os pedidos executam através de HTTP ou HTTPS com base na escolha do cliente.

