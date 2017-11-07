## <a name="prepare-your-raspberry-pi"></a>Preparar o seu Raspberry Pi

### <a name="install-raspbian"></a>Instalar Raspbian

Se esta for a primeira vez que estiver a utilizar o seu Raspberry Pi, terá de instalar o sistema de operativo Raspbian utilizando NOOBS no cartão SD incluído no kit. O [Raspberry Pi Software guia] [ lnk-install-raspbian] descreve como instalar um sistema operativo no seu Raspberry Pi. Este tutorial parte do princípio de que instalou o sistema de operativo Raspbian no seu Raspberry Pi.

> [!NOTE]
> O cartão SD incluído no [Microsoft Azure IoT Starter Kit para Raspberry Pi 3] [ lnk-starter-kits] já NOOBS instalado. Pode efetuar o arranque a Raspberry Pi deste Card e optar por instalar o SO Raspbian.

### <a name="set-up-the-hardware"></a>Configurar o hardware

Este tutorial utiliza o sensor BME280 incluído no [Microsoft Azure IoT Starter Kit para Raspberry Pi 3] [ lnk-starter-kits] para gerar dados de telemetria. Utiliza um LED para indicar quando Raspberry Pi processa um método de invocação do dashboard da solução.

Os componentes no quadro a bread são:

- LED vermelho
- 220 Ohm resistor (vermelho, vermelho, castanha)
- Sensor de BME280

O diagrama seguinte mostra como ligar o seu hardware:

![Configuração de hardware para Raspberry Pi][img-connection-diagram]

A tabela seguinte resume as ligações do Raspberry Pi para os componentes a breadboard:

| Raspberry Pi            | Breadboard             |Cor         |
| ----------------------- | ---------------------- | ------------- |
| GND (Pin 14)            | Pin de - Provider LED (18A)      | Roxo          |
| GPCLK0 (Pin 7)          | Resistor (25A)         | Orange          |
| SPI_CE0 (Pin 24)        | CS (39A)               | Azul          |
| SPI_SCLK (Pin 23)       | SCK (36A)              | Amarelo        |
| SPI_MISO (Pin 21)       | SDO (37A)              | Branco         |
| SPI_MOSI (Pin 19)       | SDI (38A)              | Verde         |
| GND (Pin 6)             | GND (35A)              | Preto         |
| 3.3 V (Pin 1)           | 3Vo (34A)              | Vermelho           |

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

### <a name="enable-spi"></a>Ativar SPI

Antes de poder executar a aplicação de exemplo, tem de ativar o barramento série periféricos Interface (SPI) Raspberry Pi. Raspberry Pi comunica com o dispositivo de sensor BME280 através do barramento SPI. Utilize o seguinte comando para editar o ficheiro de configuração:

```sh
sudo nano /boot/config.txt
```

Localize a linha:

`#dtparam=spi=on`

- Anule o comentário da linha, eliminar a `#` no início.
- Guardar as alterações (**Ctrl-O**, **Enter**) e saia do editor (**Ctrl-X**).
- Para ativar SPI, reinicie o Raspberry Pi. Reiniciando desliga o terminal, terá de iniciar sessão novamente quando o Raspberry Pi reinicia:

  ```sh
  sudo reboot
  ```


[img-connection-diagram]: media/iot-suite-v1-raspberry-pi-kit-prepare-pi/rpi2_remote_monitoring.png

[lnk-install-raspbian]: https://www.raspberrypi.org/learning/software-guide/quickstart/
[lnk-pi-wireless]: https://www.raspberrypi.org/documentation/configuration/wireless/README.md
[lnk-pi-ssh]: https://www.raspberrypi.org/documentation/remote-access/ssh/README.md
[lnk-ssh-windows]: https://www.raspberrypi.org/documentation/remote-access/ssh/windows.md
[lnk-ssh-linux]: https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md
[lnk-starter-kits]: https://azure.microsoft.com/develop/iot/starter-kits/