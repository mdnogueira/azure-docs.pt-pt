---
title: "FedRAMP Azure Blueprint automatização - manutenção"
description: "Aplicações Web para FedRAMP - manutenção"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 53acae01-bea9-4570-a9bc-734ff65262ba
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: a0546f6e10b04bbfdb5b02e5c0bbe6d907c76e72
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2017
---
# <a name="maintenance-ma"></a>Manutenção (MA)

> [!NOTE]
> Estes controlos são definidos pelo NIST e para os E.U.A. Departamento de comércio como parte da revisão a publicação especial do NIST 800-53 4. Consulte Rev 4 do NIST 800-53. para obter informações sobre testes de procedimentos e as diretrizes para cada controlo.

## <a name="nist-800-53-control-ma-1"></a>MA de controlo do NIST 800-53-1

#### <a name="system-maintenance-policy-and-procedures"></a>Política de manutenção do sistema e de procedimentos

**MA-1** organização desenvolvidas pela organização, documentos e disseminates para [atribuição: técnico definido pela organização ou funções] uma política de manutenção do sistema que endereços objetivo, âmbito, funções, responsabilidades, compromisso de gestão, coordenação entre entidades organizacionais e de conformidade; e procedimentos para facilitar a implementação do sistema de manutenção da política e controlos de manutenção do sistema associado; analisa e atualiza a política de manutenção do sistema atual [atribuição: frequência definida de organização]; e procedimentos de manutenção do sistema [atribuição: frequência definida de organização].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Política de manutenção do sistema de nível empresarial e procedimentos do cliente podem ser suficientes para este controlo de endereços. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-ma-2a"></a>MA de controlo-2.a do NIST 800-53

#### <a name="controlled-maintenance"></a>Manutenção controlada

**MA 2.a** organização agenda, efetua, documentos e revê registos de reparações nos componentes de sistema de informações em conformidade com as especificações de fabricante ou o fornecedor de e/ou requisitos organizacionais e de manutenção.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável pela manutenção controlada. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-ma-2b"></a>MA de controlo-2.b do NIST 800-53

#### <a name="controlled-maintenance"></a>Manutenção controlada

**MA 2.b** organização aprova e monitoriza todas as atividades de manutenção, se executada no site ou remotamente e se o equipamento é reparado no site ou removido para outra localização.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável pela manutenção controlada. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-ma-2c"></a>MA de controlo-2.c do NIST 800-53

#### <a name="controlled-maintenance"></a>Manutenção controlada

**MA 2.c** organização exige que [atribuição: técnico definido pela organização ou funções] explicitamente aprovar a remoção do sistema de informações ou componentes do sistema de instalações organizacionais para fora das instalações manutenção ou reparações.

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a quaisquer recursos de sistema nos centros de dados do Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure requer essa propriedade recursos (por exemplo, o dispositivo de rede ou servidor) que requerem noutro local de transferência tem a aprovação do proprietário de recurso explícita. |


 ## <a name="nist-800-53-control-ma-2d"></a>MA de controlo-2.d do NIST 800-53

#### <a name="controlled-maintenance"></a>Manutenção controlada

**MA 2.d** organização sanitizes equipamento para remover todas as informações de suporte de dados associada antes da remoção de instalações organizacionais para fora das instalações manutenção ou reparações.

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a quaisquer recursos de sistema nos centros de dados do Azure. |
| **Fornecedor (Microsoft Azure)** | Ativo proteção padrão Microsoft Azure define o elemento precauções necessárias para a transferência de noutro local de recursos de processamento. O padrão de proteção do recurso requer que os recursos de armazenamento de dados ser limpo/limpar de forma consistente com SP do NIST 800-88, diretrizes para Sanitização de suporte de dados, antes de abandonar o fileparser o Centro de dados. |


 ## <a name="nist-800-53-control-ma-2e"></a>MA de controlo-2.e do NIST 800-53

#### <a name="controlled-maintenance"></a>Manutenção controlada

**MA 2.e** A organização verifica todos os controlos de segurança potencialmente afetados para verificar que os controlos estão continuam a funcionar corretamente seguintes ações de manutenção ou reparação.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável pela manutenção controlada. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-ma-2f"></a>MA de controlo-2.f do NIST 800-53

#### <a name="controlled-maintenance"></a>Manutenção controlada

**MA 2.f** inclui a organização [atribuição: definido de organização informações relacionadas com a manutenção] nos registos de manutenção organizacional.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável pela manutenção controlada. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-ma-2-2a"></a>.A do NIST 800-53 MA de controlo-2 (2)

#### <a name="controlled-maintenance--automated-maintenance-activities"></a>Controlado manutenção | Atividades de manutenção automática

**MA-2 (2) .a** organização emprega automatizados mecanismos para a agenda, conduta e manutenção do documento e reparações.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável para automatizar atividades de manutenção. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-ma-2-2b"></a>.B do NIST 800-53 MA de controlo-2 (2)

#### <a name="controlled-maintenance--automated-maintenance-activities"></a>Controlado manutenção | Atividades de manutenção automática

**MA-2 (2) .b** organização produz atualizadas, exata, e registos de conclusão de todas as ações de manutenção e reparação de pedido, agendada, no processo e concluída.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável para automatizar atividades de manutenção. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-ma-3"></a>MA de controlo do NIST 800-53-3

#### <a name="maintenance-tools"></a>Ferramentas de manutenção

**MA 3** organização aprova, controlos e monitoriza as ferramentas de manutenção do sistema de informações.

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a quaisquer recursos de sistema nos centros de dados do Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure utiliza várias ferramentas para concluir a manutenção. Alterar e libertar o processo de manutenção de software ferramentas são aprovadas, controladas e mantidas através do Microsoft Azure. <br /> A equipa de serviços de Site mantém um inventário das ferramentas de manutenção aprovados para utilização no Centro de dados (consulte MA-3). Técnico de manutenção é direcionados para utilizar as ferramentas de manutenção fornecidas. Aprovação de gestão de centros de dados é necessário para utilizar as ferramentas não fornecidas pelo centro de dados. Ferramentas de mão físico (screwdrivers wrenches, etc.) estão isentos este controlo. <br /> Cada instalação contém uma caixa de bloqueio físico restrito ou o acesso controlado espaço para o armazenamento das ferramentas de manutenção especializadas, como a âmbitos de ether fluke testers de canal de fibra fluke, Ethernet toners, etc. A equipa de serviços do Site efetua verificações de rotina de inventário para verificar o estado de todas as ferramentas. Acesso à caixa ou manutenção espaço de armazenamento de bloqueio é controlado no acesso destaque leitor registos, que estão disponíveis na eventualidade de uma investigação. |


 ### <a name="nist-800-53-control-ma-3-1"></a>MA de controlo do NIST 800-53-3 (1)

#### <a name="maintenance-tools--inspect-tools"></a>Ferramentas de manutenção | Inspecione ferramentas

**MA-3 (1)** organização inspeciona as ferramentas de manutenção transportadas para uma instalação por técnico de manutenção para modificações incorrecto ou não autorizados.

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a quaisquer recursos de sistema nos centros de dados do Azure. |
| **Fornecedor (Microsoft Azure)** | Equipa de serviços de Site do Microsoft Azure mantém um inventário das ferramentas de manutenção aprovados para utilização no Centro de dados (consulte MA 3 para obter mais detalhes). Técnico de manutenção é direcionados para utilizar as ferramentas de manutenção fornecidas. Aprovação de DCM é necessário para utilizar as ferramentas não fornecidas pelo centro de dados. |


 ### <a name="nist-800-53-control-ma-3-2"></a>MA de controlo do NIST 800-53-3 (2)

#### <a name="maintenance-tools--inspect-media"></a>Ferramentas de manutenção | Inspecione o suporte de dados

**MA-3 (2)** verifica a organização suporte de dados que contém diagnóstico e de programas de teste para código malicioso antes do suporte de dados é utilizados no sistema de informações.

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a quaisquer recursos de sistema nos centros de dados do Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure proíbe a utilização de informática móveis ou de suportes de dados de armazenamento no ambiente de produção dos centros de dados do Microsoft Azure sem a aprovação de gestão de centros de dados. Não é permitida a utilização de suportes de dados pessoais do que está a ser utilizado no ambiente de produção dos centros de dados do Microsoft Azure. <br /> Microsoft Azure tiver implementado um processo para inspecionar os computadores portáteis antes de a ser utilizado no ambiente de produção dos centros de dados do Microsoft Azure. Officers de segurança são preparados para técnico com computadores portáteis no ambiente de produção para verificar se os computadores portáteis têm sofrido e transmitido a inspeção de desafio. |


 ### <a name="nist-800-53-control-ma-3-3"></a>MA de controlo do NIST 800-53-3 (3)

#### <a name="maintenance-tools--prevent-unauthorized-removal"></a>Ferramentas de manutenção | Impedir a remoção não autorizada

**MA-3 (3)** organização impede a remoção do equipamento de manutenção que contêm informações organizacionais verificar se está sem informações organizacionais contidos no equipamento; limpeza ou destroying não autorizada a equipamento; manter o equipamento dentro do local de; ou obtenção de uma exclusão de [atribuição: técnico definido pela organização ou funções] autorizar explicitamente a remoção do equipamento de instalação.

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a quaisquer recursos de sistema nos centros de dados do Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure emprega ferramentas de manutenção específico do Centro de dados que são mantidas dentro do local de e não são removidas. Cada função contém um espaço de armazenamento ou de caixa de bloqueio físico restrito que armazena as ferramentas de manutenção, tais como âmbitos de ether fluke, testers de canal de fibra fluke, Ethernet toners, etc. O acesso é controlado para o espaço de armazenamento ou de caixa de bloqueio no DCAT para proibir o acesso não autorizado para as ferramentas de manutenção. <br /> Informações organizacionais estão protegidas durante a manutenção pelos controlos MA 4. Para aceder a informações organizacionais, o utilizador tem de ter privilegiado contas e autenticadores. |


 ## <a name="nist-800-53-control-ma-4a"></a>MA de controlo-4.a do NIST 800-53

#### <a name="nonlocal-maintenance"></a>Manutenção Nonlocal

**MA 4.a** aprova e monitoriza as atividades de diagnóstico e de manutenção nonlocal a organização.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por efetuar a manutenção de não local em sistemas de operativos implementados de cliente. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-ma-4b"></a>MA de controlo-4.b do NIST 800-53

#### <a name="nonlocal-maintenance"></a>Manutenção Nonlocal

**MA 4.b** organização permite a utilização da manutenção nonlocal e ferramentas apenas como consistente com a política organizacional e documentados no plano de segurança para o sistema de informações de diagnóstico.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por efetuar a manutenção de não local em sistemas de operativos implementados de cliente. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-ma-4c"></a>MA de controlo-4.c do NIST 800-53

#### <a name="nonlocal-maintenance"></a>Manutenção Nonlocal

**MA 4.c** organização emprega autenticadores seguras no estabelecimento de sessões de diagnóstico e de manutenção nonlocal.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por efetuar a manutenção de não local em sistemas de operativos implementados de cliente. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-ma-4d"></a>MA de controlo-4.d do NIST 800-53

#### <a name="nonlocal-maintenance"></a>Manutenção Nonlocal

**MA 4.d** organização mantém os registos para manutenção nonlocal e atividades de diagnóstico.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por efetuar a manutenção de não local em sistemas de operativos implementados de cliente. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-ma-4e"></a>MA de controlo-4.e do NIST 800-53

#### <a name="nonlocal-maintenance"></a>Manutenção Nonlocal

**MA 4.e** A organização termina as ligações de rede e de sessão quando é feita uma manutenção nonlocal.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por efetuar a manutenção de não local em sistemas de operativos implementados de cliente. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-ma-4-2"></a>MA de controlo do NIST 800-53-4 (2)

#### <a name="nonlocal-maintenance--document-nonlocal-maintenance"></a>Manutenção Nonlocal | Manutenção Nonlocal do documento

**MA-4 (2)** os documentos da organização no plano de segurança para o sistema de informações, as políticas e procedimentos para o estabelecimento e a utilização de ligações de diagnóstico e de manutenção nonlocal.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável pela documentação manutenção não local no plano de segurança para sistemas de operativos implementados de cliente. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-ma-4-3"></a>MA de controlo do NIST 800-53-4 (3)

#### <a name="nonlocal-maintenance--comparable-security--sanitization"></a>Manutenção Nonlocal | Segurança comparável / Sanitização

**MA-4 (3)** organização exige que os serviços de diagnóstico e de manutenção nonlocal ser efetuada a partir de um sistema de informações que implementa uma capacidade de segurança comparável para a capacidade de implementado no sistema que está a ser reparado; ou remove o componente de serviço do sistema antes da manutenção nonlocal ou serviços de diagnóstico, informações sanitizes o componente (em relação a informações organizacionais) antes de remoção de instalações organizacionais e, depois do serviço efetuar, inspeciona e sanitizes o componente (em relação a software potencialmente malicioso) antes do componente do sistema de informações a restabelecer ligação.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por efetuar a manutenção de não local todos os sistemas de operativos cliente implementadas proveniente de um sistema de informações com segurança comparável. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-ma-4-6"></a>MA de controlo do NIST 800-53-4 (6)

#### <a name="nonlocal-maintenance--cryptographic-protection"></a>Manutenção Nonlocal | Proteção de criptografia

**MA-4 (6)** mecanismos criptográficos para proteger a integridade e confidencialidade de comunicações de diagnóstico e de manutenção nonlocal de implementa o sistema de informações.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por implementar mecanismos criptográficos quando efetuar a manutenção não local e de diagnóstico dos sistemas de operativos implementados de cliente. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-ma-5a"></a>MA de controlo-5.a do NIST 800-53

#### <a name="maintenance-personnel"></a>Técnico de manutenção

**MA 5.a** organização estabelece um processo de autorização de técnico de manutenção e mantém uma lista de autorizados manutenção organizações ou pessoal.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | System de nível empresarial manutenção técnico autorização escort processos e o cliente podem ser suficientes para este controlo de endereços. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-ma-5b"></a>MA de controlo-5.b do NIST 800-53

#### <a name="maintenance-personnel"></a>Técnico de manutenção

**MA 5.b** organização garante que não escorted técnico, efetuar a manutenção do sistema de informações ter necessita de autorizações de acesso.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | System de nível empresarial manutenção técnico autorização escort processos e o cliente podem ser suficientes para este controlo de endereços. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-ma-5c"></a>MA de controlo-5.c do NIST 800-53

#### <a name="maintenance-personnel"></a>Técnico de manutenção

**MA 5.c** organização designa organizacional pessoal com o acesso necessário autorizações e competence técnica para supervisionar as atividades de manutenção de técnicos de que possui as autorizações de acesso necessária.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | System de nível empresarial manutenção técnico autorização escort processos e o cliente podem ser suficientes para este controlo de endereços. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-ma-5-1a"></a>.A do NIST 800-53 MA de controlo-5 (1)

#### <a name="maintenance-personnel--individuals-without-appropriate-access"></a>Técnico de manutenção | Utilizadores sem acesso adequado

**MA-5 (1) .a** organização implementa procedimentos para a utilização de técnico de manutenção que falta clearances de segurança adequadas ou que não são citizens E.U.A., que incluem o técnico de manutenção de requisitos seguintes que não tenham necessária autorizações de acesso, clearances ou acesso formal aprovações são escorted e supervisionadas durante o desempenho de atividades no sistema de informações de diagnóstico e de manutenção por aprovados técnico organizacionais que está completamente desmarcados, ter adequado acesso autorizações e são tecnicamente qualificado; antes de iniciar manutenção ou atividades diagnóstico por parte do pessoal que não necessário ter acesso autorizações, clearances ou aprovações acesso formal, informações volátil todos os componentes de armazenamento no sistema informações são sanitized e todos os suporte de armazenamento não volátil é removidos ou fisicamente desligada do sistema e protegidos.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | System de nível empresarial manutenção técnico autorização escort processos e o cliente podem ser suficientes para este controlo de endereços. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-ma-5-1b"></a>.B do NIST 800-53 MA de controlo-5 (1)

#### <a name="maintenance-personnel--individuals-without-appropriate-access"></a>Técnico de manutenção | Utilizadores sem acesso adequado

**MA-5 (1) .b** organização desenvolvidas pela organização e implementa alternativa as proteções de segurança em caso de um componente do sistema de informações não pode ser sanitized, removido ou desligado do sistema.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | System de nível empresarial manutenção técnico autorização escort processos e o cliente podem ser suficientes para este controlo de endereços. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-ma-6"></a>MA de controlo do NIST 800-53-6

#### <a name="timely-maintenance"></a>Manutenção atempada

**MA 6** organização obtém manutenção peças de suporte e/ou spare para [atribuição: os componentes do sistema de informações definidas pela organização] dentro [atribuição: organização definidos pelo período de tempo] da falha.

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a quaisquer recursos de sistema nos centros de dados do Azure. |
| **Fornecedor (Microsoft Azure)** | Centros de dados do Microsoft Azure mantêm o técnico de manutenção residente para suportar a sistemas de infraestrutura do Centro de dados críticos, bem como as operações do Centro de dados. As equipas de identificou componentes de sistema críticos e tecnologia de segurança que sejam mantidas spares no local. Sistemas críticos são concebidas de modo a N + 1 configurações e os serviços são concebidos para ser resiliente. Isto permite que a equipa de gestão de datacenter atingir os objetivos de recuperação em caso de uma interrupção do serviço ou a situação contingency plano. Serviços do sistema de informações críticas são aprovisionados a partir de mais do que um centro de dados para evitar interrupções no serviço devido a um incidente dos centros de dados. Aplicações de cliente são responsáveis por implementar vários centros de dados para fornecer redundância e resiliência. |
