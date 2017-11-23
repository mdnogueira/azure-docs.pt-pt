---
title: "FedRAMP Azure Blueprint automatização - controlo de acesso"
description: "Aplicações Web para FedRAMP - controlo de acesso"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: f7e6cd8f-b2df-4db6-8332-de97d86c5281
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: a7a840b338af3aa6c6a71f2db3cff1e36a8a6794
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2017
---
# <a name="access-control-ac"></a>Controlo de acesso (AC)

> [!NOTE]
> Estes controlos são definidos pelo NIST e para os E.U.A. Departamento de comércio como parte da revisão a publicação especial do NIST 800-53 4. Consulte Rev 4 do NIST 800-53. para obter informações sobre testes de procedimentos e as diretrizes para cada controlo.

## <a name="nist-800-53-control-ac-1"></a>Controlo NIST 800-53 AC-1

#### <a name="access-control-policy-and-procedures"></a>Política de controlo de acesso e de procedimentos

**AC 1** organização desenvolvidas pela organização, documentos e disseminates para [atribuição: técnico definido pela organização ou funções] uma política de controlo de acesso que endereços objetivo, âmbito, funções, responsabilidades, compromisso de gestão, coordenação entre entidades organizacionais e de conformidade; e procedimentos para facilitar a implementação da política de controlo de acesso e controlos de acesso associados; analisa e atualiza a política de controlo de acesso atual [atribuição: frequência definida de organização]; procedimentos de controlo de acesso e [atribuição: frequência definida de organização].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Política de controlo de acesso de nível empresarial e procedimentos do cliente podem ser suficientes para este controlo de endereços. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-ac-2a"></a>Controlo NIST 800-53 AC-2.a

#### <a name="account-management"></a>Gestão de Contas

**AC 2.a** organização identifica e seleciona os seguintes tipos de contas de sistema de informações para suportar as funções de negócio/missions organizacional: [atribuição: tipos de conta de sistema de informações definidas pela organização].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure baseia-se em e implementa os seguintes tipos de conta de sistema: os utilizadores do Azure Active Directory (utilizados para implementar a solução e gerir o acesso aos recursos do Azure), os utilizadores do sistema operativo Windows (que é geridos pelo Active Directory), conta de serviço do SQL Server. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-ac-2b"></a>Controlo NIST 800-53 AC-2.b

#### <a name="account-management"></a>Gestão de Contas

**AC 2.b** organização atribui gestores de conta para contas de informações do sistema.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável pela atribuição gestores às contas identificadas no 02.a de AC. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-ac-2c"></a>Controlo NIST 800-53 AC-2.c

#### <a name="account-management"></a>Gestão de Contas

**AC 2.c** organização estabelece condições para a associação de grupo e a função.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável pelo estabelecimento de função e grupo de critérios de associação de tipos de conta controlados pelo cliente (Consulte AC 02.a). |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-ac-2d"></a>Controlo NIST 800-53 AC-2.d

#### <a name="account-management"></a>Gestão de Contas

**AC 2.d** organização Especifica os utilizadores autorizados de informações do sistema de, associação de grupo e a função e acesso autorizações (ou seja, privilégios) e outros atributos (conforme necessário) para cada conta.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente poderá dependem de um processo de autorização de conta de nível empresarial estabelecida. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-ac-2e"></a>Controlo NIST 800-53 AC-2.e

#### <a name="account-management"></a>Gestão de Contas

**2.e de AC** A organização necessita de aprovações por [atribuição: técnico definido pela organização ou funções] para pedidos a criar informações de contas do sistema.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente poderá dependem de um processo de autorização de conta de nível empresarial estabelecida. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-ac-2f"></a>Controlo NIST 800-53 AC-2.f

#### <a name="account-management"></a>Gestão de Contas

**AC 2.f** organização cria, ativa, modifica, desativa e remove as contas de sistema de informações em conformidade com [atribuição: procedimentos definido pela organização ou condições].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente poderá dependem de um processo de gestão de conta de nível empresarial estabelecida. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-ac-2g"></a>Controlo NIST 800-53 AC-2.g

#### <a name="account-management"></a>Gestão de Contas

**AC 2.g** organização monitoriza a utilização de contas de informações do sistema.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure implementa a auditoria e segurança do OMS dashboard da solução identidade e acesso. Este dashboard permite gestores de conta para monitorizar a utilização de contas de sistema de informações. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-ac-2h"></a>Controlo NIST 800-53 AC-2.h

#### <a name="account-management"></a>Gestão de Contas

**AC 2.h** organização notifica gestores de conta contas já não são necessárias; quando os utilizadores são terminados ou transferidos; e a utilização do sistema de informações individuais ou necessidade de saber as alterações.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Procedimentos de controlo de acesso de nível empresarial do cliente poderão estabelecer um processo para notificar o Gestor de conta apropriada quando já não é necessária uma conta. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-ac-2i"></a>Controlo NIST 800-53 AC-2.i

#### <a name="account-management"></a>Gestão de Contas

**AC 2.i** organização autoriza o acesso ao sistema de informações com base numa autorização de acesso válido; utilização prevista sistema; e outros atributos conforme exigido pela organização ou funções de negócio/missions associado.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Procedimentos de controlo de acesso de nível empresarial do cliente poderão estabelecer um processo de autorização de acesso. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-ac-2j"></a>Controlo NIST 800-53 AC-2.j

#### <a name="account-management"></a>Gestão de Contas

**AC 2.j** organização revê as contas para conformidade com os requisitos de gestão de conta [atribuição: frequência definida de organização].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por consultar contas controlado de cliente com a frequência necessária para determinar se as contas que são compatíveis com todos os requisitos da organização. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-ac-2k"></a>Controlo NIST 800-53 AC-2.k

#### <a name="account-management"></a>Gestão de Contas

**AC 2.k** organização estabelece um processo para emitir novamente as credenciais da conta partilhada/grupo (se for implementado) quando os indivíduos são removidos do grupo.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Procedimentos de controlo de acesso de nível empresarial do cliente poderão estabelecer um processo para gerir as credenciais da conta de grupo. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-ac-2-1"></a>NIST 800-53 controlo AC-2 (1)

#### <a name="account-management--automated-system-account-management"></a>Gestão de contas | A gestão de conta do sistema

**AC-2 (1)** organização emprega automatizados mecanismos para suportar a gestão de contas de informações do sistema.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure implementa a auditoria e segurança do OMS dashboard da solução identidade e acesso. Este dashboard ativar gestores de conta para monitorizar a utilização de contas de sistema de informações. OMS pode ser configurado para enviar alertas quando a atividade atípica for suspeito ou outros eventos predefinidos ocorrem. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-ac-2-2"></a>NIST 800-53 controlo AC-2 (2)

#### <a name="account-management--removal-of-temporary--emergency-accounts"></a>Gestão de contas | Remoção de contas de emergência / temporárias

**AC-2 (2)** o sistema de informações automaticamente [seleção: Remove; desativa] temporárias e emergência contas após [atribuição: organização definidos pelo período de tempo para cada tipo de conta].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint do Azure não implementar contas temporárias ou de emergência. Se não manualmente desativado, o controlador de domínio implementado desativa automaticamente todas as contas inativas após 35 dias. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-ac-2-3"></a>NIST 800-53 controlo AC-2 (3)

#### <a name="account-management--disable-inactive-accounts"></a>Gestão de contas | Desativar contas inativas

**AC-2 (3)** o sistema de informações desativa automaticamente contas inativas após [atribuição: organização definidos pelo período de tempo].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O controlador de domínio implementado por esta Blueprint Azure está configurado para desativar todas as contas de utilizador após 35 dias de inatividade. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-ac-2-4"></a>NIST 800-53 controlo AC-2 (4)

#### <a name="account-management--automated-audit-actions"></a>Gestão de contas | Ações de auditoria automatizada

**AC-2 (4)** o sistema de informações automaticamente as auditorias de ações de criação, modificação, ativar, desativar e remoção de conta e notifica, [atribuição: técnico definido pela organização ou funções].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure implementa os seguintes tipos de conta de sistema: os utilizadores do Azure Active Directory, os utilizadores do sistema operativo Windows, conta de serviço do SQL Server. Ações de gestão de conta do Azure Active Directory geram um evento no registo de atividade do Azure; Ações de gestão de conta de nível de SO geram um evento no registo do sistema. Estes registos recolhidos através da análise de registos e armazenados no repositório de OMS. OMS pode ser configurado para enviar alertas quando ocorrem eventos predefinidos.  |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-ac-2-5"></a>NIST 800-53 controlo AC-2 (5)

#### <a name="account-management--inactivity-logout"></a>Gestão de contas | Fim de sessão de inatividade

**AC-2 (5)** organização exige que os utilizadores terminar quando [atribuição: definido de organização-período de tempo de inatividade esperada ou a descrição de quando deve terminar].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Política de controlo de acesso de nível empresarial do cliente pode estabelecer uma política que os utilizadores terminar sessão quando que antecipa para ficar inativo para um período de tempo (ou outros fatores). |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-ac-2-7a"></a>Controlar o NIST 800-53 .a (7) de AC-2

#### <a name="account-management--role-based-schemes"></a>Gestão de contas | Esquemas baseada em funções

**AC-2 (7) .a** organização estabelece e gere contas de utilizador com privilégios de acordo com um esquema de acesso baseado em funções que organiza os privilégios e acesso de sistema de informações permitido em funções.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure implementa os seguintes tipos de conta de sistema: os utilizadores do Azure Active Directory, os utilizadores do sistema operativo Windows, conta de serviço do SQL Server. Privilégios de conta do Azure Active Directory são implementados utilizando o controlo de acesso baseado em funções ao atribuir utilizadores a funções; Privilégios de conta do Active Directory são implementados utilizando o controlo de acesso baseado em funções através da atribuição de utilizadores para grupos de segurança. Estes esquemas baseada em funções podem ser expandidos pelo cliente para satisfazer as necessidades de missão. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-ac-2-7b"></a>Controlar o NIST 800-53 .b (7) de AC-2

#### <a name="account-management--role-based-schemes"></a>Gestão de contas | Esquemas baseada em funções

**AC-2 (7) .b** organização monitoriza atribuições de função com privilégios.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure implementa o Dashboard de acesso e identidade da solução de auditoria e segurança do OMS. Este dashboard permite gestores de conta para monitorizar a utilização de contas de sistema de informações. Esta solução pode ser consultada para comunicar atribuições de função com privilégios. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-ac-2-7c"></a>Controlar o NIST 800-53 .c (7) de AC-2

#### <a name="account-management--role-based-schemes"></a>Gestão de contas | Esquemas baseada em funções

**.C AC-2 (7)** a organização demora [atribuição: ações definidas por organização] quando já não são adequadas atribuições de função com privilégios.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por efetuar a ação em contas controlados pelo cliente quando atribuições de função com privilégios já não são adequadas. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-ac-2-9"></a>NIST 800-53 controlo AC-2 (9)

#### <a name="account-management--restrictions-on-use-of-shared--group-accounts"></a>Gestão de contas | Restrições de utilização de contas de grupo / partilhadas

**AC-2 (9)** organização apenas permite a utilização de contas de grupo/partilhado que cumpram [atribuição: condições definido pela organização para estabelecer as contas de grupo/partilhado].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Não existem contas de acesso partilhado/grupo estão ativadas nos recursos implementados por este Blueprint do Azure. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-ac-2-10"></a>NIST 800-53 controlo AC-2 (10)

#### <a name="account-management--shared--group-account-credential-termination"></a>Gestão de contas | Terminação de credencial de conta partilhada / grupo

**AC-2 (10)** o sistema de informações termina as credenciais da conta partilhada/grupo quando membros deixam o grupo.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Não existem contas de acesso partilhado/grupo estão ativadas nos recursos implementados por este Blueprint do Azure. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-ac-2-11"></a>NIST 800-53 controlo AC-2 (11)

#### <a name="account-management--usage-conditions"></a>Gestão de contas | Condições de utilização

**AC-2 (11)** impõe o sistema de informações [atribuição: circunstâncias definido de organização e/ou condições de utilização] para [atribuição: contas do sistema de informações definidas pela organização].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure implementa um controlador de domínio ao qual estão associadas a todas as máquinas virtuais implementadas. Uma política de grupo pode ser estabelecida no Active Directory e configurada para implementar restrições de hora do dia ou por outras condições de utilização da conta. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-ac-2-12a"></a>Controlar o NIST 800-53 .a (12) de AC-2

#### <a name="account-management--account-monitoring--atypical-usage"></a>Gestão de contas | Conta a utilização de monitorização / atípica

**AC-2 (12) .a** organização monitoriza as contas de sistema de informações para [atribuição: definido de organização atípica utilização].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure implementa a auditoria e segurança do OMS dashboard da solução identidade e acesso. Este dashboard permite gestores de conta para monitorizar tentativas de acesso relativamente aos recursos implementados. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-ac-2-12b"></a>Controlar o NIST 800-53 .b (12) de AC-2

#### <a name="account-management--account-monitoring--atypical-usage"></a>Gestão de contas | Conta a utilização de monitorização / atípica

**AC-2 (12) .b** organização atípica utilização de contas de sistema de informações para os relatórios [atribuição: técnico definido pela organização ou funções].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure implementa a auditoria e segurança do OMS dashboard da solução identidade e acesso. Este dashboard ativar gestores de conta para monitorizar tentativas de acesso relativamente aos recursos implementados. Esta solução pode ser configurada para enviar alertas quando a atividade atípica for suspeito ou outros eventos predefinidos ocorrem. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-ac-2-13"></a>NIST 800-53 controlo AC-2 (13)

#### <a name="account-management--disable-accounts-for-high-risk-individuals"></a>Gestão de contas | Desativar as contas de alto risco indivíduos

**AC-2 (13)** organização desativa as contas de utilizadores está a representar um risco significativo dentro [atribuição: organização definidos pelo período de tempo] de deteção do risco.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Política de controlo de acesso de nível empresarial do cliente e procedimentos poderão estabelecer condições para desativar as contas de utilizadores está a representar um risco significativo na organização. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-ac-3"></a>Controlo NIST 800-53 AC-3

#### <a name="access-enforcement"></a>Imposição de acesso

**AC 3** o sistema de informações impõe autorizações aprovadas para acesso lógico a recursos de sistema e informações de acordo com as políticas de controlo de acesso aplicáveis.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint do Azure impõe autorizações acesso lógico a utilizar o controlo de acesso baseado em funções imposto pelo Azure Active Directory por atribuir utilizadores a funções, do Active Directory através da atribuição de utilizadores para grupos de segurança e controlos de nível de SO Windows. Funções do Azure Active Directory atribuídos a utilizadores ou grupos de controlam o acesso lógico a recursos no Azure ao nível do recurso, grupo ou subscrição. Grupos de segurança do Active Directory controlam o acesso lógico a funções e recursos de nível de SO. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-ac-4"></a>Controlo NIST 800-53 AC-4

#### <a name="information-flow-enforcement"></a>Imposição de fluxo de informações

**AC 4** o sistema de informações impõe autorizações aprovadas para controlar o fluxo de informações no sistema e entre sistemas interligados com base nos [atribuição: políticas de controlo de fluxo de informações definidas pela organização].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint do Azure impõe restrições de fluxo de informações através da utilização de grupos de segurança de rede aplicadas às sub-redes nas quais os recursos são implementados, Gateway de aplicação e o Balanceador de carga. Grupos de segurança de rede Certifique-se de que o fluxo de informações é controlado entre os recursos com base nas regras aprovadas. Balanceador de Gateway e a carga de aplicação dinamicamente encaminhar o tráfego a recursos específicos com base em funções aprovadas. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-ac-4-8"></a>NIST 800-53 controlo AC-4 (8)

#### <a name="information-flow-enforcement--security-policy-filters"></a>Imposição de fluxo de informações | Filtros de política de segurança

**AC-4 (8)** impõe do sistema de informações através de controlo de fluxo de informações [atribuição: filtros de política de segurança definido para organização] como base para tomar decisões de controlo de fluxo para [atribuição: informações definidas pela organização fluxos].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por impor o controlo de fluxo de informações nos recursos de implementação de cliente. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-ac-4-21"></a>NIST 800-53 controlo AC-4 (21)

#### <a name="information-flow-enforcement--physical--logical-separation-of-information-flows"></a>Imposição de fluxo de informações | Separação lógica / física de fluxos de informações

**AC-4 (21)** o sistema de informações separa logicamente ou fisicamente através de fluxos de informações [atribuição: mecanismos definido de organização e/ou técnicas] para realizar [atribuição: definido de organização necessário separations por tipos de informações].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por separar os fluxos de informações nos recursos de implementação de cliente. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-ac-5a"></a>Controlo NIST 800-53 AC-5.a

#### <a name="separation-of-duties"></a>Separação de deveres

**AC 5.a** organização separa [atribuição: definido de organização deveres do indivíduos].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável pela separação de deveres em contas de controlados pelo cliente. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-ac-5b"></a>Controlo NIST 800-53 AC-5.b

#### <a name="separation-of-duties"></a>Separação de deveres

**AC 5.b** organização documentos separação de deveres do indivíduos.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável pela documentação a separação de deveres em contas de controlados pelo cliente. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-ac-5c"></a>Controlo NIST 800-53 AC-5.c

#### <a name="separation-of-duties"></a>Separação de deveres

**AC 5.c** organização define autorizações de acesso de sistema de informações para suportar a separação de deveres.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure implementa o controlo de acesso baseado em funções que pode ser configurado para separar os deveres de acordo com os requisitos da organização. Privilégios de conta do Azure Active Directory são implementados utilizando o controlo de acesso baseado em funções ao atribuir utilizadores a funções; Privilégios de conta do Active Directory são implementados utilizando o controlo de acesso baseado em funções através da atribuição de utilizadores para grupos de segurança. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-ac-6"></a>Controlo NIST 800-53 AC-6

#### <a name="least-privilege"></a>Menor privilégio

**AC 6** a organização utiliza o princípio do menor privilégio, permitindo apenas autorizados acessos de utilizadores (ou processos agir em nome dos utilizadores) que são necessários para realizar tarefas atribuídas de acordo com missions organizacionais e funções de negócio.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure implementa o controlo de acesso baseado em funções para restringir os utilizadores para apenas os privilégios explicitamente atribuídos. Privilégios de conta do Azure Active Directory são implementados utilizando o controlo de acesso baseado em funções ao atribuir utilizadores a funções; Privilégios de conta do Active Directory são implementados utilizando o controlo de acesso baseado em funções através da atribuição de utilizadores para grupos de segurança.  |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-ac-6-1"></a>NIST 800-53 controlo AC-6 (1)

#### <a name="least-privilege--authorize-access-to-security-functions"></a>Menor privilégio | Autorizar o acesso às funções de segurança

**AC-6 (1)** organização explicitamente autoriza o acesso a [atribuição: funções de segurança definido por organização (implementadas no hardware, software e firmware) e informações de segurança relevantes].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Procedimentos de controlo de acesso de nível empresarial do cliente poderão estabelecer um processo de autorização de acesso que inclui o acesso a funções de segurança. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-ac-6-2"></a>NIST 800-53 controlo AC-6 (2)

#### <a name="least-privilege--non-privileged-access-for-nonsecurity-functions"></a>Menor privilégio | Sem privilégios acesso para Nonsecurity funções

**AC-6 (2)** organização exige que os utilizadores de contas de informações do sistema ou funções, com acesso a [atribuição: funções de segurança definido pela organização ou informações de segurança relevantes], utilizar contas sem privilégios ou funções, quando aceder a funções de nonsecurity.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Política de controlo de acesso de nível empresarial do cliente pode exigir que os utilizadores utilizar contas sem privilégios quando aceder ao funções nonsecurity. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-ac-6-3"></a>NIST 800-53 controlo AC-6 (3)

#### <a name="least-privilege--network-access-to-privileged-commands"></a>Menor privilégio | Acesso de rede aos comandos com privilégios

**AC-6 (3)** organização autoriza o acesso à rede [atribuição: definido de organização comandos com privilégios] apenas para [atribuição: definido organização apelativa necessidades operacionais] e documentos a lógica por detrás para esse acesso a plano de segurança para o sistema de informações.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Política de controlo de acesso de nível empresarial do cliente pode definir comandos com privilégios que podem ser acedidos através de uma rede. Nota: Os clientes têm sem acesso físico à infraestrutura do Azure. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-ac-6-5"></a>NIST 800-53 controlo AC-6 (5)

#### <a name="least-privilege--privileged-accounts"></a>Menor privilégio | Contas com privilégios

**AC-6 (5)** organização restringe contas com privilégios no sistema de informações para [atribuição: técnico definido pela organização ou funções].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Política de controlo de acesso de nível empresarial do cliente pode definir restrições para a utilização de contas com privilégios. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-ac-6-7a"></a>Controlar o NIST 800-53 .a (7) de AC 6

#### <a name="least-privilege--review-of-user-privileges"></a>Menor privilégio | Revisão de privilégios de utilizador

**.A AC-6 (7)** as revisões de organização [atribuição: frequência definida de organização] os privilégios atribuídos a [atribuição: funções definidas pelo organização ou classes de utilizadores] para validar a necessidade desses privilégios.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável pela revisão privilégios de utilizador das contas controlados pelo cliente. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-ac-6-7b"></a>Controlar o NIST 800-53 .b (7) de AC 6

#### <a name="least-privilege--review-of-user-privileges"></a>Menor privilégio | Revisão de privilégios de utilizador

**AC-6 (7) .b** a organização é reatribuído ou remove privilégios, se necessário, para refletir corretamente necessidades de negócio/missão organizacional.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por reatribuição ou remover privilégios para contas controlados pelo cliente quando for adequado. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-ac-6-8"></a>NIST 800-53 controlo AC-6 (8)

#### <a name="least-privilege--privilege-levels-for-code-execution"></a>Menor privilégio | Níveis de privilégio para a execução do código

**AC-6 (8)** impede que o sistema de informações [atribuição: software definida pelo organização] execução níveis superiores privilégios que os utilizadores executar o software.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure implementa o controlo de acesso baseado em funções para restringir os utilizadores para apenas os privilégios explicitamente atribuídos. Proteções de nível de SO da máquina virtual não permitem o software executar um nível de privilégio mais elevado que os utilizadores executar o software. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-ac-6-9"></a>NIST 800-53 controlo AC-6 (9)

#### <a name="least-privilege--auditing-use-of-privileged-functions"></a>Menor privilégio | Utilização de funções com privilégios de auditoria

**AC-6 (9)** o sistema de informações auditorias a execução das funções com privilégios.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure implementa o serviço de análise de registos no OMS. Implementado VMs e o Azure contas de armazenamento do diagnostics são ligadas origens para análise de registos assegurar que a execução das funções com privilégios é auditada. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-ac-6-10"></a>NIST 800-53 controlo AC-6 (10)

#### <a name="least-privilege--prohibit-non-privileged-users-from-executing-privileged-functions"></a>Menor privilégio | Proibir utilizadores não privilegiados execução funções com privilégios

**AC-6 (10)** o sistema de informações impede que utilizadores não privilegiados executar funções com privilégios para incluir a desativação, circumventing, ou alterar implementado as proteções de segurança/medidas preventivas.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure implementa o controlo de acesso baseado em funções para restringir os utilizadores para apenas os privilégios explicitamente atribuídos.  |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-ac-7a"></a>Controlo NIST 800-53 AC-7.a

#### <a name="unsuccessful-logon-attempts"></a>Tentativas de início de sessão sem êxito

**AC 7.a** o sistema de informações impõe um limite de [atribuição: número definido de organização] tentativas de início de sessão inválido consecutivo por um utilizador durante uma [atribuição: organização definidos pelo período de tempo].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Tentativas de início de sessão limites de portal do Azure consecutivos inválido pelos utilizadores. Uma política de grupo é aplicada ao nível do sistema operativo para todas as máquinas virtuais implementadas por esta Blueprint do Azure. A política limita tentativas de início de sessão inválido consecutivos pelos utilizadores a mais de três durante um período de 15 minutos. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-ac-7b"></a>Controlo NIST 800-53 AC-7

#### <a name="unsuccessful-logon-attempts"></a>Tentativas de início de sessão sem êxito

**7 de AC** o sistema de informações automaticamente [seleção: bloqueia o conta de nó de um [atribuição: organização definidos pelo período de tempo]; bloqueia o conta/nó até libertados por um administrador; atrasa a seguinte linha de início de sessão, de acordo com [ Atribuição: algoritmo atraso definido de organização]] quando for excedido o número máximo de tentativas sem êxito.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O portal do Azure bloqueia contas após tentativas de início de sessão inválido consecutivo pelos utilizadores. Uma política de grupo é aplicada ao nível do sistema operativo para todas as máquinas virtuais implementadas por esta Blueprint do Azure. A política bloqueia contas para três horas após três tentativas de início de sessão inválido consecutivos pelos utilizadores. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-ac-7-2"></a>NIST 800-53 controlo AC-7 (2)

#### <a name="unsuccessful-logon-attempts--purge--wipe-mobile-device"></a>Tentativas de início de sessão sem êxito | Remover / limpar o dispositivo móvel

**AC-7 (2)** o sistema de informações elimina/eliminações seletivas de informações a partir de [atribuição: definido de organização dispositivos móveis] com base no [atribuição: definida organização remover/limpar requisitos/técnicas] depois [atribuição: tentativas de início de sessão do número definido de organização] dispositivos sem consecutivos.

**Responsabilidades:**`Not Applicable`

|||
|---|---|
| **Cliente** | Dispositivos móveis não estão no âmbito dos sistemas implementado no Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure não permitir que os dispositivos móveis dentro do limite do Azure. Como tal, este controlo não é aplicável ao Microsoft Azure. |


 ## <a name="nist-800-53-control-ac-8a"></a>Controlo NIST 800-53 AC-8.a

#### <a name="system-use-notification"></a>Notificação de utilização do sistema

**AC 8.a** apresenta o sistema de informações aos utilizadores [atribuição: sistema definido de organização utilizar mensagem de notificação ou faixa] antes de conceder acesso ao sistema que fornece avisos de privacidade e segurança consistente com as federal aplicável leis, as ordens executivo, diretivas, políticas, regulamentos, normas e orientações e Estados que os utilizadores estão a aceder a um E.U.A. Sistema de informações de Government; utilização do sistema de informações pode ser monitorizada, registadas e estão sujeitos a auditoria; utilização não autorizada do sistema de informações é penalidades proibidas e sujeito a criminal e civis; e utilização do sistema informações indica consentimento para monitorização e registo.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure implementa um controlador de domínio ao qual estão associadas a todas as máquinas virtuais implementadas. Uma política de grupo implementa uma notificação de utilização do sistema que é apresentada aos utilizadores antes de início de sessão. Nota: O Azure Blueprint implementa uma notificação de utilização do sistema de exemplo. O cliente tem de editar este texto para satisfazer a organização e/ou requisitos de regulamentação corpo. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-ac-8b"></a>Controlo NIST 800-53 AC-8.b

#### <a name="system-use-notification"></a>Notificação de utilização do sistema

**AC 8.b** o sistema de informações mantém a mensagem de notificação ou uma faixa no ecrã até que os utilizadores aceitar as condições de utilização e tome as ações explícitas para iniciar sessão ou continuar a aceder ao sistema de informações.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure implementa um controlador de domínio ao qual estão associadas a todas as máquinas virtuais implementadas. Uma política de grupo implementa uma notificação de utilização do sistema que é apresentada aos utilizadores antes de início de sessão. O utilizador tem de confirmar a notificação para iniciar sessão. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-ac-8c"></a>Controlo NIST 800-53 AC-8.c

#### <a name="system-use-notification"></a>Notificação de utilização do sistema

**AC 8.c** o sistema de informações para sistemas acessíveis publicamente apresenta informações de utilização do sistema [atribuição: condições definidas de organização], antes de conceder mais acesso; apresenta referências, se existir, para monitorização, gravação, ou auditoria que são consistentes com accommodations de privacidade para esses sistemas que geralmente proíbem essas atividades; e inclui uma descrição das utilizações autorizadas do sistema.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por apresentar uma notificação de utilização do sistema em todos os recursos de acessíveis publicamente implementadas no cliente. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-ac-10"></a>Controlo NIST 800-53 AC-10

#### <a name="concurrent-session-control"></a>Controlo de sessão em simultâneo

**AC 10** o sistema de informações limita o número de sessões em simultâneo para cada [atribuição: conta definida pelo organização e/ou tipo de conta] para [atribuição: número definido de organização].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Uma política de sistema operativo é implementada para máquinas virtuais implementadas por esta Blueprint do Azure. A política implementa restrições de sessão em simultâneo (duas sessões). |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-ac-11a"></a>Controlo NIST 800-53 AC-11.a

#### <a name="session-lock"></a>Bloqueio de sessão

**AC 11.a** o sistema de informações impede o acesso adicional ao sistema iniciando um bloqueio de sessão após [atribuição: organização definidos pelo período de tempo] de inatividade ou após a receção de um pedido de um utilizador.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure implementa um controlador de domínio ao qual estão associadas a todas as máquinas virtuais implementadas. Uma política de grupo implementa um bloqueio de inatividade para sessões RDP efetuadas. Os utilizadores podem iniciar manualmente o bloqueio. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-ac-11b"></a>Controlo NIST 800-53 AC-11.b

#### <a name="session-lock"></a>Bloqueio de sessão

**AC 11.b** o sistema de informações mantém o bloqueio de sessão até que o utilizador reestablishes acesso através de procedimentos de identificação e autenticação estabelecidos.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure implementa um controlador de domínio ao qual estão associadas a todas as máquinas virtuais implementadas. Uma política de grupo implementa um bloqueio de inatividade para sessões RDP efetuadas. Os utilizadores têm reautenticação para desbloquear a sessão.  |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-ac-11-1"></a>NIST 800-53 controlo AC-11 (1)

#### <a name="session-lock--pattern-hiding-displays"></a>Bloqueio de sessão | Apresenta ocultos padrão

**(1) de AC-11** o sistema de informações conceals, através do bloqueio de sessão, as informações anteriormente visíveis no ecrã com uma imagem publicamente visível.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure implementa um controlador de domínio ao qual estão associadas a todas as máquinas virtuais implementadas. Uma política de grupo implementa um bloqueio de inatividade para sessões RDP efetuadas. O bloqueio de sessão conceals informações anteriormente visíveis. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-ac-12"></a>Controlo NIST 800-53 AC-12

#### <a name="session-termination"></a>Término de sessão

**AC-12** o sistema de informações automaticamente termina uma sessão de utilizador após [atribuição: condições definido pela organização ou que seja necessário desligar sessões de eventos de Acionador].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | A configuração de anfitrião de sessão de ambiente de trabalho remoto para máquinas virtuais Windows implementado por esta Blueprint Azure pode ser configurada para cumprir os requisitos de término de sessão de empresa. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-ac-12-1a"></a>(1) .a do NIST 800-53 controlo 12 de AC

#### <a name="session-termination--user-initiated-logouts--message-displays"></a>Término de sessão | Iniciada pelo utilizador Logouts / apresenta da mensagem

**(1) de AC-12 .a** o sistema de informações proporciona uma capacidade de fim de sessão para comunicações iniciada pelo utilizador sessões sempre que a autenticação é utilizada para obter acesso a [atribuição: recursos de informações definidas pela organização].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Os Azure portal e a máquina virtual sistemas operativos implementados por esta Blueprint Azure ativar utiliza iniciar um fim de sessão. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-ac-12-1b"></a>(1) .b do NIST 800-53 controlo 12 de AC

#### <a name="session-termination--user-initiated-logouts--message-displays"></a>Término de sessão | Iniciada pelo utilizador Logouts / apresenta da mensagem

**(1) de AC-12 .b** o sistema de informações apresenta uma mensagem de fim de sessão explícita para os utilizadores que indiquem a terminação fiável de sessões de comunicações autenticadas.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Os Azure portal e a máquina virtual sistemas operativos implementados por esta Blueprint Azure ativar utiliza iniciar um fim de sessão. O processo de fim de sessão fornece indicação aos utilizadores que a sessão foi terminada. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-ac-14a"></a>Controlo NIST 800-53 AC-14.a

#### <a name="permitted-actions-without-identification-or-authentication"></a>Ações permitidas sem identificação ou autenticação

**AC 14.a** identifica a organização [atribuição: ações de organização definido pelo utilizador] que podem ser efetuadas no sistema informações sem identificação ou autenticação consistente com as funções de negócio/missions organizacional.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por identificar ações que podem ser executadas nos recursos implementados no cliente sem autenticação (por exemplo, tais como visualizar uma página web acessível publicamente) ou de identificação. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-ac-14b"></a>Controlo NIST 800-53 AC-14.b

#### <a name="permitted-actions-without-identification-or-authentication"></a>Ações permitidas sem identificação ou autenticação

**AC 14.b** organização documentos e fornece suporte lógica por detrás no plano de segurança para o sistema de informações, as ações do utilizador não exigir autenticação ou de identificação.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por fornecer documentação para ações de utilizador não exigir identificação ou a autenticação nos recursos de implementação de cliente. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-ac-17a"></a>Controlo NIST 800-53 AC-17.a

#### <a name="remote-access"></a>Acesso Remoto

**AC 17.a** organização estabelece e documentos as restrições de utilização, requisitos de configuração/ligação e documentação de orientação de implementação para cada tipo de acesso remoto permitido.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Política de controlo de acesso de nível empresarial do cliente pode definir restrições de utilização de acesso remoto. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-ac-17b"></a>Controlo NIST 800-53 AC-17.b

#### <a name="remote-access"></a>Acesso Remoto

**AC 17.b** organização autoriza o acesso remoto para o sistema de informações antes de permitir que essas ligações.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Procedimentos de controlo de acesso de nível empresarial do cliente poderão estabelecer um processo de autorização de acesso remoto. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-ac-17-1"></a>NIST 800-53 controlo AC-17 (1)

#### <a name="remote-access--automated-monitoring--control"></a>Acesso remoto | Automatizada monitorização / controlar

**17 de AC (1)** o sistema de informações monitoriza e controlos de métodos de acesso remoto.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint do Azure fornece acesso remoto para o sistema de informações através do portal do Azure, através de ligações de ambiente de trabalho remoto através de um jumpbox e através de uma aplicação web implementada de cliente. Acede através do portal do Azure e sessões de ambiente de trabalho remotas é auditada e pode ser monitorizado através do OMS. O cliente tem de implementar controlos de acesso remoto, conforme necessário, para a aplicação web. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-ac-17-2"></a>NIST 800-53 controlo AC-17 (2)

#### <a name="remote-access--protection-of-confidentiality--integrity-using-encryption"></a>Acesso remoto | Proteção de confidencialidade / integridade através da encriptação

**17 de AC (2)** o sistema de informações implementa mecanismos criptográficos para proteger a confidencialidade e a integridade das sessões de acesso remoto.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Acesso remoto aos recursos implementado por esta Blueprint do Azure, incluindo o portal do Azure, a ligação ao ambiente de trabalho remoto e o gateway de aplicação web, são protegidas através de TLS. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-ac-17-3"></a>NIST 800-53 controlo AC-17 (3)

#### <a name="remote-access--managed-access-control-points"></a>Acesso remoto | Pontos de controlo de acesso gerido

**17 de AC (3)** todos os acessos remotos através de rotas de sistema informações [atribuição: número definido de organização] geridos pontos de controlo de acesso de rede.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Acesso remoto à aplicação notional web implementado por esta Blueprint do Azure é efetuada através de um gateway de aplicação. Acesso remoto para todos os outros recursos é através de um jumpbox. Não existem nenhum outros acessíveis publicamente os pontos finais. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-ac-17-4a"></a>Controlar o NIST 800-53 .a (4) 17 de AC

#### <a name="remote-access--privileged-commands--access"></a>Acesso remoto | Privilégio comandos / aceder

**17 de AC (4) .a** organização autoriza a execução de comandos com privilégios e acesso a informações relevantes de segurança através do acesso remoto apenas para [atribuição: organização definido pelo necessidades].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Política de controlo de acesso de nível empresarial do cliente pode definir comandos com privilégios que podem ser acedidos remotamente e incluem uma lógica por detrás. Nota: Os clientes têm sem acesso de rede diretamente a infraestrutura do Azure. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-ac-17-4b"></a>Controlar o NIST 800-53 .b (4) 17 de AC

#### <a name="remote-access--privileged-commands--access"></a>Acesso remoto | Privilégio comandos / aceder

**17 de AC (4) .b** organização documentos a lógica por detrás para esse acesso no plano de segurança para o sistema de informações.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Política de controlo de acesso de nível empresarial do cliente pode definir comandos com privilégios que podem ser acedidos remotamente e incluem uma lógica por detrás. Nota: Os clientes têm sem acesso de rede diretamente a infraestrutura do Azure. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-ac-17-9"></a>NIST 800-53 controlo AC-17 (9)

#### <a name="remote-access--disconnect--disable-access"></a>Acesso remoto | Desligar / desativar o acesso

**17 de AC (9)** organização fornece a capacidade de expeditiously desligar ou desative o acesso remoto para o sistema de informações na [atribuição: organização definidos pelo período de tempo].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint do Azure fornece acesso remoto para o sistema de informações através do portal do Azure, através de ligações de ambiente de trabalho remoto através de um jumpbox e através de uma aplicação web. Se uma conta do Azure Active Directory está desativada ou removida, o acesso ao portal do Azure está desligado imediatamente. Da mesma forma, se uma conta de nível de SO de máquina virtual está desativada ou removida, acesso de ambiente de trabalho remoto através de jumpbox é desligado imediatamente. Os clientes têm de implementar acesso remoto desligar para a aplicação web. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-ac-18a"></a>Controlo NIST 800-53 AC-18.a

#### <a name="wireless-access"></a>Acesso sem fios

**AC 18.a** organização estabelece as restrições de utilização, requisitos de configuração/ligação e orientação de implementação para o acesso sem fios.

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | Não há nenhum acesso sem fios dentro do âmbito dos sistemas implementado no Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure estabelece as restrições de utilização, requisitos de configuração/ligação e orientação de implementação para o acesso sem fios através da rede segurança padrão, que explicitamente proíbe a utilização de sem fios no ambiente do Microsoft Azure. |


 ## <a name="nist-800-53-control-ac-18b"></a>Controlo NIST 800-53 AC-18.b

#### <a name="wireless-access"></a>Acesso sem fios

**AC 18.b** organização autoriza o acesso sem fios para o sistema de informações antes de permitir que essas ligações.

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | Não há nenhum acesso sem fios dentro do âmbito dos sistemas implementado no Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure não permite o acesso sem fios dentro dos centros de dados do Microsoft Azure. |


 ### <a name="nist-800-53-control-ac-18-1"></a>NIST 800-53 controlo AC-18 (1)

#### <a name="wireless-access--authentication-and-encryption"></a>Sem fios | Autenticação e encriptação

**(1) de AC-18** o sistema de informações protege o acesso sem fios para o sistema através da autenticação de [seleção (um ou mais): os utilizadores; dispositivos] e encriptação.

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | Não há nenhum acesso sem fios dentro do âmbito dos sistemas implementado no Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure não permite o acesso sem fios dentro do ambiente do Microsoft Azure. |


 ### <a name="nist-800-53-control-ac-18-3"></a>NIST 800-53 controlo AC-18 (3)

#### <a name="wireless-access--disable-wireless-networking"></a>Sem fios | Desativar a redes sem fios

**18 de AC (3)** capacidades internamente incorporadas componentes do sistema informações antes de emissão e a implementação de redes sem fios desativa a organização, quando não se destina a utilização,.

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | Não há nenhum acesso sem fios dentro do âmbito dos sistemas implementado no Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure não permite o acesso sem fios dentro do ambiente do Microsoft Azure. |


 ### <a name="nist-800-53-control-ac-18-4"></a>NIST 800-53 controlo AC-18 (4)

#### <a name="wireless-access--restrict-configurations-by-users"></a>Sem fios | Restringir as configurações pelos utilizadores

**18 de AC (4)** organização identifica e explicitamente autoriza os utilizadores com permissão para configurar independentemente das capacidades de rede sem fios.

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | Não há nenhum acesso sem fios dentro do âmbito dos sistemas implementado no Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure não permite o acesso sem fios dentro do ambiente do Microsoft Azure. |


 ### <a name="nist-800-53-control-ac-18-5"></a>NIST 800-53 controlo AC-18 (5)

#### <a name="wireless-access--antennas--transmission-power-levels"></a>Sem fios | Antennas / níveis de energia de transmissão

**18 de AC (5)** organização seleciona antennas de botões de opção e calibrates níveis de energia de transmissão para reduzir a probabilidade de que podem ser recebidos sinais utilizáveis fora da organização controlada limites.

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | Não há nenhum acesso sem fios dentro do âmbito dos sistemas implementado no Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure não permite o acesso sem fios dentro do ambiente do Microsoft Azure. |


 ## <a name="nist-800-53-control-ac-19a"></a>Controlo NIST 800-53 AC-19.a

#### <a name="access-control-for-mobile-devices"></a>Controlo de acesso para dispositivos móveis

**AC 19.a** organização estabelece as restrições de utilização, requisitos de configuração, requisitos de ligação e orientação de implementação para dispositivos móveis controladas por organização.

**Responsabilidades:**`Not Applicable`

|||
|---|---|
| **Cliente** | Não existem não existem dispositivos móveis controlados pelo cliente no âmbito dos sistemas implementado no Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure não permitir que os dispositivos móveis dentro do limite do Azure. Como tal, este controlo não é aplicável ao Microsoft Azure. |


 ## <a name="nist-800-53-control-ac-19b"></a>Controlo NIST 800-53 AC-19.b

#### <a name="access-control-for-mobile-devices"></a>Controlo de acesso para dispositivos móveis

**AC 19.b** organização autoriza a ligação dos dispositivos móveis para sistemas de informações organizacionais.

**Responsabilidades:**`Not Applicable`

|||
|---|---|
| **Cliente** | Não existem não existem dispositivos móveis controlados pelo cliente no âmbito dos sistemas implementado no Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure não permitir que os dispositivos móveis dentro do limite do Azure. Como tal, este controlo não é aplicável ao Microsoft Azure. |


 ### <a name="nist-800-53-control-ac-19-5"></a>NIST 800-53 controlo AC-19 (5)

#### <a name="access-control-for-mobile-devices--full-device--container-based--encryption"></a>Controlo de acesso para dispositivos móveis | Total de dispositivos / baseado no contentor de encriptação

**19 de AC (5)** organização emprega [seleção: encriptação de dispositivo completo; encriptação contentor] para proteger a confidencialidade e a integridade das informações no [atribuição: definido de organização dispositivos móveis].

**Responsabilidades:**`Not Applicable`

|||
|---|---|
| **Cliente** | Não existem não existem dispositivos móveis controlados pelo cliente no âmbito dos sistemas implementado no Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure não permitir que os dispositivos móveis dentro do limite do Azure. Como tal, este controlo não é aplicável ao Microsoft Azure. |


 ## <a name="nist-800-53-control-ac-20a"></a>Controlo NIST 800-53 AC-20.a

#### <a name="use-of-external-information-systems"></a>Utilização de sistemas de informação externo

**AC 20.a** organização estabelece termos e condições, consistentes com quaisquer relações de fidedignidade estabelecidas com outras organizações proprietário, operar, e/ou a manutenção de sistemas de informação externo, permitindo autorizado indivíduos para aceder ao sistema de informações de sistemas de informação externo.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Política de controlo de acesso de nível empresarial do cliente pode incluir um aprovisionamento sobre a utilização de ofertas de serviço em nuvem em FedRAMP. Azure tenha sido concedido uma autorização provisional operar (P-/ATO) pelo FedRAMP conjunta autorização quadro (JAB) permitindo utilização de aquisição de cloud services do Azure por agências governamentais. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-ac-20b"></a>Controlo NIST 800-53 AC-20.b

#### <a name="use-of-external-information-systems"></a>Utilização de sistemas de informação externo

**AC 20.b** organização estabelece termos e condições, consistentes com quaisquer relações de fidedignidade estabelecidas com outras organizações proprietário, operar, e/ou a manutenção de sistemas de informação externo, permitindo autorizado indivíduos para processar e armazenar ou transmitir informações controlado de organização através de sistemas de informação externo.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Política de controlo de acesso de nível empresarial do cliente pode incluir um aprovisionamento sobre a utilização de ofertas de serviço em nuvem em FedRAMP. Azure tenha sido concedido uma autorização provisional operar (P-/ATO) pelo FedRAMP conjunta autorização quadro (JAB) permitindo utilização de aquisição de cloud services do Azure por agências governamentais. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-ac-20-1"></a>NIST 800-53 controlo AC-20 (1)

#### <a name="use-of-external-information-systems--limits-on-authorized-use"></a>Utilização de sistemas de informação externo | Limites de utilização autorizada

**(1) de AC-20** organização permite que as pessoas autorizadas a utilizar um sistema de informações externas para aceder ao sistema de informações ou para processar e armazenar ou transmitir informações controlado de organização apenas quando a organização verifica a implementação de controlos de segurança necessárias no sistema externo conforme especificado na política de segurança de informações da organização e planear a segurança; ou mantém aprovados informações de contratos de ligação ou processamento de sistema com a entidade de organização que aloja o sistema externo de informações.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Grupo de tecnologia de informações de nível empresarial do cliente pode verificar a conformidade de fornecedor de serviço em nuvem com requisitos de segurança de informações de organização e conceder aprovação de nível da empresa para utilizar as ofertas de serviço de nuvem associada. Azure tenha sido concedido uma autorização provisional operar (P-/ATO) pelo FedRAMP conjunta autorização quadro (JAB). Azure é avaliado por uma organização de avaliação aprovado FedRAMP terceiros (3PAO) para verificar a conformidade com o controlo de segurança FedRAMP e outros requisitos. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-ac-20-2"></a>NIST 800-53 controlo AC-20 (2)

#### <a name="use-of-external-information-systems--portable-storage-devices"></a>Utilização de sistemas de informação externo | Dispositivos de armazenamento portátil

**(2) de AC-20** organização [seleção: restringe; proíbe] a utilização de dispositivos de armazenamento portátil organização controladas por pessoas autorizadas em sistemas externos de informações.

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a quaisquer recursos de sistema nos centros de dados do Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft não permitir que os dispositivos de armazenamento portátil controlados pelo cliente no ambiente do Microsoft Azure. |


 ## <a name="nist-800-53-control-ac-21a"></a>Controlo NIST 800-53 AC-21.a

#### <a name="information-sharing"></a>Partilha de informações

**AC 21.a** organização facilita as informações de partilha, permitindo que os utilizadores autorizados determinar se autorizações de acesso atribuídas ao parceiro de partilha correspondem as restrições de acesso nas informações para [atribuição: organização informações definidas pela partilha circunstâncias onde o critério de utilizador é necessário].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Política de controlo de acesso de nível empresarial do cliente pode incluir Aprovisiona relativas à partilha de informações. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-ac-21b"></a>Controlo NIST 800-53 AC-21.b

#### <a name="information-sharing"></a>Partilha de informações

**AC 21.b** organização emprega [atribuição: definido de organização automatizada mecanismos ou processos manuais] para ajudar os utilizadores nas decisões de informações de partilha/colaboração.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente pode confiar uma informações de nível empresarial partilha a capacidade de suporte da decisão. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-ac-22a"></a>Controlo NIST 800-53 AC-22.a

#### <a name="publicly-accessible-content"></a>Conteúdo acessível publicamente

**AC 22.a** organização designa indivíduos autorizados a publicar informações para um sistema de informações acessível publicamente.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Procedimentos de controlo de acesso de nível empresarial do cliente podem designar indivíduos autorizados a publicar informações acessíveis publicamente. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-ac-22b"></a>Controlo NIST 800-53 AC-22.b

#### <a name="publicly-accessible-content"></a>Conteúdo acessível publicamente

**AC 22.b** organização trains pessoas autorizadas, para se certificar de que informações acessíveis publicamente contêm informações nonpublic.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente poderá confiar na formação de nível empresarial para os indivíduos autorizados a publicar informações acessíveis publicamente. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-ac-22c"></a>Controlo NIST 800-53 AC-22.c

#### <a name="publicly-accessible-content"></a>Conteúdo acessível publicamente

**AC 22.c** organização revê o conteúdo proposto de informações antes de proceder no sistema para se certificar de que as informações nonpublic não estão incluídas informações acessível publicamente.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Procedimentos de controlo de acesso de nível empresarial do cliente poderão estabelecer um processo de revisão para o conteúdo proposto seja publicado um sistema acessível publicamente. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-ac-22d"></a>Controlo NIST 800-53 AC-22.d

#### <a name="publicly-accessible-content"></a>Conteúdo acessível publicamente

**AC 22.d** organização revê o conteúdo no sistema informações acessível publicamente para nonpublic informações [atribuição: frequência definida de organização] e remove a essas informações, se detetado.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Procedimentos de controlo de acesso de nível empresarial do cliente poderão estabelecer um processo de revisão periódica do conteúdo publicado sistemas acessíveis publicamente. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |

