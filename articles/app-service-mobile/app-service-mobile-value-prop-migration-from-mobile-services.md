---
title: Utilizo Mobile Services, como é que o App Service me vai ajudar?
description: Conheça as vantagens que o App Service traz para os seus atuais projetos de Mobile Services.
services: app-service\mobile
documentationcenter: ios
author: adrianhall
manager: dwrede
editor: ''

ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/30/2016
ms.author: krisragh

---
# <a name="getting-started"> </a>Utilizo Mobile Services, como é que o App Service me vai ajudar?
## Descrição geral
O Serviço Móvel atual é seguro e continuará a receber suporte. No entanto, a plataforma *App Service do Azure* oferece várias vantagens à sua aplicação móvel que não estão atualmente disponíveis com os Mobile Services:

* Uma oferta mais simples, fácil e económica para as suas aplicações e que inclui tanto clientes Web como móveis
* Novas funcionalidades de anfitrião, incluindo Tarefas Web, CNames personalizados, melhor monitorização
* Integração chave na mão com Traffic Manager
* Conectividade com os seus recursos no local e VPNs utilizando VNet, além das Ligações Híbridas
* Monitorização, alertas e resolução de problemas da sua aplicação através da NewRelic ou AppInsights
* Um espectro mais alargado dos preços e recursos subjacentes da computação
* Escala automática, balanceamento de carga e monitorização do desempenho incorporados
* Capacidades de teste, cópia de segurança, recuperação e teste durante produção incorporadas

## Novas funcionalidades de alojamento
No *App Service do Azure*, o código de back-end da *Aplicação Móvel* é executado no mesmo contentor que a Aplicação Web e a Aplicação API. Como tal, pode tirar partido de todas as funcionalidades neste contentor, incluindo algumas das quais não estão atualmente presentes nos Mobile Services:

* Adição contínua de lógica de back-end em execução através das Tarefas da Web
* Garantia que o seu código de back-end está sempre em execução
* Utilização de CNames personalizados para fornecer nomes amigáveis e estáveis para os seus pontos finais de back-end móveis
* Geoescala da sua aplicação com o Traffic Manager
* Inclusão de quaisquer bibliotecas e pacotes que pretenda
* (Para .NET) Tire partido de qualquer funcionalidade do ASP.NET, incluindo MVC
* (Para Node.js) Tire partido de qualquer biblioteca JavaScript pura do ecossistema do Nó, incluindo bibliotecas MVC comuns

## Aceder a dados no local utilizando VNet
Atualmente, os Mobile Services já permitem a utilização de Ligações Híbridas para o acesso a recursos no local. No entanto, existem situações em que uma solução VPN é preferencial. Com o *App Service do Azure* pode utilizar o Azure VNet para o seu código de back-end de Aplicação Móvel.

## Utilizar o idioma de back-end favorito
O *App Service do Azure* oferece um suporte mais amplo e alargado para plataformas ASP.NET e Node.js, incluindo acesso aos tempos de execução mais recentes.

## Configurar uma escala automática
Com os Mobile Services, todas as instâncias do código de back-end eram executadas em VMs de pequena dimensão. O *App Service do Azure* permite selecionar o tamanho das VMs a partir de um conjunto de opções mais completo. De igual forma, pode rapidamente aumentar vertical ou horizontalmente para lidar com qualquer carga de cliente a receber, com base em várias métricas de desempenho.

## Estar na linha da frente
Reaja a problemas em tempo real com a monitorização e os alertas para notificar automaticamente o utilizador e a sua equipa. Integre funcionalidades avançadas de análise e monitorização de aplicações da New Relic e AppInsights e obtenha informações mais ricas sobre o desempenho da aplicação móvel. Com o *App Service do Azure*, pode agora configurar alertas de segurança com base em diversas métricas de desempenho, tanto através de programas como pelo Portal do Azure.

## Manter os elementos seguros
Crie automaticamente uma cópia de segurança do seu back-end e da sua base de dados. O seu código e os seus dados estão seguros contra desastres e podem ser facilmente restaurados, permitindo que continue a gerir o seu negócio com confiança.

## Pronto, Testar, Avançar!
Com o *App Service do Azure*, pode agora criar vários ambientes de testes e testes privados para as suas Mobile Apps. Utilize-os para realizar testes antes da implementação. Salte para produção sem qualquer período de indisponibilidade. As Web Apps são previamente carregadas, garantindo a melhor experiência para o cliente.

Pode começar a tirar partido do *App Service* do seu Serviço Móvel atual através deste [tutorial](app-service-mobile-migrating-from-mobile-services.md).

<!--HONumber=Aug16_HO1-->


