## <a name="install-the-prerequisites"></a>Instale os pré-requisitos

1. Instalar [Visual Studio 2015 ou 2017](https://www.visualstudio.com). Pode utilizar a edição da Comunidade gratuito se cumpre os requisitos de licenciamento. Lembre-se de que incluem o Visual C++ e Gestor de pacotes NuGet.

1. Instalar [git](http://www.git-scm.com) e certifique-se de que pode executar git.exe na linha de comandos.

1. Instalar [CMake](https://cmake.org/download/) e certifique-se de que pode executar cmake.exe na linha de comandos. CMake versão 3.7.2 ou posterior é recomendada. O **. msi** installer é a opção mais fácil no Windows. Adicionar o CMake ao caminho para, pelo menos, o utilizador atual quando o instalador irá pedir-lhe.

1. Instalar [Python 2.7](https://www.python.org/downloads/release/python-27). Certifique-se de adicionar o Python a sua `PATH` variável de ambiente. Aceda a **painel de controlo** > **sistema e segurança** > **sistema** > **definições de sistemaAvançadas**  >  **Variáveis de ambiente**. Adicionar `C:\Python27` para o caminho. 

1. Numa linha de comandos, execute o seguinte comando para clonar o repositório do GitHub de limite de IoT do Azure no seu computador local:

    ```cmd
    git clone https://github.com/Azure/iot-edge.git
    ```

## <a name="how-to-build-the-sample"></a>Como criar o exemplo

Agora pode compilar o limite de IoT e exemplos de tempo de execução no seu computador local:

1. Abra **linha de comandos do programador para VS 2015** ou **linha de comandos do programador para VS 2017**, consoante a sua versão.

1. Navegue para a pasta raiz na sua cópia local do repositório **iot-edge**.

1. Execute o script de compilação da seguinte forma:

    ```cmd
    tools\build.cmd --disable-native-remote-modules
    ```

Este script cria um ficheiro de solução do Visual Studio e compila a solução. Pode encontrar a solução do Visual Studio no **criar** pasta na sua cópia local do **iot edge** repositório. Se pretender criar e executar os testes de unidade, adicione o `--run-unittests` parâmetro. Se pretender criar e executar os testes de ponto a ponto, adicione o `--run-e2e-tests`.

> [!NOTE]
> Sempre que executar o **build.cmd** script, este elimina e, em seguida, recria a **criar** pasta na pasta raiz da sua cópia local a **iot edge** repositório.