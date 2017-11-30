---
title: "Orientações para o desenvolvimento das funções do Azure | Microsoft Docs"
description: "Saiba os conceitos de funções do Azure e técnicas de que precisa para desenvolver as funções no Azure, em todos os enlaces e linguagens de programação."
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: 
tags: 
keywords: "funções de guia, do azure para programadores, funções, processamento, webhooks, computação dinâmica, arquitetura sem servidor de eventos"
ms.assetid: d8efe41a-bef8-4167-ba97-f3e016fcd39e
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/12/2017
ms.author: tdykstra
ms.openlocfilehash: 80996c8bc6e40665201057ed185700ddaeea170a
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2017
---
# <a name="azure-functions-developers-guide"></a>Guia para programadores as funções do Azure
Nas funções do Azure, funções específicas partilham alguns conceitos técnicos núcleos e componentes, independentemente do idioma ou enlace que utiliza. Antes de ir para detalhes específicos de um determinado idioma ou enlace de aprendizagem, lembre-se de que leia esta descrição geral que se aplica a todos eles.

Este artigo pressupõe que já leu o [descrição geral das funções do Azure](functions-overview.md) e estiver familiarizado com [conceitos do SDK de WebJobs como acionadores, enlaces e o tempo de execução JobHost](https://github.com/Azure/azure-webjobs-sdk/wiki). As funções do Azure baseia-se sobre o SDK de WebJobs. 

## <a name="function-code"></a>Código de função
A *função* é o conceito primário nas funções do Azure. Escrever código para uma função num idioma à sua escolha e guardar os ficheiros de código e a configuração na mesma pasta. A configuração é denominada `function.json`, que contém dados de configuração JSON. São suportados vários idiomas e cada um deles tem uma experiência ligeiramente diferente otimizada para funcionar melhor para esse idioma. 

O ficheiro de function.json define os enlaces de função e outras definições de configuração. O tempo de execução utiliza este ficheiro para determinar os eventos a monitorizar e como transmitir dados para e devolver dados de execução de função. Segue-se um exemplo de ficheiro function.json.

```json
{
    "disabled":false,
    "bindings":[
        // ... bindings here
        {
            "type": "bindingType",
            "direction": "in",
            "name": "myParamName",
            // ... more depending on binding
        }
    ]
}
```

Definir o `disabled` propriedade `true` para impedir que a função a ser executado.

O `bindings` propriedade é onde configurou os acionadores e enlaces. Cada enlace tem algumas definições comuns e algumas definições que são específicas para um determinado tipo de enlace. Cada enlace requer as seguintes definições:

| Propriedade | Tipos de valores / | Comentários |
| --- | --- | --- |
| `type` |Cadeia |Tipo de enlace. Por exemplo, `queueTrigger`. |
| `direction` |'no', 'out' |Indica se o enlace é para receber dados para a função ou o envio de dados da função. |
| `name` |Cadeia |O nome que é utilizado para os dados vinculados na função. Para c#, este é um nome de argumento; para JavaScript, é a chave de uma lista de chave/valor. |

## <a name="function-app"></a>Function App
Uma aplicação de função é composta por uma ou mais funções individuais que são geridas em conjunto pelo App Service do Azure. Todas as funções de uma aplicação de função partilham o mesmo plano de preços, a implementação contínua e a versão de tempo de execução. As funções de escritas em várias linguagens podem partilhar a mesma aplicação de função. Considere uma aplicação de função como uma forma de organizar e coletivamente gerir as suas funções. 

## <a name="runtime-script-host-and-web-host"></a>Tempo de execução (anfitrião de script e anfitrião web)
O tempo de execução ou o anfitrião do script, é o anfitrião subjacente do SDK de WebJobs que escuta de eventos, reúne envia dados e, em última análise executa o código. 

Para facilitar a acionadores HTTP, há também um anfitrião web que foi concebido para manter-se à frente o anfitrião do script em cenários de produção. Ter dois anfitriões ajuda a isolar o anfitrião do script de início fim tráfego gerido através do anfitrião web.

## <a name="folder-structure"></a>Estrutura de pastas
[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Quando a definição de cópia de segurança um projeto de implementação de funções para uma aplicação de função no App Service do Azure, pode processar esta estrutura de pastas como o código de site. Pode utilizar ferramentas existentes, como de integração contínua e a implementação ou scripts de implementação personalizada para fazê-lo implementar a instalação do pacote de tempo ou transpilation de código.

> [!NOTE]
> Certifique-se implementar o `host.json` de ficheiros e pastas de funcionar diretamente para o `wwwroot` pasta. Não inclua o `wwwroot` pasta das implementações. Caso contrário, pode ficar com `wwwroot\wwwroot` pastas. 
> 
> 

## <a id="fileupdate"></a>Como atualizar os ficheiros de aplicação de função
O editor de função incorporado no portal do Azure permite-lhe atualizar o *function.json* ficheiro e o ficheiro de código para uma função. Carregar ou atualizar outros ficheiros, tais como *Package. JSON* ou *project.json* ou dependências, tem de utilizar outros métodos de implementação.

Aplicações de função são criadas no App Service, por isso, todas as [as opções de implementação disponíveis para aplicações web padrão](../app-service/app-service-deploy-local-git.md) também estão disponíveis para aplicações de função. Seguem-se alguns métodos que pode utilizar para carregar ou atualizar os ficheiros de aplicação de função. 

#### <a name="to-use-app-service-editor"></a>Para utilizar o Editor de serviço de aplicações
1. No portal das funções do Azure, clique em **funcionalidades da plataforma**.
2. No **ferramentas de desenvolvimento** secção, clique em **Editor do serviço de aplicações**.   
   Depois de o carregamento do Editor de serviço de aplicações, verá o *host.json* função de ficheiros e pastas sob *wwwroot*. 
5. Abrir ficheiros para editá-los, ou arraste e largue a partir do seu computador de desenvolvimento para carregar ficheiros.

#### <a name="to-use-the-function-apps-scm-kudu-endpoint"></a>Para utilizar o ponto final SCM (Kudu) da aplicação de função
1. Navegue para: `https://<function_app_name>.scm.azurewebsites.net`.
2. Clique em **consola de depuração > CMD**.
3. Navegue para `D:\home\site\wwwroot\` atualizar *host.json* ou `D:\home\site\wwwroot\<function_name>` para atualizar os ficheiros de uma função.
4. Arrastar e largar um ficheiro que pretende carregar para a pasta adequada na grelha de ficheiro. Existem duas áreas na grelha de ficheiro onde pode colocar um ficheiro. Para *. zip* ficheiros, é apresentada uma caixa com a etiqueta "Arraste aqui para carregar e deszipe." Para outros tipos de ficheiro, remova na grelha de ficheiro, mas fora caixa "deszipe".

<!--NOTE: I've removed documentation on FTP, because it does not sync triggers on the consumption plan --glenga -->

#### <a name="to-use-continuous-deployment"></a>Para utilizar a implementação contínua
Siga as instruções no tópico [a implementação contínua para as funções do Azure](functions-continuous-deployment.md).

## <a name="parallel-execution"></a>Execução paralela
Quando vários eventos acionadora ocorrem mais rapidamente do que um tempo de execução de função single-threaded pode processá-los, o tempo de execução pode invocar a função várias vezes em paralelo.  Se estiver a utilizar uma aplicação de função a [consumo plano de alojamento](functions-scale.md#how-the-consumption-plan-works), a aplicação de função pode aumentar horizontalmente automaticamente.  Cada instância da aplicação de função, se a aplicação é executada no consumo de um regular ou plano de alojamento [plano de alojamento do serviço de aplicações](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md), pode processar invocações de função em simultâneo em paralelo utilizando vários threads.  O número máximo de invocações de função em simultâneo em cada instância da aplicação de função varia consoante o tipo de accionador que está a ser utilizado, bem como os recursos utilizados por outras funções dentro da aplicação de função.

## <a name="functions-runtime-versioning"></a>Controlo de versões de tempo de execução de funções

Pode configurar a versão do tempo de execução de funções utilizando o `FUNCTIONS_EXTENSION_VERSION` definição de aplicação. Por exemplo, o valor "~ 1" indica que a sua aplicação de função utilizará 1 como a versão principal. Aplicações de função são atualizadas para cada versão secundária nova à medida que são lançadas. Para obter mais informações, incluindo como ver a versão exata da sua aplicação de função, consulte [como destino a versões de tempo de execução das funções do Azure](functions-versions.md).

## <a name="repositories"></a>Repositórios
O código para as funções do Azure é open source e armazenados no repositórios do GitHub:

* [Tempo de execução de funções do Azure](https://github.com/Azure/azure-webjobs-sdk-script/)
* [Portal de funções do Azure](https://github.com/projectkudu/AzureFunctionsPortal)
* [Modelos de funções do Azure](https://github.com/Azure/azure-webjobs-sdk-templates/)
* [SDK de WebJobs do Azure](https://github.com/Azure/azure-webjobs-sdk/)
* [Extensões do SDK de WebJobs do Azure](https://github.com/Azure/azure-webjobs-sdk-extensions/)

## <a name="bindings"></a>Enlaces
Eis uma tabela de todos os enlaces suportados.

[!INCLUDE [dynamic compute](../../includes/functions-bindings.md)]

## <a name="reporting-issues"></a>Relatórios de problemas
[!INCLUDE [Reporting Issues](../../includes/functions-reporting-issues.md)]

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações, consulte os seguintes recursos:

* [Best Practices for Azure Functions (Melhores Práticas para as Funções do Azure)](functions-best-practices.md)
* [Azure funções c# de referência para programadores](functions-reference-csharp.md)
* [Azure funções F # de referência para programadores](functions-reference-fsharp.md)
* [Referência de programador NodeJS de funções do Azure](functions-reference-node.md)
* [Acionadores de funções do Azure e os enlaces](functions-triggers-bindings.md)
* [As funções do Azure: O Journey](https://blogs.msdn.microsoft.com/appserviceteam/2016/04/27/azure-functions-the-journey/) no blogue de equipa do App Service do Azure. Um histórico de como foi desenvolvida das funções do Azure.

