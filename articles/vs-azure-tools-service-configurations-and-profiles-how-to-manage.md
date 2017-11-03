---
title: "Como gerir configurações de serviço e perfis | Microsoft Docs"
description: "Saiba como trabalhar com ficheiros de configuração de perfis e as configurações de serviço | que armazene as definições para os ambientes de implementação e publicar as definições para serviços em nuvem."
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 7da8c551-fb06-4057-b5c7-c77f4b39d803
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 8/11/2017
ms.author: kraigb
ms.openlocfilehash: af1205f8c3e477d123d4835c80a68b3afd6ee5ad
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-manage-service-configurations-and-profiles"></a>Como gerir configurações de serviço e perfis
## <a name="overview"></a>Descrição geral
Quando publica um serviço em nuvem, o Visual Studio armazena informações de configuração em dois tipos de ficheiros de configuração: configurações e perfis de serviço. Configurações de serviço (ficheiros. cscfg) armazenam definições para os ambientes de implementação para um serviço em nuvem do Azure. Azure utiliza estes ficheiros de configuração quando gere seus serviços em nuvem. Por outro lado, o arquivo de perfis (ficheiros .azurePubxml) definições de publicação para serviços em nuvem. Estas definições são um registo de aquilo que escolher quando utilizar o Assistente de publicação e são utilizados localmente pelo Visual Studio. Este tópico explica como trabalhar com os dois tipos de ficheiros de configuração.

## <a name="service-configurations"></a>Configurações de serviço
Pode criar várias configurações de serviço a utilizar para cada um dos seus ambientes de implementação. Por exemplo, poderá criar uma configuração de serviço para o ambiente local que utiliza para executar e testar uma aplicação do Azure e outra configuração do serviço para o seu ambiente de produção.

Pode adicionar, eliminar, mudar o nome e modificar estas configurações de serviço com base nos seus requisitos. Pode gerir estas configurações de serviço a partir do Visual Studio, conforme mostrado na ilustração seguinte.

![Gerir configurações de serviço](./media/vs-azure-tools-service-configurations-and-profiles-how-to-manage/manage-service-config.png)

Pode também abrir o **gerir configurações** caixa de diálogo de páginas de propriedades da função. Para abrir as propriedades para uma função no projeto do Azure, abra o menu de atalho para essa função e, em seguida, escolha **propriedades**. No **definições** separador, expanda o **a configuração do serviço** lista e, em seguida, selecione **gerir** para abrir o **gerir configurações** caixa de diálogo.

### <a name="to-add-a-service-configuration"></a>Para adicionar uma configuração de serviço
1. No Explorador de soluções, abra o menu de atalho para o projeto do Azure e, em seguida, selecione **gerir configurações**.
   
    O **gerir configurações de serviço** é apresentada a caixa de diálogo.
2. Para adicionar uma configuração de serviço, tem de criar uma cópia de uma configuração existente. Para tal, escolha a configuração que pretende copiar a partir da lista de nomes e, em seguida, selecione **criar cópia**.
3. (Opcional) Para dar um nome diferente da configuração do serviço, escolha a nova configuração de serviço na lista de nome e, em seguida, selecione **mudar o nome**. No **nome** texto caixa, escreva o nome que pretende utilizar para esta configuração de serviço e, em seguida, selecione **OK**.
   
    Um novo ficheiro de configuração do serviço, que é denominado ServiceConfiguration. [Nome do novo]. cscfg é adicionada ao projeto no Explorador de soluções do Azure.

### <a name="to-delete-a-service-configuration"></a>Para eliminar uma configuração de serviço
1. No Explorador de soluções, abra o menu de atalho para o projeto do Azure e, em seguida, selecione **gerir configurações**.
   
    O **gerir configurações de serviço** é apresentada a caixa de diálogo.
2. Para eliminar uma configuração de serviço, escolha a configuração que pretende eliminar a partir de **nome** lista e, em seguida, selecione **remover**. É apresentada uma caixa de diálogo Confirmar que pretende eliminar esta configuração.
3. Selecione **Eliminar**.
   
     O ficheiro de configuração de serviço é removido do projeto no Explorador de soluções do Azure.

### <a name="to-rename-a-service-configuration"></a>Para mudar o nome de uma configuração de serviço
1. No Explorador de soluções, abra o menu de atalho para o projeto do Azure e, em seguida, selecione **gerir configurações**.
   
    O **gerir configurações de serviço** é apresentada a caixa de diálogo.
2. Para mudar o nome de uma configuração de serviço, escolha a nova configuração de serviço a partir de **nome** lista e, em seguida, selecione **mudar o nome**. No **nome** texto caixa, escreva o nome que pretende utilizar para esta configuração de serviço e, em seguida, selecione **OK**.
   
    O nome do ficheiro de configuração do serviço é alterado no projeto no Explorador de soluções do Azure.

### <a name="to-change-a-service-configuration"></a>Para alterar uma configuração de serviço
* Se pretender alterar uma configuração de serviço, abra o menu de atalho para a função específica que pretende alterar no projeto do Azure e, em seguida, selecione **propriedades**. Consulte [como: configurar as funções para um serviço em nuvem do Azure com o Visual Studio](https://docs.microsoft.com/azure/vs-azure-tools-configure-roles-for-cloud-service) para obter mais informações.

## <a name="make-different-setting-combinations-by-using-profiles"></a>Certifique-combinações de uma definição diferente através da utilização de perfis
Ao utilizar um perfil, pode automaticamente preencher o **publicar assistente** com diferentes combinações de definições para diferentes fins. Por exemplo, pode ter um perfil de depuração e outra para a versão baseia-se. Nesse caso, o **depurar** perfil teria **IntelliTrace** ativada e o **depurar** configuração selecionada e os seus **versão** perfil teria **IntelliTrace** desativada e o **versão** configuração selecionada. Também pode utilizar perfis de diferentes para implementar um serviço utilizando uma conta de armazenamento diferente.

Quando executar o assistente pela primeira vez, é criado um perfil predefinido. Visual Studio armazena o perfil de um ficheiro que tem uma extensão de .azurePubXml, que é adicionada ao seu projeto do Azure no **perfis** pasta. Se especificar manualmente as diferentes opções ao executar o assistente mais tarde, o ficheiro atualiza automaticamente. Antes de executar o procedimento seguinte, deve já tiver publicado seu serviço em nuvem, pelo menos, uma vez.

### <a name="to-add-a-profile"></a>Para adicionar um perfil
1. Abra o menu de atalho para o projeto do Azure e, em seguida, selecione **publicar**.
2. Junto a **destino perfil** lista, selecione o **Guardar perfil** botão, como a seguinte ilustração mostra. Esta ação cria um perfil para si.
   
    ![Criar um novo perfil](./media/vs-azure-tools-service-configurations-and-profiles-how-to-manage/create-new-profile.png)
3. Depois do perfil ser criado, selecione **< gerir … >** no **destino perfil** lista.
   
    O **gerir perfis** aparece a caixa de diálogo, como a seguinte ilustração mostra.
   
    ![Gerir a caixa de diálogo de perfis](./media/vs-azure-tools-service-configurations-and-profiles-how-to-manage/manage-profiles.png)
4. No **nome** lista, escolha um perfil e, em seguida, selecione **criar cópia**.
5. Escolha o **fechar** botão.
   
    O novo perfil aparece na lista de perfil de destino.
6. No **destino perfil** lista, selecione o perfil que acabou de criar. As definições do Assistente para publicar são preenchidas com as opções do perfil que selecionou.
7. Selecione o **anterior** e **seguinte** botões para apresentar todas as páginas do Assistente Publicar e, em seguida, personalizar as definições para este perfil. Consulte [publicar Assistente da aplicação Azure](http://go.microsoft.com/fwlink/p/?LinkID=623085) para obter informações.
8. Depois de concluir a personalizar as definições, selecione **seguinte** para regressar à página de definições. O perfil é guardado quando publicam o serviço utilizando estas definições ou se selecionar **guardar** junto a lista de perfis.

### <a name="to-rename-or-delete-a-profile"></a>Mudar o nome ou eliminar um perfil
1. Abra o menu de atalho para o projeto do Azure e, em seguida, selecione **publicar**.
2. No **destino perfil** lista, selecione **gerir**.
3. No **gerir perfis** , selecione o perfil que pretende eliminar e, em seguida, selecione **remover**.
4. Na caixa de diálogo de confirmação que aparece, selecione **OK**.
5. Selecione **fechar**.

### <a name="to-change-a-profile"></a>Para alterar um perfil
1. Abra o menu de atalho para o projeto do Azure e, em seguida, selecione **publicar**.
2. No **destino perfil** lista, selecione o perfil que pretende alterar.
3. Selecione o **anterior** e **seguinte** botões para apresentar todas as páginas do Assistente Publicar e, em seguida, altere as definições que pretende. Consulte [publicar Assistente da aplicação Azure](http://go.microsoft.com/fwlink/p/?LinkID=623085) para obter informações.
4. Depois de concluir a alteração das definições, selecione **seguinte** para regressar à **definições** página.
5. (Opcional) selecione **publicar** para publicar o serviço em nuvem, utilizando as novas definições. Se não quiser para publicar o seu serviço em nuvem neste momento e fechar o assistente publicar, o Visual Studio solicita-o se pretender guardar as alterações ao perfil.

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre como configurar outras partes do seu projeto do Azure a partir do Visual Studio, consulte [configurar um projeto do Azure](http://go.microsoft.com/fwlink/p/?LinkID=623075)

