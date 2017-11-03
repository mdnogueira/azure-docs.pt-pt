## <a name="install-the-prerequisites"></a>Instale os pré-requisitos

Os passos neste tutorial partem do princípio de que está a executar Ubuntu Linux.

Para instalar os pacotes de pré-requisitos, abra uma shell e execute os seguintes comandos:

```bash
sudo apt-get update
sudo apt-get install curl build-essential libcurl4-openssl-dev git cmake pkg-config libssl-dev uuid-dev valgrind libglib2.0-dev libtool autoconf
```

Na shell, execute o seguinte comando para clonar o repositório do GitHub de limite de IoT do Azure no seu computador local:

```bash
git clone https://github.com/Azure/iot-edge.git
```

## <a name="how-to-build-the-sample"></a>Como criar o exemplo

Agora pode compilar o limite de IoT e exemplos de tempo de execução no seu computador local:

1. Abra uma shell.

1. Navegue para a pasta raiz na sua cópia local do repositório **iot-edge**.

1. Execute o script de compilação da seguinte forma:

    ```sh
    tools/build.sh --disable-native-remote-modules
    ```

Este script utiliza o utilitário **cmake** para criar uma pasta denominada **build** na pasta raiz da sua cópia local do repositório **iot-edge** e gerar um makefile. Em seguida, o script compila a solução, ignorando os teste de unidade e os testes completos. Se pretender criar e executar os testes de unidade, adicione o `--run-unittests` parâmetro. Se pretender criar e executar os testes de ponto a ponto, adicione o `--run-e2e-tests`.

> [!NOTE]
> Sempre que executar o script **build.sh**, este elimina e, em seguida, recria a pasta **build** na pasta raiz da sua cópia local do repositório **iot-edge**.