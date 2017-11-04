---
title: Como utilizar Hudson com o Blob storage | Microsoft Docs
description: "Descreve como utilizar Hudson Blob Storage do Azure como um repositório de artefactos de compilação."
services: storage
documentationcenter: java
author: seguler
manager: jahogg
editor: tysonn
ms.assetid: 119becdd-72c4-4ade-a439-070233c1e1ac
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 02/28/2017
ms.author: seguler
ms.openlocfilehash: e54bedff5f744004288e132efbed8c3e7981f8a6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="using-azure-storage-with-a-hudson-continuous-integration-solution"></a>Utilizar o Armazenamento do Azure com uma solução Hudson de Integração Contínua
## <a name="overview"></a>Descrição geral
As seguintes informações mostram como utilizar o Blob storage como um repositório de artefactos de compilação criado por uma solução de integração contínua Hudson (CI) ou como uma origem de ficheiros transferíveis para ser utilizado num processo de compilação. Uma das situações onde que seria útil isto é quando estiver a codificação num ambiente de desenvolvimento seja ágil (utilizando Java ou outros idiomas), baseiam-se em execução com base na integração contínua e precisa de um repositório para seus artefactos de compilação, pelo que pode, por exemplo, partilhá-los com outros membros da organização, os seus clientes, ou manter um arquivo.  Outro cenário é quando a tarefa de compilação próprio requer outros ficheiros, por exemplo, as dependências para transferir como parte da compilação de entrada.

Neste tutorial, irá utilizar o plug-in de armazenamento do Azure para CI Hudson disponibilizados pela Microsoft.

## <a name="introduction-to-hudson"></a>Introdução ao Hudson
Hudson permite a integração contínua de um projeto de software, permitindo aos programadores facilmente integrar as alterações de código e tem compilações produzidos automaticamente e frequentemente, deste modo, aumentar a produtividade dos programadores. Compilações são com versão e compilação artefactos podem ser também carregados para vários repositórios. Este artigo irá mostrar como utilizar o Blob storage do Azure como o repositório de artefactos de compilação. Esta operação também irá mostrar como transferir as dependências do Blob storage do Azure.

Obter mais informações sobre Hudson podem ser encontradas em [cumprir Hudson](http://wiki.eclipse.org/Hudson-ci/Meet_Hudson).

## <a name="benefits-of-using-the-blob-service"></a>Vantagens de utilizar o serviço Blob
Vantagens de utilizar o serviço Blob para alojar o seus artefactos de compilação de desenvolvimento seja ágil incluem:

* Elevada disponibilidade dos seus artefactos de compilação e/ou transferíveis dependências.
* Desempenho quando a sua solução de Hudson CI carrega os seus artefactos de compilação.
* Desempenho quando os clientes e os parceiros de transferir os seus artefactos de compilação.
* Controlar através de políticas de acesso de utilizador, com uma opção entre acesso anónimo, acesso de assinatura de acesso partilhado com base em expiração, privada acesso, etc.

## <a name="prerequisites"></a>Pré-requisitos
Irá precisar do seguinte para utilizar o serviço Blob com a sua solução de Hudson CI:

* Uma solução de integração contínua Hudson.
  
    Se não tem atualmente uma solução de Hudson CI, pode executar uma solução de Hudson CI utilizando o seguinte método:
  
  1. Numa máquina preparados para Java, transfira o WAR Hudson de <http://hudson-ci.org/>.
  2. Na linha de comandos que é aberta para a pasta que contém o WAR Hudson, execute o WAR Hudson. Por exemplo, se tiver transferido versão 3.1.2:
     
      `java -jar hudson-3.1.2.war`

  3. No seu browser, abra `http://localhost:8080/`. Esta ação irá abrir o dashboard Hudson.
  4. Após a primeira utilização da Hudson, conclua a configuração inicial no `http://localhost:8080/`.
  5. Depois de concluir a configuração inicial, cancelar a instância em execução do WAR Hudson, inicie o WAR Hudson novamente e volte a abrir o dashboard de Hudson `http://localhost:8080/`, que irá utilizar para instalar e configurar o plug-in de armazenamento do Azure.
     
      Enquanto uma solução de Hudson CI típica teria de ser configurada para ser executado como um serviço, com o war Hudson na linha de comandos serão suficiente para este tutorial.
* Uma conta do Azure. Pode inscrever-se numa conta do Azure em <http://www.azure.com>.
* Uma conta de armazenamento do Azure. Se ainda não tiver uma conta de armazenamento, pode criar um utilizando os passos indicados em [criar uma conta de armazenamento](../common/storage-create-storage-account.md#create-a-storage-account).
* Familiaridade com a solução de Hudson CI é recomendada, mas não é necessária, como o seguinte conteúdo irá utilizar um exemplo básico para mostrar-lhe os passos necessários ao utilizar o serviço Blob como um repositório para Hudson CI criar artefactos.

## <a name="how-to-use-the-blob-service-with-hudson-ci"></a>Como utilizar o serviço Blob com Hudson CI
Para utilizar o serviço Blob com Hudson, terá de instalar o plug-in de armazenamento do Azure, configure o plug-in para utilizar a sua conta de armazenamento e, em seguida, crie uma ação de compilação de pós-implementação que carrega os seus artefactos de compilação à sua conta de armazenamento. Estes passos são descritos nas secções seguintes.

## <a name="how-to-install-the-azure-storage-plugin"></a>Como instalar o plug-in de armazenamento do Azure
1. No dashboard do Hudson, clique em **gerir Hudson**.
2. No **gerir Hudson** página, clique em **gerir plug-ins**.
3. Clique em de **disponível** separador.
4. Clique em **outros**.
5. No **artefactos Uploaders** secção, selecione **Plug-in do armazenamento do Microsoft Azure**.
6. Clique em **Instalar**.
7. Após a instalação estiver concluída, reinicie Hudson.

## <a name="how-to-configure-the-azure-storage-plugin-to-use-your-storage-account"></a>Como configurar o plug-in de armazenamento do Azure para utilizar a sua conta de armazenamento
1. No dashboard do Hudson, clique em **gerir Hudson**.
2. No **gerir Hudson** página, clique em **Configurar sistema**.
3. No **configuração de conta de armazenamento do Microsoft Azure** secção:
   
    a. Introduza o nome de conta do storage, pode obter no [Portal do Azure](https://portal.azure.com).
   
    b. Introduza a chave de conta de armazenamento, também obtainable do [Portal do Azure](https://portal.azure.com).
   
    c. Utilize o valor predefinido para **URL de ponto final de serviço Blob** se estiver a utilizar a nuvem do Azure pública. Se estiver a utilizar uma nuvem do Azure diferente, utilize o ponto final conforme especificado no [Portal do Azure](https://portal.azure.com) para a sua conta de armazenamento.
   
    d. Clique em **validar as credenciais do armazenamento** para validar a sua conta do storage.
   
    e. [Opcional] Se tiver adicionais disponibilizadas contas de armazenamento que pretende que a sua CI Hudson, clique em **adicionar mais contas de armazenamento**.
   
    f. Clique em **guardar** para guardar as definições.

## <a name="how-to-create-a-post-build-action-that-uploads-your-build-artifacts-to-your-storage-account"></a>Como criar uma ação de compilação de pós-implementação que carrega os seus artefactos de compilação à sua conta de armazenamento
Para efeitos de instrução, primeiro vamos precisar de criar uma tarefa que vai criar vários ficheiros e, em seguida, adicionar a ação de compilação de pós-implementação para carregar os ficheiros para a sua conta de armazenamento.

1. No dashboard do Hudson, clique em **nova tarefa**.
2. A tarefa de nome **MyJob**, clique em **criar uma tarefa de estilo livre software**e, em seguida, clique em **OK**.
3. No **criar** secção da configuração da tarefa, clique em **Adicionar passo de compilação** e escolha **comando batch do Windows de executar**.
4. No **comando**, utilize os seguintes comandos:

    ```   
        md text
        cd text
        echo Hello Azure Storage from Hudson > hello.txt
        date /t > date.txt
        time /t >> date.txt
    ```

5. No **ações de pós-compilação de** secção da configuração da tarefa, clique em **carregar artefactos para o armazenamento de Blobs do Microsoft Azure**.
6. Para **nome da conta de armazenamento**, selecione a conta de armazenamento a utilizar.
7. Para **nome do contentor**, especifique o nome do contentor. (O contentor será criado se já existir quando são carregados os artefactos de compilação.) Pode utilizar variáveis de ambiente, por isso, para este exemplo introduza **${JOB_NAME}** como o nome do contentor.
   
    **Sugestão**
   
    Abaixo o **comando** secção onde introduziu um script para **comando batch do Windows de executar** é uma ligação para as variáveis de ambiente reconhecido pelo Hudson. Clique em dessa ligação para obter os nomes de variáveis de ambiente e descrições. Tenha em atenção que as variáveis de ambiente que contêm especial carateres, tais como o **BUILD_URL** variável de ambiente, não são permitidos como um nome de contentor ou o caminho virtual comum.
8. Clique em **tornar o novo contentor público por predefinição** para este exemplo. (Se pretender utilizar um contentor privado, terá de criar uma assinatura de acesso partilhado para permitir o acesso. Que está fora do âmbito deste artigo. Pode saber mais sobre assinaturas de acesso partilhado no [utilizando partilhado acesso assinaturas (SAS)](../storage-dotnet-shared-access-signature-part-1.md).)
9. [Opcional] Clique em **contentor limpa antes de carregar** se pretender que o contentor ser limpo de conteúdo antes de compilação artefactos são carregados (deixe a opção desmarcada se não pretender apagar o conteúdo do contentor).
10. Para **lista de artefactos para carregar**, introduza  **texto /*. txt**.
11. Para **caminho virtual comuns para os artefactos carregados**, introduza **${criar\_ID} / ${criar\_número}**.
12. Clique em **guardar** para guardar as definições.
13. No dashboard do Hudson, clique em **compilar agora** executar **MyJob**. Examine o resultado da consola para o estado. Mensagens de estado de armazenamento do Azure serão incluídas no resultado da consola quando inicia a ação de compilação de pós-implementação carregar os artefactos de compilação.
14. Após a conclusão bem-sucedida da tarefa, pode examinar os artefactos de compilação, abrindo o blob público.
    
    a. Inicie sessão no [Portal do Azure](https://portal.azure.com).
    
    b. Clique em **armazenamento**.
    
    c. Clique no nome de conta de armazenamento que utilizou para Hudson.
    
    d. Clique em **contentores**.
    
    e. Clique em contentor com o nome **myjob**, que é a versão em minúsculas do nome de tarefa que atribuiu quando criou a tarefa de Hudson. Os nomes dos contentores e BLOBs nomes estão em minúsculas (e maiúsculas e minúsculas) no Storage do Azure. Na lista de blobs para o contentor com o nome **myjob** deverá ver **hello.txt** e **date.txt**. Copie o URL para um destes itens e abri-lo no seu browser. Irá ver o ficheiro de texto que foi carregado como um artefacto de compilação.

Apenas uma ação de compilação de pós-implementação que carrega artefactos para o armazenamento de Blobs do Azure pode ser criada por tarefa. Tenha em atenção que a única ação de compilação posterior ao carregar os artefactos para o Blob storage do Azure pode especificar ficheiros diferentes (incluindo os carateres universais) e caminhos de ficheiros dentro do **lista de artefactos para carregar** utilizando um ponto e vírgula como separador. Por exemplo, se criar a sua Hudson produz JAR e ficheiros TXT na sua área de trabalho **criar** pasta e pretender carregar tanto para armazenamento de Blobs do Azure, utilize o seguinte para o **lista de artefactos para carregar** valor: **criar /\*. JAR; compilação /\*. txt**. Também pode utilizar a sintaxe do valor de duplo vírgula para especificar um caminho a utilizar no nome do blob. Por exemplo, se pretender que o v7 obter carregados utilizando **binários** o caminho de blob e os ficheiros TXT para obter carregado utilizando **avisos** no caminho do blob, utilize o seguinte para o **lista de artefactos para carregar** valor: **criar /\*. jar::binaries; compilação /\*. txt::notices**.

## <a name="how-to-create-a-build-step-that-downloads-from-azure-blob-storage"></a>Como criar um passo de compilação que transfere do armazenamento de Blobs do Azure
Os passos seguintes mostram como configurar um passo de compilação para transferir itens a partir do Blob storage do Azure. Isto é útil se pretender incluir itens na sua compilação, por exemplo, V7 manter no Blob storage do Azure.

1. No **criar** secção da configuração da tarefa, clique em **Adicionar passo de compilação** e escolha **transferir a partir do Blob storage do Azure**.
2. Para **nome da conta de armazenamento**, selecione a conta de armazenamento a utilizar.
3. Para **nome do contentor**, especifique o nome do contentor que tenha os blobs que pretende transferir. Pode utilizar variáveis de ambiente.
4. Para **nome do Blob**, especifique o nome do blob. Pode utilizar variáveis de ambiente. Além disso, pode utilizar um asterisco como caráter universal depois de especificar o letter(s) inicial do nome do blob. Por exemplo, **projeto\***  especificar todos os blobs cujos nomes começar a utilizar **projeto**.
5. [Opcional] Para **caminho de transferência**, especifique o caminho na máquina Hudson onde pretende transferir os ficheiros a partir do Blob storage do Azure. Também podem ser utilizadas a variáveis de ambiente. (Se não fornecer um valor para **caminho de transferência**, os ficheiros do Blob storage do Azure serão transferidos para a área de trabalho da tarefa.)

Se tiver itens adicionais que pretende transferir a partir do Blob storage do Azure, pode criar passos adicionais de compilação.

Depois de executar uma compilação, pode verificar o resultado de consola do histórico de compilação ou, observe a localização de transferência, para ver se os blobs destinados foram transferidos com êxito.

## <a name="components-used-by-the-blob-service"></a>Componentes utilizados pelo serviço Blob
O seguinte fornece uma descrição geral dos componentes de serviço Blob.

* **Conta de armazenamento**: todos os acessos ao Storage do Azure é efetuado através de uma conta de armazenamento. Este é o nível mais alto de espaço de nomes para aceder aos blobs. Uma conta pode conter um número ilimitado de contentores, desde que o tamanho total é em 100 TB.
* **Contentor**: um contentor fornece um agrupamento de um conjunto de blobs. Todos os blobs tem de estar num contentor. Uma conta pode conter um número ilimitado de contentores. Um contentor pode armazenar um número ilimitado de blobs.
* **Blob**: um ficheiro de qualquer tipo e tamanho. Existem dois tipos de blobs que podem ser armazenados no Storage do Azure: blobs de blocos e de página. A maioria dos ficheiros são blobs de blocos. Um blob de bloco única pode ser até 200 GB de tamanho. Este tutorial utiliza os blobs de blocos. Os blobs de páginas, outro tipo de blob, podem ser até 1 TB de tamanho e são mais eficiente quando intervalos de bytes num ficheiro são modificados com frequência. Para obter mais informações sobre os blobs, consulte [compreender os Blobs de blocos, os Blobs de acréscimo e Blobs de páginas](http://msdn.microsoft.com/library/azure/ee691964.aspx).
* **Formato de URL**: Blobs são endereçáveis utilizando o seguinte formato de URL:
  
    `http://storageaccount.blob.core.windows.net/container_name/blob_name`
  
    (O formato acima aplica-se a nuvem pública do Azure. Se estiver a utilizar uma nuvem do Azure diferente, utilize o ponto final dentro de [Portal do Azure](https://portal.azure.com) para determinar o ponto final do URL.)
  
    O formato acima, `storageaccount` representa o nome da sua conta do storage `container_name` representa o nome do seu contentor e `blob_name` representa o nome do seu blob, respetivamente. No nome do contentor, pode ter vários caminhos, separados por uma barra,  **/** . O nome do contentor de exemplo neste tutorial foi **MyJob**, e **${criar\_ID} / ${criar\_número}** foi utilizado para o caminho virtual comum, resultando no blob ter um URL com o seguinte formato:
  
    `http://example.blob.core.windows.net/myjob/2014-05-01_11-56-22/1/hello.txt`

## <a name="next-steps"></a>Passos seguintes
* [Cumprir Hudson](http://wiki.eclipse.org/Hudson-ci/Meet_Hudson)
* [Armazenamento do Azure SDK para Java](https://github.com/azure/azure-storage-java)
* [Referência do SDK de cliente de armazenamento do Azure](http://dl.windowsazure.com/storage/javadoc/)
* [API REST dos Serviços do Armazenamento do Azure](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Blogue da Equipa de Armazenamento do Azure](http://blogs.msdn.com/b/windowsazurestorage/)

Para obter mais informações, visite [Azure para programadores Java](/java/azure).