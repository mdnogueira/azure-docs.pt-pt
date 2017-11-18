---
title: "Arquitetura de segurança de IoT | Microsoft Docs"
description: "Orientações de arquitetura de segurança de IoT e considerações"
services: 
suite: iot-suite
documentationcenter: 
author: YuriDio
manager: timlt
editor: 
ms.assetid: 18ed3eb0-9406-44e1-8a3a-93dc6726c7ac
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/03/2017
ms.author: yurid
ms.openlocfilehash: 2482dade7d17d05b2fc90fbf22b0466227a5983b
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2017
---
# <a name="internet-of-things-security-architecture"></a>Arquitetura de segurança da Internet das coisas
Ao conceber um sistema, é importante compreender as potenciais ameaças a esse sistema e ao adicionar o defesas adequadas em conformidade, como o sistema é concebido e criado. É particularmente importante para a estrutura do produto a partir do início com segurança em mente porque compreender como um atacante poderá comprometer um sistema de ajuda a mitigações adequadas se de que estão no local a partir do início. 

## <a name="security-starts-with-a-threat-model"></a>Segurança começa com um modelo de ameaça
A Microsoft longa utilizou modelos de ameaça para os produtos e efetuou disponível publicamente do processo de modelação de ameaça da empresa. A experiência de empresa demonstra que a modelação tem vantagens inesperadas para além de compreender imediata de que são mais relativo à. Por exemplo, também cria um compromissos para um debate aberto com outras pessoas fora da equipa de desenvolvimento, que pode levar a novas ideias e melhoramentos no produto.

O objetivo da modelação de ameaça é compreender como um atacante poderá comprometer um sistema e, em seguida, certifique-se mitigações adequadas são cumpridos. Força de modelação de ameaça a equipa de design mitigações de considerar como o sistema é concebido vez depois de um sistema é implementado. Este facto é extremamente importante, porque retrofitting defesas de segurança para um conjunto de dispositivos no campo é infeasible, propensas ao erro e sair clientes em risco.

As equipas de desenvolvimento muitos efetuar uma tarefa excelente capturar os requisitos funcionais para o sistema que beneficiam de clientes. No entanto, a identificar as formas não óbvios que alguém poderá utilizar indevidamente o sistema é mais difícil. Modelação de ameaça pode ajudar a compreender que um atacante pode fazer as equipas de desenvolvimento e por que motivo. Modelação de ameaça é um processo estruturado, que cria as decisões de conceção de um debate sobre a segurança no sistema, bem como as alterações para a estrutura que são efetuadas ao longo do caminho que segurança impacto. Enquanto um modelo de ameaça é simplesmente um documento, esta documentação também representa uma forma ideal para assegurar a continuidade do conhecimento, retenção de lições aprendidas e ajuda nova equipa carregar rapidamente. Por fim, um resultado de modelação de ameaça é permitir-lhe a ter em consideração outros aspetos de segurança, tais como os compromissos de segurança que pretende fornecer aos seus clientes. Estes compromissos em conjunto com modelação de ameaça irão informar e unidade testar da sua solução Internet das coisas (IoT).

### <a name="when-to-threat-model"></a>Quando o modelo de ameaça
[Modelação de ameaça](http://www.microsoft.com/security/sdl/adopt/threatmodeling.aspx) oferece o maior valor se incorporada para a fase de conceção. Quando está a conceber, tiver a maior flexibilidade para efetuar alterações ao eliminar a ameaças. Eliminar ameaças por predefinição é o resultado desejado. É muito fácil adicionar mitigações, teste-los e assegurar que permanecem atuais e além disso, essa eliminação não é sempre possível. Torna mais difícil eliminar a ameaças à medida que um produto torna-se mais madura, por sua vez, fundamentalmente, necessitará mais tarefas e muito mais difícil fala que threat modeling numa fase inicial no desenvolvimento.

### <a name="what-to-threat-model"></a>O que o modelo de ameaça
Deve a solução como um todo do modelo de thread e também focar-se nas seguintes áreas:

* As funcionalidades de segurança e privacidade
* As funcionalidades cujas falhas são relevante de segurança
* As funcionalidades que touch um limite de fidedignidade 

### <a name="who-threat-models"></a>Quem ameaça modelos
Modelação de ameaça é um processo como qualquer outro.  É uma boa ideia tratar o documento do modelo de ameaça como qualquer outro componente da solução e a validá-lo. As equipas de desenvolvimento muitos efetuar uma tarefa excelente capturar os requisitos funcionais para o sistema que beneficiam de clientes. No entanto, a identificar as formas não óbvios que alguém poderá utilizar indevidamente o sistema é mais difícil. Modelação de ameaça pode ajudar a compreender que um atacante pode fazer as equipas de desenvolvimento e por que motivo.

### <a name="how-to-threat-model"></a>Como modelo de ameaça
A processo de modelação de ameaça é constituída por quatro passos; os passos são:

* A aplicação de modelo
* Enumerar ameaças
* Mitigar ameaças
* Validar as mitigações

#### <a name="the-process-steps"></a>Os passos do processo
Três regras geral a lembrar quando criar um modelo de ameaça:

1. Crie um diagrama de arquitetura de referência. 
2. Inicie leque primeiro. Obter uma descrição geral e compreender o sistema como um todo, antes de diving de avançada.  Isto ajuda a garantir que a descrição profunda nos locais corretos.
3. O processo de unidade, não permita que o processo de unidade. Se encontrar um problema na fase de modelação e pretende explorá-lo, vá para o mesmo!  Não pode tem de seguir estes passos slavishly.  

#### <a name="threats"></a>Ameaças
Os elementos de quatro core de um modelo de ameaça são:

* Os processos (serviços web, os serviços de Win32, * nix daemons, etc. Tenha em atenção que podem ser abstracted algumas entidades complexas (por exemplo, os gateways de campo e sensores) como um processo quando uma desagregação técnica nas seguintes áreas não é possível.
* Armazena os dados (em qualquer lugar os dados são armazenados, tal como um ficheiro de configuração ou a base de dados)
* Fluxo de dados (onde dados movem-se entre a outros elementos na aplicação)
* As entidades externas (tudo o que interage com o sistema, mas não está sob o controlo da aplicação, os exemplos incluem os utilizadores e satélite feeds)

Todos os elementos de diagrama da arquitetura estão sujeitos a ameaças vários; Utilizaremos o mnemónica ser STRIDE exigido. Leitura [Threat Modeling novamente, STRIDE](https://blogs.msdn.microsoft.com/larryosterman/2007/09/04/threat-modeling-again-stride/) para saber mais sobre os elementos STRIDE.

Diferentes elementos do diagrama de aplicação estão sujeitos a determinados ameaças STRIDE:

* Os processos são sujeitos a STRIDE
* Fluxos de dados estão sujeitos a TID
* Arquivos de dados estão sujeitos a TID e, por vezes, R, se os arquivos de dados são ficheiros de registo.
* As entidades externas estão sujeitos a SRD

## <a name="security-in-iot"></a>Segurança de IoT
Dispositivos com objetivos especiais ligados tem um número significativo de potenciais áreas de interação superfície e padrões de interação, todos os de que tem de ser considerados para fornecer uma arquitetura para proteger o acesso digital para esses dispositivos. O termo "acesso digital" é utilizado aqui para distinguir entre quaisquer operações que são executadas através da interação do dispositivo direta onde a segurança de acesso é fornecida através de controlo de acesso físico. Por exemplo, colocar o dispositivo para uma sala de um bloqueio na porta. Enquanto o acesso físico não pode ser negado utilizando o software e hardware, medidas podem ser tomadas para impedir o acesso físico à esquerda para interferências de sistema. 

Como podemos explorar os padrões de interação, veremos "controlo de dispositivos" e "dados de dispositivo" com o mesmo nível de atenção. "Controlo de dispositivo" pode ser classificado como todas as informações que são fornecidas para um dispositivo por quaisquer terceiros com o objetivo de alterar ou que influencia as respetivo comportamento para o estado ou o estado do seu ambiente. "Dados de dispositivo" podem ser classificados como todas as informações que emite um dispositivo para qualquer outro interveniente sobre o estado e o estado do respetivo ambiente observado.

Para otimizar a melhores práticas de segurança, recomenda-se que uma arquitetura IoT típica ser divididos em vários componentes/zonas como parte da threat modeling exercício. Estes zonas descritas totalmente em toda esta secção e incluem:

* Dispositivo,
* Gateway de campo
* Gateways, de nuvem e
* Serviços.

Zonas estão abrangentes para segmentar uma solução; cada zona tem muitas vezes, os seus próprios requisitos de dados e a autenticação e autorização. Zonas também podem ser utilizados para danos de isolamento e restringir o impacto das zonas de fidedignidade baixa em zonas de confiança superiores.

Cada zona é separada por um limite de fidedignidade, que é apresentado como a ponto vermelha linha no diagrama abaixo. Representa uma transição de dados/informações a partir de uma origem para outro. Durante esta transição, as informações/dados pode ser sujeito Spoofing, violação, rejeição, divulgação de informações, Denial of Service e elevação de privilégio (STRIDE).

![Zonas de segurança de IoT](media/iot-security-architecture/iot-security-architecture-fig1.png) 

Os componentes representados dentro de cada limite também são sujeitos à STRIDE, ativar uma 360 completa vista da solução de modelação de ameaça. As secções abaixo elaboradasm em todos os componentes e preocupações de segurança específicos e soluções que devem ser colocadas no local.

As secções que se seguem serão abordadas componentes padrão a geralmente encontrados destas zonas.

### <a name="the-device-zone"></a>A zona de dispositivo
O ambiente de dispositivo é o espaço físico imediato em torno do dispositivo onde físico acesso e/ou "rede local" ponto-a-ponto acesso digital para o dispositivo for viável. Um "da rede local" é pressupõe-se que uma rede que é distinto e insulated de – mas potencialmente com bridge – à Internet pública e inclui qualquer tecnologia de botões de opção sem fios short-range que permite a comunicação de ponto-a-ponto dos dispositivos. Faz *não* incluem quaisquer tecnologia de Virtualização de rede criar a ilusão de uma rede local e também não incluir redes de operador público que necessitam de quaisquer dois dispositivos a comunicar através de espaço de rede pública se se encontravam introduzir uma relação de comunicação de ponto a ponto.

### <a name="the-field-gateway-zone"></a>A zona de Gateway de campo
Gateway de campo é uma aplicação/dispositivo ou algum software de computador do servidor para fins gerais que age como ativador de comunicação e, potencialmente, como um sistema de controlo do dispositivo e o hub de processamento de dados do dispositivo. A zona de gateway de campo inclui o próprio gateway de campo e todos os dispositivos que estão anexados ao mesmo. Como o nome indica, gateways de campo agirem de processamento de dados dedicado fora das instalações, são normalmente localização vinculada, potencialmente estão sujeitos a intrusões físico e serão limitada redundância operacional. Para indicar que um gateway de campo é normalmente uma coisa um touch e sabotage sabendo que a sua função é. 

Um gateway de campo é diferente de um router de tráfego mere em que tenha tido uma função ativa na gestão de acesso e fluxo de informações, que significa que é uma aplicação resolvido ligação de rede e de entidade ou sessão de terminal. Um dispositivo NAT ou uma firewall, por outro lado, não se qualificam como gateways de campo, uma vez que não são ligação explícita ou terminals de sessão, mas em vez de um ligações rota (ou bloco) ou sessões efetuadas através de-los. O gateway de campo tem duas áreas superfície distintas. Um enfrenta os dispositivos que estão anexados ao mesmo e representa dentro da zona e o outro enfrenta todas as entidades externas e é o limite da zona.   

### <a name="the-cloud-gateway-zone"></a>A zona de gateway de nuvem
Gateway de nuvem é um sistema que permite a comunicação remota de origem e de dispositivos ou gateways de campo de vários sites diferentes em espaço de rede pública, normalmente, para um controlo baseado na nuvem e de sistema de análise de dados, Federação destes sistemas. Em alguns casos, um gateway de nuvem poderá imediatamente facilitam o acesso a dispositivos com objetivos especiais de terminals como tablets ou telemóveis. No contexto abordado aqui, "nuvem" destina-se para fazer referência a um sistema de processamento de dados dedicado que não está associado ao mesmo site que os dispositivos ligados ou gateways de campo. Também uma zona de nuvem, medidas operacionais impedem o acesso físico de destino em não são necessariamente expostas a uma infraestrutura de "nuvem pública".  

Um gateway de nuvem, potencialmente, pode ser mapeado para uma sobreposição de Virtualização de rede para insulate o gateway de nuvem e de todos os respetivos dispositivos ligados ou gateways de campo de qualquer outro tráfego de rede. O próprio gateway de nuvem é nem um sistema de controlo de dispositivos nem um processamento ou um local de armazenamento para dados de dispositivo as instalações de interface com o gateway de nuvem. A zona de gateway de nuvem inclui o gateway de nuvem, juntamente com todos os gateways de campo e dispositivos direta ou indiretamente ligados ao mesmo. O limite da zona é uma área de superfície distinta em que todas as entidades externas comunicam através de.

### <a name="the-services-zone"></a>A zona de serviços
"Serviço" está definido para este contexto como qualquer componente de software ou o módulo é interfacing com dispositivos através de um gateway de campo ou na nuvem para recolha de dados e análise, bem como para o comando e controlo.  Os serviços são mediators. Estes agirem em sua identidade para gateways e outros subsistemas, armazenarem e analisam dados, forma autónoma emitir comandos para dispositivos com base nas informações de dados ou de agendas e expõem as informações e controlam capacidades aos utilizadores finais autorizados.

### <a name="information-devices-vs-special-purpose-devices"></a>Dispositivos de informações vs dispositivos com objetivos especiais
PCs, telemóveis e tablets são principalmente os dispositivos de informações interativo. Telemóveis e tablets explicitamente estão otimizados em torno maximizando a duração da bateria. São, de preferência desativar parcialmente quando não imediatamente a interagir com uma pessoa ou quando não fornecer serviços como o reprodução de música ou ao servir pelos respetivos proprietário para uma localização específica. Numa perspetiva de sistemas, estes dispositivos de tecnologia de informações principalmente atuam como proxies para as pessoas. São "actuators pessoas" sugerindo ações e "sensores de pessoas" recolha de entrada. 

Dispositivos com objetivos especiais, de sensores de temperatura simples para as linhas de produção de fábrica complexas com milhares de componentes dentro deles, são diferentes. Estes dispositivos estão abrangidas pelo âmbito muito mais no objetivo e mesmo que fornecem algumas interface de utilizador, amplamente estão confinadas a interfacing com ou ser integradas nos recursos no mundo físico. Estes medem e comunicam circunstâncias ambientais, ative valves, controlam servos, alarmes de som, mudar lights e realizar muitas outras tarefas. Ajudam a trabalhar para o qual um dispositivo de informações é demasiado genérico, demasiado caro, demasiado grande ou demasiado brittle. O objetivo concreto dita imediatamente respetiva estrutura técnica como também a atribuição monetário disponível para produção e de operação agendada duração. A combinação destes dois fatores chaves restringe a computação disponíveis de atribuição de energia, os requisitos de espaço físico e, consequentemente, armazenamento disponível, de operacional e capacidades de segurança.  

Se algo "ficar errado" com dispositivos controllable automatizados ou remotos, por exemplo, defeitos físicos ou lógica controlo defeitos willful intrusões não autorizadas e manipulação. As muitas de produção podem ser destruídas, edifícios podem estar looted ou gravados para baixo e pessoas poderão die injured ou até mesmo. Isto é, obviamente, uma classe todo diferente de danos que alguém maxing fora do limite de um cartão crédito roubado. A barra de segurança para dispositivos que tornam coisas mover e também para dados de sensores que eventualmente resulta em comandos que causam coisas mover, tem de ser superior em qualquer cenário de banca de comércio eletrónico ou. 

### <a name="device-control-and-device-data-interactions"></a>Controlo de dispositivo e as interações de dados do dispositivo
Dispositivos com objetivos especiais ligados tem um número significativo de potenciais áreas de interação superfície e padrões de interação, todos os de que tem de ser considerados para fornecer uma arquitetura para proteger o acesso digital para esses dispositivos. O termo "acesso digital" é utilizado aqui para distinguir entre quaisquer operações que são executadas através da interação do dispositivo direta onde a segurança de acesso é fornecida através de controlo de acesso físico. Por exemplo, colocar o dispositivo para uma sala de um bloqueio na porta. Enquanto o acesso físico não pode ser negado utilizando o software e hardware, medidas podem ser tomadas para impedir o acesso físico à esquerda para interferências de sistema. 

Como podemos explorar os padrões de interação, veremos "controlo de dispositivos" e "dados de dispositivo" com o mesmo nível de atenção ao modelação de ameaça. "Controlo de dispositivo" pode ser classificado como todas as informações que são fornecidas para um dispositivo por quaisquer terceiros com o objetivo de alterar ou que influencia as respetivo comportamento para o estado ou o estado do seu ambiente. "Dados de dispositivo" podem ser classificados como todas as informações que emite um dispositivo para qualquer outro interveniente sobre o estado e o estado do respetivo ambiente observado. 

## <a name="threat-modeling-the-azure-iot-reference-architecture"></a>A arquitetura de referência do IoT do Azure de modelação de ameaça
A Microsoft utiliza o framework descrito acima para fazer threat modeling para o Azure IoT. A secção abaixo, por conseguinte, utilizamos o exemplo concreto de arquitetura de referência do IoT do Azure para demonstrar como refletir sobre a ameaça de modelação de IoT e como resolver as ameaças identificadas. No nosso caso, identificámos quatro áreas principais do foco:

* Dispositivos e as origens de dados
* Transporte de dados
* Dispositivo e o processamento de eventos, e
* Apresentação

![Para o Azure IoT de modelação de ameaça](media/iot-security-architecture/iot-security-architecture-fig2.png) 

O diagrama a seguir fornece uma visão simplificada da Microsoft arquitetura do IoT através de um modelo de diagrama de fluxo de dados que é utilizado pela ferramenta de modelação de ameaça para Microsoft:

![IoT do Azure utilizando a ferramenta de modelação de ameaça do MS de modelação de ameaça](media/iot-security-architecture/iot-security-architecture-fig3.png)

É importante ter em atenção que a arquitetura separa as capacidades do dispositivo e o gateway. Isto permite que o utilizador ao tirar partido dos dispositivos de gateway que são mais seguros: são capazes de comunicar com o gateway de nuvem utilizando protocolos segurados, que requer, normalmente, o processamento superior dos custos gerais que um dispositivo nativo - por exemplo, um thermostat - podiam fornecer em as suas próprias. A zona de serviços do Azure, partimos do pressuposto que o Gateway de nuvem é representado pelo serviço de IoT Hub do Azure.

### <a name="device-and-data-sourcesdata-transport"></a>Origens/dados de dispositivos e dados de transporte
Esta secção explicar a arquitetura descrita acima, através da lente da modelação de ameaça e fornece uma descrição geral de como podemos envolvido algumas das preocupações inerentes. Iremos focar-nos elementos principais de um modelo de ameaça:

* Processos (as nossos controlo e a itens externas)
* Comunicação (também denominada de fluxos de dados)
* Armazenamento (também denominado arquivos de dados)

#### <a name="processes"></a>Processos
Em cada uma das categorias descritas na arquitetura do IoT do Azure, vamos tentar mitigar um número de ameaças diferentes em diferentes fases/informações de dados existe no: processo, a comunicação e o armazenamento. Abaixo que lhe damos uma descrição geral das mais comuns para a categoria "processo", seguido de uma descrição geral de como estas foi ser melhor mitigadas: 

**(S) de spoofing**: um atacante poderá extrair material de chaves criptográfica a partir de um dispositivo, ao nível do software ou hardware e, subsequentemente, aceder ao sistema com um dispositivo físico ou virtual diferente com a identidade do dispositivo o material de chaves já foi tomado do. Uma boa ilustração é remoto controlos que pode ativar qualquer TV e que estejam ferramentas prankster populares.

**Recusa de serviço (D)**: um dispositivo pode ser composto incapacidade de funcionar ou comunicar por interferir com frequências de botões de opção ou cablagem corte. Por exemplo, uma câmaras de vigilância que tinha a ligação de rede ou de energia intencionalmente knocked não comunicarão dados, de todo.

**Adulteração (T)**: um atacante pode detida ou parcialmente substituir o software em execução no dispositivo, potencialmente, permitindo o software substituído tirar partido de genuína identidade do dispositivo se o material de chaves ou instalações de criptografia que contém a chave materiais estavam disponíveis para o programa ilícito. Por exemplo, um atacante pode tirar partido extraído material de chaves para intercetar e suprimir os dados do dispositivo no caminho de comunicação e substituí-lo com dados falsos, que são autenticados com o material de chave roubado.

**Divulgação de informações (I)**: se o dispositivo estiver a executar software manipulado, esse software manipulated potencialmente foi o fuga de dados para entidades não autorizados. Por exemplo, um atacante pode tirar partido extraído material de chaves ao inserir em si no caminho de comunicação entre o dispositivo e um gateway de campo ou controlador ou gateway de nuvem para siphon desativar informações.

**Elevação de privilégio (E)**: pode ser forçado um dispositivo que suporta a função específica para fazer outra coisa. Por exemplo, um valve que programado para abrir a meio de forma pode ser induzido até abrir.

| **Componente** | **Ameaças** | **Atenuação** | **Risco** | **Implementação** |
| --- | --- | --- | --- | --- |
| Dispositivo |S |Atribuição de identidade para o dispositivo e autenticar o dispositivo |Substituir o dispositivo ou parte do dispositivo com alguns outros dispositivos. Como podemos saber que estão a comunicar no dispositivo correto? |Autenticar o dispositivo, utilizar IPSec ou o Transport Layer Security (TLS). Infraestrutura deve suportar a utilização de chave pré-partilhada (PSK) nesses dispositivos que não é possível processar a criptografia completa assimétrica. Tirar partido do Azure AD, [OAuth](http://www.rfc-editor.org/in-notes/internet-drafts/draft-ietf-ace-oauth-authz-01.txt) |
| TRID |Aplica tamperproof mecanismos para o dispositivo por exemplo, tornando muito difícil para impossíveis extrair as chaves e outro material de criptografia do dispositivo. |O risco é se alguém a adulteração é o dispositivo (interferências físicos). Como são, certifique-se, que o dispositivo não adulterado. |A mitigação mais eficaz é uma capacidade de module (TPM) fidedigna plataforma que permite armazenar chaves numa especial no chip circuitry, partir da qual as chaves não não possível ler, mas só podem ser utilizadas para operações de criptografia que utilizam a chave, mas nunca divulgar a chave. Encriptação de memória do dispositivo. Gestão de chaves para o dispositivo. O código de assinatura. | |
| E |Com o controlo de acesso do dispositivo. Esquema de autorização. |Se o dispositivo permite para ações individuais ser executada com base em comandos de uma origem externa ou sensores mesmo comprometidos, isso permitirá ataque executar operações não caso contrário, acessível. |Ter o esquema de autorização para o dispositivo | |
| Gateway de campo |S |Autenticar o gateway de campo para o Gateway de nuvem (cert com base, PSK, afirmação com base,..) |Se alguém pode falsificar Gateway de campo, em seguida,-lo pode apresentar-se automaticamente como qualquer dispositivo. |TLS RSA/PSK, IPSec, [RFC 4279](https://tools.ietf.org/html/rfc4279). As mesmas preocupações de armazenamento e do atestado de chaves dos dispositivos na geral – melhor caso é utilizar o TPM. Extensão do 6LowPAN para IPSec suportar a redes de Sensor sem fios (WSN). |
| TRID |Proteger o Gateway de campo contra adulteração (TPM)? |O spoofing de ataques enganar a pensar de gateway de nuvem que é falar com gateway de campo pode resultar em divulgação de informações e a adulteração de dados |Do memória encriptação, TPM, a autenticação. | |
| E |Mecanismo de controlo de acesso para o Gateway de campo | | | |

Seguem-se alguns exemplos de ameaças nesta categoria:

Spoofing: Um intruso poderá extrair material de chaves criptográfica de um dispositivo, ou no software ou nível de hardware e, subsequentemente, acesso que já foi tomado o sistema com um dispositivo físico ou virtual diferente com a identidade do dispositivo o material de chaves do.

**Recusa de serviço**: um dispositivo pode ser composto incapacidade de funcionar ou comunicar por interferir com frequências de botões de opção ou cablagem corte. Por exemplo, uma câmaras de vigilância que tinha a ligação de rede ou de energia intencionalmente knocked não comunicarão dados, de todo.

**Adulteração**: um atacante pode detida ou parcialmente substituir o software em execução no dispositivo, potencialmente, permitindo o software substituído tirar partido de genuína identidade do dispositivo se o material de chaves ou instalações de criptografia que contém a chave materiais estavam disponíveis para o programa ilícito.

**Adulteração**: uma câmaras de vigilância que está a ser mostrada uma fotografia espetro visível um hallway vazio foi diversificada de uma fotografia de um hallway deste tipo. Um sensor smoke ou fire foi reporting alguém que contém um mais ligeira sob a mesma. Em ambos os casos, o dispositivo pode ser executado tecnicamente totalmente fidedigno para o sistema, mas irá reportar manipular informações.

**Adulteração**: um atacante pode tirar partido extraído material de chaves para intercetar e suprimir os dados do dispositivo no caminho de comunicação e substituí-lo com dados falsos, que são autenticados com o material de chave roubado.

**Adulteração**: um intruso poderá completamente ou parcialmente substituir o software em execução no dispositivo, potencialmente, permitindo que o software substituído tirar partido de genuína identidade do dispositivo se o material de chaves ou instalações de criptografia que está a reter chaves materiais estavam disponíveis para o programa ilícito.

**Divulgação de informações**: se o dispositivo estiver a executar software manipulado, esse software manipulated potencialmente foi o fuga de dados para entidades não autorizados.

**Divulgação de informações**: um atacante pode tirar partido extraído material de chaves ao inserir em si no caminho de comunicação entre o gateway do dispositivo e um controlador ou o campo ou gateway de nuvem para siphon desativar informações.

**Recusa de serviço**: O dispositivo pode ser desativado ou ativado num modo em que a comunicação não é possível (que é intencional no número de máquinas industriais).

**Adulteração**: O dispositivo pode ser reconfigurado para funcionar num Estado desconhecido para o sistema de controlo (fora parâmetros calibration conhecidos) e, por conseguinte, forneça dados que podem ser mal interpretados

**Elevação de privilégios**: pode ser forçado um dispositivo que suporta a função específica para fazer outra coisa. Por exemplo, um valve que programado para abrir a meio de forma pode ser induzido até abrir.

**Recusa de serviço**: O dispositivo pode ser ativado num Estado em que a comunicação não seja possível.

**Adulteração**: O dispositivo pode ser reconfigurado para funcionar num Estado desconhecido para o sistema de controlo (fora parâmetros calibration conhecidos) e, por conseguinte, forneça dados que podem ser mal interpretados.

**Spoofing/violação/rejeição**: Se não estão protegidos (que raramente é o caso de consumidor remoto controlos) um atacante pode manipular o estado de um dispositivo de forma anónima. Uma boa ilustração é remoto controlos que pode ativar qualquer TV e que estejam ferramentas prankster populares.

#### <a name="communication"></a>Comunicação
Ameaças à volta do caminho de comunicação entre dispositivos, dispositivos e gateways de campo e gateway de nuvem e de dispositivos. A tabela abaixo tem algumas orientações à volta de sockets abrir o dispositivo/VPN:

| **Componente** | **Ameaças** | **Atenuação** | **Risco** | **Implementação** |
| --- | --- | --- | --- | --- |
| Dispositivo IoT Hub |TID |(D) TLS (PSK/RSA) para encriptar o tráfego |Escutas ou interferir a comunicação entre o dispositivo e o gateway |Segurança ao nível do protocolo. Com protocolos personalizados, precisamos de descobrir como a protegê-los. Na maioria dos casos, a comunicação ocorre do dispositivo ao IoT Hub (dispositivo inicia a ligação). |
| Dispositivo do dispositivo |TID |(D) TLS (PSK/RSA) para encriptar o tráfego. |Leitura de dados em trânsito entre dispositivos. Adulteração dos dados. A sobrecarga do dispositivo com novas ligações |Segurança ao nível do protocolo (MQTT/AMQP/HTTP/CoAP. Com protocolos personalizados, precisamos de descobrir como a protegê-los. A mitigação para a ameaça de linha é elemento dispositivos através de um gateway de nuvem ou campo e que act apenas como clientes para a rede. O peering pode resultar numa ligação direta entre os elementos de rede após ter sido mediadas pelo gateway |
| Dispositivo de entidade externa |TID |O emparelhamento forte da entidade externa para o dispositivo |Escutas a ligação para o dispositivo. Interferências a comunicação com o dispositivo |O emparelhamento com segurança a entidade externa para o dispositivo LE NFC/Bluetooth. Controlar o painel operacional do dispositivo (físico) |
| Gateway de nuvem do Gateway de campo |TID |TLS (PSK/RSA) para encriptar o tráfego. |Escutas ou interferir a comunicação entre o dispositivo e o gateway |Segurança ao nível do protocolo (MQTT/AMQP/HTTP/CoAP). Com protocolos personalizados, precisamos de descobrir como a protegê-los. |
| Gateway de nuvem do dispositivo |TID |TLS (PSK/RSA) para encriptar o tráfego. |Escutas ou interferir a comunicação entre o dispositivo e o gateway |Segurança ao nível do protocolo (MQTT/AMQP/HTTP/CoAP). Com protocolos personalizados, precisamos de descobrir como a protegê-los. |

Seguem-se alguns exemplos de ameaças nesta categoria:

**Recusa de serviço**: restrita dispositivos são, geralmente, em ameaça DoS quando estes ativamente escutam ligações de entrada ou não solicitados datagramas numa rede, porque um atacante pode abrir várias ligações em paralelo e não-los de serviço ou de serviço -los muito lentamente, ou o dispositivo pode ser flooded com tráfego não solicitado. Em ambos os casos, o dispositivo de forma eficaz pode ser composto inoperáveis na rede.

**Spoofing, divulgação de informações**: dispositivos restrita e dispositivos com objetivos especiais tem muitas vezes, instalações de segurança de um-para-all como palavra-passe ou proteção de PIN ou detida dependem do confiadoras da rede, o que significa que irão conceder acesso ao informações quando um dispositivo na mesma rede, e essa rede muitas vezes, só está protegida por uma chave partilhada. Isto significa que, quando o segredo partilhado para o dispositivo ou rede das é divulgado, é possível controlar o dispositivo ou observar os dados emitidos do dispositivo.  

**Spoofing**: um intruso poderá intercetar ou parcialmente substituir a difusão e falsificar; o originador (man no meio)

**Adulteração**: um intruso poderá intercetar ou parcialmente substituir a difusão e enviar informações falsas 

**Divulgação de informações:** um atacante pode eavesdrop na difusão e obter informações sem autorização **Denial of Service:** um atacante pode jam o sinal de difusão e distribuição de informações de negação

#### <a name="storage"></a>Armazenamento
Todos os gateway de dispositivo e o campo tem alguma forma de armazenamento (temporário para colocação de dados, armazenamento de imagem do sistema operativo (SO)).

| **Componente** | **Ameaças** | **Atenuação** | **Risco** | **Implementação** |
| --- | --- | --- | --- | --- |
| Dispositivo de armazenamento |TRID |Encriptação de armazenamento, os registos de assinatura |Ler dados a partir do armazenamento (dados PII), adulteração dos dados de telemetria. Adulteração colocados em fila ou colocada em cache os dados de controlo de comando. Adulteração dos pacotes de atualização de firmware ou de configuração ao colocar em cache ou colocada em fila localmente pode levar a componentes de SO e/ou sistema que está a ser comprometidos |Encriptação, o código de autenticação de mensagem (MAC) ou a assinatura digital. Em que o controlo de acesso segura possível através de acesso a recursos controlar listas (ACLs) ou permissões. |
| Imagem de SO do dispositivo |TRID | |Adulteração com um SO / substituir os componentes de SO |Partição de SO só de leitura, iniciada a imagem do SO, encriptação |
| Armazenamento de Gateway de campo (colocação em fila os dados) |TRID |Encriptação de armazenamento, os registos de assinatura |Ler dados a partir do armazenamento (dados PII), adulteração dos dados de telemetria, adulteração colocados em fila ou colocada em cache os dados de controlo de comando. Adulteração dos pacotes de atualização de firmware ou de configuração (destinados a dispositivos ou gateway de campo) ao colocar em cache ou colocada em fila localmente pode levar a componentes de SO e/ou sistema que está a ser comprometidos |BitLocker |
| Imagem do SO do Gateway de campo |TRID | |Adulteração com um SO / substituir os componentes de SO |Partição de SO só de leitura, iniciada a imagem do SO, encriptação |

### <a name="device-and-event-processingcloud-gateway-zone"></a>Zona de gateway de processamento/nuvem e eventos de dispositivos
Um gateway de nuvem é o sistema permite a comunicação remota de origem e de dispositivos ou gateways de campo de vários sites diferentes em espaço rede pública, normalmente, para um controlo baseado na nuvem e de sistema de análise de dados, Federação destes sistemas. Em alguns casos, um gateway de nuvem poderá imediatamente facilitam o acesso a dispositivos com objetivos especiais de terminals como tablets ou telemóveis. No contexto abordado aqui, "nuvem" destina-se para fazer referência a um sistema de processamento de dados dedicado que não está associado ao mesmo site que os dispositivos ligados ou gateways de campo e onde medidas operacionais impedem o acesso físico destino, mas não necessariamente a um " infraestrutura de nuvem pública".  Um gateway de nuvem, potencialmente, pode ser mapeado para uma sobreposição de Virtualização de rede para insulate o gateway de nuvem e de todos os respetivos dispositivos ligados ou gateways de campo de qualquer outro tráfego de rede. O próprio gateway de nuvem é nem um sistema de controlo de dispositivos nem um processamento ou um local de armazenamento para dados de dispositivo as instalações de interface com o gateway de nuvem. A zona de gateway de nuvem inclui o gateway de nuvem, juntamente com todos os gateways de campo e dispositivos direta ou indiretamente ligados ao mesmo.

Gateway de nuvem principalmente é peça incorporada personalizada de software em execução como um serviço com pontos finais expostos ao qual ligar dispositivos e gateway de campo. Como tal, têm de ser concebido com segurança em mente. Siga [SDL](http://www.microsoft.com/sdl) processos para estruturação e criação deste serviço. 

#### <a name="services-zone"></a>Zona de serviços
Um sistema de controlo (ou controlador) é uma solução de software que interaja com um dispositivo, ou um gateway de campo ou o gateway de nuvem com o objetivo de controlar um ou vários dispositivos e/ou para recolher e/ou armazenar e/ou analisar dados de dispositivo para a apresentação, ou efeitos de controlo subsequentes. Sistemas de controlo são as entidades apenas no âmbito deste debate que imediatamente pode facilitar a interação com pessoas. A exceção é intermédia analisa controlo física em dispositivos, como um comutador que permite que uma pessoa desativar o dispositivo ou outras propriedades de alteração, e para o qual não existe nenhum equivalente funcional que pode ser acedido digitalmente. 

Intermédia analisa controlo físicas são aquelas em que qualquer ordenação dos que rege lógica restringe a função da superfície de controlo físico de forma a que uma função equivalente pode ser iniciada remotamente ou conflitos de entrada com entrada remoto podem ser evitadas – este tipo intermediated controlo analisa concecionais estão ligadas a um sistema de controlo local que utiliza a mesma funcionalidade subjacente que qualquer outro sistema de controlo remoto que o dispositivo pode ser anexado a em paralelo. Principais ameaças à nuvem de informática pode ser lido no [Alliance de segurança de nuvem (CSA)](https://cloudsecurityalliance.org/research/top-threats/) página.

## <a name="additional-resources"></a>Recursos adicionais
Consulte os artigos seguintes para obter informações adicionais:

* [Ferramenta de modelação de ameaça do SDL](https://www.microsoft.com/sdl/adopt/threatmodeling.aspx)
* [Arquitetura de referência do IoT do Microsoft Azure](https://azure.microsoft.com/updates/microsoft-azure-iot-reference-architecture-available/)

## <a name="see-also"></a>Consultar também
Para obter mais informações sobre como proteger a sua solução de IoT, consulte [proteger a implementação de IoT][lnk-security-deployment].

Também pode explorar algumas das outras funcionalidades e capacidades das soluções pré-configuradas do IoT Suite:

* [Descrição geral de solução pré-configurada de manutenção preditiva][lnk-predictive-overview]
* [Perguntas mais frequentes sobre o IoT Suite][lnk-faq]

Pode ler sobre a segurança de IoT Hub na [controlar o acesso ao IoT Hub] [ lnk-devguide-security] no guia de programadores do IoT Hub.

[lnk-predictive-overview]: iot-suite-predictive-overview.md
[lnk-faq]: iot-suite-faq.md

[lnk-security-deployment]: iot-suite-security-deployment.md
[lnk-devguide-security]: ../iot-hub/iot-hub-devguide-security.md