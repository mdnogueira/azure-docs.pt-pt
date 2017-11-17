---
title: "Azure Blueprint de processamento de pagamento - requisitos de encriptação"
description: Requisito de PCI DSS 4
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 43f75ba9-cb4e-49ab-b3f4-09e48310bc18
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 3eb5b663558c2a68c13368b179ff942dd3c53716
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="encryption-requirements-for-pci-dss-compliant-environments"></a>Requisitos de encriptação para ambientes em conformidade de PCI DSS 
## <a name="pci-dss-requirement-4"></a>Requisito de PCI DSS 4

**Encriptar transmissão de dados de cardholder em redes públicas, abra**

> [!NOTE]
> Estes requisitos são definidos pelo [Council de normas de segurança da indústria de cartão de pagamento (PCI)](https://www.pcisecuritystandards.org/pci_security/) como parte do [PCI dados segurança Standard (DSS) versão 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Consulte o PCI DSS para obter informações sobre testes de procedimentos e as diretrizes para cada requisito.

Informações confidenciais tem de ser encriptadas durante a transmissão através de redes que são facilmente aceder pessoas mal intencionadas. Configuração incorreta de redes sem fios e vulnerabilidades em protocolos de autenticação e encriptação legada continuam a ser destinos de indivíduos maliciosos que exploram estas vulnerabilidades acesso privilegiado para ambientes de dados de cardholder.

## <a name="pci-dss-requirement-41"></a>Requisito de PCI DSS 4.1

**4.1** utilizam fortes criptografia e segurança protocolos (por exemplo, TLS, IPSEC, SSH, etc.) para salvaguardar os dados confidenciais cardholder durante a transmissão através de redes de aberto, públicas, incluindo o seguinte:
- São aceites apenas chaves fidedignas e certificados.
- O protocolo a ser utilizado apenas suporta seguras versões ou configurações.
- A força da encriptação é adequada para a metodologia de encriptação em utilização. 

> [!NOTE]
> Onde é utilizado o SSL antecipado TLS, os requisitos no anexo A2 têm de ser concluídos.
>
> Exemplos de redes públicas, abra incluem, mas não estão limitados a:
> - A Internet
> - Tecnologias sem fios, incluindo 802.11 e Bluetooth
> - Tecnologias de rede móvel, por exemplo, Global System para comunicações móveis (GSM), Code divisão acesso várias (CDMA)
> - Serviço de botões de opção de pacotes de geral (GPRS)
> - Comunicações de satélite


**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | O Contoso Webstore é uma solução de PaaS que implementa criptografia segura para a implementação da seguinte forma:<br /><br />Para satisfazer os requisitos de dados em rest encriptados, [Storage do Azure](https://azure.microsoft.com/services/storage/) utiliza o seguinte:<br /><br /><ul><li>[Encriptação do serviço de armazenamento do Azure (SSE) para dados Inativos](/azure/storage/storage-service-encryption)</li><li>Base de dados do SQL Server: Uma instância de base de dados de SQL de PaaS é utilizada para demonstramos medidas de segurança da base de dados. Para obter mais informações, consulte [orientações de PCI - SQL Database do Azure](payment-processing-blueprint.md#azure-sql-database).</li><li>[Encriptação de disco do Azure (Bitlocker)](/azure/security/azure-security-disk-encryption)</li></ul>Utilizar o Cofre de chaves do Azure está alinhada com o Azure Government, PCI DSS e requisitos de HIPAA.|



### <a name="pci-dss-requirement-411"></a>Requisito de PCI DSS 4.1.1

**4.1.1** Certifique-se a redes sem fios transmitir dados cardholder ou ligado ao ambiente de dados cardholder, utilize as melhores práticas do setor (por exemplo, a IEEE 802.11i) para implementar a encriptação forte para autenticação e a transmissão.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Redes sem fios e SNMP não são implementados na solução.|



## <a name="pci-dss-requirement-42"></a>Requisito de PCI DSS 4.2

**4.2** nunca enviar PANs desprotegidas tecnologias de mensagens de utilizador final (por exemplo, correio eletrónico, instantâneas, mensagens de SMS, chat, etc.).

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A Contoso Webstore não tem qualquer soluções mensagens implementadas, que podem enviar dados de número (PAN) desprotegida conta principal.|



## <a name="pci-dss-requirement-43"></a>Requisito de PCI DSS 4.3

**4.3** Certifique-se de que as políticas de segurança e de procedimentos operacionais para encriptar as transmissões de dados de cardholder estão documentados em utilização e conhecidos para todas as partes afetadas.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Os clientes são responsáveis pela documentação e encriptar as transmissões que contêm dados cardholder.|




