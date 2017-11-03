---
title: "Tarefa de configuração predefinida para o indexador de suporte de dados do Azure"
description: "Este tópico fornece uma descrição geral das tarefas de configuração predefinida para o indexador de suporte de dados do Azure."
services: media-services
documentationcenter: 
author: Asolanki
manager: cfowler
editor: 
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/03/2017
ms.author: adsolank;juliako;
ms.openlocfilehash: ae6c4da189cd6637b4e1fa9274473b62f6664e51
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="task-preset-for-azure-media-indexer"></a>Tarefa de configuração predefinida para o indexador de suporte de dados do Azure

Indexador de suporte de dados do Azure é um processador de multimédia que utilizar para efetuar as seguintes tarefas: fará com que os ficheiros de suporte de dados e conteúdo pesquisáveis, gerar controla captioning fechada e as palavras-chave, ficheiros de elementos que fazem parte do seu elemento de índice.

Este tópico descreve as tarefas que tem de passar para a tarefa de indexação da configuração predefinida. Por exemplo completado, consulte [indexar os ficheiros de suporte de dados com o indexador de suporte de dados do Azure](media-services-index-content.md).

## <a name="azure-media-indexer-configuration-xml"></a>XML de configuração do indexador de Media Services do Azure

A tabela seguinte explica os elementos e atributos do XML de configuração.

|Nome|Requerer|Descrição|
|---|---|---|
|Input|VERDADEIRO|Ficheiros de recurso que pretende índice.<br/>Indexador de suporte de dados do Azure suporta os seguintes formatos de ficheiro do suporte de dados: MP4 MOV, WMV, MP3, M4A, WMA, AAC, WAV. <br/><br/>Pode especificar o nome de ficheiro (s) no **nome** ou **lista** atributo o **entrada** elemento (tal como mostrado abaixo). Se não especificar o ficheiro de recurso para o índice, é selecionado o ficheiro principal. Não se for definido nenhum ficheiro de elemento principal, o primeiro ficheiro no recurso de entrada está indexado.<br/><br/>Para especificar explicitamente o nome de ficheiro de recurso, efetue:<br/>```<input name="TestFile.wmv" />```<br/><br/>Também pode indexar vários ficheiros de elemento de uma só vez (até 10 ficheiros). Para efetuar este procedimento:<br/>-Criar um ficheiro de texto (o ficheiro de manifesto) e atribua-lhe uma extensão de .lst.<br/>-Adicione uma lista de todos os nomes de ficheiro de recurso no seu elemento de entrada para este ficheiro de manifesto.<br/>-Adicione ficheiros de thanifest (carregamento) para o elemento.<br/>-Especifique o nome do ficheiro de manifesto do atributo de lista de entrada.<br/>```<input list="input.lst">```<br/><br/>**Nota:** se adicionar mais do que 10 ficheiros para o ficheiro de manifesto, a tarefa de indexação irá falhar com o código de erro 2006.|
|Metadados|FALSO|Metadados para os ficheiros de recurso especificado.<br/>```<metadata key="..." value="..." />```<br/><br/>Pode fornecer valores para chaves predefinidas. <br/><br/>Atualmente, as seguintes chaves são suportadas:<br/><br/>**título** e **Descrição** - utilizado para atualizar o modelo de idioma para melhorar a precisão de reconhecimento de voz.<br/>```<metadata key="title" value="[Title of the media file]" /><metadata key="description" value="[Description of the media file]" />```<br/><br/>**nome de utilizador** e **palavra-passe** - utilizado para autenticação quando transferir os ficheiros da internet através de http ou https.<br/>```<metadata key="username" value="[UserName]" /><metadata key="password" value="[Password]" />```<br/>Os valores de nome de utilizador e palavra-passe se aplicam a todos os URLs de suporte de dados no manifesto de entrada.|
|elástica<br/><br/>Adicionar a versão 1.2. Atualmente, a única funcionalidade suportada é o reconhecimento de voz ("ASR").|FALSO|A funcionalidade de reconhecimento de voz tem as seguintes chaves de definições:<br/><br/>Idioma:<br/>-A linguagem natural para ser reconhecido no ficheiro multimedia.<br/>-Inglês, espanhol<br/><br/>CaptionFormats:<br/>-uma lista de valores separados por ponto e vírgula da legenda saída pretendido formata (se aplicável)<br/>-ttml; sami; webvtt<br/><br/><br/>GenerateAIB:<br/>-Um sinalizador booleano que especifica se é ou não um ficheiro AIB necessário (para utilização com o SQL Server e o cliente indexador IFilter). Para obter mais informações, consulte Utilizar AIB os ficheiros com o indexador de suporte de dados do Azure e o SQL Server.<br/>-True; FALSO<br/><br/>GenerateKeywords:<br/>-Um sinalizador booleano especificar se pretende ou não um ficheiro XML de palavra-chave é necessário.<br/>-True; FALSO.|

## <a name="azure-media-indexer-configuration-xml-example"></a>Exemplo do XML de configuração de indexador de suporte de dados do Azure

``` 
<?xml version="1.0" encoding="utf-8"?>  
<configuration version="2.0">  
  <input>  
    <metadata key="title" value="[Title of the media file]" />  
    <metadata key="description" value="[Description of the media file]" />  
  </input>  
  <settings>  
  </settings>  
  
  <features>  
    <feature name="ASR">    
      <settings>  
        <add key="Language" value="English"/>  
        <add key="CaptionFormats" value="ttml;sami;webvtt"/>  
        <add key="GenerateAIB" value ="true" />  
        <add key="GenerateKeywords" value ="true" />  
      </settings>  
    </feature>  
  </features>  
  
</configuration>  
```
  
## <a name="next-steps"></a>Passos seguintes

Consulte [indexar os ficheiros de suporte de dados com o indexador de suporte de dados do Azure](media-services-index-content.md).

