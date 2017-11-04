---
title: "Notas de versão para o Data Management Gateway | Microsoft Docs"
description: "Notas de versão do Data Management Gateway tory"
services: data-factory
author: nabhishek
manager: jhubbard
editor: monicar
ms.assetid: 14762e82-76d9-41c4-ba9f-14a54da29c36
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2017
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 3cc96b22b45e5c741991b11e1bbee758a569bed9
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="release-notes-for-data-management-gateway"></a>Notas de lançamento do Gateway de Gestão de Dados
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory, que está geralmente disponível (GA). Se estiver a utilizar a versão 2 do serviço do Data Factory, o que está em pré-visualização, consulte [autoalojado tempo de execução de integração na V2](../create-self-hosted-integration-runtime.md).

Um dos desafios para integração de dados modernas é mover dados para e no local para a nuvem. Fábrica de dados torna esta integração com o Data Management Gateway, que é um agente que pode instalar no local para ativar o movimento de dados de híbrida.

Consulte os artigos seguintes para obter informações detalhadas sobre o Data Management Gateway e como utilizá-la:

*  [Data Management Gateway](data-factory-data-management-gateway.md)
*  [Mover dados entre no local e na nuvem utilizando o Azure Data Factory](data-factory-move-data-between-onprem-and-cloud.md)


## <a name="current-version"></a>VERSÃO ATUAL 
Vamos manter não mais aqui as notas de versão. Obter notas de versão mais recentes [aqui](https://go.microsoft.com/fwlink/?linkid=853077)




## <a name="earlier-versions"></a>Versões anteriores
## <a name="21063477"></a>2.10.6347.7
### <a name="enhancements-"></a>Melhoramentos-
- Pode adicionar entradas DNS para o barramento de serviço da lista branca, em vez de adicionar à lista branca todos os endereços IP do Azure da sua firewall (se necessário). Pode encontrar a respetiva entrada DNS no portal do Azure (Data Factory -> 'Criar e implementar' -> 'Gateways' -> "serviceUrls" (no JSON)
- Conector do HDFS suporta agora autoassinado certificado público, permitindo-lhe ignorar a validação de SSL.
- Fixo: Problema com o gateway offline durante a atualização (devido a dissimetrias do relógio)


## <a name="2963132"></a>2.9.6313.2
### <a name="enhancements-"></a>Melhoramentos-
-   Pode adicionar entradas DNS para a lista branca de Service Bus, em vez de adicionar à lista branca todos os endereços IP do Azure da sua firewall (se necessário). Obter mais detalhes aqui.
-   Pode agora copiar dados para/de um único bloco blob até 4.75 TB, que é o tamanho máximo suportado de BLOBs de blocos. (limite anterior era 195 GB).
-   Fixa: Fora de problema de memória ao unzipping vários ficheiros pequenos durante a atividade de cópia.
-   Fixo: Índice fora do problema de intervalo durante a cópia da base de dados de documento a um servidor de SQL no local com a funcionalidade de idempotency.
-   Fixo: Script de limpeza do SQL Server não funciona com o servidor de SQL no local do Assistente para copiar.
-   Fixo: Nome de coluna com espaço no final não funciona na atividade de cópia.

## <a name="28662833"></a>2.8.66283.3
### <a name="enhancements-"></a>Melhoramentos-
- Fixo: Problema com a falta de credenciais no reinício do computador de gateway.
- Fixo: Problema com o registo durante gateway restaurar o utilizando um ficheiro de cópia de segurança.


## <a name="2762401"></a>2.7.6240.1
### <a name="enhancements-"></a>Melhoramentos-
- Fixa: Leitura incorreta de um valor nulo Decimal do Oracle como origem.

## <a name="2661922"></a>2.6.6192.2
### <a name="whats-new"></a>Novidades
- Os clientes podem fornecer comentários no gateway registar experiência.
- Suportar um novo formato de compressão: ZIP (Deflate)

### <a name="enhancements-"></a>Melhoramentos-
- Melhoramento de desempenho para Sink do Oracle, origem HDFS.
- Correção de erros para automático de gateway atualizar, capacidade de processamento paralelo de gateway.


## <a name="2561641"></a>2.5.6164.1
### <a name="enhancements"></a>Melhoramentos
- Melhor e mais robusta Gateway registo experiência-agora pode controlar o estado do progresso durante o processo de registo de Gateway, o que torna o registo experiência mais dinâmico.
- Melhoria no Gateway restaurar processo - que pode continuar a recuperar gateway, mesmo se não tiver o ficheiro de cópia de segurança de gateway com esta atualização. Isto seria requerem a repor as credenciais de serviço ligado no Portal.
- Correção de erro.

## <a name="2461511"></a>2.4.6151.1

### <a name="whats-new"></a>Novidades

- Agora pode armazenar credenciais da origem de dados localmente. As credenciais estão encriptadas. As credenciais da origem de dados podem ser recuperadas e restaurados com autoridade utilizando o ficheiro de cópia de segurança que pode ser exportado a partir do Gateway existente, todas as no local.

### <a name="enhancements-"></a>Melhoramentos-

- Experiência de registo de Gateway melhor e mais robusta.
- Suporta a deteção automática da configuração de QuoteChar para formato de texto no Assistente para copiar e melhorar a precisão de deteção de formato geral.

## <a name="2361002"></a>2.3.6100.2

- Suporte firstRowAsHeader e a deteção automática SkipLineCount no Assistente de cópia de ficheiros de texto no sistema de ficheiros no local e HDFS.
- Melhorar a estabilidade da ligação de rede entre o gateway e o Service Bus
- Algumas correções de erros


## <a name="2260721"></a>2.2.6072.1

*  Suporta a definição de proxy HTTP para o gateway utilizando o Gestor de configuração do Gateway. Se configurado, o Blob do Azure, tabelas do Azure, Azure Data Lake e Document DB são acedidos através do proxy HTTP.
*  Suporta cabeçalho de processamento para TextFormat ao copiar dados de/para Blob do Azure, Azure Data Lake Store, sistema de ficheiros no local e o HDFS no local.
*  Suporta a cópia dos dados de BLOBs de acréscimo e BLOBs de páginas, juntamente com o Blob de bloco já suportadas.
*  Apresenta um novo estado do gateway **Online (limitado)**, que indica que a funcionalidade principal do gateway funciona, exceto o suporte de operação interativa do Assistente para copiar.
*  Melhora a robustez do registo de gateway utilizando a chave de registo.

## <a name="216040"></a>2.1.6040.

*  Controlador DB2 está incluído no pacote de instalação do gateway agora. Não é necessário instalá-lo em separado.
*  Controlador DB2 suporta agora o z/SO e DB2 para posso (como / 400) juntamente com as plataformas suportadas já (Linux, Unix e Windows).
*  Suporta a utilização de base de dados do Azure Cosmos como uma origem ou de destino para os arquivos de dados no local
*  Suporta a cópia dos dados de/para acesso frequente frio/blob storage, juntamente com a conta de armazenamento para fins gerais já suportadas.
*  Permite-lhe ligar ao servidor do SQL Server no local através de gateway com privilégios de início de sessão remoto.  

## <a name="2060131"></a>2.0.6013.1

*  Pode selecionar o idioma idioma a ser utilizado por um gateway durante a instalação manual.

*  Quando o gateway não funciona conforme esperado, pode optar por enviar registos de gateway dos últimos sete dias para a Microsoft para facilitar a resolução do problema. Se o gateway não está ligado ao serviço em nuvem, pode optar por guardar e registos do gateway de arquivo.  

*  Melhoramentos de interface de utilizador para o Gestor de configuração do gateway:

    *  Tornar o estado do gateway mais visíveis no separador início.

    *  Controlos reorganizados e simplificados.

    *  Pode copiar dados a partir de um armazenamento utilizando o [ferramenta de pré-visualização de cópia sem código](data-factory-copy-data-wizard-tutorial.md). Consulte [testado cópia](data-factory-copy-activity-performance.md#staged-copy) para obter detalhes sobre esta funcionalidade em geral.
*  Pode utilizar o Data Management Gateway para dados de entrada diretamente a partir de uma base de dados do SQL Server no local para o Azure Machine Learning.

*  Melhorias de desempenho

    * Melhore o desempenho sobre a visualização de esquema/pré-visualização contra do SQL Server na ferramenta de pré-visualização de cópia sem código.

## <a name="11259531"></a>1.12.5953.1

*  Correções de erros

## <a name="11159181"></a>1.11.5918.1

*  Tamanho máximo do registo de eventos do gateway foi aumentado de 1 MB para 40 MB.

*  Uma caixa de diálogo de aviso é apresentada no caso seja necessário um reinício durante a atualização automática do gateway. Pode optar por reiniciar direito, em seguida, ou posterior.

*  No caso de falha de atualização automática, o instalador do gateway repete a atualização automática três vezes no máximo.

*  Melhorias de desempenho

    * Melhore o desempenho para carregar tabelas grandes a partir do servidor no local, num cenário de cópia sem código.

*  Correções de erros

## <a name="11058921"></a>1.10.5892.1

*  Melhorias de desempenho

*  Correções de erros

## <a name="1958652"></a>1.9.5865.2

*  Capacidade de atualização de automática zero touch
*  Ícone de tabuleiro novo com indicadores de estado do gateway
*  Capacidade para "Agora Update" do cliente
*  Capacidade de definir a hora de agendamento de atualização
*  Script do PowerShell para ativando ou desativando a atualização automática /
*  Suporte para o formato JSON  
*  Melhorias de desempenho
*  Correções de erros

## <a name="1858221"></a>1.8.5822.1

*  Melhorar a experiência de resolução de problemas
*  Melhorias de desempenho
*  Correções de erros

### <a name="1757951"></a>1.7.5795.1

*  Melhorias de desempenho
*  Correções de erros

### <a name="1757641"></a>1.7.5764.1

*  Melhorias de desempenho
*  Correções de erros

### <a name="1657351"></a>1.6.5735.1

*  Suporte no local HDFS origem/Sink
*  Melhorias de desempenho
*  Correções de erros

### <a name="1656961"></a>1.6.5696.1

*  Melhorias de desempenho
*  Correções de erros

### <a name="1656761"></a>1.6.5676.1

*  Ferramentas de diagnóstico de suporte no Configuration Manager
*  Colunas de tabela de suporte para origens de dados em tabela do Azure Data Factory
*  Suporte de armazém de dados SQL do Azure Data Factory
*  Suporte Reclusive BlobSource e FileSource do Azure Data Factory
*  Suporta CopyBehavior – MergeFiles, PreserveHierarchy e FlattenHierarchy BlobSink e FileSink com cópia binária do Azure Data Factory
*  Suporta a atividade de cópia de relatório de progresso do Azure Data Factory
*  Validação da conectividade de origem de dados de suporte do Azure Data Factory
*  Correções de erros

### <a name="1656721"></a>1.6.5672.1

*  Nome da tabela de suporte para a origem de dados ODBC do Azure Data Factory
*  Melhorias de desempenho
*  Correções de erros

### <a name="1656581"></a>1.6.5658.1

*  Sink do ficheiro de suporte do Azure Data Factory
*  Suporte preservar hierarquia na cópia de binária para o Azure Data Factory
*  Suporta Idempotency de atividade de cópia do Azure Data Factory
*  Correções de erros

### <a name="1656401"></a>1.6.5640.1

*  Suporte 3 mais origens de dados para o Azure Data Factory (ODBC OData, HDFS)
*  Suporte caráter da plica no parser de csv para o Azure Data Factory
*  Suporte de compressão (BZip2)
*  Correções de erros

### <a name="1556121"></a>1.5.5612.1

*  Suporta cinco bases de dados relacionais (MySQL, PostgreSQL, DB2, Teradata e Sybase) do Azure Data Factory
*  Suporte de compressão (Gzip e Deflate)
*  Melhorias de desempenho
*  Correções de erros

### <a name="1455491"></a>1.4.5549.1

*  Adicionar suporte de origem de dados Oracle para o Azure Data Factory
*  Melhorias de desempenho
*  Correções de erros

### <a name="1454921"></a>1.4.5492.1

*  Binário unificado que suporte serviços do Microsoft Azure Data Factory e Office 365 Power BI
*  Otimizar o processo de IU de configuração e de registo
*  O Azure Data Factory – suportam a entrada do Azure e de saída para a origem de dados do SQL Server

### <a name="1253031"></a>1.2.5303.1

*  Corrija o problema de tempo limite para suportar mais ligações de origens de dados de um processo demorado.

### <a name="1155268"></a>1.1.5526.8

*  Requer o .NET Framework 4.5.1 como pré-requisito durante a configuração.

### <a name="1051442"></a>1.0.5144.2

*  Não existem alterações que afetam os cenários do Azure Data Factory.
