---
title: "Procurar dados não estruturados no armazenamento na nuvem do Azure"
description: "Pesquisa de dados não estruturados, utilizando a pesquisa do Azure."
author: roygara
manager: timlt
services: storage
ms.service: storage
ms.topic: tutorial
ms.date: 10/12/2017
ms.author: rogara
ms.custom: mvc
ms.openlocfilehash: 930b735eb03aea6ce701b694ca527049b4c3f24d
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2017
---
# <a name="search-unstructured-data-in-cloud-storage"></a>Procurar dados não estruturados no armazenamento na cloud

Neste tutorial, irá aprender a procurar dados não estruturados com [da Azure Search](../../search/search-what-is-azure-search.md) utilizando os dados armazenados em blobs do Azure. Dados não estruturados são os dados que não estão organizados de forma predefinida ou não tem um modelo de dados. Um exemplo seria um ficheiro. txt.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um grupo de recursos
> * Criar uma conta de armazenamento
> * Criar um contentor
> * Carregar dados para o contentor
> * Criar um serviço de pesquisa através do portal
> * Utilizar o serviço procura para procurar o contentor

## <a name="download-the-sample"></a>Transferir o exemplo

Um conjunto de dados de exemplo foi preparado para si. **Transferir [clinical trials.zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials.zip)**  e deszipe-lo para a sua própria pasta.

O exemplo consiste em ficheiros de texto obtidos [clinicaltrials.gov](https://clinicaltrials.gov/ct2/results). Pode utilizá-los como ficheiros de texto de exemplo para a procura utilizando o Azure.

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [Portal do Azure](http://portal.azure.com).

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Uma conta do storage fornece uma localização única para armazenar e aceder aos seus objetos de dados do Storage do Azure.

Atualmente, existem dois tipos de contas do storage, **Blob** e **para fins gerais**. Para este tutorial, crie um **para fins gerais** conta de armazenamento.

Se não estiver familiarizado com o processo de criação de um para fins gerais conta de armazenamento, eis como criar um:

1. No menu à esquerda, selecione **contas do Storage**, em seguida, selecione **adicionar**.

2. Introduza um nome exclusivo para a sua conta de armazenamento. 

3. Selecione **Resource Manager** para sua **modelo de implementação** e selecione **fins gerais** do **conta kind** pendente.

4. Selecione **armazenamento localmente redundante (LRS)** do **replicação** pendente.

5. Em **grupo de recursos**, selecione **criar nova** e introduza um nome exclusivo.

6. Selecione uma subscrição adequada.

7. Escolha uma localização e selecionar **criar.**

  ![Pesquisa não estruturada](media/storage-unstructured-search/storagepanes2.png)

## <a name="create-a-container"></a>Criar um contentor

Contentores são semelhantes às pastas e são utilizados para armazenar blobs.

Para este tutorial, pode utilizar um único contentor para armazenar os ficheiros de texto obtidos clinicaltrials.gov.

1. Navegue até à sua conta do storage no portal do Azure.

2. Selecione **procurar blobs** em **serviço Blob.**

3. Adicione um novo contentor.

4. Nome do contentor "dados" e selecione **contentor** para o nível de acesso público.

5. Selecione **OK** para criar o contentor. 

  ![Pesquisa não estruturada](media/storage-unstructured-search/storageactinfo.png)

## <a name="upload-the-example-data"></a>Carregar os dados de exemplo

Agora que tem um contentor, pode carregar os dados de exemplo para a mesma.

1. Selecione o contentor e selecione **carregar**.

2. Selecione o ícone de pasta azul ilustrado junto do campo de ficheiros e navegue para a pasta local em que extraiu os dados de exemplo.

3. Selecione todos os ficheiros extraídos e selecione **aberta**

4. Selecione **carregar** para iniciar o processo de carregamento.

  ![Pesquisa não estruturada](media/storage-unstructured-search/upload.png)

O processo de carregamento pode demorar um pouco.

Quando estiver concluída, navegue para o contentor de dados para confirmar que os ficheiros de texto carregados.

  ![Pesquisa não estruturada](media/storage-unstructured-search/clinicalfolder.png)

## <a name="create-a-search-service"></a>Criar um serviço de pesquisa

A pesquisa do Azure é uma solução de nuvem de pesquisa-como-um-serviço que fornece aos programadores APIs e ferramentas para adicionar uma experiência de pesquisa avançadas sobre os dados em aplicações web, móveis e enterprise.

Se não estiver familiarizado com o processo de criação de um serviço de pesquisa, eis como criar um:

1. Navegue até à sua conta do storage no portal do Azure.

2. Desloque para baixo e clique em **adicionar Azure Search** em **serviço BLOB**.

3. Em **importar dados**, selecione **escolha o seu serviço**.

4. Selecione **clique aqui para criar um novo serviço de pesquisa**.

5. Dentro de **novo serviço de pesquisa** introduza um nome exclusivo para o serviço de pesquisa no **URL** campo.

6. Em **grupo de recursos**, selecione **utilizar existente** e escolha o grupo de recursos que criou anteriormente.

7. Para o **escalão de preço**, selecione o **livres** camada e clique em **selecione**.

8. Selecione **criar** ao criar o serviço de pesquisa.

  ![Pesquisa não estruturada](media/storage-unstructured-search/createsearch2.png)

## <a name="connect-your-search-service-to-your-container"></a>Ligar o serviço de pesquisa para o contentor

Agora que tem um serviço de pesquisa, pode anexá-lo para o armazenamento de Blobs. Esta secção explica o processo de escolher uma origem de dados, criar um índice e criar um indexador.

1. Navegue até à sua conta do storage.

2. Selecione **adicionar a Azure Search** em **serviço BLOB.**

3. Selecione **serviço de pesquisa** dentro **importar dados** e, em seguida, clique no serviço de pesquisa que criou na secção anterior. Esta ação abre **nova origem de dados**.

### <a name="new-data-source"></a>Nova origem de dados

  Uma origem de dados especifica os dados para o índice e como aceder aos dados. Uma origem de dados pode ser utilizada várias vezes, o mesmo serviço de pesquisa.

1. Introduza um nome para a origem de dados. Em **dados, a extrair**, selecione **conteúdo e metadados**. A origem de dados especifica quais as partes do blob são indexadas.
    
    a. Os seus próprios cenários futuros, pode também selecionar **apenas os metadados de armazenamento**. Tornaria seleção se pretender limitar os dados que estão indexados propriedades blob padrão ou propriedades definidas pelo utilizador.
    
    b. Também pode optar por **todos os metadados** obter ambas as propriedades de blob padrão e *todos os* metadados específicos do tipo de conteúdo. 

2. Uma vez que os blobs estiver a utilizar ficheiros de texto, defina **análise modo** para **texto**.
    
    a. Os seus próprios cenários futuras, poderá pretender selecionar [outros modos de análise](../../search/search-howto-indexing-azure-blob-storage.md) consoante o conteúdo dos blobs.

  ![Pesquisa não estruturada](media/storage-unstructured-search/datasources.png)

3. Selecione **contentor de armazenamento** para listar as contas de armazenamento disponível.

4. Selecione a sua conta de armazenamento, em seguida, selecione o contentor que criou anteriormente.

5. Clique em **selecione** para regressar à **nova origem de dados** e selecione **OK** para continuar.

  ![Pesquisa não estruturada](media/storage-unstructured-search/datacontainer.png)

### <a name="configure-the-index"></a>Configurar o índice

  Um índice é uma coleção de campos da sua origem de dados que pode ser procurado. O índice é a forma como o serviço de pesquisa sabe que forma os dados devem ser pesquisados.

1. No **importar dados** selecione **personalizar o índice de destino**.
 
2. Introduza um nome para o índice no **o nome do índice** campo.

3. Selecione o **recuperável** caixa de verificação do atributo em **metadata_storage_name**.

  ![Pesquisa não estruturada](media/storage-unstructured-search/valuestoselect.png)

4. Clique em **OK**, que aparece **criar um indexador**.

Os parâmetros do seu índice e os atributos, dar os parâmetros são importantes. Especificam os parâmetros *que* dados para armazenar, especifique os atributos *como* para armazenar dados.

O **o nome do campo** coluna contém os parâmetros. A tabela seguinte fornece uma listagem dos atributos disponíveis e as respetivas descrições.

### <a name="field-attributes"></a>Atributos do campo
| Atributo | Descrição |
| --- | --- |
| *Chave* |Uma cadeia que fornece o ID exclusivo de cada documento, utilizada para a pesquisa de documento. Todos os índices devem ter uma chave. Apenas um campo pode ser uma chave e o tipo deve ser definido para Edm.String. |
| *Recuperável* |Especifica se um campo pode ser devolvido num resultado da pesquisa. |
| *Filtrável* |Permite que o campo seja utilizado nas consultas de filtro.  |
| *Ordenável* |Permite a uma consulta ordenar os resultados da pesquisa através deste campo. |
| *Facetável* |Permite que um campo a ser utilizada numa estrutura de navegação por facetas para o utilizador auto-direcionada filtragem. Geralmente os campos com valores repetitivos que pode utilizar para agrupar vários documentos (por exemplo, vários documentos que se inserem numa única marca ou categoria de serviço) funcionam melhor como facetas. |
| *Pesquisável* |Marca o campo como pesquisável no texto completo. |


### <a name="create-an-indexer"></a>Criar um indexador
    
  Um indexador liga uma origem de dados com um índice de pesquisa e fornece uma agenda para a reindexar os seus dados.

1. Introduza um nome no **nome** campo e selecione **OK**.

  ![Pesquisa não estruturada](media/storage-unstructured-search/exindexer.png)

2. São recuperados para **importar dados**, selecione **OK** para concluir o processo de ligação.

Agora já ligado com êxito o blob para o serviço de pesquisa. Demora alguns minutos para que o portal mostrar que o índice é preenchido. No entanto, o serviço de pesquisa começa imediatamente a indexação para que possa começar a pesquisa de imediato.

## <a name="search-your-text-files"></a>Os ficheiros de texto de pesquisa

Para procurar os ficheiros, abra o Explorador de pesquisa no interior de índice do seu serviço de pesquisa recentemente criado.

Os passos seguintes mostram-lhe onde encontrar o Explorador de pesquisa e fornece-lhe algumas consultas de exemplo:

1. Navegue para todos os recursos e localizar o serviço de pesquisa recentemente criado.

  ![Pesquisa não estruturada](media/storage-unstructured-search/exampleurl.png)

3. Selecione o seu índice para abri-lo. 

  ![Pesquisa não estruturada](media/storage-unstructured-search/overview.png)

4. Selecione **Explorador de pesquisa** para abrir o Explorador de pesquisa, onde pode fazer consultas em direto nos seus dados.

  ![Pesquisa não estruturada](media/storage-unstructured-search/indexespane.png)

5. Selecione **pesquisa** enquanto o campo de cadeia de consulta está vazio. Devolve uma consulta vazia *todos os* os dados da sua blobs.

  ![Pesquisa não estruturada](media/storage-unstructured-search/emptySearch.png)

### <a name="full-text-search"></a>Pesquisa em texto completo 

Introduza "Myopia" o **cadeia de consulta** campo e selecione **pesquisa**. Iniciar uma pesquisa de conteúdos dos ficheiros e devolver um subconjunto dos mesmos, que contém a palavra "Myopia."

  ![Pesquisa não estruturada](media/storage-unstructured-search/secondMyopia.png) 

### <a name="system-properties-search"></a>Pesquisa de propriedades do sistema

Também pode criar consultas que procurar por propriedades do sistema utilizando o `$select` parâmetro. Introduza `$select=metadata_storage_name` para a cadeia de consulta e prima introduzir, devolver apenas esse campo específico.
    
A cadeia de consulta está a modificar diretamente o URL, pelo que não são permitidos espaços. Para procurar vários campos, utilize uma vírgula, tais como:`$select=metadata_storage_name,metadata_storage_path`
    
O `$select` parâmetro só pode ser utilizado com campos que estão marcados recuperável quando definir o seu índice.

  ![Pesquisa não estruturada](media/storage-unstructured-search/metadatasearchunstructured.png) 

Já concluiu este tutorial e têm um conjunto de dados não estruturados pesquisável.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu sobre pesquisa de dados não estruturados com a Azure Search, tais como:

> [!div class="checklist"]
> * Criar um grupo de recursos
> * Criar uma conta de armazenamento
> * Criar um contentor
> * Carregar dados para o contentor
> * Criar um serviço de pesquisa
> * Utilizar o serviço de pesquisa para procurar o contentor

Siga esta ligação para obter mais informações sobre indexar documentos de pesquisa do Azure.

> [!div class="nextstepaction"]
> [Indexar documentos no Blob Storage do Azure com a pesquisa do Azure](../../search/search-howto-indexing-azure-blob-storage.md)