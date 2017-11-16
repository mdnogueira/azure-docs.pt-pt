---
title: "Serviço de Composição do Azure Batch - composição à escala da cloud | Microsoft Docs"
description: "Componha tarefas em máquinas virtuais do Azure diretamente no Maya com o modelo de faturação de pagamento por utilização."
services: batch
author: v-dotren
manager: timlt
ms.service: batch
ms.topic: hero-article
ms.date: 09/14/2017
ms.author: danlep
ms.openlocfilehash: aab68b4275edc48e1726b26a7400bbfdd8d40d6f
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2017
---
# <a name="get-started-with-the-batch-rendering-service"></a>Introdução ao serviço Batch Rendering

O serviço Azure Batch Rendering oferece capacidades de composição à escala da cloud num modelo de faturação de pagamento por utilização. Processa o agendamento e a colocação em fila de trabalhos, a gestão de falhas e repetições e o dimensionamento automático dos seus trabalhos de composição. Suporta [Autodesk Maya](https://www.autodesk.com/products/maya/overview), [3ds Max](https://www.autodesk.com/products/3ds-max/overview), [Arnold](https://www.autodesk.com/products/arnold/overview) e [V-Ray](https://www.chaosgroup.com/vray/maya). O plug-in do Batch para Maya 2017 faz com que seja mais fácil começar a compor trabalhos no Azure diretamente a partir do seu computador.

Com Maya e 3ds Max, pode executar tarefas com a aplicação de computador [BatchLabs](https://github.com/Azure/BatchLabs) ou com a [CLI de Modelos do Batch](batch-cli-templates.md). Com a CLI do Azure Batch, pode executar trabalhos do Batch sem escrever código. Em vez disso, pode utilizar ficheiros de modelos para criar conjuntos, trabalhos e tarefas do Batch. Para obter mais informações, veja [Use Azure Batch CLI Templates and File Transfer](batch-cli-templates.md) (Utilizar Modelos da CLI do Azure Batch e a Transferência de Ficheiros).


## <a name="supported-applications"></a>Aplicações suportadas

Atualmente, o serviço Batch Rendering suporta as aplicações seguintes:

- Autodesk Maya
- Autodesk 3ds Max
- Autodesk Arnold for Maya
- Autodesk Arnold for 3ds Max
- Chaos Group V-Ray for Maya
- Chaos Group V-Ray for 3ds Max

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar o serviço Batch Rendering, precisa de:

- Uma [conta do Azure](https://azure.microsoft.com/free/).
- **Uma conta do Azure Batch.** Para obter orientações para a criação de uma conta do Batch no portal do Azure, veja [Criar uma conta do Batch com portal do Azure](batch-account-create-portal.md).
- **Uma conta do Armazenamento do Azure.** Os recursos utilizados para o trabalho de composição são armazenados Armazenamento do Azure. Pode criar uma conta de armazenamento automaticamente quando configurar a sua conta do Batch. Também pode utilizar uma conta de armazenamento já existente. Para saber mais sobre as contas de Armazenamento, veja [Acerca das contas do Storage do Azure](https://docs.microsoft.com/azure/storage/storage-create-storage-account).
- **BatchLabs** (opcional). O [BatchLabs](https://azure.github.io/BatchLabs) é uma ferramenta de cliente autónoma, gratuita e rica em funcionalidades para ajudar a criar, depurar e monitorizar aplicações do Azure Batch. Embora não seja necessário para utilizar o serviço de Composição, é uma opção útil para desenvolver e depurar as soluções do Batch.

Para utilizar o plug-in do Batch para Maya, precisa do:

- [Autodesk Maya 2017](https://www.autodesk.com/products/maya/overview).
- Um compositor suportado, como o Arnold for Maya ou o V-Ray for Maya.

## <a name="basic-batch-concepts"></a>Conceitos básicos do Batch

Antes de começar a utilizar o serviço Batch Rendering, é útil estar familiarizado com alguns conceitos do Batch, incluindo nós de computação, conjuntos e trablhos. Para saber mais sobre o Azure Batch em geral, veja [Executar cargas de trabalho paralelas intrinsecamente com o Batch](batch-technical-overview.md).

### <a name="pools"></a>Conjuntos

O Batch é um serviço de plataforma para execução de trabalhos com computação intensiva, como a composição, em **conjuntos** de **nós de computação**. Cada nó de computação num conjunto é uma máquina virtual do Azure (VM), que executa o Linux ou o Windows. 

Para obter mais informações sobre os conjuntos e os nós de computação do Batch, veja as secções [Conjunto](batch-api-basics.md#pool) e [Nó de computação](batch-api-basics.md#compute-node) do artigo [Desenvolver soluções de computação paralelas em grande escala com o Batch](batch-api-basics.md).

### <a name="jobs"></a>Tarefas

Um **trabalho** do Batch é uma coleção de tarefas que são executadas nos nós de computação num conjunto. Quando submete um trabalho de composição, o Batch divide-o em tarefas e distribui-as para os nós de computação do conjunto, para serem executados.

Pode utilizar o [portal do Azure](https://ms.portal.azure.com/) para monitorizar trabalhos e diagnosticar tarefas falhadas ao transferir os registos de aplicações e ligar-se remotamente às VMs individuais através de RDP ou SSH. Também pode utilizar a [ferramenta BatchLabs](https://azure.github.io/BatchLabs) para gerir, monitorizar e depurar.

Para obter mais informações sobre os trabalhos do Batch, veja a secção [Tarefa](batch-api-basics.md#job) do artigo [Desenvolver soluções de computação paralelas em grande escala com o Batch](batch-api-basics.md).

## <a name="options-for-provisioning-required-applications"></a>Opções para o aprovisionamento de aplicações necessárias

Poderão ser necessárias várias aplicações para compor um trabalho, como, por exemplo, uma combinação de Maya e Arnold ou de 3ds Max e V-Ray, bem como outros plug-ins de terceiros, se aplicável. Além disso, alguns clientes poderão precisar de versões específicas destas aplicações. Como tal, estão disponíveis vários métodos para aprovisionar as aplicações e o software necessários:

### <a name="pre-configured-vm-images"></a>Imagens de VMs pré-configuradas

O Azure disponibiliza imagens do Windows e do Linux, cada qual com uma versão única do Maya, do 3ds Max, do Arnold e do V-Ray pré-instalada e pronta a ser utilizada. Pode selecionar estas imagens no [portal do Azure](https://portal.azure.com), no plug-in do Maya ou no [BatchLabs](https://azure.github.io/BatchLabs) quando cria um conjunto.

No portal do Azure e no BatchLabs, pode instalar uma das imagens de VM com as aplicações pré-instaladas da seguinte forma: na secção Conjuntos da sua conta do Batch, selecione **Nova**, em seguida, em **Adicionar Conjunto**, selecione **Gráficos e Composição (Linux/Windows)**, a partir da lista pendente **Tipo de imagem**:

![Selecionar o tipo de imagem para a conta do Batch](./media/batch-rendering-service/add-pool.png)

Desloque-se para baixo e clique em **Licenciamento de gráficos e composição** para abrir o painel **Escolher licenças** e selecione uma ou mais licenças de software:

![Selecionar licenças de gráficos e de composição para o conjunto](./media/batch-rendering-service/graphics-licensing.png)

As versões de licenças específicas são as seguintes:

- Maya 2017
- 3ds Max 2018
- Arnold for Maya 5.0.1.1
- Arnold for 3ds Max 1.0.836
- V-Ray for Maya 3.52.03
- V-Ray for 3ds Max 3.60.01

### <a name="custom-images"></a>Imagens personalizadas

O Azure Batch permite-lhe fornecer uma imagem personalizada. Com esta opção, pode configurar a VM com as aplicações e versões específicas exatas de que precisa. Para obter mais informações, veja [Use a custom image to create a pool of virtual machines](https://docs.microsoft.com/azure/batch/batch-custom-images) (Utilizar uma imagem personalizada para criar um conjunto de máquinas virtuais). Tenha em atenção que a Autodesk e a Chaos Group modificaram o Arnold e o V-Ray respetivamente, no sentido de se autenticarem no nosso próprio serviço de licenciamento. Tem de confirmar que tem as versões destas aplicações com este suporte, caso contrário, o licenciamento “pay-per-use “ não funcionará. Esta validação de licenças não é necessária para o Maya nem para o 3ds Max, uma vez que as versões atualmente publicadas não precisam de servidores de licença durante a execução sem periféricos (no modo batch/linha de comandos). Se não souber ao certo como utilizar esta opção, contacte o suporte do Azure.

## <a name="options-for-submitting-a-render-job"></a>Opções para submeter trabalhos de composição

Dependendo da aplicação 3D que utilizar, existem várias opções para submeter trabalhos de composição para o serviço:

### <a name="maya"></a>Maya

Com o Maya, pode utilizar:

- [Plug-in do Batch para Maya](https://docs.microsoft.com/azure/batch/batch-rendering-service#use-the-batch-plug-in-for-maya-to-submit-a-render-job)
- Aplicação de ambiente de trabalho [BatchLabs](https://azure.github.io/BatchLabs)
- [CLI de Modelos do Batch](batch-cli-templates.md)

### <a name="3ds-max"></a>3ds Max

Com o 3ds Max, pode utilizar:

- Aplicação de ambiente de trabalho [BatchLabs](https://azure.github.io/BatchLabs) (veja [BatchLabs-data](https://github.com/Azure/BatchLabs-data/tree/master/ncj/3dsmax) [Dados do BatchLabs] para obter instruções sobre como utilizar modelos do BatchLabs do 3ds Max)
- [CLI de Modelos do Batch](batch-cli-templates.md)

Os modelos do Batch Labs do 3ds Max permitem-lhe utilizar o Serviço Azure Batch Rendering para compor cenas do VRay e do Arnold. Existem duas variações do modelo para o VRay e o Arnold, uma para cenas padrão e outra para cenas mais complexas que requerem um ficheiro de caminho do 3ds Max para elementos e texturas (ficheiro .mxp). Para obter mais informações sobre os modelos do Batch Labs do 3ds Max, veja o repositório [BatchLabs-data](https://github.com/Azure/BatchLabs-data/tree/master/ncj/3dsmax) no GitHub.

Além disso, pode utilizar o [SDK do Batch para Python](https://docs.microsoft.com/azure/batch/batch-python-tutorial) para integrar o serviço de composição no seu pipeline já existente.


## <a name="use-the-batch-plug-in-for-maya-to-submit-a-render-job"></a>Utilizar o plug-in do Batch para Maya submeter um trabalho de composição

Com o plug-in do Batch para Maya, pode submeter um trabalho para o serviço Batch Rendering diretamente a partir do Maya. As secções seguintes descrevem como configurar o trabalho no plug-in e, depois, submetê-lo. 

### <a name="load-the-batch-plug-in-for-maya"></a>Carregar o plug-in do Batch para Maya

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
