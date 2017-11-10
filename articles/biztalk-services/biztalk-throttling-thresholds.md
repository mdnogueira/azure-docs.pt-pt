---
title: "Saiba mais sobre limitação nos BizTalk Services | Microsoft Docs"
description: "Saiba mais sobre os limiares de limitação e resultante comportamentos de tempo de execução dos BizTalk Services. Limitação é com base na utilização da memória e o número de mensagens. MABS, WABS"
services: biztalk-services
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: f6663cf2-cda4-4bac-855e-27d2ad5c4fa4
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: 39fc5ef36bb581c3a81c9948fda048f6cb75eb7e
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2017
---
# <a name="biztalk-services-throttling"></a>BizTalk Services: Limitação

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

BizTalk Services do Azure implementa o serviço limitação com base nas duas condições: utilização de memória e o número de processamento de mensagens em simultâneo. Este tópico lista os limiares de limitação e descreve o comportamento de tempo de execução quando ocorre uma condição de limitação.

## <a name="throttling-thresholds"></a>Limiares de limitação
A tabela seguinte apresenta uma lista de limitação de origem e de limiares:

|  | Descrição | Limiar inferior | Limiar superior |
| --- | --- | --- | --- |
| Memória |% de sistema total disponível de memória/PageFileBytes. <p><p>Total PageFileBytes disponível é aproximadamente 2 vezes a RAM do sistema. |60% |70% |
| Processamento de mensagens |Número de mensagens em fila de processamento em simultâneo |40 * número de núcleos |100 * número de núcleos |

Quando for atingido um limiar elevado, o BizTalk Services do Azure começa a limitação. Limitação interrompe quando for atingido o limite inferior. Por exemplo, o serviço está a utilizar 65% de memória de sistema. Nesta situação, o serviço não limitação por hardware. O serviço é iniciado utilizando a 70% de memória de sistema. Nesta situação, o serviço acelera e continua a limitação até que o serviço utiliza 60% de memória de sistema (limiar inferior).

BizTalk Services do Azure controla o estado de limitação (estado normal vs. Estado otimizado) e a duração de limitação.

## <a name="runtime-behavior"></a>Comportamento de tempo de execução
Quando BizTalk Services do Azure introduz um Estado de limitação, ocorre o seguinte:

* Limitação é por instância de função. Por exemplo:<br/>
  RoleInstanceA é a limitação. RoleInstanceB não é a limitação. Nesta situação, as mensagens no RoleInstanceB são processadas como esperado. Mensagens no RoleInstanceA são eliminadas e falharam com o seguinte erro:<br/><br/>
  **Servidor está ocupado. Tente novamente.**<br/><br/>
* Quaisquer origens de extração não consultar ou transferir uma mensagem. Por exemplo:<br/>
  Um pipeline obtém mensagens de uma origem externa de FTP. Obtém a instância de função ao executar a solicitação para um Estado de limitação. Nesta situação, o pipeline para transferir mensagens adicionais até que a instância de função deixa de limitação.
* É enviada uma resposta para o cliente para que o cliente pode submeter novamente a mensagem.
* Tem de aguardar até a limitação está resolvida. Especificamente, tem de aguardar até for atingido o limite inferior.

## <a name="important-notes"></a>Notas importantes
* Não é possível desativar a limitação.
* Não é possível modificar a limitação de limiares.
* Limitação é implementada em todo o sistema.
* O servidor de base de dados SQL do Azure também tem a limitação incorporada.

## <a name="additional-azure-biztalk-services-topics"></a>Tópicos adicionais do BizTalk Services do Azure
* [Instalar os BizTalk Services do Azure SDK](http://go.microsoft.com/fwlink/p/?LinkID=241589)<br/>
* [Tutoriais: BizTalk Services do Azure](http://go.microsoft.com/fwlink/p/?LinkID=236944)<br/>
* [Como posso começar a utilizar o SDK dos Serviços BizTalk do Azure](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
* [BizTalk Services do Azure](http://go.microsoft.com/fwlink/p/?LinkID=303664)<br/>

## <a name="see-also"></a>Veja Também
* [Os BizTalk Services: Programador, básicas, Standard e Premium gráfico de edições](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
* [Serviços BizTalk: Gráfico de Estado de Aprovisionamento](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
* [Serviços BizTalk: Separadores Dashboard, Monitorizar e Dimensionar](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
* [Serviços BizTalk: Cópia de segurança e Restauro](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
* [Serviços BizTalk: Nome e Chave do Emissor](http://go.microsoft.com/fwlink/p/?LinkID=303941)<br/>

