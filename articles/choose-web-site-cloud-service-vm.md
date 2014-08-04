<properties  linkid="manage-scenarios-choose-web-app-service" urlDisplayName="Web Options for Azure" pageTitle="Azure Web Sites, Cloud Services and Virtual Machines comparison" metaKeywords="Cloud Services, Virtual Machines, Web Sites" description="Learn when to use Azure Web Sites, Cloud Services, and Virtual Machines for hosting web applications. Review a feature comparison." metaCanonical="" services="web-sites,virtual-machines,cloud-services" documentationCenter="" title=" Cloud Services" authors="jroth" solutions="" manager="paulettm" editor="mollybos" />

# Comparação entre Web Sites, Serviços em Nuvem e Máquinas Virtuais do Azure

O Azure oferece diversas formas de alojar as suas aplicações Web, como, por exemplo, os [Web Sites][1], os [Serviços em Nuvem][2] e as [Máquinas Virtuais do Azure][3]. Depois de analisar estas opções, poderá ficar na dúvida sobre qual a que melhor se adequa às suas necessidades ou não ficar esclarecido relativamente a alguns conceitos, como IaaS vs PaaS. Este artigo ajuda-o a compreender as opções que tem à disposição e a tomar a decisão certa para satisfazer as suas necessidades. Embora as três opções lhe permitam executar aplicações Web altamente escaláveis no Azure, existem diferenças que podem ajudá-lo na sua tomada de decisão.

Em muitos casos, os Web Sites do Azure são a melhor opção. Oferecem opções simples e flexíveis para a implementação e gestão e conseguem alojar Web sites de elevado volume. Pode criar rapidamente um Web site novo com software conhecido, como o WordPress, a partir da Galeria de Aplicações Web ou pode mover um Web site existente para os Web Sites do Azure. Utilizando o [Azure WebJobs SDK][4] (atualmente em pré-visualização), pode ainda adicionar o processamento de tarefas em segundo plano.

Tem, igualmente, a opção de alojar aplicações Web nos Serviços em Nuvem do Azure ou nas Máquinas Virtuais do Azure. Estas são boas escolhas quando a sua camada da Web requer o nível adicional de controlo e de personalização que estas disponibilizam; no entanto, o aumento do controlo implica um aumento na complexidade na criação, gestão e implementação das aplicações. O seguinte diagrama ilustra as diferenças entre as três opções.

![DiagramaDeOpções](./media/choose-web-site-cloud-service-vm/Websites_CloudServices_VMs_2.png)

Os Web Sites são mais fáceis de configurar, gerir e monitorizar, mas têm menos opções de configuração. O ponto fundamental é que, se não tiver um bom motivo para colocar o seu front-end da Web nos Serviços em Nuvem ou nas Máquinas Virtuais, deve utilizar os Web Sites do Azure. A parte restante deste documento fornece as informações necessárias para tomar uma decisão informada. Inclui:

* [Cenários](#scenarios)
* [Resumos dos Serviços](#services)
* [Comparação das Funcionalidades](#features)

## <a name="scenarios"></a>Cenários

### Sou proprietário de uma pequena empresa, com perspetivas de crescimento, e preciso de uma forma económica de alojar o meu site.

Os Web Sites do Azure (Windows Azure Web Sites, WAWS) são uma excelente solução para este cenário, porque pode começar a utilizá-los gratuitamente e adicionar mais capacidades sempre que necessário. Por exemplo, cada Web Site gratuito inclui um domínio fornecido pelo Azure (*a_sua_empresa*.azurewebsites.net). Quando estiver pronto para começar a utilizar o seu próprio domínio, pode adicioná-lo por apenas 9,80 € por mês (à data de 01/14). Existem muitos outros serviços e opções de dimensionamento que permitem que o site se desenvolva à medida que a procura dos utilizadores aumenta. Com os **Web Sites do Azure**, pode:

* Começar com a camada gratuita e, posteriormente, dimensionar conforme
  necessário.
* Utilizar a Galeria de Aplicações para configurar rapidamente
  aplicações Web conhecidas, como o WordPress.
* Acrescentar mais serviços e funcionalidades do Azure à sua aplicação,
  conforme necessário.
* Proteger o seu Web site com HTTPS, utilizando o certificado fornecido
  com o seu nome de domínio *a_sua_empresa*.azurewebsites.net.

### Sou Web designer ou designer gráfico e pretendo conceber e desenvolver Web sites para os meus clientes

Os Web Sites do Azure oferecem aos programadores Web tudo aquilo de que precisam para criar aplicações Web sofisticadas. Os Web Sites permitem uma forte integração com ferramentas, como o Visual Studio e a base de dados SQL. Com os **Web Sites**, os programadores podem:

* Utilizar ferramentas de linha de comandos para [tarefas
  automatizadas][5].
* Trabalhar com linguagens conhecidas, como [.Net][6], [PHP][7],
  [Node.js][8] e [Python][9].
* Selecionar três níveis de dimensionamento diferentes para dimensionar
  para capacidades muito elevadas.
* Integrar com outros serviços do Azure, como a [Base de Dados SQL][10],
  o [Barramento de Serviço][11] e o [Armazenamento][12], ou com ofertas
  de parceiros a partir da [Loja Azure][13], como o MySQL e o MongoDB.
* Integrar com ferramentas, como Visual Studio, Git, WebMatrix,
  WebDeploy, TFS e FTP.

### Estou a migrar a minha aplicação de várias camadas com um front-end da Web para a Nuvem

Se estiver a executar uma aplicação de várias camadas, como um servidor Web que comunica com um servidor de base de dados para armazenar e obter dados de Web sites, tem diversas opções no Azure. Estas opções arquitetónicas incluem os Web Sites, os Serviços em Nuvem e as Máquinas Virtuais. Em primeiro lugar, os **Web Sites** são uma boa opção para a camada da Web da sua solução e podem ser utilizados com a Base de Dados SQL do Azure para criar uma arquitetura de duas camadas. Os Web Sites também lhe permitem executar processos em segundo plano ou de execução longa ao utilizar a pré-visualização do Azure WebJobs SDK. Se precisa de uma arquitetura mais complexa ou de opções de dimensionamento mais flexíveis, os Serviços em Nuvem ou as Máquinas Virtuais são uma opção melhor.

Os **Serviços em Nuvem** permitem-lhe:

* Alojar serviços Web, de back-end e de camada média em funções de
  trabalho e de Web escaláveis.
* Alojar apenas os serviços de back-end e de camada média em funções de
  trabalho, mantendo o front-end nos Web Sites do Azure.
* Dimensionar serviços de front-end e de back-end de forma independente.

As **Máquinas Virtuais** permitem-lhe:

* Migrar mais facilmente ambientes altamente personalizáveis como
  imagens de máquina virtual.
* Executar software ou serviços que não podem ser configurados nos Web
  Sites nem nos Serviços em Nuvem.

### A minha aplicação depende de ambientes Windows ou Linux altamente personalizados

Se a sua aplicação requer uma instalação ou uma configuração complexa de software e de sistema operativo, as Máquinas Virtuais são, provavelmente, a melhor solução. Com as **Máquinas Virtuais**, pode:

* Utilizar a galeria de Máquina Virtual para começar com um sistema
  operativo, como o Windows ou o Linux, e, posteriormente,
  personalizá-lo consoante os requisitos da sua aplicação.
* Criar e carregar uma imagem personalizada de um servidor no local
  existente para execução numa máquina virtual no Azure.

### O meu site utiliza software de código aberto e pretendo alojá-lo no Azure

As três opções permitem-lhe alojar linguagens e estruturas de código aberto. Os **Serviços em Nuvem** requerem que utilize tarefas de arranque para instalar e configurar qualquer software de código aberto necessário que seja executado no Windows. Com as **Máquinas Virtuais**, o software é instalado e configurado na imagem da máquina, que pode ser baseada em Windows ou Linux. Se a sua estrutura de código aberto for suportada em Web sites, proporciona uma forma mais simples de alojar estes tipos de aplicações, uma vez que os Web sites podem ser automaticamente configurados com as linguagens e as estruturas de que a sua aplicação precisa. Os **Web Sites** permitem-lhe:

* Utilizar diversas linguagens de código aberto conhecidas, como
  [.Net][6], [PHP][7], [Node.js][8] e [Python][9].
* Configurar o WordPress, o Drupal, o Umbraco, o DNN e muitas outras
  aplicações Web de terceiros.
* Migrar uma aplicação existente ou criar uma nova a partir da Galeria
  de Aplicações.

### Tenho uma aplicação de linha de negócio que tem de ser ligada à rede empresarial

Se pretender criar uma aplicação de linha de negócio, o seu Web site poderá precisar de acesso direto a serviços ou a dados na rede empresarial. Isto é possível nos **Web Sites**, nos **Serviços em Nuvem** e nas **Máquinas Virtuais**. Cada abordagem tem as suas diferenças, que incluem o seguinte:

* Os Web Sites podem ligar-se de forma segura a recursos no local
  através da utilização do Reencaminhamento de Barramento de Serviço, o
  que permite que os serviços da rede empresarial realizem tarefas em
  nome da aplicação Web, sem ter de mover tudo para a Nuvem nem de
  configurar uma rede virtual.
* Os Serviços em Nuvem e as Máquinas Virtuais podem tirar partido da
  Rede Virtual. Com efeito, a Rede Virtual permite que as máquinas
  executadas no Azure se liguem a uma rede no local. O Azure torna-se,
  então, uma extensão do seu centro de dados empresarial.

### Pretendo alojar um API de REST ou um serviço Web para clientes móveis

Os serviços Web baseados em HTTP permitem-lhe suportar uma grande variedade de clientes, incluindo clientes móveis. Estruturas como a ASP.NET Web API são integradas com o Visual Studio para facilitar a criação e o consumo de serviços REST. Estes serviços são expostos a partir de um ponto final da Web, pelo que é possível utilizar qualquer técnica de alojamento na Web no Azure para suportar este cenário. No entanto, os **Web Sites** são uma excelente escolha para alojar a API de REST. Com os Web Sites, pode:

* Criar rapidamente um Web site para alojar o serviço Web HTTP num dos
  centros de dados distribuídos globalmente do Azure.
* Migrar serviços existentes ou criar serviços novos, tirando
  potencialmente partido do ASP.NET Web API no Visual Studio.
* Obter um SLA para disponibilidade com uma única instância ou
  dimensionar para vários computadores dedicados.
* Utilizar o site publicado para fornecer as API de REST aos clientes
  HTTP, incluindo clientes móveis.

## <a name="services"></a>Resumos dos Serviços

Os [Web Sites do Azure][1] permitem-lhe criar em pouco tempo Web Sites altamente escaláveis no Azure. Pode utilizar o Portal do Azure ou as ferramentas de linha de comandos para configurar um Web site com linguagens conhecidas, como .NET, PHP, Node.js, e Python. As estruturas suportadas já estão implementadas e não requerem mais passos de instalação. A galeria de Web Sites do Azure contém diversas aplicações de terceiros, como o Drupal e o WordPress, bem como estruturas de programação, como a Django e a CakePHP. Depois de criar um site, pode migrar um Web site existente ou criar outro totalmente novo. Os Web Sites eliminam a necessidade de gerir o hardware físico e também garantem diversas opções de dimensionamento. Pode passar de um modelo multi-inquilino partilhado para um modo padrão, no qual os computadores dedicados servem o tráfego recebido. Os Web Sites também lhe permitem integrar com outros serviços do Azure, como a Base de Dados SQL, o Barramento de Serviço e o Armazenamento. Utilizando a pré-visualização do [Azure WebJobs SDK][4], pode adicionar o processamento em segundo plano. Em resumo, os Web Sites do Azure fazem com que seja mais fácil concentrar-se na programação de aplicações, ao suportar uma vasta gama de linguagens, de aplicações de código aberto e de metodologias de implementação (FTP, Git, Web Deploy ou TFS). Se não tiver requisitos específicos que requeiram os Serviços em Nuvem ou as Máquinas Virtuais, optar pelo Web Site do Azure é, provavelmente, o mais indicado.

Os [Serviços em Nuvem][2] permitem-lhe criar aplicações Web altamente disponíveis e escaláveis num ambiente de Plataforma como Serviço (Platform as a Service, PaaS) rico. Ao contrário dos Web Sites, os serviço em nuvem começam por ser criados num ambiente de programação, como o Visual Studio, antes de serem implementados no Azure. As estruturas, como a PHP, requerem passos de implementação personalizados ou tarefas que instalam a estrutura no arranque da função. A principal vantagem dos Serviços em Nuvem é a capacidade de suportar arquiteturas mais complexas com várias camadas. Um serviço em nuvem individual poderá consistir numa função Web front-end e numa ou mais funções de trabalho. Cada camada pode ser dimensionada de forma independente. Também há um nível de controlo superior sobre a infraestrutura da sua aplicação Web. Por exemplo, pode aceder através do ambiente de trabalho remoto nos computadores que estão a executar as instâncias da função. Também pode criar scripts de IIS mais avançados e de alterações de configuração do computador que são executados no arranque da função, incluindo tarefas que requerem o controlo do administrador.

As [Máquinas Virtuais][3] permitem-lhe executar aplicações Web em máquinas virtuais no Azure. Esta capacidade também é conhecida como Infraestrutura como Serviço (Infrastructure as a Service, IaaS). Crie novas máquinas de Windows Server ou Linux através do portal ou carregue uma imagem de máquina virtual existente. As Máquinas Virtuais dão-lhe o máximo controlo sobre o sistema operativo, a configuração e o software e os serviços instalados. Esta é uma boa opção para migrar mais rapidamente aplicações Web no local complexas para a nuvem, visto que as máquinas podem ser movidas como um todo. Com as Redes Virtuais, pode ainda ligar estas máquinas virtuais a redes empresariais no local. Tal como se sucede com os Serviços em Nuvem, tem acesso remoto a estas máquinas, bem como a capacidade de executar alterações de configuração a nível administrativo. No entanto, ao contrário dos Web Sites e dos Serviços em Nuvem, tem de gerir as imagens da máquina virtual e a arquitetura da aplicação totalmente ao nível da infraestrutura. Um exemplo básico é de que deve aplicar os seus próprios patches ao sistema operativo.

## <a name="features"></a>Comparação de Funcionalidades

A tabela seguinte compara as capacidades dos Web Sites, dos Serviços em Nuvem e das Máquinas Virtuais para o ajudar a tomar a melhor decisão. As caixas com um asterisco têm uma explicação mais detalhada nas notas que se seguem à tabela.

<table  cellspacing="0" border="1">
<tr>
   <th  align="left" valign="middle">Funcionalidade</th>

   <th  align="left" valign="middle">Web Sites</th>

   <th  align="left" valign="middle">Serviços em Nuvem (funções Web)</th>

   <th  align="left" valign="middle">Máquinas Virtuais</th>

</tr>

<tr>
   <td  valign="middle"><p>Aceder a serviços como o Barramento de Serviço, o Armazenamento e a Base de Dados SQL</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Alojar camadas Web ou camadas de serviços Web de uma arquitetura de várias camadas</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Alojar camadas médias de uma arquitetura de várias camadas</p>
</td>

   <td  valign="middle" />

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Suporte integrado de MySQL-como-serviço</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X <sup>1</sup>
</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Suporte para ASP.NET, classic ASP, Node.js, PHP, Python</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Dimensionar para várias instâncias sem novas implementações</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X <sup>2</sup>
</td>

</tr>

<tr>
   <td  valign="middle"><p>Suporte para SSL</p>
</td>

   <td  valign="middle">X <sup>3</sup>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Integração do Visual Studio</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Depuração Remota</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Implementar código com TFS</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Implementar código com GIT, FTP</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle" />

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Implementar código com Web Deploy</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle"><sup>4</sup>
</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Suporte para WebMatrix</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle" />

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Implementação quase imediata</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle" />

   <td  valign="middle" />

</tr>

<tr>
   <td  valign="middle"><p>As instâncias partilham conteúdo e configuração</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle" />

   <td  valign="middle" />

</tr>

<tr>
   <td  valign="middle"><p>Dimensionar para computadores maiores sem novas implementações</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle" />

   <td  valign="middle" />

</tr>

<tr>
   <td  valign="middle"><p>Vários ambientes de implementação (produção e transição)</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

   <td  valign="middle" />

</tr>

<tr>
   <td  valign="middle"><p>Isolamento de rede com a Rede Virtual do Azure</p>
</td>

   <td  valign="middle" />

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Suporte para o Gestor de Tráfego do Azure</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Acesso remoto ao ambiente de trabalho para servidores</p>
</td>

   <td  valign="middle" />

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Capacidade de definir/executar tarefas de arranque</p>
</td>

   <td  valign="middle" />

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Gestão de atualizações automáticas do SO</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

   <td  valign="middle" />

</tr>

<tr>
   <td  valign="middle"><p>Monitorização Integrada do Ponto Final</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Mudança de plataformas totalmente integrada (32 bits/64 bits)</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

   <td  valign="middle" />

</tr>

</table>

<sup>1</sup> As funções Web ou de trabalho podem integrar o MySQL-como-serviço através das ofertas da ClearDB, mas não como parte do fluxo de trabalho do Portal de Gestão.

<sup>2</sup> Embora seja possível dimensionar as Máquinas Virtuais para várias instâncias, os serviços que estão a ser executados nestas máquinas têm de ser escritos para suportar este dimensionamento. Deve ser configurado um balanceador de carga adicional para encaminhar pedidos pelas máquinas. Finalmente, deve ser criado um Grupo de Afinidades para todas as máquinas que participam na mesma função, para protegê-las de reinícios simultâneos devido a manutenção ou a falhas de hardware.

<sup>3</sup> Nos Web Sites, o SSL para nomes de domínio personalizados só é suportado no modo padrão. Para obter mais informações sobre a utilização do SSL com os Web Sites, consulte [Configurar um certificado SSL para um Web Site do Azure][14].

<sup>4</sup> A Implementação Web é suportada para serviços em nuvem ao implementar em funções de instância única. No entanto, as funções de produção requerem que as várias instâncias cumpram o SLA do Azure. Por conseguinte, a Implementação Web não é um mecanismo de implementação adequado para serviços em nuvem em produção.



[1]: http://go.microsoft.com/fwlink/?LinkId=306051
[2]: http://go.microsoft.com/fwlink/?LinkId=306052
[3]: http://go.microsoft.com/fwlink/?LinkID=306053
[4]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs
[5]: http://www.windowsazure.com/en-us/documentation/scripts/?services=web-sites
[6]: http://www.windowsazure.com/en-us/develop/net/
[7]: http://www.windowsazure.com/en-us/develop/php/
[8]: http://www.windowsazure.com/en-us/develop/nodejs/
[9]: http://www.windowsazure.com/en-us/develop/python/
[10]: http://www.windowsazure.com/en-us/documentation/services/sql-database/
[11]: http://www.windowsazure.com/en-us/documentation/services/service-bus/
[12]: http://www.windowsazure.com/en-us/documentation/services/storage/
[13]: http://www.windowsazure.com/en-us/gallery/store/
[14]: http://www.windowsazure.com/en-us/develop/net/common-tasks/enable-ssl-web-site/