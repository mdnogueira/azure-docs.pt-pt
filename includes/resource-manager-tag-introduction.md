O utilizador aplica as etiquetas nos recursos do Azure para as organizar por categorias. Cada etiqueta é constituída por um nome e um valor. Por exemplo, pode aplicar o nome "Ambiente" e o valor "Produção" em todos os recursos na produção. Sem esta etiqueta, poderá ter dificuldades em identificar se um recurso se destina a desenvolvimento, teste ou produção. No entanto, "Ambiente" e "Produção" são apenas exemplos. Defina os nomes e os valores que se adequam para organizar a sua subscrição.

Depois de aplicar etiquetas, pode obter todos os recursos na sua subscrição com este nome e este valor da etiqueta. As etiquetas permitem-lhe obter recursos relacionados que residem em grupos de recursos diferentes. Esta abordagem é útil quando precisa de organizar os recursos de gestão ou de faturação.

As seguintes limitações aplicam-se às etiquetas:

* Cada recurso ou grupo de recursos pode ter um máximo de 15 pares de nomes/valores de etiquetas. Esta limitação aplica-se apenas a etiquetas diretamente aplicadas ao recurso ou grupo de recursos. Um grupo de recursos pode conter muitos recursos que tenham, cada um, 15 pares de nomes/valores de etiqueta. 
* O nome de etiqueta está limitado a 512 caracteres e o valor a 256. Nas contas de armazenamento, o nome da etiqueta está limitado a 128 caracteres e o valor a 256.
* As etiquetas aplicadas ao grupo de recursos não são herdadas pelos recursos nesse grupo de recursos. 

Se tiver mais de 15 valores que têm de ser associados a um recurso, utilize uma cadeia JSON para o valor da etiqueta. A cadeia JSON pode conter muitos valores que são aplicados a um nome de etiqueta individual. Este artigo mostra um exemplo de atribuição de uma cadeia JSON à etiqueta.