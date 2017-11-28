---
title: Acesso a nuvens privadas do Azure com o Visual Studio | Microsoft Docs
description: Saiba como aceder a recursos de nuvem privada utilizando o Visual Studio.
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 9d733c8d-703b-44e7-a210-bb75874c45c8
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/13/2017
ms.author: kraigb
ms.openlocfilehash: 54acfc7c686dc7025368c381d79cde93d7d48fc5
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="accessing-private-azure-clouds-with-visual-studio"></a>Acesso a nuvens privadas do Azure com o Visual Studio

Por predefinição, o Visual Studio suporta pontos finais REST de nuvem do Azure. Neste artigo, irá aprender a utilizar o certificado da sua nuvem privada para aceder e interagir com a nuvem privada a partir do Visual Studio.

1. No portal do Azure para a nuvem privada, transfira o ficheiro de definições de publicação ou contacte o administrador para um ficheiro de definições de publicação. (O ficheiro tem a extensão `.publishsettings`.)

1. No Visual Studio **Explorador de servidores**, clique com botão direito do **Azure** nó e selecione **gerir e subscrições de filtro**.

    ![Gerir o comando de subscrições](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790778.png)

1. No **gerir subscrições do Microsoft Azure** caixa de diálogo, selecione o **certificados** separador, em seguida, selecione **importação**.

    ![Importação de certificados do Azure](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790779.png)

1. No **importar Microsoft Azure subscrições** caixa de diálogo, selecione **procurar**.

    ![Procurar botão na caixa de diálogo Importar Microsoft Azure subscrições](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/browse-button.png)

1. No **abra** caixa de diálogo, navegue para o diretório onde guardou o ficheiro de definições de publicação, selecione o ficheiro e, em seguida, selecione **abra**.

    ![Selecione o ficheiro de definições de publicação](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/select-publish-settings-file.png)

1. Quando devolvido para o **importar Microsoft Azure subscrições** caixa de diálogo, selecione **importação**.

    ![Importar o ficheiro de definições de publicação](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790780.png)

    Os certificados são importados a partir do ficheiro de definições de publicação para Visual Studio e, agora pode interagir com os recursos de nuvem privada.

