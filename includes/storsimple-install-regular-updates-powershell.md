<!--author=SharS last changed: 11/18/16-->

#### <a name="to-install-regular-updates-via-windows-powershell-for-storsimple"></a>Para instalar atualizações regulares através do Windows PowerShell para StorSimple
1. Abra a consola de série do dispositivo e selecione opção 1, **iniciar sessão com acesso total**. Escreva a palavra-passe. A palavra-passe predefinida é *Password1*. 
2. Na linha de comandos, escreva:
   
     `Get-HcsUpdateAvailability`
   
    Será notificado se as atualizações estiverem disponíveis e se as atualizações são acontece ou não acontece.
3. Na linha de comandos, escreva:
   
     `Start-HcsUpdate`
   
    O processo de atualização será iniciado.

> [!IMPORTANT]
> * Este comando só se aplica a atualizações regulares. Execute este comando em apenas um controlador, mas serão atualizados em ambos os controladores. 
> * Poderá notar uma ativação pós-falha controlador durante o processo de atualização; No entanto, a ativação pós-falha não irá afetar a disponibilidade de sistema ou a operação.
> 
> 

