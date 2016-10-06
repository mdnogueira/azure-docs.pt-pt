<properties
    pageTitle="Perguntas mais frequentes do Azure Machine Learning | Microsoft Azure"
    description="Introdução do Azure Machine Learning: Perguntas mais frequentes que abrangem a faturação, capacidades e limitações de um serviço em nuvem para a modelação preditiva simplificada."
    keywords="introdução ao machine learning, modelação preditiva, o que é o machine learning"
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="paulettm"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/14/2016"
    ms.author="garye"/>


# Perguntas mais frequentes do Azure Machine Learning: faturação, capacidades, limitações e suporte

Estas perguntas mais frequentes respondem a dúvidas sobre o Azure Machine Learning, um serviço em nuvem para desenvolver modelos preditivos e operacionalizar soluções através dos serviços da Web. Estas perguntas mais frequentes incluem perguntas sobre como utilizar o serviço, incluindo o modelo de faturação, capacidades, limitações e suporte.

## Perguntas gerais

**O que é o Azure Machine Learning?**

Azure Machine Learning é um serviço completamente gerido que pode utilizar para criar, testar, operar e gerir soluções de análise preditiva na nuvem. Com apenas um browser, pode iniciar sessão, carregar dados e iniciar imediatamente experiências de Machine Learning. Modelação preditiva de arrastar e largar, uma grande palete de módulos e uma biblioteca de modelos iniciais simplificam e aceleram o ritmo das tarefas de Machine Learning.  Para obter mais informações, consulte a [Descrição geral do serviço Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/). Para uma introdução à Machine Learning que aborda conceitos e terminologia-chave, consulte [Introdução à Azure Machine Learning](machine-learning-what-is-machine-learning.md).

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

**O que é o Machine Learning Studio?**

O Machine Learning Studio é um ambiente do workbench a que tem acesso através de um browser. O Machine Learning Studio aloja uma palete dos módulos com uma interface de composição visual que lhe permite criar um ponto-a-ponto, um fluxo de trabalho de ciências de dados sob a forma de uma experiência.

Para obter mais informações sobre o Machine Learning Studio, consulte [O que é o Machine Learning Studio?](machine-learning-what-is-ml-studio.md)

**O que é o serviço de API do Machine Learning?**

O serviço de API do Machine Learning permite-lhe implementar modelos preditivos, tal como os incorporados no Machine Learning Studio, como escaláveis, com tolerância a falhas e serviços Web. Os serviços web criados pelo serviço de API do Machine Learning são APIs REST que fornecem uma interface para comunicação entre aplicações externas e os seus modelos de análise preditiva.

Consulte [Ligar a um serviço Web do Machine Learning](machine-learning-connect-to-azure-machine-learning-web-service.md) para obter mais informações.

**Onde estão apresentados os meus serviços Web clássicos? Onde estão apresentados os meus serviços Web novos baseados no Azure Resource Manager?**

Os serviços Web clássicos são apresentados no [Machine Learning Studio](http://studio.azureml.net) no separador de serviços Web. Os serviços Web novos baseados no Azure Resource Manager são apresentados no portal [Serviços Web do Microsoft Azure Machine Learning](https://services.azureml.net/). Não existe nenhuma listagem cruzada disponível.

## Perguntas do Serviço Web do Microsoft Azure Machine Learning

**Quais são os Serviços Web do Azure ML?**

Com o serviço Web Azure Machine Learning, uma aplicação externa comunica com um modelo de classificação do fluxo de trabalho de Machine Learning em tempo real. Uma chamada do serviço Web Machine Learning devolve resultados de predição para uma aplicação externa. Para realizar uma chamada do serviço Web Machine Learning, tem de transmitir uma chave de API que foi criada quando implementou o serviço Web. O serviço Web Machine Learning é baseado em REST, uma opção de arquitetura popular para projetos de programação Web.

O Azure Machine Learning tem dois tipos de serviços:

* Serviço de Pedido-Resposta (RRS) – Um serviço altamente dimensionável e de baixa latência que fornece uma interface para os modelos sem monitorização de estado criados e implementados a partir do Machine Learning Studio.
* Serviço de Execução de Lote (BES) – Um serviço assíncrono que pontua um lote de registos de dados.

Existem várias formas de utilizar a API REST e aceder ao serviço Web. Por exemplo, pode escrever uma aplicação em C#, R ou Python com o código de exemplo gerado quando implementou o serviço Web. 

O código de exemplo está disponível na página “Consumo” do serviço Web no portal dos Serviços Web do Azure Machine Learning e na página Ajuda da API no dashboard do serviço Web no Machine Learning Studio. 

Em alternativa, pode utilizar o livro do Microsoft Excel de exemplo criado para si (também disponível no dashboard do serviço Web no Studio).

**Quais são as principais atualizações dos novos Serviços Web do Azure ML?**

Para obter mais informações sobre os Novos Serviços Web do Azure Machine Learning, veja a [documentação relacionada](machine-learning-whats-new.md). 

## Perguntas sobre o Machine Learning Studio

### Criar uma experiência

**Existe um controlo da versão ou uma integração de Git para os gráficos de experimentação?**

No entanto, nenhum Machine Learning Studio retém cada iteração de uma experiência que não possa ser modificada por outros utilizadores.
Para obter mais informações, consulte [Gerir iterações de experimentação no Machine Learning Studio](machine-learning-manage-experiment-iterations.md).


### Implementar uma experimentação

**Posso implementar uma experimentação preditiva como um novo serviço Web (baseado no Azure Resource Manager) se já a tiver implementado como um serviço Web clássico?**

Não, não pode implementar uma experimentação que tenha sido implementada anteriormente como um serviço Web clássico. Em vez disso, tem de criar uma nova experimentação preditiva e implementá-la.


### Importar e exportar dados para o Machine Learning

**Que origens de dados são suportadas pelo Machine Learning?**

Os dados podem ser carregados para uma experiência do Machine Learning Studio sob uma de três formas: através do carregamento de um ficheiro local como um conjunto de dados, através de um módulo para importar dados de serviços de dados em nuvem ou através da importação de um conjunto de dados guardado a partir de outra experiência. Para saber mais sobre os formatos de ficheiro suportados, veja [Import training data into Machine Learning Studio (Importar dados de formação para o Machine Learning Studio)](machine-learning-data-science-import-data.md).


#### <a id="ModuleLimit"></a>Qual o tamanho máximo do conjunto de dados para os meus módulos?

Os módulos no Machine Learning Studio suportam conjuntos de dados até 10 GB de dados numéricos em casos de utilização comum. Se um módulo precisar mais do que uma entrada, 10 GB é o tamanho total de todas as entradas de dados. Também pode recolher exemplos de conjuntos de dados de maiores dimensões através de consultas da SQL Database do Azuro ou do Hive ou através da aprendizagem por contagens pré-processadas antes da ingestão.  

Os seguintes tipos de dados podem ser expandidos para conjuntos de dados de maiores dimensões durante a normalização da funcionalidade, estando limitados a menos de 10 GB:

- Disperso
- Categórico
- Cadeias
- Dados binários

Os seguintes módulos estão limitados a conjuntos de dados inferiores a 10 GB:

- Módulos de recomendador
- Módulo SMOTE
- Módulos de script: R, Python, SQL
- Módulos onde o tamanho dos dados de saída pode ser superior ao tamanho dos dados de entrada, tais como a adesão ou funcionalidade hash.
- Validação Cruzada, Hiperparâmetros do Modelo de Sintonização, Regressão Ordinal e Multicasse “One-vs-All”, quando o número de iterações é muito grande.

Para conjuntos de dados superiores a alguns GB, deverá carregar os dados para o armazenamento do Azure ou para a SQL Database Azure ou utilizar o HDInsight em vez de o carregar diretamente a partir do ficheiro local.


####<a id="UploadLimit"></a>Quais são os limites para o carregamento de dados?
Para conjuntos de dados superiores a alguns GB, deverá carregar os dados para o armazenamento do Azure ou para a SQL Database do Azure ou utilizar o HDInsight em vez de o carregar diretamente a partir do ficheiro local.

**Posso ler dados do Amazon S3?**

Se tiver uma pequena quantidade de dados e quiser expô-la através de um URL de http, pode utilizar o módulo [Importar dados][importar dados]. Para maiores quantidades de dados que pretenda transferir primeiro para o Storage do Azure e depois utilizar o módulo [Importar dados][importar dados] para o incluir na sua experiência.
<!--
<SEE CLOUD DS PROCESS>
-->

**Existe uma capacidade de entrada de imagem incorporada?**

Pode saber mais sobre a capacidade de entrada de imagem na referência [Importar imagens][imagem-leitor].

### Módulos

**O algoritmo, a origem de dados, o formato de dados ou a operação de transformação de dados que procuro não está no Azure Machine Learning Studio. Quais são as minhas opções?**

Pode visitar o [fórum de comentários do utilizador](http://go.microsoft.com/fwlink/?LinkId=404231) para ver os pedidos de funcionalidades que estamos a controlar. Adicione o seu voto a um pedido se já tiver sido solicitada uma funcionalidade que procura. Se a capacidade que está à procura não existir, crie um novo pedido. Também pode ver o estado do seu pedido neste fórum. Estamos a controlar com mais atenção esta lista e a atualizar frequentemente o estado de disponibilidade das funcionalidades. Além disso, com o suporte incorporado para o R e Python podem ser criadas transformações personalizadas conforme necessário.


**Posso usar o meu código existente no Machine Learning Studio?**

Sim, pode usar o seu código existente para o R ou Python no Machine Learning Studio, executá-lo na mesma experiência com os formandos do Azure Machine Learning Studio e implementar a solução como um serviço Web através do Azure Machine Learning. Para obter mais informações, consulte [Expandir a sua experiência com R](machine-learning-extend-your-experiment-with-r.md) e [Executar os scripts do Machine Learning Python no Azure Machine Learning Studio](machine-learning-execute-python-scripts.md).

**É possível utilizar algo semelhante a [PMML](http://en.wikipedia.org/wiki/Predictive_Model_Markup_Language) para definir um modelo?**

Não, isso não é suportado. No entanto, o código personalizado para o R e Python pode ser utilizado para definir um módulo.

**Quantos módulos posso executar em paralelo na minha experiência?**  

Pode executar até quatro módulos em paralelo numa experimentação.


### Processamento de dados

**Existe uma capacidade de visualizar os dados (para além das visualizações de R) interativamente na experiência?**

Ao clicar no resultado de um módulo, pode visualizar os dados e obter estatísticas.

**Quando pré-visualizar os resultados ou dados no browser, porque é que o número de linhas e colunas é limitado?**

Uma vez que os dados estão a ser transmitidos para o browser e podem ser de grande volume, o tamanho dos dados é limitado para impedir que i Machine Learning Studio fique mais lento. Para visualizar todos os dados/resultados, é melhor transferir os dados e utilizar o Excel ou outra ferramenta.

### Algoritmos

**Que algoritmos existentes são suportados no Machine Learning Studio?**

O Machine Learning Studio dispõe de algoritmos avançados, como Árvores de Decisão Escaláveis, Sistemas de Recomendação Bayesianos, Redes Neurais Profundas e Selvas de Decisão desenvolvidas junto da Microsoft Research. Estão também incluídos os pacotes open-source do Machine Learning como o Adalberto como o Vowpal Wabbit. O Machine Learning Studio suporta algoritmos de machine learning para classificação de várias classes e binária, regressão e clustering. Consulte a lista completa de [Módulos do Machine Learning][módulos de machine learning].

**Sugere automaticamente o direito ao algoritmo do Machine Learning utilizar com os meus dados?**

Não. No entanto, existem várias formas de o Machine Learning Studio comparar os resultados de cada algoritmo para determinar o algoritmo certo para o seu problema.

**Segue alguma diretriz para escolher um algoritmo em vez de outro relativamente aos algoritmos fornecidos?**
Veja [How to choose an algorithm (Como escolher um algoritmo)](machine-learning-algorithm-choice.md).

**Os algoritmos fornecidos são escritos no R ou Python?**

Não, estes algoritmos são principalmente escritos em idiomas compilados para um desempenho superior.

**Alguns detalhes pertencem aos algoritmos fornecidos?**

A documentação fornece algumas informações acerca dos algoritmos e os parâmetros fornecidos para regulação são descritos para otimizar o algoritmo.  

**A formação online é suportada?**

Não, atualmente só é suportada a reparametrização programática.

**Posso visualizar as camadas de um modelo de rede neural utilizando o módulo incorporado?**

Não.

**Posso criar os meus próprios módulos C# ou noutro idioma?**

Atualmente só podem ser criados novos módulos personalizados em R.

### Módulo de R

**Os pacotes de R estão disponíveis no Machine Learning Studio?**

O Machine Learning Studio suporta hoje 400 + CRAN R hoje e aqui está a [lista atual](http://az754797.vo.msecnd.net/docs/RPackages.xlsx) de todos os pacotes incluído. Além disso, veja [Extend your experiment with R (Expandir a sua experiência com R) ](machine-learning-extend-your-experiment-with-r.md) para saber como obter esta lista manualmente. Se o pacote que pretende não estiver nesta lista, forneça o nome de pacote no [fórum de comentários do utilizador](http://go.microsoft.com/fwlink/?LinkId=404231).

**É possível construir um módulo R personalizado?**

Sim, consulte [Autor dos módulos R personalizados no Azure Machine Learning](machine-learning-custom-r-modules.md) para obter mais informações.

**Existe um ambiente REPL para R?**

Não, não existe nenhum ambiente REPL para R no studio.

### Módulo de Python

**É possível construir um módulo Python personalizado?**

Atualmente não, mas pode utilizar um ou mais módulos [Executar script do Python][python] para obter o mesmo resultado.

**Existe um ambiente REPL para Python?**

Pode utilizar os Jupyter Notebooks no Machine Learning Studio. Para obter mais informações, consulte [Apresentação dos Jupyter Notebooks no Azure Machine Learning Studio] (http://blogs.technet.com/b/machinelearning/archive/2015/07/24/introducing-jupyter-notebooks-in-azure-ml-studio.aspx).

## Serviço Web

###Reparametrizar modelos programaticamente

**Como posso fazer a reparametrização dos modelos programáticos do Azure Machine Learning?**

Utilize as APIs para a reparametrização. Para obter mais informações, veja [Retrain Machine Learning models programmatically (Reparametrizar modelos do Machine Learning programaticamente)](machine-learning-retrain-models-programmatically.md). O código de exemplo também está disponível em [Microsoft Azure Machine Learning Retraining Demo (Demonstração de Reparametrização do Microsoft Azure Machine)](https://azuremlretrain.codeplex.com/).

### Criar

**Posso implementar o modelo localmente ou numa aplicação sem uma ligação à internet?**

Não.


**Existe uma latência de linha de base que é esperada para todos os serviços Web?**

Consulte os [limites de subscrição do Azure](../azure-subscription-service-limits.md)

### Utilizar

**Quando deveria querer executar o meu modelo preditivo como um Serviço de Execução de Lotes em comparação com um serviço de resposta a um pedido?**

O serviço de resposta a um pedido (RRS) é um serviço Web de latência baixa e alta escala que é utilizado para fornecer uma interface para modelos sem monitorização de estado que são criados e implementados a partir do ambiente de experiência. O Serviço de Execução de Lotes (BES) é um serviço de resultados assíncronos que apresenta um lote de registos de dados. A entrada para BES é semelhante à introdução de dados utilizada em RRS. A principal diferença é que BES lê um bloco de registos a partir de uma variedade de origens, como o serviço Blob e o serviço Tabela no Azure, a SQL Database do Azure, o HDInsight (consulta do grupo) e origens HTTP. Consulte [Como consumir os serviços Web do Machine Learning](machine-learning-consume-web-services.md) para obter mais informações.

**Como posso atualizar o modelo para o serviço Web implementado?**

Atualizar um modelo preditivo para um serviço já implementado é tão simples como modificar e executar novamente a experiência que utilizou para criar e guardar o modelo preparado. Assim que tiver uma nova versão do modelo preparado disponível, o Machine Learning Studio pergunta-lhe se quer atualizar o seu serviço Web. Para obter detalhes sobre como atualizar um serviço Web implementado, veja [Deploy a Machine Learning web service (Implementar um serviço Web Machine Learning)](machine-learning-publish-a-machine-learning-web-service.md).

Também pode utilizar as APIs para a reparametrização.
Para obter mais informações, veja [Retrain Machine Learning models programmatically (Reparametrizar modelos do Machine Learning programaticamente)](machine-learning-retrain-models-programmatically.md). O código de exemplo também está disponível em [Microsoft Azure Machine Learning Retraining Demo (Demonstração de Reparametrização do Microsoft Azure Machine)](https://azuremlretrain.codeplex.com/).

**Como posso monitorizar o meu serviço Web implementado na produção?**

Depois de ter sido implementado um modelo preditivo, pode monitorizá-lo a partir do portal clássico do Azure. Cada serviço implementado tem o seu próprio dashboard onde pode ver informações de monitorização relativamente a esse serviço. Para obter mais informações sobre a gestão dos seus serviços Web implementados, veja [Manage an Azure Machine Learning workspace (Gerir uma área de trabalho do Azure Machine Learning)](machine-learning-manage-workspace.md).

**Existe um local onde posso ver a saída da minha RRS/BES?**

Para RRS, vê normalmente o resultado na resposta do serviço Web. Também pode escrevê-lo para o Blob Storage do Azure. Para BES, o resultado é escrito num blob por predefinição. Também pode escrever a saída numa base de dados ou tabela, utilizando o módulo [Exportar dados][exportar dados].

**Posso criar serviços Web apenas a partir de modelos criados no Machine Learning Studio?**

Não, também pode criar serviços Web diretamente a partir dos Jupyter Notebooks e do RStudio.

**Onde posso encontrar informações sobre códigos de erro?**

Consulte [Códigos de erro do módulo Machine Learning](https://msdn.microsoft.com/library/azure/dn905910.aspx) para obter uma lista de códigos de erro e descrições.

## Escalabilidade

**O que é a escalabilidade do serviço Web?**

Atualmente, o ponto final predefinido está aprovisionado com 20 pedidos de RRS em simultâneo por ponto final. Pode aumentar para 200 pedidos em simultâneo por ponto final e pode aumentar cada serviço Web para 10 000 pontos finais por serviço Web, tal como descrito em [Dimensionar pontos finais da API](machine-learning-scaling-endpoints.md). Para BES, cada ponto final permite processar 40 pedidos de uma só vez e os pedidos adicionais que ultrapassem os 40 pedidos são colocados em fila de espera. Estes pedidos em fila de espera são executados automaticamente à medida que a fila avança.


**As tarefas de R dispersam-se pelos nós?**

Não.  


**Que quantidade de dados posso utilizar para formação?**

Os módulos no Machine Learning Studio suportam conjuntos de dados até 10 GB de dados numéricos em casos de utilização comum. Se um módulo precisar mais do que uma entrada de dados, 10 GB é o tamanho total de todas as entradas de dados. Também pode apresentar exemplos dos conjuntos de dados de maiores dimensões através de consultas no Hive ou na SQL Database do Azure ou do pré-processamento com os módulos [Aprender com as contagens][contas] módulos antes da ingestão.  

Os seguintes tipos de dados podem ser expandidos para conjuntos de dados de maiores dimensões durante a normalização da funcionalidade, estando limitados a menos de 10 GB:

- disperso
- categórico
- cadeias
- dados binários

Os seguintes módulos estão limitados a conjuntos de dados inferiores a 10 GB:

- Módulos de recomendador
- Módulo SMOTE
- Módulos de script: R, Python, SQL
- Módulos onde o tamanho dos dados de saída pode ser superior ao tamanho dos dados de entrada, tais como a adesão ou funcionalidade hash.
- Validação cruzada, hiperparâmetros do modelo de regulação, regressão ordinal e uma vs várias classes quando o número de iterações é muito grande.

Para conjuntos de dados superiores a alguns GB, deverá carregar os dados para o Storage do Azure ou para a SQL Database do Azure ou utilizar o HDInsight em vez de o carregar diretamente a partir de um ficheiro local.


**Existem limitações ao tamanho do vetor?**

As linhas e colunas são limitadas para a limitação de .NET do máx. Int: 2.147.483.647.

**É possível ajustar o tamanho da máquina virtual que está a ser utilizada para executar o serviço Web?**

Não.  

## Segurança e a disponibilidade

**Quem tem acesso ao ponto final de http para o serviço Web por predefinição? Como posso restringir o acesso ao ponto final?**

Depois de implementar um serviço Web, é criado um ponto final predefinido para esse serviço. O ponto final predefinido pode ser solicitado, utilizando a respetiva chave de API. Os pontos finais adicionais podem ser adicionados com as suas próprias chaves a partir do portal clássico do Azure ou através da programação com as APIs de gestão do serviço Web. As chaves de acesso são necessárias para solicitar o serviço Web. Consulte [Ligar a um serviço Web do Machine Learning](machine-learning-connect-to-azure-machine-learning-web-service.md) para obter mais informações.


**O que acontece se não for possível encontrar a minha conta de armazenamento do Azure?**

O Machine Learning Studio baseia-se numa conta de armazenamento do Azure para guardar dados intermédios ao executar o fluxo de trabalho. Esta conta de armazenamento é fornecida ao Machine Learning Studio no momento em que é criada uma área de trabalho. Depois de a área de trabalho ter sido criada, se a conta de armazenamento tiver sido eliminada e já não puder ser encontrada, a área de trabalho irá deixar de funcionar e todas as experiências nessa área de trabalho irão falhar.

Caso tenha eliminado acidentalmente a conta de armazenamento, recrie-a com o mesmo nome na mesma região que a conta de armazenamento eliminada. Depois disso, volte a sincronizar a Chave de Acesso.


**O que acontece se a minha chave de acesso da conta de armazenamento estiver dessincronizada?**

O Machine Learning Studio baseia-se numa conta de armazenamento do Azure fornecida pelo utilizador para armazenar dados intermédios ao executar o fluxo de trabalho. Esta conta de armazenamento é fornecida ao Machine Learning Studio no momento em que é criada uma área de trabalho e as chaves de acesso são associadas a essa área de trabalho. Se as Chaves de Acesso forem alteradas, depois de criada a área de trabalho, esta deixa de poder aceder à conta de armazenamento. Deixa de funcionar e todas as experimentações na mesma falham.

Se tiver alterado as Chaves de Acesso da conta de armazenamento, volte a sincronizá-las na área de trabalho através do portal clássico do Azure.  


## Azure Marketplace

Consulte as [perguntas mais frequentes sobre a publicação e utilização das aplicações no mercado do Machine Learning](machine-learning-marketplace-faq.md).

## Assistência e formação

**Onde posso obter formação sobre o Azure Machine Learning?**

O [Centro de Documentação do Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) aloja tutoriais em vídeo e manuais de instruções. Estes guias passo a passo incluem uma introdução aos serviços e percorrem o ciclo de vida das ciências de dados relativos à importação de dados, a limpeza de dados, criação de modelos preditivos e implementação dos mesmos na produção com o Azure Machine Learning.

Adicionamos materiais novos ao Machine Learning Center continuamente. Pode submeter pedidos para obter material de aprendizagem adicional no centro do Machine Learning no [fórum de comentários do utilizador](https://windowsazure.uservoice.com/forums/257792-machine-learning).

Também pode encontrar formação em [Microsoft Virtual Academy](http://www.microsoftvirtualacademy.com/training-courses/getting-started-with-microsoft-azure-machine-learning).

**Como posso obter assistência para o Azure Machine Learning?**

Para obter assistência técnica do Azure Machine Learning, aceda a [Assistência do Azure](/support/options/) e selecione **Machine Learning**.

O Azure Machine Learning tem também um fórum comunitário no MSDN onde pode fazer perguntas relacionadas com o Azure Machine Learning. O fórum é monitorizado pela equipa do Azure Machine Learning. Visite [Fórum do Azure](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning).

## Perguntas sobre faturação

**Como funciona a faturação do Machine Learning?**

O serviço Azure Machine Learning tem dois componentes: o Machine Learning Studio e os Serviços Web Machine Learning.

Enquanto estiver a avaliar o Machine Learning Studio, pode utilizar o escalão de faturação gratuito.  O escalão gratuito também lhe permite implementar um serviço Web clássico com capacidade limitada.

Se determinar que o Azure Machine Learning responde às suas necessidades, pode inscrever-se no escalão standard. Para se inscrever, deve ter uma Subscrição do Microsoft Azure.

No escalão Standard, é faturado mensalmente por cada área de trabalho que definir no Machine Learning Studio. Quando executa uma experimentação no studio, são-lhe faturados os recursos de computação durante essa execução. Quando implementa um Serviço Web Clássico, as transações e as horas de computação são faturadas numa base “Pay As You Go” (PAYG). 

Os Novos Serviços Web Machine Learning têm Planos de Faturação que permitem uma maior previsibilidade nos custos. O preço em escalões oferece tarifas com desconto aos clientes que precisam de uma grande capacidade.

Quando cria um plano, aceita um custo fixo que integra uma quantidade incluída de horas de computação da API e transações de API. Se precisar de mais quantidades incluídas, pode adicionar instâncias suplementares ao seu plano. Se precisar de muitas mais quantidades incluídas, pode escolher um plano de escalão superior que forneça significativamente mais quantidades incluídas e uma taxa com melhor desconto.

Depois de utilizar as quantidades incluídas nas instâncias existentes, a utilização adicional é cobrada com uma taxa de utilização excedida associada ao escalão do plano de faturação.

Nota: as quantidades incluídas são realocadas a cada 30 dias e as quantidades incluídas não utilizadas não são acumuladas.

Para obter informações adicionais sobre faturação e preços, consulte [Preços do Machine Learning](https://azure.microsoft.com/pricing/details/machine-learning/).

**O Machine Learning tem uma versão de avaliação gratuita?**

 O Azure Machine Learning tem uma opção de subscrição gratuita (veja [Preços de Machine Learning](https://azure.microsoft.com/pricing/details/machine-learning/) para obter detalhes), ao passo que o Machine Learning Studio disponibiliza uma versão de avaliação rápida de 8 horas (inicie sessão no [Machine Learning Studio](https://studio.azureml.net/?selectAccess=true&o=2) para obter esta versão de avaliação).
 
 Além disso, quando se inscreve numa versão de avaliação gratuita do Azure, pode experimentar todos os serviços do Azure durante um mês. Para obter mais informações sobre a versão de avaliação gratuita do Azure, visite [Perguntas mais frequentes sobre a versão de avaliação gratuita do Azure](/pricing/free-trial-faq/).

**O que é uma transação?**

Uma transação representa uma chamada de API à qual o Azure Machine Learning responde. As transações de chamadas do Serviço de Resposta-Pedido (RRS) e do Serviço de Execução de Lote (BES) são agregadas e cobradas conforme o seu plano de faturação.

**Posso utilizar as quantidades de transação incluídas num plano para transações RRS e BES?**

Sim, as suas transações de RRS e BES são agregadas e cobradas conforme o seu plano de faturação.

**O que é uma hora de computação de API?**

Uma hora de computação de API corresponde à unidade de faturação do tempo de duração das Chamadas à API com os recursos de computação de ML. Todas as suas chamadas são agregadas para fins de faturação. 

**Quanto tempo demora uma chamada à API de produção normal?**

Os tempos de chamada à API de produção podem variar significativamente e, geralmente, vão de centenas de milissegundos a alguns segundos. No entanto, podem necessitar de minutos, consoante a complexidade do processamento de dados e modelo de machine learning. A melhor forma de estimar os tempos de chamada à API de produção passa por referenciar um modelo no serviço de Machine Learning.

**O que é uma Hora de computação do Studio?**

Uma Hora de computação do Studio corresponde à unidade de faturação do tempo agregado de utilização dos recursos de computação no Studio pelas suas experimentações. 

**Nos Novos Serviços Web, para que serve o escalão dev/teste?**

Os novos Serviços Web do Azure ML fornecem vários escalões que pode utilizar para aprovisionar o seu plano de faturação. O escalão dev/teste é um escalão que fornece quantidades incluídas limitadas que lhe permitem testar a sua experimentação como um novo serviço Web sem incorrer em custos. Tem a oportunidade de fazer um “test drive” para ver como funciona.

**Existem encargos de armazenamento separados?** 

O escalão Gratuito de Machine Learning não requer ou permite um armazenamento separado. O escalão Standard de Machine Learning exige que os utilizadores tenham uma conta de armazenamento do Azure. O armazenamento do Azure é [faturado separadamente](https://azure.microsoft.com/pricing/details/storage/).

**Como é que o Machine Learning suporta o trabalho de elevada disponibilidade?** 

Os tempos de chamada à API de produção podem variar significativamente e, geralmente, vão de centenas de milissegundos a alguns segundos. No entanto, podem necessitar de minutos, consoante a complexidade do processamento de dados e modelo de machine learning. A melhor forma de estimar os tempos de chamada à API de produção passa por referenciar um modelo no serviço de Machine Learning.

**Que tipo específico de recursos de computação será utilizado pelas minhas chamadas à API de produção?**

O serviço Machine Learning é um serviço multi-inquilino e os recursos de computação reais utilizados no back-end variam e são otimizados no que toca ao desempenho e à previsibilidade.

### Gestão de Novos Serviços Web 

**O que acontece se eliminar o meu plano?**

O plano é removido da sua subscrição e é faturado por uma utilização proporcional.

Nota: não pode eliminar um plano que está a ser utilizado por um serviço Web. Para eliminar o plano, tem de atribuir um novo plano ao serviço Web ou eliminar o serviço Web.

**O que é uma instância de plano?**

Uma instância de plano é uma unidade de quantidades incluídas que pode adicionar ao seu plano de faturação. Quando seleciona um escalão de faturação para o seu plano de faturação, este inclui uma instância. Se precisar de mais quantidades incluídas, pode adicionar instâncias do escalão de faturação selecionado ao seu plano. 

**Quantas instâncias de plano posso adicionar?**

Pode ter uma instância do escalão dev/teste numa subscrição.

Para os escalões S1, S2 e S3, pode adicionar todas as que considerar necessárias. 

Nota: dependendo da sua utilização antecipada, poderá ser mais económico atualizar para um escalão com mais quantidades incluídas em vez de adicionar instâncias ao escalão atual.

**O que acontece quando altero os escalões do plano (atualização/mudança para um escalão inferior)?**

O plano antigo é eliminado e a utilização atual é faturada numa base proporcional. É criado um novo plano com todas as quantidades incluídas do escalão atualizado/alterado para o resto do período. 

Nota: as quantidades incluídas são alocadas por período e as quantidades não utilizadas não são acumuladas.

**O que acontece quando aumento as instâncias num plano?**

As quantidades são incluídas de forma rateada e podem demorar 24 horas até ficarem disponíveis. 

**O que acontece quando elimino uma instância de um plano?**

A instância é removida da sua subscrição e é faturado por uma utilização proporcional. 


### Inscrever-se nos planos dos Novos Serviços Web

**Como posso inscrever-me num plano?**

Existem duas formas de criar planos de faturação.

Quando implementa um novo serviço Web pela primeira vez, pode escolher um plano existente ou criar um novo plano. 

Os planos criados desta forma estão na sua região predefinida e o serviço Web será implementado nessa região. 

Se quiser implementar serviços em regiões que não a sua região predefinida, poderá ser útil definir os planos de faturação antes de implementar os serviços.

Nesse caso, pode iniciar sessão no portal dos Serviços Web do Azure Machine Learning e navegar até à página de planos. A partir daí, pode Adicionar e Eliminar planos, bem como modificar planos existentes.

**Que plano devo escolher para começar?**

Recomendamos que comece com o escalão S1 standard e monitorize o seu serviço quando à utilização. Se achar que está a utilizar as suas quantidades incluídas rapidamente, pode adicionar instâncias ou mudar para um escalão superior e obter taxas com melhores descontos. Pode ajustar o seu plano de faturação conforme necessário durante o ciclo de faturação. 

**Em que regiões estão disponíveis os novos planos?**

Os novos planos de faturação estão disponíveis nas três regiões de produção nas quais suportamos os novos serviços Web:

* EUA Centro-Sul
* Europa Ocidental
* Sudeste Asiático

**Tenho serviços Web em várias regiões. Preciso de um plano para cada região?**

Sim. Os preços dos planos variam consoante a região. Quando implementa um serviço Web noutra região, tem de lhe atribuir um plano específico para essa região.

### Novos Serviços Web – Utilização excedida

**Como posso ver se a utilização do meu serviço Web foi excedida?**

Pode ver a utilização de todos os seus planos na página Planos do portal dos Serviços Web Azure Machine Learning. Inicie sessão no portal e clique na opção de menu Planos. 

Nas colunas Transações e Computação da tabela, pode ver as quantidades incluídas do plano e a percentagem utilizada. 

**O que acontece quando utilizo a totalidade das quantidades incluídas no escalão dev/test?**

Os serviços com um escalão dev/test atribuído são interrompidos até ao próximo período ou até os mover para um dos escalões pagos.

**No caso dos Serviços Web Clássicos e da Utilização Excedida de Novos Serviços Web, como são calculados os preços para cargas de trabalho de Resposta-Pedido (RRS) e Lote (BES)?** 

Para cargas de trabalho RRS, é-lhe cobrada cada chamada de transação à API efetuada e o tempo de computação associado a esses pedidos. Os custos de Transação de API de Produção de RRS são calculados como o número total de chamadas à API que realiza multiplicado pelo preço de 1000 transações (rateado por transação individual). Os seus custos de Hora de Computação de API de Produção de RRS são calculados como a quantidade de tempo necessária para a execução de cada chamada à API, multiplicada pelo número total de transações de API, multiplicado pelo preço por Hora de Computação de API de Produção. 

Por exemplo, numa utilização excedida do escalão S1 Standard, 1.000.000 de transações de API com um tempo de execução individual de 0,72 segundos teria um custo de (1.000.000 * 0,50 $/Mil transações de API) 500 $ em custos de Transação de API de Produção e (1.000.000 * 0,72 * 2 $/h) 400 $ em Horas de Computação de API de Produção, o que totaliza 900 $.

Para cargas de trabalho BES, é cobrado da mesma forma. No entanto, os custos de transação de API representam o número de tarefas de lote submetidas e os custos de computação representam o tempo de computação associado a essas tarefas de lote. Os custos de Transação de API de Produção de BES são calculados como o número total de tarefas submetidas multiplicado pelo preço de 1000 transações (rateado por transação individual). Os custos de Hora de Computação de API de Produção de BES são calculados como a quantidade de tempo necessária para a execução de cada linha na sua tarefa, multiplicada pelo número total de linhas na sua tarefa, multiplicado pelo preço por Hora de Computação de API de Produção. Ao utilizar a calculadora de Machine Learning, o medidor de transação representa o número de tarefas que pretende submeter e o campo de tempo por transação representa o tempo combinado necessário para a execução de todas as linhas em cada tarefa. 

Por exemplo, numa utilização excedida do escalão S1 Standard, se submeter 100 tarefas por dia e cada uma for composta por 500 linhas com uma duração individual de 0,72 segundos, os custos de utilização excedida mensais seriam de (100 tarefas por dia = 3.100 tarefas/mês * 0,50 $/Mil transações de API) 1,55 $ em custos de Transação de API de Produção e (500 linhas * 0,72 s * 3100 tarefas * 2 $/h) 620 $ em Horas de Computação de API de Produção, o que totaliza 621,55 $.

### Serviços Web Clássicos do Azure ML

**A opção “Pay As You Go” ainda está disponível?**
Sim, os Serviços Web Clássicos ainda estão disponíveis no Azure Machine Learning.  

### Escalão Gratuito e Standard do Azure Machine Learning

**O que está incluído no escalão Gratuito do Azure Machine Learning?**

O escalão Gratuito do Azure Machine Learning destina-se a fornecer uma introdução aprofundada ao Azure Machine Learning Studio. Tudo o que precisa é uma conta Microsoft para se inscrever. O escalão Gratuito inclui acesso gratuito a uma área de trabalho do Azure Machine Learning Studio por [conta Microsoft](https://www.microsoft.com/account/default.aspx). Inclui a capacidade de utilizar até 10 GB de armazenamento e a capacidade de operacionalizar modelos como APIs de teste. As cargas de trabalho do escalão Gratuito não são abrangidas por um SLA e destinam-se apenas ao desenvolvimento e utilização pessoal. As cargas de trabalho do escalão Gratuito não podem aceder aos dados através da ligação a um servidor SQL no local. 

**O que está incluído no escalão e planos Standard do Azure Machine Learning?**

O escalão Standard do Azure Machine Learning é uma versão de produção paga do Azure Machine Learning Studio. A taxa mensal do serviço Azure ML Studio é faturada por área de trabalho por mês e é rateada em meses parciais. As horas de experimentação do Azure ML Studio são faturadas por hora de computação por experimentação ativa. A faturação é proporcional às horas parciais.  

O serviço da API do Azure ML é faturado consoante a opção utilizada, ou seja, um serviços Web clássico ou um novo serviço Web. 

Os encargos seguintes estão agregados por área de trabalho para a sua subscrição. 

* Subscrição de Áreas de Trabalho do Machine Learning – a Subscrição de Áreas de Trabalho do Machine Learning é uma taxa mensal que fornece acesso a uma área de trabalho do ML Studio e é necessária para executar experimentações no Studio e utilizar as APIs de produção.
* Horas de Experimentação do Studio – este medidor agrega todos os encargos de computação acumulados pela execução de experimentações no ML Studio e de chamadas à API de produção no ambiente de teste.
* Aceda aos dados através da ligação a um servidor SQL no local nos seus modelos para fins de formação e classificação.
* Para Serviços Web Clássicos: 
    * Horas de Computação da API de Produção – este medidor inclui encargos de computação acumulados pelos serviços Web em execução na produção.
    * Transações de API de Produção (em milhares) – este medidor inclui encargos acumulados por chamada para o seu serviço Web de produção.

Além dos encargos acima, no caso dos Novos Serviços Web, os encargos são agregados ao plano selecionado: 

* Plano de API S1/S2/S3 Standard (Unidades) - este medidor representa o tipo de instância selecionada para os Novos Serviços Web
* Horas de Computação de API Excedidas S1/S2/S3 Standard – este medidor inclui encargos acumulados pelos Novos Serviços Web em execução na produção depois de esgotadas as quantidades incluídas na instância ou instâncias existentes. A utilização adicional é cobrada de acordo com a sobretaxa associada ao escalão do plano S1/S2/S3.
* Transações de API Excedidas S1/S2/S3 Standard (em milhares) – este medidor inclui encargos acumulados por chamada para o seu Novo Serviço Web de produção depois de esgotadas as quantidades incluídas na instância ou instâncias existentes. A utilização adicional é cobrada de acordo com a sobretaxa associada ao escalão do plano S1/S2/S3.
* Quantidade Incluída de Horas de Computação de API – com os Novos Serviços Web, este medidor representa a quantidade incluída das Horas de Computação de API 
* Quantidade Incluída de Transações de API (em milhares) – com os Novos Serviços Web, este medidor representa a quantidade incluída de Transações de API 


**Como posso inscrever-me no escalão Gratuito do Azure ML?**

Tudo o que precisa é uma conta Microsoft. Aceda à [home page do Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) e clique em **Iniciar Agora**. Inicie sessão com a sua conta Microsoft e será criada uma área de trabalho de escalão Gratuito para si. Pode começar a explorar e criar experimentações de Machine Learning imediatamente.

**Como posso inscrever-me no Escalão Standard do Azure ML?**

Tem de ter acesso a uma subscrição do Azure para criar uma área de trabalho de ML Standard. Pode inscrever-se numa avaliação gratuita de 30 dias da subscrição do Azure e, posteriormente, atualizar para uma subscrição paga do Azure ou comprar de imediato uma subscrição paga do Azure. Em seguida, pode criar uma área de trabalho do Machine Learning a partir do portal clássico do Microsoft Azure após obter acesso à subscrição. Veja as [instruções passo a passo](https://azure.microsoft.com/trial/get-started-machine-learning-b/).

Em alternativa, pode ser convidado pelo proprietário de uma área de trabalho de ML Standard para aceder à área de trabalho do mesmo.

**Posso especificar a minha própria conta de armazenamento de blobs do Azure para utilizar com o escalão Gratuito?**

Não, o escalão Standard é equivalente à versão do serviço Machine Learning que estava disponível antes de os escalões serem introduzidos.

**Posso implementar os meus modelos de machine learning como APIs no escalão Gratuito?**

Sim, pode operacionalizar modelos de machine learning para serviços de API de teste como parte do escalão gratuito. Para colocar o serviço de API de teste em produção e obter um ponto final de produção para o serviço operacionalizado, tem de utilizar o escalão Standard. 

**Qual é a diferença entre a Avaliação Gratuita do Azure e o escalão Gratuito do Azure Machine Learning?**

A [versão de avaliação gratuita do Microsoft Azure](https://azure.microsoft.com/free/) oferece créditos que podem ser aplicados a qualquer serviço do Azure por um mês. O escalão Gratuito do Azure Machine Learning oferece acesso contínuo especificamente ao serviço Azure Machine Learning para cargas de trabalho de não produção.

**Como posso mover uma experimentação do escalão Gratuito para o escalão Standard?**

Para copiar as experimentações do escalão Gratuito para o Standard:

1.  Inicie sessão no Azure Machine Learning Studio e confirme se vê tanto a área de trabalho Gratuita como a área de trabalho Standard no seletor de área de trabalho na barra de navegação superior.
2.  Mude para a área de trabalho Gratuita se estiver na área de trabalho Standard.
3.  Na vista de lista de experimentações, selecione uma experimentação que queira copiar e clique no botão de comando Copiar.
4.  Selecione a área de trabalho Standard na caixa de diálogo de pop-up e clique no botão Copiar.
    Todos os conjuntos de dados associados, modelos preparados, etc., são copiados juntamente com a experimentação para a área de trabalho Standard.
6.  Tem de executar novamente a experimentação e voltar a publicar o seu serviço Web na área de trabalho Standard.

### Área de trabalho do Studio

**Irei ver faturas diferentes para áreas de trabalho diferentes?**

Os encargos da área de trabalho são divididos para cada medidor aplicável numa única fatura.

**Que tipo específico de recursos de computação será utilizado pela minha experimentação?**

O serviço Machine Learning é um serviço multi-inquilino e os recursos de computação reais utilizados no back-end variam e são otimizados no que toca ao desempenho e à previsibilidade.

### Acesso de convidado

**O que é o Acesso de Convidado do Azure Machine Learning Studio?**

O Acesso de Convidado é uma experimentação de avaliação restrita que lhe permite criar e executar experimentações no Azure Machine Learning Studio sem custos e sem autenticação. As sessões de convidados são não persistentes (não é possível guardá-las) e estão limitadas a 8 horas. Outras limitações incluem falta de suporte para R e Python, falta de APIs de teste e capacidade de armazenamento e tamanho do conjunto de dados restrito. Por comparação, os utilizadores que optem por iniciar sessão com uma conta Microsoft têm acesso total ao escalão Gratuito do Machine Learning Studio, descrito acima, que inclui uma área de trabalho persistente e capacidades mais abrangentes. Escolha a sua experimentação gratuita do Machine Learning ao clicar em **Introdução**, em [https://studio.azureml.net](https://studio.azureml.net) e selecione Acesso de Convidado ou Iniciar sessão com uma conta Microsoft.

<!-- Module References -->
[imagem-leitor]: https://msdn.microsoft.com/library/azure/893f8c57-1d36-456d-a47b-d29ae67f5d84/
[aderir]: https://msdn.microsoft.com/library/azure/124865f7-e901-4656-adac-f4cb08248099/
[módulos de machine learning]: https://msdn.microsoft.com/library/azure/6d9e2516-1343-4859-a3dc-9673ccec9edc/
[partição e amostras]: https://msdn.microsoft.com/library/azure/a8726e34-1b3e-4515-b59a-3e4a475654b8/
[importar dados]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[exportar dados]: https://msdn.microsoft.com/library/azure/7A391181-B6A7-4AD4-B82D-E419C0D6522C
[dividir]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[python]: https://msdn.microsoft.com/library/azure/CDB56F95-7F4C-404D-BDE7-5BB972E6F232
[contas]: https://msdn.microsoft.com/library/azure/dn913056.aspx



<!--HONumber=Sep16_HO4-->


