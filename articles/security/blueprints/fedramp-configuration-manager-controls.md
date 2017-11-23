---
title: "FedRAMP Azure Blueprint automatização - gestão de configuração"
description: "Aplicações Web para FedRAMP - gestão de configuração"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 5b953c0d-236f-4b61-b2c5-df2199490c73
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: e93aa430b7150f07210f5d1f37e2027d95334a59
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2017
---
# <a name="configuration-management-cm"></a>Gestão de configuração (CM)

> [!NOTE]
> Estes controlos são definidos pelo NIST e para os E.U.A. Departamento de comércio como parte da revisão a publicação especial do NIST 800-53 4. Consulte Rev 4 do NIST 800-53. para obter informações sobre testes de procedimentos e as diretrizes para cada controlo.

## <a name="nist-800-53-control-cm-1"></a>Controlo NIST 800-53 CM-1

#### <a name="configuration-management-policy-and-procedures"></a>Política de gestão de configuração e de procedimentos

**CM 1** organização desenvolvidas pela organização, documentos e disseminates para [atribuição: técnico definido pela organização ou funções] uma política de gestão de configuração que endereços objetivo, âmbito, funções, responsabilidades, compromisso de gestão, coordenação entre entidades organizacionais e de conformidade; e procedimentos para facilitar a implementação da política de gestão de configuração e controlos de gestão de configuração associados; analisa e atualiza a política de gestão de configuração atual [atribuição: frequência definida de organização]; e procedimentos de gestão de configuração [atribuição: frequência definida de organização].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Política de gestão de configuração de nível empresarial e procedimentos do cliente podem ser suficientes para este controlo de endereços. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-cm-2"></a>Controlo NIST 800-53 CM-2

#### <a name="baseline-configuration"></a>Configuração de linha de base

**CM 2** organização desenvolvidas pela organização, documentos e mantém sob o controlo de configuração, uma configuração de linha de base atual do sistema de informações.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Os modelos Azure Resource Manager e o associada recursos que compõem este Blueprint Azure representam uma linha de base "configuração como código" para a arquitetura implementada. A solução é fornecida apesar GitHub, que pode ser utilizada para controlo de configuração. A solução inclui uma linha de base de configuração de estado pretendido (DSC) para cada máquina virtual implementada. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-cm-2-1a"></a>(1) .a do NIST 800-53 controlo CM-2

#### <a name="baseline-configuration--reviews-and-updates"></a>Configuração de linha de base | Revisões e atualizações

**CM-2 (1) .a** organização revê e atualiza a configuração de linha de base do sistema de informações [atribuição: frequência definida de organização].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por rever e atualizar a configuração de linha de base dos recursos de implementação de cliente (para incluir aplicações, sistemas operativos, bases de dados e software). |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-cm-2-1b"></a>(1) .b do NIST 800-53 controlo CM-2

#### <a name="baseline-configuration--reviews-and-updates"></a>Configuração de linha de base | Revisões e atualizações

**CM-2 (1) .b** organização revê e atualiza a configuração de linha de base do sistema de informações quando necessário devido a [circunstâncias atribuição definido de organização].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por rever e atualizar a configuração de linha de base dos recursos implementados no cliente quando necessário. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-cm-2-1c"></a>(1) .c do NIST 800-53 controlo CM-2

#### <a name="baseline-configuration--reviews-and-updates"></a>Configuração de linha de base | Revisões e atualizações

**CM-2 (1) .c** organização revê e atualiza a configuração de linha de base do sistema informações como parte integrante das instalações de componente do sistema de informações e atualizações.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por rever e atualizar a configuração de linha de base dos recursos implementados no cliente quando necessário. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-cm-2-2"></a>NIST 800-53 controlo CM-2 (2)

#### <a name="baseline-configuration--automation-support-for-accuracy--currency"></a>Configuração de linha de base | Suporte de automatização em termos de exatidão / moeda

**CM-2 (2)** organização emprega automatizados mecanismos para manter uma configuração de linha de base atualizadas, completas, exata e prontamente disponíveis do sistema de informações.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Os modelos Azure Resource Manager e o associada recursos que compõem este Blueprint Azure representam uma linha de base "configuração como código" para a arquitetura implementada. A solução é fornecida apesar GitHub, que pode ser utilizada para controlo de configuração. No portal do Azure, um script de automatização está disponível para todos os recursos implementados e fornece uma representação sempre atualizada esses recursos.  |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-cm-2-3"></a>NIST 800-53 controlo CM-2 (3)

#### <a name="baseline-configuration--retention-of-previous-configurations"></a>Configuração de linha de base | Retenção configurações anteriores

**CM-2 (3)** mantém a organização [atribuição: organização-definido versões anteriores das configurações de linha de base do sistema informações] para suportar a reversão.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por manter versões anteriores das configurações de linha de base para os recursos implementados no cliente. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-cm-2-7a"></a>Controlar o NIST 800-53 .a (7) do CM-2

#### <a name="baseline-configuration--configure-systems-components-or-devices-for-high-risk-areas"></a>Configuração de linha de base | Configurar os sistemas, componentes ou dispositivos para áreas de alto risco

**.A CM-2 (7)** os problemas de organização [atribuição: dispositivos, sistemas informações definidas pela organização ou componentes do sistema] com [atribuição: configurações definido de organização] para indivíduos estiverem em deslocação para localizações de que a organização Considere do risco significativo.

**Responsabilidades:**`Not Applicable`

|||
|---|---|
| **Cliente** | Não existem nenhum dispositivos físicos controlados pelo cliente no âmbito dos sistemas implementado no Azure. |
| **Fornecedor (Microsoft Azure)** | Conteúdo de cliente do Microsoft Azure nunca é armazenado fora do Microsoft Azure, que está localizado fisicamente dentro de continental dos Estados Unidos. Técnico do Microsoft Azure não viajam com dispositivos contidos o inventário do Microsoft Azure. Como tal, este controlo não é aplicável ao Microsoft Azure. |


 ### <a name="nist-800-53-control-cm-2-7b"></a>Controlar o NIST 800-53 .b (7) do CM-2

#### <a name="baseline-configuration--configure-systems-components-or-devices-for-high-risk-areas"></a>Configuração de linha de base | Configurar os sistemas, componentes ou dispositivos para áreas de alto risco

**.B CM-2 (7)** a organização aplica [atribuição: as proteções de segurança definido para organização] para os dispositivos quando os indivíduos devolvem.

**Responsabilidades:**`Not Applicable`

|||
|---|---|
| **Cliente** | Não existem nenhum dispositivos físicos controlados pelo cliente no âmbito dos sistemas implementado no Azure. |
| **Fornecedor (Microsoft Azure)** | Conteúdo de cliente do Microsoft Azure nunca é armazenado fora do Microsoft Azure e técnicos do Microsoft Azure não viajam com dispositivos contidos o inventário do Microsoft Azure, deste modo, não é aplicável a este controlo. |


 ## <a name="nist-800-53-control-cm-3a"></a>Controlo NIST 800-53 CM-3.a

#### <a name="configuration-change-control"></a>Controlo de alterações de configuração

**CM 3.a** organização determina os tipos de alterações para o sistema de informações que são controladas por configuração.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por determinar que tipos de alterações a recursos implementados de cliente (para incluir aplicações, sistemas operativos, bases de dados e software) são controladas por configuração. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-cm-3b"></a>Controlo NIST 800-53 CM-3.b

#### <a name="configuration-change-control"></a>Controlo de alterações de configuração

**CM 3.b** organização revê alterações propostas controlado de configuração para o sistema de informações e aprova ou disapproves essas alterações com consideração explícita de análises de impacto de segurança.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável pela revisão alterações propostas controlado de configuração para os recursos implementados no cliente. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-cm-3c"></a>Controlo NIST 800-53 CM-3.c

#### <a name="configuration-change-control"></a>Controlo de alterações de configuração

**CM 3.c** a configuração de documentos da organização uma alteração das decisões associadas com o sistema de informações.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável pela documentação alterações controlado de configuração associadas a recursos implementados de cliente (consulte CM 03.b). |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-cm-3d"></a>Controlo NIST 800-53 CM-3.d

#### <a name="configuration-change-control"></a>Controlo de alterações de configuração

**CM 3.d** organização implementa alterações aprovadas controlado de configuração para o sistema de informações.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável pela implementação de alterações de configuração controlada aprovadas no CM 03.b. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-cm-3e"></a>Controlo NIST 800-53 CM-3.e

#### <a name="configuration-change-control"></a>Controlo de alterações de configuração

**3.e de CM** A organização mantém os registos de alterações controlado de configuração para o sistema de informações para [atribuição: organização definidos pelo período de tempo].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por manter um registo de alterações controlado de configuração para os recursos implementados no cliente. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-cm-3f"></a>Controlo NIST 800-53 CM-3.f

#### <a name="configuration-change-control"></a>Controlo de alterações de configuração

**CM 3.f** organização auditorias e revê actividades associadas a alterações controlado de configuração para o sistema de informações.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável pela auditoria e rever as alterações de configuração. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-cm-3g"></a>Controlo NIST 800-53 CM-3.g

#### <a name="configuration-change-control"></a>Controlo de alterações de configuração

**CM 3.g** organização coordena e fornece supervisão para atividades de controlo de alterações de configuração através de [atribuição: elemento de controlo para alteração de configuração definida por organização (por exemplo, AD, placa)] que convenes [seleção ( um ou mais): [atribuição: frequência definida de organização]; [Atribuição: condições de alteração de configuração definida pelo organização]].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por coordenação e pelo fornecimento de supervisão para atividades de controlo de alterações de configuração. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-cm-3-1a"></a>(1) .a do NIST 800-53 controlo CM 3

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>Controlo de alterações de configuração | Automatizada documento / notificação / Prohibition das alterações

**CM-3 (1) .a** organização emprega mecanismos automáticos para documentos propostas de alterações para o sistema de informações.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por empregar mecanismos automáticos para documentos alterações propostas (consulte CM 03.b). |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-cm-3-1b"></a>(1) .b do NIST 800-53 controlo CM 3

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>Controlo de alterações de configuração | Automatizada documento / notificação / Prohibition das alterações

**CM-3 (1) .b** organização emprega mecanismos automatizados para o notificar [atribuição: autoridades de aprovação definido organizados] de alterações propostas às informações de sistema e o pedido de alterar a aprovação.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável pela utilização de um mecanismo automatizado para aprovação de rota e a pedido para que as alterações propostas recursos implementados no cliente. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-cm-3-1c"></a>(1) .c do NIST 800-53 controlo CM 3

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>Controlo de alterações de configuração | Automatizada documento / notificação / Prohibition das alterações

**CM-3 (1) .c** organização emprega automatizados mecanismos para realçar propostas de alterações para o sistema de informações que não foram aprovados ou disapproved por [atribuição: organização definidos pelo período de tempo].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável pela utilização de um mecanismo para realçar propostas de alterações unreviewed automatizado. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-cm-3-1d"></a>(1) .d do NIST 800-53 controlo CM 3

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>Controlo de alterações de configuração | Automatizada documento / notificação / Prohibition das alterações

**CM-3 (1) .d** organização emprega automatizados mecanismos para proibir alterações para o sistema de informações até aprovações designadas são recebidas.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável pela utilização de um mecanismo automatizado para proibir a implementação de alterações não aprovadas a recursos implementados no cliente. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-cm-3-1e"></a>(1) .e do NIST 800-53 controlo CM 3

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>Controlo de alterações de configuração | Automatizada documento / notificação / Prohibition das alterações

**.E CM-3 (1)** A organização utiliza mecanismos automatizados para todas as alterações para o sistema de informações de documentos.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável pela utilização de um mecanismo automático para documentos todas as alterações implementadas para os recursos implementados de cliente. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-cm-3-1f"></a>(1) .f do NIST 800-53 controlo CM 3

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>Controlo de alterações de configuração | Automatizada documento / notificação / Prohibition das alterações

**.F CM-3 (1)** organização emprega mecanismos automatizados para o notificar [atribuição: técnico definido de organização] quando alterações aprovadas para o sistema de informações estão concluídas.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável pela utilização de um mecanismo automatizado para fornecer notificações quando forem concluídas alterações aprovadas para os recursos implementados no cliente. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-cm-3-2"></a>NIST 800-53 controlo CM-3 (2)

#### <a name="configuration-change-control--test--validate--document-changes"></a>Controlo de alterações de configuração | Testar / validar / alterações de documentos

**CM-3 (2)** organização testes, valida e documentos alterações para o sistema de informações antes de implementar as alterações no sistema operacional.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por teste, validação e documentar as alterações aos recursos implementados no cliente antes da implementação. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-cm-3-4"></a>NIST 800-53 controlo CM-3 (4)

#### <a name="configuration-change-control--security-representative"></a>Controlo de alterações de configuração | Representante de segurança

**CM-3 (4)** a organização necessita de um representante de segurança de informações para ser um membro do [atribuição: elemento de controlo de alterações de configuração de definido de organização].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável pela atribuição de um representante de segurança de informações para ser um membro do elemento de controlo de alterações definido no CM 03.g. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-cm-3-6"></a>NIST 800-53 controlo CM-3 (6)

#### <a name="configuration-change-control--cryptography-management"></a>Controlo de alterações de configuração | Gestão de criptografia

**CM-3 (6)** organização garante que os mecanismos criptográficos utilizada para fornecer [atribuição: as proteções de segurança definido para organização] estão em gestão de configuração.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por assegurar que os mecanismos criptográficos estão em gestão de configuração. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-cm-4"></a>Controlo NIST 800-53 CM-4

#### <a name="security-impact-analysis"></a>Análise de impacto de segurança

**CM 4** organização analisa as alterações para o sistema de informações para determinar o potencial impactos de segurança antes da implementação de alteração.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável pela análise alterações propostas aos recursos implementados no cliente. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-cm-4-1"></a>NIST 800-53 controlo CM-4 (1)

#### <a name="security-impact-analysis--separate-test-environments"></a>Análise de impacto de segurança | Ambientes de teste separada

**CM-4 (1)** organização analisa as alterações para o sistema de informações num ambiente de teste separada antes da implementação num ambiente operacional, procurando impactos de segurança devidos a falhas, fragilidades, incompatibilidade ou intencional malice.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável pela análise alterações propostas aos recursos implementados de cliente num ambiente de teste antes da implementação num ambiente operacional. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-cm-5"></a>Controlo NIST 800-53 CM-5

#### <a name="access-restrictions-for-change"></a>Restrições de acesso de alteração

**CM 5** organização define, documentos, aprova e impõe restrições de acesso físicas e lógicas associadas a alterações no sistema de informações.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Azure Active Directory conta privilégios são implementados utilizando o controlo de acesso baseado em funções ao atribuir utilizadores a funções fornecer strict controlo sobre quais os utilizadores podem ver e controlo implementado recursos. Privilégios de conta do Active Directory são implementados utilizando o controlo de acesso baseado em funções através da atribuição de utilizadores para grupos de segurança. Estes grupos de segurança controlam as ações que os utilizadores podem executar no que respeita à configuração do sistema operativo. Estes esquemas baseada em funções podem ser expandidos pelo cliente para satisfazer as necessidades de missão. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-cm-5-1"></a>NIST 800-53 controlo CM-5 (1)

#### <a name="access-restrictions-for-change--automated-access-enforcement--auditing"></a>Restrições de alteração de acesso | A atribuição de imposição de acesso / auditoria

**(1) de 5 CM** o sistema de informações impõe restrições de acesso e suporta a auditoria das ações de imposição.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Azure Active Directory conta privilégios são implementados utilizando o controlo de acesso baseado em funções ao atribuir utilizadores a funções fornecer strict controlo sobre quais os utilizadores podem ver e controlo implementado recursos. Privilégios de conta do Active Directory são implementados utilizando o controlo de acesso baseado em funções através da atribuição de utilizadores para grupos de segurança. Estes grupos de segurança controlam as ações que os utilizadores podem executar no que respeita à configuração do sistema operativo. Todos os acessos e as tentativas de acesso são auditadas. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-cm-5-2"></a>NIST 800-53 controlo CM-5 (2)

#### <a name="access-restrictions-for-change--review-system-changes"></a>Restrições de alteração de acesso | Rever as alterações de sistema

**CM-5 (2)** organização revê as alterações de sistema de informações [atribuição: frequência definida de organização] e [atribuição: circunstâncias definido de organização] para determinar se ocorreram alterações não autorizadas.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por rever as alterações aos recursos implementados de cliente para determinar se ocorreram alterações não autorizadas. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-cm-5-3"></a>NIST 800-53 controlo CM-5 (3)

#### <a name="access-restrictions-for-change--signed-components"></a>Restrições de alteração de acesso | Componentes assinados

**CM-5 (3)** o sistema de informações impede a instalação do [atribuição: componentes de software e firmware definido de organização] sem verificação que o componente foi digitalmente assinado com um certificado que é reconhecido e aprovadas pela organização.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Máquinas virtuais implementadas por esta Blueprint Azure implementa o AppLocker do Windows para especificar quais os utilizadores podem instalar e/ou executar aplicações específicas. Adicionais, todas as janelas de atualizações do sistema operativo são singed digitalmente. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-cm-5-5a"></a>Controlar o NIST 800-53 .a (5) do CM 5

#### <a name="access-restrictions-for-change--limit-production--operational-privileges"></a>Restrições de alteração de acesso | Limitar a produção / operacional privilégios

**CM-5 (5) .a** organização limita privilégios para alterar os componentes de informações do sistema e informações relacionadas com o sistema dentro de um ambiente operacional ou de produção.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por limitar privilégios para efetuar alterações no implementadas no cliente de produção ou ambientes operacionais. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-cm-5-5b"></a>Controlar o NIST 800-53 .b (5) do CM 5

#### <a name="access-restrictions-for-change--limit-production--operational-privileges"></a>Restrições de alteração de acesso | Limitar a produção / operacional privilégios

**CM-5 (5) .b** organização revê e reevaluates privilégios [atribuição: frequência definida de organização].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável pela revisão e reevaluating privilégios definidos no .a CM-05 (05). |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-cm-6a"></a>Controlo NIST 800-53 CM-6.a

#### <a name="configuration-settings"></a>Definições de configuração

**CM 6.a** organização estabelece e documentos definições de configuração para produtos de tecnologia de informação utilizadas dentro do sistema de informações utilizando [atribuição: listas de verificação de configuração de segurança definido para organização] que refletem o modo mais restritivo consistente com a requisitos operacionais.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint de Azure inclui uma linha de base de configuração de estado pretendido (DSC) para cada máquina virtual implementada. Estes scripts do PowerShell declarativas definem e configurar os recursos aos quais são aplicadas. A linha de base DSC incluído para recursos implementados por esta solução pode ser expandida pelo cliente para satisfazer as necessidades de missão. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-cm-6b"></a>NIST 800-53 controlo CM-6. b

#### <a name="configuration-settings"></a>Definições de configuração

**CM-6. b** organização implementa as definições de configuração.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint de Azure inclui uma linha de base de configuração de estado pretendido (DSC) para cada máquina virtual implementada. As linhas de base é aplicada automaticamente a máquinas virtuais durante a implementação através da extensão da máquina virtual de script personalizado. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-cm-6c"></a>NIST 800-53 controlo CM-6. c

#### <a name="configuration-settings"></a>Definições de configuração

**CM-6. c** organização identifica, documentos e aprova qualquer desvios em relação às definições de configuração estabelecida para [atribuição: os componentes do sistema de informações definidas pela organização] com base no [atribuição: definido de organização requisitos operacionais de].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por identificar, documentar e aprovar os desvios em relação às definições de configuração estabelecida para recursos implementados no cliente. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-cm-6d"></a>Controlo NIST 800-53 CM-6.d

#### <a name="configuration-settings"></a>Definições de configuração

**CM 6.d** os monitores de organização e controlos de alterações das definições de configuração em conformidade com políticas organizacionais e procedimentos.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure implementa o DSC de automatização. Automation DSC Alinha a configurações de máquinas com uma configuração específica definida por organização. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-cm-6-1"></a>NIST 800-53 controlo CM-6 (1)

#### <a name="configuration-settings--automated-central-management--application--verification"></a>Definições de configuração | A atribuição de gestão Central / aplicação / verificação

**CM-6 (1)** organização emprega automatizados mecanismos para gerir centralmente, aplicam-se e verifique as definições de configuração do [atribuição: os componentes do sistema de informações definidas pela organização].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure implementa o Automation DSC do Azure. Automation DSC Alinha a configurações de máquinas com uma configuração específica definida de organização e monitoriza continuamente as alterações. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-cm-6-2"></a>NIST 800-53 controlo CM-6 (2)

#### <a name="configuration-settings--respond-to-unauthorized-changes"></a>Definições de configuração | Responder a alterações não autorizadas

**CM-6 (2)** organização emprega [atribuição: as proteções de segurança definido para organização] para responder a alterações não autorizadas a [atribuição: definições de configuração definida pelo organização].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure implementa o Automation DSC do Azure. Parte do Azure Operations Management Suite (OMS), o DSC de automatização pode ser configurado para gerar um alerta ou para resolver erros de configuração quando detetado. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-cm-7a"></a>Controlo NIST 800-53 CM-7.a

#### <a name="least-functionality"></a>Pelo menos, a funcionalidade

**CM 7.a** a organização configurar o sistema de informações para fornecer apenas as capacidades essenciais.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Os recursos implementados por este Blueprint do Azure estão configurados para fornecer a funcionalidade, pelo menos, para o respetivo objetivo pretendido. Uma linha de base de configuração de estado pretendido (DSC) está incluída para cada máquina virtual implementada. Estes scripts do PowerShell declarativas definem e configurar os recursos aos quais são aplicadas. A linha de base DSC incluído para recursos implementados por esta solução pode ser expandida pelo cliente para limitar ainda mais a funcionalidade para satisfazer as necessidades de missão. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-cm-7b"></a>Controlo NIST 800-53 CM-7

#### <a name="least-functionality"></a>Pelo menos, a funcionalidade

**7 CM** organização proíbe ou restringe a utilização das seguintes funções, portas, protocolos, e/ou serviços: [atribuição: organização proibidas ou restritas as funções definidas pelo, portas, protocolos e/ou serviços].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure implementa grupos de segurança de rede e Gateway de aplicação do Azure para restringir a utilização de portas e protocolos para apenas os que são necessárias. Gateway de aplicação, grupos de segurança de rede e linhas de base de DSC para máquinas virtuais podem ser mais configuradas pelo cliente para restringir a utilização de serviços para fornecer apenas a funcionalidade pretendida, protocolos, portas e funções. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-cm-7-1a"></a>(1) .a do NIST 800-53 controlo 7 de CM

#### <a name="least-functionality--periodic-review"></a>Funcionalidade de, pelo menos | Revisão periódica

**CM-7 (1) .a** organização analisa o sistema de informações [atribuição: frequência definida de organização] para identificar os serviços, protocolos, portas e desnecessárias e/ou nonsecure funções.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável pela revisão recursos implementados de cliente (para incluir aplicações, sistemas operativos, bases de dados e software) para identificar funções desnecessárias e/ou não seguros, portas, protocolos e serviços. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-cm-7-1b"></a>(1) .b do NIST 800-53 controlo 7 de CM

#### <a name="least-functionality--periodic-review"></a>Funcionalidade de, pelo menos | Revisão periódica

**CM-7 (1) .b** desativa a organização [atribuição: serviços no sistema informações consideradas ser desnecessários e/ou nonsecure, protocolos, portas e funções definidas pelo organização].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por desativar a funções, portas, protocolos e serviços que foram considerados ser desnecessários ou não seguros. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-cm-7-2"></a>NIST 800-53 controlo CM-7 (2)

#### <a name="least-functionality--prevent-program-execution"></a>Funcionalidade de, pelo menos | Impedir a execução do programa

**CM-7 (2)** em conformidade com a execução do programa de impede que o sistema de informações [seleção (um ou mais): [atribuição: políticas definidas pelo organização sobre software programa e restrições de utilização]; as regras de autorização os termos de licenciamento e condições de utilização do programa de software].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por impedir a execução do programa em conformidade com políticas de utilização do programa de software definida pelo cliente. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-cm-7-5a"></a>Controlar o NIST 800-53 .a (5) 7 de CM

#### <a name="least-functionality--authorized-software--whitelisting"></a>Funcionalidade de, pelo menos | Autorizado Software / adicionar à lista branca

**CM-7 (5) .a** identifica a organização [atribuição: programas de software definida pelo organização autorizado a executar no sistema informações].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por identificar os programas de software autorizada. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-cm-7-5b"></a>Controlar o NIST 800-53 .b (5) 7 de CM

#### <a name="least-functionality--authorized-software--whitelisting"></a>Funcionalidade de, pelo menos | Autorizado Software / adicionar à lista branca

**CM-7 (5) .b** a organização utiliza uma política de negar-all, permitir pelos exceções para permitir a execução de programas de software autorizada no sistema de informações.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável pela utilização de uma política de negar-all, permitir pelos exceção para permitir a execução de programas de software autorizada recursos implementados no cliente. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-cm-7-5c"></a>Controlar o NIST 800-53 .c (5) 7 de CM

#### <a name="least-functionality--authorized-software--whitelisting"></a>Funcionalidade de, pelo menos | Autorizado Software / adicionar à lista branca

**CM-7 (5) .c** organização revê e atualiza a lista de programas de software autorizada [atribuição: frequência definida de organização].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por rever e atualizar a lista de programas de software autorizada. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-cm-8a"></a>Controlo NIST 800-53 CM-8.a

#### <a name="information-system-component-inventory"></a>Inventário de componente do sistema de informações

**CM 8.a** organização desenvolvidas pela organização e documentos um inventário dos componentes do sistema de informações que reflete o atual sistema de informações com precisão; inclui todos os componentes dentro do limite do sistema de informações de autorização; se encontra em o nível de granularidade considerar necessário para controlar e relatórios; e inclui [atribuição: informações definidas pela organização considerar necessárias para alcançar accountability de componente do sistema de informações eficazes].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure implementa todos os recursos para um grupo de recursos do Azure Resource Manager. O Azure Resource Manager fornece uma lista atualizada sempre de recursos implementados e podem ser personalizado para recursos de etiqueta e o grupo de gestão de inventário. Recursos implementados por esta solução recebem uma etiqueta de recurso específico que pode ser associada ao limite de sistema. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-cm-8b"></a>Controlo NIST 800-53 CM-8.b

#### <a name="information-system-component-inventory"></a>Inventário de componente do sistema de informações

**CM 8.b** organização revê e atualiza o inventário de componente do sistema de informações [atribuição: frequência definida de organização].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure implementa todos os recursos para um grupo de recursos do Azure Resource Manager. O Azure Resource Manager fornece uma lista atualizada sempre de recursos implementados disponíveis para revisão no portal do Azure. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-cm-8-1"></a>NIST 800-53 controlo CM-8 (1)

#### <a name="information-system-component-inventory--updates-during-installations--removals"></a>Inventário de componente do sistema de informações | As atualizações durante as instalações / remoções

**(1) de 8 CM** organização atualiza o inventário dos componentes do sistema de informações como parte integrante das instalações de componente, remoção e atualizações do sistema de informações.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure implementa todos os recursos para um grupo de recursos do Azure Resource Manager. O painel de recursos no portal do Azure apresenta uma lista de todos os recursos implementados, fornecendo um inventário sempre atualizado, como recursos são implementados e removidos. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-cm-8-2"></a>NIST 800-53 controlo CM-8 (2)

#### <a name="information-system-component-inventory--automated-maintenance"></a>Inventário de componente do sistema de informações | Manutenção automática

**(2) de 8 CM** organização emprega mecanismos automatizados para o ajudar a manter um inventário atualizado, completo, exata e prontamente disponível dos componentes do sistema de informações.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure implementa todos os recursos para um grupo de recursos do Azure Resource Manager. O painel de recursos no portal do Azure apresenta uma lista de todos os recursos implementados, fornecendo um inventário sempre atualizado, como recursos são implementados e removidos. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-cm-8-3a"></a>Controlar o NIST 800-53 .a (3) do CM-8

#### <a name="information-system-component-inventory--automated-unauthorized-component-detection"></a>Inventário de componente do sistema de informações | A atribuição de deteção de componentes não autorizado

**.A CM-8 (3)** organização emprega mecanismos automatizados [atribuição: frequência definida de organização] para detetar a presença dos componentes de hardware, software e firmware não autorizadas no sistema de informações.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por empregar automatizados mecanismos para detetar a presença de software não autorizado no recursos implementados no cliente. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-cm-8-3b"></a>Controlar o NIST 800-53 .b (3) do CM-8

#### <a name="information-system-component-inventory--automated-unauthorized-component-detection"></a>Inventário de componente do sistema de informações | A atribuição de deteção de componentes não autorizado

**CM-8 (3) .b** organização efetua as seguintes ações quando são detetados componentes não autorizados: [seleção (um ou mais): desativa o acesso à rede por esses componentes; isola os componentes; notifica [atribuição: definido de organização técnico ou funções]].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por efetuar a ação quando é detetado software não autorizado. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-cm-8-4"></a>NIST 800-53 controlo CM-8 (4)

#### <a name="information-system-component-inventory--accountability-information"></a>Inventário de componente do sistema de informações | Informações de Accountability

**CM-8 (4)** organização inclui as informações de inventário de componente de sistema de informações, um meio para identificar por [seleção (um ou mais): nome; a posição; a função], indivíduos responsáveis/responsável para administrar esses componentes .

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure implementa todos os recursos para um grupo de recursos do Azure Resource Manager. Os sinalizadores de recursos do Azure são chave / valor pares que podem ser utilizadas para categorizar os recursos para fins de accountability e/ou de gestão. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-cm-8-5"></a>NIST 800-53 controlo CM-8 (5)

#### <a name="information-system-component-inventory--no-duplicate-accounting-of-components"></a>Inventário de componente do sistema de informações | Nenhuma contabilidade duplicada dos componentes

**CM-8 (5)** organização verifica se todos os componentes dentro do limite do sistema de informações de autorização não são duplicados em outros inventários de componente do sistema de informações.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure implementa todos os recursos para um grupo de recursos do Azure Resource Manager. O Azure Resource Manager fornece uma lista atualizada sempre de recursos implementados. Recursos implementados por esta solução recebem uma etiqueta de recurso específico que pode ser associada ao limite de sistema. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-cm-9a"></a>Controlo NIST 800-53 CM-9.a

#### <a name="configuration-management-plan"></a>Plano de gestão de configuração

**CM 9.a** organização desenvolvidas pela organização, documentos e implementa um esquema de gestão de configuração para o sistema de informações que endereços funções, responsabilidades e os processos de gestão de configuração e procedimentos.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por desenvolver, documentar e implementar um plano de gestão de configuração para os recursos implementados no cliente. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-cm-9b"></a>Controlo NIST 800-53 CM-9.b

#### <a name="configuration-management-plan"></a>Plano de gestão de configuração

**CM 9.b** organização desenvolvidas pela organização, documentos e implementa um esquema de gestão de configuração para o sistema de informações que estabelece um processo para ciclo de identificação de itens de configuração ao longo da vida de desenvolvimento do sistema e Gerir a configuração dos itens de configuração.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por desenvolver, documentar e implementar um plano de gestão de configuração para os recursos implementados no cliente. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-cm-9c"></a>Controlo NIST 800-53 CM-9.c

#### <a name="configuration-management-plan"></a>Plano de gestão de configuração

**CM 9.c** organização desenvolvidas pela organização, documentos e implementa um esquema de gestão de configuração para o sistema de informações que define a configuração de itens para o sistema de informações e coloca os itens de configuração em configuração gestão.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por desenvolver, documentar e implementar um plano de gestão de configuração para os recursos implementados no cliente. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-cm-9d"></a>Controlo NIST 800-53 CM-9.d

#### <a name="configuration-management-plan"></a>Plano de gestão de configuração

**CM 9.d** organização desenvolvidas pela organização, documentos e implementa um esquema de gestão de configuração para o sistema de informações que protege o plano de gestão de configuração de divulgação não autorizada e modificação.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por desenvolver, documentar e implementar um plano de gestão de configuração para os recursos implementados no cliente. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-cm-10a"></a>Controlo NIST 800-53 CM-10.a

#### <a name="software-usage-restrictions"></a>Restrições de utilização de software

**CM 10.a** a organização utiliza o software e a documentação associada em conformidade com contratos de contrato e leis de direitos de autor.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Licenças do Windows e o SQL Server estão incluídas para os recursos implementados por este Blueprint do Azure. Esta é uma funcionalidade incorporada do Azure. As organizações com contratos de licença de software existente poderão considerar a implementação de modelos de licença alternativo. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-cm-10b"></a>Controlo NIST 800-53 CM-10.b

#### <a name="software-usage-restrictions"></a>Restrições de utilização de software

**CM 10.b** organização controla a utilização de software e a documentação associada protegidos pelo licenças quantidade para controlar o copiar e a distribuição.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Licenças do Windows e o SQL Server estão incluídas para os recursos implementados por este Blueprint do Azure. O utilizador não é necessário para controlar a utilização de licenças em separado. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-cm-10c"></a>Controlo NIST 800-53 CM-10.c

#### <a name="software-usage-restrictions"></a>Restrições de utilização de software

**CM 10.c** organização controla e documentos a utilização de tecnologia para se certificar de que esta capacidade não é utilizada para a distribuição não autorizada, apresentar, desempenho ou reprodução do obra de partilha de ficheiros de ponto a ponto.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Não há nenhuma capacidade implementada por esta Blueprint Azure de partilha de ficheiros de ponto a ponto. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-cm-10-1"></a>NIST 800-53 controlo CM 10 (1)

#### <a name="software-usage-restrictions--open-source-software"></a>As restrições de utilização de software | Software Open Source

**CM-10 (1)** organização estabelece as seguintes restrições sobre a utilização de software open source para: [atribuição: restrições definidas pelo organização].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Política de gestão de configuração de nível empresarial do cliente pode endereços restrições sobre a utilização de software código aberto. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-cm-11a"></a>Controlo NIST 800-53 CM-11.a

#### <a name="user-installed-software"></a>Software instalado por utilizador

**CM 11.a** organização estabelece [atribuição: as políticas definidas pelo organização] que rege a instalação de software pelos utilizadores.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável pelo estabelecimento de uma política que rege a instalação do software de cliente implementadas recursos pelos utilizadores. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-cm-11b"></a>Controlo NIST 800-53 CM-11.b

#### <a name="user-installed-software"></a>Software instalado por utilizador

**CM 11.b** organização impõe as políticas de instalação de software através de [atribuição: os métodos definidos pelo organização].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável pela imposição de políticas de instalação de software. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-cm-11c"></a>Controlo NIST 800-53 CM-11.c

#### <a name="user-installed-software"></a>Software instalado por utilizador

**CM 11.c** organização monitoriza a conformidade com a política em [atribuição: frequência definida de organização].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável pela monitorização a compatibilidade dos recursos de implementação de cliente com as políticas identificadas no CM 11.a. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-cm-11-1"></a>NIST 800-53 controlo CM-11 (1)

#### <a name="user-installed-software--alerts-for-unauthorized-installations"></a>Utilizador-Software instalado que | Alertas de instalações não autorizadas

**CM-11 (1)** os alertas de sistema de informações [atribuição: técnico definido pela organização ou funções] quando a instalação do software não autorizada é detetada.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por fornecer alertas quando a instalação do software não autorizada é detetada. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |

