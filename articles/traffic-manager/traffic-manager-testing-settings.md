---
title: "Verifique as definições do Gestor de tráfego do Azure | Microsoft Docs"
description: "Este artigo irá ajudá-lo a verificar as definições do Gestor de tráfego"
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: 2180b640-596e-4fb2-be59-23a38d606d12
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: kumud
ms.openlocfilehash: aadff1806a7cb22347283143563467366e857569
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="verify-traffic-manager-settings"></a>Verifique as definições do Gestor de tráfego

Para testar as definições do Gestor de tráfego, tem de ter vários clientes em várias localizações, a partir da qual pode executar os testes. Em seguida, coloque os pontos finais do perfil do Traffic Manager para baixo um de cada vez.

* Defina o valor de TTL do DNS baixa que as alterações propagarem rapidamente (por exemplo, 30 segundos).
* Conhece os endereços IP dos seus serviços em nuvem do Azure e Web sites no perfil de que está a testar.
* Utilize as ferramentas que permitem-lhe um nome DNS de resolver para um endereço IP e apresentar esse endereço.

Que está a verificar para ver que os nomes DNS de resolver para endereços IP dos pontos finais no seu perfil. Devem resolver os nomes de forma consistente com o método de encaminhamento de tráfego definido no perfil do Gestor de tráfego. Pode utilizar as ferramentas, como **nslookup** ou **aprofundar** para resolver nomes DNS.

Os exemplos seguintes ajudam a testar o perfil do Traffic Manager.

### <a name="check-traffic-manager-profile-using-nslookup-and-ipconfig-in-windows"></a>Verifique o perfil do Traffic Manager utilizar nslookup e ipconfig no Windows

1. Abra uma linha de comandos do Windows PowerShell ou um comando como administrador.
2. Tipo `ipconfig /flushdns` para esvaziar a cache de resolução DNS.
3. Digite `nslookup <your Traffic Manager domain name>`. Por exemplo, o seguinte comando verifica o nome de domínio com o prefixo *myapp.contoso*

        nslookup myapp.contoso.trafficmanager.net

    Resultado típico mostra as seguintes informações:

    + O nome DNS e endereço IP do servidor DNS que está a ser acedido para resolver este nome de domínio do Traffic Manager.
    + O nome de domínio do Gestor de tráfego que escreveu na linha de comandos após "nslookup" e o endereço IP para o qual resolve o domínio do Traffic Manager. O segundo endereço IP é o importantes para verificar. Deve corresponder a um público endereço IP virtual (VIP) para um dos serviços cloud ou Web sites no perfil do Gestor de tráfego que está a testar.

## <a name="how-to-test-the-failover-traffic-routing-method"></a>Como testar o método de encaminhamento de tráfego de ativação pós-falha

1. Deixe todos os pontos finais cópias de segurança.
2. Utilizar um único cliente, o pedido resolução de DNS para o nome de domínio da empresa utilizando nslookup ou um utilitário semelhante.
3. Certifique-se de que o endereço IP resolvido corresponde ao ponto final principal.
4. Traga a sua primário endpoint ou remova o ficheiro de monitorização para que esse gestor de tráfego pensa que a aplicação está desativado.
5. Aguarde que o DNS Time-to-Live (TTL) do perfil de Gestor de tráfego mais de dois minutos adicionais. Por exemplo, se o TTL de DNS é de 300 segundos (5 minutos), tem de aguardar durante sete minutos.
6. Esvaziar a DNS cliente pedido e a cache de resolução de DNS com nslookup. No Windows, pode esvaziar a cache DNS com o comando ipconfig /flushdns.
7. Certifique-se de que o endereço IP resolvido corresponde ao seu ponto final secundário.
8. Repetir o processo, desativar por sua vez a cada ponto final. Certifique-se de que o DNS devolve o endereço IP do próximo ponto de final na lista. Quando todos os pontos finais estão inativos, deve obter o endereço IP do ponto final principal novamente.

## <a name="how-to-test-the-weighted-traffic-routing-method"></a>Como testar o método de encaminhamento de tráfego ponderado

1. Deixe todos os pontos finais cópias de segurança.
2. Utilizar um único cliente, o pedido resolução de DNS para o nome de domínio da empresa utilizando nslookup ou um utilitário semelhante.
3. Certifique-se de que o endereço IP resolvido corresponde a um dos seus pontos finais.
4. Esvaziar a cache do cliente DNS e repita os passos 2 e 3 para cada ponto final. Deverá ver endereços IP diferentes para cada um dos seus pontos finais.

## <a name="how-to-test-the-performance-traffic-routing-method"></a>Como testar o método de encaminhamento de tráfego de desempenho

Para testar efetivamente um método de encaminhamento de tráfego de desempenho, tem de ter os clientes localizados em diferentes partes o mundo. Pode criar os clientes em diferentes regiões do Azure que podem ser utilizadas para testar os serviços. Se tiver uma rede global, pode iniciar sessão para os clientes nas outras partes do mundo remotamente e executar os testes a partir daí.

Em alternativa, existem livre baseada na web pesquisa de DNS e aprofundar serviços disponíveis. Algumas destas ferramentas dão-lhe a capacidade de verificar a resolução do nome DNS de várias localizações em todo o mundo. Efetue uma pesquisa no "Pesquisa de DNS" para obter exemplos. Serviços de terceiros como Gomez ou da keynote //Build podem ser utilizados para confirmar que os perfis de estão a distribuir o tráfego, conforme esperado.

## <a name="next-steps"></a>Passos seguintes

* [Sobre os métodos de encaminhamento de tráfego do Gestor de tráfego](traffic-manager-routing-methods.md)
* [Considerações de desempenho para o Gestor de Tráfego](traffic-manager-performance-considerations.md)
* [Resolução de problemas do estado degradado do Gestor de Tráfego](traffic-manager-troubleshooting-degraded.md)
