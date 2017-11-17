---
title: Azure Blueprint de processamento de pagamento - requisitos de CHD
description: Requisito de PCI DSS 3
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 9736f7c8-c632-4b86-8b8a-6e4f45c1a7aa
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 356599cbe1e4e1948a5ec16d0d504835fa7dcd43
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="chd-requirements-for-pci-dss-compliant-environments"></a>Requisitos de CHD para ambientes em conformidade de PCI DSS
## <a name="pci-dss-requirement-3"></a>Requisito de PCI DSS 3

**Proteger os dados armazenados cardholder**

> [!NOTE]
> Estes requisitos são definidos pelo [Council de normas de segurança da indústria de cartão de pagamento (PCI)](https://www.pcisecuritystandards.org/pci_security/) como parte do [PCI dados segurança Standard (DSS) versão 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Consulte o PCI DSS para obter informações sobre testes de procedimentos e as diretrizes para cada requisito.

Métodos de proteção, tais como encriptação, truncagem, máscara, e hash é componentes mais importantes cardholder de proteção de dados. Se um intruso evita a outros controlos de segurança e obtém acesso aos dados encriptados, sem as chaves criptográficas corretas, os dados são ilegíveis e inutilizáveis para essa pessoa. Outros métodos de proteção de dados armazenados em vigor também devem ser considerados como potenciais oportunidades de mitigação de riscos. Por exemplo, métodos para minimizar o risco incluem não armazenar dados de cardholder, a menos que não é necessária dados cardholder absolutamente necessário, truncar se DESLOQUEM completo e não enviar PANs desprotegidas utilizar tecnologias de mensagens de utilizador final, tais como o correio electrónico e instantâneas processamento de mensagens.
Consulte o PCI DSS e PA DSS Glossário de termos de licenciamento, abreviaturas e utilização de acrónimos para definições de "criptografia segura" e outros termos de PCI DSS.

## <a name="pci-dss-requirement-31"></a>Requisito de PCI DSS 3.1

**3.1** armazenamento de dados de cardholder mantenha a um mínimo através da implementação de políticas de retenção e disposal dados, procedimentos e os processos que incluem, pelo menos, o seguinte para todo o armazenamento de dados (CHD) cardholder:
- Limitação de armazenamento quantidade e retenção tempo dados ao que é necessário para requisitos legais, de regulamentação e empresariais
- Requisitos de retenção específico para dados cardholder
- Processos para eliminação segura dos dados quando já não é necessário
- Um processo para identificar e eliminar em segurança trimestral armazenados dados cardholder que exceda a retenção definida.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Azure é responsável por assegurar que os dados de clientes designados para eliminação são desativados de forma segura utilizando NIST protocolos compatíveis 800 88 especificados nas respetivas políticas Disposal seguro. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A Contoso Webstore não elimina nem destruir qualquer CHD armazenado. No entanto, todos os dados são encriptados e não primário dados de número (PAN) de conta são armazenados.<br /><br />|



## <a name="pci-dss-requirement-32"></a>Requisito de PCI DSS 3.2

**3.2** não armazenar dados confidenciais autenticação depois da autorização (mesmo se encriptado). Se não for recebidos dados confidenciais de autenticação, compor todos os dados irrecuperável após a conclusão do processo de autorização. 
- Há uma justificação de negócio, e 
- Os dados são armazenados de forma segura.
Dados de autenticação confidenciais incluem os dados como citou nos seguintes requisitos 3.2.1 através de 3.2.3:


**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A Contoso Webstore não elimina nem destruir qualquer CHD armazenado; os dados de exemplo são armazenados durante apenas a fins de demonstração. No entanto, todos os dados são encriptada e não primária conta dados de número (PAN) estão armazenados.<br /><br />|



### <a name="pci-dss-requirement-321"></a>Requisito de PCI DSS 3.2.1

**3.2.1** não armazene os conteúdos de qualquer controlar (a partir do stripe torção localizado na cópia de segurança de um cartão, equivalentes dados contidos num chip ou noutro local) após a autorização. Estes dados em alternativa são chamados controlar completa, controlar, controlar 1, controlar 2 e dados torção stripe. 

> [!NOTE]
> No decorrer normal da empresa, os seguintes elementos de dados do stripe a torção poderão ter de ser mantidos: 
> - Nome do cardholder 
> - Número de conta principal (PAN) 
> - Data de expiração 
> - Código do serviço 
>
> Para minimizar o risco, armazene apenas estes elementos de dados conforme necessário para empresas.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A Contoso Webstore não armazena o conteúdo completo qualquer CHD.|



### <a name="pci-dss-requirement-322"></a>Requisito de PCI DSS 3.2.2

**3.2.2** não armazene o código de verificação do cartão ou o valor (três dígitos ou com quatro dígitos número impresso no início ou back of um cartão de pagamento utilizada para verificar as transações de cartão-não-presente) após a autorização.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A Contoso Webstore encripta todos os dados, incluindo os exemplos CVV.|



### <a name="pci-dss-requirement-323"></a>Requisito de PCI DSS 3.2.3

**3.2.3** não armazene o número de identificação pessoal (PIN) ou o bloco PIN encriptado após a autorização.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A Contoso Webstore armazenam informações de PIN.|



## <a name="pci-dss-requirement-33"></a>Requisito de PCI DSS 3.3

**3.3** máscara DESLOQUEM quando apresentado (os dígitos de seis e últimos quatro primeiro são o número máximo de dígitos a apresentar), para que apenas pessoal com legítima de negócios tem de pode ver o PAN. completo 

> [!NOTE]
> Este requisito não substituir mais rigorosas requisitos no local para apresenta dos dados cardholder — por exemplo, jurídicos ou um pagamento cartão marca os requisitos para recibos do ponto de venda (POS).

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A Contoso Webstore dissimula o número de conta principal (PAN) utilizando a encriptação transparente de dados, colunas encriptadas sempre e máscara de dados dinâmicos. Para obter mais informações, consulte [orientações de PCI - SQL Database do Azure](payment-processing-blueprint.md#azure-sql-database).|



## <a name="pci-dss-requirement-34"></a>Requisito de PCI DSS 3.4

**3.4** compor PAN ilegível em qualquer local que se encontra armazenado (incluindo portátil digital suporte de dados, suportes de dados de cópia de segurança e nos registos), utilizando uma das abordagens seguintes:
- Hashes unidirecionais com base na criptografia segura, (hash tem de ser do PAN completo)
- Truncagem (hash não pode ser utilizado para substituir o segmento truncado do PAN)
- Índice de tokens e pads (pads tem de ser armazenados em segurança)
- Criptografia segura com processos de gestão de chaves associados e procedimentos. 

> [!NOTE]
> É um esforço relativamente trivial para um indivíduo malicioso para reconstrua dados PAN original se tiverem acesso à versão truncada e hash de um PAN. Em que protegido por hash e truncado versões do mesmo PAN estão presentes no ambiente de uma entidade, controlos adicionais têm de ser implementados para garantir que as versões com hash e truncadas não podem ser correlacionadas com reconstrua o PAN. original

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | O Contoso Webstore encripta todos os dados de cartão de crédito e utiliza o Cofre de chaves do Azure para gerir chaves, impedir a obtenção de CHD.<br /><br />Para obter mais informações, consulte [orientações de PCI - encriptação](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-341"></a>Requisito de PCI DSS 3.4.1

**3.4.1** se for utilizada a encriptação de disco (em vez de encriptação da base de dados de nível de ficheiro ou de coluna), acesso lógico tem de ser gerido separadamente e independentemente do sistema de operativo nativo autenticação e mecanismos de controlo de acesso (por exemplo, por não utilizar bases de dados de conta de utilizador local ou as credenciais de início de sessão gerais de rede). As chaves de desencriptação não pode ser associadas a contas de utilizador. 

> [!NOTE]
> Este requisito aplica-se para além de todos os outros encriptação PCI DSS e requisitos de gestão de chaves.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A Contoso Webstore encripta os dados armazenados todos os e segrega o tráfego para impedir ataques de elevação de privilégio para funções de DevOps.<br /><br />Como o ambiente de serviço de aplicações está protegido e bloqueado, tem de haver um mecanismo para permitir qualquer DevOps versões ou as alterações que poderão ser necessárias, tais como a capacidade de monitorizar uma aplicação Web utilizando o Kudu.<br /><br />Uma máquina virtual é estabelecida como um jumpbox (anfitrião de bastion) com as seguintes configurações:<br /><br /><ul><li>[Extensão de antimalware](/azure/security/azure-security-antimalware)</li><li>[Extensão de monitorização do OMS](/azure/virtual-machines/virtual-machines-windows-extensions-oms)</li><li>[Extensão de diagnóstico de VM](/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)</li><li>[Disco encriptado do BitLocker](/azure/security/azure-security-disk-encryption)</li></ul>Utilizar o Cofre de chaves do Azure está alinhada com o Azure Government, PCI DSS e requisitos de HIPAA.|



## <a name="pci-dss-requirement-35"></a>Requisito de PCI DSS 3.5

**3.5** procedimentos de documentos e a implementar para proteger as chaves utilizadas para proteger os dados armazenados cardholder contra divulgação e utilização indevida. 

> [!NOTE]
> Este requisito aplica-se às chaves utilizadas para encriptar dados armazenados cardholder e também se aplica a chave de encriptação as chaves utilizadas para proteger chaves de encriptação de dados — estas chaves de encriptação de chave tem de ser pelo menos tão fortes como a chave de encriptação de dados.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | **Para clientes que utilizam o Cofre de chaves:**<br /><br />Microsoft Azure garante que cofres de chaves do cliente são logicamente isoladas entre si e logicamente isoladas entre o plane de gestão do serviço Cofre de chaves. O Cofre de chaves foi concebido para que a Microsoft não tem qualquer acesso colocado ao Cofre de chaves do cliente. <br /><br />As chaves são salvaguardadas pelo Microsoft Azure, utilizando algoritmos de norma da indústria, comprimentos de chave e módulos de segurança de hardware (HSMs).<br /><br />Uma chave armazenada no Cofre de chave do Microsoft Azure pode ser utilizada para proteger a chave de outro. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A Contoso Webstore fornece documentação para ilustrar e ajudar a implementar uma solução de chave protegida para proteger a demonstração CHD.|



### <a name="pci-dss-requirement-351"></a>Requisito de PCI DSS 3.5.1

**3.5.1** *requisitos adicionais para fornecedores de serviços só:* manter uma descrição documentada da criptografia arquitetura que inclui:
- Detalhes de todos os algoritmos, protocolos e as chaves utilizadas para a proteção de dados de cardholder, incluindo data chave de segurança e de expiração
- Descrição da utilização de chave para cada chave
- Inventário de qualquer HSMs e outros SCDs utilizados para gestão de chaves 

> [!NOTE]
> Este requisito é uma melhor prática até 31 de Janeiro de 2018, após o qual torna-se um requisito.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | **Para clientes que utilizam o Cofre de chaves:**<br /><br />Microsoft Azure garante que cofres de chaves do cliente são logicamente isoladas entre si e logicamente isoladas entre o plane de gestão do serviço Cofre de chaves. O Cofre de chaves foi concebido para que a Microsoft não tem qualquer acesso colocado ao Cofre de chaves do cliente. <br /><br />As chaves são salvaguardadas pelo Microsoft Azure, utilizando algoritmos de norma da indústria, comprimentos de chave e módulos de segurança de hardware (HSMs).<br /><br />Uma chave armazenada no Cofre de chave do Microsoft Azure pode ser utilizada para proteger a chave de outro. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A Contoso Webstore fornece documentação para ilustrar e ajudar a implementar uma solução de chave protegida para proteger a demonstração CHD.|



### <a name="pci-dss-requirement-352"></a>Requisito de PCI DSS 3.5.2

**3.5.2** restringir o acesso a chaves criptográficas para o menor número de conservadores chaves necessários.


**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | **Para clientes que utilizam o Cofre de chaves:**<br /><br />O Cofre de chaves suporta políticas de acesso granular, que permitem conceder acesso a funcionalidades específicas para efetuar operações específicas para entidades específicas de um proprietário de Cofre de chaves. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Gestão de chaves contoso Webstore está isolada para conta de utilizador (admin # #@contosowebstore.com).|



### <a name="pci-dss-requirement-353"></a>Requisito de PCI DSS 3.5.3

**3.5.3** armazenar chaves secretas e privadas utilizadas para encriptar/desencriptar dados cardholder numa (ou mais) dos seguintes formulários de todas as vezes:
- Encriptado com uma chave de encriptação de chave de pelo menos tão forte como a chave de encriptação de dados, e que é armazenado em separado da chave de encriptação de dados
- Dentro de um dispositivo de criptografia seguro (por exemplo, um módulo de hardware (anfitrião) de segurança (HSM) ou um dispositivo de ponto de interação aprovado PTS)
- Como, pelo menos, dois componentes chaves full-length ou partilhas de chaves, em conformidade com um setor - aceites método 

> [!NOTE]
> Não é necessário que as chaves públicas ser armazenados destes formulários.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | **Para clientes que utilizam o Cofre de chaves:**<br /><br />As chaves são armazenadas nos cofres de chaves específicos identificados pelo cliente.<br /><br />O Cofre de chaves podem ser acedido em simultâneo e globalmente por várias aplicações que reduz a necessidade de copiar uma chave e armazenar em várias localizações. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | O Contoso Webstore utiliza o Cofre de chaves do Azure para toda a gestão de chave. Para obter mais informações, consulte [orientações de PCI - encriptação](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-354"></a>Requisito de PCI DSS 3.5.4

**3.5.4** armazenar as chaves criptográficas nas localizações menos possíveis.


**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | **Para clientes que utilizam o Cofre de chaves:**<br /><br />As chaves são armazenadas nos cofres de chaves específicos identificados pelo cliente. <br /><br />O Cofre de chaves podem ser acedido em simultâneo e globalmente por várias aplicações que reduz a necessidade de copiar uma chave e armazenar em várias localizações. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | O Contoso Webstore utiliza o Cofre de chaves do Azure para toda a gestão de chave. Para obter mais informações, consulte [orientações de PCI - encriptação](payment-processing-blueprint.md#encryption-and-secrets-management).|



## <a name="pci-dss-requirement-36"></a>Requisito de PCI DSS 3.6

**3.6** totalmente documentos e implementar todos os processos de gestão de chaves e procedimentos para chaves criptográficas utilizadas para encriptação de dados de cardholder, incluindo o seguinte. 

> [!NOTE]
> Várias normas da indústria para gestão de chaves estão disponíveis a partir de vários recursos, incluindo NIST, que pode ser encontrado em http://csrc.nist.gov.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | O Contoso Webstore utiliza o Cofre de chaves do Azure para toda a gestão de chave. Para obter mais informações, consulte [orientações de PCI - encriptação](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-361"></a>Requisito de PCI DSS 3.6.1

**3.6.1** geração de chaves de criptografia seguras

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | **Para clientes que utilizam o Cofre de chaves:** <br /><br />Quando a gerar chaves no Cofre de chave, Azure é responsável pela geração de chaves por especificações do cliente. As chaves são geradas a utilizar um HSM. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | O Contoso Webstore utiliza o Cofre de chaves do Azure para toda a gestão de chave. Para obter mais informações, consulte [orientações de PCI - encriptação](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-362"></a>Requisito de PCI DSS 3.6.2

**3.6.2** secure distribuição de chave de criptografia

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | **Para clientes que utilizam o Cofre de chaves:**<br /><br />Traga a seus próprios ferramenta de chave (BYOK) contém a chave de cliente e está direcionada para um cofre de segurança específico que está associado a uma subscrição do Azure específica. A chave apenas pode ser importada para o Cofre de chaves da subscrição definido, na região especificada. Este processo utiliza os procedimentos de encriptação fornecidos pelo fabricante de hardware. Os clientes recebem uma notificação de que a transferência foi concluída com êxito. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | O Contoso Webstore utiliza o Cofre de chaves do Azure para toda a gestão de chave. Para obter mais informações, consulte [orientações de PCI - encriptação](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-363"></a>Requisito de PCI DSS 3.6.3

**3.6.3** proteger o armazenamento de chaves criptográfico

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | **Para clientes que utilizam o Cofre de chaves:**<br /><br />As chaves são armazenadas nos HSMs e são protegidas através de segurança de criptografia do fabricante de hardware. Os metadados de chaves é armazenado no armazenamento do Azure num estado encriptado, que é exclusivo para cada Cofre de chaves. <br /><br /> |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | O Contoso Webstore utiliza o Cofre de chaves do Azure para toda a gestão de chave. Para obter mais informações, consulte [orientações de PCI - encriptação](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-364"></a>Requisito de PCI DSS 3.6.4

**3.6.4** alterações criptografia de chave para chaves atingiram o fim do respetivo cryptoperiod (por exemplo, após um período de tempo definido foi efectuada com êxito e/ou depois de uma determinada quantidade de texto de cifra tem foram produzida por uma determinada chave), conforme definido pelo associada fornecedor da aplicação ou proprietário chave e do setor com base nas melhores práticas e diretrizes (por exemplo, publicação especial do NIST 800-57).

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | **Para clientes que utilizam o Cofre de chaves:**<br /><br />O Cofre de chaves suporta a funcionalidade atualizar ou implementar chaves, que é definida pelo cliente. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | O Contoso Webstore utiliza o Cofre de chaves do Azure para toda a gestão de chave. Para obter mais informações, consulte [orientações de PCI - encriptação](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-365"></a>Requisito de PCI DSS 3.6.5

**3.6.5** extinção ou substituição (por exemplo, arquivo, destruição e/ou revogação) das chaves conforme considerar necessário quando a integridade da chave tem sido enfraquecida (por exemplo, distanciamento de um empregado com dados de conhecimento de uma chave de texto não encriptado componente), ou chaves se suspeita serem de ser comprometida. 

> [!NOTE]
> Se extintos ou substituídos chaves criptográficas têm de ser mantidos, estas chaves devem ser arquivadas em segurança (por exemplo, utilizando uma chave de encriptação de chave). As chaves criptográficas arquivadas só devem ser utilizadas para fins de verificação/desencriptação.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | **Para clientes que utilizam o Cofre de chaves:**<br /><br />O Cofre de chaves suporta a funcionalidade de extinguir ou substituir, que é definida pelo cliente. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | O Contoso Webstore utiliza o Cofre de chaves do Azure para toda a gestão de chave. Para obter mais informações, consulte [orientações de PCI - encriptação](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-366"></a>Requisito de PCI DSS 3.6.6

**3.6.6** se forem utilizadas manuais operações gestão de chaves criptográficas texto não encriptado, estas operações tem de ser geridas utilizando dividir dados de conhecimento e controlo duplo. 

> [!NOTE]
> Exemplos de operações de gestão de chaves manuais incluem, mas não estão limitados a: geração, transmissão, carregar, armazenamento e destruição de chaves.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | O Contoso Webstore utiliza o Cofre de chaves do Azure para toda a gestão de chave. Para obter mais informações, consulte [orientações de PCI - encriptação](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-367"></a>Requisito de PCI DSS 3.6.7

**3.6.7** prevenção de substituição não autorizada das chaves criptográficas.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | **Para clientes que utilizam o Cofre de chaves:**<br /><br />Cofres de chave são logicamente separados e não suportam a autorização de diretório entre. Como resultado, é impedida a substituição não autorizada. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | O Contoso Webstore utiliza o Cofre de chaves do Azure para toda a gestão de chave. Para obter mais informações, consulte [orientações de PCI - encriptação](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-368"></a>Requisito de PCI DSS 3.6.8

**3.6.8** requisito para conservadores de chaves criptográficas chaves formally reconhecer que compreender e aceitar as responsabilidades conservador de chave.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Gestão de chaves contoso Webstore está isolada para conta de utilizador (admin # #@contosowebstore.com).|



## <a name="pci-dss-requirement-37"></a>Requisito de PCI DSS 3.7

**3.7** Certifique-se de que as políticas de segurança e de procedimentos operacionais para proteger os dados armazenados cardholder estão documentados em utilização e conhecidos para todas as partes afetadas.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | O Contoso Webstore utiliza o Cofre de chaves do Azure para toda a gestão de chave. Para obter mais informações, consulte [orientações de PCI - encriptação](payment-processing-blueprint.md#encryption-and-secrets-management).|




