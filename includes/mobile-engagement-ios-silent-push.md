> [!IMPORTANT]
> Para receber Notificações Push do Mobile Engagement, tem de ativar as `Silent Remote Notifications` na aplicação. Tem de adicionar o valor da notificação remota à matriz UIBackgroundModes no ficheiro Info.plist.
> 
> 

1. Abra o ficheiro `info.plist` no projeto
2. Clique com o botão direito do rato no item superior na lista (`Information Property List`) e adicione uma nova linha
   
    ![](./media/mobile-engagement-ios-silent-push/xcode-plist-add-silent-push1.png)
3. Na nova linha introduza `Required background modes`
   
    ![](./media/mobile-engagement-ios-silent-push/xcode-plist-add-silent-push2.png)
4. Clique na seta para a esquerda para expandir a linha
5. Adicione o seguinte valor ao item 0 `App downloads content in response to push notifications`
   
    ![](./media/mobile-engagement-ios-silent-push/xcode-plist-add-silent-push3.png)
6. Depois de efetuar a alteração, o XML info.plist deverá conter a seguinte chave e valor:
   
        <key>UIBackgroundModes</key>
        <array>
        <string>remote-notification</string>
        </array>
7. Se estiver a utilizar o **Xcode 7+** e o **iOS 9+**:
   
   * Ative **Notificações Push** em Destinos > Nome do Destino > Capacidades.

