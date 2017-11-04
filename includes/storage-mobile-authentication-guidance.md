## <a name="configure-your-application-to-access-azure-storage"></a>Configurar a sua aplicação para aceder ao armazenamento do Azure
Existem duas formas de autenticar a aplicação para aceder aos serviços de armazenamento:

* : Partilhado utilize partilhado chave para fins de teste
* Assinatura de acesso partilhada (SAS): A utilização SAS para aplicações de produção

### <a name="shared-key"></a>Chave Partilhada
Autenticação de chave partilhada significa que a aplicação irá utilizar o nome da conta e a chave de conta para aceder aos serviços de armazenamento. Para efeitos de rapidamente que mostra como utilizar esta biblioteca, vamos utilizar a autenticação de chave partilhada nesta Introdução.

> [!WARNING] 
> **Utilize apenas autenticação de chave partilhada para fins de teste!** O nome da conta e a chave de conta, que fornece acesso de leitura/escrita completo para a conta de armazenamento associada, serão distribuídos para cada pessoa que transfere a sua aplicação. Este é **não** é uma boa praticar como o risco de ter a sua chave fica comprometido por clientes não fidedignos.
> 
> 

Quando utilizar a autenticação de chave partilhada, irá criar um [cadeia de ligação](../articles/storage/common/storage-configure-connection-string.md). A cadeia de ligação é composta por:  

* O **DefaultEndpointsProtocol** -pode escolher HTTP ou HTTPS. No entanto, através de HTTPS é altamente recomendável.
* O **nome da conta** -o nome da sua conta de armazenamento
* O **chave da conta** - na [Portal do Azure](https://portal.azure.com), navegue até à sua conta de armazenamento e clique em de **chaves** ícone para encontrar estas informações.
* (Opcional) **EndpointSuffix** -isto é utilizado para serviços do storage em regiões com os sufixos de outro ponto final, tais como o Azure China ou governação do Azure.

Eis um exemplo de cadeia de ligação utilizando a autenticação de chave partilhada:

`"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"`

### <a name="shared-access-signatures-sas"></a>Assinaturas de Acesso Partilhado (SAS)
Para uma aplicação móvel, o método recomendado para autenticar um pedido por um cliente contra o serviço de armazenamento do Azure é utilizar uma assinatura de acesso partilhado (SAS). SAS permite-lhe conceder acesso de cliente a um recurso para um determinado período de tempo, com um conjunto especificado de permissões.
Como o proprietário da conta de armazenamento, terá de gerar um SAS para os seus clientes móveis consumir. Para gerar a SAS, irá provavelmente pretender escrever um serviço separado que gera o SAS seja distribuído aos seus clientes. Para fins de teste, pode utilizar o [Explorador de armazenamento do Microsoft Azure](http://storageexplorer.com) ou [Portal do Azure](https://portal.azure.com) para gerar um SAS. Quando cria a SAS, pode especificar o intervalo de tempo durante o qual a SAS é válida e as permissões que concede a SAS para o cliente.

O exemplo seguinte mostra como utilizar o Explorador de armazenamento do Microsoft Azure para gerar um SAS.

1. Se ainda não o fez, [instalar o Explorador de armazenamento do Microsoft Azure](http://storageexplorer.com)
2. Estabelecer a ligação à subscrição.
3. Clique na sua conta de armazenamento e clique no separador "Ações" na parte inferior esquerda. Clique em "Obter assinatura de acesso partilhado" para gerar uma "cadeia de ligação" para a sua SAS.
4. Eis um exemplo de uma cadeia de ligação de SAS que concede ler e escrever as permissões de serviço, o contentor e o nível de objeto para o serviço blob da conta de armazenamento.
   
   `"SharedAccessSignature=sv=2015-04-05&ss=b&srt=sco&sp=rw&se=2016-07-21T18%3A00%3A00Z&sig=3ABdLOJZosCp0o491T%2BqZGKIhafF1nlM3MzESDDD3Gg%3D;BlobEndpoint=https://youraccount.blob.core.windows.net"`

Como pode, ao utilizar um SAS, não está a expor a chave da conta na sua aplicação. Pode saber mais sobre SAS e melhores práticas para utilizar SAS verificando [assinaturas de acesso partilhado: compreender o modelo SAS](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md).

