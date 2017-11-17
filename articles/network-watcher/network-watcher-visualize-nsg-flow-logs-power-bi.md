---
title: "Os registos de fluxo do grupo de segurança de rede de Azure visualizing com o Power BI | Microsoft Docs"
description: "Esta página descreve como visualizar registos de fluxo NSG com o Power BI."
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 1e4f95fa-f5f0-4e03-bc25-008fbfc4934c
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 19bd7ed4bab915d7918a192a046653666cfaa498
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2017
---
# <a name="visualizing-network-security-group-flow-logs-with-power-bi"></a>Registos de fluxo do grupo de segurança de rede visualizing com o Power BI

Os registos de fluxo de grupo de segurança de rede permitem-lhe ver informações sobre o tráfego IP de entrada e de saída nos grupos de segurança de rede. Estes fluir Mostrar registos fluxos de saída e entrados numa base por regra, o NIC o fluxo aplica-se a, informações de 5 cadeias de identificação sobre o fluxo (origem/destino IP, porta de origem/destino, protocolo), e se o tráfego foi permitido ou negado.

Pode ser difícil obter informações sobre os dados de registo de fluxo pesquisando manualmente os ficheiros de registo. Neste artigo, podemos fornecer uma solução para visualizar os registos de fluxo mais recentes e obter informações sobre o tráfego na sua rede.

## <a name="scenario"></a>Cenário

No cenário seguinte, mas ligar o ambiente de trabalho do Power BI para a conta de armazenamento que tiver configurado como o sink relativamente aos nossos dados NSG fluxo de registo. Depois de estamos a ligar a nossa conta do storage, o Power BI transfere e analisa os registos para fornecer uma representação visual de tráfego que é registado por grupos de segurança de rede.

Os visuais fornecidos no modelo, pode examinar a utilizar:

* Talkers superiores
* Dados de fluxo de série de tempo por decisão direção e regra
* Fluxos pelo endereço MAC de Interface de rede
* Fluxos por NSG e regra
* Fluxos por porta de destino

O modelo fornecido é editável por isso pode modificá-lo para adicionar novos dados, os visuais, ou editar as consultas de acordo com as suas necessidades.

## <a name="setup"></a>Configurar

Antes de começar, tem de ter rede segurança grupo fluxo o registo ativado num ou vários grupos de segurança de rede na sua conta. Para obter instruções sobre como ativar a segurança de rede flua registos, consulte o artigo seguinte: [introdução ao registo do fluxo de grupos de segurança de rede](network-watcher-nsg-flow-logging-overview.md).

Também tem de ter o cliente de ambiente de trabalho do Power BI instalado no seu computador e o espaço livre suficiente no seu computador para transferir e carregar os dados de registo que existe na sua conta do storage.

![Diagrama de Visio][1]

### <a name="steps"></a>Passos

1. Transfira e abra o modelo seguinte do Power BI na aplicação de ambiente de trabalho do Power BI [modelo os registos de fluxo de PowerBI de observador de rede](https://aka.ms/networkwatcherpowerbiflowlogstemplate)
1. Introduza os parâmetros de consulta necessários
    1. **StorageAccountName** – Especifica o nome da conta de armazenamento que contém os registos de fluxo NSG que gostaria para carregar e visualizar.
    1. **NumberOfLogFiles** – Especifica o número de ficheiros de registo que pretende transferir e visualizar no Power BI. Por exemplo, se for especificado 50, os 50 ficheiros de registo mais recentes. FF temos 2 NSGs ativada e configurada para enviar registos de fluxo NSG para esta conta, em seguida, podem ser visualizadas últimas 25 horas de registos.

    ![principal do Power BI][2]

1. Introduza a chave de acesso para a sua conta de armazenamento. Pode encontrar chaves de acesso válido ao navegar para a sua conta do storage no Azure portal e selecionar **chaves de acesso** no menu de definições. Clique em **Connect** aplicar as alterações.

    ![chaves de acesso][3]

    ![chave de acesso 2][4]

4.  Os registos são transferir e analisá-la e pode agora utilizar visuais previamente criados.

## <a name="understanding-the-visuals"></a>Noções sobre visuais

Fornecida no modelo são um conjunto de elementos visuais que ajuda a fazer sentido os NSG do fluxo de dados de registo. As imagens seguintes mostram um exemplo de dashboard do aspeto quando preenchida com dados. A seguir Vamos examinar cada visual em maior detalhe 

![Power BI][5]
 
A parte superior Talkers visual mostra os IPs que iniciou a maioria das ligações durante o período especificado. O tamanho das caixas corresponde ao número de ligações relativo. 

![toptalkers][6]

Os gráficos de série de tempo seguinte mostram o número de fluxos durante o período. O gráfico superior é segmentado pela direcção do fluxo e menor é segmentado pela decisão que tomou (permitir ou negar). Este visual, pode examinar as tendências de tráfego ao longo do tempo e detetar qualquer picos anormais ou recusar no tráfego ou segmentação do tráfego.

![flowsoverperiod][7]

A mostrar gráficos seguintes fluxos de interface de rede, com o limite superior segmentado por direcção do fluxo e menor por segmentados por decisão efetuada. Com esta informação, pode obter informações acerca da qual as VMs comunicados mais relativo para outras pessoas, e se o tráfego para uma VM específica está a ser permitido ou negado.

![flowspernic][8]

O gráfico de rodas anel seguinte mostra uma análise detalhada do fluxos por porta de destino. Com esta informação, pode ver as portas de destino mais comuns utilizadas dentro do período de tempo especificado.

![anel][9]

O seguinte gráfico de barras apresenta o fluxo por NSG e da regra. Com esta informação, pode ver os NSGs responsáveis pela maioria do tráfego e a divisão de tráfego de um NSG pela regra.

![barchart][10]
 
Os gráficos informativos seguintes apresentam informações sobre os NSGs presentes nos registos, o número de fluxos capturados durante o período e a data do registo mais antigo capturada. Estas informações dá-lhe uma ideia dos quais os NSGs estão a ser registado e o intervalo de datas dos fluxos.

![infochart1][11]

![infochart2][12]

Este modelo inclui as seguintes segmentações de dados para permitir-lhe ver apenas os dados que é mais interessado em. Pode filtrar os grupos de recursos, os NSGs e regras. Também pode filtrar as informações de 5 cadeias de identificação, decisão e o tempo que o registo foi escrito.

![segmentações de dados][13]

## <a name="conclusion"></a>Conclusão

Iremos mostrou, neste cenário, que, ao utilizar registos de fluxo de grupo de segurança de rede fornecidos por observador de rede e o Power BI, mas é possível visualizar e compreender o tráfego. Utilizando o modelo fornecido, o Power BI transfere os registos diretamente a partir do armazenamento e processa-los localmente. Tempo decorrido para carregar o modelo varia consoante o número de ficheiros pedida e tamanho total dos ficheiros transferidos.

Pode personalizar este modelo para as suas necessidades. Existem várias formas de várias que pode utilizar o Power BI com o fluxo de registos de grupo de segurança rede. 

## <a name="notes"></a>Notas

* Os registos por predefinição são armazenados no`https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/`

    * Se existem outros dados noutro diretório, as consultas de extração e processar os dados têm de ser modificados.

* O modelo fornecido não é recomendado para utilização com mais de 1 GB de registos.

* Se tiver uma grande quantidade de registos, recomendamos que estiver a investigar uma solução utilizando outro arquivo de dados como o Data Lake ou SQL server.

## <a name="next-steps"></a>Passos Seguintes

Saiba como visualizar os seus registos de fluxo NSG com a pilha de Elastick, visitando [registos de fluxo de visualizar NSG de observador de rede do Azure com ferramentas open source](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)

[1]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure1.png
[2]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure2.png
[3]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure3.png
[4]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure4.png
[5]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure5.png
[6]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure6.png
[7]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure7.png
[8]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure8.png
[9]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure9.png
[10]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure10.png
[11]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure11.png
[12]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure12.png
[13]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure13.png
