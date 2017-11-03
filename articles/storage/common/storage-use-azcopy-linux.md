---
title: Copiar ou mover dados para o Storage do Azure com o AzCopy no Linux | Microsoft Docs
description: "Utilize o AzCopy no utilitário do Linux para mover ou copiar dados de ou para conteúdo de blob e o ficheiro. Copiar dados para o armazenamento do Azure de ficheiros locais ou copie os dados dentro ou entre contas de armazenamento. Migre facilmente os dados para armazenamento do Azure."
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
ms.date: 28/9/2017
ms.author: seguler
ms.openlocfilehash: d77ac39b7fcf6a23ebc58a2bbf9dc7de664edbb3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="transfer-data-with-azcopy-on-linux"></a>Transferência de dados com o AzCopy no Linux
O AzCopy no Linux é um utilitário da linha de comandos concebido para copiar dados para e do armazenamento de Blobs do Microsoft Azure e o ficheiro de utilização de comandos simples com um desempenho ideal. Pode copiar dados de um objeto para outro dentro da sua conta de armazenamento, ou entre contas de armazenamento.

Existem duas versões do AzCopy que pode transferir. AzCopy no Linux é criado com o .NET Core Framework, que está direcionada para plataformas Linux oferta estilo POSIX opções da linha de comandos. [AzCopy no Windows](../storage-use-azcopy.md) baseia-se com o .NET Framework e oferece opções de linha de comandos de estilo do Windows. Este artigo abrange AzCopy no Linux.

## <a name="download-and-install-azcopy"></a>Transfira e instale o AzCopy
### <a name="installation-on-linux"></a>Instalação no Linux

O artigo inclui comandos para várias versões do Ubuntu.  Utilize o `lsb_release -a` comando para confirmar a sua versão de distribuição e o nome. 

AzCopy no Linux requer o .NET Core framework (versão 1.1.x) na plataforma. Consulte as instruções de instalação no [.NET Core](https://www.microsoft.com/net/download/linux) página.

Por exemplo, vamos a instalar o .NET Core no Ubuntu 16.04. Para o guia de instalação mais recente, visite [.NET Core no Linux](https://www.microsoft.com/net/download/linux) página de instalação.


```bash
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-xenial-prod xenial main" > /etc/apt/sources.list.d/dotnetdev.list'
sudo apt-get update
sudo apt-get install dotnet-dev-1.1.4
```

Depois de ter instalado o .NET Core, transfira e instale o AzCopy.

```bash
wget -O azcopy.tar.gz https://aka.ms/downloadazcopyprlinux
tar -xf azcopy.tar.gz
sudo ./install.sh
```

Pode remover os ficheiros extraídos assim que estiver instalado AzCopy no Linux. Em alternativa se não tiver privilégios de Superutilizador, também pode executar AzCopy utilizando o script de shell 'azcopy' na pasta extraída. 


## <a name="writing-your-first-azcopy-command"></a>Escrever o seu primeiro comando do AzCopy
A sintaxe básica para comandos do AzCopy é:

```azcopy
azcopy --source <source> --destination <destination> [Options]
```

Os exemplos seguintes demonstram vários cenários para copiar dados para e do Microsoft Azure Blobs e dos ficheiros. Consulte o `azcopy --help` menu para uma explicação detalhada de parâmetros utilizados em cada amostra.

## <a name="blob-download"></a>Blob: Transferir
### <a name="download-single-blob"></a>Transferir BLOBs único

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --include "abc.txt"
```

Se a pasta `/mnt/myfiles` não existir, AzCopy criá-lo e transfere `abc.txt ` para a nova pasta.

### <a name="download-single-blob-from-secondary-region"></a>Transferir BLOBs único da região secundária

```azcopy
azcopy \
    --source https://myaccount-secondary.blob.core.windows.net/mynewcontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --include "abc.txt"
```

Tenha em atenção que tem de ter o armazenamento georredundante com acesso de leitura ativado.

### <a name="download-all-blobs"></a>Transferir todos os blobs

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --recursive
```

Suponha que residem os seguintes blobs no contentor especificado:  

```
abc.txt
abc1.txt
abc2.txt
vd1/a.txt
vd1/abcd.txt
```

Após a operação de transferência, o diretório `/mnt/myfiles` inclui os seguintes ficheiros:

```
/mnt/myfiles/abc.txt
/mnt/myfiles/abc1.txt
/mnt/myfiles/abc2.txt
/mnt/myfiles/vd1/a.txt
/mnt/myfiles/vd1/abcd.txt
```

Se não especificar opção `--recursive`, não existem BLOBs não serão transferidos.

### <a name="download-blobs-with-specified-prefix"></a>Transferir blobs com o prefixo especificado

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --include "a" \
    --recursive
```

Assumir que residem os seguintes blobs no contentor especificado. Todos os blobs a partir do prefixo `a` são transferidas.

```
abc.txt
abc1.txt
abc2.txt
xyz.txt
vd1\a.txt
vd1\abcd.txt
```

Após a operação de transferência, a pasta `/mnt/myfiles` inclui os seguintes ficheiros:

```
/mnt/myfiles/abc.txt
/mnt/myfiles/abc1.txt
/mnt/myfiles/abc2.txt
```

O prefixo aplica-se para o diretório virtual, o que faz a primeira parte do nome do blob. No exemplo mostrado acima, o diretório virtual não corresponde o prefixo especificado, pelo que não existem BLOBs é transferido. Além disso, se a opção `--recursive` não for especificado, AzCopy não transferir blobs.

### <a name="set-the-last-modified-time-of-exported-files-to-be-same-as-the-source-blobs"></a>Defina o período de última modificação de ficheiros exportados para ser o mesmo que os blobs de origem

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination "/mnt/myfiles" \
    --source-key <key> \
    --preserve-last-modified-time
```

Também pode excluir os blobs da operação de transferência com base na respetiva hora de última modificação. Por exemplo, se pretende excluir blobs cuja última hora de modificação é igual ou mais recente do que o ficheiro de destino, adicione o `--exclude-newer` opção:

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --preserve-last-modified-time \
    --exclude-newer
```

Ou se pretende excluir blobs cuja última hora de modificação é igual ou mais antiga do que o ficheiro de destino, adicione o `--exclude-older` opção:

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --preserve-last-modified-time \
    --exclude-older
```

## <a name="blob-upload"></a>Blob: carregar
### <a name="upload-single-file"></a>Carregar ficheiro único

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --include "abc.txt"
```

Se o contentor de destino especificado não existir, o AzCopy criá-lo e carrega o ficheiro para a mesma.

### <a name="upload-single-file-to-virtual-directory"></a>Carregar ficheiros única para o diretório virtual

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --include "abc.txt"
```

Se o diretório virtual especificado não existir, o AzCopy carrega o ficheiro para incluir o diretório virtual no nome do blob (*por exemplo,*, `vd/abc.txt` no exemplo acima).

### <a name="upload-all-files"></a>Carregar todos os ficheiros

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --recursive
```

Especificar a opção `--recursive` carrega o conteúdo do diretório especificado para recursivamente de armazenamento de BLOBs, que significa que todas as subpastas e os ficheiros são carregados bem. Por exemplo, suponha residem os seguintes ficheiros na pasta `/mnt/myfiles`:

```
/mnt/myfiles/abc.txt
/mnt/myfiles/abc1.txt
/mnt/myfiles/abc2.txt
/mnt/myfiles/subfolder/a.txt
/mnt/myfiles/subfolder/abcd.txt
```

Após a operação de carregamento, o contentor inclui os seguintes ficheiros:

```
abc.txt
abc1.txt
abc2.txt
subfolder/a.txt
subfolder/abcd.txt
```

Quando a opção `--recursive` não for especificado, apenas os seguintes três ficheiros são carregados:

```
abc.txt
abc1.txt
abc2.txt
```

### <a name="upload-files-matching-specified-pattern"></a>Carregar ficheiros correspondentes ao padrão especificado

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --include "a*" \
    --recursive
```

Partem do princípio de residem os seguintes ficheiros na pasta `/mnt/myfiles`:

```
/mnt/myfiles/abc.txt
/mnt/myfiles/abc1.txt
/mnt/myfiles/abc2.txt
/mnt/myfiles/xyz.txt
/mnt/myfiles/subfolder/a.txt
/mnt/myfiles/subfolder/abcd.txt
```

Após a operação de carregamento, o contentor inclui os seguintes ficheiros:

```
abc.txt
abc1.txt
abc2.txt
subfolder/a.txt
subfolder/abcd.txt
```

Quando a opção `--recursive` não for especificado, AzCopy ignora os ficheiros que estão em diretórios secundárias:

```
abc.txt
abc1.txt
abc2.txt
```

### <a name="specify-the-mime-content-type-of-a-destination-blob"></a>Especifique o tipo de conteúdo de MIME de um blob de destino
Por predefinição, o AzCopy define o tipo de conteúdo de um blob de destino para `application/octet-stream`. No entanto, pode especificar explicitamente o tipo de conteúdo através da opção `--set-content-type [content-type]`. Esta sintaxe define o tipo de conteúdo para todos os blobs numa operação de carregamento.

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/myContainer/ \
    --dest-key <key> \
    --include "ab" \
    --set-content-type "video/mp4"
```

Se a opção `--set-content-type` for especificado sem um valor, em seguida, o AzCopy define cada blob ou tipo de conteúdo do ficheiro, de acordo com a extensão de ficheiro.

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/myContainer/ \
    --dest-key <key> \
    --include "ab" \
    --set-content-type
```

## <a name="blob-copy"></a>Blob: cópia
### <a name="copy-single-blob-within-storage-account"></a>Copiar blob único na conta de armazenamento

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer1 \
    --destination https://myaccount.blob.core.windows.net/mycontainer2 \
    --source-key <key> \
    --dest-key <key> \
    --include "abc.txt"
```

Quando copiar um blob sem - opção de cópia de sincronização, um [cópia do lado do servidor](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) é efetuar a operação.

### <a name="copy-single-blob-across-storage-accounts"></a>Copiar blob único em contas de armazenamento

```azcopy
azcopy \
    --source https://sourceaccount.blob.core.windows.net/mycontainer1 \
    --destination https://destaccount.blob.core.windows.net/mycontainer2 \
    --source-key <key1> \
    --dest-key <key2> \
    --include "abc.txt"
```

Quando copiar um blob sem - opção de cópia de sincronização, um [cópia do lado do servidor](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) é efetuar a operação.

### <a name="copy-single-blob-from-secondary-region-to-primary-region"></a>Copiar blob único da região secundária para a região primária

```azcopy
azcopy \
    --source https://myaccount1-secondary.blob.core.windows.net/mynewcontainer1 \
    --destination https://myaccount2.blob.core.windows.net/mynewcontainer2 \
    --source-key <key1> \
    --dest-key <key2> \
    --include "abc.txt"
```

Tenha em atenção que tem de ter o armazenamento georredundante com acesso de leitura ativado.

### <a name="copy-single-blob-and-its-snapshots-across-storage-accounts"></a>Copiar blob único e o respetivos instantâneos em contas de armazenamento

```azcopy
azcopy \
    --source https://sourceaccount.blob.core.windows.net/mycontainer1 \
    --destination https://destaccount.blob.core.windows.net/mycontainer2 \
    --source-key <key1> \
    --dest-key <key2> \
    --include "abc.txt" \
    --include-snapshot
```

Após a operação de cópia, o contentor de destino inclui o blob e respetivos instantâneos. O contentor inclui o seguinte blob e respetivos instantâneos:

```
abc.txt
abc (2013-02-25 080757).txt
abc (2014-02-21 150331).txt
```

### <a name="synchronously-copy-blobs-across-storage-accounts"></a>Em sincronia copiar os blobs em contas de armazenamento
AzCopy por predefinição copia dados entre dois pontos finais de armazenamento de forma assíncrona. Por conseguinte, a operação de cópia é executada em segundo plano utilizando a capacidade de reserva de largura de banda que não tenha nenhum SLA em termos de rápido como um blob é copiado. 

O `--sync-copy` opção garante que a operação de cópia obtém velocidade consistente. AzCopy efetua a cópia síncrona ao transferir os blobs para copiar da origem especificada para memória local e, em seguida, carregá-los para o destino de armazenamento de Blobs.

```azcopy
azcopy \
    --source https://myaccount1.blob.core.windows.net/myContainer/ \
    --destination https://myaccount2.blob.core.windows.net/myContainer/ \
    --source-key <key1> \
    --dest-key <key2> \
    --include "ab" \
    --sync-copy
```

`--sync-copy`pode gerar o custo de saída adicionais em comparação comparado a cópia assíncrona. A abordagem recomendada é utilizar esta opção na VM do Azure, que se encontra na mesma região que a sua conta de armazenamento de origem para evitar o custo de saída.

## <a name="file-download"></a>Ficheiro: Transferir
### <a name="download-single-file"></a>Transferência de ficheiro único

```azcopy
azcopy \
    --source https://myaccount.file.core.windows.net/myfileshare/myfolder1/ \
    --destination /mnt/myfiles \
    --source-key <key> \
    --include "abc.txt"
```

Se a fonte especificada é uma partilha de ficheiros do Azure, em seguida, tem de especificar se o nome de ficheiro exata, (*por exemplo,* `abc.txt`) para transferir um ficheiro único ou especificar a opção `--recursive` para transferir todos os ficheiros na recursivamente a partilha. Tentativa de especificar um padrão de ficheiro e a opção `--recursive` resultados em conjunto num erro.

### <a name="download-all-files"></a>Transferir todos os ficheiros

```azcopy
azcopy \
    --source https://myaccount.file.core.windows.net/myfileshare/ \
    --destination /mnt/myfiles \
    --source-key <key> \
    --recursive
```

Tenha em atenção que não são transferidas quaisquer pastas vazias.

## <a name="file-upload"></a>Ficheiro: carregar
### <a name="upload-single-file"></a>Carregar ficheiro único

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.file.core.windows.net/myfileshare/ \
    --dest-key <key> \
    --include abc.txt
```

### <a name="upload-all-files"></a>Carregar todos os ficheiros

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.file.core.windows.net/myfileshare/ \
    --dest-key <key> \
    --recursive
```

Tenha em atenção que quaisquer pastas vazias não são carregadas.

### <a name="upload-files-matching-specified-pattern"></a>Carregar ficheiros correspondentes ao padrão especificado

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.file.core.windows.net/myfileshare/ \
    --dest-key <key> \
    --include "ab*" \
    --recursive
```

## <a name="file-copy"></a>Ficheiro: cópia
### <a name="copy-across-file-shares"></a>Copiar em partilhas de ficheiros

```azcopy
azcopy \
    --source https://myaccount1.file.core.windows.net/myfileshare1/ \
    --destination https://myaccount2.file.core.windows.net/myfileshare2/ \
    --source-key <key1> \
    --dest-key <key2> \
    --recursive
```
Quando copiar um ficheiro através de partilhas de ficheiros, um [cópia do lado do servidor](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) é efetuar a operação.

### <a name="copy-from-file-share-to-blob"></a>Copiar a partir de partilha de ficheiros para o blob

```azcopy
azcopy \ 
    --source https://myaccount1.file.core.windows.net/myfileshare/ \
    --destination https://myaccount2.blob.core.windows.net/mycontainer/ \
    --source-key <key1> \
    --dest-key <key2> \
    --recursive
```
Quando copiar um ficheiro de partilha de ficheiros para o blob, um [cópia do lado do servidor](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) é efetuar a operação.

### <a name="copy-from-blob-to-file-share"></a>Copiar a partir do blob para partilha de ficheiros

```azcopy
azcopy \
    --source https://myaccount1.blob.core.windows.net/mycontainer/ \
    --destination https://myaccount2.file.core.windows.net/myfileshare/ \
    --source-key <key1> \
    --dest-key <key2> \
    --recursive
```
Quando copiar um ficheiro a partir do blob para a partilha de ficheiros, um [cópia do lado do servidor](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) é efetuar a operação.

### <a name="synchronously-copy-files"></a>Em sincronia copiar ficheiros
Pode especificar o `--sync-copy` opção para copiar dados de armazenamento de ficheiros ao armazenamento de ficheiros, do armazenamento de ficheiros para o Blob Storage e do armazenamento de BLOBs para o armazenamento de ficheiro de forma síncrona. AzCopy executa esta operação ao descarregar a origem de dados à memória local e, em seguida, carregá-lo para o destino. Neste caso, o custo de saída padrão se aplica.

```azcopy
azcopy \
    --source https://myaccount1.file.core.windows.net/myfileshare1/ \
    --destination https://myaccount2.file.core.windows.net/myfileshare2/ \
    --source-key <key1> \
    --dest-key <key2> \
    --recursive \
    --sync-copy
```

Quando copiar do armazenamento de ficheiros para o Blob Storage, o tipo de blob predefinido é blob de bloco, o utilizador pode especificar a opção `--blob-type page` para alterar o tipo de blob de destino. Os tipos disponíveis são `page | block | append`.

Tenha em atenção que `--sync-copy` poderá gerar a saída adicional a comparação com cópia assíncrona de custos. A abordagem recomendada é utilizar esta opção na VM do Azure, que se encontra na mesma região que a sua conta de armazenamento de origem para evitar o custo de saída.

## <a name="other-azcopy-features"></a>Outras funcionalidades do AzCopy
### <a name="only-copy-data-that-doesnt-exist-in-the-destination"></a>Apenas os dados de cópia que não existem no destino
O `--exclude-older` e `--exclude-newer` parâmetros permitem-lhe excluir recursos de origem de anterior ou mais recente do que está a ser copiado, respetivamente. Se pretender apenas copiar recursos de origem que não existem no destino, pode especificar ambos os parâmetros de comando do AzCopy:

    --source http://myaccount.blob.core.windows.net/mycontainer --destination /mnt/myfiles --source-key <sourcekey> --recursive --exclude-older --exclude-newer

    --source /mnt/myfiles --destination http://myaccount.file.core.windows.net/myfileshare --dest-key <destkey> --recursive --exclude-older --exclude-newer

    --source http://myaccount.blob.core.windows.net/mycontainer --destination http://myaccount.blob.core.windows.net/mycontainer1 --source-key <sourcekey> --dest-key <destkey> --recursive --exclude-older --exclude-newer

### <a name="use-a-configuration-file-to-specify-command-line-parameters"></a>Utilize um ficheiro de configuração para especificar parâmetros da linha de comandos

```azcopy
azcopy --config-file "azcopy-config.ini"
```

Pode incluir quaisquer parâmetros de linha de comandos do AzCopy num ficheiro de configuração. AzCopy processa os parâmetros no ficheiro como se tivesse sido especificados na linha de comandos, efetuar uma substituição direta com o conteúdo do ficheiro.

Partem do princípio de um ficheiro de configuração com o nome `copyoperation`, que contém as linhas seguintes. Cada parâmetro do AzCopy pode ser especificado numa única linha.

    --source http://myaccount.blob.core.windows.net/mycontainer --destination /mnt/myfiles --source-key <sourcekey> --recursive --quiet

ou, no separar linhas:

    --source http://myaccount.blob.core.windows.net/mycontainer
    --destination /mnt/myfiles
    --source-key<sourcekey>
    --recursive
    --quiet

AzCopy falha se dividir o parâmetro por duas linhas, conforme mostrado aqui para o `--source-key` parâmetro:

    http://myaccount.blob.core.windows.net/mycontainer
    /mnt/myfiles
    --sourcekey
    <sourcekey>
    --recursive
    --quiet

### <a name="specify-a-shared-access-signature-sas"></a>Especifique uma assinatura de acesso partilhado (SAS)

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer1 \
    --destination https://myaccount.blob.core.windows.net/mycontainer2 \
    --source-sas <SAS1> \
    --dest-sas <SAS2> \
    --include abc.txt
```

Também pode especificar uma SAS no contentor do URI:

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer1/?SourceSASToken \
    --destination /mnt/myfiles \
    --recursive
```

Tenha em atenção que o AzCopy atualmente suporta apenas o [conta SAS](https://docs.microsoft.com/en-us/azure/storage/storage-dotnet-shared-access-signature-part-1).

### <a name="journal-file-folder"></a>Pasta de ficheiros do diário de alterações
Sempre que emitir um comando do AzCopy, este verifica se existe um ficheiro de diário de alterações na pasta predefinida ou se existe uma pasta que especificou através desta opção. Se o ficheiro do diário de alterações não existe em qualquer local, o AzCopy processa a operação como novo e gera um novo ficheiro de diário de alterações.

Se o ficheiro do diário de alterações existe, o AzCopy verifica se a linha de comandos de entrada corresponde a linha de comandos no ficheiro de diário de alterações. Se as duas linhas de comando corresponderem, o AzCopy retoma a operação incompleta. Se não corresponderem, o AzCopy pede ao utilizador optar por substituir o ficheiro de diário de alterações para iniciar uma operação de novo, ou para cancelar a operação atual.

Se pretender utilizar a localização predefinida para o ficheiro do diário de alterações:

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --resume
```

Se omitir opção `--resume`, ou especificar a opção `--resume` sem o caminho da pasta, conforme mostrado acima, o AzCopy cria o ficheiro do diário de alterações na localização predefinida, que é `~\Microsoft\Azure\AzCopy`. Se o ficheiro do diário de alterações já existir, o AzCopy retoma a operação com base no ficheiro diário de alterações.

Se pretender especificar uma localização para o ficheiro de diário personalizada:

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key key \
    --resume "/mnt/myjournal"
```

Este exemplo cria o ficheiro do diário de alterações se já existir. Se existir, o AzCopy retoma a operação com base no ficheiro diário de alterações.

Se pretender retomar uma operação do AzCopy, repita o comando a mesmo. AzCopy no Linux, em seguida, irá pedir confirmação:

```azcopy
Incomplete operation with same command line detected at the journal directory "/home/myaccount/Microsoft/Azure/AzCopy", do you want to resume the operation? Choose Yes to resume, choose No to overwrite the journal to start a new operation. (Yes/No)
```

### <a name="output-verbose-logs"></a>Registos verbosos de saída

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --verbose
```

### <a name="specify-the-number-of-concurrent-operations-to-start"></a>Especifique o número de operações simultâneas para iniciar
Opção `--parallel-level` Especifica o número de operações de cópia em simultâneo. Por predefinição, o AzCopy é iniciado um determinado número de operações simultâneas para aumentar o débito de transferência de dados. O número de operações simultâneas é igual oito vezes o número de processadores tem. Se estiver a executar o AzCopy através de uma rede de largura de banda reduzida, pode especificar um número inferior de – nível de paralelo para evitar falhas causadas por concorrência de recursos.

[!TIP]
>Para ver a lista completa dos parâmetros do AzCopy, consulte 'azcopy – Ajuda' menu.

## <a name="known-issues-and-best-practices"></a>Problemas conhecidos e melhores práticas
### <a name="error-net-core-is-not-found-in-the-system"></a>Erro: .NET Core não foi encontrado no sistema.
Se ocorrer um erro a indicar que o .NET Core não está instalado no sistema, o caminho para o binário .NET Core `dotnet` poderá estar em falta.

Para resolver este problema, localize o .NET Core binário no sistema:
```bash
sudo find / -name dotnet
```

Esta ação devolve o caminho para o dotnet binário. 

    /opt/rh/rh-dotnetcore11/root/usr/bin/dotnet
    /opt/rh/rh-dotnetcore11/root/usr/lib64/dotnetcore/dotnet
    /opt/rh/rh-dotnetcore11/root/usr/lib64/dotnetcore/shared/Microsoft.NETCore.App/1.1.2/dotnet

Agora, adicione este caminho para a variável de caminho. Para o sudo, edição secure_path para conter o caminho para dotnet binário:
```bash 
sudo visudo
### Append the path found in the preceding example to 'secure_path' variable
```

Neste exemplo, a variável de secure_path lê como:

```
secure_path = /sbin:/bin:/usr/sbin:/usr/bin:/opt/rh/rh-dotnetcore11/root/usr/bin/
```

Para o utilizador atual, editar.bash_profile/.profile para incluir o caminho para o binário na variável de caminho do dotnet 
```bash
vi ~/.bash_profile
### Append the path found in the preceding example to 'PATH' variable
```

Certifique-se de que o .NET Core está agora no caminho:
```bash
which dotnet
sudo which dotnet
```

### <a name="error-installing-azcopy"></a>Erro ao instalar o AzCopy
Se ocorrerem problemas com a instalação do AzCopy, pode tentar executar o AzCopy utilizando o script de bash no extraídos `azcopy` pasta.

```bash
cd azcopy
./azcopy
```

### <a name="limit-concurrent-writes-while-copying-data"></a>Limitar escritas em simultâneo ao copiar dados
Quando copia blobs ou ficheiros com o AzCopy, tenha em atenção que outra aplicação pode ser modificar os dados enquanto estiver a copiar. Se for possível, certifique-se de que os dados que está a copiar não está a ser modificados durante a operação de cópia. Por exemplo, quando copiar um VHD associado uma máquina virtual do Azure, certifique-se de que não existem outras aplicações atualmente estiver a escrever para o VHD. É uma boa forma de fazê-lo por leasing o recurso a ser copiados. Em alternativa, pode criar um instantâneo do VHD primeiro e, em seguida, copie o instantâneo.

Se não podem impedir outras aplicações de escrever para blobs ou ficheiros enquanto estão a ser copiados, em seguida, tenha em atenção que o tempo de que conclusão da tarefa, os recursos copiados podem já não ter paridade completa com os recursos de origem.

### <a name="run-one-azcopy-instance-on-one-machine"></a>Execute uma instância do AzCopy num computador.
AzCopy foi concebido para maximizar a utilização do seu recurso de máquina para acelerar a transferência de dados, recomendamos que execute apenas uma instância do AzCopy num computador e especifique a opção `--parallel-level` se precisar de mais simultâneas operações. Para obter mais detalhes, escreva `AzCopy --help parallel-level` na linha de comandos.

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre o Storage do Azure e AzCopy, consulte os seguintes recursos:

### <a name="azure-storage-documentation"></a>Documentação do Storage do Azure:
* [Introdução ao Storage do Azure](../storage-introduction.md)
* [Criar uma conta de armazenamento](../storage-create-storage-account.md)
* [Gerir a blobs com o Explorador de armazenamento](https://docs.microsoft.com/en-us/azure/vs-azure-tools-storage-explorer-blobs)
* [Utilizar a CLI do Azure 2.0 com o Storage do Azure](../storage-azure-cli.md)
* [Como utilizar o Blob storage do C++](../blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [Como utilizar o Armazenamento de blobs do Java](../blobs/storage-java-how-to-use-blob-storage.md)
* [Como utilizar o Armazenamento de blobs do Node.js](../blobs/storage-nodejs-how-to-use-blob-storage.md)
* [Como utilizar o Armazenamento de blobs do Python](../blobs/storage-python-how-to-use-blob-storage.md)

### <a name="azure-storage-blog-posts"></a>Mensagens de blogue de armazenamento do Azure:
* [Anunciar AzCopy na pré-visualização do Linux](https://azure.microsoft.com/en-in/blog/announcing-azcopy-on-linux-preview/)
* [Introdução ao pré-visualização de biblioteca de movimento de dados de armazenamento do Azure](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/)
* [AzCopy: Introdução ao copiar síncrona e tipo de conteúdo personalizado](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/01/13/azcopy-introducing-synchronous-copy-and-customized-content-type.aspx)
* [AzCopy: Anunciar disponibilidade geral do 3.0 AzCopy plus versão de pré-visualização do AzCopy 4.0 com suporte de tabela e ficheiro](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/10/29/azcopy-announcing-general-availability-of-azcopy-3-0-plus-preview-release-of-azcopy-4-0-with-table-and-file-support.aspx)
* [AzCopy: Otimizado para cenários de cópia em grande escala](http://go.microsoft.com/fwlink/?LinkId=507682)
* [AzCopy: Suporte para o armazenamento georredundante com acesso de leitura](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/04/07/azcopy-support-for-read-access-geo-redundant-account.aspx)
* [AzCopy: Transferir dados com o SAS token e o modo reiniciável](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/09/07/azcopy-transfer-data-with-re-startable-mode-and-sas-token.aspx)
* [AzCopy: Utilizando o Blob de cópia de conta em vários locais](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/04/01/azcopy-using-cross-account-copy-blob.aspx)
* [AzCopy: Carregar/transferência de ficheiros para Blobs do Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/12/03/azcopy-uploading-downloading-files-for-windows-azure-blobs.aspx)

