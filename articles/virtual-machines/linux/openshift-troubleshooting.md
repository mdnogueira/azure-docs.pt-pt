---
title: "Resolver problemas de implementação de OpenShift no Azure | Microsoft Docs"
description: "Resolver problemas de implementação de OpenShift no Azure."
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
ms.openlocfilehash: 35e554d3a9c7e7d56546ae9723c33eb59e906472
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2017
---
# <a name="troubleshoot-openshift-deployment-in-azure"></a>Resolver problemas de implementação de OpenShift no Azure

Se o cluster OpenShift não implementar com êxito, tente estas tarefas para restringir o problema de resolução de problemas. Ver o estado de implementação e comparar a lista seguinte de códigos de saída:

- 3 do código de saída: O nome de utilizador de subscrição do Red Hat / ID de organização ou palavra-passe / chave de ativação está incorreto
- 4 de código de saída: O ID do conjunto Red Hat estão incorreta ou não existem nenhum elegibilidade disponíveis
- 5 do código de saída: não é possível aprovisionar o Volume do agrupamento dinâmico Docker
- 6 de código de saída: Falha na instalação do OpenShift Cluster
- 7 de código de saída: instalação OpenShift Cluster foi concluída com êxito mas o fornecedor de solução em nuvem do Azure falha na configuração - configuração mestre no problema do nó principal
- 8 do código de saída: instalação OpenShift Cluster foi concluída com êxito mas o fornecedor de solução em nuvem do Azure falha na configuração - configuração de nó no problema do nó principal
- 9 de código de saída: instalação OpenShift Cluster foi concluída com êxito mas o fornecedor de solução em nuvem do Azure falha na configuração - configuração de nó no problema Infra ou nó de aplicação
- 10 do código de saída: instalação OpenShift Cluster criada com êxito, mas a configuração do fornecedor de solução em nuvem do Azure falha - corrigir mestre nós ou não é possível definir o principal como unschedulable
- 11 do código de saída: Falha ao iniciar a implementação
- 12 de código de saída: não foi possível implementar o registo

Para códigos de saída 7 a 10, o cluster OpenShift foi instalado, mas a configuração do fornecedor de solução em nuvem do Azure falhou. Pode SSH para o nó mestre (OpenShift origem) ou o nó de bastion (plataforma de contentor OpenShift) e a partir do SSH há para cada nó de cluster para corrigir os problemas.

Uma causa comum para as falhas com códigos de saída 7 9 é que o principal de serviço não tinha as permissões adequadas para a subscrição ou o grupo de recursos. Se este for o problema, atribua as permissões corretas e manualmente voltar a executar o script que falhou e todos os scripts subsequentes.

Não se esqueça de reiniciar o serviço de falha (por exemplo, systemctl reinício atómico-openshift-node.service) antes de executar os scripts de novo.

Para obter mais de resolução de problemas, SSH para o nó principal na porta 2200 (origem) ou o nó de bastion na porta 22 (contentor de plataforma). Tem de estar na raiz (sudo su-) e, em seguida, navegue até ao diretório seguinte: /var/lib/waagent/custom-script/download.

Aqui pode ver que pastas com o nome "0" e "1". Em cada um destas pastas, verá dois ficheiros, "stderr" e "stdout." Examine estes ficheiros para determinar qual a falha ocorreu.
