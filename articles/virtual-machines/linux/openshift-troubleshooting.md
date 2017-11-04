---
title: "Resolução de problemas de implementação de OpenShift no Azure | Microsoft Docs"
description: "Resolução de problemas de implementação de OpenShift no Azure"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 
ms.author: haroldw
ms.openlocfilehash: ea3664870480f6ed170972a5f52940dc4a852219
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2017
---
# <a name="troubleshooting-openshift-deployment-in-azure"></a>Resolução de problemas de implementação de OpenShift no Azure

Se o cluster OpenShift não implementar com êxito, existem algumas tarefas de resolução de problemas que podem ser feitas para restringir o problema. Ver o estado de implementação e comparar a lista seguinte de códigos de saída.

- Código de saída 3: O Red Hat subscrição nome de utilizador / palavra-passe ou o ID de organização / chave de ativação está incorreto
- Código de saída 4: O ID de agrupamento do Red Hat estão incorreto ou não existem nenhum elegibilidade disponíveis
- Código de saída 5: Não é possível aprovisionar o Volume do agrupamento dinâmico Docker
- Código de saída 6: Falha na instalação do OpenShift Cluster
- Código de saída 7: Instalação OpenShift Cluster foi concluída com êxito, mas o fornecedor de nuvem do Azure falha na configuração - configuração mestre no problema do nó principal
- Código de saída 8: OpenShift Cluster instalação concluída com êxito, mas falha na configuração do fornecedor de nuvem do Azure - configuração de nó no problema do nó principal
- Código de saída 9: OpenShift Cluster instalação concluída com êxito, mas falha na configuração do fornecedor de nuvem do Azure - configuração de nó no problema Infra ou nó de aplicação
- Código de saída 10: Instalação do OpenShift Cluster foi efetuada com êxito, mas a configuração do fornecedor de nuvem do Azure falha - corrigir mestre nós ou não é possível definir o principal como unschedulable
- Código de saída 11: Falha ao iniciar a implementar
- Código de saída 12: Falha ao implementar o registo

Para códigos de saída 7 a 10, o OpenShift Cluster foi instalado mas a configuração do fornecedor de nuvem do Azure falhou. Pode SSH para o nó mestre (origem) ou o nó de Bastion (plataforma de contentor) e a partir do SSH há para cada um de nós no cluster e corrigir os problemas.

Uma causa comum para as falhas com 7 de códigos de saída - 9 é o Principal de serviço não tinha as permissões adequadas para a subscrição ou o grupo de recursos. Se isto, de facto, o problema, em seguida, atribua as permissões corretas e volte a executar o script que falhou um todos os scripts subsequentes manualmente.
Não se esqueça de reiniciar o serviço de falha (por exemplo, systemctl reinício atómico-openshift-node.service) antes de executar os scripts de novo.

Para obter mais de resolução de problemas, SSH para o nó mestre na porta 2200 (origem) ou o nó de Bastion na porta 22 (contentor de plataforma). Tem de ser raiz (sudo su-) e, em seguida, navegue até ao diretório seguinte: /var/lib/waagent/custom-script/download

Deverá ver uma pasta denominada '0' e '1'. Em cada um destas pastas, verá dois ficheiros, stderr e stdout. Pode ver através destes ficheiros para determinar qual a falha ocorreu.
