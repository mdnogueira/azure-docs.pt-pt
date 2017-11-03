---
title: "Replicar Georreplicação num registo de contentor do Azure"
description: "Começar a criar e gerir os registos do contentor do Azure georreplicação."
services: container-registry
documentationcenter: 
author: SteveLas
manager: balans
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-registry
ms.devlang: 
ms.topic: overview-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/24/2017
ms.author: stevelas
ms.custom: 
ms.openlocfilehash: 7a05f12e7873b8280dd737b008e186626017125e
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2017
---
# <a name="geo-replication-in-azure-container-registry"></a>Replicação geográfica no registo de contentor do Azure

As empresas que pretendem uma presença local ou uma cópia de segurança frequente, optar por executar serviços a partir de várias regiões do Azure. Como melhor prática, colocar um registo de contentor em cada região onde as imagens são executadas permite operações de fecho de rede, permitindo rápido, as transferências de camada de imagem fiável.

A georreplicação ativa um registo de contentor do Azure para funcionar como um único registo, que serve várias regiões com multi-mestre registos regionais.

> [!IMPORTANT]
> A funcionalidade de georreplicação do registo de contentor do Azure está atualmente na **pré-visualização**. Pré-visualizações ficam disponíveis para si condition que está a concordar com o [termos de utilização suplementares](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Alguns aspetos desta funcionalidade pode ser alterada antes da disponibilidade geral (DG).
>

Um registo georreplicação fornece as seguintes vantagens:

* Os nomes de imagens/registo/etiqueta únicos podem ser utilizados em várias regiões
* Acesso de registo de fecho de rede de implementações regionais
* Não existem taxas de saída adicionais, como imagens são solicitadas a partir de um registo local, replicado na mesma região que o anfitrião de contentor
* Gestão única de um registo em várias regiões

## <a name="example-use-case"></a>Caso de utilização de exemplo
Contoso é executado um site de presença pública localizado nos E.U.A., Canadá e na Europa. Para efetuar estas mercados com conteúdo local e de fecho de rede, a Contoso é executado [serviço de contentor Azure](/azure/container-service/kubernetes/) (ACS) Kubernetes clusters no EUA oeste, EUA leste, Canadá Central e Europa Ocidental. A aplicação de Web site, implementada como uma imagem de Docker, utiliza o mesmo código e imagem em todas as regiões. Conteúdo, local nessa região, é obtido a partir de uma base de dados, o que é aprovisionado de forma exclusiva em cada região. Cada implementação regional tem configuração exclusiva para o de recursos, como a base de dados local.

A equipa de desenvolvimento está localizada em Seattle WA, utilizando o Centro de dados de EUA oeste.

![Enviar para vários registos](media/container-registry-geo-replication/before-geo-replicate.png)<br />*Enviar para vários registos*

Antes de utilizar as funcionalidades de georreplicação, Contoso tinha um registo com base em E.U.A. nos EUA oeste, com um registo adicional na Europa Ocidental. Para efetuar estas regiões diferentes, a equipa de desenvolvimento que tiveram de push imagens dois registos diferentes.

```bash
docker push contoso.azurecr.io/pubic/products/web:1.2
docker push contosowesteu.azurecr.io/pubic/products/web:1.2
```
![Extrair a partir de vários registos](media/container-registry-geo-replication/before-geo-replicate-pull.png)<br />*Extrair a partir de vários registos*

Típicos desafios de vários registos incluem:

* Os clusters EUA leste, EUA oeste e Canadá Central todos os solicitar a partir do registo de EUA oeste, taxas de saída a incorrer em como cada um dos anfitriões contentor remoto solicitar imagens a partir centros de dados de EUA oeste.
* A equipa de desenvolvimento tem push imagens registos EUA oeste e na Europa Ocidental.
* A equipa de desenvolvimento tem de configurar e manter cada implementação regional com os nomes de imagens referenciar registo local.
* Acesso de registo tem de ser configurado para cada região.

## <a name="benefits-of-geo-replication"></a>Vantagens da replicação geográfica

![Extrair um registo georreplicação](media/container-registry-geo-replication/after-geo-replicate-pull.png)

Utilizar a funcionalidade de georreplicação de registo de contentor do Azure, são realizadas destas vantagens:

* Faça a gestão de um único registo em todas as regiões:`contoso.azurecr.io`
* Gerir uma configuração única de implementações de imagens como todas as regiões utilizado o mesmo URL de imagem:`contoso.azurecr.io/public/products/web:1.2`
* Emitir para um único registo, enquanto ACR gere a replicação geográfica, incluindo webhooks regional para notificações locais

## <a name="configure-geo-replication"></a>Configurar georreplicação
Configurar a georreplicação é tão fácil como clicando em regiões num mapa.

Replicação geográfica é uma funcionalidade do [registos Premium](container-registry-skus.md) apenas. Se o registo não estiver ainda Premium, pode alterar de base e padrão para Premium no [portal do Azure](https://portal.azure.com):

![SKUs mudança no portal do Azure](media/container-registry-skus/update-registry-sku.png)

Para configurar a georreplicação para o registo de Premium, inicie sessão no portal do Azure em http://portal.azure.com.

Navegue para o registo de contentor do Azure e selecione **replicações**:

![Replicações no registo do contentor de portal do Azure da IU](media/container-registry-geo-replication/registry-services.png)

Um mapa é apresentado que mostra todas as regiões do Azure atual:

 ![Mapa de região no portal do Azure](media/container-registry-geo-replication/registry-geo-map.png)

* Hexagons azuis representam réplicas atuais
* Verdes hexagons representam regiões de réplica possíveis
* Hexagons cinzentos representam regiões do Azure ainda não está disponíveis para a replicação

Para configurar uma réplica, selecione um hexagon verde, em seguida, selecione **criar**:

 ![Criar a IU de replicação no portal do Azure](media/container-registry-geo-replication/create-replication.png)

Para configurar as réplicas adicionais, selecione os hexagons verdes para outras regiões, em seguida, clique em **criar**.

ACR começa a sincronização imagens entre as réplicas configuradas. Depois de concluído, o portal reflete *pronto*. O estado da réplica no portal não atualiza automaticamente. Utilize o botão de atualização para ver o estado atualizado.

## <a name="geo-replication-pricing"></a>A georreplicação de preços

Replicação geográfica é uma funcionalidade do [Premium SKU](container-registry-skus.md#premium) do registo de contentor do Azure. Quando se replica um registo para as regiões pretendidas, pode implicar taxas de registo Premium para cada região.

No exemplo anterior, a Contoso consolidados dois registos para um, a adição de réplicas para EUA leste, Canadá Central e Europa Ocidental. Contoso seria paga Premium quatro vezes por mês, sem qualquer configuração adicional ou a gestão. Cada região agora obtém as imagens localmente, melhorando o desempenho, fiabilidade sem taxas de saída de rede dos EUA oeste para Canadá e EUA Leste.

## <a name="summary"></a>Resumo

A georreplicação, pode gerir os seus centros de dados regionais como uma nuvem global. Como as imagens são utilizadas em vários serviços do Azure, pode beneficiar de uma plane de gestão única enquanto mantém o fecho de rede, rápida e fiável imagem local obtém.

## <a name="next-steps"></a>Passos seguintes

Consulte a série de três partes tutorial, [georreplicação no registo de contentor do Azure](container-registry-tutorial-prepare-registry.md). Percorrer a criar um registo georreplicação, criar um contentor e, em seguida, implementar com um único `docker push` comando para várias aplicações Web regional para instâncias de contentores.

> [!div class="nextstepaction"]
> [Replicação geográfica no registo de contentor do Azure](container-registry-tutorial-prepare-registry.md)