---
title: Criar um pacote de suporte do StorSimple | Microsoft Docs
description: Saiba como criar, desencriptar e editar um pacote de suporte para o dispositivo StorSimple.
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: eac76f5f-5db1-4c92-af8c-54053b91e66c
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: addfb01998271ee3a431d92bf2a6fec70f0577a6
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2017
---
# <a name="create-and-manage-a-storsimple-support-package"></a>Criar e gerir um pacote de suporte do StorSimple
> [!NOTE]
> O portal clássico para StorSimple foi preterido. Os gestores de dispositivo do StorSimple será automaticamente mudada para o novo portal do Azure de acordo com a agenda de preterição. Irá receber uma mensagem de e-mail e uma notificação de movimentação do portal. Este documento também será descontinuado em breve. Para ver a versão deste artigo para o novo portal do Azure, aceda a [criar e gerir um pacote de suporte do StorSimple](storsimple-8000-create-manage-support-package.md). Para quaisquer perguntas sobre a mudança, consulte [FAQ: mover para o portal do Azure](storsimple-8000-move-azure-portal-faq.md).

## <a name="overview"></a>Descrição geral
Um pacote de suporte do StorSimple é um mecanismo de fácil utilização que recolhe todos os registos relevantes para ajudar a resolver quaisquer problemas de dispositivos do StorSimple Support da Microsoft. Os registos recolhidos são comprimidos e encriptados.

Este tutorial inclui instruções passo a passo para criar e gerir o pacote de suporte.

## <a name="create-and-upload-a-support-package-in-the-azure-classic-portal"></a>Criar e carregar um pacote de suporte no portal clássico do Azure
Pode criar e carregar um pacote de suporte para o site Support da Microsoft através de **manutenção** página do serviço no portal clássico do Azure.

> [!NOTE]
> O carregamento requer um passkey de suporte. Engenheiro de suporte deve fornecer esta-lhe um e-mail.
> 
> 

Um pacote de suporte comprimidos e encriptados (ficheiro. cab) é criado e carregado para o site de suporte. O engenheiro de suporte, em seguida, pode obter este pacote do site de suporte para resolver o problema.

Execute os seguintes passos no portal clássico para criar um pacote de suporte.

#### <a name="to-create-a-support-package-in-the-azure-classic-portal"></a>Para criar um pacote de suporte no portal clássico do Azure
1. Selecione **dispositivos** > **manutenção**.
2. No **pacote de suporte** secção, selecione **criar e carregar pacote de suporte**.
3. No **criar e carregar pacote de suporte** diálogo caixa, efetue o seguinte:
   
    ![Criar o pacote de suporte](./media/storsimple-create-manage-support-package/IC740923.png)
   
   * No **suporte Passkey** texto, introduza o passkey. Engenheiro de suporte da Microsoft deve enviar esta passkey para si por e-mail.
   * Selecione a caixa de verificação para fornecer consentimento para carregar automaticamente o pacote de suporte para o site Support da Microsoft.
   * Clique no ícone de verificação ![Ícone de verificação](./media/storsimple-create-manage-support-package/IC740895.png).

## <a name="manually-create-a-support-package"></a>Criar manualmente um pacote de suporte
Em alguns casos, terá de criar manualmente o pacote de suporte através do Windows PowerShell para StorSimple. Por exemplo:

* Se precisar de remover os ficheiros de registo antes de partilhar com Support da Microsoft a informações confidenciais.
* Se estiver a ter dificuldade em carregar o pacote devido a problemas de conectividade.

Pode partilhar o pacote de suporte manualmente gerado com Support da Microsoft através de correio eletrónico. Execute os seguintes passos para criar um pacote de suporte no Windows PowerShell para StorSimple.

#### <a name="to-create-a-support-package-in-windows-powershell-for-storsimple"></a>Para criar um pacote de suporte no Windows PowerShell para StorSimple
1. Para iniciar uma sessão do Windows PowerShell como administrador no computador remoto que é utilizado para ligar ao dispositivo StorSimple, introduza o seguinte comando:
   
    `Start PowerShell`
2. Na sessão do Windows PowerShell, ligar à consola do SSAdmin do seu dispositivo:
   
   * Na linha de comandos, introduza:
     
       `$MS = New-PSSession -ComputerName <IP address for DATA 0> -Credential SSAdmin -ConfigurationName "SSAdminConsole"`
   * Na caixa de diálogo aberta, introduza a palavra-passe de administrador do dispositivo. A palavra-passe predefinida é:
     
      `Password1`
     
      ![Caixa de diálogo de credencial de PowerShell](./media/storsimple-create-manage-support-package/IC740962.png)
   * Selecione **OK**.
   * Na linha de comandos, introduza:
     
      `Enter-PSSession $MS`
3. Na sessão que se abre, introduza o comando adequado.
   
   * Para partilhas de rede que estão protegido de palavra-passe, introduza:
     
       `Export-HcsSupportPackage –PackageTag "MySupportPackage" –Credential "Username" -Force`
     
       Será solicitada uma palavra-passe, um caminho para a pasta partilhada de rede e uma frase de acesso de encriptação (porque o pacote de suporte é encriptado). Em seguida, é criado um pacote de suporte na pasta especificada.
   * Para partilhas não estão protegido de palavra-passe, não é necessário o `-Credential` parâmetro. Introduza o seguinte:
     
       `Export-HcsSupportPackage –PackageTag "MySupportPackage" -Force`
     
       Criar o pacote de suporte para os dois controladores na pasta partilhada de rede especificado. É um ficheiro de encriptados, comprimido que pode ser enviado ao Microsoft Support para resolução de problemas. Para obter mais informações, consulte [contactar o suporte da Microsoft](storsimple-contact-microsoft-support.md).

### <a name="the-export-hcssupportpackage-cmdlet-parameters"></a>Os parâmetros do cmdlet HcsSupportPackage de exportação
Pode utilizar os seguintes parâmetros com o cmdlet Export-HcsSupportPackage.

| Parâmetro | Necessários/opcionais | Descrição |
| --- | --- | --- |
| `-Path` |Necessário |Utilize para fornecer a localização da pasta partilhada rede em que o pacote de suporte está colocado. |
| `-EncryptionPassphrase` |Necessário |Utilize para fornecer uma frase de acesso para o ajudar a encriptar o pacote de suporte. |
| `-Credential` |Opcional |Utilize para fornecer credenciais de acesso para a pasta partilhada de rede. |
| `-Force` |Opcional |Utilize para ignorar o passo de confirmação da frase de acesso de encriptação. |
| `-PackageTag` |Opcional |Utilize para especificar um diretório na *caminho* no qual o pacote de suporte está colocado. A predefinição é a [nome do dispositivo]-[à data atual e time:yyyy-MM-dd-HH-mm-ss]. |
| `-Scope` |Opcional |Especificar como **Cluster** (predefinição) para criar um pacote de suporte para os dois controladores. Se pretender criar um pacote apenas para o controlador atual, especifique **controlador**. |

## <a name="edit-a-support-package"></a>Editar um pacote de suporte
Depois de ter gerado um pacote de suporte, poderá ter de editar o pacote para remover informações confidenciais. Isto pode incluir os nomes de volume, endereços IP do dispositivo e nomes de cópia de segurança dos ficheiros do registo.

> [!IMPORTANT]
> Só é possível editar um pacote de suporte que foi gerado através do Windows PowerShell para StorSimple. Não é possível editar um pacote criado no portal clássico do Azure com o serviço StorSimple Manager.
> 
> 

Para editar um pacote de suporte antes de o carregar no site Support da Microsoft, primeiro desencriptar o pacote de suporte, editar os ficheiros e, em seguida, reencriptá-lo. Execute os seguintes passos.

#### <a name="to-edit-a-support-package-in-windows-powershell-for-storsimple"></a>Para editar um pacote de suporte no Windows PowerShell para StorSimple
1. Gerar um pacote de suporte, conforme descrito anteriormente, [para criar um pacote de suporte no Windows PowerShell para StorSimple](#to-create-a-support-package-in-windows-powershell-for-storsimple).
2. [Transferir o script](http://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) localmente no cliente.
3. Importe o módulo do Windows PowerShell. Especifique o caminho para a pasta local em que tiver transferido o script. Para importar o módulo, introduza:
   
    `Import-module <Path to the folder that contains the Windows PowerShell script>`
4. Todos os ficheiros são *.aes* ficheiros que são comprimidos e encriptados. Para descomprimir e desencriptar ficheiros, introduza:
   
    `Open-HcsSupportPackage <Path to the folder that contains support package files>`
   
    Tenha em atenção que as extensões de ficheiro real são agora apresentadas para todos os ficheiros.
   
    ![Editar o pacote de suporte](./media/storsimple-create-manage-support-package/IC750706.png)
5. Quando lhe for pedida a frase de acesso de encriptação, introduza a frase de acesso que utilizou quando o pacote de suporte foi criado.
   
        cmdlet Open-HcsSupportPackage at command pipeline position 1
   
        Supply values for the following parameters:EncryptionPassphrase: ****
6. Procure a pasta que contém os ficheiros de registo. Uma vez que os ficheiros de registo são agora descomprimir e desencriptar, estes terão as extensões de ficheiro original. Modifique estes ficheiros para remover quaisquer informações específicas do cliente, como nomes de volume e endereços IP do dispositivo e guardar os ficheiros.
7. Feche os ficheiros para comprimi-los com gzip e encriptá-las com AES 256. Trata-se para a velocidade e segurança de transferir o pacote de suporte através de uma rede. Para comprimir e encriptar os ficheiros, introduza o seguinte:
   
    `Close-HcsSupportPackage <Path to the folder that contains support package files>`
   
    ![Editar o pacote de suporte](./media/storsimple-create-manage-support-package/IC750707.png)
8. Quando solicitado, forneça uma frase de acesso de encriptação para o pacote de suporte modificadas.
   
        cmdlet Close-HcsSupportPackage at command pipeline position 1
        Supply values for the following parameters:EncryptionPassphrase: ****
9. Anote a nova frase de acesso, para que pode partilhá-lo com o Microsoft Support quando solicitado.

### <a name="example-editing-files-in-a-support-package-on-a-password-protected-share"></a>Exemplo: Editar ficheiros de um pacote de suporte numa partilha protegida por palavra-passe
O exemplo seguinte mostra como desencriptar, editar e reencriptar um pacote de suporte.

        PS C:\WINDOWS\system32> Import-module C:\Users\Default\StorSimple\SupportPackage\HCSSupportPackageTools.psm1

        PS C:\WINDOWS\system32> Open-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw

        cmdlet Open-HcsSupportPackage at command pipeline position 1

        Supply values for the following parameters:

        EncryptionPassphrase: ****

        PS C:\WINDOWS\system32> Close-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw

        cmdlet Close-HcsSupportPackage at command pipeline position 1

        Supply values for the following parameters:

        EncryptionPassphrase: ****

        PS C:\WINDOWS\system32>

## <a name="next-steps"></a>Passos seguintes
* Saiba como [utilizar pacotes de suporte e registos de dispositivos para resolver problemas relacionados com a implementação de dispositivo](storsimple-troubleshoot-deployment.md#support-packages-and-device-logs-available-for-troubleshooting).
* Saiba como [utilizam o serviço StorSimple Manager para administrar o dispositivo StorSimple](storsimple-manager-service-administration.md).

