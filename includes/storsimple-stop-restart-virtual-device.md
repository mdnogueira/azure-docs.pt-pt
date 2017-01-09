#### <a name="to-stop-and-start-a-virtual-device"></a>Para parar e reiniciar um dispositivo virtual
Para parar um dispositivo virtual, clique no respetivo nome e, em seguida, em **Encerramento**. Enquanto o dispositivo virtual está a ser encerrado, o seu estado será **A Parar**. Quando o dispositivo virtual parar, o estado muda para **Parado**.

Utilize os seguintes cmdlets para parar e iniciar um dispositivo virtual.

`Stop-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

`Start-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

#### <a name="to-restart-a-virtual-device"></a>Para reiniciar um dispositivo virtual
Quando um dispositivo virtual estiver em execução e pretender reiniciá-lo, clique no respetivo nome e, em seguida, em **Reiniciar**. Enquanto o dispositivo virtual estiver a ser reiniciado, o seu estado será **A Reiniciar**. Quando o dispositivo virtual estiver pronto para ser utilizado, o seu estado será **Em Execução**.

Utilize os seguintes cmdlets para reiniciar um dispositivo virtual.

`Restart-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`



<!--HONumber=Jan17_HO1-->


