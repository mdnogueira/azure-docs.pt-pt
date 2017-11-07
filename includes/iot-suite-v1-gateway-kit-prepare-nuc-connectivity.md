## <a name="prepare-your-intel-nuc"></a>Preparar o Intel NUC

Para concluir a configuração de hardware, tem de:

- Ligar o seu NUC Intel para a fonte de alimentação incluído no kit.
- Ligar o seu NUC Intel à sua rede com um cabo de Ethernet.

Agora concluiu a configuração de hardware do dispositivo de gateway Intel NUC.

### <a name="sign-in-and-access-the-terminal"></a>A iniciar sessão e aceder ao terminal

Tem duas opções para aceder a um ambiente de terminal no seu NUC Intel:

- Se tiver um teclado e monitor ligado à sua NUC Intel, pode aceder diretamente a shell. As credenciais predefinidas são username **raiz** e palavra-passe **raiz**.

- Acesso a shell no seu NUC Intel utilizando SSH a partir do seu computador de secretária.

#### <a name="sign-in-with-ssh"></a>Inicie sessão com SSH

Para iniciar sessão com SSH, terá do endereço IP do seu NUC Intel. Se tiver um teclado e monitor ligado à sua NUC Intel, utilize o `ifconfig` comando para localizar o endereço IP. Em alternativa, ligue para o router para listar os endereços dos dispositivos na sua rede.

Inicie sessão com o nome de utilizador **raiz** e palavra-passe **raiz**.

#### <a name="optional-share-a-folder-on-your-intel-nuc"></a>Opcional: Partilhar uma pasta no seu NUC Intel

Opcionalmente, pode pretender partilhar uma pasta no seu NUC Intel com o seu ambiente de trabalho. Uma pasta de partilha permite-lhe utilizar o seu editor de texto preferido de ambiente de trabalho (tais como [Visual Studio Code](https://code.visualstudio.com/) ou [texto Sublime](http://www.sublimetext.com/)) para editar ficheiros no seu NUC Intel em vez de utilizar `nano` ou `vi`.

Para partilhar uma pasta com o Windows, configure um servidor de Samba em NUC o Intel. Em alternativa, utilize o servidor SFTP no NUC Intel com um cliente SFTP no seu computador de secretária.
