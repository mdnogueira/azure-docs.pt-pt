---
title: "Configurar o proxy web para dispositivos de série 8000 do StorSimple | Microsoft Docs"
description: "Saiba como utilizar o Windows PowerShell para StorSimple para configurar definições de proxy web para o dispositivo StorSimple."
services: storsimple
documentationcenter: 
author: alkohli
manager: 
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2017
ms.author: alkohli
ms.openlocfilehash: 1109e44ed9c6aa8a0f7305b8a50410316711589c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-web-proxy-for-your-storsimple-device"></a>Configurar o proxy web para o dispositivo StorSimple

## <a name="overview"></a>Descrição geral

Este tutorial descreve como utilizar o Windows PowerShell para StorSimple para configurar e ver as definições de proxy web para o dispositivo StorSimple. As definições de proxy web são utilizadas pelo dispositivo StorSimple ao comunicar com a nuvem. Um servidor proxy web é utilizado para adicionar outra camada de segurança, conteúdo de filtro, cache facilidade requisitos de largura de banda ou até mesmo ajuda com a análise.

As orientações neste tutorial aplica-se apenas a dispositivos físicos de série 8000 do StorSimple. A configuração do Web proxy não é suportada no dispositivo de nuvem de StorSimple (8010 e 8020).

O proxy Web é um _opcional_ configuração para o dispositivo StorSimple. Pode configurar o proxy de web apenas através do Windows PowerShell para StorSimple. A configuração é um processo de dois passos da seguinte forma:

1. Configurar definições de proxy web através do Assistente de configuração ou o Windows PowerShell para StorSimple cmdlets primeiro.
2. Em seguida, ative as definições de proxy web configurada através do Windows PowerShell para StorSimple cmdlets.

Depois de concluída a configuração de proxy web, pode ver as definições de proxy web configuradas no serviço do Gestor de dispositivos do Microsoft Azure StorSimple e o Windows PowerShell para StorSimple.

Depois de ler este tutorial, será capaz de:

* Configure o web proxy utilizando o Assistente de configuração e os cmdlets.
* Ative o proxy da web através de cmdlets.
* Ver as definições de proxy web no portal do Azure.
* Resolva os erros durante a configuração do proxy web.


## <a name="configure-web-proxy-via-windows-powershell-for-storsimple"></a>Configurar o proxy web através do Windows PowerShell para StorSimple

Utilize o seguinte para configurar as definições de proxy web:

* Assistente de configuração para ajudá-lo durante os passos de configuração.
* Cmdlets do Windows PowerShell para StorSimple.

Cada um destes métodos é abordada nas secções seguintes.

## <a name="configure-web-proxy-via-the-setup-wizard"></a>Configurar o proxy web através do Assistente de configuração

Utilize o Assistente de configuração para ajudá-lo através dos passos para configuração do proxy web. Execute os seguintes passos para configurar o web proxy no seu dispositivo.

#### <a name="to-configure-web-proxy-via-the-setup-wizard"></a>Para configurar o proxy web através do Assistente de configuração

1. No menu da consola de série, selecione a opção 1, **iniciar sessão com acesso total** e forneça o **palavra-passe de administrador do dispositivo**. Escreva o seguinte comando para iniciar uma sessão de Assistente de configuração:
   
    `Invoke-HcsSetupWizard`
2. Se esta for a primeira vez que utiliza o Assistente de configuração para o registo do dispositivo, terá de configurar todas as definições de rede necessários até chegar a configuração de proxy web. Se o dispositivo já está registado, aceite todas as definições de rede configurados até chegar a configuração de proxy web. No Assistente de configuração, quando lhe for pedido para configurar as definições de proxy web, escreva **Sim**.
3. Para o **URL do Web Proxy**, especifique o endereço IP ou o nome de domínio completamente qualificado (FQDN) do servidor proxy web e a porta TCP número que pretende que o seu dispositivo para utilizar quando comunicar com a nuvem. Utilize o seguinte formato:
   
    `http://<IP address or FQDN of the web proxy server>:<TCP port number>`
   
    Por predefinição, é especificado o número da porta TCP 8080.
4. Escolha o tipo de autenticação como **NTLM**, **básico**, ou **nenhum**. Básica é a autenticação menos segura para a configuração do servidor proxy. NT LAN Manager (NTLM) é um protocolo de autenticação altamente seguro e complexas que utiliza um sistema de mensagens de três vias (por vezes, quatro se for necessária a integridade adicional) para autenticar um utilizador. A autenticação de predefinido é NTLM. Para obter mais informações, consulte [básico](http://hc.apache.org/httpclient-3.x/authentication.html) e [autenticação NTLM](http://hc.apache.org/httpclient-3.x/authentication.html). 
   
   > [!IMPORTANT]
   > **O serviço do Gestor de dispositivos do StorSimple, os gráficos de monitorização de dispositivos não funcionam quando básica ou autenticação NTLM está ativada na configuração do servidor proxy para o dispositivo. Para os gráficos monitorização funcione, tem de garantir que a autenticação está definida como NONE.**
  
5. Se tiver ativado a autenticação, fornecer um **nome de utilizador de Proxy de Web** e um **palavra-passe do Web Proxy**. Terá também de confirmar a palavra-passe.
   
    ![Configurar o Web Proxy Device1 do StorSimple](./media/storsimple-configure-web-proxy/IC751830.png)

Se estiver a registar o seu dispositivo pela primeira vez, continue com o registo. Se o dispositivo já estava registado, sai do assistente. As definições configuradas são guardadas.

Web proxy está agora ativado. Pode ignorar o [ativar o web proxy](#enable-web-proxy) passo e aceder diretamente à [ver as definições de proxy web no portal do Azure](#view-web-proxy-settings-in-the-azure-portal).

## <a name="configure-web-proxy-via-windows-powershell-for-storsimple-cmdlets"></a>Configurar o proxy web através do Windows PowerShell para StorSimple cmdlets

É uma forma alternativa para configurar as definições de proxy web através do Windows PowerShell para StorSimple cmdlets. Execute os seguintes passos para configurar o web proxy.

#### <a name="to-configure-web-proxy-via-cmdlets"></a>Para configurar o proxy web através de cmdlets
1. No menu da consola de série, selecione a opção 1, **iniciar sessão com acesso total**. Quando solicitado, forneça o **palavra-passe de administrador do dispositivo**. A palavra-passe predefinida é `Password1`.
2. Na linha de comandos, escreva:
   
    `Set-HcsWebProxy -Authentication NTLM -ConnectionURI "<http://<IP address or FQDN of web proxy server>:<TCP port number>" -Username "<Username for web proxy server>"`
   
    Forneça e confirme a palavra-passe quando lhe for pedido.
   
    ![Configurar o Web Proxy Device3 do StorSimple](./media/storsimple-configure-web-proxy/IC751831.png)

O proxy web já está configurado e tem de ser ativado.

## <a name="enable-web-proxy"></a>Ativar o proxy da web

Web proxy está desativado por predefinição. Depois de configurar as definições de proxy web no dispositivo StorSimple, utilize o Windows PowerShell para StorSimple para ativar as definições de proxy web.

> [!NOTE]
> **Este passo não é necessário se tiver utilizado o Assistente de configuração para configurar o web proxy. Web proxy automaticamente está ativado por predefinição, depois de uma sessão de Assistente de configuração.**


Execute os seguintes passos no Windows PowerShell para StorSimple ativar o proxy web no seu dispositivo:

#### <a name="to-enable-web-proxy"></a>Para ativar o web proxy
1. No menu da consola de série, selecione a opção 1, **iniciar sessão com acesso total**. Quando solicitado, forneça o **palavra-passe de administrador do dispositivo**. A palavra-passe predefinida é `Password1`.
2. Na linha de comandos, escreva:
   
    `Enable-HcsWebProxy`
   
    Agora ter ativada a configuração de proxy web no dispositivo StorSimple.
   
    ![Configurar o Web Proxy Device4 do StorSimple](./media/storsimple-configure-web-proxy/IC751832.png)

## <a name="view-web-proxy-settings-in-the-azure-portal"></a>Ver as definições de proxy web no portal do Azure

As definições de proxy web são configuradas através da interface do Windows PowerShell e não podem ser alteradas de no portal. No entanto, podem ver estas definições configuradas no portal. Execute os seguintes passos para ver o web proxy.

#### <a name="to-view-web-proxy-settings"></a>Para ver as definições de proxy web
1. Navegue para **serviço StorSimple Manager do dispositivo > dispositivos**. Selecione e clique no dispositivo e, em seguida, aceda a **definições do dispositivo > rede**.

    ![Clique em rede](./media/storsimple-8000-configure-web-proxy/view-web-proxy-1.png)

2. No **as definições de rede** painel, clique em de **Web proxy** mosaico.

    ![Clique em web proxy](./media/storsimple-8000-configure-web-proxy/view-web-proxy-2.png)

3. No **Web proxy** painel, reveja as definições de proxy web configuradas no dispositivo StorSimple.
   
    ![Ver definições de proxy web](./media/storsimple-8000-configure-web-proxy/view-web-proxy-3.png)


## <a name="errors-during-web-proxy-configuration"></a>Erros durante a configuração do proxy web

Se as definições de proxy web foram configuradas incorretamente, são apresentadas mensagens de erro para o utilizador no Windows PowerShell para StorSimple. A tabela seguinte explica alguns destas mensagens de erro, as causas prováveis e ações recomendadas.

| Série não. | Erro HRESULT código | Causa possível | Ação recomendada |
|:--- |:--- |:--- |:--- |
| 1. |0x80070001 |Comando é executado a partir do controlador de passivo e não é capaz de comunicar com o controlador de Active Directory. |Execute o comando no controlador ativo. Para executar o comando a partir do controlador de passivo, é necessário corrigir a conectividade do passivo ao controlador ativo. Tem de interagir com Support da Microsoft se este conectividade está danificada. |
| 2. |0x800710dd - o identificador da operação não é válido |Definições de proxy não são suportadas no dispositivo de nuvem do StorSimple. |Definições de proxy não são suportadas no dispositivo de nuvem do StorSimple. Só podem ser configurados num dispositivo físico StorSimple. |
| 3. |0x80070057 - parâmetro inválido |Um dos parâmetros fornecidos para as definições de proxy não é válido. |O URI não é fornecido no formato correto. Utilize o seguinte formato:`http://<IP address or FQDN of the web proxy server>:<TCP port number>` |
| 4. |0x800706BA - servidor de RPC não está disponível |A causa raiz é um dos seguintes:</br></br>Cluster não está operacional. </br></br>Serviço de DataPath não está em execução.</br></br>O comando é executado a partir do controlador passivo e não é capaz de comunicar com o controlador de Active Directory. |Interagir com Support da Microsoft para garantir que o cluster está operacional e datapath serviço está em execução.</br></br>Execute o comando a partir do controlador ativo. Se pretender executar o comando a partir do controlador de passivo, certifique-se de que o controlador passivo consegue comunicar com o controlador de Active Directory. Tem de interagir com Support da Microsoft se este conectividade está danificada. |
| 5. |0x800706be - falha de chamada RPC |Cluster está inativo. |Interagir com Support da Microsoft para se certificar de que o cluster está operacional. |
| 6. |0x8007138f - recurso de cluster não encontrado |Recurso de cluster do serviço de plataforma não foi encontrado. Isto pode acontecer quando a instalação não estava correta. |Terá de efetuar uma reposição de fábrica no dispositivo. Terá de criar um recurso de plataforma. Contacte Support da Microsoft para passos seguintes. |
| 7. |0x8007138c - recurso de cluster não online |Plataforma ou datapath recursos do cluster não estão online. |Contacte Support da Microsoft para ajudar a garantir que o recurso de serviço de plataforma e datapath estão online. |

> [!NOTE]
> * Não é exaustiva da lista acima das mensagens de erro.
> * Erros relacionados com as definições de proxy web não serão apresentados no portal do Azure no seu serviço do Gestor de dispositivos do StorSimple. Se houver um problema com o web proxy depois de concluída a configuração, o estado do dispositivo será alterado para **Offline** no portal clássico. |

## <a name="next-steps"></a>Passos Seguintes
* Se ocorrer algum problema ao implementar o seu dispositivo ou configurar as definições de proxy web, consulte [resolver problemas relacionados com a implementação do dispositivo StorSimple](storsimple-troubleshoot-deployment.md).
* Para saber como utilizar o serviço do Gestor de dispositivos do StorSimple, aceda à [utilizar o serviço do Gestor de dispositivos do StorSimple para administrar o dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

