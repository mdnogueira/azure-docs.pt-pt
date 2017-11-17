---
title: Azure Blueprint de processamento de pagamento - requisitos de identidade
description: Requisito de PCI DSS 8
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 1a398601-8c48-4f8e-b3d4-eba94edad61c
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: f77cc3c9926b5316913c70e5f4412383e55c5193
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="identity-requirements-for-pci-dss-compliant-environments"></a>Requisitos de identidades para ambientes em conformidade de PCI DSS 
## <a name="pci-dss-requirement-8"></a>Requisito de PCI DSS 8

**Identificar e autenticar o acesso a componentes do sistema**

> [!NOTE]
> Estes requisitos são definidos pelo [Council de normas de segurança da indústria de cartão de pagamento (PCI)](https://www.pcisecuritystandards.org/pci_security/) como parte do [PCI dados segurança Standard (DSS) versão 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Consulte o PCI DSS para obter informações sobre testes de procedimentos e as diretrizes para cada requisito.

Atribuir uma (ID) de identificação exclusiva a cada pessoa com acesso assegura que cada indivíduo exclusivamente responsável por respetivas ações. Quando tal accountability está no local, as ações efetuadas nos dados críticos e os sistemas são efetuadas pelo e podem ser rastreadas até os utilizadores autorizados e conhecidos e processos.
A eficácia de uma palavra-passe em grande medida é determinada pela estrutura e implementação do sistema de autenticação – particularmente, frequência tentativas de palavra-passe podem ser efetuadas por um atacante e os métodos de segurança para proteger as palavras-passe do utilizador at the point of entrada, durante a transmissão e no armazenamento.

> [!NOTE]
> Estes requisitos são aplicáveis a todas as contas, incluindo contas de ponto de venda, com capacidades administrativas e todas as contas utilizadas para ver ou aceder cardholder dados ou aos sistemas de acesso com dados cardholder. Isto inclui contas utilizadas por fornecedores e de terceiros (por exemplo, para suporte ou manutenção). Estes requisitos não se aplicam a contas utilizadas pelos consumidores (por exemplo, cardholders). No entanto, os requisitos de 8.1.1, 8.2, 8.5, 8.2.3 através de 8.2.5 e 8.1.6 através de 8.1.8 não são se destina a aplicar às contas de utilizador dentro de uma aplicação de ponto de venda de pagamento, que apenas têm acesso a um número de cartão de cada vez para facilitar uma única transação ( tais como contas cashier).
 
## <a name="pci-dss-requirement-81"></a>Requisito de PCI DSS 8.1

**8.1** definir e implementar políticas e procedimentos para assegurar a gestão de identificação de utilizador adequada para não-os utilizadores e administradores em todos os componentes de sistema da seguinte forma.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A Contoso Webstore fornece um caso de utilização e uma descrição para a utilização correta de administradores para a implementação de exemplo.|



### <a name="pci-dss-requirement-811"></a>Requisito de PCI DSS 8.1.1

**8.1.1** atribuir um ID exclusivo de todos os utilizadores antes de permitir-lhes para componentes de sistema de acesso ou dados cardholder.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A Contoso Webstore implementa o Azure Active Directory e controlo de acesso Azure Active Directory Role-Based (RBAC) para garantir que todos os utilizadores têm um ID exclusivo. Para obter mais informações, consulte [orientações de PCI - gestão de identidades](payment-processing-blueprint.md#identity-management).<br /><br />|



### <a name="pci-dss-requirement-812"></a>Requisito de PCI DSS 8.1.2

**8.1.2** controlar adição, eliminação e modificação de IDs de utilizador, as credenciais e outros objetos de identificador.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A Contoso Webstore implementa o Azure Active Directory e controlo de acesso Azure Active Directory Role-Based (RBAC) para garantir que todos os utilizadores têm um ID exclusivo. Para obter mais informações, consulte [orientações de PCI - gestão de identidades](payment-processing-blueprint.md#identity-management).<br /><br />|



### <a name="pci-dss-requirement-813"></a>Requisito de PCI DSS 8.1.3

**8.1.3** imediatamente revogar o acesso para qualquer terminada utilizadores.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | O Contoso Webstore utiliza o Azure Active Directory para gestão de utilizadores. Revogação de utilizadores pode ser feita no Active Directory.|



### <a name="pci-dss-requirement-814"></a>Requisito de PCI DSS 8.1.4

**8.1.4** remover nem desativar as contas de utilizador inativo no prazo de 90 dias.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | O Contoso Webstore utiliza o Azure Active Directory para gestão de utilizadores. O `-enableADDomainPasswordPolicy` opção pode ser definida para garantir que as palavras-passe expirarem dentro de 90 dias.|



### <a name="pci-dss-requirement-815"></a>Requisito de PCI DSS 8.1.5

**8.1.5** Gerir IDs utilizadas por terceiros para aceder, suportar ou manter os componentes de sistema através de acesso remoto da seguinte forma:
- Ativado apenas durante o período de tempo necessários e desativado quando não está em utilização.
- Monitorizados quando está em utilização.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Microsoft Azure ADOTOU políticas de segurança empresarial e organizacional aplicável, incluindo uma política de segurança de informações. As políticas tenham sido aprovadas, publicadas e comunicadas ao Microsoft Azure. A política de segurança de informações requer que o acesso aos recursos do Microsoft Azure para ser concedida com base no justificação de negócio, com autorização do proprietário do ativo e limitada com base no princípios "necessidade-para-saber" e "menor privilégio". Além disso, a política também aborda os requisitos de ciclo de vida do gestão de acesso, incluindo aprovisionamento de acesso, autenticação, autorização de acesso, revisões de acesso de remoção de direitos de acesso e periódica. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Demonstração de Contoso Webstore tiver implementado o Azure Active Directory e o controlo de acesso de Azure Active Directory Role-Based para gerir o acesso de utilizador para a instalação. Para obter mais informações, consulte [orientações de PCI - gestão de identidades](payment-processing-blueprint.md#identity-management).<br /><br />|



### <a name="pci-dss-requirement-816"></a>Requisito de PCI DSS 8.1.6

**8.1.6** repetido de limite de tentativas de acesso, bloqueando a terminar o ID de utilizador após mais de seis tentativas.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Contoso Webstore tiver implementado clara separação de deveres (SOD) para todos os utilizadores de demonstração de. Para obter mais informações, consulte ""Do Azure Active Directory Identity Protection"em [orientações de PCI - gestão de identidades](payment-processing-blueprint.md#identity-management).|



### <a name="pci-dss-requirement-817"></a>Requisito de PCI DSS 8.1.7

**8.1.7** definir a duração do bloqueio para um mínimo de 30 minutos ou até que um administrador ativa a ID de utilizador.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Os clientes serão responsáveis pela criação, imposição e uma política de palavra-passe em conformidade com os requisitos de PCI DSS de monitorização.|



### <a name="pci-dss-requirement-818"></a>Requisito de PCI DSS 8.1.8

**8.1.8** se uma sessão está inativa há mais de 15 minutos, requerem o autenticação novamente para reativar o terminal ou sessão do utilizador.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Os clientes serão responsáveis pela criação, imposição e uma política de palavra-passe em conformidade com os requisitos de PCI DSS de monitorização.|



## <a name="pci-dss-requirement-82"></a>Requisito de PCI DSS 8.2

**8.2** além de atribuir um ID exclusivo, certifique-se gestão de autenticação de utilizador adequada para não-os utilizadores e administradores em todos os componentes de sistema ao empregar, pelo menos, um dos seguintes métodos para autenticar todos os utilizadores:
- Algo que saiba, tal como uma palavra-passe ou o frase de acesso
- Algo tiver, tais como um dispositivo token ou smart card
- Algo estiver, tais como um biométrica

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A implementação de Contoso Webstore para autenticação multifator foi desativada para fornecer a facilidade de utilização para a demonstração. Autenticação multifator pode ser implementada utilizando [Azure multi-factor Authentication](https://azure.microsoft.com/services/multi-factor-authentication/).|



### <a name="pci-dss-requirement-821"></a>Requisito de PCI DSS 8.2.1

**8.2.1** utilização de criptografia segura, compor todas as credenciais de autenticação (tais como palavras-passe/expressões) ilegíveis durante a transmissão e o armazenamento em todos os componentes de sistema.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Microsoft Azure estabelecida procedimentos de gestão de chaves para gerir as chaves criptográficas ao longo do respetivo ciclo de vida (por exemplo, geração, distribuição, revogação). Microsoft Azure utiliza a infraestrutura PKI empresarial da Microsoft. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A Contoso Webstore impõe palavras-passe fortes, documentadas no guia de implementação. Para obter mais informações, consulte [orientações de PCI - encriptação](payment-processing-blueprint.md#encryption-and-secrets-management).<br /><br />|



### <a name="pci-dss-requirement-822"></a>Requisito de PCI DSS 8.2.2

**8.2.2** verificar a identidade do utilizador antes de modificar as credenciais de autenticação — por exemplo, efetuar reposições de palavra-passe, novos tokens de aprovisionamento ou gerar chaves de novo.


**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Microsoft Azure estabelecida procedimentos de gestão de chaves para gerir as chaves criptográficas ao longo do respetivo ciclo de vida (por exemplo, geração, distribuição, revogação). Microsoft Azure utiliza a infraestrutura PKI empresarial da Microsoft. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A Contoso Webstore impõe palavras-passe fortes, documentadas no guia de implementação. Para obter mais informações, consulte [orientações de PCI - encriptação](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-823"></a>Requisito de PCI DSS 8.2.3

**8.2.3**  /frases de acesso de palavras-passe tem de cumprir os seguintes:
- Exigir um comprimento mínimo de, pelo menos, sete carateres.
- Conter carateres alfabéticos tanto numérico.
Em alternativa, as palavras-passe/frases de acesso tem de ter a complexidade e força, pelo menos, equivalente para os parâmetros especificados acima.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A Contoso Webstore impõe palavras-passe fortes, documentadas no guia de implementação.|



### <a name="pci-dss-requirement-824"></a>Requisito de PCI DSS 8.2.4

**8.2.4** alterar as palavras-passe/frases de acesso de utilizador, pelo menos, uma vez a cada 90 dias.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | O Contoso Webstore utiliza o Azure Active Directory para gestão de utilizadores. O `-enableADDomainPasswordPolicy` opção pode ser definida para garantir que as palavras-passe expirarem, pelo menos, uma vez a cada 90 dias.|



### <a name="pci-dss-requirement-825"></a>Requisito de PCI DSS 8.2.5

**8.2.5** não permitir que uma pessoa submeter uma nova palavra-passe/frase de acesso que é o mesmo que qualquer um dos últimos quatro palavras-passe/frases de acesso seja tenha utilizado.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A Contoso Webstore impõe palavras-passe fortes, documentadas no guia de implementação. Para obter mais informações, consulte [orientações de PCI - gestão de identidades](payment-processing-blueprint.md#identity-management).<br /><br />|



### <a name="pci-dss-requirement-826"></a>Requisito de PCI DSS 8.2.6

**8.2.6** definir palavras-passe/frases de acesso para a primeira, utilizar e após a reposição de um valor exclusivo para cada utilizador e alterar imediatamente após a primeira utilização.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A Contoso Webstore impõe palavras-passe fortes, documentadas no guia de implementação. Para obter mais informações, consulte [orientações de PCI - gestão de identidades](payment-processing-blueprint.md#identity-management).<br /><br />|



## <a name="pci-dss-requirement-83"></a>Requisito de PCI DSS 8.3

**8.3** proteger todo individual não consola o acesso administrativo e todo o acesso remoto para o ambiente de dados cardholder (CDE) utilizando a autenticação multifator.

> [!NOTE]
> Autenticação multifator requer que um mínimo de dois métodos de três autenticação (consulte 8.2 requisito para as descrições dos métodos de autenticação) utilizado para autenticação. Utilizar um fator duas vezes (por exemplo, através de duas palavras-passe separadas) não é considerado autenticação multifator.


**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Os administradores do Azure são necessários para utilizar a autenticação multifator para aceder ao efetuar a manutenção e administração para sistemas do Azure e servidores. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A Contoso Webstore cria três contas durante a implementação: administrador, sqladmin e edna (o utilizador predefinido registado para a aplicação web durante a execução de demonstração). Autenticação multifator não está implementada para a demonstração.|



### <a name="pci-dss-requirement-831"></a>Requisito de PCI DSS 8.3.1

**8.3.1** incorporate multi-factor authentication para todos os acessos de consola não para o CDE para empregados com acesso administrativo.

> [!NOTE]
> Este requisito é uma melhor prática até 31 de Janeiro de 2018, após o qual torna-se um requisito.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Os administradores do Azure são necessários para utilizar a autenticação multifator para aceder ao efetuar a manutenção e administração para sistemas do Azure e servidores. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A Contoso Webstore cria três contas durante a implementação: administrador, sqladmin e edna (o utilizador predefinido registado para a aplicação web durante a execução de demonstração). Autenticação multifator não está implementada para a demonstração.|



### <a name="pci-dss-requirement-832"></a>Requisito de PCI DSS 8.3.2

**8.3.2** incorporate multi-factor authentication para todos os acessos de rede remota (utilizador e administrador e incluindo o acesso de terceiros para suporte ou manutenção) provenientes de fora da rede da entidade.


**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Os administradores do Azure são necessários para utilizar a autenticação multifator para aceder ao efetuar a manutenção e administração para sistemas do Azure e servidores. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A Contoso Webstore cria três contas durante a implementação: administrador, sqladmin e edna (o utilizador predefinido registado para a aplicação web durante a execução de demonstração). Autenticação multifator não está implementada para a demonstração.|



## <a name="pci-dss-requirement-84"></a>Requisito de PCI DSS 8.4

**8.4** documento e comunicar as políticas de autenticação e procedimentos para todos os utilizadores, incluindo:
- Documentação de orientação sobre as credenciais de autenticação forte
- Orientações para a forma como os utilizadores devem proteger as respetivas credenciais de autenticação
- Instruções para não reutilizar anteriormente palavras-passe utilizadas
- Pode ficar comprometidas instruções para alterar as palavras-passe, se não houver qualquer suspeita a palavra-passe

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Os clientes são responsáveis por seguintes orientações e documentar e comunicar procedimentos de autenticação e políticas a todos os utilizadores.|



## <a name="pci-dss-requirement-85"></a>Requisito de PCI DSS 8.5

**8.5** não utilizar o grupo, partilhado, ou os IDs de genéricos, palavras-passe ou outros métodos de autenticação da seguinte forma:
- Os IDs de utilizador genérico são desativados ou removidos.
- Não existem IDs de utilizador partilhadas para a administração de sistema e de outras funções críticas.
- Partilhados e IDs de utilizador genérico não são utilizados para administrar os componentes de sistema.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A Contoso Webstore cria três contas durante a implementação: administrador, sqladmin e edna (o utilizador predefinido registado para a aplicação web durante a execução de demonstração). Autenticação multifator não está implementada para a demonstração.|



### <a name="pci-dss-requirement-851"></a>Requisito de PCI DSS 8.5.1

**8.5.1** **requisitos adicionais para fornecedores de serviços só:** fornecedores de serviços com acesso remoto para o local de cliente (por exemplo, para suporte de sistemas de POS ou servidores) tem de utilizar um (credencial de autenticação exclusivo Por exemplo, uma palavra-passe/frase de acesso) para cada cliente. 

> [!NOTE]
> Este requisito não se destina a aplicar aos fornecedores de alojamento partilhados ao aceder ao seu próprio ambiente de alojamento, onde estão alojadas vários ambientes de clientes.

**Responsabilidades:&nbsp;&nbsp;`Not Applicable`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não é aplicável para clientes do Microsoft Azure. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Não é aplicável para clientes do Microsoft Azure.|



## <a name="pci-dss-requirement-86"></a>Requisito de PCI DSS 8.6

**8.6** sempre que forem utilizados outros mecanismos de autenticação (por exemplo, tokens de segurança física ou lógica, os smart cards, certificados, etc.), a utilização destes mecanismos deve ser atribuída da seguinte forma:
- Mecanismos de autenticação tem de ser atribuídos a uma conta individuais e não partilhados entre várias contas.
- Controlos de e/ou físicos lógicos tem de ser implementados para garantir que apenas a conta que se destinam pode utilizar essa mecanismo para obter acesso.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A Contoso Webstore cria três contas durante a implementação: administrador, sqladmin e edna (o utilizador predefinido registado para a aplicação web durante a execução de demonstração). Autenticação multifator não está implementada para a demonstração. Todo o acesso é gerido através de [Cofre de chaves do Azure](https://azure.microsoft.com/services/key-vault/), que ajuda a salvaguardar as chaves criptográficas e segredos utilizados pelas aplicações em nuvem e de serviços. |



## <a name="pci-dss-requirement-87"></a>Requisito de PCI DSS 8.7

**8.7** todos os acessos a uma base de dados que contém dados cardholder (incluindo o acesso por aplicações, os administradores e todos os outros utilizadores) é limitado da seguinte forma:
- Todos os acesso de utilizador para, consultas de utilizador do e ações do utilizador nas bases de dados são através de métodos programáticos.
- Apenas os administradores de base de dados ter a capacidade de aceder diretamente ao mesmo ou consultar as bases de dados.
- Os IDs de aplicação para aplicações de base de dados só podem ser utilizados pelas aplicações (e não por utilizadores individuais ou outros processos de aplicações não).

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A Contoso Webstore protege todos os dados de cardholder com o Cofre de chaves do Azure e encriptação dos registos é descrita na documentação de implementação. Para obter mais informações, consulte [orientações de PCI - encriptação](payment-processing-blueprint.md#encryption-and-secrets-management).<br /><br />|



## <a name="pci-dss-requirement-88"></a>Requisito de PCI DSS 8.8

**8.8** Certifique-se de que as políticas de segurança e de procedimentos operacionais para identificação e autenticação estão documentados em utilização e conhecidos para todas as partes afetadas.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Os clientes são responsáveis por garantir que as políticas de segurança e de procedimentos operacionais para identificação e autenticação estão documentados em utilização e conhecidos para todas as partes afetadas.|




