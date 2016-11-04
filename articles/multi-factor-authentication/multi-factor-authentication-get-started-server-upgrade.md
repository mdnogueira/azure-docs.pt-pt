---
title: Atualizar o PhoneFactor Agent para o Servidor Multi-Factor Authentication do Azure
description: Este documento descreve como começar a utilizar o Servidor MFA do Azure e como atualizar a partir de um phonefactor agent mais antigo.
services: multi-factor-authentication
documentationcenter: ''
author: kgremban
manager: femila
editor: curtland

ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/04/2016
ms.author: kgremban

---
# Atualizar o PhoneFactor Agent para o Servidor Multi-Factor Authentication do Azure
Atualizar do PhoneFactor Agent v5.x ou anterior no Servidor Multi-Factor Authentication do Azure requer a desinstalação do PhoneFactor Agent e dos componentes afiliados antes de o Servidor Multi-Factor Authentication e os respetivos componentes afiliados poderem ser instalados.

## Para atualizar o PhoneFactor Agent para o Servidor Multi-Factor Authentication do Azure
<ol>
<li>Primeiro, faça uma cópia de segurança do ficheiro de dados do PhoneFactor. A localização de instalação predefinida é C:\Programas\PhoneFactor\Data\Phonefactor.pfdata.


<li>Se o Portal de Utilizador estiver instalado:</li>
<ol>
<li>Navegue para a pasta de instalação e faça uma cópia de segurança do ficheiro web.config. A localização de instalação predefinida é C:\inetpub\wwwroot\PhoneFactor.</li>


<li>Se tiver adicionado temas personalizados ao portal, faça uma cópia de segurança da pasta personalizada abaixo do diretório C:\inetpub\wwwroot\PhoneFactor\App_Themes.</li>


<li>Desinstale o Portal de Utilizador através do PhoneFactor Agent (disponível apenas se estiver instalado no mesmo servidor do PhoneFactor Agent) ou através dos Programas e Funcionalidades do Windows.</li></ol>




<li>Se o Serviço Web da Aplicação Móvel estiver instalado:

<ol>
<li>Vá para a pasta de instalação e faça uma cópia de segurança do ficheiro web.config. A localização de instalação predefinida é C:\inetpub\wwwroot\PhoneFactorPhoneAppWebService.</li>
<li>Desinstale o Serviço Web da Aplicação Móvel através dos Programas e Funcionalidades do Windows.</li></ol>

<li>Se o SDK do Serviço Web estiver instalado, desinstale-o através do PhoneFactor Agent ou dos Programas e Funcionalidades do Windows.

<li>Desinstale o PhoneFactor Agent através dos Programas e Funcionalidades do Windows.

<li>Instale o Servidor Multi-Factor Authentication. Tenha em atenção que o caminho de instalação é escolhido do registo da instalação do PhoneFactor Agent anterior, pelo que deve instalar na mesma localização (por exemplo, C:\Programas\PhoneFactor). As novas instalações terão um caminho de instalação predefinido diferente (por exemplo, C:\Programas\Multi-Factor Authentication Server). O ficheiro de dados deixado pelo PhoneFactor Agent anterior deve ser atualizado durante a instalação, pelo que os seus utilizadores e definições devem permanecer depois de instalar o novo Servidor Multi-Factor Authentication.

<li>Se lhe for solicitado, ative o Servidor Multi-Factor Authentication e certifique-se de que é atribuído ao grupo de replicação correto.

<li>Se o SDK do Serviço Web tiver sido instalado anteriormente, instale o novo SDK do Serviço Web através da Interface de Utilizador do Servidor Multi-Factor Authentication. Tenha em atenção que o nome do diretório virtual predefinido é agora “MultiFactorAuthWebServiceSdk”, em vez de “PhoneFactorWebServiceSdk”. Se pretender utilizar o nome anterior, tem de alterar o nome do diretório virtual durante a instalação. Caso contrário, se permitir que a instalação utilize o novo nome predefinido, terá de alterar o URL em todas as aplicações que referenciam o SDK do Serviço Web, como o Portal de Utilizador e o Serviço Web da Aplicação Móvel, para apontarem para a localização correta.

<li>Se o Portal de Utilizador tiver sido instalado anteriormente no Servidor do PhoneFactor Agent, instale o novo Portal de Utilizador do Multi-Factor Authentication através da Interface de Utilizador do Servidor Multi-Factor Authentication. Tenha em atenção que o nome do diretório virtual predefinido é agora “MultiFactorAuth”, em vez de “PhoneFactor”. Se pretender utilizar o nome anterior, tem de alterar o nome do diretório virtual durante a instalação. Caso contrário, se permitir que a instalação utilize o novo nome predefinido, deve clicar no ícone do Portal de Utilizador no Servidor Multi-Factor Authentication e atualizar o URL do Portal de Utilizador no separador Definições.

<li>Se o Portal de Utilizador e/ou o Serviço Web da Aplicação Móvel tiverem sido instalados anteriormente num servidor diferente do PhoneFactor Agent:

<ol>
<li>Vá para a localização de instalação (por exemplo, C:\Programas\PhoneFactor) e copie o(s) instalador(es) adequado(s) para o outro servidor. Existem instaladores de 32 e 64 bits para o Portal de Utilizador e o Serviço Web da Aplicação Móvel. São denominados MultiFactorAuthenticationUserPortalSetupXX.msi e MultiFactorAuthenticationMobileAppWebServiceSetupXX.msi, respetivamente.</li>
<li>Para instalar o Portal de Utilizador no servidor Web, abra uma linha de comandos como administrador e execute MultiFactorAuthenticationUserPortalSetupXX.msi. Tenha em atenção que o nome do diretório virtual predefinido é agora “MultiFactorAuth”, em vez de “PhoneFactor”. Se pretender utilizar o nome anterior, tem de alterar o nome do diretório virtual durante a instalação. Caso contrário, se permitir que a instalação utilize o novo nome predefinido, deve clicar no ícone do Portal de Utilizador no Servidor Multi-Factor Authentication e atualizar o URL do Portal de Utilizador no separador Definições. Os utilizadores existentes terão de ser informados sobre o novo URL.</li>
<li>Vá para a localização de instalação do Portal de Utilizador (por exemplo, C:\inetpub\wwwroot\MultiFactorAuth) e edite o ficheiro web.config. Copie os valores nas secções appSettings e applicationSettings a partir do ficheiro web.config original cuja cópia de segurança foi feita antes da atualização para o novo ficheiro web.config. Se o novo nome do diretório virtual predefinido foi mantido quando instalou o SDK do Serviço Web, altere o URL na secção applicationSettings para apontar para a localização correta. Se quaisquer outras predefinições foram alteradas no ficheiro web.config anterior, aplique as mesmas alterações ao novo ficheiro web.config.</li>
<li>Para instalar o Serviço Web da Aplicação Móvel no servidor Web, abra uma linha de comandos como administrador e execute MultiFactorAuthenticationMobileAppWebServiceSetupXX.msi. Tenha em atenção que o nome do diretório virtual predefinido é agora “MultiFactorAuthMobileAppWebService”, em vez de “PhoneFactorPhoneAppWebService”. Se pretender utilizar o nome anterior, tem de alterar o nome do diretório virtual durante a instalação. Poderá pretender escolher um nome mais curto para que seja mais fácil para os utilizadores finais o escreverem nos respetivos dispositivos móveis. Caso contrário, se permitir que a instalação utilize o novo nome predefinido, deve clicar no ícone da Aplicação Móvel no Servidor Multi-Factor Authentication e atualizar o URL do Serviço Web da Aplicação Móvel.</li>
<li>Vá para a localização de instalação do Serviço Web da Aplicação Móvel (por exemplo, C:\inetpub\wwwroot\MultiFactorAuthMobileAppWebService) e edite o ficheiro web.config. Copie os valores nas secções appSettings e applicationSettings a partir do ficheiro web.config original cuja cópia de segurança foi feita antes da atualização para o novo ficheiro web.config. Se o novo nome do diretório virtual predefinido foi mantido quando instalou o SDK do Serviço Web, altere o URL na secção applicationSettings para apontar para a localização correta. Se quaisquer outras predefinições foram alteradas no ficheiro web.config anterior, aplique as mesmas alterações ao novo ficheiro web.config.</li></ol>



<!--HONumber=Sep16_HO3-->


