# <a name="compute"></a>Computação
Azure permite-lhe implementar e monitorizar o código da aplicação em execução dentro de um centro de dados da Microsoft. Quando cria uma aplicação e executá-la no Azure, o código e a configuração em conjunto é chamado um Azure serviço alojado. Serviços alojados são fáceis de gerir, aumentar e reduzir verticalmente, reconfigure e atualizar com novas versões do código das aplicações. Este artigo incida no Azure alojado modelo de serviço de aplicações.<a id="compare" name="compare"></a>

## Índice<a id="_GoBack" name="_GoBack"></a>
* [Vantagens do modelo de aplicação do Azure][Azure Application Model Benefits]
* [Conceitos de principais de serviço alojado][Hosted Service Core Concepts]
* [Considerações de Design do serviço alojado][Hosted Service Design Considerations]
* [Conceber a sua aplicação para a escala][Designing your Application for Scale]
* [Definição de serviço alojado e a configuração][Hosted Service Definition and Configuration]
* [O ficheiro de definição de serviço][The Service Definition File]
* [O ficheiro de configuração de serviço][The Service Configuration File]
* [Criar e implementar um serviço alojado][Creating and Deploying a Hosted Service]
* [Referências][References]

## <a id="benefits"></a>Vantagens do modelo de aplicação do azure
Quando implementar a aplicação como um serviço alojado, o Azure cria um ou mais máquinas virtuais (VMs) que contêm código da aplicação e inicia as VMs em máquinas físicas que reside dos centros de dados do Azure. Como os pedidos de cliente para a aplicação alojada introduzir o Centro de dados, um balanceador de carga distribui estes pedidos igualmente para as VMs. Enquanto a aplicação será alojada no Azure, obtém três principais vantagens:

* **Elevada disponibilidade.** Elevada disponibilidade significa que Azure garante que a aplicação está em execução quanto possível e é capaz de responder a pedidos de cliente. Se a aplicação termina (devido a uma exceção não processada, por exemplo), em seguida, Azure detetará esta e irá automaticamente voltar a iniciar a aplicação. Se a máquina a aplicação está em execução no experiências algum tipo de falha de hardware, em seguida, Azure será também detetá-lo e criar uma nova VM noutra máquina física do trabalho e executar automaticamente o código a partir daí. Nota: Por ordem para a sua aplicação obter o contrato de nível de serviço da Microsoft de 99,95% disponível, tem de ter, pelo menos, duas VMs com o código da aplicação. Isto permite que uma VM para processar pedidos de cliente ao Azure move o código de uma VM com falhas para uma VM nova, bom.
* **Escalabilidade.** Azure permite-lhe facilmente e alterar dinamicamente o número de VMs com o código da aplicação para processar a carga real que está a ser colocada na sua aplicação. Isto permite-lhe ajustar a sua aplicação para a carga de trabalho que os seus clientes estão a colocar no mesmo ao pagar apenas para as VMs terá quando que precisar. Quando pretender alterar o número de VMs, Azure responde dentro de minutos, tornando a possível alterar dinamicamente o número de VMs em execução como muitas vezes, conforme o desejado.
* **Capacidade de gestão.** Porque o Azure é uma plataforma como um oferta de serviço (PaaS), gere a infraestrutura (hardware próprio, eletricidade e redes) necessária para manter estas máquinas em execução. Azure também gere a plataforma, assegurando um sistema operativo atualizado com todas as os patches corretos e atualizações de segurança, bem como atualizações de componentes, tais como o .NET Framework e o servidor de informações da Internet. Porque todas as VMs estão a executar o Windows Server 2008, o Azure fornece funcionalidades adicionais, tais como a monitorização de diagnóstico, suporte de ambiente de trabalho remoto, as firewalls e configuração do arquivo de certificados. Todas estas funcionalidades são fornecidas ao não custo extra. Na verdade, quando executar a sua aplicação no Azure, a licença de sistema operativo (SO) do Windows Server 2008 está incluída. Uma vez que todas as VMs estão a executar o Windows Server 2008, qualquer código que é executado no Windows Server 2008 funciona apenas ajustar quando em execução no Azure.

## <a id="concepts"></a>Alojado conceitos principais de serviço
Quando a aplicação é implementada como um serviço alojado no Azure, é executada como um ou mais *funções.* A *função* simplesmente refere-se a configuração e de ficheiros de aplicação. Pode definir uma ou mais funções para a sua aplicação, cada um com o seu próprio conjunto de ficheiros de aplicação e a configuração. Para cada função na sua aplicação, pode especificar o número de VMs, ou *instâncias de função*, para executar. A figura abaixo mostram dois exemplos simples de uma aplicação modelada como um serviço alojado com funções e instâncias de função.

##### <a name="figure-1-a-single-role-with-three-instances-vms-running-in-an-azure-data-center"></a>Figura 1: Uma única função com três instâncias (VMs) em execução no Centro de dados do Azure
![Imagem][0]

##### <a name="figure-2-two-roles-each-with-two-instances-vms-running-in-an-azure-data-center"></a>Figura 2: Duas funções, cada um com duas instâncias (VMs) em execução no Centro de dados do Azure
![Imagem][1]

Instâncias de função normalmente processam pedidos de cliente de Internet introduzir o Centro de dados através de que é chamado um *ponto final de entrada*. Uma única função pode ter mais de entrada ou 0 pontos finais. Cada ponto final indica um protocolo (HTTP, HTTPS ou TCP) e uma porta. É comum para configurar uma função com dois pontos finais de entrada: HTTP a escutar na porta 80 e HTTPS está a escutar na porta 443. A figura abaixo mostra um exemplo de duas funções diferentes com pontos finais de entrada diferentes instruir os pedidos de cliente aos mesmos.

![Imagem][2]

Quando cria um serviço alojado no Azure, é atribuído um endereço IP publicamente endereçável que os clientes podem utilizar para aceder ao mesmo. Após criar o serviço alojado tem de selecionar também um prefixo de URL que está mapeado para esse endereço IP. Este prefixo tem de ser exclusivo é essencialmente reservar o *prefixo*. URL cloudapp.net, pelo que ninguém mais pode ter. Os clientes comunicam com as instâncias de função utilizando o URL. Normalmente, irá não distribuir ou publicar do Azure *prefixo*. cloudapp.net URL. Em vez disso, irá adquirir um nome DNS da sua entidade de registo DNS da preferência e configure o seu nome DNS para redirecionar os pedidos de cliente para o URL do Azure. Para obter mais detalhes, consulte [configurar um nome de domínio personalizado no Azure][Configuring a Custom Domain Name in Azure].

## <a id="considerations"></a>Alojado considerações de Design de serviço
Ao conceber uma aplicação para ser executada num ambiente de nuvem, existem várias considerações de pensar como latência, elevada disponibilidade e escalabilidade.

Decidir onde localizar o código da aplicação é uma consideração importante quando em execução num serviço alojado no Azure. É comum para implementar a aplicação para os centros de dados que são mais próximo para os seus clientes para reduzir a latência e obter o melhor desempenho possível.
No entanto, pode escolher um centro de dados próximo da sua empresa ou mais próximo dos seus dados, se tiver algumas questões legais ou jurisdictional sobre os dados e a respetiva localização. Existem seis centros de dados em torno de todo o mundo capaz de alojar o código da aplicação. A tabela abaixo mostra as localizações disponíveis:

<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">

<tbody>

<tr>

<td style="width: 100px;">
**País/região**

</td>

<td style="width: 200px;">
**Regiões secundárias**

</td>
</tr>

<tr>

<td>
Estados Unidos

</td>

<td>
Centro-Sul & Centro-Norte

</td>
</tr>

<tr>

<td>
Europa

</td>

<td>
Norte & oeste

</td>
</tr>

<tr>

<td>
Ásia

</td>

<td>
Sudeste & leste

</td>
</tr>
</tbody>
</table>
Ao criar um serviço alojado, selecione uma região secundária que indica a localização na qual pretende que o seu código para executar.

Para alcançar a elevada disponibilidade e escalabilidade, é extremamente importante que os dados da sua aplicação seja mantida num repositório central acessível a várias instâncias de função. Para ajudar com esta opção, o Azure oferece várias opções de armazenamento como blobs, tabelas e base de dados SQL. Consulte o [ofertas de armazenamento de dados no Azure] [ Data Storage Offerings in Azure] artigo para obter mais informações sobre estas tecnologias de armazenamento. A figura abaixo mostra como o Balanceador de carga no interior do Centro de dados do Azure distribui os pedidos de cliente para instâncias de função diferentes que têm acesso para o mesmo armazenamento de dados.

![Imagem][3]

Normalmente, pretende localizar o código da aplicação e os dados no mesmo centro de dados como para esta ação permite baixa latência (um melhor desempenho) quando o código da aplicação acede os dados. Além disso, não lhe serem cobrados largura de banda quando são movidos dados em torno dentro do mesmo centro de dados.

## <a id="scale"></a>Conceber a sua aplicação para a escala
Por vezes, poderá querer efetuar uma única aplicação (por exemplo, um web site simples) e o tiver alojado no Azure. Mas frequentemente, a aplicação pode consistir em várias funções que todos os funcionam em conjunto. Por exemplo, a figura abaixo, existem duas instâncias da função de site, três instâncias de função de ordem de processamento e uma instância da função do gerador de relatório. Estas funções estão todos a funcionar em conjunto e o código para todos eles pode ser agrupado e implementado como uma única unidade no Azure.

![Imagem][4]

O motivo principal para dividir uma aplicação em diferentes funções cada a ser executado no seu próprio conjunto de instâncias de função (ou seja, VMs) está a dimensionar as funções de forma independente. Por exemplo, durante a época, muitos clientes podem ser compra produtos da sua empresa, pelo que pode querer aumentar o número de instâncias de função com a sua função de site, bem como o número de instâncias de função a executar a função de ordem de processamento. Após a época, poderá receber uma grande quantidade de produtos devolvido, pelo que poderá ainda necessita de um grande número de instâncias de Web site, mas menos instâncias de ordem de processamento. Durante o restante do ano, só poderá ser necessário instâncias alguns Web site e ordem de processamento. Ao longo tudo isto, poderá ter apenas uma instância do gerador de relatório. A flexibilidade de implementações baseada em funções no Azure permite-lhe adaptar facilmente a sua aplicação para as suas necessidades de negócio.

É comum para a função de instâncias no serviço alojado comunicam entre si. Por exemplo, a função de site aceita a ordem de um cliente, mas, em seguida,-descarrega a ordem de processamento para as instâncias de função ordem de processamento. A melhor forma de passar o formulário de trabalho um conjunto de instâncias de função para outro conjunto de instâncias está a utilizar a tecnologia de colocação fornecida pelo Azure, o serviço fila ou filas do Service Bus. A utilização de uma fila é uma parte crítica aqui o bloco. A fila permite que o serviço alojado dimensionar as respetivas funções de forma independente, permitindo-lhe equilibrar a carga de trabalho em relação custo. Se o número de mensagens na fila aumenta ao longo do tempo, em seguida, pode dimensionar o número de instâncias de função de ordem de processamento. Se o número de mensagens na fila diminui ao longo do tempo, em seguida, pode dimensionar o abaixo o número de instâncias de função de ordem de processamento. Desta forma, apenas são pagar para as instâncias necessárias para processar a carga de trabalho real.

A fila também fornece a fiabilidade. Quando o dimensionamento para baixo o número de ordem de processamento de instâncias de função, o Azure decide que instâncias para terminar. Pode decidir a terminar uma instância que está a processar uma mensagem de fila. No entanto, uma vez que o processamento da mensagem não for concluída com êxito, a mensagem fica visível novamente para outra ordem de processamento da instância de função que escolherá-lo e processa-os. Devido a visibilidade da mensagem de fila, mensagens garantidas, eventualmente, obter processados. A fila também age como um balanceador de carga por distribuição de forma eficaz as mensagens de instâncias de função todos e que as mensagens de pedido a partir do mesmo.

Para as instâncias de função de site, pode monitorizar o tráfego entra-los e optar por aumentar o número deles ou também reduzir vertical. A fila permite-lhe aumentar o número de instâncias de função de site independentemente das instâncias de função da ordem de processamento. Isto é extremamente útil e dá-lhe muita flexibilidade. Obviamente, se a sua aplicação consiste em funções adicionais, pode adicionar filas adicionais como conduit para comunicar entre-los para poder tirar partido do mesmo dimensionar e benefícios de custos.

## <a id="defandcfg"></a>Alojada a definição de serviço e configuração
Implementar um serviço alojado para o Azure necessita também de ter um ficheiro de definição de serviço e um ficheiro de configuração do serviço. Estes ficheiros são ficheiros XML e permitem-lhe forma declarativa especificar opções de implementação para o serviço alojado. O ficheiro de definição de serviço descreve todas as funções que compõem o serviço alojado e como comunicam. O ficheiro de configuração do serviço descreve o número de instâncias para cada função e as definições utilizadas para configurar cada instância de função.

## <a id="def"></a>o ficheiro de definição de serviço
Como posso mencionado anteriormente, a definição de serviço (. CSDEF) o ficheiro é um ficheiro XML que descreve as várias funções que compõem a sua aplicação completa. O esquema de completado para o ficheiro XML que pode ser encontrado aqui: [http://msdn.microsoft.com/library/windowsazure/ee758711.aspx] [-].
O ficheiro CSDEF contém um elemento de WebRole ou WorkerRole para cada função que pretende na sua aplicação. Implementar uma função como uma função da web (utilizando o elemento de WebRole) significa que o código será executada numa instância de função com o Windows Server 2008 e o servidor de informação Internet (IIS).
Implementar uma função, como uma função de trabalho (utilizando o elemento de WorkerRole) significa que a instância de função serão a ter o Windows Server 2008 no mesmo (não será instalada IIS).

Pode criar e implementar uma função de trabalho utiliza algumas outro mecanismo para escutar os pedidos web recebidos certamente (por exemplo, o código foi possível criar e utilizar um HttpListener .NET). Uma vez que todas as instâncias de função são o Windows Server 2008, o seu código pode executa quaisquer operações que estão normalmente disponíveis para uma aplicação em execução no Windows Server
2008.

Para cada função, indicar que o tamanho da VM pretendido que devem utilizar instâncias dessa função. A tabela abaixo mostra os vários tamanhos VM atualmente disponíveis e os atributos de cada:

<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">

<tbody>

<tr align="left" valign="top">

<td>
**Tamanho da VM**

</td>

<td>
**CPU**

</td>

<td>
**RAM**

</td>

<td>
**Disco**

</td>

<td>
**Pico de rede e/s**

</td>
</tr>

<tr align="left" valign="top">

<td>
**Muito pequeno**

</td>

<td>
1 x 1.0 GHz

</td>

<td>
768 MB

</td>

<td>
20GB

</td>

<td>
\~5 Mbps

</td>
</tr>

<tr align="left" valign="top">

<td>
**Pequeno**

</td>

<td>
1 x 1.6 GHz

</td>

<td>
1,75 GB

</td>

<td>
225GB

</td>

<td>
\~100 Mbps

</td>
</tr>

<tr align="left" valign="top">

<td>
**Média**

</td>

<td>
2 x 1.6 GHz

</td>

<td>
3,5 GB

</td>

<td>
490GB

</td>

<td>
\~200 Mbps

</td>
</tr>

<tr align="left" valign="top">

<td>
**Grande**

</td>

<td>
4 x 1.6 GHz

</td>

<td>
7 GB

</td>

<td>
1TB

</td>

<td>
\~400 Mbps

</td>
</tr>

<tr align="left" valign="top">

<td>
**Muito grande**

</td>

<td>
8 x 1.6 GHz

</td>

<td>
14 GB

</td>

<td>
2TB

</td>

<td>
\~800 Mbps

</td>
</tr>
</tbody>
</table>
São-lhe cobrados hora a hora para cada VM utiliza como uma instância de função e lhe é cobrados também para todos os dados que as instâncias da função Enviar fora do Datacenter. Não lhe serem cobrados para introduzir o Centro de dados de dados. Para obter mais informações, consulte [preços do Azure] [preços do Azure]. Em geral, é recomendado utilizar várias instâncias de função pequeno, por oposição a algumas instâncias grande, para que a aplicação é mais resistente a falhas. Após todos os menos instâncias de função tiver, é mais desastroso uma falha dos mesmos global da sua aplicação. Além disso, tal como mencionado anteriormente, tem de implementar, pelo menos, duas instâncias para cada função para obter o contrato de nível de serviço de 99,95% Microsoft fornece.

O ficheiro de definição (. CSDEF) do serviço é também onde tem de especificar demasiados atributos sobre cada função na sua aplicação. Seguem-se alguns dos itens mais úteis disponíveis:

* **Certificados**. Utilize certificados de encriptação de dados ou se o seu serviço web suporta SSL. Todos os certificados têm de ser carregado para o Azure. Para obter mais informações, consulte [gestão de certificados no Azure][Managing Certificates in Azure]. Esta definição de XML instala certificados carregada anteriormente para o arquivo de certificados a instância de função para que fiquem utilizáveis pelo código da aplicação.
* **Nomes de definição de configuração**. Para os valores que pretende que a sua aplicação (ões) ler durante a execução numa instância de função. O valor real das definições de configuração está definido no ficheiro de configuração (. CSCFG) do serviço que pode ser atualizado em qualquer altura, sem necessidade de Reimplementar o seu código. Na verdade, pode código as suas aplicações de forma para detetar os valores de configuração foi alterada sem incorrer em qualquer período de inatividade.
* **Pontos finais de entrada**. Aqui poderá Especifica qualquer HTTP, HTTPS ou TCP pontos finais (com portas) que pretende expor ao mundo externo através do seu *prefixo*. cloadapp.net URL. Quando o Azure implementa a função, esta irá configurar a firewall na instância de função automaticamente.
* **Pontos finais internos**. Aqui, pode especificar qualquer HTTP ou TCP pontos finais que pretende que sejam expostos a outras instâncias de função que são implementadas como parte da sua aplicação. Pontos finais internos permitem todas as instâncias de função na sua aplicação para comunicar entre si, mas não estão acessíveis para qualquer instâncias de função que não pertencem à sua aplicação.
* **Importar módulos**. Estes opcionalmente instalar útil componentes as instâncias da função. Componentes existem para o diagnóstico de monitorização, ambiente de trabalho remoto e Azure ligar (o que permite que a instância de função aceder a recursos no local através de um canal seguro).
* **Armazenamento local**. Isto aloca um subdiretório na instância de função para a sua aplicação utilizar. Descrita com maior detalhe no [ofertas de armazenamento de dados no Azure] [ Data Storage Offerings in Azure] artigo.
* **Tarefas de arranque**. Tarefas de arranque dão-lhe uma forma de instalar os componentes de pré-requisitos numa instância de função como arrancarem a. As tarefas podem ser executados elevadas como administrador, se necessário.

## <a id="cfg"></a>o ficheiro de configuração de serviço
O ficheiro de configuração (. CSCFG) do serviço é um ficheiro XML que descreve as definições que podem ser alteradas sem Reimplementar a sua aplicação. O esquema de completado para o ficheiro XML que pode ser encontrado aqui: [http://msdn.microsoft.com/library/windowsazure/ee758710.aspx][http://msdn.microsoft.com/library/windowsazure/ee758710.aspx].
O ficheiro CSCFG contém um elemento de função para cada função na sua aplicação. Seguem-se alguns dos itens que pode especificar no ficheiro CSCFG:

* **Versão do SO**. Este atributo permite-lhe selecionar a versão de sistema operativo (SO) que pretende utilizada para todas as instâncias de função com o código da aplicação. Este SO é conhecido como o *convidado SO*, e cada nova versão inclui as atualizações disponíveis no momento o SO convidado é libertado e patches de segurança mais recentes. Se definir o valor do atributo osVersion para "\*", em seguida, o Azure atualiza automaticamente o SO convidado em cada uma das suas instâncias de função como novas versões de SO convidado fiquem disponíveis. No entanto, pode escolher fora das atualizações automáticas selecionando um versão de SO de convidados específico. Por exemplo, definir o atributo osVersion para um valor de "WA-convidado-SO-2.8\_201109-01" faz com que todas as instâncias da função obter o que é descrito nesta página web: [http://msdn.microsoft.com/library/hh560567.aspx] [http://msdn.microsoft.com/library/hh560567.aspx]. Para obter mais informações sobre as versões de SO convidado, consulte [gerir atualizações para o SO de convidados Azure].
* **Instâncias**. O valor deste elemento indica o número de instâncias de função que pretende aprovisionado com o código para uma função específica. Uma vez que pode carregar um novo ficheiro CSCFG no Azure (sem Reimplementar a sua aplicação), que é trivially simples alterar o valor para este elemento e carregar um novo ficheiro CSCFG dinamicamente aumentar ou reduzir o número de instâncias de função com o código da aplicação . Isto permite-lhe dimensionar facilmente a aplicação de cópia de segurança ou para baixo para a carga de trabalho real às exigências ao também controlar quanto são-lhe cobrados para executar as instâncias da função.
* **Os valores de definição de configuração**. Este elemento indica os valores para definições (conforme definido no ficheiro CSDEF). A função pode ler estes valores, enquanto estiver em execução. Estes valores de definições de configuração são normalmente utilizadas nas cadeias de ligação à base de dados do SQL Server ou ao Storage do Azure, mas pode ser utilizados para qualquer finalidade que pretendidos ao nível.

## <a id="hostedservices"></a>Criar e implementar um serviço alojado
A criação de um serviço alojado requer primeiro passar para o [Azure Management Portal] e aprovisionar do Centro de um serviço alojado, especificando um prefixo de DNS e os dados quiser, fundamentalmente, o código em execução no. Em seguida, no seu ambiente de desenvolvimento, crie o seu ficheiro de definição (. CSDEF) do serviço, criar o seu código da aplicação e os ficheiros de pacote (. zip) para um ficheiro de pacote (. CSPKG) do serviço. Também tem de preparar o ficheiro de configuração (. CSCFG) do serviço. Para implementar a sua função, carregar os ficheiros CSPKG e CSCFG com a API de gestão de serviço do Azure. Depois de implementada, o Azure, aprovisionar instâncias de função no Centro de dados (com base nos dados de configuração), extraia o código da aplicação do pacote, copiá-lo para as instâncias de função e as instâncias de arranque. Agora, o seu código está em execução.

A figura abaixo mostra os ficheiros CSPKG e CSCFG que criar no seu computador de desenvolvimento. O ficheiro CSPKG contém o ficheiro CSDEF e o código para duas funções. Depois de carregar os ficheiros CSPKG e CSCFG com a API de gestão de serviço do Azure, o Azure cria as instâncias de função no Centro de dados. Neste exemplo, o ficheiro CSCFG indicado que a Azure deve criar três instâncias de função \#1 e duas instâncias de função \#2.

![Imagem][5]

Para obter mais informações sobre como implementar, atualizar e reconfigurar as funções, consulte o [implementar e atualizar aplicações do Azure] [ Deploying and Updating Azure Applications] artigo.<a id="Ref" name="Ref"></a>

## <a id="references"></a>Referências
* [Criar um serviço alojado do Azure][Creating a Hosted Service for Azure]
* [Gerir serviços alojados no Azure][Managing Hosted Services in Azure]
* [Migrar as aplicações do Azure][Migrating Applications to Azure]
* [Configuração de uma aplicação do Azure][Configuring an Azure Application]

<div style="width: 700px; border-top: solid; margin-top: 5px; padding-top: 5px; border-top-width: 1px;">

<p>Escritas pelo Jeffrey Richter (Wintellect)</p>

</div>

[Azure Application Model Benefits]: #benefits
[Hosted Service Core Concepts]: #concepts
[Hosted Service Design Considerations]: #considerations
[Designing your Application for Scale]: #scale
[Hosted Service Definition and Configuration]: #defandcfg
[The Service Definition File]: #def
[The Service Configuration File]: #cfg
[Creating and Deploying a Hosted Service]: #hostedservices
[References]: #references
[0]: ./media/application-model/application-model-3.jpg
[1]: ./media/application-model/application-model-4.jpg
[2]: ./media/application-model/application-model-5.jpg
[Configuring a Custom Domain Name in Azure]: http://www.windowsazure.com/develop/net/common-tasks/custom-dns/
[Data Storage Offerings in Azure]: http://www.windowsazure.com/develop/net/fundamentals/cloud-storage/
[3]: ./media/application-model/application-model-6.jpg
[4]: ./media/application-model/application-model-7.jpg

[Azure Pricing]: http://www.windowsazure.com/pricing/calculator/
[Managing Certificates in Azure]: http://msdn.microsoft.com/library/windowsazure/gg981929.aspx
[http://msdn.microsoft.com/library/windowsazure/ee758710.aspx]: http://msdn.microsoft.com/library/windowsazure/ee758710.aspx
[http://msdn.microsoft.com/library/hh560567.aspx]: http://msdn.microsoft.com/library/hh560567.aspx
[gerir atualizações para o SO de convidados Azure]: http://msdn.microsoft.com/library/ee924680.aspx
[Azure Management Portal]: http://manage.windowsazure.com/
[5]: ./media/application-model/application-model-8.jpg
[Deploying and Updating Azure Applications]: http://www.windowsazure.com/develop/net/fundamentals/deploying-applications/
[Creating a Hosted Service for Azure]: http://msdn.microsoft.com/library/gg432967.aspx
[Managing Hosted Services in Azure]: http://msdn.microsoft.com/library/gg433038.aspx
[Migrating Applications to Azure]: http://msdn.microsoft.com/library/gg186051.aspx
[Configuring an Azure Application]: http://msdn.microsoft.com/library/windowsazure/ee405486.aspx
