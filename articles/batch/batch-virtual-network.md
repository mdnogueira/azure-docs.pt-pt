---
title: Aprovisionar o conjunto do Azure Batch numa rede virtual | Microsoft Docs
description: "Pode criar um conjunto do Batch numa rede virtual para que nós de computação podem comunicar de forma segura com outras VMs na rede, tais como um servidor de ficheiros."
services: batch
author: v-dotren
manager: timlt
ms.service: batch
ms.topic: article
ms.date: 10/16/2017
ms.author: v-dotren
ms.openlocfilehash: f34647afc600b72704859952d0a40edad4a3b40f
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="create-an-azure-batch-pool-in-a-virtual-network"></a>Criar um conjunto do Azure Batch numa rede virtual


Quando cria um conjunto do Azure Batch, pode aprovisionar o agrupamento de uma sub-rede de um [rede virtual do Azure](../virtual-network/virtual-networks-overview.md) (VNet) que especificou. Este artigo explica como configurar um conjunto do Batch numa VNet. 



## <a name="why-use-a-vnet"></a>Porquê utilizar uma VNet?


Um conjunto do Azure Batch tem definições para permitir que nós de computação para comunicar entre si - por exemplo, para executar tarefas de várias instâncias. Estas definições não necessitam de uma VNet separada. No entanto, por predefinição, os nós não consegue comunicar com máquinas virtuais que não fazem parte do conjunto do Batch, tal como um servidor de licenciamento ou de um servidor de ficheiros. Para permitir que nós de computação do conjunto comunicar de forma segura com outras máquinas virtuais ou com uma rede no local, pode aprovisionar o agrupamento de uma sub-rede de uma VNet do Azure. 



## <a name="prerequisites"></a>Pré-requisitos

* **Autenticação**. Para utilizar uma VNet do Azure, a API do cliente do Batch tem de utilizar a autenticação do Azure Active Directory. O suporte do Azure Batch para o Azure AD está documentado em [Autenticar soluções de serviço do Batch com o Active Directory](batch-aad-auth.md). 

* **Uma VNet do Azure**. Para preparar uma VNet com uma ou mais sub-redes seguinte com antecedência, pode utilizar o portal do Azure, Azure PowerShell, a Interface de linha de comandos (CLI do Azure) ou outros métodos. Para criar uma VNet com base no Azure Resource Manager, consulte [criar uma rede virtual com várias sub-redes](../virtual-network/virtual-networks-create-vnet-arm-pportal.md). Para criar uma VNet clássica, consulte [criar uma rede virtual (clássica) com várias sub-redes](../virtual-network/create-virtual-network-classic.md).

### <a name="vnet-requirements"></a>Requisitos de VNet
[!INCLUDE [batch-virtual-network-ports](../../includes/batch-virtual-network-ports.md)]
    
## <a name="create-a-pool-with-a-vnet-in-the-portal"></a>Criar um conjunto com uma VNet no portal

Uma vez que criou a VNet e atribuída uma sub-rede, pode criar um conjunto do Batch com essa VNet. Siga estes passos para criar um conjunto a partir do portal do Azure: 



1. No portal do Azure, navegue para a sua conta do Batch. Esta conta tem de ser na mesma subscrição e região como o grupo de recursos que contém a VNet que pretende utilizar. 
2. No **definições** janela no lado esquerdo, selecione o **agrupamentos** item de menu.
3. No **agrupamentos** janela, selecione o **adicionar** comando.
4. No **adicionar conjunto** janela, selecione a opção que pretende utilizar o **tipo de imagem** pendente. 
5. Selecione o correto **oferta/publicador/Sku** para a sua imagem personalizada.
6. Especifique o restante necessário as definições, incluindo o **tamanho de nó**, **dedicados de nós de destino**, e **baixa nós prioridade**, bem como qualquer pretendido definições opcionais.
7. No **rede Virtual**, selecione a rede virtual e a sub-rede que pretende utilizar.
  
  ![Adicionar o conjunto com a rede virtual](./media/batch-virtual-network/add-vnet-pool.png)

## <a name="user-defined-routes-for-forced-tunneling"></a>Rotas definidas pelo utilizador para a imposição do túnel

Poderá ter requisitos na sua organização para o tráfego vinculado à Internet de redirecionamento (forçado) da sub-rede voltar à sua localização no local para inspeção e registo. Pode ativou a imposição do túnel para as sub-redes existentes na sua VNet. 

Para garantir que os nós de computação do conjunto de lote do Azure funcionam numa VNet que tem imposição do túnel ativada, tem de adicionar o seguinte [rotas definidas pelo utilizador](../virtual-network/virtual-networks-udr-overview.md) para essa sub-rede:

* O serviço Batch necessita para comunicar connosco de computação do conjunto para agendar tarefas. Para ativar esta comunicação, adicione uma rota definida pelo utilizador para cada endereço IP utilizado pelo serviço Batch na região onde existe a sua conta do Batch. Para obter a lista de endereços IP do serviço Batch, contacte o suporte do Azure.

* Certifique-se de que o tráfego de saída ao Storage do Azure (especificamente, os URLs do formulário `<account>.table.core.windows.net`, `<account>.queue.core.windows.net`, e `<account>.blob.core.windows.net`) não é bloqueada através do seu dispositivo de rede no local.

Quando adiciona uma rota definida pelo utilizador, definir a rota de cada prefixo de endereço IP do Batch relacionado e defina **próximo salto tipo** para **Internet**. Consulte o exemplo seguinte:

![Rota definida pelo utilizador](./media/batch-virtual-network/user-defined-route.png)

## <a name="next-steps"></a>Passos seguintes

- Para uma descrição geral aprofundada do Batch, consulte [paralelo em grande escala desenvolver soluções com o Batch de computação](batch-api-basics.md).
- Para obter mais informações sobre como criar uma rota definida pelo utilizador, consulte [criar uma rota definida pelo utilizador - portal do Azure](../virtual-network/create-user-defined-route-portal.md).
