<!--author=SharS last changed: 12/01/15-->

#### <a name="to-enter-maintenance-mode"></a>Introduza o modo de manutenção
1. No menu da consola de série, selecione a opção 1, **iniciar sessão com acesso total**.
2. Escreva a palavra-passe. A palavra-passe predefinida é **Password1**.
3. Na linha de comandos, escreva
   
     `Enter-HcsMaintenanceMode`
4. Verá uma mensagem de aviso informá-lo de que modo de manutenção irá interromper todos os pedidos de e/s e sever a ligação ao portal clássico do Azure e lhe-á de confirmação. Tipo **Y** para introduzir o modo de manutenção.
   
    Tanto os controladores irão reiniciar automaticamente. Quando o reinício estiver concluído, será apresentada outra mensagem que indica que o dispositivo estiver no modo de manutenção.

