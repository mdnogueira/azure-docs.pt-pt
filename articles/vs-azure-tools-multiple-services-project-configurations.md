---
title: "Configurar o projeto do Azure através de várias configurações de serviço | Microsoft Docs"
description: "Saiba como configurar um projeto de serviço em nuvem do Azure, alterando os ficheiros servicedefinition. Csdef, ServiceConfiguration.Local.cscfg e Serviceconfiguration."
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: a4fb79ed-384f-4183-9f74-5cac257206b9
ms.service: multiple
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/11/2017
ms.author: kraigb
ms.openlocfilehash: a6f9b300cd832c5f9615f70ee297e3c5ad728e44
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="configuring-your-azure-project-in-visual-studio-to-use-multiple-service-configurations"></a>Configurar o projeto do Azure no Visual Studio para utilizar várias configurações de serviço

Um projeto de serviço em nuvem do Azure no Visual Studio inclui três ficheiros de configuração: `ServiceDefinition.csdef`, `ServiceConfiguration.Local.cscfg`, e `ServiceConfiguration.Cloud.cscfg`:

- `ServiceDefinition.csdef`é implementado no Azure para descrever os requisitos de serviço em nuvem e as respetivas funções e para fornecer definições que se aplicam a todas as instâncias. As definições podem ser lidos durante a execução utilizando a API de tempo de execução de alojamento do Azure Service. Este ficheiro pode ser atualizado no Azure apenas quando o serviço em nuvem está parado.
- `ServiceConfiguration.Local.cscfg`e `ServiceConfiguration.Cloud.cscfg` fornecer valores para as definições na definição do ficheiro e especifique o número de instâncias para executar para cada função. O ficheiro "Local" contém valores utilizados na depuração local; o ficheiro "Nuvem" é implementado no Azure como `ServiceConfiguration.cscfg` e fornece as definições para o ambiente de servidor. Este ficheiro pode ser atualizado enquanto o serviço em nuvem está em execução no Azure.

Definições de configuração são geridas e modificadas no Visual Studio com páginas de propriedades para a função aplicável (a função com o botão direito e selecione **propriedades**, ou faça duplo clique a função). As alterações podem ser confinadas para qualquer configuração é escolhida no **a configuração do serviço** pendente. As propriedades de funções web e de trabalho são semelhantes, exceto onde descritos nas secções seguintes.

![VS_Solution_Explorer_Roles_Properties](./media/vs-azure-tools-multiple-services-project-configurations/IC784076.png)

Para obter informações sobre os esquemas subjacentes para a definição de serviço e os ficheiros de configuração de serviço, consulte o [. csdef esquema XML](cloud-services/schema-csdef-file.md) e [. cscfg esquema XML](cloud-services/schema-cscfg-file.md) artigos. Para obter mais informações sobre a configuração do serviço, consulte [como configurar os serviços em nuvem](cloud-services/cloud-services-how-to-configure-portal.md).


## <a name="configuration-page"></a>Página de configuração

### <a name="service-configuration"></a>Configuração do serviço

Seleciona que `ServiceConfiguration.*.cscfg` ficheiro é afetado por alterações. Por predefinição, são variantes Local e na nuvem, e pode utilizar o **gerir...**  comandos para copiar, mudar o nome e remover ficheiros de configuração. Estes ficheiros são adicionados ao seu projeto de serviço de nuvem e são apresentados no **Explorador de soluções**. No entanto, mudar o nome ou remover as configurações pode ser feita apenas a partir deste controlo.

### <a name="instances"></a>Instâncias

Definir o **instância** contagem de propriedade para o número de instâncias de serviço deve executar para esta função.

Definir o **tamanho da VM** propriedade **Extra pequena**, **pequeno**, **média**, **grande**, ou **Extra grande**.  Para obter mais informações, consulte [tamanhos para serviços em nuvem](cloud-services/cloud-services-sizes-specs.md).

### <a name="startup-action-web-role-only"></a>Ação de arranque (apenas função da Web)

Defina esta propriedade para especificar que o Visual Studio deve iniciar um browser para os pontos finais HTTP ou pontos finais de HTTPS ou ambos quando iniciar a depuração.

O **ponto final de HTTPS** opção só está disponível se já definiu um ponto final de HTTPS para a sua função. Pode definir um ponto final de HTTPS no **pontos finais** página de propriedades.

Se já tiver adicionado um ponto final de HTTPS, a opção de ponto final HTTPS está ativada por predefinição e o Visual Studio inicia um browser para este ponto final quando iniciar depuração, além de um browser para o ponto final HTTP, partindo do princípio de ambas as opções de arranque ativadas.

### <a name="diagnostics"></a>Diagnóstico

Diagnóstico é ativado por predefinição, para a função da Web. A conta de armazenamento e de projeto de serviço em nuvem do Azure são definidos para utilizar o emulador de armazenamento local. Quando estiver pronto para implementar no Azure, pode selecionar o botão de construtor (**...** ) para utilizar em vez disso, o storage do Azure. Pode transferir os dados de diagnóstico para a conta de armazenamento a pedido ou em intervalos agendados automaticamente. Para obter mais informações sobre o diagnóstico do Azure, consulte [ativar diagnósticos no Cloud Services do Azure e máquinas virtuais](cloud-services/cloud-services-dotnet-diagnostics.md).

## <a name="settings-page"></a>Página de definições

No **definições** página, pode adicionar as definições para uma configuração como pares nome-valor. Código em execução na função de pode ler os valores das definições de configuração em tempo de execução a utilizar classes fornecidas pelo [biblioteca gerida do Azure](http://go.microsoft.com/fwlink?LinkID=171026), especificamente, o [GetConfigurationSettingValue](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.getconfigurationsettingvalue.aspx) método.

### <a name="configuring-a-connection-string-for-a-storage-account"></a>Configurar uma cadeia de ligação para uma conta de armazenamento

Uma cadeia de ligação é uma definição que fornece informações de ligação e a autenticação para o emulador de armazenamento ou para uma conta de armazenamento do Azure. Sempre que o código numa função de aceder ao armazenamento do Azure (blobs, filas ou tabelas), necessita de uma cadeia de ligação.

> [!Note]
> Uma cadeia de ligação para a conta de armazenamento do Azure tem de utilizar um formato definido (consulte [configurar cadeias de ligação de armazenamento do Azure](storage/common/storage-configure-connection-string.md)).

Pode definir a cadeia de ligação para utilizar o armazenamento local conforme necessário, em seguida, defina a uma conta de armazenamento do Azure, ao implementar a aplicação de serviço em nuvem. Falha ao definir a cadeia de ligação corretamente pode fazer com que a sua função não iniciar, ou percorra os Estados de inicialização, ocupados e parar.

Para criar uma cadeia de ligação, selecione **Adicionar definição** e defina **tipo** para "Cadeia de ligação".

Novo ou existente para cadeias de ligação, selecione **...** * à direita do **valor** campo para abrir o **criar cadeia de ligação de armazenamento** diálogo:

1. Em **estabelecer ligação utilizando**, escolha o **sua subscrição** opção para selecionar uma conta de armazenamento de uma subscrição. Visual Studio, em seguida, obtém as credenciais da conta de armazenamento automaticamente a partir de `.publishsettings` ficheiro.
1. Selecionar **introduzir manualmente as credenciais** permite-lhe especificar o nome da conta e chave diretamente utilizando informações a partir do portal do Azure. Copiar a chave de conta: uma. Navegue para a conta de armazenamento do Azure portal e selecione **gerir chaves**.
    2. No **gerir chaves de acesso** página, selecione o texto da chave de acesso primária e prima Ctrl + C para copiar.
1. Selecione uma das opções de ligação. **Especifique os pontos finais personalizados** pede-lhe especificar URLs específicos para blobs, tabelas e coloca em fila. Os pontos finais personalizados permitem-lhe utilizar [domínios personalizados](storage/blobs/storage-custom-domain-name.md) e para controlar o acesso mais exatamente. Consulte [configurar cadeias de ligação de armazenamento do Azure](./storage/common/storage-configure-connection-string.md).
1. Selecione **OK**, em seguida, **ficheiro > guardar** para atualizar a configuração com a nova cadeia de ligação.

Novamente, ao publicar a aplicação no Azure, escolha a configuração do serviço que contém a conta de armazenamento do Azure para a cadeia de ligação. Depois da aplicação for publicada, certifique-se de que a aplicação funciona conforme esperado nos serviços de armazenamento do Azure.

Para obter mais informações sobre como atualizar configurações de serviço, consulte a secção [gerir cadeias de ligação para as contas do storage](vs-azure-tools-configure-roles-for-cloud-service.md#manage-connection-strings-for-storage-accounts).

## <a name="endpoints-page"></a>Página pontos finais

Uma função da web, normalmente, tem um único ponto final HTTP na porta 80. Uma função de trabalho, por outro lado, pode ter qualquer número de pontos finais HTTP, HTTPS ou TCP. Pontos finais podem ser pontos finais de entrada, que são disponibilizados aos clientes externos, ou pontos finais internos, que estão disponíveis para outras funções que estão em execução no serviço.

- Para disponibilizar um ponto final de HTTP para clientes externos e browsers da Web, altere o tipo de ponto final de entrada e especifique um nome e um número de porta pública.
- Para disponibilizar um ponto final de HTTPS de clientes externos e browsers da Web, alterar o tipo de ponto final para **entrada**e especifique um nome, um número de porta pública e um nome de certificado de gestão. Também tem de definir o certificado no **certificados** página de propriedades antes de poder especificar um certificado de gestão. 
- Para disponibilizar um ponto final de acesso interno por outras funções no serviço em nuvem, altere o tipo de ponto final interno para e especifique um nome e portas privadas possíveis para este ponto final.

## <a name="local-storage-page"></a>Página de armazenamento local

Pode utilizar o **armazenamento Local** página de propriedades para um ou mais recursos de armazenamento local para uma função de reserva. Um recurso de armazenamento local é um diretório reservado no sistema de ficheiros de máquina virtual do Azure na qual está em execução uma instância de uma função.

## <a name="certificates-page"></a>Página Certificados

O **certificados** página de propriedades adiciona informações sobre os certificados de configuração do serviço. Tenha em atenção que os certificados não são reunidos com o serviço; tem de carregar os certificados em separado para o Azure através de [portal do Azure](http://portal.azure.com).

Adicionar um certificado aqui adiciona informações sobre os certificados à configuração do serviço. Certificados não são reunidos com o serviço; tem de carregar os certificados em separado através do portal do Azure.

Para associar um certificado com a função, forneça um nome para o certificado. Utilize este nome para fazer referência ao certificado quando configurar um ponto final de HTTPS no **pontos finais** página. Em seguida, especifique se o arquivo de certificados é **máquina Local** ou **utilizador atual** e o nome do arquivo. Por fim, introduza o thumbprint do certificado. Se o certificado tem o User\Personal atual arquivo (meu), pode introduzir o thumbprint do certificado ao selecionar o certificado a partir de uma lista povoada. Se residir em qualquer outra localização, introduza o valor de thumbprint manualmente.

Quando adiciona um certificado do arquivo de certificados, todos os certificados intermédios são adicionados automaticamente para as definições de configuração para si. Além disso, estes certificados intermédios tem de ser carregados para o Azure para configurar corretamente o seu serviço para SSL.

Quaisquer certificados de gestão que associa o seu serviço aplicam-se ao seu serviço apenas quando está a ser executado na nuvem. Quando o serviço está em execução no ambiente de desenvolvimento local, utiliza um certificado padrão que é gerido pelo emulador de computação.
