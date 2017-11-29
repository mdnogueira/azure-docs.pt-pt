---
title: "Instalação de tempo de execução das funções do Azure | Microsoft Docs"
description: "Como instalar o Runtime de funções do Azure"
services: functions
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 05/08/2017
ms.author: anwestg
ms.openlocfilehash: b6078ba7b553773294ebbf11949f7d3b53f46f0c
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2017
---
# <a name="install-the-azure-functions-runtime-preview"></a>Instalar a pré-visualização de tempo de execução de funções do Azure

Se gostaria de instalar a pré-visualização de Runtime de funções do Azure, tem de seguir estes passos:

1. Certifique-se de que a máquina transmite os requisitos mínimos.
1. Transferir o [instalador de pré-visualização do tempo de execução das funções do Azure](https://aka.ms/azafr).
1. Instale a pré-visualização de Runtime de funções do Azure.
1. Conclua a configuração de pré-visualização do tempo de execução de funções de Azure.

## <a name="prerequisites"></a>Pré-requisitos

Antes de instalar a pré-visualização de Runtime de funções do Azure, tem de ter o seguinte:

1. Uma máquina com o Microsoft Windows Server 2016 ou o Microsoft Windows 10 criadores Update (Professional ou Enterprise Edition).
1. Uma instância do SQL Server em execução dentro da sua rede.  Edição mínima necessária é o SQL Server Express.

## <a name="install-the-azure-functions-runtime-preview"></a>Instalar a pré-visualização de tempo de execução de funções do Azure

O instalador de pré-visualização do tempo de execução de funções do Azure orienta-o a instalação da pré-visualização do tempo de execução de funções de Azure gestão e as funções de trabalho.  É possível instalar a função de gestão e de trabalho no mesmo computador.  No entanto, à medida que adiciona mais funções, tem de implementar mais funções de trabalho em máquinas adicionais para poder dimensionar as suas funções em vários workers.

## <a name="install-the-management-and-worker-role-on-the-same-machine"></a>Instalar a função de trabalho e de gestão no mesmo computador

1. Execute o instalador de pré-visualização do tempo de execução de funções do Azure.

    ![Instalador de pré-visualização do tempo de execução de funções do Azure][1]

1. Clique em **Seguinte**.
1. Depois de ter leia os termos do **EULA**, **a caixa de verificação** para aceitar os termos de licenciamento e clique em **seguinte** para avançar.
1. Agora, selecione as funções que pretende instalar neste computador **função de gestão de funções** e/ou **função de trabalho de funções** e clique em **seguinte**.

    ![Instalador de pré-visualização de tempo de execução de funções do Azure - seleção da função][3]

    > [!NOTE]
    > Pode instalar o **função de trabalho de funções** em muitas outras máquinas. Para tal, siga estas instruções e selecionar apenas **função de trabalho de funções** no instalador do.

1. Clique em **seguinte** ter o **Assistente de configuração de tempo de execução de funções do Azure** iniciar o processo de instalação no seu computador.
1. Depois de concluído, o Assistente de configuração irá iniciar o **Runtime de funções do Azure** ferramenta de configuração.

    ![As funções do Azure em tempo de execução pré-visualização instalador concluída][5]

    > [!NOTE]
    > Se estiver a instalar num **Windows 10** e **contentor** funcionalidade não foi ativada anteriormente, o **configuração de tempo de execução de funções do Azure** pede-lhe reiniciar a máquina para concluir a instalação.

## <a name="configure-the-azure-functions-runtime"></a>Configurar o tempo de execução de funções do Azure

Para concluir a instalação de tempo de execução de funções de Azure tem de concluir a configuração.

1. O **Runtime de funções do Azure** ferramenta de configuração mostra que funções são instaladas no seu computador.

    ![Ferramenta de configuração da pré-visualização de tempo de execução de funções do Azure][6]

1. Clique em de **base de dados** separador, introduza os detalhes de ligação da sua instância do SQL Server e clique em **aplicar**.  Isto é necessário para o tempo de execução de funções do Azure criar uma base de dados para suportar o tempo de execução.
    
    ![Configuração de base de dados de pré-visualização de tempo de execução de funções do Azure][7]

1. Clique em de **credenciais** separador.  Neste ecrã tem de criar duas novas credenciais para utilização com uma partilha de ficheiros para o alojamento de todas as suas funções do Azure.  Especifique **nome de utilizador** e **palavra-passe** combinações para o **proprietário da partilha de ficheiros** e para o **utilizador de partilha de ficheiros**, em seguida, clique em **Aplicar**.

    ![As credenciais de pré-visualização do tempo de execução das funções do Azure][8]

1. Clique em de **partilha de ficheiros** separador.  Este ecrã tem de especificar os detalhes do **partilha de ficheiros** localização.  Isto pode ser criado por si ou que pode utilizar uma partilha de ficheiros existente e clique em **aplicar**.  Se selecionar uma nova localização de partilha de ficheiros, tem de especificar um diretório para utilização pelo Runtime de funções do Azure.
    
    ![Partilha de ficheiros de pré-visualização de tempo de execução de funções do Azure][9]

1. Clique em de **IIS** separador.  Este separador mostra os detalhes dos sites no IIS que irá criar a ferramenta de configuração do Runtime de funções do Azure.  Clique em **aplicar** para concluir.

    ![As funções do Azure em tempo de execução pré-visualização IIS][10]

1. Clique em de **serviços** separador.  Este separador mostra o estado dos serviços na sua ferramenta de configuração do Runtime de funções do Azure.  Se após a configuração inicial do **serviço de ativação de anfitrião de funções do Azure** não está em execução clique **iniciar serviço**.

    ![As funções do Azure em tempo de execução pré-visualização Configruation concluída][11]

1. Por fim, navegue para o **Portal de tempo de execução das funções do Azure** como `https://<machinename>/`.

    ![Portal de pré-visualização do tempo de execução de funções do Azure][12]


<!--Image references-->
[1]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer1.png
[2]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer2-EULA.png
[3]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer3-ChooseRoles.png
[4]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer4-Install.png
[5]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer5-InstallComplete.png
[6]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration1.png
[7]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration2_SQL.png
[8]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration3_Credentials.png
[9]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration4_Fileshare.png
[10]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration5_IIS.png
[11]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration6_Services.png
[12]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal.png
