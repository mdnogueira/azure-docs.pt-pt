---
title: Configurar o proxy web para um dispositivo StorSimple | Microsoft Docs
description: "Saiba como utilizar o Windows PowerShell para StorSimple para configurar definições de proxy web para o dispositivo StorSimple."
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 6c2ca351-a7c6-4da6-ab5e-c081e6d08261
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: alkohli
ms.openlocfilehash: be4a8facc2f5951b6ebc027088d43ef47504acd1
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2017
---
# <a name="configure-web-proxy-for-your-storsimple-device"></a>Configurar o proxy web para o dispositivo StorSimple
> [!NOTE]
> O portal clássico para StorSimple foi preterido. Os gestores de dispositivo do StorSimple será automaticamente mudada para o novo portal do Azure de acordo com a agenda de preterição. Irá receber uma mensagem de e-mail e uma notificação de movimentação do portal. Este documento também será descontinuado em breve. Para ver a versão deste artigo para o novo portal do Azure, aceda a [configurar o proxy web para o dispositivo StorSimple](storsimple-8000-configure-web-proxy.md). Para quaisquer perguntas sobre a mudança, consulte [FAQ: mover para o portal do Azure](storsimple-8000-move-azure-portal-faq.md).

## <a name="overview"></a>Descrição geral
Este tutorial descreve como utilizar o Windows PowerShell para StorSimple para configurar e ver as definições de proxy web para o dispositivo StorSimple. As definições de proxy web são utilizadas pelo dispositivo StorSimple ao comunicar com a nuvem. Um servidor proxy web é utilizado para adicionar outra camada de segurança, conteúdo de filtro, cache facilidade requisitos de largura de banda ou até mesmo ajuda com a análise.

Web proxy é uma configuração opcional para o dispositivo StorSimple. Pode configurar o proxy de web apenas através do Windows PowerShell para StorSimple. A configuração é um processo de dois passos da seguinte forma:

1. Configurar definições de proxy web através do Assistente de configuração ou o Windows PowerShell para StorSimple cmdlets primeiro.
2. Em seguida, ative as definições de proxy web configurada através do Windows PowerShell para StorSimple cmdlets.

Depois de concluída a configuração de proxy web, pode ver as definições de proxy web configuradas no serviço Microsoft Azure StorSimple Manager e o Windows PowerShell para StorSimple. 

Depois de ler este tutorial, será capaz de:

* Configurar o web proxy utilizando o Assistente de configuração e cmdlets
* Ativar o proxy da web através de cmdlets
* Ver as definições de proxy web no portal clássico do Azure
* Resolver erros durante a configuração do proxy web

## <a name="configure-web-proxy-via-windows-powershell-for-storsimple"></a>Configurar o proxy web através do Windows PowerShell para StorSimple
Utilize o seguinte para configurar as definições de proxy web:

* Assistente de configuração para ajudá-lo durante os passos de configuração.
* Cmdlets do Windows PowerShell para StorSimple.

Cada um destes métodos é abordada nas secções seguintes.

## <a name="configure-web-proxy-via-the-setup-wizard"></a>Configurar o proxy web através do Assistente de configuração
Pode utilizar o Assistente de configuração para ajudá-lo através dos passos para configuração do proxy web. Execute os seguintes passos para configurar o web proxy no seu dispositivo.

#### <a name="to-configure-web-proxy-via-the-setup-wizard"></a>Para configurar o proxy web através do Assistente de configuração
1. No menu da consola de série, selecione a opção 1, **iniciar sessão com acesso total** e forneça o **palavra-passe de administrador do dispositivo**. Escreva o seguinte comando para iniciar uma sessão de Assistente de configuração:
   
    `Invoke-HcsSetupWizard`
2. Se esta for a primeira vez que utiliza o Assistente de configuração para o registo do dispositivo, terá de configurar todas as definições de rede necessários até chegar a configuração de proxy web. Se o dispositivo já está registado, pode aceitar todas as definições de rede configurados até chegar a configuração de proxy web. No Assistente de configuração, quando lhe for pedido para configurar as definições de proxy web, escreva **Sim**.
3. Para o **URL do Web Proxy**, especifique o endereço IP ou o nome de domínio completamente qualificado (FQDN) do servidor proxy web e a porta TCP número que pretende que o seu dispositivo para utilizar quando comunicar com a nuvem. Utilize o seguinte formato:
   
    `http://<IP address or FQDN of the web proxy server>:<TCP port number>`
   
    Por predefinição, é especificado o número da porta TCP 8080.
4. Escolha o tipo de autenticação como **NTLM**, **básico**, ou **nenhum**. Básica é a autenticação menos segura para a configuração do servidor proxy. NT LAN Manager (NTLM) é um protocolo de autenticação altamente seguro e complexas que utiliza um sistema de mensagens de três vias (por vezes, quatro se for necessária a integridade adicional) para autenticar um utilizador. A autenticação de predefinido é NTLM. Para obter mais informações, consulte [básico](http://hc.apache.org/httpclient-3.x/authentication.html) e [autenticação NTLM](http://hc.apache.org/httpclient-3.x/authentication.html). 
   
   > [!IMPORTANT]
   > **O serviço StorSimple Manager, os gráficos de monitorização de dispositivos não funcionam quando básica ou autenticação NTLM está ativada na configuração do servidor proxy para o dispositivo. Para os gráficos monitorização funcione, terá de Certifique-se de que a autenticação está definida como NONE.**
   > 
   > 
5. Se estiver a utilizar a autenticação, forneça um **nome de utilizador de Proxy de Web** e um **palavra-passe do Web Proxy**. Também terá de confirmar a palavra-passe.
   
    ![Configurar o Web Proxy Device1 do StorSimple](./media/storsimple-configure-web-proxy/IC751830.png)

Se estiver a registar o seu dispositivo pela primeira vez, continue com o registo. Se o dispositivo já estava registado, o assistente será fechada. As definições configuradas serão guardadas.

Web proxy será agora também ativado. Pode ignorar o [ativar o web proxy](#enable-web-proxy) passo e aceder diretamente à [ver as definições de proxy web no portal clássico do Azure](#view-web-proxy-settings-in-the-azure-classic-portal).

## <a name="configure-web-proxy-via-windows-powershell-for-storsimple-cmdlets"></a>Configurar o proxy web através do Windows PowerShell para StorSimple cmdlets
É uma forma alternativa para configurar as definições de proxy web através do Windows PowerShell para StorSimple cmdlets. Execute os seguintes passos para configurar o web proxy.

#### <a name="to-configure-web-proxy-via-cmdlets"></a>Para configurar o proxy web através de cmdlets
1. No menu da consola de série, selecione a opção 1, **iniciar sessão com acesso total**. Quando solicitado, forneça o **palavra-passe de administrador do dispositivo**. A palavra-passe predefinida é `Password1`.
2. Na linha de comandos, escreva:
   
    `Set-HcsWebProxy -Authentication NTLM -ConnectionURI "<http://<IP address or FQDN of web proxy server>:<TCP port number>" -Username "<Username for web proxy server>"`
   
    Forneça e confirme a palavra-passe quando lhe for pedido, conforme mostrado abaixo.
   
    ![Configurar o Web Proxy Device3 do StorSimple](./media/storsimple-configure-web-proxy/IC751831.png)

O proxy web já está configurado e tem de ser ativado.

## <a name="enable-web-proxy"></a>Ativar o proxy da web
Web proxy está desativado por predefinição. Depois de configurar as definições de proxy web no dispositivo StorSimple, terá de utilizar o Windows PowerShell para StorSimple para ativar as definições de proxy web.

> [!NOTE]
> **Este passo não será necessário se tiver utilizado o Assistente de configuração para configurar o web proxy. Web proxy automaticamente está ativado por predefinição, depois de uma sessão de Assistente de configuração.**
> 
> 

Execute os seguintes passos no Windows PowerShell para StorSimple ativar o proxy web no seu dispositivo:

#### <a name="to-enable-web-proxy"></a>Para ativar o web proxy
1. No menu da consola de série, selecione a opção 1, **iniciar sessão com acesso total**. Quando solicitado, forneça o **palavra-passe de administrador do dispositivo**. A palavra-passe predefinida é `Password1`.
2. Na linha de comandos, escreva:
   
    `Enable-HcsWebProxy`
   
    Agora ter ativada a configuração de proxy web no dispositivo StorSimple.
   
    ![Configurar o Web Proxy Device4 do StorSimple](./media/storsimple-configure-web-proxy/IC751832.png)

## <a name="view-web-proxy-settings-in-the-azure-classic-portal"></a>Ver as definições de proxy web no portal clássico do Azure
As definições de proxy web são configuradas através da interface do Windows PowerShell e não podem ser alteradas a partir do portal clássico do. No entanto, podem ver estas definições configuradas no portal clássico. Execute os seguintes passos para ver o web proxy.

#### <a name="to-view-web-proxy-settings"></a>Para ver as definições de proxy web
1. Navegue para **serviço StorSimple Manager > dispositivos**. Selecione e clique no dispositivo e, em seguida, aceda a **configurar**.
2. Percorra o **configurar** página para **as definições de proxy de Web** secção. Pode ver as definições de proxy web configuradas no seu dispositivo StorSimple, conforme mostrado abaixo.
   
    ![Vista Web Proxy no Portal de gestão](./media/storsimple-configure-web-proxy/ViewWebProxyPortal_M.png)

## <a name="errors-during-web-proxy-configuration"></a>Erros durante a configuração do proxy web
Se as definições de proxy web foram configuradas incorretamente, mensagens de erro serão apresentadas ao utilizador no Windows PowerShell para StorSimple. A tabela seguinte explica alguns destas mensagens de erro, as causas prováveis e ações recomendadas.

| Série não. | Erro HRESULT código | Causa possível | Ação recomendada |
|:--- |:--- |:--- |:--- |
| 1. |0x80070001 |Comando é executado a partir do controlador de passivo e não é capaz de comunicar com o controlador de Active Directory. |Execute o comando no controlador ativo. Para executar o comando a partir do controlador de passivo, terá de corrigir a conectividade do passivo ao controlador ativo. Terá de interagir com o Microsoft Support se este conectividade está danificada. |
| 2. |0x800710dd - o identificador da operação não é válido |Definições de proxy não são suportadas no dispositivo virtual StorSimple. |Definições de proxy não são suportadas no dispositivo virtual StorSimple. Só podem ser configurados num dispositivo físico StorSimple. |
| 3. |0x80070057 - parâmetro inválido |Um dos parâmetros fornecidos para as definições de proxy não é válido. |O URI não é fornecido no formato correto. Utilize o seguinte formato:`http://<IP address or FQDN of the web proxy server>:<TCP port number>` |
| 4. |0x800706BA - servidor de RPC não está disponível |A causa raiz é um dos seguintes:</br></br>Cluster não está operacional.</br></br>Serviço de DataPath não está em execução.</br></br>O comando é executado a partir do controlador passivo e não é capaz de comunicar com o controlador de Active Directory. |Volte a iniciar o Support da Microsoft para garantir que o cluster está operacional e datapath serviço está em execução.</br></br>Execute o comando a partir do controlador ativo. Se pretender executar o comando a partir do controlador de passivo, terá de Certifique-se de que o controlador passivo pode comunicar com o controlador de Active Directory. Terá de interagir com o Microsoft Support se este conectividade está danificada. |
| 5. |0x800706be - falha de chamada RPC |Cluster está inativo. |. Interagir com Support da Microsoft para se certificar de que o cluster está operacional. |
| 6. |0x8007138f - recurso de cluster não encontrado |Recurso de cluster do serviço de plataforma não foi encontrado. Isto pode acontecer quando a instalação não estava correta. |Terá de efetuar uma reposição de fábrica no dispositivo. Terá de criar um recurso de plataforma. Contacte Support da Microsoft para os passos seguintes. |
| 7. |0x8007138c - recurso de cluster não online |Plataforma ou datapath recursos do cluster não estão online. |Contacte Support da Microsoft para ajudar a garantir que o recurso de serviço de plataforma e datapath estão online. |

> [!NOTE]
> * Não é exaustiva da lista acima das mensagens de erro. 
> * Erros relacionados com as definições de proxy web não serão apresentados no portal clássico do Azure no serviço StorSimple Manager. Se houver um problema com o web proxy depois de concluída a configuração, o estado do dispositivo será alterado para **Offline** no portal clássico. |
> 
> 

## <a name="next-steps"></a>Passos Seguintes
* Se ocorrer algum problema ao implementar o seu dispositivo ou configurar as definições de proxy web, consulte [resolver problemas relacionados com a implementação do dispositivo StorSimple](storsimple-troubleshoot-deployment.md).
* Para saber como utilizar o serviço StorSimple Manager, aceda à [utilizam o serviço StorSimple Manager para administrar o dispositivo StorSimple](storsimple-manager-service-administration.md).

