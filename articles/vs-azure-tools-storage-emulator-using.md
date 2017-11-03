---
title: Configurar e utilizar o emulador de armazenamento com o Visual Studio | Microsoft Docs
description: Configurar e utilizar o emulador de armazenamento com o Visual Studio
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: c8e7996f-6027-4762-806e-614b93131867
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 8/17/2017
ms.author: kraigb
ms.openlocfilehash: f4cd8ccc3b186cf2b4178b7d8a98d8928c705cbc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configuring-and-using-the-storage-emulator-with-visual-studio"></a>Configurar e utilizar o emulador de armazenamento com o Visual Studio
[!INCLUDE [storage-try-azure-tools](../includes/storage-try-azure-tools.md)]

## <a name="overview"></a>Descrição geral
O ambiente de desenvolvimento de SDK do Azure inclui o emulador de armazenamento, um utilitário que simula os Blob, tabela e fila, serviços de armazenamento disponíveis no Azure no seu computador de desenvolvimento local. Se estiver a criar um serviço em nuvem que utiliza os serviços de armazenamento do Azure ou escrever qualquer aplicação externa que chama os serviços de armazenamento, pode testar código localmente contra o emulador de armazenamento. As ferramentas do Azure para o Microsoft Visual Studio integrar a gestão do emulador de armazenamento para o Visual Studio. As ferramentas Azure inicializar a base de dados de emulador de armazenamento na primeira utilização, inicia o serviço de emulador de armazenamento, quando executa ou depurar o seu código a partir do Visual Studio e fornece acesso só de leitura para os dados de emulador de armazenamento através do Explorador de armazenamento do Azure.

Para obter informações detalhadas sobre o emulador de armazenamento, incluindo os requisitos de sistema e instruções de configuração personalizada, consulte [utilizar o emulador do Storage do Azure para desenvolvimento e teste](storage/common/storage-use-emulator.md).

> [!NOTE]
> Existem algumas diferenças de funcionalidade entre a simulação de emulador de armazenamento e os serviços de armazenamento do Azure. Consulte [diferenças entre o armazenamento emulador e serviços de armazenamento do Azure](storage/common/storage-use-emulator.md) na documentação do Azure SDK para obter informações sobre as diferenças específicas.
> 
> 

## <a name="configuring-a-connection-string-for-the-storage-emulator"></a>Configurar uma cadeia de ligação para o emulador de armazenamento
Para aceder ao emulador do storage a partir do código de uma função, irá querer configurar uma cadeia de ligação que aponta para o emulador de armazenamento e que mais tarde pode ser alterado para apontar para uma conta de armazenamento do Azure. Uma cadeia de ligação é uma definição de configuração que pode ler a função no tempo de execução para ligar a uma conta de armazenamento. Para obter mais informações sobre como criar cadeias de ligação, consulte [configurar a aplicação Azure](https://msdn.microsoft.com/library/azure/2da5d6ce-f74d-45a9-bf6b-b3a60c5ef74e#BK_SettingsPage).

> [!NOTE]
> Pode devolver uma referência à conta de emulador do storage a partir do código utilizando a **DevelopmentStorageAccount** propriedade. Esta abordagem funciona corretamente se pretender aceder ao emulador do storage a partir do código, mas se planeia publicar a aplicação no Azure, terá de criar uma cadeia de ligação para aceder à sua conta de armazenamento do Azure e modificar o código para utilizar essa ligação cadeia antes de o publicar. Se estiver oscila frequentemente entre a conta de emulador do storage e uma conta de armazenamento do Azure, uma cadeia de ligação irá simplificar este processo.
> 
> 

## <a name="initializing-and-running-the-storage-emulator"></a>A inicializar e executar o emulador de armazenamento
Pode especificar que quando executa ou depurar o seu serviço no Visual Studio, Visual Studio inicia automaticamente o emulador de armazenamento. No Explorador de soluções, abra o menu de atalho para a sua **Azure** projeto e escolha **propriedades**. No **desenvolvimento** separador o **iniciar emulador do Storage de Azure** lista, escolha **verdadeiro** (se já não está definido para esse valor).

Na primeira vez que executa ou de depuração do serviço do Visual Studio, o emulador do storage inicia um processo de inicialização. Este processo reserva portas locais para o emulador do storage e cria a base de dados do emulador de armazenamento. Depois de concluído, este processo não precisa de executar novamente a menos que a base de dados do emulador de armazenamento é eliminado.

> [!NOTE]
> A partir de Junho de 2012 versão das ferramentas do Azure, o emulador de armazenamento é executado, por predefinição, no SQL Server Express LocalDB. Nas versões anteriores das ferramentas do Azure, o emulador de armazenamento é executada relativamente a uma instância predefinida do SQL Express 2005 ou 2008, que é necessário instalar antes de instalar o SDK do Azure. Também pode executar o emulador de armazenamento em relação a uma instância nomeada do SQL Server Express ou com um nome ou a instância predefinida do Microsoft SQL Server. Se precisar de configurar o emulador de armazenamento para executar uma instância diferente a instância predefinida, consulte [utilizar o emulador do Storage do Azure para desenvolvimento e teste](storage/common/storage-use-emulator.md).
> 
> 

O emulador do storage fornece uma interface de utilizador para ver o estado dos serviços de armazenamento local e para iniciar, parar e repô-las. Assim que foi iniciado o serviço de emulador de armazenamento, pode apresentar a interface de utilizador de modo a iniciar ou parar o serviço clicando no ícone da área de notificação para o emulador do Microsoft Azure na barra de tarefas do Windows.

## <a name="viewing-storage-emulator-data-in-server-explorer"></a>Visualizar os dados de emulador de armazenamento no Explorador de servidores
O nó de armazenamento do Azure no Explorador de servidores permite-lhe ver os dados e alterar as definições para os dados de BLOBs e de tabela na suas contas do storage, incluindo o emulador de armazenamento. Consulte [recursos de gerir o armazenamento de Blobs do Azure com o Explorador de armazenamento (pré-visualização)](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs) para obter mais informações.

