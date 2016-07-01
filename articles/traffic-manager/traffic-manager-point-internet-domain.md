<properties
   pageTitle="Apontar um domínio de Internet da empresa para um domínio do Traffic Manager | Microsoft Azure"
   description="Este artigo ajuda-o a apontar o nome de domínio da sua empresa para um nome de domínio do Traffic Manager."
   services="traffic-manager"
   documentationCenter=""
   authors="joaoma"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/17/2016"
   ms.author="joaoma" />

# Apontar um domínio de Internet da empresa para um domínio do Traffic Manager do Azure

Para apontar o nome de domínio da sua empresa para um nome de domínio do Traffic Manager, modifique o registo de recursos DNS no servidor DNS de Internet para utilizar o tipo de registo CNAME, que mapeia o nome de domínio da empresa para o nome de domínio do seu perfil do Traffic Manager. Pode ver o nome de domínio do Traffic Manager na secção **Geral** na página Configuração do perfil do Traffic Manager.

Por exemplo, para apontar o nome de domínio da empresa www.contoso.com para o nome de domínio do Traffic Manager contoso.trafficmanager.net, atualize o registo de recursos DNS para o seguinte:

    www.contoso.com IN CNAME contoso.trafficmanager.net

Todos os pedidos de tráfego para *www.contoso.com* serão agora direcionados para *contoso.trafficmanager.net*.

>[AZURE.IMPORTANT] Não é possível apontar um domínio de segundo nível, como *contoso.com*, para o domínio do Traffic Manager. Esta é uma limitação do protocolo DNS, que não permite registos CNAME para nomes de domínio de segundo nível.

## Passos seguintes

[Métodos de encaminhamento do Traffic Manager](traffic-manager-routing-methods.md)

[Traffic Manager – Desativar, ativar ou eliminar um perfil](disable-enable-or-delete-a-profile.md)

[Traffic Manager – Desativar ou ativar um ponto final](disable-or-enable-an-endpoint.md)



<!--HONumber=Jun16_HO2-->


