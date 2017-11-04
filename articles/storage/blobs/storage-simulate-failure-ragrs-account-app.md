---
title: "Simule uma falha ao aceder ao armazenamento com redundância de acesso de leitura no Azure | Microsoft Docs"
description: Simular um erro ao aceder ao armazenamento georredundante com acesso de leitura
services: storage
documentationcenter: 
author: georgewallace
manager: timlt
editor: 
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: csharp
ms.topic: tutorial
ms.date: 10/12/2017
ms.author: gwallace
ms.custom: mvc
ms.openlocfilehash: 2919eb0e301000b53f4f63799e9c65aad45ca9f2
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2017
---
# <a name="simulate-a-failure-in-accessing-read-access-redundant-storage"></a>Simule uma falha ao aceder ao armazenamento com redundância de acesso de leitura

Este tutorial é parte dois de uma série. Neste tutorial, pode inserir uma resposta de falha com o Fiddler para os pedidos para um [acesso de leitura georredundante](../common/storage-redundancy.md#read-access-geo-redundant-storage) conta de armazenamento (RA-GRS) para simular uma falha e ter a aplicação de ler a partir do ponto final secundário.

![Cenário de aplicação](media/storage-simulate-failure-ragrs-account-app/scenario.png)

Na parte dois da série, saiba como:

> [!div class="checklist"]
> * Executar e colocar em pausa a aplicação
> * Simule uma falha
> * Simular o restauro do ponto final principal

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

* Transfira e instale [Fiddler](https://www.telerik.com/download/fiddler)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Para concluir este tutorial, tem de ter concluído o tutorial de armazenamento anterior: [tornar os dados da aplicação de elevada disponibilidade com armazenamento do Azure][previous-tutorial].

## <a name="launch-fiddler"></a>Inicie o fiddler

Abra Fiddler, selecione **regras** e **personalizar regras**.

![Personalizar regras Fiddler](media/storage-simulate-failure-ragrs-account-app/figure1.png)

O Fiddler ScriptEditor inicia que mostra o **SampleRules.js** ficheiro. Este ficheiro é utilizado para personalizar o Fiddler. Cole o seguinte exemplo de código no `OnBeforeResponse` função. O novo código é comentado para se certificar de que a lógica cria não se encontra implementada imediatamente. Depois de concluído selecione **ficheiro** e **guardar** para guardar as alterações.

```javascript
    /*
        // Simulate data center failure
        // After it is successfully downloading the blob, pause the code in the sample,
        // uncomment these lines of script, and save the script.
        // It will intercept the (probably successful) responses and send back a 503 error. 
        // When you're ready to stop sending back errors, comment these lines of script out again 
        //     and save the changes.

        if ((oSession.hostname == "contosoragrs.blob.core.windows.net") 
            && (oSession.PathAndQuery.Contains("HelloWorld"))) {
            oSession.responseCode = 503;  
        }
    */
```

![Colar regra personalizada](media/storage-simulate-failure-ragrs-account-app/figure2.png)

## <a name="start-and-pause-the-application"></a>Iniciar e interromper a aplicação

No Visual Studio, prima **F5** ou selecione **iniciar** para iniciar a depuração da aplicação. Assim que a aplicação começa a leitura do ponto final principal, prima **qualquer tecla** na janela da consola ao colocar em pausa a aplicação.

## <a name="simulate-failure"></a>Simular falha

Com a aplicação está em pausa, pode agora anule o comentário personalizada da regra foi guardado no Fiddler um passo anterior. Este código de exemplo procura para pedidos para a conta de armazenamento RA-GRS e se o caminho contém o nome da imagem, `HelloWorld`, devolve um código de resposta `503 - Service Unavailable`.

Navegue para o Fiddler e selecione **regras** -> **personalizar regras...** .  Anule o comentário saída as seguintes linhas, substitua `STORAGEACCOUNTNAME` com o nome da conta de armazenamento. Selecione **ficheiro** -> **guardar** para guardar as alterações.

```javascript
         if ((oSession.hostname == "STORAGEACCOUNTNAME.blob.core.windows.net")
         && (oSession.PathAndQuery.Contains("HelloWorld"))) {
         oSession.responseCode = 503;
         }
```

Para retomar a aplicação, prima **qualquer tecla** .

Assim que a aplicação for iniciada novamente a ser executado, os pedidos para o ponto final principal começarem a falhar. A aplicação tenta restabelecer a ligação ao ponto final do primário 5 vezes. Após o limiar de falha de cinco tentativas, a imagem que solicita secundária só de leitura ponto final. Depois da aplicação com êxito obtém a imagem de 20 vezes do ponto final secundário, a aplicação tenta estabelecer ligação ao ponto final do principal. Se o ponto final principal está inacessível ainda, a aplicação retoma ler a partir do ponto final secundário. Este padrão está a [disjuntor](/azure/architecture/patterns/circuit-breaker.md) padrão descrita no tutorial anterior.

![Colar regra personalizada](media/storage-simulate-failure-ragrs-account-app/figure3.png)

## <a name="simulate-primary-endpoint-restoration"></a>Simular o restauro do ponto final principal

Com a Fiddler regra personalizada definida no passo anterior, os pedidos para o ponto final principal falharem. Para simular o principal ponto de final a funcionar novamente, remova a lógica ao inserir o `503` erro.

Para colocar em pausa a aplicação, prima **qualquer tecla**.

### <a name="remove-the-custom-rule"></a>Remover a regra personalizada

Navegue para o Fiddler e selecione **regras** e **personalizar regras...** .  Comente ou remova a lógica personalizada no `OnBeforeResponse` função, deixando a função de predefinição. Selecione **ficheiro** e **guardar** para guardar as alterações.

![Remover regra personalizada](media/storage-simulate-failure-ragrs-account-app/figure5.png)

Quando terminar, prima **qualquer tecla** para retomar a aplicação. A aplicação continua ler a partir do ponto final principal até chegar a 999 leituras.

![Retomar a aplicação](media/storage-simulate-failure-ragrs-account-app/figure4.png)

## <a name="next-steps"></a>Passos seguintes

Parte dois da série, aprendeu sobre simulando uma falha ao testar o armazenamento georredundante com acesso de leitura tais como:

> [!div class="checklist"]
> * Executar e colocar em pausa a aplicação
> * Simule uma falha
> * Simular o restauro do ponto final principal

Siga esta ligação para ver os exemplos de armazenamento pré-criadas.

> [!div class="nextstepaction"]
> [Exemplos de script de armazenamento do Azure](storage-samples-blobs-cli.md)

[previous-tutorial]: storage-create-geo-redundant-storage.md