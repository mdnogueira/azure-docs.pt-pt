---
title: "Atualização do servidor MFA do Azure | Microsoft Docs"
description: "Passos e as orientações para atualizar o servidor do Azure multi-factor Authentication para uma versão mais recente."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: 50bb8ac3-5559-4d8b-a96a-799a74978b14
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: joflore
ms.reviewer: richagi
ms.custom: it-pro
ms.openlocfilehash: a56078fcd61114d0ca78e2dd00445c988a8a3c6c
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="upgrade-to-the-latest-azure-multi-factor-authentication-server"></a>Atualizar para o servidor de autenticação de multi-factor do Azure mais recente

Este artigo explica como o processo de atualização v 6.0 do servidor multi-factor Authentication (MFA) do Azure ou superior. Se precisar de atualizar uma versão antiga do PhoneFactor Agent, consulte [atualizar do PhoneFactor Agent para o servidor do Azure multi-factor Authentication](multi-factor-authentication-get-started-server-upgrade.md).

Se estiver a atualizar do v6. x ou anterior v7.x ou mais recente, altere todos os componentes do .NET 2.0 para .NET 4.5. Todos os componentes também requerem o Microsoft Visual C++ Redistributable atualização 2015 1 ou superior. O instalador do servidor MFA instala ambas as versões x86 e x64 destes componentes, se ainda não estiverem instalados. Se o Portal de utilizador e o serviço Web da aplicação móvel são executados em servidores separados, terá de instalar estes pacotes antes da atualização desses componentes. Pode pesquisar para a atualização mais recente do Microsoft Visual C++ 2015 Redistributable a [Microsoft Download Center](https://www.microsoft.com/en-us/download/). 

## <a name="install-the-latest-version-of-azure-mfa-server"></a>Instale a versão mais recente do servidor MFA do Azure

1. Utilize as instruções no [transferir o servidor do Azure multi-factor Authentication](multi-factor-authentication-get-started-server.md#download-the-mfa-server) para obter a versão mais recente do servidor MFA do Azure.
2. Faça uma cópia de segurança do ficheiro de dados do servidor MFA localizado em c:\Programas\Microsoft multi-Factor Authentication Server\Data\PhoneFactor.pfdata (assumindo que a localização de instalação predefinida) no seu servidor mestre do MFA.
3. Se executar vários servidores para elevada disponibilidade, altere os sistemas de cliente autenticarem para o servidor MFA para que possam parar o envio de tráfego para os servidores que estiver a atualizar. Se utilizar um balanceador de carga, remove um servidor MFA do Balanceador de carga, a atualização e, em seguida, adicione o servidor no farm.
4. Execute o instalador de novo em cada servidor MFA. Atualize primeiro servidores subordinadas, porque podem ler o ficheiro de dados antigo a ser replicado pelo mestre de. 

  Não terá de desinstalar o servidor MFA atual antes de executar o instalador. O instalador efetua uma atualização no local. O caminho de instalação é escolhido do registo da instalação anterior, pelo que é instalada na mesma localização (por exemplo, c:\Programas\Microsoft files\servidor multi-Factor Authentication Server). 
  
5. Se lhe for pedida para instalar um pacote de atualização de Microsoft Visual C++ 2015 Redistributable, aceitar o pedido. Ambas as versões x86 e x64 do pacote estão instaladas.
5. Se utilizar o Web Service SDK, lhe for pedido para instalar o novo Web Service SDK. Quando instala o novo Web Service SDK, certifique-se de que o nome do diretório virtual corresponde ao diretório virtual instalado anteriormente (por exemplo, MultiFactorAuthWebServiceSdk).
6. Repita os passos em todos os servidores subordinados. Promova um dos subordinados para o novo mestre, em seguida, atualize o servidor de mestra antiga. 

## <a name="upgrade-the-user-portal"></a>Atualizar o Portal de utilizador

1. Faça uma cópia de segurança do ficheiro Web. config que se encontra no diretório virtual da localização de instalação do Portal de utilizador (por exemplo, C:\inetpub\wwwroot\MultiFactorAuth). Se as alterações foram efetuadas o tema predefinido, efetuar uma cópia de segurança da pasta App_Themes\Default. É melhor criar uma cópia da pasta predefinida e criar um novo tema que para alterar o tema predefinido.
2. Se o Portal de utilizador é executado no mesmo servidor que os outros componentes de servidor MFA, a instalação do servidor MFA solicitará ao atualizar o Portal de utilizador. Aceitar o pedido e instalar a atualização do Portal de utilizador. Certifique-se de que o nome do diretório virtual corresponde ao diretório virtual instalado anteriormente (por exemplo, MultiFactorAuth).
3. Se o Portal de utilizador estiver no seu próprio servidor, copie o ficheiro de MultiFactorAuthenticationUserPortalSetup64.msi localização da instalação de um dos servidores do MFA e colocá-la para o servidor web do Portal de utilizador. Execute o instalador. 

  Se ocorrer indicar um erro, "Microsoft Visual C++ Redistributable atualização 2015 1 ou superior" é necessária, transfira e instale o pacote de atualização mais recente do [Microsoft Download Center](https://www.microsoft.com/download/). Instale versões x86 e x64.

4. Depois do software atualizado do Portal de utilizador estiver instalado, compare a cópia de segurança do Web. config efetuadas no passo 1 com o novo ficheiro Web. config. Não se existirem nenhum novos atributos na Web. config novo, copie o Web. config cópia de segurança para o diretório virtual para substituir o novo. Outra opção consiste em copiar/colar os valores de appSettings e o URL de SDK do serviço Web do ficheiro de cópia de segurança para o novo Web. config.

Se tiver o Portal de utilizador em vários servidores, repita a instalação em todos eles. 


## <a name="upgrade-the-mobile-app-web-service"></a>Atualizar o serviço Web de aplicação móvel

1. Faça uma cópia de segurança do ficheiro Web. config que se encontra no diretório virtual da localização de instalação do serviço Web da aplicação móvel (por exemplo, C:\inetpub\wwwroot\app ou C:\inetpub\wwwroot\MultiFactorAuthMobileAppWebService).
2. Copie o ficheiro de MultiFactorAuthenticationMobileAppWebServiceSetup64.msi localização da instalação dos servidores de MFA e coloque-o para o servidor de web de registo de aplicação móvel.
3. Execute o instalador. 

  Se ocorrer um erro a indicar que é necessária o Microsoft Visual C++ Redistributable atualização 2015 1 ou superior, transfira e instale o pacote de atualização mais recente do [Microsoft Download Center](https://www.microsoft.com/download/). Instale versões x86 e x64.

4. Depois de instala o software atualizado do serviço Web da aplicação móvel, compare o ficheiro Web. config que foi feito no passo 1 com o novo ficheiro Web. config. Não se existirem nenhum novos atributos na Web. config novo, pode copiar o Web. config guardado no diretório virtual e substituir novo. Outra opção consiste em copiar/colar os valores de appSettings e o URL de SDK do serviço Web do ficheiro de cópia de segurança para o novo Web. config.

Se tiver o serviço Web da aplicação móvel em vários servidores, repita a instalação em todos eles. 

## <a name="upgrade-the-ad-fs-adapters"></a>Atualizar os adaptadores do AD FS


### <a name="if-mfa-runs-on-different-servers-than-ad-fs"></a>Se o MFA é executado num servidor diferente do AD FS

Estas instruções aplicam-se apenas se executar o servidor multi-factor Authentication em separado dos seus servidores do AD FS. Se ambos os serviços são executados nos servidores do mesmos, ignore esta secção e avance para os passos de instalação. 

1. Guardar uma cópia do ficheiro multifactorauthenticationadfsadapter. config que foi registado no AD FS ou exportar a configuração utilizando o seguinte comando do PowerShell: `Export-AdfsAuthenticationProviderConfigurationData -Name [adapter name] -FilePath [path to config file]`. O nome do adaptador é "WindowsAzureMultiFactorAuthentication" ou "AzureMfaServerAuthentication" consoante a versão instalada anteriormente.
2. Copie os seguintes ficheiros da localização de instalação do servidor MFA para os servidores do AD FS:

  - MultiFactorAuthenticationAdfsAdapterSetup64.msi
  - Register-MultiFactorAuthenticationAdfsAdapter.ps1
  - Unregister-MultiFactorAuthenticationAdfsAdapter.ps1
  - MultiFactorAuthenticationAdfsAdapter.config

3. Editar o script register-multifactorauthenticationadfsadapter.ps1 adicionando `-ConfigurationFilePath [path]` ao fim do `Register-AdfsAuthenticationProvider` comando. Substitua *[caminho]* com o caminho completo para o multifactorauthenticationadfsadapter. config, ficheiro ou o ficheiro de configuração exportado no passo anterior. 

  Verifique os atributos no novo multifactorauthenticationadfsadapter. config para ver se corresponderem a ficheiro de configuração antigo. Se os atributos adicionados ou removidos na nova versão, copie os valores de atributo do ficheiro de configuração antigo para a nova ou modificar o ficheiro de configuração antigo para corresponder.

### <a name="install-new-ad-fs-adapters"></a>Instalar novos adaptadores do AD FS

> [!IMPORTANT] 
> Os utilizadores não terão de efetuar verificação de dois passos durante os passos 3-8 desta secção. Se tiver o AD FS configurado em vários clusters, pode remover, atualizar e restaurar cada cluster no farm independentemente os outros clusters para evitar períodos de indisponibilidade.

1. Remova alguns servidores do AD FS do farm. Atualize estes servidores, enquanto outros ainda estão em execução.
2. Instale o adaptador AD FS novo nos servidores de removidos do farm do AD FS. Se o servidor MFA é instalado em cada servidor do AD FS, pode atualizar através do administrador do servidor MFA UX. Caso contrário, atualize, executando MultiFactorAuthenticationAdfsAdapterSetup64.msi. 

  Se ocorrer indicar um erro, "Microsoft Visual C++ Redistributable atualização 2015 1 ou superior" é necessária, transfira e instale o pacote de atualização mais recente do [Microsoft Download Center](https://www.microsoft.com/download/). Instale versões x86 e x64.

3. Aceda a **do AD FS** > **políticas de autenticação** > **Editar política de autenticação Multifator Global**. Desmarque **WindowsAzureMultiFactorAuthentication** ou **AzureMFAServerAuthentication** (consoante a versão atual instalada). 

  Depois de concluída este passo, a verificação através do servidor MFA não está disponível neste cluster de AD FS, até que conclua o passo 8.

4. Anular o registo a versão mais antiga do adaptador AD FS, executando o script do PowerShell unregister-multifactorauthenticationadfsadapter.ps1. Certifique-se de que o *-nome* parâmetro ("WindowsAzureMultiFactorAuthentication" ou "AzureMFAServerAuthentication") corresponde ao nome que foi apresentado no passo 3. Isto aplica-se a todos os servidores no mesmo cluster do AD FS, uma vez que existe uma configuração central.
5. Registe o adaptador AD FS novo ao executar o script register-multifactorauthenticationadfsadapter.ps1 PowerShell. Isto aplica-se a todos os servidores no mesmo cluster do AD FS, uma vez que existe uma configuração central.
6. Reinicie o serviço do AD FS nos servidores de removidos do farm do AD FS.
7. Adicionar os servidores atualizados para o farm do AD FS e remova os outros servidores do farm.
8. Aceda a **do AD FS** > **políticas de autenticação** > **Editar política de autenticação Multifator Global**. Verifique **AzureMfaServerAuthentication**.
9. Repita o passo 2 para atualizar os servidores de removidos agora o farm do AD FS e reinicie o serviço do AD FS nesses servidores.
10. Adicione os servidores no farm do AD FS.

## <a name="next-steps"></a>Passos seguintes

- Obter exemplos de [avançadas cenários com multi-factor Authentication do Azure e VPNs de terceiros](multi-factor-authentication-advanced-vpn-configurations.md)

- [Sincronizar o servidor MFA com o Windows Server Active Directory](multi-factor-authentication-get-started-server-dirint.md)

- [Configurar a autenticação do Windows](multi-factor-authentication-get-started-server-windows.md) para as suas aplicações
