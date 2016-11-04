Alguns pacotes podem não ser instalados através do pip quando são executados no Azure.  Tal pode dever-se, simplesmente, ao facto de o pacote não estar disponível no Índice do Pacote do Python.  Talvez seja necessário um compilador (não está disponível nenhum compilador no computador que executa a aplicação Web no Serviço de Aplicações do Azure).

Nesta secção, vamos abordar formas de lidar com este problema.

### Solicitar rodas
Se a instalação do pacote exigir um compilador, deve tentar contactar o proprietário do pacote para pedir que as rodas sejam disponibilizadas para o pacote.

Com a recente disponibilidade do [Microsoft Visual C++ Compiler para o Python 2.7][Microsoft Visual C++ Compiler para o Python 2.7], agora é mais fácil criar pacotes com código nativo do Python 2.7.

### Criar rodas (requer o Windows)
Nota: ao utilizar esta opção, garanta que compila o pacote através de um ambiente Python que corresponde à plataforma/arquitetura/versão que é utilizada na aplicação Web no Serviço de Aplicações do Azure (Windows/32 bits/2.7 ou 3.4).

Se o pacote não for instalado por ser necessário um compilador, pode instalar o compilador no computador local e criar uma roda para o pacote, que irá incluir no seu repositório.

Utilizadores MAC/Linux: se não tiver acesso a um computador do Windows, veja [Criar uma Máquina Virtual com o Windows][Criar uma Máquina Virtual com o Windows] para saber como criar uma VM no Azure.  Pode utilizá-la para criar as rodas, adicioná-las ao repositório e eliminar a VM, se assim o desejar. 

Para o Python 2.7, pode instalar o [Microsoft Visual C++ Compiler para o Python 2.7][Microsoft Visual C++ Compiler para o Python 2.7].

Para o Python 3.4, pode instalar o [Microsoft Visual C++ 2010 Express][Microsoft Visual C++ 2010 Express].

Para criar rodas, precisa do pacote de rodas:

    env\scripts\pip install wheel

Deverá utilizar `pip wheel` para compilar uma dependência:

    env\scripts\pip wheel azure==0.8.4

Deste modo, cria um ficheiro .whl na pasta \wheelhouse.  Adicione a pasta \wheelhouse e os ficheiros de rodas ao repositório.

Edite o requirements.txt para adicionar a opção `--find-links` à parte superior. Deste modo, diz ao pip para procurar uma correspondência exata na pasta local antes de aceder ao índice de pacote do python.

    --find-links wheelhouse
    azure==0.8.4

Se pretender incluir todas as suas dependências na pasta \wheelhouse e não utilizar o índice de pacote do python, pode forçar o pip a ignorar o índice de pacote adicionando `--no-index` à parte superior do requirements.txt.

    --no-index

### Personalizar a instalação
Pode personalizar o script de implementação para instalar um pacote no ambiente virtual através de um instalador alternativo, tal como uma instalação\_fácil.  Veja o deploy.cmd para obter um exemplo comentado.  Garanta que esses pacotes não estão listados no requirements.txt, para impedir que o pip os instale.

Adicione o seguinte ao script de implementação:

    env\scripts\easy_install somepackage

Também poderá utilizar a instalação\_fácil para instalar a partir de um instalador .exe (alguns são compatíveis com o formato zip, pelo que são suportados pela instalação\_fácil).  Adicione o instalador ao repositório e invoque a instalação\_fácil transferindo o caminho para o executável.

Adicione o seguinte ao script de implementação:

    env\scripts\easy_install "%DEPLOYMENT_SOURCE%\installers\somepackage.exe"

### Incluir o ambiente virtual no repositório (requer o Windows)
Nota: ao utilizar esta opção, garanta que utiliza um ambiente virtual que corresponde à plataforma/arquitetura/versão que é utilizada na aplicação Web no Serviço de Aplicações do Azure (Windows/32 bits/2.7 ou 3.4).

Se incluir o ambiente virtual no repositório, pode impedir o script de implementação de fazer a gestão do ambiente virtual no Azure mediante a criação de um ficheiro vazio:

    .skipPythonDeployment

Recomendamos que elimine o ambiente virtual existente na aplicação para impedir os ficheiros restantes resultantes da gestão automática do ambiente virtual.

[Criar uma Máquina Virtual com o Windows]: http://azure.microsoft.com/documentation/articles/virtual-machines-windows-hero-tutorial/
[Microsoft Visual C++ Compiler para o Python 2.7]: http://aka.ms/vcpython27
[Microsoft Visual C++ 2010 Express]: http://go.microsoft.com/?linkid=9709949


<!--HONumber=Sep16_HO3-->


