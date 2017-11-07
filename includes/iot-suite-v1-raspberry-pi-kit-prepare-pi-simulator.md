## <a name="prepare-your-raspberry-pi"></a>Preparar o seu Raspberry Pi

### <a name="install-raspbian"></a>Instalar Raspbian

Se esta for a primeira vez que estiver a utilizar o seu Raspberry Pi, terá de instalar o sistema de operativo Raspbian utilizando NOOBS no cartão SD incluído no kit. O [Raspberry Pi Software guia] [ lnk-install-raspbian] descreve como instalar um sistema operativo no seu Raspberry Pi. Este tutorial parte do princípio de que instalou o sistema de operativo Raspbian no seu Raspberry Pi.

> [!NOTE]
> O cartão SD incluído no [Microsoft Azure IoT Starter Kit para Raspberry Pi 3] [ lnk-starter-kits] já NOOBS instalado. Pode efetuar o arranque a Raspberry Pi deste Card e optar por instalar o SO Raspbian.

Para concluir a configuração de hardware, tem de:

- Ligar o seu Raspberry Pi para a fonte de alimentação incluído no kit.
- Ligar o seu Raspberry Pi à sua rede com o cabo de Ethernet incluído no seu kit. Em alternativa, pode configurar [sem fios conectividade] [ lnk-pi-wireless] para sua Raspberry Pi.

Agora concluiu a configuração de hardware do seu Raspberry Pi.

### <a name="sign-in-and-access-the-terminal"></a>A iniciar sessão e aceder ao terminal

Tem duas opções para aceder a um ambiente de terminal no seu Raspberry Pi:

- Se tiver um teclado e monitor ligado à sua Raspberry Pi, pode utilizar o GUI Raspbian para aceder a uma janela de terminal.

- Aceder a linha de comandos no seu Raspberry Pi utilizando SSH a partir do seu computador de secretária.

#### <a name="use-a-terminal-window-in-the-gui"></a>Utilizar uma janela de terminal no GUI

As credenciais predefinidas Raspbian são username **pi** e palavra-passe **raspberry**. Na barra de tarefas no GUI, pode iniciar o **Terminal** utilitário utilizando o ícone que se assemelha um monitor.

#### <a name="sign-in-with-ssh"></a>Inicie sessão com SSH

Pode utilizar o SSH para o acesso da linha de comandos para sua Raspberry Pi. O artigo [SSH (Secure Shell)] [ lnk-pi-ssh] descreve como configurar SSH no seu Raspberry Pi e como ligar a partir de [Windows] [ lnk-ssh-windows] ou [ SO Linux & Mac][lnk-ssh-linux].

Inicie sessão com o nome de utilizador **pi** e palavra-passe **raspberry**.

#### <a name="optional-share-a-folder-on-your-raspberry-pi"></a>Opcional: Partilhar uma pasta no seu Raspberry Pi

Opcionalmente, pode pretender partilhar uma pasta no seu Raspberry Pi com o seu ambiente de trabalho. Uma pasta de partilha permite-lhe utilizar o seu editor de texto preferido de ambiente de trabalho (tais como [Visual Studio Code](https://code.visualstudio.com/) ou [texto Sublime](http://www.sublimetext.com/)) para editar ficheiros no seu Raspberry Pi em vez de utilizar `nano` ou `vi`.

Para partilhar uma pasta com o Windows, configure um servidor de Samba em Raspberry Pi. Em alternativa, utilize o incorporado [SFTP](https://www.raspberrypi.org/documentation/remote-access/) servidor com um cliente SFTP no ambiente de trabalho.

[lnk-install-raspbian]: https://www.raspberrypi.org/learning/software-guide/quickstart/
[lnk-pi-wireless]: https://www.raspberrypi.org/documentation/configuration/wireless/README.md
[lnk-pi-ssh]: https://www.raspberrypi.org/documentation/remote-access/ssh/README.md
[lnk-ssh-windows]: https://www.raspberrypi.org/documentation/remote-access/ssh/windows.md
[lnk-ssh-linux]: https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md
[lnk-starter-kits]: https://azure.microsoft.com/develop/iot/starter-kits/