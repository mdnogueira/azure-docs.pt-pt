---
title: Aprovisionar conjuntos do Azure Batch de imagens personalizadas | Microsoft Docs
description: "Pode criar um lote de nós que contêm o software e os dados que precisa para a sua aplicação de computação do conjunto a partir de uma imagem personalizada para aprovisionar. Imagens personalizadas são uma forma eficaz para configurar nós de computação para executar as cargas de trabalho do Batch."
services: batch
author: v-dotren
manager: timlt
ms.service: batch
ms.topic: article
ms.date: 10/11/2017
ms.author: v-dotren
ms.openlocfilehash: d62abd673f89fd51edba721119d1680762a60c76
ms.sourcegitcommit: 5d772f6c5fd066b38396a7eb179751132c22b681
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2017
---
# <a name="use-a-managed-custom-image-to-create-a-pool-of-virtual-machines"></a>Utilizar uma imagem personalizada gerida para criar um conjunto de máquinas virtuais 

Quando cria um conjunto do Azure Batch com a configuração de Máquina Virtual, especifique uma imagem de VM que fornece o sistema operativo para cada nó de computação no conjunto. Pode criar um conjunto de máquinas virtuais com uma imagem do Azure Marketplace ou com uma imagem personalizada (uma imagem de VM ter criado e configurado por si). A imagem personalizada tem de ser um *imagem gerida* recursos na mesma subscrição do Azure e região da conta de Batch.

## <a name="why-use-a-custom-image"></a>Porquê utilizar uma imagem personalizada?
Quando fornecer uma imagem personalizada, tem controlo sobre a configuração do sistema operativo e o tipo de sistema operativo e os discos de dados a ser utilizado. A imagem personalizada pode incluir aplicações e dados de referência que fiquem disponíveis em todos os nós do conjunto de Batch, assim como terem sido aprovisionados.

Utilizar uma imagem personalizada poupa tempo no preparar nós de computação do conjunto para executar a carga de trabalho do Batch. Apesar de poder utilizar uma imagem do Azure Marketplace e instalar software em cada nó de computação após o aprovisionamento, utilizar uma imagem personalizada poderá ser mais eficiente.

Utilizar uma imagem personalizada configurada para o seu cenário, pode fornecer várias vantagens:

- **Configurar o sistema operativo (SO)**. Pode efetuar uma configuração especial do sistema operativo no disco do sistema operativo da imagem personalizada. 
- **Aplicações de pré-instalação.** Pode criar uma imagem personalizada com aplicações pré-instaladas no disco do SO, que é mais eficiente e menos propensas ao erro de instalação de aplicações após o aprovisionamento de nós de computação com o StartTask.
- **Poupar tempo de reinício em VMs.** Instalação da aplicação requer, normalmente, reiniciar a VM, que é um processo demorado. Pode poupar tempo de reinício através da pré-instalação de aplicações. 
- **Copie muito grandes quantidades de dados uma vez.** Pode fazer parte de dados estáticos da imagem personalizada gerida ao copiá-los para os discos de dados de uma imagem gerida. Isto só tem de ser feita uma vez e disponibiliza a cada nó do conjunto de dados.
- **Escolha dos tipos de disco.** Pode criar uma imagem personalizada gerida a partir de um VHD, a partir de um disco gerido de uma VM do Azure, um instantâneo destes discos ou a seus próprios instalação Linux ou do Windows que tiver configurado. Tem a opção de utilizar o premium storage para o disco do SO e o disco de dados.
- **Aumente os conjuntos de qualquer dimensão.** Quando utiliza uma imagem personalizada gerida para criar um conjunto, o conjunto pode crescer para qualquer dimensão que solicitar. Não é necessário efetuar cópias de blob de imagem VHDs para acomodar o número de VMs. 


## <a name="prerequisites"></a>Pré-requisitos

- **Um recurso de imagem gerido**. Para criar um conjunto de máquinas virtuais utilizando uma imagem personalizada, terá de criar um recurso de imagem gerido na mesma subscrição do Azure e região da conta de Batch. Para obter opções preparar uma imagem gerida, consulte a secção seguinte.
- **Autenticação do Azure Active Directory (AAD)**. O cliente de Batch API tem de utilizar a autenticação do AAD. Suporte de lote do Azure para o AAD está documentado na [soluções de serviço de Batch de autenticar com o Active Directory](batch-aad-auth.md).

    
## <a name="prepare-a-custom-image"></a>Preparar uma imagem personalizada
Pode preparar uma imagem gerida a partir de um VHD, de uma VM do Azure com discos geridos ou de um instantâneo VM. 

Quando preparar a imagem, tenha em consideração os seguintes pontos:

* Confirme que a imagem de SO base que vai utilizar para aprovisionar o conjunto do Batch não tem nenhuma extensão do Azure pré-instalada, como a extensão Custom Script. Se a imagem tiver uma extensão pré-instalada, o Azure poderá encontrar problemas durante a implementação da VM.
* Certifique-se de que a imagem do SO base que fornece utiliza unidade temporária predefinida. O agente de nó do Batch atualmente espera unidade temporária predefinida.
* Não é possível eliminar o recurso de imagem gerido referenciado por um conjunto do Batch para a duração do conjunto. Se o recurso de imagem gerido for eliminado, em seguida, o conjunto não é possível crescer qualquer ainda mais. 

### <a name="to-create-a-managed-image"></a>Para criar uma imagem gerida
Pode utilizar qualquer existente preparado Windows ou Linux disco do sistema operativo para criar uma imagem gerida. Por exemplo, se pretender utilizar uma imagem do local, em seguida, carregue o disco local para uma conta de armazenamento do Azure que está a ser a mesma subscrição e região da conta do Batch utilizar o AzCopy ou outra ferramenta de carregamento. Para obter passos detalhados para carregar um VHD e criar uma imagem gerida, consulte a documentação de orientação para [Windows](../virtual-machines/windows/upload-generalized-managed.md) ou [Linux](../virtual-machines/linux/upload-vhd.md) VMs.

Também pode preparar uma imagem gerida de um novo ou existente VM do Azure, ou um instantâneo VM. 

* Se estiver a criar uma nova VM, pode utilizar uma imagem do Azure Marketplace como a imagem de base para a imagem gerida e, em seguida, personalizá-lo. 

* Se planear capturar a imagem através do portal, certifique-se de que a VM é criada com um disco gerido. Esta é a predefinição de armazenamento ao criar uma VM.

* Assim que a VM está em execução, ligue ao mesmo através de DRP (para Windows) ou SSH (para Linux). Instalar obrigatoriamente qualquer software necessário ou copiar dados pretendido e, em seguida, generalize a VM.  

Para obter os passos generalizar uma VM do Azure e criar uma imagem gerida, consulte a documentação de orientação para [Windows](../virtual-machines/windows/capture-image-resource.md) ou [Linux](../virtual-machines/linux/capture-image.md) VMs.

Dependendo de como pretende criar um conjunto do Batch com a imagem, é necessário o seguinte identificador para a imagem:

* Se pretender criar um conjunto com a imagem com as APIs do Batch, o **ID de recurso** da imagem, que tem o formato `/subscriptions/xxxx-xxxxxx-xxxxx-xxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage`. 
* Se planeia utilizar o portal, o **nome** da imagem. 





## <a name="create-a-pool-from-a-custom-image-in-the-portal"></a>Criar um agrupamento de uma imagem personalizada no portal

Depois de guardar a imagem personalizada e conhecer o respetivo ID de recurso ou o nome, pode criar um conjunto do Batch a partir dessa imagem. Os passos seguintes mostram como criar um conjunto a partir do portal do Azure.

> [!NOTE]
> Se estiver a criar o conjunto utilizar uma das APIs do Batch, certifique-se de que a identidade que utilizar para autenticação do AAD tem permissões para o recurso de imagem. Consulte [soluções de serviço de Batch de autenticar com o Active Directory](batch-aad-auth.md).
>

1. No portal do Azure, navegue para a sua conta do Batch. Esta conta tem de ser na mesma subscrição e região como o grupo de recursos que contém a imagem personalizada. 
2. No **definições** janela no lado esquerdo, selecione o **agrupamentos** item de menu.
3. No **agrupamentos** janela, selecione o **adicionar** comando.
4. No **adicionar conjunto** janela, selecione **imagem personalizada (Linux/Windows)** do **tipo de imagem** pendente. Do **imagem de VM personalizada** lista pendente, selecione o nome de imagem (ditada abreviada do ID do recurso).
5. Selecione o correto **oferta/publicador/Sku** para a sua imagem personalizada.
6. Especifique o restante necessário as definições, incluindo o **tamanho de nó**, **dedicados de nós de destino**, e **baixa nós prioridade**, bem como qualquer pretendido definições opcionais.

    Por exemplo, para uma imagem personalizada do Centro de dados do Microsoft Windows Server 2016, o **adicionar conjunto** surge a janela conforme mostrado abaixo:

    ![Adicionar o conjunto de imagem personalizada do Windows](media/batch-custom-images/add-pool-custom-image.png)
  
Para verificar se um conjunto existente é baseado numa imagem personalizada, consulte o **sistema operativo** propriedade na secção de resumo de recurso do **conjunto** janela. Se o conjunto foi criado a partir de uma imagem personalizada, está definida como **imagem de VM personalizada**.

Todas as imagens personalizadas associadas um agrupamento são apresentadas no agrupamento de **propriedades** janela.
 
## <a name="next-steps"></a>Passos seguintes

- Para uma descrição geral aprofundada do Batch, consulte [paralelo em grande escala desenvolver soluções com o Batch de computação](batch-api-basics.md).
