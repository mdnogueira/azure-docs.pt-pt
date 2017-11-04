---
title: "Configurar o projeto do Azure através de várias configurações de serviço | Microsoft Docs"
description: "Saiba como configurar um projeto de serviço em nuvem do Azure, alterando os ficheiros servicedefinition. Csdef e serviceconfiguration. Cscfg."
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
ms.date: 11/11/2016
ms.author: kraigb
ms.openlocfilehash: deb69101e855bcad56b9212736c52ace72631f0a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configuring-your-azure-project-using-multiple-service-configurations"></a>Configurar o projeto do Azure através de várias configurações de serviço
Um projeto de serviço em nuvem do Azure inclui dois ficheiros de configuração: servicedefinition. Csdef e serviceconfiguration. Cscfg. Estes ficheiros são compactados com a aplicação do serviço em nuvem do Azure e implementados no Azure.

* O **servicedefinition. Csdef** ficheiro contém os metadados que é exigido pelo ambiente do Azure para os requisitos da sua aplicação de serviço em nuvem, incluindo as funções às quais contém. Este ficheiro também contém as definições de configuração que se aplicam a todas as instâncias. Estas definições de configuração podem ser lidos durante a execução utilizando a API de tempo de execução de alojamento do Azure Service. Não é possível atualizar este ficheiro enquanto o serviço está em execução no Azure.
* O **serviceconfiguration. Cscfg** define valores para as definições de configuração definidas no ficheiro de definição do serviço de ficheiros e especifica o número de instâncias para executar para cada função. Este ficheiro pode ser atualizado enquanto o serviço em nuvem está em execução no Azure.

As ferramentas do Azure para o Microsoft Visual Studio fornecem as páginas de propriedade que pode utilizar para configurar as definições de configuração armazenadas nesses ficheiros. Para aceder as páginas de propriedades, clique duas vezes a referência de função sob o projeto de serviço em nuvem do Azure no Explorador de soluções, ou com o botão direito a referência de função e escolha **propriedades**, conforme mostrado na figura seguinte.

![VS_Solution_Explorer_Roles_Properties](./media/vs-azure-tools-multiple-services-project-configurations/IC784076.png)

Para obter informações sobre os esquemas subjacentes para a definição de serviço e os ficheiros de configuração de serviço, consulte o [esquema referência](https://msdn.microsoft.com/library/azure/dd179398.aspx). Para obter mais informações sobre a configuração do serviço, consulte [como configurar os serviços em nuvem](cloud-services/cloud-services-how-to-configure.md).

## <a name="configuring-role-properties"></a>Configurar as propriedades da função
As páginas de propriedades para uma função da web e uma função de trabalho são semelhantes, embora existam algumas diferenças, indicadas nas secções seguintes.

Do **colocação em cache** página, pode configurar a colocação em cache dos serviços do Azure.

### <a name="configuration-page"></a>Página de configuração
No **configuração** página, pode definir estas propriedades:

**Instâncias**

Definir o **instância** contagem de propriedade para o número de instâncias de serviço deve executar para esta função.

Definir o **tamanho da VM** propriedade **Extra pequena**, **pequeno**, **média**, **grande**, ou **Extra grande**.  Para obter mais informações, consulte [tamanhos para serviços em nuvem](cloud-services/cloud-services-sizes-specs.md).

**Ação de arranque** (Web função apenas)

Defina esta propriedade para especificar que o Visual Studio deve iniciar um browser para os pontos finais HTTP ou pontos finais de HTTPS ou ambos quando iniciar a depuração.

A opção de ponto final HTTPS está disponível apenas se já definiu um ponto final de HTTPS para a sua função. Pode definir um ponto final de HTTPS no **pontos finais** página de propriedades.

Se já tiver adicionado um ponto final de HTTPS, a opção de ponto final HTTPS está ativada por predefinição e o Visual Studio irá iniciar um browser para este ponto final quando iniciar a depuração, além de um browser para o ponto final HTTP. Esta parte do princípio de que ambas as opções de arranque estão ativadas.

**Diagnóstico**

Por predefinição, o diagnóstico está ativado para a função da Web. A conta de armazenamento e de projeto de serviço em nuvem do Azure são definidos para utilizar o emulador de armazenamento local. Quando estiver pronto para implementar no Azure, pode selecionar o botão de construtor (**...** ) para atualizar a conta de armazenamento a utilizar o armazenamento do Azure na nuvem. Pode transferir os dados de diagnóstico para a conta de armazenamento a pedido ou em intervalos agendados automaticamente. Para obter mais informações sobre o diagnóstico do Azure, consulte [ativar diagnósticos no Cloud Services do Azure e máquinas virtuais](cloud-services/cloud-services-dotnet-diagnostics.md).

## <a name="settings-page"></a>Página de definições
No **definições** página, pode adicionar as definições de configuração para o seu serviço. Definições de configuração são pares nome-valor. Código em execução na função de pode ler os valores das definições de configuração em tempo de execução a utilizar classes fornecidas pelo [biblioteca gerida do Azure](http://go.microsoft.com/fwlink?LinkID=171026). Especificamente, o [GetConfigurationSettingValue](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.getconfigurationsettingvalue.aspx) método devolve o valor de uma definição de configuração com o nome no tempo de execução.

### <a name="configuring-a-connection-string-to-a-storage-account"></a>Configurar uma cadeia de ligação para uma conta de armazenamento
Uma cadeia de ligação é uma definição de configuração que fornece informações de ligação e a autenticação para o emulador de armazenamento ou para uma conta de armazenamento do Azure. Sempre que o seu código tem aceder a dados de serviços de armazenamento do Azure – isto é, blob, fila ou os dados da tabela – a partir do código em execução numa função, terá de definir uma cadeia de ligação para essa conta de armazenamento.

Uma cadeia de ligação que aponta para uma conta de armazenamento do Azure tem de utilizar um formato definido. Para obter informações sobre como criar cadeias de ligação, consulte [configurar cadeias de ligação de armazenamento do Azure](storage/common/storage-configure-connection-string.md).

Quando estiver pronto para testar o seu serviço contra os serviços de armazenamento do Azure, ou quando estiver pronto para implementar o seu serviço em nuvem no Azure, pode alterar o valor de todas as cadeias de ligação para apontar para a sua conta do storage do Azure. Selecione (**...** ), selecione **introduza as credenciais da conta de armazenamento**. Introduza as suas informações de conta que inclui o nome da conta e a chave de conta. No **cadeia de ligação de conta de armazenamento** caixa de diálogo, também pode indicar se pretende utilizar predefinição pontos finais de HTTPS (opção predefinida), os pontos finais HTTP predefinidos ou pontos finais personalizados. Pode optar por utilizar os pontos finais personalizados se tiver registado um nome de domínio personalizado para o seu serviço, conforme descrito em [configurar um nome de domínio personalizado para dados de BLOBs numa conta de armazenamento do Azure](storage/blobs/storage-custom-domain-name.md).

> [!IMPORTANT]
> Tem de modificar as cadeias de ligação para apontar para uma conta de armazenamento do Azure antes de implementar o serviço. Conseguir fazê-lo poderá fazer com que a função não iniciar, ou percorra os Estados de inicialização, ocupados e parar.
> 
> 

## <a name="endpoints-page"></a>Página pontos finais
Uma função de trabalho pode ter qualquer número de pontos finais HTTP, HTTPS ou TCP. Pontos finais podem ser pontos finais de entrada, que são disponibilizados aos clientes externos, ou pontos finais internos, que estão disponíveis para outras funções que estão em execução no serviço.

* Para disponibilizar um ponto final de HTTP para clientes externos e browsers da Web, altere o tipo de ponto final de entrada e especifique um nome e um número de porta pública.
* Para disponibilizar um ponto final de HTTPS de clientes externos e browsers da Web, alterar o tipo de ponto final para **entrada**e especifique um nome, um número de porta pública e um nome de certificado de gestão.
  
    Tenha em atenção que antes de especificar um certificado de gestão, tem de definir o certificado no **certificados** página de propriedades.
* Para disponibilizar um ponto final de acesso interno por outras funções no serviço em nuvem, altere o tipo de ponto final interno para e especifique um nome e portas privadas possíveis para este ponto final.

## <a name="local-storage-page"></a>Página de armazenamento local
Pode utilizar o **armazenamento Local** página de propriedades para um ou mais recursos de armazenamento local para uma função de reserva. Um recurso de armazenamento local é um diretório reservado no sistema de ficheiros de máquina virtual do Azure na qual está em execução uma instância de uma função.

## <a name="certificates-page"></a>Página Certificados
No **certificados** página, pode associar certificados a sua função. Os certificados que adicionar podem ser utilizados para configurar os pontos finais de HTTPS no **pontos finais** página de propriedades.

O **certificados** página de propriedades adiciona informações sobre os certificados de configuração do serviço. Tenha em atenção que os certificados não são reunidos com o serviço; tem de carregar os certificados em separado para o Azure através de [portal clássico do Azure](http://go.microsoft.com/fwlink/?LinkID=213885).

Para associar um certificado com a função, forneça um nome para o certificado. Utilize este nome para fazer referência ao certificado quando configurar um ponto final de HTTPS no **pontos finais** página de propriedades. Em seguida, especifique se o arquivo de certificados é **máquina Local** ou **utilizador atual** e o nome do arquivo. Por fim, introduza o thumbprint do certificado. Se o certificado tem o User\Personal atual arquivo (meu), pode introduzir o thumbprint do certificado ao selecionar o certificado a partir de uma lista povoada. Se residir em qualquer outra localização, introduza o valor de thumbprint manualmente.

Quando adiciona um certificado do arquivo de certificados, todos os certificados intermédios são adicionados automaticamente para as definições de configuração para si. Estes certificados intermédios também devem ser carregados para o Azure para configurar corretamente o seu serviço para SSL.

Quaisquer certificados de gestão que associa o seu serviço aplicam-se ao seu serviço apenas quando está a ser executado na nuvem. Quando o serviço está em execução no ambiente de desenvolvimento local, utiliza um certificado padrão que é gerido pelo emulador de computação.

## <a name="configuring-the-azure-cloud-service-project"></a>Configurar o projeto de serviço em nuvem do Azure
Para configurar as definições que se aplicam a um projeto de serviço em nuvem do Azure completo, primeira vez que abrir o menu de atalho para esse nó do projeto e, em seguida, escolha o propriedades para abrir as respetivas páginas de propriedades. A tabela seguinte mostra as páginas de propriedades.

| Página de propriedades | Descrição |
| --- | --- |
| Aplicação |Nesta página, pode apresentar informações sobre a versão das ferramentas do Azure que utiliza este projeto de serviço de nuvem e pode atualizar para a versão atual das ferramentas. |
| Criar eventos |Nesta página, pode definir os eventos de compilação pré e pós-implementação compilação. |
| Desenvolvimento |Nesta página, pode especificar instruções de configuração de compilação e as condições sob as quais são executados quaisquer eventos de compilação pós-implementação. |
| Web |Nesta página, pode configurar as definições relacionadas com o servidor web. |

