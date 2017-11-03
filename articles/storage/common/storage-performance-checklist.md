---
title: "Azure armazenamento desempenho e escalabilidade lista de verificação | Microsoft Docs"
description: "Uma lista de verificação das práticas comprovadas para utilização com o Storage do Azure no desenvolvimento performant aplicações."
services: storage
documentationcenter: 
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 959d831b-a4fd-4634-a646-0d2c0c462ef8
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2016
ms.author: tamram
ms.openlocfilehash: 6f5a136d1be7a4bb4093baad820271770305b718
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="microsoft-azure-storage-performance-and-scalability-checklist"></a>Lista de Verificação de Desempenho e Escalabilidade do Armazenamento do Microsoft Azure
## <a name="overview"></a>Descrição geral
Desde a versão dos serviços de armazenamento do Microsoft Azure, Microsoft desenvolveu várias comprovadas práticas para utilizar estes serviços de forma performant e este artigo serve para consolidar o mais importante-las numa lista lista de verificação estilo. A intenção este artigo é para ajudar os programadores de aplicações, certifique-se de que estão a utilizar práticas comprovadas com armazenamento do Azure e para ajudar a identificar outras práticas comprovadas deve considerar a adoção. Este artigo não tenta abranger todas as possíveis otimização de desempenho e escalabilidade — exclui os que são pequenos no respetivo impacto nas ou não é amplamente aplicável. Mesmo que possível prever comportamento da aplicação durante a conceção, é útil para manter estas em mente antecipadamente para evitar estruturas que serão depare com problemas de desempenho.  

Cada programador de aplicações utilizando o armazenamento do Azure deve ter a hora para ler este artigo e verifique se a aplicação segue cada uma das práticas de comprovado listadas abaixo.  

## <a name="checklist"></a>Lista de verificação
Este artigo organiza as práticas comprovadas para os seguintes grupos. Práticas comprovadas aplicadas-se a:  

* Todos os serviços de armazenamento do Azure (blobs, tabelas, filas e ficheiros)
* Blobs
* Tabelas
* Filas  

| Concluído | Área | Categoria | Pergunta |
| --- | --- | --- | --- |
| &nbsp; | Todos os Serviços |Objetivos de escalabilidade |[A aplicação foi concebida para evitar atingir os objetivos de escalabilidade?](#subheading1) |
| &nbsp; | Todos os Serviços |Objetivos de escalabilidade |[A Convenção de nomenclatura concebida para permitir a melhor balanceamento de carga?](#subheading47) |
| &nbsp; | Todos os Serviços |Redes |[Dispositivos de lado cliente dispõem de largura de banda suficientemente alta e baixa latência para alcançar o desempenho necessário?](#subheading2) |
| &nbsp; | Todos os Serviços |Redes |[Dispositivos de lado cliente dispõem de uma ligação de qualidade suficientemente elevada?](#subheading3) |
| &nbsp; | Todos os Serviços |Redes |[A aplicação de cliente estão localizada "perto de" conta de armazenamento?](#subheading4) |
| &nbsp; | Todos os Serviços |Distribuição de Conteúdos |[Está a utilizar uma CDN para distribuição de conteúdos?](#subheading5) |
| &nbsp; | Todos os Serviços |Acesso de cliente direto |[Está a utilizar SAS e o CORS para permitir o acesso direto ao armazenamento em vez do proxy?](#subheading6) |
| &nbsp; | Todos os Serviços |Colocação em cache |[Raramente é a colocação em cache dados da aplicação que são utilizados repetidamente e as alterações?](#subheading7) |
| &nbsp; | Todos os Serviços |Colocação em cache |[É a aplicação de criação de batches atualizações (a colocação em cache-os do lado do cliente e, em seguida, carregar conjuntos de maior)?](#subheading8) |
| &nbsp; | Todos os Serviços |Configuração do .NET |[Tiver configurado o cliente para utilizar um número suficiente de ligações simultâneas?](#subheading9) |
| &nbsp; | Todos os Serviços |Configuração do .NET |[Tiver configurado .NET para utilizar um número suficiente de threads?](#subheading10) |
| &nbsp; | Todos os Serviços |Configuração do .NET |[Está a utilizar o .NET 4.5 ou posterior, que foi melhorado recolha de lixo?](#subheading11) |
| &nbsp; | Todos os Serviços |Paralelismo |[Pode certificar-se de que paralelismo é tem um vínculo adequadamente para que não a sobrecarga suas capacidades de cliente ou os destinos de escalabilidade?](#subheading12) |
| &nbsp; | Todos os Serviços |Ferramentas |[Estão a utilizar a versão mais recente do Microsoft fornecida ferramentas e bibliotecas de cliente?](#subheading13) |
| &nbsp; | Todos os Serviços |Tentativas |[Estão a utilizar um término exponencial repetir a política de limitação de erros e tempos limite?](#subheading14) |
| &nbsp; | Todos os Serviços |Tentativas |[As tentativas de evitando aplicação destina-se a erros de não repetição?](#subheading15) |
| &nbsp; | Blobs |Objetivos de escalabilidade |[Tem um grande número de clientes aceder em simultâneo num único objeto?](#subheading46) |
| &nbsp; | Blobs |Objetivos de escalabilidade |[A aplicação é permanecendo no destino de escalabilidade da largura de banda ou operações para um blob único?](#subheading16) |
| &nbsp; | Blobs |Copiar os Blobs |[Está a copiar os blobs de forma eficiente?](#subheading17) |
| &nbsp; | Blobs |Copiar os Blobs |[São utilizando o AzCopy para cópias em massa de blobs?](#subheading18) |
| &nbsp; | Blobs |Copiar os Blobs |[Está a utilizar para importar/exportar do Azure para transferir grandes volumes de dados?](#subheading19) |
| &nbsp; | Blobs |Utilizar metadados |[São armazenar metadados utilizados frequentemente sobre blobs nos respetivos metadados?](#subheading20) |
| &nbsp; | Blobs |Carregar rápida |[Ao tentar carregar um blob rapidamente, é-carregar blocos em paralelo?](#subheading21) |
| &nbsp; | Blobs |Carregar rápida |[Ao tentar carregar rapidamente muitas blobs, é-carregar blobs em paralelo?](#subheading22) |
| &nbsp; | Blobs |Tipo de Blob correto |[Está a utilizar blobs de páginas ou blobs de blocos quando for adequado?](#subheading23) |
| &nbsp; | Tabelas |Objetivos de escalabilidade |[Está a aproximar-se os destinos de escalabilidade para as entidades por segundo?](#subheading24) |
| &nbsp; | Tabelas |Configuração |[Está a utilizar JSON para os pedidos de tabela?](#subheading25) |
| &nbsp; | Tabelas |Configuração |[Ter tenha desativado Nagle para melhorar o desempenho de pedidos pequenos?](#subheading26) |
| &nbsp; | Tabelas |As tabelas e partições |[Ter corretamente particionada os dados?](#subheading27) |
| &nbsp; | Tabelas |Partições frequente |[São evitando padrões só de acréscimo e só de preceder?](#subheading28) |
| &nbsp; | Tabelas |Partições frequente |[As inserções/atualizações são distribuídas por várias partições?](#subheading29) |
| &nbsp; | Tabelas |Âmbito de consulta |[Concebido o esquema para permitir consultas de ponto a ser utilizado na maioria dos casos e consultas de tabela a ser utilizadas com moderação?](#subheading30) |
| &nbsp; | Tabelas |Densidade de consulta |[Efetue a análise de normalmente apenas consultas e devolvem linhas que irá utilizar a sua aplicação?](#subheading31) |
| &nbsp; | Tabelas |Dados devolvidos restritiva |[Está a utilizar a filtragem para evitar que devolvam entidades que não são necessários?](#subheading32) |
| &nbsp; | Tabelas |Dados devolvidos restritiva |[Está a utilizar projeção para evitar que as propriedades que não são necessários?](#subheading33) |
| &nbsp; | Tabelas |Denormalization |[Ter desnormalizadas os dados que evitar consultas ineficazes ou vários pedidos de leitura ao tentar obter dados?](#subheading34) |
| &nbsp; | Tabelas |Inserir/atualizar/eliminar |[São a criação de batches de pedidos que precisem de ser transacional ou pode ser feita ao mesmo tempo para reduzir a ida e volta?](#subheading35) |
| &nbsp; | Tabelas |Inserir/atualizar/eliminar |[São evitando obter uma entidade apenas para determinar se deve chamar insert ou update?](#subheading36) |
| &nbsp; | Tabelas |Inserir/atualizar/eliminar |[Ter considerados armazenar série de dados que frequentemente serão obtidos em conjunto uma única entidade como propriedades em vez de várias entidades?](#subheading37) |
| &nbsp; | Tabelas |Inserir/atualizar/eliminar |[Para entidades que sempre serão obtidas em conjunto e podem ser escritas em lotes (por exemplo, dados de séries de tempo), ter é considerado utilizar blobs em vez de tabelas?](#subheading38) |
| &nbsp; | Filas |Objetivos de escalabilidade |[Está a aproximar-se os destinos de escalabilidade da mensagens por segundo?](#subheading39) |
| &nbsp; | Filas |Configuração |[Ter tenha desativado Nagle para melhorar o desempenho de pedidos pequenos?](#subheading40) |
| &nbsp; | Filas |Tamanho da Mensagem |[São sua compacto de mensagens para melhorar o desempenho da fila?](#subheading41) |
| &nbsp; | Filas |Obter em massa |[A obter várias mensagens numa única operação "Get"?](#subheading42) |
| &nbsp; | Filas |Frequência de consulta |[Está a consulta com frequência suficiente para reduzir a latência percetível da sua aplicação?](#subheading43) |
| &nbsp; | Filas |Mensagem de actualização |[Está a utilizar UpdateMessage para armazenar o progresso no processamento de mensagens, evitando a necessidade de Reprocessar a mensagem completa se ocorrer um erro?](#subheading44) |
| &nbsp; | Filas |Arquitetura |[Está a utilizar filas para tornar a sua aplicação toda escalável mais por manter as cargas de trabalho de longa execução fora do caminho crítico e, em seguida, dimensionamento independente?](#subheading45) |

## <a name="allservices"></a>Todos os serviços
Esta secção lista comprovadas práticas aplicáveis a utilização de qualquer um dos serviços de armazenamento do Azure (blobs, tabelas, filas ou ficheiros).  

### <a name="subheading1"></a>Objetivos de escalabilidade
Cada um dos serviços de armazenamento do Azure tem destinos de escalabilidade da capacidade (GB), a taxa de transacção e a largura de banda. Se a sua aplicação atinja ou exceda qualquer um dos objetivos de escalabilidade, pode encontrar transação maiores latências ou limitação. Quando um serviço de armazenamento acelera a sua aplicação, o serviço começa a devolver "503 servidor ocupado" ou "500 tempo limite da operação" códigos de erro para algumas transações de armazenamento. Esta secção descreve a abordagem geral para lidar com os objetivos de escalabilidade e objetivos de escalabilidade da largura de banda em particular. As secções que lidam com os serviços de armazenamento individuais abordam objetivos de escalabilidade no contexto desse serviço específico:  

* [Largura de banda de blob e pedidos por segundo](#subheading16)
* [Entidades da tabela por segundo](#subheading24)
* [Mensagens de fila por segundo](#subheading39)  

#### <a name="sub1bandwidth"></a>Destino de escalabilidade da largura de banda para todos os serviços
No momento da escrita, os destinos de largura de banda nos E.U.A. para uma conta de armazenamento georredundante (GRS) são 10 gigabits por segundo (Gbps) de entrada (dados enviados para a conta de armazenamento) e 20 Gbps de saída (dados enviados a partir da conta de armazenamento). Para uma conta de armazenamento localmente redundante (LRS), os limites são superiores – 20 Gbps de entrada e de 30 Gbps de saída.  Limites de largura de banda internacional pode ser mais baixas e pode ser encontrados no nosso [página de destinos de escalabilidade](http://msdn.microsoft.com/library/azure/dn249410.aspx).  Para obter mais informações sobre as opções de redundância do armazenamento, consulte as ligações na [recursos úteis](#sub1useful) abaixo.  

#### <a name="what-to-do-when-approaching-a-scalability-target"></a>O que fazer quando atingir um destino de escalabilidade
Se a aplicação estiver a atingir os objetivos de escalabilidade para uma única conta de armazenamento, considere a adoção de uma das abordagens seguintes:  

* Reconsider a carga de trabalho que faz com que a aplicação para abordar ou exceder o destino de escalabilidade. Pode estruturar diferente para utilizar menos largura de banda ou capacidade ou menos transações?
* Se uma aplicação pode exceder um dos destinos de escalabilidade, deve criar várias contas de armazenamento e partição os dados da aplicação entre essas várias contas de armazenamento. Se utilizar este padrão, não se esqueça de criar uma aplicação para que possa adicionar mais contas do storage no futuro para balanceamento de carga. No momento da escrita, cada subscrição do Azure pode ter até 100 contas de armazenamento.  As contas de armazenamento também tem sem qualquer custo que não seja a sua utilização em termos de dados armazenados, as transações efetuadas ou dados transferidos.
* Se a aplicação pedidos com êxito os destinos de largura de banda, considere a compressão de dados no cliente para reduzir a largura de banda necessária para enviar dados para o serviço de armazenamento.  Tenha em atenção que isto pode poupar largura de banda e melhorar o desempenho de rede, pode também ter alguns impactos negativos.  Deve avaliar o impacto do desempenho deste devido aos requisitos de processamento adicional para comprimir e descomprimir os dados no cliente. Além disso, o armazenamento de dados comprimidos pode dificultar resolver problemas, uma vez que poderia ser mais difícil ver os dados armazenados através de ferramentas padrão.
* Se a sua aplicação pedidos com êxito os destinos de escalabilidade, em seguida, certifique-se de que está a utilizar um término exponencial para tentativas (consulte [tentativas](#subheading14)).  É melhor certificar-se de nunca abordar os destinos de escalabilidade (utilizando um dos métodos acima), mas isto irá garantir a que sua aplicação não será apenas continuará a tentar rapidamente, tornando a limitação worse.  

#### <a name="useful-resources"></a>Recursos Úteis
As hiperligações seguintes fornecem detalhes adicionais sobre metas de escalabilidade:

* Consulte [metas de desempenho e escalabilidade do Storage do Azure](storage-scalability-targets.md) para obter informações sobre os objetivos de escalabilidade.
* Consulte [replicação de armazenamento do Azure](storage-redundancy.md) e a mensagem de blogue [as opções de redundância do armazenamento do Azure e de armazenamento redundantes do acesso de leitura Georreplicação](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx) para obter informações sobre as opções de redundância do armazenamento.
* Para obter informações atualizadas sobre os preços para os serviços do Azure, consulte [preços do Azure](https://azure.microsoft.com/pricing/overview/).  

### <a name="subheading47"></a>Convenção de nomenclatura de partição
Armazenamento do Azure utiliza um esquema de partições com base no intervalo para balancear a escala e a carga do sistema. A chave de partição é utilizada para a partição de dados para intervalos e estas são com balanceamento de carga entre o sistema. Isto significa que as convenções de nomenclatura como lexical ordenar (por exemplo, msftpayroll msftperformance, msftemployees, etc.) ou utilizando os carimbos tempo (log20160101 log20160102, log20160102, etc.) serão cada as partições potencialmente a ser localizadas conjuntamente no mesmo servidor de partição, até que uma operação de balanceamento de carga divide-los em intervalos menores. Por exemplo, todos os blobs num contentor podem ser servidos por um único servidor até que a carga sobre estes blobs requer mais reequilíbrio dos intervalos de partição. Da mesma forma, um grupo de contas ligeiramente carregados com os respetivos nomes dispostas numa ordem lexical pode ser servido por um único servidor até que a carga de uma ou todas estas contas requerem-los para serem divididas em vários servidores de partições. Cada operação de balanceamento de carga pode ter impacto na latência de chamadas de armazenamento durante a operação. A capacidade do sistema para processar uma rajada repentino de tráfego para uma partição é limitada pela escalabilidade do servidor de uma única partição até a operação de balanceamento de carga no kicks e efetua novamente o balanceamento de intervalo de chaves de partição.  

Pode seguir algumas melhores práticas para reduzir a frequência das operações.  

* Examine a Convenção de nomenclatura que utilizar para as contas, contentores, blobs, tabelas e filas, rigorosamente. Considere lhe o prefixo nomes de conta com um hash de 3 dígitos a utilizar uma função de hash que melhor se adapta às suas necessidades.  
* Se organizar os seus dados através de carimbos ou identificadores numéricos, tem de garantir que não está a utilizar um padrões de tráfego de só de acréscimo (ou apenas de preceder). Estes padrões não são adequados para um intervalo de-com base em sistema de criação de partições e foi antecedência para todo o tráfego Ir para uma única partição e limitar o sistema de forma eficaz balanceamento de carga. Por exemplo, se tiver operações diárias que utilizam um objeto de blob com um carimbo como AAAAMMDD, todo o tráfego para essa operação diária é direcionado para um único objeto que é fornecido por um servidor de partições únicas. Se observar a por limites de blob por partição limites de satisfazer as suas necessidades e considere danificando a esta operação em blobs vários se for necessário. Da mesma forma, se armazenar dados de séries de tempo nas suas tabelas, tudo o tráfego pode ser direcionado para a última parte do espaço de nomes de chave. Se tiver de utilizar carimbos ou os IDs de numéricos, o prefixo de id com um hash de 3 dígitos ou no caso de carimbos prefixo a parte de segundos de tempo, tais como ssyyyymmdd. Se a lista e consultar operações são efetuadas regularmente, escolha uma função de hash irá limitar o número de consultas. Noutros casos, um prefixo aleatório pode ser suficiente.  
* Para obter informações adicionais sobre o esquema de partições utilizado no Storage do Azure, leia o documento SOSP [aqui](http://sigops.org/sosp/sosp11/current/2011-Cascais/printable/11-calder.pdf).

### <a name="networking"></a>Redes
Enquanto a API chama independentemente, muitas vezes, as restrições de rede físico da aplicação tem um impacto significativo no desempenho. Os seguintes descrevem algumas das limitações que os utilizadores podem encontrar.  

#### <a name="client-network-capability"></a>Capacidade de rede do cliente
##### <a name="subheading2"></a>Débito
Largura de banda, o problema é, muitas vezes, as capacidades do cliente. Por exemplo, enquanto uma única conta de armazenamento pode processar igual ou mais de entrada a 10 Gbps (consulte [metas de escalabilidade da largura de banda](#sub1bandwidth)), a velocidade de rede numa instância de função de trabalho do Azure "Pequeno" só é capaz de aproximadamente 100 Mbps. Maior instâncias do Azure têm NICs com maior capacidade, pelo que deve considerar a utilização de uma instância maior ou mais VM se precisar de limites de rede superiores de um único computador. Se está a aceder ao serviço de armazenamento de uma aplicação no local no, em seguida, a mesma regra aplica-se: compreender as capacidades de rede do dispositivo cliente e a conectividade de rede para a localização de armazenamento do Azure e a melhorar-os conforme necessário ou estruturar o aplicação funcione dentro as respetivas capacidades.  

##### <a name="subheading3"></a>Qualidade de ligação
À semelhança de qualquer utilização de rede, lembre-se de que condições de rede, resultando em perda de pacotes e erros mais lento débito Efetivo.  Utilizar o WireShark ou NetMon pode ajudar a diagnosticar o problema.  

##### <a name="useful-resources"></a>Recursos Úteis
Para mais informações sobre os tamanhos de máquina virtual e largura de banda alocada, consulte [tamanhos de Windows VM](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) ou [tamanhos de VM com Linux](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  

#### <a name="subheading4"></a>Localização
Em qualquer ambiente distribuído, colocar o cliente próximo do servidor fornece o melhor desempenho. Para aceder ao armazenamento do Azure com a latência mais baixa, a melhor localização para o cliente está na mesma região do Azure. Por exemplo, se tiver um Web Site do Azure que utiliza o armazenamento do Azure, deve localize-los ambos numa única região (por exemplo, EUA Oeste ou Sudeste asiático). Isto reduz a latência e o custo — no momento da escrita, a utilização de largura de banda numa única região é gratuita.  

Se o cliente aplicações não alojadas no Azure (tais como aplicações de dispositivos móveis ou nos serviços de empresa no local), em seguida, novamente colocando a conta de armazenamento numa região próximo os dispositivos que irão aceder aos mesmos, será geralmente reduzir a latência. Se os clientes são distribuídos amplamente (por exemplo, alguns na América do Norte e algumas na Europa), em seguida, deve considerar a utilização de várias contas de armazenamento: uma localizado na região Norte American e um numa região Europa. Isto irá ajudar a reduzir a latência para os utilizadores em ambas as regiões. Esta abordagem é normalmente mais fácil a implementar se os dados a aplicação armazena é específico para utilizadores individuais e não necessita de replicar dados entre contas de armazenamento.  Abrangente para distribuição de conteúdos, recomenda-se uma CDN – consulte a secção seguinte para obter mais detalhes.  

### <a name="subheading5"></a>Distribuição de conteúdo
Por vezes, uma aplicação tem de fornecer os mesmos conteúdos a vários utilizadores (por exemplo, um produto vídeo de demonstração utilizado na home page de um Web site), localizado na mesma ou várias regiões. Neste cenário, deve utilizar uma rede de entrega de conteúdos (CDN), tais como o CDN do Azure e a CDN utilizaria o armazenamento do Azure como a origem dos dados. Ao contrário de uma conta de armazenamento do Azure que existe numa única região e que não é possível entregar o conteúdo com baixa latência a outras regiões, a CDN do Azure utiliza servidores em vários centros de dados em todo o mundo. Além disso, uma CDN, normalmente, pode suportar limites de saída muito superiores de uma única conta de armazenamento.  

Para obter mais informações sobre a CDN do Azure, consulte [CDN do Azure](https://azure.microsoft.com/services/cdn/).  

### <a name="subheading6"></a>Utilizando a SAS e o CORS
Quando precisar de autorizar código como JavaScript no browser da web de um utilizador ou uma aplicação de telemóvel para aceder a dados no armazenamento do Azure, uma abordagem é utilizar uma aplicação na função da web como um proxy: autentica o dispositivo do utilizador com a função da web , que por sua vez se autentica com o serviço de armazenamento. Desta forma, pode evitar expor as chaves de conta de armazenamento em dispositivos inseguras. No entanto, isto coloca uma grande sobrecarga na função da web porque tem de passar em todos os dados transferidos entre o dispositivo do utilizador e o serviço de armazenamento através da função da web. Pode evitar utilizar uma função da web como um proxy para o serviço de armazenamento através de acesso partilhado assinaturas (SAS), por vezes, em conjunto com cabeçalhos de partilha de recursos de várias origens (CORS). Através da SAS, pode permitir que o dispositivo do utilizador fazer pedidos diretamente a um serviço de armazenamento através de um token de acesso limitado. Por exemplo, se um utilizador pretende carregar uma fotografia à sua aplicação, a função da web pode gerar e enviar para o dispositivo do utilizador, um token SAS, que concede permissão para escrever um blob específico ou um contentor para os seguintes 30 minutos (após o qual o token SAS expira).

Normalmente, um browser não irá permitir JavaScript numa página alojada por um Web site de um domínio para efetuar operações específicas, tais como um "Colocar" para outro domínio. Por exemplo, se uma função da web em "contosomarketing.cloudapp.net" do anfitrião e pretende utilizar JavaScript do lado de cliente para carregar um blob para a sua conta de armazenamento em do "contosoproducts.blob.core.windows.net," o browser "mesma política de origem" será forbid esta operação. CORS é uma funcionalidade de browser que permite que o domínio de destino (neste caso, a conta de armazenamento) para comunicar com o browser que confia pedidos com origem no domínio de origem (neste caso, a função da web).  

Ambas estas tecnologias podem ajudar a evitar carga desnecessária (e congestionamentos) na sua aplicação web.  

#### <a name="useful-resources"></a>Recursos Úteis
Para obter mais informações sobre SAS, consulte [assinaturas de acesso partilhado, parte 1: compreender o modelo SAS](../storage-dotnet-shared-access-signature-part-1.md).  

Para obter mais informações sobre a CORS, consulte [suporte de partilha de recursos de várias origens (CORS) para os serviços de armazenamento do Azure](http://msdn.microsoft.com/library/azure/dn535601.aspx).  

### <a name="caching"></a>Colocação em cache
#### <a name="subheading7"></a>Obter dados
Em geral, a obtenção de dados de um serviço, uma vez é melhor do que recuperá-la em duas vezes. Considere o exemplo de uma aplicação web MVC em execução numa função da web que já foi obtido um blob de 50MB do serviço de armazenamento para servirem como conteúdo para um utilizador. A aplicação, em seguida, foi possível obter esse mesmo blob sempre que um utilizador solicita- ou -foi cache-la localmente para o disco e reutilizar a versão em cache para pedidos de utilizador subsequentes. Além disso, sempre que um utilizador solicita dados, a aplicação foi problema obter com um cabeçalho condicional para o tempo de modificação, o que seria evitar a obter o blob de todo, se este não foi modificada. Pode aplicar este mesmo padrão para trabalhar com as entidades da tabela.  

Em alguns casos, pode decidir que a aplicação pode partem do princípio de que o blob permanece válido para um curto período de tempo após a obtenção-lo e durante este período a aplicação têm de verificar se o blob foi modificado.

Configuração, pesquisa e outros dados que são sempre utilizados pela aplicação são excelentes candidatos para a colocação em cache.  

Para obter um exemplo de como obter propriedades de um blob para detetar a data da última modificação através do .NET, consulte [conjunto e obter as propriedades e metadados](../blobs/storage-properties-metadata.md). Para obter mais informações sobre as transferências condicionais, consulte [condicionalmente atualizar uma cópia Local de um Blob](http://msdn.microsoft.com/library/azure/dd179371.aspx).  

#### <a name="subheading8"></a>Carregamento de dados em lotes
Em alguns cenários de aplicação, pode agregar dados localmente e, em seguida, carregá-lo periodicamente num batch em vez de carregamento de cada conjunto de dados imediatamente. Por exemplo, uma aplicação web poderá manter um ficheiro de registo de atividades: a aplicação foi possível carregar os detalhes de cada atividade como ocorre como uma entidade de tabela (o que necessita de muitas operações de armazenamento) ou foi possível guardar os detalhes de atividade para um ficheiro de registo local e, em seguida, periodicamente, carregar todos os detalhes de atividade como um ficheiro delimitado para um blob. Se cada entrada de registo for de 1KB de tamanho, pode carregar milhares numa única transação "Colocar Blob" (pode carregar um blob de até 64MB de tamanho de uma transação única). Obviamente, se o computador local falhas antes do carregamento, potencialmente perderá alguns dados de registo: o programador da aplicação tem de design para a possibilidade de dispositivo cliente ou carregar falhas.  Se os dados de atividade tem de ser transferida para timespans (não apenas única atividade), os blobs são recomendados através de tabelas.

### <a name="net-configuration"></a>Configuração do .NET
Se utilizar o .NET Framework, esta secção lista as várias definições de configuração rápido que pode utilizar para fazer melhorias de desempenho significativas.  Se utilizar outros idiomas, certifique-se semelhante conceitos aplicam-se no idioma que escolheu.  

#### <a name="subheading9"></a>Aumentar o limite de ligação predefinido
No .NET, o seguinte código aumenta o predefinição limite de ligação (que é, normalmente, 2 num ambiente de cliente ou 10 num ambiente de servidor) para 100. Normalmente, deve definir o valor para aproximadamente o número de threads utilizados pela sua aplicação.  

```csharp
ServicePointManager.DefaultConnectionLimit = 100; //(Or More)  
```

Tem de definir o limite de ligação antes de abrir todas as ligações.  

Para outras linguagens de programação, consulte a documentação de nesse idioma para determinar como definir o limite de ligação.  

Para obter mais informações, consulte a mensagem de blogue [serviços Web: ligações simultâneas](http://blogs.msdn.com/b/darrenj/archive/2005/03/07/386655.aspx).  

#### <a name="subheading10"></a>Aumentar ThreadPool Min Threads, se utilizar o código síncrono com Async tarefas
Este código irá aumentar os threads de mín. do conjunto de threads:  

```csharp
ThreadPool.SetMinThreads(100,100); //(Determine the right number for your application)  
```

Para obter mais informações, consulte [ThreadPool.SetMinThreads método](http://msdn.microsoft.com/library/system.threading.threadpool.setminthreads%28v=vs.110%29.aspx).  

#### <a name="subheading11"></a>Tirar partido da recolha de lixo do .NET 4.5
Utilize o .NET 4.5 ou posterior para a aplicação de cliente para tirar partido das melhorias de desempenho na recolha de lixo do servidor.

Para obter mais informações, consulte o artigo [uma descrição geral dos melhoramentos de desempenho no .NET 4.5](http://msdn.microsoft.com/magazine/hh882452.aspx).  

### <a name="subheading12"></a>Paralelismo unbounded
Enquanto o paralelismo pode ser para um desempenho ótimo, tenha cuidado sobre como utilizar o paralelismo unbounded (sem limite no número de threads e/ou pedidos paralelos) para carregar ou transferir dados, utilizando vários workers para aceder a várias partições (contentores, filas, ou as partições de tabela) na mesma conta de armazenamento ou aceder a vários itens na mesma partição. Se o paralelismo unbounded, a aplicação pode exceder as capacidades do dispositivo de cliente ou escalabilidade da conta de armazenamento destina-se resultando em períodos de latência superiores e limitação.  

### <a name="subheading13"></a>Bibliotecas de cliente de armazenamento e ferramentas
Utilize sempre o mais recente fornecidas bibliotecas de cliente e as ferramentas da Microsoft. No momento da escrita, existem bibliotecas de cliente disponíveis para .NET, Windows Phone, Windows e tempo de execução, Java, C++, bem como bibliotecas de pré-visualização para outros idiomas. Além disso, a Microsoft lançou comandos da CLI do Azure para trabalhar com o Storage do Azure e cmdlets do PowerShell. Microsoft ativamente desenvolvidas pela organização estas ferramentas com o desempenho em mente, mantém-los atualizados com as versões mais recentes do serviço e assegura que processam muitas das práticas de desempenho comprovado internamente.  

### <a name="retries"></a>Tentativas
#### <a name="subheading14"></a>Limitação/ServerBusy
Em alguns casos, o serviço de armazenamento pode limitar a aplicação ou pode ser simplesmente não é possível servir o pedido devido a alguma condição transitória e devolver uma mensagem de "503 servidor ocupado" ou "tempo limite 500".  Isto pode acontecer se a aplicação está a aproximar-se qualquer um dos objetivos de escalabilidade, ou se o sistema é reequilíbrio os dados particionados para permitir um maior débito.  A aplicação de cliente, normalmente, deverá repetir a operação que faz com que este tipo de erro: a tentar mais tarde do mesmo pedido terá êxito. No entanto, se o serviço de armazenamento é a limitação da aplicação porque está a exceder os objetivos de escalabilidade, ou mesmo que o serviço não foi possível servir o pedido por algum motivo, as repetições agressiva se normalmente worse o problema. Por este motivo, deve utilizar um exponencial novamente desativar (a predefinição de bibliotecas de cliente para este comportamento). Por exemplo, a aplicação pode repetir após 2 segundos, em seguida, 4 segundos, em seguida, 10 segundos, em seguida, 30 segundos e, em seguida, dê a cópia de segurança completamente. Este comportamento resulta na sua aplicação significativamente reduzir a carga no serviço em vez de exacerbating quaisquer problemas.  

Tenha em atenção que os erros de conectividade podem ser repetidos de imediato, pois não são o resultado da limitação e devem ser transitórios.  

#### <a name="subheading15"></a>Erros de não repetição
As bibliotecas de cliente estão cientes que erros são capazes de repetição e que não são. No entanto, se estiver a escrever o seu próprio código contra o armazenamento de REST API, não existem alguns erros que não deve repetir: por exemplo, um 400 (pedido incorreto) resposta indica que a aplicação cliente enviou um pedido que não foi possível processar porque não era um formato esperado. Reenviar este pedido resultará a resposta mesma sempre, pelo que não existe nenhum ponto no repeti-lo. Se estiver a escrever o seu próprio código contra o REST API de armazenamento, tenha em atenção o que significa que os códigos de erro e a forma adequada para repetir (ou não) para cada um deles.  

#### <a name="useful-resources"></a>Recursos Úteis
Para mais informações sobre códigos de erro de armazenamento, consulte [estado e códigos de erro](http://msdn.microsoft.com/library/azure/dd179382.aspx) no web site do Microsoft Azure.  

## <a name="blobs"></a>Blobs
Para além das práticas comprovadas para [todos os serviços](#allservices) descrito anteriormente, o seguinte comprovada práticas aplicam-se especificamente ao serviço blob.  

### <a name="blob-specific-scalability-targets"></a>Objetivos de escalabilidade de blob específico
#### <a name="subheading46"></a>Vários clientes aceder em simultâneo num único objeto
Se tiver um grande número de clientes aceder em simultâneo num único objeto terá de considerar por objeto e o armazenamento objetivos de escalabilidade da conta. O número exato de clientes que possam aceder a um único objeto irá variar dependendo de fatores, tais como o número de clientes que solicitam o objeto em simultâneo, o tamanho do objeto, condições etc de rede.

Se o objeto pode ser distribuído através de uma CDN, como imagens ou vídeos servido a partir de um Web site, em seguida, deve utilizar uma CDN. Consulte [aqui](#subheading5).

Outros cenários tais como simulações científicos onde os dados são confidenciais tem duas opções. O primeiro é escalonar acesso da sua carga de trabalho de forma a que o objeto é acedido durante um período de vs tempo a ser acedido em simultâneo. Em alternativa, pode copiar temporariamente o objeto para várias contas de armazenamento, aumentando o IOPS total por objeto e em contas de armazenamento. Nos testes limitado encontrámos que cerca 25 VMs em simultâneo foi possível transferir um blob de 100GB em paralelo (cada VM foi parallelizing a transferência com 32 threads). Se tiver 100 clientes que necessitam aceder ao objeto, primeiro copiá-lo para uma segunda conta de armazenamento e, em seguida, as primeiro 50 VMs aceder primeiro blob e o acesso de VMs segundo 50 blob segundo. Os resultados variam consoante o comportamento de aplicações, de modo deverá testar isto durante a conceção. 

#### <a name="subheading16"></a>Operações por BLOBs e largura de banda
Pode ler ou escrever um blob único em até um máximo de 60 MB por segundo (isto é aproximadamente 480 Mbps que excede as capacidades de várias redes de lado de cliente (incluindo o NIC físico no dispositivo cliente). Além disso, um blob único suporta até 500 pedidos por segundo. Se tiver vários clientes que têm de ler o mesmo blob e poderá exceder estes limites, deve considerar a utilização de uma CDN para distribuir o blob.  

Para obter mais informações sobre o débito de destino para blobs, consulte [metas de desempenho e escalabilidade do Storage do Azure](storage-scalability-targets.md).  

### <a name="copying-and-moving-blobs"></a>Copiar e mover Blobs
#### <a name="subheading17"></a>Copiar Blob
O armazenamento de API de REST versão 2012-02-12 introduzida a capacidade útil para copiar os blobs em contas: uma aplicação cliente pode instruir o serviço de armazenamento para copiar um blob de outra origem (possivelmente por uma conta de armazenamento diferente) e, em seguida, permita que executar o serviço a cópia de forma assíncrona. Isto pode reduzir significativamente a largura de banda necessária para a aplicação quando estiver a migrar dados de outras contas de armazenamento porque não é necessário transferir e carregar os dados.  

A primeira consideração, no entanto, é que, quando copiar entre contas de armazenamento, não há nenhuma garantia de tempo no quando a cópia será concluído. Se a aplicação tem de concluir uma cópia de BLOBs rapidamente sob o seu controlo, poderá ser melhor copiar o blob, transferindo-à uma VM e, em seguida, carregá-lo para o destino.  Para obter previsão completa nesta situação, certifique-se de que é efetuada a cópia por uma VM em execução na mesma região do Azure; caso contrário, as condições de rede poderá (e provavelmente serão) afetar o desempenho de cópia.  Além disso, pode monitorizar o progresso de uma cópia assíncrona através de programação.  

Tenha em atenção que cópias dentro da mesma conta de armazenamento, o próprio são geralmente concluídas rapidamente.  

Para obter mais informações, consulte [copiar Blob](http://msdn.microsoft.com/library/azure/dd894037.aspx).  

#### <a name="subheading18"></a>Utilizar o AzCopy
A equipa de armazenamento do Azure foi lançada uma ferramenta da linha de comandos "AzCopy" que é criada para solucionar transferir blobs muitas para, de e, em contas de armazenamento de massa.  Esta ferramenta está otimizada para este cenário e pode alcançar a velocidades máximas de transferência elevada.  A sua utilização está encouraged para carregamento em massa, transfira e cenários de cópia. Para obter mais informações sobre o assunto e transferi-lo, consulte [transferir dados com o utilitário de linha de comandos do AzCopy](storage-use-azcopy.md).  

#### <a name="subheading19"></a>Serviço de importação/exportação do Azure
Para grandes volumes de dados (mais de 1TB), o Storage do Azure oferece o serviço de importação/exportação, o que permite carregar e transferir a partir do blob storage pelo envio de unidades de disco rígido.  Pode colocar os dados num disco rígido e envia-as à Microsoft para carregamento ou enviar um disco rígido em branco para a Microsoft para transferir dados.  Para obter mais informações, consulte [utilizar o serviço de importação/exportação do Microsoft Azure para transferir dados para o Blob Storage](../storage-import-export-service.md).  Isto pode ser muito mais eficiente do que o carregamento/transferir este volume de dados através da rede.  

### <a name="subheading20"></a>Utilizar metadados
O serviço blob suporta pedidos head, que podem incluir metadados sobre o blob. Por exemplo, se a aplicação necessária os dados EXIF fora de uma fotografia, este obter a fotografia e extraia. Para poupar largura de banda e melhorar o desempenho, a aplicação foi possível armazenar os dados EXIF nos metadados do blob quando a aplicação carregados de fotografia: pode, em seguida, obter os dados EXIF nos metadados utilizando apenas um cabeçalho de pedido, poupando largura de banda significativa e o tempo de processamento necessário para extrair os dados EXIF sempre que o blob é de leitura. Isto é útil em cenários em que apenas terá dos metadados e não o conteúdo completo de um blob.  Tenha em atenção que apenas de 8 KB de metadados podem ser armazenado por blob (o serviço não aceita um pedido para o arquivo superior), pelo que o se os dados não cabem esse tamanho, não poderá utilizar esta abordagem.  

Para obter um exemplo de como obter os metadados de um blob através do .NET, consulte [conjunto e obter as propriedades e metadados](../blobs/storage-properties-metadata.md).  

### <a name="uploading-fast"></a>Carregar rápida
Para carregar os blobs rápida, é a primeira pergunta para responder: tem a carregar um blob ou muitas?  Utilize o abaixo orientações para determinar o método correto a utilizar dependendo do seu cenário.  

#### <a name="subheading21"></a>Carregar um blob grande rapidamente
Para carregar um único blob grande rapidamente, a aplicação cliente deverá carregar os respetivos blocos ou páginas em paralelo (que está a ser mindful dos destinos de escalabilidade para os blobs individuais e a conta de armazenamento como um todo).  Tenha em atenção que as bibliotecas oficiais da Microsoft fornecidos pelos clientes de armazenamento RTM (.NET, Java) têm a capacidade para efetuar este procedimento.  Para cada uma das bibliotecas, utilize o abaixo especificada/propriedade do objeto para definir o nível de concorrência:  

* .NET: Conjunto ParallelOperationThreadCount num objeto BlobRequestOptions a ser utilizado.
* Java/Android: Utilizar BlobRequestOptions.setConcurrentRequestCount()
* NODE.js: Utilize parallelOperationThreadCount nas opções de pedido ou o serviço blob.
* C++: Utilize o método de blob_request_options::set_parallelism_factor.

#### <a name="subheading22"></a>Carregar rapidamente muitas blobs
Para carregar blobs muitos rapidamente, carregue blobs em paralelo. Este é mais rápida do que o carregamento blobs único simultaneamente com bloco parallel carregamentos porque esta propaga o carregamento em várias partições do serviço de armazenamento. Um blob único só suporta um débito de 60 MB por segundo (Mbps aproximadamente 480). No momento da escrita, uma conta com base em E.U.A. LRS suporta até 20 entrada Gbps que é muito mais do que o débito suportado por um blob individuais.  [AzCopy](#subheading18) efetua carregamentos em paralelo, por predefinição e é recomendado para este cenário.  

### <a name="subheading23"></a>Escolher o tipo correto de blob
Storage do Azure suporta dois tipos de BLOBs: *página* blobs e *bloco* blobs. Para um cenário de utilização de determinada à sua escolha do tipo de blob irá afetar o desempenho e escalabilidade da sua solução. Os blobs de blocos são adequados quando pretender carregar grandes quantidades de dados de forma eficiente: por exemplo, uma aplicação cliente poderá ter de carregar fotografias ou as vídeo para o blob storage. Os blobs de páginas são adequados se a aplicação precisa de realizar gravações aleatórias nos dados: por exemplo, os VHDs do Azure são armazenados como blobs de páginas.  

Para obter mais informações, consulte [compreender os Blobs de blocos, os Blobs de acréscimo e Blobs de páginas](http://msdn.microsoft.com/library/azure/ee691964.aspx).  

## <a name="tables"></a>Tabelas
Para além das práticas comprovadas para [todos os serviços](#allservices) descrito anteriormente, o seguinte comprovada práticas aplicam-se especificamente para o serviço de tabela.  

### <a name="subheading24"></a>Objetivos de escalabilidade da tabela específico
Para além das limitações de largura de banda de uma conta de armazenamento completa, tabelas de ter o limite de escalabilidade específico seguinte.  Tenha em atenção que o sistema será o balanceamento de carga à medida que aumenta o tráfego, mas se o tráfego tiver bursts repentino, poderá não conseguir obter este volume de débito imediatamente.  Se o seu padrão de tiver bursts, deverá ver a limitação de e/ou tempos limite durante a rajadas como armazenamento de serviço automaticamente saldos de carregamento terminar a tabela.  Ramping lentamente, geralmente, a cópia de segurança tem melhores resultados como proporciona a hora do sistema para o balanceamento de carga adequadamente.  

#### <a name="entities-per-second-account"></a>Entidades por segundo (conta)
O limite de escalabilidade para aceder a tabelas é até 20.000 entidades (de 1KB cada) por segundo para uma conta.  Em geral, cada entidade que é inserida, atualizar, eliminado ou analisados contagens para este destino.  Por isso, uma inserção de lote que contém 100 entidades contabilizaria como 100 entidades.  Uma consulta que verifica a existência de entidades de 1000 devolve 5 contabilizaria como entidades de 1000.  

#### <a name="entities-per-second-partition"></a>Entidades por segundo (partição)
Dentro de uma única partição, o destino de escalabilidade para aceder a tabelas é 2.000 entidades (de 1KB cada) por segundo, utilizando o mesmo contando conforme descrito na secção anterior.  

### <a name="configuration"></a>Configuração
Esta secção apresenta várias definições de configuração rápido que pode utilizar para fazer melhorias de desempenho significativas no serviço tabela:  

#### <a name="subheading25"></a>Utilizar JSON
A partir da versão de serviço de armazenamento 2013-08-15, o serviço de tabela suporta utilizando JSON em vez do formato de AtomPub baseado em XML para a transferência de dados de tabela. Isto pode reduzir o tamanho do payload, quanto 75% e pode significativamente melhorar o desempenho da sua aplicação.

Para obter mais informações, consulte o post [tabelas do Microsoft Azure: introdução ao JSON](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/05/windows-azure-tables-introducing-json.aspx) e [formato de Payload para operações de serviço tabela](http://msdn.microsoft.com/library/azure/dn535600.aspx).

#### <a name="subheading26"></a>Nagle desativado
Algoritmo da Nagle é amplamente implementado em redes TCP/IP como um meio para melhorar o desempenho de rede. No entanto, não é ideal em todas as circunstâncias (por exemplo, ambientes de elevada disponibilidade interativas). Para o armazenamento do Azure, o algoritmo do Nagle tem um impacto negativo no desempenho de pedidos para os serviços tabela e fila e deve desativá-lo se for possível.  

Para obter mais informações, consulte o nosso artigo de blogue [algoritmo do Nagle não é amigável para pedidos de pequenas](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/06/25/nagle-s-algorithm-is-not-friendly-towards-small-requests.aspx), que explica por que motivo o algoritmo do Nagle mal interage com pedidos de tabela e fila e mostra como desativá-lo no seu cliente aplicação.  

### <a name="schema"></a>Esquema
Como representar e os dados de consulta é o fator único maior que afeta o desempenho do serviço tabela. Enquanto todas as aplicações for diferente, esta secção descreve algumas práticas comprovadas gerais que se relacionam com a:  

* Estruturar tabela
* Consultas eficiente
* Atualizações de dados eficiente  

#### <a name="subheading27"></a>As tabelas e partições
As tabelas são divididas em partições. Cada entidade armazenada numa partição partilha a mesma chave de partição e tem uma chave de linha exclusivo para identificá-lo dentro dessa partição. As partições fornecem vantagens, mas também introduzem limites de escalabilidade.  

* Benefícios: Pode atualizar as entidades da mesma partição numa única, atómico, batch transação que contenha até 100 operações de armazenamento separada (limite de tamanho total de 4MB). Pressupondo que o mesmo número de entidades a recuperar, pode também consultar dados dentro de uma única partição forma mais eficiente do que os dados que abrange partições (embora continue a ler para obter mais recomendações sobre consultar os dados de tabela).
* O limite de escalabilidade: acesso para entidades armazenadas numa única partição não pode ser com balanceamento de carga porque partições suportam transações de atomic batch. Por este motivo, o destino de escalabilidade para uma partição de tabela individuais é menor para o serviço de tabela como um todo.  

Devido a estas características de tabelas e partições, deve que adotar os seguintes princípios de design:  

* Dados que a aplicação cliente frequentemente atualizado ou consultada na mesma unidade lógica de trabalho devem estar localizados na mesma partição.  Isto poderá ser porque a aplicação é agregar escritas ou de que pretende tirar partido das operações de atomic batch.  Além disso, os dados de uma única partição podem ser de forma mais eficiente consultados numa única consulta de dados em partições.
* Dados que a aplicação de cliente não inserção/atualização ou a consulta na mesma unidade lógica do trabalho (uma consulta simples ou atualização do batch) têm de estar localizada em partições separadas.  Uma nota importante é que não é não existe nenhum limite para o número de chaves de partição numa tabela individual, para que ter milhões de chaves de partição não é um problema e não irá afetar o desempenho.  Por exemplo, se a aplicação é um Web site popular com início de sessão do utilizador, com o Id de utilizador como a chave de partição pode ser uma boa opção.  

#### <a name="hot-partitions"></a>Partições frequente
Uma partição de acesso frequente é aquele que está a receber uma percentagem desproporcionada de tráfego para uma conta e não pode ser com balanceamento de carga porque se trata de uma única partição.  Em geral, frequente são criadas partições uma de duas formas:  

##### <a name="subheading28"></a>Apenas a acrescentar e preceder padrões apenas
O padrão "Acrescentar apenas" é uma onde todos os (ou quase todos os) o tráfego para um determinado PK aumentam e diminui, de acordo com a hora atual.  Um exemplo é se a aplicação utilizou a data atual como uma chave de partição para dados de registo.  Isto resulta em todas as inserções contínuo para o último partição na tabela e o sistema não é possível o balanceamento de carga porque todas as escritas vai até ao fim da sua tabela.  Se o volume de tráfego para esse partição exceder o destino do nível de partição escalabilidade, em seguida, irá resultar na limitação.  É melhor para se certificar de que o tráfego é enviado para várias partições, para ativar o balanceamento de carga os pedidos entre a tabela.  

##### <a name="subheading29"></a>Dados de tráfego elevado
Se o esquema de partições resulta numa partição única que tenha apenas os dados que são muito mais utilizados que outras partições, poderá ver também a limitação como que partição se aproxima do destino de escalabilidade para uma única partição.  É melhor certificar-se de que o esquema de partição resulta em nenhum única partição atingir os objetivos de escalabilidade.  

#### <a name="querying"></a>Consultar
Esta secção descreve comprovadas práticas para consultar o serviço de tabela.  

##### <a name="subheading30"></a>Âmbito de consulta
Existem várias formas para especificar o intervalo de entidades para consulta.  Segue-se um debate das utilizações de cada.  

Em geral, evite análises (consultas maiores do que uma única entidade), mas se deve analisar, tente organizar os dados para que as análises obtêm os dados que precisar, sem análise ou devolver quantidades significativas de entidades que não precisa.  

###### <a name="point-queries"></a>Ponto de consultas
Uma consulta de ponto obtém exatamente uma entidade. Efetua este procedimento, especificando a chave de partição e a chave de linha da entidade a obter. Estas consultas são muito eficientes e deve utilizar sempre que possível.  

###### <a name="partition-queries"></a>Consultas de partição
Uma consulta de partição é uma consulta que obtém um conjunto de dados que partilha uma chave de partição comuns. Normalmente, a consulta Especifica um intervalo de valores de chave de linha ou um intervalo de valores para algumas propriedades de entidade, além de uma chave de partição. Estas são menos eficientes do que o ponto de consultas e devem ser utilizadas com moderação.  

###### <a name="table-queries"></a>Consultas de tabela
Uma consulta de tabela é uma consulta que obtém um conjunto de entidades que partilham uma chave de partição comuns. Estas consultas não são eficientes e deve evitar se possível.  

##### <a name="subheading31"></a>Densidade de consulta
Outro fator chave na eficiência de consulta é o número de entidades devolvido em comparação com o número de entidades analisado para encontrar o conjunto devolvido. Se a aplicação executa uma consulta de tabela com um filtro para um valor de propriedade que apenas 1% das partilhas de dados, a consulta irá analisar 100 entidades para cada uma entidade devolve. Os destinos de escalabilidade da tabela abordados anteriormente todos os relacionados com o número de entidades analisados e não é o número de entidades devolvido: uma densidade de consulta baixa facilmente pode fazer com que o serviço de tabela limitar a aplicação porque tem analisar muitas entidades para obter a entidade que procura.  Consulte a secção abaixo no [denormalization](#subheading34) para obter mais informações sobre como evitar esta situação.  

##### <a name="limiting-the-amount-of-data-returned"></a>Limitar a quantidade de dados devolvidos
###### <a name="subheading32"></a>Filtragem
Quando souber que uma consulta devolverá entidades que não precisa da aplicação de cliente, considere utilizar um filtro para reduzir o tamanho do conjunto de devolvido. Enquanto as entidades não devolvidas para o cliente ainda contam para os limites de escalabilidade, irá melhorar o desempenho da aplicação devido ao tamanho do payload reduzida de rede e o número reduzido de entidades que a aplicação cliente tem de processar.  Consulte acima nota no [densidade de consulta](#subheading31), no entanto – os destinos de escalabilidade referem-se ao número de entidades analisados, para uma consulta, que filtra muitas entidades ainda poderá resultar em limitação, mesmo que algumas entidades são devolvidas.  

###### <a name="subheading33"></a>Projeção
Se a sua aplicação de cliente tiver apenas um conjunto limitado de propriedades de entidades na tabela, pode utilizar a projeção para limitar o tamanho do conjunto de dados devolvido. Tal como acontece com a filtragem, isto ajuda a reduzir a carga de rede e de processamento de cliente.  

##### <a name="subheading34"></a>Denormalization
Ao contrário de trabalhar com bases de dados relacionais, as práticas comprovadas de forma eficiente consultar os dados de tabela levar para denormalizing os seus dados. Duplicar ou seja, os mesmos dados em várias entidades (um para cada chave pode utilizar para localizar os dados) para minimizar o número de entidades que uma consulta deve analisar para localizar os dados de cliente precisa de, em vez de ter que analisar grandes quantidades de entidades para localizar os dados da aplicação tem de lication.  Por exemplo, num Web site comércio eletrónico, poderá encontrar uma ordem tanto por ID de cliente (Atribuir-me as ordens este cliente) e a data (Atribuir-me ordens numa data).  No Table Storage, é melhor armazenar a entidade (ou uma referência ao mesmo) duas vezes – uma vez com o nome de tabela, PK e RK para facilitar a localizar pelo cliente ID, uma vez para facilitar a encontrá-las na data.  

#### <a name="insertupdatedelete"></a>Inserir/atualizar/eliminar
Esta secção descreve comprovadas práticas para modificar entidades armazenadas no serviço tabela.  

##### <a name="subheading35"></a>Criação de batches
Transações de batches são conhecidas como transações de grupo de entidade (ETG) no Storage do Azure; todas as operações de dentro de um ETG tem de estar numa única partição numa tabela individual. Sempre que possível, utilize ETGs para efetuar as introduções, atualizações e eliminações em lotes. Isto reduz o número de ida e volta da sua aplicação de cliente para o servidor, reduz o número de transações facturável (um ETG contagens como uma transação única para fins de faturação e pode conter até 100 operações de armazenamento) e permite atualizações atómicas (all operações ou todos os não bem-sucedidos dentro de um ETG). Ambientes com altas latências, tais como dispositivos móveis irão beneficiar significativamente ETGs a utilizar.  

##### <a name="subheading36"></a>Upsert
Tabela de utilização **Upsert** operações sempre que possível. Existem dois tipos de **Upsert**, que podem ser mais eficiente do que um tradicional **inserir** e **atualização** operações:  

* **InsertOrMerge**: Utilize esta opção quando pretender carregar um subconjunto de propriedades da entidade, mas não tem a certeza se a entidade já existe. Se a entidade existe, esta chamada atualiza as propriedades incluídas no **Upsert** operação, deixando todas as propriedades existentes como estão, se a entidade não existir, insere-lo a entidade de novo. Isto é semelhante ao utilizar projeção numa consulta, em que apenas terá de carregar as propriedades que estão a alterar.
* **InsertOrReplace**: Utilize esta opção quando pretender carregar uma entidade completamente nova, mas não tem a certeza se já existe. Só deve utilizar este quando sabe que a entidade recentemente carregada é inteiramente correta porque completamente substitui a entidade antiga. Por exemplo, pretende atualizar a entidade que armazena a localização do utilizador atual, independentemente se pretende ou não a aplicação tem armazenadas anteriormente os dados de localização do utilizador; a nova entidade de localização está concluída e não é necessário qualquer informação sobre qualquer entidade anterior.

##### <a name="subheading37"></a>Armazenar de dados de séries de uma única entidade
Por vezes, uma aplicação armazena uma série de dados que frequentemente necessita de obter, uma vez: por exemplo, uma aplicação pode controlar a utilização da CPU ao longo do tempo para desenhar um gráfico graduais dos dados das últimas 24 horas. Uma abordagem é ter uma entidade de tabela por hora, com cada entidade que representa uma hora específica e armazenar a utilização da CPU para essa hora. Para desenhar estes dados, a aplicação tem de obter as entidades que contém os dados a partir de 24 horas mais recentes.  

Em alternativa, a aplicação foi possível armazenar a utilização da CPU para cada hora como uma propriedade de diferente de uma entidade única: para atualizar a cada hora, a aplicação pode utilizar um único **InsertOrMerge Upsert** chamada para atualizar o valor para o mais recente hora. Para representar os dados, a aplicação só tem de obter uma única entidade em vez de 24, tornando-se para uma consulta muito eficiente (consulte acima debate sobre [consultar âmbito](#subheading30)).

##### <a name="subheading38"></a>Armazenar dados estruturados em blobs
Dados estruturados, por vezes, funciona como se deve passar nas tabelas, mas os intervalos de entidades sempre são obtidos em conjunto e pode ser inserido batch.  Um bom exemplo desta situação é um ficheiro de registo.  Neste caso, pode vários minutos de registos do batch, insira-as e, em seguida, são sempre obter alguns minutos de registos de cada vez, bem como.  Neste caso, para um desempenho, é melhor utilizar blobs em vez de tabelas, uma vez que pode reduzir significativamente o número de objetos escritas/devolvido, bem como normalmente, o número de pedidos que necessitam de efetuadas.  

## <a name="queues"></a>Filas
Para além das práticas comprovadas para [todos os serviços](#allservices) descrito anteriormente, o seguinte comprovada práticas aplicam-se especificamente ao serviço fila.  

### <a name="subheading39"></a>Limites de escalabilidade
Uma fila única pode processar aproximadamente 2.000 mensagens (de 1KB cada) por segundo (cada número AddMessage, GetMessage e DeleteMessage como uma mensagem aqui). Se isto é insuficiente para a sua aplicação, deve utilizar várias filas e distribuídos as mensagens por-los.  

Ver os objetivos de escalabilidade atual em [metas de desempenho e escalabilidade do Storage do Azure](storage-scalability-targets.md).  

### <a name="subheading40"></a>Nagle desativado
Consulte a secção de configuração de tabela que descreve o algoritmo de Nagle — o algoritmo de Nagle é geralmente incorreto para o desempenho de pedidos de fila, e deve desativá-lo.  

### <a name="subheading41"></a>Tamanho da mensagem
Fila de desempenho e escalabilidade fique à medida que aumenta de tamanho da mensagem. Deve colocar apenas as informações que necessita do recetor numa mensagem.  

### <a name="subheading42"></a>Obtenção do batch
Pode obter até 32 mensagens numa fila numa única operação. Isto pode reduzir o número e voltas da aplicação cliente, que é especialmente útil para ambientes, como dispositivos móveis, com latência elevada.  

### <a name="subheading43"></a>Intervalo de consulta da fila
Consultar a maioria das aplicações para mensagens a partir de uma fila, que pode ser uma das origens de maiores de transações para essa aplicação. Selecione o intervalo de consulta da forma sensata: com demasiada frequência de consulta pode fazer com que a aplicação para abordar os destinos de escalabilidade para a fila. No entanto, ao 200 000 transações para $0.01 (no momento da escrita), um único processador consulta depois a cada segundo durante um mês seria custo inferior a 15 cents custos, por isso, não é normalmente um fator que afeta a sua escolha de intervalo de consulta.  

Para informações de custo atualizado, consulte [preços do Storage do Azure](https://azure.microsoft.com/pricing/details/storage/).  

### <a name="subheading44"></a>UpdateMessage
Pode utilizar **UpdateMessage** para aumentar o tempo limite de invisibilidade ou para atualizar informações de estado de uma mensagem. Embora seja poderosa, lembre-se de que cada **UpdateMessage** operação conta para o destino de escalabilidade. No entanto, isto pode ser uma abordagem que ter um fluxo de trabalho que passa a uma tarefa de uma fila para o seguinte, como cada passo da tarefa é concluído muito mais eficiente. Utilizar o **UpdateMessage** operação permite à aplicação guardar o estado da tarefa para a mensagem e, em seguida, continuar a trabalhar, em vez de colocação voltar a mensagem para o próximo passo da tarefa sempre que um passo é concluído.  

Para obter mais informações, consulte o artigo [como: alterar os conteúdos de uma mensagem em fila](../queues/storage-dotnet-how-to-use-queues.md#change-the-contents-of-a-queued-message).  

### <a name="subheading45"></a>Arquitetura de aplicações
Deve utilizar filas para tornar a arquitetura de aplicações dimensionáveis. Segue-se algumas formas de utilizar filas para tornar a sua aplicação mais escalável:  

* Pode utilizar filas para criar registos de segurança do trabalho para processamento e uniforme saída cargas de trabalho na sua aplicação. Por exemplo, foi fila dos pedidos de utilizadores para efetuar o trabalho intensivas do processador, tais como o redimensionamento de imagens carregadas.
* Pode utilizar as filas de desemparelhar as partes da sua aplicação para que pode dimensionamento independente. Por exemplo, uma web front-end foi colocar os resultados do inquérito dos utilizadores para uma fila para posterior análise e armazenamento. Pode adicionar mais instâncias de função de trabalho para processar os dados de fila conforme necessário.  

## <a name="conclusion"></a>Conclusão
Este artigo discutidos alguns dos mais comuns, comprovada práticas para otimizar o desempenho ao utilizar o armazenamento do Azure. Aconselhamos cada programador de aplicações a avaliar as respetivas aplicações com base nas práticas acima apresentadas e a considerar adotar as recomendações dadas no sentido de obter um melhor desempenho para as aplicações que utilizem o Armazenamento do Azure.