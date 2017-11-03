---
title: "Utilizando o emulador do Express para executar e depurar um serviço em nuvem do Azure num computador local | Microsoft Docs"
description: "Utilizando o emulador do Express para executar e depurar um serviço em nuvem num computador local"
services: visual-studio-online
documentationcenter: n/a
author: mikejo
manager: ghogen
editor: 
ms.assetid: 73108f98-a552-4817-b7a1-551367b71906
ms.service: visual-studio-online
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/06/2017
ms.author: mikejo
ms.openlocfilehash: 9c258ad7de5e25b4b304f5e56d93abeff1187f71
ms.sourcegitcommit: b83781292640e82b5c172210c7190cf97fabb704
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2017
---
# <a name="using-emulator-express-to-run-and-debug-an-azure-cloud-service-on-a-local-machine"></a>Utilizando o emulador do Express para executar e depurar um serviço em nuvem do Azure num computador local
Ao utilizar o emulador Express, pode testar e depurar um serviço em nuvem sem executar o Visual Studio como administrador. Pode definir as definições de projeto para utilizar o emulador Express ou o emulador completo, dependendo dos requisitos do seu serviço em nuvem. Para obter mais informações sobre o emulador completo, consulte [executar uma aplicação do Azure no emulador de computação](storage/common/storage-use-emulator.md).

## <a name="using-emulator-express-in-visual-studio"></a>Utilizando o emulador rápida no Visual Studio
Quando cria um projeto do Azure no Azure SDK 2.3 ou posterior, emulador Express é automaticamente utilizado. Para os projetos existentes que foram criados com uma versão anterior do SDK do Azure, utilize os seguintes passos para selecionar o emulador Express:

1. Criar ou abrir um projeto de serviço em nuvem do Azure no Visual Studio.

1. No **Explorador de soluções**, clique com o botão direito no projeto e, no menu de contexto, selecione **propriedades**.

1. Nas páginas de propriedades projetos, selecione o **Web** separador.

    ![Propriedades de um projeto de serviço em nuvem do Azure](./media/vs-azure-tools-emulator-express-debug-run/web-properties.png)

1. Em **servidor de desenvolvimento Local**, selecione **utilize IIS Express opção**.

1. Em **emulador**, selecione **utilizar emulador Express**.
   
1. Para iniciar o emulador Express, execute o seguinte comando numa linha de comandos: 

    ```
    csrun.exe /useemulatorexpress
    ```

## <a name="emulator-express-limitations"></a>Limitações do emulador rápida
Os seguintes problemas são conhecidos limitações do emulador Express: 

- Emulador Express não é compatível com o servidor Web do IIS.
- O serviço em nuvem pode conter várias funções, mas cada função é limitada a uma instância.
- Não é possível aceder a números de porta abaixo 1000. Se utilizar um fornecedor de autenticação que normalmente utiliza uma porta abaixo 1000, poderá ter de alterar este valor para um número de porta que é superior a 1000.
- Quaisquer limitações que se aplicam ao emulador de computação do Azure também se aplicam ao emulador Express. Por exemplo, não pode ter mais de 50 instâncias de função por implementação. Para obter mais informações sobre o emulador de computação do Azure, consulte [executar uma aplicação do Azure no emulador de computação](http://go.microsoft.com/fwlink/p/?LinkId=623050).

## <a name="next-steps"></a>Passos seguintes
[Depuração cloud services do Azure](https://msdn.microsoft.com/library/azure/ee405479.aspx)
