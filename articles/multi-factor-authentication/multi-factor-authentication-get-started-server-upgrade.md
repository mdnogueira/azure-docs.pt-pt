---
title: Atualizar o PhoneFactor para o Servidor de MFA do Azure | Microsoft Docs
description: "Começar a utilizar o Servidor de MFA do Azure e quando atualizar a partir do phonefactor agent mais antigo."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 42838ff7-bdf2-4d06-bacc-b3839a00cd76
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/06/2017
ms.author: kgremban
ms.openlocfilehash: 7ab7e693909f807781744ae53eed75d425096590
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="upgrade-the-phonefactor-agent-to-azure-multi-factor-authentication-server"></a>Atualizar o PhoneFactor Agent para o Servidor Multi-Factor Authentication do Azure
Para atualizar o PhoneFactor Agent v5.x ou o mais antigo para o Servidor Multi-Factor Authentication do Azure, desinstale o PhoneFactor Agent e os componentes afiliados primeiro. Em seguida, o Servidor Multi-Factor Authentication e os respetivos componentes afiliados podem ser instalados.

## <a name="uninstall-the-phonefactor-agent"></a>Desinstalar o PhoneFactor Agent

1. Primeiro, faça uma cópia de segurança do ficheiro de dados do PhoneFactor. A localização de instalação predefinida é C:\Programas\PhoneFactor\Data\Phonefactor.pfdata.

2. Se o Portal de Utilizador estiver instalado:
  1. Navegue para a pasta de instalação e faça uma cópia de segurança do ficheiro web.config. A localização de instalação predefinida é C:\inetpub\wwwroot\PhoneFactor.

  2. Se tiver adicionado temas personalizados ao portal, faça uma cópia de segurança da pasta personalizada abaixo do diretório C:\inetpub\wwwroot\PhoneFactor\App_Themes.

  3. Desinstale o Portal de Utilizador através do PhoneFactor Agent (disponível apenas se estiver instalado no mesmo servidor do PhoneFactor Agent) ou através dos Programas e Funcionalidades do Windows.

3. Se o Serviço Web da Aplicação Móvel estiver instalado:

  1. Vá para a pasta de instalação e faça uma cópia de segurança do ficheiro web.config. A localização de instalação predefinida é C:\inetpub\wwwroot\PhoneFactorPhoneAppWebService.

  2. Desinstale o Serviço Web da Aplicação Móvel através dos Programas e Funcionalidades do Windows.

4. Se o SDK do Serviço Web estiver instalado, desinstale-o através do PhoneFactor Agent ou dos Programas e Funcionalidades do Windows.

5. Desinstale o PhoneFactor Agent através dos Programas e Funcionalidades do Windows.

## <a name="install-the-multi-factor-authentication-server"></a>Instale o Servidor Multi-Factor Authentication

O caminho de instalação é escolhido a partir do registo da instalação do PhoneFactor Agent anterior, pelo que deve instalar na mesma localização (por exemplo, C:\Programas\PhoneFactor). As novas instalações têm um caminho de instalação predefinido diferente (por exemplo, C:\Programas\Multi-Factor Authentication Server). O ficheiro de dados deixado pelo PhoneFactor Agent anterior deve ser atualizado durante a instalação, pelo que os seus utilizadores e definições devem permanecer depois de instalar o novo Servidor Multi-Factor Authentication.

1. Se lhe for solicitado, ative o Servidor Multi-Factor Authentication e certifique-se de que é atribuído ao grupo de replicação correto.

2. Se o SDK do Serviço Web tiver sido instalado anteriormente, instale o novo SDK do Serviço Web através da Interface de Utilizador do Servidor Multi-Factor Authentication.

  O nome do diretório virtual predefinido é agora **MultiFactorAuthWebServiceSdk**, em vez de **PhoneFactorWebServiceSdk**. Se pretender utilizar o nome anterior, tem de alterar o nome do diretório virtual durante a instalação. Caso contrário, se permitir que a instalação utilize o novo nome predefinido, tem de alterar o URL em todas as aplicações que referenciam o SDK do Serviço Web, (como o Portal de Utilizador e o Serviço Web da Aplicação Móvel), para apontarem para a localização correta.

3. Se o Portal de Utilizador tiver sido instalado anteriormente no Servidor do PhoneFactor Agent, instale o novo Portal de Utilizador do Multi-Factor Authentication através da Interface de Utilizador do Servidor Multi-Factor Authentication.

  O nome do diretório virtual predefinido é agora **MultiFactorAuth**, em vez de **PhoneFactor**. Se pretender utilizar o nome anterior, tem de alterar o nome do diretório virtual durante a instalação. Caso contrário, se permitir que a instalação utilize o novo nome predefinido, deve clicar no ícone do Portal de Utilizador no Servidor Multi-Factor Authentication e atualizar o URL do Portal de Utilizador no separador Definições.

4. Se o Portal de Utilizador e/ou o Serviço Web da Aplicação Móvel tiverem sido instalados anteriormente num servidor diferente do PhoneFactor Agent:

  1. Vá para a localização de instalação (por exemplo, C:\Programas\PhoneFactor) e copie um ou mais instaladores adequados para o outro servidor. Existem instaladores de 32 e 64 bits para o Portal de Utilizador e o Serviço Web da Aplicação Móvel. São denominados MultiFactorAuthenticationUserPortalSetupXX.msi e MultiFactorAuthenticationMobileAppWebServiceSetupXX.msi.

  2. Para instalar o Portal de Utilizador no servidor Web, abra uma linha de comandos como administrador e execute MultiFactorAuthenticationUserPortalSetupXX.msi.

    O nome do diretório virtual predefinido é agora **MultiFactorAuth**, em vez de **PhoneFactor**. Se pretender utilizar o nome anterior, tem de alterar o nome do diretório virtual durante a instalação. Caso contrário, se permitir que a instalação utilize o novo nome predefinido, deve clicar no ícone do Portal de Utilizador no Servidor Multi-Factor Authentication e atualizar o URL do Portal de Utilizador no separador Definições. Os utilizadores existentes precisam de ser informados sobre o novo URL.

  3. Vá para a localização de instalação do Portal de Utilizador (por exemplo, C:\inetpub\wwwroot\MultiFactorAuth) e edite o ficheiro web.config. Copie os valores nas secções appSettings e applicationSettings a partir do ficheiro web.config original cuja cópia de segurança foi feita antes da atualização para o novo ficheiro web.config. Se o novo nome do diretório virtual predefinido foi mantido quando instalou o SDK do Serviço Web, altere o URL na secção applicationSettings para apontar para a localização correta. Se quaisquer outras predefinições foram alteradas no ficheiro web.config anterior, aplique as mesmas alterações ao novo ficheiro web.config.

  4. Para instalar o Serviço Web da Aplicação Móvel no servidor Web, abra uma linha de comandos como administrador e execute MultiFactorAuthenticationMobileAppWebServiceSetupXX.msi.

    O nome do diretório virtual predefinido é agora **MultiFactorAuthMobileAppWebService**, em vez de **PhoneFactorPhoneAppWebService**. Se pretender utilizar o nome anterior, tem de alterar o nome do diretório virtual durante a instalação. Poderá pretender escolher um nome mais curto para que seja mais fácil para os utilizadores finais o escreverem nos respetivos dispositivos móveis. Caso contrário, se permitir que a instalação utilize o novo nome predefinido, deve clicar no ícone da Aplicação Móvel no Servidor Multi-Factor Authentication e atualizar o URL do Serviço Web da Aplicação Móvel.

  5. Vá para a localização de instalação do Serviço Web da Aplicação Móvel (por exemplo, C:\inetpub\wwwroot\MultiFactorAuthMobileAppWebService) e edite o ficheiro web.config. Copie os valores nas secções appSettings e applicationSettings a partir do ficheiro web.config original cuja cópia de segurança foi feita antes da atualização para o novo ficheiro web.config. Se o novo nome do diretório virtual predefinido foi mantido quando instalou o SDK do Serviço Web, altere o URL na secção applicationSettings para apontar para a localização correta. Se quaisquer outras predefinições foram alteradas no ficheiro web.config anterior, aplique as mesmas alterações ao novo ficheiro web.config.

## <a name="next-steps"></a>Passos seguintes

- [Instale o portal de utilizadores](multi-factor-authentication-get-started-portal.md) do Servidor Multi-Factor Authentication do Azure.

- [Configurar a Autenticação do Windows](multi-factor-authentication-get-started-server-windows.md) nas suas aplicações. 
