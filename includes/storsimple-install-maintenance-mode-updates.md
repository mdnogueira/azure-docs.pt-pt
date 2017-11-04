<!--author=SharS last changed: 9/17/15-->

#### <a name="to-install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Para instalar atualizações de modo de manutenção através do Windows PowerShell para StorSimple
1. Se não tiver o feito, aceda à consola de série do dispositivo e selecione opção 1, **iniciar sessão com acesso total**. 
2. Escreva a palavra-passe. A palavra-passe predefinida é **Password1**.
3. Na linha de comandos, escreva:
   
     `Get-HcsUpdateAvailability` 
4. Será notificado se as atualizações estiverem disponíveis e se as atualizações são acontece ou não acontece. Para aplicar atualizações acontece, tem de colocar o dispositivo em modo de manutenção. Consulte [passo 2: modo de manutenção introduza](../articles/storsimple/storsimple-update-device.md#step2) para obter instruções.
5. Quando o dispositivo está no modo de manutenção, a linha de comandos, escreva:`Start-HcsUpdate`
6. Será solicitado para confirmação. Depois de confirmar as atualizações, serão instalados no controlador de que atualmente estão a aceder. Depois das atualizações são instaladas, o controlador será reiniciado. 
7. Monitorize o estado das atualizações. Escreva:
   
    `Get-HcsUpdateStatus`
   
    Se o `RunInProgress` é `True`, a atualização ainda está em curso. Se `RunInProgress` é `False`, ele indica que a atualização foi concluída.  
8. Quando a atualização é instalada no controlador de atual e foi reiniciado, ligue para o controlador de outro e efetuar os passos 1 a 6.
9. Depois de ambos os controladores foram atualizados, saia do modo de manutenção. Consulte [passo 4: modo de manutenção de saída](../articles/storsimple/storsimple-update-device.md#step4) para obter instruções.

