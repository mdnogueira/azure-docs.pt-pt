---
title: "Introdução ao serviço de aplicações no Linux | Microsoft Docs"
description: Saiba mais sobre o App Service do Azure no Linux.
keywords: "serviço de aplicações do Azure, linux, oss"
services: app-service
documentationcenter: 
author: naziml
manager: cfowler
editor: 
ms.assetid: bc85eff6-bbdf-410a-93dc-0f1222796676
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 02/16/2017
ms.author: wesmc
ms.custom: mvc
ms.openlocfilehash: 89cb7dc488da42724f212d13f8550064ff8b9188
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2017
---
# <a name="introduction-to-azure-app-service-on-linux"></a>Introdução ao App Service do Azure no Linux

[Aplicação Web](../app-service-web-overview.md) é uma plataforma de computação completamente gerida que está otimizada para o alojamento de Web sites e aplicações web. Os clientes podem utilizar do serviço de aplicações no Linux para o anfitrião web apps nativamente do Linux para pilhas de aplicação suportadas. As secções seguintes lista as pilhas de aplicação são atualmente suportadas.

## <a name="languages"></a>Linguagens

Serviço de aplicações no Linux suporta um número de imagens incorporadas para aumentar a produtividade de programador. Se o tempo de execução requer que a aplicação não é suportado nas imagens do incorporada, existem instruções sobre como [criar a sua própria imagem Docker](tutorial-custom-docker-image.md) para implementar a aplicação Web para contentores.

| Idioma | Versões suportadas |
|---|---|
| Node.js | 4.4, 4.5, 6.2, 6.6, 6.9-6.11, 8.0, 8.1 |
| PHP | 5.6, 7.0 |
| .NET Core | 1.0, 1.1 |
| Ruby | 2.3 |

## <a name="deployments"></a>Implementações

* FTP
* Local Git
* GitHub
* Bitbucket

## <a name="devops"></a>DevOps

* Ambientes de teste
* [Registo de contentor do Azure](https://docs.microsoft.com/azure/container-registry/container-registry-intro) e DockerHub CI/CD

## <a name="console-publishing-and-debugging"></a>Consola, publicar e depuração

* Ambientes
* Implementações
* Consola básica
* SSH

## <a name="scaling"></a>Dimensionamento

* Os clientes podem dimensionar as web apps e reduzir verticalmente ao alterar o escalão dos respetivos [plano do App Service](https://docs.microsoft.com/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview?toc=%2fazure%2fapp-service-web%2ftoc.json)

## <a name="locations"></a>Localizações

Verifique o [Dashboard de estado do Azure](https://azure.microsoft.com/status).

## <a name="limitations"></a>Limitações

O portal do Azure mostra apenas as funcionalidades que funcionam atualmente para a aplicação Web para contentores. Como ativamos mais funcionalidades, poderá ser visíveis no portal.

Algumas funcionalidades, tais como a integração de rede virtual, do Azure Active Directory /-autenticação de terceiros ou as extensões de site do Kudu, ainda não estão disponíveis. Depois destas funcionalidades estão disponíveis, atualizaremos nossa documentação e o blogue sobre as alterações.

Serviço de aplicações no Linux só é suportado com [básico e padrão](https://azure.microsoft.com/pricing/details/app-service/plans/) planos de serviço de aplicações e não tem um [gratuito ou partilhado](https://azure.microsoft.com/pricing/details/app-service/plans/) camada. Seguem-se também restrições importantes para o serviço de aplicações no Linux:

* Não é possível criar a aplicação Web para contentores num plano do serviço de aplicações já aloja não Linux Web Apps.
* Ao criar a aplicação Web para contentores num grupo de recursos que contém não Linux Web Apps, tem de criar um plano de serviço de aplicações numa região diferente que o plano de serviço aplicacional existente.

## <a name="troubleshooting"></a>Resolução de problemas

Quando a aplicação não consegue iniciar ou pretende verificar o registo da sua aplicação, verifique os registos do Docker no diretório LogFiles. Pode aceder a este diretório através do seu site SCM ou através de FTP.
Registo de `stdout` e `stderr` do seu contentor, tem de ativar **registo do contentor de Docker** em **registos de diagnóstico**.

![Ativar o registo][2]

![Utilizar o Kudu para ver registos de Docker][1]

Pode aceder ao site SCM de **ferramentas avançadas** no **ferramentas de desenvolvimento** menu.

## <a name="next-steps"></a>Passos seguintes

Consulte as ligações seguintes para começar a utilizar o serviço de aplicações no Linux. Pode publicar perguntas e problemas no [nosso fórum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

* [Como utilizar uma imagem personalizada do Docker da aplicação Web para contentores](quickstart-custom-docker-image.md)
* [Utilizando o .NET Core no App Service do Azure no Linux](quickstart-dotnetcore.md)
* [Utilizar Ruby no App Service do Azure no Linux](quickstart-ruby.md)
* [Aplicação de Web do App Service do Azure para contentores FAQ](app-service-linux-faq.md)
* [Suporte SSH para o serviço de aplicações do Azure no Linux](app-service-linux-ssh-support.md)
* [Configurar ambientes no App Service do Azure de teste](../../app-service/web-sites-staged-publishing.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Docker Hub a implementação contínua com a aplicação Web para contentores](./app-service-linux-ci-cd.md)

<!--Image references-->
[1]: ./media/app-service-linux-intro/kudu-docker-logs.png
[2]: ./media/app-service-linux-intro/logging.png
