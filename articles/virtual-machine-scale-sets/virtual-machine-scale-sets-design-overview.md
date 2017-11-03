---
title: "Considerações de design para conjuntos de dimensionamento de Máquina Virtual do Azure | Microsoft Docs"
description: "Saiba mais sobre as considerações de design para os conjuntos de dimensionamento de Máquina Virtual do Azure"
keywords: "conjuntos de dimensionamento de máquina virtual de máquina virtual do Linux,"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: madhana
editor: tysonn
tags: azure-resource-manager
ms.assetid: c27c6a59-a0ab-4117-a01b-42b049464ca1
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 06/01/2017
ms.author: negat
ms.openlocfilehash: 0b05359938f4da544c4cb2a6fe60cfaf228478e1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="design-considerations-for-scale-sets"></a>Considerações de design para conjuntos de dimensionamento
Este tópico descreve considerações de design para conjuntos de dimensionamento de Máquina Virtual. Para obter informações sobre quais são os conjuntos de dimensionamento de Máquina Virtual, consulte [descrição geral de conjuntos de dimensionamento de Máquina Virtual](virtual-machine-scale-sets-overview.md).

## <a name="when-to-use-scale-sets-instead-of-virtual-machines"></a>Quando utilizar escala define em vez de máquinas virtuais?
Geralmente, conjuntos de dimensionamento são úteis para implementar a infraestrutura de elevada disponibilidade em que um conjunto de máquinas têm configuração semelhante. No entanto, algumas funcionalidades só estão disponíveis nos conjuntos de dimensionamento enquanto outras funcionalidades só estão disponíveis nas VMs. Para tomar uma decisão informada sobre quando utilizar cada tecnologia, iremos deve primeiro Observe algumas das funcionalidades utilizadas frequentemente que estão disponíveis em conjuntos de dimensionamento, mas não VMs:

### <a name="scale-set-specific-features"></a>Funcionalidades de específicas do conjunto de dimensionamento

- Depois de especificar que a escala de definir a configuração, pode simplesmente atualizar a propriedade "capacidade" para implementar mais VMs em paralelo. Isto é muito mais simples do que escrever um script para orquestrar a implementação de várias VMs individuais em paralelo.
- Pode [utilizar dimensionamento automático do Azure para dimensionar automaticamente um conjunto de dimensionamento](./virtual-machine-scale-sets-autoscale-overview.md) mas VMs não individuais.
- Pode [VMs de conjunto de dimensionamento de recriação de imagem](https://docs.microsoft.com/rest/api/virtualmachinescalesets/manage-a-vm) mas [VMs individuais não](https://docs.microsoft.com/rest/api/compute/virtualmachines).
- Pode [bandeira](./virtual-machine-scale-sets-design-overview.md) conjunto de dimensionamento de VMs para uma maior fiabilidade e tempos de implementação mais rápidos. Não é possível fazê-com VMs individuais, a menos que escrever código personalizado para efetuar este procedimento.
- Pode especificar um [atualizar política](./virtual-machine-scale-sets-upgrade-scale-set.md) torna mais fácil de implementar atualizações entre VMs no seu conjunto de dimensionamento. Com VMs individuais, é necessário orquestrar atualizações por si.

### <a name="vm-specific-features"></a>Funcionalidades específicas do VM

Por outro lado, algumas funcionalidades só estão disponíveis em VMs (pelo menos para o tempo a ser):

- Pode anexar os discos de dados para as VMs individuais específicas, mas os discos de dados anexados estão configurados para todas as VMs num conjunto de dimensionamento.
- Pode anexar os discos de dados não vazio, mas não as VMs num conjunto de dimensionamento de VMs individuais.
- Pode instantâneos de uma VM individual, mas não uma VM num conjunto de dimensionamento.
- Pode capturar uma imagem de uma VM individual, mas não a partir de uma VM num conjunto de dimensionamento.
- Pode migrar uma VM individual discos nativo para gerido discos, mas não o conseguir fazer para as VMs num conjunto de dimensionamento.
- Pode atribuir endereços IP públicos do IPv6 a nics individuais de VM, mas não é possível fazer para as VMs num conjunto de dimensionamento. Tenha em atenção que pode atribuir endereços IP públicos de IPv6 para balanceadores à frente de qualquer uma das VMs individuais de carga ou conjunto de dimensionamento de VMs.

## <a name="storage"></a>Armazenamento

### <a name="scale-sets-with-azure-managed-disks"></a>Conjuntos de dimensionamento com discos gerida do Azure
Podem ser criados conjuntos de dimensionamento com [Azure geridos discos](../virtual-machines/windows/managed-disks-overview.md) em vez de contas do storage do Azure tradicional. Os discos geridos fornecem as seguintes vantagens:
- Não dispõe de pré-criar um conjunto de contas do storage do Azure para as VMs de conjunto de escala.
- Pode definir [discos de dados ligados](virtual-machine-scale-sets-attached-disks.md) para as VMs no seu dimensionamento definido.
- Conjuntos de dimensionamento podem ser configurados para [suporta até 1000 VMs num conjunto](virtual-machine-scale-sets-placement-groups.md). 

Se tiver um modelo existente, também pode [atualizar o modelo a utilizar discos geridos](virtual-machine-scale-sets-convert-template-to-md.md).

### <a name="user-managed-storage"></a>Armazenamento gerida pelo utilizador
Um conjunto de dimensionamento não está definido com discos gerida do Azure baseia-se em contas de armazenamento criados pelo utilizador para armazenar os discos de SO das VMs no conjunto. É recomendado um rácio de 20 VMs por conta de armazenamento ou menos alcançar máximo e/s e também tirar partido das _provocam um aprovisionamento_ (ver abaixo). Também é recomendável que distribuídos os carateres de início dos nomes de conta de armazenamento por alfabeto. Se o fizer, ajuda a propagar-se a carga em sistemas diferentes de internos. 


## <a name="overprovisioning"></a>Provocam um aprovisionamento
Conjuntos de dimensionamento atualmente predefinida para "provocam"um aprovisionamento VMs. Com provocam um aprovisionamento de ativada, a escala defina realmente spins mais VMS que pediu, em seguida, elimina as VMs adicionais depois do número solicitado de VMs é aprovisionado com êxito. Provocam um aprovisionamento melhora aprovisionamento taxas de êxito e reduz o tempo de implementação. Não é-lhe faturado para as VMs adicionais e não é considerado os limites de quota.

Enquanto provocam um aprovisionamento melhorar as taxas de êxito de aprovisionamento, pode fazer com que confuso comportamento para uma aplicação que não foi concebido para processar VMs Extras apresentação e, em seguida, disappearing. Para ativar provocam um aprovisionamento desativar, certifique-se de que tem a seguinte cadeia no seu modelo: `"overprovision": "false"`. Podem encontrar mais detalhes no [documentação da API de REST de conjunto de dimensionamento](/rest/api/virtualmachinescalesets/create-or-update-a-set).

Se o conjunto de dimensionamento utiliza armazenamento gerida pelo utilizador, e desativar provocam um aprovisionamento, pode ter mais de 20 VMs por conta de armazenamento, mas não é recomendado para ir acima 40 por motivos de desempenho de e/s. 

## <a name="limits"></a>Limites
Um conjunto de dimensionamento incorporada numa imagem do Marketplace (também conhecido como uma imagem de plataforma) e configurado para utilizar discos gerida do Azure suporta uma capacidade de até 1000 VMs. Se configurar o conjunto para suportar mais do que 100 VMs de dimensionamento, nem todos os cenários funcionam da mesma (por exemplo o balanceamento de carga). Para obter mais informações, consulte [trabalhar com conjuntos de dimensionamento de máquina virtual grande](virtual-machine-scale-sets-placement-groups.md). 

Um conjunto configurado com as contas de armazenamento gerida pelo utilizador de dimensionamento está limitado a 100 VMs (e 5 contas do storage são recomendadas para desta escala).

Um conjunto de dimensionamento incorporado numa imagem personalizada (um criadas por si) pode ter uma capacidade de até 300 VMs quando configurado com discos gerida do Azure. Se o conjunto de dimensionamento é configurado com contas de armazenamento gerida pelo utilizador, tem de criar todos os VHDs de disco de SO dentro de uma conta de armazenamento. Como resultado, o número máximo recomendado número de VMs num conjunto de dimensionamento incorporado numa imagem personalizada e armazenamento gerida pelo utilizador é 20. Se desativar provocam um aprovisionamento, pode ir até 40.

Para obter mais VMs que estes limites permitem, terá de implementar vários conjuntos de dimensionamento, conforme mostrado no [este modelo](https://github.com/Azure/azure-quickstart-templates/tree/master/301-custom-images-at-scale).

