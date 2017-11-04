### <a name="install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Instalar atualizações de modo de manutenção através do Windows PowerShell para StorSimple

Ao aplicar as atualizações de modo de manutenção para o dispositivo StorSimple, todos os pedidos de e/s são colocadas em pausa. Os serviços, tais como a memória de acesso aleatório não volátil (NVRAM) ou o serviço clustering estão parados. Tanto os controladores de reiniciar o computador quando introduzir ou sair neste modo. Quando sair deste modo, todos os serviços retomar e estão em bom Estados. (Isto pode demorar alguns minutos.)

> [!IMPORTANT]
> * Antes de introduzir o modo de manutenção, certifique-se de que ambos os controladores de dispositivo estão em bom Estados no portal do Azure. Se o controlador não está em bom estado, [contactar o suporte da Microsoft](../articles/storsimple/storsimple-8000-contact-microsoft-support.md) para conhecer os passos seguintes.
> * Quando estiver no modo de manutenção, terá de atualizar primeiro um controlador e, em seguida, o controlador de outro.

1. Utilize o PuTTY para ligar a consola de série. Siga as instruções detalhadas em [Utilizar o PuTTY para ligar à consola de série](../articles/storsimple/storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console). Na linha de comandos, prima **Enter**. Selecione a opção 1 para iniciar sessão no dispositivo com acesso total.

2. Para colocar o controlador em modo de manutenção, escreva:
    
    `Enter-HcsMaintenanceMode`

    Ambos os controladores reiniciar no modo de manutenção.

3. Instale as atualizações de modo de manutenção. Escreva:

    `Start-HcsUpdate`

    Lhe for pedida a confirmação. Depois de confirmar as atualizações, são instaladas num controlador que atualmente estão a aceder. Depois das atualizações são instaladas, reinicia o controlador.

4. Monitorize o estado das atualizações. Como o controlador atual está a atualizar e não é possível processar a quaisquer outros comandos, inicie sessão no controlador de ponto a ponto. Escreva:

    `Get-HcsUpdateStatus`

    Se o `RunInProgress` é `True`, a atualização ainda está em curso. Se `RunInProgress` é `False`, ele indica que a atualização foi concluída.

5. Depois das atualizações de firmware do disco são aplicadas com êxito e reiniciou o controlador atualizado, verifique a versão de firmware do disco. No controlador de atualizados, escreva:

    `Get-HcsFirmwareVersion`
   
    As versões de firmware esperado disco são:`XMGJ, XGEG, KZ50, F6C2, VR08, N003, 0107`

6. Sair do modo de manutenção. Escreva o seguinte comando para cada controlador de dispositivo:

    `Exit-HcsMaintenanceMode`

    Os controladores reiniciam quando sair do modo de manutenção.

7. Regresse ao portal do Azure. O portal não pode mostrar se instalou as atualizações de modo de manutenção para 24 horas.