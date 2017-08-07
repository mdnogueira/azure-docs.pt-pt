---
title: "Utilizar o serviço Azure Batch Rendering para compor na cloud | Microsoft Docs"
description: "Componha tarefas em máquinas virtuais do Azure diretamente no Maya com o modelo de faturação de pagamento por utilização."
services: batch
author: tamram
manager: timlt
ms.service: batch
ms.topic: hero-article
ms.date: 07/31/2017
ms.author: tamram
ms.translationtype: HT
ms.sourcegitcommit: 7bf5d568e59ead343ff2c976b310de79a998673b
ms.openlocfilehash: 4d22f92cafdbceee5213361d6d2b2f38904d12c6
ms.contentlocale: pt-pt
ms.lasthandoff: 08/01/2017

---

# <a name="get-started-with-the-batch-rendering-service"></a>Introdução ao serviço Batch Rendering

O serviço Azure Batch Rendering oferece capacidades de composição à escala da cloud num modelo de faturação de pagamento por utilização. Processa o agendamento e a colocação em fila de trabalhos, a gestão de falhas e repetições e o dimensionamento automático dos seus trabalhos de composição. O Batch Rendering suporta Autodesk Maya, 3ds Max e Arnold, sendo que vão ser suportadas mais aplicações brevemente. O plug-in do Batch para Maya 2017 faz com que seja mais fácil começar a compor trabalhos no Azure diretamente a partir do seu computador. 

## <a name="supported-applications"></a>Aplicações suportadas

Atualmente, o serviço Batch Rendering suporta as aplicações seguintes:

- Autodesk Maya
- Autodesk 3ds Max
- Autodesk Arnold

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar o serviço Batch Rendering, precisa de:

- Uma [conta do Azure](https://azure.microsoft.com/free/). 
- **Uma conta do Azure Batch.** Para obter orientações para a criação de uma conta do Batch no portal do Azure, veja [Criar uma conta do Batch com portal do Azure](batch-account-create-portal.md).
- **Uma conta do Armazenamento do Azure.** Os recursos utilizados para o trabalho de composição são armazenados Armazenamento do Azure. Pode criar uma conta de armazenamento automaticamente quando configurar a sua conta do Batch. Também pode utilizar uma conta de armazenamento já existente. Para saber mais sobre as contas de Armazenamento, veja [Acerca das contas do Storage do Azure](https://docs.microsoft.com/azure/storage/storage-create-storage-account).

Para utilizar o plug-in do Batch para Maya, precisa do:

- **Maya 2017**
- **Arnold for Maya**

Também pode utilizar o [portal do Azure](https://portal.azure.com) para criar conjuntos de máquinas virtuais que estão pré-configuradas com Maya, 3ds Max e Arnold. Pode utilizar o portal para monitorizar trabalhos e diagnosticar tarefas falhadas ao transferir os registos de aplicações e ligar-se remotamente às VMs individuais através de RDP ou SSH.

## <a name="basic-batch-concepts"></a>Conceitos básicos do Batch

Antes de começar a utilizar o serviço Batch Rendering, é útil estar familiarizado com alguns conceitos do Batch, incluindo nós de computação, conjuntos e trablhos. Para saber mais sobre o Azure Batch em geral, veja [Executar cargas de trabalho paralelas intrinsecamente com o Batch](batch-technical-overview.md).

### <a name="pools"></a>Conjuntos

O Batch é um serviço de plataforma para execução de trabalhos com computação intensiva, como a composição, em **conjuntos** de **nós de computação**. Cada nó de computação num conjunto é uma máquina virtual do Azure (VM), que executa o Linux ou o Windows. 

Para obter mais informações sobre os conjuntos e os nós de computação do Batch, veja as secções [Conjunto](batch-api-basics.md#pool) e [Nó de computação](batch-api-basics.md#compute-node) do artigo [Desenvolver soluções de computação paralelas em grande escala com o Batch](batch-api-basics.md).

### <a name="jobs"></a>Tarefas

Um **trabalho** do Batch é uma coleção de tarefas que são executadas nos nós de computação num conjunto. Quando submete um trabalho de composição, o Batch divide-o em tarefas e distribui-as para os nós de computação do conjunto, para serem executados.

Para obter mais informações sobre os trabalhos do Batch, veja a secção [Tarefa](batch-api-basics.md#job) do artigo [Desenvolver soluções de computação paralelas em grande escala com o Batch](batch-api-basics.md).

## <a name="use-the-batch-plug-in-for-maya-to-submit-a-render-job"></a>Utilizar o plug-in do Batch para Maya submeter um trabalho de composição

Com o plug-in do Batch para Maya, pode submeter um trabalho para o serviço Batch Rendering diretamente a partir do Maya. As secções seguintes descrevem como configurar o trabalho no plug-in e, depois, submetê-lo. 

### <a name="load-the-batch-plug-in-in-maya"></a>Carregar o plug-in do Batch para Maya

O plug-in do Batch está disponível no [GitHub](https://github.com/Azure/azure-batch-maya/releases). Deszipe o arquivo num diretório à sua escolha. Pode carregar o plug-in diretamente a partir do diretório *azure_batch_maya*.

Para carregar o plug-in no Maya:

1. Execute o Maya.
2. Abra **Window** (Janela)  > **Settings/Preferences** (Definições/Preferências) > **Plug-in Manager** (Gestor de Plug-ins).
3. Clique em **Browse** (Procurar).
4. Navegue para *azure_batch_maya/plug-in/AzureBatch.py* e selecione-o.

### <a name="authenticate-access-to-your-batch-and-storage-accounts"></a>Autenticar o acesso às suas contas do Batch e de Armazenamento

Para utilizar o plug-in, tem de se autenticar com as chaves das contas do Azure Batch e do Armazenamento do Azure. Para obter as chaves das contas:

1. Apresente o plug-in do Maya e selecione os separador **Config** (Configuração).
2. Navegue para o [portal do Azure](https://portal.azure.com).
3. Selecione **Contas do Batch**, no menu do lado esquerdo. Se necessário, clique em **Mais Serviços** e filtre por _Batch_.
4. Localize a conta do Batch pretendida na lista.
5. Selecione o item de menu **Chaves** para ver o nome, o URL e as chaves de acesso da conta:
    - Cole o URL da conta do Batch no campo **Serviço** do plug-in do Batch.
    - Cole o nome da conta no campo **Conta do Batch**.
    - Cole a chave da conta primária no campo **Chave do Batch**.
7. Selecione Contas de Armazenamento, no menu do lado esquerdo. Se necessário, clique em **Mais Serviços** e filtre por _Armazenamento_.
8. Localize a conta de Armazenamento pretendida na lista.
9. Selecione o item de menu **Chaves de Acesso** para apresentar o nome e as chaves da conta de Armazenamento.
    - Cole o nome da conta de Armazenamento no campo **Conta de Armazenamento** do plug-in do Batch.
    - Cole a chave da conta primária no campo **Chave de Armazenamento**.
10. Clique em **Autenticar** para se certificar de que o plug-in pode aceder a ambas as contas.

Depois de se ter autenticado com êxito, o plug-in define o campo de estado como **Autenticado**: 

![Autenticar as contas do Batch e de Armazenamento](./media/batch-rendering-service/authentication.png)

### <a name="configure-a-pool-for-a-render-job"></a>Configurar um conjunto para um trabalho de composição

Depois de ter autenticado as contas do Batch e de Armazenamento, configure um conjunto para o trabalho de composição. O plug-in guarda as suas seleções entre sessões. Assim que tiver configurado a sua configuração preferencial, não terá de modificá-la, a menos que sofra alterações.

As secções seguintes descrevem as opções à disposição e que estão disponíveis no separador **Submeter**.

#### <a name="specify-a-new-or-existing-pool"></a>Especificar um conjunto novo ou existente

Para especificar um conjunto no qual pretende executar o trabalho de composição, selecione o separador **Submeter**. Este separador oferece opções para criar um conjunto novo ou um já existente:

- Pode **aprovisionar automaticamente um conjunto para este trabalho** (a opção predefinida). Quando escolhe esta opção, o Batch cria o conjunto exclusivamente para o trabalho atual e elimina-o automaticamente quando o trabalho de composição é concluído. Esta opção é melhor se só tiver um trabalho de composição para concluir.
- Pode **reutilizar um conjunto persistente existente**. Se tiver um conjunto existente que esteja inativo, pode especificá-lo para executar o trabalho de composição, selecionando-o na lista pendente. Reutilizar um conjunto persistente existente poupa o tempo necessário para aprovisionar o conjunto.  
- Pode **criar um conjunto persistente novo**. Escolher esta opção cria um conjunto novo para executar o trabalho. Não elimina o conjunto após a conclusão do trabalho, para que o possa reutilizar em trabalhos futuros. Selecione esta opção se precisar de executar trabalhos de composição continuamente. Nos trabalhos subsequentes, pode selecionar **reutilizar um conjunto persistente existente** para utilizar o conjunto persistente que criou para o primeiro trabalho.

![Especificar o conjunto, a imagem do SO, o tamanho da VM e o licenciamento](./media/batch-rendering-service/submit.png)

Para obter mais informações sobre como são acumulados os encargos para as VMs do Azure, veja [FAQ dos Preços do Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#faq) e [FAQ dos Preços do Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/#faq).

#### <a name="specify-the-os-image-to-provision"></a>Especificar a imagem do SO a aprovisionar

Pode especificar o tipo de imagem do SO a utilizar para aprovisionar os nós de computação do conjunto no separador **Env** (Ambiente). Atualmente, o Batch suporta as opções de imagens seguintes para os trabalhos de composição:

|Sistema Operativo  |Imagem  |
|---------|---------|
|Linux     |Pré-visualização do Batch no CentOS |
|Windows     |Pré-visualização do Batch no Windows |

#### <a name="choose-a-vm-size"></a>Selecionar um tamanho de VM

Pode especificar o tamanho da VM no separador **Env** (Ambiente). Para obter mais informações sobre os tamanhos de VMs disponíveis, veja [Linux VM sizes in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes) (Tamanhos de VMs do Linux no Azure) e [Windows VM sizes in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) (Tamanhos de VMs do Windows no Azure). 

![Especificar a imagem do SO e o tamanho da VM no separador Env (Ambiente)](./media/batch-rendering-service/environment.png)

#### <a name="specify-licensing-options"></a>Especificar as opções de licenciamento

Pode especificar as licenças que pretende utilizar no separador **Env** (Ambiente). As opções incluem:

- **Maya**, que está ativado por predefinição.
- **Arnold**, que está ativado se o Arnold for detetado como sendo o motor de composição ativo do Maya.

 Se quiser utilizar a sua própria licença para as composições, pode configurar o ponto final da mesma ao adicionar as variáveis de ambiente adequadas à tabela. Se o fizer, confirme que anula a seleção das opções de licenciamento predefinidas.

> [!IMPORTANT]
> A utilização das licenças é cobrada enquanto as VMs estiverem a ser executadas no conjunto, mesmo que não estejam a ser utilizadas atualmente para a composição. Para evitar custos adicionais, navegue para o separador **Pools** (Conjuntos) e redimensione o conjunto para 0 nós, até estar pronto para executar outro trabalho de composição. 
>
>

#### <a name="manage-persistent-pools"></a>Gerir conjuntos persistentes

Pode gerir um conjunto persistente existente no separador **Pools** (Conjuntos). A seleção de um conjunto na lista apresenta o estado atual do mesmo.

No separador **Pools**, também pode eliminar o conjunto e redimensionar o número de VMs no mesmo. Pode redimensionar um conjunto para 0 nós, de modo a evitar incorrer em custos entre cargas de trabalho.

![Ver, redimensionar e eliminar conjuntos](./media/batch-rendering-service/pools.png)

### <a name="configure-a-render-job-for-submission"></a>Configurar um trabalho de composição para submissão

Depois de especificar os parâmetros do conjunto que vai executar o trabalho de composição, configure o trabalho propriamente dito. 

#### <a name="specify-scene-parameters"></a>Especificar os parâmetros de cenas

O plug-in do Batch deteta o motor de composição que está a utilizar no Maya num dado momento e apresenta as definições de composição adequadas no separador **Submit** (Submeter). Estas definições incluem o fotograma de início, o fotograma de fim, o prefixo de saída e o passo do fotograma. Pode substituir as definições de composição do ficheiro de cena mediante a especificação de definições diferentes no plug-in. As alterações que fizer às definições do plug-in não são aplicadas às definições de composição do ficheiro de cena, pelo que pode fazer alterações trabalho a trabalho sem ter de voltar a carregar o ficheiro de cena.

O plug-in avisa-o se o motor de composição que selecionou no Maya não for suportado.

Se carregar uma cena nova enquanto o plug-in estiver aberto, clique no botão **Refresh** (Atualizar) para garantir que as definições são atualizadas.

#### <a name="resolve-asset-paths"></a>Resolver caminhos de recursos

Quando carrega o plug-in, este procura referências a ficheiros externos no ficheiro de cena. Estas referências são apresentadas no separador **Assets** (Recursos). Se não for possível resolver um caminho referenciado, o plug-in tenta localizar o ficheiro em algumas localizações predefinidas, incluindo:

- A localização do ficheiro de cena 
- O diretório _sourceimages_ do projeto atual.
- O diretório de trabalho atual. 

Se, ainda assim, o recurso não for encontrado, é apresentado com um ícone de aviso:

![Os recursos em falta são apresentados com um ícone de aviso](./media/batch-rendering-service/missing_assets.png)

Se souber onde está localizada uma referência de ficheiro não resolvida, pode clicar no ícone de aviso para que lhe seja pedido que adicione um caminho de pesquisa. Depois, o plug-in utiliza este caminho de pesquisa para tentar resolver os eventuais recursos em falta. Pode adicionar qualquer número de caminhos de pesquisa adicionais.

Quando uma referência for resolvida, é apresentada com um ícone verde claro:

![Os recursos resolvidos mostram um ícone verde claro](./media/batch-rendering-service/found_assets.png)

Se a sua cena precisar de outros ficheiros que o plug-in não detetou, pode acrescentar ficheiros ou diretórios adicionais. Utilize os botões **Add Files** (Adicionar Ficheiros) e **Add Directory** (Adicionar Diretórios). Se carregar uma cena nova enquanto o plug-in estiver aberto, não se esqueça de clicar em **Refresh** para atualizar as referências da cena.

#### <a name="upload-assets-to-an-asset-project"></a>Carregar recursos para um projeto de recursos

Quando submete um trabalho de composição, os ficheiros referenciados apresentados no separador **Assets** são carregados automaticamente para o Armazenamento do Azure como projetos de recursos. Também pode carregar os ficheiros de recursos de forma independente do trabalho de composição com o botão **Upload** (Carregar), no separador **Assets** Por predefinição, o nome do projeto de recursos é o mesmo do projeto do Maya atual e é especificado no campo **Project** (Projeto). Quando os ficheiros de recursos são carregados, a estrutura de ficheiros local é preservada. 

Depois de carregados, os recursos podem ser referenciados por qualquer número de trabalhos de composição. Todos os recursos carregados estão disponíveis para qualquer trabalho que referencie o projeto de recursos, quer estejam ou não incluídos na cena. Para alterar o projeto de recursos referenciado pelo seu próximo trabalho, altere o nome no campo **Project**, no separador **Assets**. Se quiser excluir algum ficheiro referenciado do carregamento, anule a seleção do mesmo com o botão verde junto à lista.

#### <a name="submit-and-monitor-the-render-job"></a>Submeter e monitorizar o trabalho de composição

Depois de ter configurado o trabalho de composição no plug-in, clique no botão **Submit Job** (Submeter Trabalho), no separador **Submit**, para submeter o trabalho para o Batch:

![Submeter o trabalho de composição](./media/batch-rendering-service/submit_job.png)

Pode monitorizar um trabalho que esteja em curso a partir do separador **Jobs** (Trabalhos) do plug-in. Selecione um trabalho na lista para apresentar o estado atual do mesmo. Também pode utilizar este separador para cancelar e eliminar trabalhos, bem como para transferir as saídas e os registos de composição. 

Para transferir saídas, modifique o campo **Outputs** (Saídas) para definir o diretório de destino pretendido. Clique no ícone de roda dentada para iniciar um processo em segundo plano que observa o trabalho e transfere as saídas à medida que o processo avança: 

![Ver o estado do trabalho e transferir saídas](./media/batch-rendering-service/jobs.png)

Pode fechar o Maya sem interromper o processo de transferência.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o Batch, veja [Executar cargas de trabalho paralelas intrinsecamente com o Batch](batch-technical-overview.md).
