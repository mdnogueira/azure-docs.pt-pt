<!--author=alkohli last changed: 12/01/15-->


#### <a name="to-configure-and-register-the-device"></a>Para configurar e registar o dispositivo
1. Aceda à interface do Windows PowerShell na consola de série do dispositivo StorSimple. Para obter as instruções, veja [Utilizar o PuTTY para ligar à consola de série do dispositivo](#use-putty-to-connect-to-the-device-serial-console). **Confirme que está a seguir o procedimento rigorosamente. Caso contrário, não conseguirá aceder à consola.**
2. Na sessão apresentada, prima Enter uma vez para obter uma linha de comandos. 
3. Ser-lhe-á solicitado que escolha o idioma que pretende configurar para o dispositivo. Especifique o idioma e prima Enter. 
   
    ![Configurar e registar o dispositivo 1 do StorSimple](./media/storsimple-configure-and-register-device/HCS_RegisterYourDevice1-include.png)
4. No menu da consola de série apresentado, selecione a opção 1 para iniciar sessão com acesso total. 
   
    ![Registar o dispositivo 2 do StorSimple](./media/storsimple-configure-and-register-device/HCS_RegisterYourDevice2-include.png)
   
     Execute os passos 5 a 12 para configurar as definições de rede mínimas necessárias para o dispositivo. **Estes passos de configuração têm de ser executados no controlador ativo do dispositivo.** O menu da consola de série indica o estado do controlador na mensagem de faixa. Se não estiver ligado ao controlador ativo, desligue e volte a ligar ao controlador ativo.
5. Na linha de comandos, escreva a sua palavra-passe. A palavra-passe predefinida do dispositivo é **Password1**.
6. Escreva o seguinte comando:
   
     `Invoke-HcsSetupWizard` 
7. Será apresentado um assistente de configuração para o ajudar a configurar as definições de rede do dispositivo. Forneça as seguintes informações: 
   
   * Endereço IP da interface de rede DATA 0
   * Máscara de sub-rede
   * Gateway
   * Endereço IP do servidor DNS Primário
   * Endereço IP do servidor NTP Primário
     
     > [!NOTE]
     > Poderá ter de aguardar alguns minutos para que sejam aplicadas as definições de DNS e de máscara de sub-rede. Se receber uma mensagem de erro “O dispositivo não está pronto.”, verifique a ligação de rede física na interface da rede DATA 0 do controlador ativo.
     > 
     > 
8. (Opcional) Configure o servidor proxy Web. Apesar de a configuração do proxy Web ser opcional, **tenha em atenção que se utilizar um proxy Web, só pode configurá-lo aqui**. Para obter mais informações, veja [Configure web proxy for your device (Configurar o proxy Web para o seu dispositivo)](../articles/storsimple/storsimple-configure-web-proxy.md). Caso se depare com problemas durante este passo, veja a orientação de resolução de problemas para [Errors during web proxy configuration (Erros durante a configuração do proxy Web)](../articles/storsimple/storsimple-troubleshoot-deployment.md#errors-during-the-optional-web-proxy-settings).

     > [!NOTE]
     > Pode premir Ctrl + C em qualquer momento para sair do assistente de configuração. As definições que aplicou antes de emitir este comando serão mantidas.

1. Por motivos de segurança, a palavra-passe de administrador do dispositivo expira após a primeira sessão e terá de ser alterada nas sessões subsequentes. Quando lhe for solicitado, forneça uma palavra-passe de administrador do dispositivo. Uma palavra-passe de administrador do dispositivo válida tem de ter entre 8 e 15 carateres. A palavra-passe tem de conter uma combinação de carateres em minúsculas, carateres em maiúsculas, números e carateres especiais.
2. A palavra-passe do Snapshot Manager do StorSimple também é definida aqui. Utilize esta palavra-passe quando autenticar um dispositivo com o anfitrião do Windows a executar o Snapshot Manager do StorSimple. Quando lhe for solicitado, forneça uma palavra-passe de 14 a 15 carateres. A palavra-passe tem de conter uma combinação de carateres em minúsculas, carateres em maiúsculas, números e carateres especiais. 
   
   ![Registar o dispositivo 4 do StorSimple](./media/storsimple-configure-and-register-device/HCS_RegisterYourDevice4-include.png)
   
   Só pode repor a palavra-passe do Snapshot Manager do StorSimple na interface de serviço do StorSimple Manager. Para obter os passos detalhados, veja [Change the StorSimple passwords using the StorSimple Manager serivce (Alterar as palavras-passe do StorSimple com o serviço StorSimple Manager)](../articles/storsimple/storsimple-change-passwords.md).
   
   Para resolver problemas durante este passo, veja a orientação de resolução de problemas para [Errors related to passwords (Erros relacionados a palavra-passes)](../articles/storsimple/storsimple-troubleshoot-deployment.md#errors-related-to-device-administrator-and-storsimple-snapshot-manager-passwords).
3. O último passo do assistente de configuração regista o dispositivo com o serviço StorSimple Manager. Para o poder fazer, precisa da chave de registo do serviço obtida no passo 2. Depois de fornecer a chave de registo, poderá ter de aguardar 2 a 3 minutos até o dispositivo ficar registado.
   
   Para resolver possíveis falhas de registo do dispositivo, veja [Errors during device registration (Erros durante o registo do dispositivo)](../articles/storsimple/storsimple-troubleshoot-deployment.md#errors-during-device-registration). Para obter a resolução de problemas detalhada, pode também ver [Step-by-step troubleshooting example (Exemplo de resolução de problemas passo-a-passo)](../articles/storsimple/storsimple-troubleshoot-deployment.md#step-by-step-storsimple-troubleshooting-example).
4. Uma vez registado o dispositivo, aparecerá uma chave de Encriptação de Dados do Serviço. Copie esta chave e guarde-a numa localização segura.
   
   > [!WARNING]
   > Esta chave será necessária com a chave de registo do serviço para registar dispositivos adicionais com o serviço StorSimple Manager. Veja [Segurança do StorSimple](../articles/storsimple/storsimple-security.md) para obter mais informações sobre esta chave.
   > 
   > 
   
    ![Registar o dispositivo 6 do StorSimple](./media/storsimple-configure-and-register-device/HCS_RegisterYourDevice6-include.png)
   
    Para copiar o texto da janela da consola de série, basta selecioná-lo. Em seguida, poderá colá-lo na área de transferência ou num editor de texto. NÃO utilize Ctrl + C para copiar a chave de encriptação de dados do serviço. A utilização de Ctrl + C fecha o assistente de configuração. Como resultado, a palavra-passe de administrador do dispositivo e a palavra-passe do Snapshot Manager do StorSimple não serão alteradas e o dispositivo irá reverter para as palavras-passe predefinidas.
5. Feche a consola de série.
6. Regresse ao Portal Clássico do Azure e execute os seguintes passos:
   
   1. Faça duplo clique no serviço StorSimple Manager para aceder à página **Início Rápido**.
   2. Clique em **Ver dispositivos ligados**.
   3. Na página **Dispositivos**, verifique se o dispositivo foi ligado com êxito ao serviço, vendo o seu estado. O estado do dispositivo deve ser **Online**. Se o estado do dispositivo for **Offline**, aguarde alguns minutos para que o dispositivo fique online.
   
   ![Página Dispositivos do StorSimple](./media/storsimple-configure-and-register-device/HCS_DevicesPageM-include.png) 
   
   > [!IMPORTANT]
   > Quando o dispositivo estiver online, ligue os cabos de rede que tinha desligado no início deste passo.
   > 
   > 

Depois de o dispositivo estar registado com êxito e se não ficar online, pode executar o `Test-HcsmConnection -Verbose` para confirmar que a conectividade da rede está em bom estado. Para obter a utilização detalhada deste cmdlet, veja a [referência de cmdlets para Test-HcsmConnection](https://technet.microsoft.com/library/dn715782.aspx).

![Vídeo disponível](./media/storsimple-configure-and-register-device/Video_icon.png) **Vídeo disponível**

Para ver um vídeo que mostra como configurar e registar o dispositivo através do Windows PowerShell para StorSimple, clique [aqui](https://azure.microsoft.com/documentation/videos/initialize-the-storsimple-appliance/).

