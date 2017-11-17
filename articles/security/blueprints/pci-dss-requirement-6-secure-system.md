---
title: Azure Blueprint de processamento de pagamento - requisitos de sistema segura
description: Requisito de PCI DSS 6
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 79889fdb-52d2-42db-9117-6e2f33d501e0
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 24c8d90d3fec27258165472e99ba3d36ffcba733
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="secure-system-requirements-for-pci-dss-compliant-environments"></a>Requisitos de sistema segura para ambientes em conformidade de PCI DSS 
## <a name="pci-dss-requirement-6"></a>Requisito de PCI DSS 6

**Desenvolver e a manter segurados sistemas e aplicações**

> [!NOTE]
> Estes requisitos são definidos pelo [Council de normas de segurança da indústria de cartão de pagamento (PCI)](https://www.pcisecuritystandards.org/pci_security/) como parte do [PCI dados segurança Standard (DSS) versão 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Consulte o PCI DSS para obter informações sobre testes de procedimentos e as diretrizes para cada requisito.

Indivíduos unscrupulous utilizam vulnerabilidades de segurança para acesso privilegiado ao sistemas. Muitas destas vulnerabilidades são corrigidas pelo patches de segurança de fornecedor fornecidos pelo, que podem ser instaladas por entidades que gerem os sistemas. Todos os sistemas têm de ter todos os patches de software apropriada para proteger contra o comprometimento de dados de cardholder por que pessoas e software malicioso e ser explorada.

> [!NOTE]
> Patches de software adequados são os patches que foram avaliadas e testados suficientemente para determinar que os patches não estão em conflito com as configurações de segurança existente. Para aplicações desenvolvidas internamente, várias vulnerabilidades podem ser evitada, utilizando os processos de desenvolvimento de sistema padrão e segura de técnicas de programação.

## <a name="pci-dss-requirement-61"></a>Requisito de PCI DSS 6.1

**6.1** estabelecer um processo para identificar vulnerabilidades de segurança, utilizando seguras fora origens de informações de vulnerabilidade de segurança e atribuir um risco de classificação (por exemplo, como "Alta", "média," ou "baixa") para segurança recentemente detetada vulnerabilidades.

> [!NOTE]
> As classificações de risco devem basear-se as melhores práticas da indústria, bem como tendo em conta os potencial impacto. Por exemplo, os critérios de classificação vulnerabilidades podem incluir tendo em conta a pontuação base CVSS e/ou a classificação por fabricante e/ou tipo dos sistemas afetados. 
> 
> Métodos para avaliação de vulnerabilidades e atribuir classificações de risco irão variar com base no ambiente e a estratégia de avaliação de risco de uma organização. As classificações de risco devem, no mínimo, identifique todas as vulnerabilidades consideradas "risco elevado" para o ambiente. Para além de classificação de risco, vulnerabilidades podem ser consideradas "críticas" se que representam uma ameaça imminent para o ambiente, os sistemas críticos impacto, e/ou resultariam num potencial comprometimento se não estiver resolvido. Exemplos de sistemas críticos podem incluir sistemas de segurança, destinado ao público dispositivos e sistemas, bases de dados e outros sistemas que armazenam, processo, ou transmitir dados cardholder.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Procedimentos tenham sido estabelecidos e implementados para procurar as vulnerabilidades nos anfitriões hipervisores num limite âmbito. Análise de vulnerabilidade é executada em sistemas operativos de servidor, bases de dados e dispositivos de rede com a ferramentas de análise de vulnerabilidade adequada. Análises de vulnerabilidade são efetuadas numa base trimestral no mínimo. Microsoft Azure contratos com assessors independentes para efetuar testes penetração do limite do Microsoft Azure. Equipa Red exercícios regularmente também são executados e resultados utilizada para efetuar melhoramentos de segurança. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A Contoso Webstore reduz o risco de vulnerabilidades de segurança utilizando um Gateway de aplicação com WAF e o ruleset OWASP ativada. Para obter mais informações, consulte [orientações de PCI - mitigação de riscos de segurança vulnerabilidades](payment-processing-blueprint.md#application-gateway).|



## <a name="pci-dss-requirement-62"></a>Requisito de PCI DSS 6.2

**6.2** Certifique-se de que todos os componentes do sistema e o software estão protegidos de vulnerabilidades conhecidas através da instalação de patches de segurança aplicável-fornecida pelo fabricante. Instalação de patches de segurança críticas dentro de um mês de versão.

> [!NOTE]
> Devem ser identificados patches de segurança críticas, de acordo com o processo definido na 6.1 requisito de classificação de risco.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Microsoft Azure é responsável por assegurar que todos os dispositivos de rede e o software de SO de hipervisor se encontra protegido contra vulnerabilidades conhecidas através da instalação de patches de segurança aplicável-fornecida pelo fabricante. A menos que um cliente solicita não utilizar o serviço, existe um processo de gestão de correção para garantir que as vulnerabilidades de nível do sistema operativo são impedidas e remediadas de forma atempada. Servidores de produção são analisados para validar a compatibilidade de patch mensalmente. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | O Contoso Webstore é uma solução de serviço de PaaS. O Azure oferece manutenção de todos os patches de serviço.|



## <a name="pci-dss-requirement-63"></a>Requisito de PCI DSS 6.3

**6.3** desenvolver aplicações de software internos e externos (incluindo baseada na web acesso administrativo às aplicações) em segurança, da seguinte forma:
- Em conformidade com a norma PCI DSS (por exemplo, autenticação segura e registo)
- Com base nas normas da indústria e/ou as melhores práticas
- Incorporando a segurança de informações ao longo do ciclo de vida de desenvolvimento de software 

> [!NOTE]
> Isto aplica-se a todos os software desenvolvido internamente, bem como bespoke ou personalizadas de software desenvolvidas por terceiros.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Aplicações do Microsoft Azure e os pontos finais são desenvolvidos de acordo com a metodologia de Microsoft Security Development Lifecycle (SDL) que está de acordo com requisitos de DSS. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A Contoso Webstore foi concebida para seguir as melhores práticas industriais para proteger CHD. Documentação de orientação de implementação fornece os detalhes sobre o meassures de segurança e o registo está ativado.|



### <a name="pci-dss-requirement-631"></a>Requisito de PCI DSS 6.3.1

**6.3.1** remover desenvolvimento, teste e/ou contas de aplicação personalizada, IDs de utilizador e palavras-passe antes de ficam ativas ou são disponibilizadas aos clientes de aplicações.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Revisão de segurança Final (FSR) é efetuada em versões principais antes da implementação de produção por um designado segurança do Advisor fora da equipa de desenvolvimento do Azure para garantir que são lançadas apenas aplicações prontas para produção. Como parte desta revisão final-lo é certificar-se de que todas as contas de teste e dados de teste foram removidos. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | O Contoso Webstore fornece um serviço de teste que está registado e isolado. Cada uma das camadas de rede tem um grupo de segurança de rede dedicado [NSG]. Para obter mais informações, consulte [orientações de PCI - grupos de segurança de rede](payment-processing-blueprint.md#network-security-groups).|



### <a name="pci-dss-requirement-632"></a>Requisito de PCI DSS 6.3.2

**6.3.2** antes de código personalizado da revisão para disponibilizar para produção ou de clientes para identificar quaisquer potencial codificação vulnerabilidade (através de processos manuais ou automáticos) para incluir, pelo menos, o seguinte:
- Alterações de código são revistas pessoas que não seja o autor do código de origem e, por indivíduos knowledgeable sobre técnicas de revisão de código e seguros de práticas de codificação.
- Revisões de código Certifique-se for desenvolvido código de acordo com as diretrizes de codificação seguras
- As correções apropriadas estão implementados antes da versão
- Resultados da revisão de código são revistos e aprovados pelo management antes da versão 

> [!NOTE]
> Este requisito para código revisões aplica-se a todos os código personalizado (interno e destinado ao público), como parte do ciclo de vida de desenvolvimento do sistema. 
>
> Revisões de código podem ser efetuadas por knowledgeable técnico interno ou de terceiros. As aplicações web de destinado ao público são também sujeita controlos adicionais, para resolver em curso ameaças e vulnerabilidades após a implementação, conforme definido no PCI DSS requisito 6.6.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Aplicações do Microsoft Azure e os pontos finais são desenvolvidos de acordo com a metodologia de Microsoft Security Development Lifecycle (SDL). |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | O Contoso Webstore fornece um serviço de teste que está registado e isolado. Cada uma das camadas de rede tem um grupo de segurança de rede dedicado [NSG]. Para obter mais informações, consulte [orientações de PCI - grupos de segurança de rede](payment-processing-blueprint.md#network-security-groups).|



## <a name="pci-dss-requirement-64"></a>Requisito de PCI DSS 6.4

**6.4** siga Alterar controlo processos e procedimentos para todas as alterações para componentes do sistema. Os processos tem de incluir o seguinte (consulte os requisitos de 6.4.1 para 6.4.6).

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Microsoft segue a documentação de orientação do NIST sobre considerações de segurança no desenvolvimento de software em segurança dessa informação devem ser integradas no SDLC de inception de sistema. Permite a integração expedição das práticas de segurança no Microsoft SDL:<ul><li>Identificação antecipada e a respetiva mitigação de vulnerabilidades de segurança e as configurações incorretas</li><li>Deteção de software potencial codificação desafios causados por controlos de segurança necessárias</li><li>Identificação dos serviços de segurança partilhado e reutilização de segurança melhores práticas ferramentas que melhora a postura de segurança através de técnicas e métodos comprovados</li><li>Imposição de programa de gestão de risco já abrangente da Microsoft</li></ul>Microsoft Azure estabelecida alteração e libertar a processos de gestão para controlar a implementação de alterações principais, incluindo:<ul><li>A identificação e a documentação da alteração planeada</li><li>Identificação dos cenários, as prioridades e objetivos empresariais durante o planeamento de produto</li><li>Especificação de estrutura de funcionalidade/componente</li><li>Revisão de preparação operacional numa predefinido critérios /-lista de verificação para avaliar o risco/impacto geral</li><li>Gestão testar, autorização e de alterações com base nos critérios de entrada/saída DEV (desenvolvimento), INT (integração teste), fase (pré-produção) e PROD (produção) ambientes conforme apropriado. Os clientes são responsáveis pelas suas próprias aplicações alojadas no Microsoft Azure.</li></ul> |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Demonstração de Contoso Webstore fornece um serviço de teste que está registado e isolado. <br /><br />Cada uma das camadas de rede tem um grupo de segurança de rede dedicado [NSG]. Para obter mais informações, consulte [orientações de PCI - grupos de segurança de rede](payment-processing-blueprint.md#network-security-groups).<br /><br />As alterações são registadas com o Operations Management Suite e Runbooks são utilizados para recolher registos. [Operations Management Suite (OMS)](/azure/operations-management-suite/) fornece registos um vasto conjunto de alterações. As alterações podem ser revistas e podem ser verificadas em termos de exatidão. Para obter instruções mais específicas, consulte [orientações de PCI - Operations Management Suite](payment-processing-blueprint.md#logging-and-auditing).|



### <a name="pci-dss-requirement-641"></a>Requisito de PCI DSS 6.4.1

**6.4.1** separar ambientes de desenvolvimento/teste de ambientes de produção e impor a separação com controlos de acesso.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Consulte a secção "Microsoft Azure" para [requisito 6.4](#pci-dss-requirement-6-4). |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | O Contoso Webstore fornece um serviço de teste que está registado e isolado. Cada uma das camadas de rede tem um grupo de segurança de rede dedicado [NSG]. Para obter mais informações, consulte [orientações de PCI - grupos de segurança de rede](payment-processing-blueprint.md#network-security-groups).|



### <a name="pci-dss-requirement-642"></a>Requisito de PCI DSS 6.4.2

**6.4.2** separação de deveres entre ambientes de desenvolvimento/teste e produção

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Consulte a secção "Microsoft Azure" para [requisito 6.4](#pci-dss-requirement-6-4). |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | O Contoso Webstore fornece um serviço de teste que está registado e isolado. Cada uma das camadas de rede tem um grupo de segurança de rede dedicado [NSG]. Para obter mais informações, consulte [orientações de PCI - grupos de segurança de rede](payment-processing-blueprint.md#network-security-groups).|



### <a name="pci-dss-requirement-643"></a>Requisito de PCI DSS 6.4.3

**6.4.3** dados de produção (em direto PANs) não são utilizados para programação ou testes.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Consulte a secção "Microsoft Azure" para [requisito 6.4](#pci-dss-requirement-6-4). |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A Contoso Webstore tem não conta principal número (PAN) dados dinâmicos.|



### <a name="pci-dss-requirement-644"></a>Requisito de PCI DSS 6.4.4

**6.4.4** remoção de dados de teste e contas de componentes do sistema antes do sistema fica ativo ou entra em produção.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Consulte a secção "Microsoft Azure" para [requisito 6.4](#pci-dss-requirement-6-4). |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A Contoso Webstore não tem quaisquer contas de teste.|



### <a name="pci-dss-requirement-645"></a>Requisito de PCI DSS 6.4.5

**6.4.5** procedimentos de controlo de alterações para a implementação de patches de segurança e alterações de software tem de incluir o seguinte:
- **6.5.4.1** documentação de impacto.
- **6.5.4.2** Documented alterar a aprovação por entidades confiadoras autorizadas.
- **6.5.4.3** funcionalidade de teste para verificar que a alteração não afeta negativamente a segurança do sistema.
- **6.5.4.4** procedimentos de back-out.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Consulte a secção "Microsoft Azure" para [requisito 6.4](#pci-dss-requirement-6-4). |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | O Contoso Webstore é uma solução de serviço de PaaS. E o Azure oferece manutenção de todos os patches de serviço.|



### <a name="pci-dss-requirement-646"></a>Requisito de PCI DSS 6.4.6

**6.4.6** após a conclusão de uma alteração significativa, todos os requisitos de PCI DSS relevantes tem de ser implementados em todos os sistemas de novos ou alterados e redes e documentação atualizado conforme aplicável.

> [!NOTE]
> Este requisito é uma melhor prática até 31 de Janeiro de 2018, após o qual torna-se um requisito.


**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Consulte a secção "Microsoft Azure" para [requisito 6.4](#pci-dss-requirement-6-4). |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | O Contoso Webstore é uma solução de serviço de PaaS. E o Azure oferece manutenção de todos os patches de serviço.|



## <a name="pci-dss-requirement-65"></a>Requisito de PCI DSS 6.5

**6.5** endereço comuns de codificação vulnerabilidades em processos de desenvolvimento de software da seguinte forma:
- Preparar os programadores, pelo menos, anual no atualizado técnicas de codificação seguras, incluindo como evitar vulnerabilidades de codificação comuns.
- Desenvolva aplicações com base em diretrizes de codificação seguras.

> [!NOTE]
> As vulnerabilidades listadas em 6.5.1 através de 6.5.10 eram atuais com as melhores práticas da indústria quando esta versão do PCI DSS foi publicado. No entanto, conforme as melhores práticas industriais para a gestão de vulnerabilidade são atualizadas (por exemplo, o guia de OWASP SANS CWE superior 25, certificado Secure codificação, etc.), as melhores práticas atuais tem de ser utilizadas para estes requisitos. 
> 
> [!NOTE]
> Requisitos 6.5.1 através de 6.5.6, abaixo, são aplicáveis a todas as aplicações (internas ou externas). Requisitos 6.5.7 através de 6.5.10, abaixo, aplicam-se a aplicações web e de interfaces de aplicação (internas ou externas). 

- **6.5.1** falhas de injeção, particularmente injeção de SQL. Considere também a Injeção de comando do SO, LDAP e XPath falhas de injeção, bem como outras falhas de inserção.
- **6.5.2** memória intermédia interna está cheio
- **6.5.3** inseguras armazenamento criptográfico
- **6.5.4** inseguras comunicações
- **6.5.5** processamento de erros incorrecto
- **6.5.6** todas as vulnerabilidades de "alto risco" identificadas no processo de identificação de vulnerabilidade (conforme definido no PCI DSS requisito 6.1)
- **6.5.7** (XSS) de scripts entre sites
- **6.5.8** controlo de acesso incorrecto (por exemplo, referências inseguras direta objeto, Falha ao restringir acesso, transversal de diretório e falhas para restringir o acesso de utilizador a funções de URL)
- **6.5.9** falsificação de pedidos entre sites (CSRF)
- **6.5.10** quebrada sessão e autenticação de gestão

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Consulte a secção "Microsoft Azure" para [requisito 6.4](#pci-dss-requirement-6-4). |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Demonstração de contoso Webstore fornece orientações para o modelo de desenvolvimento, um DFD e ameaças segura para ilustrar práticas de desenvolvimento seguro.|



## <a name="pci-dss-requirement-66"></a>Requisito de PCI DSS 6.6

**6.6** para aplicações web de destinado ao público, endereço novas ameaças e vulnerabilidades de forma contínua e certifique-se de que estas aplicações estão protegidas contra ataques conhecidos por qualquer um dos seguintes métodos:

- Rever aplicações web de destinado ao público através de ferramentas de avaliação de segurança de vulnerabilidade de aplicação manual ou automática ou métodos, pelo menos anual e depois das alterações 

   > [!NOTE]
   > Esta avaliação não é igual as análises de vulnerabilidade efetuadas para 11.2 requisito. 

- Instalar uma solução automatizada de técnica que Deteta e impede ataques baseados na web (por exemplo, uma firewall de aplicação web) à frente de aplicações web de destinado ao público, para verificar continuamente todo o tráfego.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Microsoft Azure os testes de aplicações web de destinado ao público como parte do respetivo processo do SDL antes de aplicações são implementadas para o ambiente de produção. Além disso, a Microsoft analisa todas as aplicações de web de destinado ao público na produção regularmente para detetar qualquer vulnerabilidades possíveis. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A solução de referência reduz o risco de vulnerabilidades de segurança utilizando um Gateway de aplicação com WAF e o ruleset OWASP ativada. Para obter mais informações, consulte [orientações de PCI - mitigação de riscos de segurança vulnerabilidades](payment-processing-blueprint.md#application-gateway).|



## <a name="pci-dss-requirement-67"></a>Requisito de PCI DSS 6.7

**6.7** Certifique-se de que as políticas de segurança e de procedimentos operacionais de desenvolvimento e manutenção de proteger os sistemas e aplicações estão documentadas em utilização e conhecidas para todas as partes afetadas.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A solução de referência reduz o risco de vulnerabilidades de segurança utilizando um Gateway de aplicação com WAF e o ruleset OWASP ativada. Para obter mais informações, consulte [orientações de PCI - mitigação de riscos de segurança vulnerabilidades](payment-processing-blueprint.md#application-gateway).|




