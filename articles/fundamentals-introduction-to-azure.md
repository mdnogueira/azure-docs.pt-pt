---
title: "Introdução ao Microsoft Azure | Microsoft Docs"
description: "Novo no Microsoft Azure? Obter uma descrição geral básica dos serviços oferece com exemplos de como são úteis."
services: " "
documentationcenter: .net
author: rboucher
manager: carolz
editor: 
ms.assetid: 6f47f711-2208-4c21-8c1d-826a54c05c29
ms.service: multiple
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2015
ms.author: robb
ms.openlocfilehash: 2d2cc04887d90b9f005bfe5310a9ce2fb2f7e217
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="introducing-microsoft-azure"></a>Introdução ao Microsoft Azure
Microsoft Azure é plataforma de aplicações da Microsoft para a nuvem pública.  O objetivo deste artigo é para lhe fornecer uma Fundação para compreender as noções básicas do Azure, mesmo se não souber nada sobre nuvem informática.

**Como ler este artigo**

Azure está a crescer tempo para que seja fácil de obter sobrecarregado.  Começar com os serviços básicos, que são apresentados primeiro neste artigo e, em seguida, avance para serviços adicionais. Não significa que não é possível utilizar apenas os serviços adicionais por si, mas os serviços básicos constituem o núcleo de uma aplicação em execução no Azure.

**Enviar comentários**

Os seus comentários são importantes. Este artigo deve dar uma descrição geral efetiva do Azure. Se não existir, indique-na secção de comentários na parte inferior da página. Atribua o detalhe esperado ver e como melhorar o artigo.  

## <a name="the-components-of-azure"></a>Os componentes do Azure
Azure grupos serviços em categorias no Portal de gestão e em vários ajudas de visual, como o [Novidades do Azure gráfico informativo](https://azure.microsoft.com/documentation/infographics/azure/) . O Portal de gestão está a utilizar para gerir a maioria das (mas não todos) serviços no Azure.

Este artigo irá utilizar um **organização diferente** para falar sobre os serviços com base na função semelhante e para chamar serviços secundárias importantes que fazem parte dos maiores.  

![Componentes do Azure](./media/fundamentals-introduction-to-azure/AzureComponentsIntroNew780.png)   
 *Figura: O Azure oferece acessível através da Internet serviços de aplicações em execução nos centros de dados do Azure.*

## <a name="management-portal"></a>Portal de Gestão
O Azure tem uma interface web chamada o [Portal de gestão](http://manage.windowsazure.com) que permite aos administradores de acesso e administrar funcionalidades mais, mas não todas as do Azure.  Microsoft versões, normalmente, o portal de IU mais recente na versão beta antes de o extinguir antigo. Denomina-se a uma mais recente do ["Portal de pré-visualização do Azure"](https://portal.azure.com/).

Se, normalmente, uma sobreposição longa ambos os portais estão ativos. Enquanto os serviços principais irão aparecer em ambos os portais, nem todas as funcionalidades podem estar disponíveis em ambas. Mais recentes serviços podem aparecer na mais recentes do portais primeiro e mais antigos serviços e funcionalidades apenas podem existir no mais antigos.  A mensagem aqui é que se não encontrar algo no portal do anterior, verifique a uma mais recente e vice-versa.

## <a name="compute"></a>Computação
Uma das ações mais básicas que uma plataforma em nuvem está a executar aplicações. Cada um dos modelos de computação do Azure tem as suas próprias funções para reproduzir.

Pode utilizar estas tecnologias em separado ou combine-os conforme necessário para criar a base de direita para a sua aplicação. A abordagem que escolher depende do que problemas que está a tentar resolver.

### <a name="azure-virtual-machines"></a>Máquinas Virtuais do Azure
![Máquinas virtuais do Azure ROBBCSIART_TEST](./media/fundamentals-introduction-to-azure/mscsiart_VirtualMachinesIntroNew_12345.png)   
*Figura: Máquinas virtuais do Azure dá-lhe controlo total sobre instâncias de máquina virtual na nuvem.*

A capacidade de criar uma máquina virtual a pedido, se uma imagem de padrão ou do fornecer, pode ser bastante útil. Esta abordagem, geralmente conhecida como infraestrutura como serviço (IaaS), é o que fornece as Virtual Machines do Azure. A figura 2 mostra uma combinação de como uma Máquina Virtual (VM) é executado e como criar um partir de um VHD.  

Para criar uma VM, tem de especificar o VHD a utilizar e tamanho da VM.  Em seguida, paga para o tempo que a VM está em execução. Paga por minuto e apenas enquanto está em execução, apesar de não existir um custo de armazenamento mínima para manter o VHD disponível. O Azure oferece uma galeria de gráfico de cotações VHDs (denominadas "imagens") que contém um sistema operativo de arranque para iniciar a partir de. Estes incluem opções de parceiro da Microsoft e, como o servidor do Windows e Linux, SQL Server, Oracle e muitos mais. Está a criar VHDs e imagens e, em seguida, carregá-los por si. Pode carregar até mesmo os VHDs que contêm apenas os dados e, em seguida, aceder aos mesmos a partir do seu VMs em execução.

Independentemente do local onde o VHD provém do, forma permanente pode armazenar quaisquer alterações efetuadas enquanto uma VM está em execução. Da próxima vez que cria uma VM a partir desse VHD, escolha coisas onde parou. Os VHDs que criar as máquinas virtuais estão armazenados em blobs de armazenamento do Azure, que abordadas mais tarde.  Isto significa que obtém a redundância para assegurar que as suas VMs não irão desaparecer devido a falhas de hardware e o disco. Também é possível copiar o VHD foi alterado fora do Azure e executá-la localmente.

A aplicação é executado dentro de um ou mais máquinas virtuais, dependendo de como criou antes ou optar por criar agora a partir do zero.

Esta abordagem bastante geral para a informática em nuvem pode ser utilizada para resolver problemas de diferentes muitos.

**Cenários de máquinas virtuais**

1. **Dev/teste** -poderá utilizá-los para criar uma plataforma de desenvolvimento e teste económico que pode encerrar quando tiver terminado a utilizá-la. Também pode criar e executar aplicações que utilizem qualquer idiomas e as bibliotecas que quiser. As aplicações podem utilizar qualquer uma das opções de gestão de dados que o Azure oferece e também pode optar por utilizar o SQL Server ou outro DBMS em execução num ou mais máquinas virtuais.
2. **Mover as aplicações do Azure (comparação de precisão e shift)** -"Comparação de precisão e shift" refere-se para mover a aplicação muito como pretende utilizar um forklift para mover um objeto grande.  "De comparação de precisão" o VHD do seu centro de dados local e "deslocar-" para o Azure e executá-la não existe.  Normalmente, terá de fazer alguns cálculos para remover dependências noutros sistemas. Se existirem demasiados, pode escolher a opção 3 em vez disso.  
3. **Expandir o seu centro de dados** -utilize as VMs do Azure como uma extensão do seu centro de dados no local, com o SharePoint ou outras aplicações. Para suportar isto, é possível criar os domínios do Windows na nuvem através da execução do Active Directory em VMs do Azure. Pode utilizar a rede Virtual do Azure (mencionado mais tarde) associar a rede local e a sua rede no Azure em conjunto.

### <a name="web-apps"></a>Aplicações Web
![ROBBCSIART_TEST de aplicações Web do Azure](./media/fundamentals-introduction-to-azure/mscsiart_AzureWebsitesIntroNew_12345.png)   
 *Figura: As aplicações Web do Azure é executada uma aplicação de Web site na nuvem sem ter de gerir o servidor web subjacente.*

Um dos aspetos mais comuns que pessoas fazer na nuvem é executado sites e aplicações web. Máquinas virtuais do Azure permite isto, mas ainda sai, com a responsabilidade de administrar uma ou mais VMs e os sistemas de operativos subjacentes. Funções de web de serviços na nuvem podem fazer isto, mas implementar e mantê-los ainda demora trabalho administrativo.  E se pretender apenas um Web site em que outra pessoa encarrega-se do trabalho administrativo para si?

Isto é exatamente o que fornece as Web Apps. Este modelo de computação oferece um ambiente de web gerida utilizando o portal de gestão do Azure, bem como os APIs. Pode mover uma aplicação de Web site existente para as Web Apps inalterados, ou pode criar um novo diretamente na nuvem. Depois de um Web site está em execução, pode adicionar ou remover instâncias dinamicamente, depender de Web Apps do Azure para a carga de pedidos de equilíbrio entre eles. As aplicações do Azure oferece uma opção partilhada, onde o seu Web site é executada numa máquina virtual com outros sites, e uma opção padrão que permite que um site ser executada na sua própria VM. A opção padrão também permite-lhe aumentar o tamanho (informática power) das suas instâncias se for necessário.

Para o desenvolvimento, as aplicações Web suporta .NET, PHP, Node.js, Java e Python, juntamente com a base de dados SQL e o MySQL (a partir de ClearDB, um parceiro da Microsoft) para o armazenamento relacional. Também fornece suporte incorporado para várias aplicações populares, incluindo WordPress, o Joomla e o Drupal. O objetivo consiste em fornecer uma plataforma de baixo custo, dimensionável e amplamente útil para a criação de Web sites e aplicações web na nuvem pública.

**Cenários de aplicações Web**

As Web Apps se destina a ser útil para as empresas, os programadores e governamentais de conceção da web. Para as empresas, é uma solução fácil de gerir, dimensionável, altamente segura e disponibilidade elevada para executar a presença de sites. Quando precisar de configurar um Web site, é melhor começar a utilizar Web Apps do Azure e continuar a serviços em nuvem, assim que precisa de uma funcionalidade que não está disponível. Consulte o fim da secção "Computação" para obter mais ligações que podem ajudar a escolher entre as opções.

### <a name="cloud-services"></a>Serviços Cloud
![Serviço em nuvem do Azure](./media/fundamentals-introduction-to-azure/CloudServicesIntroNew.png)   
*Figura: Cloud Services do Azure fornece um local para executar código personalizado altamente dimensionável numa plataforma como um ambiente de serviço (PaaS)*

Suponha que pretende criar uma aplicação na nuvem que consiga suportar muitos utilizadores em simultâneo, não requer muito administração e nunca ficar inativo. Poderá ser um fabricante de software estabelecida, por exemplo, que decidiu adotar Software como serviço (SaaS) ao criar uma versão de uma das suas aplicações na nuvem. Ou poderá ser um arranque de criação de uma aplicação de consumidor que espera que irá aumentar rapidamente. Se estiver a criar no Azure, o modelo de execução deve utilizar?

As aplicações Web do Azure permite criar este tipo de aplicação web, mas existem algumas restrições. Não tem acesso administrativo, por exemplo, que significa que não é possível instalar o software arbitrário. Máquinas virtuais do Azure dá-lhe muita flexibilidade, incluindo acesso administrativo e certamente pode utilizá-la para criar uma aplicação muito dimensionável, mas terá de lidar com vários aspetos de administração e fiabilidade por si. O que pretende que é uma opção que dá-lhe o controlo necessário, mas também faz a maior parte do trabalho necessário para a administração e fiabilidade.

Isto é exatamente o que é fornecido pelos serviços de nuvem do Azure. Esta tecnologia é expressamente concebida para suportar dimensionável, fiável e baixa-admin aplicações e de um exemplo de que tem geralmente designada por plataforma como serviço (PaaS). Utilizá-la, criar uma aplicação utilizando a tecnologia que escolher, como c#, Java, PHP, Python, Node.js ou outra coisa. Em seguida, executa o código em máquinas virtuais (denominadas instâncias) com uma versão do Windows Server.

Mas estas VMs são diferentes das que cria com máquinas virtuais do Azure. Para um aspeto, Azure próprio gere, fazer coisas como instalar correções de erros de sistema operativo e automaticamente disponibilizando novos corrigido imagens. Isto significa que a aplicação não deve manter o estado em instâncias de função web ou de trabalho; em vez disso, devem ser mantida das opções de gestão de dados do Azure descritas na secção seguinte. Azure monitoriza também estas VMs, reiniciar quaisquer com ativação. Pode definir serviços em nuvem para criar automaticamente mais ou menos instâncias em resposta ao pedido. Isto permite-lhe processar uma maior utilização e, em seguida, dimensionar, pelo que não são pagar como muito quando existe menos utilização.

Se tiver duas funções à sua escolha quando cria uma instância, ambos baseiam no Windows Server. A principal diferença entre os dois é que uma instância de uma função web executa o IIS, enquanto uma instância de uma função de trabalho não. Ambos são geridas da mesma forma, no entanto, e é comum para uma aplicação utilizar ambos. Por exemplo, uma instância de função da web pode aceitar pedidos de utilizadores, em seguida, passá-los para uma instância de função de trabalho para processamento. Dimensionar a sua aplicação ou reduzir verticalmente, pode pedir que o Azure criar mais instâncias de uma função ou encerrar instâncias existentes. E semelhantes para máquinas virtuais do Azure, está a cobrados apenas para o tempo que cada instância de função web ou de trabalho está em execução.

**Cenários de serviços cloud**

Serviços cloud são ideais suportar de escalamento horizontal maciço quando precisar de mais controlo sobre o a plataforma que forneceu as Web Apps do Azure, mas não precisa de controlo sobre o sistema operativo subjacente.

#### <a name="choosing-a-compute-model"></a>Escolher um modelo de computação
A página [comparação de Web Apps do Azure, os serviços de nuvem e máquinas virtuais](app-service/choose-web-site-cloud-service-vm.md) fornece informações mais detalhadas sobre como escolher um modelo de computação.

## <a name="data-management"></a>Gestão de Dados
As aplicações precisam de dados e precisam de diferentes tipos de aplicações diferentes tipos de dados. Por este motivo, o Azure fornece várias formas diferentes de armazenar e gerir os dados. O Azure oferece muitas opções de armazenamento, mas todas as foram concebidas para armazenamento muito durável.  Com qualquer uma destas opções, existem sempre 3 cópias dos seus dados mantidos sincronizados através de um datacenter do Azure – 6, se permitir que o Azure utilizar a redundância geográfica para fazer cópias de segurança para outro datacenter pelo menos 300 quilómetros ausente.     

### <a name="in-virtual-machines"></a>Nas máquinas virtuais
Já tem sido indicada a capacidade de executar o SQL Server ou o DBMS outro numa VM criada com máquinas virtuais do Azure. Tenha em atenção que esta opção não está limitada a sistemas relacionais; também está livre para executar as tecnologias de NoSQL como MongoDB e Cassandra. Executar o seu próprio sistema da base de dados é são replicados de simples-it que estamos utilizados para nos nossos própria centros de dados- mas também requer processamento a administração desse DBMS.  Em outras opções, no Azure processa mais ou a totalidade da administração para si.

Novamente, o estado da Máquina Virtual e qualquer disco de dados adicionais criar ou carregar são apoiados por armazenamento de BLOBs (que abordadas mais tarde).  

### <a name="azure-sql-database"></a>Base de Dados SQL do Azure
![Base de dados do Storage do Azure SQL](./media/fundamentals-introduction-to-azure/StorageAzureSQLDatabaseIntroNew.png)   

*Figura: Base de dados SQL do Azure fornece um serviço de base de dados relacional gerido na nuvem.*

Para o armazenamento relacional, o Azure oferece a funcionalidade de base de dados SQL. Não permita que a nomenclatura enganar. Isto é diferente de uma base de dados do SQL Server típico fornecidas pelo SQL Server em execução por cima do Windows Server.  

Anteriormente denominadas SQL Azure, SQL Database do Azure fornece todas as funcionalidades principais de uma base de dados relacional sistema de gestão, incluindo transações atómicas, acesso a dados em simultâneo por vários utilizadores com a integridade dos dados, consultas de ANSI SQL e um modelo de programação familiar. Como o SQL Server, a base de dados SQL pode ser acedida utilizando o Entity Framework, ADO.NET, JDBC e outros dados familiares acesso tecnologias. Também suporta a maioria da linguagem T-SQL, juntamente com ferramentas do SQL Server, tais como o SQL Server Management Studio. Para qualquer pessoa familiarizado com o SQL Server (ou outra base de dados relacional), utilizando a base de dados SQL é simples.

Mas a base de dados SQL não se encontra apenas um DBMS na nuvem-é de um serviço de PaaS. Continuar a controlar os dados e quem pode aceder, mas a base de dados do SQL Server encarrega-se do trabalho grunt administrativos, tais como gerir a infraestrutura de hardware e manter o software de base de dados e sistema operativo actualizado automaticamente. Base de dados do SQL Server também fornece elevada disponibilidade, cópias de segurança automáticas, ponto no tempo capacidades de restauro e podem replicar cópias em regiões geográficas.  

**Cenários para a base de dados SQL**

Se estiver a criar uma aplicação do Azure (utilizando qualquer um dos modelos de computação), que necessita de armazenamento relacional, base de dados SQL pode ser uma boa opção. As aplicações em execução fora da nuvem também podem utilizar este serviço, pelo que existem muitos outros cenários. Por exemplo, os dados armazenados na base de dados SQL podem ser acedidos dos sistemas de cliente diferentes, incluindo computadores de secretária, portáteis, tablets e telemóveis. E porque fornece elevada disponibilidade incorporada através da replicação, utilizando a base de dados do SQL Server pode ajudar a minimizar o período de indisponibilidade.

### <a name="tables"></a>Tabelas
![Tabelas do Storage do Azure](./media/fundamentals-introduction-to-azure/StorageTablesIntroNew.png)  

*Figura: As tabelas do Azure fornece uma forma simples de NoSQL para armazenar dados.*

Esta funcionalidade é por vezes denominada diferentes termos como de parte de uma maior funcionalidade chamada "Storage do Azure". Se vir "tabelas", "Tabelas do Azure" ou "tabelas de armazenamento", é a mesma coisa.  

E não ser confundido com o nome: esta tecnologia não fornece o armazenamento relacional. Na verdade, é um exemplo de uma abordagem de NoSQL chamado um arquivo de chave/valor. As tabelas do Azure permitem uma aplicação armazenar propriedades de vários tipos, por exemplo, cadeias, números inteiros e datas. Uma aplicação, em seguida, pode obter um grupo de propriedades, fornecendo uma chave exclusiva para esse grupo. Enquanto as operações complexas, como não são suportadas associações, tabelas oferecem acesso rápido aos dados introduzidos. Também está muito dimensionáveis, com uma única tabela reter muito como um terabyte de dados. E correspondentes à sua simplicidade, as tabelas são, normalmente, menos dispendiosas a utilizar ao armazenamento relacional da base de dados de SQL.

**Cenários para tabelas**

Suponha que pretende criar uma aplicação do Azure que necessita de um acesso rápido às escreveu os dados, talvez muitas-la, mas não terá de executar consultas SQL complexas estes dados. Por exemplo, imagine que está a criar uma aplicação de consumidor que necessita de armazenar informações de perfil de cliente para cada utilizador. A aplicação vai ser muito mais populares, pelo que terá de permitir a muitos dados, mas que não fazer muito com estes dados para além do armazenamento, em seguida, a obter formas simples. Este é exatamente o tipo de cenário onde tabelas do Azure faz sentido.

### <a name="blobs"></a>Blobs
![Blobs de armazenamento do Azure](./media/fundamentals-introduction-to-azure/StorageBlobsIntroNew.png)    
*Figura: Blobs do Azure fornece dados binários não estruturados.*  

Os Blobs do Azure (novamente "Blob Storage" apenas "Blobs de armazenamento", sendo a mesma coisa) foi concebida para armazenar os dados binários não estruturados. Como tabelas, Blobs fornece armazenamento económico e um único blob pode ser igual a 1TB (um terabyte). Aplicações do Azure também podem utilizar unidades do Azure, que permitem blobs fornecer armazenamento persistente para um sistema de ficheiros do Windows montado numa instância do Azure. A aplicação vê comum ficheiros do Windows, mas o conteúdo, na verdade, é armazenado no blob.

O blob storage é utilizado por muitas outras funcionalidades do Azure (incluindo máquinas virtuais), pelo que pode processar certamente as cargas de trabalho demasiado.

**Cenários para Blobs**

Uma aplicação que armazena os ficheiros de vídeos, em grande escala, ou outras informações binárias pode utilizar blobs para armazenamento cheap simple. Os BLOBs também normalmente são utilizados em conjunto com outros serviços como rede de entrega de conteúdos, serão abordadas mais tarde.  

### <a name="import--export"></a>Importação/Exportação
![Serviço de exportação de importação do Azure](./media/fundamentals-introduction-to-azure/ImportExportIntroNew.png)  

*Figura: Importação do Azure / exportação fornece a capacidade de enviar um disco rígido físico ou a partir do Azure para dados em massa mais rápido e mais barata importar ou exportar.*  

Por vezes, pretender mover uma grande quantidade de dados no Azure. Que deverá demorar muito tempo, talvez dias e utilizar muita largura de banda. Nestes casos, pode utilizar o Azure para importar/exportar, que permite-lhe são enviados encriptada com Bitlocker 3.5" SATA unidades de disco rígido diretamente para os centros de dados do Azure, onde Microsoft irá transferir os dados para o armazenamento de BLOBs para si.  Após a conclusão do carregamento, Microsoft é fornecido unidades de volta para si.  Também pode solicitar que grandes quantidades de dados a partir do Blob Storage ser exportadas em unidades de disco rígido e enviadas novamente através de correio.

**Cenários para importar / exportar**

* **Migração de dados grandes** -sempre tiver grandes quantidades de dados (Terabytes), que pretende carregar para o Azure, o serviço de importação/exportação é muito mais rápido e talvez mais barata que transferi-lo através da internet. Depois dos dados estão em blobs, pode processá-lo para outras formas, como o armazenamento de tabelas ou uma base de dados do SQL Server.
* **Arquivados recuperação de dados** -pode utilizar para importar/exportar ter grande quantidades de dados armazenados no Blob Storage do Azure para um dispositivo de armazenamento que envia e, em seguida, tiver desse dispositivo entregar uma localização pretendidos ao nível de transferência de Microsoft. Porque esta ação irá demorar algum tempo, não é uma boa opção para recuperação após desastre. É melhor dos dados arquivados que não precisa de acesso rápido aos.

### <a name="file-service"></a>Serviço de ficheiros
![Serviço de ficheiros do Azure](./media/fundamentals-introduction-to-azure/FileServiceIntroNew.png)    
*Figura: Serviços de ficheiros do Azure fornece SMB \\ \\server\share caminhos para aplicações em execução na nuvem.*

No local, é comum para grandes quantidades de armazenamento de ficheiros acessível através do protocolo de bloco de mensagem de servidor (SMB) utilizando um \\ \\Server\share formato. O Azure tem agora um serviço que permite-lhe utilizar este protocolo na nuvem. As aplicações em execução no Azure podem utilizá-lo a partilha de ficheiros entre VMs utilizando APIs como ReadFile e WriteFile do sistema de ficheiros familiar. Além disso, os ficheiros também podem ser acedidos em simultâneo através de uma interface REST, que lhe permite aceder às partilhas no local quando também configurar uma rede virtual. Ficheiros do Azure baseia-se do serviço blob, pelo que esta herde o mesmo disponibilidade, características de durabilidade, escalabilidade e redundância geográfica incorporadas no armazenamento do Azure.

**Cenários para ficheiros do Azure**

* **Migrar as aplicações existentes para a nuvem** -o facilitar a migrar aplicações no local à nuvem que utilize partilhas de ficheiros para partilhar dados entre as partes da aplicação. Cada VM liga-se à partilha de ficheiros e, em seguida, pode ler e escrever ficheiros, tal como que iria contra um ficheiro no local partilhar.
* **Partilhar as definições da aplicação** -é um padrão comum para aplicações distribuídas, para que os ficheiros de configuração numa localização central onde possam ser acedidos a partir de outro número de máquinas virtuais. Estes ficheiros de configuração podem ser armazenados numa partilha de ficheiros do Azure e lidos por todas as instâncias da aplicação. As definições também podem ser geridas através da interface REST, que permite o acesso em todo o mundo, para os ficheiros de configuração.
* **Partilha de diagnóstico** - pode guardar e partilhar ficheiros de diagnóstico como registos, métricas e informações de falhas. Ter estes ficheiros estão disponíveis através de SMB e REST interface permite que aplicações a utilizar uma variedade de ferramentas de análise para processar e analisar os dados de diagnóstico.
* **Dev/teste/depuração** - quando os administradores ou programadores estão a funcionar em máquinas virtuais na nuvem, precisam, frequentemente, um conjunto de ferramentas ou utilitários. Instalar e distribuir estes utilitários em cada máquina virtual são demorada. Com os ficheiros do Azure, um programador ou um administrador pode armazenar as suas ferramentas favoritas numa partilha de ficheiros e ligar aos mesmos a partir de qualquer máquina virtual.

## <a name="networking"></a>Redes
Azure é executado atualmente em vários centros de dados distribuídos por todo o mundo. Quando executar uma aplicação ou armazenar dados, pode selecionar um ou mais nestes centros de dados a utilizar. Também pode ligar a estes centros de dados de várias formas de utilizar os serviços de abaixo.

### <a name="virtual-network"></a>Rede Virtual
![VirtualNetwork](./media/fundamentals-introduction-to-azure/VirtualNetworkIntroNew.png)   

*Figura: Redes virtuais fornece uma rede privada na nuvem, de modo a diferentes serviços podem comunicar entre si ou aos recursos no local se configurar uma VPN em vários locais ligação.*  

Uma forma útil de utilizar uma nuvem pública é tratá-los como uma extensão do seu próprio datacenter.

Porque pode criar VMs a pedido, em seguida, removê-las (e parar a pagar) quando já não forem necessárias, pode ter a informática energia apenas quando quiser. E uma vez que as máquinas virtuais do Azure permite-lhe criar VMs em execução SharePoint, o Active Directory e o outro software familiarizado no local, esta abordagem pode trabalhar com as aplicações que já tem.

Para tornar esta realmente útil, no entanto, os utilizadores aparece ser capaz de tratar estas aplicações, como se estivessem a ser executadas no seu próprio datacenter. Isto é exatamente o que permite a rede Virtual do Azure. Utilizar um dispositivo de gateway VPN, um administrador pode configurar uma rede privada virtual (VPN) entre a rede local e as VMs que são implementadas para uma rede virtual no Azure. Porque atribuir os endereços do seu próprio IP v4 para a nuvem VMs, estas aparecem na sua própria rede. Os utilizadores na sua organização podem aceder a aplicações dessas VMs contenham como se estivessem a ser executadas localmente.

Para obter mais informações sobre o planeamento e criar uma rede virtual que funciona para si, consulte [rede Virtual](virtual-network/virtual-networks-overview.md).

### <a name="express-route"></a>ExpressRoute
![ExpressRoute](./media/fundamentals-introduction-to-azure/ExpressRouteIntroNew.png)   

*Figura: ExpressRoute utiliza uma rede Virtual do Azure, mas encaminha ligações através de mais rapidamente as linhas dedicadas em vez da Internet pública.*  

Se precisar de mais largura de banda ou segurança do que uma rede Virtual do Azure pode fornecer a ligação, pode ver no ExpressRoute. Em alguns casos, ExpressRoute pode também poupar dinheiro. Ainda terá uma rede virtual no Azure, mas a ligação entre o Azure e o seu site utiliza uma ligação dedicada que passam para a Internet pública. Para utilizar este serviço, terá de ter um contrato com um fornecedor de serviços de rede ou um fornecedor do exchange.

Configurar a ligação necessita de mais tempo ExpressRoute e de planeamento, por isso, pode querer começar com uma VPN de site a site, em seguida, migrar para uma ligação ExpressRoute.

Para obter mais informações sobre o ExpressRoute, consulte [descrição geral técnica do ExpressRoute](expressroute/expressroute-introduction.md).

### <a name="traffic-manager"></a>Gestor de Tráfego
![TrafficManager](./media/fundamentals-introduction-to-azure/TrafficManagerIntroNew.png)   

*Figura: O Gestor de tráfego do Azure permite-lhe encaminhar o tráfego global para o serviço com base em regras inteligentes.*

Se a aplicação do Azure está em execução em vários datacenters, pode utilizar o Gestor de tráfego do Azure para encaminhar pedidos de utilizadores inteligentemente entre instâncias da aplicação. Também pode encaminhar o tráfego para serviços não em execução no Azure, desde que estejam acessíveis a partir da internet.  

Pode executar uma aplicação do Azure com os utilizadores apenas um único parte do mundo apenas um datacenter do Azure. No entanto, uma aplicação com utilizadores dispersos por todo o mundo, é mais provável ser executada em vários datacenters, talvez mesmo todos eles. Nesta situação segundo, enfrentam um problema: como inteligentemente instruir os utilizadores para instâncias de aplicações? Na maioria das vezes, provavelmente pretende que cada utilizador para aceder ao centro de dados mais próximo da forma, uma vez que, provavelmente proporcionará durante o tempo de resposta melhor. Mas e se essa instância da aplicação estiver sobrecarregado ou não está disponível? Neste caso, seria nice direcionar o respetivo pedido automaticamente para outro datacenter. Isto é exatamente o que é efetuado pelo Gestor de tráfego do Azure.

O proprietário de uma aplicação define as regras que especificam a forma como os pedidos de utilizadores devem ser direcionados para centros de dados, em seguida, baseia-se no Gestor de tráfego para realizar estas regras. Por exemplo, os utilizadores poderão normalmente são direcionados para o datacenter do Azure mais próximo, mas obterem enviados para outra quando o tempo de resposta do seu centro de dados predefinido excede o tempo de resposta de outros centros de dados. Para as aplicações distribuídas globalmente com muitos utilizadores, é útil ter um serviço incorporado para lidar com problemas, como estes.

Gestor de tráfego utiliza o serviço de nomes de diretório (DNS) para os utilizadores de rota para pontos finais de serviço, mas ainda mais tráfego não passa através do Gestor de tráfego após por essa ligação. Este procedimento mantém o Gestor de tráfego que está a ser um congestionamento que pode abrandar as suas comunicações de serviço.

## <a name="developer-services"></a>Serviços de Programação
O Azure disponibiliza várias ferramentas para ajudar os programadores e profissionais de TI criar e manter as aplicações na nuvem.  

### <a name="azure-sdk"></a>SDK para o Azure
Novamente no 2008, a versão de pré-lançamento relativo ao primeiro do Azure suportada apenas desenvolvimento de .NET. Hoje em dia, no entanto, pode criar aplicações do Azure em praticamente qualquer idioma. Atualmente, a Microsoft disponibiliza SDKs de específicas do idioma para .NET, Java, PHP, Node.js, Ruby e Python. Também é um SDK geral do Azure que fornece suporte básico para qualquer idioma, tais como C++.  

Estes SDKs ajudam a criar, implementar e gerir aplicações do Azure. Estiverem disponíveis ou de [www.microsoftazure.com](https://azure.microsoft.com/downloads/) ou GitHub e pode ser utilizada com o Visual Studio e o Eclipse. O Azure também disponibiliza ferramentas de linha de comandos que os programadores podem utilizar com qualquer ambiente de desenvolvimento ou editor, incluindo ferramentas para implementar aplicações do Azure de Linux e Macintosh sistemas.

Juntamente com ajudando-o a criar aplicações do Azure, estes SDKs também fornecem bibliotecas de cliente que o ajudam a criar software que utiliza serviços do Azure. Por exemplo, poderá criar uma aplicação que lê e escreve os blobs do Azure ou criar uma ferramenta que implementa as aplicações do Azure através da interface de gestão do Azure.

### <a name="visual-studio-team-services"></a>Visual Studio Team Services
Visual Studio Team Services é um nome de marketing que abrangem um número dos serviços de que o ajudar a desenvolver aplicações do Azure.

Para evitar confusões - não fornece uma versão alojada ou baseadas na Web do Visual Studio. Ainda tem sua cópia local em execução do Visual Studio. Mas fornece muitas outras ferramentas que podem ser muito úteis.

Inclui um sistema de controlo de origem alojada chamado Team Foundation Service, que oferece controlo de versão e o controlo de item de trabalho.  Mesmo pode utilizar Git para controlo de versão, se preferir que. E pode variar o sistema de controlo de origem que utilizar ao projeto. Pode criar os projetos de equipa privada ilimitados acessível a partir em qualquer lugar do mundo.  

Visual Studio Team Services fornece um serviço de teste de carga. Pode executar os testes de carga criados no Visual Studio em VMs na nuvem. Especifique o número total de utilizadores que pretende carregar teste com e Visual Studio Team Services automaticamente irá determinar quantos agentes são necessários, rotação de cópia de segurança de máquinas virtuais necessárias e executar os testes de carga. Se tiver um subscritor do MSDN, obter milhares de minutos de utilizador gratuitos de cada mês de teste de carga.

Visual Studio Team Services também oferece suporte para desenvolvimento seja ágil com funcionalidades como a integração contínua baseia-se, Kanban quadros e gabinetes virtual de equipa.

**Visual Studio Team Services cenários**

Visual Studio Team Services é uma boa opção para as empresas que precisam de colaborar em todo o mundo e não tiver a infraestrutura no local para o fazer. Pode obter a configuração em minutos, escolha um sistema de controlo de origem e começar a escrever código e a criação de nesse dia.  As ferramentas de equipa fornecem um local para coordenação e colaboração e as ferramentas adicionais fornecem a análise necessária para testar e otimizar rapidamente a sua aplicação.

Mas as organizações que já tem um sistema local, podem testar novos projetos no Visual Studio Team Services se encontra mais eficiente.   

### <a name="application-insights"></a>Application Insights
![Application Insights](./media/fundamentals-introduction-to-azure/ApplicationInsights.png)  

*Figura: Desempenho do Application Insights monitores e a utilização da sua aplicação web ou de dispositivo em direto.*

Quando tiver publicado a aplicação - se de que é executado em dispositivos móveis, computadores de secretária ou browsers de web - Application Insights indica como é executar e que os utilizadores estão a fazer com o mesmo. Irá manter uma contagem de falhas e de resposta lenta, alerta se os valores cruzam inaceitável limiares e ajudarem a diagnosticar problemas.

Quando desenvolver uma nova funcionalidade, planeie medir o sucesso com os utilizadores. Ao analisar padrões de utilização, compreender o que funciona melhor para os seus clientes e melhorar a sua aplicação em cada ciclo de desenvolvimento.

Apesar de estar alojado no Azure, o Application Insights funciona para um intervalo ampla e crescente de aplicações tanto na fora do Azure. J2EE e ASP.NET web apps são abordadas, bem como iOS, as aplicações Android, OSX e Windows. Telemetria é enviada a partir de um SDK criado com a aplicação a ser analisado e apresentado no serviço do Application Insights no Azure.

Se pretender que a análise mais especializadas, exporte o fluxo de telemetria para uma base de dados ou para o Power BI ou quaisquer outras ferramentas.

**Cenários do Application Insights**

Estiver a desenvolver uma aplicação. Poderá ser uma aplicação web ou uma aplicação de dispositivo ou uma aplicação de dispositivo com um back-end da web.

* Otimizar o desempenho da sua aplicação, depois da sua publicação ou enquanto está a ser carregar teste.  Application Insights agrega telemetria de todas as instâncias instaladas e apresenta os gráficos de tempos de resposta, pedido e contagens de exceção, tempos de resposta de dependência e outros indicadores de desempenho. Estes ajudam a otimizar o desempenho da sua aplicação. Pode inserir código para reportar mais dados específicos se o ficheiro necessário.
* Detetar e diagnosticar problemas na sua aplicação em direto. Pode obter alertas por correio eletrónico se indicadores de desempenho cruzam limiares aceitáveis. Pode investigar sessões de utilizador específico, por exemplo ver o pedido que causou uma exceção.
* Controlar a utilização para avaliar o êxito de cada funcionalidade de novo. Ao conceber um novo bloco de utilizador, planeie medir quanto é utilizado e se os utilizadores alcançarem os objetivos previstos. Application Insights dá-lhe dados de utilização básica, tais como as vistas de página web e pode inserir código para controlar a experiência de utilizador mais detalhadamente.

### <a name="automation"></a>Automatização
Nenhum likes waste fazer os processos manuais mais e ao longo do tempo. A automatização do Azure fornece uma forma para que possa criar, monitorizar, gerir e implementar recursos no seu ambiente do Azure.  

Automatização utiliza "runbooks", que utiliza o Windows fluxos de trabalho PowerShell (em vez de apenas regular PowerShell) nos bastidores. Os Runbooks se destinam a ser executado sem interação do utilizador. Fluxos de trabalho do PowerShell permite que o estado de um script seja guardada em pontos de verificação ao longo do processo. Em seguida, se ocorrer uma falha, não terá de iniciar um script a partir do início. Pode reiniciá-lo no último ponto de verificação. Isto poupa muito trabalho ao tentar efetuar o script processar todas as possíveis falhas.

**Cenários de automatização**

A automatização do Azure é uma boa opção para automatizar as tarefas manuais, execução longa, propensas ao erro e frequentemente repetidas no Azure.

### <a name="api-management"></a>Gestão de API
Criar e publicar Interfaces de Programador de aplicações (APIs) na internet é uma forma comum de fornecer serviços e aplicações. Se esses serviços resellable (por exemplo, dados de Meteorologia), uma organização pode permitir que terceiros aceder a esses serviços mesmos uma taxa. À medida que mais parceiros, normalmente terá otimizar e controlar o acesso.  Alguns parceiros mesmo poderão ter dados num formato diferente.

Gestão de API do Azure torna mais fácil para as organizações a publicar APIs para parceiros, funcionários e os programadores de terceiros de forma segura e à escala. Disponibiliza um outro ponto final de API e age como um proxy para chamar o ponto final real, fornecendo serviços como colocação em cache, transformação, limitação, o controlo de acesso e a agregação de análise.

**Cenários de gestão de API**

Digamos da sua empresa tem um conjunto de dispositivos que necessitam de chamada de volta para um serviço central para obter dados – por exemplo, uma empresa de envio que possui dispositivos em cada camião em viagem.  Certamente a empresa irá querer configurar um sistema para controlar a sua própria trucks para que possa fiável prever e atualizar prazos de entrega. Pode saber trucks quantos tem e planear adequadamente.  Cada camião terá de um dispositivo que chama novamente numa localização central com de posicionamento e velocidade de dados e talvez muito mais.

Um cliente da empresa envio seria provavelmente também beneficiar obter estes dados de posicionamento.  O cliente pode utilizá-lo saber até que ponto produtos devem viajar, em que estes ficar bloqueados, quanto estes pagar ao longo de determinados rotas (se combinado com estes paga enviar). Se a empresa de envio agrega já estes dados, poderão paga muitos clientes para a mesma.  Mas, em seguida, a empresa de envio tem de fornecer uma forma de fornecer os dados de clientes. Assim que fornecem acesso aos clientes, não podem ter controlo sobre a frequência de dados está a ser consultados. Terá de fornecer as regras sobre quem pode aceder aos quais os dados. Todas estas regras, teria de ser incorporado no respetivo API externo. Este é onde pode ajudar a API Management.  

## <a name="identity-and-access"></a>Identidade e Acesso
Trabalhar com a identidade faz parte da maioria das aplicações. Saber quem é um utilizador permite que uma aplicação decidir como deve interagir com esse utilizador. O Azure oferece serviços para ajudar a controlar, bem como integrá-lo com os arquivos de identidade que já esteja a utilizar a identidade.

### <a name="active-directory"></a>Active Directory
Como a maioria dos serviços de diretório, o Azure Active Directory armazena informações sobre utilizadores e as organizações que pertencem a. Permite aos utilizadores iniciar sessão, em seguida, fornece-los com tokens podem apresentar a aplicações para provar a sua identidade. Também permite a sincronização das informações de utilizador com o Windows Server Active Directory em execução no local na sua rede local. Enquanto o mecanismos e formatos de dados utilizados pelo Azure Active Directory não são idênticos a são utilizados no Windows Server Active Directory, as funções executa são bastante semelhantes.

É importante compreender que o Azure Active Directory foi concebido principalmente para utilização por aplicações em nuvem. Pode ser utilizado por aplicações em execução no Azure, por exemplo, ou outras plataformas de cloud. Também é utilizado por aplicações de nuvem da Microsoft, tais como no Office 365. Se pretender expandir o seu centro de dados para a nuvem com Virtual Machines do Azure e a rede Virtual do Azure, no entanto, Azure Active Directory não é a escolha certa. Em vez disso, deverá executar o Windows Server Active Directory nas máquinas virtuais.

Para permitir que aplicações aceder às informações contém, o Azure Active Directory fornece uma API RESTful chamado do Azure Active Directory Graph. Esta API permite que as aplicações em execução em quaisquer objetos de diretório de acesso de plataforma e as relações entre eles.  Por exemplo, uma aplicação autorizada poderá utilizar esta API para saber mais sobre um utilizador, os grupos que pertence ao e outras informações. Aplicações também podem ver as relações entre os utilizadores their social gráfico-dar-lhes essa mais inteligentemente trabalhar com as ligações entre as pessoas.

Outra funcionalidade deste serviço, o controlo de acesso de diretório Active Directory do Azure, torna mais fácil para uma aplicação aceitar as informações de identidade do Facebook, Google, Windows Live ID e outros fornecedores de identidade populares. Em vez de exigir a aplicação para compreender os formatos de diversos dados e os protocolos utilizados por cada um destes fornecedores, o controlo de acesso traduz todos eles num único formato comuns. Também permite que uma aplicação aceitar inícios de sessão de um ou mais domínios do Active Directory. Por exemplo, um fornecedor que fornece uma aplicação SaaS poderá utilizar o controlo de acesso do Azure Active Directory para dar aos utilizadores em cada um dos respetivos clientes-início de sessão único para a aplicação.

Serviços de diretório são underpinning um núcleo de computação no local. Não deve ser surprising que estão contidos também importantes na nuvem.

### <a name="multi-factor-authentication"></a>Multi-Factor Authentication
![Multi-Factor Authentication do Azure](./media/fundamentals-introduction-to-azure/MFAIntroNew.png)   

*Figura: Multi-factor Authentication fornece a funcionalidade para a sua aplicação verificar a mais de uma forma de identificação*

Segurança é sempre importante. Autenticação multifator (MFA) ajuda a assegurar que apenas os utilizadores próprios aceder das respetivas contas. MFA (também conhecido como de dois fatores autenticação ou "2FA") necessita que os utilizadores forneçam dois destes três métodos de verificação de identidade para inícios de sessão de utilizador e de transações.

* Algo sabe (normalmente uma palavra-passe)
* Algo que tem (um dispositivo fidedigno não for facilmente duplicado, como um telefone)
* Algo que são (biometria)

Por isso, quando um utilizador inicia sessão, pode exigir que também verificar a sua identidade com uma aplicação móvel, uma chamada telefónica ou uma mensagem de texto em combinação com a palavra-passe. Por predefinição, o Azure Active Directory suporta a utilização de palavras-passe como o método de autenticação apenas para inícios de sessão do utilizador. Pode utilizar a MFA, juntamente com o Azure AD ou com aplicações personalizadas e diretórios através do SDK do MFA. Também pode utilizá-lo juntamente com as aplicações no local utilizando o servidor multi-factor Authentication.

**Cenários MFA**

Proteção de início de sessão em contas confidenciais, tais como inícios de sessão do banco e acesso de código de origem onde entrada não autorizada foi tem uma propriedade de financeira ou intelectual elevada custo.   

## <a name="mobile"></a>Telemóvel
Se estiver a criar uma aplicação para um dispositivo móvel, Azure pode ajudar a armazenar dados na nuvem, autenticar os utilizadores e enviar notificações push sem ter de escrever um elevado grau de código personalizado.

Enquanto pode certamente construir o back-end para uma aplicação móvel utilizar máquinas virtuais, serviços em nuvem ou as aplicações Web, pode demora muito menos tempo ao escrever os componentes de serviço subjacente utilizando os serviços do Azure.

### <a name="mobile-apps"></a>Mobile Apps
![Mobile Apps](./media/fundamentals-introduction-to-azure/MobileServicesIntroNew.png)

*Figura: As aplicações móveis fornece uma funcionalidade frequentemente requerida por aplicações que a interface com dispositivos móveis.*

Mobile Apps do Azure fornece muitas funções útil podem poupar tempo ao criar um back-end para uma aplicação móvel. Permite-lhe fazer simples de aprovisionamento e gestão de dados armazenados na base de dados do SQL. Com o código do lado do servidor pode facilmente utilizar as opções de armazenamento de dados adicionais, como o armazenamento de BLOBs ou MongoDB. As Mobile Apps fornece suporte para notificações, embora em certos casos, em vez disso, pode utilizar os Notification Hubs conforme descrito a seguir.  O serviço tem também uma API REST que pode chamar a aplicação móvel para fazer o trabalho. As Mobile Apps também fornece a capacidade para autenticar os utilizadores através da Microsoft e do Active Directory, bem como outros fornecedores de identidade conhecida como o Facebook, Twitter e Google.   

Pode utilizar outros serviços do Azure como o Service Bus e funções de trabalho e ligar a sistemas no local. Ainda pode consumir 3rd suplementos de terceiros da loja do Azure (como SendGrid de correio eletrónico) para fornecer funcionalidades adicionais.

Bibliotecas de cliente nativo para Android, iOS, HTML/JavaScript, Windows Phone e loja Windows tornam mais fácil desenvolver para aplicações em todas as plataformas móveis principais. Uma API REST permite-lhe utilizar a funcionalidade de dados e a autenticação de Mobile Services com as aplicações em diferentes plataformas. Um único serviço móvel pode criar várias aplicações de cliente para que possam fornecer uma experiência de utilizador consistente em todos os dispositivos.

Como o Azure suporta já grande escala, pode processar o tráfego à medida que a aplicação for mais popular.  Monitorização e de registo são suportadas para o ajudar a resolver problemas e gerir o desempenho.

### <a name="notification-hubs"></a>Hubs de Notificação
![NotificationHubs](./media/fundamentals-introduction-to-azure/NotificationHubsIntroNew.png)  

*Figura: Notification Hubs fornece uma funcionalidade frequentemente requerida por aplicações que a interface com dispositivos móveis.*

Enquanto pode escrever o código para fazer as notificações de Mobile Apps do Azure, os Hubs de notificação está otimizado para difusão milhões de notificações push altamente personalizado dentro de minutos.  Não tem de se preocupar com detalhes como operadora móvel ou fabricante do dispositivo. Pode visar individuais ou milhões de utilizadores com uma única chamada de API.

Hubs de notificação foi concebido para trabalhar com qualquer back-end. Pode utilizar Mobile Apps do Azure, um back-end personalizado na nuvem em execução em qualquer fornecedor ou de um back-end de no local.

**Cenários de Hub de notificação** se foram escrever um jogo móvel onde jogadores demorou activa, poderá ter de notificar player 2 que player 1 terminou a ativar. Se é tudo o que precisa de fazer, pode apenas utilizar Mobile Apps. Mas se tiver 100 000 utilizadores desempenha o seu jogo e pretende enviar uma hora oferta confidencial para todos os utilizadores, os Notification Hubs é a melhor escolha.

Pode enviar notícias de última hora, sporting eventos e notificações de anúncio de produto para milhões de utilizadores com baixa latência. As empresas podem notificar os seus empregados sobre a nova hora confidenciais as comunicações, como vendas potenciais, para que os funcionários não têm constantemente verificar e-mail ou outras aplicações para se manter informado sobre. Também pode enviar one-time-palavras-passe necessária para a autenticação multifator.

## <a name="back-up"></a>Cópia de segurança
Cada empresa tem de criar cópias de segurança e restaurar dados. Pode utilizar o Azure para cópia de segurança e restaurar a aplicação na nuvem ou no local. O Azure oferece diferentes opções para ajudar a dependendo do tipo de cópia de segurança.

### <a name="site-recovery"></a>Site Recovery
O Azure Site Recovery (anteriormente Gestor de recuperação do Hyper-V) pode ajudar a proteger aplicações importantes através da coordenação da replicação e recuperação de sites. Recuperação de sites fornece a capacidade de proteger aplicações com base no Hyper-v, VMWare ou SAN para o seu próprio site secundário, para o site de um fornecedor de alojamento ou para o Azure e evitar o custo e a complexidade de criar e gerir a sua própria localização secundária. Azure encripta os dados e as comunicações e têm a opção de ativar a encriptação de dados em rest demasiado.

Monitoriza continuamente o estado de funcionamento dos seus serviços e ajuda a automatizar a recuperação ordenada de serviços na eventualidade de ocorrer uma falha de site no Centro de dados principal. As máquinas virtuais podem ser recuperadas de forma organizada para ajudar a restaurar rapidamente o serviço, mesmo para cargas de trabalho de vários escalões complexas.

Recuperação de sites funciona com tecnologias existentes, tais como a réplica do Hyper-V, System Center e SQL Server Always On. Veja [descrição geral do Azure Site Recovery](site-recovery/site-recovery-overview.md) para obter mais detalhes.

### <a name="azure-backup"></a>Azure Backup
![Azure Backup](./media/fundamentals-introduction-to-azure/AzureBackupIntroNew.png)  

*Figura: Cópia de segurança do Azure efetua a cópia dos dados de servidores do Windows no local para a nuvem.*  

Cópia de segurança do Azure efetua a cópia de segurança de dados de servidores no local com o Windows Server para a nuvem. Pode gerir as cópias de segurança diretamente a partir de ferramentas de cópia de segurança no Windows Server 2012, Windows Server 2012 Essentials ou System Center 2012 - Data Protection Manager. Em alternativa, pode utilizar um agente de cópia de segurança especializado.

Dados é mais seguro porque as cópias de segurança são encriptadas antes da transmissão e armazenados encriptado no Azure e protegido por um certificado que carregar. O serviço utiliza a mesma proteção de dados redundantes e altamente disponível encontrada no armazenamento do Azure.  Pode criar cópias de segurança ficheiros e pastas com base numa agenda regular ou imediatamente, executar cópias de segurança completas ou incrementais. Depois de dados de cópias de segurança para a nuvem, os utilizadores autorizados podem facilmente recuperar cópias de segurança em qualquer servidor. Também proporciona as políticas de retenção de dados configuráveis, compressão de dados e dados de transferência de limitação para que consiga gerir o custo de armazenar e transferência de dados.

**Cenários de cópia de segurança do Azure**

Se tiver já a utilizar o Windows Server ou o System Center, cópia de segurança do Azure é uma solução natural para fazer uma cópia de segurança do sistema de ficheiros de servidores, máquinas virtuais e bases de dados do SQL Server.  Funciona com ficheiros encriptados, comprimidos e dispersos. Existem algumas limitações, pelo que deve [verificar os pré-requisitos de cópia de segurança do Azure](http://technet.microsoft.com/library/dn296608.aspx) primeiro.

## <a name="messaging-and-integration"></a>Mensagens e Integração
Independentemente do que este está a fazer, código frequentemente tem de interagir com outro código.  Em algumas situações, tudo o que precisa é de mensagens em fila básicas. Noutros casos, são necessárias as interações mais complexas. O Azure oferece várias formas para resolver estes problemas. Figura 5 ilustra as opções.

### <a name="queues"></a>Filas
![Reencaminhamento de barramento de serviço do Azure](./media/fundamentals-introduction-to-azure/QueuesIntroNew.png)

*Figura: As filas permitem soltas coupling entre as partes de uma aplicação e facilitam o dimensionamento.*  

Colocação em fila é uma ideia simple: uma aplicação coloca uma mensagem numa fila, e essa mensagem, eventualmente, é lida por outra aplicação. Se a sua aplicação tiver apenas este serviço simples, as filas do Azure pode ser a melhor opção.

Devido à forma como o Azure queria ao longo do tempo, as filas de armazenamento do Azure e filas do Service Bus fornecerem serviços de colocação semelhantes. Os motivos por que motivo iria querer utilizar um detrimento dos outros são abordados no documento técnico bastante [filas do Azure e filas do Service Bus - comparados e Contrasted](http://msdn.microsoft.com/library/azure/hh767287.aspx).  Em vários cenários, quer irá funcionar.

**Cenários de fila**

Hoje em dia é uma utilização comum de filas permitir que uma instância de função da web comunicar com uma instância de função de trabalho na mesma aplicação de serviços em nuvem.

Por exemplo, suponha que criar uma aplicação do Azure para a partilha de vídeo. A aplicação é composta por código do PHP em execução numa função da web que permite que os utilizadores carregar e veja vídeos, juntamente com uma função de trabalho implementado em c# que traduz as vídeo carregada em vários formatos.

Quando uma instância de função web obtém um novo vídeo de um utilizador, pode armazenar o vídeo num blob, em seguida, enviar uma mensagem para uma função de trabalho através de uma fila informar-onde encontrar este vídeo de novo. Um trabalho função instância-it não importa a que será um, em seguida, leia a mensagem da fila e desempenhar as traduções de vídeos necessárias em segundo plano.

Structuring uma aplicação desta forma permite o processamento assíncrono e também faz a aplicação mais fáceis de escala, desde que o número de instâncias de função da web e instâncias de função de trabalho pode ser diversificado de forma independente. Também pode utilizar o tamanho da fila como um acionador para aumentar o número de funções de trabalho acima e abaixo. Demasiado elevado, e adicione várias funções. Quando obtém inferior, pode reduzir o número de executar funções poupar dinheiro.  

Pode utilizar este mesmo padrão entre muitos diferentes partes da sua aplicação, mesmo que não utilizem funções web e de trabalho.  Permite-lhe dimensionar as peças em ambos os lados da fila acima e abaixo como a pedido e necessita de tempo de processamento.

### <a name="service-bus"></a>Service Bus
Se são executados na nuvem, no seu centro de dados, um dispositivo móvel ou algures outra forma, as aplicações precisam de interagir. O objetivo do Service Bus do Azure é permitir que as aplicações em execução praticamente qualquer lugar trocar dados.

Para além das filas de (um para um) descritas anteriormente, o Service Bus também fornece a outros métodos de comunicação.

#### <a name="service-bus-relay"></a>Reencaminhamento do Service Bus
![Reencaminhamento de barramento de serviço do Azure](./media/fundamentals-introduction-to-azure/ServiceBusRelayIntroNew.png)

*Figura: O reencaminhamento do Service Bus permite a comunicação entre aplicações em diferentes lados de uma firewall.*

Service Bus permite comunicação direta através do respetivo serviço de reencaminhamento, fornecendo uma forma segura para interagir através de firewalls. Reencaminhamentos do Service Bus permitem às aplicações comunicar ao trocar mensagens através de um ponto final alojado na nuvem, em vez de localmente.

**Cenários de reencaminhamento do Service Bus**

As aplicações que comunicam através do Service Bus poderão ser software em execução na outra plataforma de nuvem ou de aplicações do Azure. Também podem ser aplicações executadas fora da nuvem, no entanto. Por exemplo, considere uma companhia aérea que implementa os serviços de reserva em computadores no seu próprio datacenter. A companhia aérea tem de expor esses serviços para muitos clientes, incluindo inicio de quiosques no aeroportos, terminals do agente de reserva e talvez até mesmo telemóveis dos clientes. -Pode utilizar o Service Bus para fazer isto, criar aproximadamente ligado conjugadas interações entre várias aplicações.

#### <a name="service-bus-topics-and-subscriptions"></a>Tópicos e Subscrições do Service Bus
![Tópicos do barramento de serviço do Azure](./media/fundamentals-introduction-to-azure/ServiceBusTopicsSubsIntroNew.png)   
 *Figura: Tópicos de Service Bus permite que várias aplicações publicar mensagens e outras aplicações para subscrever para receber mensagens que cumprem critérios específicos.*

O Service Bus fornece um mecanismo de publicação e subscrição chamado tópicos e subscrições. Com publish-subscribe, uma aplicação pode enviar mensagens para um tópico, enquanto outras aplicações podem criar subscrições deste tópico. Isto permite a comunicação de um-para-muitos entre um conjunto de aplicações, permitindo que a mesma mensagem ser lidos pelos destinatários vários.

**Tópicos de Service Bus e cenários de subscrições**

Em qualquer altura estiver a configurar em que existem muitas mensagens de todas as importantes, mas vários sistemas a jusante necessário apenas escutar diferentes subconjuntos destas comunicações, tópico de barramento de serviço e as subscrições são uma boa opção.

### <a name="biztalk-services"></a>Serviços BizTalk
![Serviços BizTalk](./media/fundamentals-introduction-to-azure/BizTalkServicesIntroNew.png)   
 *Figura: BizTalk Services fornece a capacidade de transformação XML formatos de mensagens na nuvem.*

Por vezes, precisa de ligar sistemas que comunicam com diferentes formatos de mensagens. É comum para empresas XML mensagens formatos, mesmo quando um padrão comum está disponível e de esquemas de base de dados diferente. Em vez de escrever uma grande quantidade de código personalizado, pode utilizar o BizTalk Server no local para integrar vários sistemas.  BizTalk Services do Azure fornece o mesmo tipo de serviço, mas na nuvem. Pode paga apenas o que utiliza e não se preocupe escala como tiver no local.

**Cenários de serviços BizTalk**

Interações de empresa-empresa (B2B) necessitam, normalmente, este tipo de conversão.  Por exemplo, uma empresa de criação de airplanes tem ordenar partes de vários fornecedores de partes. Pode ter vários fornecedores de partes.  Esses ordens devem ser automatizadas para aceder diretamente a partir de sistemas de construtores avião para os sistemas de fornecedores.  Nenhuma das empresas pretende alterar os respetivos sistemas de núcleos e formatos de mensagem e não é muito provável que esses formatos são os mesmos. BizTalk Services podem demorar mensagens e traduzir entre os formatos de nova ambas as direções. O fornecedor de avião, pode efetuar o trabalho traduzir ou podem de vários fornecedores, dependendo do que quiser ter maior controlo e a quantidade de tradução necessária.     

## <a name="compute-assistance"></a>Assistência de computação
O Azure oferece assistência para serviços que não é necessário executar sempre.  

### <a name="scheduler"></a>Scheduler
![Azure Scheduler](./media/fundamentals-introduction-to-azure/SchedulerIntroNew.png)   
*Figura: o agendador do Azure fornece uma forma para agendar trabalhos num momento específico durante um período específico.*

Por vezes, as aplicações só tem de executar um determinado período de tempo. No Azure, pode poupar dinheiro com este tipo de aplicação em vez de permitir que uma aplicação apenas continuará a ser executada 24x7 aguardar dados a processar. O agendador do Azure permite-lhe agendar quando uma aplicação deve executar com base num intervalo de tempo ou num calendário. É fiável e irá verificar que é executado um processo, mesmo se houver falhas de centro de dados, de máquina e de rede. Utilize a API de REST do agendador para gerir estas ações.

Quando ocorre um alarme agendada, o agendador envia mensagens HTTP ou HTTPS para um ponto final específico ou pode colocar uma mensagem numa fila de armazenamento.  Por isso, tem de ter a sua aplicação tiver um ponto final acessível ou que o monitorizar uma fila de armazenamento. Em seguida, uma vez, obtém a mensagem, pode efetuar qualquer ação que está programado para.

**Cenários de programador**

* Ações de aplicação recorrente: por exemplo, um serviço pode periodicamente obter dados a partir do twitter e recolher os dados para um feed regular.
* Manutenção diária: registo de processamento ou eliminação, realizar cópias de segurança e outras ligados intermitentemente agendar tarefas.
* Tarefas que são executadas a noite.
* Tarefas de aplicações Web, como a eliminação diária de registos, realizar cópias de segurança e outras tarefas de manutenção. Um administrador pode optar por criar cópias de segurança da sua base de dados às 1: 00 todos os dias durante os próximos meses 9, por exemplo.

API do agendador permite-lhe criar, atualizar, eliminar, ver e gerir coleções de tarefas e tarefas agendadas através de programação.

## <a name="performance"></a>Desempenho
Desempenho é sempre importante para uma aplicação. As aplicações têm tendência aceder aos mesmos dados mais e mais. Uma forma para melhorar o desempenho é manter uma cópia do próximo esses dados para a aplicação, o que minimiza o tempo necessário para obtê-lo. O Azure oferece serviços diferentes para fazê-lo.

### <a name="azure-caching"></a>Colocação em Cache do Azure
![Colocação em Cache do Azure](./media/fundamentals-introduction-to-azure/AzureCacheIntroNew.png)   
 **Figura: Uma aplicação do Azure pode colocar em cache dados na memória e mesmo dividir-por muitas funções de trabalho**

Aceder aos dados armazenados em qualquer um dos gestão de dados do Azure, base de dados SQL de serviços, tabelas ou Blobs-é bastante rápida. Ainda aceder aos dados armazenados na memória é mais rápido do mesmo. Por este motivo, a manter uma cópia de memória dos dados acedidos com frequência pode melhorar o desempenho da aplicação. Pode utilizar a colocação em cache na memória do Azure para efetuar este procedimento.

Uma aplicação de serviços em nuvem pode armazenar dados nesta cache, em seguida, obtê-lo diretamente sem precisar de aceder ao armazenamento persistente. A cache pode ser mantida dentro VMs da sua aplicação ou fornecida por VMs dedicadas apenas para a colocação em cache. Em ambos os casos, a cache pode ser distribuída, com os dados que contém a propagação através de várias VMs num datacenter do Azure.

O Azure tem uma série de tecnologias de cache diferente que tenha desviado ao longo do tempo. A ordem que foram introduzidos, há partilhado, na função, geridos e a cache de Redis. A partilhado a colocação em cache é uma tecnologia de mais antiga e não deve criar novas implementações com o mesmo. A Cache geridos tem as mesmas funcionalidades da cache de função, mas como um serviço gerido fora do Portal de gestão do Azure. A Cache de Redis está em pré-visualização. A implementação de Redis tem o número maior de funcionalidades e é recomendada quando escrever o novo código de colocação em cache.

**Cenários de Cache do Azure**

Uma aplicação que repetidamente lê um catálogo de produtos poderá beneficiar da utilização deste tipo de colocação em cache, por exemplo, desde o tipo de dados tem de estará disponível mais rapidamente. A tecnologia também suporta bloqueio, permitindo que utilizou leitura/escrita, bem como os dados só de leitura. E aplicações ASP.NET podem utilizar o serviço para armazenar dados de sessão com apenas uma alteração da configuração.

### <a name="content-delivery-network"></a>Rede de Entrega de Conteúdos
![CDN do Azure](./media/fundamentals-introduction-to-azure/CDNIntroNew.png)   
 **Figura: Cópias de um blob podem ser colocadas em cache em sites em todo o mundo.**

Suponha que precisa armazenar dados de BLOBs que serão acedidos por utilizadores em todo o mundo. Talvez é um vídeo da mais recente mundo Cup correspondência, por exemplo, ou atualizações de controladores ou um popular e-book transferível. Armazenar uma cópia dos dados em vários centros de dados do Azure irá ajudá-lo, mas se existirem muitos dos utilizadores, provavelmente, não é suficiente. Para o mesmo um melhor desempenho, pode utilizar a CDN do Azure.

A CDN tem dezenas de sites em todo o mundo, cada capacidade para armazenar cópias de blobs do Azure. Na primeira vez que um utilizador alguma parte do mundo acede a um blob específico, informações que contém são copiadas de um datacenter do Azure para armazenamento CDN local em que geografia. Depois da primeira, acessos de parte do mundo utilizará a cópia de BLOBs em cache na CDN-não terá de ir para o datacenter do Azure mais próximo. O resultado é um acesso mais rápido para dados acedidos com frequência pelos utilizadores em qualquer lugar do mundo.

**Cenários CDN**

É comum para utilizar a CDN com os Media Services para fornecer as vídeo em todo o mundo. Vídeo é normalmente grande e necessita de uma grande quantidade de largura de banda.  Os Media Services é abordou noutro local nesta página.

## <a name="big-data-and-big-compute"></a>Macrodados e Macrocomputação
### <a name="hdinsight-hadoop"></a>HDInsight (Hadoop)
![HDInsight](./media/fundamentals-introduction-to-azure/HDInsightIntroNew.png)   
 **Figura: HDInsight ajuda-o com o processamento em massa de quantidades enormes de dados**

Durante muitos anos, o volume de análise de dados tiver sido realizado em dados relacionais armazenados num armazém de dados criado com uma DBMS relacional. Este tipo de análise de negócio é importante ainda, e será muito tempo. Mas e se os dados que pretende analisar, por isso, grande que bases de dados relacionais apenas não consegue processá-lo? E suponha que os dados não relacionais? Poderá ser os registos do servidor no Centro de dados, por exemplo, ou dados de evento históricos dos sensores ou outra coisa. Em casos como esta, terá do que é conhecido como um problema de macrodados. Outra abordagem é necessário.

A tecnologia dominante hoje para análise de macrodados é o Hadoop. Um Apache abrir projeto de origem, esta tecnologia armazena os dados utilizando o distribuídas ficheiro sistema Hadoop (HDFS), em seguida, permite aos programadores criar tarefas de MapReduce para analisar os dados. HDFS propaga dados em vários servidores, em seguida, os segmentos de execução da tarefa de MapReduce em cada um, permitindo que os macrodados ser processados em paralelo.

O HDInsight é o nome do serviço de baseado no Apache Hadoop do Azure. HDInsight permite HDFS armazenar dados no cluster e distribuí-la através de várias VMs. Também se propaga a lógica de uma tarefa de MapReduce entre essas VMs. Tal como com o Hadoop no local, os dados são processada lógica localmente-o e os dados funciona no estão na mesma VM- e em paralelo para um melhor desempenho. HDInsight também pode armazenar dados no Azure armazenamento cofre (ASV), que utiliza os blobs.  Utilizar ASV permite poupar dinheiro porque pode eliminar o cluster do HDInsight quando não está em utilização, mas ainda manter os seus dados na nuvem.

HDinsight suporta outros componentes do ecossistema do Hadoop, incluindo Hive e Pig. Microsoft também tiver criado componentes que tornam mais fácil trabalhar com dados produzidos pelo HDInsight utilizando as ferramentas de BI tradicionais, como o adaptador de HiveODBC e Explorador de dados que funcionam com o Excel.

### <a name="high-performance-computing-big-compute"></a>Elevado desempenho (Macrocomputação) de computação
Uma das formas mais apelativo para utilizar uma plataforma em nuvem está a executar elevado desempenho (HPC) de computação e de outras aplicações "De Macrocomputação". Os exemplos incluem aplicações de engenharia especializadas incorporadas a utilizar o padrão da indústria MPI Message Passing Interface (), bem como aplicações constrangedoramente paralelas so-called, esses modelos de riscos financeiros.

Essence de Macrocomputação está a executar o código em várias máquinas em simultâneo. No Azure, isto significa que a executar muitos virtual as máquinas em simultâneo, tudo a funcionar em paralelo para resolver um problema. Fazê-lo requer alguma forma aos recursos e agendar as aplicações, ou seja, para distribuir o respetivo trabalho entre estas instâncias. HPC Pack gratuita da Microsoft e outras soluções de cluster de computação podem executar também no Azure, tendo partido dos serviços de computação e de infraestrutura do Azure para adicionar capacidade a pedido a um cluster de computação no local ou executar aplicações de Macrocomputação inteiramente na nuvem.

O Azure oferece um intervalo de VM os tamanhos de instância com as configurações diferentes do CPU núcleos, memória, capacidade do disco e outras características para cumprir os requisitos de aplicações diferentes. O trabalho de instâncias de A8 e A9 recentemente introduzido bem para muitos de computação intensivas cargas de trabalho e aplicações MPI paralelas em particular, porque têm de alta velocidade, CPUs especializados e grandes quantidades de memória. Em determinadas configurações de instâncias beneficiam de numa rede de latência baixa e alta débito aplicações na nuvem que inclui a tecnologia de (RDMA) de acesso remoto direto à memória para uma eficiência máxima de aplicações MPI paralelas.

O Azure também oferece aos programadores de aplicações de Macrocomputação e parceiros um conjunto completo de capacidades de computação, serviços, opções de arquitetura e ferramentas de desenvolvimento. Azure suporta Macrocomputação fluxos de trabalho personalizados que envolvem fluxos de trabalho de dados especializadas e trabalho e tarefas agendamento padrões que podem ser dimensionados para milhares de núcleos de computação.

## <a name="media"></a>Meios de comunicação social
![Os Media Services do Azure](./media/fundamentals-introduction-to-azure/MediaServicesIntroNew.png)   
 **Figura: Dos Media Services é uma plataforma para aplicações que fornecem as vídeo e outros suportes de dados para os clientes em todo o mundo.**

Vídeo constitui uma grande parte do tráfego de Internet de hoje e essa percentagem será ainda maior amanhã. Ainda não fornecer as vídeo na web é simple. Existem muitas variáveis, como o algoritmo de codificação e a resolução de ecrã do ecrã do utilizador. Vídeo também tende a ter bursts a pedido, como um pico Sábado noite pedidos quando muitas pessoas decidir que gostaria de ver um filme online.

Tendo em conta a popularidade, é uma segura bet que muitas aplicações novas serão criadas nesse vídeo de utilização. Ainda todos eles terá de resolver alguns dos problemas mesmos e efetuar cada um deles resolver esses problemas na sua própria faz com que não existem sentido. Uma abordagem de melhor consiste em criar uma plataforma que fornece soluções comuns para muitas aplicações a utilizar. E criar esta plataforma na nuvem tem algumas vantagens encriptadas. Pode ser amplamente disponível numa base pay as you go e também pode lidar com a variabilidade em termos de que as vídeos aplicações frequentemente enfrentam.

Media Services do Azure resolve este problema. Fornece um conjunto de componentes de nuvem que o facilitar a vida para as pessoas a criar e executar aplicações utilizando as vídeo e outros suportes de dados.

Como mostra a figura, os Media Services fornecem um conjunto de componentes para aplicações que funcionam com vídeos e outros suportes de dados. Por exemplo, inclui um suporte de dados de inserção componente ao carregar as vídeo para os serviços de suporte de dados (onde são armazenado em Blobs do Azure), um componente de codificação que suporta vários formatos de áudio e vídeos, um componente de proteção de conteúdos que fornece gestão de direitos digitais, um componente para inserir anúncios um fluxo de vídeo, componentes para transmissão em fluxo e muito mais. Parceiros da Microsoft podem também fornecer componentes para a plataforma, em seguida, tiver Microsoft distribuir esses componentes e cobrar em nome daqueles.

As aplicações que utilizam esta plataforma podem ser executados no Azure ou noutro local. Por exemplo, uma aplicação de ambiente de trabalho para a próxima um vídeo de produção pode permitir que utilizadores carregar as vídeo aos Media Services, em seguida, processá-lo de várias formas. Em alternativa, um serviço de gestão de conteúdo baseado na nuvem em execução no Azure poderá baseiam-se nos serviços de multimédia para processar e distribuir as vídeo. Onde quer que é executado e que faz, cada aplicação escolhe que componentes tem de utilizar, aceder às mesmas através de RESTful interfaces.

Para distribuir o que produz, uma aplicação pode utilizar a CDN do Azure, outro CDN, ou enviar apenas bits diretamente aos utilizadores. No entanto, existe obtém, vídeo criado utilizando os Media Services pode ser utilizado por vários sistemas de cliente, incluindo Windows, Macintosh, HTML 5, iOS, Android, Windows Phone, Flash e Silverlight. O objetivo é tornar mais fácil criar aplicações modernas do suporte de dados.

**Referências**

Para obter uma vista mais visual como funciona o serviços de suporte de dados, transfira o [cartaz de serviços de suporte de dados do Azure][Azure Media Services Poster].

## <a name="commerce"></a>Comércio
O aumento súbito de Software como um serviço é transformar como podemos criar aplicações. É também transformar como podemos propor aplicações. Uma vez que exista uma aplicação SaaS na nuvem, faz sentido em que os potenciais clientes devem procurar soluções online. E esta alteração aplica-se aos dados, bem como as aplicações. Por que motivo não devem pessoas serve para a nuvem para conjuntos de dados disponíveis comercialmente? Microsoft endereços ambos estes preocupações com a [Azure Marketplace](https://azure.microsoft.com/marketplace/).

![Comércio do Azure](./media/fundamentals-introduction-to-azure/CommerceIntroNew.png)   
 **Figura: Azure Marketplace e de armazenamento do Azure permitem-lhe localizar e comprar conjuntos de dados comerciais e de aplicações do Azure e utilizá-los como parte das aplicações do Azure.**

A diferença entre os dois é que o Marketplace está fora do Portal de gestão do Azure, mas o arquivo de pode ser acedido a partir dentro do portal. Potenciais clientes podem procurar para localizar aplicações do Azure que satisfazem as suas necessidades. Os clientes podem procurar comerciais conjuntos de dados, incluindo dados demográficos, dados financeiros, dados geográficos e muito mais. Quando encontrar algo que gostou, poderem aceder ao mesmo a partir do fornecedor, diretamente através de localizações na web Marketplace ou arquivo ou em alguns casos de Portal de gestão. As aplicações podem também utilizar a API de pesquisa do Bing através do Marketplace, concedendo-lhes acesso para os resultados de pesquisas de web.

**Cenários de comércio**

SendGrid é uma aplicação na loja do Azure que permite-lhe enviar correio eletrónico. Oferece funcionalidades adicionais, como as estatísticas e entrega fiável.  Pode comprar esta aplicação e os serviços relacionados em vez de tentar criar uma infraestrutura de tal por si.  

## <a name="getting-started"></a>Introdução
Agora que tiver a visão, o passo seguinte é escrever a sua primeira aplicação do Azure. Escolha a linguagem [obter o SDK adequado](/downloads/)e vá para o mesmo. Nuvem informática é a predefinição de nova – começar agora mesmo.

[Azure Media Services Poster]: http://azure.microsoft.com/documentation/infographics/media-services/
