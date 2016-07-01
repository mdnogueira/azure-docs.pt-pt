<properties
    pageTitle="Descrição geral da funcionalidade do Azure Batch | Microsoft Azure"
    description="Conheça as funcionalidades do serviço Batch e das respetivas APIs de um ponto de vista de programação."
    services="batch"
    documentationCenter=".net"
    authors="yidingzhou"
    manager="timlt"
    editor=""/>

<tags
    ms.service="batch"
    ms.devlang="multiple"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="big-compute"
    ms.date="05/12/2016"
    ms.author="yidingz;marsma"/>

# Descrição geral das funcionalidades do Azure Batch

Este artigo fornece uma descrição geral básica das funcionalidades principais da API do serviço Azure Batch. Se programar uma solução computacional distribuída ao utilizar as APIs [REST Batch][batch_rest_api] ou [.NET Batch][batch_net_api], irá utilizar muitas das entidades e funcionalidades descritas abaixo.

> [AZURE.TIP] Para obter uma descrição geral técnica de um nível superior do Batch, consulte [Noções básicas do Azure Batch](batch-technical-overview.md).

## <a name="workflow"></a>Fluxo de trabalho do serviço Batch

O seguinte fluxo de trabalho de alto nível é o que normalmente se utiliza por quase todos os cenários computacionais distribuídos desenvolvidos no âmbito do serviço Batch:

1. Carregue os *ficheiros de dados* que pretende utilizar no seu cenário computacional distribuído para uma conta de [Armazenamento do Azure][azure_storage]. Estes ficheiros têm de estar na conta de Armazenamento para que o serviço Batch possa aceder aos mesmos. As tarefas irão transferir estes ficheiros para os [nós de computação](#computenode) quando são executados.

2. Carregue os *ficheiros binários* dependentes para a sua conta de Armazenamento. Estes ficheiros binários incluem o programa a ser executado pelas tarefas e qualquer uma das respetivas assemblagens dependentes. Estes ficheiros também têm de ser acedidos a partir da sua conta de Armazenamento, de modo a poderem ser transferidos para os nós de computação pelas tarefas.

3. Crie um [Conjunto](#pool) de nós de computação. Especifique o [tamanho dos nós de computação][cloud_service_sizes] a utilizar quando o conjunto for criado e, quando uma tarefa for executada, é-lhe atribuído um nó neste conjunto.

4. Crie uma [Tarefa](#job). Uma tarefa permite-lhe gerir uma coleção de tarefas.

5. Adicione [Tarefas](#task) à tarefa. Cada tarefa utiliza o programa que carregou para processar informações no(s) ficheiro(s) de dados que carregou para a sua conta de Armazenamento.

6. Monitorize o progresso da tarefa e obtenha os resultados.

> [AZURE.NOTE] Vai necessitar de uma [conta do Batch](batch-account-create-portal.md) para utilizar o serviço Batch, sendo que quase todas as soluções utilizam uma conta de [Armazenamento do Azure][azure_storage] para obtenção e armazenamento de ficheiros. O Batch atualmente suporta apenas o tipo de conta de armazenamento **Fins gerais**, conforme descrito no passo n.º 5 [Criar uma conta de armazenamento](../storage/storage-create-storage-account.md#create-a-storage-account) em [Sobre as contas de armazenamento do Azure](../storage/storage-create-storage-account.md).

Nas secções abaixo, vai conhecer cada um dos recursos mencionados no fluxo de trabalho acima, bem como muitas outras funcionalidades do Batch que vão permitir o seu cenário computacional distribuído.

## <a name="resource"></a> Recursos do serviço Batch

Quando utilizar o Batch, vai utilizar muitos dos seguintes recursos. Alguns destes recursos, tais como contas, nós de computação, conjuntos e tarefas, são utilizados em todas as soluções Batch. Outros recursos, como as agendas de tarefas e os pacotes de aplicações, são funcionalidades úteis, mas opcionais.

- [Conta](#account)
- [Nó de computação](#computenode)
- [Conjunto](#pool)
- [Tarefa](#job)
- [Tarefa](#task)
    - [Tarefa de início](#starttask)
    - [Tarefa do gestor de tarefas](#jobmanagertask)
    - [Tarefas de preparação e de lançamento da tarefa](#jobpreprelease)
    - [Tarefas de várias instâncias](#multiinstance)
    - [Dependências de tarefas](#taskdep)
- [Agendas de tarefas](#jobschedule)
- [Pacotes de aplicações](#appkg)

### <a name="account"></a>Conta

Uma conta do Batch é uma entidade identificada exclusivamente no âmbito do serviço Batch. Todo o processamento está associado a uma conta do Batch. Quando efetua operações com o serviço Batch, necessita do nome da conta e da chave da conta. Para criar e gerir uma conta do Batch, consulte [Criar e gerir uma conta do Azure Batch no portal do Azure](batch-account-create-portal.md).

### <a name="computenode"></a>Nó de computação

Um nó de computação é uma máquina virtual do Azure dedicada a uma carga de trabalho específica para a sua aplicação. O tamanho de um nó determina o número de núcleos de CPU, a capacidade da memória e o tamanho do sistema de ficheiros local que está alocado ao nó. Um nó pode ser de qualquer um dos [tamanhos de nó de serviço em nuvem][cloud_service_sizes], exceto A0.

Os nós podem executar executáveis e scripts, incluindo executáveis (.exe), ficheiros de comandos (.cmd), ficheiros Batch (.bat) e scripts do PowerShell. Um nó também tem os seguintes atributos:

- Uma **estrutura de pastas** standard e **variáveis de ambiente** associadas com detalhes sobre os caminhos são criadas em cada nó de computação. Consulte [Ficheiros e diretórios](#files) abaixo para obter mais informações.
- **Variáveis de ambiente** disponíveis para referência por tarefas.
- Definições de **Firewall** que são configuradas para controlar o acesso.
- Se o **acesso remoto** a um nó de computação for necessário (para depuração, por exemplo), um ficheiro RDP pode ser obtido e, em seguida, pode ser utilizado para aceder ao nó através do *Ambiente de Trabalho Remoto*.

### <a name="pool"></a>Conjunto

Um conjunto é uma coleção de nós no qual a aplicação é executada. O conjunto pode ser criado manualmente por si ou pelo serviço Batch automaticamente quando especifica o trabalho a ser realizado. Pode criar e gerir um conjunto que satisfaça as necessidades da sua aplicação, sendo que os conjuntos podem ser utilizados apenas pela conta do Batch na qual foram criados. Uma conta do Batch pode ter mais do que um conjunto.

Os conjuntos do Azure Batch são criados com base na plataforma de computação principal do Azure: os conjuntos do Batch proporcionam a alocação em grande escala, a instalação de aplicações, a distribuição de dados e a monitorização do estado de funcionamento, bem como o ajuste flexível do número de nós de computação dentro de um conjunto (dimensionamento).

Um nome e um endereço IP exclusivos são atribuídos a cada nó que seja adicionado a um conjunto. Quando um nó é removido de um conjunto, quaisquer alterações efetuadas no sistema operativo ou nos ficheiros são perdidas e o respetivo nome e endereço IP são lançados para utilização futura. Quando um nó deixa um conjunto, a sua duração termina.

Pode configurar um conjunto para permitir a comunicação entre os nós dentro do mesmo. Se num conjunto for solicitada a comunicação intraconjunto, o serviço Batch permite portas superiores a 1100 em cada nó no conjunto. Cada nó no conjunto está configurado para permitir ligações de entrada apenas a este intervalo de portas e apenas a partir de outros nós dentro do conjunto. Se a aplicação não necessitar de comunicação entre nós, o serviço Batch pode alocar um número potencialmente grande de nós ao conjunto a partir de vários clusters e centros de dados diferentes para permitir uma maior potência de processamento paralelo.

Quando cria um conjunto, pode especificar os seguintes atributos:

- **Tamanho dos nós** no conjunto
    - Deve ser selecionado um tamanho de nó adequado, tendo em conta as caraterísticas e os requisitos da aplicação ou das aplicações que vão ser executadas nos nós. Normalmente, o tamanho dos nós é selecionado partindo do princípio de que será executada uma tarefa de cada vez no nó. Ter em consideração aspetos como se a aplicação tem vários threads e a quantidade de memória que consome irá ajudar a determinar o tamanho de nó mais adequado e económico. É possível ter várias tarefas atribuídas e várias instâncias de aplicações executadas em paralelo, sendo que nesse caso é normalmente escolhido um nó maior. Consulte “Política de agendamento de tarefas” abaixo para obter mais informações.
    - Todos os nós num conjunto têm de ser do mesmo tamanho. Se pretende executar aplicações diferentes com requisitos de sistema e/ou níveis de carga diferentes , devem ser criados conjuntos em separado.
    - Todos os [tamanhos de nó de serviço em nuvem][cloud_service_sizes] podem ser configurados para um conjunto, exceto A0.

- **Família de sistemas operativos** e **versão** que é executada nos nós
    - Tal como acontece com as funções de trabalho no âmbito dos Serviços Cloud, a *Família de SO* e a *Versão do SO* podem ser especificadas (para obter mais informações sobre as funções de trabalho, consulte a secção [Informações sobre os serviços em nuvem][about_cloud_services] em *Opções de Alojamento de Computação Fornecidas pelo Azure*).
    - A Família de SO também determina quais as versões do .NET que estão instaladas com o SO.
    - Tal como com as funções de trabalho, recomenda-se que `*` seja especificado para a Versão do SO, para que os nós sejam automaticamente atualizados e que não exista nenhum trabalho necessário para servir versões recém-publicadas. O caso de utilização principal para escolher uma versão do SO específica tem o intuito de garantir que é mantida a compatibilidade da aplicação, permitindo a realização de testes de retrocompatibilidade antes de permitir que a versão seja atualizada. Assim que estiver validada, a versão do SO para o conjunto pode ser atualizada e a nova imagem do SO instalada – qualquer tarefa em execução será interrompida e colocada novamente em fila.

- **Número de nós de destino** que devem estar disponíveis para o conjunto

- **Política de dimensionamento** para o conjunto
    - Para além do número de nós, também pode especificar uma [fórmula de dimensionamento automático](batch-automatic-scaling.md) para um conjunto. O serviço Batch irá executar a fórmula e ajustar o número de nós dentro do conjunto com base em vários parâmetros de conjunto e tarefa que pode especificar.

- Política de **agendamento de tarefas**
    - A opção de configuração [máximo de tarefas por nó](batch-parallel-node-tasks.md) determina o número máximo de tarefas que podem ser executadas em paralelo em cada nó dentro do conjunto.
    - A configuração predefinida é que uma tarefa seja executada num nó de computação de cada vez, mas existem cenários onde é vantajoso ter mais do que uma tarefa executada num nó ao mesmo tempo. Um exemplo é aumentar a utilização do nó se uma aplicação tiver de aguardar por E/S. Ter mais de uma aplicação executada em simultâneo irá aumentar a utilização da CPU. Outro exemplo consiste em reduzir o número de nós no conjunto. Isto poderia reduzir a quantidade de transferência de dados necessária para grandes conjuntos de dados de referência – se um tamanho de nó A1 for suficiente para uma aplicação, pode ser escolhido em alternativa o tamanho de nó A4 e o conjunto pode ser configurado para 8 tarefas paralelas, cada uma a utilizar um núcleo.
    - Também é possível especificar um “tipo de preenchimento” que determina se o Batch propaga as tarefas uniformemente em todos os nós ou se preenche cada nó com o número máximo de tarefas antes de atribuir tarefas a outro nó no conjunto.

- **Estado de comunicação** dos nós no conjunto
    - Um conjunto pode ser configurado para permitir a comunicação entre os nós no conjunto, que determina a respetiva infraestrutura de rede subjacente. Tenha em atenção que isto também tem impacto sobre a colocação dos nós nos clusters.
    - Na maior parte dos cenários, as tarefas operam de forma independente e não necessitam de comunicar entre si, mas pode existir algumas aplicações nas quais as tarefas têm de comunicar.

- **Tarefa de início** para nós no conjunto
    - Uma *tarefa de início* pode ser especificada e é executada sempre que um nó de computação se junta ao conjunto e quando um nó é reiniciado. Isto é frequentemente utilizado para instalar uma aplicação a ser utilizada pelas tarefas em execução no nó.

### <a name="job"></a>Tarefa

Uma tarefa é uma coleção de tarefas e especifica como a computação é efetuada em nós de computação num conjunto.

- A tarefa especifica o **conjunto** no qual o trabalho será executado. O conjunto pode ser um conjunto existente, criado anteriormente para utilização por parte de muitas tarefas ou criado a pedido para cada tarefa associada uma agenda de tarefas, ou para todas as tarefas associadas a uma agenda da tarefas.
- É possível especificar uma **prioridade de tarefa** opcional. Quando uma tarefa for submetida com uma prioridade superior à das tarefas atualmente em curso, as tarefas da tarefa de prioridade superior são introduzidas na fila à frente das tarefas da tarefa de prioridade inferior. As tarefas de prioridade inferior que já estão em execução não serão substituídas.
- As **restrições** de tarefas especificam determinados limites para as suas tarefas.
    - É possível definir para as tarefas uma **hora máxima do relógio**. Se as tarefas forem executadas durante mais tempo do que a hora máxima do relógio especificada, a tarefa e todas as tarefas associadas serão terminadas.
    - O Azure Batch consegue detetar as tarefas que falham e repeti-las. O **número máximo de repetições de tarefas** pode ser especificado como uma restrição, incluindo o facto de uma tarefa ser sempre ou nunca repetida. Repetir uma tarefa significa que a tarefa é recolocada na fila para ser executada novamente.
- É possível adicionar tarefas à tarefa através da sua aplicação cliente ou é possível especificar uma [tarefa do Gestor de Tarefas](#jobmanagertask). Uma tarefa do gestor de tarefas utiliza a API do Batch e contém as informações necessárias para criar as tarefas necessárias para uma tarefa, com a tarefa a ser executada num dos nós de computação dentro do conjunto. A tarefa do gestor de tarefas é processada especificamente pelo Batch – é colocada na fila após a criação da tarefa e é reiniciada se esta falhar. Uma tarefa do Gestor de Tarefas é necessária para as tarefas criadas por uma agenda de tarefas, já que é a única forma de definir as tarefas antes de a tarefa ser instanciada. São apresentadas mais informações sobre as tarefas do gestor de tarefas abaixo.

### <a name="task"></a>Tarefa

Uma tarefa é uma unidade de computação que está associada a uma tarefa e é executada num nó. As tarefas são atribuídas a um nó para execução ou são colocadas na fila até que um nó fique livre. Uma tarefa utiliza os seguintes recursos:

- A aplicação especificada na **linha de comandos** da tarefa.

- Os **ficheiros de recursos** que contêm os dados a serem processados. Estes ficheiros são copiados automaticamente para o nó a partir do armazenamento de blobs numa conta de Armazenamento do Azure de **Fins gerais**. Para obter mais informações, consulte *Tarefa de início* e [Ficheiros e diretórios](#files) abaixo.

- As **variáveis de ambiente** que são necessárias para a aplicação. Para obter mais informações, consulte [Definições de ambiente para tarefas](#environment) abaixo.

- As **restrições** sob as quais a computação deve ocorrer. Por exemplo, o tempo máximo dentro do qual a tarefa pode ser executada, o número máximo de vezes que uma tarefa deve ser repetida se falhar e o tempo máximo durante o qual os ficheiros no diretório de trabalho são retidos.

Além das tarefas que define para realizar a computação num nó, também são fornecidas pelo serviço Batch as seguintes tarefas especiais:

- [Tarefa de início](#starttask)
- [Tarefa do gestor de tarefas](#jobmanagertask)
- [Tarefas de preparação e de lançamento da tarefa](#jobmanagertask)
- [Tarefas de várias instâncias](#multiinstance)
- [Dependências de tarefas](#taskdep)

#### <a name="starttask"></a>Tarefa de início

Ao associar uma **tarefa de início** a um conjunto, pode configurar o ambiente de funcionamento dos respetivos nós, efetuar ações como a instalação de software ou o início de processos em segundo plano. A tarefa de início é executada sempre que um nó é iniciado enquanto permanecer no conjunto, incluindo quando o nó for adicionado pela primeira vez ao conjunto. Uma vantagem principal da tarefa de início é que contém todas as informações necessárias para configurar nós de computação e instalar aplicações necessárias à execução de tarefas da tarefa. Assim, aumentar o número de nós num conjunto é tão simples como especificar a nova contagem de nós de destino – o Batch já tem todas as informações necessárias para configurar os novos nós e para os preparar para aceitar tarefas.

À semelhança de qualquer tarefa do Batch, é possível especificar uma lista de **ficheiros de recursos** no [Armazenamento do Azure][azure_storage], além de uma **linha de comandos** para ser executada. O Azure Batch começar por copiar os ficheiros do Armazenamento do Azure e, em seguida, executa a linha de comandos. Para uma tarefa de início de um conjunto, a lista de ficheiros geralmente contém o pacote de aplicação ou os respetivos ficheiros, mas também pode incluir dados de referência a serem utilizados por todas as tarefas em execução nos nós de computação. A linha de comandos da tarefa de início poderia executar um script do PowerShell ou efetuar uma operação `robocopy`, por exemplo, para copiar os ficheiros da aplicação para a pasta “partilhado” e, em seguida, executar subsequentemente um MSI ou `setup.exe`.

> [AZURE.IMPORTANT] O Batch atualmente suporta *apenas* o tipo de conta de armazenamento **Fins gerais**, conforme descrito no passo n.º 5 [Criar uma conta de armazenamento](../storage/storage-create-storage-account.md#create-a-storage-account) em [Sobre as contas de armazenamento do Azure](../storage/storage-create-storage-account.md). As suas tarefas Batch (incluindo tarefas standard, tarefas de início, preparação da tarefa e tarefas de lançamento da tarefa) têm de especificar os ficheiros de recursos que residem *apenas* nas contas de armazenamento de **Fins gerais**.

É normalmente desejável que o serviço Batch aguarde até que a tarefa de início seja concluída antes de considerar o nó pronto para que lhe sejam atribuídas tarefas, no entanto, isto é configurável.

Se uma tarefa de início falhar um nó de computação, o estado do nó é atualizado para refletir a falha e o nó não estará disponível para atribuição de tarefas. Uma tarefa de início pode falhar se ocorrer um problema ao copiar os respetivos ficheiros de recursos do armazenamento ou se o processo executado pela respetiva linha de comandos devolve um código de saída diferente de zero.

#### <a name="jobmanagertask"></a>Tarefa do gestor de tarefas

Uma **Tarefa do Gestor de tarefas** é geralmente utilizada para controlar e/ou monitorizar a execução de tarefas. Por exemplo, criar e submeter as tarefas para uma tarefa, determinar mais tarefas para executar e determinar quando o trabalho está concluído. No entanto, uma tarefa do Gestor de Tarefas não se restringe a estas atividades. É uma tarefa totalmente apta que pode executar quaisquer ações necessárias para a tarefa. Por exemplo, uma tarefa do Gestor de Tarefas pode transferir um ficheiro especificado como um parâmetro, analisar o conteúdo desse ficheiro e submeter mais tarefas com base nesse conteúdo.

Uma tarefa do gestor de tarefas é iniciada antes de todas as outras tarefas e inclui as seguintes funcionalidades:

- É submetida automaticamente como uma tarefa pelo serviço de Batch quando a tarefa é criada.

- É agendada para execução antes das outras tarefas numa tarefa.

- O seu nó associado é o último a ser removido de um conjunto quando este está a ser reduzido.

- A sua terminação pode ser vinculada à terminação de todas as tarefas na tarefa.

- A tarefa do gestor de tarefas recebe a prioridade mais elevada quando tem de ser reiniciada. Se um nó inativo não estiver disponível, o serviço de Batch pode terminar uma das outras tarefas em execução no conjunto, de modo a criar espaço para a tarefa do gestor de tarefas ser executada.

- Uma tarefa do gestor de tarefas numa tarefa não tem prioridade sobre as tarefas de outras tarefas. No âmbito das tarefas, apenas as prioridades ao nível da tarefa são respeitadas.

#### <a name="jobpreprelease"></a>Tarefas de preparação e de lançamento da tarefa

O Batch fornece a tarefa de preparação da tarefa para a configuração da execução pré-tarefa e a tarefa de lançamento da tarefa para a manutenção ou limpeza pós-tarefa.

- **Tarefa de preparação da tarefa** – A tarefa de preparação de tarefas é executada em todos os nós de computação programados para executar tarefas antes de qualquer uma das outras tarefas da tarefa serem executadas. Utilize a tarefa de preparação da tarefa para copiar dados partilhados por todas as tarefas, mas exclusivos da tarefa, por exemplo.
- **Tarefa de lançamento da tarefa** – Quando uma tarefa tiver sido concluída, a tarefa de lançamento da tarefa é executada em cada nó no conjunto que executou, pelo menos, uma tarefa. Utilize a tarefa de lançamento da tarefa para eliminar dados copiados pela tarefa de preparação da tarefa ou para comprimir e carregar dados de registo de diagnóstico, por exemplo.

Tanto as tarefas de lançamento como as de preparação da tarefa permitem-lhe especificar uma linha de comandos para ser executada quando a tarefa é invocada e oferecem funcionalidades como a transferência de ficheiros, a execução elevada, variáveis de ambiente personalizadas, duração máxima de execução, contagem de repetições e tempo de retenção de ficheiros.

Para obter mais informações sobre as tarefas de lançamento e de preparação da tarefa, consulte [Executar tarefas de preparação e de conclusão da tarefa em nós de computação do Azure Batch](batch-job-prep-release.md).

#### <a name="multiinstance"></a>Tarefas de várias instâncias

Um [tarefa de várias instâncias](batch-mpi.md) é uma tarefa que está configurada para ser executada simultaneamente em mais do que um nó de computação. Com tarefas de várias instâncias, pode ativar cenários de computação de alto desempenho, como a Interface de Passagem de Mensagens (MPI – Message Passing Interface), que necessitam de um grupo de nós de computação alocados em conjunto para processar uma carga de trabalho única.

Para ver um debate detalhado sobre a execução de tarefas MPI no Batch ao utilizar a biblioteca .NET do Batch, consulte [Utilizar tarefas de várias instâncias para executar aplicações de Interface de Passagem de Mensagens (MPI) no Azure Batch](batch-mpi.md).

#### <a name="taskdep"></a>Dependências de tarefas

As dependências de tarefas, como o nome indica, permitem-lhe especificar que uma tarefa depende da conclusão de outras tarefas antes da sua execução. Esta funcionalidade fornece suporte para situações em que uma tarefa “a jusante” consome o resultado de uma tarefa “a montante” ou quando uma tarefa a montante efetua alguma inicialização que seja necessária para uma tarefa a jusante. Para utilizar esta funcionalidade, tem primeiro de ativar as dependências de tarefas na sua tarefa do Batch. Em seguida, para cada tarefa que dependa de outra (ou de muitas outras), especifique as tarefas das quais essa tarefa depende.

Com as dependências de tarefas, pode configurar cenários tais como o seguinte:

* A *tarefaB* depende da *tarefaA* (a *tarefaB* não inicia a execução enquanto a *tarefaA* não tiver sido concluída)
* A *tarefaC* depende da *tarefaA* e da *tarefaB*
* A *tarefaD* depende de um intervalo de tarefas, tal como o das tarefas *1* à *10*, antes de ser executada

Consulte o código de exemplo [TaskDependencies][github_sample_taskdeps] no repositório do GitHub [amostras-azure-batch][github_samples]. Aí verá como configurar as tarefas que dependem de outras tarefas ao utilizar a biblioteca [.NET do Batch][batch_net_api].

### <a name="jobschedule"></a>Tarefas agendadas

As agendas de tarefas permitem-lhe criar tarefas recorrentes no âmbito do serviço Batch. Uma agenda de tarefas especifica quando executar tarefas e inclui as especificações das tarefas a executar. Uma agenda de tarefa permite a especificação da duração da agenda – quando e durante quanto tempo a agenda está em vigor – e com que frequência durante esse período de tempo as tarefas devem ser criadas.

### <a name="appkg"></a>Pacotes de aplicações

A funcionalidade [pacotes de aplicações](batch-application-packages.md) facilita a gestão e a implementação de aplicações nos nós de computação nos seus conjuntos. Com os pacotes de aplicações, pode carregar e gerir facilmente várias versões das aplicações executadas pelas suas tarefas, incluindo ficheiros binários e de suporte e, em seguida, implementar automaticamente uma ou mais destas aplicações nos nós de computação no seu conjunto.

O Batch processa os detalhes do trabalho com o Armazenamento do Azure em segundo plano para armazenar e implementar em segurança os seus pacotes de aplicações em nós de computação para que o seu código e os custos de gestão possam ser simplificados.

Para saber mais sobre a funcionalidade de pacote de aplicação, consulte [Implementação de aplicações com pacotes de aplicações do Azure Batch](batch-application-packages.md).

## <a name="files"></a>Ficheiros e diretórios

Cada tarefa tem um diretório de trabalho no qual cria zero ou mais ficheiros e diretórios para armazenar o programa que é executado pela tarefa, os dados que processa e o resultado do processamento efetuado pela tarefa. Estes ficheiros e diretórios ficam então disponíveis para utilização por outras tarefas durante a execução de uma tarefa. Todas as tarefas, ficheiros e diretórios num nó pertencem a uma conta de utilizador individual.

O serviço Batch expõe uma parte do sistema de ficheiros num nó, como o “diretório de raiz”. O diretório de raiz está disponível para uma tarefa ao aceder à variável de ambiente `%AZ_BATCH_NODE_ROOT_DIR%`. Para obter mais informações sobre como utilizar variáveis de ambiente, consulte [Definições de ambiente para tarefas](#environment).

![Estrutura de diretórios do nó de computação][1]

O diretório de raiz contém a seguinte estrutura de diretórios:

- **Partilhado** – Esta localização é um diretório partilhado para todas as tarefas executadas num nó, independentemente da tarefa. No nó, o diretório partilhado é acedido através de `%AZ_BATCH_NODE_SHARED_DIR%`. Este diretório proporciona acesso de leitura/escrita para todas as tarefas executadas no nó. As tarefas podem criar, ler, atualizar e eliminar ficheiros neste diretório.

- **Arranque** – Esta localização é utilizada por uma tarefa de início como o diretório de trabalho. Todos os ficheiros que são transferidos pelo serviço Batch para iniciar a tarefa de início também são armazenados neste diretório. No nó, o diretório de início está disponível através da variável de ambiente `%AZ_BATCH_NODE_STARTUP_DIR%`. A tarefa de início pode criar, ler, atualizar e eliminar ficheiros neste diretório e este diretório pode ser utilizado pelas tarefas de início para configurar o sistema operativo.

- **Tarefas** – É criado um diretório para cada tarefa executada no nó, acedido através de `%AZ_BATCH_TASK_DIR%`. Dentro do diretório de cada tarefa, o serviço Batch cria um diretório de trabalho (`wd`) cujo caminho exclusivo é especificado pela variável de ambiente `%AZ_BATCH_TASK_WORKING_DIR%`. Este diretório proporciona acesso de leitura/escrita à tarefa. A tarefa pode criar, ler, atualizar e eliminar ficheiros neste diretório e este diretório é mantido com base na restrição *RetentionTime* especificada para a tarefa.
  - `stdout.txt` e `stderr.txt` – Estes ficheiros são escritos para a pasta da tarefa durante a execução da mesma.

Quando um nó é removido do conjunto, todos os ficheiros que estão armazenados no nó são removidos.

## <a name="lifetime"></a>Duração do nó de computação e de conjunto

Quando estruturar a sua solução do Azure Batch, tem de tomar uma decisão de design em relação a como e quando são criados os conjuntos e durante quanto tempo os nós de computação são mantidos disponíveis dentro desses conjuntos.

Num extremo do espectro, é possível criar um conjunto para cada tarefa quando a tarefa é submetida e os respetivos nós removidos assim que a execução das tarefas esteja concluída. Isto irá maximizar a utilização, já que os nós apenas são alocados quando for absolutamente necessário e são encerrados logo que fiquem inativos. Apesar de isto significar que a tarefa tem de aguardar que os nós sejam alocados, é importante ter em atenção que as tarefas serão agendadas para os nós assim que estes estejam individualmente disponíveis, alocados e que a tarefa de início esteja concluída. O Batch *não* aguarda até que todos os nós dentro de um conjunto estejam disponíveis para poder atribuir tarefas, o que garante a máxima utilização de todos os nós disponíveis.

No outro extremo do espectro, se a prioridade mais elevada for dar início às tarefas, é possível criar um conjunto antecipadamente e disponibilizar os respetivos nós antes da submissão das tarefas. Neste cenário, as tarefas da tarefa podem ser iniciadas de imediato, mas os nós podem manter-se inativos enquanto aguardam que as tarefas sejam atribuídas.

Uma abordagem combinada, normalmente utilizada para processar carga variável mas em curso, é ter um conjunto para o qual são submetidas várias tarefas, mas aumentar ou reduzir verticalmente o número de nós consoante a carga de tarefas (consulte *Dimensionamento de aplicações* abaixo). Isto pode ser efetuado reativamente, com base na carga atual, ou proativamente se for possível prever a carga.

## <a name="scaling"></a>Dimensionamento de aplicações

Com o [dimensionamento automático](batch-automatic-scaling.md), pode fazer com que o serviço Batch ajuste dinamicamente o número de nós de computação num conjunto de acordo com a carga de trabalho e a utilização de recursos atual do seu cenário de computação. Isto permite-lhe reduzir o custo global de execução da sua aplicação ao utilizar apenas os recursos de que necessita e libertar aqueles de que não necessita. Pode especificar as definições de dimensionamento automático para um conjunto quando é criado ou ativar o dimensionamento mais tarde, e pode atualizar as definições de dimensionamento num conjunto preparado para dimensionamento automático.

O dimensionamento automático é realizado ao especificar uma **fórmula de dimensionamento automático** para um conjunto. O serviço Batch utiliza esta fórmula para determinar o número de destino de nós no conjunto para o próximo intervalo de dimensionamento (um intervalo que pode especificar).

Por exemplo, talvez uma tarefa requer que submeta um grande número de tarefas agendadas para execução. Pode atribuir uma fórmula de dimensionamento ao conjunto que ajusta o número de nós no conjunto com base no número atual de tarefas pendentes, bem como a taxa de conclusão de uma dessas tarefas. O serviço Batch avalia periodicamente a fórmula e redimensiona o conjunto com base na carga de trabalho e nas suas definições de fórmula.

Uma fórmula de dimensionamento pode basear-se nas métricas seguintes:

- **Métricas de tempo** – Baseadas em estatísticas recolhidas a cada cinco minutos no número de horas especificado.

- **Métricas de recurso** – Baseadas na utilização da CPU, na utilização da largura de banda, na utilização da memória e no número de nós.

- **Métricas de tarefas** – Baseadas no estado das tarefas, como Ativa, Pendente e Concluída.

Quando o dimensionamento automático diminui o número de nós de computação num conjunto, as tarefas que estão em execução devem ser tidas em consideração. Para suportar isto, a sua fórmula pode incluir uma definição de política de desalocação de nós que especifica se as tarefas em execução são imediatamente paradas ou se podem ser concluídas antes do nó ser removido do conjunto.

> [AZURE.TIP] Para maximizar a utilização de recursos de computação, defina o número de destino de nós para zero no final de uma tarefa, mas permita que as tarefas em execução sejam concluídas.

Para obter mais informações sobre o dimensionamento automático de uma aplicação, consulte [Dimensionar automaticamente nós de computação num conjunto do Azure Batch](batch-automatic-scaling.md).

## <a name="cert"></a>Segurança com certificados

Normalmente, tem de utilizar certificados quando encriptar ou desencriptar informações confidenciais para tarefas, tais como a chave para uma [conta de Armazenamento do Azure][azure_storage]. Para suportar isto, os certificados podem ser instalados em nós. Os segredos encriptados são transmitidos para as tarefas através dos parâmetros da linha de comandos ou incorporados num dos recursos da tarefa, sendo que os certificados instalados podem ser utilizados para desencriptá-los.

Utilize a operação [Adicionar certificado][rest_add_cert] (API REST do Batch) ou o método [CertificateOperations.CreateCertificate][net_create_cert] (API .NET do Batch) para adicionar um certificado a uma conta do Batch. Em seguida, pode associar o certificado a um conjunto novo ou existente. Quando um certificado é associado a um conjunto, o serviço Batch instala o certificado em cada nó no conjunto. O serviço Batch instala os certificados adequados quando o nó é iniciado, antes de iniciar quaisquer tarefas, incluindo as tarefas de início e as tarefas do gestor de tarefas.

## <a name="scheduling"></a>Prioridade de agendamento

Pode atribuir uma prioridade às tarefas que criar no Batch. O serviço Batch utiliza o valor de prioridade da tarefa para determinar a ordem de agendamento das tarefas dentro de uma conta. Os valores de prioridade variam entre -1000 a 1000, sendo -1000 a prioridade mais baixa e 1000 a prioridade mais alta. Pode atualizar a prioridade de uma tarefa ao utilizar a operação [Atualizar as propriedades de uma tarefa][rest_update_job] (API REST do Batch) ou ao modificar a prioridade [CloudJob.Priority][net_cloudjob_priority] (API .NET do Batch).

Dentro da mesma conta, as tarefas de prioridade mais alta têm precedência de agendamento sobre as tarefas com prioridade mais baixa. Uma tarefa com um valor de prioridade superior numa conta não tem precedência de agendamento sobre outra tarefa com um valor de prioridade inferior numa conta diferente.

O agendamento de tarefas no âmbito dos conjuntos é independente. Entre conjuntos diferentes, não é garantido que uma tarefa de prioridade superior seja agendada primeiro se o respetivo conjunto associado tiver poucos nós inativos. No mesmo conjunto, as tarefas com o mesmo nível de prioridade têm as mesmas hipóteses de serem agendadas.

## <a name="environment"></a>Definições de ambiente para tarefas

Cada tarefa que seja executada dentro de uma tarefa do Batch tem acesso a variáveis de ambiente definidas tanto pelo serviço Batch (definidas pelo sistema, consulte a tabela abaixo) como pelo utilizador. As aplicações e os scripts executados por tarefas em nós de computação têm acesso a estas variáveis de ambiente durante a execução no nó.

Defina variáveis de ambiente definidas pelo utilizador quando utilizar a operação [Adicionar uma tarefa a uma tarefa][rest_add_task] (API REST do Batch) ou ao modificar a propriedade [CloudTask.EnvironmentSettings][net_cloudtask_env] (.NET API do Batch) quando adicionar tarefas a uma tarefa.

Obtenha as variáveis de ambiente de uma tarefa, definidas pelo sistema e pelo utilizador, ao utilizar a operação [Obter informações sobre uma tarefa][rest_get_task_info] (API REST do Batch) ou ao aceder à propriedade [CloudTask.EnvironmentSettings][net_cloudtask_env] (.NET API do Batch). Como foi mencionado, os processos em execução num nó de computação também podem aceder a todas as variáveis de ambiente, por exemplo, ao utilizar a sintaxe `%VARIABLE_NAME%` familiar.

Para cada tarefa que esteja agendada dentro de uma tarefa, o seguinte conjunto de variáveis de ambiente definidas pelo sistema é definido pelo serviço Batch:

| Nome da Variável de Ambiente       | Descrição                                                              |
|---------------------------------|--------------------------------------------------------------------------|
| `AZ_BATCH_ACCOUNT_NAME`         | O nome da conta à qual pertence a tarefa.                       |
| `AZ_BATCH_JOB_ID`               | O ID da tarefa à qual pertence a tarefa.                             |
| `AZ_BATCH_JOB_PREP_DIR`         | O caminho completo do diretório de tarefas de preparação da tarefa no nó.         |
| `AZ_BATCH_JOB_PREP_WORKING_DIR` | O caminho completo do diretório de tarefas de trabalho da tarefa no nó. |
| `AZ_BATCH_NODE_ID`              | O ID do nó no qual a tarefa é executada.                         |
| `AZ_BATCH_NODE_ROOT_DIR`        | O caminho completo do diretório de raiz no nó.                         |
| `AZ_BATCH_NODE_SHARED_DIR`      | O caminho completo do diretório partilhado no nó.                       |
| `AZ_BATCH_NODE_STARTUP_DIR`     | O caminho completo do diretório de tarefas de arranque de nós de computação no nó.    |
| `AZ_BATCH_POOL_ID`              | O ID do conjunto no qual a tarefa está a ser executada.                         |
| `AZ_BATCH_TASK_DIR`             | O caminho completo do diretório de tarefas no nó.                         |
| `AZ_BATCH_TASK_ID`              | O ID da tarefa atual.                                              |
| `AZ_BATCH_TASK_WORKING_DIR`     | O caminho completo do diretório de trabalho no nó.                 |

>[AZURE.NOTE] Não é possível substituir nenhuma das variáveis definidas pelo sistema acima. São só de leitura.

## <a name="errorhandling"></a>Processamento de erros

Pode considerar necessário processar falhas de tarefas e de aplicações dentro da sua solução do Batch.

### Processamento de falhas de tarefas
As falhas de tarefas enquadram-se nestas categorias:

- **Falhas de agendamento**
    - Se a transferência de ficheiros especificados para uma tarefa falhar por algum motivo, é definido um “erro de agendamento” definido para a tarefa.
    - As causas dos erros de agendamento podem dever-se ao facto de os ficheiros terem sido movidos, de a conta de Armazenamento já não estar disponível ou de ter ocorrido outro problema que impediu a cópia bem-sucedida dos ficheiros para o nó.
- **Falhas de aplicações**
    - O processo especificado pela linha de comandos da tarefa também pode falhar. Considera-se que o processo falhou quando um código de saída diferente de zero é devolvido pelo processo executado pela tarefa.
    - Para as falhas de aplicações, é possível configurar o Batch para repetir automaticamente a tarefa até um número de vezes especificado.
- **Falhas de restrições**
    - Pode ser definida uma restrição que especifica a duração de execução máxima para uma tarefa, a *maxWallClockTime*. Isto pode ser útil para terminar tarefas “suspensas”.
    - Quando o tempo máximo tiver sido excedido, a tarefa é marcada como *concluída*, mas o código de saída está definido para `0xC000013A` e o campo *schedulingError* será marcado como `{ category:"ServerError", code="TaskEnded"}`.

### Depurar falhas de aplicações

Durante a execução, uma aplicação poderá produzir resultados de diagnóstico que podem ser utilizados para resolver problemas. Como foi mencionado em [Ficheiros e diretórios](#files) acima, o serviço Batch envia resultados stdout e stderr para ficheiros `stdout.txt` e `stderr.txt` localizados no diretório da tarefa no nó de computação. Ao utilizar [ComputeNode.GetNodeFile][net_getfile_node] e [CloudTask.GetNodeFile][net_getfile_task] na API .NET do Batch, pode obter estes e outros ficheiros para fins de resolução de problemas.

É possível executar uma depuração ainda mais extensiva ao iniciar sessão num nó de computação através do *Ambiente de Trabalho Remoto*. Pode [obter um ficheiro do protocolo de ambiente de trabalho remoto a partir de um nó][rest_rdp] (API REST do Batch) ou utilizar o método [ComputeNode.GetRDPFile][net_rdp] (API .NET do Batch) para início de sessão remoto.

>[AZURE.NOTE] Para se ligar a um nó através do RDP, primeiro tem de criar um utilizador no nó. [Adicione uma conta de utilizador a um nó][rest_create_user] na API REST do Batch ou utilize o método [ComputeNode.CreateComputeNodeUser][net_create_user] no .NET do Batch.

### Explicação de falhas ou interrupções de tarefas

É possível que ocasionalmente as tarefas falhem ou sejam interrompidas. A própria aplicação da tarefa pode falhar, o nó no qual a tarefa está a ser executada pode ser reiniciado ou o nó pode ser removido do conjunto durante uma operação de redimensionamento se política de desalocação do conjunto estiver definida para remover nós imediatamente sem aguardar que as tarefas estejam concluídas. Em todos os casos, a tarefa pode ser automaticamente colocada na fila pelo Batch para execução noutro nó.

Também é possível que um problema intermitente faça com que uma tarefa fique suspensa ou demore demasiado tempo a ser executada. É possível definir o tempo de execução máximo para uma tarefa e, se for excedido, o Batch irá interromper a aplicação da tarefa.

### Resolução de problemas em nós de computação “incorretos”

Em situações onde algumas das suas tarefas estejam a falhar, a aplicação cliente ou o serviço Batch podem examinar os metadados das tarefas com falhas para identificar um nó a funcionar incorretamente. Cada nó num conjunto recebe um ID exclusivo e o nó no qual é executada uma tarefa está incluído nos metadados da tarefa. Uma vez identificado, pode efetuar várias ações:

- **Reiniciar o nó** ([REST][rest_reboot] | [.NET][net_reboot])

    Reiniciar o nó pode, por vezes, limpar problemas latentes, tais como processos bloqueados ou falhados. Tenha em atenção que se o conjunto utilizar uma tarefa de início ou a sua tarefa utilizar uma tarefa de preparação da tarefa, estes serão executados quando o nó for reiniciado.

- **Recriar imagem do nó** ([REST][rest_reimage] | [.NET][net_reimage])

    Isto reinstala o sistema operativo no nó. Tal como acontece com o reinício de um nó, as tarefas de início e as tarefas de preparação da tarefa são novamente executadas depois de ter sido recriada a imagem do nó.

- **Remover o nó do conjunto** ([REST][rest_remove] | [.NET][net_remove])

    Por vezes, é necessário remover completamente o nó do conjunto.

- **Desativar o agendamento de tarefas no nó** ([REST][rest_offline] | [.NET][net_offline])

    Esta ação coloca o nó “offline” de forma eficaz para que não lhe sejam atribuídas mais tarefas, mas permite que o nó permaneça em execução e no conjunto. Isto permite-lhe investigar melhor a causa das falhas sem perder os dados da tarefa com falhas e sem que o nó cause mais falhas nas tarefas. Por exemplo, pode desativar o agendamento de tarefas no nó e, em seguida, iniciar sessão remotamente para examinar os registos de eventos do nó ou efetuar outras ações de resolução de problemas. Depois de terminar a investigação, pode colocar novamente o nó online ao ativar o agendamento de tarefas ([REST][rest_online], [.NET][net_online]) ou realizar uma das outras ações descritas acima.

> [AZURE.IMPORTANT] Com cada ação acima (reiniciar, recriar imagem, remover, desativar o agendamento de tarefas) é possível especificar a forma como as tarefas atualmente em execução no nó são processadas quando executar a ação. Por exemplo, quando desativar o agendamento de tarefas num nó com a biblioteca de cliente .NET do Batch, pode especificar um valor de enumeração [DisableComputeNodeSchedulingOption][net_offline_option] para especificar se pretende **Terminar** as tarefas em execução, **Recolocá-las na fila** para agendamento noutros nós ou permitir que as tarefas em execução sejam concluídas antes de realizar a ação (**TaskCompletion**).

## Passos seguintes

- Criar a sua primeira aplicação do Batch ao seguir os passos em [Introdução à Biblioteca do Azure Batch para .NET](batch-dotnet-get-started.md)
- Transferir e criar o projeto de exemplo [Batch Explorer][batch_explorer_project] para utilização enquanto programa as suas soluções do Batch. Ao utilizar o Batch Explorer, pode realizar o seguinte e muito mais:
  - Monitorizar e manipular conjuntos e tarefas na sua conta do Batch
  - Transferir `stdout.txt`, `stderr.txt` e outros ficheiros a partir de nós
  - Criar utilizadores em nós e transferir ficheiros RDP para início de sessão remoto

[1]: ./media/batch-api-basics/node-folder-structure.png

[about_cloud_services]: ../cloud-services/cloud-services-choose-me.md
[azure_storage]: https://azure.microsoft.com/services/storage/
[batch_explorer_project]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[cloud_service_sizes]: ../cloud-services/cloud-services-sizes-specs.md
[msmpi]: https://msdn.microsoft.com/library/bb524831.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_sample_taskdeps]:  https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies

[batch_net_api]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_cloudjob_jobmanagertask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobmanagertask.aspx
[net_cloudjob_priority]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.priority.aspx
[net_cloudpool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_cloudtask_env]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.environmentsettings.aspx
[net_create_cert]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.createcertificate.aspx
[net_create_user]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.createcomputenodeuser.aspx
[net_getfile_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.getnodefile.aspx
[net_getfile_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.getnodefile.aspx
[net_multiinstancesettings]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.aspx
[net_rdp]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.getrdpfile.aspx
[net_reboot]: https://msdn.microsoft.com/library/azure/mt631495.aspx
[net_reimage]: https://msdn.microsoft.com/library/azure/mt631496.aspx
[net_remove]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.removefrompoolasync.aspx
[net_offline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.disableschedulingasync.aspx
[net_online]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.enableschedulingasync.aspx
[net_offline_option]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.disablecomputenodeschedulingoption.aspx

[batch_rest_api]: https://msdn.microsoft.com/library/azure/Dn820158.aspx
[rest_add_job]: https://msdn.microsoft.com/library/azure/mt282178.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[rest_add_cert]: https://msdn.microsoft.com/library/azure/dn820169.aspx
[rest_add_task]: https://msdn.microsoft.com/library/azure/dn820105.aspx
[rest_create_user]: https://msdn.microsoft.com/library/azure/dn820137.aspx
[rest_get_task_info]: https://msdn.microsoft.com/library/azure/dn820133.aspx
[rest_multiinstance]: https://msdn.microsoft.com/library/azure/mt637905.aspx
[rest_multiinstancesettings]: https://msdn.microsoft.com/library/azure/dn820105.aspx#multiInstanceSettings
[rest_update_job]: https://msdn.microsoft.com/library/azure/dn820162.aspx
[rest_rdp]: https://msdn.microsoft.com/library/azure/dn820120.aspx
[rest_reboot]: https://msdn.microsoft.com/library/azure/dn820171.aspx
[rest_reimage]: https://msdn.microsoft.com/library/azure/dn820157.aspx
[rest_remove]: https://msdn.microsoft.com/library/azure/dn820194.aspx
[rest_offline]: https://msdn.microsoft.com/library/azure/mt637904.aspx
[rest_online]: https://msdn.microsoft.com/library/azure/mt637907.aspx



<!--HONumber=Jun16_HO2-->


