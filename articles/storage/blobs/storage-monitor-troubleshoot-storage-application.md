---
title: "Monitorizar e resolver problemas de uma aplicação de armazenamento na nuvem no Azure | Microsoft Docs"
description: "Utilize as ferramentas de diagnóstico, métricas e alertas para resolver problemas e monitorizar uma aplicação na nuvem."
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
ms.date: 09/19/2017
ms.author: gwallace
ms.custom: mvc
ms.openlocfilehash: db88c331f79d83e0124519f8b6dbb34514b456dd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-and-troubleshoot-a-cloud-storage-application"></a>Monitorizar e resolver problemas de uma aplicação de armazenamento na nuvem

Este tutorial é parte de quatro e a parte final de uma série. Saiba como monitorizar e resolver problemas de uma aplicação de armazenamento na nuvem.

Na parte quatro da série, saiba como:

> [!div class="checklist"]
> * Ativar o registo e métricas
> * Ativar alertas de erros de autorização
> * Execute o tráfego de teste com os tokens SAS incorretos
> * Transferir e analisar os registos

[Análise de armazenamento do Azure](../common/storage-analytics.md) fornece dados de registos e métricos para uma conta de armazenamento. Estes dados disponibilizam informações sobre o estado de funcionamento da sua conta de armazenamento. Antes de poder ser visibilidade na sua conta de armazenamento, terá de configurar a recolha de dados. Este processo envolve ativar o registo, configurar as métricas e ativar os alertas.

Registo e as métricas das contas do storage são ativadas a partir de **diagnóstico** separador no portal do Azure. Existem dois tipos de métricas. **Agregação** métricas recolher percentagens de entrada/saída, disponibilidade, latência e êxito. Estas métricas são agregadas para o blob, fila, tabela e serviços de ficheiros. **Por API** recolhe o mesmo conjunto de métricas para cada operação de armazenamento na API do serviço de armazenamento do Azure. Registo de armazenamento permite-lhe detalhes do registo de pedidos com êxito ou falhados na sua conta de armazenamento. Estes registos permitem-lhe ver os detalhes de leitura, escrita e eliminação operações relativamente a tabelas do Azure, filas e blobs. Eles também permitem-lhe ver as razões para pedidos falhados como os tempos limite, limitação e erros de autorização.

## <a name="log-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [Portal do Azure](https://portal.azure.com)

## <a name="turn-on-logging-and-metrics"></a>Ativar o registo e métricas

No menu à esquerda, selecione **grupos de recursos**, selecione **myResourceGroup**e, em seguida, selecione a sua conta de armazenamento na lista de recursos.

Em **diagnóstico** definir **estado** para **no**. Certifique-se **Blob métricas agregadas**, **Blob por métricas de API**, e **Blob registos** estão ativadas.

Quando terminar, clique em **guardar**

![Painel de diagnóstico](media/storage-monitor-troubleshoot-storage-application/figure1.png)

## <a name="enable-alerts"></a>Ativar alertas

Alertas fornecem uma forma dos administradores de e-mail ou acionar um webhook com base numa métrica de ser, um limiar. Neste exemplo, permitir um alerta para o `SASClientOtherError` métrica.

### <a name="navigate-to-the-storage-account-in-the-azure-portal"></a>Navegue para a conta do storage no portal do Azure

No menu à esquerda, selecione **grupos de recursos**, selecione **myResourceGroup**e, em seguida, selecione a sua conta de armazenamento na lista de recursos.

Sob o **monitorização** secção, selecione **regras de alerta**.

Selecione **+ Adicionar alerta**, em **adicionar uma regra de alerta**, preencha as informações necessárias. Escolha `SASClientOtherError` do **métrica** pendente.

![Painel de diagnóstico](media/storage-monitor-troubleshoot-storage-application/figure2.png)

## <a name="simulate-an-error"></a>Simular um erro

Para simular um alerta válido, pode tentar pedir um blob inexistente da sua conta de armazenamento. Para fazer isto, substitua o `<incorrect-blob-name>` valor com um valor que não existe. Execute o seguinte exemplo de código algumas horas simular blob pedidos falhados.

```azurecli-interactive
sasToken=$(az storage blob generate-sas \
    --account-name <storage-account-name> \
    --account-key <storage-account-key> \
    --container-name <container> \
    --name <incorrect-blob-name> \
    --permissions r \
    --expiry `date --date="next day" +%Y-%m-%d` \
    --output tsv)

curl https://<storage-account-name>.blob.core.windows.net/<container>/<incorrect-blob-name>?$sasToken
```

A imagem seguinte é um alerta de exemplo que se baseia-se desativar a falha simulada executou com o exemplo anterior.

 ![Alerta de exemplo](media/storage-monitor-troubleshoot-storage-application/alert.png)

## <a name="download-and-view-logs"></a>Transferir e ver registos

Os registos de armazenamento armazenam dados de um conjunto de blobs num contentor do blob denominado **$logs** na sua conta de armazenamento. Este contentor não aparecer se lista todos os contentores de BLOBs na sua conta, mas pode ver o respetivo conteúdo se pode aceder diretamente à mesma.

Neste cenário, utilize [Microsoft Message Analyzer](http://technet.microsoft.com/library/jj649776.aspx) para interagir com a sua conta de armazenamento do Azure.

### <a name="download-microsoft-message-analyzer"></a>Transferir o Microsoft Message Analyzer

Transferir [Microsoft Message Analyzer](https://www.microsoft.com/download/details.aspx?id=44226) e instalar a aplicação.

Iniciar a aplicação e escolha **ficheiro** > **abra** > **de outras origens de ficheiro**.

No **Seletor de ficheiros** caixa de diálogo, selecione **+ adicionar a ligação do Azure**. Introduza o **nome da conta de armazenamento** e **chave da conta** e clique em **OK**.

![Microsoft mensagem analisador - adicionar caixa de diálogo de ligação de armazenamento do Azure](media/storage-monitor-troubleshoot-storage-application/figure3.png)

Assim que estiver ligado, expanda os contentores na árvore de armazenamento da vista para visualizar os blobs de registo. Selecione o início de sessão mais recente e clique em **OK**.

![Microsoft mensagem analisador - adicionar caixa de diálogo de ligação de armazenamento do Azure](media/storage-monitor-troubleshoot-storage-application/figure4.png)

No **nova sessão** caixa de diálogo, clique em **iniciar** para ver o início de sessão.

Depois de abre o registo, pode ver os eventos de armazenamento. Como pode ver na imagem seguinte, Ocorreu uma `SASClientOtherError` acionada na conta de armazenamento. Para obter informações adicionais no registo de armazenamento, visite [análise de armazenamento](../common/storage-analytics.md).

![Message de Microsoft Analyzer. o-visualização de eventos](media/storage-monitor-troubleshoot-storage-application/figure5.png)

[Explorador de armazenamento](https://azure.microsoft.com/features/storage-explorer/) é outra ferramenta que pode ser utilizada para interagir com as contas de armazenamento, incluindo o **$logs** contentor e os registos estão contidos na mesma.

## <a name="next-steps"></a>Passos seguintes

Na parte de quatro e a parte final da série, aprendeu a monitorizar e resolver problemas com a sua conta do storage, tais como:

> [!div class="checklist"]
> * Ativar o registo e métricas
> * Ativar alertas de erros de autorização
> * Execute o tráfego de teste com os tokens SAS incorretos
> * Transferir e analisar os registos

Siga esta ligação para ver os exemplos de armazenamento pré-criadas.

> [!div class="nextstepaction"]
> [Exemplos de script de armazenamento do Azure](storage-samples-blobs-cli.md)