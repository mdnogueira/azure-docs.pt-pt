---
title: "Recuperação de desastre do implementar utilizando a cópia de segurança e restaurar na API Management do Azure | Microsoft Docs"
description: "Saiba como utilizar a cópia de segurança e restaurar para efetuar a recuperação após desastre na API Management do Azure."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 6f10be3c-f796-4a6c-bacd-7931b6aa82af
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: apimpm
ms.openlocfilehash: 51ec88ae2a4f10b68c7d74324c3a3a25d2893891
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2017
---
# <a name="how-to-implement-disaster-recovery-using-service-backup-and-restore-in-azure-api-management"></a>Como implementar a recuperação após desastre com serviço cópia de segurança e restaurar na API Management do Azure
Escolhendo publicar e gerir as suas APIs através de API Management do Azure são tirar partido das muitas tolerância a falhas e as capacidades de infraestrutura que caso contrário, teria de conceber, implementar e gerir. A plataforma do Azure atenua grande fração de potenciais falhas na fração do custo.

Para recuperar de disponibilidade problemas que afetem a região onde a API Management service está alojado, estará preparados para reconstitute o seu serviço numa região diferente em qualquer altura. Dependendo dos seus objetivos de disponibilidade e o objetivo de tempo de recuperação poderá de reserva um serviço de cópia de segurança num ou mais regiões e tentar manter a respetiva configuração e o conteúdo em sincronização com o serviço do Active Directory. A cópia de segurança do serviço e a funcionalidade de restauro fornece o bloco modular necessário para implementar a sua estratégia de recuperação após desastre.

Este guia mostra como autenticar os pedidos do Azure Resource Manager e como cópia de segurança e restaurar as instâncias de serviço de API Management.

> [!NOTE]
> O processo de cópia de segurança e restaurar uma instância de serviço de API Management para recuperação após desastre também pode ser utilizado para replicar as instâncias de serviço de API Management para cenários como o teste.
>
> Tenha em atenção que cada cópia de segurança expira após 30 dias. Se tentar restaurar uma cópia de segurança depois do período de expiração de 30 dias expirou, o restauro falhará com uma `Cannot restore: backup expired` mensagem.
>
>

## <a name="authenticating-azure-resource-manager-requests"></a>Os pedidos de autenticação do Azure Resource Manager
> [!IMPORTANT]
> A API REST para cópia de segurança e restauro utiliza o Azure Resource Manager e tem um mecanismo de autenticação diferentes que as APIs REST para gerir as entidades de API Management. Os passos nesta secção descrevem como autenticar os pedidos do Azure Resource Manager. Para obter mais informações, consulte [pedidos de autenticação do Azure Resource Manager](http://msdn.microsoft.com/library/azure/dn790557.aspx).
>
>

Todas as tarefas que fazer sobre recursos geridos com o Azure Resource Manager têm de ser autenticadas no Azure Active Directory, utilizando os seguintes passos.

* Adicione uma aplicação para o inquilino do Azure Active Directory.
* Definir as permissões para a aplicação que adicionou.
* Obter o token para autenticar pedidos para o Azure Resource Manager.

O primeiro passo é criar uma aplicação do Azure Active Directory. Inicie sessão no [Portal clássico do Azure](http://manage.windowsazure.com/) com a subscrição que contém o serviço de gestão de API de instância e navegue para o **aplicações** separador para a predefinição do Azure Active Directory.

> [!NOTE]
> Se o diretório do Azure Active Directory predefinido não está visível à sua conta, contacte o administrador da subscrição do Azure para conceder as permissões necessárias para a sua conta.

![Criar aplicações do Azure Active Directory][api-management-add-aad-application]

Clique em **adicionar**, **adicionar uma aplicação que a minha organização está a desenvolver**e escolha **aplicação cliente nativa**. Introduza um nome descritivo e clique na seta seguinte. Introduza um URL de marcador de posição como `http://resources` para o **URI de redirecionamento**, uma vez que é um campo obrigatório, mas não será utilizado o valor mais tarde. Clique na caixa de verificação para guardar a aplicação.

Assim que a aplicação é guardada, clique em **configurar**, desloque para baixo até o **permissões para outras aplicações** secção e clique em **Adicionar aplicação**.

![Adicionar permissões][api-management-aad-permissions-add]

Selecione **Windows** **API de gestão de serviços do Azure** e clique na caixa de verificação para adicionar a aplicação.

![Adicionar permissões][api-management-aad-permissions]

Clique em **permissões delegadas** junto recentemente adicionado **Windows** **API de gestão de serviços do Azure** aplicações, marque a caixa de **serviço de acesso do Azure Gestão (pré-visualização)**e clique em **guardar**.

![Adicionar permissões][api-management-aad-delegated-permissions]

Antes de invocar as APIs que gerar a cópia de segurança e restaurá-lo, é necessário obter um token. O exemplo seguinte utiliza o [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) pacote nuget ao obter o token.

```c#
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System;

namespace GetTokenResourceManagerRequests
{
    class Program
    {
        static void Main(string[] args)
        {
            var authenticationContext = new AuthenticationContext("https://login.microsoftonline.com/{tenant id}");
            var result = authenticationContext.AcquireToken("https://management.azure.com/", {application id}, new Uri({redirect uri});

            if (result == null) {
                throw new InvalidOperationException("Failed to obtain the JWT token");
            }

            Console.WriteLine(result.AccessToken);

            Console.ReadLine();
        }
    }
}
```

Substitua `{tentand id}`, `{application id}`, e `{redirect uri}` utilizando as instruções seguintes.

Substitua `{tenant id}` com o id de inquilino da aplicação do Azure Active Directory que acabou de criar. Pode aceder ao id clicando **ver pontos finais**.

![Pontos Finais][api-management-aad-default-directory]

![Pontos Finais][api-management-endpoint]

Substitua `{application id}` e `{redirect uri}` utilizando o **Id de cliente** e o URL do **redirecionar Uris** secção da sua aplicação do Azure Active Directory **configurar** separador.

![Recursos][api-management-aad-resources]

Assim que os valores são especificados, o exemplo de código deverá devolver um token semelhante ao seguinte exemplo.

![Token][api-management-arm-token]

Antes de chamar as operações de cópia de segurança e restauro descritas nas secções seguintes, defina o cabeçalho de pedido de autorização para a chamada REST.

```c#
request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);
```

## <a name="step1"></a>Cópia de segurança um serviço de API Management
Para fazer uma cópia de segurança de um problema do serviço de API Management os seguintes pedidos de HTTP:

`POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/backup?api-version={api-version}`

Em que:

* `subscriptionId`-id de subscrição que contém o serviço de API Management está a tentar efetuar a cópia de segurança
* `resourceGroupName`-uma cadeia no formato 'Api - predefinição-{serviço região}' onde `service-region` identifica a região do Azure onde a API de gestão de serviço está a tentar cópia de segurança está alojada, por exemplo,`North-Central-US`
* `serviceName`-o nome do serviço de API Management estiver a efetuar uma cópia de segurança especificado no momento da respetiva criação
* `api-version`-Substitua`2014-02-14`

No corpo do pedido, especifique o nome de conta do storage do Azure de destino, a chave de acesso, o nome de contentor do blob e o nome de cópia de segurança:

```
'{  
    storageAccount : {storage account name for the backup},  
    accessKey : {access key for the account},  
    containerName : {backup container name},  
    backupName : {backup blob name}  
}'
```

Defina o valor da `Content-Type` cabeçalho do pedido para `application/json`.

Cópia de segurança é uma operação de execução longa que pode demorar vários minutos a concluir.  Se o pedido foi efetuado com êxito e o processo de cópia de segurança foi iniciado irá receber um `202 Accepted` código de estado de resposta com uma `Location` cabeçalho.  Tornar a 'GET' pedidos para o URL no `Location` cabeçalho para saber o estado da operação. Enquanto a cópia de segurança está em curso continuará a receber um código de estado de '202 aceites'. Um código de resposta `200 OK` indicará a conclusão com êxito da operação de cópia de segurança.

Tenha em atenção as seguintes restrições ao efetuar um pedido de cópia de segurança.

* **Contentor** especificado no corpo do pedido **tem de existir**.
* Enquanto a cópia de segurança está em curso **não deve tentar quaisquer operações de gestão de serviço** , tais como a atualização SKU ou mudança para versão anterior, alteração de nome de domínio, etc.
* Restauro de um **cópia de segurança é assegurada apenas para 30 dias** desde o momento da respetiva criação.
* **Dados de utilização** utilizado para criar relatórios de análise **não está incluído** na cópia de segurança. Utilize [API de REST de gestão de API do Azure] [ Azure API Management REST API] periodicamente obter relatórios de análise para salvaguarda.
* A frequência com que efetuar cópias de segurança do serviço irá afetar o objetivo de ponto de recuperação. Para minimizar-Aconselhamos implementar cópias de segurança regulares, bem como efetuar cópias de segurança a pedido depois de efetuar alterações importantes ao seu serviço de API Management.
* **As alterações** efetuadas à configuração do serviço (por exemplo, APIs, políticas, aspeto portal para programadores) durante a cópia de segurança está em processo operação **podem não ser incluídas na cópia de segurança e, por conseguinte, serão perdidas**.

## <a name="step2"></a>Restaurar um serviço de API Management
Para restaurar uma API Management serviço a partir de uma cópia de segurança criado anteriormente efetuar os seguintes pedidos de HTTP:

`POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/restore?api-version={api-version}`

Em que:

* `subscriptionId`-id de subscrição que contém o serviço de API Management que está a restaurar uma cópia de segurança para
* `resourceGroupName`-uma cadeia no formato 'Api - predefinição-{serviço região}' onde `service-region` identifica a região do Azure onde está alojado o serviço de API Management que está a restaurar uma cópia de segurança para, por exemplo,`North-Central-US`
* `serviceName`-o nome da API de gestão que está a ser restaurado para o serviço especificado no momento da respetiva criação
* `api-version`-Substitua`2014-02-14`

No corpo do pedido, especifique a localização do ficheiro de cópia de segurança, ou seja, o nome de conta de armazenamento do Azure, a chave de acesso, nome do contentor de blob e nome de cópia de segurança:

```
'{  
    storageAccount : {storage account name for the backup},  
    accessKey : {access key for the account},  
    containerName : {backup container name},  
    backupName : {backup blob name}  
}'
```

Defina o valor da `Content-Type` cabeçalho do pedido para `application/json`.

Restauro é uma operação de execução longa que poderá demorar até 30 ou mais minutos a concluir.  Se o pedido foi efetuado com êxito e o processo de restauro foi iniciado irá receber um `202 Accepted` código de estado de resposta com uma `Location` cabeçalho.  Tornar a 'GET' pedidos para o URL no `Location` cabeçalho para saber o estado da operação. Durante o restauro irá continuar a receber 'aceites 202' código de estado. Um código de resposta `200 OK` indicará a conclusão com êxito da operação de restauro.

> [!IMPORTANT]
> **O SKU** do serviço que está a ser restaurado para **tem de corresponder ao** o SKU do serviço de cópia de segurança seja restaurado.
>
> **As alterações** efetuadas à configuração do serviço (por exemplo, APIs, políticas, aspeto portal para programadores) ao restauro operação está em curso **podem ser substituídos**.
>
>

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes blogues da Microsoft para duas instruções diferentes do processo de cópia de segurança/restauro.

* [Replicar contas de gestão de API do Azure](https://www.returngis.net/en/2015/06/replicate-azure-api-management-accounts/)
  * Obrigado a Gisela a contribuição a este artigo.
* [API Management do Azure: Cópia de segurança e restaurar a configuração](http://blogs.msdn.com/b/stuartleeks/archive/2015/04/29/azure-api-management-backing-up-and-restoring-configuration.aspx)
  * A abordagem de detalhado por José não coincide com a documentação de orientação oficial mas é muito interessante.

[Backup an API Management service]: #step1
[Restore an API Management service]: #step2


[Azure API Management REST API]: http://msdn.microsoft.com/library/azure/dn781421.aspx

[api-management-add-aad-application]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-add-aad-application.png

[api-management-aad-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions.png
[api-management-aad-permissions-add]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions-add.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-delegated-permissions.png
[api-management-aad-default-directory]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-default-directory.png
[api-management-aad-resources]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-resources.png
[api-management-arm-token]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-arm-token.png
[api-management-endpoint]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-endpoint.png
