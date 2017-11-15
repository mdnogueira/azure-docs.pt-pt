---
title: "Amostras de modelo de política | Microsoft Docs"
description: "Exemplos JSON para a política do Azure"
services: azure-policy
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
tags: 
ms.assetid: 
ms.service: azure-policy
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: 
ms.date: 11/13/2017
ms.author: banders
ms.custom: mvc
ms.openlocfilehash: 875c8c87f62ecf0e7459dfcec22089854ad95594
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2017
---
# <a name="templates-for-azure-policy"></a>Modelos de política do Azure

A tabela seguinte inclui ligações para modelos json para a política do Azure. Estes exemplos que se encontram no [repositório de amostras de política do Azure](https://github.com/Azure/azure-policy).

| | |
|---|---|
|**Computação**||
| [Imagens VM aprovadas](scripts/allowed-custom-images.md) | Requer que apenas aprovados personalizado imagens no seu ambiente. Especifique uma matriz de IDs de imagem aprovada. |
| [Auditoria quando a VM não utiliza disco geridos](scripts/create-vm-managed-disk.md) | Auditorias quando é criada uma máquina virtual que utiliza discos geridos.|
| [Auditoria se a extensão não existe](scripts/audit-ext-not-exist.md) | Auditorias se uma extensão não estiver implementada com uma máquina virtual. Especifique o fabricante de extensão e o tipo para verificar se esta foi implementada. |
| [Permitir que a imagem VM personalizada de um grupo de recursos](scripts/allow-custom-vm-image.md) |  Requer que as imagens personalizadas ser proveniente de um grupo de recursos aprovados. Especifique o nome do grupo de recursos aprovados. |
| [Negar a vantagem de utilizar híbrida](scripts/deny-hybrid-use.md) | Proíbe a utilização do Azure híbrida utilizar benefício (AHUB). Utilize quando não pretender permitir a utilização das licenças no local. |
| [Não é permitida extensões de VM](scripts/not-allowed-vm-ext.md) | Proíbe a utilização de extensões especificadas. Especifique uma matriz que contém os tipos de extensão proibido. |
| [Permitir apenas uma determinada imagem de plataforma VM](scripts/allow-certain-vm-image.md) | Requer que as máquinas virtuais, utilize uma versão específica do UbuntuServer. |
| [Criar a VM com o disco geridos](scripts/use-managed-disk-vm.md) | Requer que as máquinas virtuais utilizam discos geridos.|
|**Monitorização**||
| [Definição de diagnóstico de auditoria](scripts/audit-diag-setting.md) | Auditorias se as definições de diagnóstico não ativadas para especificar tipos de recursos. Especifique uma matriz de tipos de recurso para verificar se as definições de diagnóstico estão ativadas. |
|**Convenções de texto e nome**||
| [Permitir que vários padrões de nome](scripts/allow-multiple-name-patterns.md) | Permitir que um dos muitos padrões de nome a utilizar para recursos. |
| [Exigir como padrão](scripts/enforce-like-pattern.md) | Certifique-se de que os nomes de recursos satisfazem a condição like para um padrão. |
| [Exigir correspondência de padrão](scripts/enforce-match-pattern.md) | Certifique-se de que os nomes de recursos correspondem o padrão de nomenclatura. |
| [Exigir padrão de correspondência de etiqueta](scripts/enforce-tag-match-pattern.md) | Certifique-se de que um valor de etiqueta corresponde a um padrão de texto. |
|**Rede**||
| [Permitido SKUs de Gateway de aplicação](scripts/allowed-app-gate-sku.md) | Requer que os gateways de aplicação utilizem um SKU aprovado. Especifique uma matriz de SKUs aprovados. |
| [Se o observador de rede não está ativada para a região de auditoria](scripts/net-watch-not-enabled.md) | Auditorias se observador de rede não está ativada para uma região especificada. Especifique o nome da região para verificar se o observador de rede está ativada. |
| [NSG X em cada NIC](scripts/nsg-on-nic.md) | Requer que um grupo de segurança de rede específica é utilizado com cada interface de rede virtual. Especifique o ID do grupo de segurança de rede a utilizar. |
| [NSG X em cada sub-rede](scripts/nsg-on-subnet.md) | Requer que um grupo de segurança de rede específica é utilizado com todas as sub-redes virtuais. Especifique o ID do grupo de segurança de rede a utilizar. |
| [Largura de banda permitida do Expressroute](scripts/allowed-er-band.md) | Requer que as rotas rápidas utilizem um conjunto especificado de larguras de banda. Especifique uma matriz de SKUs que pode ser especificado para o Expressroute. |
| [Permissão de localização de Peering de Expressroute](scripts/allowed-peering-er.md) | Requer que a utilização de rotas Express especificado localizações de peering. Especifique uma matriz de localizações de peering permitidas. |
| [SKUs de permitidos do Expressroute](scripts/allowed-er-skus.md) | Requer que as rotas Express um SKU aprovado. Especifique uma matriz de SKUs permitidos. |
| [Permitido SKUs de Balanceador de carga](scripts/allowed-lb-skus.md) | Requer que os balanceadores de carga um SKU aprovado. Especifique uma matriz de SKUs permitidos. |
| [Nenhuma rede peering à rede de ER](scripts/no-peering-er-net.md) | Proíbe uma rede de peering de que está a ser associado a uma rede de um grupo de recursos especificado. Utilize para impedir a ligação com a infraestrutura de rede gerido central. Especifique o nome do grupo de recursos para impedir a associação. |
| [Nenhuma tabela de rota definida pelo utilizador](scripts/no-user-def-route-table.md)  |Proíbe a redes virtuais de que está a ser implementada com uma tabela de rota definida pelo utilizador. |
| [Permitido SKUs de Gateway de rede Virtual](scripts/no-user-def-route-table.md) | Requer que os gateways de rede virtual, utilize um tipo SKU e gateway aprovado. Especifique uma matriz de SKUs aprovados e uma matriz de tipos de gateway aprovados. |
| [Utilizar a sub-rede aprovado para interfaces de rede VM](scripts/use-approved-subnet-vm-nics.md) | Requer que as interfaces de rede, utilize uma sub-rede aprovada. Especifique o ID da sub-rede aprovado. |
| [Utilize a vNet aprovado para interfaces de rede VM](scripts/use-approved-vnet-vm-nics.md) | Requer que as interfaces de rede, utilize uma rede virtual aprovada. Especifique o ID da rede virtual aprovado. |
|**Etiquetas**||
| [Iniciativa de política de etiquetas de faturação](scripts/billing-tags-policy-init.md) | Necessita de valores de etiqueta especificada para o nome do produto e de centro de custos. Utiliza políticas incorporadas para aplicar e impor etiquetas necessárias. Especifique os valores necessários para as etiquetas.  |
| [Impor a etiqueta e o respetivo valor dos grupos de recursos](scripts/enforce-tag-rg.md) | Requer uma etiqueta e o valor num grupo de recursos. Especifique o nome de tag necessária e o valor.  |
|**SQL**||
| [O nível de base de dados do SQL Server de auditoria definição de auditoria](scripts/audit-sql-db-audit-setting.md) | As auditorias de definições de auditoria de base de dados do SQL Server se essas definições não correspondem uma definição especificada. Especifique um valor que indica se as definições de auditoria devem ser ativadas ou desativadas.  |
| [Estado de encriptação transparente de dados de auditoria](scripts/audit-trans-data-enc-status.md) | Se não estiver ativada, as auditorias a encriptação transparente de dados do SQL Server da base de dados.  |
| [Definição de deteção de nível de ameaças de base de dados de auditoria](scripts/audit-db-threat-det-setting.md) | As auditorias de políticas de alerta de segurança do SQL Server da base de dados se as políticas não estão definidas para o estado especificado. Especifique um valor que indica se a deteção de ameaças está ativada ou desativada.  |
| [O nível de servidor do SQL Server de auditoria definição de auditoria](scripts/audit-sql-ser-leve-audit-setting.md) | As auditorias de definições de auditoria do servidor SQL se essas definições não correspondem uma definição especificada. Especifique um valor que indica se as definições de auditoria devem ser ativadas ou desativadas. |
| [Definição de deteção de ameaças ao nível do servidor de auditoria](scripts/audit-sql-ser-threat-det-setting.md) | As auditorias de políticas de alerta de segurança do SQL Server da base de dados se as políticas não estão definidas para o estado especificado. Especifique um valor que indica se a deteção de ameaças está ativada ou desativada.  |
| [Permitido SKUs de BD do SQL Server](scripts/allowed-sql-db-skus.md) | Necessita de bases de dados do SQL Server utilizam um SKU aprovado. Especifique uma matriz de IDs de SKU permitido ou uma matriz de nomes SKU permitidos. |
|**Armazenamento**||
| [SKUs permitidos para as contas de armazenamento e máquinas virtuais](scripts/allowed-skus-storage.md) | Requer que as contas de armazenamento e máquinas virtuais SKUs aprovados. Utiliza políticas incorporadas para garantir a aprovada SKUs. Especifique uma matriz de máquinas virtuais aprovadas SKUs e uma matriz de conta de armazenamento aprovados SKUs. |
| [Certifique-se a tráfego https apenas para a conta de armazenamento](scripts/ensure-https-stor-acct.md) | Necessita de contas de armazenamento para utilizar o tráfego HTTPS.  |
| [Negar o acesso esporádico camadas para contas de armazenamento](scripts/deny-cool-access-tiering.md) | Proíbe a utilização de acesso esporádico camadas para contas do blob storage.  |
| [Certifique-se a encriptação de ficheiros de armazenamento](scripts/ensure-store-file-enc.md) | Requer que a encriptação de ficheiros está ativada para contas de armazenamento.  |
|**Política incorporada**||
| [Localizações permitidas](scripts/allowed-locs.md) | Requer que todos os recursos são implementados em localizações aprovadas. Especifique uma matriz de localizações aprovadas.  |
| [Tipos de recursos permitidos](scripts/allowed-res-types.md) | Garante que apenas os tipos de recursos aprovados são implementados. Especifique uma matriz de tipos de recursos que são permitidos.  |
| [Permitido SKUs de conta de armazenamento](scripts/allowed-stor-acct-skus.md) | Requer que as contas do storage um SKU aprovado. Especifique uma matriz de SKUs aprovados. |
| [Aplicar a etiqueta e o respetivo valor predefinido](scripts/apply-tag-def-val.md) | Acrescenta um nome de etiqueta especificado e valor, se essa tag não for fornecido. Especifique o nome de etiqueta e o valor a aplicar.  |
| [Impor a etiqueta e o respetivo valor](scripts/enforce-tag-val.md) | Requer um nome de etiqueta especificada e o valor. Especifique o nome de etiqueta e o valor para impor.  |
| [Não é permitida tipos de recursos](scripts/not-allowed-res-type.md) | Proíbe a implementação dos tipos de recurso especificado. Especifique uma matriz de tipos de recursos para bloquear.  |
| [Necessita do SQL Server versão 12.0](scripts/req-sql-12.md) | Necessita de servidores do SQL Server para utilizar a versão 12.0.  |
| [Exigir encriptação de conta de armazenamento](scripts/req-store-acct-enc.md) | Requer a encriptação de blob de utilização da conta de armazenamento.  |
