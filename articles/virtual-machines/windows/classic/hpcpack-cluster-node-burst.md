---
title: "Adicionar nós rajada para um cluster HPC Pack | Microsoft Docs"
description: "Saiba como expandir um cluster HPC Pack no Azure a pedido através da adição de instâncias de função de trabalho em execução num serviço em nuvem"
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-service-management,hpc-pack
ms.assetid: 24b79a8a-24ad-4002-ae76-75abc9b28c83
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: big-compute
ms.date: 10/14/2016
ms.author: danlep
ms.openlocfilehash: 96e332504509ae98e280d1c8585b0b294e0e07cb
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2017
---
# <a name="add-on-demand-burst-nodes-to-an-hpc-pack-cluster-in-azure"></a>Adicionar nós pedido "rajada" para um cluster HPC Pack no Azure
Se configurar um [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) cluster no Azure, poderá pretender que uma forma rapidamente aumentar a capacidade de cluster ou para baixo, sem mantendo um conjunto de nó de computação pré-configurada VMs. Este artigo mostra como adicionar nós pedido "rajada" (instâncias de função de trabalho em execução num serviço em nuvem) como recursos de computação para um nó principal no Azure. 

> [!IMPORTANT] 
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Resource Manager e clássico](../../../resource-manager-deployment-model.md). Este artigo abrange utilizando o modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

![Impulsar nós][burst]

Os passos neste artigo que ajudam adicionar nós do Azure rapidamente a um baseado na nuvem HPC Pack head nó VM para uma implementação de uma prova de conceito ou teste. Os passos de alto nível são os mesmos que os passos para "impulsar para o Azure" para adicionar a um cluster do pacote HPC no local a capacidade de computação de nuvem. Para um tutorial, consulte [configurar um cluster de cálculo híbrido com o Microsoft HPC Pack](../../../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md). Para orientações detalhadas e considerações para implementações de produção, consulte [Impulsar para o Azure com o Microsoft HPC Pack](https://technet.microsoft.com/library/gg481749.aspx).

## <a name="prerequisites"></a>Pré-requisitos
* **Nó principal HPC Pack implementado na VM do Azure** -pode utilizar um nó principal autónomo VM ou um que faz parte de um cluster maior. Para criar um nó principal autónomo, consulte [implementar um nó de cabeçalho de pacote HPC numa VM do Azure](../../virtual-machines-windows-hpcpack-cluster-headnode.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Para opções de implementação de cluster HPC Pack automáticas, consulte [cluster opções para criar e gerir um Windows HPC no Azure com o Microsoft HPC Pack](../../virtual-machines-windows-hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
  
  > [!TIP]
  > Se utilizar o [script de implementação de HPC Pack IaaS](hpcpack-cluster-powershell-script.md) para criar o cluster no Azure, pode incluir nós rajada do Azure na sua implementação automática. Consulte os exemplos esse artigo.
  > 
  > 
* **Subscrição do Azure** - para adicionar nós do Azure, pode escolher a mesma subscrição utilizada para implementar o nó principal VM, ou uma subscrição diferente (ou subscrições).
* **Quota de núcleos** -poderá ter de aumentar a quota de núcleos, especialmente se optar por implementar vários nós do Azure com tamanhos especializados. Para aumentar uma quota [abrir um pedido de suporte ao cliente online](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) , sem encargos.

## <a name="step-1-create-a-cloud-service-and-a-storage-account-for-the-azure-nodes"></a>Passo 1: Criar um serviço em nuvem e de uma conta de armazenamento para os nós do Azure
Utilize o portal do Azure ou ferramentas equivalentes para configurar os seguintes recursos que são necessários para implementar os nós do Azure:

* Um novo serviço em nuvem do Azure (clássica)
* Uma nova conta de armazenamento do Azure (clássica)

> [!NOTE]
> Não reutilize um serviço em nuvem existente na sua subscrição. 
> 
> 

**Considerações**

* Configure um serviço de nuvem separado para cada modelo de nó do Azure que pretende criar. No entanto, pode utilizar a mesma conta de armazenamento para vários modelos de nó.
* Recomendamos que localizar o serviço em nuvem e a conta de armazenamento para a implementação na mesma região do Azure.

## <a name="step-2-configure-an-azure-management-certificate"></a>Passo 2: Configurar um certificado de gestão do Azure
Para adicionar nós do Azure como recursos de computação, é necessário um certificado de gestão no nó principal e carregar um correspondentes de certificados para a subscrição do Azure utilizada para a implementação.

Para este cenário, pode escolher o **certificado de gestão do Azure de HPC predefinido** que HPC Pack instala e configura automaticamente no nó principal. Este certificado é útil para implementações de prova do conceito e fins de teste. Para utilizar este certificado, carregue o ficheiro C:\Program Files\Microsoft HPC Pack 2012\Bin\hpccert.cer do nó principal do VM para a subscrição. Para carregar o certificado no [portal do Azure](https://portal.azure.com):

1. Clique em **subscrições** > *your_subscription_name*.

2. Clique em **certificados de gestão** > **carregar**.

Para obter opções adicionais para configurar o certificado de gestão, consulte [cenários para configurar o certificado de gestão do Azure para o Azure Burst implementações](http://technet.microsoft.com/library/gg481759.aspx).

## <a name="step-3-deploy-azure-nodes-to-the-cluster"></a>Passo 3: Implementar nós do Azure para o cluster
Os passos para adicionar e iniciar nós do Azure neste cenário, geralmente, são os mesmos que os passos com um nó principal no local. Para obter mais informações, consulte as secções seguintes [passos para implementar nós do Azure com o Microsoft HPC Pack](https://technet.microsoft.com/library/gg481758.aspx):

* Criar um modelo de nó do Azure
* Adicionar nós do Azure para o cluster Windows HPC
* Inicie nós (aprovisionar) do Azure

Depois de adicionar e iniciar os nós, estas estão prontas para que possa utilizar para executar tarefas de cluster.

Se tiver problemas ao implementar nós do Azure, consulte o artigo [resolver problemas com implementações de nós do Azure com o Microsoft HPC Pack](http://technet.microsoft.com/library/jj159097.aspx).

## <a name="next-steps"></a>Passos seguintes
* Para utilizar um tamanho de instância de computação intensiva para os nós de rajada, consulte as considerações no [tamanhos de VM de computação de elevado desempenho](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Se pretender automaticamente aumentar ou diminuir os recursos, de acordo com a carga de trabalho do cluster de computação do Azure, consulte [automaticamente aumentar e diminuir a recursos de computação do Azure num cluster HPC Pack](hpcpack-cluster-node-autogrowshrink.md).

<!--Image references-->
[burst]: ./media/hpcpack-cluster-node-burst/burst.png
