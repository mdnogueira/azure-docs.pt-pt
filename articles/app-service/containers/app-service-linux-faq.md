---
title: App Service do Azure no Linux FAQ | Microsoft Docs
description: App Service do Azure no Linux FAQ.
keywords: "serviço de aplicações do Azure, aplicação web, faq, linux, oss"
services: app-service
documentationCenter: 
author: ahmedelnably
manager: cfowler
editor: 
ms.assetid: 
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: aelnably;wesmc
ms.openlocfilehash: d262d9c2bd23a09c2efdb5fd6695bb2ed29cae54
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2017
---
# <a name="azure-app-service-on-linux-faq"></a>App Service do Azure no Linux FAQ

Com a versão do serviço de aplicações no Linux, estamos a trabalhar na adição de funcionalidades e a efetuar melhoramentos à nossa plataforma. Este artigo fornece respostas às questões que os nossos clientes tinham vindo a pedir-nos recentemente.

Se tiver uma pergunta, comentário no artigo e iremos irá responder logo que possível.

## <a name="built-in-images"></a>Imagens incorporadas

**Pretende copiar os contentores de Docker incorporados que fornece a plataforma. Onde posso encontrar os ficheiros?**

Pode encontrar todos os ficheiros de Docker no [GitHub](https://github.com/azure-app-service). Pode encontrar todos os contentores de Docker no [Docker Hub](https://hub.docker.com/u/appsvc/).

**Quais são os valores esperados para a secção do ficheiro de arranque quando configurar a pilha de tempo de execução?**

Para Node.js, especifique o ficheiro de configuração PM2 ou o ficheiro de script. Para .NET Core, especifique o nome DLL compilado. Para Ruby, pode especificar que pretende iniciar a aplicação com o script Ruby.

## <a name="management"></a>Gestão

**O que acontece quando posso prima o botão de reinício no portal do Azure?**

Esta ação é o mesmo que um reinício de Docker.

**Pode utilizar Secure Shell (SSH) para ligar à máquina virtual de contentor aplicação (VM)?**

Sim, pode fazê-lo através do site de gestão (SCM) do controlo de origem.

**Como posso criar um plano de serviço de aplicações do Linux através de um SDK ou um modelo Azure Resource Manager?**

Tem de definir o **reservado** campo o app service para *verdadeiro*.

## <a name="continuous-integration-and-deployment"></a>Integração e implementação contínua

**A minha aplicação web ainda utiliza uma imagem de contentor do Docker antiga depois posso atualizou a imagem no Hub de Docker. Suportam integração contínua e a implementação de contentores personalizados?**

Para configurar a integração/implementação contínua para registo de contentor do Azure ou DockerHub imagens pela verificação do seguinte artigo [a implementação contínua com a aplicação Web para contentores](./app-service-linux-ci-cd.md). Para os registos do privada, pode atualizar o contentor por parar e, em seguida, iniciar a aplicação web. Ou pode alterar ou adicionar uma definição de aplicação fictício para forçar uma atualização do seu contentor.

**Suportam ambientes de testes?**

Sim.

**Pode utilizar *do web deploy* para implementar a minha aplicação web?**

Sim, tem de definir uma aplicação definição chamado `WEBSITE_WEBDEPLOY_USE_SCM` para *falso*.

**Falha na implementação do Git da minha aplicação ao utilizar a aplicação web de Linux. Como posso solução o problema?**

Se falhar a implementação de Git para a sua aplicação web do Linux, pode escolher as seguintes opções alternativas para implementar o código de aplicação:

- Utilizar a funcionalidade de entrega contínua (pré-visualização): pode armazenar o código fonte da aplicação num repositório de Git de serviços da equipa ou repositório do GitHub para utilizar a distribuição contínua do Azure. Para obter mais detalhes, consulte [como configurar a entrega contínua da aplicação web de Linux](https://blogs.msdn.microsoft.com/devops/2017/05/10/use-azure-portal-to-setup-continuous-delivery-for-web-app-on-linux/).

- Utilize o [ZIP implementar API](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file): para utilizar esta API, [SSH para a aplicação web](https://docs.microsoft.com/en-us/azure/app-service/containers/app-service-linux-ssh-support#making-a-client-connection) e aceda à pasta onde pretende implementar o seu código. Execute o seguinte:

   ```
   curl -X POST -u <user> --data-binary @<zipfile> https://{your-sitename}.scm.azurewebsites.net/api/zipdeploy
   ```

   Se obtiver um erro que o `curl` comando não foi encontrado, certifique-se instalar o curl utilizando `apt-get install curl` antes de executar o anterior `curl` comando.

## <a name="language-support"></a>Suporte de idiomas

**Pretende utilizar websockets na minha aplicação Node.js, definições especiais ou configurações para definir?**

Sim, desativar `perMessageDeflate` no seu código de Node.js do lado do servidor. Por exemplo, se estiver a utilizar o socket.io, efetue o seguinte:
```
var io = require('socket.io')(server,{
  perMessageDeflate :false
});
```

**Suportam aplicações de .NET Core uncompiled?**

Sim.

**Suportam compositor como um Gestor de dependência para aplicações PHP?**

Sim. Durante a implementação de Git, o Kudu deve detetar que está a implementar uma aplicação PHP (graças à presença de um ficheiro de composer.lock), e Kudu irá acionar uma instalação de compositor por si.

## <a name="custom-containers"></a>Contentores personalizados

**Estou a utilizar o meu próprio contentor personalizado. Quero a plataforma para montar uma partilha SMB para a `/home/` diretório.**

Pode fazê-lo definindo a `WEBSITES_ENABLE_APP_SERVICE_STORAGE` definição de aplicação para *verdadeiro* ou removendo a aplicação definir completamente. Tenha em atenção que o se o fizer Isto fará com que reinícios do contentor quando o armazenamento de plataforma passa através de uma alteração. 

>[!NOTE]
>Se o `WEBSITES_ENABLE_APP_SERVICE_STORAGE` definição é *falso*, a `/home/` directory não irão ser partilhado entre instâncias de escala e ficheiros que são escritos existe não irão ser persistente entre reinícios.

**A minha contentor personalizado demora muito tempo a iniciar e, a plataforma reinicia o contentor antes de concluir a iniciar.**

Pode configurar a quantidade de tempo que a plataforma aguardará antes de que reinicia o contentor. Para tal, defina o `WEBSITES_CONTAINER_START_TIME_LIMIT` definição de aplicação para o valor que pretende. O valor predefinido é 230 segundos e o valor máximo é 600 segundos.

**O que é o formato para o URL do servidor de registo privada?**

Forneça o URL de registo completo, incluindo `http://` ou `https://`.

**O que é o formato para o nome da imagem na opção de registo privada?**

Adicione o nome de imagem completa, incluindo o URL de registo privada (por exemplo, myacr.azurecr.io/dotnet:latest). Imagem de nomes que utilizam uma porta personalizada [não pode ser introduzido através do portal](https://feedback.azure.com/forums/169385-web-apps/suggestions/31304650). Para definir `docker-custom-image-name`, utilize o [ `az` ferramenta da linha de comandos](https://docs.microsoft.com/cli/azure/webapp/config/container?view=azure-cli-latest#az_webapp_config_container_set).

**Pode expor mais de uma porta no meu imagem personalizada do contentor?**

Estamos atualmente não suportam a exposição de mais de uma porta.

**Pode colocar meu próprio armazenamento?**

Não é atualmente suportada colocar o seu próprio armazenamento.

**Por que motivo não pode procurar os ficheiro sistema ou com processos meus contentor personalizado do SCM site?**

O site SCM é executado num contentor separado. Não é possível verificar os processos de sistema ou em execução do ficheiro do contentor de aplicação.

**Escuta do meu contentor personalizada para uma porta diferente da porta 80. Como configurar a minha aplicação para encaminhar pedidos para essa porta?**

Temos de deteção automática de porta. Também pode especificar uma aplicação definição chamado *WEBSITES_PORT* e atribua-lhe o valor do número de porta esperado. Anteriormente, a plataforma utilizada o *porta* definição de aplicação. Iremos estiver a planear despromover esta definição de aplicação e utilizar *WEBSITES_PORT* exclusivamente.

**É necessário implementar HTTPS no meu contentor personalizado?**

Não, a plataforma processa a terminação HTTPS, as extremidades de front-partilhadas.

## <a name="pricing-and-sla"></a>Preços e SLA

**O que é o preço, agora que o serviço estiver geralmente disponível?**

São-lhe cobrados os preços de App Service do Azure normal para o número de horas que a aplicação for executada.

## <a name="other-questions"></a>Outras questões

**Quais são os carateres suportados em nomes de definições de aplicação?**

Pode utilizar apenas letras (A-Z, a-z), números (0-9) e caráter de sublinhado (_) para as definições da aplicação.

**Onde podem a pedir novas funcionalidades?**

Pode submeter a ideia no [fórum de comentários do Web Apps](https://aka.ms/webapps-uservoice). Adicione "[Linux]" para o título da sua ideia.

## <a name="next-steps"></a>Passos seguintes

* [O que é o serviço de aplicações do Azure no Linux?](app-service-linux-intro.md)
* [Configurar ambientes no App Service do Azure de teste](../../app-service/web-sites-staged-publishing.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Implementação contínua com a aplicação Web para contentores](./app-service-linux-ci-cd.md)
