---
title: "Replicação de dados no armazenamento do Azure | Microsoft Docs"
description: "Os dados na sua conta de armazenamento do Microsoft Azure são replicados para durabilidade e elevada disponibilidade. As opções de replicação incluem o armazenamento localmente redundante (LRS), o armazenamento com redundância de zona (ZRS), o armazenamento georredundante (GRS) e o armazenamento georredundante com acesso de leitura (RA-GRS)."
services: storage
documentationcenter: 
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 86bdb6d4-da59-4337-8375-2527b6bdf73f
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: tamram
ms.openlocfilehash: dbc81edd24ee714fbb173ed395a2f2fc91773fff
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="azure-storage-replication"></a>Replicação do Storage do Azure
Os dados na sua conta de armazenamento do Microsoft Azure são sempre replicados para garantir a durabilidade e a elevada disponibilidade. A replicação copia os dados, no mesmo centro de dados ou para um segundo centro de dados, consoante a opção de replicação que escolher. A replicação protege os dados e preserva o tempo ativo da sua aplicação em caso de falhas de hardware transitórias. Se os dados são replicados para um segundo Centro de dados, está protegida contra uma falha catastrófica na localização primária.

A replicação garante que a sua conta de armazenamento cumpre o [Contrato de Nível de Serviço (SLA) para o Armazenamento](https://azure.microsoft.com/support/legal/sla/storage/) mesmo em caso de falhas. Leia o SLA para obter mais informações sobre as garantias do Armazenamento do Azure quanto à durabilidade e à disponibilidade.

Quando cria uma conta de armazenamento, tem de selecionar uma das seguintes opções de replicação:

* [Armazenamento localmente redundante (LRS)](#locally-redundant-storage)
* [Armazenamento com redundância de zona (ZRS)](#zone-redundant-storage)
* [Armazenamento georredundante (GRS)](#geo-redundant-storage)
* [Armazenamento georredundante com acesso de leitura (RA-GRS)](#read-access-geo-redundant-storage)

Armazenamento georredundante com acesso de leitura (RA-GRS) é a opção predefinida quando cria uma conta de armazenamento.

A tabela seguinte fornece uma descrição geral rápida das diferenças entre LRS, ZRS, GRS e RA-GRS, enquanto cada tipo de replicação em mais detalhe de endereço seções subsequentes.

| Estratégia de replicação | LRS | ZRS | GRS | RA-GRS |
|:--- |:--- |:--- |:--- |:--- |
| Os dados são replicados em vários datacenters. |Não |Sim |Sim |Sim |
| Podem ler dados a partir de uma localização secundária, bem como a localização primária. |Não |Não |Não |Sim |
| Número de cópias dos dados mantidas em nós separados. |3 |3 |6 |6 |

Consulte [preços do Storage do Azure](https://azure.microsoft.com/pricing/details/storage/) para informações sobre preços para as opções de redundância diferentes.

> [!NOTE]
> Armazenamento Premium suporta armazenamento apenas localmente redundante (LRS). Para obter informações sobre o Premium Storage, consulte [Premium Storage: armazenamento de elevado desempenho para cargas de trabalho de Máquina Virtual de Azure](../../virtual-machines/windows/premium-storage.md).
>

## <a name="locally-redundant-storage"></a>Armazenamento localmente redundante
[!INCLUDE [storage-common-redundancy-LRS](../../../includes/storage-common-redundancy-LRS.md)]

## <a name="zone-redundant-storage"></a>Armazenamento com redundância de zona
Armazenamento com redundância de zona (ZRS) replica os dados de forma assíncrona entre centros de dados dentro de uma ou duas regiões, além de armazenar as três réplicas semelhantes ao LRS, deste modo, fornecendo uma durabilidade superior ao LRS. Os dados armazenados na ZRS são duráveis, mesmo se o datacenter primário está indisponível ou irrecuperáveis.
Os clientes que pretendem utilizar o ZRS deverá ter em atenção que:

* O ZRS só está disponível para blobs de blocos em contas do storage para fins gerais e é suportada apenas em versões de serviço de armazenamento 2014-02-14 e posterior.
* Uma vez que a replicação assíncrona envolve um atraso, na eventualidade de ocorrer um desastre local é possível que as alterações que ainda não tem sido replicadas para o secundário será perdidas se não não possível recuperar os dados a partir de principal.
* A réplica não pode estar disponível até que a Microsoft inicia a ativação pós-falha para o secundário.
* Contas ZRS não não possível converter mais tarde LRS ou GRS. Da mesma forma, uma conta existente do LRS ou GRS não pode ser convertida para uma conta ZRS.
* Contas ZRS não tiverem as métricas ou capacidade de registos.

## <a name="geo-redundant-storage"></a>Armazenamento georredundante
[!INCLUDE [storage-common-redundancy-GRS](../../../includes/storage-common-redundancy-GRS.md)]

## <a name="read-access-geo-redundant-storage"></a>Armazenamento georredundante com acesso de leitura
Armazenamento georredundante com acesso de leitura (RA-GRS) maximiza a disponibilidade para a sua conta de armazenamento, fornecendo acesso só de leitura aos dados na localização secundária, além de replicação em duas regiões fornecida pelo GRS.

Quando ativar o acesso só de leitura aos seus dados na região secundária, os dados estão disponíveis no ponto de final secundário, além de ponto final principal para a sua conta de armazenamento. O ponto final secundário é semelhante para o ponto final principal, mas acrescenta o sufixo `–secondary` ao nome da conta. Por exemplo, se o ponto final principal para o serviço Blob é `myaccount.blob.core.windows.net`, em seguida, o ponto final secundário é `myaccount-secondary.blob.core.windows.net`. As chaves de acesso para a sua conta de armazenamento são as mesmas para os pontos de finais primários e secundários.

Considerações:

* A aplicação tem de gerir o ponto final de interagir com ao utilizar o RA-GRS.
* Uma vez que a replicação assíncrona envolve um atraso, na eventualidade de ocorrer um desastre regional é possível que as alterações que ainda não tem sido replicadas para a região secundária serão perdidas se não não possível recuperar os dados da região primária.
* Se o Microsoft inicia a ativação pós-falha para a região secundária, será leu e acesso de escrita para que os dados após a ativação pós-falha foi concluída. Para obter mais informações, consulte [orientações de recuperação após desastre](../storage-disaster-recovery-guidance.md). 
* RA-GRS destina-se a fins de elevada disponibilidade. Para obter orientações sobre a escalabilidade, reveja o [lista de verificação de desempenho](../storage-performance-checklist.md).

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

<a id="howtochange"></a>
#### <a name="1-how-can-i-change-the-geo-replication-type-of-my-storage-account"></a>1. Como posso alterar o tipo de replicação georreplicação da minha conta de armazenamento?

   Pode alterar o tipo de replicação georreplicação da sua conta de armazenamento entre LRS, GRS e RA-GRS utilizando o [portal do Azure](https://portal.azure.com/), [Azure Powershell](storage-powershell-guide-full.md) ou através de programação utilizando uma das nossas bibliotecas de cliente do armazenamento muitos . Tenha em atenção que as contas ZRS não podem ser convertida LRS ou GRS. Da mesma forma, uma conta existente do LRS ou GRS não pode ser convertida para uma conta ZRS.

<a id="changedowntime"></a>
#### <a name="2-will-there-be-any-down-time-if-i-change-the-replication-type-of-my-storage-account"></a>2. Haverá qualquer tempo se alterar o tipo de replicação da minha conta de armazenamento?

   Não, haverá qualquer tempo.

<a id="changecost"></a>
#### <a name="3-will-there-be-any-additional-cost-if-i-change-the-replication-type-of-my-storage-account"></a>3. Haverá qualquer custo adicional caso eu mude o tipo de replicação da minha conta de armazenamento?

   Sim. Se mudar do LRS para o GRS (ou RA-GRS) para a sua conta de armazenamento, seria implicar um custo adicional de saída ao copiar dados existentes de localização primária para a localização secundária. Depois dos dados iniciais são copiados há sem qualquer encargo ainda mais a saída adicionais para georreplicação replicar os dados a partir de principal para localização secundária. Os detalhes de custos de largura de banda podem ser encontrados no [página de preços do Storage do Azure](https://azure.microsoft.com/pricing/details/storage/blobs/). Se alterar o GRS para LRS, há sem custos adicionais, mas os dados serão eliminados a partir da localização secundária.

<a id="ragrsbenefits"></a>
#### <a name="4-how-can-ra-grs-help-me"></a>4. Como é que o RA-GRS-me pode ajudar?
   
   Armazenamento GRS fornece replicação dos seus dados de um site primário para uma região secundária que é centenas de quilómetros região primária. Esse caso, os seus dados são duráveis mesmo em caso de uma falha regional completa ou de um desastre no qual a região primária não é recuperável. Inclui o armazenamento RA-GRS isto e adiciona a capacidade de ler os dados da localização secundária. Para algumas ideias sobre como tirar partido desta capacidade, consulte [conceber altamente disponível aplicações utilizando o armazenamento RA-GRS](../storage-designing-ha-apps-with-ragrs.md). 

<a id="lastsynctime"></a>
#### <a name="5-is-there-a-way-for-me-to-figure-out-how-long-it-takes-to-replicate-my-data-from-the-primary-to-the-secondary-region"></a>5. Existe alguma forma para me permitir perceber quanto tempo demora para replicar os meus dados a partir de principal para a região secundária?
   
   Se estiver a utilizar o armazenamento RA-GRS, pode verificar a hora da última sincronização da sua conta de armazenamento. Hora da última sincronização é um valor de data/hora GMT; todas as escritas primárias antes da hora da última sincronização tem sido escrita com êxito na localização secundária, o que significa que estão disponíveis para ler a partir da localização secundária. Escreve principal após a hora da última sincronização pode ou não estar disponível para leituras ainda. Pode consultar este valor a utilizar o [portal do Azure](https://portal.azure.com/), [Azure PowerShell](storage-powershell-guide-full.md), ou através de programação utilizando a API REST ou uma das bibliotecas de cliente de armazenamento. 

<a id="outage"></a>
#### <a name="6-how-can-i-switch-to-the-secondary-region-if-there-is-an-outage-in-the-primary-region"></a>6. Como pode mudar para a região secundária se houver uma falha na região primária?
   
   Consulte o artigo [o que fazer se ocorrer uma falha de armazenamento do Azure](../storage-disaster-recovery-guidance.md) para obter mais detalhes.

<a id="rpo-rto"></a>
#### <a name="7-what-is-the-rpo-and-rto-with-grs"></a>7. Qual é o RPO e RTO com a GRS?
   
   Objetivo de ponto de recuperação (RPO): Na GRS e RA-GRS, o armazenamento do serviço no modo assíncrono georreplicação-são replicados os dados a partir de principal para a localização secundária. Se houver um desastre regional principais e uma ativação pós-falha tem de ser efetuada, alterações de delta recentes, que não foram georreplicação poderão perder-se. O número de minutos de potenciais dados perdidos é referido como o RPO (o que significa que o ponto no tempo para que os dados podem ser recuperados). Normalmente, temos um RPO menos de 15 minutos, apesar de não existe atualmente nenhum SLA em georreplicação quanto tempo demora.

   Objetivo de tempo de recuperação (RTO): Esta é uma medida de quanto tempo demora-nos para fazer a ativação pós-falha e obter a conta de armazenamento online se tivermos que efetuar uma ativação pós-falha. O tempo para fazer a ativação pós-falha inclui o seguinte:
    * O tempo que demora-nos investigar e determinar se estamos pode recuperar os dados na localização principal ou se é necessário efetuar uma ativação pós-falha.
    * Ativação pós-falha a conta alterando as entradas DNS principais para apontar para a localização secundária.

   Iremos tirar da responsabilidade do preservam os seus dados muito é importante que, de modo a se existir qualquer possibilidade de recuperar os dados, iremos manter a fazer a ativação pós-falha e concentrar-se na recuperar os dados na localização primária. No futuro, planeamos fornecem uma API para que possa acionar uma ativação pós-falha ao nível de uma conta, que, em seguida, permitiria controlar o RTO por si, mas este ainda não está disponível.
   
## <a name="next-steps"></a>Passos seguintes
* [Ao conceber aplicações altamente disponíveis a utilizar o armazenamento RA-GRS](../storage-designing-ha-apps-with-ragrs.md)
* [Preços do Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/)
* [Sobre as contas de armazenamento do Azure](../storage-create-storage-account.md)
* [Azure metas de desempenho e escalabilidade de armazenamento](storage-scalability-targets.md)
* [Armazenamento do Microsoft Azure redundância opções e acesso de leitura georreplicação armazenamento redundantes](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)
* [Sosp - Storage do Azure: Um elevada serviço em nuvem armazenamento com consistência forte](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)

