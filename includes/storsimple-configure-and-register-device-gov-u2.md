<!--author=SharS last changed: 02/22/2016-->

### <a name="to-configure-and-register-the-device"></a>Para configurar e registar o dispositivo
1. Aceda à interface do Windows PowerShell na consola de série do dispositivo StorSimple. Para obter as instruções, veja [Utilizar o PuTTY para ligar à consola de série do dispositivo](../articles/storsimple/storsimple-deployment-walkthrough-gov-u2.md#use-putty-to-connect-to-the-device-serial-console). **Confirme que está a seguir o procedimento rigorosamente. Caso contrário, não conseguirá aceder à consola.**
2. Na sessão apresentada, prima Enter uma vez para obter uma linha de comandos.
3. Ser-lhe-á solicitado que escolha o idioma que pretende configurar para o dispositivo. Especifique o idioma e prima Enter.
   
    ![Configurar e registar o dispositivo 1 do StorSimple](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice1-gov-include.png)
4. No menu da consola de série apresentado, selecione a opção 1 para iniciar sessão com acesso total.
   
    ![Registar o dispositivo 2 do StorSimple](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice2-gov-include.png)
5. Execute os seguintes passos para configurar as definições de rede mínimas necessárias para o seu dispositivo.
   
   > [!IMPORTANT]
   > Estes passos de configuração têm de ser executados no controlador ativo do dispositivo. O menu da consola de série indica o estado do controlador na mensagem de faixa. Se não estiver a ligar ao controlador ativo, desligue e volte a ligar ao controlador ativo.
   > 
   > 
   
   1. Na linha de comandos, escreva a sua palavra-passe. A palavra-passe predefinida do dispositivo é **Password1**.
   2. Escreva o seguinte comando:
      
        `Invoke-HcsSetupWizard`
   3. Será apresentado um assistente de configuração para o ajudar a configurar as definições de rede do dispositivo. Forneça as seguintes informações:
      
      * Endereço de IP da interface rede DATA 0
      * Máscara de sub-rede
      * Gateway
      * Endereço IP do servidor DNS Primário
      * Endereço IP do servidor NTP Primário
      
      > [!NOTE]
      > Poderá ter de aguardar alguns minutos para a máscara de sub-rede e as definições de DNS sejam aplicadas.
      > 
      > 
   4. Opcionalmente, configure o servidor proxy web.
      
      > [!IMPORTANT]
      > Apesar da configuração de proxy web é opcional, lembre-se de que se utilizar um proxy web, só pode configurá-lo aqui. Para obter mais informações, veja [Configure web proxy for your device (Configurar o proxy Web para o seu dispositivo)](../articles/storsimple/storsimple-configure-web-proxy.md).
      > 
      > 
6. Prima Ctrl + C para sair do Assistente de configuração.
7. Instale as atualizações da seguinte forma:
   
   1. Utilize o cmdlet seguinte para definir IPs em ambos os controladores:
      
      `Set-HcsNetInterface -InterfaceAlias Data0 -Controller0IPv4Address <Controller0 IP> -Controller1IPv4Address <Controller1 IP>`
   2. Na linha de comandos, execute `Get-HcsUpdateAvailability`. Devem ser notificados que atualizações estão disponíveis.
   3. Execute `Start-HcsUpdate`. Pode executar este comando em qualquer nó. Actualizações serão aplicadas no primeiro controlador, o controlador de efetuar a ativação pós-falha e, em seguida, as atualizações serão aplicadas de outro controlador.
      
      Pode monitorizar o progresso da atualização executando `Get-HcsUpdateStatus`.    
      
      A seguinte saída de exemplo mostra a atualização em curso.
      
      ````
      Controller0>Get-HcsUpdateStatus
      RunInprogress       : True
      LastHotfixTimestamp : 4/13/2015 10:56:13 PM
      LastUpdateTimestamp : 4/13/2015 10:35:25 PM
      Controller0Events   :
      Controller1Events   :
      ````
      
      A saída de exemplo seguinte indica que a atualização foi concluída.
      
      ```
      Controller1>Get-HcsUpdateStatus
      
      RunInprogress       : False
      LastHotfixTimestamp : 4/13/2015 10:56:13 PM
      LastUpdateTimestamp : 4/13/2015 10:35:25 PM
      Controller0Events   :
      Controller1Events   :
      ```
      
      Pode demorar até 11 horas para aplicar a todas as atualizações, incluindo as atualizações do Windows.
8. Execute o seguinte cmdlet para apontar o dispositivo ao portal do Microsoft Azure Government (porque aponta para o portal clássico do Azure público por predefinição). Esta ação irá reiniciar tanto os controladores. Recomendamos que utilize duas sessões PuTTY para ligar em simultâneo para ambos os controladores para que possa ver quando cada controlador é reiniciado.
   
    `Set-CloudPlatform -AzureGovt_US`
   
   Verá uma mensagem de confirmação. Aceite a predefinição (**Y**).
9. Execute o cmdlet seguinte para retomar a configuração:
   
    `Invoke-HcsSetupWizard`
   
    ![Assistente de configuração de retomar](./media/storsimple-configure-and-register-device-gov-u2/HCS_ResumeSetup-gov-include.png)
   
   Quando retomar a configuração, o assistente será a versão de atualização 2.
10. Aceite as definições de rede. Verá uma mensagem de validação após a aceitação de cada definição.
11. Por motivos de segurança, a palavra-passe de administrador do dispositivo expira após a primeira sessão e terá de a alterar agora. Quando lhe for solicitado, forneça uma palavra-passe de administrador do dispositivo. Uma palavra-passe de administrador do dispositivo válida tem de ter entre 8 e 15 carateres. A palavra-passe tem de conter três dos seguintes: minúsculas, maiúsculas, números e carateres especiais.
    
    <br/>![Registar o dispositivo 5 do StorSimple](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice5_gov-include.png)
12. O último passo do assistente de configuração regista o dispositivo com o serviço StorSimple Manager. Para tal, terá da chave de registo do serviço que obteve no [passo 2: obter a chave de registo do serviço](../articles/storsimple/storsimple-deployment-walkthrough-gov-u2.md#step-2-get-the-service-registration-key). Depois de fornecer a chave de registo, poderá ter de aguardar 2 a 3 minutos até o dispositivo ficar registado.
    
    > [!NOTE]
    > Pode premir Ctrl + C em qualquer momento para sair do assistente de configuração. Se tiver introduzido todas as definições de rede (Endereço IP para Data 0, Máscara de sub-rede e Gateway), as entradas serão mantidas.
    > 
    > 
    
    ![Progresso de registo do StorSimple](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegistrationProgress-gov-include.png)
13. Uma vez registado o dispositivo, aparecerá uma chave de Encriptação de Dados do Serviço. Copie esta chave e guarde-a numa localização segura. **Esta chave será necessária com a chave de registo do serviço para registar dispositivos adicionais com o serviço StorSimple Manager.** Veja [Segurança do StorSimple](../articles/storsimple/storsimple-security.md) para obter mais informações sobre esta chave.
    
    ![Registar o dispositivo 7 do StorSimple](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice7_gov-include.png)    
    
    > [!IMPORTANT]
    > Para copiar o texto da janela da consola de série, basta selecioná-lo. Em seguida, poderá colá-lo na área de transferência ou num editor de texto.
    > 
    > NÃO utilize Ctrl + C para copiar a chave de encriptação de dados do serviço. A utilização de Ctrl + C fecha o assistente de configuração. Como resultado, a palavra-passe de administrador do dispositivo não será alterada e o dispositivo irá reverter para a palavra-passe predefinida.
    > 
    > 
14. Feche a consola de série.
15. Regresse ao Portal de administração pública do Azure e execute os seguintes passos:
    
    1. Faça duplo clique no serviço StorSimple Manager para aceder à página **Início Rápido**.
    2. Clique em **Ver dispositivos ligados**.
    3. Na página **Dispositivos**, verifique se o dispositivo foi ligado com êxito ao serviço, vendo o seu estado. O estado do dispositivo deve ser **Online**.
       
        ![Página Dispositivos do StorSimple](./media/storsimple-configure-and-register-device-gov-u2/HCS_DeviceOnline-gov-include.png)
       
        Se o estado do dispositivo for **Offline**, aguarde alguns minutos para que o dispositivo fique online.
       
        Se o dispositivo continuar offline mesmo após alguns minutos, terá então de verificar se a sua rede de firewall foi configurada do modo descrito em [Requisitos de rede para o seu dispositivo StorSimple](../articles/storsimple/storsimple-system-requirements.md).
       
        Verifique se a porta 9354 está aberta para comunicação de saída, uma vez que é utilizada pelo barramento de serviço para a comunicação Serviço a dispositivo do StorSimple Manager.

