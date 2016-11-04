O Azure irá determinar a versão do Python a utilizar para o ambiente virtual com a seguinte prioridade:

1. versão especificada no runtime.txt na pasta raiz
2. versão especificada pela definição Python na configuração da aplicação Web (o painel **Definições** > **Definições da Aplicação** da sua aplicação Web no Portal do Azure)
3. a predefinição é python-2.7 se nenhuma das versões acima estiver especificada

Os valores válidos para o conteúdo de 

    \runtime.txt

são:

* python-2.7
* python-3.4

Se a versão micro (terceiro dígito) estiver especificada, será ignorada.

<!--HONumber=Sep16_HO3-->


