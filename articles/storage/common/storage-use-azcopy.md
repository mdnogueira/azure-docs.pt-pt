---
title: Copiar ou mover dados para o Storage do Azure com o AzCopy no Windows | Microsoft Docs
description: "Utilize o AzCopy no utilitário Windows para mover ou copiar dados de ou para o blob, tabela e o conteúdo do ficheiro. Copiar dados para o armazenamento do Azure de ficheiros locais ou copie os dados dentro ou entre contas de armazenamento. Migre facilmente os dados para armazenamento do Azure."
services: storage
documentationcenter: 
author: seguler
manager: jahogg
editor: tysonn
ms.assetid: aa155738-7c69-4a83-94f8-b97af4461274
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/14/2017
ms.author: seguler
ms.openlocfilehash: 1a4c52babe76e59eacb30e8be91ed934cdbe305b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="transfer-data-with-the-azcopy-on-windows"></a>Transferência de dados com o AzCopy no Windows
O AzCopy é um utilitário da linha de comandos concebido para copiar dados para e do armazenamento de Blobs do Microsoft Azure, ficheiros e tabela utilizando comandos simples com um desempenho ideal. Pode copiar dados de um objeto para outro dentro da sua conta de armazenamento, ou entre contas de armazenamento.

Existem duas versões do AzCopy que pode transferir. AzCopy no Windows baseia-se com o .NET Framework e o estilo de Windows esta oferece opções de linha de comandos. [AzCopy no Linux](storage-use-azcopy-linux.md) baseia-se com o .NET Core Framework que está direcionada para plataformas Linux oferta estilo POSIX opções da linha de comandos. Este artigo abrange AzCopy no Windows.

## <a name="download-and-install-azcopy-on-windows"></a>Transfira e instale o AzCopy no Windows

Transferir o [versão mais recente do AzCopy no Windows](http://aka.ms/downloadazcopy).

Depois de instalar o AzCopy no Windows utilizando o instalador, abra uma janela de comandos e navegue para o diretório de instalação do AzCopy no seu computador - onde o `AzCopy.exe` executável está localizado. Se assim o desejar, pode adicionar a localização de instalação do AzCopy para o caminho do sistema. Por predefinição, o AzCopy é instalado para `%ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy` ou `%ProgramFiles%\Microsoft SDKs\Azure\AzCopy`.

## <a name="writing-your-first-azcopy-command"></a>Escrever o seu primeiro comando do AzCopy

A sintaxe básica para comandos do AzCopy é:

```azcopy
AzCopy /Source:<source> /Dest:<destination> [Options]
```

Os exemplos seguintes demonstram uma variedade de cenários para copiar dados para e do Microsoft Azure, ficheiros, tabelas e Blobs. Consulte o [AzCopy parâmetros](#azcopy-parameters) secção para obter uma explicação detalhada de parâmetros utilizados em cada amostra.

## <a name="download-blobs-from-blob-storage"></a>Transferir blobs do armazenamento de BLOBs

Vamos ver várias formas para transferir blobs utilizando o AzCopy.

### <a name="download-a-single-blob"></a>Transferir um blob único

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:"abc.txt"
```

Tenha em atenção que, se a pasta `C:\myfolder` não existir, AzCopy cria e transferência `abc.txt ` para a nova pasta.

### <a name="download-a-single-blob-from-the-secondary-region"></a>Transferir um blob único a partir da região secundária

```azcopy
AzCopy /Source:https://myaccount-secondary.blob.core.windows.net/mynewcontainer /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt
```

Tenha em atenção que tem de ter o armazenamento georredundante de acesso de leitura ativado para aceder a região secundária.

### <a name="download-all-blobs-in-a-container"></a>Transferir todos os blobs num contentor

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /S
```

Suponha que residem os seguintes blobs no contentor especificado:  

    abc.txt
    abc1.txt
    abc2.txt
    vd1\a.txt
    vd1\abcd.txt

Após a operação de transferência, o diretório `C:\myfolder` inclui os seguintes ficheiros:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\vd1\a.txt
    C:\myfolder\vd1\abcd.txt

Se não especificar opção `/S`, não existem blobs são transferidos.

### <a name="download-blobs-with-a-specific-prefix"></a>Transferir blobs com um prefixo específico

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:a /S
```

Assumir que residem os seguintes blobs no contentor especificado. Todos os blobs a partir do prefixo `a` são transferidas:

    abc.txt
    abc1.txt
    abc2.txt
    xyz.txt
    vd1\a.txt
    vd1\abcd.txt

Após a operação de transferência, a pasta `C:\myfolder` inclui os seguintes ficheiros:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt

O prefixo aplica-se para o diretório virtual, o que faz a primeira parte do nome do blob. No exemplo mostrado acima, o diretório virtual não corresponde o prefixo especificado, pelo que não está a ser transferido. Além disso, se a opção `\S` não for especificado, AzCopy não transferir blobs.

### <a name="set-the-last-modified-time-of-exported-files-to-be-same-as-the-source-blobs"></a>Defina o período de última modificação de ficheiros exportados para ser o mesmo que os blobs de origem

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT
```

Também pode excluir os blobs da operação de transferência com base na respetiva hora de última modificação. Por exemplo, se pretende excluir blobs cuja última hora de modificação é igual ou mais recente do que o ficheiro de destino, adicione o `/XN` opção:

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT /XN
```

Se pretende excluir blobs cuja última hora de modificação é igual ou mais antiga do que o ficheiro de destino, adicione o `/XO` opção:

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT /XO
```

## <a name="upload-blobs-to-blob-storage"></a>Carregue os blobs no armazenamento de BLOBs

Vamos ver várias formas de carregar a blobs com AzCopy.

### <a name="upload-a-single-blob"></a>Carregar um blob único

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:"abc.txt"
```

Se o contentor de destino especificado não existir, o AzCopy criá-lo e carrega o ficheiro para a mesma.

### <a name="upload-a-single-blob-to-a-virtual-directory"></a>Carregar um blob único para um diretório virtual

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer/vd /DestKey:key /Pattern:abc.txt
```

Se o diretório virtual especificado não existir, o AzCopy carrega o ficheiro para incluir o diretório virtual no respetivo nome (*por exemplo,*, `vd/abc.txt` no exemplo acima).

### <a name="upload-all-blobs-in-a-folder"></a>Carregar todos os blobs numa pasta

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /S
```

Especificar a opção `/S` carrega o conteúdo do diretório especificado para recursivamente de armazenamento de BLOBs, que significa que todas as subpastas e os ficheiros são carregados bem. Por exemplo, suponha residem os seguintes ficheiros na pasta `C:\myfolder`:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\subfolder\a.txt
    C:\myfolder\subfolder\abcd.txt

Após a operação de carregamento, o contentor inclui os seguintes ficheiros:

    abc.txt
    abc1.txt
    abc2.txt
    subfolder\a.txt
    subfolder\abcd.txt

Se não especificar opção `/S`, AzCopy carregue em modo recursivo. Após a operação de carregamento, o contentor inclui os seguintes ficheiros:

    abc.txt
    abc1.txt
    abc2.txt

### <a name="upload-blobs-matching-a-specific-pattern"></a>Carregar um padrão específico de correspondência de blobs

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:a* /S
```

Partem do princípio de residem os seguintes ficheiros na pasta `C:\myfolder`:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\xyz.txt
    C:\myfolder\subfolder\a.txt
    C:\myfolder\subfolder\abcd.txt

Após a operação de carregamento, o contentor inclui os seguintes ficheiros:

    abc.txt
    abc1.txt
    abc2.txt
    subfolder\a.txt
    subfolder\abcd.txt

Se não especificar opção `/S`, AzCopy carrega apenas blobs que não residem num diretório virtual:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt

### <a name="specify-the-mime-content-type-of-a-destination-blob"></a>Especifique o tipo de conteúdo de MIME de um blob de destino

Por predefinição, o AzCopy define o tipo de conteúdo de um blob de destino para `application/octet-stream`. A partir da versão 3.1.0, pode especificar explicitamente o tipo de conteúdo através da opção `/SetContentType:[content-type]`. Esta sintaxe define o tipo de conteúdo para todos os blobs numa operação de carregamento.

```azcopy
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.blob.core.windows.net/myContainer/ /DestKey:key /Pattern:ab /SetContentType:video/mp4
```

Se especificar `/SetContentType` sem um valor, o AzCopy define cada blob ou tipo de conteúdo do ficheiro, de acordo com a extensão de ficheiro.

```azcopy
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.blob.core.windows.net/myContainer/ /DestKey:key /Pattern:ab /SetContentType
```

## <a name="copy-blobs-in-blob-storage"></a>Copiar os blobs no armazenamento de BLOBs

Vamos ver várias formas para copiar os blobs de uma localização para outra utilizando o AzCopy.

### <a name="copy-a-single-blob-from-one-container-to-another-within-the-same-storage-account"></a>Copiar um blob único de um contentor para outro dentro da mesma conta de armazenamento 

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1 /Dest:https://myaccount.blob.core.windows.net/mycontainer2 /SourceKey:key /DestKey:key /Pattern:abc.txt
```

Quando copiar um blob dentro de uma conta de armazenamento, um [cópia do lado do servidor](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) é efetuar a operação.

### <a name="copy-a-single-blob-from-one-storage-account-to-another"></a>Copiar um blob único a partir de uma conta de armazenamento para outro

```azcopy
AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt
```

Quando copiar um blob em contas de armazenamento, um [cópia do lado do servidor](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) é efetuar a operação.

### <a name="copy-a-single-blob-from-the-secondary-region-to-the-primary-region"></a>Copiar um blob único da região secundária para a região primária

```azcopy
AzCopy /Source:https://myaccount1-secondary.blob.core.windows.net/mynewcontainer1 /Dest:https://myaccount2.blob.core.windows.net/mynewcontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt
```

Tenha em atenção que tem de ter o armazenamento georredundante de acesso de leitura ativado para aceder ao armazenamento secundário.

### <a name="copy-a-single-blob-and-its-snapshots-from-one-storage-account-to-another"></a>Copiar um blob único e o respetivos instantâneos de uma conta de armazenamento para outro

```azcopy
AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt /Snapshot
```

Após a operação de cópia, o contentor de destino inclui o blob e respetivos instantâneos. Pressupondo que os BLOBs no exemplo acima tem dois instantâneos, o contentor inclui os BLOBs e instantâneos que se seguem:

    abc.txt
    abc (2013-02-25 080757).txt
    abc (2014-02-21 150331).txt

### <a name="copy-all-blobs-in-a-container-to-another-storage-account"></a>Copiar todos os blobs num contentor para outra conta de armazenamento 

```azcopy
AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 
/Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /S
```

Especificar a opção /S carrega o conteúdo do recursivamente contentor especificado. Consulte [carregar todos os blobs numa pasta](#upload-all-blobs-in-a-folder) para obter mais informações e um exemplo.

### <a name="synchronously-copy-blobs-from-one-storage-account-to-another"></a>Copiar em sincronia blobs a partir de uma conta de armazenamento para outro

AzCopy por predefinição copia dados entre dois pontos finais de armazenamento de forma assíncrona. Por conseguinte, a operação de cópia é executado no fundo utilizando a capacidade de reserva de largura de banda que não tenha nenhum SLA em termos de como rápido a copiar um blob e AzCopy verifica periodicamente o estado da cópia até que a cópia for concluída ou falhada.

O `/SyncCopy` opção garante que a operação de cópia obtém velocidade consistente. AzCopy efetua a cópia síncrona ao transferir os blobs para copiar da origem especificada para memória local e, em seguida, carregá-los para o destino de armazenamento de Blobs.

```azcopy
AzCopy /Source:https://myaccount1.blob.core.windows.net/myContainer/ /Dest:https://myaccount2.blob.core.windows.net/myContainer/ /SourceKey:key1 /DestKey:key2 /Pattern:ab /SyncCopy
```

`/SyncCopy`pode gerar o custo de saída adicionais em comparação com a cópia assíncrona, a abordagem recomendada é para utilizar esta opção na VM do Azure que se encontra na mesma região que a sua conta de armazenamento de origem para evitar o custo de saída.

## <a name="download-files-from-file-storage"></a>Transferir ficheiros do armazenamento de ficheiros

Vamos ver várias formas para transferir ficheiros utilizando o AzCopy.

### <a name="download-a-single-file"></a>Transferir um ficheiro único

```azcopy
AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/myfolder1/ /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt
```

Se a fonte especificada é uma partilha de ficheiros do Azure, em seguida, tem de especificar se o nome de ficheiro exata, (*por exemplo,* `abc.txt`) para transferir um ficheiro único ou especificar a opção `/S` para transferir todos os ficheiros na recursivamente a partilha. Tentativa de especificar um padrão de ficheiro e a opção `/S` resultados em conjunto num erro.

### <a name="download-all-files-in-a-directory"></a>Transferir todos os ficheiros num diretório

```azcopy
AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/ /Dest:C:\myfolder /SourceKey:key /S
```

Tenha em atenção que não são transferidas pastas vazias.

## <a name="upload-files-to-an-azure-file-share"></a>Carregar ficheiros para uma partilha de ficheiros do Azure

Vamos ver várias formas de carregar ficheiros através do AzCopy.

### <a name="upload-a-single-file"></a>Carregar um ficheiro único

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /Pattern:abc.txt
```

### <a name="upload-all-files-in-a-folder"></a>Carregar todos os ficheiros numa pasta

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /S
```

Tenha em atenção que as pastas vazias não são carregadas.

### <a name="upload-files-matching-a-specific-pattern"></a>Carregar ficheiros que correspondam um padrão específico

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /Pattern:ab* /S
```

## <a name="copy-files-in-file-storage"></a>Copiar os ficheiros no File storage

Vamos ver várias formas de copiar os ficheiros numa partilha de ficheiros do Azure utilizando o AzCopy.

### <a name="copy-from-one-file-share-to-another"></a>Copiar de partilha de ficheiros para outro

```azcopy
AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare1/ /Dest:https://myaccount2.file.core.windows.net/myfileshare2/ /SourceKey:key1 /DestKey:key2 /S
```
Quando copiar um ficheiro através de partilhas de ficheiros, um [cópia do lado do servidor](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) é efetuar a operação.

### <a name="copy-from-an-azure-file-share-to-blob-storage"></a>Copiar a partir de uma partilha de ficheiros do Azure para o Blob storage

```azcopy
AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare/ /Dest:https://myaccount2.blob.core.windows.net/mycontainer/ /SourceKey:key1 /DestKey:key2 /S
```
Quando copiar um ficheiro de partilha de ficheiros para o blob, um [cópia do lado do servidor](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) é efetuar a operação.

### <a name="copy-a-blob-from-blob-storage-to-an-azure-file-share"></a>Copiar um blob de armazenamento de BLOBs para uma partilha de ficheiros do Azure

```azcopy
AzCopy /Source:https://myaccount1.blob.core.windows.net/mycontainer/ /Dest:https://myaccount2.file.core.windows.net/myfileshare/ /SourceKey:key1 /DestKey:key2 /S
```
Quando copiar um ficheiro de um blob para uma partilha de ficheiros, um [cópia do lado do servidor](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) é efetuar a operação.

### <a name="synchronously-copy-files"></a>Em sincronia copiar ficheiros

Pode especificar o `/SyncCopy` opção para copiar dados do armazenamento de ficheiros para armazenamento de ficheiros, do armazenamento de ficheiros para o Blob Storage e do armazenamento de BLOBs para o armazenamento de ficheiro de forma síncrona, AzCopy fazê-lo ao transferir a origem de dados à memória local e a carregá-la para o destino. Custo de saída padrão aplica-se.

```azcopy
AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare1/ /Dest:https://myaccount2.file.core.windows.net/myfileshare2/ /SourceKey:key1 /DestKey:key2 /S /SyncCopy
```

Quando copiar a partir do armazenamento de ficheiros para o Blob storage, o tipo de blob predefinido é blob de bloco o utilizador pode especificar a opção `/BlobType:page` para alterar o tipo de blob de destino.

Tenha em atenção que `/SyncCopy` pode gerar os custos de saída adicionais em comparação comparados a cópia assíncrona. A abordagem recomendada é utilizar esta opção na VM do Azure que se encontra na mesma região que a sua conta de armazenamento de origem para evitar o custo de saída.

## <a name="export-data-from-table-storage"></a>Exportar dados do armazenamento de tabelas

Vamos observe exportar dados do Table storage do Azure utilizando o AzCopy.

### <a name="export-a-table"></a>Exportar uma tabela

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key
```

AzCopy escreve um ficheiro de manifesto para a pasta de destino especificado. O ficheiro de manifesto é utilizado no processo de importação para localizar os ficheiros de dados necessário e efetuar a validação de dados. O ficheiro de manifesto utiliza a seguinte convenção de nomenclatura por predefinição:

    <account name>_<table name>_<timestamp>.manifest

Utilizador também pode especificar a opção `/Manifest:<manifest file name>` para definir o nome de ficheiro de manifesto.

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /Manifest:abc.manifest
```

### <a name="split-an-export-from-table-storage-into-multiple-files"></a>Dividir uma exportação do armazenamento de tabelas em vários ficheiros

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/mytable/ /Dest:C:\myfolder /SourceKey:key /S /SplitSize:100
```

AzCopy utiliza um *índice volume* em nomes de ficheiros de dados dividido para distinguir vários ficheiros. O índice de volume é composto por duas partes: um *índice de intervalos de chave de partição* e um *divisão ficheiro índice*. Ambos os índices são baseado em zero.

O índice de intervalo de chaves de partição é 0 se o utilizador especificar a opção `/PKRS`.

Por exemplo, suponha que AzCopy gera dois ficheiros de dados depois do utilizador Especifica opção `/SplitSize`. Os nomes de ficheiro de dados resultante poderão ser:

    myaccount_mytable_20140903T051850.8128447Z_0_0_C3040FE8.json
    myaccount_mytable_20140903T051850.8128447Z_0_1_0AB9AC20.json

Tenha em atenção que o mínimo possível de valor para a opção `/SplitSize` é 32 MB. Se o destino especificado é Blob storage, o AzCopy divide o ficheiro de dados quando os respetivos tamanhos de atingirem a limitação de tamanho do blob (200GB), independentemente da opção se `/SplitSize` foi especificado pelo utilizador.

### <a name="export-a-table-to-json-or-csv-data-file-format"></a>Exportar uma tabela para o formato de ficheiro de dados JSON ou CSV

Por predefinição, o AzCopy exporta as tabelas para ficheiros de dados JSON. Pode especificar a opção `/PayloadFormat:JSON|CSV` para exportar as tabelas como JSON ou CSV.

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /PayloadFormat:CSV
```

Quando especificar o formato de payload CSV, o AzCopy também gera um ficheiro de esquema com a extensão de ficheiro `.schema.csv` para cada ficheiro de dados.

### <a name="export-table-entities-concurrently"></a>Exportar as entidades da tabela em simultâneo

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /PKRS:"aa#bb"
```

AzCopy inicia operações simultâneas para exportar entidades quando o utilizador Especifica opção `/PKRS`. Cada operação exporta um intervalo de chave de partição.

Tenha em atenção que o número de operações simultâneas também é controlado pela opção `/NC`. AzCopy utiliza o número de processadores de núcleo como o valor predefinido de `/NC` quando copiar as entidades da tabela, mesmo se `/NC` não foi especificado. Quando o utilizador Especifica opção `/PKRS`, AzCopy utiliza o menor dos dois valores - intervalos de chaves de partição versus implícita ou explicitamente especificado operações simultâneas - para determinar o número de operações simultâneas para iniciar. Para obter mais detalhes, escreva `AzCopy /?:NC` na linha de comandos.

### <a name="export-a-table-to-blob-storage"></a>Exportar uma tabela para o Blob storage

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:https://myaccount.blob.core.windows.net/mycontainer/ /SourceKey:key1 /Destkey:key2
```

AzCopy gera um ficheiro de dados JSON para o contentor de Blobs com a seguinte convenção de nomenclatura:

    <account name>_<table name>_<timestamp>_<volume index>_<CRC>.json

O ficheiro de dados JSON gerado segue o formato de payload para metadados mínimo. Para obter detalhes sobre este formato de payload, consulte [formato de Payload para operações de serviço tabela](http://msdn.microsoft.com/library/azure/dn535600.aspx).

Tenha em atenção que, ao exportar as tabelas para blobs, AzCopy transfere as entidades de tabela para ficheiros de dados temporária local e, em seguida, carrega as entidades para o blob. Estes ficheiros de dados temporária são colocados na pasta de ficheiros do diário de alterações com o caminho predefinido "<code>%LocalAppData%\Microsoft\Azure\AzCopy</code>", pode especificar a opção/z [pasta de ficheiros de diário] para o diário de alterações a localização da pasta de ficheiros e, por conseguinte, alterar a localização de ficheiros de dados temporária. Tamanho dos ficheiros de dados temporária é decidido pelo tamanho as entidades da tabela e o tamanho especificado com a opção /SplitSize, apesar do ficheiro de dados temporária no disco local é eliminado de forma instantânea, depois de terem sido carregado para o blob, certifique-se de que tem espaço suficiente em disco local para armazenar estes ficheiros de dados temporária antes de estes sejam eliminados.

## <a name="import-data-into-table-storage"></a>Importar dados para o Table storage

Vamos observe a importar dados para o Table storage do Azure utilizando o AzCopy.

### <a name="import-a-table"></a>Importe uma tabela

```azcopy
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.table.core.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace
```

A opção `/EntityOperation` indica como inserir entidades na tabela. Os valores possíveis são:

* `InsertOrSkip`: Ignora a uma entidade existente ou introduza uma nova entidade se não existir na tabela.
* `InsertOrMerge`: Intercala uma entidade existente ou introduza uma nova entidade se não existir na tabela.
* `InsertOrReplace`: Substitui uma entidade existente ou introduza uma nova entidade se não existir na tabela.

Tenha em atenção que não é possível especificar a opção `/PKRS` no cenário de importação. Ao contrário do cenário de exportação, na qual tem de especificar a opção `/PKRS` para iniciar as operações em simultâneo, o AzCopy inicia o operações em simultâneo por predefinição quando importar uma tabela. O número predefinido de operações simultâneas iniciado é igual ao número de processadores de núcleo No entanto, pode especificar um número diferente de em simultâneo com a opção `/NC`. Para obter mais detalhes, escreva `AzCopy /?:NC` na linha de comandos.

Tenha em atenção que AzCopy só suporta a importação para JSON, CSV não. AzCopy não suporta importações de tabela JSON criados pelo utilizador e os ficheiros de manifesto. Ambos estes ficheiros têm de ser provenientes uma exportação de tabela do AzCopy. Para evitar erros,. Modifique o JSON exportado ou manifesto do ficheiro.

### <a name="import-entities-into-a-table-from-blob-storage"></a>Importar entidades para uma tabela a partir do Blob storage

Assuma um contentor do Blob contém o seguinte: ficheiro de um JSON que representam uma tabela do Azure e o respetivo ficheiro de manifesto associado.

    myaccount_mytable_20140103T112020.manifest
    myaccount_mytable_20140103T112020_0_0_0AF395F1DC42E952.json

Pode executar o seguinte comando para importar as entidades numa tabela com o ficheiro de manifesto nesse contentor de blob:

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:https://myaccount.table.core.windows.net/mytable /SourceKey:key1 /DestKey:key2 /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:"InsertOrReplace"
```

## <a name="other-azcopy-features"></a>Outras funcionalidades do AzCopy

Vamos ver alguns outras funcionalidades do AzCopy.

### <a name="only-copy-data-that-doesnt-exist-in-the-destination"></a>Apenas os dados de cópia que não existem no destino

O `/XO` e `/XN` parâmetros permitem-lhe excluir recursos de origem de anterior ou mais recente do que está a ser copiado, respetivamente. Se pretender apenas copiar recursos de origem que não existem no destino, pode especificar ambos os parâmetros de comando do AzCopy:

    /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /XO /XN

    /Source:C:\myfolder /Dest:http://myaccount.file.core.windows.net/myfileshare /DestKey:<destkey> /S /XO /XN

    /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:http://myaccount.blob.core.windows.net/mycontainer1 /SourceKey:<sourcekey> /DestKey:<destkey> /S /XO /XN

Tenha em atenção que isto não é suportado quando a origem ou de destino é uma tabela.

### <a name="use-a-response-file-to-specify-command-line-parameters"></a>Utilize um ficheiro de resposta para especificar parâmetros da linha de comandos

```azcopy
AzCopy /@:"C:\responsefiles\copyoperation.txt"
```

Pode incluir quaisquer parâmetros de linha de comandos do AzCopy num ficheiro de resposta. AzCopy processa os parâmetros no ficheiro como se tivesse sido especificados na linha de comandos, efetuar uma substituição direta com o conteúdo do ficheiro.

Partem do princípio de um ficheiro de resposta com o nome `copyoperation.txt`, que contém as linhas seguintes. Cada parâmetro do AzCopy pode ser especificado numa única linha

    /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /Y

ou, no separar linhas:

    /Source:http://myaccount.blob.core.windows.net/mycontainer
    /Dest:C:\myfolder
    /SourceKey:<sourcekey>
    /S
    /Y

AzCopy falha se dividir o parâmetro por duas linhas, conforme mostrado aqui para o `/sourcekey` parâmetro:

    http://myaccount.blob.core.windows.net/mycontainer
     C:\myfolder
    /sourcekey:
    <sourcekey>
    /S
    /Y

### <a name="use-multiple-response-files-to-specify-command-line-parameters"></a>Utilizar vários ficheiros de resposta para especificar parâmetros da linha de comandos

Partem do princípio de um ficheiro de resposta com o nome `source.txt` que especifica um contentor de origem:

    /Source:http://myaccount.blob.core.windows.net/mycontainer

E um ficheiro de resposta com o nome `dest.txt` que especifica uma pasta de destino no sistema de ficheiros:

    /Dest:C:\myfolder

E um ficheiro de resposta com o nome `options.txt` que especifica as opções para AzCopy:

    /S /Y

Para chamar o AzCopy com estes ficheiros de resposta, todos os que residem num diretório `C:\responsefiles`, utilize este comando:

```azcopy
AzCopy /@:"C:\responsefiles\source.txt" /@:"C:\responsefiles\dest.txt" /SourceKey:<sourcekey> /@:"C:\responsefiles\options.txt"   
```

AzCopy processa este comando, tal como se de que incluiu todos os parâmetros individuais na linha de comandos:

```azcopy
AzCopy /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /Y
```

### <a name="specify-a-shared-access-signature-sas"></a>Especifique uma assinatura de acesso partilhado (SAS)

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1 /Dest:https://myaccount.blob.core.windows.net/mycontainer2 /SourceSAS:SAS1 /DestSAS:SAS2 /Pattern:abc.txt
```

Também pode especificar uma SAS no contentor do URI:

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1/?SourceSASToken /Dest:C:\myfolder /S
```

### <a name="journal-file-folder"></a>Pasta de ficheiros do diário de alterações

Sempre que emitir um comando do AzCopy, este verifica se existe um ficheiro de diário de alterações na pasta predefinida ou se existe uma pasta que especificou através desta opção. Se o ficheiro do diário de alterações não existe em qualquer local, o AzCopy processa a operação como novo e gera um novo ficheiro de diário de alterações.

Se o ficheiro do diário de alterações existe, o AzCopy verifica se a linha de comandos de entrada corresponde a linha de comandos no ficheiro de diário de alterações. Se as duas linhas de comando corresponderem, o AzCopy retoma a operação incompleta. Se não corresponderem, lhe for pedido para optar por substituir o ficheiro de diário de alterações para iniciar uma operação de novo, ou para cancelar a operação atual.

Se pretender utilizar a localização predefinida para o ficheiro do diário de alterações:

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Z
```

Se omitir opção `/Z`, ou especificar a opção `/Z` sem o caminho da pasta, conforme mostrado acima, o AzCopy cria o ficheiro do diário de alterações na localização predefinida, que é `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`. Se o ficheiro do diário de alterações já existir, o AzCopy retoma a operação com base no ficheiro diário de alterações.

Se pretender especificar uma localização para o ficheiro de diário personalizada:

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Z:C:\journalfolder\
```

Este exemplo cria o ficheiro do diário de alterações se já existir. Se existir, o AzCopy retoma a operação com base no ficheiro diário de alterações.

Se pretender retomar uma operação do AzCopy:

```azcopy
AzCopy /Z:C:\journalfolder\
```

Neste exemplo retoma a última operação que pode ter falhado para concluir.

### <a name="generate-a-log-file"></a>Gerar um ficheiro de registo

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /V
```

Se especificar a opção `/V` sem fornecer um caminho de ficheiro para o registo verboso, em seguida, AzCopy cria o ficheiro de registo na localização predefinida, que é `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`.

Caso contrário, pode criar um ficheiro de registo numa localização personalizada:

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /V:C:\myfolder\azcopy1.log
```

Tenha em atenção que se especificar um caminho relativo a seguinte opção `/V`, tais como `/V:test/azcopy1.log`, em seguida, o registo verboso é criado no diretório de trabalho atual dentro de uma subpasta denominada `test`.

### <a name="specify-the-number-of-concurrent-operations-to-start"></a>Especifique o número de operações simultâneas para iniciar

Opção `/NC` Especifica o número de operações de cópia em simultâneo. Por predefinição, o AzCopy é iniciado um determinado número de operações simultâneas para aumentar o débito de transferência de dados. Para operações de tabela, é igual ao número de processadores que tem o número de operações simultâneas. Para operações de Blob e o ficheiro, o número de operações simultâneas é igual a 8 vezes o número de processadores que tem. Se estiver a executar o AzCopy através de uma rede de largura de banda reduzida, pode especificar um número inferior de /NC evitar falhas causadas por concorrência de recursos.

### <a name="run-azcopy-against-the-azure-storage-emulator"></a>Execute o AzCopy contra o emulador do Storage do Azure

Pode executar AzCopy contra o [emulador do Storage do Azure](storage-use-emulator.md) para blobs:

```azcopy
AzCopy /Source:https://127.0.0.1:10000/myaccount/mycontainer/ /Dest:C:\myfolder /SourceKey:key /SourceType:Blob /S
```

Também pode executá-lo para tabelas:

```azcopy
AzCopy /Source:https://127.0.0.1:10002/myaccount/mytable/ /Dest:C:\myfolder /SourceKey:key /SourceType:Table
```

## <a name="azcopy-parameters"></a>Parâmetros do AzCopy

Parâmetros para AzCopy são descritos abaixo. Também pode escrever um dos seguintes comandos na linha de comandos para obter ajuda na utilização AzCopy:

* Para obter ajuda detalhada da linha de comandos do AzCopy:`AzCopy /?`
* Para obter ajuda detalhada com quaisquer parâmetros de AzCopy:`AzCopy /?:SourceKey`
* Para obter exemplos da linha de comandos:`AzCopy /?:Samples`

### <a name="sourcesource"></a>/ De origem: "origem"

Especifica os dados de origem a partir dos quais pretende copiar. A origem pode ser um diretório do sistema de ficheiros, um contentor de blob, um diretório virtual de blob, uma partilha de ficheiros de armazenamento, um diretório do ficheiro de armazenamento ou uma tabela do Azure.

**Aplica-se a:** Blobs, ficheiros, tabelas

### <a name="destdestination"></a>/ Dest: "destino"

Especifica o destino para copiar para. O destino pode ser um diretório do sistema de ficheiros, um contentor de blob, um diretório virtual de blob, uma partilha de ficheiros de armazenamento, um diretório do ficheiro de armazenamento ou uma tabela do Azure.

**Aplica-se a:** Blobs, ficheiros, tabelas

### <a name="patternfile-pattern"></a>/ Padrão: "-padrão de ficheiros"

Especifica um padrão de ficheiro que indica que os ficheiros para copiar. O comportamento do parâmetro /Pattern é determinado pela localização dos dados de origem e a presença da opção de modo recursivo. Modo recursivo é especificado através da opção /S.

Se a fonte especificada é um diretório no sistema de ficheiros, em seguida, carateres universais padrão estão em vigor e o padrão de ficheiro fornecido é comparado com ficheiros dentro do diretório. Se a opção que /s for especificado, em seguida, o AzCopy também corresponde ao padrão especificado em relação a todos os ficheiros em subpastas sob o diretório.

Se a origem especificada é um contentor de blob ou um diretório virtual, caracteres universais não são aplicadas. Se a opção que /s for especificado, em seguida, AzCopy interpreta o padrão de ficheiro especificado como um prefixo de blob. Se a opção que /s não for especificado, o AzCopy, em seguida, corresponda ao padrão ficheiros contra dos nomes blob exato.

Se a fonte especificada é uma partilha de ficheiros do Azure, em seguida, terá de especificar o nome de ficheiro exata, (por exemplo, abc.txt) para copiar um ficheiro único, ou especificar a opção /S para copiar todos os ficheiros no recursivamente a partilha. A tentar especificar ambos os um ficheiro padrão e opção /S em conjunto os resultados num erro.

AzCopy utiliza uma correspondência sensível quando o /Source é um contentor de blob ou um diretório virtual de blob e utiliza uma correspondência sensível em todos os outros casos.

O padrão de ficheiro predefinido utilizado quando não é especificada nenhuma padrão de ficheiro é *.* para uma localização de ficheiros de sistema ou um prefixo vazio para uma localização de armazenamento do Azure. Especificar vários padrões de ficheiro não é suportada.

**Aplica-se a:** Blobs, ficheiros

### <a name="destkeystorage-key"></a>/ DestKey: "chave de armazenamento"

Especifica a chave de conta de armazenamento para o recurso de destino.

**Aplica-se a:** Blobs, ficheiros, tabelas

### <a name="destsassas-token"></a>/ DestSAS: "sas token"

Especifica uma assinatura de acesso partilhado (SAS) com permissões de leitura e escrita para o destino (se aplicável). Coloque o SAS com aspas, como pode contém carateres especiais de linha de comandos.

Se o recurso de destino é um contentor de BLOBs, a partilha de ficheiros ou a tabela, pode especificar esta opção seguida o token SAS, ou pode especificar a SAS como parte do contentor do blob de destino, a partilha de ficheiros ou URI da tabela, sem esta opção.

Se a origem e destino forem ambos os blobs, em seguida, o blob de destino têm de residir na mesma conta de armazenamento como o blob de origem.

**Aplica-se a:** Blobs, ficheiros, tabelas

### <a name="sourcekeystorage-key"></a>/ SourceKey: "chave de armazenamento"

Especifica a chave de conta de armazenamento para o recurso de origem.

**Aplica-se a:** Blobs, ficheiros, tabelas

### <a name="sourcesassas-token"></a>/ SourceSAS: "sas token"

Especifica uma assinatura de acesso partilhado com permissões de leitura e de lista para a origem (se aplicável). Coloque o SAS com aspas, como pode contém carateres especiais de linha de comandos.

Se o recurso de origem é um contentor de blob e não uma chave nem uma SAS é fornecida, o contentor do blob é de leitura através do acesso anónimo.

Se a origem é uma partilha de ficheiros ou tabela, tem de ser fornecida uma chave ou uma SAS.

**Aplica-se a:** Blobs, ficheiros, tabelas

### <a name="s"></a>/S

Especifica o modo recursivo para operações de cópia. No modo de recursiva, o AzCopy copia todos os blobs ou ficheiros que correspondam ao padrão de ficheiro especificado, incluindo as existentes na subpastas.

**Aplica-se a:** Blobs, ficheiros

### <a name="blobtypeblock--page--append"></a>/ BlobType: "bloquear" | "página" | "acrescentar"

Especifica se o blob de destino é um blob de blocos, um blob de página ou um blob de acréscimo. Esta opção é aplicável apenas quando estiver a carregar um blob. Caso contrário, é gerado um erro. Se o destino for um blob e esta opção não for especificada, por predefinição, o AzCopy cria um blob de blocos.

**Aplica-se a:** Blobs

### <a name="checkmd5"></a>/ CheckMD5

Calcula um hash MD5 para dados transferidos e verifica se o hash MD5 armazenados no blob ou propriedade de conteúdo-MD5 do ficheiro corresponda ao hash calculado. A verificação de MD5 está desativada por predefinição, pelo que tem de especificar esta opção para efetuar a verificação de MD5 quando a transferência de dados.

Tenha em atenção que o armazenamento do Azure não garante que o hash MD5 armazenado para o blob ou o ficheiro está atualizado. É responsabilidade do cliente para atualizar o MD5 sempre que o blob ou o ficheiro é modificado.

AzCopy sempre define a propriedade Content-MD5 para um blob do Azure ou o ficheiro após carregá-lo para o serviço.  

**Aplica-se a:** Blobs, ficheiros

### <a name="snapshot"></a>/ Instantâneo de

Indica se a transferência de instantâneos. Esta opção só é válida quando a origem é um blob.

Os instantâneos do blob transferidos são mudados neste formato: nome do blob (hora de instantâneo) .extension

Por predefinição, os instantâneos não são copiados.

**Aplica-se a:** Blobs

### <a name="vverbose-log-file"></a>/ V: [verboso--ficheiro de registo]

Saídas verboso as mensagens de estado para um ficheiro de registo.

Por predefinição, o ficheiro de registo verboso é denominado AzCopyVerbose.log no `%LocalAppData%\Microsoft\Azure\AzCopy`. Se especificar uma localização de ficheiros existentes para esta opção, o registo verboso é acrescentado a esse ficheiro.  

**Aplica-se a:** Blobs, ficheiros, tabelas

### <a name="zjournal-file-folder"></a>/ Z [pasta de ficheiros de diário]

Especifica uma pasta de ficheiros do diário de alterações para retomar uma operação.

AzCopy suporta sempre retomar se uma operação foi interrompida.

Se esta opção não for especificada, ou se for especificado sem um caminho de pasta, o AzCopy cria o ficheiro do diário de alterações na localização predefinida, que é % LocalAppData%\Microsoft\Azure\AzCopy.

Sempre que emitir um comando do AzCopy, este verifica se existe um ficheiro de diário de alterações na pasta predefinida ou se existe uma pasta que especificou através desta opção. Se o ficheiro do diário de alterações não existe em qualquer local, o AzCopy processa a operação como novo e gera um novo ficheiro de diário de alterações.

Se o ficheiro do diário de alterações existe, o AzCopy verifica se a linha de comandos de entrada corresponde a linha de comandos no ficheiro de diário de alterações. Se as duas linhas de comando corresponderem, o AzCopy retoma a operação incompleta. Se não corresponderem, lhe for pedido para optar por substituir o ficheiro de diário de alterações para iniciar uma operação de novo, ou para cancelar a operação atual.

O ficheiro do diário de alterações é eliminado após a conclusão com êxito da operação.

Tenha em atenção que a retomar a uma operação a partir de um ficheiro de diário criada por uma versão anterior do AzCopy não é suportada.

**Aplica-se a:** Blobs, ficheiros, tabelas

### <a name="parameter-file"></a>/@:"Parameter-File"

Especifica um ficheiro que contém os parâmetros. AzCopy processa os parâmetros no ficheiro, como se tivesse sido especificados na linha de comandos.

No ficheiro de resposta, pode especificar vários parâmetros numa única linha, ou especifique cada parâmetro na sua própria linha. Tenha em atenção que um parâmetro individuais não pode abranger várias linhas.

Ficheiros de resposta podem incluir as linhas de comentários que começam com o símbolo de #.

Pode especificar vários ficheiros de resposta. No entanto, tenha em atenção que não suporta o AzCopy aninhadas ficheiros de resposta.

**Aplica-se a:** Blobs, ficheiros, tabelas

### <a name="y"></a>/Y

Suprime todos os pedidos de confirmação do AzCopy.

**Aplica-se a:** Blobs, ficheiros, tabelas

### <a name="l"></a>/L

Especifica uma operação de listagem apenas; Não existem dados são copiados.

AzCopy interpreta através desta opção como uma simulação para executar a linha de comandos sem esta opção /L e contagens objetos quantos são copiados, pode especificar a opção /V ao mesmo tempo para verificar quais os objetos são copiados no registo verboso.

O comportamento desta opção também é determinado pela localização dos dados de origem e a presença da recursiva modo opção /S e de ficheiros padrão opção /Pattern.

AzCopy necessita da permissão de LISTAGEM e de leitura desta localização de origem ao utilizar esta opção.

**Aplica-se a:** Blobs, ficheiros

### <a name="mt"></a>/MT

Define last-modified tempo o ficheiro transferido o mesmo que o blob de origem ou do ficheiro.

**Aplica-se a:** Blobs, ficheiros

### <a name="xn"></a>/XN

Exclui um recurso de origem mais recente. O recurso não é copiado se a hora da última modificação da origem de é igual ou mais recente do que o destino.

**Aplica-se a:** Blobs, ficheiros

### <a name="xo"></a>/XO
Exclui um recurso de origem de anterior. O recurso não é copiado se a hora da última modificação da origem de é igual ou anterior ao destino.

**Aplica-se a:** Blobs, ficheiros

### <a name="a"></a>/A

Carrega apenas os ficheiros que tenham o atributo de arquivo definida.

**Aplica-se a:** Blobs, ficheiros

### <a name="iarashcnetoi"></a>/ IA: [RASHCNETOI]

Carrega apenas os ficheiros que tem um conjunto de atributos especificado.

Os atributos disponíveis incluem:

* R = ficheiros só de leitura
* A = ficheiros prontos para arquivar
* S = ficheiros de sistema
* H = Hidden ficheiros
* C = Compressed ficheiros
* N = ficheiros Normal
* I = ficheiros encriptados
* T = ficheiros temporários
* O = ficheiros Offline
* Posso = indexado não ficheiros

**Aplica-se a:** Blobs, ficheiros

### <a name="xarashcnetoi"></a>/ XA: [RASHCNETOI]

Exclui ficheiros que tem um conjunto de atributos especificado.

Os atributos disponíveis incluem:

* R = ficheiros só de leitura
* A = ficheiros prontos para arquivar
* S = ficheiros de sistema
* H = Hidden ficheiros
* C = Compressed ficheiros
* N = ficheiros Normal
* I = ficheiros encriptados
* T = ficheiros temporários
* O = ficheiros Offline
* Posso = indexado não ficheiros

**Aplica-se a:** Blobs, ficheiros

### <a name="delimiterdelimiter"></a>/ Delimitador: "delimiter"

Indica o carácter de delimitador utilizado para delimitar os diretórios virtuais num nome de blob.

Por predefinição, utiliza AzCopy / como caráter delimitador. No entanto, o AzCopy suporta a utilização de qualquer caráter comuns (por exemplo, @, # ou %) como um delimitador. Se precisar de incluir um dos seguintes carateres especiais na linha de comandos, coloque o nome de ficheiro com aspas duplas.

Esta opção só é aplicável para transferir blobs.

**Aplica-se a:** Blobs

### <a name="ncnumber-of-concurrent-operations"></a>/ NC: "número-de-em simultâneo-operações de"

Especifica o número de operações simultâneas.

AzCopy por predefinição é iniciado um determinado número de operações simultâneas para aumentar o débito de transferência de dados. Tenha em atenção que o elevado número de operações simultâneas num ambiente de largura de banda baixa pode sobrecarregar a ligação de rede e impedir que as operações de conclusão totalmente. Limitar operações simultâneas com base na largura de banda disponível real.

O limite superior para operações simultâneas é 512.

**Aplica-se a:** Blobs, ficheiros, tabelas

### <a name="sourcetypeblob--table"></a>/ SourceType: "Blob" | "Tabela"

Especifica que o `source` recursos é um blob disponível no ambiente de desenvolvimento local, em execução no emulador do storage.

**Aplica-se a:** Blobs, tabelas

### <a name="desttypeblob--table"></a>/ DestType: "Blob" | "Tabela"

Especifica que o `destination` recursos é um blob disponível no ambiente de desenvolvimento local, em execução no emulador do storage.

**Aplica-se a:** Blobs, tabelas

### <a name="pkrskey1key2key3"></a>/ PKRS: "key&#1;key&#2;key&#3;..."

Divide o intervalo de chave de partição para ativar a exportação de dados de tabela em paralelo, o que aumenta a velocidade da operação de exportação.

Se esta opção não for especificada, o AzCopy utiliza um único thread para exportar as entidades da tabela. Por exemplo, se o utilizador Especifica /PKRS: "aa #bb", em seguida, AzCopy inicia três operações simultâneas.

Cada operação exporta um dos três intervalos de chaves de partição, conforme mostrado abaixo:

  [chave de partição primeiro, aa)

  [aa, bb)

  [bb, chave de partição último]

**Aplica-se a:** tabelas

### <a name="splitsizefile-size"></a>/ SplitSize: "tamanho de ficheiro"

Especifica o ficheiro exportado dividir tamanho em MB, o valor mínimo permitido é de 32.

Se esta opção não for especificada, o AzCopy exporta os dados da tabela para um único ficheiro.

Se os dados de tabela são exportados para um blob e o tamanho do ficheiro exportado atinge o limite de 200 GB de tamanho do blob, AzCopy divide o ficheiro exportado, mesmo se esta opção não está especificada.

**Aplica-se a:** tabelas

### <a name="entityoperationinsertorskip--insertormerge--insertorreplace"></a>/ EntityOperation: "InsertOrSkip" | "InsertOrMerge" | "InsertOrReplace"

Especifica o comportamento de importação de dados de tabela.

* InsertOrSkip - ignora a uma entidade existente ou introduza uma nova entidade se não existir na tabela.
* InsertOrMerge - intercala uma entidade existente ou introduza uma nova entidade se não existir na tabela.
* InsertOrReplace - substitui uma entidade existente ou introduza uma nova entidade se não existir na tabela.

**Aplica-se a:** tabelas

### <a name="manifestmanifest-file"></a>/ Manifesto: "ficheiro de manifesto"

Especifica o ficheiro de manifesto para a tabela Exportar e importar a operação.

Esta opção é opcional durante a operação de exportação, o AzCopy gera um ficheiro de manifesto com o nome predefinido se esta opção não for especificada.

Esta opção é necessária durante a operação de importação para localizar os ficheiros de dados.

**Aplica-se a:** tabelas

### <a name="synccopy"></a>/ SyncCopy

Indica se deve copiar em sincronia blobs ou ficheiros entre dois pontos finais de armazenamento do Azure.

AzCopy por predefinição utiliza cópia assíncrona do lado do servidor. Especifique esta opção para efetuar uma cópia síncrona, que transfere ficheiros ou de blobs para memória local e, em seguida, carrega-os ao Storage do Azure.

Pode utilizar esta opção quando copiar os ficheiros dentro do armazenamento de BLOBs no armazenamento de ficheiros ou armazenamento de BLOBs para o armazenamento de ficheiro ou vice-versa.

**Aplica-se a:** Blobs, ficheiros

### <a name="setcontenttypecontent-type"></a>/ SetContentType: "content-type"

Especifica o tipo de conteúdo de MIME para blobs de destino ou ficheiros.

AzCopy define o tipo de conteúdo para um blob ou um ficheiro para application/octet-stream por predefinição. Pode definir o tipo de conteúdo para todos os blobs ou ficheiros especificando explicitamente um valor para esta opção.

Se especificar esta opção sem um valor, em seguida, AzCopy define cada blob ou tipo de conteúdo do ficheiro, de acordo com a extensão de ficheiro.

**Aplica-se a:** Blobs, ficheiros

### <a name="payloadformatjson--csv"></a>/ PayloadFormat: "JSON" | "CSV"

Especifica o formato do ficheiro de dados exportados de tabela.

Se esta opção não for especificada, por predefinição, AzCopy exporta ficheiro de dados de tabela no formato JSON.

**Aplica-se a:** tabelas

## <a name="known-issues-and-best-practices"></a>Problemas conhecidos e melhores práticas

Vamos observe alguns dos problemas conhecidos e melhores práticas.

### <a name="limit-concurrent-writes-while-copying-data"></a>Limitar escritas em simultâneo ao copiar dados

Quando copia blobs ou ficheiros com o AzCopy, tenha em atenção que outra aplicação pode ser modificar os dados enquanto estiver a copiar. Se for possível, certifique-se de que os dados que está a copiar não está a ser modificados durante a operação de cópia. Por exemplo, quando copiar um VHD associado uma máquina virtual do Azure, certifique-se de que não existem outras aplicações atualmente estiver a escrever para o VHD. É uma boa forma de fazê-lo por leasing o recurso a ser copiados. Em alternativa, pode criar um instantâneo do VHD primeiro e, em seguida, copie o instantâneo.

Se não podem impedir outras aplicações de escrever para blobs ou ficheiros enquanto estão a ser copiados, em seguida, tenha em atenção que o tempo de que conclusão da tarefa, os recursos copiados podem já não ter paridade completa com os recursos de origem.

### <a name="run-one-azcopy-instance-on-one-machine"></a>Execute uma instância do AzCopy num computador.

AzCopy foi concebido para maximizar a utilização do seu recurso de máquina para acelerar a transferência de dados, recomendamos que execute apenas uma instância do AzCopy num computador e especifique a opção `/NC` se precisar de mais simultâneas operações. Para obter mais detalhes, escreva `AzCopy /?:NC` na linha de comandos.

### <a name="enable-fips-compliant-md5-algorithms-for-azcopy-when-you-use-fips-compliant-algorithms-for-encryption-hashing-and-signing"></a>Ativar algoritmos MD5 compatíveis com FIPS para AzCopy quando a "algoritmos compatíveis com o utilize FIPS para encriptação, hashing e iniciar sessão."

AzCopy por predefinição utiliza a implementação de .NET MD5 para calcular o MD5 quando copiar objetos, mas existem alguns requisitos de segurança que têm o AzCopy para ativar a definição de MD5 compatíveis com FIPS.

Pode criar um ficheiro App. config `AzCopy.exe.config` com a propriedade `AzureStorageUseV1MD5` e colocá-la reserve com AzCopy.exe.

    <?xml version="1.0" encoding="utf-8" ?>
    <configuration>
      <appSettings>
        <add key="AzureStorageUseV1MD5" value="false"/>
      </appSettings>
    </configuration>

Para a propriedade "AzureStorageUseV1MD5":

* VERDADEIRO - o valor predefinido, o AzCopy utiliza implementação .NET MD5.
* FALSO-AzCopy utiliza algoritmos MD5 compatíveis com FIPS.

Algoritmos compatíveis com FIPS estão desativados por predefinição no Windows. Pode alterar esta definição de política no seu computador. Na janela executar (Windows + R), escreva secpol.msc para abrir o **política de segurança Local** janela. No **definições de segurança** janela, navegue para **definições de segurança** > **políticas locais** > **opções de segurança**. Localize o **criptografia de sistema: algoritmos compatíveis com o utilize FIPS para encriptação, hashing e iniciar sessão** política. Faça duplo clique na política para ver o valor apresentado no **definição de segurança** coluna.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o Storage do Azure e AzCopy, consulte os seguintes recursos:

### <a name="azure-storage-documentation"></a>Documentação do Storage do Azure:
* [Introdução ao Storage do Azure](../storage-introduction.md)
* [Como utilizar o Blob storage a partir do .NET](../blobs/storage-dotnet-how-to-use-blobs.md)
* [Como utilizar o File storage a partir do .NET](../storage-dotnet-how-to-use-files.md)
* [Como utilizar o Table storage do .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Como criar, gerir ou eliminar uma conta de armazenamento](../storage-create-storage-account.md)
* [Transferência de dados com o AzCopy no Linux](storage-use-azcopy-linux.md)

### <a name="azure-storage-blog-posts"></a>Mensagens de blogue de armazenamento do Azure:
* [Introdução ao pré-visualização de biblioteca de movimento de dados de armazenamento do Azure](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/)
* [AzCopy: Introdução ao copiar síncrona e tipo de conteúdo personalizado](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/01/13/azcopy-introducing-synchronous-copy-and-customized-content-type.aspx)
* [AzCopy: Anunciar disponibilidade geral do 3.0 AzCopy plus versão de pré-visualização do AzCopy 4.0 com suporte de tabela e ficheiro](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/10/29/azcopy-announcing-general-availability-of-azcopy-3-0-plus-preview-release-of-azcopy-4-0-with-table-and-file-support.aspx)
* [AzCopy: Otimizado para cenários de cópia em grande escala](http://go.microsoft.com/fwlink/?LinkId=507682)
* [AzCopy: Suporte para o armazenamento georredundante com acesso de leitura](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/04/07/azcopy-support-for-read-access-geo-redundant-account.aspx)
* [AzCopy: Transferir dados com o SAS token e o modo reiniciável](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/09/07/azcopy-transfer-data-with-re-startable-mode-and-sas-token.aspx)
* [AzCopy: Utilizando o Blob de cópia de conta em vários locais](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/04/01/azcopy-using-cross-account-copy-blob.aspx)
* [AzCopy: Carregar/transferência de ficheiros para Blobs do Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/12/03/azcopy-uploading-downloading-files-for-windows-azure-blobs.aspx)