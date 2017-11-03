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
ms.date: 03/19/2017
ms.author: kraigb
ms.openlocfilehash: b2578c837732ab05d538e9b896ed3a3035075a70
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="accessing-private-azure-clouds-with-visual-studio"></a>Acesso a nuvens privadas do Azure com o Visual Studio
Por predefinição, o Visual Studio suporta pontos finais REST de nuvem do Azure público. Neste tópico, irá aprender a utilizar o certificado da sua nuvem privada para aceder - e interagir com - a nuvem privada a partir do Visual Studio.

## <a name="to-access-a-private-azure-cloud-in-visual-studio"></a>Para aceder a um Azure privado nuvem no Visual Studio
1. No [portal clássico do Azure](http://go.microsoft.com/fwlink/?LinkID=213885) para a nuvem privada, transfira o ficheiro de definições de publicação ou contacte o administrador para um ficheiro de definições de publicação. Na versão pública do Azure, é a ligação para transferir este [https://manage.windowsazure.com/publishsettings/](https://manage.windowsazure.com/publishsettings/). (O ficheiro transferido deve ter uma extensão de `.publishsettings`)

1. Abrir Visual Studio

1. No **Explorador de servidores**, clique com botão direito do **Azure** nó e, no menu de contexto, selecione **gerir e subscrições de filtro**.
   
    ![Gerir o comando de subscrições](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790778.png)

1. No **gerir subscrições do Microsoft Azure** caixa de diálogo, selecione o **certificados** separador e, em seguida, selecione **importação**.
   
    ![Importação de certificados do Azure](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790779.png)

1. No **importar Microsoft Azure subscrições** caixa de diálogo, selecione **procurar**.

    ![Procurar botão na caixa de diálogo Importar Microsoft Azure subscrições](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/browse-button.png)

1. No **abra** caixa de diálogo, navegue para o diretório onde guardou o ficheiro de definições de publicação, selecione o ficheiro e, em seguida, selecione **abra**.

    ![Selecione o ficheiro de definições de publicação](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/select-publish-settings-file.png)

1. Quando devolvido para o **importar Microsoft Azure subscrições** caixa de diálogo, selecione **importação**.

    ![Importar o ficheiro de definições de publicação](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790780.png)

    Os certificados são importados a partir do ficheiro de definições de publicação para Visual Studio e, agora pode interagir com os recursos de nuvem privada.
   
## <a name="next-steps"></a>Passos seguintes
- [Publicação de um serviço em nuvem do Azure a partir do Visual Studio](https://msdn.microsoft.com/library/azure/ee460772.aspx)
- [Como: transferir e importar publicar definições e informações de subscrição](https://msdn.microsoft.com/library/dn385850\(v=nav.70\).aspx)
