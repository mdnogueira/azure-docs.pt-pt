Com o Azure Resource Manager, pode definir parâmetros para os valores a utilizar ao implementar o modelo. O modelo inclui um `parameters` secção que contém todos os valores de parâmetro. Cada valor do parâmetro é utilizado pelo modelo para definir os recursos que pretende implementar.

> [!NOTE]
> Não defina parâmetros para valores que permanecem sempre iguais. Defina parâmetros apenas para valores que variam, com base no projeto que estiver a implementar ou com base no ambiente onde está a implementar.

Quando definir os parâmetros:

* Para especificar os valores permitidos que um utilizador pode fornecer durante a implementação, utilize o **allowedValues** campo.

* Para atribuir valores predefinidos para o parâmetro quando não existem valores são fornecidos durante a implementação, utilize o **defaultValue** campo. 
