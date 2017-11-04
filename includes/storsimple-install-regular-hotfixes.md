<!--author=SharS last changed: 9/17/15-->

#### <a name="to-install-regular-hotfixes-via-windows-powershell-for-storsimple"></a>Instalar correções regulares através do Windows PowerShell para StorSimple
1. Ligar à consola de série do dispositivo. Para obter mais informações, consulte [passo 1: ligar a consola de série do](../articles/storsimple/storsimple-update-device.md#step1).
2. No menu da consola de série, selecione a opção 1, **iniciar sessão com acesso total**. Escreva a palavra-passe. A palavra-passe predefinida é **Password1**.
3. Na linha de comandos, escreva:
   
    ```
    Start-HcsHotfix
    ```
   
    > [!IMPORTANT]
    >
    > Este comando só se aplica a correções regulares. Execute este comando em apenas um controlador, mas serão atualizados em ambos os controladores.
    > Poderá notar uma ativação pós-falha controlador durante o processo de atualização; No entanto, a ativação pós-falha não irá afetar a disponibilidade de sistema ou a operação.

4. Quando lhe for pedido, forneça o caminho para a pasta de rede partilhada que contém os ficheiros de correção.
5. Será solicitado para confirmação. Tipo **Y** para prosseguir com a instalação de correção.

