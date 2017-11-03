O script de implementação ignorará a criação do ambiente virtual no Azure se detetar que já existe um ambiente virtual compatível,  o que pode acelerar a implementação significativamente.  Os pacotes já instalados serão ignorados pelo pip.

Em determinadas situações, poderá querer forçar a eliminação desse ambiente virtual.  Deverá fazê-lo se optar por incluir um ambiente virtual como parte do seu repositório.  Poderá também querer fazê-lo se precisar eliminar determinados pacotes ou testar alterações ao ficheiro requirements.txt.

Existem algumas opções para gerir o ambiente virtual existente no Azure:

### <a name="option-1-use-ftp"></a>Opção 1: utilizar o FTP
Com um cliente FTP, ligue-se ao servidor para poder eliminar a pasta env.  Tenha em atenção que alguns clientes FTP (por exemplo, browsers) podem ser só de leitura e não lhe permitem eliminar pastas, pelo que deverá garantir que utiliza um cliente FTP com essa capacidade.  O nome de anfitrião do FTP e o utilizador são apresentados no painel da aplicação Web no [Portal do Azure](https://portal.azure.com).

### <a name="option-2-toggle-runtime"></a>Opção 2: ativar/desativar o runtime
Esta é uma alternativa que tira partido do facto de o script de implementação eliminar a pasta env quando não corresponder à versão pretendida do Python.  Deste modo, elimina de forma eficaz o ambiente existente e cria um novo.

1. Mude para uma versão diferente do Python (através do runtime.txt ou do painel **Definições da Aplicação** no Portal do Azure)
2. Emita consolidações de git de algumas alterações (ignore os erros de instalação do pip, se aplicável)
3. Regresse à versão inicial do Python
4. Emita consolidações de git de algumas alterações novamente

### <a name="option-3-customize-deployment-script"></a>Opção 3: personalizar o script de implementação
Se tiver personalizado o script de implementação, pode alterar o código no deploy.cmd para forçá-lo a eliminar a pasta env.

