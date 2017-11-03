---
title: "Resolução de problemas de ativação do Windows máquina virtual no Azure | Microsoft Docs"
description: "Fornece os passos de resolução de problemas para corrigir problemas de ativação de máquina virtual do Windows no Azure"
services: virtual-machines-windows, azure-resource-manager
documentationcenter: 
author: genlin
manager: willchen
editor: 
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/26/2017
ms.author: genli
ms.openlocfilehash: a6e0750e28c2d71721450d6ba84a1caa362c638b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-azure-windows-virtual-machine-activation-problems"></a>Resolução de problemas de ativação de máquina virtual do Azure Windows

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Se tiver problemas ao ativar a máquina virtual do Azure Windows (VM) que é criada a partir de uma imagem personalizada, pode utilizar as informações fornecidas neste documento para resolver o problema. 

## <a name="symptom"></a>Sintoma

Ao tentar ativar uma VM do Windows Azure, receber um erro mensagem assemelha-se o exemplo seguinte:

**Erro: 0xC004F074 que licensingservice de Software reportou que não foi possível ativar o computador. Foi possível contactar o não ManagementService de chaves (KMS). Consulte o registo de eventos de aplicações para obter informações adicionais.**

## <a name="cause"></a>Causa
Geralmente, problemas de ativação de VM do Azure ocorrerem se a VM do Windows não está configurada utilizando a chave de configuração de cliente KMS adequada, ou se a VM do Windows tem um problema de conectividade ao serviço Azure KMS (kms.core.windows.net, porta 1668). 

## <a name="solution"></a>Solução

>[!NOTE]
>Se estiver a utilizar uma VPN de site a site e a imposição do túnel, consulte [rotas personalizadas do Azure de utilização para ativar a ativação KMS com imposição do túnel](http://blogs.msdn.com/b/mast/archive/2015/05/20/use-azure-custom-routes-to-enable-kms-activation-with-forced-tunneling.aspx). 
>
>Se estiver a utilizar o ExpressRoute e tiver uma rota predefinida publicadas, consulte [VM do Azure podem falhar ao ativar através do ExpressRoute](http://blogs.msdn.com/b/mast/archive/2015/12/01/azure-vm-may-fail-to-activate-over-expressroute.aspx).

### <a name="step-1-configure-the-appropriate-kms-client-setup-key-for-windows-server-2016-and-windows-server-2012-r2"></a>Passo 1 configurar a chave de configuração de cliente KMS adequada (para Windows Server 2016 e o Windows Server 2012 R2)

Para a VM é criada a partir de uma imagem personalizada do Windows Server 2016 ou o Windows Server 2012 R2, tem de configurar a chave de configuração de cliente KMS adequada para a VM.

Este passo não é aplicável ao Windows 2012 ou Windows 2008 R2. Utiliza a funcionalidade de ativação de Máquina Virtual de automatização (AVMA), que é suportada apenas pelo Windows Server 2016 e o Windows Server 2012 R2.

1. Executar **slmgr.vbs /dlv** numa linha de comandos elevada. Verifique o valor de descrição na saída e, em seguida, determine se foi criado de revenda (canal de revenda) ou suporte de dados de licença de volume (VOLUME_KMSCLIENT):
  
    ```
    cscript c:\windows\system32\slmgr.vbs /dlv
    ```

2. Se **slmgr.vbs /dlv** mostra o canal de revenda, execute os seguintes comandos para definir o [chave de configuração de cliente do KMS](https://technet.microsoft.com/library/jj612867%28v=ws.11%29.aspx?f=255&MSPPError=-2147217396) para a versão do Windows Server que está a ser utilizado e forçá-lo novamente a ativação: 

    ```
    cscript c:\windows\system32\slmgr.vbs /ipk <KMS client setup key>

    cscript c:\windows\system32\slmgr.vbs /ato
     ```

    Por exemplo, para o Datacenter do Windows Server 2016, deve executar o seguinte comando:

    ```
    cscript c:\windows\system32\slmgr.vbs /ipk CB7KF-BWN84-R7R2Y-793K2-8XDDG
    ```

### <a name="step-2-verify-the-connectivity-between-the-vm-and-azure-kms-service"></a>Passo 2-verificar a conectividade entre o serviço de VM e do Azure KMS

1. Transferir e extrair o [Psping](http:/technet.microsoft.com/en-us/sysinternals/jj729731.aspx) ferramenta para uma pasta local na VM que não a ativar. 

2. Aceda ao início, uma procura do Windows PowerShell, clique com o botão direito do Windows PowerShell e, em seguida, selecionar executar como administrador.

3. Certifique-se de que a VM está configurada para utilizar o servidor Azure KMS correto. Para tal, execute o seguinte comando:
  
    ```
    iex “$env:windir\system32\cscript.exe $env:windir\system32\slmgr.vbs /skms
    kms.core.windows.net:1688
    ```
    O comando deverá devolver: nome da máquina de serviço de gestão de chaves definido como kms.core.windows.net:1688 com êxito.

4. Certifique-se utilizando o Psping que tem conetividade ao servidor do KMS. Mude para a pasta onde extraiu a transferência Pstools.zip e, em seguida, execute o seguinte:
  
    ```
    \psping.exe kms.core.windows.net:1688
    ```
  
  Na linha segundo a última de saída, certifique-se de que vê: enviados = 4, recebidos = 4, Lost = 0 (0% perda).

  Se Lost for maior que 0 (zero), a VM não tem conectividade ao servidor KMS. Nesta situação, é capaz de resolver kms.core.windows.net se a VM se encontra numa rede virtual e tiver um servidor DNS personalizado especificado, tem de se certificar que o servidor DNS. Em alternativa, alterar o servidor DNS para um que resolver kms.core.windows.net.

  Tenha em atenção que, se remover todos os servidores DNS de uma rede virtual, VMs utilizem interno do serviço DNS do Azure. Este serviço pode ser resolvido kms.core.windows.net.
  
Certifique-se também de que a firewall do convidado não foi configurada de forma que bloqueariam tentativas de ativação.

5. Depois de verificar a conetividade concluída com êxito kms.core.windows.net, execute o seguinte comando nessa linha de comandos elevada do Windows PowerShell. Este comando tenta a ativação várias vezes.

    ```
    1..12 | % { iex “$env:windir\system32\cscript.exe $env:windir\system32\slmgr.vbs /ato” ; start-sleep 5 }
    ```

Uma ativação com êxito devolve as informações que é semelhante a:

**Ativar Windows(R), ServerDatacenter edition (12345678-1234-1234-1234-12345678)... Produto ativado com êxito.**

## <a name="faq"></a>FAQ 

### <a name="i-created-the-windows-server-2016-from-azure-marketplace-do-i-need-to-configure-kms-key-for-activating-the-windows-server-2016"></a>Criar o Windows Server 2016 do Azure Marketplace. É necessário configurar a chave KMS para ativar o Windows Server 2016 
 
Não. A imagem no Azure Marketplace tiver a chave de configuração do cliente KMS adequada já configurada. 

### <a name="does-windows-activation-work-the-same-way-regardless-if-the-vm-is-using-azure-hybrid-use-benefit-hub-or-not"></a>Ativação do Windows funciona da mesma forma independentemente se a VM estiver a utilizar híbrida utilize benefício (do HUB do Azure) ou não? 
 
Sim. 
 
### <a name="what-happens-if-windows-activation-period-expires"></a>O que acontece se expirar o período de ativação do Windows? 
 
Quando o período de tolerância expirou e o Windows ainda não se encontra ativado, o Windows Server 2008 R2 e versões posteriores do Windows irão mostrar notificações adicionais sobre a ativação. A imagem de fundo de ambiente de trabalho permanece preta e Windows Update irá instalar a segurança e apenas as atualizações críticas, mas não opcionais atualizações. Consulte a secção de notificações na parte inferior do [condições de licenciamento](http://technet.microsoft.com/en-us/library/ff793403.aspx) página.   

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contacte o suporte.
Se ainda precisar de ajuda, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para obter o seu problema resolvido rapidamente.