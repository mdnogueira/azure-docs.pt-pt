<!--author=alkohli last changed: 9/17/15-->

#### <a name="to-complete-the-minimum-storsimple-device-setup"></a>Para concluir a configuração mínima do dispositivo StorSimple
1. Na página **Dispositivos**, selecione o dispositivo, clique na seta junto ao nome do dispositivo para ir para a página específica do dispositivo. 
   
    ![Página de dispositivos com o dispositivo online](./media/storsimple-complete-minimum-device-setup/HCS_DevicesPageM-include.png) 
2. Clique no ícone de início rápido ![Ícone Início Rápido](./media/storsimple-complete-minimum-device-setup/HCS_QuickStartIcon-include.png) para aceder à página de início rápido do dispositivo. Clique em **Concluir a configuração do dispositivo** para iniciar o assistente **Configurar dispositivo**.
   
    ![Página de início rápido do dispositivo](./media/storsimple-complete-minimum-device-setup/Device_Quick_Start_page_1M.png)
3. Na página **Definições Básicas**, realize o seguinte:
   
   1. Forneça um **nome amigável** para o dispositivo. O nome predefinido do dispositivo reflete informações como o modelo e número de série do dispositivo. Pode atribuir um nome amigável com um máximo de 64 carateres para gerir o dispositivo.
   2. Defina o **fuso horário** com base na localização geográfica onde o dispositivo está a ser implementado. O dispositivo utiliza este fuso horário para todas as operações agendadas.
   3. Em **Definições de DNS**, forneça um endereço para o **Servidor DNS Secundário**. Se estiver a utilizar IPv6, o campo será preenchido com base no prefixo IPv6 fornecido na interface do Windows PowerShell. 
      Se o servidor DNS secundário não estiver configurado, não lhe será permitido guardar a configuração do dispositivo.
   4. Nas interfaces preparadas para iSCSI, ative, pelo menos, uma rede para iSCSI. Tem de haver, no mínimo, uma interface de rede preparada para a nuvem e uma interface preparada para iSCSI. DATA 0 é automaticamente ativado para a nuvem.
      
      ![Definições básicas para a configuração mínima do dispositivo StorSimple](./media/storsimple-complete-minimum-device-setup/HCS_MinDeviceSetupBasicSettings1-include.png)
4. Clique no ícone de seta. ![Ícone de seta do StorSimple](./media/storsimple-complete-minimum-device-setup/HCS_ArrowIcon-include.png)
5. Na página **Interfaces de Rede**, forneça os endereços IP fixos para o Controlador 0 e o Controlador 1. Se a interface DATA 0 tiver sido configurada para IPv4, os endereços IP fixos têm de ser fornecidos no formato IPv4. Se tiver indicado um prefixo para a configuração IPv6, os endereços IP fixos serão preenchidos automaticamente nestes campos.

    > [!NOTE] 
    > - Os endereços IP fixos do controlador têm de ser IPs livres na sub-rede acessível pelo endereço IP do dispositivo.
    > - Os endereços IP fixos do controlador são utilizados para a manutenção de atualizações do dispositivo e, por conseguinte, os IPs fixos têm de ser encaminháveis e conseguir estabelecer ligação à Internet.

    ![Interfaces de rede para a configuração mínima do dispositivo StorSimple](./media/storsimple-complete-minimum-device-setup/HCS_MinDeviceSetupNetworkInterfaces2-include.png)

1. Clique no ícone de verificação ![Ícone de verificação do StorSimple](./media/storsimple-complete-minimum-device-setup/HCS_CheckIcon-include.png).
   Voltará à página **Início Rápido** do dispositivo.
   
   > [!NOTE]
   > Pode modificar todas as outras definições do dispositivo em qualquer altura acedendo à página **Configurar**.
   > 
   > 

![Vídeo disponível](./media/storsimple-complete-minimum-device-setup/Video_icon.png) **Vídeo disponível**

Para ver um vídeo que demonstra como concluir a configuração mínima do dispositivo, clique [aqui](https://azure.microsoft.com/documentation/videos/minimum-storsimple-device-setup/).

