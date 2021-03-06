# Descrição geral
## [Azure e IoT](iot-hub-what-is-azure-iot.md)
## [O que é o Hub IoT do Azure?](iot-hub-what-is-iot-hub.md)
## [Descrição geral da gestão de dispositivos](iot-hub-device-management-overview.md)

# [Introdução](iot-hub-get-started.md)

## Configurar o dispositivo
### [Simular um dispositivo no PC](iot-hub-get-started-simulated.md)
#### [.NET](iot-hub-csharp-csharp-getstarted.md)
#### [Java](iot-hub-java-java-getstarted.md)
#### [Node.js](iot-hub-node-node-getstarted.md)
#### [Python](iot-hub-python-getstarted.md)

### [Utilizar um simulador online](iot-hub-raspberry-pi-web-simulator-get-started.md)

### [Utilizar um dispositivo físico](iot-hub-get-started-physical.md)
#### [Raspberry Pi com Python](iot-hub-raspberry-pi-kit-python-get-started.md)
#### [Raspberry Pi com Node.js](iot-hub-raspberry-pi-kit-node-get-started.md)
#### [Raspberry Pi com C](iot-hub-raspberry-pi-kit-c-get-started.md)

#### [MXChip IoT DevKit com Arduino](iot-hub-arduino-iot-devkit-az3166-get-started.md)

#### [Intel Edison com Node.js](iot-hub-intel-edison-kit-node-get-started.md)
#### [Intel Edison com C](iot-hub-intel-edison-kit-c-get-started.md)

#### [Adafruit Feather HUZZAH ESP8266 com Arduino IDE](iot-hub-arduino-huzzah-esp8266-get-started.md)
#### [Sparkfun ESP8266 Thing Dev com Arduino IDE](iot-hub-sparkfun-esp8266-thing-dev-get-started.md)
#### [Adafruit Feather M0 com Arduino IDE](iot-hub-adafruit-feather-m0-wifi-kit-arduino-get-started.md)

## Cenários de IoT alargados
### [Gerir mensagens de dispositivo na cloud com o iothub-explorer](iot-hub-explorer-cloud-device-messaging.md)
### [Guardar mensagens do Hub IoT no armazenamento de dados do Azure](iot-hub-store-data-in-azure-table-storage.md)
### [Visualização de Dados no Power BI](iot-hub-live-data-visualization-in-power-bi.md)
### [Visualização de Dados com as Aplicações Web](iot-hub-live-data-visualization-in-web-apps.md)
### [Previsão meteorológica com o Azure Machine Learning](iot-hub-weather-forecast-machine-learning.md)
### [Gestão de dispositivos com o iothub-explorer](iot-hub-device-management-iothub-explorer.md)
### [Monitorização remota e notificações com Aplicações Lógicas](iot-hub-monitoring-notifications-with-azure-logic-apps.md)

# Guias de procedimentos
## Planear
### [Comparação entre o Hub IoT e os Hubs de Eventos](iot-hub-compare-event-hubs.md)
### [Dimensionar a sua solução](iot-hub-scaling.md)
### [Elevada disponibilidade e recuperação após desastre](iot-hub-ha-dr.md)
### [Suportar protocolos adicionais](iot-hub-protocol-gateway.md)
## [Programar](iot-hub-how-to.md)
### [Guia para programadores](iot-hub-devguide.md)
#### [Guia de funcionalidades de dispositivo para cloud](iot-hub-devguide-d2c-guidance.md)
#### [Guia de funcionalidades da cloud para dispositivo](iot-hub-devguide-c2d-guidance.md)
#### [Enviar e receber mensagens](iot-hub-devguide-messaging.md)
##### [Enviar mensagens de dispositivo para cloud para o Hub IoT](iot-hub-devguide-messages-d2c.md)
##### [Ler mensagens de dispositivo para cloud a partir do ponto final incorporado ](iot-hub-devguide-messages-read-builtin.md)
##### [Utilizar pontos finais personalizados e regras de encaminhamento para mensagens de dispositivo para cloud](iot-hub-devguide-messages-read-custom.md)
##### [Enviar mensagens de cloud para dispositivo a partir do Hub IoT](iot-hub-devguide-messages-c2d.md)
##### [Criar e ler mensagens do Hub IoT](iot-hub-devguide-messages-construct.md)
##### [Escolher um protocolo de comunicação](iot-hub-devguide-protocols.md)
#### [Carregar ficheiros de um dispositivo](iot-hub-devguide-file-upload.md)
#### [Gerir identidades do dispositivo](iot-hub-devguide-identity-registry.md)
#### [Controlar o acesso ao Hub IoT](iot-hub-devguide-security.md)
#### [Compreender os dispositivos duplos](iot-hub-devguide-device-twins.md)
#### [Invocar métodos diretos num dispositivo](iot-hub-devguide-direct-methods.md)
#### [Programar tarefas em vários dispositivos](iot-hub-devguide-jobs.md)
#### [Pontos finais do Hub IoT](iot-hub-devguide-endpoints.md)
#### [Linguagem da consulta](iot-hub-devguide-query-language.md)
#### [Quotas e limitação](iot-hub-devguide-quotas-throttling.md)
#### [Exemplos de preços](iot-hub-devguide-pricing.md)
#### [SDKs de dispositivos e de serviços](iot-hub-devguide-sdks.md)
#### [Suporte para MQTT](iot-hub-mqtt-support.md)
#### [Glossário](iot-hub-devguide-glossary.md)
### [Utilizar o SDK do dispositivo IoT para C](iot-hub-device-sdk-c-intro.md)
#### [Utilizar o IoTHubClient](iot-hub-device-sdk-c-iothubclient.md)
#### [Utilizar o serializador](iot-hub-device-sdk-c-serializer.md)
### Processar mensagens do dispositivo para a cloud
#### [.NET](iot-hub-csharp-csharp-process-d2c.md)
#### [Java](iot-hub-java-java-process-d2c.md)
### Enviar mensagens da cloud para o dispositivo
#### [.NET](iot-hub-csharp-csharp-c2d.md)
#### [Java](iot-hub-java-java-c2d.md)
#### [Node.js](iot-hub-node-node-c2d.md)
### Carregar ficheiros a partir de dispositivos
#### [.NET](iot-hub-csharp-csharp-file-upload.md)
#### [Java](iot-hub-java-java-file-upload.md)
#### [Node.js](iot-hub-node-node-file-upload.md)
### Introdução aos dispositivos duplos
#### [Back-end de Node.js/Dispositivo Node.js](iot-hub-node-node-twin-getstarted.md)
#### [Back-end de .NET/Dispositivo Node.js](iot-hub-csharp-node-twin-getstarted.md)
#### [Dispositivo de back-end .NET/.NET](iot-hub-csharp-csharp-twin-getstarted.md)
#### [Back-end de Java/Dispositivo Java](iot-hub-java-java-twin-getstarted.md)
### Utilizar métodos diretos
#### [Back-end de Node.js/Dispositivo Node.js](iot-hub-node-node-direct-methods.md)
#### [Back-end de .NET/Dispositivo Node.js](iot-hub-csharp-node-direct-methods.md)
#### [Dispositivo de back-end .NET/.NET](iot-hub-csharp-csharp-direct-methods.md)
#### [Back-end de Java/Dispositivo Java](iot-hub-java-java-direct-methods.md)
### Introdução à gestão de dispositivos
#### [Back-end de Node.js/Dispositivo Node.js](iot-hub-node-node-device-management-get-started.md)
#### [Back-end de .NET/Dispositivo Node.js](iot-hub-csharp-node-device-management-get-started.md)
#### [Dispositivo de back-end .NET/.NET](iot-hub-csharp-csharp-device-management-get-started.md)
#### [Back-end de Java/Dispositivo Java](iot-hub-java-java-device-management-getstarted.md)
### Como utilizar as propriedades duplas
#### [Back-end de Node.js/Dispositivo Node.js](iot-hub-node-node-twin-how-to-configure.md)
#### [Back-end de .NET/Dispositivo Node.js](iot-hub-csharp-node-twin-how-to-configure.md)
#### [Dispositivo de back-end .NET/.NET](iot-hub-csharp-csharp-twin-how-to-configure.md)
#### [Back-end de Java/Dispositivo Java](iot-hub-java-java-twin-how-to-configure.md)
### Utilizar tarefas de dispositivo para atualizar o firmware do dispositivo
#### [Back-end de Node/Dispositivo Node](iot-hub-node-node-firmware-update.md)
#### [Back-end de .NET/Dispositivo Node.js](iot-hub-csharp-node-firmware-update.md)
#### [Dispositivo de back-end .NET/.NET](iot-hub-csharp-csharp-firmware-update.md)
#### [Back-end de Java/Dispositivo Java](iot-hub-java-java-firmware-update.md)
### Agendar e difundir tarefas
#### [Back-end de Node.js/Dispositivo Node.js](iot-hub-node-node-schedule-jobs.md)
#### [Back-end de .NET/Dispositivo Node.js](iot-hub-csharp-node-schedule-jobs.md)
#### [Java](iot-hub-java-java-schedule-jobs.md)

## Gerir
### Criar um hub IoT 
#### [Utilizar o portal do Azure](iot-hub-create-through-portal.md)
#### [Utilizar o Azure PowerShell](iot-hub-create-using-powershell.md)
#### [Utilizar a CLI do Azure](iot-hub-create-using-cli.md)
#### [Utilizar a CLI](iot-hub-create-using-cli-nodejs.md)
#### [Utilizar a API REST](iot-hub-rm-rest.md)
#### [Utilizar um modelo do Azure PowerShell](iot-hub-rm-template-powershell.md)
#### [Utilizar um modelo do .NET](iot-hub-rm-template.md)
### Configurar o carregamento de ficheiros
#### [Utilizar o portal do Azure](iot-hub-configure-file-upload.md)
#### [Utilizar o Azure PowerShell](iot-hub-configure-file-upload-powershell.md)
#### [Utilizar a CLI do Azure](iot-hub-configure-file-upload-cli.md)
### [Monitorizar com diagnósticos](iot-hub-monitor-resource-health.md)
#### [Migrar para as definições de diagnóstico](iot-hub-migrate-to-diagnostics-settings.md)
#### [Monitorização de operações](iot-hub-operations-monitoring.md)
### [Métricas de utilização](iot-hub-metrics.md)
### [Gerir dispositivos IoT em massa](iot-hub-bulk-identity-mgmt.md)
### [Configurar a filtragem de IPs](iot-hub-ip-filtering.md)

## Proteger
### [Segurança desde o início](iot-hub-security-ground-up.md)
### [Melhores práticas de segurança](iot-hub-security-best-practices.md)
### [Arquitetura de segurança](iot-hub-security-architecture.md)
### [Proteger a sua implementação de IoT](iot-hub-security-deployment.md)
### Proteger com certificados X.509 de AC
#### [Descrição geral da segurança de certificado X.509 de AC](iot-hub-x509ca-overview.md)
##### [Conceitos da segurança de certificado X.509 de AC](iot-hub-x509ca-concept.md)
#### [Introdução à segurança de certificado X.509 de AC](iot-hub-security-x509-get-started.md)
##### [Criar certificados - PowerShell](iot-hub-security-x509-create-certificates.md)

# Referência
## [Exemplos de código](https://azure.microsoft.com/en-us/resources/samples/?service=iot-hub)
## [CLI do Azure](/cli/azure/iot)
## [.NET (Serviço)](/dotnet/api/microsoft.azure.devices)
## [.NET (Dispositivos)](/dotnet/api/microsoft.azure.devices.client)
## [Java (Serviço)](/java/api/com.microsoft.azure.sdk.iot.service)
## [Java (Dispositivos)](/java/api/com.microsoft.azure.sdk.iot.device)
## [Node.js (Dispositivos)](https://docs.microsoft.com/en-us/javascript/api/azure-iot-device/)
## [Node.js (Serviço)](https://docs.microsoft.com/en-us/javascript/api/azure-iothub)
## [SDK de dispositivo para C](https://azure.github.io/azure-iot-sdk-c/index.html)
## [Azure IoT Edge](http://azure.github.io/iot-edge/)
## [REST (Fornecedor de Recursos)](https://docs.microsoft.com/rest/api/iothub/iothubresource)
## [REST (Identidades do Dispositivo)](https://docs.microsoft.com/rest/api/iothub/deviceapi)
## [REST (Dispositivos Duplos)](https://docs.microsoft.com/rest/api/iothub/devicetwinapi)
## [REST (Mensagens de Dispositivo)](https://docs.microsoft.com/rest/api/iothub/httpruntime)
## [REST (Tarefas)](https://docs.microsoft.com/rest/api/iothub/jobapi)

# Relacionado
## [Azure IoT Suite](https://azure.microsoft.com/documentation/suites/iot-suite/)
## [Azure Event Hubs](https://azure.microsoft.com/documentation/services/event-hubs/)
## [Stream Analytics](https://azure.microsoft.com/documentation/services/stream-analytics/)
## [Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/)

# Recursos
## [Catálogo de dispositivos Azure Certified for IoT](https://catalog.azureiotsuite.com/)
## [Centro de Programadores do Azure IoT](https://azure.microsoft.com/develop/iot/)
## [Mapa do Azure](https://azure.microsoft.com/roadmap/?category=internet-of-things)
## [Ferramenta DeviceExplorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer)
## [Ferramenta iothub-diagnostics](https://github.com/Azure/iothub-diagnostics)
## [Ferramenta iothub-explorer](https://github.com/Azure/iothub-explorer)
## [Percurso de aprendizagem](https://azure.microsoft.com/documentation/learning-paths/iot-hub/)
## [Fórum do MSDN](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=azureiothub)
## [Preços](https://azure.microsoft.com/pricing/details/iot-hub/)
## [Calculadora de preços](https://azure.microsoft.com/pricing/calculator/)
## [Atualizações de serviço](https://azure.microsoft.com/updates/?product=iot-hub)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-iot-hub)
## [Estudos de caso técnicos](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured)
## [Vídeos](https://azure.microsoft.com/documentation/videos/index/?services=iot-hub)
