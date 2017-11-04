---
title: "A indexação de ficheiros de suporte de dados com o indexador de Media Services do Azure"
description: "Indexador de suporte de dados do Azure permite-lhe para que o conteúdo dos seus ficheiros de suporte de dados pesquisáveis e para gerar um transcript de texto completo para captioning fechada e as palavras-chave. Este tópico mostra como utilizar o indexador de suporte de dados."
services: media-services
documentationcenter: 
author: Asolanki
manager: cfowler
editor: 
ms.assetid: 827a56b2-58a5-4044-8d5c-3e5356488271
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 07/20/2017
ms.author: adsolank;juliako;johndeu
ms.openlocfilehash: f75be3280ffd869339972859c028a178ec728480
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="indexing-media-files-with-azure-media-indexer"></a>A indexação de ficheiros de suporte de dados com o indexador de Media Services do Azure
Indexador de suporte de dados do Azure permite-lhe para que o conteúdo dos seus ficheiros de suporte de dados pesquisáveis e para gerar um transcript de texto completo para captioning fechada e as palavras-chave. Pode processar um ficheiro de multimédia ou vários num lote.  

> [!IMPORTANT]
> Quando indexação conteúdo, certifique-se de que utiliza ficheiros de suporte de dados com reconhecimento de voz muito simples (sem leitores de música em segundo plano, ruído, efeitos ou hiss microfone). Alguns exemplos de conteúdo apropriado: registadas reuniões, lectures ou apresentações. O seguinte conteúdo poderá não ser adequado para indexação: filmes, mostra TV, qualquer caráter com áudio misto e efeitos de som, mal registadas conteúdo com o ruído de fundo (hiss).
> 
> 

Uma tarefa de indexação pode gerar as saídas seguintes:

* Fechado ficheiros legenda nos seguintes formatos: **SAMI**, **TTML**, e **WebVTT**.
  
    Ficheiros de legendas incluem uma etiqueta chamada Recognizability, as pontuações uma tarefa de indexação com base na forma como reconhecível o reconhecimento de voz as vídeo de origem.  Pode utilizar o valor de Recognizability nos ficheiros de saída do ecrã de Facilidade de utilização. Uma pontuação baixa tal significa fracas resultados de indexação devido a qualidade de áudio.
* Ficheiro de palavra-chave (XML).
* Áudio indexação ficheiro blob (AIB) para utilização com o SQL server.
  
    Para obter mais informações, consulte [utilizando ficheiros de AIB com indexador de suporte de dados do Azure e o SQL Server](https://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/).

Este tópico mostra como criar tarefas de indexação para **um recurso de índice** e **vários ficheiros de índice**.

As atualizações mais recentes do indexador de suporte de dados do Azure, consulte [blogues de Media Services](#preset).

## <a name="using-configuration-and-manifest-files-for-indexing-tasks"></a>Utilizar ficheiros de configuração e de manifesto para tarefas de indexação
Pode especificar mais detalhes para as tarefas de indexação através da utilização de uma configuração de tarefas. Por exemplo, pode especificar que os metadados para utilizar para o ficheiro de suporte de dados. Estes metadados é utilizado pelo motor de idioma para expandir o vocabulário e melhora consideravelmente a precisão de reconhecimento de voz.  Também são capazes de especificar os ficheiros de saída pretendidas.

Também pode processar vários ficheiros de suporte de dados em simultâneo utilizando um ficheiro de manifesto.

Para obter mais informações, consulte [tarefas da configuração predefinida para o indexador de suporte de dados do Azure](https://msdn.microsoft.com/library/dn783454.aspx).

## <a name="index-an-asset"></a>Um recurso de índice
O método seguinte carrega um ficheiro de suporte de dados como um recurso e cria uma tarefa para o elemento de índice.

Tenha em atenção que, não se for especificado nenhum ficheiro de configuração, o ficheiro de multimédia irá ser indexado com todas as definições predefinidas.

    static bool RunIndexingJob(string inputMediaFilePath, string outputFolder, string configurationFile = "")
    {
        // Create an asset and upload the input media file to storage.
        IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
            "My Indexing Input Asset",
            AssetCreationOptions.None);

        // Declare a new job.
        IJob job = _context.Jobs.Create("My Indexing Job");

        // Get a reference to the Azure Media Indexer.
        string MediaProcessorName = "Azure Media Indexer";
        IMediaProcessor processor = GetLatestMediaProcessorByName(MediaProcessorName);

        // Read configuration from file if specified.
        string configuration = string.IsNullOrEmpty(configurationFile) ? "" : File.ReadAllText(configurationFile);

        // Create a task with the encoding details, using a string preset.
        ITask task = job.Tasks.AddNew("My Indexing Task",
            processor,
            configuration,
            TaskOptions.None);

        // Specify the input asset to be indexed.
        task.InputAssets.Add(asset);

        // Add an output asset to contain the results of the job.
        task.OutputAssets.AddNew("My Indexing Output Asset", AssetCreationOptions.None);

        // Use the following event handler to check job progress.  
        job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);

        // Launch the job.
        job.Submit();

        // Check job execution and wait for job to finish.
        Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
        progressJobTask.Wait();

        // If job state is Error, the event handling
        // method for job progress should log errors.  Here we check
        // for error state and exit if needed.
        if (job.State == JobState.Error)
        {
            Console.WriteLine("Exiting method due to job error.");
            return false;
        }

        // Download the job outputs.
        DownloadAsset(task.OutputAssets.First(), outputFolder);

        return true;
    }

    static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
    {
        IAsset asset = _context.Assets.Create(assetName, options);

        var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
        assetFile.Upload(filePath);

        return asset;
    }

    static void DownloadAsset(IAsset asset, string outputDirectory)
    {
        foreach (IAssetFile file in asset.AssetFiles)
        {
            file.Download(Path.Combine(outputDirectory, file.Name));
        }
    }

    static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors
        .Where(p => p.Name == mediaProcessorName)
        .ToList()
        .OrderBy(p => new Version(p.Version))
        .LastOrDefault();

        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor",
                                                       mediaProcessorName));

        return processor;
    }  
<!-- __ -->
### <a id="output_files"></a>Ficheiros de saída
Por predefinição, uma tarefa de indexação gera os seguintes ficheiros de saída. No primeiro elemento de saída serão armazenados os ficheiros.

Quando existe mais do que um ficheiro de suporte de dados de entrada, o indexador irá gerar um ficheiro de manifesto para as saídas da tarefa, com o nome 'JobResult.txt'. Para cada ficheiro de suporte de dados de entrada, o AIB resultante, SAMI, TTML, WebVTT e ficheiros de palavra-chave, são sequencialmente numerados e com o nome de utilizar o Alias"".

| Nome de ficheiro | Descrição |
| --- | --- |
| **InputFileName.aib** |Ficheiro de blob de indexação áudio. <br/><br/> Ficheiro de áudio indexar o Blob (AIB) é um ficheiro binário que pode ser pesquisado no Microsoft SQL server utilizando a pesquisa em texto completo.  O ficheiro AIB é mais poderoso que os ficheiros de legenda simples, porque contém alternativas para cada palavra, permitindo uma experiência de pesquisa muito mais rica. <br/> <br/>Requer a instalação do Suplemento SQL Server do indexador num máquina com Microsoft SQL server 2008 ou posterior. Pesquisar AIB com o Microsoft SQL pesquisa de texto completo do servidor fornece mais exatos resultados de pesquisa que a procura os ficheiros de legendas gerados pelo WAMI. Isto acontece porque o AIB contém alternativas word que som semelhante ao passo que os ficheiros de legendas contenham a palavra mais elevada de confiança para cada segmento de áudio. Se procurar palavras ditas upmost importância, em seguida, é recomendado para utilizar o AIB em conjunto com o Microsoft SQL Server.<br/><br/> Para transferir o suplemento do, clique em <a href="http://aka.ms/indexersql">suplemento do SQL do Azure Media indexador</a>. <br/><br/>Também é possível utilizar outros motores de busca como o Apache Lucene/Solr para simplesmente o vídeo com base na legenda fechada e os ficheiros XML de palavra-chave de índice, mas tal resultará em menos exatos resultados da pesquisa. |
| **InputFileName.smi**<br/>**InputFileName.ttml**<br/>**InputFileName.vtt** |Fechado ficheiros de legenda (CC) nos formatos SAMI, TTML e WebVTT.<br/><br/>Podem ser utilizadas para tornar o ficheiros de áudio e vídeos acessível para pessoas com hearing disability.<br/><br/>Fechado de legenda de ficheiros incluem uma etiqueta chamada <b>Recognizability</b> que pontua uma tarefa de indexação com base na forma como reconhecível o reconhecimento de voz as vídeo de origem.  Pode utilizar o valor de <b>Recognizability</b> nos ficheiros de saída do ecrã de Facilidade de utilização. Uma pontuação baixa tal significa fracas resultados de indexação devido a qualidade de áudio. |
| **InputFileName.kw.xml<br/>InputFileName.info** |Palavra-chave e as informações de ficheiros. <br/><br/>Ficheiro de palavra-chave é um ficheiro XML que contém as palavras-chave extraídas do conteúdo reconhecimento de voz, com frequência e informações de deslocamento. <br/><br/>O ficheiro de informações é um ficheiro de texto simples que contém informações granulares sobre cada termo reconhecido. A primeira linha é especial e contém a classificação de Recognizability. Cada linha subsequente é uma lista separada por separador dos seguintes dados: iniciar o tempo, a hora de fim, word/frase, confiança. Os tempos são indicados em segundos e a confiança é fornecida como um número entre 0-1. <br/><br/>Linha de exemplo: "1.20 1.45 word 0.67" <br/><br/>Estes ficheiros podem ser utilizados para um número de objetivos, como, por exemplo, para efetuar análises de reconhecimento de voz, ou expostos a motores de pesquisa, tais como o Bing, Google ou do Microsoft SharePoint para tornar os ficheiros de suporte de dados mais detetável ou até mesmo utilizados para fornecer os anúncios mais relevantes. |
| **JobResult.txt** |Manifesto de saída, presente apenas quando a indexação de vários ficheiros, que contém as seguintes informações:<br/><br/><table border="1"><tr><th>InputFile</th><th>Alias</th><th>MediaLength</th><th>Erro</th></tr><tr><td>a.mp4</td><td>Media_1</td><td>300</td><td>0</td></tr><tr><td>b.mp4</td><td>Media_2</td><td>0</td><td>3000</td></tr><tr><td>c.mp4</td><td>Media_3</td><td>600</td><td>0</td></tr></table><br/> |

Se não estiver todos os ficheiros de suporte de dados de entrada são indexados com êxito, a tarefa de indexação irá falhar com o código de erro 4000. Para obter mais informações, consulte [códigos de erro](#error_codes).

## <a name="index-multiple-files"></a>Vários ficheiros de índice
O método seguinte carrega vários ficheiros de suporte de dados como um recurso e cria uma tarefa de todos os estes ficheiros batch do índice.

Um ficheiro de manifesto com a extensão de .lst se encontra criada e de carregamento para o elemento. O ficheiro de manifesto contém a lista de todos os ficheiros de recurso. Para obter mais informações, consulte [tarefas da configuração predefinida para o indexador de suporte de dados do Azure](https://msdn.microsoft.com/library/dn783454.aspx).

    static bool RunBatchIndexingJob(string[] inputMediaFiles, string outputFolder)
    {
        // Create an asset and upload to storage.
        IAsset asset = CreateAssetAndUploadMultipleFiles(inputMediaFiles,
            "My Indexing Input Asset - Batch Mode",
            AssetCreationOptions.None);

        // Create a manifest file that contains all the asset file names and upload to storage.
        string manifestFile = "input.lst";            
        File.WriteAllLines(manifestFile, asset.AssetFiles.Select(f => f.Name).ToArray());
        var assetFile = asset.AssetFiles.Create(Path.GetFileName(manifestFile));
        assetFile.Upload(manifestFile);

        // Declare a new job.
        IJob job = _context.Jobs.Create("My Indexing Job - Batch Mode");

        // Get a reference to the Azure Media Indexer.
        string MediaProcessorName = "Azure Media Indexer";
        IMediaProcessor processor = GetLatestMediaProcessorByName(MediaProcessorName);

        // Read configuration.
        string configuration = File.ReadAllText("batch.config");

        // Create a task with the encoding details, using a string preset.
        ITask task = job.Tasks.AddNew("My Indexing Task - Batch Mode",
            processor,
            configuration,
            TaskOptions.None);

        // Specify the input asset to be indexed.
        task.InputAssets.Add(asset);

        // Add an output asset to contain the results of the job.
        task.OutputAssets.AddNew("My Indexing Output Asset - Batch Mode", AssetCreationOptions.None);

        // Use the following event handler to check job progress.  
        job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);

        // Launch the job.
        job.Submit();

        // Check job execution and wait for job to finish.
        Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
        progressJobTask.Wait();

        // If job state is Error, the event handling
        // method for job progress should log errors.  Here we check
        // for error state and exit if needed.
        if (job.State == JobState.Error)
        {
            Console.WriteLine("Exiting method due to job error.");
            return false;
        }

        // Download the job outputs.
        DownloadAsset(task.OutputAssets.First(), outputFolder);

        return true;
    }

    private static IAsset CreateAssetAndUploadMultipleFiles(string[] filePaths, string assetName, AssetCreationOptions options)
    {
        IAsset asset = _context.Assets.Create(assetName, options);

        foreach (string filePath in filePaths)
        {
            var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
            assetFile.Upload(filePath);
        }

        return asset;
    }

### <a name="partially-succeeded-job"></a>Tarefa parcialmente criada com êxito
Se não estiver todos os ficheiros de suporte de dados de entrada são indexados com êxito, a tarefa de indexação irá falhar com o código de erro 4000. Para obter mais informações, consulte [códigos de erro](#error_codes).

As saídas mesmas (como as tarefas com êxito) são geradas. Pode consultar o ficheiro de manifesto de saída para saber quais os ficheiros de entrada são falhou, de acordo com os valores da coluna de erro. Para os ficheiros de entrada que falharam, AIB resultante, SAMI, TTML, WebVTT e palavra-chave ficheiros não serão gerados.

### <a id="preset"></a>Predefinição de tarefas para o indexador de Media Services do Azure
O processamento do indexador de suporte de dados do Azure pode ser personalizado, fornecendo uma tarefa opcional juntamente com a tarefa da configuração predefinida.  O seguinte descreve o formato do xml configuração.

| Nome | Requerer | Descrição |
| --- | --- | --- |
| **entrada** |FALSO |Ficheiros de recurso que pretende índice.</p><p>Indexador de suporte de dados do Azure suporta os seguintes formatos de ficheiro do suporte de dados: MP4 WMV, MP3, M4A, WMA, AAC, WAV.</p><p>Pode especificar o nome de ficheiro (s) no **nome** ou **lista** atributo o **entrada** elemento (tal como mostrado abaixo). Se não especificar o ficheiro de recurso para o índice, é selecionado o ficheiro principal. Não se for definido nenhum ficheiro de elemento principal, o primeiro ficheiro no recurso de entrada está indexado.</p><p>Para especificar explicitamente o nome de ficheiro de recurso, efetue:<br/>`<input name="TestFile.wmv">`<br/><br/>Também pode indexar vários ficheiros de elemento de uma só vez (até 10 ficheiros). Para efetuar este procedimento:<br/><br/><ol class="ordered"><li><p>Criar um ficheiro de texto (o ficheiro de manifesto) e atribua-lhe uma extensão de .lst. </p></li><li><p>Adicione uma lista de todos os nomes de ficheiro de recurso no seu elemento de entrada para este ficheiro de manifesto. </p></li><li><p>Adicione ficheiros de thanifest (carregamento) para o elemento.  </p></li><li><p>Especifique o nome do ficheiro de manifesto do atributo de lista de entrada.<br/>`<input list="input.lst">`</li></ol><br/><br/>Nota: Se adicionar mais do que 10 ficheiros para o ficheiro de manifesto, a tarefa de indexação falhará com o código de erro 2006. |
| **metadados** |FALSO |Metadados para os ficheiros de recurso especificado utilizados para vocabulário Adaptation.  É útil para preparar o indexador para reconhecer palavras vocabulário não padrão, tais como os substantivos adequado.<br/>`<metadata key="..." value="..."/>` <br/><br/>Pode fornecer **valores** para predefinidas **chaves**. Atualmente são suportadas as seguintes chaves:<br/><br/>"title" e "Descrição" - utilizado para adaptation vocabulário para otimizar a linguagem de modelo para o seu trabalho e melhorar a precisão de reconhecimento de voz.  Os valores de seed pesquisas de Internet para localizar texto contexto relevante documentos, utilizando os conteúdos para aumentar o dicionário interno durante a sua tarefa Indexing.<br/>`<metadata key="title" value="[Title of the media file]" />`<br/>`<metadata key="description" value="[Description of the media file] />"` |
| **funcionalidades** <br/><br/> Adicionar a versão 1.2. Atualmente, a única funcionalidade suportada é o reconhecimento de voz ("ASR"). |FALSO |A funcionalidade de reconhecimento de voz tem as seguintes chaves de definições:<table><tr><th><p>Chave</p></th>        <th><p>Descrição</p></th><th><p>Valor de exemplo</p></th></tr><tr><td><p>Idioma</p></td><td><p>A linguagem natural para ser reconhecido no ficheiro multimedia.</p></td><td><p>Inglês, espanhol</p></td></tr><tr><td><p>CaptionFormats</p></td><td><p>uma lista de valores separados por ponto e vírgula dos formatos de legenda saída pretendida (se aplicável)</p></td><td><p>ttml; sami; webvtt</p></td></tr><tr><td><p>GenerateAIB</p></td><td><p>Um sinalizador booleano que especifica se é ou não um ficheiro AIB necessário (para utilização com o SQL Server e o cliente indexador IFilter).  Para obter mais informações, consulte <a href="http://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/">utilizando ficheiros de AIB com indexador de suporte de dados do Azure e o SQL Server</a>.</p></td><td><p>TRUE; FALSO</p></td></tr><tr><td><p>GenerateKeywords</p></td><td><p>Um sinalizador booleano que especifica se pretende ou não um ficheiro XML de palavra-chave é necessário.</p></td><td><p>TRUE; FALSO. </p></td></tr><tr><td><p>ForceFullCaption</p></td><td><p>Um sinalizador booleano que especifica se deve ou não forçar completas legendas (independentemente do nível de confiança).  </p><p>Predefinição é false, caso em que palavras e expressões que têm um menos do que o nível de confiança de 50% são omitidos das saídas da legenda final e substituídos por nas reticências ("...").  Nas reticências são úteis para auditoria e controlo de qualidade de legenda.</p></td><td><p>TRUE; FALSO. </p></td></tr></table> |

### <a id="error_codes"></a>Códigos de erro
No caso de um erro, o indexador de suporte de dados do Azure devem reportar fazer uma cópia de um dos códigos de erro seguinte:

| Código | Nome | Razões possíveis |
| --- | --- | --- |
| 2000 |Configuração inválida |Configuração inválida |
| 2001 |Recursos de entrada inválidos |Falta a entrada ativos ou elemento vazio. |
| 2002 |Manifesto inválido |O manifesto está vazio ou manifest contém itens inválidos. |
| 2003 |Falha ao transferir o ficheiro de multimédia |URL inválido no ficheiro de manifesto. |
| 2004 |Protocolo não suportado |Não é suportado o protocolo do URL de suporte de dados. |
| 2005 |Tipo de ficheiro não suportado |Tipo de ficheiro do suporte de dados de entrada não é suportado. |
| 2006 |Demasiados ficheiros de entrada |Existem mais de 10 ficheiros no manifesto de entrada. |
| 3000 |Falha ao descodificar o ficheiro de multimédia |Codec de suporte de dados não suportado <br/>ou<br/> Ficheiros de suporte de dados danificada <br/>ou<br/> Nenhuma sequência de áudio no suporte de dados de entrada. |
| 4000 |A indexação de batch parcialmente foi concluída com êxito |Alguns dos ficheiros de suporte de dados de entrada não ser indexados. Para obter mais informações, consulte <a href="#output_files">ficheiros de saída</a>. |
| outro |Erros internos |Contacte a equipa de suporte. indexer@microsoft.com |

## <a id="supported_languages"></a>Idiomas suportados
Atualmente, são suportados os idiomas inglês e espanhol. Para obter mais informações, consulte [a mensagem de blogue de versão v1.2](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/).

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Ligações relacionadas
[Descrição geral da análise de serviços de multimédia do Azure](media-services-analytics-overview.md)

[Utilizar AIB ficheiros com o indexador de Media Services do Azure e do SQL Server](https://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/)

[A indexação de ficheiros de suporte de dados pré-visualização do indexador 2 Media Services do Azure](media-services-process-content-with-indexer2.md)

