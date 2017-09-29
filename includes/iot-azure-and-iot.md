
# <a name="azure-and-internet-of-things"></a>Azure e Internet das Coisas

Bem-vindo ao Microsoft Azure e à Internet das Coisas (IoT). Este artigo apresenta as características comuns de uma solução de IoT que poderá implementar com os serviços do Azure. As soluções de IoT precisam de comunicações bidirecionais seguras entre as vastas quantidades de dispositivos e um back-end de soluções. Por exemplo, o back-end de soluções poderá utilizar a análise preditiva automatizada para desvendar informações do seu fluxo de eventos entre os dispositivos e a cloud.

O [Hub Azure IoT][lnk-iot-hub] é um bloco modular fundamental de qualquer solução de IoT que utiliza os serviços do Azure. O Hub IoT é um serviço totalmente gerido que permite comunicações bidirecionais fiáveis e seguras entre milhões de dispositivos IoT e um back-end de soluções. 

O [Azure IoT Suite][lnk-iot-suite] fornece implementações ponto a ponto completas desta arquitetura para cenários de IoT específicos. Por exemplo:

* A solução de *monitorização remota* permite-lhe monitorizar o estado dos dispositivos, como máquinas de venda automática.
* A solução de *manutenção preditiva* ajuda-o a prever as necessidades de manutenção dos dispositivos, como as bombas de estações de bombagem remotas, e a evitar períodos de indisponibilidade não agendados.
* A solução da *fábrica ligada* ajuda-o a ligar e monitorizar os dispositivos industriais.

## <a name="iot-solution-architecture"></a>Arquitetura da solução de IoT

O diagrama seguinte mostra uma arquitetura da solução de IoT típica. O diagrama não inclui os nomes de quaisquer serviços específicos do Azure, mas descreve os elementos essenciais numa arquitetura da solução de IoT genérica. Nesta arquitetura, os dispositivos IoT recolhem dados que enviam para um gateway de nuvem. O gateway da cloud disponibiliza os dados para processamento por parte de outros serviços de back-end. O back-end de soluções fornece dados às aplicações de linha de negócio ou aos operadores humanos através de um dashboard ou relatório.

![Arquitetura da solução de IoT][img-solution-architecture]

> [!NOTE]
> Para um debate aprofundado da arquitetura do IoT, veja [Microsoft Azure IoT Reference Architecture (Arquitetura de Referência do Microsoft Azure IoT)][lnk-refarch].

### <a name="device-connectivity"></a>Conectividade dos dispositivos

Nesta solução de IoT, os dispositivos enviam telemetria, como as leituras dos sensores de uma estação de bombagem, para um ponto final da cloud para armazenamento e processamento. Num cenário de manutenção preventiva, o back-end de solução poderá utilizar o fluxo de dados dos sensores para determinar quando uma bomba específica precisa de manutenção. Os dispositivos também podem receber e responder a mensagens da cloud para o dispositivo, lendo as mensagens a partir de um ponto final da cloud. Por exemplo, no cenário de manutenção preventiva, o back-end da solução poderá enviar mensagens a outras bombas na estação de bombagem para que comecem a reencaminhar os fluxos imediatamente antes do início previsto da manutenção. Este procedimento asseguraria que o engenheiro de manutenção poderia começar a corrigir o problema de imediato.

Um dos maiores desafios de projetos IoT consiste na ligação fiável e segura de dispositivos à solução de back-end. Os dispositivos IoT têm características diferentes em comparação a outros clientes, como browsers e aplicações móveis. Os dispositivos IoT:

* São frequentemente sistemas integrados sem nenhum operador humano.
* Podem ser implementados em localizações remotas, onde o acesso físico é dispendioso.
* Podem apenas ser acessíveis através do back-end da solução. Não há outro modo de interagir com o dispositivo.
* Podem ter potência e recursos de processamento limitados.
* Podem ter uma conectividade de rede intermitente, lente ou dispendiosa.
* Podem ter de utilizar protocolos de aplicação proprietários, personalizados ou específicos da indústria.
* Podem ser criados utilizando um grande conjunto de plataformas de hardware e software populares.

Para além dos requisitos identificados acima, qualquer solução de IoT deverá também proporcionar dimensionamento, segurança e fiabilidade. A implementação do conjunto de requisitos de conectividade resultante com tecnologias tradicionais, como contentores Web e mediadores de mensagens, é difícil e morosa. O Hub IoT do Azure e o Azure IoT device SDKs facilitam a implementação das soluções que cumpram estes requisitos.

Um dispositivo pode comunicar diretamente com um ponto final de gateway da cloud. Se o dispositivo não conseguir utilizar nenhum dos protocolos de comunicações suportados pelo gateway da cloud, pode ligar-se através de um gateway intermédio. Por exemplo, o [gateway de protocolo do Azure IoT][lnk-protocol-gateway] pode efetuar a tradução do protocolo se os dispositivos não puderem utilizar qualquer um dos protocolos que o Hub IoT suporta.

### <a name="data-processing-and-analytics"></a>Processamento e análise dos dados

Na cloud, a maior parte do processamento de dados ocorre num back-end de soluções de IoT. O back-end da solução de IoT:

* Recebe a telemetria à escala dos seus dispositivos e determina como processar e armazenar esses dados. 
* Pode permitir o envio de comandos a partir da cloud para dispositivos específicos.
* Fornece capacidades de registo de dispositivos que lhe permitem aprovisionar os dispositivos e controlar quais são os dispositivos que podem ligar-se à sua infraestrutura.
* Permite-lhe acompanhar o estado dos seus dispositivos e monitorizar as respetivas atividades.

No cenário de manutenção preditiva, o back-end da solução armazena os dados de telemetria históricos. O back-end de soluções pode utilizar estes dados para identificar padrões que indiciam a necessidade de proceder à manutenção de uma bomba específica.

As soluções de IoT podem incluir ciclos de feedback automáticos. Por exemplo, um módulo de análise no back-end de solução pode identificar, com base na telemetria, que a temperatura de uma máquina específica está acima dos níveis de funcionamento normais. A solução pode, em seguida, enviar um comando ao dispositivo, instruindo-o para tomar uma ação corretiva.

### <a name="presentation-and-business-connectivity"></a>Apresentação e conectividade empresarial

A camada de apresentação e conectividade empresarial permite aos utilizadores finais interagirem com os dispositivos e soluções de IoT. Permite que os utilizadores vejam e analisem os dados recolhidos a partir dos dispositivos. Estas vistas podem apresentar-se sob a forma de dashboards ou relatórios capazes de mostrar dados históricos ou dados quase em tempo real. Por exemplo, um operador pode verificar o estado de uma estação de bombagem específica e ver todos os alertas gerados pelo sistema. Esta camada também permite a integração do back-end da solução de IoT com aplicações da linha de negócio existentes para se ligarem a processos empresariais ou fluxos de trabalho da empresa. Por exemplo, a solução de manutenção preditiva pode integrar-se com um sistema de agendamento. Quando a solução identifica uma bomba a precisar de manutenção, o sistema de agendamento marca a visita de um engenheiro à estação de bombagem.

![Dashboard da solução de IoT][img-dashboard]

[img-solution-architecture]: ./media/iot-azure-and-iot/iot-reference-architecture.png
[img-dashboard]: ./media/iot-azure-and-iot/iot-suite.png

[lnk-iot-hub]: ../articles/iot-hub/iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: ../articles/iot-suite/iot-suite-overview.md
[lnk-machinelearning]: http://azure.microsoft.com/documentation/services/machine-learning/
[Azure IoT Suite]: http://azure.microsoft.com/solutions/iot
[lnk-protocol-gateway]:  ../articles/iot-hub/iot-hub-protocol-gateway.md
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
