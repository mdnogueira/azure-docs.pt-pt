---
title: Perguntas mais frequentes do Azure Machine Learning (FAQ) | Microsoft Docs
description: "Introdução do Azure Machine Learning: Perguntas mais frequentes que abrangem a faturação, capacidades e limitações de um serviço em nuvem para a modelação preditiva simplificada."
keywords: "introdução ao machine learning, modelação preditiva, o que é o machine learning"
services: machine-learning
documentationcenter: 
author: garyericson
manager: paulettm
editor: cgronlun
ms.assetid: a4a32a06-dbed-4727-a857-c10da774ce66
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/02/2017
ms.author: garye
ms.translationtype: Human Translation
ms.sourcegitcommit: 80be19618bd02895d953f80e5236d1a69d0811af
ms.openlocfilehash: 0a1e23cd52ab5c10791a11d93753b54eb1c1b71a
ms.contentlocale: pt-pt
ms.lasthandoff: 06/07/2017


---
<a id="azure-machine-learning-frequently-asked-questions-billing-capabilities-limitations-and-support" class="xliff"></a>

# Perguntas mais frequentes do Azure Machine Learning: faturação, capacidades, limitações e suporte
Seguem-se algumas perguntas mais frequentes (FAQ) e as respostas correspondentes sobre o Azure Machine Learning, um serviço cloud para desenvolver modelos preditivos e operacionalizar soluções através de serviços Web. Estas FAQ respondem a perguntas sobre como utilizar o serviço, o que inclui o modelo de faturação, as capacidades, as limitações e o suporte.

**Tem alguma pergunta que não encontre aqui?**

O Azure Machine Learning tem um fórum no MSDN, onde os membros da comunidade de ciência de dados podem fazer perguntas sobre o Azure Machine Learning. O fórum é monitorizado pela equipa do Azure Machine Learning. Aceda ao [Fórum do Azure Machine Learning](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning) para procurar as respostas ou para publicar uma nova pergunta.

<a id="general-questions" class="xliff"></a>

## Perguntas gerais
**O que é o Azure Machine Learning?**

Azure Machine Learning é um serviço completamente gerido que pode utilizar para criar, testar, operar e gerir soluções de análise preditiva na nuvem. Com apenas um browser, pode iniciar sessão, carregar dados e iniciar imediatamente experiências de machine learning. A modelação preditiva de arrastar e largar, uma grande palete de módulos e uma biblioteca de modelos iniciais simplificam e aceleram o ritmo das tarefas comuns de machine learning. Para obter mais informações, consulte a [Descrição geral do serviço Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/). Para obter uma introdução ao machine learning que explica conceitos e terminologia-chave, veja [Introdução ao Azure Machine Learning](machine-learning-what-is-machine-learning.md).

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

**O que é o Machine Learning Studio?**

O Machine Learning Studio é um ambiente de workbench a que pode aceder através de um browser. O Machine Learning Studio aloja uma palete de módulos numa interface de composição visual que o ajuda criar um fluxo de trabalho de ciência de dados completo sob a forma de uma experimentação.

Para obter mais informações sobre o Machine Learning Studio, consulte [O que é o Machine Learning Studio?](machine-learning-what-is-ml-studio.md)

**O que é o serviço de API do Machine Learning?**

O serviço de API do Machine Learning permite-lhe implementar modelos preditivos, tal como os incorporados no Machine Learning Studio, como serviços Web dimensionáveis e tolerantes a falhas. Os serviços Web criados pelo serviço de API do Machine Learning são APIs REST que disponibilizam uma interface para comunicação entre aplicações externos e os seus modelos analíticos preditivos.

Para obter mais informações, veja [How to consume an Azure Machine Learning Web service](machine-learning-consume-web-services.md) (Como consumir um serviço Web do Azure Machine Learning).

**Onde estão apresentados os meus serviços Web clássicos? Onde estão apresentados os meus novos serviços Web (baseados no Azure Resource Manager)?**

Os serviços Web criados com o modelo de implementação Clássica e os serviços Web criados com o novo modelo de implementação Azure Resource Manager são apresentados no portal dos [Serviços Web do Microsoft Azure Machine Learning](https://services.azureml.net/).

Os serviços Web clássicos também são apresentados em [Machine Learning Studio](http://studio.azureml.net), no separador **Serviços Web**.

<a id="azure-machine-learning-questions" class="xliff"></a>

## Perguntas sobre o Azure Machine Learning
**O que são os Serviços Web do Azure Machine Learning?**

Os serviços Web Machine Learning proporcionam uma interface entre uma aplicação e um modelo de classificação de fluxo de trabalho do Machine Learning. As aplicações externas podem utilizar o Azure Machine Learning para comunicar com um modelo de classificação de fluxo de trabalho do Machine Learning. Uma chamada para o serviço Web do Machine Learning devolve resultados de predição para uma aplicação externa. Para fazer uma chamada para um serviço Web, tem de transmitir uma chave de API que foi criada quando implementou esse serviço. Os serviços Web Machine Learning baseiam-se em REST, uma opção de arquitetura popular para projetos de programação Web.

O Azure Machine Learning tem dois tipos de serviços Web:

* Serviço de Pedido-Resposta (RRS): um serviço altamente dimensionável e de baixa latência que proporciona uma interface para os modelos sem monitorização de estado criados e implementados com o Machine Learning Studio.
* Serviço de Execução de Lote (BES): um serviço assíncrono que classifica um lote de registos de dados.

Existem várias formas de utilizar a API REST e aceder ao serviço Web. Por exemplo, pode escrever uma aplicação em C#, R ou Python com o código de exemplo que foi gerado para si quando implementou o serviço Web.

O código de exemplo está disponível:
- Na página Consumo do serviço Web no portal dos Serviços Web do Azure Machine Learning
- Na página Ajuda da API no dashboard do serviço Web no Machine Learning Studio

Também pode utilizar o livro de exemplo do Microsoft Excel que é criado para si e que está disponível no dashboard do serviço Web no Machine Learning Studio.

**Quais são as principais atualizações ao Azure Machine Learning?**

Para saber quais são as atualizações mais recentes, veja [What's new in Azure Machine Learning (Novidades no Azure Machine Learning)](machine-learning-whats-new.md).

<a id="machine-learning-studio-questions" class="xliff"></a>

## Perguntas sobre o Machine Learning Studio
<a id="import-and-export-data-for-machine-learning" class="xliff"></a>

### Importar e exportar dados para o Machine Learning
**Que origens de dados são suportadas pelo Machine Learning?**

Pode transferir dados para uma experimentação do Machine Learning Studio de três formas:

- Carregar um ficheiro local como um conjunto de dados
- Utilizar um módulo para importar dados a partir de serviços de dados na cloud
- Importar um conjunto de dados guardado a partir do outra experimentação

Para saber mais sobre os formatos de ficheiro suportados, veja [Import training data into Machine Learning Studio (Importar dados de formação para o Machine Learning Studio)](machine-learning-data-science-import-data.md).

#### <a id="ModuleLimit"></a>Qual o tamanho máximo do conjunto de dados para os meus módulos?
Os módulos no Machine Learning Studio suportam conjuntos de dados até 10 GB de dados numéricos em casos de utilização comum. Se um módulo precisar de mais do que uma entrada, 10 GB é o valor do tamanho total de todas as entradas de dados. Também pode utilizar consultas do Hive ou da Base de Dados SQL do Azure para extrair exemplos de conjuntos de dados maiores ou utilizar o pré-processamento de Aprendizagem por Contagens antes da ingestão.  

Os seguintes tipos de dados podem ser expandidos para conjuntos de dados de maiores dimensões durante a normalização da funcionalidade, estando limitados a menos de 10 GB:

* Dispersos
* Categórico
* Cadeias
* Dados binários

Os seguintes módulos estão limitados a conjuntos de dados inferiores a 10 GB:

* Módulos de recomendador
* Módulo Synthetic Minority Oversampling Technique (SMOTE)
* Módulos de script: R, Python, SQL
* Módulos onde o tamanho dos dados de saída pode ser superior ao tamanho dos dados de entrada, tais como Associação ou Hashing de Funcionalidade
* Validação Cruzada, Hiperparâmetros do Modelo de Otimização, Regressão Ordinal e Multicasse “One-vs-All”, quando o número de iterações é muito grande

#### <a id="UploadLimit"></a>Quais são os limites para o carregamento de dados?
Para conjuntos de dados superiores a alguns GB, carregue os dados para o Armazenamento do Azure ou para a Base de Dados SQL do Azure ou utilize o Azure HDInsight em vez de os carregar diretamente a partir de um ficheiro local.

**Posso ler dados do Amazon S3?**

Se tiver uma pequena quantidade de dados e quiser expô-los através de um URL HTTP, pode utilizar o módulo [Importar Dados][import-data]. Para quantidades de dados maiores, transfira primeiro para o Armazenamento do Azure e, depois, utilize o módulo [Importar dados][import-data] para incluí-los na sua experimentação.
<!--

<SEE CLOUD DS PROCESS>
-->

**Existe uma capacidade de entrada de imagem incorporada?**

Pode saber mais sobre a capacidade de entrada de imagem na referência [Importar imagens][image-reader].

<a id="modules" class="xliff"></a>

### Módulos
**O algoritmo, a origem de dados, o formato de dados ou a operação de transformação de dados que procuro não está no Azure Machine Learning Studio. Quais são as minhas opções?**

Pode visitar o [fórum de comentários dos utilizadores](http://go.microsoft.com/fwlink/?LinkId=404231) para ver os pedidos de funcionalidades que estamos a acompanhar. Adicione o seu voto a um pedido se já tiver sido pedida uma funcionalidade que procura. Se a capacidade que está à procura não existir, crie um pedido novo. Também pode ver o estado do seu pedido neste fórum. Estamos a controlar com mais atenção esta lista e a atualizar frequentemente o estado de disponibilidade das funcionalidades. Além disso, pode utilizar o suporte incorporado para R e Python para criar transformações personalizadas conforme necessário.

**Posso usar o meu código existente no Machine Learning Studio?**

Sim, pode usar o seu código existente para o R ou Python no Machine Learning Studio, executá-lo na mesma experiência com os formandos do Azure Machine Learning Studio e implementar a solução como um serviço Web através do Azure Machine Learning. Para obter mais informações, consulte [Expandir a sua experiência com R](machine-learning-extend-your-experiment-with-r.md) e [Executar os scripts do Machine Learning Python no Azure Machine Learning Studio](machine-learning-execute-python-scripts.md).

**É possível utilizar algo semelhante a [PMML](http://en.wikipedia.org/wiki/Predictive_Model_Markup_Language) para definir um modelo?**

Não, a Predictive Model Markup Language (PMML) não é suportada. Pode utilizar código R e Python personalizado para definir um módulo.

**Quantos módulos posso executar em paralelo na minha experiência?**  

Pode executar até quatro módulos em paralelo numa experimentação.

<a id="data-processing" class="xliff"></a>

### Processamento de dados
**Existe uma capacidade para visualizar dados (para além das visualizações de R) interativamente na experiência?**

Clique na saída de um módulo para visualizar os dados e obter estatísticas.

**Quando pré-visualizo resultados ou dados num browser, o número de linhas e colunas é limitado. Porquê?**

Uma vez que é possível que sejam enviadas grandes quantidades de dados para o browser, o tamanho dos dados é limitado, para evitar que o Machine Learning fique mais lento. Para visualizar todos os dados/resultados, é melhor transferir os dados e utilizar o Excel ou outra ferramenta.

<a id="algorithms" class="xliff"></a>

### Algoritmos
**Que algoritmos existentes são suportados no Machine Learning Studio?**

O Machine Learning Studio dispõe de algoritmos avançados, como Árvores de Decisão Escaláveis, Sistemas de Recomendação Bayesianos, Redes Neurais Profundas e Selvas de Decisão desenvolvidas junto da Microsoft Research. Estão também incluídos pacotes de código aberto e dimensionáveis de machine learning, como o Vowpal Wabbit. O Machine Learning Studio suporta algoritmos de machine learning para classificação de várias classes e binária, regressão e clustering. Consulte a lista completa de [Módulos do Machine Learning][machine-learning-modules].

**Sugere automaticamente o algoritmo correto do Machine Learning a utilizar com os meus dados?**

Não, mas o Machine Learning Studio proporciona várias formas de comparar os resultados de cada algoritmo para determinar o algoritmo certo para o seu problema.

**Segue alguma diretriz para escolher um algoritmo em vez de outro relativamente aos algoritmos fornecidos?**

Veja [How to choose an algorithm (Como escolher um algoritmo)](machine-learning-algorithm-choice.md).

**Os algoritmos fornecidos são escritos em R ou Python?**

Não, estes algoritmos são principalmente escritos em linguagens compiladas, para permitir um desempenho melhor.

**São fornecidos alguns detalhes dos algoritmos?**

A documentação disponibiliza algumas informações acerca dos algoritmos e são descritos os parâmetros de otimização para melhorar o algoritmo.  

**A formação online é suportada?**

Não, atualmente só é suportada a reparametrização programática.

**Posso visualizar as camadas de um Modelo de Rede Neural com o módulo incorporado?**

Não.

**Posso criar os meus próprios módulos em C# ou noutra linguagem?**

Atualmente, só pode utilizar R para criar novos módulos personalizados.

<a id="r-module" class="xliff"></a>

### Módulo de R
**Os pacotes R estão disponíveis no Machine Learning Studio?**

O Machine Learning Studio suporta mais de 400 pacotes CRAN R hoje e esta é a [lista atual](http://az754797.vo.msecnd.net/docs/RPackages.xlsx) de todos os pacotes incluídos. Além disso, veja [Extend your experiment with R (Expandir a sua experiência com R) ](machine-learning-extend-your-experiment-with-r.md) para saber como obter esta lista manualmente. Se o pacote que pretende não estiver nesta lista, indique-o no [fórum de comentários dos utilizadores](http://go.microsoft.com/fwlink/?LinkId=404231).

**É possível criar um módulo R personalizado?**

Sim, consulte [Autor dos módulos R personalizados no Azure Machine Learning](machine-learning-custom-r-modules.md) para obter mais informações.

**Existe um ambiente REPL para R?**

Não, não existe nenhum ambiente Read-Eval-Print-Loop (REPL) para R no Studio.

<a id="python-module" class="xliff"></a>

### Módulo de Python
**É possível criar um módulo Python personalizado?**

Atualmente não, mas pode utilizar um ou mais módulos [Executar script do Python][python] para obter o mesmo resultado.

**Existe um ambiente REPL para Python?**

Pode utilizar os Jupyter Notebooks no Machine Learning Studio. Para obter mais informações, consulte [Introducing Jupyter Notebooks in Azure Machine Learning Studio (Introdução do Bloco de Notas do Jupyter no Azure Machine Learning Studio)](http://blogs.technet.com/b/machinelearning/archive/2015/07/24/introducing-jupyter-notebooks-in-azure-ml-studio.aspx).

<a id="web-service" class="xliff"></a>

## Serviço Web
<a id="retrain" class="xliff"></a>

### Preparar novamente
**Como posso reparametrizar os modelos do Azure Machine Learning de forma programática?**

Utilize as APIs de reparametrização. Para obter mais informações, veja [Retrain Machine Learning models programmatically (Reparametrizar modelos do Machine Learning programaticamente)](machine-learning-retrain-models-programmatically.md). O código de exemplo também está disponível em [Microsoft Azure Machine Learning Retraining Demo (Demonstração de Reparametrização do Microsoft Azure Machine)](https://azuremlretrain.codeplex.com/).

<a id="create" class="xliff"></a>

### Criar
**Posso implementar o modelo localmente ou numa aplicação que não tenha ligação à Internet?**

Não.

**Existe uma latência de linha de base que seja esperada para todos os serviços Web?**

Veja os [Azure subscription limits (limites das subscrições do Azure)](../azure-subscription-service-limits.md)

<a id="use" class="xliff"></a>

### Utilizar
**Quando deveria querer executar o meu modelo preditivo como um serviço de Execução de Lotes em comparação com um serviço de Resposta a um Pedido?**

O serviço de resposta a um pedido (RRS) é um serviço Web de latência baixa e alta escala que é utilizado para fornecer uma interface para modelos sem monitorização de estado que são criados e implementados a partir do ambiente de experiência. O Serviço de Execução de Lotes (BES) é um serviço que classifica, de forma assíncrona, um lote de registos de dados. A entrada para BES é como a entrada de dados que o RRS utiliza. A principal diferença é que BES lê um bloco de registos a partir de várias origens, como o Armazenamento de Blobs do Azure, o Armazenamento de Tabelas do Azure, a Base de Dados SQL do Azure, o HDInsight (consulta do Hive) e origens HTTP. Para obter mais informações, veja [How to consume an Azure Machine Learning Web service](machine-learning-consume-web-services.md) (Como consumir um serviço Web do Azure Machine Learning).

**Como posso atualizar o modelo para o serviço Web implementado?**

Para atualizar um modelo preditivo para um serviço já implementado, modifique e volte a executar a experimentação que utilizou para criar e guardar o modelo preparado. Assim que tiver uma nova versão do modelo preparado disponível, o Machine Learning Studio pergunta-lhe se quer atualizar o seu serviço Web. Para obter detalhes sobre como atualizar um serviço Web implementado, veja [Deploy a Machine Learning web service (Implementar um serviço Web Machine Learning)](machine-learning-publish-a-machine-learning-web-service.md).

Também pode utilizar as APIs para a reparametrização.
Para obter mais informações, veja [Retrain Machine Learning models programmatically (Reparametrizar modelos do Machine Learning programaticamente)](machine-learning-retrain-models-programmatically.md). O código de exemplo também está disponível em [Microsoft Azure Machine Learning Retraining Demo (Demonstração de Reparametrização do Microsoft Azure Machine)](https://azuremlretrain.codeplex.com/).

**Como posso monitorizar o meu serviço Web implementado na produção?**

Depois de implementar um modelo preditivo, pode monitorizá-lo a partir do portal clássico do Azure (apenas serviços Web clássicos) ou do portal dos Serviços Web do Azure Machine Learning. Cada serviço implementado tem o seu próprio dashboard onde pode ver informações de monitorização relativamente a esse serviço. Para obter mais informações sobre como gerir os seus serviços Web implementados, veja [Manage a Web service using the Azure Machine Learning Web Services portal (Gerir um serviço Web com o portal dos Serviços Web do Azure Machine Learning)](machine-learning-manage-new-webservice.md) e [Manage an Azure Machine Learning workspace (Gerir uma área de trabalho do Azure Machine Learning)](machine-learning-manage-workspace.md).

**Existe um local onde posso ver a saída da minha RRS/BES?**

Para RRS, vê normalmente o resultado na resposta do serviço Web. Também pode escrevê-lo no Armazenamento de Blobs do Azure. Para BES, o resultado é escrito num blob por predefinição. Também pode utilizar o módulo [Exportar Dados][export-data] para escrever a saída numa base de dados ou numa tabela.

**Só posso criar serviços Web a partir de modelos criados no Machine Learning Studio?**

Não, também pode utilizar o Jupyter Notebooks e o RStudio para criar serviços Web diretamente.

**Onde posso encontrar informações sobre códigos de erro?**

Consulte [Códigos de erro do módulo Machine Learning](https://msdn.microsoft.com/library/azure/dn905910.aspx) para obter uma lista de códigos de erro e descrições.

<a id="scalability" class="xliff"></a>

## Escalabilidade
**O que é a escalabilidade do serviço Web?**

Atualmente, o ponto final predefinido está aprovisionado com 20 pedidos de RRS em simultâneo por ponto final. Pode dimensioná-lo para 200 pedidos em simultâneo por ponto final e dimensionar cada serviço Web para 10 000 pontos finais por serviço Web, conforme descrito em [Scaling a Web Service (Dimensionar um Serviço Web)](machine-learning-scaling-webservice.md). Para BES, cada ponto final pode processar 40 pedidos de uma só vez e os pedidos adicionais que ultrapassem os 40 são colocados em fila de espera. Estes pedidos em fila de espera são executados automaticamente à medida que a fila avança.

**As tarefas de R são distribuídas pelos nós?**

Não.  

**Que quantidade de dados posso utilizar para formação?**

Os módulos no Machine Learning Studio suportam conjuntos de dados até 10 GB de dados numéricos em casos de utilização comum. Se um módulo precisar de mais do que uma entrada, o tamanho total de todas as entradas de dados são 10 GB. Também pode extrair exemplos de conjuntos de dados maiores através de consultas de Hive e da Base de Dados SQL do Azure ou do pré-processamento com os módulos [Aprendizagem por Contagens][counts] antes da ingestão.  

Os seguintes tipos de dados podem ser expandidos para conjuntos de dados de maiores dimensões durante a normalização da funcionalidade, estando limitados a menos de 10 GB:

* Dispersos
* Categórico
* Cadeias
* Dados binários

Os seguintes módulos estão limitados a conjuntos de dados inferiores a 10 GB:

* Módulos de recomendador
* Módulo Synthetic Minority Oversampling Technique (SMOTE)
* Módulos de script: R, Python, SQL
* Módulos onde o tamanho dos dados de saída pode ser superior ao tamanho dos dados de entrada, tais como Associação ou Hashing de Funcionalidade
* Validação Cruzada, Hiperparâmetros do Modelo de Otimização, Regressão Ordinal e Multicasse “One-vs-All”, quando o número de iterações é muito grande

Para conjuntos de dados superiores a alguns GB, carregue os dados para o Armazenamento do Azure ou para a Base de Dados SQL do Azure ou utilize o HDInsight em vez de os carregar diretamente a partir de um ficheiro local.

**Existem limitações ao tamanho do vetor?**

As linhas e colunas são limitadas para a limitação de .NET do máx. Int: 2.147.483.647.

**Posso ajustar o tamanho da máquina virtual que executa o serviço Web?**

Não.  

<a id="security-and-availability" class="xliff"></a>

## Segurança e a disponibilidade
**Quem tem acesso ao ponto final de http do serviço Web por predefinição? Como posso restringir o acesso ao ponto final?**

Depois de implementar um serviço Web, é criado um ponto final predefinido para esse serviço. O ponto final predefinido pode ser chamado através da respetiva chave de API. Pode adicionar mais pontos finais com as respetivas chaves a partir do portal clássico do Azure ou programaticamente, mediante a utilização das APIs de Gestão do Serviço Web. As chaves de acesso são necessárias para solicitar o serviço Web. Para obter mais informações, veja [How to consume an Azure Machine Learning Web service](machine-learning-consume-web-services.md) (Como consumir um serviço Web do Azure Machine Learning).

**O que acontece se não for possível encontrar a minha conta de armazenamento do Azure?**

O Machine Learning Studio baseia-se numa conta de armazenamento do Azure fornecida pelo utilizador para guardar dados intermédios quando executa o fluxo de trabalho. Esta conta de armazenamento é fornecida ao Machine Learning Studio no momento em que é criada uma área de trabalho. Depois de a área de trabalho ter sido criada, se a conta de armazenamento tiver sido eliminada e já não puder ser encontrada, a área de trabalho deixa de funcionar e todas as experimentações da mesma falham.

Caso tenha eliminado acidentalmente a conta de armazenamento, recrie-a com o mesmo nome na mesma região que a conta de armazenamento eliminada. Depois disso, volte a sincronizar a chave de acesso.

**O que acontece se a chave de acesso da minha conta de armazenamento estiver dessincronizada?**

O Machine Learning Studio baseia-se numa conta de armazenamento do Azure fornecida pelo utilizador para armazenar dados intermédios quando executa o fluxo de trabalho. Esta conta de armazenamento é fornecida ao Machine Learning Studio no momento em que é criada uma área de trabalho e as chaves de acesso são associadas a essa área de trabalho. Se as chaves de acesso forem alteradas, depois de criada a área de trabalho, esta deixa de poder aceder à conta de armazenamento. Deixa de funcionar e todas as experimentações na mesma falham.

Se tiver alterado as chaves de acesso da conta de armazenamento, utilize o portal clássico do Azure e volte a sincronizá-las na área de trabalho.  

<a id="support-and-training" class="xliff"></a>

## Assistência e formação
**Onde posso obter formação sobre o Azure Machine Learning?**

O [Centro de Documentação do Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) aloja tutoriais em vídeo e manuais de procedimentos. Estes guias passo a passo incluem uma introdução aos serviços e explicam o ciclo de vida da ciência de dados relativamente a importação de dados, limpeza de dados, criação de modelos preditivos e implementação dos mesmos na produção com o Azure Machine Learning.

Adicionamos materiais novos ao Centro do Machine Learning continuamente. Pode submeter pedidos para obter material de aprendizagem adicional no centro do Machine Learning no [fórum de comentários do utilizador](https://windowsazure.uservoice.com/forums/257792-machine-learning).

Também pode encontrar formação em [Microsoft Virtual Academy](http://www.microsoftvirtualacademy.com/training-courses/getting-started-with-microsoft-azure-machine-learning).

**Como posso obter suporte para o Azure Machine Learning?**

Para obter suporte técnico para o Azure Machine Learning, aceda a [Suporte do Azure](/support/options/) e selecione **Machine Learning**.

O Azure Machine Learning também tem um fórum comunitário no MSDN, onde pode fazer perguntas sobre o Azure Machine Learning. O fórum é monitorizado pela equipa do Azure Machine Learning. Aceda ao [Azure Forum (Fórum do Azure)](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning).

<a id="billing-questions" class="xliff"></a>

## Perguntas sobre faturação
**Como funciona a faturação do Machine Learning?**

O Azure Machine Learning tem dois componentes: o Machine Learning Studio e os serviços Web Machine Learning.

Enquanto estiver a avaliar o Machine Learning Studio, pode utilizar o escalão de faturação Gratuito. O escalão gratuito também lhe permite implementar um serviço Web Clássico com capacidade limitada.

Se achar que o Azure Machine Learning dá resposta às suas necessidades, pode inscrever-se no escalão Standard. Para se inscrever, tem de ter uma subscrição do Microsoft Azure.

No escalão Standard, é faturado mensalmente por cada área de trabalho que definir no Machine Learning Studio. Quando executa uma experimentação no studio, são-lhe faturados os recursos de computação durante essa execução. Quando implementa um Serviço Web Clássico, as transações e as horas de computação são faturadas segundo o modelo Pay As You Go.

Os serviços Web (baseados no Resource Manager) novos têm planos de faturação que permitem uma maior previsibilidade nos custos. O preço em escalões oferece tarifas com desconto aos clientes que precisam de uma grande capacidade.

Quando cria um plano, aceita um custo fixo que integra uma quantidade incluída de horas de computação da API e transações de API. Se precisar de mais quantidades incluídas, pode adicionar instâncias ao seu plano. Se precisar de muitas mais quantidades incluídas, pode escolher um plano de escalão superior que forneça significativamente mais quantidades incluídas e uma taxa com melhor desconto.

Depois de utilizar as quantidades incluídas nas instâncias existentes, a utilização adicional é cobrada à tarifa de utilização excedida associada ao escalão do plano de faturação.

> [!NOTE]
As quantidades incluídas são realocadas a cada 30 dias e as quantidades incluídas não utilizadas não são acumuladas.

Para obter informações adicionais sobre faturação e preços, consulte [Preços do Machine Learning](https://azure.microsoft.com/pricing/details/machine-learning/).

**O Machine Learning tem uma versão de avaliação gratuita?**

 O Azure Machine Learning tem uma opção de subscrição gratuita, que está explicada em [Preços do Machine Learning](https://azure.microsoft.com/pricing/details/machine-learning/). Quando inicia sessão no [Machine Learning Studio](https://studio.azureml.net/?selectAccess=true&o=2), este tem disponível uma versão de avaliação rápida de oito horas.

 Além disso, quando se inscreve numa versão de avaliação gratuita do Azure, pode experimentar todos os serviços do Azure durante um mês. Para obter mais informações sobre a versão de avaliação gratuita do Azure, aceda às [Perguntas mais frequentes sobre a versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial-faq/).

**O que é uma transação?**

Uma transação representa uma chamada de API à qual o Azure Machine Learning responde. As transações de chamadas do Serviço de Resposta-Pedido (RRS) e do Serviço de Execução de Lote (BES) são agregadas e cobradas conforme o seu plano de faturação.

**Posso utilizar as quantidades de transação incluídas num plano para transações RRS e BES?**

Sim, as suas transações de RRS e BES são agregadas e cobradas conforme o seu plano de faturação.

**O que é uma hora de computação de API?**

A hora de computação de API é a unidade de faturação para o tempo que as chamadas à API demoram a ser executadas quando são utilizados recursos de computação do Machine Learning. Todas as suas chamadas são agregadas para fins de faturação.

**Quanto tempo demora uma chamada normal à API de produção?**

Os tempos das chamadas às API de produção variam significativamente, indo geralmente de centenas de milissegundos a alguns segundos. Algumas chamadas poderão precisar de minutos, dependendo da complexidade do processamento dos dados e do modelo de machine learning. A melhor forma de estimar os tempos de chamada à API de produção passa por referenciar um modelo no serviço de Machine Learning.

**O que é uma hora de computação do Studio?**

Uma hora de computação do Studio corresponde à unidade de faturação do tempo agregado durante o qual as suas experimentações utilizam recursos de computação no Studio.

**Nos serviços Web (baseados no Azure Resource Manager) novos, a que se destina o escalão Dev/Test?**

Os serviços Web baseados no Resource Manager proporcionam vários escalões e pode utilizá-los para aprovisionar o seu plano de faturação. O escalão de preço Dev/Test proporciona quantidades incluídas limitadas que lhe permitem testar a sua experimentação como um serviço Web sem incorrer em custos. Tem a possibilidade de ver como funciona.

**Existem encargos de armazenamento separados?**

O escalão Gratuito de Machine Learning não requer ou permite um armazenamento separado. O escalão Standard de Machine Learning exige que os utilizadores tenham uma conta de armazenamento do Azure. O Armazenamento do Azure é [faturado separadamente](https://azure.microsoft.com/pricing/details/storage/).

**O Machine Learning suporta elevada disponibilidade?**

Sim. Para obter mais detalhes, veja [Preços do Machine Learning](https://azure.microsoft.com/en-us/pricing/details/machine-learning/), onde está disponível uma descrição no contrato de nível de serviço (SLA).

**Que tipo específico de recursos de computação será utilizado pelas minhas chamadas à API de produção?**

O serviço Machine Learning é um serviço multi-inquilino. Os recursos de computação reais utilizados no back-end variam e estão otimizados para desempenho e previsibilidade.

<a id="management-of-new-resource-manager-based-web-services" class="xliff"></a>

### Gestão de serviços Web (baseados no Resource Manager) Novos
**O que acontece se eliminar o meu plano?**

O plano é removido da sua subscrição e é faturado pela utilização rateada.

> [!NOTE]
Não pode eliminar um plano que esteja a ser utilizado por um serviço Web. Para eliminar o plano, tem de atribuir um novo plano ao serviço Web ou eliminar o serviço Web.

**O que é uma instância de plano?**

Uma instância de plano é uma unidade de quantidades incluídas que pode adicionar ao seu plano de faturação. Quando seleciona um escalão de faturação para o seu plano de faturação, este inclui uma instância. Se precisar de mais quantidades incluídas, pode adicionar instâncias do escalão de faturação selecionado ao seu plano.

**Quantas instâncias de plano posso adicionar?**

Pode ter uma instância do escalão de preço Dev/Test numa subscrição.

Nos escalões Standard S1, Standard S2 e Standard S3, pode adicionar tantas quantas for necessário.

> [!NOTE]
Dependendo da sua utilização antecipada, poderá ser mais económico atualizar para um escalão que tenha mais quantidades incluídas em vez de adicionar instâncias ao atual.

**O que acontece quando altero os escalões do plano (atualização/mudança para um escalão inferior)?**

O plano antigo é eliminado e a utilização atual é faturada numa base proporcional. É criado um novo plano com todas as quantidades incluídas do escalão atualizado/alterado para o resto do período.

> [!NOTE]
As quantidades incluídas são alocadas por período e as quantidades não utilizadas não são acumuladas.

**O que acontece quando aumento as instâncias num plano?**

As quantidades são incluídas de forma rateada e podem demorar 24 horas até ficarem disponíveis.

**O que acontece quando elimino uma instância de um plano?**

A instância é removida da sua subscrição e é faturado pela utilização rateada.

<a id="sign-up-for-new-resource-manager-based-web-services-plans" class="xliff"></a>

### Inscrever-se em planos de serviços Web (baseados no Resource Manager) Novos
**Como posso inscrever-me num plano?**

Existem duas formas de criar planos de faturação.

Quando implementa um serviço Web baseado no Resource Manager pela primeira vez, pode escolher um plano existente ou criar um novo.

Os planos que criar desta forma estão na sua região predefinida e o seu serviço Web será implementado nessa região.

Se quiser implementar serviços em regiões que não a sua predefinida, poderá ser útil definir os planos de faturação antes de implementar o serviço.

Nesse caso, pode iniciar sessão no portal dos Serviços Web do Azure Machine Learning e aceder à página Planos. Aí, pode adicionar e eliminar planos, bem como modificar planos existentes.

**Que plano devo escolher para começar?**

Recomendamos que comece com o escalão Standard S1 e que monitorize o seu serviço quando à utilização. Se achar que está a utilizar as suas quantidades incluídas rapidamente, pode adicionar instâncias ou mudar para um escalão superior e obter tarifas com descontos melhores. Pode ajustar o seu plano de faturação conforme necessário durante o ciclo de faturação.

**Em que regiões estão disponíveis os novos planos?**

Os novos planos de faturação estão disponíveis nas três regiões de produção nas quais suportamos os novos serviços Web:

* EUA Centro-Sul
* Europa Ocidental
* Sudeste Asiático

**Tenho serviços Web em várias regiões. Preciso de um plano para cada região?**

Sim. Os preços dos planos variam consoante a região. Quando implementa um serviço Web noutra região, tem de lhe atribuir um plano específico para essa região. Para obter mais informações, veja [Produtos disponíveis por região]( https://azure.microsoft.com/regions/services/).

<a id="new-web-services-overages" class="xliff"></a>

### Novos Serviços Web – Utilizações excedidas
**Como posso ver se excedi a utilização do meu serviço Web?**

Pode ver a utilização de todos os seus planos na página Planos do portal dos Serviços Web Azure Machine Learning. Inicie sessão no portal e clique na opção de menu **Planos**.

Nas colunas **Transações** e **Computação** da tabela, pode ver as quantidades incluídas do plano e a percentagem utilizada.

**O que acontece quando utilizo a totalidade das quantidades incluídas no escalão de preço Dev/Test?**

Os serviços que tenham o escalão de preço Dev/Test atribuído são parados até ao próximo período ou até os mover para um dos escalões pagos.

**No caso dos serviços Web Clássicos e de utilizações excedidas dos serviços Web (baseados no Resource Manager) Novos, como são calculados os preços para cargas de trabalho de Serviço de Pedido-Resposta (RRS) e de Lote (BES)?**

Para cargas de trabalho de RRS, é-lhe cobrada cada chamada de transação à API que fizer e o tempo de computação associado a esses pedidos. Os custos de transações de API de produção de RRS são calculados como o número total de chamadas à API que realiza multiplicado pelo preço de mil transações (rateado por transação individual). Os custos de hora de computação de API de produção de RRS são calculados como a quantidade de tempo necessária para a execução de cada chamada à API, multiplicada pelo número total de transações de API, multiplicado pelo preço por hora de computação de API de produção.

Por exemplo, numa utilização excedida do escalão Standard S1, 1 000 000 de transações de API com um tempo de execução individual de 0,72 segundos teria um custo de (1 000. 00 0,50 $/Mil transações de API) 500 $ em custos de transação de API de produção e (1 000 000 0,72 * 2 $/h) 400 $ em horas de computação de API de produção, o que totaliza 900 $.

As cargas de trabalho de BEST são cobradas da mesma forma. No entanto, os custos de transação de API representam o número de tarefas de lote que submeter e os custos de computação representam o tempo de computação associado a essas tarefas de lote. Os custos de transação de API de produção de BES são calculados como o número total de tarefas submetidas multiplicado pelo preço de mil transações (rateado por transação individual). Os custos de hora de computação de API de produção de BES são calculados como a quantidade de tempo necessária para a execução de cada linha na sua tarefa, multiplicada pelo número total de linhas na tarefa, multiplicado pelo preço por hora de computação de API de produção. Quando utiliza a calculadora do Machine Learning, o medidor de transação representa o número de tarefas que planeia submeter e o campo de tempo por transação representa o tempo combinado necessário para a execução de todas as linhas em cada tarefa.

Por exemplo, imaginemos a utilização excedida de Standard S1 e submete 100 tarefas por dia, que cada consistem em 500 linhas que demoram 0,72 segundos cada. Os custos de utilização excedida mensais seriam de (100 tarefas por dia = 3100 tarefas/mês 0,50 $/1 Mil transações de API) 1,55 $ em custos de transação de API de produção (500 linhas 0,72 segundos 3100 Tarefas 2 $/hr) 620 $ em horas de computação de API de produção, num total de 621,55 $.

<a id="azure-machine-learning-classic-web-services" class="xliff"></a>

### Serviços Web Clássico do Azure Machine Learning
**A opção Pay As You Go ainda está disponível?**

Sim, os Serviços Web Clássicos ainda estão disponíveis no Azure Machine Learning.  

<a id="azure-machine-learning-free-and-standard-tier" class="xliff"></a>

### Escalão Gratuito e Standard do Azure Machine Learning
**O que está incluído no escalão Gratuito do Azure Machine Learning?**

O escalão Gratuito do Azure Machine Learning destina-se a fornecer uma introdução aprofundada ao Azure Machine Learning Studio. Tudo o que precisa é uma conta Microsoft para se inscrever. O escalão Gratuito inclui acesso gratuito a uma área de trabalho do Azure Machine Learning Studio por [conta Microsoft](https://www.microsoft.com/account/default.aspx). Neste escalão, pode utilizar até 10 GB de armazenamento e operacionalizar modelos como APIs de teste. As cargas de trabalho do escalão Gratuito não são abrangidas por um SLA e destinam-se apenas ao desenvolvimento e utilização pessoal. 

As áreas de trabalho do escalão gratuito têm as seguintes limitações:

* As cargas de trabalho não podem aceder a dados ao ligar a um servidor no local que execute o SQL Server.
* Não é possível implementar novos serviços Web base do Resource Manager.


**O que está incluído no escalão e planos Standard do Azure Machine Learning?**

O escalão Standard do Azure Machine Learning é uma versão de produção paga do Azure Machine Learning Studio. O valor mensal do Azure Machine Learning Studio é faturado por área de trabalho por mês e é rateado em meses parciais. As horas de experimentação do Azure Machine Learning Studio são faturadas por hora de computação por experimentação ativa. A faturação é proporcional às horas parciais.  

O serviço de API do Azure Machine Learning é faturado consoante seja um serviço Web Clássico ou um serviço Web (baseado no Resource Manager) Novo.

Os encargos seguintes estão agregados por área de trabalho para a sua subscrição.

* Subscrição de Área de Trabalho do Machine Learning: a subscrição de área de trabalho do Machine Learning é um valor mensal que proporciona acesso a uma área de trabalho do Machine Learning Studio. A subscrição é necessária para executar experimentações no Studio e utilizar as APIs de produção.
* Horas de Experimentação do Studio: este medidor agrega todos os custos de computação que são acumulados pela execução de experimentações no Machine Learning Studio e de chamadas à API de produção no ambiente de teste.
* Aceda aos dados ao ligar a um servidor no local que execute o SQL Server nos seus modelos, para fins de preparação e classificação.
* Nos serviços Web Clássicos:
  * Horas de Computação da API de Produção: este medidor inclui custos de computação que são acumulados pelos serviços Web em execução na produção.
  * Transações da API de Produção (em milhares): este medidor inclui custos que são acumulados por chamada para o seu serviço Web de produção.

Para além dos custos anteriores, no caso dos serviços Web baseados no Resource Manager, os custos são agregados ao plano selecionado:

* Plano de API Standard S1/S2/S3 (Unidades): este medidor representa o tipo de instância selecionada para os serviços Web baseados no Resource Manager.
* Horas de Computação de API Excedidas Standard S1/S2/S3: este medidor inclui custos que são acumulados pelos serviços Web baseados no Resource Manager em execução na produção depois de esgotadas as quantidades incluídas nas instâncias existentes. A utilização adicional é cobrada de acordo com a sobretaxa associada ao escalão do plano S1/S2/S3.
* Transações de API Excedidas Standard S1/S2/S3 (em milhares): este medidor inclui custos que são acumulados por chamada para os serviços Web baseados no Resource Manager depois de esgotadas as quantidades incluídas nas instâncias existentes. A utilização adicional é cobrada de acordo com a sobretaxa associada ao escalão do plano S1/S2/S3.
* Quantidade Incluída de Horas de Computação de API: nos serviços Web baseados no Resource Manager, este medidor representa a quantidade incluída de horas de computação de API.
* Quantidade Incluída de Transações de API (em milhares): nos serviços Web baseados no Resource Manager, este medidor representa a quantidade incluída de transações de API.

**Como me posso inscrever no escalão Gratuito do Azure Machine Learning?**

Tudo o que precisa é uma conta Microsoft. Aceda à [home page do Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) e clique em **Comece Agora**. Inicie sessão com a sua conta Microsoft e será criada uma área de trabalho do escalão Gratuito por si. Pode começar a explorar e criar experimentações de Machine Learning imediatamente.

**Como me posso inscrever no escalão Standard do Azure Machine Learning?**

Tem de ter, primeiro, acesso a uma subscrição do Azure para criar uma área de trabalho Standard do Machine Learning. Pode inscrever-se numa subscrição de avaliação gratuita de 30 dias do Azure e, posteriormente, atualizar para uma subscrição do Azure paga ou pode comprar uma subscrição de imediato. Em seguida, pode criar uma área de trabalho do Machine Learning a partir do portal clássico do Microsoft Azure após obter acesso à subscrição. Veja as [instruções passo a passo](https://azure.microsoft.com/trial/get-started-machine-learning-b/).

Em alternativa, pode ser convidado pelo proprietário de uma área de trabalho standard do Machine Learning para aceder à área de trabalho do mesmo.

**Posso especificar a minha própria conta de Armazenamento de Blobs do Azure para utilizar com o escalão Gratuito?**

Não, o escalão Standard é equivalente à versão do serviço Machine Learning que estava disponível antes de os escalões serem introduzidos.

**Posso implementar os meus modelos de aprendizagem automática como APIs no escalão Gratuito?**

Sim, pode operacionalizar modelos de machine learning para serviços de API de teste como parte do escalão Gratuito. Para colocar o serviço de API de teste em produção e obter um ponto final de produção para o serviço operacionalizado, tem de utilizar o escalão Standard.

**Qual é a diferença entre a avaliação gratuita do Azure e o escalão Gratuito do Azure Machine Learning?**

A [versão de avaliação gratuita do Microsoft Azure](https://azure.microsoft.com/free/) oferece créditos que podem ser aplicados a qualquer serviço do Azure por um mês. O escalão Gratuito do Azure Machine Learning oferece acesso contínuo especificamente ao Azure Machine Learning para cargas de trabalho de não produção.

**Como posso mover uma experimentação do escalão Gratuito para o escalão Standard?**

Para copiar as experimentações do escalão Gratuito para o Standard:

1. Inicie sessão no Azure Machine Learning Studio e confirme que consegue ver tanto a área de trabalho Gratuita, como a Standard, no seletor de área de trabalho na barra de navegação superior.
2. Mude para a área de trabalho Gratuita se estiver na área de trabalho Standard.
3. Na vista de lista de experimentações, selecione uma experimentação que queira copiar e clique no botão do comando **Copiar**.
4. Selecione a área de trabalho Standard na caixa de diálogo que se abre e clique no botão **Copiar**.
   Todos os conjuntos de dados associados, modelos preparados, etc., são copiados juntamente com a experimentação para a área de trabalho Standard.
5. Tem de executar novamente a experimentação e voltar a publicar o seu serviço Web na área de trabalho Standard.

<a id="studio-workspace" class="xliff"></a>

### Área de trabalho do Studio
**Irei ver faturas diferentes para áreas de trabalho diferentes?**

Os encargos da área de trabalho são divididos para cada medidor aplicável numa única fatura.

**Que tipo específico de recursos de computação será utilizado pelas minhas experimentações?**

O serviço Machine Learning é um serviço multi-inquilino. Os recursos de computação reais utilizados no back-end variam e estão otimizados para desempenho e previsibilidade.

<a id="guest-access" class="xliff"></a>

### Acesso de Convidado
**O que é o Acesso de Convidado do Azure Machine Learning Studio?**

O acesso de Convidado é uma experiência de avaliação limitada. Pode criar e executar experimentações no Azure Machine Learning Studio sem custos e sem autenticação. As sessões de convidados são não persistentes (não podem ser guardadas) e estão limitadas a 8 horas. Outras limitações incluem falta de suporte para R e Python, falta de APIs de teste e capacidade de armazenamento e tamanho dos conjuntos de dados restritos. Por comparação, os utilizadores que optem por iniciar sessão com uma conta Microsoft têm acesso total ao escalão Gratuito do Machine Learning Studio, que está descrito acima, o qual inclui uma área de trabalho persistente e capacidades mais abrangentes. Para escolher a sua experimentação gratuita do Machine Learning, clique em **Introdução**, em [https://studio.azureml.net](https://studio.azureml.net), e selecione **Acesso de Convidado** ou inicie sessão com uma conta Microsoft.

<!-- Module References -->
[image-reader]: https://msdn.microsoft.com/library/azure/893f8c57-1d36-456d-a47b-d29ae67f5d84/
[join]: https://msdn.microsoft.com/library/azure/124865f7-e901-4656-adac-f4cb08248099/
[machine-learning-modules]: https://msdn.microsoft.com/library/azure/6d9e2516-1343-4859-a3dc-9673ccec9edc/
[partition-and-sample]: https://msdn.microsoft.com/library/azure/a8726e34-1b3e-4515-b59a-3e4a475654b8/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[export-data]: https://msdn.microsoft.com/library/azure/7A391181-B6A7-4AD4-B82D-E419C0D6522C
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[python]: https://msdn.microsoft.com/library/azure/CDB56F95-7F4C-404D-BDE7-5BB972E6F232
[counts]: https://msdn.microsoft.com/library/azure/dn913056.aspx

