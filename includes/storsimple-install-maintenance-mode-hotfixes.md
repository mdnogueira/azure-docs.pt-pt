<!--author=SharS last changed: 9/17/15-->

#### <a name="to-install-maintenance-mode-hotfixes-via-windows-powershell-for-storsimple"></a>Instalar correções de modo de manutenção através do Windows PowerShell para StorSimple
> [!IMPORTANT]
> No modo de manutenção, tem de aplicar primeiro a correção num controlador e, em seguida, no outro controlador de.
> 
> 

1. Colocar o dispositivo no modo de manutenção. Consulte [passo 2: modo de manutenção introduza](../articles/storsimple/storsimple-update-device.md#step2) para obter instruções sobre como introduzir o modo de manutenção.
2. Para aplicar a correção, escreva:
   
     `Start-HcsHotfix` 
3. Quando lhe for pedido, forneça o caminho para a pasta de rede partilhada que contém os ficheiros de correção.
4. Será solicitado para confirmação. Tipo **Y** para prosseguir com a instalação de correção.
5. Depois de ter aplicado a correção num controlador, inicie sessão para o controlador de outro. Aplique a correção, tal como fez para o controlador de anterior.
6. Depois das correções são aplicadas, saia do modo de manutenção. Consulte [passo 4: modo de manutenção de saída](../articles/storsimple/storsimple-update-device.md#step4) para obter instruções.

