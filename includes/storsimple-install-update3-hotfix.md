<!--author=alkohli last changed: 09/01/16-->

#### <a name="to-download-hotfixes"></a>Para transferir correções
Execute os seguintes passos para transferir a atualização de software a partir do Catálogo Microsoft Update.

1. Inicie o Internet Explorer e navegue para [http://catalog.update.microsoft.com](http://catalog.update.microsoft.com).
2. Se esta for a primeira vez que utiliza o Catálogo Microsoft Update neste computador, clique em **Instalar** quando lhe for pedido para instalar o suplemento do Catálogo Microsoft Update.
    ![Instalar o catálogo](./media/storsimple-install-update2-hotfix/HCS_InstallCatalog-include.png)
3. Na caixa de pesquisa do catálogo Microsoft Update, introduza o número de Base de dados de conhecimento (KB) de correção que pretende transferir, por exemplo **3186843**e, em seguida, clique em **pesquisa**.
   
    A listagem de correção é apresentado, por exemplo, **cumulativa 3.0 de atualização de pacote de Software para a série de 8000 do StorSimple**.
   
    ![Catálogo de pesquisa](./media/storsimple-install-update2-hotfix/HCS_SearchCatalog1-include.png)
4. Clique em **Adicionar**. A atualização é adicionada ao cesto.
5. Procure as correções adicionais listados na tabela acima (**3186859**) e adicione cada o cesto.
6. Clique em **Ver Cesto**.
7. Clique em **Transferir**. Especifique ou **Pesquise** uma localização local onde pretende que as transferências apareçam. As atualizações são transferidas para a localização especificada e colocadas numa pasta secundárias com o mesmo nome que a atualização. A pasta também pode ser copiada para uma partilha de rede que é acessível a partir do dispositivo.

> [!NOTE]
> As correções devem ser acessíveis a ambos os controladores para detetar as mensagens de erro potencial do controlador de ponto a ponto.
> 
> 

#### <a name="to-install-and-verify-regular-mode-hotfixes"></a>Para instalar e verificar correções do modo normal
Execute os seguintes passos para instalar e verificar correções do modo normal. Se já instalou-los utilizando o Portal do Azure, avançar diretamente para [instalar e certifique-se correções do modo de manutenção](#to-install-and-verify-maintenance-mode-hotfixes).

1. Para instalar as correções, aceda à interface do Windows PowerShell na consola de série do dispositivo StorSimple. Siga as instruções detalhadas em [Utilizar o PuTTY para ligar à consola de série](../articles/storsimple/storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console). Na linha de comandos, prima **Enter**.
2. Selecione a **Opção 1** para iniciar sessão no dispositivo com acesso total. Recomendamos que primeiro instale a correção no controlador passivo.
3. Para instalar a correção, na linha de comandos, escreva:
   
    `Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`
   
    Utilize o IP em vez de DNS no caminho de partilha no comando acima. O parâmetro da credencial é utilizado apenas se aceder a uma partilha autenticada.
   
    Recomendamos que utilize o parâmetro da credencial para aceder a partilhas. Mesmo as partilhas abertas para "todos" não são normalmente abertas para utilizadores não autenticados.
   
    Forneça a palavra-passe quando lhe for pedida.
   
    É apresentada abaixo uma saída de exemplo.
   
        ````
        Controller0>Start-HcsHotfix -Path \\10.100.100.100\share
        \hcsmdssoftwareupdate.exe -Credential contoso\John
   
        Confirm
   
        This operation starts the hotfix installation and could reboot one or
        both of the controllers. If the device is serving I/Os, these will not
        be disrupted. Are you sure you want to continue?
        [Y] Yes [N] No [?] Help (default is "Y"): Y
   
        ````
4. Escreva **Y** quando lhe for pedido para confirmar a instalação da correção.
5. Monitorize a atualização com o cmdlet `Get-HcsUpdateStatus`. A atualização será concluída primeiro no controlador passivo. Assim que o controlador passivo for atualizado, existirá uma ativação pós-falha e a atualização será então aplicada no outro controlador. A atualização é concluída quando ambos os controladores são atualizados.
   
    A seguinte saída de exemplo mostra a atualização em curso. O `RunInprogress` será `True` quando a atualização está em curso.

    ```
    Controller0>Get-HcsUpdateStatus
    RunInprogress       : True
    LastHotfixTimestamp :
    LastUpdateTimestamp : 8/29/2016 2:04:02 AM
    Controller0Events   :
    Controller1Events   :
    ```
   
     A saída de exemplo seguinte indica que a atualização foi concluída. O `RunInProgress` será `False` quando a atualização for concluída.
   
    ```
    Controller0>Get-HcsUpdateStatus
    RunInprogress       : False
    LastHotfixTimestamp : 8/30/2016 9:15:55 AM
    LastUpdateTimestamp : 8/30/2016 9:06:07 AM
    Controller0Events   :
    Controller1Events   :
    ```

    > [!NOTE] 
    > Ocasionalmente, o cmdlet comunica `False` quando a atualização ainda está em curso. Para se certificar de que a correção foi concluída, aguarde alguns minutos, execute novamente este comando e certifique-se de que o `RunInProgress` é `False`. Se for, então a correção foi concluída.

1. Assim que a atualização do software for concluída, verifique as versões do software do sistema. Escreva:
   
    `Get-HcsSystem`
   
    Deverá ver as seguintes versões:
   
   * `HcsSoftwareVersion: 6.3.9600.17759`
   * `CisAgentVersion:  1.0.9343.0`
   * `MdsAgentVersion: 30.0.4698.16`
     
     Se os números de versão não alterar depois de aplicar a atualização, ele indica que a correção não conseguiu aplicar. Se tal acontecer, entre em contacto com o [Suporte da Microsoft](../articles/storsimple/storsimple-contact-microsoft-support.md) para obter assistência.
     
     > [!IMPORTANT]
     > Tem de reiniciar o controlador ativo através do cmdlet `Restart-HcsController` antes de aplicar as atualizações restantes. 
     > 
     > 
2. Repita os passos 3 a 5 para instalar a correção de controladores e firmware LSI **KB3186859**. Após a correção está instalada, utilize o `Get-HcsSystem` cmdlet. A versão de LSI deve ser:
   
   * `Lsisas2Version: 2.0.78.00`
3. Repita os passos 3 a 5 para instalar a atualização Storport e Spaceport **KB3121261**.
4. Se estão a atualizar a partir da atualização 2 ou versão anterior, também terá de transferir:
   
   * correção de iSCSI utilizando KB3146621
   * Correção WMI utilizando KB3103616

#### <a name="to-install-and-verify-maintenance-mode-hotfixes"></a>Para instalar e verificar correções do modo de manutenção
Utilize KB3121899 para instalar atualizações de firmware do disco. Estas são atualizações disruptivas e demoram cerca de 30 minutos a serem concluídas. Pode optar por instalá-las numa janela de manutenção planeada ao estabelecer uma ligação à consola de série do dispositivo.

Tenha em atenção que se o firmware do disco já estiver atualizado, não terá de instalar estas atualizações. Execute o cmdlet `Get-HcsUpdateAvailability` a partir da consola de série do dispositivo para verificar se as atualizações estão disponíveis e se as atualizações são disruptivas (modo de manutenção) ou não disruptivas (modo normal).

Para instalar as atualizações de firmware do disco, siga as instruções abaixo.

1. Colocar o dispositivo no modo de manutenção. Tenha em atenção que não deve utilizar a comunicação remota do Windows PowerShell ao ligar a um dispositivo em modo de manutenção. Em vez disso, execute este cmdlet no controlador de dispositivo quando estiver ligado através da consola de série do dispositivo. Escreva:
   
    `Enter-HcsMaintenanceMode`
   
    É apresentada abaixo uma saída de exemplo.
   
        Controller0>Enter-HcsMaintenanceMode
        Checking device state...
   
        In maintenance mode, your device will not service IOs and will be disconnected from the Microsoft Azure StorSimple Manager service. Entering maintenance mode will end the current session and reboot both controllers, which takes a few minutes to complete. Are you sure you want to enter maintenance mode?
        [Y] Yes [N] No (Default is "Y"): Y
   
        -----------------------MAINTENANCE MODE------------------------
        Microsoft Azure StorSimple Appliance Model 8100
        Name: Update2-8100-SHG0997879L76673
        Copyright (C) 2014 Microsoft Corporation. All rights reserved.
        You are connected to Controller0 - Passive
        ---------------------------------------------------------------
   
        Serial Console Menu
        [1] Log in with full access
        [2] Log into peer controller with full access
        [3] Connect with limited access
        [4] Change language
        Please enter your choice>
   
    Em seguida, reinicie ambos os controladores no modo de manutenção.
2. Para instalar a atualização de firmware do disco, escreva:
   
    `Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`
   
    É apresentada abaixo uma saída de exemplo.
   
        Controller1>Start-HcsHotfix -Path \\10.100.100.100\share\DiskFirmwarePackage.exe -Credential contoso\john
        Enter Password:
        WARNING: In maintenance mode, hotfixes should be installed on each controller sequentially. After the hotfix is installed on this controller, install it on the peer controller.
        Confirm
        This operation starts a hotfix installation and could reboot one or both of the controllers. By installing new updates you agree to, and accept any additional terms associated with, the new functionality listed in the release notes (https://go.microsoft.com/fwLink/?LinkID=613790). Are you sure you want to continue?
        [Y] Yes [N] No (Default is "Y"): Y
        WARNING: Installation is currently in progress. This operation can take several minutes to complete.
3. Monitorize o progresso da instalação com o comando `Get-HcsUpdateStatus`. A atualização está completa quando o `RunInProgress` é alterado para `False`.
4. Após a instalação estar concluída, o controlador em que a correção do modo de manutenção foi instalada reinicia. Inicie sessão com a opção 1 com acesso total e verifique a versão de firmware do disco. Escreva:
   
   `Get-HcsFirmwareVersion`
   
   As versões de firmware do disco esperadas são:
   
   `XMGG, XGEG, KZ50, F6C2, VR08`
   
   É apresentada abaixo uma saída de exemplo.
   
       -----------------------MAINTENANCE MODE------------------------
       Microsoft Azure StorSimple Appliance Model 8100
       Name: Update2-8100-SHG0997879L76YD
       Software Version: 6.3.9600.17705
       Copyright (C) 2014 Microsoft Corporation. All rights reserved.
       You are connected to Controller1
       ---------------------------------------------------------------
   
       Controller1>Get-HcsFirmwareVersion
   
       Controller0 : TalladegaFirmware
         ActiveBIOS:0.45.0006
         BackupBIOS:0.45.0008
         MainCPLD:17.0.0005
         ActiveBMCRoot:2.0.000E
         BackupBMCRoot:2.0.000E
         BMCBoot:2.0.0001
         LsiFirmware:19.00.00.00
         LsiBios:07.37.00.00
         Battery1Firmware:06.29
         Battery2Firmware:06.29
         DomFirmware:X231600
         CanisterFirmware:3.5.0.32
         CanisterBootloader:5.03
         CanisterConfigCRC:0xD1B030A4
         CanisterVPDStructure:0x06
         CanisterGEMCPLD:0x17
         CanisterVPDCRC:0xEE3504B4
         MidplaneVPDStructure:0x0C
         MidplaneVPDCRC:0xA6BD4F64
         MidplaneCPLD:0x10
         PCM1Firmware:1.00|1.05
         PCM1VPDStructure:0x05
         PCM1VPDCRC:0x41BEF99C
         PCM2Firmware:1.00|1.05
         PCM2VPDStructure:0x05
         PCM2VPDCRC:0x41BEF99C
   
         DisksFirmware
         SEAGATE:ST400FM0073:XGEG
         SEAGATE:ST400FM0073:XGEG
         SEAGATE:ST400FM0073:XGEG
         SEAGATE:ST400FM0073:XGEG
         SEAGATE:ST4000NM0023:XMGG
         SEAGATE:ST4000NM0023:XMGG
         SEAGATE:ST4000NM0023:XMGG
         SEAGATE:ST4000NM0023:XMGG
         SEAGATE:ST4000NM0023:XMGG
         SEAGATE:ST4000NM0023:XMGG
         SEAGATE:ST4000NM0023:XMGG
         SEAGATE:ST4000NM0023:XMGG
   
    Execute o comando `Get-HcsFirmwareVersion` no segundo controlador para se certificar que a versão do software foi atualizada. Em seguida, pode sair do modo de manutenção. Para tal, escreva o seguinte comando em cada controlador de dispositivo:
   
   `Exit-HcsMaintenanceMode`
5. Os controladores reinicie quando sair do modo de manutenção. Assim que as atualizações do firmware do disco forem aplicadas com êxito e o dispositivo tiver saído do modo de manutenção, regresse ao portal clássico do Azure. Tenha em atenção que o portal poderá não mostrar que instalou as atualizações de modo de manutenção para 24 horas.

