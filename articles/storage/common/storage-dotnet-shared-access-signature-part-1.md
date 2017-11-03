---
title: Utilizar a partilhado assinaturas de acesso (SAS) no Storage do Azure | Microsoft Docs
description: Saiba como utilizar assinaturas de acesso partilhado (SAS) para delegar o acesso a recursos de armazenamento do Azure, incluindo ficheiros, tabelas, filas e blobs.
services: storage
documentationcenter: 
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 46fd99d7-36b3-4283-81e3-f214b29f1152
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/18/2017
ms.author: tamram
ms.openlocfilehash: 32e92e6ffc376d27297810596691f0371770e86d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="using-shared-access-signatures-sas"></a>Utilizar assinaturas de acesso partilhado (SAS)

Uma assinatura de acesso partilhado (SAS) fornece uma forma de conceder acesso limitado a objetos da sua conta do storage para outros clientes, sem a chave de conta a exposição. Neste artigo, iremos fornecer uma descrição geral do modelo de SAS, rever as melhores práticas SAS e observe alguns exemplos.

Para obter exemplos de códigos adicionais através da SAS para além daquelas aqui apresentadas, consulte [introdução ao armazenamento de Blobs do Azure no .NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/) e outros exemplos disponíveis no [exemplos de código do Azure](https://azure.microsoft.com/documentation/samples/?service=storage) biblioteca. Pode transferir as aplicações de exemplo e executá-los ou procurar o código no GitHub.

## <a name="what-is-a-shared-access-signature"></a>O que é uma assinatura de acesso partilhado?
Uma assinatura de acesso partilhado fornece acesso delegado a recursos na sua conta de armazenamento. Com uma SAS, pode conceder clientes acesso a recursos na sua conta de armazenamento sem partilha as chaves de conta. Este é o ponto de chave da utilização de assinaturas de acesso partilhado nas suas aplicações – uma SAS é uma forma segura para partilhar os seus recursos de armazenamento sem comprometer as chaves de conta.

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

Uma SAS dá-lhe um controlo granular sobre o tipo de acesso que conceder a clientes que tenham a SAS, incluindo:

* O intervalo durante o qual a SAS é válida, incluindo a hora de início e a hora de expiração.
* As permissões concedidas à SAS. Por exemplo, uma SAS para um blob poderá conceder a leitura e permissões de escrita a esse blob, mas não eliminar as permissões.
* Opcional endereço IP ou intervalo de endereços IP dos quais o armazenamento do Azure irá aceitar a SAS. Por exemplo, poderá especificar um intervalo de endereços IP que pertençam a sua organização.
* O protocolo através do qual o Storage do Azure irá aceitar a SAS. Pode utilizar este parâmetro opcional para restringir o acesso aos clientes que utilizam o HTTPS.

## <a name="when-should-you-use-a-shared-access-signature"></a>Quando deve utilizar uma assinatura de acesso partilhado?
Pode utilizar um SAS quando se pretende fornecer acesso a recursos na sua conta de armazenamento para qualquer cliente não possessing chaves de acesso da sua conta de armazenamento. A conta de armazenamento inclui tanto uma chave de acesso primária e secundária, que conceda acesso administrativo à sua conta e todos os recursos dentro da mesma. A exposição de qualquer uma destas chaves abre-se a possibilidade de utilização por acidente ou maliciosa na sua conta. Assinaturas de acesso partilhado fornecem uma alternativa segura que permite aos clientes de leitura, escrita e eliminação de dados na sua conta de armazenamento, de acordo com as permissões que tiver explicitamente concedido e sem necessidade de uma chave de conta.

Um cenário comum, onde é útil uma SAS é um serviço em que os utilizadores ler e escrever os seus próprios dados à sua conta de armazenamento. Num cenário em que uma conta do storage armazena dados de utilizador, existem dois padrões de conceção comuns:

1. Os clientes carregar e transferir dados através de um serviço de proxy de front-end que efetua a autenticação. Este serviço de proxy de front-end tem a vantagem de permitir que a validação de regras de negócio, mas para grandes quantidades de dados ou transações de volume elevado, criação de um serviço que pode ser dimensionados para corresponder a pedido pode ser difícil ou dispendiosa.

  ![Diagrama do cenário: serviço proxy de front-end](./media/storage-dotnet-shared-access-signature-part-1/sas-storage-fe-proxy-service.png)   

1. Um serviço simples autentica o cliente conforme necessário e, em seguida, gera uma SAS. Depois do cliente recebe a SAS, podem aceder a recursos da conta de armazenamento diretamente com as permissões definidas através da SAS e para o intervalo permitido através da SAS. O SAS atenua a necessidade de encaminhamento de todos os dados através do serviço de proxy de front-end.

  ![Diagrama do cenário: serviço do fornecedor da SAS](./media/storage-dotnet-shared-access-signature-part-1/sas-storage-provider-service.png)   

Muitos serviços do mundo real podem utilizar uma versão híbrida destas duas abordagens. Por exemplo, alguns dados possam ser processados e validar através do proxy de front-end, enquanto outros dados são guardados e/ou diretamente através da SAS de leitura.

Além disso, terá de utilizar uma SAS para autenticar o objeto de origem numa operação de cópia em determinados cenários:

* Quando copiar um blob para outro blob que reside numa conta do storage diferente, tem de utilizar uma SAS para autenticar o blob de origem. Opcionalmente, pode utilizar um SAS para autenticar, bem como o blob de destino.
* Quando copiar um ficheiro para outro ficheiro, que reside numa conta do storage diferente, tem de utilizar uma SAS para autenticar o ficheiro de origem. Opcionalmente, pode utilizar um SAS para autenticar o ficheiro de destino.
* Quando copiar um blob para um ficheiro ou um ficheiro para um blob, tem de utilizar uma SAS para autenticar o objeto de origem, mesmo que residem os objetos de origem e de destino dentro da mesma conta de armazenamento.

## <a name="types-of-shared-access-signatures"></a>Tipos de assinaturas de acesso partilhado
Pode criar dois tipos de assinaturas de acesso partilhado:

* **Serviço SAS.** O serviço SAS delega o acesso a um recurso em apenas um dos serviços de armazenamento: o serviço Blob, Fila, Tabela ou Ficheiro. Consulte [construir um serviço SAS](https://msdn.microsoft.com/library/dn140255.aspx) e [exemplos de SAS do serviço](https://msdn.microsoft.com/library/dn140256.aspx) para obter informações aprofundadas sobre construir o token de SAS do serviço.
* **Conta SAS.** O conta SAS delegados acesso aos recursos num ou mais dos serviços de armazenamento. Todas as operações disponíveis através de um serviço SAS também estão disponíveis através de uma conta SAS. Além disso, com a conta SAS, pode delegar o acesso às operações que se aplicam a um determinado serviço como **Get/defina as propriedades de serviço** e **obter estatísticas de serviço**. Também pode delegar o acesso às operações de leitura, escrita e eliminação em contentores de blobs, tabelas, filas e partilhas de ficheiros que não são permitidos com um serviço SAS. Consulte [construir uma conta SAS](https://msdn.microsoft.com/library/mt584140.aspx) para obter informações aprofundadas sobre construir o token SAS de conta.

## <a name="how-a-shared-access-signature-works"></a>Como funciona uma assinatura de acesso partilhado
Uma assinatura de acesso partilhado é um URI assinado que aponta para um ou mais recursos de armazenamento e inclui um token que contém um conjunto especial de parâmetros de consulta. O token indica como os recursos podem ser acedidos pelo cliente. Um dos parâmetros de consulta, a assinatura é construído a partir dos parâmetros SAS e assinado com a chave de conta. Esta assinatura é utilizada pelo armazenamento do Azure para autenticar a SAS.

Eis um exemplo de um URI de SAS, que mostra o URI do recurso e o token SAS:

![Componentes de um URI de SAS](./media/storage-dotnet-shared-access-signature-part-1/sas-storage-uri.png)   

O token SAS é uma cadeia gerar no *cliente* lado (consulte o [exemplos SAS](#sas-examples) secção para obter exemplos de código). Um token SAS que gerar com a biblioteca de clientes de armazenamento, por exemplo, não é controlado pelo armazenamento do Azure de qualquer forma. Pode criar um número ilimitado de tokens SAS do lado do cliente.

Quando um cliente fornece um URI de SAS ao Storage do Azure como parte de um pedido, o serviço verifica a assinatura para verificar se é válido para autenticar o pedido e parâmetros SAS. Se o serviço verifica se a assinatura é válida, em seguida, o pedido é autenticado. Caso contrário, o pedido é recusado com o código de erro 403 (proibido).

## <a name="shared-access-signature-parameters"></a>Parâmetros de assinatura de acesso partilhado
A conta SAS tokens SAS de serviço incluem alguns parâmetros comuns em também de ter alguns parâmetros que são diferentes.

### <a name="parameters-common-to-account-sas-and-service-sas-tokens"></a>Os parâmetros comuns a conta SAS e tokens SAS do serviço
* **Versão de API** um parâmetro opcional que especifica a versão do serviço de armazenamento a utilizar para executar o pedido.
* **Versão de Service** um parâmetro obrigatório que especifica a versão do serviço de armazenamento a utilizar para autenticar o pedido.
* **Hora de início.** Este é o tempo no qual a SAS passa a ser válida. A hora de início para uma assinatura de acesso partilhado é opcional. Se uma hora de início for omitida, o SAS tem efeito imediato. A hora de início tem de ser expresso em UTC (Hora Universal Coordenada), com um designador de UTC especial ("Z"), por exemplo `1994-11-05T13:15:30Z`.
* **Hora de expiração.** Este é o tempo após o qual a SAS já não é válida. Melhores práticas recomendado especifique uma hora de expiração para um SAS ou que associá-la a uma política de acesso armazenada. A hora de expiração tem de ser expresso em UTC (Hora Universal Coordenada), com um designador de UTC especial ("Z"), por exemplo `1994-11-05T13:15:30Z` (consulte mais abaixo).
* **Permissões.** As permissões especificadas na SAS indicam as operações que pode executar o cliente contra o recurso de armazenamento através da SAS. Diferirem permissões disponíveis para uma conta SAS e um serviço SAS.
* **IP.** Um parâmetro opcional que especifica um endereço IP ou um intervalo de endereços IP fora do Azure (consulte a secção [estado de configuração de sessão de encaminhamento](../../expressroute/expressroute-workflows.md#routing-session-configuration-state) para Express Route) partir do qual aceitar pedidos.
* **Protocolo.** Um parâmetro opcional que especifica o protocolo permitido para um pedido. Os valores possíveis são HTTPS e HTTP (`https,http`), que é apenas o valor predefinido ou HTTPS (`https`). Tenha em atenção que HTTP só não é um valor permitido.
* **Assinatura.** A assinatura é construída a partir de outros parâmetros especificados como parte de token e, em seguida, são encriptadas. É utilizado para autenticar a SAS.

### <a name="parameters-for-a-service-sas-token"></a>Parâmetros para um token SAS de serviço
* **Recursos de armazenamento.** Recursos de armazenamento para o qual pode delegar o acesso com um serviço SAS incluem:
  * Contentores e blobs
  * Partilhas de ficheiros e ficheiros
  * Filas
  * As tabelas e os intervalos de entidades de tabela.

### <a name="parameters-for-an-account-sas-token"></a>Parâmetros para um token SAS de conta
* **Serviço ou serviços.** Uma conta SAS pode delegar o acesso a uma ou mais dos serviços de armazenamento. Por exemplo, pode criar uma conta SAS que delegados aceder ao serviço Blob e o ficheiro. Ou pode criar uma SAS que delegados de acesso para todos os quatro dos serviços (Blob, fila, tabela e ficheiro).
* **Tipos de recursos de armazenamento.** Uma conta SAS aplica-se para uma ou mais classes de recursos de armazenamento, em vez de um recurso específico. Pode criar uma conta SAS para delegar o acesso ao:
  * APIs de nível de serviço, que são chamadas contra o recurso de conta de armazenamento. Os exemplos incluem **Get/defina as propriedades de serviço**, **obter estatísticas de serviço**, e **listar contentores/filas/tabelas/partilhas**.
  * APIs de ao nível do contentor, que são chamadas contra os objetos de contentor para cada serviço: blob contentores, filas, tabelas e partilhas de ficheiros. Os exemplos incluem **criar/eliminar contentor**, **criar/eliminar fila**, **criar/eliminar tabela**, **Criar/Eliminar partilha**e  **Listar os Blobs/os ficheiros e diretórios**.
  * APIs de ao nível do objeto, que são chamadas contra ficheiros, as entidades da tabela, fila de mensagens e blobs. Por exemplo, **colocar Blob**, **consulta entidade**, **obter mensagens**, e **criar ficheiro**.

## <a name="examples-of-sas-uris"></a>Exemplos de SAS URIs

### <a name="service-sas-uri-example"></a>Exemplo de URI de SAS do serviço

Eis um exemplo de um serviço URI de SAS que fornece de leitura e escrita permissões para um blob. A tabela divide cada parte de URI para compreender como contribui para a SAS:

```
https://myaccount.blob.core.windows.net/sascontainer/sasblob.txt?sv=2015-04-05&st=2015-04-29T22%3A18%3A26Z&se=2015-04-30T02%3A23%3A26Z&sr=b&sp=rw&sip=168.1.5.60-168.1.5.70&spr=https&sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D
```

| Nome | Parte da SAS | Descrição |
| --- | --- | --- |
| URI de blob |`https://myaccount.blob.core.windows.net/sascontainer/sasblob.txt` |O endereço do blob. Tenha em atenção que através de HTTPS é vivamente recomendável. |
| Versão de serviços de armazenamento |`sv=2015-04-05` |Para serviços de armazenamento versão 2012-02-12 e posterior, este parâmetro indica a versão a utilizar. |
| Hora de início |`st=2015-04-29T22%3A18%3A26Z` |Especificado na hora UTC. Se pretender que a SAS para ser válida imediatamente, omita a hora de início. |
| Hora de expiração |`se=2015-04-30T02%3A23%3A26Z` |Especificado na hora UTC. |
| Recurso |`sr=b` |O recurso é um blob. |
| Permissões |`sp=rw` |As permissões concedidas através da SAS incluem Read (r) e escrever (m). |
| Intervalo de IP |`sip=168.1.5.60-168.1.5.70` |O intervalo de endereços IP do que um pedido serão aceites. |
| Protocolo |`spr=https` |São permitidos apenas pedidos através de HTTPS. |
| Assinatura |`sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D` |Utilizado para autenticar o acesso para o blob. A assinatura é uma HMAC calculada através de uma cadeia para assinar e a chave, utilizando o algoritmo SHA256 e, em seguida, codificado com codificação Base64. |

### <a name="account-sas-uri-example"></a>Exemplo de URI de SAS de conta

Eis um exemplo de uma conta SAS que utiliza os mesmos parâmetros comuns no token. Uma vez que estes parâmetros são descritos acima, estes não são descritas aqui. Apenas os parâmetros que são específicas para a conta que SAs é descrito na tabela abaixo.

```
https://myaccount.blob.core.windows.net/?restype=service&comp=properties&sv=2015-04-05&ss=bf&srt=s&st=2015-04-29T22%3A18%3A26Z&se=2015-04-30T02%3A23%3A26Z&sr=b&sp=rw&sip=168.1.5.60-168.1.5.70&spr=https&sig=F%6GRVAZ5Cdj2Pw4tgU7IlSTkWgn7bUkkAg8P6HESXwmf%4B
```

| Nome | Parte da SAS | Descrição |
| --- | --- | --- |
| URI do recurso |`https://myaccount.blob.core.windows.net/?restype=service&comp=properties` |O Blob ponto final de serviço, com parâmetros para obter as propriedades do serviço (quando chamado com GET) ou definir as propriedades do serviço (quando chamado com definido). |
| Serviços |`ss=bf` |O SAS aplica-se aos serviços Blob e ficheiro |
| Tipos de recursos |`srt=s` |O SAS aplica-se às operações de nível de serviço. |
| Permissões |`sp=rw` |As permissões de conceder acesso para ler e operações de escrita. |

Uma vez que as permissões estão limitadas para o nível de serviço, as operações acessíveis com este SAS são **obter propriedades de serviço Blob** (leitura) e **definir propriedades de serviço Blob** (escrita). No entanto, com um URI de recursos diferente, o mesmo token SAS também pode ser utilizado para delegar o acesso às **obter estatísticas de serviço Blob** (leitura).

## <a name="controlling-a-sas-with-a-stored-access-policy"></a>Controlar um SAS com uma política de acesso armazenada
Uma assinatura de acesso partilhado pode efetuar uma das duas formas:

* **SAS ad hoc:** quando cria um SAS ad hoc, a hora de início, hora de expiração e as permissões para a SAS estão todos os especificado no URI de SAS (ou implícitas, no caso em que a hora de início for omitida). Este tipo de SAS pode ser criado como uma conta SAS ou um serviço SAS.
* **SAS com a política de acesso armazenada:** uma política de acesso armazenada está definido no contentor de recursos – um contentor de blob, tabela, fila, ou partilha – de ficheiros e pode ser utilizado para gerir restrições para um ou mais assinaturas de acesso partilhado. Quando associa um SAS com uma política de acesso armazenada, a SAS herda as restrições - a hora de início, hora de expiração e permissões – definidas para a política de acesso armazenada.

> [!NOTE]
> Atualmente, uma conta SAS tem de ser um SAS ad hoc. Armazenados acesso políticas não são suportadas ainda para a conta SAS.

A diferença entre os dois formulários é importante para um cenário de chave: revogação. Como um URI de SAS é um URL, qualquer pessoa que obtém a SAS pode utilizá-lo, independentemente de quem o criou originalmente. Se uma SAS é publicada publicamente, pode ser utilizada por qualquer pessoa do mundo. Uma SAS concede acesso a recursos a qualquer pessoa possessing-lo até uma das quatro ações acontece:

1. A hora de expiração especificada na SAS foi atingida.
2. A hora de expiração especificada na política de acesso armazenada referenciada pela SAS foi atingida (se uma política de acesso armazenada é referenciada e especifica uma hora de expiração). Isto pode ocorrer porque o intervalo decorrida ou porque tiver modificado a política de acesso armazenada com um período de tempo de expiração no passado, que é uma forma de revogar a SAS.
3. A política de acesso armazenada referenciada através da SAS é eliminada, que é outra forma de revogar a SAS. Tenha em atenção que se recrie a política de acesso armazenada com exatamente o mesmo nome, todos os tokens SAS existentes novamente será válidos de acordo com as permissões associadas essa política de acesso armazenada (partindo do princípio de que a hora de expiração a SAS não passou). Se estiver a destinar a revogar a SAS, lembre-se de que utilize um nome diferente se recrie a política de acesso com uma data de expiração futura.
4. A chave de conta que utilizou para criar a SAS é novamente gerada. Voltar a gerar uma chave de conta fará com que todos os componentes da aplicação utilizando essa chave para falhar a autenticação até que está a ser atualizadas para utilizar a chave de conta válido ou a chave de conta recentemente gerada novamente.

> [!IMPORTANT]
> Uma assinatura de acesso partilhado URI está associada a chave de conta utilizada para criar a assinatura e o associados armazenados política de acesso (se aplicável). Não se for especificada nenhuma política de acesso armazenada, é a única forma de revogar uma assinatura de acesso partilhado alterar a chave de conta.

## <a name="authenticating-from-a-client-application-with-a-sas"></a>Autenticação de uma aplicação de cliente com uma SAS
Um cliente que está na posse de um SAS pode utilizar a SAS para autenticar um pedido de uma conta de armazenamento para o qual possui as chaves de conta. Uma SAS pode ser incluída numa cadeia de ligação ou utilizada a diretamente a partir do método ou construtor adequado.

### <a name="using-a-sas-in-a-connection-string"></a>Com uma SAS numa cadeia de ligação
[!INCLUDE [storage-use-sas-in-connection-string-include](../../../includes/storage-use-sas-in-connection-string-include.md)]

### <a name="using-a-sas-in-a-constructor-or-method"></a>Utilizar um SAS num construtor ou método
Vários construtores de biblioteca de cliente de armazenamento do Azure e sobrecargas do método oferecem um parâmetro SAS, para que pode autenticar um pedido para o serviço com uma SAS.

Por exemplo, aqui um URI de SAS é utilizado para criar uma referência para um blob de bloco. O SAS fornece as credenciais apenas necessárias para o pedido. A referência de blob de bloco, em seguida, é utilizada para uma operação de escrita:

```csharp
string sasUri = "https://storagesample.blob.core.windows.net/sample-container/" +
    "sampleBlob.txt?sv=2015-07-08&sr=b&sig=39Up9JzHkxhUIhFEjEH9594DJxe7w6cIRCg0V6lCGSo%3D" +
    "&se=2016-10-18T21%3A51%3A37Z&sp=rcw";

CloudBlockBlob blob = new CloudBlockBlob(new Uri(sasUri));

// Create operation: Upload a blob with the specified name to the container.
// If the blob does not exist, it will be created. If it does exist, it will be overwritten.
try
{
    MemoryStream msWrite = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
    msWrite.Position = 0;
    using (msWrite)
    {
        await blob.UploadFromStreamAsync(msWrite);
    }

    Console.WriteLine("Create operation succeeded for SAS {0}", sasUri);
    Console.WriteLine();
}
catch (StorageException e)
{
    if (e.RequestInformation.HttpStatusCode == 403)
    {
        Console.WriteLine("Create operation failed for SAS {0}", sasUri);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }
    else
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}

```

## <a name="best-practices-when-using-sas"></a>Melhores práticas através da SAS
Quando utilizar assinaturas de acesso partilhado nas suas aplicações, tem de ter em consideração dois potenciais riscos:

* Se Ocorreu uma fuga uma SAS, pode ser utilizada por qualquer pessoa que obtém, que potencialmente pode comprometer a sua conta do storage.
* Se uma SAS fornecido para uma aplicação cliente expira e a aplicação não consegue obter um novo SAS do seu serviço, em seguida, funcionalidade da aplicação pode ser hindered.

As seguintes recomendações para a utilização de assinaturas de acesso partilhado podem ajudar a mitigar estes riscos:

1. **Utilizam sempre HTTPS** para criar ou distribuir uma SAS. Se uma SAS é transmitida através de HTTP e forem intercetada, um atacante efetuar um ataque man-in-the-middle é capaz de ler a SAS e, em seguida, utilizá-lo tal como o utilizador pretendido pode ter potencialmente comprometer a dados confidenciais ou permitindo danos nos dados pelo utilizador mal intencionado.
2. **Referência armazenadas as políticas de acesso sempre que possível.** Políticas de acesso armazenada dão-lhe a opção para revogar permissões sem ter de voltar a gerar as chaves de conta de armazenamento. Defina a expiração nestes muito distante (ou infinito) e certifique-se de que é atualizado regularmente movê-lo farther no futuro.
3. **Utilize um SAS ad hoc near-term tempos de expiração.** Desta forma, mesmo que uma SAS esteja comprometida, é válido apenas para um curto período de tempo. Esta prática é especialmente importante se não é possível referenciar uma política de acesso armazenada. Near-term tempos de expiração também limitam a quantidade de dados que podem ser gravados para um blob ao limitar o tempo disponível para carregar ao mesmo.
4. **Ter clientes renovar automaticamente a SAS, se necessário.** Os clientes devem renovar a SAS bem antes da expiração, para permitir que o tempo de novas tentativas se o serviço que fornece a SAS não está disponível. Se a SAS se destinar a ser utilizado para um pequeno número de curta duração, imediatas operações que são esperados para ser concluída dentro do período de expiração, em seguida, isto poderá ser desnecessário como a SAS não deve ser renovado. No entanto, se tiver o cliente que está a efetuar regularmente pedidos através de SAS, em seguida, a possibilidade de expiração entra play. A chave consideração é para equilibrar a necessidade de SAS ser curta duração (como anteriormente indicado) com a necessidade de se certificar de que o cliente está a pedir renovação antecipadamente suficiente (para evitar a interrupção devido a SAS expirar antes de renovação com êxito).
5. **Seja cuidadoso com a hora de início do SAS.** Se definir a hora de início para uma SAS para **agora**, em seguida, devido a relógio desfasamento (diferenças na hora atual de acordo com as diferentes máquinas), falhas poderão ser observadas ligados intermitentemente para o primeiro poucos minutos. Em geral, defina a hora de início para ter pelo menos de 15 minutos no passado. Em alternativa, não defina-o de todo, que tornará válido imediatamente em todos os casos. O mesmo geralmente aplica-se a hora de expiração também – Lembre-se de que pode observar até 15 minutos de relógio dissimetrias em qualquer direção no qualquer pedido. Para clientes com uma versão REST antes de 2012-02-12, a duração máxima para um SAS que não façam referência a uma política de acesso armazenada é 1 hora e quaisquer políticas de especificar o prazo mais do que o que irá falhar.
6. **Ser específico com o recurso para ser acedido.** É melhor prática de segurança fornecer um utilizador com os privilégios mínimos necessários. Se um utilizador necessita apenas de acesso de leitura para uma entidade única, em seguida, conceda acesso de leitura para essa entidade única e não de leitura/escrita/eliminar acesso a todas as entidades. Isto também ajuda a lessen os danos se uma SAS for comprometida, porque a SAS tem menos potência às mãos de um atacante.
7. **Tenha em atenção que a conta será faturada por qualquer utilização, incluindo feito com uma SAS.** Se fornecer o acesso de escrita para um blob, um utilizador pode optar por carregar um blob de 200GB. Se concedeu ao-los, bem como o acesso de leitura, estes podem optar por transferi-lo 10 vezes, incorrer em custos de saída em 2 TB por si. Novamente, fornece permissões limitadas para ajudar a mitigar as ações possíveis de utilizadores mal intencionados. Utilize o SAS de curta duração para reduzir esta ameaça (mas ser mindful do relógio dissimetrias na hora de fim).
8. **Valide os dados escritos através da SAS.** Quando uma aplicação cliente escreve dados para a sua conta de armazenamento, tenha em atenção que só pode haver problemas com os dados. Se a sua aplicação requer que os dados ser validados ou autorizados antes que fique pronta a utilizar, deverá efetuar esta validação depois dos dados são escritos e antes de ser utilizado pela sua aplicação. Esta prática também protege contra dados danificados ou maliciosos a ser escritos à sua conta, por um utilizador a quem adquiriu corretamente a SAS ou por um utilizador explorá uma SAS obtida ilicitamente.
9. **Não utilize sempre SAS.** Por vezes, os riscos associados uma operação específica em relação a sua conta do storage suplantam as vantagens de SAS. Para operações, crie um serviço de camada média que escreve a sua conta do storage depois de efetuar o negócio da regra de validação, a autenticação e a auditoria. Além disso, por vezes, é mais simples gerir o acesso de outras formas. Por exemplo, se pretender que todos os blobs num contentor legíveis publicamente, pode tornar o contentor público, em vez de fornecer uma SAS para todos os clientes de acesso.
10. **Utilize a análise de armazenamento para monitorizar a sua aplicação.** Pode utilizar o registo e as métricas para observar qualquer pico de pedidos de falhas de autenticação devido a uma falha no seu serviço do fornecedor SAS ou para a remoção de uma política de acesso armazenada inadvertida. Consulte o [blogue da equipa de armazenamento do Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/08/03/windows-azure-storage-logging-using-logs-to-track-storage-requests.aspx) para obter informações adicionais.

## <a name="sas-examples"></a>Exemplos SAS
Segue-se alguns exemplos de ambos os tipos de assinaturas de acesso partilhado, a conta SAS e serviço SAS.

Para executar estes exemplos c#, tem de referenciar os seguintes pacotes de NuGet no seu projeto:

* [Biblioteca de clientes do Storage do Azure para .NET](http://www.nuget.org/packages/WindowsAzure.Storage), versão 6 ou posterior (para utilizar a conta SAS).
* [Gestor de Configuração do Azure](http://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager)

Para obter exemplos adicionais que mostram como criar e testar uma SAS, consulte [exemplos de código do Azure para armazenamento](https://azure.microsoft.com/documentation/samples/?service=storage).

### <a name="example-create-and-use-an-account-sas"></a>Exemplo: Criar e utilizar uma conta SAS
Exemplo de código seguinte cria uma conta SAS que é válido para os serviços de ficheiros e de Blob e atribui o cliente permissões de leitura, escrita e permissões de lista de acesso a APIs de nível de serviço. A conta SAS restringe o protocolo para HTTPS, para que o pedido tem de ser efetuado com HTTPS.

```csharp
static string GetAccountSASToken()
{
    // To create the account SAS, you need to use your shared key credentials. Modify for your account.
    const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);

    // Create a new access policy for the account.
    SharedAccessAccountPolicy policy = new SharedAccessAccountPolicy()
        {
            Permissions = SharedAccessAccountPermissions.Read | SharedAccessAccountPermissions.Write | SharedAccessAccountPermissions.List,
            Services = SharedAccessAccountServices.Blob | SharedAccessAccountServices.File,
            ResourceTypes = SharedAccessAccountResourceTypes.Service,
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Protocols = SharedAccessProtocol.HttpsOnly
        };

    // Return the SAS token.
    return storageAccount.GetSharedAccessSignature(policy);
}
```

Para utilizar a conta SAS de acesso a APIs de nível de serviço para o serviço Blob, construir um objeto de cliente do Blob utilizando a SAS e o ponto final de armazenamento de BLOBs para a sua conta de armazenamento.

```csharp
static void UseAccountSAS(string sasToken)
{
    // Create new storage credentials using the SAS token.
    StorageCredentials accountSAS = new StorageCredentials(sasToken);
    // Use these credentials and the account name to create a Blob service client.
    CloudStorageAccount accountWithSAS = new CloudStorageAccount(accountSAS, "account-name", endpointSuffix: null, useHttps: true);
    CloudBlobClient blobClientWithSAS = accountWithSAS.CreateCloudBlobClient();

    // Now set the service properties for the Blob client created with the SAS.
    blobClientWithSAS.SetServiceProperties(new ServiceProperties()
    {
        HourMetrics = new MetricsProperties()
        {
            MetricsLevel = MetricsLevel.ServiceAndApi,
            RetentionDays = 7,
            Version = "1.0"
        },
        MinuteMetrics = new MetricsProperties()
        {
            MetricsLevel = MetricsLevel.ServiceAndApi,
            RetentionDays = 7,
            Version = "1.0"
        },
        Logging = new LoggingProperties()
        {
            LoggingOperations = LoggingOperations.All,
            RetentionDays = 14,
            Version = "1.0"
        }
    });

    // The permissions granted by the account SAS also permit you to retrieve service properties.
    ServiceProperties serviceProperties = blobClientWithSAS.GetServiceProperties();
    Console.WriteLine(serviceProperties.HourMetrics.MetricsLevel);
    Console.WriteLine(serviceProperties.HourMetrics.RetentionDays);
    Console.WriteLine(serviceProperties.HourMetrics.Version);
}
```

### <a name="example-create-a-stored-access-policy"></a>Exemplo: Criar uma política de acesso armazenada
O código seguinte cria uma política de acesso armazenado num contentor. Pode utilizar a política de acesso para especificar restrições para um serviço SAS no contentor ou os respetivos blobs.

```csharp
private static async Task CreateSharedAccessPolicyAsync(CloudBlobContainer container, string policyName)
{
    // Create a new shared access policy and define its constraints.
    // The access policy provides create, write, read, list, and delete permissions.
    SharedAccessBlobPolicy sharedPolicy = new SharedAccessBlobPolicy()
    {
        // When the start time for the SAS is omitted, the start time is assumed to be the time when the storage service receives the request.
        // Omitting the start time for a SAS that is effective immediately helps to avoid clock skew.
        SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
        Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List |
            SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Create | SharedAccessBlobPermissions.Delete
    };

    // Get the container's existing permissions.
    BlobContainerPermissions permissions = await container.GetPermissionsAsync();

    // Add the new policy to the container's permissions, and set the container's permissions.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    await container.SetPermissionsAsync(permissions);
}
```

### <a name="example-create-a-service-sas-on-a-container"></a>Exemplo: Criar um serviço SAS num contentor
O código seguinte cria um SAS num contentor. Se o nome de uma política de acesso armazenada existente não for fornecido, essa política está associada a SAS. Se nenhuma política de acesso armazenada for fornecida, o código cria um SAS ad-hoc no contentor.

```csharp
private static string GetContainerSasUri(CloudBlobContainer container, string storedPolicyName = null)
{
    string sasContainerToken;

    // If no stored policy is specified, create a new access policy and define its constraints.
    if (storedPolicyName == null)
    {
        // Note that the SharedAccessBlobPolicy class is used both to define the parameters of an ad-hoc SAS, and
        // to construct a shared access policy that is saved to the container's shared access policies.
        SharedAccessBlobPolicy adHocPolicy = new SharedAccessBlobPolicy()
        {
            // When the start time for the SAS is omitted, the start time is assumed to be the time when the storage service receives the request.
            // Omitting the start time for a SAS that is effective immediately helps to avoid clock skew.
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List
        };

        // Generate the shared access signature on the container, setting the constraints directly on the signature.
        sasContainerToken = container.GetSharedAccessSignature(adHocPolicy, null);

        Console.WriteLine("SAS for blob container (ad hoc): {0}", sasContainerToken);
        Console.WriteLine();
    }
    else
    {
        // Generate the shared access signature on the container. In this case, all of the constraints for the
        // shared access signature are specified on the stored access policy, which is provided by name.
        // It is also possible to specify some constraints on an ad-hoc SAS and others on the stored access policy.
        sasContainerToken = container.GetSharedAccessSignature(null, storedPolicyName);

        Console.WriteLine("SAS for blob container (stored access policy): {0}", sasContainerToken);
        Console.WriteLine();
    }

    // Return the URI string for the container, including the SAS token.
    return container.Uri + sasContainerToken;
}
```

### <a name="example-create-a-service-sas-on-a-blob"></a>Exemplo: Criar um serviço SAS num blob
O código seguinte cria um SAS no blob. Se o nome de uma política de acesso armazenada existente não for fornecido, essa política está associada a SAS. Se nenhuma política de acesso armazenada for fornecida, o código cria um SAS ad-hoc no blob.

```csharp
private static string GetBlobSasUri(CloudBlobContainer container, string blobName, string policyName = null)
{
    string sasBlobToken;

    // Get a reference to a blob within the container.
    // Note that the blob may not exist yet, but a SAS can still be created for it.
    CloudBlockBlob blob = container.GetBlockBlobReference(blobName);

    if (policyName == null)
    {
        // Create a new access policy and define its constraints.
        // Note that the SharedAccessBlobPolicy class is used both to define the parameters of an ad-hoc SAS, and
        // to construct a shared access policy that is saved to the container's shared access policies.
        SharedAccessBlobPolicy adHocSAS = new SharedAccessBlobPolicy()
        {
            // When the start time for the SAS is omitted, the start time is assumed to be the time when the storage service receives the request.
            // Omitting the start time for a SAS that is effective immediately helps to avoid clock skew.
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Create
        };

        // Generate the shared access signature on the blob, setting the constraints directly on the signature.
        sasBlobToken = blob.GetSharedAccessSignature(adHocSAS);

        Console.WriteLine("SAS for blob (ad hoc): {0}", sasBlobToken);
        Console.WriteLine();
    }
    else
    {
        // Generate the shared access signature on the blob. In this case, all of the constraints for the
        // shared access signature are specified on the container's stored access policy.
        sasBlobToken = blob.GetSharedAccessSignature(null, policyName);

        Console.WriteLine("SAS for blob (stored access policy): {0}", sasBlobToken);
        Console.WriteLine();
    }

    // Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```

## <a name="conclusion"></a>Conclusão
Assinaturas de acesso partilhado são úteis para fornecer permissões limitadas à sua conta de armazenamento para clientes que não devem ter a chave de conta. Como tal, são uma parte vital do modelo de segurança para qualquer aplicação utilizando o armazenamento do Azure. Se seguir as melhores práticas listadas aqui, pode utilizar a SAS para fornecer maior flexibilidade de acesso a recursos na sua conta de armazenamento, sem comprometer a segurança da sua aplicação.

## <a name="next-steps"></a>Passos Seguintes
* [Partilhado assinaturas de acesso, parte 2: Criar e utilizar um SAS com o Blob storage](../blobs/storage-dotnet-shared-access-signature-part-2.md)
* [Gerir o acesso de leitura anónimo a contentores e blobs](../blobs/storage-manage-access-to-resources.md)
* [Delegar Acesso com uma Assinatura de Acesso Partilhado](http://msdn.microsoft.com/library/azure/ee395415.aspx)
* [Introduzir tabela e fila SAS](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx)
