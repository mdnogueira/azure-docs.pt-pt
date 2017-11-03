---
title: "A exportação contínua de telemetria do Application Insights | Microsoft Docs"
description: "Exportar dados de diagnóstico e utilização para o armazenamento no Microsoft Azure e transferi-la a partir daí."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: 5b859200-b484-4c98-9d9f-929713f1030c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/23/2017
ms.author: mbullwin
ms.openlocfilehash: 7d1f648bc2c2a42cfbd668f180bce8f56ebd065b
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="export-telemetry-from-application-insights"></a>Exportar a telemetria do Application Insights
Pretende manter a sua telemetria durante mais tempo do que o período de retenção padrão? Ou processá-la de alguma forma especializada? A exportação contínua é ideal para este. Os eventos que vir no portal do Application Insights podem ser exportados para o armazenamento no Microsoft Azure no formato JSON. A partir daí pode transferir os dados e escrever independentemente código que precisa de processá-la.  

Utilizar a exportação contínua pode implicar um encargos adicionais. Verifique o [modelo de preços](http://azure.microsoft.com/pricing/details/application-insights/).

Antes de configurar a exportação contínua, existem algumas alternativas que poderá pretender considerar:

* O botão de exportação na parte superior do painel de métricas ou procura permite-lhe transferir as tabelas e gráficos para uma folha de cálculo do Excel.

* [Análise de](app-insights-analytics.md) fornece um idioma de consulta poderosa para telemetria. -Também pode exportar os resultados.
* Se estiver à procura para [explore os dados no Power BI](app-insights-export-power-bi.md), pode fazê-lo sem utilizar a exportação contínua.
* O [REST API de acesso a dados](https://dev.applicationinsights.io/) permite-lhe aceder programaticamente à sua telemetria.

Após a exportação contínua copia os dados para armazenamento (onde-pode permanecer para, desde que pretender), ainda está disponível no Application Insights para o habitual [período de retenção](app-insights-data-retention-privacy.md).

## <a name="setup"></a>Criar uma exportação contínua
1. No recurso do Application Insights para a sua aplicação, abra a exportação contínua e selecione **adicionar**:

    ![Desloque para baixo e clique em exportação contínua](./media/app-insights-export-telemetry/01-export.png)

2. Escolha a telemetria tipos de dados que pretende exportar.

3. Crie ou selecione um [conta do storage do Azure](../storage/common/storage-introduction.md) onde pretende armazenar os dados.

    > [!Warning]
    > Por predefinição, a localização de armazenamento será definida para a mesma região geográfica que o recurso do Application Insights. Se armazenar numa região diferente, pode implicar custos de transferência.

    ![Clique em Adicionar, destino exportar, conta de armazenamento e, em seguida, criar um novo arquivo ou escolha um arquivo existente](./media/app-insights-export-telemetry/02-add.png)

4. Crie ou selecione um contentor no armazenamento:

    ![Clique em tipos de evento de escolha](./media/app-insights-export-telemetry/create-container.png)

Assim que tiver criado a exportação, inicia vai. Apenas obter dados que são recebidos depois de criar a exportação.

Pode haver um atraso de sobre uma hora até os dados são apresentados no armazenamento.

### <a name="to-edit-continuous-export"></a>Para editar a exportação contínua

Se pretender alterar os tipos de evento mais tarde, basta editar a exportação:

![Clique em tipos de evento de escolha](./media/app-insights-export-telemetry/05-edit.png)

### <a name="to-stop-continuous-export"></a>Para parar a exportação contínua

Para parar a exportação, clique em Desativar. Ao clicar em ativar novamente, a exportação será reiniciado com novos dados. Não irá obter os dados que chegaram no portal enquanto a exportação foi desativada.

Para parar a exportação permanentemente, elimine-o. Se o fizer, não elimina os dados do armazenamento.

### <a name="cant-add-or-change-an-export"></a>Não é possível adicionar ou alterar uma exportação?
* Para adicionar ou alterar exportações, necessita de direitos de acesso de proprietário, Contribuidor ou Contribuidor do Application Insights. [Saiba mais sobre as funções][roles].

## <a name="analyze"></a>Os eventos obter?
Os dados exportados são a telemetria não processados que recebemos da sua aplicação, exceto que iremos adicionar dados de localização que iremos calcular do endereço IP do cliente.

Dados que foi ignorados pelo [amostragem](app-insights-sampling.md) não está incluído nos dados exportados.

Outras métricas calculadas não estão incluídas. Por exemplo, vamos não exportar utilisation de CPU média, mas vamos exportar a telemetria não processados a partir do qual é calculada a média.

Os dados também incluem os resultados de qualquer [testes web de disponibilidade](app-insights-monitor-web-app-availability.md) que configurou.

> [!NOTE]
> **Amostragem.** Se a aplicação envia uma grande quantidade de dados, a funcionalidade de amostragem pode operar e enviar apenas uma fração da telemetria gerada. [Saiba mais sobre a amostragem.](app-insights-sampling.md)
>
>

## <a name="get"></a>Inspecione os dados
Pode inspecionar o armazenamento diretamente no portal. Clique em **procurar**, selecione a sua conta de armazenamento e, em seguida, abra **contentores**.

Para inspecionar o armazenamento do Azure no Visual Studio, abra **vista**, **Cloud Explorer**. (Se não tiver esse comando de menu, tem de instalar o SDK do Azure: Abra o **novo projeto** caixa de diálogo, expanda Visual c# / nuvem e escolha **obter o Microsoft Azure SDK para .NET**.)

Quando abrir o arquivo de blob, verá um contentor com um conjunto de ficheiros de blob. O URI de cada ficheiro derivado do seu nome de recurso do Application Insights, a chave de instrumentação, a telemetria-tipo/data/hora. (O nome do recurso é todo em minúsculas, e a chave de instrumentação omite traços.)

![Inspecione o arquivo de blob com uma ferramenta adequada](./media/app-insights-export-telemetry/04-data.png)

A data e hora são UTC e são quando a telemetria foi deposited no arquivo de - não o tempo que foi gerado. Por isso, se escrever código para transferir os dados, este pode mover forma linear através de dados.

Eis a forma do caminho:

    $"{applicationName}_{instrumentationKey}/{type}/{blobDeliveryTimeUtc:yyyy-MM-dd}/{ blobDeliveryTimeUtc:HH}/{blobId}_{blobCreationTimeUtc:yyyyMMdd_HHmmss}.blob"

onde

* `blobCreationTimeUtc`é de hora em que o blob foi criado no interna transição armazenamento
* `blobDeliveryTimeUtc`é o tempo quando blob é copiado para o armazenamento de destino de exportação

## <a name="format"></a>Formato de dados
* Cada blob é um ficheiro de texto que contém vários ' \n'-separated linhas. Contém a telemetria processada durante um período de tempo de aproximadamente meio um minuto.
* Cada linha representa um ponto de dados de telemetria, como uma vista de página ou a pedido.
* Cada linha é um documento JSON não formatado. Se pretende manter-se e stare-la, abra-o no Visual Studio e escolha edite, avançadas, ficheiro de formato:

![Ver a telemetria com uma ferramenta adequada](./media/app-insights-export-telemetry/06-json.png)

Durações de tempo são em ticks, onde os batimentos de 10 000 = 1ms. Por exemplo, estes valores mostram um tempo de 1ms para enviar um pedido a partir do browser, 3ms para receberem e 1.8s para processar a página no browser:

    "sendRequest": {"value": 10000.0},
    "receiveRequest": {"value": 30000.0},
    "clientProcess": {"value": 17970000.0}

[Referência para os tipos de propriedade e os valores de modelo de dados de detalhado.](app-insights-export-data-model.md)

## <a name="processing-the-data"></a>Processar os dados
Em pequena escala, pode escrever alguns códigos para solicitar, à excepção os dados, leia-o para uma folha de cálculo e assim sucessivamente. Por exemplo:

    private IEnumerable<T> DeserializeMany<T>(string folderName)
    {
      var files = Directory.EnumerateFiles(folderName, "*.blob", SearchOption.AllDirectories);
      foreach (var file in files)
      {
         using (var fileReader = File.OpenText(file))
         {
            string fileContent = fileReader.ReadToEnd();
            IEnumerable<string> entities = fileContent.Split('\n').Where(s => !string.IsNullOrWhiteSpace(s));
            foreach (var entity in entities)
            {
                yield return JsonConvert.DeserializeObject<T>(entity);
            }
         }
      }
    }

Para um maior exemplo de código, consulte [utilizando uma função de trabalho][exportasa].

## <a name="delete"></a>Eliminar dados antigos
Tenha em atenção que é responsável por gerir a sua capacidade de armazenamento e eliminar os dados antigos, se necessário.

## <a name="if-you-regenerate-your-storage-key"></a>Se voltar a gerar a chave de armazenamento...
Se alterar a chave para o armazenamento, a exportação contínua será deixam de funcionar. Verá uma notificação na sua conta do Azure.

Abra o painel de exportação contínua e editar a exportação. Editar o destino de exportação, mas basta deixar o mesmo armazenamento seleccionado. Clique em OK para confirmar.

![Editar a exportação contínua, abrir e fechar thee Exportar destino.](./media/app-insights-export-telemetry/07-resetstore.png)

A exportação contínua será reiniciado.

## <a name="export-samples"></a>Exemplos de exportação

* [Exportar para o SQL Server utilizando o Stream Analytics][exportasa]
* [Exemplo de análise de fluxo 2](app-insights-export-stream-analytics.md)

No escalas maiores, considere [HDInsight](https://azure.microsoft.com/services/hdinsight/) -clusters do Hadoop na nuvem. O HDInsight fornece uma variedade de tecnologias para gerir e análise de macrodados e pode utilizá-lo para processar os dados que tenham sido exportados do Application Insights.

## <a name="q--a"></a>P&R
* *Mas todas as pretendo que uma única transferência de um gráfico.*  

    Sim, pode fazê-lo. Na parte superior do painel, clique em **exportar dados**.
* *Posso configurar uma exportação, mas não existe nenhum dado no meu arquivo.*

    Application Insights recebeu qualquer telemetria da sua aplicação, uma vez que configurou a exportação? Só irá receber novos dados.
* *Tentou definir uma exportação, mas foi negado o acesso*

    Se a conta pertence a sua organização, terá de ser um membro dos grupos de proprietários ou contribuintes.
* *Pode exportar diretamente para o meu próprio arquivo local?*

    Não, Lamentamos. A nossa motor de exportação atualmente só funciona com o storage do Azure neste momento.  
* *Existe algum limite para a quantidade de dados colocados no meu arquivo?*

    Não. Iremos irá manter enviar por push dados até que elimine a exportação. Irá parar se podemos atingiu os limites externos para o blob storage, mas que pretty grande. Está a funcionar para controlar a quantidade de armazenamento que utiliza.  
* *Quantos blobs deverá ver no armazenamento*

  * Para cada tipo de dados selecionados para exportar, um novo blob é criado cada minuto (se dados estiver disponíveis).
  * Além disso, para aplicações com a tráfego elevado, unidades de partições adicionais são alocadas. Neste caso, cada unidade cria um blob cada minuto.
* *Posso regenerada a chave para o meu armazenamento ou alterou o nome do contentor e agora a exportação não funciona.*

    Editar a exportação e abrir o painel de destino de exportação. Deixe o mesmo armazenamento selecionado como anteriormente e clique em OK para confirmar. Exportação será reiniciado. Se a alteração foi dentro de alguns últimos dias, não irá perder dados.
* *Pode interromper a exportação?*

    Sim. Clique em Desativar.

## <a name="code-samples"></a>Exemplos de código

* [Exemplo de análise de fluxo](app-insights-export-stream-analytics.md)
* [Exportar para o SQL Server utilizando o Stream Analytics][exportasa]
* [Referência para os tipos de propriedade e os valores de modelo de dados de detalhado.](app-insights-export-data-model.md)

<!--Link references-->

[exportasa]: app-insights-code-sample-export-sql-stream-analytics.md
[roles]: app-insights-resources-roles-access-control.md
