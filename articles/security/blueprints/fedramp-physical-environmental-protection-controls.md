---
title: "FedRAMP Azure Blueprint automatização - proteção física e ambiental"
description: "Aplicações Web para FedRAMP - proteção física e ambiental"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 0bf8349b-450d-413c-a535-6f7b80b82781
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: 9e9e473285f4e82e1da40b8c3d496d65733fbd45
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2017
---
# <a name="physical-and-environmental-protection-pe"></a>Proteção física e ambiental (PE)

> [!NOTE]
> Estes controlos são definidos pelo NIST e para os E.U.A. Departamento de comércio como parte da revisão a publicação especial do NIST 800-53 4. Consulte Rev 4 do NIST 800-53. para obter informações sobre testes de procedimentos e as diretrizes para cada controlo.

## <a name="nist-800-53-control-pe-1"></a>PE de controlo do NIST 800-53-1

#### <a name="physical-and-environmental-protection-policy-and-procedures"></a>Política de proteção física e ambiental e procedimentos

**PE-1** organização desenvolvidas pela organização, documentos e disseminates para [atribuição: técnico definido pela organização ou funções] uma política de proteção física e ambiental objetivo, âmbito, funções, responsabilidades, gestão de endereços compromisso, coordenação entre entidades organizacionais e de conformidade; e procedimentos para facilitar a implementação da política de proteção física e ambiental e controlos de proteção associados física e ambiental; analisa e atualiza a política de proteção física e ambiental atual [atribuição: frequência definida de organização]; e procedimentos de proteção física e ambiental [atribuição: frequência definida de organização].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Política de proteção de nível empresarial física e ambiental e procedimentos do cliente podem ser suficientes para este controlo de endereços. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-pe-2a"></a>Controlo PE-2.a do NIST 800-53

#### <a name="physical-access-authorizations"></a>Autorizações de acesso físico

**PE 2.a** organização desenvolvidas pela organização, aprova e mantém uma lista de pessoas com acesso autorizado ao local de onde reside o sistema de informações.

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a quaisquer recursos de sistema nos centros de dados do Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure implementa este requisito em nome dos clientes. O acesso físico a um centro de dados do Microsoft deve ser aprovado pela equipa de gestão de centros de dados (DCM) utilizando a ferramenta de acesso do Centro de dados. Atribuições de acesso necessitam de uma data de fim, após o qual o acesso é removido automaticamente e tem de ser reapproved. Além disso, quando já não é necessário acesso, officers de segurança do Centro de dados ou gestão manualmente pedir a terminação de acesso. |


 ## <a name="nist-800-53-control-pe-2b"></a>Controlo PE-2.b do NIST 800-53

#### <a name="physical-access-authorizations"></a>Autorizações de acesso físico

**PE 2.b** organização emite credenciais de autorização de acesso de instalações.

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a quaisquer recursos de sistema nos centros de dados do Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure implementa este requisito em nome dos clientes. A ferramenta de acesso do Centro de dados é a origem autoritativa listar todos os empregados com acesso autorizado ao centro de dados específico. A ferramenta está ligada com dispositivos de controlo de acesso de segurança física do Centro de dados e autoriza o acesso com base nos níveis de acesso que forem aprovadas pela equipa de DCM. Níveis de acesso estão atribuídos na ferramenta para a Microsoft de um utilizador emitido distintivo ou um destaque de acesso temporário que é atribuído no Centro de dados por Supervisor de sala de controlo (CR). Níveis de acesso são aprovadas pela equipa de DCM. Para além de credenciais atribuídas a destaques físicos, algumas áreas do Centro de dados exigem inscrição dos dados de Biometria do utilizador (geometria da mão ou impressão digital). |


 ## <a name="nist-800-53-control-pe-2c"></a>Controlo PE-2.c do NIST 800-53

#### <a name="physical-access-authorizations"></a>Autorizações de acesso físico

**PE 2.c** organização revê a lista de acesso com detalhes sobre instalações autorizados acesso pessoas [atribuição: frequência definida de organização].

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a quaisquer recursos de sistema nos centros de dados do Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure implementa este requisito em nome dos clientes. Para além da revogação do acesso descrita na parte de um Microsoft Azure revisões autorizado listas de acesso para os datacenters do Azure todos os 90 dias para remover/atualização individual acesso conforme necessário. |


 ## <a name="nist-800-53-control-pe-2d"></a>Controlo PE-2.d do NIST 800-53

#### <a name="physical-access-authorizations"></a>Autorizações de acesso físico

**PE 2.d** organização remove indivíduos da lista de acesso das instalações quando já não é necessário acesso.

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a quaisquer recursos de sistema nos centros de dados do Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure implementa este requisito em nome dos clientes. Microsoft Azure remove acesso automaticamente quando a data de fim de atribuição de acesso for atingida. Quando já não é necessário acesso, gestão ou officers de segurança do Centro de dados manualmente solicitará a terminação de acesso na ferramenta de acesso do Centro de dados. Além disso, o Microsoft Azure irá remover qualquer autorizações de acesso desnecessárias detetadas devido a revisão do acesso lista descrito na parte c. |


 ## <a name="nist-800-53-control-pe-3a"></a>Controlo PE-3.a do NIST 800-53

#### <a name="physical-access-control"></a>Controlo de acesso físico

**PE 3.a** a organização impuser autorizações de acesso físico em [atribuição: entrada/saída definido de organização aponta para o local de onde reside o sistema de informações] verificando autorizações acesso individuais antes de conceder acesso a instalações; e controlar a entrada/saída para a instalação utilizando [seleção (um ou mais): [atribuição: controlo de acesso físico definido de organização sistemas/dispositivos]; guards].

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a quaisquer recursos de sistema nos centros de dados do Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure implementa este requisito em nome dos clientes. Microsoft Azure impõe autorizações de acesso físico para todos os pontos de acesso físico para centros de dados do Azure utilizando 24x7 staffing, alarmes, vídeo vigilância, a autenticação multifator e dispositivos do portal de acesso restrito. |


 ## <a name="nist-800-53-control-pe-3b"></a>Controlo PE-3.b do NIST 800-53

#### <a name="physical-access-control"></a>Controlo de acesso físico

**PE 3.b** organização mantém os registos de auditoria de acesso físico para [atribuição: pontos de entrada/saída definido de organização].

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a quaisquer recursos de sistema nos centros de dados do Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure implementa este requisito em nome dos clientes. Todos os acessos a instalações do Centro de dados do Azure são registados e auditados. |


 ## <a name="nist-800-53-control-pe-3c"></a>Controlo PE-3.c do NIST 800-53

#### <a name="physical-access-control"></a>Controlo de acesso físico

**PE 3.c** organização fornece [atribuição: as proteções de segurança definido para organização] para controlar o acesso a áreas dentro do local de oficialmente designado como acessível publicamente.

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a quaisquer recursos de sistema nos centros de dados do Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure implementa este requisito em nome dos clientes. Os datacenters do Azure não contêm áreas que são designadas como acessível publicamente. |


 ## <a name="nist-800-53-control-pe-3d"></a>Controlo PE-3.d do NIST 800-53

#### <a name="physical-access-control"></a>Controlo de acesso físico

**PE 3.d** organização escorts visitantes e monitoriza as atividades de visitantes [atribuição: circunstâncias definido de organização exigir escorts de visitantes e monitorização].

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a quaisquer recursos de sistema nos centros de dados do Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure implementa este requisito em nome dos clientes. Todos os visitantes que aprovou o acesso ao centro de dados (consulte PE 2) são designados como 'Escort apenas' no respetivos destaques ou através de outros rectângulos visual (por exemplo, destaques coloridos) e são necessários permanecem com as respetivas escorts permanente. Visitantes escorted não tem quaisquer níveis de acesso que lhes são concedidos e só podem viajam no acesso do respetivos escorts. Escorts monitorizar todas as atividades do respetivo visitantes enquanto no Centro de dados. |


 ## <a name="nist-800-53-control-pe-3e"></a>Controlo PE-3.e do NIST 800-53

#### <a name="physical-access-control"></a>Controlo de acesso físico

**PE 3.e** A organização protege as chaves, combinações e outros dispositivos de acesso físico.

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a quaisquer recursos de sistema nos centros de dados do Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure implementa este requisito em nome dos clientes. Chaves físicas e destaques de acesso temporário estão protegidos dentro do Centro de operações de segurança (SOC). Officers de segurança são 24x7 staffed. As chaves são modificadas ao pessoal específico por correspondente destaque de acesso da pessoa para a chave física. A realização de inventários de chaves são um durante cada shift e não são permitidas chaves para ser levada para outro local. |


 ## <a name="nist-800-53-control-pe-3f"></a>Controlo PE-3.f do NIST 800-53

#### <a name="physical-access-control"></a>Controlo de acesso físico

**PE 3.f** inventários de organização [atribuição: dispositivos de acesso físico definido de organização] cada [atribuição: frequência definida de organização].

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a quaisquer recursos de sistema nos centros de dados do Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure implementa este requisito em nome dos clientes. Dispositivos de acesso físico dentro dos centros de dados do Azure são inventariados em, pelo menos, uma base anuais. As chaves e destaques de acesso temporário são inventariadas em várias vezes por dia durante cada shift. Leitores de distintivo de acesso e semelhantes dispositivos de acesso estão ligados ao sistema de segurança física onde o estado é continuamente representado. |


 ## <a name="nist-800-53-control-pe-3g"></a>Controlo PE-3.g do NIST 800-53

#### <a name="physical-access-control"></a>Controlo de acesso físico

**PE 3.g** organização alterações combinações e chaves [atribuição: frequência definida de organização] e/ou quando as chaves são perdidas, combinações de são comprometidas, ou se a indivíduos são transferidos e foi terminados.  

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a quaisquer recursos de sistema nos centros de dados do Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure implementa este requisito em nome dos clientes. Centros de dados do Microsoft Azure tem procedimentos para implementar em casos quando um destaque de acesso ou chave é perdido ou uma pessoa é terminada transferida. Em caso de um terminação ou transferência, acesso a pessoas é imediatamente removido do sistema e as respetivas destaque acesso removida. |


 ### <a name="nist-800-53-control-pe-3-1"></a>PE de controlo do NIST 800-53-3 (1)

#### <a name="physical-access-control--information-system-access"></a>Controlo de acesso físico | Acesso do sistema de informações

**PE-3 (1)** a organização impuser autorizações de acesso físico para o sistema de informações para além dos controlos de acesso físico para instalações em [atribuição: espaços físicos definido pela organização que contém um ou mais componentes do o sistema de informações].

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a quaisquer recursos de sistema nos centros de dados do Azure. |
| **Fornecedor (Microsoft Azure)** | Obter o Microsoft Azure restringe áreas dentro os datacenters da Microsoft que contenham sistemas críticos (por exemplo, colocations ambientes críticos, gabinetes MDF, etc.) através de vários mecanismos de segurança, tais como o controlo de acesso eletrónicas, dispositivos biométricos, e controlos de anti passback. É concedido acesso ao Azure colocations como um nível superior, separado de acesso DCAT que outras áreas de acesso do Centro de dados. Além disso, todas as do Azure FTE e fornecedores que têm acesso para os colocations Azure Government são necessários para formally são submetidos a filtragem de nuvem da Microsoft e verificação de aceita E.U.A. antes de ser autorizado acesso para o ambiente. Consulte a secção de PS-03 para obter mais detalhes sobre o e filtragens nuvem para os colocations Azure Government. |


 ## <a name="nist-800-53-control-pe-4"></a>NIST 800-53 controlo PE-4

#### <a name="access-control-for-transmission-medium"></a>Controlo de acesso para a meio de transmissão

**PE 4** organização controla o acesso físico aos [atribuição: linhas de transmissão e a distribuição do sistema de informações definidas pela organização] dentro de instalações organizacionais utilizando [atribuição: as proteções de segurança definido para organização] .

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a quaisquer recursos de sistema nos centros de dados do Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure implementa este requisito em nome dos clientes. Microsoft Azure tiver implementado o controlo de acesso para a meio de transmissão através da estrutura e a criação dos gabinetes de moldura de distribuição de Main (MDF) e colocations para proteger linhas de transmissão e a distribuição do sistema de informações de danos acidentais, interrupção e adulteração físico. Acesso MDF gabinetes e colocations exigem dois fator de autenticação (destaque de acesso e biometria). Isto garante que o acesso é restringido a apenas empregados autorizados (consulte PE-2, PE 3). Dentro do MDF, linhas de transmissão e distribuição estão protegidas de danos acidentais, interrupção e adulteração física através da utilização de metal conduits, bastidores bloqueados, cages ou trays cabo. |


 ## <a name="nist-800-53-control-pe-5"></a>NIST 800-53 controlo PE-5

#### <a name="access-control-for-output-devices"></a>Controlo de acesso para dispositivos de saída

**PE 5** organização controla o acesso físico aos dispositivos de saída do sistema de informações para impedir que pessoas não autorizadas obter a saída.

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a quaisquer recursos de sistema nos centros de dados do Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure implementa este requisito em nome dos clientes. Centros de dados do Microsoft Azure não dispõe de dispositivos de saída (monitores, impressoras, dispositivos de áudio, etc.) permanentemente ligados aos recursos do Azure ou recursos partilhados de Azure. Além de não ter os dispositivos de saída, officers segurança efetuar instruções físicas do local de várias vezes por shift verificação para itens como portas que está a ser bloqueadas e bastidores que está a ser protegidas. Acesso de centro de dados é limitado para as pessoas que aprovou autorizações de acesso. Colocations requerem que dois fator de autenticação (destaque de acesso e biometria) para obter acesso. |


 ## <a name="nist-800-53-control-pe-6a"></a>Controlo PE-6.a do NIST 800-53

#### <a name="monitoring-physical-access"></a>Monitorização de acesso físico

**PE 6.a** organização monitoriza acesso físico ao local de onde reside o sistema de informações para detetar e responder a incidentes de segurança física.

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a quaisquer recursos de sistema nos centros de dados do Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure implementa este requisito em nome dos clientes. Acesso físico é monitorizado por implementar dispositivos de segurança e os processos dos centros de dados. Os exemplos incluem 24x7 monitorização eletrónica de controlo de acesso, alarme e sistemas de vídeos, bem como 24x7 em patrols de segurança do site do grounds e das instalações. Um Supervisor de sala de controlo está localizado no SOC permanente para fornecer monitorização de acesso físico no Centro de dados. |


 ## <a name="nist-800-53-control-pe-6b"></a>PE do controlo NIST 800-53-6. b

#### <a name="monitoring-physical-access"></a>Monitorização de acesso físico

**PE-6. b** organização revê registos de acesso físico [atribuição: frequência definida de organização] e após a ocorrência de [atribuição: os eventos definidos por organização ou potenciais indicações de eventos].

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a quaisquer recursos de sistema nos centros de dados do Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure implementa este requisito em nome dos clientes. Registos de acesso físico são revistos continuamente e mantidos para revisão de investigação subsequente. |


 ## <a name="nist-800-53-control-pe-6c"></a>PE do controlo NIST 800-53-6. c

#### <a name="monitoring-physical-access"></a>Monitorização de acesso físico

**PE-6. c** organização coordena os resultados de revisões e as investigações com a capacidade de resposta a incidentes organizacional. 

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a quaisquer recursos de sistema nos centros de dados do Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure implementa este requisito em nome dos clientes. Eventos de segurança que ocorrem no Centro de dados estão documentados pela equipa de segurança. A equipa de segurança cria relatórios que os detalhes de um evento de segurança depois de ocorrer o evento de captura. <br /> Para incidentes que necessitam de notificação de government, será coordenar a equipa de segurança do Microsoft Azure com o fornecedor de principais de aplicação (por exemplo, Office 365) para notificar o cliente de agência government, nos certificados e FedRAMP dentro diretrizes E.U.A.-certificados (consulte IR-6). |


 ### <a name="nist-800-53-control-pe-6-1"></a>PE de controlo do NIST 800-53-6 (1)

#### <a name="monitoring-physical-access--intrusion-alarms--surveillance-equipment"></a>Monitorização de acesso físico | Intrusões alarmes / vigilância equipamento

**PE-6 (1)** organização monitoriza alarmes intrusões físico e de equipamento de vigilância.

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a quaisquer recursos de sistema nos centros de dados do Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure implementa este requisito em nome dos clientes. Além da segurança de trazidos 24x7, centros de dados do Microsoft Azure (concedido e totalmente gerido) também utilizam monitorização CCTV e sistemas de alarme. Os alarmes são monitorizados e emitidos pelo Supervisor de sala de controlo stationed 24x7 o SOC. Durante uma situação de resposta, o Supervisor de sala de controlo utiliza câmaras na área de incidente que está a ser investigado para dar o dispositivo de resposta informações em tempo real. |


 ### <a name="nist-800-53-control-pe-6-4"></a>PE de controlo do NIST 800-53-6 (4)

#### <a name="monitoring-physical-access--monitoring-physical-access-to-information-systems"></a>Monitorização de acesso físico | Acesso físico aos sistemas de informação de monitorização

**PE-6 (4)** organização monitoriza acesso físico para o sistema de informações para além de monitorização de acesso físico do local de como [atribuição: espaços físicos definido pela organização que contém um ou mais componentes das informações sistema].

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a quaisquer recursos de sistema nos centros de dados do Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure monitoriza o acesso físico a instalações de, bem como os sistemas de informação dentro dos centros de dados. Equipamento dos todos os Microsoft online services é colocado em localizações de centros de dados, onde o acesso físico é monitorizado. Todos os colocalização e gabinetes MDF estão protegidos pelo controlo de acesso, alarmes e vídeo. |


 ## <a name="nist-800-53-control-pe-8a"></a>Controlo PE-8.a do NIST 800-53

#### <a name="visitor-access-records"></a>Registos de acesso de visitantes

**PE 8.a** organização mantém os registos de acesso de visitantes ao local de onde reside o sistema de informações para [atribuição: organização definidos pelo período de tempo].

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a quaisquer recursos de sistema nos centros de dados do Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure implementa este requisito em nome dos clientes. Registos de acesso do Centro de dados são mantidos na ferramenta de acesso do Centro de dados sob a forma de pedidos aprovados. Conforme descrito em PE 3, visitantes têm de ser escorted permanente. O escort acesso no Centro de dados é registado e se for necessário pode ser correlacionado com o visitante para revisão futura. |


 ## <a name="nist-800-53-control-pe-8b"></a>Controlo PE-8.b do NIST 800-53

#### <a name="visitor-access-records"></a>Registos de acesso de visitantes

**PE 8.b** organização revê registos de acesso de visitantes [atribuição: frequência definida de organização].

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a quaisquer recursos de sistema nos centros de dados do Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure implementa este requisito em nome dos clientes. Visitantes com um pedido de acesso aprovados terá o respetivo registo de acesso revisto momento a que sua identificação é verificada em relação a um formulário de government emitido ID ou Microsoft emitido destaque. Conforme descrito em PE 3, visitantes são escorted permanente, o Centro de dados. |


 ### <a name="nist-800-53-control-pe-8-1"></a>PE de controlo do NIST 800-53-8 (1)

#### <a name="visitor-access-records--automated-records-maintenance--review"></a>Registos de acesso de visitantes | Automatizada registos manutenção / rever

**PE-8 (1)** organização emprega automatizados mecanismos para facilitar a manutenção e revisão dos registos de acesso de visitantes.

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a quaisquer recursos de sistema nos centros de dados do Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure mantém os registos de acesso do Centro de dados no DCAT sob a forma de pedidos DCAT aprovados. Só podem ser aprovados DCAT pedidos pela equipa de DCM. Níveis de acesso no Centro de dados são atribuídos e geridas dentro do DCAT. Acesso de centro de dados é revisto trimestralmente. Todos os acessos ao centros de dados do Azure é registado no DCAT e estão disponível para as investigações possíveis futuras. Visitantes têm de ser escorted permanente. Acesso a escort no Centro de dados é registado dentro de alarme monitorização sistema e se for necessário pode ser correlacionado com o visitante para revisão futura. Acesso de visitantes está a ser revisto continuamente pelo escort atribuído e o supervisor de sala de controlo através de CCTV e alarme sistema de monitorização. Visitantes não são fornecidos com o acesso e tem de ser acompanhados os respetivos escorts permanente. |


 ## <a name="nist-800-53-control-pe-9"></a>PE de controlo do NIST 800-53-9

#### <a name="power-equipment-and-cabling"></a>Equipamento de energia e cablagem

**PE 9** a organização protege equipamento de energia e cablagem para o sistema de informações de danos e destruição de energia.

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a quaisquer recursos de sistema nos centros de dados do Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure implementa este requisito em nome dos clientes. O Microsoft Azure oferece protective espaços e a etiquetagem adequado para cabos. Equipamento de infraestrutura do Microsoft Azure, por exemplo, cabos, linhas elétrica e generators cópia de segurança tem de ser colocado em ambientes que tenham sido foi desenvolvidos para ser protegida contra riscos ambientais como roubo fire, explosives, smoke, máximo, dust, vibration terramoto, chemicals prejudiciais, interferências elétrica, falhas de energia elétrica disturbances (picos). Todos os recursos de serviços online portátil (por exemplo, bastidores, servidores, dispositivos de rede) tem de ser bloqueados ou fastened no local para fornecer proteção contra danos de roubo ou movimento. Cabos de sistema de energia e informações em qualquer ambiente do Microsoft Azure são adequadamente a etiqueta e protegidos contra interception ou danos. Em todos os pontos de dentro de um ambiente para evitar interferências, energia e informações de cabos de sistema são separados umas das outras. |


 ## <a name="nist-800-53-control-pe-10a"></a>Controlo PE-10.a do NIST 800-53

#### <a name="emergency-shutoff"></a>Emergência Shutoff

**PE 10.a** organização fornece a capacidade de encerrar desativar energia para o sistema de informações ou o sistema individuais componentes em situações de emergência.

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a quaisquer recursos de sistema nos centros de dados do Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure implementa este requisito em nome dos clientes. Microsoft Azure instalou os botões de emergência energia desativado (EPO) em localizações no Centro de dados conforme exigido pelo código local fire. Em alguns centros de dados do Microsoft Azure geridos, a conceção do Centro de dados já não necessita de botões EPO. |


 ## <a name="nist-800-53-control-pe-10b"></a>Controlo PE-10.b do NIST 800-53

#### <a name="emergency-shutoff"></a>Emergência Shutoff

**PE 10.b** organização coloca comutadores de emergência shutoff ou dispositivos na [atribuição: localização definido de organização por informações do sistema ou componente de sistema] para facilitar o acesso fácil e seguro para técnico.

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a quaisquer recursos de sistema nos centros de dados do Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure implementa este requisito em nome dos clientes. Botões EPO são estrategicamente colocados para permitir a ativação em situações de emergência. Botões EPO podem ser colocados no colocations, manned centros de operação de instalações (FOCs), ou conforme exigido pelo código local fire. |


 ## <a name="nist-800-53-control-pe-10c"></a>Controlo PE-10.c do NIST 800-53

#### <a name="emergency-shutoff"></a>Emergência Shutoff

**PE 10.c** a organização protege a capacidade de shutoff de energia emergência ativação não autorizado.

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a quaisquer recursos de sistema nos centros de dados do Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure implementa este requisito em nome dos clientes. Para evitar ativação acidental, botões EPO podem ter um bastidor protective, necessitam de ativação dupla ou utilizam um alarme audible como um aviso antes de ativação. Além disso, os botões EPO são em vídeo vigilância. |


 ## <a name="nist-800-53-control-pe-11"></a>NIST 800-53 controlo PE-11

#### <a name="emergency-power"></a>Energia emergência

**PE 11** organização fornece uma curto prazo de alimentação ininterrupta para facilitar a [seleção (um ou mais): um encerramento do sistema informações ordenado; transição do sistema informações a longo prazo potência alternativo] em caso de um perda de origem de energia principal.

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a quaisquer recursos de sistema nos centros de dados do Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure implementa este requisito em nome dos clientes. Microsoft Azure tem implementada energia emergência protegendo equipamento de centro de dados e circuitos com uma potência ininterrupta fornecem system (UPS) que fornece uma fonte de alimentação curto prazo para fornecer a energia até generators conseguem fique online. |


 ### <a name="nist-800-53-control-pe-11-1"></a>PE de controlo do NIST 800-53-11 (1)

#### <a name="emergency-power--long-term-alternate-power-supply---minimal-operational-capability"></a>Energia emergência | Longa duração alternativa fonte de alimentação - mínima capacidade operacional

**PE-11 (1)** organização fornece uma longa duração alternativa fonte de alimentação para o sistema de informações que seja capaz de manutenção minimamente necessária capacidade operacional em caso de uma perda expandida da origem de energia principal.

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a quaisquer recursos de sistema nos centros de dados do Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure tem implementada uma longa duração alternativa fonte de alimentação para o sistema de informações que seja capaz de manter que uma mínima necessária capacidade operacional quando uma perda expandida da origem de energia principal ocorre. Quando a energia falha ou número cair para um nível de tensão inaceitável, sistemas de ininterrupta (UPS) iniciar instantaneamente e assumir a carga de energia. Isto fornece suficiente energia para os servidores a executar até os generators podem demorar mais. Emergência generators fornecem a energia de cópia de segurança para falhas expandidas e para a manutenção planeada e podem operar o Centro de dados com reservas de fuel no local em caso de catástrofe natural. Azure mantém o gerador de diesel em muitos dos nossos centros de dados. Cópia de segurança generators são utilizados quando for necessário para ajudar a manter a estabilidade da grelha ou na reparação extraordinary e situações de manutenção que requerem-na tomar nossos centros de dados fora da grelha de energia. |


 ## <a name="nist-800-53-control-pe-12"></a>PE de controlo do NIST 800-53-12

#### <a name="emergency-lighting"></a>Emergência iluminação da

**PE-12** organização emprega e mantém iluminação da emergência automática para o sistema de informações que ativa na eventualidade de ocorrer uma falha de energia ou uma interrupção e que abrange emergência sai e rotas de evacuação dentro de instalações.

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a quaisquer recursos de sistema nos centros de dados do Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure implementa este requisito em nome dos clientes. Centros de dados do Microsoft Azure (concedido e totalmente gerido) implementam iluminação da emergência sob a forma de sobrecarga iluminação da emergência no circuitos dedicadas é feita pelo UPS e sistemas de gerador (PE-11 consulte). Iluminação da emergência automática é implementada ao longo de todas as rotas de evacuação, sai de emergência e dentro de colocations de acordo com as Fire nacional de normalização e o código de segurança de vida de associação de proteção (NFPA). No caso de energia do utilitário se tenha perdida, de iluminação da emergência automaticamente passará a energia fornecida pelos sistemas UPS e gerador. Os sistemas de emergência lighting dentro dos centros de dados do Microsoft Azure são submetidos a manutenção de rotina para se certificar de que permanecem na ordem de trabalho adequada. |


 ## <a name="nist-800-53-control-pe-13"></a>Controlo PE-13 do NIST 800-53

#### <a name="fire-protection"></a>Proteção de fire

**PE 13** a organização utiliza e mantém fire supressão e deteção de dispositivos/sistemas para o sistema de informações que são suportados por uma fonte de energia independentes.

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a quaisquer recursos de sistema nos centros de dados do Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure implementa este requisito em nome dos clientes. Microsoft Azure tem implementada fire proteção através da instalação de sistemas de supressão de deteção e fire fire em centros de dados dos Microsoft Azure. <br /> Centros de dados do Microsoft Azure implementam mecanismos de deteção de fire robusto. A abordagem de proteção do Microsoft Azure fire inclui a utilização de photoelectric detetores smoke instalado abaixo o piso e o limite que estão integradas com o sistema de sprinkler fire proteção. Além disso, existem sistemas Xtralis VESDA (muito antecipado Smoke deteção Apparatus) em cada colocalização monitorizar ondas eletromagnéticas. Unidades VESDA são sistemas de amostragem de ar altamente confidenciais instalado ao longo de vários espaços de alto valor. Unidades VESDA permitem obter uma resposta de investigação antes um alarme de deteção fire real. <br /> Caixas de alarme fire 'Pull estação' são instaladas ao longo dos centros de dados para notificação de alarme de fire manual. Acionar extinguishers encontram-se ao longo dos centros de dados e são corretamente inspecionadas, manutenção e etiquetados anual. A equipa de segurança patrols todas as áreas de criar várias vezes cada shift. Centro de dados técnicos efetuar uma passagem de site diária garantir que todos os fire veja, que estão a ser cumpridos os requisitos. <br /> Áreas que contém o equipamento elétrica confidencial (colocations, MDFs, etc.) estão protegidos pelo interlock duplo pré-ação (dry pipe) sprinkler sistemas. Sprinklers dry pipe são um sistema de pré-ação de duas etapas que necessita de uma ativação head do sprinkler (devido a térmico), bem como deteção smoke para libertar o máximo. A ativação head sprinkler versões a pressão de ar de pipes nomeados que permite que os pipes preencher com máximo. Máximo é lançado quando um smoke ou térmico detector também é ativado. <br /> Acionar deteção/supressão e sistemas de emergência lighting são com fios para os sistemas de gerador fornecendo uma fonte de alimentação redundantes e AGREGA de centro de dados. |


 ### <a name="nist-800-53-control-pe-13-1"></a>PE de controlo do NIST 800-53-13 (1)

#### <a name="fire-protection--detection-devices--systems"></a>Acionados proteção | Dispositivos de deteção / sistemas

**PE-13 (1)** emprega de organização acionados deteção/sistemas de dispositivos para o sistema de informações que ativar automaticamente e notificar [atribuição: técnico definido pela organização ou funções] e [atribuição: emergência definido de organização dispositivos de resposta] em caso de um acionar.

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a quaisquer recursos de sistema nos centros de dados do Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure emprega acionados deteção/sistemas de dispositivos para o sistema de informações que ativar automaticamente e notificar o Centro de dados técnicos, juntamente com dispositivos de resposta de emergência em caso de um acionar. No caso de um dos mecanismos de deteção fire está ativado em qualquer colocalização, o departamento de fire local é automaticamente notificado através do sistema de alarme acionar. Além disso, os sistemas de deteção de proteção e fire fire estão associados para o sistema de segurança notificar a equipa de instalações e de segurança local. |


 ### <a name="nist-800-53-control-pe-13-2"></a>PE de controlo do NIST 800-53-13 (2)

#### <a name="fire-protection--suppression-devices--systems"></a>Acionados proteção | Dispositivos de supressão / sistemas

**PE-13 (2)** emprega de organização acionados supressão/sistemas de dispositivos para o sistema de informações que fornecer notificação automática de qualquer ativação para [atribuição: técnico definido pela organização ou funções] e [atribuição: definido pelo organização emergência dispositivos de resposta].

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a quaisquer recursos de sistema nos centros de dados do Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure implementa este requisito em nome dos clientes. No caso de um dos sistemas de supressão fire está ativado no Centro de dados, o departamento de fire local é automaticamente notificado através do sistema de alarme acionar. Além disso, os sistemas de deteção de proteção e fire fire estão associados para o sistema de segurança notificar a equipa de instalações e de segurança local. |


 ### <a name="nist-800-53-control-pe-13-3"></a>PE de controlo do NIST 800-53-13 (3)

#### <a name="fire-protection--automatic-fire-suppression"></a>Acionados proteção | Supressão de Fire automática

**PE-13 (3)** a organização utiliza uma capacidade de supressão fire automática para o sistema de informações quando a instalação não é staffed numa base contínua.

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a quaisquer recursos de sistema nos centros de dados do Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure implementa este requisito em nome dos clientes. Centros de dados do Microsoft Azure são 24x7 staffed. Sistemas de supressão fire envolvimento automaticamente sem intervenção manual quando é detetada uma situação de alarme acionar. |


 ## <a name="nist-800-53-control-pe-14a"></a>Controlo PE-14.a do NIST 800-53

#### <a name="temperature-and-humidity-controls"></a>Relativos à temperatura e humidade controlos

**PE 14.a** organização mantém os níveis relativos à temperatura e humidade dentro do local de onde reside o sistema de informações em [atribuição: aceitável definido de organização].

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a quaisquer recursos de sistema nos centros de dados do Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure implementa este requisito em nome dos clientes. Microsoft Azure mantém os níveis relativos à temperatura e humidade de acordo com as diretrizes de American Society de Heating, Refrigerating e Air-conditioning engenheiros (ASHRAE). Os níveis relativos à temperatura e humidade continuamente são monitorizados pelo sistema de gestão de edifício do Centro de dados (BMS). |


 ## <a name="nist-800-53-control-pe-14b"></a>Controlo PE-14.b do NIST 800-53

#### <a name="temperature-and-humidity-controls"></a>Relativos à temperatura e humidade controlos

**PE 14.b** organização monitoriza os níveis relativos à temperatura e humidade [atribuição: frequência definida de organização].

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a quaisquer recursos de sistema nos centros de dados do Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure implementa este requisito em nome dos clientes. Em centros de dados do Microsoft Azure, os níveis relativos à temperatura e humidade são monitorizados continuamente pelo sistema de gestão de criação (BMS). Técnico de centro de dados monitorizar o BMS das instalações operações Center (FOC), para que estes possam gerir temperatura e humidade no Centro de dados antes de quaisquer pontos de alarme for excedidos. |


 ### <a name="nist-800-53-control-pe-14-2"></a>PE de controlo do NIST 800-53-14 (2)

#### <a name="temperature-and-humidity-controls--monitoring-with-alarms--notifications"></a>Relativos à temperatura e humidade controlos | Monitorização com alarmes / notificações

**PE-14 (2)** a organização utiliza temperatura e humidade monitorização que fornece uma notificação de alterações potencialmente prejudiciais ou alarme para técnicos ou equipamento do cliente.

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a quaisquer recursos de sistema nos centros de dados do Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure implementa este requisito em nome dos clientes. Em centros de dados do Microsoft Azure, os níveis relativos à temperatura e humidade são monitorizados continuamente pelo sistema de gestão de criação (BMS). Técnico de centro de dados monitorizar o BMS das instalações operações Center (FOC), para que estes possam gerir temperatura e humidade no Centro de dados antes de quaisquer pontos de alarme for excedidos. |


 ## <a name="nist-800-53-control-pe-15"></a>PE de controlo do NIST 800-53-15

#### <a name="water-damage-protection"></a>Proteção de danos máximo

**PE-15** a organização protege o sistema de informações de danos resultantes de fugas de níveis máximos, fornecendo mestres valves shutoff ou isolamento são conhecidos e acessível a funcionar corretamente, ao pessoal de chave.

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a quaisquer recursos de sistema nos centros de dados do Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure implementa este requisito em nome dos clientes. Microsoft Azure fornece deteção de máximo/fuga nas áreas com um risco de fuga de níveis máximos (por exemplo, unidades de processadores de ar). Sistemas de supressão fire também tem alarmes de deteção de fuga que são monitorizados. O sistema de deteção de máximo/fuga está integrado com o sistema de alarme e notificação das instalações. Os sistemas de sprinkler dos centros de dados têm as zonas corretas. Técnico de centro de dados estiver familiarizado com procedimentos de emergência exigir a utilização de valves de shutoff máximo e as respetivas localizações. Os risers sprinkler têm a capacidade de ser encerradas individualmente ou como um grupo através de valves de porta. Todos os sprinklers no espaço de crítico são sprinklers de tipo de pré-ação interlock duplo que necessitam de duas formas de ativação antes do fluxo é iniciado. A pressão do sistema sprinkler é monitorizada e alarmed contra fugas de níveis máximos. |


 ### <a name="nist-800-53-control-pe-15-1"></a>PE de controlo do NIST 800-53-15 (1)

#### <a name="water-damage-protection--automation-support"></a>Máximo de proteção de danos | Suporte de automatização

**15 de PE (1)** organização emprega automatizados mecanismos para detetar a presença de níveis máximos in the vicinity of o sistema de informações e alertas [atribuição: técnico definido pela organização ou funções].

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a quaisquer recursos de sistema nos centros de dados do Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure emprega automatizados mecanismos para detetar a presença de níveis máximos em dos centros de dados e alertas do Centro de dados pessoal. O Azure oferece deteção máximo/fuga nas áreas com um risco de fuga de níveis máximos (por exemplo, unidades de processadores de ar). Sistemas de supressão fire também tem alarmes de deteção de fuga que são monitorizados. O sistema de deteção de máximo/fuga está integrado com o sistema de alarme e notificação das instalações. A pressão do sistema sprinkler é monitorizada e alarmed contra fugas de níveis máximos. |


 ## <a name="nist-800-53-control-pe-16"></a>PE de controlo do NIST 800-53-16

#### <a name="delivery-and-removal"></a>Entrega e de remoção

**PE-16** organização autoriza, monitoriza e controla [atribuição: tipos definidos de organização dos componentes do sistema de informações] entrar e sair da instalação e mantém registos dos itens.

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a quaisquer recursos de sistema nos centros de dados do Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure implementa este requisito em nome dos clientes. Microsoft Azure implementa strict imposição de que tem permissão para introduzir e sair do Centro de dados. Todos os componentes/recursos de sistema são controlados na base de dados de ferramenta de gestão do recurso. |


 ## <a name="nist-800-53-control-pe-17a"></a>Controlo PE-17.a do NIST 800-53

#### <a name="alternate-work-site"></a>Site de trabalho alternativo

**PE 17.a** organização emprega [atribuição: controlos de segurança definido para organização] em sites de trabalho alternativo.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Aprovisiona de site de trabalho alternativo de nível empresarial do cliente pode ser suficiente para este controlo de endereços. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-pe-17b"></a>Controlo PE-17.b do NIST 800-53

#### <a name="alternate-work-site"></a>Site de trabalho alternativo

**PE 17.b** organização avalia como exequível, controla a eficácia de segurança em sites de trabalho alternativo.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Aprovisiona de site de trabalho alternativo de nível empresarial do cliente pode ser suficiente para este controlo de endereços. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-pe-17c"></a>Controlo PE-17.c do NIST 800-53

#### <a name="alternate-work-site"></a>Site de trabalho alternativo

**PE 17.c** organização fornece um meio para empregados para comunicar com o técnico de segurança de informações em caso de incidentes de segurança ou problemas.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Aprovisiona de site de trabalho alternativo de nível empresarial do cliente pode ser suficiente para este controlo de endereços. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-pe-18"></a>Controlo PE-18 do NIST 800-53

#### <a name="location-of-information-system-components"></a>Localização dos componentes do sistema de informações

**PE-18** posições de organização de componentes do sistema informações dentro do local de para minimizar a potenciais danos de [atribuição: definido de organização físicos e ambientais riscos] e minimizam a oportunidade para não autorizado acesso.

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a quaisquer recursos de sistema nos centros de dados do Azure. |
| **Fornecedor (Microsoft Azure)** | Azure implementa o Centro de dados estratégicos abordagem de design para satisfazer a localização do controlo de componentes do sistema de informações. Equipamento dos todos os Microsoft online services é colocado em locais que tenham sido foi desenvolvidos para ser protegida contra riscos ambientais, tais como roubo, fire, explosives, smoke, máximo, dust, vibration, terramoto, chemicals prejudiciais, interferências elétrica, falhas de energia, disturbances elétrica (picos) e radiation. A instalação e a infraestrutura de tem implementado bracing seismic para proteção contra riscos ambientais. Todos os colocalização e gabinetes MDF estão protegidos pelo controlo de acesso, alarmes e vídeo. A instalação também é patrolled por officers segurança 24x7. Todos os recursos do Azure portátil bloqueados ou fastened no local para fornecer proteção contra danos de roubo ou movimento. |


