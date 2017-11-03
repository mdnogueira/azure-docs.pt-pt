---
title: "Inspeção de pacotes com observador de rede do Azure | Microsoft Docs"
description: "Este artigo descreve como utilizar o observador de rede para efetuar a inspeção de pacotes aprofundada recolhida a partir de uma VM"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 7b907d00-9c35-40f5-a61e-beb7b782276f
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 1ad6ca4abe73336ce9ce3539fdaf2a9d7dd23fa6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="packet-inspection-with-azure-network-watcher"></a>Inspeção de pacotes com observador de rede do Azure

Utilizar a funcionalidade de captura de pacotes do observador de rede, pode iniciar e gerir sessões de capturas na suas VMs do Azure no portal, PowerShell, CLI e através de programação através do SDK e a REST API. Captura de pacotes permite-lhe para cenários de endereço que necessitam de dados ao nível do pacote, fornecendo as informações num formato prontamente utilizável. Tirar partido das ferramentas livremente disponíveis para inspecionar os dados, pode examinar comunicações enviadas de e para as VMs e obter informações sobre o tráfego de rede. Incluem algumas utilizações de exemplo de dados de captura de pacotes: investigar problemas de rede ou aplicação, detetar tentativas de utilização indevida e intrusão de rede ou manter a conformidade regulamentar. Neste artigo, mostramos como abrir um ficheiro de captura de pacotes fornecido pelo observador de rede utilizando uma ferramenta open source para populares. Fornecemos também exemplos que mostra como calcular uma latência de ligação, identificar tráfego anormal e examine as estatísticas de rede.

## <a name="before-you-begin"></a>Antes de começar

Este artigo passa através de alguns cenários previamente configurados uma captura de pacotes que foi anteriormente executada com êxito. Estes cenários mostram capacidades que podem ser acedidas ao rever uma captura de pacotes. Este cenário utiliza [WireShark](https://www.wireshark.org/) inspecionar a captura de pacotes.

Este cenário pressupõe que já tenha sido executada uma captura de pacotes numa máquina virtual. Para saber como criar a visita de captura de pacotes [capturas de pacotes de gerir com o portal](network-watcher-packet-capture-manage-portal.md) ou com REST, visitando [gerir capturas de pacotes com a REST API](network-watcher-packet-capture-manage-rest.md).

## <a name="scenario"></a>Cenário

Neste cenário, poderá:

* Reveja uma captura de pacotes

## <a name="calculate-network-latency"></a>Calcular a latência de rede

Neste cenário, mostramos como ver o tempo de ida e volta inicial (RTT) de uma conversação de protocolo de controlo de transmissão (TCP) ocorrer entre dois pontos finais.

Quando é estabelecida uma ligação de TCP, os três primeiros pacotes enviados na ligação de seguem um padrão que normalmente denominado como o handshake de três vias. Ao examinar os primeiros dois pacotes enviados neste handshake, um pedido inicial do cliente e uma resposta do servidor, iremos pode calcular a latência quando esta ligação foi estabelecida. Esta latência é referida como o tempo de ida e volta (RTT). Para obter mais informações sobre o protocolo TCP e o handshake de três vias consulte o seguinte recurso. https://support.microsoft.com/en-US/Help/172983/Explanation-of-the-Three-Way-Handshake-via-TCP-IP

### <a name="step-1"></a>Passo 1

Inicie o WireShark

### <a name="step-2"></a>Passo 2

Carga de **.cap** ficheiro a partir do seu captura de pacotes. Este ficheiro pode ser encontrado no blob foi guardado no nosso localmente na máquina virtual, dependendo de como tiver configurado.

### <a name="step-3"></a>Passo 3

Para ver o tempo de ida e volta (RTT) inicial no conversações de TCP, iremos será apenas possível observar primeiro dois pacotes envolvidos no handshake TCP. Vamos utilizar dois pacotes no handshake de três vias, que são [SIN], [SIN, ACK] pacotes. Estes são denominados para sinalizadores definidas no cabeçalho do TCP. O último pacote no handshake, o pacote [ACK], não será utilizado neste cenário. O pacote [SIN] é enviado pelo cliente. Quando é recebido o servidor envia o pacote [ACK] como uma confirmação de receção de SIN do cliente. Tirar partido do facto de que a resposta do servidor requer pouco sobrecarga, iremos calcular o RTT subtraindo o tempo [SIN, ACK] pacote foi recebido pelo cliente dentro do tempo [SIN] o pacote foi enviado pelo cliente.

Este valor com o WireShark é calculado para-nos.

Para ver mais facilmente os dois primeiros pacotes no handshake de três vias TCP, iremos irá utilizar a capacidade de filtragem fornecida pelo WireShark.

Para aplicar o filtro na WireShark, expanda o segmento de "Protocolo de controlo de transmissão" de um pacote de [SIN] no seu captura e examinar os sinalizadores definidos no cabeçalho do TCP.

Uma vez que estamos a procura para filtrar todas as [SIN] e [SIN, ACK] pacotes, sob sinalizadores cofirm que o bit sin está definido como 1, em seguida, clique direito no bit sin -> aplicar como filtro -> selecionados.

![figura 7][7]

### <a name="step-4"></a>Passo 4

Agora que tem filtrados a janela para ver apenas os pacotes com o conjunto de bits [SIN], pode selecionar facilmente conversações que está interessado para ver o RTT inicial. Uma forma simples para ver o RTT na WireShark basta clicar em dropdown marcado analysis "SEQ/ACK". Em seguida, verá RTT apresentado. Neste caso, o RTT foi 0.0022114 segundos ou 2.211 ms.

![figura 8][8]

## <a name="unwanted-protocols"></a>Protocolos indesejáveis

Pode ter várias aplicações em execução numa instância de máquina virtual que implementou no Azure. Muitas destas aplicações comunicam através da rede, talvez sem a sua permissão explícita. Captura de pacotes a utilizar para armazenar a comunicação de rede, pode investigar como aplicações estão a comunicar na rede e procure eventuais problemas.

Neste exemplo, vamos rever anterior foi executada a captura de pacotes indesejável protocolos que possam indicar a comunicação não autorizada de uma aplicação em execução no seu computador.

### <a name="step-1"></a>Passo 1

Com a mesma captura no cenário anterior, clique **estatísticas** > **hierarquia de protocolo**

![menu de hierarquia de protocolo][2]

É apresentada a janela de hierarquia do protocolo. Esta vista fornece uma lista de todos os protocolos que estavam a ser utilizadas durante a sessão de captura e o número de pacotes transmitidos e recebidas utilizando os protocolos. Esta vista pode ser útil para localizar indesejável tráfego de rede na sua rede ou de máquinas virtuais.

![hierachy protocolo aberta][3]

Como pode ver na captura de ecrã seguinte, ocorreu tráfego utilizando o protocolo BitTorrent, que é utilizado para a partilha de ficheiros de ponto a ponto. Como um administrador não pretende ver BitTorrent tráfego nas específico máquinas virtuais. Agora tem em consideração este tráfego, pode remover o software de ponto a ponto instalado nesta máquina virtual, ou bloquear o tráfego através de grupos de segurança de rede ou uma Firewall. Além disso, pode optar por executar capturas de pacotes com base numa agenda, pelo que pode rever a utilização do protocolo nas suas máquinas virtuais regularmente. Para obter um exemplo sobre como automatizar tarefas de rede no azure, visite [monitorizem os recursos de rede com a automatização do azure](network-watcher-monitor-with-azure-automation.md)

## <a name="finding-top-destinations-and-ports"></a>Localizar destinos principais e portas

Compreender os tipos de tráfego, os pontos finais e as portas comunicadas através de é um importante quando monitorização ou a resolução de problemas de aplicações e recursos na sua rede. Utilizar um ficheiro de captura de pacotes de acima, podemos pode rapidamente saber os nosso VM está a comunicar com os destinos superiores e as portas que está a ser utilizadas.

### <a name="step-1"></a>Passo 1

Com a mesma captura no cenário anterior, clique **estatísticas** > **estatísticas de IPv4** > **destinos e portas**

![janela de captura de pacotes][4]

### <a name="step-2"></a>Passo 2

Como podemos examine os resultados de que uma linha representa enviados, existem várias ligações na porta 111. A porta mais utilizada foi 3389, que é o ambiente de trabalho remoto e o restante é portas dinâmicas de RPC.

Apesar deste tráfego pode significar que nada, é uma porta que foi utilizada para várias ligações e é desconhecido para o administrador.

![figura 5][5]

### <a name="step-3"></a>Passo 3

Agora que Determinámos uma saída da porta local, pode filtrar a nossa captura com base na porta.

O filtro neste cenário seria:

```
tcp.port == 111
```

Introduza o texto de filtro de acima na caixa de texto de filtro e clique em enter.

![figura 6][6]

Na lista de resultados, é possível ver que todo o tráfego for proveniente de uma máquina virtual local na mesma sub-rede. Se ainda não compreendemos por que motivo está a ocorrer este tráfego, mais iremos pode inspecionar os pacotes para determinar por que motivo é efetuar estas chamadas na porta 111. Com esta informação é pode tome as medidas adequadas.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as funcionalidades de diagnóstico do observador de rede, visitando [descrição geral da monitorização de rede do Azure](network-watcher-monitoring-overview.md)

[1]: ./media/network-watcher-deep-packet-inspection/figure1.png
[2]: ./media/network-watcher-deep-packet-inspection/figure2.png
[3]: ./media/network-watcher-deep-packet-inspection/figure3.png
[4]: ./media/network-watcher-deep-packet-inspection/figure4.png
[5]: ./media/network-watcher-deep-packet-inspection/figure5.png
[6]: ./media/network-watcher-deep-packet-inspection/figure6.png
[7]: ./media/network-watcher-deep-packet-inspection/figure7.png
[8]: ./media/network-watcher-deep-packet-inspection/figure8.png













