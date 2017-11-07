## <a name="build-iot-edge"></a>Criar IoT Edge

Este tutorial utiliza módulos personalizados do IoT limite para comunicar com a solução pré-configurada de monitorização remota. Por conseguinte, terá de criar limite IoT módulos a partir do código de origem personalizada. As secções seguintes descrevem como instalar o limite de IoT e criar o módulo de limite de IoT personalizado.

### <a name="install-iot-edge"></a>Instalar o limite de IoT

Os passos seguintes descrevem como instalar o software de limite de IoT previamente compilado em NUC o Intel:

1. Configure repositórios do pacote inteligente necessário executando os seguintes comandos no NUC Intel:

    ```bash
    smart channel --add IoT_Cloud type=rpm-md name="IoT_Cloud" baseurl=http://iotdk.intel.com/repos/iot-cloud/wrlinux7/rcpl13/ -y
    smart channel --add WR_Repo type=rpm-md baseurl=https://distro.windriver.com/release/idp-3-xt/public_feeds/WR-IDP-3-XT-Intel-Baytrail-public-repo/RCPL13/corei7_64/
    ```

    Introduza `y` quando o comando pede-lhe que **incluir este canal?**.

1. Atualize o Gestor de pacote inteligente executando o seguinte comando:

    ```bash
    smart update
    ```

1. Instale o pacote de limite de IoT do Azure executando o seguinte comando:

    ```bash
    smart config --set rpm-check-signatures=false
    smart install packagegroup-cloud-azure -y
    ```

1. Certifique-se a instalação ao executar o exemplo de "Olá mundo". Este exemplo escreve uma mensagem de mundo Olá o ficheiro log.txT cada cinco segundos. Os seguintes comandos, execute o exemplo de "Olá mundo":

    ```bash
    cd /usr/share/azureiotgatewaysdk/samples/hello_world/
    ./hello_world hello_world.json
    ```

    Ignorar todas **argumento inválido** mensagens quando deixa de amostra.

    Utilize o seguinte comando para ver os conteúdos do ficheiro de registo:

    ```bash
    cat log.txt | more
    ```

### <a name="troubleshooting"></a>Resolução de problemas

Se receber o erro "não pacote fornece util-linux-dev", tente reiniciar o NUC Intel.
