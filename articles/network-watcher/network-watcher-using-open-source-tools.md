---
title: "Visualizar padrões de tráfego de rede com o observador de rede do Azure e ferramentas open source | Microsoft Docs"
description: "Esta página descreve como utilizar a captura de pacotes do observador de rede com Capanalysis para visualizar os padrões de tráfego de e para as suas VMs."
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 936d881b-49f9-4798-8e45-d7185ec9fe89
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 61abda6053fe743e294f309df3a6e1041052ec6e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="visualize-network-traffic-patterns-to-and-from-your-vms-using-open-source-tools"></a>Visualizar os padrões de tráfego de rede de e para as suas VMs utilizando ferramentas open source

Capturas de pacotes contêm dados de rede que permitem efetuar rede forense e de inspeção de pacotes aprofundada. Existem abre-se muitas ferramentas de origem que pode utilizar para analisar as capturas de pacotes para obter informações sobre a sua rede. Uma ferramenta deste tipo é CapAnalysis, uma ferramenta de visualização de captura de pacotes de código aberto. Visualizar dados de captura de pacotes é uma forma útil rapidamente derivar conhecimentos aprofundados acerca do padrões e anomalias dentro da sua rede. Visualizações também fornecem um meio de partilhar estas informações de forma facilmente consumíveis.

Observador de rede do Azure fornece a capacidade para capturar estes dados valiosos, permitindo-lhe efetuar capturas de pacotes na sua rede. Neste artigo, fornecemos instruções sobre como visualizar e obter conhecimentos aprofundados sobre pacote captura com CapAnalysis observador de rede.

## <a name="scenario"></a>Cenário

Tiver uma aplicação web simples implementada numa VM no Azure a utilizar ferramentas open source para visualizar o tráfego de rede para identificar rapidamente os padrões de fluxo e qualquer anomalias possíveis. Com o observador de rede, pode obter uma captura de pacotes do seu ambiente de rede e armazene-o diretamente na sua conta de armazenamento. CapAnalysis, em seguida, pode inserir a captura de pacotes diretamente a partir do blob de armazenamento e visualizar o respetivo conteúdo.

![cenário][1]

## <a name="steps"></a>Passos

### <a name="install-capanalysis"></a>Instalar CapAnalysis

Para instalar CapAnalysis numa máquina virtual, pode consultar as instruções oficiais aqui https://www.capanalysis.net/ca/how-to-install-capanalysis.
Ordem CapAnalysis acedem remotamente aos, é necessário abrir a porta 9877 na sua VM ao adicionar uma nova regra de segurança de entrada. Para obter mais informações sobre como criar regras nos grupos de segurança de rede, consulte [criar regras num NSG existente](../virtual-network/virtual-networks-create-nsg-arm-pportal.md#create-rules-in-an-existing-nsg). Assim que a regra foi adicionada com êxito, deverá conseguir aceder CapAnalysis do`http://<PublicIP>:9877`

### <a name="use-azure-network-watcher-to-start-a-packet-capture-session"></a>Utilize o observador de rede do Azure para iniciar uma sessão de captura de pacotes

Observador de rede permite-lhe capturar pacotes para controlar o tráfego que entra e sai de uma máquina virtual. Pode consultar as instruções apresentadas em [capturas de pacotes de gerir com o observador de rede](network-watcher-packet-capture-manage-portal.md) para iniciar uma sessão de captura de pacotes. Nesta captura de pacotes pode ser armazenada no blob de armazenamento para ser acedido por CapAnalysis.

### <a name="upload-a-packet-capture-to-capanalysis"></a>Carregar uma captura de pacotes para CapAnalysis
Diretamente pode carregar uma captura de pacotes executada por observador de rede utilizando o separador "Importação de URL" e fornecendo uma hiperligação para o blob de armazenamento onde está armazenada a captura de pacotes.

Ao fornecer uma ligação para CapAnalysis, certifique-se a acrescentar um token SAS para o URL do blob de armazenamento.  Para tal, navegue para assinatura de acesso partilhado a partir da conta de armazenamento, designar as permissões permitidas e clique no botão gerar a SAS para criar um token. Em seguida, pode acrescentar este token SAS para o URL de blob de armazenamento de captura de pacotes.

O URL resultante será algo semelhante ao seguinte: http://storageaccount.blob.core.windows.net/container/location?addSASkeyhere


### <a name="analyzing-packet-captures"></a>Capturas de pacotes analisa

CapAnalysis oferece várias opções para visualizar a captura de pacotes, cada análise fornecer numa perspetiva de diferentes. Com estas resumos visual, pode compreender as tendências de tráfego de rede e rapidamente detetar qualquer atividade invulgar. Algumas destas funcionalidades são apresentadas na lista seguinte:

1. Tabelas de fluxo

    Esta tabela fornece a lista de fluxos de vários protocolos associados com o fluxo, bem como o IP de origem e de destino, o carimbo de hora associados com os fluxos e os dados de pacote.

    ![página de fluxo de capanalysis][5]

1. Descrição geral do protocolo

    Este painel permite-lhe ver rapidamente a distribuição de tráfego de rede através de vários protocolos e localizações geográficas.

    ![Descrição geral do protocolo de capanalysis][6]

1. Estatísticas

    Este painel permite-lhe ver estatísticas de tráfego de rede – bytes enviados e recebidos a partir de origem e destino IPs, fluxos para cada uma de origem e destino IPs, o protocolo utilizado para vários fluxos e a duração de fluxos.

    ![estatísticas de capanalysis][7]

1. geomap

    Este painel fornece-lhe uma vista de mapa de tráfego de rede, cores dimensionamento para o volume de tráfego de cada país. Pode selecionar países realçados para ver as estatísticas de fluxo adicionais, tais como a proporção de dados enviados e recebidos IPs desse país.

    ![geomap][8]

1. Filtros

    CapAnalysis fornece um conjunto de filtros para análise rápida de pacotes específicos. Por exemplo, pode optar por filtrar os dados pelo protocolo para obter informações específicas sobre esse subconjunto de tráfego.

    ![filtros][11]

    Visite [https://www.capanalysis.net/ca/#about](https://www.capanalysis.net/ca/#about) para saber mais sobre as capacidades de todos os CapAnalysis.

## <a name="conclusion"></a>Conclusão

Funcionalidade de captura de pacotes do observador de rede permite-lhe capturar os dados necessários para efetuar forenses de rede e compreender melhor o tráfego de rede. Neste cenário, iremos mostrou como capturas de pacotes do observador de rede podem facilmente ser integradas com ferramentas de visualização de open source. Ao utilizar ferramentas open source como CapAnalysis para visualizar as capturas de pacotes, pode executar a inspeção de pacotes aprofundada e identificar rapidamente as tendências dentro do seu tráfego de rede.

## <a name="next-steps"></a>Passos seguintes

Para mais informações sobre os registos de fluxo NSG, visite [os registos de NSG fluxo](network-watcher-nsg-flow-logging-overview.md)

Saiba como visualizar os registos de fluxo NSG com o Power BI, visitando [visualizar NSG flui registos com o Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
<!--Image references-->

[1]: ./media/network-watcher-using-open-source-tools/figure1.png
[2]: ./media/network-watcher-using-open-source-tools/figure2.png
[3]: ./media/network-watcher-using-open-source-tools/figure3.png
[4]: ./media/network-watcher-using-open-source-tools/figure4.png
[5]: ./media/network-watcher-using-open-source-tools/figure5.png
[6]: ./media/network-watcher-using-open-source-tools/figure6.png
[7]: ./media/network-watcher-using-open-source-tools/figure7.png
[8]: ./media/network-watcher-using-open-source-tools/figure8.png
[9]: ./media/network-watcher-using-open-source-tools/figure9.png
[10]: ./media/network-watcher-using-open-source-tools/figure10.png
[11]: ./media/network-watcher-using-open-source-tools/figure11.png
