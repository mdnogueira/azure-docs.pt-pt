---
title: Gateway de Ambiente de Trabalho Remoto e Servidor Multi-Factor Authentication do Azure com o RADIUS
description: "Esta é a página do Multi-Factor Authentication do Azure que irá ajudar a implementar o Gateway de Ambiente de Trabalho Remoto (RD) e o Servidor Multi-Factor Authentication do Azure com o RADIUS."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: curtand
ms.assetid: f2354ac4-a3a7-48e5-a86d-84a9e5682b42
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/15/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 3b14925f41138904aa10a172f83dffa3c6662700


---
# <a name="remote-desktop-gateway-and-azure-multi-factor-authentication-server-using-radius"></a>Gateway de Ambiente de Trabalho Remoto e Servidor Multi-Factor Authentication do Azure com o RADIUS
Em muitos casos, o Gateway de Ambiente de Trabalho Remoto utiliza o NPS local para autenticar os utilizadores. Este documento descreve como encaminhar um pedido RADIUS fora do Gateway de Ambiente de Trabalho Remoto (através do NPS local) para o Servidor Multi-Factor Authentication.

O Servidor Multi-Factor Authentication deve ser instalado num servidor separado, o qual irá utilizar o proxy no pedido RADIUS de volta para o NPS no Servidor de Gateway de Ambiente de Trabalho Remoto. Depois de o NPS validar o nome de utilizador e a palavra-passe, irá devolver uma resposta ao Servidor Multi-Factor Authentication que efetua o segundo fator de autenticação antes de devolver um resultado ao gateway.

## <a name="configure-the-rd-gateway"></a>Configurar o Gateway de RD
O Gateway de RD tem de ser configurado para enviar a autenticação RADIUS para um Servidor Multi-Factor Authentication do Azure. Depois de o Gateway de RD ter sido instalado, configurado e estar a funcionar, vá para as propriedades do Gateway de RD. Vá para o separador Arquivo de CAPs de RD e altere-o para utilizar um servidor Central com NPS em vez do servidor Local com NPS. Adicione um ou mais Servidores Multi-Factor Authentication do Azure como servidores RADIUS e especifique um segredo partilhado para cada servidor.

## <a name="configure-nps"></a>Configurar o NPS
O Gateway de RD utiliza o NPS para enviar o pedido RADIUS para o Multi-Factor Authentication do Azure. É necessário alterar o tempo limite para impedir que o Gateway de RD o exceda antes de a autenticação multifator ser concluída. Utilize o procedimento seguinte para configurar o NPS.

1. No NPS, expanda o menu Clientes e Servidor RADIUS na coluna esquerda e clique em Grupos de Servidores RADIUS Remotos. Vá para as propriedades do GRUPO DE SERVIDORES DO GATEWAY TS. Edite o servidor(es) RADIUS apresentado(s) e vá para o separador Balanceamento de Carga. Altere o "Número de segundos sem resposta antes de o pedido ser cancelado" e o "Número de segundos entre pedidos quando o servidor é identificado como não disponível" para 30-60 segundos. Clique no separador Autenticação/Conta e certifique-se de que as portas RADIUS especificadas correspondem às portas em que o Servidor Multi-Factor Authentication irá escutar.
2. O NPS também tem de ser configurado para receber as autenticações RADIUS novamente do Servidor Multi-Factor Authentication do Azure. Clique em Clientes RADIUS no menu esquerdo. Adicione o Servidor Multi-Factor Authentication do Azure como cliente RADIUS. Escolha um nome amigável e especifique um segredo partilhado.
3. Expanda a secção Políticas na navegação esquerda e clique em Políticas de Pedido de Ligação. Deve conter uma Política de Pedido de Ligação denominada POLÍTICA DE AUTORIZAÇÃO DO GATEWAY TS que foi criada quando o Gateway de RD foi configurado. Esta política encaminha os pedidos RADIUS para o Servidor Multi-Factor Authentication.
4. Copie esta política para criar uma nova. Na nova política, adicione uma condição que corresponda ao Nome Amigável do Cliente com o nome amigável definido no passo 2 acima para o cliente RADIUS do Servidor Multi-Factor Authentication do Azure. Altere o Fornecedor de Autenticação para Computador Local. Esta política assegura que, quando é recebido um pedido RADIUS do Servidor Multi-Factor Authentication do Azure, a autenticação ocorre localmente em vez de enviar um pedido RADIUS novamente para o Servidor Multi-Factor Authentication do Azure, o que poderia resultar numa condição de ciclo. Para impedir a condição de ciclo, esta nova política tem de ser ordenada ACIMA da política original que encaminha para o Servidor Multi-Factor Authentication.

## <a name="configure-azure-multi-factor-authentication"></a>Configurar o Multi-Factor Authentication do Azure
- - -
O Servidor Multi-Factor Authentication do Azure está configurado como um proxy RADIUS entre o Gateway de RD e o NPS.  Deve ser instalado num servidor associado a um domínio separado do servidor do Gateway de RD. Utilize o seguinte procedimento para configurar o Servidor Multi-Factor Authentication do Azure.

1. Abra o Servidor Multi-Factor Authentication do Azure e clique no ícone Autenticação RADIUS. Selecione a caixa de verificação Ativar autenticação RADIUS.
2. No separador Clientes, certifique-se de que as portas correspondem ao que está configurado no NPS e clique no botão Adicionar... Adicione o endereço IP do servidor do Gateway de RD, o nome da aplicação (opcional) e um segredo partilhado. O segredo partilhado terá de ser o mesmo no Servidor Multi-Factor Authentication do Azure e no Gateway de RD.
3. Clique no separador Destino e selecione o botão de opção Servidores RADIUS.
4. Clique no botão Adicionar... Introduza o endereço IP, o segredo partilhado e as portas do servidor NPS. A menos que esteja a utilizar um NPS central, o cliente RADIUS e o destino RADIUS serão os mesmos. O segredo partilhado tem de corresponder a uma configuração na secção Cliente RADIUS do servidor NPS.

![Autenticação Radius](./media/multi-factor-authentication-get-started-server-rdg/radius.png)




<!--HONumber=Dec16_HO1-->


