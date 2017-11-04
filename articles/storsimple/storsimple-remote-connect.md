---
title: Ligar remotamente ao dispositivo StorSimple | Microsoft Docs
description: "Explica como configurar o seu dispositivo para a gestão remota e como ligar ao Windows PowerShell para StorSimple através de HTTP ou HTTPS."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 923377aa-f451-4656-87de-5e95a34a6a2a
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b916173e127394d3ea06eded36285bdbbf884b12
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="connect-remotely-to-your-storsimple-8000-series-device"></a>Ligar remotamente ao dispositivo de série 8000 do StorSimple

## <a name="overview"></a>Descrição geral
Pode utilizar a comunicação remota do Windows PowerShell para ligar ao dispositivo StorSimple. Quando se liga desta forma, não verá um menu. (Pode ver um menu apenas se utilizar a consola de série do dispositivo para ligar.) Com comunicação remota do Windows PowerShell, ligar a um espaço de execução específico. Também pode especificar o idioma de apresentação. 

Para obter mais informações sobre como utilizar a comunicação remota do Windows PowerShell para gerir o seu dispositivo, vá para [utilize o Windows PowerShell para StorSimple administrar o dispositivo StorSimple](storsimple-windows-powershell-administration.md).

Este tutorial explica como configurar o seu dispositivo para a gestão remota e, em seguida, como ligar ao Windows PowerShell para StorSimple. Pode utilizar HTTP ou HTTPS para ligar através de comunicação remota do Windows PowerShell. No entanto, quando são decidir como ligar ao Windows PowerShell para StorSimple, considere o seguinte: 

* Ligar diretamente a consola de série do dispositivo é seguro, mas a ligação à consola de série através de comutadores de rede não está. Tenha cuidado do risco de segurança ao ligar a consola de série do dispositivo através de comutadores de rede. 
* Ligar através de uma sessão HTTP poderá oferecem mais segurança a que se ligam através da consola de série através da rede. Embora não seja o método mais seguro, é aceitável em redes fidedignas. 
* Ligar através de uma sessão HTTPS com um certificado auto-assinado é a mais segura e a opção recomendada.

Pode ligar remotamente à interface do Windows PowerShell. No entanto, o acesso remoto para o seu dispositivo StorSimple através da interface do Windows PowerShell não está ativado por predefinição. Terá de ativar a gestão remota no dispositivo pela primeira vez e, em seguida, no cliente que é utilizado para aceder ao seu dispositivo.

Os passos descritos neste artigo foram executados num sistema anfitrião com o Windows Server 2012 R2.

## <a name="connect-through-http"></a>Ligar através de HTTP
A ligar para o Windows PowerShell para StorSimple através de uma sessão HTTP oferece mais segurança a que se ligam através da consola de série do dispositivo StorSimple. Embora não seja o método mais seguro, é aceitável em redes fidedignas.

Pode utilizar o portal clássico do Azure ou a consola de série para configurar a gestão remota. Selecione entre os seguintes procedimentos:

* [Utilize o portal clássico do Azure para ativar a gestão remota através de HTTP](#use-the-azure-classic-portal-to-enable-remote-management-over-http)
* [Utilize a consola de série para ativar a gestão remota através de HTTP](#use-the-serial-console-to-enable-remote-management-over-http)

Depois de ativar a gestão remota, utilize o procedimento seguinte para preparar o cliente para uma ligação remota.

* [Preparar o cliente para a ligação remota](#prepare-the-client-for-remote-connection)

### <a name="use-the-azure-classic-portal-to-enable-remote-management-over-http"></a>Utilize o portal clássico do Azure para ativar a gestão remota através de HTTP
Execute os seguintes passos no portal clássico do Azure para ativar a gestão remota através de HTTP.

#### <a name="to-enable-remote-management-through-the-azure-classic-portal"></a>Para ativar a gestão remota através do portal clássico do Azure
1. Acesso **dispositivos** > **configurar** para o seu dispositivo.
2. Desloque-se para baixo até à secção **Gestão Remota**.
3. Defina **Ativar Gestão Remota** como **Sim**.
4. Pode agora optar por ligar através de HTTP. (A predefinição é ligar através de HTTPS). Certifique-se de que o HTTP está selecionado.
   
   > [!NOTE]
   > A ligação através de HTTP é aceitável apenas em redes fidedignas.
   > 
   > 
5. Clique em **Guardar** na parte inferior da página.

### <a name="use-the-serial-console-to-enable-remote-management-over-http"></a>Utilize a consola de série para ativar a gestão remota através de HTTP
Execute os passos seguintes na consola de série do dispositivo para ativar a gestão remota.

#### <a name="to-enable-remote-management-through-the-device-serial-console"></a>Para ativar a gestão remota através da consola de série do dispositivo
1. No menu da consola de série, selecione a opção 1. Para obter mais informações sobre como utilizar a consola de série do dispositivo, vá para [ligar ao Windows PowerShell para StorSimple através da consola de série do dispositivo](storsimple-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console).
2. Na linha de comandos, escreva:`Enable-HcsRemoteManagement –AllowHttp`
3. Será notificado sobre vulnerabilidades de segurança da utilização de HTTP para ligar ao dispositivo. Quando lhe for solicitado, confirme escrevendo **Y**.
4. Certifique-se de que está ativado HTTP escrevendo:`Get-HcsSystem`
5. Certifique-se de que o **RemoteManagementMode** campo mostra **HttpsAndHttpEnabled**. A ilustração seguinte mostra estas definições no PuTTY.
   
     ![Série HTTPS e HTTP ativada](./media/storsimple-remote-connect/HCS_SerialHttpsAndHttpEnabled.png)

### <a name="prepare-the-client-for-remote-connection"></a>Preparar o cliente para a ligação remota
Execute os seguintes passos no cliente para ativar a gestão remota.

#### <a name="to-prepare-the-client-for-remote-connection"></a>Para preparar o cliente para a ligação remota
1. Inicie uma sessão do Windows PowerShell como administrador.
2. Escreva o seguinte comando para adicionar o endereço IP do dispositivo StorSimple à lista de anfitriões fidedigna do cliente: 
   
     `Set-Item wsman:\localhost\Client\TrustedHosts <device_ip> -Concatenate -Force`
   
     Substitua <*device_ip*> com o endereço IP do seu dispositivo; por exemplo: 
   
     `Set-Item wsman:\localhost\Client\TrustedHosts 10.126.173.90 -Concatenate -Force`
3. Escreva o seguinte comando para guardar as credenciais de dispositivo numa variável: 
   
    ```
    $cred = Get-Credential
    ```
    
4. Na caixa de diálogo que é apresentado:
   
   1. Escreva o nome de utilizador neste formato: *device_ip\SSAdmin*.
   2. Escreva a palavra-passe de administrador dispositivo que foi definida quando o dispositivo foi configurado com o Assistente de configuração. A palavra-passe predefinida é *Password1*.
5. Inicie uma sessão do Windows PowerShell no dispositivo, escrevendo este comando:
   
     `Enter-PSSession -Credential $cred -ConfigurationName SSAdminConsole -ComputerName <device_ip>`
   
   > [!NOTE]
   > Para criar uma sessão do Windows PowerShell para utilização com o dispositivo virtual StorSimple, acrescente o `–Port` parâmetro e especifique a porta pública que configurou no sistema de interação remota para o dispositivo Virtual StorSimple.
   > 
   > 
   
     Nesta fase, deve ter uma sessão do Windows PowerShell remota ativa para o dispositivo.
   
    ![Comunicação remota do PowerShell através de HTTP](./media/storsimple-remote-connect/HCS_PSRemotingUsingHTTP.png)

## <a name="connect-through-https"></a>Ligar através de HTTPS
A ligar para o Windows PowerShell para StorSimple através de uma sessão HTTPS é o método mais seguro e recomendado para ligar remotamente ao dispositivo Microsoft Azure StorSimple. Os procedimentos seguintes explicam como configurar os computadores cliente e a consola de série para que possa utilizar HTTPS para ligar ao Windows PowerShell para StorSimple.

Pode utilizar o portal clássico do Azure ou a consola de série para configurar a gestão remota. Selecione entre os seguintes procedimentos:

* [Utilize o portal clássico do Azure para ativar a gestão remota através de HTTPS](#use-the-azure-classic-portal-to-enable-remote-management-over-https)
* [Utilize a consola de série para ativar a gestão remota através de HTTPS](#use-the-serial-console-to-enable-remote-management-over-https)

Depois de ativar a gestão remota, utilize os procedimentos seguintes para preparar o anfitrião para um gestão remota e ligar ao dispositivo a partir do anfitrião remoto.

* [Preparar o anfitrião para a gestão remota](#prepare-the-host-for-remote-management)
* [Ligar ao dispositivo a partir do anfitrião remoto](#connect-to-the-device-from-the-remote-host)

### <a name="use-the-azure-classic-portal-to-enable-remote-management-over-https"></a>Utilize o portal clássico do Azure para ativar a gestão remota através de HTTPS
Execute os seguintes passos no portal clássico do Azure para ativar a gestão remota através de HTTPS.

#### <a name="to-enable-remote-management-over-https-from-the-azure-classic-portal"></a>Para ativar a gestão remota através de HTTPS do portal clássico do Azure
1. Acesso **dispositivos** > **configurar** para o seu dispositivo.
2. Desloque-se para baixo até à secção **Gestão Remota**.
3. Defina **Ativar Gestão Remota** como **Sim**.
4. Agora pode optar por ligar através de HTTPS. (A predefinição é ligar através de HTTPS). Certifique-se de que o HTTPS é selecionado. 
5. Clique em **Transferir certificado de gestão remota**. Especifique uma localização para guardar este ficheiro. Terá de instalar este certificado no computador cliente ou anfitrião que irá utilizar para ligar ao dispositivo.
6. Clique em **Guardar** na parte inferior da página.

### <a name="use-the-serial-console-to-enable-remote-management-over-https"></a>Utilize a consola de série para ativar a gestão remota através de HTTPS
Execute os passos seguintes na consola de série do dispositivo para ativar a gestão remota.

#### <a name="to-enable-remote-management-through-the-device-serial-console"></a>Para ativar a gestão remota através da consola de série do dispositivo
1. No menu da consola de série, selecione a opção 1. Para obter mais informações sobre como utilizar a consola de série do dispositivo, vá para [ligar ao Windows PowerShell para StorSimple através da consola de série do dispositivo](storsimple-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console).
2. Na linha de comandos, escreva: 
   
     `Enable-HcsRemoteManagement`
   
    Isto deve ativar HTTPS no seu dispositivo.
3. Certifique-se de que foi ativado HTTPS, escrevendo: 
   
     `Get-HcsSystem`
   
    Certifique-se de que o **RemoteManagementMode** campo mostra **HttpsEnabled**. A ilustração seguinte mostra estas definições no PuTTY.
   
     ![Série ativadas por HTTPS](./media/storsimple-remote-connect/HCS_SerialHttpsEnabled.png)
4. Da saída das `Get-HcsSystem`, copie o número de série do dispositivo e guardá-lo para utilização posterior.
   
   > [!NOTE]
   > O número de série mapeia para o nome CN do certificado.
   > 
   > 
5. Obter um certificado de gestão remota, escrevendo: 
   
     `Get-HcsRemoteManagementCert`
   
    Um certificado semelhante ao seguinte serão apresentados.
   
    ![Obter o certificado de gestão remota](./media/storsimple-remote-connect/HCS_GetRemoteManagementCertificate.png)
6. Copie as informações do certificado de **---BEGIN CERTIFICATE---** para **---fim certificado---** num editor de texto, como o bloco de notas e guarde-como um ficheiro. cer. (Será copiar este ficheiro para o anfitrião remoto quando preparar o anfitrião.)
   
   > [!NOTE]
   > Para gerar um novo certificado, utilize o `Set-HcsRemoteManagementCert` cmdlet.
   > 
   > 

### <a name="prepare-the-host-for-remote-management"></a>Preparar o anfitrião para a gestão remota
Para preparar o computador anfitrião para uma ligação remota que utiliza uma sessão HTTPS, execute os seguintes procedimentos:

* [Importar o ficheiro. cer para o arquivo de raiz do cliente ou anfitrião remoto](#to-import-the-certificate-on-the-remote-host).
* [Adicionar os números de série do dispositivo para o ficheiro de anfitriões no seu anfitrião remoto](#to-add-device-serial-numbers-to-the-remote-host).

Cada um destes procedimentos é descrita abaixo.

#### <a name="to-import-the-certificate-on-the-remote-host"></a>Para importar o certificado no anfitrião remoto
1. O ficheiro. cer com o botão direito e selecione **instalar certificado**. Isto irá iniciar o Assistente de importação de certificados.
   
    ![Assistente de importação de certificados 1](./media/storsimple-remote-connect/HCS_CertificateImportWizard1.png)
2. Para **localização do arquivo**, selecione **máquina Local**e, em seguida, clique em **seguinte**.
3. Selecione **colocar todos os certificados no seguinte arquivo**e, em seguida, clique em **procurar**. Navegue para o arquivo de raiz do seu anfitrião remoto e, em seguida, clique em **seguinte**.
   
    ![Assistente de importação de certificados 2](./media/storsimple-remote-connect/HCS_CertificateImportWizard2.png)
4. Clique em **Concluir**. É apresentada uma mensagem que indica que a importação foi concluída com êxito.
   
    ![Assistente de importação de certificados 3](./media/storsimple-remote-connect/HCS_CertificateImportWizard3.png)

#### <a name="to-add-device-serial-numbers-to-the-remote-host"></a>Para adicionar números de série do dispositivo para o anfitrião remoto
1. Inicie o bloco de notas como administrador e, em seguida, abra o ficheiro hosts localizado em \Windows\System32\Drivers\etc.
2. Adicione as seguintes três entradas ao seu ficheiro de anfitriões: **endereço IP de dados 0**, **endereço de IP fixo do controlador 0**, e **endereço IP fixo do controlador 1**.
3. Introduza o número de série do dispositivo que guardou anteriormente. Mapear isto para o endereço IP conforme mostrado na imagem seguinte. Para o controlador 0 e 1 de controlador, acrescentar **Controller0** e **Controller1** no final do número de série (nome CN).
   
    ![Adicionar nome CN ao ficheiro de anfitriões](./media/storsimple-remote-connect/HCS_AddingCNNameToHostsFile.png)
4. Guarde o ficheiro de anfitriões.

### <a name="connect-to-the-device-from-the-remote-host"></a>Ligar ao dispositivo a partir do anfitrião remoto
Utilize o Windows PowerShell e do SSL para introduzir uma sessão de SSAdmin no seu dispositivo a partir de um anfitrião remoto ou o cliente. A sessão de SSAdmin mapeia para a opção 1 no [consola de série](storsimple-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console) menu do seu dispositivo.

Execute o seguinte procedimento no computador a partir do qual pretende estabelecer a ligação remota do Windows PowerShell.

#### <a name="to-enter-an-ssadmin-session-on-the-device-by-using-windows-powershell-and-ssl"></a>Para introduzir uma sessão de SSAdmin no dispositivo utilizando o Windows PowerShell e do SSL
1. Inicie uma sessão do Windows PowerShell como administrador.
2. Adicione endereço IP do dispositivo para anfitriões fidedigna do cliente, escrevendo:
   
     `Set-Item wsman:\localhost\Client\TrustedHosts <device_ip> -Concatenate -Force`
   
    Onde <*device_ip*> é o endereço IP do seu dispositivo; por exemplo: 
   
     `Set-Item wsman:\localhost\Client\TrustedHosts 10.126.173.90 -Concatenate -Force`
3. Crie uma credencial nova escrevendo: 
   
     `$cred = New-Object pscredential @("<IP of target device>\SSAdmin", (ConvertTo-SecureString -Force -AsPlainText "<Device Administrator Password>"))`
   
    Onde <*IP do dispositivo-alvo*> é o endereço IP de dados 0 para o seu dispositivo; por exemplo, **10.126.173.90** conforme mostrado na imagem anterior do ficheiro de anfitriões. Além disso, forneça a palavra-passe de administrador para o seu dispositivo.
4. Crie uma sessão introduzindo:
   
     `$session = New-PSSession -UseSSL -ComputerName <Serial number of target device> -Credential $cred -ConfigurationName "SSAdminConsole"`
   
    Para o parâmetro - ComputerName no cmdlet, forneça o <*número de série do dispositivo-alvo*>. Este número de série foi mapeado para o endereço IP de dados 0 no ficheiro de anfitriões no seu anfitrião remoto; Por exemplo, **SHX0991003G44MT** conforme mostrado na imagem seguinte.
5. Escreva: 
   
     `Enter-PSSession $session`
6. Terá de aguardar alguns minutos e, em seguida, será ligado para o seu dispositivo através de HTTPS através de SSL. Verá uma mensagem que indica que está ligado ao seu dispositivo.
   
    ![Comunicação remota do PowerShell através de HTTPS e SSL](./media/storsimple-remote-connect/HCS_PSRemotingUsingHTTPSAndSSL.png)

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [através do Windows PowerShell para administrar o dispositivo StorSimple](storsimple-windows-powershell-administration.md).
* Saiba mais sobre [utilizando o serviço StorSimple Manager para administrar o dispositivo StorSimple](storsimple-manager-service-administration.md).

