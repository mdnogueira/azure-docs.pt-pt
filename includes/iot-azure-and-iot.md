# Azure e Internet das Coisas

Bem-vindo ao Microsoft Azure e à Internet das Coisas (IoT). Este artigo apresenta uma arquitetura da solução IoT que descreve as características comuns de uma solução IoT que poderá implementar com os serviços do Azure. As soluções IoT exigem uma comunicação bidirecional segura entre dispositivos, possivelmente uns milhões deles, e um back-end da solução que utilize, por exemplo, a análise preditiva automatizada para desvendar informações a partir do seu fluxo de eventos do dispositivo para a nuvem.

O IoT Hub do Azure é um bloco modular fundamental para implementar esta arquitetura da solução IoT com os serviços do Azure e IoT Suite fornece implementações ponto a ponto completas desta arquitetura para cenários IoT específicos. Por exemplo, a solução *monitorização remota* permite monitorizar o estado dos dispositivos como distribuidores automáticos e a *manutenção preditiva* ajuda a prever as necessidades de manutenção dos dispositivos como bombas em estações de bombagem remotas e a evitar períodos de inatividade não agendados.

## Arquitetura da solução IoT

O diagrama seguinte mostra uma arquitetura da solução IoT típica. Tenha em atenção que não inclui os nomes de quaisquer serviços específicos do Azure, mas descreve os elementos essenciais numa arquitetura da solução IoT genérica. Nesta arquitetura, os dispositivos IoT recolhem dados que enviam para um gateway de nuvem. O gateway de nuvem disponibiliza os dados para processamento por outros serviços de back-end a partir de onde os dados são entregues a outras aplicações da linha de negócio ou a operadores humanos através de um dashboard ou de outro dispositivo de apresentação.

![Arquitetura da solução IoT][img-solution-architecture]

> [AZURE.NOTE] Para um debate aprofundado sobre a arquitetura IoT, veja [Microsoft Azure IoT Reference Architecture (Arquitetura de Referência da IoT do Microsoft Azure)][lnk-refarch].

### Conectividade dos dispositivos

Nesta arquitetura da solução IoT, os dispositivos enviam telemetria, como as leituras dos sensores de uma estação de bombagem, para um ponto final da nuvem para armazenamento e processamento. Num cenário de manutenção preventiva, o back-end poderá utilizar o fluxo de dados dos sensores para determinar quando uma bomba específica precisa de manutenção. Os dispositivos também podem receber e responder a comandos da nuvem para o dispositivo, lendo as mensagens a partir de um ponto final da nuvem. Por exemplo, no cenário de manutenção preventiva, o back-end da solução poderá enviar comandos a outras bombas na estação de bombagem para que comecem a reencaminhar os fluxos imediatamente antes do início previsto da manutenção, de modo a garantir que o técnico de manutenção possa começar assim que chegar.

Um dos maiores desafios que enfrentam os projetos IoT é como ligar de modo seguro e fiável os dispositivos ao back-end da solução para permitir que o dispositivo envie a telemetria e obtenha os comandos. Os dispositivos IoT têm características diferentes em comparação a outros clientes, como browsers e aplicações móveis. Dispositivos IoT:

- São frequentemente sistemas integrados sem nenhum operador humano.
- Podem ser implementados em localizações remotas, onde o acesso físico é muito dispendioso.
- Podem apenas ser acessíveis através do back-end da solução. Não há outro modo de interagir com o dispositivo.
- Podem ter potência e recursos de processamento limitados.
- Podem ter uma conectividade de rede intermitente, lenta ou dispendiosa.
- Podem ter de utilizar protocolos de aplicações proprietários personalizados ou específicos da indústria.
- Podem ser criados utilizando um grande conjunto de plataformas de hardware e software populares.

Para além dos requisitos identificados acima, qualquer solução IoT deverá também proporcionar dimensionamento, segurança e fiabilidade. A implementação do conjunto de requisitos de conectividade resultante com tecnologias tradicionais, como contentores Web e mediadores de mensagens, é difícil e morosa. O IoT Hub do Azure e os SDKs dos Dispositivos IoT facilitam a implementação das soluções que cumpram estes requisitos.

Um dispositivo pode comunicar diretamente com um ponto final de gateway de nuvem ou, se o dispositivo não puder utilizar os protocolos de comunicações que o gateway de nuvem suporta, pode ligar através de um gateway intermédio, como o [Gateway do protocolo IoT Hub][lnk-protocol-gateway], que realiza a conversão dos protocolos. Por exemplo, do Common Industrial Protocol (CIP) para o AMQPS.

### Processamento e análise dos dados

Na nuvem, é no back-end da solução IoT que ocorre a maior parte do processamento de dados da solução, em particular a filtragem e a telemetria de agregação e o encaminhamento para outros serviços. Back-end da solução IoT:

- Recebe a telemetria à escala dos seus dispositivos e determina como processar e armazenar esses dados. 
- Pode permitir-lhe enviar comandos a partir da nuvem para dispositivos específicos.
- Fornece capacidades de registo de dispositivos que lhe permitem aprovisionar dispositivos e controlar que dispositivos estão autorizados a ligar à sua infraestrutura.
- Permite-lhe acompanhar o estado dos seus dispositivos e monitorizar as respetivas atividades.

No cenário de manutenção preventiva, o back-end da solução armazena os dados históricos de telemetria que utiliza para identificar padrões e analisa a telemetria à medida que chega para detetar padrões que indiquem a necessidade de manutenção numa bomba específica.

As soluções IoT podem incluir ciclos de feedback automáticos. Por exemplo, um módulo de análise no back-end pode identificar, com base na telemetria, que a temperatura de um dispositivo específico está acima dos níveis de funcionamento normais e enviar um comando ao dispositivo, instruindo-o para tomar uma ação corretiva.

### Apresentação e conectividade empresarial

A camada de apresentação e conectividade empresarial permite aos utilizadores finais interagirem com os dispositivos e soluções IoT. Permite que os utilizadores vejam e analisem os dados recolhidos a partir dos dispositivos. Estas vistas podem ter a forma de dashboards ou de relatórios do BI e podem apresentar dados históricos ou dados quase em tempo real. Por exemplo, um operador pode verificar o estado de uma estação de bombagem específica e ver todos os alertas gerados pelo sistema. Esta camada também permite a integração do back-end da solução IoT com aplicações da linha de negócio existentes para se ligarem a processos empresariais ou fluxos de trabalho da empresa. Por exemplo, a solução da manutenção preventiva pode ser integrada a um sistema de agendamento que marca a visita de um técnico a uma estação de bombagem quando a solução identifica uma bomba a precisar de manutenção.

![Dashboard da solução IoT][img-dashboard]

[img-solution-architecture]: ./media/iot-azure-and-iot/iot-reference-architecture.png
[img-dashboard]: ./media/iot-azure-and-iot/iot-suite.png

[lnk-machinelearning]: http://azure.microsoft.com/documentation/services/machine-learning/
[Azure IoT Suite]: http://azure.microsoft.com/solutions/iot
[lnk-protocol-gateway]:  ../articles/iot-hub/iot-hub-protocol-gateway.md
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf


<!--HONumber=Sep16_HO3-->


