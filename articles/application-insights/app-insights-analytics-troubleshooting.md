---
title: "Resolver problemas de análise no Azure Application Insights | Microsoft Docs"
description: "Problemas de análise do Application Insights? Comece aqui. "
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: 9bbd5859-3584-4d80-9b6d-d5910fa48baa
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 07/11/2016
ms.author: mbullwin
ms.openlocfilehash: 3ef9150cad62e9f9fc43f7afcbbbc01d89884a12
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="troubleshoot-analytics-in-application-insights"></a>Resolver problemas de Análise no Application Insights
Problemas com [Application Insights Analytics](app-insights-analytics.md)? Comece aqui. A análise é a ferramenta poderosa pesquisa do Azure Application Insights.

## <a name="limits"></a>Limites
* Atualmente, os resultados da consulta estão limitados a apenas através de uma semana dos últimos dados.
* Browsers iremos testar o: edições mais recentes do Chrome, o limite e o Internet Explorer.

## <a name="known-incompatible-browser-extensions"></a>Extensões de browser incompatível conhecidos
* Ghostery

Desativar a extensão ou utilize um browser diferente.

## <a name="e-a"></a>"Error inesperado"
![Ecrã de um erro inesperado](./media/app-insights-analytics-troubleshooting/010.png)

Ocorreu um erro interno durante o tempo de execução portal – exceção não processada.

* Limpar a cache do browser. 

## <a name="e-b"></a>403... Volte a tentar recarregar
![403... Volte a tentar recarregar](./media/app-insights-analytics-troubleshooting/020.png)

Ocorreu um erro relacionado com a autenticação (durante a autenticação ou durante a geração do token de acesso). O portal, não pode ter nenhuma forma de recuperar sem alterar as definições do browser.

* Certifique-se [cookies de terceiros estão ativados](#cookies) no browser. 

## <a name="authentication"></a>403... Certifique-se a zona de segurança
![403 … .verify zona de segurança](./media/app-insights-analytics-troubleshooting/030.png)

Ocorreu um erro relacionado com a autenticação (durante a autenticação ou durante a geração do token de acesso). O portal, não pode ter nenhuma forma de recuperar sem alterar as definições do browser.

1. Certifique-se [cookies de terceiros estão ativados](#cookies) no browser. 
2. Utilizou um favorito, marcador ou ligação guardada para abrir o portal do Analytics? Iniciou sessão com credenciais diferentes das que utilizou quando guardou a ligação?
3. Tente utilizar uma janela do browser em privado/incognito (depois de fechar todas essas janelas). Terá de fornecer as suas credenciais. 
4. Abrir outra janela do browser (comum) e aceda a [Azure](https://portal.azure.com). Termine a sessão. Em seguida, abra a ligação e inicie a sessão com as credenciais corretas.
5. Os utilizadores do Edge ou do Internet Explorer também podem obter este erro quando as definições da zona de confiança não são suportadas.
   
    Certifique-se ambos [portal da análise](https://analytics.applicationinsights.io) e [portal do Azure Active Directory](https://portal.azure.com) estão na mesma zona de segurança:
   
   * No Internet Explorer, abra **opções da Internet**, **segurança**, **fidedigna sites**, **Sites**:
     
     ![Caixa de diálogo Opções da Internet, a adição de um site aos Sites fidedignos](./media/app-insights-analytics-troubleshooting/033.png)
     
     Na lista de Sites, se algum dos URLs seguintes estiverem incluídos, certifique-se de que também são incluídos outros:
     
     https://analytics.applicationinsights.io<br/>
     https://login.microsoftonline.com<br/>
     https://login.windows.net

## <a name="e-d"></a>404 ... Recurso não encontrado
![404 … recurso não encontrado](./media/app-insights-analytics-troubleshooting/040.png)

Recurso de aplicação foi eliminado do Application Insights e não já está disponível. Isto pode acontecer se tiver guardado o URL para a página de análise.

## <a name="e-e"></a>403 ... Sem autorização
![403 … não autorizado](./media/app-insights-analytics-troubleshooting/050.png)

Não tem permissão para abrir esta aplicação numa análise.

* Foi possível obter a ligação de outra pessoa? Peça-lhes para assegurar que está a ser o [leitores ou contribuintes para este grupo de recursos](app-insights-resources-roles-access-control.md).
* Foi possível guardar a ligação com credenciais diferentes? Abra o [portal do Azure](https://portal.azure.com), termine sessão e, em seguida, experimente esta ligação novamente, fornecer as credenciais corretas.

## <a name="html-storage"></a>403 ... Armazenamento HTML5
Utiliza o nosso portal HTML5 localStorage e sessionStorage.

* Chrome: Definições, privacidade, definições de conteúdo.
* Internet Explorer: Opções da Internet, separador Avançadas, segurança, ativar o armazenamento de DOM

![403... tente ativar armazenamento HTML5](./media/app-insights-analytics-troubleshooting/060.png)

## <a name="e-g"></a>404 ... Não foi encontrada uma subscrição
![404 ... Não foi encontrada uma subscrição](./media/app-insights-analytics-troubleshooting/070.png)

O URL é inválido. 

* Abra o recurso de aplicação no [portal do Application Insights](https://portal.azure.com). Em seguida, utilize o botão de análise.

## <a name="e-h"></a>404... página não existe
![404 ... Página não existe](./media/app-insights-analytics-troubleshooting/080.png)

O URL é inválido.

* Abra o recurso de aplicação no [portal do Application Insights](https://portal.azure.com). Em seguida, utilize o botão de análise.

## <a name="cookies"></a>Permitir cookies de terceiros
  Consulte [como desativar cookies de terceiros](http://www.digitalcitizen.life/how-disable-third-party-cookies-all-major-browsers), mas tenha em atenção, temos de **ativar** -los.


[!INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]

