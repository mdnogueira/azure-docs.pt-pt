---
title: "Descrição geral do Gestor de dados do Microsoft Azure StorSimple | Microsoft Docs"
description: "Fornece uma descrição geral do serviço StorSimple Manager de dados (pré-visualização privada)"
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/22/2016
ms.author: vidarmsft
ms.openlocfilehash: aedb44610fe57055851538b9dbdb810e66e58d73
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="storsimple-data-manager-overview-private-preview"></a>Descrição geral do Gestor de dados do StorSimple (pré-visualização privada)

## <a name="overview"></a>Descrição geral

Microsoft Azure StorSimple é uma solução de armazenamento de nuvem híbrida endereços complexidades de dados não estruturados normalmente associadas a partilhas de ficheiros. StorSimple utiliza armazenamento na nuvem como uma extensão da solução no local e automaticamente os dados de camadas entre o armazenamento no local e o armazenamento na nuvem. Integrado proteção de dados no local e na nuvem instantâneos, elimina a necessidade de uma infraestrutura de armazenamento sprawling. Arquivo e recuperação após desastre é também totalmente integrada com a nuvem a agir como uma localização fora das instalações.

O serviço de transformação de dados que iremos introduzir neste documento, permite-lhe uma forma totalmente integrada aceder aos dados StorSimple na nuvem. Este serviço fornece APIs para extrair dados do StorSimple e apresentá-lo para outros serviços do Azure em formatos que podem ser prontamente consumidas. Os formatos suportados nesta pré-visualização são blobs do Azure e os recursos de Media Services do Azure. Esta transformação permite-lhe ligar facilmente segurança serviços como o Media Services do Azure, Azure HDInsight, do Azure Machine Learning e da Azure Search para operar dados no dispositivo de no local de série 8000 do StorSimple.

Um diagrama de blocos de alto nível que ilustra isto é mostrado abaixo.

![Diagrama de alto nível](./media//storsimple-data-manager-overview/high-level-diagram.png)

Este documento explica como pode inscrever para uma versão de pré-visualização privada deste serviço. Também explica como pode utilizar este serviço para escrever as aplicações que utilizam dados StorSimple e outros serviços do Azure na nuvem.

## <a name="sign-up-for-data-manager-preview"></a>Inscrever-se para a pré-visualização do Gestor de dados
Antes de se inscrever para o serviço do Gestor de dados, reveja os seguintes pré-requisitos.

### <a name="prerequisites"></a>Pré-requisitos

Neste exercício parte do princípio de que tem
* Uma subscrição do Azure Active Directory.
* o acesso ao StorSimple registado dispositivos de série 8000
* todas as chaves associadas com o dispositivo de série 8000 do StorSimple.

### <a name="sign-up"></a>Inscrever-se

O Gestor de dados do StorSimple está em pré-visualização privada. Execute os seguintes passos para se inscrever para uma versão de pré-visualização privada deste serviço:

1.  Iniciar sessão no portal do Azure com a extensão de dados de StorSimple Manager em: [https://aka.ms/HybridDataManager](https://aka.ms/HybridDataManager). Utilize as suas credenciais do Azure para iniciar sessão.

2.  Clique em de  **+**  ícone para criar um serviço. Clique em **armazenamento** e, em seguida, clique em **ver todos os** no painel que abre-se.

    ![Ícone de Gestor de dados do StorSimple pesquisa](./media/storsimple-data-manager-overview/search-data-manager-icon.png)

3. Pode ver o ícone do Gestor de dados do StorSimple.

    ![Selecione o ícone do Gestor de dados do StorSimple](./media/storsimple-data-manager-overview/select-data-manager-icon.png)

4. Clique em Gestor de dados do StorSimple ícone e, em seguida, clique em **criar**. Escolha a subscrição que pretende ativar para a pré-visualização privada e, em seguida, clique em **inscrever-me!**

    ![Inscrever-me](./media/storsimple-data-manager-overview/sign-me-up.png)

5. Esta ação envia um pedido para carregar a. Iremos carregar, logo que possível. Depois de ativar a sua subscrição, pode criar um serviço StorSimple Manager de dados.

6. Facilmente para aceder ao serviço StorSimple Manager de dados, clique no ícone de estrela para afixá-lo aos seus favoritos.

    ![Gestores de dados do StorSimple de acesso](./media/storsimple-data-manager-overview/access-data-managers.png)


## <a name="next-steps"></a>Passos seguintes

[Utilize o Gestor de dados StorSimple IU para transformar os seus dados](storsimple-data-manager-ui.md).