---
title: Funcionalidade do sistema operativo no App Service do Azure
description: "Saiba mais sobre a funcionalidade de SO disponível para aplicações web, o back-ends de aplicações móveis e API apps no App Service do Azure"
services: app-service
documentationcenter: 
author: cephalin
manager: erikre
editor: mollybos
ms.assetid: 39d5514f-0139-453a-b52e-4a1c06d8d914
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/01/2016
ms.author: cephalin
ms.openlocfilehash: 18ff5c81d0aa5e8a28ed8a11dad19811d2fa1d2c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="operating-system-functionality-on-azure-app-service"></a>Funcionalidade do sistema operativo no App Service do Azure
Este artigo descreve as funcionalidades de sistema operativo comuns de linha de base que está disponível para todas as aplicações em execução no [App Service do Azure](http://go.microsoft.com/fwlink/?LinkId=529714). Esta funcionalidade inclui ficheiros, rede, o acesso ao registo e os registos de diagnóstico e eventos. 

<a id="tiers"></a>

## <a name="app-service-plan-tiers"></a>Escalões do plano de serviço de aplicações
Serviço de aplicações executa aplicações de cliente num ambiente de alojamento de multi-inquilino. As aplicações implementadas no **livres** e **partilhados** camadas executam em processos de trabalho em máquinas virtuais partilhadas, enquanto as aplicações implementadas no **padrão** e **Premium**  camadas executam em ais dedicado especificamente para aplicações associadas um único cliente.

Porque o serviço de aplicações suporta uma experiência totalmente integrada de dimensionamento entre diferentes camadas, a configuração de segurança imposta para aplicações do App Service permanece igual. Isto garante que as aplicações não subitamente comportar-se de forma diferente, falhar formas inesperado, quando muda de plano de serviço de aplicações de um escalão para outro.

<a id="developmentframeworks"></a>

## <a name="development-frameworks"></a>Estruturas de programação
Escalões de preço do serviço de aplicações controlar a quantidade de recursos de computação (CPU, armazenamento de disco, memória e saída de rede) disponíveis para aplicações. No entanto, o leque de funcionalidades do framework disponíveis para aplicações permanece igual independentemente das camadas de dimensionamento.

Serviço de aplicações suporta uma variedade de estruturas de programação, incluindo ASP.NET, ASP clássico, node.js, PHP e Python - todas que executadas como extensões no IIS. Para simplificar e normalizar a configuração de segurança, aplicações de serviço de aplicações são geralmente executam as várias estruturas de programação com as respetivas predefinições. Pode ter sido uma abordagem para configurar aplicações personalizar a área de superfície de API e a funcionalidade para cada estrutura de desenvolvimento individuais. Serviço de aplicações assume em vez disso, uma abordagem mais genérica, permitindo uma linha de base comum de funcionalidades de sistema operativo, independentemente da estrutura de desenvolvimento de uma aplicação.

As secções seguintes resumem os tipos gerais das funcionalidades de sistema operativo disponível para aplicações do App Service.

<a id="FileAccess"></a>

## <a name="file-access"></a>Acesso a ficheiros
Existem várias unidades no App Service, incluindo unidades locais e unidades de rede.

<a id="LocalDrives"></a>

### <a name="local-drives"></a>Unidades locais
O núcleo, o serviço de aplicações é um serviço em execução em cima da infraestrutura do Azure PaaS (plataforma como serviço). Como resultado, as unidades locais "anexados" a uma máquina virtual são os mesmos tipos de unidade disponíveis para qualquer função de trabalho em execução no Azure. Isto inclui uma unidade de sistema operativo (a unidade D:\), uma unidade de aplicação que contém os ficheiros de cspkg do pacote do Azure utilizados exclusivamente pelo App Service (e acessível aos clientes) e uma unidade de "utilizador" (a unidade C:\), cujo tamanho varia consoante o tamanho da VM .

<a id="NetworkDrives"></a>

### <a name="network-drives-aka-unc-shares"></a>Unidades de rede (também conhecido como partilhas UNC)
É um dos aspetos exclusivos do App Service, que faz com que a implementação de aplicação e da manutenção simples que todo o conteúdo de utilizador é armazenado num conjunto de partilhas UNC. Este modelo será muito mapeia o padrão comuns do armazenamento de conteúdo utilizado pelo ambientes que tenham vários servidores com balanceamento de carga de alojamento na web no local. 

No App Service, existem número de partilhas UNC criadas em cada centro de dados. Uma percentagem do conteúdo de utilizador para todos os clientes em cada centro de dados é atribuída a cada partilha UNC. Além disso, todos os o ficheiro de conteúdo para a subscrição de um único cliente é sempre colocada em UNC a mesmo partilham. 

Tenha em atenção que devido a como o trabalho de serviços de nuvem, a máquina virtual específica responsável para alojar uma partilha UNC será alterado ao longo do tempo. É garantido que partilhas UNC serão montadas por diferentes máquinas virtuais que são colocados cima e para baixo no decorrer normal de operações. Por este motivo, as aplicações nunca devem certificar-pressupostos hard-coded que as informações da máquina num caminho de ficheiro UNC permanecerá estáveis ao longo do tempo. Em vez disso, deve utilizar o conveniente *faux* caminho absoluto **D:\home\site** que fornece o serviço de aplicações. Este caminho absoluto faux fornece um método portátil, agnóstico de aplicação e utilizador para que faça referência a um própria aplicação. Ao utilizar **D:\home\site**, um pode transferir os ficheiros partilhados da aplicação para aplicações sem ter de configurar um novo caminho absoluto para cada transferência.

<a id="TypesOfFileAccess"></a>

### <a name="types-of-file-access-granted-to-an-app"></a>Tipos de acesso a ficheiros concedidas a uma aplicação
Subscrição de cada cliente tem uma estrutura de diretórios reservado numa partilha UNC específica no Centro de dados. Um cliente pode ter várias aplicações criadas dentro de um centro de dados específicas, por isso, todos os diretórios que pertencem a uma subscrição de cliente único são criados em UNC a mesmo partilha. A partilha pode incluir diretórios como os referentes ao conteúdo, erro e registos de diagnóstico e versões anteriores da aplicação criada pelo controlo de origem. Conforme esperado, os diretórios de aplicação de um cliente estão disponíveis para leitura e de acesso de escrita no tempo de execução pelo código da aplicação da aplicação.

Nas unidades locais anexadas à máquina virtual que executa uma aplicação, serviço de aplicações reserva-se um segmento de espaço na unidade C:\ para armazenamento temporário local de específico da aplicação. Embora uma aplicação tem acesso de leitura/escrita completa no seu próprio armazenamento temporário local, esse armazenamento realmente não se destina a ser utilizada diretamente pelo código da aplicação. Em vez disso, o objetivo é fornecer armazenamento temporário de ficheiros para o IIS e web estruturas de aplicações. Serviço de aplicações também limita a quantidade de armazenamento local temporário disponível para cada aplicação para impedir o consumo excessivos quantidades de armazenamento de ficheiros local de aplicações individuais.

Dois exemplos de como o App Service utiliza o armazenamento temporário local são o diretório para ficheiros temporários do ASP.NET e o diretório para o IIS ficheiros comprimidos. O sistema de compilação do ASP.NET utiliza o diretório de "Ficheiros temporários do ASP.NET" como uma localização da cache de compilação temporário. Para armazenar o resultado da resposta comprimido, o IIS utiliza o diretório de "IIS comprimido os ficheiros temporários". Ambos os tipos de ficheiro utilização (bem como outros) remapeados no App Service para o armazenamento local temporário por aplicação. Este processo de remapeamento assegura que funcionalidades continua conforme esperado.

Cada aplicação no App Service for executada como uma identidade do processo de trabalho aleatórias de baixo privilégio exclusivo chamada "identidade do conjunto aplicacional", mais descrita aqui: [http://www.iis.net/learn/manage/configuring-security/application-pool-identities ](http://www.iis.net/learn/manage/configuring-security/application-pool-identities). Código da aplicação utiliza esta identidade para acesso só de leitura básico para a unidade de sistema operativo (a unidade D:\). Isto significa que o código da aplicação pode listar as estruturas de diretório comum e ler ficheiros comuns na unidade de sistema operativo. Apesar de isto poderá parecem estar num nível abrangente um pouco de acesso, o mesmo diretórios e ficheiros são acessíveis quando Aprovisiona uma função de trabalho num Azure serviço alojado e ler o conteúdo de unidade. 

<a name="multipleinstances"></a>

### <a name="file-access-across-multiple-instances"></a>Acesso a ficheiros através de várias instâncias
O diretório de raiz contém conteúdo de uma aplicação e pode escrever código da aplicação. Se uma aplicação é executada em várias instâncias, o diretório inicial é partilhado entre todas as instâncias para que o mesmo diretório de ver todas as instâncias. Por isso, por exemplo, se uma aplicação guarda ficheiros carregados para o diretório raiz, esses ficheiros estão imediatamente disponíveis para todas as instâncias. 

<a id="NetworkAccess"></a>

## <a name="network-access"></a>Acesso à rede
Código da aplicação pode utilizar TCP/IP e ligações para Internet acessíveis os pontos finais que expõem serviços externos de rede de protocolos UDP com base para se certificar de saída. As aplicações podem utilizar estes protocolos mesmos para ligar a serviços no Azure &#151; por exemplo, através do estabelecimento de ligações de HTTPS para a base de dados SQL.

Também é uma funcionalidade limitada para aplicações estabelecer uma ligação de local loopback e tiverem uma aplicação escutar num socket que local loopback. Esta funcionalidade não existe principalmente para permitir que as aplicações que escutar sockets de local loopback como parte da respetiva funcionalidade. Tenha em atenção que cada aplicação vê uma ligação de loopback "privado"; Não é possível escutar App "A" para um socket de local loopback estabelecido pela aplicação "B".

Os pipes nomeados também são suportados como um mecanismo de comunicação entre processos (IPC) entre diferentes processos coletivamente executarem uma aplicação. Por exemplo, o módulo FastCGI do IIS depende de pipes nomeados para coordenar processos individuais que executam as páginas do PHP.

<a id="Code"></a>

## <a name="code-execution-processes-and-memory"></a>A execução do código, processos e memória
Conforme indicado anteriormente, as aplicações executadas dentro dos processos de trabalho de baixo privilégio com uma identidade do conjunto aplicacional de aleatório. Código da aplicação tem acesso para o espaço de memória associado com o processo de trabalho, bem como quaisquer processos subordinados que podem ser gerados por processos CGI ou outras aplicações. No entanto, uma aplicação não é possível aceder à memória ou dados de outra aplicação, mesmo se for na mesma máquina virtual.

As aplicações podem executar scripts ou páginas escritas com estruturas de programação web suportado. Serviço de aplicações não configure quaisquer definições de framework de web para modos mais restritos. Por exemplo, aplicações ASP.NET em execução no App Service executam em confiança "completa" em vez de um modo de confiança mais restrito. Estruturas Web, incluindo clássico ASP tanto ASP.NET, podem chamar componentes de COM dentro do processo (mas não fora de componentes de COM do processo) como ADO (ActiveX Data Objects) que estão registados por predefinição no sistema operativo Windows.

As aplicações podem gerar e executar código arbitrário. É permitido para uma aplicação para efetuar ações como expandiram uma shell de comandos ou executar um script do PowerShell. No entanto, apesar de código arbitrário e processos podem ser gerados a partir de uma aplicação, programas executáveis e scripts são ainda restritos a privilégios concedidos para o conjunto aplicacional principal. Por exemplo, uma aplicação pode gerar um ficheiro executável que efetua uma chamada HTTP de saída, mas esse mesmo executável não é possível tentar desvincular o endereço IP de uma máquina virtual do respetivo NIC. Efetuar uma chamada de rede de saída é permitido para o código de privilégio baixo, mas a tentar reconfigurar as definições de rede numa máquina virtual necessita de privilégios administrativos.

<a id="Diagnostics"></a>

## <a name="diagnostics-logs-and-events"></a>Registos de diagnóstico e de eventos
Informações de registo são outro conjunto de dados que tentam aceder a algumas aplicações. Os tipos de informações de registo disponíveis para o código em execução no App Service inclui diagnóstico e registar informações geradas por uma aplicação que também é fácil de aceder à aplicação. 

Por exemplo, registos de HTTP de W3C gerados por uma aplicação Active Directory estão disponíveis num diretório de registo na localização de partilha de rede criado para a aplicação, ou disponíveis no armazenamento de BLOBs, se um cliente tiver configurado o registo de W3C para armazenamento. A última opção permite grandes quantidades de registos de ser reunidas sem o risco de excede o limite de armazenamento de ficheiros associado uma partilha de rede.

Num vein semelhante, informações de diagnóstico em tempo real a partir de aplicações de .NET podem também ser registadas com o rastreio de .NET e a infraestrutura de diagnóstico, com opções para escrever as informações de rastreio para partilha de rede a aplicação, ou em alternativa a uma localização de armazenamento de Blobs.

Áreas de diagnóstico de registo e rastreio que não está disponível para as aplicações são eventos ETW do Windows e registos de eventos do Windows comuns (por exemplo, sistema, aplicações e segurança de registos de eventos). Uma vez que as informações de rastreio ETW podem ser, potencialmente, podem ser visualizados globais (com o direito ACLs), estão bloqueadas de leitura e acesso de escrita para eventos ETW. Os programadores poderão reparar que chamadas à API para ler e escrever ETW eventos e registos de eventos do Windows comuns são apresentados funcionar, mas isto acontece porque o serviço de aplicações é "faking" as chamadas para que apareçam com êxito. Na realidade, o código de aplicação não tem acesso a estes dados de eventos.

<a id="RegistryAccess"></a>

## <a name="registry-access"></a>Acesso de registo
As aplicações têm acesso só de leitura para muito (embora não todas) do registo da máquina virtual que estão em execução. Na prática, isto significa que as chaves de registo que permite o acesso só de leitura ao grupo de utilizadores locais podem ser acedidas por aplicações. Uma área de registo que não é atualmente suportado para leitura ou de acesso de escrita está a HKEY\_atual\_ramo de registo do utilizador.

Acesso de escrita para o registo está bloqueado, incluindo o acesso a quaisquer chaves de registo por utilizador. Perspetiva da aplicação, o acesso de escrita ao registo deve nunca ser confiado no ambiente do Azure, uma vez que as aplicações podem (e fazem) obter migrada através de diferentes máquinas virtuais. O armazenamento gravável apenas persistente que pode ser dependente de uma aplicação está a estrutura de diretório de conteúdo por aplicação armazenada nas partilhas UNC do serviço de aplicações. 

## <a name="more-information"></a>Mais informações

[Sandbox de aplicação Web do Azure](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox) -as informações mais atualizadas sobre o ambiente de execução do serviço de aplicações. Esta página é mantida diretamente pela equipa de desenvolvimento do serviço de aplicações.

> [!NOTE]
> Se pretender começar a utilizar o App Service do Azure antes de se inscrever numa conta do Azure, aceda a [Experimentar o App Service](https://azure.microsoft.com/try/app-service/), onde pode criar de imediato uma aplicação Web de arranque de curta duração no App Service. Sem cartões de crédito; sem compromissos.
> 
> 


