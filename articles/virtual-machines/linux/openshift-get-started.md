---
title: "OpenShift na descrição geral do Azure | Microsoft Docs"
description: "OpenShift na descrição geral do Azure."
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
ms.openlocfilehash: f9641b52db91a4356f6d5789a8cd78a6bb3da02b
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2017
---
# <a name="openshift-overview"></a>Descrição geral de OpenShift

OpenShift é uma plataforma de aplicação do contentor aberta e extensível que coloca docker e Kubernetes para a empresa.  

OpenShift inclui Kubernetes para a gestão e orquestração do contentor. Adiciona o programador e ferramentas de centrada em operações que permitem:

- Desenvolvimento de aplicações rápida
- Facilitar a implementação e dimensionamento
- Manutenção de ciclo de vida de longo prazo para equipas e aplicações

Existem vários ofertas de OpenShift que dois são disponível para ser executada no Azure.

- OpenShift Origin
- OpenShift Container Platform
- OpenShift Online
- OpenShift dedicado

Das quatro ofertas abrangidas, dois estão disponíveis para os clientes implementar no Azure por si próprios - origem OpenShift e OpenShift contentor plataforma.

## <a name="openshift-origin"></a>OpenShift Origin

[Código aberto](https://www.openshift.org/) projeto a montante de OpenShift é Comunidade suportada. Origem pode ser instalada em CentOS ou RHEL.

## <a name="openshift-container-platform"></a>OpenShift Container Platform

Enterprise pronto ([oferta comercial](https://www.openshift.com)) versão do Red Hat, que é suportado pelo Red Hat. Cliente adquire a elegibilidade necessária para a plataforma de contentor OpenShift e é responsável pela instalação e gestão de toda a infraestrutura.

Uma vez que o cliente é "proprietário" a plataforma de toda, podem instalar no seu datacenter no local, nuvem pública (Azure AWS, Google, etc.), etc.

## <a name="openshift-online"></a>OpenShift Online

Red Hat geridos **multi-inquilino** OpenShift (através da plataforma de contentor). Red Hat gere toda a infraestrutura subjacente (VMs, OpenShift cluster, funcionamento em rede, armazenamento, etc.). 

Cliente implementa contentores, mas não controla os anfitriões os contentores executam. Uma vez que for multi-inquilino, contentores podem estar localizados conjuntamente em anfitriões VM mesmos como contentores de outros clientes. Custo é por contentor.

## <a name="openshift-dedicated"></a>OpenShift dedicado

Red Hat geridos **único inquilino** OpenShift (através da plataforma de contentor). Red Hat gere toda a infraestrutura subjacente (VMs, OpenShift cluster, funcionamento em rede, armazenamento, etc.). O cluster é específico para um cliente e é executado numa nuvem pública (AWS, Google, do Azure - futuras no 2018 antecipado). Cluster inicial inclui quatro nós de aplicação para $48K/ano (pagamento compromisso durante um ano completo).

## <a name="next-steps"></a>Passos seguintes

- [Configurar pré-requisitos comuns para OpenShift no Azure](./openshift-prerequisites.md)
- [Implementar OpenShift origem](./openshift-origin.md)
- [Implementar OpenShift contentor plataforma](./openshift-container-platform.md)
- [Publicar tarefas de implementação](./openshift-post-deployment.md)
- [Resolução de problemas de implementação de OpenShift](./openshift-troubleshooting.md)
