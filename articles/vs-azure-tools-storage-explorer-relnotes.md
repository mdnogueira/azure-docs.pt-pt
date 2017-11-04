---
title: "Notas de versão do Explorador de armazenamento do Microsoft Azure (pré-visualização) | Microsoft Docs"
description: "Notas de versão do Explorador de armazenamento do Microsoft Azure (pré-visualização)"
services: storage
documentationcenter: na
author: cawa
manager: paulyuk
editor: 
ms.assetid: 
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/31/2017
ms.author: cawa
ms.openlocfilehash: c1a3370d29b47da752e4ab1ea67ccc1a4cdd94df
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2017
---
# <a name="microsoft-azure-storage-explorer-preview-release-notes"></a>Notas de versão do Explorador de armazenamento do Microsoft Azure (pré-visualização)

Este artigo contém a versão de lançamento de notas do Explorador de armazenamento do Azure 0.9.0 (pré-visualização), bem como as notas de versão para versões anteriores.

[Explorador de armazenamento do Microsoft Azure (pré-visualização)](./vs-azure-tools-storage-manage-with-storage-explorer.md) é uma aplicação autónoma que lhe permite trabalhar facilmente com dados de armazenamento do Azure no Windows, macOS e Linux.

## <a name="version-091--090-preview"></a>Versão 0.9.1 / 0.9.0 (pré-visualização)
10/20/2017

### <a name="download-azure-storage-explorer-091-preview"></a>Transferir o Explorador de armazenamento do Azure 0.9.1 (pré-visualização)
- [Explorador de armazenamento do Azure 0.9.1 (pré-visualização) para Windows](https://go.microsoft.com/fwlink/?LinkId=708343)
- [Explorador de armazenamento do Azure 0.9.1 (pré-visualização) para Mac](https://go.microsoft.com/fwlink/?LinkId=708342)
- [Explorador de armazenamento do Azure 0.9.1 (pré-visualização) para Linux](https://go.microsoft.com/fwlink/?LinkId=722418)

### <a name="new"></a>novo
* Suporte de pré-visualização para a base de dados do Azure Cosmos:
    * [Documentação online](./cosmos-db/tutorial-documentdb-and-mongodb-in-storage-explorer.md)
    * Criar bases de dados e de coleções
    * Manipular dados
    * Consultar, criar ou eliminar documentos
    * Atualizar os procedimentos armazenados, funções definidas pelo utilizador ou acionadores
    * Utilize as cadeias de ligação para ligar a e gerir as bases de dados
* Melhoria do desempenho de carregamento/transferir blobs pequenos muitos.
* Adicionar uma ação de "All Repita" se houver falhas num grupo de carregamento de blob ou grupo de transferências de blob.
* Explorador de armazenamento será agora colocado em pausa iteração durante o carregamento de blob/transferência se detetar que a ligação de rede foi perdida. Em seguida, pode retomar iteração assim que a ligação de rede foi restabelecida.
* Foi adicionada a capacidade para "Fechar All", "Feche outros" e "Fechar" separadores através do menu de contexto.
* Agora, o Explorador de armazenamento utiliza caixas de diálogo nativas e menus de contexto nativo.
* Explorador de armazenamento é agora mais acessível. Melhoramentos incluem:
    * Suporte de leitor de ecrã melhorada, para NVDA no Windows e para VoiceOver no Mac
    * Temas melhorada elevado contraste
    * Correções de foco do teclado tabbing e teclado

### <a name="fixes"></a>Correções
* Se tentou abrir ou transferir um blob com um nome de ficheiro inválido do Windows, a operação falhará. Explorador de armazenamento será detetar se um nome de blob é inválido e peça se gostaria de codificá-lo ou ignorar o blob. Explorador de armazenamento também Deteta se um nome de ficheiro parece ser codificados e pedir-lhe se pretende descodificá-lo antes de carregar.
* Durante o carregamento de blob, o editor do contentor do blob de destino seria, por vezes, não corretamente atualizar. Esta correção.
* O suporte de várias formas de cadeias de ligação e SAS URIs regressed. Podemos ter resolvidos todos os problemas conhecidos, mas envie comentários, se ainda ocorrerem problemas.
* A notificação de atualização foi interrompida para alguns utilizadores numa 0.9.0. Corrigir este problema e para os que são afetados pelos erros, pode transferir manualmente a versão mais recente do Explorador de armazenamento [aqui](https://azure.microsoft.com/en-us/features/storage-explorer/)

### <a name="known-issues"></a>Problemas conhecidos
* Explorador de armazenamento não suporta contas ADFS.
* Teclas de atalho para "Explorador de vista" e "Vista de gestão de contas" devem ser Ctrl / Cmd + Shift + E e Ctrl / Cmd + Shift + A respetivamente.
* Quando a filtrar a pilha do Azure, carregar determinados ficheiros como blobs de acréscimo pode falhar.
* Depois de clicar em "Cancelar" uma tarefa, pode demorar um pouco para essa tarefa Cancelar. Isto acontece porque está a utilizar a solução de filtro de cancelar descrita aqui.
* Se escolher o certificado PIN/smart card errado, terá de reiniciar para ter o Explorador de armazenamento se esqueça de que decisão.
* O painel de definições de conta pode mostrar que terá de reintroduzir as credenciais para filtrar as subscrições.
* Mudar o nome de blobs (individualmente ou dentro de um contentor do blob cujo nome foi alterado) não preserva a instantâneos. Todas as outras propriedades e metadados para blobs, ficheiros e entidades são preservados durante uma mudança de nome.
* Embora a pilha do Azure atualmente não suporta partilhas de ficheiros, um nó de partilhas de ficheiros continua a aparecer sob uma conta de armazenamento de pilha do Azure ligada.
* A shell de Electron utilizada pelo Explorador de armazenamento tem problemas com algumas aceleração de hardware da GPU (unidade de processamento de gráficos). Se o Explorador de armazenamento apresenta uma janela de principal (vazia) em branco, pode tentar iniciar o Explorador de armazenamento na linha de comandos e desativar a aceleração de GPU adicionando o `--disable-gpu` comutador:
```
./StorageExplorer.exe --disable-gpu
```
* Para os utilizadores no Ubuntu 14.04, terá de garantir GCC é atualizada - Isto pode ser efetuado executando os comandos seguintes e, em seguida, reiniciar o computador:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para os utilizadores no Ubuntu 17.04, terá de instalar GConf - Isto pode ser efetuado executando os comandos seguintes e, em seguida, reiniciar o computador:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-0816-preview"></a>Versão 0.8.16 (pré-visualização)
8/21/2017

### <a name="download-azure-storage-explorer-0816-preview"></a>Transferir o Explorador de armazenamento do Azure 0.8.16 (pré-visualização)
* [Transferir o Explorador de armazenamento do Azure 0.8.16 (pré-visualização) para Windows](https://go.microsoft.com/fwlink/?LinkId=809306)
* [Transferir o Explorador de armazenamento do Azure 0.8.16 (pré-visualização) para Mac](https://go.microsoft.com/fwlink/?LinkId=809307)
* [Transferir o Explorador de armazenamento do Azure 0.8.16 (pré-visualização) para Linux](https://go.microsoft.com/fwlink/?LinkId=809308)

### <a name="new"></a>novo
* Quando abre um blob, Explorador de armazenamento irá solicitar-lhe carregar o ficheiro transferido, desde que seja detetada uma alteração
* Pilha de Azure início de sessão experiência melhorada
* Melhoria do desempenho de carregamento/transferência muitos ficheiros pequenos ao mesmo tempo


### <a name="fixes"></a>Correções
* Para alguns tipos de BLOBs, optando por "substituir" durante um conflito de carregamento, por vezes, resultaria no carregamento a ser reiniciado.
* Na versão 0.8.15, carregamentos, por vezes, seriam compartimento de 99%.
* Quando o carregamento dos ficheiros para uma partilha de ficheiros, se tiver optado por carregar para um diretório que não foi ainda existe, o carregamento irão falhar.
* Explorador de armazenamento foi incorretamente gerar carimbos de data / hora para assinaturas de acesso partilhado e consultas de tabela.


### <a name="known-issues"></a>Problemas conhecidos
* Atualmente, o utilizando um nome e a cadeia de ligação de chave não funcionam. Irá ser corrigida na versão seguinte. Até que, em seguida, pode utilizar anexar com nome e chave.
* Se tentar abrir um ficheiro com um nome de ficheiro inválido do Windows, a transferência resultará num ficheiro não foi encontrado o erro.
* Depois de clicar em "Cancelar" uma tarefa, pode demorar um pouco para essa tarefa Cancelar. Esta é uma limitação da biblioteca do nó de armazenamento do Azure.
* Depois de concluir o carregamento de um blob, no separador que iniciado o carregamento é atualizado. Esta é uma alteração do comportamento anterior e também fará com que ser direcionado novamente para a raiz do contentor que na.
* Se escolher o certificado PIN/smart card errado, terá de reiniciar para ter o Explorador de armazenamento se esqueça de que decisão.
* O painel de definições de conta pode mostrar que terá de reintroduzir as credenciais para filtrar as subscrições.
* Mudar o nome de blobs (individualmente ou dentro de um contentor do blob cujo nome foi alterado) não preserva a instantâneos. Todas as outras propriedades e metadados para blobs, ficheiros e entidades são preservados durante uma mudança de nome.
* Embora a pilha do Azure atualmente não suporta partilhas de ficheiros, um nó de partilhas de ficheiros continua a aparecer sob uma conta de armazenamento de pilha do Azure ligada.
* Para os utilizadores no Ubuntu 14.04, terá de garantir GCC é atualizada - Isto pode ser efetuado executando os comandos seguintes e, em seguida, reiniciar o computador:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Para os utilizadores no Ubuntu 17.04, terá de instalar GConf - Isto pode ser efetuado executando os comandos seguintes e, em seguida, reiniciar o computador:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="previous-releases"></a>Versões anteriores

* [Versão 0.8.14](#version-0814)
* [Versão 0.8.13](#version-0813)
* [Versão 0.8.12 / 0.8.11 / 0.8.10](#version-0812--0811--0810)
* [Versão 0.8.9 / 0.8.8](#version-089--088)
* [Versão 0.8.7](#version-087)
* [Versão 0.8.6](#version-086)
* [Versão 0.8.5](#version-085)
* [Versão 0.8.4](#version-084)
* [Versão 0.8.3](#version-083)
* [Versão 0.8.2](#version-082)
* [Versão 0.8.0](#version-080)
* [Versão 0.7.20160509.0](#version-07201605090)
* [Versão 0.7.20160325.0](#version-07201603250)
* [Versão 0.7.20160129.1](#version-07201601291)
* [Versão 0.7.20160105.0](#version-07201601050)
* [Versão 0.7.20151116.0](#version-07201511160)


### <a name="version-0814"></a>Versão 0.8.14
06/22/2017

### <a name="new"></a>novo

* Versão atualizada do Electron para 1.7.2 para tirar o máximo partido das várias atualizações de segurança críticas
* Pode agora rapidamente aceder ao guia de resolução de problemas online no menu de ajuda
* Explorador de armazenamento de resolução de problemas [guia][2]
* [Instruções] [ 3] sobre a ligação a uma subscrição de pilha do Azure

### <a name="known-issues"></a>Problemas conhecidos

* Botões de caixa de diálogo de confirmação de pasta de eliminação não registar os cliques do rato no Linux. Solução consiste em utilizar a tecla Enter
* Se escolher o certificado PIN/smart card errado, em seguida, terá de reiniciar para ter o Explorador de armazenamento se esqueça de decisão
* Ter mais de 3 grupos de blobs ou ficheiros carregar ao mesmo tempo, poderá fazer com erros
* O painel de definições de conta pode mostrar que terá de reintroduzir as credenciais para filtrar as subscrições
* Mudar o nome de blobs (individualmente ou dentro de um contentor do blob cujo nome foi alterado) não preserva a instantâneos. Todas as outras propriedades e metadados para blobs, ficheiros e entidades são preservados durante uma mudança de nome.
* Embora a pilha do Azure atualmente não suporta partilhas de ficheiros, um nó de partilhas de ficheiros continua a aparecer sob uma conta de armazenamento de pilha do Azure ligada.
* Ubuntu 14.04 instalação necessita de versão do gcc atualizado ou atualizado – passos para atualizar abaixo:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

### <a name="version-0813"></a>Versão 0.8.13
05/12/2017

#### <a name="new"></a>novo

* Explorador de armazenamento de resolução de problemas [guia][2]
* [Instruções] [ 3] sobre a ligação a uma subscrição de pilha do Azure

#### <a name="fixes"></a>Correções

* Corrigido: O carregamento de ficheiros tinha uma elevada probabilidade de causar um fora de erro de memória
* Fixo: Pode agora iniciar sessão com PIN/smart card
* Corrigido: Abrir no Portal agora funciona com o Azure China, Datacenters do Azure, Azure US Government e pilha do Azure
* Corrigido: Ao carregar uma pasta para um contentor de blob, um erro de "Operação ilegal", por vezes, ocorreriam
* Corrigido: Selecionar tudo foi desativado durante a gestão de instantâneos
* Fixo: Os metadados do base blob podem obter substituídos depois de visualizar as propriedades dos respetivos instantâneos.

#### <a name="known-issues"></a>Problemas conhecidos

* Se escolher o certificado PIN/smart card errado, em seguida, terá de reiniciar para ter o Explorador de armazenamento se esqueça de decisão
* Enquanto ampliado ou reduzir, o nível de zoom momentaneamente pode repor o nível predefinido
* Ter mais de 3 grupos de blobs ou ficheiros carregar ao mesmo tempo, poderá fazer com erros
* O painel de definições de conta pode mostrar que terá de reintroduzir as credenciais para filtrar as subscrições
* Mudar o nome de blobs (individualmente ou dentro de um contentor do blob cujo nome foi alterado) não preserva a instantâneos. Todas as outras propriedades e metadados para blobs, ficheiros e entidades são preservados durante uma mudança de nome.
* Embora a pilha do Azure atualmente não suporta partilhas de ficheiros, um nó de partilhas de ficheiros continua a aparecer sob uma conta de armazenamento de pilha do Azure ligada.
* Ubuntu 14.04 instalação necessita de versão do gcc atualizado ou atualizado – passos para atualizar abaixo:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```


### <a name="version-0812--0811--0810"></a>Versão 0.8.12 / 0.8.11 / 0.8.10
04/07/2017

#### <a name="new"></a>novo

* Explorador de armazenamento agora será fechada automaticamente quando instala uma atualização de notificação de atualização
* Acesso rápido no local fornece uma experiência melhorada para trabalhar com os recursos acedidos com frequência
* No editor de contentor do Blob, agora, pode ver que o virtual machine um blob em leasing pertence
* Agora pode fechar o painel do lado esquerdo
* A deteção é executada em simultâneo como transferência
* Estatísticas de utilização no editores de contentor do Blob, a partilha de ficheiros e a tabela para ver o tamanho do recurso ou seleção
* Pode agora o início de sessão ao Azure Active Directory (AAD) com base em contas de pilha do Azure.
* Agora pode carregar ficheiros de arquivo ao longo de 32MB para contas de armazenamento Premium
* Suporte de acessibilidade de melhorada
* Agora pode adicionar fidedigna de Base-64 codificado certificados x. 509 SSL, irá editar -&gt; certificados SSL -&gt; importar certificados

#### <a name="fixes"></a>Correções

* Corrigido: depois de atualizar as credenciais de uma conta, a vista de árvore seria, por vezes, não atualiza automaticamente
* Corrigido: gerar uma SAS para tabelas e filas de emulador resultaria num URL inválido
* Fixo: contas de armazenamento premium podem agora ser expandidas enquanto um proxy está ativado
* Corrigido: o botão ' Aplicar ' da página de gestão de contas não funciona se tiver selecionadas de contas de 1 ou 0
* Fixo: carregar blobs que necessitam de resoluções de conflito poderão falhar - fixo em 0.8.11
* Corrigido: enviar comentários foi quebrada em 0.8.11 - fixo em 0.8.12

#### <a name="known-issues"></a>Problemas conhecidos

* Após a atualização para 0.8.10, terá de atualizar todas as suas credenciais.
* Enquanto ampliado ou reduzir, o nível de zoom momentaneamente pode repor o nível predefinido.
* Ter mais de 3 grupos de blobs ou ficheiros carregar ao mesmo tempo poderá causar erros.
* O painel de definições de conta pode mostrar que terá de reintroduzir as credenciais para filtrar as subscrições.
* Mudar o nome de blobs (individualmente ou dentro de um contentor do blob cujo nome foi alterado) não preserva a instantâneos. Todas as outras propriedades e metadados para blobs, ficheiros e entidades são preservados durante uma mudança de nome.
* Embora a pilha do Azure atualmente não suporta partilhas de ficheiros, um nó de partilhas de ficheiros continua a aparecer sob uma conta de armazenamento de pilha do Azure ligada.
* Ubuntu 14.04 instalação necessita de versão do gcc atualizado ou atualizado – passos para atualizar abaixo:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```


### <a name="version-089--088"></a>Versão 0.8.9 / 0.8.8
02/23/2017

<iframe width="560" height="315" src="https://www.youtube.com/embed/R6gonK3cYAc?ecver=1" frameborder="0" allowfullscreen></iframe>

<iframe width="560" height="315" src="https://www.youtube.com/embed/SrRPCm94mfE?ecver=1" frameborder="0" allowfullscreen></iframe>


#### <a name="new"></a>novo

* Explorador de armazenamento 0.8.9 irá transferir automaticamente a versão mais recente de atualizações.
* URI de SAS gerado para anexar uma conta de armazenamento correções: utilizar um portal iria resultar num erro.
* Agora pode criar, gerir e promover instantâneos do blob.
* Pode agora iniciar sessão para contas de Azure China, Datacenters do Azure e Azure US Government.
* Agora, pode alterar o nível de zoom. Utilize as opções no menu da vista para aplicar Zoom no, Zoom Out e repor Zoom.
* Carateres Unicode já são suportados nos metadados de utilizador para os blobs e de ficheiros.
* Melhoramentos de acessibilidade.
* Notas de versão a versão seguinte podem ser visualizadas a notificação de atualização. Também pode ver as notas de versão atual no menu de ajuda.

#### <a name="fixes"></a>Correções

* Fixo: o número de versão é agora apresentado corretamente no painel de controlo do Windows
* Fixo: pesquisa já não é limitada a 50 000 nós
* Fixo: carregar para uma partilha de ficheiros puserem para sempre se o diretório de destino já não existe
* Fixa: estabilidade melhorada para carregamentos de tempo e as transferências

#### <a name="known-issues"></a>Problemas conhecidos

* Enquanto ampliado ou reduzir, o nível de zoom momentaneamente pode repor o nível predefinido.
* Acesso rápido só funciona com itens de subscrição com base. Recursos locais ou recursos ligados através de chave ou o SAS token não são suportados nesta versão.
* Pode demorar um acesso rápido a alguns segundos para navegar para o recurso de destino, dependendo da quantidade de recursos que tem.
* Ter mais de 3 grupos de blobs ou ficheiros carregar ao mesmo tempo poderá causar erros.

12/16/2016
### <a name="version-087"></a>Versão 0.8.7

<iframe width="560" height="315" src="https://www.youtube.com/embed/Me4Y4jxoer8?ecver=1" frameborder="0" allowfullscreen></iframe>

#### <a name="new"></a>novo

* Pode escolher como resolver conflitos no início de sessão de uma atualização, a transferência ou a cópia na janela de atividades
* Coloque o cursor sobre um separador para ver o caminho completo do recurso de armazenamento
* Ao clicar num separador, sincronizar com a localização no painel de navegação do lado esquerdo

#### <a name="fixes"></a>Correções

* Fixo: Explorador de armazenamento é agora uma aplicação fidedigna no Mac
* Fixo: Ubuntu 14.04 é novamente suportada
* Fixo:, Por vezes, a conta de adicionar IU está intermitente ao carregar as subscrições
* Fixo:, Por vezes, nem todos os recursos de armazenamento foram listados no painel de navegação do lado esquerdo
* Corrigido: O painel de ações por vezes apresentado ações vazias
* Fixo: O tamanho da janela da última sessão fechada é agora retido
* Fixo: Pode abrir vários separadores para o mesmo recurso utilizando o menu de contexto

#### <a name="known-issues"></a>Problemas conhecidos

* Acesso rápido só funciona com itens de subscrição com base. Recursos locais ou recursos ligados através de chave ou o SAS token não são suportados nesta versão
* Poderá demorar um acesso rápido a alguns segundos para navegar para o recurso de destino, dependendo da quantidade de recursos tem
* Ter mais de 3 grupos de blobs ou ficheiros carregar ao mesmo tempo, poderá fazer com erros
* Os identificadores de pesquisa procura em aproximadamente 50 000 nós - depois da primeira, o desempenho pode ser afetado ou pode fazer com que a exceção não processada
* Pela primeira vez utilizando o Explorador de armazenamento no macOS, poderá ver vários pedidos solicitando a permissão de utilizador para acesso de keychain. Sugerimos que selecionou permitir sempre que, de modo a linha de comandos não irá aparecer novamente

11/18/2016
### <a name="version-086"></a>Versão 0.8.6

#### <a name="new"></a>novo

* Pode agora pin utilizado mais frequentemente serviços para o acesso rápido para navegação fácil
* Pode agora abrir editores vários separadores diferentes. Único clique para abrir um separador temporário; Faça duplo clique para abrir um separador permanente. Também pode clicar no separador temporário para o tornar um separador permanente
* Efetuamos desempenho percetível e melhoramentos estabilidade para carregamentos e transferências, especialmente para ficheiros grandes em máquinas rápidas
* Pastas "virtuais" vazias agora podem ser criadas em contentores de BLOBs
* Ter novamente introduzimos pesquisa de âmbito com a nossa nova pesquisa avançada subcadeia, pelo que tem agora duas opções para procurar:
    * Global pesquisar - basta introduzir um termo de pesquisa na caixa de texto de pesquisa
    * Pesquisa de âmbito - clique no ícone da lupa junto de um nó, em seguida, adicionar um termo de pesquisa ao fim do caminho, ou com o botão direito e selecione "Pesquisa de aqui"
* Foi adicionado temas vários: claro (predefinição), escuros, elevado contraste negra e elevado contraste em branco. Aceda a editar -&gt; temas para alterar a sua preferência de temas
* Pode modificar as propriedades de Blob e ficheiro
* Agora suportamos o codificado (base64) e não codificado de fila de mensagens
* No Linux, um SO de 64 bits é agora necessário. Para esta versão suportamos apenas 64 bits Ubuntu 16.04.1 LTS
* Iremos foi atualizado com a nossa logótipo!

#### <a name="fixes"></a>Correções

* Corrigido: Ecrã freezing problemas
* Fixo: Segurança avançada
* Corrigido: Contas anexadas, por vezes, duplicadas foi apresentada
* Fixo: Um blob com um tipo de conteúdo foi possível gerar uma exceção
* Corrigido: Abrir o painel de consulta uma tabela vazia não foi possível
* Fixo: Erros na pesquisa de varia
* Fixo: Aumentar o número de recursos carregado a partir do 50 a 100 quando clicar em "Mais carga"
* Corrigido: Na primeira execução, se uma conta é iniciada, iremos agora selecionar todas as subscrições para essa conta por predefinição

#### <a name="known-issues"></a>Problemas conhecidos

* Esta versão do Explorador de armazenamento não é executado no Ubuntu 14.04
* Para abrir vários separadores para o mesmo recurso, não continuamente clique no mesmo recurso. Clique no outro recurso e, em seguida, volte atrás e, em seguida, clique no recurso original para abri-lo novamente noutro separador
* Acesso rápido só funciona com itens de subscrição com base. Recursos locais ou recursos ligados através de chave ou o SAS token não são suportados nesta versão
* Poderá demorar um acesso rápido a alguns segundos para navegar para o recurso de destino, dependendo da quantidade de recursos tem
* Ter mais de 3 grupos de blobs ou ficheiros carregar ao mesmo tempo, poderá fazer com erros
* Os identificadores de pesquisa procura em aproximadamente 50 000 nós - depois da primeira, o desempenho pode ser afetado ou pode fazer com que a exceção não processada

10/03/2016
### <a name="version-085"></a>Versão 0.8.5

#### <a name="new"></a>novo

* Agora pode utilizar chaves geradas pelo Portal SAS para anexar a contas de armazenamento e de recursos

#### <a name="fixes"></a>Correções

* Corrigido: condição provável de antecipação durante a pesquisa, por vezes, causado nós para se tornar não é possível expandir
* Fixo: "Utilizar HTTP" não funciona ao ligar a contas de armazenamento com o nome da conta e chave
* Fixo: Chaves SAS (especialmente gerado pelo Portal aqueles) devolverem um erro de "à direita barra"
* Fixo: tabela importar problemas
    * Por vezes, a chave de partição e a chave de linha foram inversa
    * Não é possível ler as chaves de partição "null"

#### <a name="known-issues"></a>Problemas conhecidos

* Identificadores de pesquisa procura em aproximadamente 50 000 nós - depois da primeira, o desempenho pode ser afetado
* Pilha do Azure não suporta atualmente a ficheiros, pelo que tentar expandir ficheiros irá mostrar um erro

09/12/2016
### <a name="version-084"></a>Versão 0.8.4

<iframe width="560" height="315" src="https://www.youtube.com/embed/cr5tOGyGrIQ?ecver=1" frameborder="0" allowfullscreen></iframe>

#### <a name="new"></a>novo

* Gerar hiperligações diretas para contas de armazenamento, contentores, filas, tabelas ou partilhas de ficheiros para a partilha e suportam o facilitar o acesso aos recursos da sua - Windows e Mac OS
* Procure os contentores de BLOBs, tabelas, filas, partilhas de ficheiros ou contas de armazenamento a partir da caixa de pesquisa
* Agora pode agrupar cláusulas no construtor de consultas de tabela
* Mudar o nome e copiar/colar contentores de BLOBs, partilhas de ficheiros, tabelas, blobs, blob pastas, ficheiros e diretórios de contas ligados por SAS e contentores
* Mudar o nome e a cópia dos contentores de BLOBs e partilhas de ficheiros agora preservar as propriedades e os metadados

#### <a name="fixes"></a>Correções

* Fixo: não é possível editar as entidades da tabela se contiverem propriedades booleanos ou binárias

#### <a name="known-issues"></a>Problemas conhecidos

* Identificadores de pesquisa procura em aproximadamente 50 000 nós - depois da primeira, o desempenho pode ser afetado

08/03/2016
### <a name="version-083"></a>Versão 0.8.3

<iframe width="560" height="315" src="https://www.youtube.com/embed/HeGW-jkSd9Y?ecver=1" frameborder="0" allowfullscreen></iframe>

#### <a name="new"></a>novo

* Mudar o nome de contentores, tabelas, partilhas de ficheiros
* Experiência melhorada de construtor de consultas
* Capacidade para guardar e carregar consultas
* Direcionar ligações para tabelas de contas ou de contentores, filas, de armazenamento ou partilhas para a partilha e facilmente aceder aos seus recursos de ficheiros (apenas Windows - macOS suportam disponível em breve!)
* Capacidade para gerir e configurar as regras CORS

#### <a name="fixes"></a>Correções

* Fixo: Accounts Microsoft requerem reautenticação cada 8-12 horas

#### <a name="known-issues"></a>Problemas conhecidos

* Por vezes, a IU pode aparecer congelada - maximizando a janela de ajuda a resolver este problema
* instalação de macOS pode necessitar de permissões elevadas
* Painel de definições de conta pode mostrar que terá de reintroduzir as credenciais para filtrar as subscrições
* Mudar o nome de partilhas de ficheiros contentores de BLOBs, tabelas e não preserva a metadados ou outras propriedades no contentor, por exemplo, a quota de partilha de ficheiros, um nível de acesso público ou políticas de acesso
* Mudar o nome de blobs (individualmente ou dentro de um contentor do blob cujo nome foi alterado) não preserva a instantâneos. Todas as outras propriedades e metadados para blobs, ficheiros e entidades são preservados durante uma mudança de nome
* Copiar ou mudar o nome de recursos não funciona no contas ligados por SAS

07/07/2016
### <a name="version-082"></a>Versão 0.8.2

<iframe width="560" height="315" src="https://www.youtube.com/embed/nYgKbRUNYZA?ecver=1" frameborder="0" allowfullscreen></iframe>

#### <a name="new"></a>novo

* As contas de armazenamento estão agrupadas por subscrições; armazenamento de desenvolvimento e de recursos ligados através de SAS ou de chave são apresentados no nó (locais e anexadas)
* Terminar sessão das contas no painel de "Definições de conta do Azure"
* Configurar definições de proxy para ativar e gerir o início de sessão
* Criar e quebra de concessões de blob
* Contentores de BLOBs aberta, filas, tabelas e os ficheiros de clique único

#### <a name="fixes"></a>Correções

* Corrigido: inseridas com bibliotecas .NET ou Java de fila de mensagens não é corretamente descodificar a partir de base64
* Fixo: $metrics tabelas não são apresentadas para contas do Blob Storage
* Fixo: nó de tabelas não funciona para o armazenamento (desenvolvimento) local

#### <a name="known-issues"></a>Problemas conhecidos

* instalação de macOS pode necessitar de permissões elevadas

06/15/2016
### <a name="version-080"></a>Versão 0.8.0

<iframe width="560" height="315" src="https://www.youtube.com/embed/ycfQhKztSIY?ecver=1" frameborder="0" allowfullscreen></iframe>

<iframe width="560" height="315" src="https://www.youtube.com/embed/k4_kOUCZ0WA?ecver=1" frameborder="0" allowfullscreen></iframe>

<iframe width="560" height="315" src="https://www.youtube.com/embed/3zEXJcGdl_k?ecver=1" frameborder="0" allowfullscreen></iframe>

#### <a name="new"></a>novo

* Suporte de partilha de ficheiros: ver, carregar, transferir, copiar ficheiros e diretórios, SAS URIs (criar e ligar)
* Experiência de utilizador para ligar ao armazenamento com as chaves de conta ou SAS URIs melhorada
* Exportar os resultados da consulta de tabela
* Reordenação de coluna de tabela e personalização
* Ver os contentores de BLOBs de $logs e $metrics tabelas para contas de armazenamento com a métrica ativada
* Melhorado exportar e importar o comportamento, inclui agora o tipo de valor de propriedade

#### <a name="fixes"></a>Correções

* Fixo: carregar ou transferir blobs grandes pode resultar em carregamentos/transferências incompletas
* Fixo: Editar, adicionar ou importar uma entidade com um valor de cadeia numérica ("1") irá convertê-lo para duplo
* Corrigido: Não é possível expandir o nó de tabela no ambiente de desenvolvimento local

#### <a name="known-issues"></a>Problemas conhecidos

* $metrics tabelas não estão visíveis para contas do Blob Storage
* Fila de mensagens através de programação adicionadas não pode ser apresentada corretamente se as mensagens são codificadas com codificação Base64

05/17/2016
### <a name="version-07201605090"></a>Versão 0.7.20160509.0

#### <a name="new"></a>novo

* Falhas de processamento para a aplicação de erros melhor

#### <a name="fixes"></a>Correções

* Erros fixo onde as mensagens de barra de informações por vezes, não apareçam quando as credenciais de início de sessão eram necessárias

#### <a name="known-issues"></a>Problemas conhecidos

* As tabelas: Adicionar, editar ou importar uma entidade que tem uma propriedade com um valor numérico ambiguously, tais como "1" ou "1.0" e o utilizador tenta enviá-lo como um `Edm.String`, o valor regressará através do cliente de API como um Edm.Double

03/31/2016

### <a name="version-07201603250"></a>Versão 0.7.20160325.0

<iframe width="560" height="315" src="https://www.youtube.com/embed/imbgBRHX65A?ecver=1" frameborder="0" allowfullscreen></iframe>

<iframe width="560" height="315" src="https://www.youtube.com/embed/ceX-P8XZ-s8?ecver=1" frameborder="0" allowfullscreen></iframe>


#### <a name="new"></a>novo

* Suporte de tabela: visualização, consultar, exportação, importar e as operações CRUD de entidades
* Suporte da fila: visualizar, adicionar, dequeueing mensagens
* Gerar o URI SAS para contas de armazenamento
* Ligar a contas de armazenamento com o URI SAS
* Notificações de atualização para que as futuras atualizações Explorador de armazenamento
* Atualizado para o aspeto e funcionalidade

#### <a name="fixes"></a>Correções

* Melhorias de desempenho e fiabilidade

### <a name="known-issues-amp-mitigations"></a>Problemas conhecidos &amp; mitigações

* Transferência de ficheiros grandes blob não funciona corretamente - é recomendável utilizar o AzCopy enquanto foi resolver este problema
* As credenciais da conta não serão possível obter nem em cache se na pasta raiz não é possível localizar ou não pode ser escrita
* Se podemos são adicionar, editar ou importar uma entidade que tem uma propriedade com um valor numérico ambiguously, tais como "1" ou "1.0", e o utilizador tentar enviá-lo como um `Edm.String`, o valor regressará através do cliente de API como um Edm.Double
* Quando importar os ficheiros CSV com os registos de múltiplas linhas, os dados poderão obter chopped ou scrambled

02/03/2016

### <a name="version-07201601291"></a>Versão 0.7.20160129.1

#### <a name="fixes"></a>Correções

* Melhoria do desempenho global quando o carregamento, a transferência e copiar os blobs

01/14/2016

### <a name="version-07201601050"></a>Versão 0.7.20160105.0

#### <a name="new"></a>novo

* Apoio técnico para Linux (funcionalidades de paridade para OSX)
* Adicionar contentores de Blobs com a chave de assinaturas de acesso partilhado (SAS)
* Adicionar contas de armazenamento para o Azure China
* Adicionar contas de armazenamento com os pontos finais personalizados
* Abra e visualize os blobs de texto e imagem de conteúdo
* Ver e editar propriedades de BLOBs e metadados

#### <a name="fixes"></a>Correções

* Corrigido: carregar ou transferir um grande número de blobs (500 +) pode, por vezes, fazer com que a aplicação tem um ecrã branco
* Fixo: quando definir o nível de acesso de público do contentor de blob, o novo valor não é atualizado até que defina novamente o foco no contentor. Além disso, a caixa de diálogo sempre assume a predefinição "Sem acesso de público" e não o valor atual real.
* Geral melhor teclado acessibilidade e IU de suporte
* Texto de histórico de navegação estrutural encapsula num wrapper quando é longo com espaço em branco
* Caixa de diálogo SAS suporta a validação de entrada
* Armazenamento local continuam a estar disponíveis mesmo se as credenciais de utilizador expiraram
* Quando é eliminado um contentor de blob aberta, o Explorador do blob no lado direito está fechado

#### <a name="known-issues"></a>Problemas conhecidos

* Instalação do Linux tem de versão do gcc atualizado ou atualizado – passos para atualizar abaixo:
    * `sudo add-apt-repository ppa:ubuntu-toolchain-r/test`
    * `sudo apt-get update`
    * `sudo apt-get upgrade`
    * `sudo apt-get dist-upgrade`

11/18/2015
### <a name="version-07201511160"></a>Versão 0.7.20151116.0

#### <a name="new"></a>novo

* macOS e as versões do Windows
* Iniciar sessão para ver as contas do Storage – utilizar a sua conta da organização, Account Microsoft, 2FA, etc.
* Armazenamento de desenvolvimento local (utilizar o emulador do storage apenas de Windows)
* Suporte de recursos do Azure Resource Manager e clássico
* Criar e eliminar blobs, filas ou tabelas
* Procurar blobs específicos, filas ou tabelas
* Explorar o conteúdo de contentores de BLOBs
* Ver e navegar através de diretórios
* Carregar, transferir e eliminar os blobs e pastas
* Ver e editar propriedades de BLOBs e metadados
* Gerar chaves SAS
* Gerir e criar políticas de acesso armazenada (SAP)
* Procurar blobs pelo prefixo
* Arraste 'n largar ficheiros para carregar ou transferir

#### <a name="known-issues"></a>Problemas conhecidos

* Ao definir o nível de acesso de público do contentor de blob, o novo valor não for atualizado até voltar a definir o foco no contentor
* Quando abre a caixa de diálogo para definir o nível de acesso público, indica sempre "Sem acesso de público" como a predefinição e não o valor real atual
* Não é possível mudar o nome de blobs transferidos
* Quando ocorre um erro e não é apresentado o erro de estado de entradas de registo de atividade será, por vezes, obter "presa" num em curso
* Por vezes, falha ou fica completamente branco ao tentar carregar ou transferir um grande número de blobs
* Por vezes, cancelar uma operação de cópia não funciona
* Durante a criação de um contentor (tabela/fila/BLOBs), se introduzir um nome inválido e continuar a criar outro sob um tipo de contentor diferente, não é possível definir o foco no tipo de novo
* Não é possível criar uma nova pasta ou mudar o nome de pasta




[2]: https://support.microsoft.com/en-us/help/4021389/storage-explorer-troubleshooting-guide
[3]: vs-azure-tools-storage-manage-with-storage-explorer.md
