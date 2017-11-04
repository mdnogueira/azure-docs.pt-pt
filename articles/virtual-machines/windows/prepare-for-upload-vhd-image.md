---
title: Preparar um VHD do Windows para carregar para o Azure | Microsoft Docs
description: Como preparar um VHD do Windows ou o VHDX antes de carregar para o Azure
services: virtual-machines-windows
documentationcenter: 
author: glimoli
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 7802489d-33ec-4302-82a4-91463d03887a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: 67832fd20b758af6fd7a31c0099ce8019bb2442d
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2017
---
# <a name="prepare-a-windows-vhd-or-vhdx-to-upload-to-azure"></a>Preparar um VHD do Windows ou o VHDX para carregar para o Azure
Antes de carregar uma Windows as máquinas virtuais (VM) no local para o Microsoft Azure, tem de preparar o disco rígido virtual (VHD ou VHDX). Azure suporta apenas as VMs de geração 1 que estão no formato de ficheiro VHD e tem um disco de tamanho fixo. O tamanho máximo permitido para o VHD é 1,023 GB. Pode converter uma geração 1 VM a partir do VHDX ficheiro sistema VHD e a partir de um disco de expansão dinâmica para tamanho fixo. Mas não é possível alterar a geração de uma VM. Para obter mais informações, consulte [devo criar uma geração 1 ou 2 VM no Hyper-V](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).

Para obter mais informações sobre a política de suporte para a VM do Azure, consulte [servidor software o suporte da Microsoft para as VMs do Microsoft Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).

> [!Note]
> As instruções neste artigo aplicam-se para a versão de 64 bits do Windows Server 2008 R2 e o sistema de operativo posterior do Windows server. Para obter informações sobre a versão de 32 bits em execução do sistema operativo no Azure, consulte [suporte para sistemas operativos de 32 bits em máquinas virtuais do Azure](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines).

## <a name="convert-the-virtual-disk-to-vhd-and-fixed-size-disk"></a>Converter o disco virtual para o VHD e o disco de tamanho fixo 
Se precisar de converter o disco virtual para o formato necessário para o Azure, utilize um dos métodos nesta secção. Cópia de segurança da VM antes de executar o processo de conversão do disco virtual e certifique-se de que o VHD do Windows funciona corretamente no servidor local. Resolva os eventuais erros na própria VM antes de tentar converter ou carregá-la para o Azure.

Depois de converter o disco, crie uma VM que utiliza o disco convertido. Abra e inicie sessão VM para concluir a preparação da VM para o carregamento.

### <a name="convert-disk-using-hyper-v-manager"></a>Converter disco com o Gestor de Hyper-V
1. Abra o Gestor de Hyper-V e selecione o seu computador local à esquerda. No menu superior a lista de computador, clique em **ação** > **editar disco**.
2. No **localizar disco rígido Virtual** ecrã, localize e selecione o disco virtual.
3. No **selecionar ação** ecrã e, em seguida, selecione **converter** e **seguinte**.
4. Se precisar de efetuar a conversão de VHDX, selecione **VHD** e, em seguida, clique em **seguinte**
5. Se precisar de efetuar a conversão de um disco de expansão dinâmica, selecione **um tamanho fixo** e, em seguida, clique em **seguinte**
6. Localize e selecione um caminho para guardar o novo ficheiro VHD.
7. Clique em **Concluir**.

>[!NOTE]
>Os comandos neste artigo tem de ser executados numa sessão do PowerShell elevada.

### <a name="convert-disk-by-using-powershell"></a>Converter disco utilizando o PowerShell
Pode converter um disco virtual utilizando o [Convert-VHD](http://technet.microsoft.com/library/hh848454.aspx) comando no Windows PowerShell. Selecione **executar como administrador** quando inicie o PowerShell. 

Converte o seguinte comando de exemplo de VHDX ao VHD e de tamanho fixo de um disco expansão dinâmica:

```Powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```
Neste comando, substitua o valor para "-caminho" com o caminho para o disco rígido virtual que pretende converter e o valor de "-DestinationPath" com o novo caminho e nome do disco convertido.

### <a name="convert-from-vmware-vmdk-disk-format"></a>Converter o formato de disco VMDK de VMware
Se tiver uma imagem de VM do Windows a [formato de ficheiro VMDK](https://en.wikipedia.org/wiki/VMDK), convertê-lo a um VHD, utilizando o [Microsoft VM conversor](https://www.microsoft.com/download/details.aspx?id=42497). Para obter mais informações, consulte o artigo de blogue [como converter um VMDK VMware em Hyper-V VHD](http://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx).

## <a name="set-windows-configurations-for-azure"></a>Definir as configurações do Windows para o Azure

Na VM que pretende carregar para o Azure, execute todos os comandos nos seguintes passos de uma [janela de linha de comandos elevada](https://technet.microsoft.com/library/cc947813.aspx):

1. Remova quaisquer rota estática persistente na tabela de encaminhamento:
   
   * Para ver a tabela de rotas, execute `route print` na linha de comandos.
   * Verifique o **rotas de persistência** secções. Se existir uma rota persistente, utilize [rota eliminar](https://technet.microsoft.com/library/cc739598.apx) removê-lo.
2. Remova o proxy de WinHTTP:
   
    ```PowerShell
    netsh winhttp reset proxy
    ```
3. Defina a política de SAN de disco para [Onlineall](https://technet.microsoft.com/library/gg252636.aspx). 
   
    ```PowerShell
    diskpart 
    ```
    Na janela de linha de comandos aberta, escreva os seguintes comandos:

     ```DISKPART
    san policy=onlineall
    exit   
    ```

4. Definir a hora de hora Universal Coordenada (UTC) para o Windows e o tipo de arranque do serviço de hora do Windows (w32time) para **automaticamente**:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\TimeZoneInformation' -name "RealTimeIsUniversal" 1 -Type DWord

    Set-Service -Name w32time -StartupType Auto
    ```
5. Definir o perfil de energia o **elevado desempenho**:

    ```PowerShell
    powercfg /setactive SCHEME_MIN
    ```

## <a name="check-the-windows-services"></a>Verifique os serviços do Windows
Certifique-se de que cada um dos seguintes serviços do Windows está definida para o **valores predefinidos do Windows**. Estes são os números mínimos dos serviços de que tem de ser definidos para se certificar de que a VM possui conectividade. Para repor as definições de arranque, execute os seguintes comandos:
   
```PowerShell
Set-Service -Name bfe -StartupType Auto
Set-Service -Name dhcp -StartupType Auto
Set-Service -Name dnscache -StartupType Auto
Set-Service -Name IKEEXT -StartupType Auto
Set-Service -Name iphlpsvc -StartupType Auto
Set-Service -Name netlogon -StartupType Manual
Set-Service -Name netman -StartupType Manual
Set-Service -Name nsi -StartupType Auto
Set-Service -Name termService -StartupType Manual
Set-Service -Name MpsSvc -StartupType Auto
Set-Service -Name RemoteRegistry -StartupType Auto
```

## <a name="update-remote-desktop-registry-settings"></a>Atualizar as definições de registo do ambiente de trabalho remoto
Certifique-se de que as seguintes definições estão configuradas corretamente para ligação ao ambiente de trabalho remoto:

>[!Note] 
>Poderá receber uma mensagem de erro quando executa o **Set ItemProperty-caminho ' HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal serviços - nome &lt;nome do objeto&gt; &lt;valor&gt;** nestes passos. A mensagem de erro pode ser ignorada. Apenas significa que o domínio não está a enviar se a configuração através de um objeto de política de grupo.
>
>

1. Protocolo RDP (Remote Desktop Protocol) está ativado:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0 -Type DWord

    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "fDenyTSConnections" -Value 0 -Type DWord
    ```
   
2. A porta RDP está corretamente definida (porta 3389 predefinida):
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "PortNumber" 3389 -Type DWord
    ```
    Quando implementar uma VM, as regras predefinidas são criadas em relação a porta 3389. Se pretender alterar o número de porta, fazê-lo após a implementação da VM no Azure.

3. O serviço de escuta está à escuta em cada interface de rede:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "LanAdapter" 0 -Type DWord
   ```
4. Configure o modo de autenticação de nível de rede para as ligações RDP:
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "UserAuthentication" 1 -Type DWord

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "SecurityLayer" 1 -Type DWord

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "fAllowSecProtocolNegotiation" 1 -Type DWord
     ```

5. Defina o valor de ligação keep-alive:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "KeepAliveEnable" 1 -Type DWord
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "KeepAliveInterval" 1 -Type DWord
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "KeepAliveTimeout" 1 -Type DWord
    ```
6. Restabeleça a ligação:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "fDisableAutoReconnect" 0 -Type DWord
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "fInheritReconnectSame" 1 -Type DWord
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "fReconnectSame" 0 -Type DWord
    ```
7. Limite o número de ligações simultâneas:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "MaxInstanceCount" 4294967295 -Type DWord
    ```
8. Se existirem quaisquer certificados autoassinados associados para o serviço de escuta do RDP, remova-as:
    
    ```PowerShell
    Remove-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "SSLCertificateSHA1Hash"
    ```
    Isto é certificar-se de que pode ligar no início ao implementar a VM. Também pode rever este numa fase posterior após a implementação da VM no Azure se for necessário.

9. Se a VM irá fazer parte de um domínio, verifique todas as definições seguintes para se certificar de que as definições anteriores não são revertidas. As políticas que tem de ser verificadas são os seguintes:
    
    - RDP está ativado:

         Computador Configuration\Policies\Windows Settings\Administrative Templates\ Components\Remote Services\Remote de ambiente de trabalho sessão de ambiente de trabalho Host\Connections:
         
         **Permitir que os utilizadores liguem remotamente utilizando o ambiente de trabalho remoto**

    - Política de grupo NLA:

        Settings\Administrative Host\Security de sessão de ambiente de trabalho de ambiente de trabalho Services\Remote Templates\Components\Remote: 
        
        **Exigir autenticação de utilizador para ligações remotas utilizando a autenticação de nível de rede**
    
    - Manter as definições de Alive:

        Computador Configuration\Policies\Windows Settings\Administrative administrativos\Componentes Components\Remote Services\Remote de ambiente de trabalho sessão de ambiente de trabalho Host\Connections: 
        
        **Configurar o intervalo de ligação keep-alive**

    - Definições de restabelecimento de ligação:

        Computador Configuration\Policies\Windows Settings\Administrative administrativos\Componentes Components\Remote Services\Remote de ambiente de trabalho sessão de ambiente de trabalho Host\Connections: 
        
        **Restabelecimento de ligação automático**

    - Limite o número de definições de ligações:

        Computador Configuration\Policies\Windows Settings\Administrative administrativos\Componentes Components\Remote Services\Remote de ambiente de trabalho sessão de ambiente de trabalho Host\Connections: 
        
        **Limitar o número de ligações**

## <a name="configure-windows-firewall-rules"></a>Configurar regras de Firewall do Windows
1. Ative Firewall do Windows três perfis (domínio, Standard e público):

   ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile' -name "EnableFirewall" -Value 1 -Type DWord
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile' -name "EnableFirewall" -Value 1 -Type DWord
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\Standardprofile' -name "EnableFirewall" -Value 1 -Type DWord
   ```

2. Execute o seguinte comando do PowerShell para permitir WinRM através de três perfis de firewall (domínio, privados e público) e ativar o serviço de remota do PowerShell:
   
   ```PowerShell
    Enable-PSRemoting -force
    netsh advfirewall firewall set rule dir=in name="Windows Remote Management (HTTP-In)" new enable=yes
    netsh advfirewall firewall set rule dir=in name="Windows Remote Management (HTTP-In)" new enable=yes
   ```
3. Ative as seguintes regras de firewall permitir o tráfego RDP 

   ```PowerShell
    netsh advfirewall firewall set rule group="Remote Desktop" new enable=yes
   ```   
4. Ative a regra de impressora partilha de ficheiros e para que a VM pode responder a um comando ping dentro da rede Virtual:

   ```PowerShell
    netsh advfirewall firewall set rule dir=in name="File and Printer Sharing (Echo Request - ICMPv4-In)" new enable=yes
   ``` 
5. Se a VM irá fazer parte de um domínio, verifique as definições seguintes para se certificar de que as definições anteriores não são revertidas. As políticas de AD tem de ser verificadas são os seguintes:

    - Ativar os perfis de Firewall do Windows

        Computador Configuration\Policies\Windows Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Domain Profile\Windows Firewall: **proteger todas as ligações de rede**

       Computador Configuration\Policies\Windows Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Standard Profile\Windows Firewall: **proteger todas as ligações de rede**

    - Ativar RDP 

        Computador Configuration\Policies\Windows Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Domain Profile\Windows Firewall: **permitir entradas exceções de ambiente de trabalho remoto**

        Computador Configuration\Policies\Windows Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Standard Profile\Windows Firewall: **permitir entradas exceções de ambiente de trabalho remoto**

    - Ativar o ICMP V4

        Computador Configuration\Policies\Windows Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Domain Profile\Windows Firewall: **permitir exceções de ICMP**

        Computador Configuration\Policies\Windows Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Standard Profile\Windows Firewall: **permitir exceções de ICMP**

## <a name="verify-vm-is-healthy-secure-and-accessible-with-rdp"></a>Certifique-se de que a VM está em bom estado, segura e acessível com RDP 
1. Para certificar-se de que o disco está em bom estado e consistente, execute uma operação de verificação do disco durante o reinício VM seguinte:

    ```PowerShell
    Chkdsk /f
    ```
    Certifique-se de que o relatório mostra um disco limpo e bom estado de funcionamento.

2. Configurar as definições de dados de configuração de arranque (BCD). 

    > [!Note]
    > Certifique-se de executar estes comandos na janela CMD elevada e **não** no PowerShell:
   
   ```CMD
   bcdedit /set {bootmgr} integrityservices enable
   
   bcdedit /set {default} device partition=C:
   
   bcdedit /set {default} integrityservices enable
   
   bcdedit /set {default} recoveryenabled Off
   
   bcdedit /set {default} osdevice partition=C:
   
   bcdedit /set {default} bootstatuspolicy IgnoreAllFailures
   ```
3. Certifique-se de que o repositório de Instrumentations de gestão do Windows é consistente. Para efetuar isto, execute o seguinte comando:

    ```PowerShell
    winmgmt /verifyrepository
    ```
    Se o repositório está danificado, consulte o artigo [WMI: existência de danos no repositório, ou não](https://blogs.technet.microsoft.com/askperf/2014/08/08/wmi-repository-corruption-or-not).

4. Certifique-se de que qualquer outra aplicação não está a utilizar a porta 3389. Esta porta é utilizada para o serviço RDP no Azure. Pode executar **netstat - anob** para ver as portas são utilizadas na VM:

    ```PowerShell
    netstat -anob
    ```

5. Se o VHD do Windows que pretende carregar um controlador de domínio, em seguida, siga estes passos:

    A. Siga [estes passos Extras](https://support.microsoft.com/kb/2904015) para preparar o disco.

    B. Certifique-se de que sabe a palavra-passe do DSRM no caso de ter que iniciar a VM no DSRM, a determinada altura. Pode querer fazer referência a esta ligação para definir o [palavra-passe DSRM](https://technet.microsoft.com/library/cc754363(v=ws.11).aspx).

6. Certifique-se de que a conta de administrador incorporada e a palavra-passe são conhecidos para si. Poderá pretender para repor a palavra-passe de administrador local atual e certifique-se de que pode utilizar esta conta para iniciar sessão no Windows através da ligação de RDP. Esta permissão de acesso é controlada pelo objeto de política de grupo "Permitir início de sessão através dos serviços de ambiente de trabalho remotos". Pode ver este objeto no Editor de políticas de grupo de Local em:

    Configuração de computador Definições Settings\Local Policies\User Rights Assignment

7. Verifique o seguinte AD políticas para se certificar de que não estão a bloquear o acesso RDP através de RDP nem a partir da rede:

    - Acesso de configuração definições Settings\Local Policies\User Rights Assignment\Deny do computador para este computador a partir da rede

    - Registo de configuração definições Settings\Local Policies\User Rights Assignment\Deny do computador no através dos serviços de ambiente de trabalho remoto


8. Reiniciar a VM para se certificar de que o Windows é bom ainda pode ser contactado através da ligação de RDP. Neste momento, poderá querer criar uma VM no seu local Hyper-V para certificar-se de que a VM está a iniciar completamente e, em seguida, testar se se trata de RDP acessível.

9. Remover quaisquer filtros de Interface de controlador de transporte adicionais, como o software que analisa TCP pacotes ou firewalls adicionais. Também pode rever este numa fase posterior após a implementação da VM no Azure se for necessário.

10. Desinstale qualquer software de terceiros e controladores que está relacionado com componentes físicos ou qualquer outra tecnologia de virtualização.

### <a name="install-windows-updates"></a>Instalar atualizações do Windows
A configuração ideal é **tem o nível de patch de no computador em que a versão mais recente**. Se não for possível, certifique-se de que são instaladas as seguintes atualizações:

|                       |                   |           |                                       Versão de ficheiro mínimo x64       |                                      |                                      |                            |
|-------------------------|-------------------|------------------------------------|---------------------------------------------|--------------------------------------|--------------------------------------|----------------------------|
| Componente               | Binário            | Windows 7 e Windows Server 2008 R2 | Windows 8 e Windows Server 2012             | Windows 8.1 e Windows Server 2012 R2 | Windows 10 e Windows Server 2016 RS1 | Windows 10 RS2             |
| Armazenamento                 | Disk.sys          | 6.1.7601.23403 - KB3125574         | 6.2.9200.17638 / 6.2.9200.21757 - KB3137061 | 6.3.9600.18203 - KB3137061           | -                                    | -                          |
|                         | Storport.sys      | 6.1.7601.23403 - KB3125574         | 6.2.9200.17188 / 6.2.9200.21306 - KB3018489 | 6.3.9600.18573 - KB4022726           | 10.0.14393.1358 - KB4022715          | 10.0.15063.332             |
|                         | NTFS.sys          | 6.1.7601.23403 - KB3125574         | 6.2.9200.17623 / 6.2.9200.21743 - KB3121255 | 6.3.9600.18654 - KB4022726           | 10.0.14393.1198 - KB4022715          | 10.0.15063.447             |
|                         | Iologmsg.dll      | 6.1.7601.23403 - KB3125574         | 6.2.9200.16384 - KB2995387                  | -                                    | -                                    | -                          |
|                         | Classpnp.sys      | 6.1.7601.23403 - KB3125574         | 6.2.9200.17061 / 6.2.9200.21180 - KB2995387 | 6.3.9600.18334 - KB3172614           | 10.0.14393.953 - KB4022715           | -                          |
|                         | Volsnap.sys       | 6.1.7601.23403 - KB3125574         | 6.2.9200.17047 / 6.2.9200.21165 - KB2975331 | 6.3.9600.18265 - KB3145384           | -                                    | 10.0.15063.0               |
|                         | partmgr.sys       | 6.1.7601.23403 - KB3125574         | 6.2.9200.16681 - KB2877114                  | 6.3.9600.17401 - KB3000850           | 10.0.14393.953 - KB4022715           | 10.0.15063.0               |
|                         | volmgr.sys        |                                    |                                             |                                      |                                      | 10.0.15063.0               |
|                         | Volmgrx.sys       | 6.1.7601.23403 - KB3125574         | -                                           | -                                    | -                                    | 10.0.15063.0               |
|                         | Msiscsi.sys       | 6.1.7601.23403 - KB3125574         | 6.2.9200.21006 - KB2955163                  | 6.3.9600.18624 - KB4022726           | 10.0.14393.1066 - KB4022715          | 10.0.15063.447             |
|                         | MSDSM.sys         | 6.1.7601.23403 - KB3125574         | 6.2.9200.21474 - KB3046101                  | 6.3.9600.18592 - KB4022726           | -                                    | -                          |
|                         | MPIO.sys          | 6.1.7601.23403 - KB3125574         | 6.2.9200.21190 - KB3046101                  | 6.3.9600.18616 - KB4022726           | 10.0.14393.1198 - KB4022715          | -                          |
|                         | Fveapi.dll        | 6.1.7601.23311 - KB3125574         | 6.2.9200.20930 - KB2930244                  | 6.3.9600.18294 - KB3172614           | 10.0.14393.576 - KB4022715           | -                          |
|                         | Fveapibase.dll    | 6.1.7601.23403 - KB3125574         | 6.2.9200.20930 - KB2930244                  | 6.3.9600.17415 - KB3172614           | 10.0.14393.206 - KB4022715           | -                          |
| Rede                 | netvsc.sys        | -                                  | -                                           | -                                    | 10.0.14393.1198 - KB4022715          | 10.0.15063.250 - KB4020001 |
|                         | mrxsmb10.sys      | 6.1.7601.23816 - KB4022722         | 6.2.9200.22108 - KB4022724                  | 6.3.9600.18603 - KB4022726           | 10.0.14393.479 - KB4022715           | 10.0.15063.483             |
|                         | mrxsmb20.sys      | 6.1.7601.23816 - KB4022722         | 6.2.9200.21548 - KB4022724                  | 6.3.9600.18586 - KB4022726           | 10.0.14393.953 - KB4022715           | 10.0.15063.483             |
|                         | Mrxsmb.sys        | 6.1.7601.23816 - KB4022722         | 6.2.9200.22074 - KB4022724                  | 6.3.9600.18586 - KB4022726           | 10.0.14393.953 - KB4022715           | 10.0.15063.0               |
|                         | Tcpip.sys         | 6.1.7601.23761 - KB4022722         | 6.2.9200.22070 - KB4022724                  | 6.3.9600.18478 - KB4022726           | 10.0.14393.1358 - KB4022715          | 10.0.15063.447             |
|                         | http.sys          | 6.1.7601.23403 - KB3125574         | 6.2.9200.17285 - KB3042553                  | 6.3.9600.18574 - KB4022726           | 10.0.14393.251 - KB4022715           | 10.0.15063.483             |
|                         | vmswitch.sys      | 6.1.7601.23727 - KB4022719         | 6.2.9200.22117 - KB4022724                  | 6.3.9600.18654 - KB4022726           | 10.0.14393.1358 - KB4022715          | 10.0.15063.138             |
| Principal                    | Ntoskrnl.exe      | 6.1.7601.23807 - KB4022719         | 6.2.9200.22170 - KB4022718                  | 6.3.9600.18696 - KB4022726           | 10.0.14393.1358 - KB4022715          | 10.0.15063.483             |
| Serviços de Ambiente de Trabalho Remoto | rdpcorets.dll     | 6.2.9200.21506 - KB4022719         | 6.2.9200.22104 - KB4022724                  | 6.3.9600.18619 - KB4022726           | 10.0.14393.1198 - KB4022715          | 10.0.15063.0               |
|                         | TERMSRV.dll       | 6.1.7601.23403 - KB3125574         | 6.2.9200.17048 - KB2973501                  | 6.3.9600.17415 - KB3000850           | 10.0.14393.0 - KB4022715             | 10.0.15063.0               |
|                         | termdd.sys        | 6.1.7601.23403 - KB3125574         | -                                           | -                                    | -                                    | -                          |
|                         | Win32k.sys        | 6.1.7601.23807 - KB4022719         | 6.2.9200.22168 - KB4022718                  | 6.3.9600.18698 - KB4022726           | 10.0.14393.594 - KB4022715           | -                          |
|                         | rdpdd.dll         | 6.1.7601.23403 - KB3125574         | -                                           | -                                    | -                                    | -                          |
|                         | Rdpwd.sys         | 6.1.7601.23403 - KB3125574         | -                                           | -                                    | -                                    | -                          |
| Segurança                | Devido a WannaCrypt | KB4012212                          | KB4012213                                   | KB4012213                            | KB4012606                            | KB4012606                  |
|                         |                   |                                    | KB4012216                                   |                                      | KB4013198                            | KB4013198                  |
|                         |                   | KB4012215                          | KB4012214                                   | KB4012216                            | KB4013429                            | KB4013429                  |
|                         |                   |                                    | KB4012217                                   |                                      | KB4013429                            | KB4013429                  |
       
### Quando utilizar o sysprep<a id="step23"></a>    

Sysprep é um processo que é possível executar para uma instalação do windows que irá repor a instalação do sistema e irá fornecer um "a Box experiência", removendo todos os dados pessoais e reponha vários componentes. Fazê-lo, normalmente, se pretender criar um modelo a partir da qual pode implementar várias outras VMs que tiverem uma configuração específica. Esta opção é denominada uma **generalizado imagem**.

Se, em vez disso, pretende-se apenas ao criar uma VM a partir de um disco, não têm de utilizar o sysprep. Nesta situação, pode pois criar simplesmente a VM de que é conhecida como um **imagem especializada**.

Para obter mais informações sobre como criar uma VM a partir de um disco especializado, consulte:

- [Criar uma VM a partir de um disco especializado](create-vm-specialized.md)
- [Criar uma VM a partir de um disco especializado do VHD](https://azure.microsoft.com/resources/templates/201-vm-specialized-vhd/)

Se pretender criar uma imagem de generalizado, terá de executar o sysprep. Para obter mais informações sobre o Sysprep, consulte [como Sysprep de utilização: uma introdução](http://technet.microsoft.com/library/bb457073.aspx). 

Nem todas as funções ou aplicação que é instalada num computador baseado em Windows suporta este generalização. Por isso, antes de executar este procedimento, consulte o artigo seguinte para se certificar de que a função do computador é suportada pelo sysprep. Para obter mais informações, [suporte de Sysprep para funções de servidor](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

### <a name="steps-to-generalize-a-vhd"></a>Passos para generalizar um VHD

>[!NOTE]
> Depois de executar o sysprep.exe conforme especificado nos passos seguintes, desative a VM e não ativá-la novamente até que crie uma imagem do mesmo no Azure.

1. Iniciar sessão para a VM do Windows.
2. Executar **linha de comandos** como administrador. 
3. Altere o diretório para: **%windir%\system32\sysprep**e, em seguida, execute **sysprep.exe**.
3. No **ferramenta de preparação de sistema** caixa de diálogo, selecione **introduza sistema Out-of-Box experiência (OOBE)**e certifique-se de que o **Generalize** caixa de verificação está selecionada.

    ![Ferramenta de preparação do sistema](media/prepare-for-upload-vhd-image/syspre.png)
4. No **as opções de encerramento**, selecione **encerramento**.
5. Clique em **OK**.
6. Quando o Sysprep for concluído, encerre a VM. Não utilize **reiniciar** para encerre a VM.
7. O VHD está agora pronto para ser carregado. Para obter mais informações sobre como criar uma VM a partir de um disco generalizado, consulte [carregar um VHD generalizado e utilizá-la para criar um novo VMs no Azure](sa-upload-generalized.md).


## <a name="complete-recommended-configurations"></a>Conclua as configurações recomendadas
As seguintes definições não afetam a carregar o VHD. No entanto, recomendamos vivamente que configurou-los.

* Instalar o [as VMs do Azure agente](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Em seguida, pode ativar as extensões de VM. As extensões VM de implementar a maioria da funcionalidade crítica que possam pretende utilizar com as suas VMs tal como repor palavras-passe, configurar o RDP e assim sucessivamente. Para obter mais informações, consulte:

    - [Agente da VM e extensões – parte 1](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-1/)
    - [Extensões – parte 2 e o agente da VM](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/)
* O registo de captura pode ser úteis na resolução de problemas de falha do Windows. Ative a recolha de registos de informação:
  
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name "CrashDumpEnable" -Value "2" -Type DWord
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name "DumpFile" -Value "%SystemRoot%\MEMORY.DMP"
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name "AutoReboot" -Value 0 -Type DWord
    New-Item -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps'
    New-ItemProperty -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps' -name "DumpFolder" -Value "c:\CrashDumps"
    New-ItemProperty -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps' -name "DumpCount" -Value 10 -Type DWord
    New-ItemProperty -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps' -name "DumpType" -Value 2 -Type DWord
    Set-Service -Name WerSvc -StartupType Manual
    ```
    Se receber erros durante a qualquer um dos procedimentos passos neste artigo, isto significa que as chaves de registo já existe. Nesta situação, utilize os seguintes comandos em vez disso:

    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name "CrashDumpEnable" -Value "2" -Type DWord
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name "DumpFile" -Value "%SystemRoot%\MEMORY.DMP"
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps' -name "DumpFolder" -Value "c:\CrashDumps"
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps' -name "DumpCount" -Value 10 -Type DWord
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps' -name "DumpType" -Value 2 -Type DWord
    Set-Service -Name WerSvc -StartupType Manual
    ```
*  Depois de criada a VM no Azure, recomendamos que coloque o ficheiro de paginação no volume "Unidade Temporal" para melhorar o desempenho. Pode configurar esta da seguinte forma:

    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management' -name "PagingFiles" -Value "D:\pagefile"
    ```
Se não houver qualquer disco de dados que está ligado à VM, a letra de unidade do volume unidade Temporal é, geralmente, "D." Este designação pode ser diferente, consoante o número de unidades disponíveis e as definições que efetuar.

## <a name="next-steps"></a>Passos seguintes
* [Carregar uma imagem de VM do Windows Azure para implementações do Resource Manager](upload-generalized-managed.md)
* [Resolução de problemas de ativação de máquina virtual do Azure Windows](troubleshoot-activation-problems.md)

