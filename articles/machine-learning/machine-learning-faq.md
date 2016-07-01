<properties
    pageTitle="Perguntas mais frequentes do Azure Machine Learning | Microsoft Azure"
    description="Introdução do Azure Machine Learning: Perguntas mais frequentes que abrangem a faturação, capacidades e limitações de um serviço em nuvem para a modelação preditiva simplificada."
    keywords="machine learning introduction,predictive modeling,what is machine learning"
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
    ms.date="04/18/2016"
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


## Perguntas sobre faturação

**Como funciona a faturação do Machine Learning?**

Para obter informações sobre faturação e preços, consulte [Preços do Machine Learning](https://azure.microsoft.com/pricing/details/machine-learning/).

**O Machine Learning tem uma versão de avaliação gratuita?**

 Quando inicia sessão numa versão de avaliação gratuita do Azure, pode experimentar os serviços do Azure durante um mês. Para obter mais informações sobre a versão de avaliação gratuita do Azure, visite [Perguntas mais frequentes sobre a versão de avaliação gratuita do Azure](/pricing/free-trial-faq/).

## Perguntas sobre o Machine Learning Studio

### Criar uma experiência

**Existe um controlo da versão ou uma integração de Git para os gráficos de experimentação?**

No entanto, nenhum Machine Learning Studio retém cada iteração de uma experiência que não possa ser modificada por outros utilizadores.
Para obter mais informações, consulte [Gerir iterações de experimentação no Machine Learning Studio](machine-learning-manage-experiment-iterations.md).

### Importar e exportar dados para o Machine Learning

**Que origens de dados são suportadas pelo Machine Learning?**

Os dados podem ser carregados para uma experiência do Machine Learning Studio sob uma de três formas: através do carregamento de um ficheiro local como um conjunto de dados, através de um módulo para importar dados de serviços de dados em nuvem ou através da importação de um conjunto de dados guardado a partir de outra experiência. Consulte [Importar dados de formação para o Machine Learning Studio](machine-learning-data-science-import-data.md) para saber mais sobre os formatos de ficheiro suportados.


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
- Validação cruzada, hiperparâmetros do modelo de sintonização, regressão ordinal e uma vs várias classes quando o número de iterações é muito grande.

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

**É possível utilizar algo semelhante ao [PMML](http://en.wikipedia.org/wiki/Predictive_Model_Markup_Language) para definir um modelo?**

Não, isso não é suportado. No entanto, o código personalizado para o R e Python pode ser utilizado para definir um módulo.

**Quantos módulos posso executar em paralelo na minha experiência?**  

Pode executar até 4 módulos em paralelo numa experiência.


### Processamento de dados

**Existe uma capacidade de visualizar os dados (para além das visualizações de R) interativamente na experiência?**

Ao clicar na saída de um módulo, pode visualizar os dados e obter estatísticas.

**Quando pré-visualizar os resultados ou dados no browser, porque é que o número de linhas e colunas é limitado?**

Uma vez que os dados estão a ser transmitidos para o browser e podem ser de grande volume, o tamanho dos dados é limitado para impedir que i Machine Learning Studio fique mais lento. Para visualizar todos os dados/resultados, é melhor transferir os dados e utilizar o Excel ou outra ferramenta.

### Algoritmos

**Que algoritmos existentes são suportados no Machine Learning Studio?**

O Machine Learning Studio dispõe de algoritmos avançados como as árvores de decisão escaláveis, os sistemas de recomendação bayesianos, as redes neurais profundas e o grupo de decisões desenvolvidas junto da Microsoft Research. Estão também incluídos os pacotes open-source do Machine Learning como o Adalberto como o Vowpal Wabbit. O Machine Learning Studio suporta algoritmos de machine learning para classificação de várias classes e binária, regressão e clustering. Consulte a lista completa de [Módulos do Machine Learning][módulos de machine learning].

**Sugere automaticamente o direito ao algoritmo do Machine Learning utilizar com os meus dados?**

Não. No entanto, existem várias formas de o Machine Learning Studio comparar os resultados de cada algoritmo para determinar o algoritmo certo para o seu problema.

**Segue alguma diretriz para escolher um algoritmo em vez de outro relativamente aos algoritmos fornecidos?**
Consulte o artigo [Como escolher um algoritmo](machine-learning-algorithm-choice.md).

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

O Machine Learning Studio suporta hoje 400 + CRAN R hoje e aqui está a [lista atual](http://az754797.vo.msecnd.net/docs/RPackages.xlsx) de todos os pacotes incluído. Além disso, consulte [Expandir a sua experiência com R ](machine-learning-extend-your-experiment-with-r.md) para saber como obter esta lista manualmente. Se o pacote que pretende não estiver nesta lista, forneça o nome de pacote no [fórum de comentários do utilizador](http://go.microsoft.com/fwlink/?LinkId=404231).

**É possível construir um módulo R personalizado?**

Sim, consulte [Autor dos módulos R personalizados no Azure Machine Learning](machine-learning-custom-r-modules.md) para obter mais informações.

**Existe um ambiente REPL para R?**

Não, não existe nenhum ambiente REPL para R no studio.

### Módulo de Python

**É possível construir um módulo Python personalizado?**

Atualmente não, mas pode utilizar um ou mais módulos [python][Executar script do Python] para obter o mesmo resultado.

**Existe um ambiente REPL para Python?**

Pode utilizar os Jupyter Notebooks no Machine Learning Studio. Para obter mais informações, consulte [Apresentação dos Jupyter Notebooks no Azure Machine Learning Studio] (http://blogs.technet.com/b/machinelearning/archive/2015/07/24/introducing-jupyter-notebooks-in-azure-ml-studio.aspx).

## Serviço Web

###Reparametrização dos modelos programáticos

**Como posso fazer a reparametrização dos modelos programáticos do Azure Machine Learning?**

Utilize as APIs para a reparametrização. Consulte [Reparametrização dos modelos programáticos do Machine Learning](machine-learning-retrain-models-programmatically.md) para obter mais informações. O código de exemplo também está disponível na [demonstração de reparametrização do Microsoft Azure Machine](https://azuremlretrain.codeplex.com/).

### Criar

**Posso implementar o modelo localmente ou numa aplicação sem uma ligação à internet?**

Não.


**Existe uma latência de linha de base que é esperada para todos os serviços Web?**

Consulte os [limites de subscrição do Azure](../azure-subscription-service-limits.md)

### Utilizar

**Quando deveria querer executar o meu modelo preditivo como um Serviço de Execução de Lotes em comparação com um serviço de resposta a um pedido?**

O serviço de resposta a um pedido (RRS) é um serviço Web de latência baixa e alta escala que é utilizado para fornecer uma interface para modelos sem monitorização de estado que são criados e implementados a partir do ambiente de experiência. O Serviço de Execução de Lotes (BES) é um serviço de resultados assíncronos que apresenta um lote de registos de dados. A entrada para BES é semelhante à introdução de dados utilizada em RRS. A principal diferença é que BES lê um bloco de registos a partir de uma variedade de origens, como o serviço Blob e o serviço Tabela no Azure, a SQL Database do Azure, o HDInsight (consulta do grupo) e origens HTTP. Consulte [Como consumir os serviços Web do Machine Learning](machine-learning-consume-web-services.md) para obter mais informações.

**Como posso atualizar o modelo para o serviço Web implementado?**

Atualizar um modelo preditivo para um serviço já implementado é tão simples como modificar e executar novamente a experiência que utilizou para criar e guardar o modelo treinado. Assim que tiver uma nova versão do modelo treinado disponível, o Machine Learning Studio irá pedir-lhe se pretende atualizar o seu serviço Web. Consulte [Implementar um serviço Web do Machine Learning](machine-learning-publish-a-machine-learning-web-service.md) para obter detalhes sobre como atualizar um serviço Web implementado.

Também pode utilizar as APIs para a reparametrização.
Consulte [Reparametrização dos modelos programáticos do Machine Learning](machine-learning-retrain-models-programmatically.md) para obter mais informações. O código de exemplo também está disponível na [demonstração de reparametrização do Microsoft Azure Machine](https://azuremlretrain.codeplex.com/).

**Como posso monitorizar o meu serviço Web implementado na produção?**

Depois de ter sido implementado um modelo preditivo, pode monitorizá-lo a partir do portal clássico do Azure. Cada serviço implementado tem o seu próprio dashboard onde pode ver informações de monitorização relativamente a esse serviço.

**Existe um local onde posso ver a saída da minha RRS/BES?**

Para RRS, vê normalmente o resultado na resposta do serviço Web. Também pode escrevê-lo para o Blob Storage do Azure. Para BES, o resultado é escrito num blob por predefinição. Também pode escrever a saída numa base de dados ou tabela, utilizando o módulo [Exportar dados][exportar dados].

**Posso criar serviços Web apenas a partir de modelos criados no Machine Learning Studio?**

Não, também pode criar serviços Web diretamente a partir dos Jupyter Notebooks e do RStudio.

**Onde posso encontrar informações sobre códigos de erro?**

Consulte [Códigos de erro do módulo Machine Learning](https://msdn.microsoft.com/library/azure/dn905910.aspx) para obter uma lista de códigos de erro e descrições.

## Escalabilidade

**O que é a escalabilidade do serviço Web?**

Atualmente, o ponto final predefinido está aprovisionado com 20 pedidos de RRS em simultâneo por ponto final. Pode aumentar para 200 pedidos em simultâneo por ponto final e pode aumentar cada serviço Web para 10 000 pontos finais por serviço Web, tal como descrito em [Dimensionar pontos finais da API](machine-learning-scaling-endpoints.md). Para BES, cada ponto final permite processar 40 pedidos de uma só vez e os pedidos adicionais que ultrapassem os 40 pedidos são colocados em fila de espera. Estes pedidos em fila de espera serão executados automaticamente à medida que a fila avança.


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


**O que acontece se não for possível encontrar a minha conta de armazenamento do Asure?**

O Machine Learning Studio baseia-se numa conta de armazenamento do Azure para guardar dados intermédios ao executar o fluxo de trabalho. Esta conta de armazenamento é fornecida ao Machine Learning Studio no momento em que é criada uma área de trabalho. Depois de a área de trabalho ter sido criada, se a conta de armazenamento tiver sido eliminada e já não puder ser encontrada, a área de trabalho irá deixar de funcionar e todas as experiências nessa área de trabalho irão falhar.

Caso tenha eliminado acidentalmente a conta de armazenamento, a única forma de a recuperar é recriar a conta de armazenamento com o mesmo nome na mesma região que a conta de armazenamento eliminada. Depois disso, volte a sincronizar a chave de acesso.


**O que acontece se a minha chave de acesso da conta de armazenamento estiver dessincronizada?**

O Machine Learning Studio baseia-se numa conta de armazenamento do Azure para guardar dados intermédios ao executar o fluxo de trabalho. Esta conta de armazenamento é fornecida ao Machine Learning Studio no momento em que é criada uma área de trabalho e as chaves de acesso são associadas a essa área de trabalho. Depois de a área de trabalho ter sido criada, se as chaves de acesso forem alteradas, esse espaço de armazenamento já não consegue aceder à conta de armazenamento - deixará de funcionar e todas as experiências começarão a falhar.

Se tiver alterado as chaves de acesso da conta de armazenamento, volte a sincronizar as teclas de acesso no espaço de trabalho utilizando o portal clássico do Azure.  


## Azure Marketplace

Consulte as [perguntas mais frequentes sobre a publicação e utilização das aplicações no mercado do Machine Learning](machine-learning-marketplace-faq.md).

## Assistência e formação

**Onde posso obter formação sobre o Azure Machine Learning?**

O [Centro de documentação do Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) aloja tutoriais em vídeo e manuais de instruções. Estes guias passo a passo incluem uma introdução aos serviços e percorrem o ciclo de vida das ciências de dados relativos à importação de dados, a limpeza de dados, criação de modelos preditivos e implementação dos mesmos na produção com o Azure Machine Learning.

Iremos adicionar continuamente material novo ao centro do Machine Learning. Pode submeter pedidos para obter material de aprendizagem adicional no centro do Machine Learning no [fórum de comentários do utilizador](https://windowsazure.uservoice.com/forums/257792-machine-learning).

Também pode encontrar formação em [Microsoft Virtual Academy](http://www.microsoftvirtualacademy.com/training-courses/getting-started-with-microsoft-azure-machine-learning).

**Como posso obter assistência para o Azure Machine Learning?**

Para obter assistência técnica do Azure Machine Learning, aceda a [Assistência do Azure](/support/options/) e selecione **Machine Learning**.

O Azure Machine Learning tem também um fórum comunitário no MSDN onde pode fazer perguntas relacionadas com o Azure Machine Learning. O fórum é monitorizado pela equipa do Azure Machine Learning. Visite [Fórum do Azure](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning).


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



<!--HONumber=Jun16_HO2-->


