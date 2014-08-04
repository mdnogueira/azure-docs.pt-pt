<properties  umbracoNaviHide="0" pageTitle="Application Model" metaKeywords="Azure, Azure, application model, Azure application model, development model, Azure development model, hosted service, Azure hosted service, web role, worker role" description="Learn about the Azure hosted service application model. Understand core concepts, design considerations, defining and configuring your application, and scaling." linkid="dev-net-fundamentals-application-model" urlDisplayName="Application Model" headerExpose="" footerExpose="" disqusComments="1" title="Application Model" authors="" />

# Modelos de Execução do Azure

O Azure oferece vários modelos de execução para executar aplicações. Cada modelo inclui um conjunto diferente de serviços, pelo que a sua escolha se deverá basear no que pretende exatamente fazer. Este artigo aborda três deles, descrevendo cada tecnologia e dando exemplos de cenários de utilização.

## Índice

* [Máquinas Virtuais](#VMachine)
* [Web Sites](#WebSites)
* [Serviços em Nuvem](#CloudServices)
* [Que modelo devo utilizar? Tomar uma decisão](#WhatShouldIUse)

<h2><a  id="VMachine" ></a>Máquinas Virtuais</h2>


As Máquinas Virtuais do Azure permitem que os programadores, as equipas de operações de TI, entre outras, criem e utilizem máquinas virtuais na nuvem. Ao disponibilizar o que é conhecido como *Infraestrutura como Serviço (Infrastructure as a Service, IaaS)*, esta tecnologia pode ser utilizada de várias formas. [A figura 1](#Fig1) mostra os seus componentes básicos.

<a name="Fig1"></a>![01_CreatingVMs](./media/fundamentals-application-models/ExecModels_01_CreatingVMs.png)

**Figura 1: As Máquinas Virtuais do Azure disponibilizam a Infraestrutura como Serviço.**

Tal como mostra a figura, pode criar máquinas virtuais através do Portal de Gestão do Azure ou da API de Gestão de Serviços do Azure com base em REST. O Portal de Gestão é acessível a partir de qualquer browser comum, incluindo o Internet Explorer, o Mozilla e o Chrome. Para a API de REST, a Microsoft fornece ferramentas de scripting de clientes para sistemas Windows, Linux e Macintosh. Esta interface também pode ser utilizada por qualquer outro software.

Independentemente de como aceder à plataforma, para criar uma nova Máquina Virtual (Virtual Machine, VM) terá de escolher um disco rígido virtual (Virtual Hard Disk, VHD) para a imagem da VM. Estes VHD são armazenados em blobs do Azure.

Para começar, tem duas opções:

1.  Carregar o seu próprio VHD 2.  Utilizar os VHD fornecidos pela Microsoft e respetivos parceiros na
    galeria de Máquinas Virtuais do Azure ou no Web site[VMDepot][1] de
    código aberto da Microsoft.

Os VHD na galeria e no VMDepot incluem imagens limpas de sistemas operativos Microsoft e Linux, assim como imagens que incluem produtos da Microsoft e de terceiros aí instalados. As opções estão sempre a aumentar. Os exemplos englobam várias versões, edições e configurações de:

* Windows Server
* Servidores Linux, tais como Suse, Ubuntu e CentOS
* SQL Server
* BizTalk Server
* SharePoint Server

Juntamente com um VHD, também tem de especificar o tamanho da sua nova VM. As estatísticas completas para cada tamanho estão listadas [na biblioteca do Azure][2].

* **Extra pequeno**, com um núcleo partilhado e 768 MB de memória.
* **Pequeno**, com 1 núcleo e 1,75 GB de memória.
* **Médio**, com 2 núcleos e 3,5 GB de memória.
* **Grande**, com 4 núcleos e 7 GB de memória.
* **Extra grande**, com 8 núcleos e 14 GB de memória.
* **A6**, com 4 núcleos e 28 GB de memória.
* **A7**, com 8 núcleos e 56 GB de memória.

Finalmente, pode escolher o centro de dados do Azure onde a sua nova VM será executada, quer esteja nos EUA, na Europa ou na Ásia.

Quando a VM estiver em execução, paga por cada hora em que é executada e deixa de pagar quando a remover. O montante a pagar não depende da quantidade da VM utilizada; baseia-se apenas nas horas totais. Uma VM que esteja parada durante uma hora tem o mesmo custo que outra bastante carregada.

Cada VM em execução tem um *disco do SO* associado, mantido num blob. Quando cria uma VM através de um VHD da galeria, esse VHD é copiado para o disco do SO da sua VM. Quaisquer alterações efetuadas ao sistema operativo da VM em execução são armazenadas aqui. Por exemplo, se instalar uma aplicação que modifique o registo do Windows, essa alteração será armazenada no seu disco do SO da VM. Da próxima vez que criar uma VM a partir desse disco do SO, a VM continua a ser executada no mesmo estado em que foi deixada. Quanto aos VHD armazenados na galeria, a Microsoft aplica atualizações quando forem necessárias, como, por exemplo, patches do sistema operativo. Contudo, quanto aos VHD nos seus discos do SO, cabe-lhe a si aplicar estas atualizações (embora o Windows Update esteja ativado por predefinição).

Também é possível modificar as VM em execução e, depois, capturá-las com a ferramenta Sysprep. A ferramenta Sysprep remove especificações, como, por exemplo, o nome da máquina, para que seja possível utilizar uma imagem do VHD para criar VM adicionais com a mesma configuração geral. Estas imagens são armazenadas no Portal de Gestão, juntamente com as suas imagens carregadas, para que possam ser utilizadas como ponto de partida para outras VM.

As Máquinas Virtuais também monitorizam o hardware que aloja cada VM que criar. Se um servidor físico que executa uma VM falhar, a plataforma reconhece esta falha e inicia a mesma VM noutra máquina. E pressupondo que tem a licença correta, pode copiar um VHD alterado para fora do seu disco do SO e executá-lo depois noutro sítio, como no seu centro de dados no local ou noutro fornecedor de serviços em nuvem.

Juntamente com o respetivo disco do SO, a sua VM tem um ou mais discos de dados. Apesar de cada um destes discos ser semelhante a uma unidade de disco instalada na sua VM, o conteúdo de cada um está, efetivamente, armazenado num blob. Cada gravação efetuada num disco de dados é armazenada persistentemente no blob subjacente. Tal como com todos os blobs, o Azure replica-os dentro de um centro de dados único e em todos os centros de dados para se proteger contra falhas.

As VM em execução podem ser geridas com o Portal de Gestão, o PowerShell e outras ferramentas de scripting ou diretamente através da API de REST. (na verdade, tudo o que é possível fazer através do Portal de Gestão
pode ser feito por programação através desta API). Os parceiros da Microsoft, tais como a RightScale e a ScaleXtreme, também prestam serviços de gestão baseados na API de REST.

As Máquinas Virtuais do Azure podem ser utilizadas de várias formas. Os principais cenários que a Microsoft visa incluem:

* **VM para programação e teste.** Normalmente, os grupos de programação
  precisam de VM com configurações específicas para criar aplicações. As
  Máquinas Virtuais do Azure são uma forma simples e económica de criar
  estas máquinas, de as utilizar e de as remover quando já não são
  necessárias.
* **Executar aplicações na nuvem.** Para algumas aplicações, a execução
  na nuvem pública é vantajosa a nível económico. Pense, por exemplo,
  numa aplicação com grandes picos de procura. É sempre possível comprar
  máquinas suficientes para o seu centro de dados executar esta
  aplicação, mas é provável que a maioria não seja utilizada a maior
  parte do tempo. A execução desta aplicação no Azure permite-lhe pagar
  VM extras apenas quando são necessárias, encerrando-as quando deixa de
  haver um pico de procura. Ou imagine que é uma start-up e que precisa
  rapidamente de recursos informáticos a pedido e sem qualquer
  compromisso. Mais uma vez, o Azure pode ser a escolha certa.
* **Expandir o seu centro de dados para a nuvem pública.** Com a Rede
  Virtual do Azure, a sua organização pode criar uma rede virtual
  (Virtual Network, VNET) que faz com que um grupo de MV do Azure pareça
  pertencer à sua própria rede no local, o que faz com que a execução de
  aplicações, como o SharePoint, entre outras, no Azure, seja uma
  abordagem que poderá ser mais fácil de implementar e/ou menos
  dispendiosa do que executá-las no seu centro de dados.
* **Recuperação após desastre.** Em vez de pagar continuamente por um
  centro de dados de reserva que é pouco utilizado, a recuperação após
  desastre baseada em IaaS permite-lhe pagar os recursos informáticos
  necessários apenas quando realmente precisa deles. Por exemplo, se o
  seu centro de dados principal ficar inoperativo, pode criar VM em
  execução no Azure para executar aplicações essenciais e encerrá-las
  depois, quando já não forem necessárias.

Estas não são as únicas possibilidades, mas são bons exemplos de como poderia utilizar as Máquinas Virtuais do Azure.

### Agrupar MV - Serviços em Nuvem

Quando criar uma nova VM com Máquinas Virtuais do Azure, pode optar por executá-la de forma autónoma ou integrá-la num grupo de VM em execução conjunta num *serviço em nuvem*. (apesar de terem nomes semelhantes, não confunda este conceito com os Serviços em Nuvem, o nome da tecnologia de PaaS do Azure; não são a mesma coisa). Cada VM autónoma é atribuída ao seu próprio endereço IP público, ao passo que todas as VM no mesmo serviço em nuvem são acessíveis através de um único endereço IP público. [A figura 2](#Fig2) mostra como isto se processa.

<a name="Fig2"></a>![02_CloudServices](./media/fundamentals-application-models/ExecModels_02_CloudServices.png)

**Figura 2: Cada VM autónoma tem o seu próprio endereço IP público, ao passo que as VM agrupadas num serviço em nuvem são expostas através de um único endereço IP público.**

Por exemplo, se criasse uma máquina virtual para criar e testar uma aplicação simples, provavelmente utilizaria uma VM autónoma. Se, ao invés, criasse uma aplicação com várias camadas, um front-end da Web, uma base de dados e talvez até uma camada média, o mais provável seria ligar várias VM a um serviço em nuvem, tal como sugere a figura 2.

Agrupar VM num serviço em nuvem também lhe permite utilizar outras opções. O Azure fornece balanceamento de carga para VM no mesmo serviço em nuvem, difundindo os pedidos dos utilizadores nessas VM. As VM ligadas desta forma conseguem também comunicar diretamente uma com a outra na rede local dentro de um centro de dados do Azure.

As VM no mesmo serviço em nuvem podem igualmente ser agrupadas em um ou mais *conjuntos de disponibilidades*. Para perceber por que é que isto é importante, pense numa aplicação Web que executa várias VM de front-end. Se todas estas VM forem implementadas na mesma máquina física ou até no mesmo suporte de máquinas, uma única falha de hardware pode torná-las a todas inacessíveis. Contudo, se estas VM estiverem agrupadas num conjunto de disponibilidades, o Azure implementá-las-á em todo o centro de dados para que nenhuma delas partilhe pontos de falha.

### Cenário: Executar uma Aplicação com o SQL Server

Para perceber melhor como as Máquinas Virtuais do Azure funcionam, é importante analisar alguns cenários mais detalhadamente. Imagine, por exemplo, que pretende criar uma aplicação Web fiável e escalonável para executar no Azure. Uma forma de o fazer é executar a lógica da aplicação em uma ou mais VM do Azure e utilizar depois o SQL Server para a gestão de dados. [A figura 3](#Fig3) mostra como isto se processa.

<a name="Fig3"></a>![03_AppUsingSQLServer](./media/fundamentals-application-models/ExecModels_03_AppUsingSQLServer.png)

**Figura 3: Uma aplicação executada em Máquinas Virtuais do Azure pode utilizar o SQL Server para armazenamento.**

Neste exemplo, os dois tipos de VM são criados a partir de VHD padrão na galeria. A lógica da aplicação é executada no Windows Server 2008 R2, pelo que o programador cria três VM a partir deste VHD e, em seguida, instala a respetiva aplicação em cada uma. Dado que todas estas VM se encontram no mesmo serviço em nuvem, o programador pode configurar o balanceamento de carga do hardware para difundir pedidos em todas elas. O programador também cria duas VM a partir do VHD da galeria que contém o SQL Server 2012; quando estiverem em execução, o programador configura o SQL Server em cada instância para utilizar o espelhamento de base de dados com ativação pós-falha automática. Isto não é necessário. A aplicação pode utilizar apenas uma única instância do SQL Server, embora optar por esta abordagem aumente a fiabilidade.

### Cenário: Executar um Farm do SharePoint

Imagine que uma organização pretende criar um farm do SharePoint, mas não quer executar o farm no respetivo centro de dados. Talvez o centro de dados no local esteja com poucos recursos ou talvez a unidade de negócio que está a criar o farm não queira lidar com o grupo de TI da organização. Em casos como estes, executar o SharePoint em Máquinas Virtuais do Azure pode fazer todo o sentido. [A figura 4](#Fig4) mostra como isto se processa.

<a name="Fig4"></a>![04_SharePointFarm](./media/fundamentals-application-models/ExecModels_04_SharePointFarm.png)

**Figura 4: As Máquinas Virtuais do Azure permitem executar farms do SharePoint na nuvem.**

Os farms do SharePoint têm vários componentes, sendo cada um executado numa VM do Azure criada a partir de um VHD diferente. Precisa de:

* Microsoft SharePoint. Estão disponíveis imagens de teste na galeria ou
  a organização disponibiliza o seu próprio VHD.
* Microsoft SQL Server. O SharePoint depende do SQL Server, pelo que a
  organização cria VM que são executadas no SQL Server 2012 a partir de
  um VHD padrão da galeria.
* Windows Server Active Directory. O SharePoint também requer o Active
  Directory, pelo que a organização cria controladores de domínio na
  nuvem mediante a utilização de uma imagem do Windows Server a partir
  da galeria. Isto não é forçosamente necessário; também é possível
  utilizar controladores de domínio no local, mas o SharePoint interage
  muitas vezes com o Active Directory, pelo que a abordagem apresentada
  aqui terá um desempenho melhor.

Apesar de não ser mostrado na figura, este farm do SharePoint está provavelmente ligado a uma rede no local que utiliza a Rede Virtual do Azure, o que permite que as VM, e as aplicações nelas contidas, pareçam ser locais para as pessoas que utilizam essa rede.

Tal como mostram estes exemplos, pode utilizar Máquinas Virtuais do Azure para criar e executar novas aplicações na nuvem, executar aplicações existentes ou utilizá-las ainda de outras formas. Qualquer que seja a sua opção, o objetivo da tecnologia é o mesmo -- oferecer uma base genérica para informática em nuvem pública.

<h2><a  id="WebSites" ></a>Web Sites</h2>


As pessoas utilizam as tecnologias Web das mais variadas formas. As empresas podem ter de migrar ou configurar um Web site de presença que possa gerir milhões de visitas por semana e ser implementado em vários centros de dados no mundo. As agência de Web Design poderão trabalhar com uma equipa de programadores para criar uma aplicação Web personalizada capaz de gerir milhares de utilizadores. O programador de uma empresa poderá ter de configurar uma aplicação para monitorizar relatórios de despesas na Nuvem para utilizadores autenticados a partir do Active Directory da empresa. Os consultores de TI poderão utilizar uma aplicação de código aberto popular para configurar um sistema de gestão de conteúdos para uma pequena empresa. Tudo isto é possível com as Máquinas Virtuais do Azure. Contudo, criar e gerir VM não processadas exige algumas competências e esforço. Se tiver de implementar um Web site ou uma aplicação Web, existe uma solução mais fácil (e barata) -- a abordagem vulgarmente conhecida como PaaS (Platform as a Service, Plataforma como Serviço). Tal como mostra a figura 5, o Azure oferece esse serviço com os Web Sites.

<a name="Fig5"></a>![05_Websites](./media/fundamentals-application-models/ExecModels_05_Websites.png)

**Figura 5: Os Web Sites do Azure suportam Web sites estáticos, aplicações Web populares e aplicações Web personalizadas desenvolvidas com várias tecnologias.**

Os Web Sites do Azure são desenvolvidos sobre os Serviços em Nuvem do Azure para criar uma solução de Plataforma como Serviço otimizada para executar aplicações Web. Tal como mostra a figura, os Web Sites são executados num conjunto de VM únicas que podem conter vários Web sites criados por vários utilizadores, assim como VM padrão que pertencem a um utilizador individual. As VM são uma parte de um conjunto de recursos gerido pelos Web Sites do Azure, permitindo, assim, uma grande fiabilidade e tolerância a falhas. É fácil começar. Com os Web Sites do Azure, os utilizadores podem selecionar várias aplicações, estruturas e modelos e criar um Web site em segundos. Depois, podem utilizar as ferramentas de programação (WebMatrix, Visual Studio ou qualquer outro editor) e as opções de controlo de origem de que mais gostam para configurar uma integração contínua e programarem como equipa. As aplicações baseadas num MySQL DB podem consumir um serviço MySQL fornecido ao Azure pela ClearDB, um parceiro da Microsoft. Com os Web Sites os programadores podem criar aplicações Web grandes e escalonáveis. A tecnologia suporta a criação de aplicações recorrendo a ASP.NET, PHP, Node.js e Python. As aplicações podem utilizar, por exemplo, sessões fixas e as aplicações Web existentes podem ser movidas para esta plataforma na nuvem sem quaisquer alterações. As aplicações criadas nos Web Sites podem utilizar outros aspetos do Azure, tais como o Barramento de Serviço, a Base de Dados SQL e o Armazenamento de Blobs. Também pode executar várias cópias de uma aplicação em várias VM, com os Web Sites a fazer o balanceamento de carga automaticamente entre elas. E dado que são criadas novas instâncias dos Web Sites em VM já existentes, iniciar uma nova instância de aplicação é muito rápido; é significativamente mais rápido do que esperar que seja criada uma nova VM. Tal como mostra a [Figura 5](#Fig5), pode publicar códigos e outro conteúdo Web nos Web Sites de várias formas. Pode utilizar FTP, FTPS ou a tecnologia WebDeploy da Microsoft. Os Web Sites também suportam a publicação de código a partir de sistemas de controlo de origem, incluindo Git, GitHub, CodePlex, BitBucket, Dropbox, Mercurial, Team Foundation Server e Team Foundation Service, um serviço baseado na nuvem.

<h2><a  id="CloudServices" ></a>Serviços em Nuvem</h2>


As Máquinas Virtuais do Azure oferecem o IaaS, ao passo que os Web Sites do Azure oferecem o alojamento na Web. A terceira opção de computação, os Serviços em Nuvem, oferece a *Plataforma como Serviço (Platform as a Service, PaaS)*. Esta tecnologia foi concebida para suportar aplicações escalonáveis, fiáveis e cuja operação não é dispendiosa. Também pretendem despreocupar os programadores com questões relacionadas com a gestão da plataforma utilizada, permitindo-lhes concentrar-se apenas nas suas aplicações. [A Figura 6](#Fig6) ilustra a ideia.

<a name="Fig6"></a>![06_CloudServices2](./media/fundamentals-application-models/ExecModels_06_CloudServices2.png)

**Figura 6: Os Serviços em Nuvem do Azure oferecem a Plataforma como Serviço.**

Tal como as outras opções de computação do Azure, os Serviços em Nuvem baseiam-se em VM. A tecnologia proporciona duas opções de VM ligeiramente diferentes -- as instâncias de *funções da Web* executam uma variante do Windows Server com IIS, ao passo que as instâncias de *funções de trabalho* executam a mesma variante do Windows Server sem IIS. As aplicações de Serviços em Nuvem baseiam-se numa combinação destas duas opções.

Por exemplo, uma simples aplicação poderá utilizar apenas uma função da Web, ao passo que uma outra mais complexa poderá utilizar uma função da Web para processar a receção de pedidos dos utilizadores e passar, depois, o trabalho que esses pedidos criam para uma função de trabalho, para processamento (esta comunicação pode utilizar o Barramento de Serviço ou as Filas do Azure).

Tal como sugere a figura, todas as VM numa única aplicação são executadas no mesmo serviço em nuvem, conforme descrito anteriormente nas Máquinas Virtuais do Azure. Devido a isso, os utilizadores acedem à aplicação através de um único endereço IP público, sendo o balanceamento de carga dos pedidos feito automaticamente em todas as VM da aplicação. E, tal como se sucede com os serviços em nuvem criados com as Máquinas Virtuais do Azure, a plataforma implementará as VM numa aplicação de Serviços em Nuvem de forma a evitar pontos de falha do hardware.

Apesar de as aplicações serem executadas em máquinas virtuais, é importante perceber que os Serviços em Nuvem disponibilizam PaaS e não IaaS. Pense nisso desta forma: Com IaaS, do qual as Máquinas Virtuais do Azure são um exemplo, tem primeiro de criar e configurar o ambiente onde a sua aplicação será executada e implementar, depois, a aplicação nesse ambiente. É responsável pela gestão da maior parte deste processo, incluindo pela implementação de novas versões de patches do sistema operativo em cada VM. Com PaaS, pelo contrário, é como se o ambiente já existisse. Só precisa de implementar a sua aplicação. A gestão da plataforma onde é executada, incluindo a implementação de novas versões do sistema operativo, é controlada automaticamente.

Com os Serviços em Nuvem, não são criadas máquinas virtuais. Em vez disso, tem de fornecer um ficheiro de configuração que diz ao Azure a quantidade que pretende de cada máquina virtual, como, por exemplo, três instâncias da função da Web e duas instâncias da função de trabalho, e a plataforma cria-as automaticamente. Continua a poder escolher o tamanho que essas VM devem ter -- as opções são idênticas às das VM do Azure --, mas não tem de as criar explicitamente. Se a sua aplicação tiver de gerir uma carga maior, pode pedir mais VM e o Azure criará essas instâncias. Se a carga diminuir, pode encerrar essas instâncias e deixar de as pagar.

Normalmente, as aplicações de Serviços em Nuvem são disponibilizadas aos utilizadores através de um processo de dois passos. O programador começa por carregar a aplicação para a área de transição da plataforma. Quando o programador estiver pronto para ativar a aplicação, terá de utilizar o Portal de Gestão do Azure para pedir que a mesma entre em funcionamento. Esta mudança entre transição e produção pode ser feita sem qualquer paragem, o que permite que as aplicações em execução sejam atualizadas para uma nova versão sem interromper a atividade dos utilizadores.

Os Serviços em Nuvem também oferecem monitorização. Tal como as Máquinas Virtuais do Azure, os Serviços em Nuvem detetarão uma falha no servidor físico e reiniciarão as VM que estavam a ser executadas nesse servidor numa nova máquina. Mas os Serviços em Nuvem também detetam VM e aplicações com falhas, não apenas falhas de hardware. Ao contrário das Máquinas Virtuais, dispõem de um agente dentro de cada função da Web e função de trabalho, pelo que são capazes de iniciar novas VM e instâncias de aplicações caso ocorram falhas.

A natureza de PaaS dos Serviços em Nuvem também tem outras implicações. Uma das mais importantes é que as aplicações desenvolvidas nesta tecnologia devem ser escritas para serem executadas corretamente quando uma instância de função da Web ou função de trabalho falhar. Para tal, as aplicações de Serviços em Nuvem não devem manter o estado no sistema de ficheiros das suas próprias VM. Ao contrário das VM criadas com as Máquinas Virtuais do Azure, as escritas efetuadas nas VM dos Serviços em Nuvem não são persistentes; não há nada que se compare a um disco de dados das Máquinas Virtuais. Em vez disso, as aplicações dos Serviços em Nuvem devem escrever explicitamente todo o estado na Base de Dados SQL, nos blobs, nas tabelas ou noutro dispositivo de armazenamento externo. Criar aplicações desta forma facilita o seu dimensionamento e torna-as mais resistentes a falhas, dois objetivos importantes dos Serviços em Nuvem.

<h2><a  id="WhatShouldIUse" ></a>Que modelo devo utilizar? Tomar uma decisão</h2>


Os três modelos de execução do Azure permitem-lhe programar aplicações escalonáveis e fiáveis na nuvem. Dada esta similaridade essencial, qual dos três deve utilizar? A resposta depende do que pretende fazer.

As Máquinas Virtuais oferecem a solução mais geral. Se quiser ter o máximo controlo possível ou se precisar de VM mais genéricas, como, por exemplo, para programação e teste, esta é a melhor opção. As Máquinas Virtuais também são a melhor opção para executar aplicações no local e prontas a utilizar na nuvem, conforme ilustrado no exemplo do SharePoint descrito anteriormente. E dado que as VM que criar com esta tecnologia podem ser semelhantes às suas VM no local, também é provável que sejam a melhor opção para a recuperação após desastre. Claro que a contrapartida é o facto de esta grande potencialidade estar associada a uma grande responsabilidade -- a IaaS exige algum trabalho administrativo.

Os Web Sites são a opção certa se quiser criar um Web site simples. Isto é particularmente verdade se o seu site se basear numa aplicação existente, como o Joomla, o WordPress ou o Drupal. Os Web sites são também uma boa opção para criar aplicações Web com pouca necessidade de administração, até mesmo aplicações que requeiram grande dimensionamento, ou para mover aplicações Web IIS existentes para a nuvem pública. Também oferece uma rápida implementação. Pode começar a executar quase imediatamente uma nova instância da sua aplicação, ao passo que implementar uma nova VM com as Máquinas Virtuais ou os Serviços em Nuvem pode demorar alguns minutos.

Os Serviços em Nuvem, o modelo de execução inicial oferecido pelo Azure, são uma abordagem explicitamente de PaaS. Embora a linha entre a PaaS e o alojamento na Web seja ténue, os Serviços em Nuvem têm algumas diferenças importantes face aos Web Sites, incluindo:

* Ao contrário dos Web Sites, os Serviços em Nuvem conferem acesso
  administrativo às VM da sua aplicação, o que lhe permite instalar
  software arbitrário de que a sua aplicação precisa, o que não é
  possível fazer nos Web Sites.
* Uma vez que os Serviços em Nuvem oferecem funções da Web e funções de
  trabalho, são uma opção melhor do que os Web Sites para aplicações de
  várias camadas cuja lógica de negócio requer VM separadas.
* Os Serviços em Nuvem proporcionam ambientes separados de transição e
  de produção, tornando as atualizações das aplicações um pouco mais
  simples do que com os Web Sites.
* Ao contrário dos Web Sites, pode utilizar tecnologias de rede, como a
  Rede Virtual do Azure e o Azure Connect, para ligar computadores no
  local a aplicações de Serviços em Nuvem.
* Os Serviços em Nuvem permitem utilizar o Ambiente de Trabalho Remoto
  para se ligar diretamente às VM de uma aplicação, o que não é possível
  com os Web Sites.

Visto que se trata de PaaS, os Serviços em Nuvem também oferecem algumas vantagens em relação às Máquinas Virtuais do Azure. São realizadas automaticamente mais tarefas de gestão, como, por exemplo, a implementação de atualizações do sistema operativo, pelo que é provável que, desta forma, os seus custos de operações sejam inferiores aos da abordagem de IaaS das Máquinas Virtuais do Azure.

Os três modelos de execução do Azure têm prós e contras. Para fazer a melhor escolha, é necessário que compreenda estas condições, sabendo o que pretende alcançar, e optar então pelo modelo que melhor se adequa às suas necessidades.

Por vezes, não há um modelo de execução mais adequado do que outro. Nestas situações, é perfeitamente válido combinar opções. Por exemplo, imagine que está a programar uma aplicação e que gostaria de ter as vantagens de gestão das funções da Web dos Serviços em Nuvem, mas também precisa de utilizar um SQL Server padrão, por motivos de compatibilidade ou de desempenho. Neste caso, a melhor opção é combinar modelos de execução, conforme mostra a [Figura 7](#Fig7).

<a name="Fig7"></a>![07_CombineTechnologies](./media/fundamentals-application-models/ExecModels_07_CombineTechnologies.png)

**Figura 7: Uma única aplicação pode utilizar vários modelos de execução.**

Tal como ilustra a figura, as VM de Serviços em Nuvem são executadas num serviço em nuvem separado das VM das Máquinas Virtuais. Ainda assim, as duas conseguem comunicar de forma bastante eficiente, pelo que esta é, por vezes, a melhor opção para criar aplicações.

O Azure disponibiliza modelos de execução diferentes porque as plataformas em nuvem têm de suportar muitos cenários diferentes. Qualquer pessoa que pretenda utilizar esta plataforma eficazmente -- e, se leu até aqui, é provavelmente o seu caso -- tem de perceber cada um deles.



[1]: http://vmdepot.msopentech.com/
[2]: http://msdn.microsoft.com/en-us/library/windowsazure/dn197896.aspx