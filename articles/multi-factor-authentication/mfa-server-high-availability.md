---
title: Configurar o servidor MFA do Azure para elevada disponibilidade | Microsoft Docs
description: "Implemente várias instâncias do Azure multi-factor Authentication Server nas configurações que fornecem elevada disponibilidade."
services: multi-factor-authentication
keywords: Azure MFA,
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: 
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2017
ms.author: joflore
ms.reviewer: richagi
ms.custom: it-pro
ms.openlocfilehash: f22e0df75830a048e535384de8a3eec51bf91fd8
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="configure-azure-multi-factor-authentication-server-for-high-availability"></a>Configurar o servidor do Azure multi-factor Authentication para elevada disponibilidade

Para alcançar a elevada disponibilidade à sua implementação do servidor do MFA do Azure, terá de implementar vários servidores MFA. Esta secção fornece informações sobre uma estrutura com balanceamento de carga para alcançar os destinos de elevada disponibilidade em que a implementação de servidor de MFS do Azure.

## <a name="mfa-server-overview"></a>Descrição geral do servidor MFA

A arquitetura de serviço do servidor MFA do Azure é composta por vários componentes conforme mostrado no diagrama seguinte:

 ![Arquitetura de servidor MFA](./media/mfa-server-high-availability/mfa-ha-architecture.png)

Um servidor MFA é um servidor do Windows que tem instalado o software de Azure multi-factor Authentication. A instância do servidor MFA tem de ser ativada pelo serviço de MFA do Azure para a função. Mais de um servidor MFA pode ser instalado no local.

O primeiro servidor MFA que é instalado é o mestre de servidor MFA após a ativação pelo serviço de MFA do Azure por predefinição. O servidor MFA principal tem uma cópia da base de dados PhoneFactor.pfdata gravável. As instalações subsequentes de instâncias de servidor MFA são conhecidas como slaves. Slaves o MFA tem uma cópia só de leitura replicada da base de dados PhoneFactor.pfdata. Servidores MFA replicam informações utilizando a chamada de procedimento remoto (RPC). Todos os servidores de MFA tem coletivamente estar associado a um domínio ou autónomo para replicar informações.

Mestre de MFA e multisservidor MFA servidores comunicam com o serviço de MFA quando é necessária a autenticação de dois fatores. Por exemplo, quando um utilizador tenta obter acesso a uma aplicação que exija a autenticação de dois fatores, o utilizador será primeiro autenticado por um fornecedor de identidade, por exemplo, do Active Directory (AD).

Após a autenticação com êxito com o AD, o servidor MFA irá comunicar com o serviço de MFA. O servidor MFA aguarda a notificação do serviço de MFA para permitir ou negar o acesso do utilizador para a aplicação.

Se o servidor mestre do MFA ficar offline, as autenticações ainda podem ser processadas, mas não não possível processar operações que necessitem de alterações para a base de dados do MFA. (Os exemplos incluem: a adição de utilizadores, self-service as alterações PIN e alterar as informações de utilizador)

## <a name="deployment"></a>Implementação

Considere os seguintes pontos importantes para o servidor MFA do Azure e os respetivos componentes relacionados com o balanceamento de carga.

* **Utilizar o padrão RADIUS para alcançarem elevada disponibilidade**. Se estiver a utilizar servidores de MFA do Azure como servidores RADIUS, pode configurar um servidor MFA potencialmente como um destino de autenticação RADIUS primário e outros servidores de MFA do Azure como destinos de autenticação secundária. No entanto, este método para alcançarem elevada disponibilidade não pode ser prático porque tem de aguardar um período de limite de tempo para ocorrer quando a autenticação falha no destino de autenticação primária antes que pode ser autenticados contra o destino de autenticação secundária. É mais eficiente para equilibrar o tráfego RADIUS entre o cliente RADIUS e servidores RADIUS (neste caso, os servidores de MFA do Azure que funcionam como servidores RADIUS), para que possa configurar clientes RADIUS com um único URL podem apontar para.
* **Precisa de promover manualmente MFA slaves**. Se o servidor mestre do MFA do Azure ficar offline, os servidores de MFA do Azure secundário continuar a processar pedidos MFA. No entanto, até que um servidor mestre do MFA está disponível, administradores não podem adicionar utilizadores ou modificar as definições da MFA, e os utilizadores não podem efetuar alterações com o portal de utilizador. Promover um subordinado MFA para a função de mestre é sempre um processo manual.
* **Separability dos componentes**. O servidor MFA do Azure é composto por vários componentes que podem ser instalados na mesma instância do Windows Server ou em várias instâncias. Estes componentes incluem o Portal de utilizador, serviço Web da aplicação móvel e o adaptador AD FS (agente). Este separability torna possível utilizar o Proxy de aplicações Web para publicar o Portal de utilizador e o servidor de Web de aplicação móvel a partir da rede de perímetro. Este tipo de configuração adiciona a segurança geral da estrutura, conforme mostrado no diagrama seguinte. O Portal de utilizador de MFA e o servidor de Web de aplicação móvel também podem ser implementado em configurações de balanceamento de carga HA.

   ![Servidor MFA com uma rede de perímetro](./media/mfa-server-high-availability/mfasecurity.png)

* **Palavra-passe Monouso (OTP) ao longo do SMS (aka SMS unidirecional) requer a utilização de sessões temporária se tráfego com balanceamento de carga**. SMS unidirecional é uma opção de autenticação que faz com que o servidor MFA enviar uma mensagem de texto que contém uma OTP dos utilizadores. O utilizador introduz o OTP, numa janela de linha de comandos para completar o desafio MFA. Se tiver o balanceamento de carga servidores de MFA do Azure, o mesmo servidor que serviu o pedido de autenticação inicial tem de ser o servidor que recebe a mensagem OTP do utilizador; Se o outro servidor MFA recebe a resposta OTP, o desafio de autenticação falha. Para obter mais informações, consulte [um tempo de palavra-passe através de SMS ao servidor MFA do Azure](https://blogs.technet.microsoft.com/enterprisemobility/2015/03/02/one-time-password-over-sms-added-to-azure-mfa-server).
* **Implementações com balanceamento de carga do Portal de utilizador e do serviço Web da aplicação móvel requerem sessões temporária**. Se lhe é balanceamento de carga do Portal de utilizador de MFA e o serviço Web da aplicação móvel, cada sessão tem de permanecer no mesmo servidor.

## <a name="high-availability-deployment"></a>Implementação de elevada disponibilidade

O diagrama seguinte mostra uma implementação de balanceamento de carga HA completa do MFA do Azure e os respetivos componentes, juntamente com o AD FS para referência.

 ![MFA Server HA implementação do Azure](./media/mfa-server-high-availability/mfa-ha-deployment.png)

Tenha em atenção os seguintes itens para a área proporcionalmente numerado do diagrama anterior.

1. Os servidores de MFA do Azure dois (MFA1 e MFA2) com balanceamento de carga (mfaapp.contoso.com) e estão configurados para utilizar uma porta estática (4443) para replicar a base de dados PhoneFactor.pfdata. O Web Service SDK está instalado em cada um do servidor MFA para ativar a comunicação através da porta TCP 443 com os servidores do ADFS. Os servidores MFA são implementados numa configuração de balanceamento de carga sem monitorização de estado. No entanto, se pretendesse utilizar OTP através de SMS, tem de utilizar o balanceamento de carga com monitorização de estado.
   ![Servidor MFA do Azure - o servidor de aplicações HA](./media/mfa-server-high-availability/mfaapp.png)

   > [!NOTE]
   > Uma vez RPC utiliza portas dinâmicas, não se recomenda para abrir as firewalls até o intervalo de portas dinâmicas RPC, potencialmente, pode utilizar. Se tiver uma firewall **entre** seus servidores de aplicações do MFA, deve configurar o servidor MFA para comunicar uma porta estática para o tráfego de replicação entre multisservidor e de servidores principais e abrir essa porta na firewall. Pode forçar a porta estática através da criação de um valor de registo DWORD em ```HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Positive Networks\PhoneFactor``` chamado ```Pfsvc_ncan_ip_tcp_port``` e definindo o valor como uma porta estática disponível. Ligações sempre são iniciadas pelo serviço secundário MFA servidores ao principal, a porta estática só é necessária no mestre de, mas uma vez que pode promover um subordinado para ser o mestre em qualquer altura, deve definir a porta estática em todos os servidores de MFA.

2. Os dois servidores de aplicação de Mobile do MFA do Portal de utilizador (MAS1 de cópia de segurança de MFA e MAS2 de cópia de segurança de MFA) estão com de balanceamento de carga um **com monitorização de estado** configuration (mfa.contoso.com). Recuperar que temporária sessões são um requisito para o Portal de utilizador de MFA e o serviço de aplicação móvel de balanceamento de carga.
   ![Servidor do MFA do Azure - Portal de utilizador e do serviço de aplicações móveis HA](./media/mfa-server-high-availability/mfaportal.png)
3. O farm do servidor do ADFS está balanceados em carga e publicado na Internet através de proxies ADFS com balanceamento de carga na rede de perímetro. Cada servidor do ADFS utiliza o agente de AD FS para comunicar com os servidores de MFA do Azure com um único com balanceamento de carga URL (mfaapp.contoso.com) através da porta TCP 443.

## <a name="next-steps"></a>Passos seguintes

* [Instalar e configurar o servidor MFA do Azure](multi-factor-authentication-get-started-server.md)
