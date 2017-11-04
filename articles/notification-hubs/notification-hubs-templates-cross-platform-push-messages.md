---
title: Modelos
description: "Este tópico explica os modelos para os hubs de notificação do Azure."
services: notification-hubs
documentationcenter: .net
author: ysxu
manager: erikre
editor: 
ms.assetid: a41897bb-5b4b-48b2-bfd5-2e3c65edc37e
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 06/29/2016
ms.author: yuaxu
ms.openlocfilehash: 1ca24a4bf08ecdbe1c1e47a931613144309a04a9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="templates"></a>Modelos
## <a name="overview"></a>Descrição geral
Modelos de ativar uma aplicação de cliente especificar o formato exato de notificações que pretende receber. Utilizar modelos, uma aplicação pode tenha em consideração várias vantagens diferentes, incluindo o seguinte:

* Um back-end de plataforma desconhecidas
* Notificações personalizadas
* Independência de versão do cliente
* Localização fácil

Esta secção fornece dois exemplos aprofundados sobre como utilizar os modelos para enviar notificações de plataforma agnóstico direcionada para todos os seus dispositivos em plataformas e para personalizar a notificação de difusão para cada dispositivo.

## <a name="using-templates-cross-platform"></a>Utilizar modelos de plataforma
É a forma de padrão para enviar notificações push para enviar em cada notificação que está a ser enviados um payload específico para serviços de notificação de plataforma (WNS, APNS). Por exemplo, para enviar um alerta para APNS, o payload é um objeto Json com o seguinte formato:

    {"aps": {"alert" : "Hello!" }}

Para enviar uma mensagem de alerta semelhante uma aplicação da loja Windows, o payload XML é:

    <toast>
      <visual>
        <binding template=\"ToastText01\">
          <text id=\"1\">Hello!</text>
        </binding>
      </visual>
    </toast>

Pode criar payloads semelhantes para MPNS (Windows Phone) e plataformas (Android) do GCM.

Este requisito força o back-end da aplicação para produzir payloads diferentes para cada plataforma e eficaz faz com que o back-end responsável por parte da camada de apresentação da aplicação. Algumas questões incluem a localização e esquemas gráficas (especialmente para aplicações da loja Windows que inclua as notificações para vários tipos de mosaicos).

A funcionalidade de modelo de Hubs de notificação permite que uma aplicação de cliente criar registos especiais, denominados registos de modelo, que incluem, além do conjunto de etiquetas, um modelo. A funcionalidade de modelo de Hubs de notificação permite que uma aplicação de cliente associar os dispositivos com modelos se estiver a trabalhar com instalações (preferidas) ou registos. Tendo em conta os exemplos de payload anterior, as informações de independentes da plataforma apenas se a mensagem de alerta real (Olá!). Um modelo é um conjunto de instruções para o Hub de notificação sobre a formatar uma mensagem de independentes da plataforma para o registo da aplicação desse cliente específico. No exemplo anterior, a mensagem independentes da plataforma é uma propriedade de único: **mensagem = Olá!**.

A imagem seguinte ilustra o processo de acima:

![](./media/notification-hubs-templates/notification-hubs-hello.png)

O modelo para o registo de aplicação de cliente iOS é o seguinte:

    {"aps": {"alert": "$(message)"}}

O modelo correspondente para a aplicação de cliente da loja Windows é:

    <toast>
        <visual>
            <binding template=\"ToastText01\">
                <text id=\"1\">$(message)</text>
            </binding>
        </visual>
    </toast>

Tenha em atenção que a mensagem real é substituída pelos $ a expressão (mensagem). Esta expressão dá instruções ao Notification Hub, sempre que envia uma mensagem para este registo específico, para criar uma mensagem que se segue e comutadores no valor de comuns.

Se estiver a trabalhar com o modelo de instalação, a chave de "modelos" de instalação contém um JSON de vários modelos. Se estiver a trabalhar com o modelo de registo, a aplicação cliente pode criar vários registos para utilizar vários modelos; Por exemplo, um modelo para mensagens de alerta e um modelo para atualizações de mosaico. Aplicações de cliente também podem combinar registos nativos (registos com nenhum modelo) e registos de modelo.

O Hub de notificação envia uma notificação para cada modelo sem considerar se pertencem à mesma aplicação de cliente. Este comportamento pode ser utilizado para traduzir notificações independentes da plataforma em mais de notificações. Por exemplo, a mesma mensagem independentes plataforma para o Hub de notificação possa ser totalmente integrada convertida num alerta de alerta e uma atualização de mosaico, sem necessidade de back-end a ter em consideração-lo. Tenha em atenção que algumas plataformas (por exemplo, iOS) poderão fechar várias notificações ao mesmo dispositivo se serem enviados num curto período de tempo.

## <a name="using-templates-for-personalization"></a>Utilizar modelos para personalização
Outra vantagem em utilizar modelos é a capacidade de utilizar os Notification Hubs para efetuar a personalização por registo de notificações. Por exemplo, considere uma aplicação de Meteorologia que apresenta um mosaico com as condições de Meteorologia numa localização específica. Um utilizador pode escolher entre graus Celsius ou Fahrenheit e uma previsão única ou de cinco dias. Utilizar modelos, pode registar cada instalação da aplicação de cliente para o formato necessário (1 dia Celsius, de 1 dia Fahrenheit, 5 dias Celsius, 5 dias Fahrenheit), e tem de enviar um único que contém todas as informações necessárias para preencher esses modelos de back-end (por exemplo, um previsão com graus Celsius e Fahrenheit de cinco dias).

O modelo para a previsão de um dia com Celsius temperatures é o seguinte:

    <tile>
      <visual>
        <binding template="TileWideSmallImageAndText04">
          <image id="1" src="$(day1_image)" alt="alt text"/>
          <text id="1">Seattle, WA</text>
          <text id="2">$(day1_tempC)</text>
        </binding>  
      </visual>
    </tile>

A mensagem enviada para o Hub de notificação contém as seguintes propriedades:

<table border="1">

<tr><td>day1_image</td><td>day2_image</td><td>day3_image</td><td>day4_image</td><td>day5_image</td></tr>

<tr><td>day1_tempC</td><td>day2_tempC</td><td>day3_tempC</td><td>day4_tempC</td><td>day5_tempC</td></tr>

<tr><td>day1_tempF</td><td>day2_tempF</td><td>day3_tempF</td><td>day4_tempF</td><td>day5_tempF</td></tr>
</table><br/>

Ao utilizar este padrão, o back-end envia apenas uma única mensagem sem ter de armazenar as opções de personalização específicos para os utilizadores das aplicações. A imagem seguinte ilustra este cenário:

![](./media/notification-hubs-templates/notification-hubs-registration-specific.png)

## <a name="how-to-register-templates"></a>Como registar modelos
Para registar modelos utilizando o modelo de instalação (preferencial) ou o modelo de registo, consulte [registo gestão](notification-hubs-push-notification-registration-management.md).

## <a name="template-expression-language"></a>Idioma da expressão de modelo
Os modelos são limitados para formatos de documento XML ou JSON. Além disso, apenas pode colocar expressões em locais específicos; Por exemplo, atributos de nó ou valores de XML, cadeia de valores de propriedade para JSON.

A tabela seguinte mostra o idioma permitido em modelos:

| expressão | Descrição |
| --- | --- |
| $(prop) |Referência a uma propriedade de evento com o nome fornecido. Os nomes de propriedades não são maiúsculas e minúsculas. Esta expressão é resolvido para o valor da propriedade texto ou para uma cadeia vazia se a propriedade não está presente. |
| $(prop, n) |Conforme apresentado acima, mas o texto é explicitamente recortado pelo n carateres, por exemplo $(título, 20) clips o conteúdo da propriedade de título em 20 carateres. |
| . (prop, n) |Conforme apresentado acima, mas o texto é com o sufixo reticências conforme está anexada. O tamanho total da cadeia de recortados e o sufixo não pode exceder n carateres. . (title, 20) com uma propriedade de entrada "Este é a linha de título" resultados nas **este é o título...** |
| %(prop) |Semelhante $(name) exceto a que o resultado é codificado em URI. |
| #(prop) |Utilizado em modelos JSON (por exemplo, para iOS e Android modelos).<br><br>Esta função funciona exatamente igual $(prop) anteriormente especificado, exceto quando utilizado em modelos JSON (por exemplo, modelos de Apple). Neste caso, se esta função não está rodeada por "{','}" (por exemplo, 'myJsonProperty': '(nome) #'), e avalia para um número no formato de Javascript, por exemplo, regexp: (0 &#124; (&#91; 1 e 9 &#93; &#91; 0-9 &#93;;*))(\. &#91; 0-9 &#93; +)? ((i &#124; I) (+ &#124;-)? &#91; 0-9 &#93; +)?, em seguida, a saída JSON é um número.<br><br>Por exemplo, ' destaque: '#(nome)' torna-se 'destaque': 40 (e não '40'). |
| 'text' ou "text" |Um literal. As literais contenham arbitrário texto entre aspas única ou duplos. |
| Expr1 + expr2 |O operador de concatenação associar duas expressões numa cadeia única. |

As expressões podem ser qualquer uma dos formulários anteriores.

Quando utilizar concatenação, a expressão completa tem de ser rodeado com {}. Por exemplo, {$(prop) + '-' + $(prop2)}. |

Por exemplo, o seguinte não é um modelo XML válido:

    <tile>
      <visual>
        <binding $(property)>
          <text id="1">Seattle, WA</text>
        </binding>  
      </visual>
    </tile>


Como explicado anteriormente, quando utilizar concatenação, expressões devem ser moldadas numa chavetas. Por exemplo:

    <tile>
      <visual>
        <binding template="ToastText01">
          <text id="1">{'Hi, ' + $(name)}</text>
        </binding>  
      </visual>
    </tile>

