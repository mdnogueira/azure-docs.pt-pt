---
title: "MATLAB clusters em máquinas virtuais | Microsoft Docs"
description: "Utilize máquinas virtuais do Microsoft Azure para criar clusters de servidor de computação MATLAB distribuídas para executar intensivas de computação paralelas MATLAB cargas de trabalho"
services: virtual-machines-windows
documentationcenter: 
author: mscurrell
manager: timlt
editor: 
ms.assetid: e9980ce9-124a-41f1-b9ec-f444c8ea5c72
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Windows
ms.workload: infrastructure-services
ms.date: 05/09/2016
ms.author: markscu
ms.openlocfilehash: 177f8a61487130e718e3e6cfb779b17a3ed8ed69
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2017
---
# <a name="create-matlab-distributed-computing-server-clusters-on-azure-vms"></a>Criar clusters de servidor de informática distribuída da MATLAB em VMs do Azure
Utilize máquinas virtuais do Microsoft Azure para criar um ou mais clusters de servidor de computação MATLAB distribuídas para executar as intensivas de computação paralelas MATLAB cargas de trabalho. Instalar o software de servidor de informática distribuída da MATLAB numa VM para utilizar como uma imagem de base e utilizar um modelo de início rápido do Azure ou do script do PowerShell do Azure (disponível no [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster)) para implementar e gerir o cluster. Após a implementação, ligue para o cluster para executar as cargas de trabalho.

## <a name="about-matlab-and-matlab-distributed-computing-server"></a>Sobre MATLAB e Server informática de MATLAB distribuído
O [MATLAB](http://www.mathworks.com/products/matlab/) plataforma está otimizada para resolver problemas de engenharia e científicos. MATLAB os utilizadores com simulações de larga escala e tarefas de processamento de dados podem utilizar paralelo MathWorks informática produtos para acelerar as respetivas cargas de trabalho de computação intensiva, tirando partido dos clusters de computação e os serviços de grelha. [Paralela caixa de ferramentas de informática](http://www.mathworks.com/products/parallel-computing/) permite que os utilizadores MATLAB parallelize aplicações e tirar partido de processadores de vários núcleos, GPUs e clusters de computação. [Servidor de informática distribuída da MATLAB](http://www.mathworks.com/products/distriben/) permite aos utilizadores MATLAB utilizar vários computadores num cluster de computação.

Através da utilização de máquinas virtuais do Azure, pode criar clusters MATLAB distribuídas Server informática que tenham as mesmas mecanismos disponíveis ao submeter trabalho paralelo como clusters no local, tais como interativa, tarefas do batch, tarefas independentes e comunicar tarefas. Utilizar o Azure em conjunto com a plataforma de MATLAB tem muitas vantagens em comparação com o aprovisionamento e utilizar tradicional hardware no local: tamanhos de um intervalo de máquina virtual, a criação de clusters a pedido para paga apenas os recursos de computação utilize e a capacidade para testar modelos à escala.  

## <a name="prerequisites"></a>Pré-requisitos
* **Computador cliente** -, terá de um computador de cliente baseados em Windows para comunicar com o Azure e o cluster de servidor de informática distribuída da MATLAB após a implementação.
* **O Azure PowerShell** -consulte [como instalar e configurar o Azure PowerShell](/powershell/azure/overview) para instalá-lo no seu computador cliente.
* **Subscrição do Azure** -se não tiver uma subscrição, pode criar um [conta gratuita](https://azure.microsoft.com/free/) em apenas alguns minutos. Para clusters maiores, considere uma subscrição pay as you go ou outras opções de compra.
* **quota de vCPUs** -poderá ter de aumentar a quota de vCPU para implementar um cluster de grandes dimensões ou mais de um cluster de servidor de informática distribuída da MATLAB. Para aumentar uma quota [abrir um pedido de suporte ao cliente online](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) , sem encargos.
* **Licenças MATLAB, caixa de ferramentas de computação paralelas e MATLAB distribuídas Server informática** -os scripts partem do princípio de que o [MathWorks alojado do License Manager](http://www.mathworks.com/products/parallel-computing/mathworks-hosted-license-manager/) é utilizada para todas as licenças.  
* **Software MATLAB distribuídas Server informática** -será instalado numa VM que será utilizada como a imagem de VM base para as VMs do cluster.

## <a name="high-level-steps"></a>Passos de nível elevados
Para utilizar máquinas virtuais do Azure para os clusters de servidor de informática distribuída da MATLAB, são necessários os seguintes passos de alto nível. Instruções detalhadas são na documentação que acompanha o modelo de início rápido e scripts no [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster).

1. **Criar uma imagem VM base**  

   * Transfira e instale o software de servidor de informática distribuída da MATLAB para esta VM.

     > [!NOTE]
     > Este processo poderá demorar algumas horas, mas apenas é necessário fazê-lo, uma vez para cada versão do MATLAB utilizar.   
     >
     >
2. **Criar um ou mais clusters**  

   * Utilize o script do PowerShell fornecido ou o modelo de início rápido para criar um cluster a partir da imagem VM base.   
   * Gerir clusters utilizando o script do PowerShell fornecido que lhe permite listar, pausar, retomar e eliminar os clusters.

## <a name="cluster-configurations"></a>Configurações de cluster
Atualmente, o script de criação do cluster e o modelo permitem-lhe criar uma única topologia MATLAB distribuídas Server informática. Se pretender, crie um ou mais clusters adicionais, com cada cluster ter um número diferente de trabalho VMs, utilizando tamanhos de VM diferentes e assim sucessivamente.

### <a name="matlab-client-and-cluster-in-azure"></a>Cliente MATLAB e cluster no Azure
O nó de cliente MATLAB, nó de Programador de tarefas MATLAB e MATLAB distribuídas Server informática "" nós de trabalho são todos configurados como VMs do Azure numa rede virtual, conforme mostrado na figura seguinte.


* Para utilizar o cluster, estabelecer ligação ao ambiente de trabalho remoto para o nó de cliente. O nó cliente executa o cliente MATLAB.
* O nó de cliente tem uma partilha de ficheiros que pode ser acedida por todos os funcionários.
* Gestor de licenças alojado MathWorks é utilizado para as verificações de licença de todo o software MATLAB.
* Por predefinição, é criado um trabalho do servidor de informática distribuída da MATLAB por vCPU na função de trabalho VMs, mas pode especificar qualquer número.

## <a name="use-an-azure-based-cluster"></a>Utilizar um Cluster baseado no Azure
Como com outros tipos de clusters de servidor de informática distribuída da MATLAB, tem de utilizar o Gestor de clusters de perfil no cliente MATLAB (no cliente VM) para criar um perfil de cluster MATLAB Programador de tarefas.

![Gestor de clusters de perfil](./media/matlab-mdcs-cluster/cluster_profile_manager.png)

## <a name="next-steps"></a>Passos seguintes
* Para obter instruções detalhadas implementar e gerir clusters de servidor de informática distribuída da MATLAB no Azure, consulte o [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster) repositório que contém os modelos e scripts.
* Vá para o [MathWorks site](http://www.mathworks.com/) para documentação detalhada para MATLAB e MATLAB distribuídas Server informática.
