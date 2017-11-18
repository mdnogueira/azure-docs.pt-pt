---
title: "Certificações do Microsoft Azure para SAP | Microsoft Docs"
description: "Lista atualizada de configurações atuais e certificações de SAP na plataforma do Azure."
services: virtual-machines-linux
documentationcenter: 
author: RicksterCDN
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/12/2017
ms.author: rclaus
ms.custom: 
ms.openlocfilehash: 865fa54c908481b3f4c211f12293538c617b6129
ms.sourcegitcommit: a036a565bca3e47187eefcaf3cc54e3b5af5b369
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2017
---
# <a name="sap-certifications-and-configurations-running-on-microsoft-azure"></a>SAP certificações e configurações em execução no Microsoft Azure

SAP e a Microsoft tem um comprimento histórico de trabalhar em conjunto numa parceria segura que tem mútua vantagens para os seus clientes. Microsoft está constantemente a atualizar a sua plataforma e submeter novos detalhes de certificação para SAP para garantir que o Microsoft Azure é a melhor plataforma para executar as cargas de trabalho do SAP. As tabelas seguintes descrevem a nossa lista de certificações a crescer e configurações suportadas. 

## <a name="sap-hana-certifications"></a>Certificações SAP HANA
Referências:

- [A nota 2316233 - SAP HANA no Microsoft Azure (instâncias de grande)](https://launchpad.support.sap.com/#/notes/2316233) que abrangem instâncias grande HANA sobre o suporte de SAP HANA.
- [SAP HANA certificadas IaaS plataformas](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Amazon%20Web%20Services%2CMicrosoft%20Azure) para suporte de SAP HANA para nativo VMs do Azure.

| Produto SAP | SO Suportados | Ofertas do Azure |
| --- | --- | --- |
| Edição de programador do SAP HANA (incluindo o software de cliente HANA composto SQLODBC, ODBO Windows apenas, ODBC, JDBC controladores, HANA studio e a base de dados HANA) | Red Hat Enterprise Linux, SUSE Linux Enterprise | Família de série D VM |
| HANA num de negócio | SUSE Linux Enterprise | DS14_v2 |
| SAP HANA DE S/4 |Red Hat Enterprise Linux, SUSE Linux Enterprise | Disponibilidade controlada para GS5, SAP HANA no Azure (instâncias de grandes) |
| Suite no HANA, OLTP | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5 para implementações de nó único para cenários de não produção, SAP HANA no Azure (instâncias de grandes) |
| HANA Enterprise para BW, OLAP | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5 para implementações de nó único, SAP HANA no Azure (instâncias de grandes) |
| SAP BW/4 HANA | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5 para implementações de nó único, SAP HANA no Azure (instâncias de grandes) |

## <a name="sap-netweaver-certifications"></a>Certificações SAP NetWeaver
O Microsoft Azure está certificado para os seguintes produtos SAP, com suporte total da Microsoft e da SAP.
Referências:

- [1928533 - aplicações SAP no Azure: os tipos de produtos suportados e VM do Azure](https://launchpad.support.sap.com/#/notes/1928533) para todas as aplicações de NetWeaver SAP com base, incluindo SAP TREX, SAP LiveCache e servidor de conteúdo de SAP. Todas as bases de dados e, excluindo SAP HANA.


| Produto SAP | SO convidado | RDBMS | Tipos de Máquina Virtual |
| --- | --- | --- | --- |
| Software SAP Business Suite |Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (Windows e Oracle Linux apenas), DB2, SAP ASE |A5 para A11, D11 para D14, DS11 para DS14, DS11_v2 para DS15_v2, GS1 para GS5, série M |
| SAP Business All-in-One |Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux |SQL Server, Oracle (Windows e Oracle Linux apenas), DB2, SAP ASE |A5 para A11, D11 para D14, DS11 para DS14, DS11_v2 para DS15_v2, GS1 para GS5, série M |
| SAP BusinessObjects BI |Windows |N/D |A5 para A11, D11 para D14, DS11 para DS14, DS11_v2 para DS15_v2, GS1 para GS5, série M |
| SAP NetWeaver |Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux |SQL Server, Oracle (Windows e Oracle Linux apenas), DB2, SAP ASE |A5 para A11, D11 para D14, DS11 para DS14, DS11_v2 para DS15_v2, GS1 para GS5, série M |

## <a name="other-sap-workload-supported-on-azure"></a>Outras cargas de trabalho do SAP suportadas no Azure

| Produto SAP | SO convidado | RDBMS | Tipos de Máquina Virtual |
| --- | --- | --- | --- |
| SAP Business no SQL Server | Windows  | SQL Server | Certificado de NetWeaver todos os tipos VM |
| SAP BPC 10.01 MS SP08 | Windows | | Todos os tipos de NetWeaver certificadas VM<br /> A nota #2451795 |
| Plataforma de negócio objetos BI do SAP | Windows | | A nota #2145537 |
| Serviços de dados SAP 4.2 | | | A nota #2288344 |
| Plataforma de comércio Hybris SAP 5. x e 6. x | Windows | SQL Server, Oracle | Certificado de NetWeaver todos os tipos VM<br /> [Hybris Wiki](https://wiki.hybris.com/display/SUP/Using+the+hybris+Platform+with+the+Cloud) |
