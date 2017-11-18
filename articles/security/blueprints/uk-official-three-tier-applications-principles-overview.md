---
title: "Blueprint da automatização do Azure - descrição geral do National informático segurança Centre segurança princípios da nuvem"
description: "Blueprint da automatização do Azure - descrição geral do National informático segurança Centre segurança princípios da nuvem"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 49652fd9-b8c6-4a88-bc5e-0b58a0260ed6
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/17/2017
ms.author: jomolesk
ms.openlocfilehash: 0be18e2c2354ea8f766eb48db793c906e565a201
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2017
---
# <a name="national-cyber-security-centre-cloud-security-principles-overview"></a>Descrição geral de segurança de informático Centre princípios de segurança de nuvem nacional


> [!NOTE]
> Estes princípios de segurança são definidos por o RU National informático segurança Centre (NCSC). Consulte [documentação NCSC](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) para obter informações sobre procedimentos e as diretrizes para cada princípio de segurança de teste.



## <a name="ncsc-cloud-security-principle-1"></a>Princípio de segurança de nuvem NCSC 1
### <a name="data-in-transit-protection"></a>Dados em trânsito proteção
Transiting redes de dados de utilizador devem ser protegidos adequadamente contra adulteração e escutas.

Isto deve ser possível através de uma combinação de:

- proteção de rede - negar o atacante a capacidade de intercetar dados
- encriptação - negar o atacante a capacidade de leitura de dados


**Responsabilidades:**`Shared`


|||
|---|---|
| **Cliente** | Este Blueprint Azure configura recursos para comunicar utilizando apenas seguros protocolos. O componente de WAF do Gateway de aplicação está configurado para aceitar communicators de utiliza externa através de HTTPS/TLS e comunicar com o conjunto de back-end apenas através de HTTPS/TLS. Os serviços de ambiente de trabalho remoto estão configurados para utilizar ligações seguras. VPN é utilizada para proteger o tráfego web entre AppGateway e o Azure. |
| **Fornecedor&nbsp;(Microsoft&nbsp;Azure)** | Azure utiliza o protocolo Transport Layer Security (TLS) 1.2 de norma da indústria com chaves de encriptação RSA/SHA256 de 2048 bits, tal como recomendado pela CESG/NCSC, para encriptar as comunicações entre o cliente e a nuvem tanto internamente entre sistemas do Azure e datacentres. Por exemplo, quando os administradores utilizam o Portal do Microsoft Azure para gerir o serviço para a sua organização, os dados transmitidos entre o portal e o dispositivo do administrador são enviados um canal do TLS encriptado. Quando liga um utilizador de correio eletrónico Outlook.com utilizando um browser padrão, a ligação HTTPS fornece um canal seguro para receber e enviar correio eletrónico.<br /> <br /> Azure oferece uma gama de opções para proteger os seus próprios dados e o tráfego de que os seus clientes. As funcionalidades de gestão de certificado incorporadas no Azure dar aos administradores flexibilidade para configurar certificados e chaves de encriptação para sistemas de gestão, serviços individuais, sessões de secure shell (SSH), ligações de rede privada virtual (VPN), ligações de ambiente de trabalho remoto (RDP) e outras funções. <br /><br /> Os programadores podem utilizar os fornecedores de serviços de criptografia (CSPs) incorporados no Microsoft .NET Framework para aceder aos algoritmos de encriptação AES (Advanced Standard), juntamente com a funcionalidade de algoritmo de Hash seguro (SHA-2) para lidar com essas tarefas, como validar digital assinaturas. O Cofre de chaves do Azure ajuda os clientes a salvaguardar as chaves criptográficas e segredos armazenando-las nos módulos de segurança de hardware (HSMs). |


 ## <a name="ncsc-cloud-security-principle-2"></a>Princípio de segurança de nuvem NCSC 2
### <a name="asset-protection-and-resilience"></a>Proteção de recurso e resiliência
Dados de utilizador e os recursos de armazenamento ou processamento, devem ser protegidos contra físico adulteração, perda, danos ou seizure.

Aspetos a considerar são:

1. Localização física e Legal Jurisdiction
2. Segurança Datacentre
3. Dados de proteção de Rest
4. Sanitisation de dados
5. Equipamento Disposal
6. Resiliência física e disponibilidade


 ## <a name="ncsc-cloud-security-principle-21"></a>Princípio de segurança de nuvem NCSC 2.1
### <a name="physical-location-and-legal-jurisdiction"></a>Localização física e Legal Jurisdiction
Para compreender as circunstâncias legais sob a qual os dados foi possível aceder sem o seu consentimento tem de identificar as localizações a que está armazenado, processado e gerido.
Irá também é necessário compreender os controlos de processamento de dados como no âmbito do serviço são impostas, relativo à legislation RU. Inadequadas proteção de dados de utilizador pode resultar em jurídico e regulamentar aprovar ou reputational danos.


**Responsabilidades:**`Customer`

> [!NOTE]
> Serviços do Azure estão implementados regionally e os clientes podem configurar a determinados serviços do Azure para armazenar dados de cliente apenas numa única região. Microsoft Azure fornece uma lista de datacentres globalmente disponíveis para fornecer disponibilidade e fiabilidade numa escala global. Todos os datacentres do Azure foi certificou contra a norma ISO/IEC 27001: 2013. A Georreplicação RU consiste em 2 regiões: sul do RU e RU oeste.

|||
|---|---|
| **Cliente** | Este Blueprint Azure pede-lhe o administrador para cada região implementar os recursos do Azure. As regiões recomendadas para implementação são sul do RU ou RU oeste. |
| **Fornecedor&nbsp;(Microsoft&nbsp;Azure)** | Não Aplicável |


 ## <a name="ncsc-cloud-security-principle-22"></a>Princípio de segurança de nuvem NCSC 2.2
### <a name="datacentre-security"></a>Segurança Datacentre
Localizações utilizadas para fornecer serviços de nuvem tem física proteção contra acesso unauthorised, adulteração, roubo ou a reconfiguração dos sistemas. Proteções inadequadas poderão resultar no divulgação, alteração ou perda de dados.


**Responsabilidades:**`Microsoft Azure`


|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico aos recursos do sistema no Azure datacentres; medidas de proteção de segurança datacentre são implementadas e geridas pelo Microsoft Azure. Este princípio é herdado a partir do Microsoft Azure. |
| **Fornecedor&nbsp;(Microsoft&nbsp;Azure)** | Microsoft Azure implementa este princípio em nome dos clientes. Microsoft Azure é executado em instalações do Microsoft distribuídas geograficamente, espaço e utilitários de partilha com outros serviços online da Microsoft. Cada função é concebida para ser executado 24 x 7 x 365 e emprega várias medidas de norma da indústria para ajudar a proteger as operações de falha de energia, intrusões físico e falhas de rede. Estes datacentres está em conformidade com as normas da indústria (por exemplo, ISO 27001) para segurança física e disponibilidade. Estes são geridos, monitorizados e administrados pela equipa de operações da Microsoft. <br /> <br /> Clientes do Azure podem ter a certeza de que os controlos de segurança física são cumpridos datacentres em todas as do Azure devido a Azure que contém os certificados em todos os datacentres para padrão ISO/IEC 27001: 2013. A Georreplicação RU consiste em 2 regiões: sul do RU e RU oeste. |


 ## <a name="ncsc-cloud-security-principle-23"></a>Princípio de segurança de nuvem NCSC 2.3
### <a name="data-at-rest-protection"></a>Dados de proteção de Rest
Para garantir que os dados não estão disponíveis para entidades unauthorised com acesso físico à infraestrutura, contidos no âmbito do serviço de dados de utilizador devem ser protegidos, independentemente do suporte de dados de armazenamento que é mantido. Sem medidas adequadas no local, dados podem ser divulgados inadvertidamente no suporte de dados rejeitados, perdido ou roubado.


**Responsabilidades:**`Shared`

> [!NOTE]
> Todas as soluções de encriptação que o Microsoft Azure oferece aos seus clientes facilmente integram com o Cofre de chaves do Azure, que permite obter o facilitar a gestão de chaves de encriptação.

|||
|---|---|
| **Cliente** | Confidencialidade e integridade dos todo o armazenamento de BLOBs implementado por esta solução Blueprint do Azure é protegido através da utilização de encriptação de serviço de armazenamento do Azure (SSE). SSE salvaguarda dados Inativos dentro de contas do storage do Azure através da encriptação AES de 256 bits. |
| **Fornecedor&nbsp;(Microsoft&nbsp;Azure)** | O Azure disponibiliza uma vasta gama de capacidades de encriptação, concedendo aos clientes a flexibilidade para escolher a solução que melhor se adeque às suas necessidades. O Cofre de chaves do Azure ajuda os clientes facilmente e custo de forma eficaz manter o controlo das chaves utilizado por serviços e aplicações em nuvem para encriptar dados. Encriptação de disco do Azure permite aos clientes encriptar as máquinas virtuais. Encriptação do serviço de armazenamento do Azure torna possível encriptar todos os dados colocados a conta de armazenamento de um cliente. |


 ## <a name="ncsc-cloud-security-principle-24"></a>Princípio de segurança de nuvem NCSC 2.4
### <a name="data-sanitisation"></a>Sanitisation de dados
O processo de aprovisionamento, migração e anular o aprovisionamento de recursos não deve resultar num acesso unauthorised aos dados do utilizador.

Sanitização inadequada de dados pode resultar em:

- Dados de utilizador que está a ser mantidos indefinidamente pelo fornecedor de serviço
- Dados de utilizador que está a ser acessíveis a outros utilizadores do serviço como recursos são reutilizados
- Dados de utilizador que está a ser perdido ou das divulgados no suporte de dados rejeitados, perdido ou roubado.


**Responsabilidades:**`Microsoft Azure`


|||
|---|---|
| **Cliente** | O Microsoft Azure oferece contractual oferece garantias ao rendimento relativas a eliminação de dados permanente no Azure. Como tal, este princípio é herdado a partir do Microsoft Azure. |
| **Fornecedor&nbsp;(Microsoft&nbsp;Azure)** | Microsoft Azure segue o processo de disposal NIST SP800-88r1 com a classificação de dados alinhada com FIPS 199 moderada. Fornece NIST para proteger apagar abordagem (através de firmware da unidade de disco rígido) para unidades de que o suportam. Para unidades de disco rígido que não não possível limpar Microsoft destroys-los e apresenta-os a recuperação de informações impossível (por exemplo, disintegrate, shred, pulverize ou incinerate). O meio adequado disposal é determinado pelo tipo de recurso. Registos de destruição são retidos. Todos os serviços do Microsoft Azure utilizam serviços de gestão de armazenamento e disposal aprovados suporte de dados. <br />  <br /> Após a expiração ou terminação de uma subscrição de serviço, o cliente pode contactar a Microsoft e informe-se a: <br /><br /> (1) a desativar a conta do cliente e, em seguida, eliminar os dados de cliente; ou <br /> (2) a manter os dados armazenados no online serviço numa conta função limitado pelo menos 90 dias após a expiração ou terminação da subscrição do cliente (o "período de retenção") para esse cliente poderá extrair os dados. Após a expiração do período de retenção, a Microsoft irá desativar a conta do cliente e eliminar todos os dados. Colocar em cache ou cópias de segurança serão removidas dentro de 30 dias após o fim do período de retenção. |


 ## <a name="ncsc-cloud-security-principle-25"></a>Princípio de segurança de nuvem NCSC 2,5
### <a name="equipment-disposal"></a>Equipamento Disposal
Depois de equipamento utilizado para prestar um serviço chega ao fim do respetivo vida útil, deve ser eliminado de uma forma que não comprometer a segurança dos dados de utilizador armazenados no serviço, ou de serviço.


**Responsabilidades:**`Microsoft Azure`


|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico aos recursos do sistema no Azure datacentres; procedimentos de disposal equipamento são implementados e geridos pelo Microsoft Azure. Este princípio é herdado a partir do Microsoft Azure. |
| **Fornecedor&nbsp;(Microsoft&nbsp;Azure)** | Microsoft Azure implementa este princípio em nome dos clientes. Após fim-de-vida um sistema, Microsoft técnico operacional siga dados rigorosos tratamento de procedimentos e os processos de disposal de hardware para o ajudar a garantir que nenhum hardware que poderão conter dados de cliente é disponibilizada para entidades não fidedignas. Microsoft Azure segue o processo de disposal NIST SP800-88r1 com a classificação de dados alinhada com FIPS 199 moderada. Fornece NIST para proteger apagar abordagem (através de firmware da unidade de disco rígido) para unidades de que o suportam. Para unidades de disco rígido que não não possível limpar Microsoft destroys-los e apresenta-os a recuperação de informações impossível (por exemplo, disintegrate, shred, pulverize ou incinerate). O meio adequado disposal é determinado pelo tipo de recurso. Registos de destruição são retidos. Todos os serviços do Microsoft Azure utilizam serviços de gestão de armazenamento e disposal aprovados suporte de dados. |


 ## <a name="ncsc-cloud-security-principle-26"></a>Princípio de segurança de nuvem NCSC 2.6
### <a name="physical-resilience-and-availability"></a>Resiliência física e disponibilidade
Serviços de ter diferentes níveis de resiliência, que irão afetar a sua capacidade de funcionam normalmente em caso de falhas, incidentes ou ataques. Um serviço, sem garantias de disponibilidade poderá tornar-se disponível, possivelmente para períodos de prolongado, independentemente do impacto na sua empresa.


**Responsabilidades:**`Shared`

> [!NOTE]
> Se o cliente configura o Microsoft Azure adequadamente ao ativar o Azure Site Recovery e alternativo armazenamento de dados à outra datacentre georreplicação graficamente localizado, Microsoft Azure pode suportar a operação contínua de recursos implementados no cliente.

|||
|---|---|
| **Cliente** | O cliente é responsável pelo estabelecimento de um site de armazenamento alternativo. A instrução de implementação do controlo de cliente deve abordar a capacidade do cliente para operar em caso de um incidente. |
| **Fornecedor&nbsp;(Microsoft&nbsp;Azure)** | Microsoft Azure tem RU National informático segurança Centre (NCSC) aprovados datacentres em diferentes localizações geográficas (Sul do RU e RU oeste) para fornecer resiliência e disponibilidade. Será responsabilidade do cliente para reservar capacidade numa região alternativa utilizando o serviço de recuperação de sites do Azure. Assim que tiver configurado o Azure Site Recovery, o Azure iniciar e parar os serviços do cliente numa transição sem problemas para o site de processamento alternativo. |


 ## <a name="ncsc-cloud-security-principle-3"></a>Princípio de segurança de nuvem NCSC 3
### <a name="separation-between-users"></a>Separação entre os utilizadores
Um utilizador mal intencionado ou comprometido do serviço não deve poderá afetar o serviço ou os dados de outro.

Fatores que afetam a separação de utilizador incluem:

- onde são implementados controlos de separação – isto é muito deve influenciado pelo modelo de serviço (por exemplo, IaaS, PaaS, SaaS)
- que estão a partilhar o serviço com – isto é ditado pelo modelo de implementação (por exemplo, nuvem pública, private ou Comunidade)
- o nível de garantia disponível na implementação de controlos de separação

> [!NOTE]
> Num serviço IaaS deve considerar a separação fornecida pelos componentes de rede, armazenamento e computação. Além disso, os serviços SaaS e PaaS criados após IaaS poderão herdar algumas das propriedades de separação da infraestrutura do IaaS subjacente.

**Responsabilidades:**`Microsoft Azure`


|||
|---|---|
| **Cliente** | Microsoft Azure garante isolamento para cada utilizador impedir que um utilizador mal intencionado ou comprometido que afetam o serviço ou os dados de outro. Como tal, este princípio é herdado a partir do Microsoft Azure. |
| **Fornecedor&nbsp;(Microsoft&nbsp;Azure)** | Devido a servidores em nuvem do cliente que está a ser virtual, o paradigma separação física já não se aplica. Microsoft Azure foi concebido para ajudar a identificar e contador riscos inerentes num ambiente multi-inquilino. Armazenamento de dados e processamento logicamente é segregado entre utilizadores do Azure utilizando o Active Directory e a funcionalidade desenvolvida especificamente para os serviços de multi-inquilino, que visa para garantir que os dados de utilizador armazenados no partilhado datacentres do Azure não está acessível por outro organização. <br /> <br /> Fundamentais para qualquer arquitetura da nuvem partilhado é o isolamento fornecido para cada utilizador impedir que um utilizador mal intencionado ou comprometido que afetam o serviço ou os dados de outro. <br /> <br /> Para obter mais informações sobre o Microsoft separação de inquilino, consulte a descrição completa no [Azure Blueprint - princípios de segurança de nuvem NCSC - cliente responsabilidades matriz](https://aka.ms/blueprintuk-gcrm). |


 ## <a name="ncsc-cloud-security-principle-4"></a>Princípio de segurança de nuvem NCSC 4
### <a name="governance-framework"></a>Estrutura de governação
O fornecedor de serviço deve ter uma estrutura de governação de segurança que coordena e direciona a gestão do serviço e informações dentro da mesma. Irão ser fundamentalmente undermined quaisquer controlos técnicos implementados fora esta estrutura.
Ter uma estrutura de governação Efetivo Certifique-se esse procedimento técnico, controlos físicos e técnicos continuem a funcionar através da duração de um serviço. É também deve responder a alterações no serviço, desenvolvimentos tecnológicas e o aspecto de novas ameaças.


**Responsabilidades:**`Microsoft Azure`


|||
|---|---|
| **Cliente** | Microsoft Azure mantém uma estrutura de governação de segurança documentado para serviços do Azure. Como tal, este princípio é herdado a partir do Microsoft Azure. |
| **Fornecedor&nbsp;(Microsoft&nbsp;Azure)** | A estrutura de conformidade do Microsoft inclui uma metodologia padrão para definir domínios de conformidade, determinar quais os objetivos de aplicam a uma equipa fornecido ou elemento e capturar como objetivos de controlo de domínio são resolvidos com detalhes suficientes de acordo com uma determinado conjunto de normas da indústria, os regulamentos ou requisitos comerciais. A estrutura mapeia controlos várias normas de regulamentação, que permite que a Microsoft para conceber e criar serviços, utilizando um conjunto comum de controlos, deste modo, simplificando hoje conformidade numa série de normas e à medida que evoluem no futuro. <br /> <br /> Os processos de conformidade do Microsoft também o tornam mais fácil para os clientes alcançar a compatibilidade em vários serviços e satisfazer as suas necessidades de alteração de forma eficiente. Em conjunto, a tecnologia de Otimização da segurança e processos de conformidade Efetivo ativar Microsoft manter e expanda um conjunto avançado de certificações de terceiros. Estes certificações ajudam os clientes a demonstrar de preparação de conformidade para os seus clientes, auditores e regulators. <br /> <br />  Azure está em conformidade com um conjunto amplo de internacional, bem como as normas de conformidade regional e específicos da indústria, tais como ISO 27001, FedRAMP, SOC 1 e certificados SOC 2. Verificação da conformidade com os controlos de segurança estrita contidos nestas normas por rigorosas auditorias de terceiros que demonstram cumpram as normas do sector trabalho, certificações, attestations e autorizações e trabalham com a serviços do Azure. <br /> <br /> O Azure foi concebido com uma estratégia de conformidade que ajuda os objetivos de negócio de endereço de clientes, bem como as normas do sector e regulamentos. A estrutura de conformidade de segurança inclui teste e auditoria fases, análises de segurança, melhores práticas de risco gestão e análise de benchmark de segurança para alcançar certificados e attestations. <br /> <br /> Para obter mais informações sobre aderência da Microsoft para compatibilidade estruturas, consulte a descrição completa no [Azure Blueprint - princípios de segurança de nuvem NCSC - cliente responsabilidades matriz](https://aka.ms/blueprintuk-gcrm).|


 ## <a name="ncsc-cloud-security-principle-5"></a>Princípio de segurança de nuvem NCSC 5
### <a name="operational-security"></a>Segurança operacional
O serviço tem de ser operado e geridos de forma segura para impedir o, detetar ou impedir ataques. Segurança operacional boa não deve requerer processos complexos, bureaucratic, demorados ou dispendiosos.

Existem quatro elementos a ter em consideração:

- Configuração e gestão de alterações - deve certificar-se as alterações no sistema foram testadas e authorised corretamente. As alterações não devem alterar inesperadamente propriedades de segurança
- Gestão de vulnerabilidade - deve identificar e mitigar problemas de segurança nos componentes constituintes
- Monitorização protective - deve colocar medidas no local para detetar ataques e a atividade unauthorised no serviço
- Gestão de incidentes - Certifique-se de que pode responder a incidentes e recuperar de um serviço seguro e disponível




 ## <a name="ncsc-cloud-security-principle-51"></a>Princípio de segurança de nuvem NCSC 5.1
### <a name="configuration-and-change-management"></a>Configuração e gestão de alterações
Deve ter uma imagem exata dos ativos que compõem o serviço, juntamente com as respetivas dependências e configurações.
As alterações que podem afetar a segurança do serviço devem ser identificadas e geridas. Alterações unauthorised devem ser detetadas.
Em que a alteração não é gerida efetivamente, vulnerabilidades de segurança podem ser introduzidas unwittingly a um serviço. E, mesmo em que exista deteção da vulnerabilidade, poderá não ser totalmente Atenuado.


**Responsabilidades:**`Shared`


|||
|---|---|
| **Cliente** | Os modelos Azure Resource Manager e o associada recursos que compõem este Blueprint Azure representam uma linha de base "configuração como código" para a arquitetura implementada. A solução é fornecida apesar GitHub, que pode ser utilizada para controlo de configuração. <br /> <br /> Azure Active Directory conta privilégios são implementados utilizando o controlo de acesso baseado em funções ao atribuir utilizadores a funções fornecer strict controlo sobre quais os utilizadores podem ver e controlo implementado recursos. Privilégios de conta do Active Directory são implementados utilizando o controlo de acesso baseado em funções através da atribuição de utilizadores para grupos de segurança. Estes grupos de segurança controlam as ações que os utilizadores podem executar no que respeita à configuração do sistema operativo. Estes esquemas baseada em funções podem ser expandidos pelo cliente para satisfazer as necessidades de missão. <br /> <br /> Para poder estar em conformidade com este princípio, ainda é necessária configuração pelo cliente para utilização em produção. Como tal, estas configurações terá de fazer parte do processo de gestão de alteração do cliente. |
| **Fornecedor&nbsp;(Microsoft&nbsp;Azure)** | Microsoft Azure analisa e atualiza definições de configuração e configurações de linha de base de dispositivos de hardware, software e rede anual. As alterações são desenvolvidas, testadas e aprovadas antes de introduzir o ambiente de produção de um ambiente de desenvolvimento e/ou de teste. <br /> <br />Microsoft Azure aplica-se as configurações de linha de base utilizando o processo de alteração e versão para o processo de configuração de arranque de configuração para componentes de dispositivo de hardware e de rede introduzir e componentes de software do Microsoft Azure (por exemplo, OS recursos de infraestrutura, RDFE, XStore, etc.) Ambiente de produção do Microsoft Azure conforme descrito abaixo. <br /> <br /> As configurações de linha de base necessárias para os serviços baseados no Azure são revistas pela equipa de segurança do Azure e a conformidade e as equipas de serviço como parte de testes antes da implementação do serviço de produção. |


 ## <a name="ncsc-cloud-security-principle-52"></a>Princípio de segurança de nuvem NCSC 5.2
### <a name="vulnerability-management"></a>Gestão de vulnerabilidade
Fornecedores de serviços devem ter dos processos de gestão no local para identificar, Triar e mitigar vulnerabilidades. Os serviços que não, rapidamente deixará de estar vulnerável a ataques com métodos publicamente conhecidos e ferramentas.


**Responsabilidades:**`Customer`


|||
|---|---|
| **Cliente** | O cliente é responsável por vulnerabilidade análise nos recursos de implementação de cliente (para incluir aplicações, sistemas operativos, bases de dados e software). A instrução de implementação do cliente deve abordar as ferramentas utilizadas para efetuar análises de vulnerabilidade. |
| **Fornecedor&nbsp;(Microsoft&nbsp;Azure)** | Gestão de atualizações de segurança ajuda a proteger os sistemas de vulnerabilidades conhecidas. Sistemas de implementação integrada do Azure utiliza para gerir a distribuição e a instalação das atualizações de segurança para Microsoft software. Azure também é possível desenhar nos recursos do Microsoft Security Response Centre (MSRC), que identifica, monitoriza, responde a e resolve incidentes de segurança e na nuvem vulnerabilidades à volta o relógio, cada dia do ano. |


 ## <a name="ncsc-cloud-security-principle-53"></a>Princípio de segurança de nuvem NCSC 5.3
### <a name="protective-monitoring"></a>Monitorização protective
Um serviço que não monitorizar eficazmente de ataque, utilização indevida e avaria será pouco provável detetar ataques (com êxito e êxito). Como resultado, será possível responder rapidamente a potenciais compromissos dos seus ambientes e dados.


**Responsabilidades:**`Customer`


|||
|---|---|
| **Cliente** | O cliente é responsável pela monitorização de recursos de implementação de cliente (para incluir aplicações, sistemas operativos, bases de dados e software). A instrução de implementação do controlo de cliente deve abordar os mecanismos para monitorizar, detetar e responder a ataques, utilização indevida e avaria. |
| **Fornecedor&nbsp;(Microsoft&nbsp;Azure)** | Microsoft Azure Security definiu os requisitos para a monitorização do Active Directory. Serviço equipas configurar ferramentas de monitorização de Active Directory, de acordo com estes requisitos. Ferramentas de monitorização de Active Directory incluem o agente de monitorização (MA) e o System Centre Operations Manager (SCOM), que estão configurados para fornecer alertas em tempo real ao pessoal de segurança do Microsoft Azure em situações que requerem ação imediata. |


 ## <a name="ncsc-cloud-security-principle-54"></a>Princípio de segurança de nuvem NCSC 5.4
### <a name="incident-management"></a>Gestão de incidentes
A menos que estejam processos de gestão de incidentes previamente cuidadosamente planeada no local, decisões fracas se prevê a ser efetuada quando ocorrem incidentes, potencialmente exacerbating o impacto geral nos utilizadores.
Estes processos não precisa de ser complexo ou necessitam de grandes quantidades de descrição, mas a gestão de incidentes boa será minimise o impacto para os utilizadores de segurança, fiabilidade e problemas de ambiente com um serviço.


**Responsabilidades:**`Shared`

> [!NOTE]
> Nos casos em que os incidentes de segurança de cliente podem afetar o estado de segurança do Microsoft Azure, o cliente é responsável por notificar o Microsoft Azure.

|||
|---|---|
| **Cliente** | O cliente é responsável pelo estabelecimento de um processo de gestão de incidentes para os recursos implementados de cliente (incluir aplicações, sistemas operativos, bases de dados e software). A instrução de implementação do cliente deve abordar relatórios incidentes e os alertas, atempadas respostas de incidentes de suporte e informações de reencaminhamento para o PGA e outras organizações HMG conforme apropriado. |
| **Fornecedor&nbsp;(Microsoft&nbsp;Azure)** | Microsoft tiver implementado um processo de gestão de incidentes de segurança para facilitar uma resposta coordenada para incidentes um deve ocorrer. <br /> <br /> Se o Microsoft toma em consideração de qualquer acesso não autorizado a quaisquer dados de clientes armazenados no respetivo equipamento ou das suas instalações, ou acesso não autorizado a esse equipamento ou instalações, resultando em perda, divulgação ou alteração dos dados de cliente, o Microsoft tiver indicado que Este irá: <br /> <br />   -Notificar retomadas rapidamente o cliente de incidente de segurança; <br /> -Detetar investigar o incidente de segurança e forneça o cliente com informações detalhadas sobre o incidente de segurança; e <br /> -Tome medidas de razoáveis e linha de comandos para atenuar os efeitos e minimizar um danos resultantes de incidente de segurança.  <br />  <br /> Foi estabelecida uma arquitetura de gestão de incidentes com funções definidos e responsabilidades atribuídas. A equipa de gestão de incidentes (WASIM) do Windows Azure segurança é responsável por gerir incidentes de segurança, incluindo Escalamento e assegurar o envolvimento de equipas especialista em quando for necessário. Gestores de operações do Azure são responsáveis por supervisionar investigação e resolução de incidentes de segurança e privacidade com suporte de outras funções. <br /> <br /> Para obter mais informações sobre a resposta a incidentes da Microsoft processos consulte a descrição completa no [Azure Blueprint - princípios de segurança de nuvem NCSC - cliente responsabilidades matriz](https://aka.ms/blueprintuk-gcrm). |


 ## <a name="ncsc-cloud-security-principle-6"></a>Princípio de segurança de nuvem NCSC 6
### <a name="personnel-security"></a>Segurança do pessoal
Em que o técnico de fornecedor de serviço tem acesso aos seus dados e os sistemas tem um elevado grau de confiança no respetiva fidedignidade. E filtragens de detalhado, suportada por formação adequada, reduz a probabilidade de compromisso acidental ou malicioso pela equipa de fornecedor de serviço.
O fornecedor de serviço deve do requerente técnico e filtragens de segurança e formação de segurança regular. Técnico nestas funções deve compreender as responsabilidades. Fornecedores, deverá certificar-limpar como ecrã e gerir técnico dentro de funções com privilégios.


**Responsabilidades:**`Shared`


|||
|---|---|
| **Cliente** | O cliente é responsável por filtragem indivíduos e fornecer formação de segurança regular para indivíduos com acesso a recursos implementados no cliente. A instrução de implementação do cliente deve abordar os critérios e filtragens para funções e a frequência de formação de segurança. |
| **Fornecedor&nbsp;(Microsoft&nbsp;Azure)** | Técnico de Microsoft Azure que operam de serviços do Azure e fornece suporte ao cliente (ou subcontratados Microsoft que ajudá-lo com operações de plataforma, resolução de problemas e o suporte técnico) são submetidos a um Microsoft padrão em segundo plano (ou equivalente) certifique-se Avalie as educação do empregado, a sua admissão e histórico criminal. As verificações de fundo amplamente estão de acordo com os requisitos do Government de RU BPSS/BS7858. Não incluem uma verificação de identidade formal especificamente.  <br /> <br /> Microsoft inclui Aprovisiona nondisclosure nos respetivos contratos de empregado e subcontractor. Todos os funcionários da Microsoft adequados e subcontratados participem num programa de formação de segurança do Microsoft Azure patrocinado que informa os funcionários do respetivos responsabilidades de segurança de informações. <br /> <br /> Equipa de serviços do Microsoft Azure ou subcontratados suspeitas de consolidar a falhas de segurança e/ou a violação de política de segurança de informações estão sujeitos a um processo de investigação e as medidas adequadas disciplinary até e incluindo terminação. Se as circunstâncias garantir, Microsoft pode referir-se a fim de prosecution por uma agência de imposição da lei. <br /> <br /> Para completar este sistema de verificações e as educação de segurança, a Microsoft implementa combinações de controlos preventivo, defesas e reativa para ajudar a proteger contra programador não autorizado e/ou atividade administrativa, incluindo o seguinte mecanismos: <br />  <br /> -Controlos de acesso sólida nos dados confidenciais, incluindo um requisito de autenticação de dois fatores baseada em smart card efetuar as operações confidenciais. <br /> -Combinações de controlos de melhoram a deteção independente de atividade maliciosa. <br /> -Vários níveis de monitorização, registo e de relatórios. |


 ## <a name="ncsc-cloud-security-principle-7"></a>Princípio de segurança de nuvem NCSC 7
### <a name="secure-development"></a>Desenvolvimento seguro
Os serviços devem concebidos e desenvolvidos para identificar e mitigar ameaças para a segurança dos mesmos. Aqueles que não podem estar vulneráveis a problemas de segurança que podem comprometer os seus dados, resultar na perda de serviço ou ativar outras atividades maliciosas.


**Responsabilidades:**`Shared`


|||
|---|---|
| **Cliente** | As máquinas virtuais implementadas por esta Blueprint Azure executar sistemas operativos Windows. O Windows fornece a validação de integridade de ficheiro em tempo real, a proteção e recuperação de ficheiros de sistema de núcleos que são instaladas como parte do Windows ou atualizações do sistema Windows autorizadas através da capacidade de proteção de recursos do Windows (WRP), o que permite em tempo real a verificação de integridade. <br /> <br /> Windows tem proteções para impedir a execução do código em localizações de memória restrita: sem executar (NX), prazos de esquema de espaço de endereço (ASLR) e a prevenção de execução de dados (DEP). <br /> <br /> Este Blueprint Azure implementa as proteções de antimalware baseada no anfitrião para todos os Windows as máquinas virtuais implementadas implementadas através do Microsoft Windows Defender. O Windows Defender está configurado para atualizar automaticamente ambas as assinaturas antimalware da proteção e de motor como versão fique disponível. <br /> <br /> Para poder estar em conformidade com este princípio, ainda é necessária configuração pelo cliente para utilização em produção. Como tal, estas configurações terá de ser uma parte do processo de desenvolvimento seguro do cliente. |
| **Fornecedor&nbsp;(Microsoft&nbsp;Azure)** | O Microsoft Security Development Lifecycle (SDL) fornece um processo modelling de ameaça eficaz para identificar ameaças e vulnerabilidades no software e serviços. Ameaças modelling é um exercício de equipa, que abrange o operations manager, gestores de programa/projeto, os programadores e testers e representa uma tarefa de análise de segurança de chave efetuada para o design da solução. Os membros da equipa utilizam a ferramenta de Modelling de ameaça para SDL para modelar todos os serviços e projetos, quando estes são criados e quando forem atualizados com as novas funcionalidades e funções. Ameaças e modelos de abrangem todo o código exposto a superfície de ataque e todo o código escritas pelo ou está licenciado de terceiros, considere todos os limites de fidedignidade. O sistema STRIDE (Spoofing, violação, rejeição, divulgação de informações, recusa de serviço e elevação de privilégios) é utilizado para ajudar a identificar e resolver ameaças de segurança no início do processo de conceção antes de pode afetar a clientes. |


 ## <a name="ncsc-cloud-security-principle-8"></a>Princípio de segurança de nuvem NCSC 8
### <a name="supply-chain-security"></a>Segurança de cadeia de fornecimento
O fornecedor de serviço deve certificar-se de que a cadeia de fornecimento, suporta satisfatória todas as dos princípios de segurança que o serviço afirmações para implementar.
Serviços cloud, muitas vezes, confiarem em serviços e produtos de terceiros. Por conseguinte, se este princípio não está implementado, compromisso de cadeia de fornecimento pode undermine a segurança do serviço e afetar a implementação de outros princípios de segurança.


**Responsabilidades:**`Shared`


|||
|---|---|
| **Cliente** | O cliente é responsável por fornecer documentação de cadeia de fornecimento segura para quaisquer terceiros adquiriu o software e sistemas operativos utilizados na respetiva subscrição do Azure. A instrução de implementação do cliente deve abordar a exceção seguir os processos identificados por esta documentação de cadeia de fornecimento. |
| **Fornecedor&nbsp;(Microsoft&nbsp;Azure)** | O grupo do Microsoft Cloud fornecer cadeia (MCSC) consiste em seis equipas exclusivas cada contribuir para a proteção do Azure de ameaças para a cadeia de fornecer.  <br />  <br /> -Aprovisionamento <br /> -Operações de cliente <br /> -Qualidade implementação <br /> -Gestão de relação fornecedor <br /> -Spares <br />  <br /> Para obter mais informações sobre o grupo MCSC da Microsoft, consulte a descrição completa no [Azure Blueprint - princípios de segurança de nuvem NCSC - cliente responsabilidades matriz](https://aka.ms/blueprintuk-gcrm). |


 ## <a name="ncsc-cloud-security-principle-9"></a>Princípio de segurança de nuvem NCSC 9
### <a name="secure-user-management"></a>Proteger a gestão de utilizadores
O fornecedor deve disponibilizar as ferramentas para gerir em segurança a utilização do seu serviço. Os procedimentos e interfaces de gestão são uma parte vital a barreira de segurança, a impedir o acesso unauthorised e alteração dos seus recursos, aplicações e dados.

Aspetos a considerar são:

- Autenticação de utilizadores para interfaces de gestão e canais de suporte
- Separação e controlo de acesso dentro de interfaces de gestão



 ## <a name="ncsc-cloud-security-principle-91"></a>Princípio de segurança de nuvem NCSC 9.1
### <a name="authentication-of-users-to-management-interfaces-and-within-support-channels"></a>Autenticação de utilizadores para Interfaces de gestão e dentro de canais de suporte
Para manter um serviço seguro, a necessidade dos utilizadores sejam autenticados corretamente antes de efetuar atividades de gestão, falhas de relatório ou pedido de alterações para o serviço.
Estas atividades podem ser efetuadas através de um portal de web de gestão de serviço ou através de outros canais, tais como o telefone ou e-mail. É provável que incluem essas funções como aprovisionamento novos elementos de serviço, a gerir contas de utilizador e a gestão de dados do utilizador.
Fornecedores de serviços tem de garantir que todos os pedidos de gestão que pode ter um impacto de segurança são efetuados através de canais seguros e autenticados. Se os utilizadores não são autenticados vivamente, em seguida, um impostor poderá com êxito efetuar ações privilegiadas, pondo em risco a segurança dos dados ou serviço.


**Responsabilidades:**`Shared`


|||
|---|---|
| **Cliente** | Quando os administradores de aceder ao Portal do Microsoft Azure para gerir recursos do Azure para a sua organização, os dados transmitidos entre o portal e o dispositivo do administrador são enviados ao longo de um canal encriptado de Transport Layer Security (TLS) utilizar RSA de 2048 bits / Chaves de encriptação de SHA256, tal como recomendado pela CESG/NCSC.  <br /> <br /> Este Blueprint Azure emprega autenticação do Windows, ambiente de trabalho remoto e o BitLocker. Estes componentes podem ser configurados confiar nos módulos criptográficos FIPS 140 validada. <br /> <br /> Este Blueprint do Azure impõe autorizações acesso lógico a utilizar o controlo de acesso baseado em funções imposto pelo Azure Active Directory por atribuir utilizadores a funções, do Active Directory através da atribuição de utilizadores para grupos de segurança e controlos de nível de SO Windows. Funções do Azure Active Directory atribuídos a utilizadores ou grupos de controlam o acesso lógico a recursos no Azure ao nível do recurso, grupo ou subscrição. Grupos de segurança do Active Directory controlam o acesso lógico a funções e recursos de nível de SO. |
| **Fornecedor&nbsp;(Microsoft&nbsp;Azure)** | Os clientes administrar os seus recursos do Azure através do portal do Azure, que fornece acesso a todas as máquinas virtuais, bases de dados, serviços em nuvem, e outros recursos configurados para a conta do cliente. Acesso Web ao portal do Azure está protegido por ligações de Transport Layer Security (TLS) 1.2 de norma da indústria utilizando chaves de encriptação RSA/SHA256 de 2048 bits, como recomendado pela CESG/NCSC. Controlos de acesso baseado em funções são fornecidos para permitir aos clientes fornecer acesso limitado aos recursos de gestão do Azure para utilizadores e grupos específicos. |


 ## <a name="ncsc-cloud-security-principle-92"></a>Princípio de segurança de nuvem NCSC 9.2
### <a name="separation-and-access-control-within-management-interfaces"></a>Separação e controlo de acesso dentro de Interfaces de gestão
Muitos serviços em nuvem são geridos através de aplicações web ou de APIs. Estas interfaces são uma parte de chave de segurança do serviço. Se os utilizadores não são separados adequadamente dentro de interfaces de gestão, um utilizador poderá afetar o serviço ou modificar os dados de outro.
As contas com privilégios administrativas provavelmente tem acesso a grandes volumes de dados. Constraining as permissões de utilizadores individuais para esses absolutamente necessário pode ajudar a limitar os danos causados por utilizadores mal intencionados, credenciais comprometidas ou dispositivos comprometidos.
Controlo de acesso baseado em funções fornece um mecanismo para atingir esse objetivo e é provável que haja uma capacidade particularmente importante para os utilizadores gerir implementações maiores.
Expor interfaces de gestão a menor redes (por exemplo, Comunidade em vez de redes públicas) torna mais difícil para os atacantes aceder e ataques, tal como faria primeiro têm de ter acesso a uma destas redes. Orientações sobre a avaliação dos riscos de exposição das interfaces a diferentes tipos de redes é fornecida ao abrigo 11 princípio.


**Responsabilidades:**`Shared`


|||
|---|---|
| **Cliente** | Este Blueprint Azure implementa um Gateway de aplicação, o Balanceador de carga e configura as regras de grupo de segurança de rede para controlar commutations limites externos e entre sub-redes internas. Funcionalidade de utilizador está separada da funcionalidade de gestão do sistema através da imposição de controlos de acesso lógico e arquitetura de sistema. Interfaces para a funcionalidade de gestão do sistema são separadas das interfaces de utilizador. Conectividade da gestão de todos os é através de um bastion segura anfitrião (jumpbox) localizado numa sub-rede gestão com regras de grupo de segurança de rede para limitar o acesso a recursos de produção, conforme apropriado. |
| **Fornecedor&nbsp;(Microsoft&nbsp;Azure)** | Conforme descrito na separação entre separação do utilizador está incorporada no Azure o núcleo. Azure Active Directory (Azure AD ou o AAD) podem ser utilizados para fornecer a cada utilizador que efetua a autenticação para o portal do Azure com acesso a apenas os direito para ver e gerir os recursos. Como resultado, as contas de cliente diferentes são estritamente separadas entre si quando gerido através do portal do Azure comuns. |


 ## <a name="ncsc-cloud-security-principle-10"></a>Princípio de segurança de nuvem NCSC 10
### <a name="identity-and-authentication"></a>Identidade e autenticação
Todos os acessos a interfaces de serviço devem ser restrita para utilizadores autenticados e authorised.
Autenticação fraca para estas interfaces pode ativar o acesso unauthorised aos seus sistemas, resultando no roubo ou modificação dos seus dados, as alterações ao seu serviço, ou um denial of service.
Importante ainda, deve ocorrer autenticação através de canais segurados. Correio eletrónico, HTTP ou telefone estão vulneráveis a ataques interception e de engenharia social.


**Responsabilidades:**`Shared`


|||
|---|---|
| **Cliente** | Este Blueprint Azure emprega do Active Directory para gestão de contas. Acesso a recursos implementados por este Blueprint Azure se encontra protegido contra ataques de repetição, as funcionalidades de Kerberos incorporada do Azure Active Directory, do Active Directory e o sistema operativo Windows. A autenticação Kerberos, o autenticador enviado pelo cliente contém dados adicionais, tais como uma lista IP encriptada, carimbos de cliente e duração de permissão. Se um pacote é reproduzido, o timestamp é verificado. Se o timestamp é anterior ao, ou o mesmo que um autenticador anterior, o pacote foi rejeitado. |
| **Fornecedor&nbsp;(Microsoft&nbsp;Azure)** | O Azure oferece serviços para ajudar a controlar a identidade, bem como integrá-lo com os arquivos de identidade que já podem estar em utilização. Azure AD é um serviço de gestão de identidades e acessos abrangente para a nuvem que ajuda a proteger o acesso a dados no local e as aplicações em nuvem. Azure AD também simplifica a gestão de utilizadores e grupos através da combinação de serviços de diretório principais, governação de identidade, segurança e gestão de acesso de aplicações avançadas. |


 ## <a name="ncsc-cloud-security-principle-11"></a>Princípio de segurança de nuvem NCSC 11
### <a name="external-interface-protection"></a>Proteção da Interface externa
Todas as interfaces externas ou menos fidedignas do serviço devem ser identificadas e defended adequadamente.
Se algumas das interfaces expostas são privadas (por exemplo, interfaces de gestão), em seguida, o impacto de compromisso pode ser mais significativo.
Pode utilizar modelos diferentes para ligar a serviços em nuvem que expõem os seus sistemas empresariais para diferentes níveis de risco.


**Responsabilidades:**`Shared`


|||
|---|---|
| **Cliente** | Este Blueprint Azure implementa recursos uma arquitetura com uma sub-rede separada web, uma sub-rede de base de dados, sub-rede do Active Directory e sub-rede de gestão. Sub-redes logicamente são separados por regras de grupos de segurança de rede aplicadas às sub-redes individuais para restringir o tráfego entre sub-redes de forma a que só é necessário para a funcionalidade de gestão e de sistema (por exemplo, o tráfego externo não consegue aceder à base de dados, gestão, ou sub-redes do Active Directory).  <br /> <br /> Um Gateway de aplicação é implementado para gerir as ligações externas para uma aplicação web implementada de cliente. Ligações externas para acesso de gestão estão limitadas a jumpbox (anfitrião de bastion) implementado uma sub-rede de gestão com regras de segurança de rede aplicadas para restringir ligações externas ao autorizados endereços IP. |
| **Fornecedor&nbsp;(Microsoft&nbsp;Azure)** | Microsoft utiliza um método chama "Red agrupamento" para melhorar a processos através de teste regular penetração e controlos de segurança do Azure. A equipa de Red é um grupo de funcionários trabalhadores a tempo inteiro na Microsoft que se centra na efetuar direcionados e persistentes ataques contra Microsoft infraestrutura, plataformas e aplicações, mas não aplicações de fim-clientes ou dados. <br /> <br /> Para obter mais informações sobre o agrupamento de Red da Microsoft, bem como uma descrição do agrupamento azul esforços, consulte a descrição completa no [Azure Blueprint - princípios de segurança de nuvem NCSC - cliente responsabilidades matriz](https://aka.ms/blueprintuk-gcrm).  |


 ## <a name="ncsc-cloud-security-principle-12"></a>Princípio de segurança de nuvem NCSC 12
### <a name="secure-service-administration"></a>Proteger o serviço de administração
Sistemas utilizados para administração de um serviço em nuvem serão altamente com acesso privilegiado a esse serviço. Os respetivos comprometimento teria um impacto significativo, incluindo os meios para ignorar os controlos de segurança e roubar ou manipular grandes volumes de dados.
A estrutura, implementação e gestão dos sistemas de administração devem seguir boa prática enterprise, enfrenta recognising o respetivo valor elevado aos atacantes.


**Responsabilidades:**`Shared`


|||
|---|---|
| **Cliente** | O cliente é responsável por assegurar que uma estação de trabalho segura para a administração da respetiva subscrição do Azure e os recursos implementados de cliente (para incluir aplicações, sistemas operativos, bases de dados e software). A instrução de implementação do cliente deve abordar os mecanismos utilizados para mitigar o risco de exploração de recursos implementados no cliente. |
| **Fornecedor&nbsp;(Microsoft&nbsp;Azure)** | Técnico de operações do Microsoft Azure é necessários para utilizar estações de trabalho administração seguro (SAWs; além disso, conhecido como estações de trabalho de acesso privilegiado ou PAWs). A abordagem VIMOS é uma extensão com a prática recomendada bem estabelecida para utilizar admin separada e as contas de utilizador para técnico administrativos. Esta prática utiliza uma conta de administrador individualmente atribuída é completamente independente da conta de utilizador padrão do utilizador. VIMOS baseia-se que prática de separação de conta ao fornecer uma estação de trabalho fidedigna para essas contas confidenciais. |


 ## <a name="ncsc-cloud-security-principle-13"></a>Princípio de segurança de nuvem NCSC 13
### <a name="audit-information-for-users"></a>Informações de auditoria para os utilizadores
Deve ser fornecido com a auditoria, registos necessários para monitorizar o acesso ao seu serviço e os dados retidos dentro da mesma. O tipo de informações de auditoria disponíveis e que terá um impacto direto na sua capacidade para detetar e responder à atividade inapropriada ou maliciosa na timescales razoável.


**Responsabilidades:**`Shared`


|||
|---|---|
| **Cliente** | Os eventos auditados por esta Blueprint Azure incluem os auditada por registos de atividade do Azure para recursos implementados, ao nível do SO registos e registos do Active Directory. Estes registos de eventos incluem informações suficientes para determinar quando ocorrem eventos, a origem do evento, o resultado de evento e outras informações detalhadas que suporte a investigação de incidentes de segurança. Os clientes poderão selecionar eventos adicionais a serem auditados para satisfazer as necessidades de missão. Todos os recursos do Azure, tiver registos de auditoria disponíveis no Portal do Azure. |
| **Fornecedor&nbsp;(Microsoft&nbsp;Azure)** | Análise de registos do Azure recolhe registos de eventos ocorrer dentro de uma organização sistemas e redes, assim que estes ocorram, antes que qualquer pessoa pode adulterá-los e permite a diferentes tipos de análise por correlacionar dados em vários computadores. Azure permite aos clientes efetuar a geração de eventos de segurança e a recolha do IaaS do Azure e PaaS funções para o armazenamento central nas suas subscrições. Estes eventos recolhidos podem ser exportados para as informações de segurança no local e sistemas de gestão (SIEM) de evento para a monitorização em curso. Depois dos dados são transferidos para o armazenamento, existem muitas opções para ver os dados de diagnóstico. <br /> <br /> Diagnóstico de incorporadas do Azure pode ajudar a depurar. Para aplicações que são implementadas no Azure, um conjunto de eventos de segurança do sistema operativo estão ativadas por predefinição. Os clientes podem adicionar, remover ou modificar eventos a serem auditados ao personalizar a política de auditoria do sistema operativo. <br /> <br /> Um nível elevado, é fácil bastante e simples começar a recolher registos utilizar o reencaminhamento de eventos de Windows (WEF) ou mais avançadas do diagnóstico do Azure quando as VMs baseadas no Windows são implementadas utilizando o IaaS no Azure. Além disso, o diagnóstico do Azure pode ser configurado para recolher os registos e eventos de instâncias de função de PaaS. Ao utilizar VMs baseadas no IaaS, um cliente é simplesmente configura e permite que os eventos de segurança desejados da mesma forma que permitem que os servidores do Windows para iniciar as auditorias na respetiva datacentre no local. Para aplicações web, também é possível ativar o registo do IIS, se for esse o principal da aplicação e a implementação no Azure. Os clientes podem sempre armazenar dados de segurança em contas do storage no geolocalizações suportadas à escolha para satisfazer os requisitos de soberania dos dados. |


 ## <a name="ncsc-cloud-security-principle-14"></a>Princípio de segurança de nuvem NCSC 14
### <a name="secure-use-of-the-service"></a>Proteger a utilização do serviço
A segurança dos serviços em nuvem e os dados retidos dentro delas pode ser undermined se utilizar o serviço mal. Por conseguinte, terá de determinados responsabilidades ao utilizar o serviço pela ordem dos seus dados para ser protegido adequado.
A extensão da sua responsabilidade irá variar consoante os modelos de implementação do serviço em nuvem e o cenário em que pretende utilizar o serviço. Funcionalidades específicas dos serviços individuais podem também ter efeito. Por exemplo, como uma rede de entrega de conteúdos protege a chave privada ou, como um fornecedor de pagamento de nuvem Deteta as transações fraudulentas, são considerações de segurança importante over and above as considerações gerais abrangidas por princípios de segurança da nuvem.  
Com ofertas de IaaS e PaaS, for o responsável pela significativos aspetos de segurança dos seus dados e cargas de trabalho. Por exemplo, se a obter um IaaS computação instância, normalmente será responsável por instalar um sistema operativo moderno, configurar esse sistema operativo de forma segura, implementar em segurança todas as aplicações e também manter essa instância através da aplicação patches ou efetuar a manutenção necessária.


**Responsabilidades:**`Customer`

> [!NOTE]
> O cliente pode utilizar o Centre de segurança do Azure para o ajudar a evitar, detetar e responder a ameaças com uma maior visibilidade e controlo sobre a segurança dos recursos do Azure. Centre de segurança do Azure fornece gestão de políticas e monitorização de segurança integrada nas subscrições do Azure, ajuda a detetar ameaças que caso contrário podem passar despercebidas e funciona com um ecossistema abrangente de soluções de segurança.

|||
|---|---|
| **Cliente** | Os modelos Azure Resource Manager e o associada recursos que compõem este Blueprint Azure siga uma abordagem de defence-na profundidade para segurança. Para poder estar em conformidade com este princípio, é necessária configuração adicional pelo cliente para utilização em produção (por exemplo, software de gestão de base de dados, implementação de aplicação web). |
| **Fornecedor&nbsp;(Microsoft&nbsp;Azure)** | Não Aplicável |

## <a name="disclaimer"></a>Exclusão de Responsabilidade

 - Este documento destina-se apenas a fins informativos. MICROSOFT FAZ COM QUE NÃO OFERECE NENHUMA GARANTIA, EXPRESSAS, IMPLÍCITA OU ESTATUTARIAMENTE AS INFORMAÇÕES NESTE DOCUMENTO. Este documento é fornecido "como-está." As informações e opiniões expressados neste documento, incluindo URLs e outras referências de Web site da Internet, podem ser alteradas sem aviso prévio. Os clientes ler este documento da sua responsabilidade utilizá-la.
 - Este documento não fornece aos clientes com quaisquer direitos legais a nenhuma propriedade intelectual de qualquer produto da Microsoft ou de soluções.
 - Os clientes podem copiar e utilizar este documento para efeitos de referência interno.
 - Algumas recomendações neste documento poderão resultar numa maior dados, a rede ou a utilização de recursos de computação no Azure e podem aumentar os custos de licenciamento ou de subscrição do Azure do cliente.
 - Esta arquitetura destina-se para servir como base para os clientes ajustar os seus requisitos específicos e não devem ser utilizada como-é num ambiente de produção.
 - Este documento é desenvolvido como uma referência e não deve ser utilizado para definir todas as formas através do qual um cliente pode cumpre os requisitos de conformidade específico e regulamentos. Os clientes devem procurar suporte legal da respetiva organização em implementações de cliente aprovados.