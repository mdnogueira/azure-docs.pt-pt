---
title: "Adicionar uma aplicação de Java para Web Apps do Azure App Service"
description: "Este tutorial mostra como adicionar uma aplicação à sua instância do Azure Web Apps do App Service que já está configurado para utilizar o Java ou página."
services: app-service\web
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 9b46528b-e2d0-4f26-b8d7-af94bd8c31ef
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 04/25/2017
ms.author: robmcm
ms.openlocfilehash: 1309985d7f1b93230b38ada2ee2687b1db10a791
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="add-a-java-application-to-azure-app-service-web-apps"></a>Adicionar uma aplicação de Java para Web Apps do Azure App Service
Depois de ter a inicializar a aplicação web de Java no [App Service do Azure] [ Azure App Service] conforme documentado no [criar uma aplicação web Java no App Service do Azure](app-service-web-get-started-java.md), pode carregar a aplicação, colocando o WAR no **webapps** pasta.

O caminho de navegação para o **webapps** pasta diferente com base na forma como configurar a instância de aplicações Web.

* Se configurar a sua aplicação web utilizando o Azure Marketplace, o caminho para o **webapps** pasta está no formato **d:\home\site\wwwroot\bin\application\_server\webapps**, onde **aplicação\_servidor** é o nome do servidor de aplicações em vigor na sua instância de aplicações Web. 
* Se configurar a sua aplicação web utilizando a IU, o caminho de configuração do Azure a **webapps** pasta está no formato **d:\home\site\wwwroot\webapps**. 

Tenha em atenção que pode utilizar o controlo de origem para carregar a aplicação ou páginas web, incluindo [cenários de integração contínua](app-service-continuous-deployment.md). FTP também é uma opção para carregar a aplicação ou páginas web; Para obter mais informações sobre como implementar as aplicações através de FTP, consulte [implementar a aplicação a utilizar FTP](app-service-deploy-ftp.md).

Tenha em atenção para as web apps do Tomcat: depois de carregar o ficheiro WAR para o **webapps** pasta, o servidor de aplicações Tomcat irá detetar que adição e irá carregá-lo automaticamente. Tenha em atenção que se copiar os ficheiros (que não sejam ficheiros WAR) para o diretório de raiz, o servidor de aplicação terá de ser reiniciados para que esses ficheiros são utilizados. A funcionalidade de autoload para as Tomcat as web apps Java em execução no Azure baseia-se num novo ficheiro WAR a ser adicionado ou novos ficheiros ou diretórios adicionado o **webapps** pasta. 

<a name="see-also"></a>

## <a name="see-also"></a>Veja Também
Para obter mais informações sobre como utilizar o Azure com o Java, veja o [Centro de Programadores do Java do Azure].

[Application-insights-App-insights-Java-Get-Started](../application-insights/app-insights-java-get-started.md)

<!-- URL List -->

[Centro de Programadores do Java do Azure]: https://azure.microsoft.com/develop/java/
[Azure App Service]: http://go.microsoft.com/fwlink/?LinkId=529714
