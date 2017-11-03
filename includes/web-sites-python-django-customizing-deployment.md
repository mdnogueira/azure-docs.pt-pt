O Azure determinará se a aplicação utiliza o Python **se ambas estas condições forem verdadeiras**:

* ficheiro requirements.txt na pasta raiz
* qualquer ficheiro .py na pasta raiz OU um ficheiro runtime.txt a especificar o python

Quando for esse o caso, será utilizado um script da implementação específico do Python, que executa a sincronização padrão de ficheiros, bem como as operações adicionais do Python, tais como:

* Gestão automática do ambiente virtual
* Instalação de pacotes listados no ficheiro requirements.txt com pip
* Criação do web.config adequado com base na versão do Python selecionada
* Recolha de ficheiros estáticos para aplicações Django

Pode controlar determinados aspetos dos passos de implementação predefinidos sem ter de personalizar o script.

Se pretender ignorar todos os passos da implementação específicos do Python, pode criar este ficheiro vazio:

    \.skipPythonDeployment

Se pretender ignorar a recolha de ficheiros estáticos para a aplicação Django:

    \.skipDjango 

Para obter mais controlo sobre a implementação, pode substituir o script de implementação predefinido criando os seguintes ficheiros:

    \.deployment
    \deploy.cmd

Pode utilizar o [interface de linha de comandos do Azure] [ Azure command-line interface] para criar os ficheiros.  Utilize este comando na pasta do projeto:

    azure site deploymentscript --python

Se estes ficheiros não existirem, o Azure cria um script de implementação temporário e executa-o.  É idêntico ao criado com o comando acima.

[Azure command-line interface]: http://azure.microsoft.com/downloads/
