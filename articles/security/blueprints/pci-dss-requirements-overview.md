---
title: "Azure Blueprint de processamento de pagamento - descrição geral de alto nível"
description: "Azure Blueprint de processamento de pagamento - matriz de responsabilidade do cliente (descrição geral)"
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 23cf68d8-bebd-4ac4-a194-39e052281c0e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 49acce706f09fe08b257ce8a8554de5da20060a1
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="pci-dss-requirements---high-level-overview"></a>Requisitos de PCI DSS - descrição geral de alto nível

A e Payment Card Industry Data segurança (PCI DSS) foi desenvolvido para encorajar e melhorar a segurança dos dados cardholder e facilitar a adoção abrangente de medidas de segurança de dados com consistência global. PCI DSS fornece uma linha de base dos requisitos técnicos e operacionais concebidas para proteger os dados da conta. PCI DSS aplica-se a todas as entidades envolvidas no processamento de cartão de pagamento, incluindo merchants, processadores, acquirers, emissores e fornecedores de serviços. PCI DSS também se aplica a todas as outras entidades que armazenarem, processam ou transmitem dados cardholder (CHD) e/ou dados confidenciais de autenticação (SAD). Segue-se uma descrição geral de alto nível 12 requisitos PCI DSS.

> [!NOTE]
> Estes requisitos são definidos pelo [Council de normas de segurança da indústria de cartão de pagamento (PCI)](https://www.pcisecuritystandards.org/pci_security/) como parte do [PCI dados segurança Standard (DSS) versão 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Consulte o PCI DSS para obter informações sobre testes de procedimentos e as diretrizes para cada requisito.

|   |   |
|---|---|
| **Criar e manter segura<br/>sistemas e de rede** | 1. [Instalar e manter uma configuração de firewall para proteger os dados de cardholder](pci-dss-requirement-1-firewall.md)<br/><br/> 2. [Não utilize as predefinições fornecida pelo fabricante para palavras-passe do sistema e outros parâmetros de segurança](pci-dss-requirement-2-password.md) |  
| **Proteger os dados de Cardholder** | 3. [Proteger os dados armazenados cardholder](pci-dss-requirement-3-chd.md)<br/><br/> 4. [Encriptar transmissão de dados de cardholder em redes públicas, abra](pci-dss-requirement-4-encryption.md) |
| **Manter uma vulnerabilidade<br/>programa de gestão** | 5. [Proteger todos os sistemas contra software maligno e atualizar regularmente o software de antivírus ou programas](pci-dss-requirement-5-malware.md)<br/><br/> 6. [Desenvolver e a manter segurados sistemas e aplicações](pci-dss-requirement-6-secure-system.md) |
| **Implementar o acesso seguro<br/>controlar medidas** | 7. [Restringir o acesso a dados cardholder por empresas necessidade de conhecimento](pci-dss-requirement-7-access.md)<br/><br/> 8. [Identificar e autenticar o acesso a componentes do sistema](pci-dss-requirement-8-identity.md) <br/><br/> 9. [Restringir o acesso físico aos dados de cardholder](pci-dss-requirement-9-physical-access.md) |
| **Monitorize regularmente e<br/>redes de teste** | 10. [Controlar e monitorizar o acesso de todos os recursos de rede e a dados de cardholder](pci-dss-requirement-10-monitoring.md) <br/><br/> 11. [Testar regularmente os sistemas de segurança e processos](pci-dss-requirement-11-testing.md) |
| **Manter uma informações<br/>política de segurança** | 12. [Manter uma política de segurança de informações de endereços para todo o pessoal](pci-dss-requirement-12-policy.md) |

