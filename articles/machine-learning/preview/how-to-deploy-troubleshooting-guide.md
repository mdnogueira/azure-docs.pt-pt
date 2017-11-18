---
title: "Implementação de Machine Learning do Azure guia de resolução de problemas | Microsoft Docs"
description: "Guia de resolução de problemas de implementação e a criação do serviço"
services: machine-learning
author: raymondl
ms.author: raymondl
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 11/16/2017
ms.openlocfilehash: 8eafb16abeb939a16b1ddb024853300c453bcd9a
ms.sourcegitcommit: a036a565bca3e47187eefcaf3cc54e3b5af5b369
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2017
---
# <a name="troubleshooting-service-deployment-and-environment-setup"></a>Resolução de problemas de implementação de serviço e configuração do ambiente
As informações seguintes podem ajudar a determinar a causa de erros ao configurar o ambiente de gestão de modelo.

## <a name="model-management-environment"></a>Ambiente de gestão de modelo
### <a name="contributor-permission-required"></a>Permissão de contribuinte necessária
Precisa de acesso de contribuinte a subscrição ou o grupo de recursos para configurar um cluster para a implementação dos seus serviços web.

### <a name="resource-availability"></a>Disponibilidade de recursos
Tem de ter recursos suficientes disponíveis na sua subscrição, pelo que pode aprovisionar os recursos de ambiente.

### <a name="subscription-caps"></a>Caps de subscrição
A subscrição pode ter um limite na qual pode impedi-lo de aprovisionar os recursos de ambiente de faturação. Remova essa cap para ativar o aprovisionamento.

### <a name="enable-debug-and-verbose-options"></a>Ativar a depuração e opções verbosas
Utilize o `--debug` e `--verbose` sinalizadores no comando de configuração para mostrar as informações de depuração e rastreio como o ambiente está a ser aprovisionado.

```
az ml env setup -l <location> -n <name> -c --debug --verbose 
```

## <a name="service-deployment"></a>Implementação de serviço
As informações seguintes podem ajudar a determinar a causa de erros durante a implementação ou ao chamar o serviço web.

### <a name="service-logs"></a>registos do serviço
O `logs` opção do serviço CLI fornece dados de registo do Docker e Kubernetes.

```
az ml service logs realtime -i <web service id>
```

Para as definições de registo adicionais, utilize o `--help` (ou `-h`) opção.

```
az ml service logs realtime -h
```

### <a name="debug-and-verbose-options"></a>Opções de depuração e verboso
Utilize o `--debug` sinalizador para mostrar registos de depuração, o serviço está a ser implementado.

```
az ml service create realtime -m <modelfile>.pkl -f score.py -n <service name> -r python --debug
```

Utilize o `--verbose` sinalizador para ver detalhes adicionais, como o serviço está a ser implementado.

```
az ml service create realtime -m <modelfile>.pkl -f score.py -n <service name> -r python --verbose
```

### <a name="enable-request-logging-in-app-insights"></a>Ativar o pedido de registo no App Insights
Definir o `-l` sinalizador como verdadeiro quando a criação de um serviço web para ativar o registo ao nível do pedido. Os registos de pedido são escritos para a instância do App Insights para o seu ambiente no Azure. Pesquisa para esta instância com o nome do ambiente utilizados quando usar o `az ml env setup` comando.

- Definir `-l` como verdadeiro quando a criação do serviço.
- Abrir Insights da aplicação no portal do Azure. Utilize o seu nome de ambiente para localizar a instância do App Insights.
- Uma vez no App Insights, clique em procura no menu superior para ver os resultados.
- Ou vá para `Analytics`  >  `Exceptions`  >  `exceptions take | 10`.


### <a name="add-error-handling-in-scoring-script"></a>Adicionar no script de classificação de processamento de erros
Utilizar excepção a processar no seu `scoring.py` do script **executar** função para devolver a mensagem de erro como parte da sua saída de serviço web.

Exemplo de Python:
```
    try:
        <code to load model and score>
   except Exception as e:
        return(str(e))
```

## <a name="other-common-problems"></a>Outros problemas comuns
- Se a instalação do pip do azure-cli-ml falhar com o erro `cannot find the path specified` num computador Windows, terá de ativar o suporte de caminho longos. Consulte https://blogs.msdn.microsoft.com/jeremykuhne/2016/07/30/net-4-6-2-and-long-paths-on-windows-10/. 
- Se o `env setup` comando falha com `LocationNotAvailableForResourceType`, provavelmente, estiver a utilizar a localização incorreta (região) para a recursos de aprendizagem. Certifique-se de que a localização especificada com o `-l` parâmetro é `eastus2`, `westcentralus`, ou `australiaeast`.
- Se o `env setup` comando falha com `Resource quota limit exceeded`, certifique-se de que tem suficiente núcleos disponíveis na sua subscrição e que não estão a ser utilizados os recursos de segurança em outros processos.
- Se o `env setup` comando falha com `Invalid environment name. Name must only contain lowercase alphanumeric characters`, certifique-se de que o nome do serviço não conter letras maiúsculas, símbolos ou o caráter de sublinhado (_) (como no *my_environment*).
- Se o `service create` comando falha com `Service Name: [service_name] is invalid. The name of a service must consist of lower case alphanumeric characters (etc.)`, certifique-se de que o nome do serviço entre 3 e 32 carateres de comprimento; inicia e termina com carateres alfanuméricos minúsculos; e não conter letras maiúsculas, símbolos que não sejam hífen (-) e o período ( . ), ou o caráter de sublinhado (_) (como no *my_webservice*).
- Repita se obtiver um `502 Bad Gateway` Ocorreu um erro ao chamar o serviço web. Normalmente, significa que o contentor não foi implementado para o cluster.
- Se obtiver `CrashLoopBackOff` Ocorreu um erro ao criar um serviço, verifique os registos. Normalmente, é o resultado de dependências em falta no **init** função.
