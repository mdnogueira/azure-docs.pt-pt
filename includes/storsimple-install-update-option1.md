<!--author=SharS last changed: 03/17/2016-->

#### <a name="to-download-hotfixes"></a>Para transferir correções
Execute os seguintes passos para transferir a atualização de software.

1. Inicie o Internet Explorer e navegue para [http://catalog.update.microsoft.com](http://catalog.update.microsoft.com).
2. Se esta for a primeira vez que utiliza o Catálogo Microsoft Update neste computador, clique em **Instalar** quando lhe for pedido para instalar o suplemento do Catálogo Microsoft Update.
    ![Instalar o catálogo](./media/storsimple-install-update-option-1/HCS_InstallCatalog-include.png)
3. Na caixa de pesquisa do catálogo Microsoft Update, introduza o número de Base de dados de conhecimento (KB) de correção que pretende transferir, por exemplo **3063418**e, em seguida, clique em **pesquisa**.
4. Verá o **StorSimple atualização 1.2 aplicação atualização** pacote. Clique em **Adicionar**. A atualização será adicionada ao cesto.
5. Procure as correções adicionais listados na tabela acima (**3043005** e **3063416**) e adicione cada o cesto.
6. Clique em **Ver Cesto**.
   
    ![Ver cesto](./media/storsimple-install-update-option-1/HCS_InstallBasket-include.png)
7. Clique em **Transferir**. Especifique ou **Pesquise** uma localização local onde pretende que as transferências apareçam. As atualizações são transferidas para a localização especificada e colocadas numa sub-pasta com o mesmo nome que a atualização. A pasta também pode ser copiada para uma partilha de rede que é acessível a partir do dispositivo.

> [!NOTE]
> As correções devem ser acessíveis a ambos os controladores para detetar as mensagens de erro potencial do controlador de ponto a ponto.
> 
> 

#### <a name="to-install-and-verify-regular-mode-hotfixes"></a>Para instalar e verificar correções do modo normal
Execute os seguintes passos para instalar e verificar as correções de modo normal. Se já instalou-los utilizando o Portal do Azure, avançar diretamente para [instalar e certifique-se correções do modo de manutenção](#to-install-and-verify-maintenance-mode-hotfixes).

1. Para instalar a atualização de software, aceder à interface do Windows PowerShell na consola de série do dispositivo StorSimple. Siga as instruções detalhadas em [Utilizar o PuTTY para ligar à consola de série](../articles/storsimple/storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console). Na linha de comandos, prima **Enter**.
2. Selecione a **Opção 1** para iniciar sessão no dispositivo com acesso total.
3. Para instalar o pacote de atualização, na linha de comandos, escreva:
   
    `Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`
   
    Utilize o IP em vez de DNS no caminho de partilha no comando acima. O parâmetro da credencial é utilizado apenas se aceder a uma partilha autenticada.
   
    Recomendamos que utilize o parâmetro da credencial para aceder a partilhas. Mesmo as partilhas abertas para "todos" não são normalmente abertas para utilizadores não autenticados.
   
    É apresentada abaixo uma saída de exemplo.
   
    ```
    Controller0>Start-HcsHotfix -Path \\10.100.100.100\share
    \hcsmdssoftwareupdate.exe -Credential contoso\John

    Confirm

    This operation starts the hotfix installation and could reboot one or
    both of the controllers. If the device is serving I/Os, these will not
    be disrupted. Are you sure you want to continue?
    [Y] Yes [N] No [?] Help (default is "Y"): Y
    ```

4. Escreva **Y** quando lhe for pedido para confirmar a instalação da correção.
5. Monitorize a atualização com o cmdlet `Get-HcsUpdateStatus`.
   
    A seguinte saída de exemplo mostra a atualização em curso. O `RunInprogress` será `True` quando a atualização está em curso.
   
    ```
    Controller0>Get-HcsUpdateStatus
    RunInprogress       : True
    LastHotfixTimestamp : 9/02/2015 10:36:13 PM
    LastUpdateTimestamp : 9/02/2015 10:35:25 PM
    Controller0Events   :
    Controller1Events   :
    ```
   
     A saída de exemplo seguinte indica que a atualização foi concluída. O `RunInProgress` será `False` quando a atualização for concluída.

    ```
    Controller1>Get-HcsUpdateStatus

    RunInprogress       : False
    LastHotfixTimestamp : 9/02/2015 10:56:13 PM
    LastUpdateTimestamp : 9/02/2015 10:35:25 PM
    Controller0Events   :
    Controller1Events   :
    ```
   
   > [!NOTE]
   > Ocasionalmente, o cmdlet comunica `False` quando a atualização ainda está em curso. Para se certificar de que a correção foi concluída, aguarde alguns minutos, execute novamente este comando e certifique-se de que o `RunInProgress` é `False`. Se for, então a correção foi concluída.
   > 
   > 
6. Assim que a atualização do software for concluída, verifique as versões do software do sistema. Escreva o seguinte comando:
   
    `Get-HcsSystem`
   
    Deverá ver as seguintes versões:
   
   * HcsSoftwareVersion: 6.3.9600.17584
   * CisAgentVersion: 1.0.9049.0
   * MdsAgentVersion: 26.0.4696.1433
     
     Se os números de versão não alterar depois de aplicar a atualização, ele indica que a correção não conseguiu aplicar. Se tal acontecer, entre em contacto com o [Suporte da Microsoft](../articles/storsimple/storsimple-contact-microsoft-support.md) para obter assistência.
7. Repita os passos 3 a 5 para instalar a correção de modo de regular restantes (KB3043005).

#### <a name="to-install-and-verify-maintenance-mode-hotfixes"></a>Para instalar e verificar correções do modo de manutenção
Utilize KB3063416 para instalar atualizações de firmware do disco. Estas atualizações acontece e demorar cerca de 30 45 minutos a concluir. Pode optar por instalá-las numa janela de manutenção planeada ao estabelecer uma ligação à consola de série do dispositivo.

Para instalar as atualizações de firmware do disco, siga as instruções abaixo.

1. Colocar o dispositivo em modo de manutenção. Tenha em atenção que não deve utilizar a comunicação remota do Windows PowerShell ao ligar a um dispositivo em modo de manutenção. Terá de executar este cmdlet no controlador de dispositivo quando estiver ligado através da consola de série do dispositivo. Escreva:
   
    `Enter-HcsMaintenanceMode`
   
    É apresentada abaixo uma saída de exemplo.
   
        Controller0>Enter-HcsMaintenanceMode
        Checking device state...
   
        In maintenance mode, your device will not service IOs and will be disconnected from the Microsoft Azure StorSimple Manager service. Entering maintenance mode will end the current session and reboot both controllers, which takes a few minutes to complete. Are you sure you want to enter maintenance mode?
        [Y] Yes [N] No (Default is "Y"): Y
   
        -----------------------MAINTENANCE MODE------------------------
        Microsoft Azure StorSimple Appliance Model 8100
        Name: Update1-8100-SHG0997879L76YD
        Software Version: 6.3.9600.17584
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
        This operation starts a hotfix installation and could reboot one or both of the controllers. Are you sure you want to continue?
        [Y] Yes [N] No (Default is "Y"): Y
        WARNING: Installation is currently in progress. This operation can take several minutes to complete.
3. Monitorize o progresso da instalação com o comando `Get-HcsUpdateStatus`. A atualização está completa quando o `RunInProgress` é alterado para `False`.
4. Após a instalação estiver concluída, o controlador em que foi instalada a correção de modo de manutenção será reiniciado. Inicie sessão com a opção 1 com acesso total e verifique a versão de firmware do disco. Escreva:
   
   `Get-HcsFirmwareVersion`
   
   As versões de firmware do disco esperadas são:
   
   `XMGG, XGEE, KZ50, F6C2, VR08`
   
   Execute o comando `Get-HcsFirmwareVersion` no segundo controlador para se certificar que a versão do software foi atualizada. Em seguida, pode sair do modo de manutenção. Escreva o seguinte comando para cada controlador de dispositivo:
   
   `Exit-HcsMaintenanceMode`
5. Os controladores reinicie quando sair do modo de manutenção. Assim que as atualizações do firmware do disco forem aplicadas com êxito e o dispositivo tiver saído do modo de manutenção, regresse ao portal clássico do Azure. Tenha em atenção que o portal poderá não mostrar que instalou as atualizações de modo de manutenção para 24 horas.

