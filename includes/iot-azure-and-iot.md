
<a id="azure-and-internet-of-things" class="xliff"></a>

# Azure e Internet das Coisas

Bem-vindo ao Microsoft Azure e à Internet das Coisas (IoT). Este artigo apresenta uma arquitetura da solução de IoT que descreve as características comuns de uma solução de IoT que poderá implementar com os serviços do Azure. As soluções de IoT necessitam de comunicação bidirecional e segura entre dispositivos, possivelmente com números na casa dos milhões e um back-end de solução. Por exemplo, uma solução de back-end poderá utilizar a análise preditiva automatizada para descobrir informações a partir da transmissão de eventos do dispositivo para a nuvem.

O Hub IoT do Azure é um bloco modular fundamental ao implementar esta arquitetura de solução de IoT com os serviços do Azure. O IoT Suite fornece implementações completas de ponta a ponta desta arquitetura para cenários de IoT específicos. Por exemplo:

* A solução de *monitorização remota* permite-lhe monitorizar o estado dos dispositivos, como máquinas de venda automática.
* A solução de *manutenção preditiva* ajuda-o a prever a necessidade de manutenção dos dispositivos, como bombas nas estações de bombagem remotas e a evitar o tempo de inatividade não agendado.
* A solução da *fábrica ligada* ajuda-o a ligar e monitorizar os dispositivos industriais.

<a id="iot-solution-architecture" class="xliff"></a>

## Arquitetura da solução de IoT

O diagrama seguinte mostra uma arquitetura da solução de IoT típica. O diagrama não inclui os nomes de quaisquer serviços específicos do Azure, mas descreve os elementos essenciais numa arquitetura da solução de IoT genérica. Nesta arquitetura, os dispositivos IoT recolhem dados que enviam para um gateway de nuvem. O gateway de nuvem disponibiliza os dados para processamento por outros serviços de back-end, a partir de onde são enviados para outras aplicações de linha de negócio ou para operadores humanos através de um dashboard ou de outro dispositivo de apresentação.

![Arquitetura da solução de IoT][img-solution-architecture]

> [!NOTE]
> Para um debate aprofundado da arquitetura do IoT, veja [Microsoft Azure IoT Reference Architecture (Arquitetura de Referência do Microsoft Azure IoT)][lnk-refarch].

<a id="device-connectivity" class="xliff"></a>

### Conectividade dos dispositivos

Nesta arquitetura da solução de IoT, os dispositivos enviam telemetria, como as leituras dos sensores de uma estação de bombagem, para um ponto final da nuvem para armazenamento e processamento. Num cenário de manutenção preventiva, o back-end de solução poderá utilizar o fluxo de dados dos sensores para determinar quando uma bomba específica precisa de manutenção. Os dispositivos também podem receber e responder a mensagens da cloud para o dispositivo, lendo as mensagens a partir de um ponto final da cloud. Por exemplo, no cenário de manutenção preventiva, o back-end da solução poderá enviar mensagens a outras bombas na estação de bombagem para que comecem a reencaminhar os fluxos imediatamente antes do início previsto da manutenção. Este procedimento iria assegurar que o engenheiro de manutenção poderia começar assim que chegasse.

Um dos maiores desafios de projetos IoT consiste na ligação fiável e segura de dispositivos à solução de back-end. Os dispositivos IoT têm características diferentes em comparação a outros clientes, como browsers e aplicações móveis. Os dispositivos IoT:

* São frequentemente sistemas integrados sem nenhum operador humano.
* Podem ser implementados em localizações remotas, onde o acesso físico é dispendioso.
* Podem apenas ser acessíveis através do back-end da solução. Não há outro modo de interagir com o dispositivo.
* Podem ter potência e recursos de processamento limitados.
* Podem ter uma conectividade de rede intermitente, lente ou dispendiosa.
* Podem ter de utilizar protocolos de aplicação proprietários, personalizados ou específicos da indústria.
* Podem ser criados utilizando um grande conjunto de plataformas de hardware e software populares.

Para além dos requisitos identificados acima, qualquer solução de IoT deverá também proporcionar dimensionamento, segurança e fiabilidade. A implementação do conjunto de requisitos de conectividade resultante com tecnologias tradicionais, como contentores Web e mediadores de mensagens, é difícil e morosa. O Hub IoT do Azure e o Azure IoT device SDKs facilitam a implementação das soluções que cumpram estes requisitos.

Um dispositivo pode comunicar diretamente com um ponto final de gateway de nuvem ou, se não puder utilizar os protocolos de comunicações que o gateway de nuvem suporta, pode ligar-se através de um gateway intermédio. Por exemplo, o [gateway de protocolo do Azure IoT][lnk-protocol-gateway] pode efetuar a tradução do protocolo se os dispositivos não puderem utilizar qualquer um dos protocolos que o Hub IoT suporta.

<a id="data-processing-and-analytics" class="xliff"></a>

### Processamento e análise dos dados

Na nuvem, é no back-end da solução de IoT que ocorre a maior parte do processamento de dados, como a filtragem e a agregação de telemetria e o encaminhamento desta para outros serviços. O back-end da solução de IoT:

* Recebe a telemetria à escala dos seus dispositivos e determina como processar e armazenar esses dados. 
* Pode permitir-lhe enviar comandos a partir da nuvem para dispositivos específicos.
* Fornece capacidades de registo de dispositivos que lhe permitem aprovisionar dispositivos e controlar que dispositivos estão autorizados a ligar à sua infraestrutura.
* Permite-lhe acompanhar o estado dos seus dispositivos e monitorizar as respetivas atividades.

No cenário de manutenção preditiva, o back-end da solução armazena os dados de telemetria históricos. O back-end de solução pode utilizar estes dados para identificar padrões que indicam que a manutenção se deve a uma bomba específica.

As soluções de IoT podem incluir ciclos de feedback automáticos. Por exemplo, um módulo de análise no back-end de solução pode identificar, com base na telemetria, que a temperatura de uma máquina específica está acima dos níveis de funcionamento normais. A solução pode, em seguida, enviar um comando ao dispositivo, instruindo-o para tomar uma ação corretiva.

<a id="presentation-and-business-connectivity" class="xliff"></a>

### Apresentação e conectividade empresarial

A camada de apresentação e conectividade empresarial permite aos utilizadores finais interagirem com os dispositivos e soluções de IoT. Permite que os utilizadores vejam e analisem os dados recolhidos a partir dos dispositivos. Estas vistas podem ter a forma de dashboards ou de relatórios do BI e podem apresentar dados históricos ou dados quase em tempo real. Por exemplo, um operador pode verificar o estado de uma estação de bombagem específica e ver todos os alertas gerados pelo sistema. Esta camada também permite a integração do back-end da solução de IoT com aplicações da linha de negócio existentes para se ligarem a processos empresariais ou fluxos de trabalho da empresa. Por exemplo, a solução da manutenção preventiva pode ser integrada num sistema de agendamento que marca a visita de um técnico a uma estação de bombagem quando a solução identifica uma bomba a precisar de manutenção.

![Dashboard da solução de IoT][img-dashboard]

[img-solution-architecture]: ./media/iot-azure-and-iot/iot-reference-architecture.png
[img-dashboard]: ./media/iot-azure-and-iot/iot-suite.png

[lnk-machinelearning]: http://azure.microsoft.com/documentation/services/machine-learning/
[Azure IoT Suite]: http://azure.microsoft.com/solutions/iot
[lnk-protocol-gateway]:  ../articles/iot-hub/iot-hub-protocol-gateway.md
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
