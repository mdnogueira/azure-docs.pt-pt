---
title: Proteger o acesso ao Azure Logic Apps | Microsoft Docs
description: "Adicione a segurança para proteger o acesso a acionadores, entradas e saídas, parâmetros de ação e serviços utilizado com fluxos de trabalho no Azure Logic Apps."
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 9fab1050-cfbc-4a8b-b1b3-5531bee92856
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 11/22/2016
ms.author: LADocs; jehollan
ms.openlocfilehash: 45a4e476f930e0f5f6633dc5b3b35b66dc6dfa20
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="secure-access-to-your-logic-apps"></a>Acesso seguro a suas logic apps

Existem várias ferramentas disponíveis para ajudar a proteger a sua aplicação lógica.

* Proteger o acesso ao acionar uma aplicação lógica (acionador de pedido de HTTP)
* Proteger o acesso ao gerir, editar ou de leitura de uma aplicação lógica
* Proteger o acesso ao conteúdo de entradas e saídas para uma execução
* Proteger parâmetros ou entradas dentro ações num fluxo de trabalho
* Proteger o acesso aos serviços que receber pedidos de um fluxo de trabalho

## <a name="secure-access-to-trigger"></a>Acesso seguro a acionar

Quando trabalha com uma aplicação lógica que é acionado um pedido de HTTP ([pedido](../connectors/connectors-native-reqres.md) ou [Webhook](../connectors/connectors-native-webhook.md)), pode restringir o acesso, para que apenas os clientes autorizados podem acionados a aplicação lógica. Todos os pedidos para uma aplicação lógica estão encriptados e protegidos por SSL.

### <a name="shared-access-signature"></a>Assinatura de acesso partilhado

Cada ponto final de pedido para uma aplicação lógica inclui um [assinatura de acesso partilhado (SAS)](../storage/common/storage-dotnet-shared-access-signature-part-1.md) como parte do URL. Cada URL contém um `sp`, `sv`, e `sig` parâmetro de consulta. Permissões especificadas pelo `sp`e correspondem aos métodos HTTP permitidos, `sv` é a versão utilizada para gerar, e `sig` é utilizado para autenticar o acesso a acionar. A assinatura é gerada utilizando o algoritmo SHA256 com uma chave secreta em todos os caminhos do URL e as propriedades. A chave secreta nunca está exposta e publicada e são mantidos encriptados e armazenados como parte da aplicação lógica. A aplicação lógica autoriza apenas acionadores que contém uma assinatura válida criada com a chave secreta.

#### <a name="regenerate-access-keys"></a>Voltar a gerar chaves de acesso

Pode voltar a gerar uma nova chave segura em qualquer altura através do portal de REST API ou do Azure. Todos os URLs atuais que foram gerados anteriormente utilizando a chave antiga são invalidados e já não estará autorizados a acionar a aplicação lógica.

1. No portal do Azure, abra a aplicação lógica que pretende voltar a gerar uma chave
1. Clique em de **chaves de acesso** item de menu em **definições**
1. Escolha a chave para voltar a gerar e concluir o processo

URLs que obter depois da regeneração da sessão com a nova chave de acesso.

#### <a name="creating-callback-urls-with-an-expiration-date"></a>Criar URLs de chamada de retorno com uma data de expiração

Se estiver a partilhar o URL com outras entidades, pode gerar os URLs com chaves específicas e as datas de expiração conforme necessário. Pode, em seguida, perfeitamente reverta chaves ou certifique-se de que o acesso a acionar uma aplicação é restringido para um determinado timespan. Pode especificar uma data de expiração de um URL através de [aplicações lógicas REST API](https://docs.microsoft.com/rest/api/logic/workflowtriggers):

``` http
POST 
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/workflows/{workflowName}/triggers/{triggerName}/listCallbackUrl?api-version=2016-06-01
```

No corpo do inclui a propriedade `NotAfter` como uma cadeia de data JSON, que devolve um URL de chamada de retorno que só é válido até o `NotAfter` data e hora.

#### <a name="creating-urls-with-primary-or-secondary-secret-key"></a>Criar os URLs com a chave secreta primária ou secundária

Ao gerar ou lista de URLs de chamada de retorno para acionadores baseado em pedidos, também pode especificar qual a chave a utilizar para assinar o URL.  Pode gerar um URL assinado por uma chave específica através de [aplicações lógicas REST API](https://docs.microsoft.com/rest/api/logic/workflowtriggers) da seguinte forma:

``` http
POST 
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/workflows/{workflowName}/triggers/{triggerName}/listCallbackUrl?api-version=2016-06-01
```

No corpo do inclui a propriedade `KeyType` como `Primary` ou `Secondary`.  Esta ação devolve um URL assinado pela chave segura especificada.

### <a name="restrict-incoming-ip-addresses"></a>Restringir os endereços recebidos de IP

Para além de assinatura de acesso partilhado, pode pretender restringir a chamar uma aplicação lógica apenas a partir de clientes específicos.  Por exemplo, se gere o ponto final através da API Management do Azure, pode restringir a aplicação lógica para apenas aceitar o pedido quando o pedido é proveniente de endereço IP da instância de API Management.

Esta definição pode ser configurada nas definições da aplicação lógica:

1. No portal do Azure, abra a aplicação lógica que pretende adicionar restrições de endereço IP
1. Clique em de **configuração de controlo de acesso** item de menu em **definições**
1. Especifique a lista de intervalos de endereços IP para ser aceites pelo acionador

Um intervalo IP válido assume o formato `192.168.1.1/255`. Se pretender que a aplicação lógica para accionar apenas como uma aplicação lógica aninhados, selecione o **outras as logic apps** opção. Esta opção escreve uma matriz vazia para o recurso significado apenas chamadas do serviço próprio (as logic apps de principal) acionados com êxito.

> [!NOTE]
> Pode continuar a executar uma aplicação lógica com um acionador de pedido através da API REST / gestão `/triggers/{triggerName}/run` independentemente do IP. Este cenário requer a autenticação com a API de REST do Azure e todos os eventos eram apresentadas no registo de auditoria do Azure. Acesso de conjunto de políticas de controlo em conformidade.

#### <a name="setting-ip-ranges-on-the-resource-definition"></a>A definição de intervalos de IP na definição do recurso

Se estiver a utilizar um [modelo de implementação](logic-apps-create-deploy-template.md) para automatizar as implementações, as definições de intervalo IP podem ser configuradas no modelo de recursos.  

``` json
{
    "properties": {
        "definition": {
        },
        "parameters": {},
        "accessControl": {
            "triggers": {
                "allowedCallerIpAddresses": [
                    {
                        "addressRange": "192.168.12.0/23"
                    },
                    {
                        "addressRange": "2001:0db8::/64"
                    }
                ]
            }
        }
    },
    "type": "Microsoft.Logic/workflows"
}

```

### <a name="adding-azure-active-directory-oauth-or-other-security"></a>A adição do Azure Active Directory, OAuth ou outro administrativo

Para adicionar mais protocolos de autorização por cima de uma aplicação lógica, [API Management do Azure](https://azure.microsoft.com/services/api-management/) oferece monitorização otimizada, segurança, política e a documentação para qualquer ponto final com a capacidade para expor uma aplicação lógica como uma API. Gestão de API do Azure pode expor um ponto de final público ou privado para a aplicação lógica, que pode utilizar o Azure Active Directory, certificado, OAuth ou outras normas de segurança. Quando é recebido um pedido, a API Management do Azure reencaminha o pedido para a aplicação lógica (efetuar quaisquer restrições em trânsito ou transformações necessárias). Pode utilizar as definições de intervalo IP recebidas da aplicação lógica para permitir apenas a aplicação lógica para ser acionadas a partir da API Management.

## <a name="secure-access-to-manage-or-edit-logic-apps"></a>Proteger o acesso para gerir ou editar as logic apps

Pode restringir o acesso às operações de gestão numa aplicação lógica, para que apenas utilizadores específicos ou grupos são capazes de efetuar operações no recurso. As Logic apps utilizam o Azure [controlo de acesso baseado em funções (RBAC)](../active-directory/role-based-access-control-configure.md) funcionalidade e pode ser personalizada com as ferramentas do mesmas.  Existem algumas funções incorporadas, que pode atribuir os membros da sua subscrição, bem como:

* **Contribuinte de aplicação lógica** -fornece acesso para ver, editar e atualizar uma aplicação lógica.  Não é possível remover o recurso ou efetuar operações de admin.
* **Operador de aplicação lógica** - pode ver a aplicação lógica histórico de execução e ativar/desativar.  Não é possível editar ou atualizar a definição.

Também pode utilizar [bloqueio de recursos do Azure](../azure-resource-manager/resource-group-lock-resources.md) para impedir que alterar ou eliminar as logic apps. Esta funcionalidade é útil para impedir que os recursos de produção de alterações ou eliminações.

## <a name="secure-access-to-contents-of-the-run-history"></a>Proteger o acesso ao conteúdo do histórico de execução

Pode restringir o acesso ao conteúdo de entradas e saídas da execução anterior para intervalos de endereços IP específicos.  

Todos os dados dentro de uma execução de fluxo de trabalho são encriptados em trânsito e rest. Quando é efetuada uma chamada para o histórico de execução, o serviço autentica o pedido e fornece ligações para o pedido e resposta entradas e saídas. Esta ligação pode ser protegida pelo pedidos apenas para ver o conteúdo a partir de um intervalo de endereços IP designado devolverem o conteúdo. Pode utilizar esta capacidade para controlo de acesso adicionais. Pode ainda especificar um endereço IP, como `0.0.0.0` pelo que ninguém pode aceder a entradas/saídas. Apenas um utilizador com permissões de administrador foi possível remover esta restrição, fornecendo a possibilidade de acesso de 'just-in-time' para o conteúdo do fluxo de trabalho.

Esta definição pode ser configurada nas definições de recursos do portal do Azure:

1. No portal do Azure, abra a aplicação lógica que pretende adicionar restrições de endereço IP
1. Clique em de **configuração de controlo de acesso** item de menu em **definições**
1. Especifique a lista de intervalos de endereços IP para acesso ao conteúdo

#### <a name="setting-ip-ranges-on-the-resource-definition"></a>A definição de intervalos de IP na definição do recurso

Se estiver a utilizar um [modelo de implementação](logic-apps-create-deploy-template.md) para automatizar as implementações, as definições de intervalo IP podem ser configuradas no modelo de recursos.  

``` json
{
    "properties": {
        "definition": {
        },
        "parameters": {},
        "accessControl": {
            "contents": {
                "allowedCallerIpAddresses": [
                    {
                        "addressRange": "192.168.12.0/23"
                    },
                    {
                        "addressRange": "2001:0db8::/64"
                    }
                ]
            }
        }
    },
    "type": "Microsoft.Logic/workflows"
}
```

## <a name="secure-parameters-and-inputs-within-a-workflow"></a>Proteger os parâmetros e entradas num fluxo de trabalho

Pode querer parametrizar alguns aspetos de uma definição de fluxo de trabalho para implementação em ambientes. Além disso, alguns parâmetros podem ser parâmetros segurados que não a pretende ver apresentado ao editar um fluxo de trabalho, tais como um ID de cliente e o segredo do cliente para [autenticação do Azure Active Directory](../connectors/connectors-native-http.md#authentication) de uma ação de HTTP.

### <a name="using-parameters-and-secure-parameters"></a>Utilizar parâmetros e parâmetros segurados

Para aceder ao valor de um parâmetro de recurso em runtime, o [linguagem de definição de fluxo de trabalho](http://aka.ms/logicappsdocs) fornece um `@parameters()` operação. Além disso, pode [especificar parâmetros no modelo de implementação de recursos](../azure-resource-manager/resource-group-authoring-templates.md#parameters). Porém, se especificou o tipo de parâmetro como `securestring`, o parâmetro não será devolvido com o resto da definição do recurso e não estar acessível através da visualização o recurso após a implementação.

> [!NOTE]
> Se o parâmetro é utilizado em cabeçalhos ou do corpo de um pedido, o parâmetro poderão ser visível acedendo o histórico de execução e o pedido de HTTP de saída. Certifique-se de que define as políticas de acesso ao conteúdo em conformidade.
> Cabeçalhos de autorização nunca são visíveis através de entradas e saídas. Por isso, se o segredo está a ser utilizado não existe, o segredo não é recuperável.

#### <a name="resource-deployment-template-with-secrets"></a>Modelo de implementação de recursos com segredos

O exemplo seguinte mostra uma implementação que faça referência a um parâmetro seguro do `secret` no tempo de execução. No ficheiro de parâmetros separados, pode especificar o valor de ambiente para o `secret`, ou utilize [do Azure Resource Manager KeyVault](../azure-resource-manager/resource-manager-keyvault-parameter.md) obter segredos em implementar tempo.

``` json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "secretDeploymentParam": {
      "type": "securestring"
    }
  },
  "variables": {},
  "resources": [
    {
      "name": "secret-deploy",
      "type": "Microsoft.Logic/workflows",
      "location": "westus",
      "tags": {
        "displayName": "LogicApp"
      },
      "apiVersion": "2016-06-01",
      "properties": {
        "definition": {
          "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
          "actions": {
            "Call_External_API": {
              "type": "http",
              "inputs": {
                "headers": {
                  "Authorization": "@parameters('secret')"
                },
                "body": "This is the request"
              },
              "runAfter": {}
            }
          },
          "parameters": {
            "secret": {
              "type": "SecureString"
            }
          },
          "triggers": {
            "manual": {
              "type": "Request",
              "kind": "Http",
              "inputs": {
                "schema": {}
              }
            }
          },
          "contentVersion": "1.0.0.0",
          "outputs": {}
        },
        "parameters": {
          "secret": {
            "value": "[parameters('secretDeploymentParam')]"
          }
        }
      }
    }
  ],
  "outputs": {}
}
```

## <a name="secure-access-to-services-receiving-requests-from-a-workflow"></a>Proteger o acesso aos serviços de receção de pedidos de um fluxo de trabalho

Existem várias formas para ajudar a proteger qualquer ponto final que tem de aceder a aplicação lógica.

### <a name="using-authentication-on-outbound-requests"></a>Utilizar a autenticação nos pedidos de saída

Ao trabalhar com um HTTP, HTTP + Swagger (API aberto) ou Webhook ação, pode adicionar autenticação para o pedido de envio. Pode incluir a autenticação básica, autenticação de certificado ou a autenticação do Azure Active Directory. Podem ser encontrados detalhes sobre como configurar esta autenticação [neste artigo](../connectors/connectors-native-http.md#authentication).

### <a name="restricting-access-to-logic-app-ip-addresses"></a>Restringir o acesso a endereços IP de aplicação lógica

Todas as chamadas a partir das logic apps provenientes de um conjunto específico de endereços IP por região. Pode adicionar adicionais de filtragem para aceitar apenas pedidos desses endereços IP designados. Para obter uma lista desses endereços IP, consulte [limites de aplicação de lógica e configuração](logic-apps-limits-and-config.md#configuration).

### <a name="on-premises-connectivity"></a>Conectividade no local

As Logic apps fornecem integração com vários serviços para fornecer seguras e fiáveis no local comunicação.

#### <a name="on-premises-data-gateway"></a>Gateway de dados no local

Muitos conetores geridos para aplicações lógicas fornecem conectividade segura para sistemas no local, incluindo o sistema de ficheiros, SQL Server, SharePoint, DB2 e muito mais. O gateway transmite dados a partir de origens do local em canais encriptados através do Service Bus do Azure. Todo o tráfego origina como tráfego de saída seguro do agente de gateway. Saiba mais sobre [como funciona o gateway de dados](logic-apps-gateway-install.md#gateway-cloud-service).

#### <a name="azure-api-management"></a>API Management do Azure

[Gestão de API do Azure](https://azure.microsoft.com/services/api-management/) tem opções de conectividade no local, incluindo a integração de ExpressRoute e de VPN de site a site para proxy segura e comunicação para sistemas no local. No Designer de aplicação lógica, pode selecionar rapidamente uma API exposta a partir de API Management do Azure dentro de um fluxo de trabalho, que fornece acesso rápido aos sistemas no local.

## <a name="next-steps"></a>Passos seguintes
[Criar um modelo de implementação](logic-apps-create-deploy-template.md)  
[Processamento de exceções](logic-apps-exception-handling.md)  
[Monitorizar as aplicações lógicas](logic-apps-monitor-your-logic-apps.md)  
[Diagnosticar problemas e falhas de aplicação lógica](logic-apps-diagnosing-failures.md)  
