---
title: Azure Blueprint de processamento de pagamento - requisitos de acesso
description: Requisito de PCI DSS 7
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: ac3afee9-0471-465d-a115-67488a1635a6
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 5a3c9eac552fb96309cfa791a2e72a7102662e60
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="access-requirements-for-pci-dss-compliant-environments"></a>Requisitos de acesso em conformidade de PCI DSS ambientes 
## <a name="pci-dss-requirement-7"></a>Requisito de PCI DSS 7

**Restringir o acesso a dados cardholder por empresas necessidade de conhecimento**

> [!NOTE]
> Estes requisitos são definidos pelo [Council de normas de segurança da indústria de cartão de pagamento (PCI)](https://www.pcisecuritystandards.org/pci_security/) como parte do [PCI dados segurança Standard (DSS) versão 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Consulte o PCI DSS para obter informações sobre testes de procedimentos e as diretrizes para cada requisito.

Para garantir que os dados críticos só podem ser acedidos por parte do pessoal autorizado, sistemas e processos tem de ser implementados para limitar o acesso com base na necessidade de conhecer e, de acordo com responsabilidades de trabalho.

"Precisa de saber" é quando são concedidos direitos de acesso para apenas o mínimo de dados e os privilégios necessários para efetuar uma tarefa.

## <a name="pci-dss-requirement-71"></a>Requisito de PCI DSS 7.1

**7.1** limite o acesso aos componentes de sistema e dados cardholder apenas essas pessoas cujo trabalho requer esse acesso.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Azure impõe ISMS as políticas existentes relativos à acesso técnico do Azure para componentes do sistema do Azure, a verificação da eficácia de controlo de acesso, fornecendo Just-In-Time acesso administrativo, revogar o acesso quando já não é necessário e, garantindo que os empregados da aceder ao ambiente de plataforma do Azure tem uma empresa necessário. Acesso do Azure para ambientes de cliente é altamente restrita e só é permitido com aprovação do cliente.<br /><br />Os procedimentos foram estabelecidos para restringir o acesso físico ao centro de dados para os empregados autorizados, fornecedores, subcontratados e visitantes. Verificação de segurança e verificação são necessários para técnico necessidade de acesso temporário para o Centro de dados interior das instalações. Registos de acesso físico são revistos cada trimestre por equipas do Azure. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Os clientes são responsáveis por limitar o acesso aos componentes de sistema e dados cardholder apenas essas pessoas cujo trabalho requer esse acesso. Isto inclui a limitação e restringir o acesso ao Portal de gestão do Azure, bem como especificar contas ou funções com permissão para criar, modificar ou eliminar serviços PaaS.|



### <a name="pci-dss-requirement-711"></a>Requisito de PCI DSS 7.1.1

**7.1.1** tem de definir acesso para cada função, incluindo:
- Recursos de dados e os componentes de sistema que cada função necessita de aceder à respetiva função de tarefa
- Nível de privilégio necessário (por exemplo, utilizador, administrador, etc.) para aceder a recursos

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Os clientes são responsáveis por definir e documentar um processo de aprovação de ID de utilizador, definir menos privilégios, restringir o acesso a dados cardholder, utilizando os IDs exclusivos, que fornece a separação de deveres e revogar o acesso de utilizador quando já não é necessário.|



### <a name="pci-dss-requirement-712"></a>Requisito de PCI DSS 7.1.2

**7.1.2** restringir o acesso ao utilizador com privilégios IDs de menos privilégios necessários para desempenhar responsabilidades de trabalho.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Microsoft Azure ADOTOU políticas de segurança empresarial e organizacional aplicável, incluindo uma política de segurança de informações. As políticas tenham sido aprovadas, publicadas e comunicadas para o Windows Azure. A política de segurança de informações do Microsoft Azure requer que o acesso aos recursos do Microsoft Azure ser concedidos com base no justificação de negócio, com autorização do proprietário do ativo e com base na "necessidade-para-saber" e princípios de "menor privilégio". A política também os requisitos de ciclo de vida do gestão de acesso, incluindo acesso aprovisionamento de autorização de acesso, remoção de autenticação de direitos de acesso, os endereços e revê acesso periódico. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A Contoso Webstore cria três contas durante a implementação: administrador, sqladmin e edna (o utilizador predefinido registado para a aplicação web durante a execução de demonstração). Funções de utilizador estão limitadas a deveres com base no cenário de demonstração documentado.|



### <a name="pci-dss-requirement-713"></a>Requisito de PCI DSS 7.1.3

**7.1.3** atribuir acesso com base na classificação de tarefa e a função individuais técnico.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A Contoso Webstore cria três contas durante a implementação: administrador, sqladmin e edna (o utilizador predefinido registado para a aplicação web durante a execução de demonstração). Funções de utilizador estão limitadas a deveres com base no cenário de demonstração documentado.|



### <a name="pci-dss-requirement-714"></a>Requisito de PCI DSS 7.1.4

**7.1.4** exigir a aprovação documentada por entidades confiadoras autorizadas especificando os privilégios necessários.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Os clientes são responsáveis por limitar o acesso aos componentes de sistema e dados cardholder apenas essas pessoas cujo trabalho requer esse acesso. Isto inclui a limitação e restringir o acesso ao Portal de gestão do Azure, bem como especificar contas ou funções com permissão para criar, modificar ou eliminar serviços PaaS.|



## <a name="pci-dss-requirement-72"></a>Requisito de PCI DSS 7.2

**7.2** estabelecer um sistema de controlo de acesso para os componentes de sistemas que restringe o acesso com base num utilizador precisam de saber e está definido como "Negar tudo", a menos que especificamente permitido.
Este sistema de controlo de acesso tem de incluir o seguinte:
- 7.2.1 cobertura de todos os componentes de sistema.
- 7.2.2 atribuição de de privilégios para indivíduos com base na classificação de tarefa e a função.
- 7.2.3 predefinido "Negar todos os" definição.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | O Contoso Webstore utiliza o Azure Active Directory para restringir o acesso apenas a utilizadores designados. Para obter mais informações, consulte [orientações de PCI - gestão de identidades](payment-processing-blueprint.md#identity-management).|



## <a name="pci-dss-requirement-73"></a>Requisito de PCI DSS 7.3

**7.3** Certifique-se de que as políticas de segurança e de procedimentos operacionais para restringir o acesso a dados cardholder estão documentados em utilização e conhecidos para todas as partes afetadas.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A documentação de Contoso Webstore fornece um caso de utilização e uma descrição sobre quem utiliza CHD e como CHD é utilizada.|




