---
title: Funcionalidades do motor de regras do Azure CDN | Microsoft Docs
description: "Documentação de referência para a CDN do Azure regras de funcionalidades e as condições de correspondência do motor."
services: cdn
documentationcenter: 
author: Lichard
manager: akucer
editor: 
ms.assetid: 669ef140-a6dd-4b62-9b9d-3f375a14215e
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: rli
ms.openlocfilehash: 107601fcc53e5f5b6f809bb3c7fceaf5e5c03d36
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2017
---
# <a name="azure-cdn-rules-engine-features"></a>Funcionalidades do motor de regras CDN do Azure
Este tópico apresenta uma lista de descrições detalhadas das funcionalidades disponíveis para a rede de entrega de conteúdos (CDN) do Azure [motor de regras](cdn-rules-engine.md).

O terceiro parte de uma regra é a funcionalidade. Uma funcionalidade define o tipo de ação que será aplicada ao tipo de pedido identificado por um conjunto de condições de correspondência.

## <a name="access"></a>Access

Estas funcionalidades são concebidas para controlar o acesso ao conteúdo.


Nome | Objetivo
-----|--------
Negar o acesso | Determina se a todos os pedidos são rejeitados com resposta 403 Proibido.
Autenticação de token | Determina se a autenticação baseada em tokens será aplicada a um pedido.
Código de recusa de token de autenticação | Determina o tipo de resposta que vai ser devolvido para um utilizador quando um pedido é negado devido a autenticação baseada em tokens.
Autenticação de token ignorar URL maiúsculas / minúsculas | Determina se será sensível comparações de URL, efetuadas pela autenticação baseada em tokens.
Parâmetro de autenticação de token | Determina se o parâmetro de cadeia de consulta baseado em tokens de autenticação deve ser alterado.

### <a name="deny-access"></a>Negar o acesso
**Objetivo**: determina se a todos os pedidos são rejeitados com resposta 403 Proibido.

Valor | Resultado
------|-------
Ativado| Faz com que todos os pedidos que satisfaçam os critérios de correspondência para ser rejeitada com resposta 403 Proibido.
Desativado| Restaura o comportamento predefinido. O comportamento predefinido é permitir que o servidor de origem para determinar o tipo de resposta que vai ser devolvido.

**Comportamento de predefinido**: desativado

> [!TIP]
   > Uma utilização possíveis para esta funcionalidade é a associar a uma condição de correspondência de cabeçalho de pedido para bloquear o acesso ao referrers HTTP que estão a utilizar ligações inline ao seu conteúdo.

### <a name="token-auth"></a>Autenticação de token
**Objetivo:** determina se a autenticação baseada em tokens será aplicada a um pedido.

Se a autenticação baseada em tokens está ativada, apenas pedidos que fornecem um token de encriptados e está em conformidade com os requisitos especificados por esse token serão cumpridos.

A chave de encriptação que será utilizada para encriptar e desencriptar valores token é determinada pela chave primária e as opções de chave de cópia de segurança na página de autenticação de Token. Tenha em atenção que as chaves de encriptação são específicas da plataforma.

Valor | Resultado
------|---------
Ativado | Protege os conteúdos solicitados com a autenticação baseada em tokens. Apenas os pedidos de clientes que fornecem um token válido e cumpram os requisitos serão cumpridos. Transações de FTP são excluídas da autenticação baseada em tokens.
Desativado| Restaura o comportamento predefinido. O comportamento predefinido é permitir que a configuração da autenticação baseada em tokens para determinar se um pedido esteja protegido.

**Comportamento predefinido:** desativado.

###<a name="token-auth-denial-code"></a>Código de recusa de token de autenticação
**Objetivo:** determina o tipo de resposta que vai ser devolvido para um utilizador quando um pedido é negado devido a autenticação baseada em tokens.

Os códigos de resposta disponíveis são listados abaixo.

Código de Resposta|Nome de resposta|Descrição
----------------|-----------|--------
301|Movida permanentemente|Este código de estado redireciona os utilizadores não autorizados para o URL especificado no cabeçalho de localização.
302|Encontrado|Este código de estado redireciona os utilizadores não autorizados para o URL especificado no cabeçalho de localização. Este código de estado é o método de padrão da indústria da execução de um redirecionamento.
307|Redirecionamento temporário|Este código de estado redireciona os utilizadores não autorizados para o URL especificado no cabeçalho de localização.
401|Não autorizado|Combinar este código de estado com o cabeçalho de resposta WWW-Authenticate permite-lhe solicitar um utilizador para autenticação.
403|Proibido|Esta é a mensagem de estado de proibido de 403 padrão que um utilizador não autorizado ao tentar aceder a conteúdo protegido.
404|Ficheiro Não Encontrado|Este código de estado indica que o cliente HTTP foi capaz de comunicar com o servidor, mas o conteúdo solicitado não foi encontrado.

#### <a name="url-redirection"></a>URL de redirecionamento

Esta funcionalidade suporta o redirecionamento de URL para um URL definido pelo utilizador quando está configurado para devolver um código de estado 3xx. Este URL definido pelo utilizador pode ser especificado, efetuando os seguintes passos:

1. Selecione um código de resposta 3xx para a funcionalidade de código de recusa do Token Auth.
2. Selecione a opção de nome de cabeçalho opcionais "Localização".
3. Defina a opção de valor de cabeçalho opcional para o URL pretendido.

Se um URL não está definido para um código de estado 3xx, em seguida, a página de resposta padrão para um código de estado 3xx será devolvida ao utilizador.

Redirecionamento de URL só é aplicável para 3xx códigos de resposta.

A opção de valor de cabeçalho opcional suporta carateres alfanuméricos, aspas e espaços.

#### <a name="authentication"></a>Autenticação

Esta funcionalidade suporta a capacidade de incluir o cabeçalho WWW-Authenticate quando a responder a um pedido não autorizado para conteúdo protegido pela autenticação baseada em tokens. Se o cabeçalho WWW-Authenticate tiver sido definido para "básico" na configuração, será solicitado o utilizador não autorizado para as credenciais da conta.

A configuração de acima pode ser alcançada, efetuando os seguintes passos:

1. Selecione "401" como o código de resposta para a funcionalidade de código de recusa do Token Auth.
2. Selecione "WWW-Authenticate" a opção de nome de cabeçalho opcionais.
3. Defina a opção de valor de cabeçalho opcionais para "básico".

O cabeçalho WWW-Authenticate só é aplicável para 401 códigos de resposta.

### <a name="token-auth-ignore-url-case"></a>Autenticação de token ignorar URL maiúsculas / minúsculas
**Objetivo:** determina se comparações de URL, efetuadas pela autenticação baseada em tokens vai estar em maiúsculas e minúsculas.

Os parâmetros afetados por esta funcionalidade são:

- ec_url_allow
- ec_ref_allow
- ec_ref_deny

Os valores válidos são:

Valor|Resultado
---|----
Ativado|Faz com que o nosso servidor edge Ignorar maiúsculas / minúsculas ao comparar os URLs para os parâmetros de autenticação baseada em tokens.
Desativado|Restaura o comportamento predefinido. O comportamento predefinido é de comparações de URL para a autenticação de Token estar maiúsculas e minúsculas.

**Comportamento predefinido:** desativado.
 
### <a name="token-auth-parameter"></a>Parâmetro de autenticação de token
**Objetivo:** determina se o parâmetro de cadeia de consulta baseado em tokens de autenticação deve ser alterado.

Informações da chave:

- A opção de valor define o nome de parâmetro de cadeia de consulta através da qual pode ser especificado um token.
- A opção de valor não pode ser definida como "ec_token."
- Certifique-se de que o nome definido na opção valor contém apenas caracteres válidos de URL.

Valor|Resultado
----|----
Ativado|A opção de valor define o nome de parâmetro de cadeia de consulta através do qual os tokens devem ser definidos.
Desativado|Um token pode ser especificado como um parâmetro de cadeia de consulta não definido no URL do pedido.

**Comportamento predefinido:** desativado. Um token pode ser especificado como um parâmetro de cadeia de consulta não definido no URL do pedido.

## <a name="caching"></a>Colocação em cache

Estas funcionalidades são concebidas para personalizar quando e como conteúdo é colocado em cache.

Nome | Objetivo
-----|--------
Parâmetros de largura de banda | Determina se os parâmetros (por exemplo, ec_rate e ec_prebuf) de limitação de largura de banda estará ativa.
Limitação de largura de banda | Acelera a largura de banda para a resposta fornecida pelos nossos servidores edge.
Ignorar a Cache | Determina se o pedido pode tirar partido da tecnologia colocação em cache.
Tratamento de cabeçalho de Cache-Control | Controla a geração dos cabeçalhos Cache-Control pelo servidor edge, quando a funcionalidade de idade de máxima externo está ativa.
Cadeia de consulta de chave da cache | Determina se a chave de cache irá incluir ou excluir os parâmetros de cadeia de consulta associados a um pedido.
Chave da cache reescrever | Reescreve chave da cache associado a um pedido.
Concluir o preenchimento da Cache | Determina o que acontece quando um resultados de pedido numa falha de acerto na cache parciais num servidor edge.
Comprima os tipos de ficheiro | Define os formatos de ficheiro que irão ser comprimidos no servidor.
Idade de máx. de interno predefinida | Determina o intervalo de idade máxima de predefinido para o servidor edge a revalidação de cache do servidor de origem.
Tratamento de cabeçalho de expirar | Controla a geração dos cabeçalhos de expira por um servidor edge, quando a funcionalidade de idade de máxima externo está ativa.
Externa de atribuição de idade máxima | Determina o intervalo de idade máxima de browser de modo a revalidação de cache do servidor edge.
Forçar interna de atribuição de idade máxima | Determina o intervalo de idade máxima para o servidor edge revalidação de cache do servidor de origem.
Suporte de 264 (transferência progressiva HTTP) | Determina os tipos de formatos de ficheiro 264 que podem ser utilizados para transmitir o conteúdo.
Pedido de Cache não honor | Determina se não-pedidos um cliente HTTP serão reencaminhados para o servidor de origem.
Ignorar a Cache não de origem | Determina se o nosso CDN ignorará determinadas diretivas servidas a partir de um servidor de origem.
Ignorar intervalos Unsatisfiable | Determina a resposta que será devolvida aos clientes quando um pedido gera um código de estado 416 pedido intervalo não satisfatório.
Interna obsoleta máx. | Controlos quanto posterior à hora de expiração normal um recurso em cache pode ser fornecido de um servidor edge quando o servidor edge não consegue revalidate o elemento em cache com o servidor de origem.
Partilha de Cache parciais | Determina se um pedido pode gerar o conteúdo parcialmente em cache.
Prevalidate conteúdos em cache | Determina se o conteúdo em cache vai ser elegível para revalidação antecipada antes do valor de TTL expire.
Atualizar os ficheiros de Cache de Zero bytes | Determina o modo como os pedidos de um cliente HTTP para um elemento de cache de 0 bytes é processado pelos nossos servidores edge.
Códigos de estado colocáveis de conjunto | Define o conjunto de códigos de estado que podem resultar num conteúdos em cache.
Entrega de conteúdos obsoleta com o erro | Determina se expirado os conteúdos em cache serão entregues quando ocorre um erro durante a revalidação de cache ou ao obter os conteúdos solicitados a partir do servidor de origem do cliente.
Obsoleta ao Revalidate | Melhora o desempenho ao permitir que os nossos servidores edge servir de cliente obsoleto para o autor do pedido, enquanto a revalidação ocorre.
Comentar | A funcionalidade de comentário permite uma nota a adicionar dentro de uma regra.

###<a name="bandwidth-parameters"></a>Parâmetros de largura de banda
**Objetivo:** determina se os parâmetros (por exemplo, ec_rate e ec_prebuf) de limitação de largura de banda estará ativa.

Parâmetros de limitação de largura de banda determinam se a velocidade de transferência de dados para o pedido do cliente será limitada a uma taxa personalizada.

Valor|Resultado
--|--
Ativado|Permite que os nossos servidores edge honrar pedidos de limitação de largura de banda.
Desativado|Faz com que o nosso servidores edge ignorar os parâmetros de limitação de largura de banda. Os conteúdos solicitados serão servido normalmente (ou seja, sem limitação de largura de banda).

**Comportamento predefinido:** ativada.

###<a name="bandwidth-throttling"></a>Limitação de largura de banda
**Objetivo:** acelera a largura de banda para a resposta fornecida pelos nossos servidores edge.

Ambas as opções seguintes tem de ser definidas para configurar corretamente a limitação de largura de banda.

Opção|Descrição
--|--
KBytes por segundo|Defina esta opção para a largura de banda máxima (Kb por segundo) que pode ser utilizada para fornecer a resposta.
Segundos de Prebuf|Defina esta opção para o número de segundos que os nossos servidores edge aguarda até que a limitação de largura de banda. É o objetivo deste período de tempo de largura de banda sem restrições impedir que um leitor de suporte de dados com problemas de memória intermédia de stuttering ou devido à limitação de largura de banda.

**Comportamento predefinido:** desativado.

###<a name="bypass-cache"></a>Ignorar a Cache
**Objetivo:** determina se o pedido pode tirar partido da tecnologia colocação em cache.

Valor|Resultado
--|--
Ativado|Faz com que todos os pedidos para passar para o servidor de origem, mesmo que o conteúdo foi colocado em cache anteriormente no servidores edge.
Desativado|Faz com que servidores edge aos recursos da cache, de acordo com a política de cache definido no respetivos cabeçalhos de resposta.

**Comportamento predefinido:**

- **HTTP grande:** desativado

<!---
- **ADN:** Enabled
--->

###<a name="cache-control-header-treatment"></a>Tratamento de cabeçalho do controlo de cache
**Objetivo:** controla a geração dos cabeçalhos Cache-Control pelo servidor edge quando externos de idade máxima de funcionalidade está ativa.

A forma mais fácil para alcançar este tipo de configuração é colocar a idade máxima externos e as funcionalidades de Cache-Control de tratamento de cabeçalho na mesma instrução.

Valor|Resultado
--|--
Substituir|Garante que as seguintes ações irão ocorrer:<br/> -Substitui o cabeçalho de Cache-Control gerado pelo servidor de origem. <br/>-Adiciona o cabeçalho de Cache-Control produzido pela funcionalidade de idade de máxima externo para a resposta.
Pass Through|Garante que o cabeçalho de Cache-Control produzido pela funcionalidade de idade de máxima externo nunca é adicionado à resposta. <br/> Se o servidor de origem produz um cabeçalho de Cache-Control, este irá percorrer para o utilizador final. <br/> Se o servidor de origem não produz um cabeçalho de Cache-Control, em seguida, esta opção pode fazer com que o cabeçalho de resposta que não contém um cabeçalho de Cache-Control.
Adicionar se estiverem em falta|Se o servidor de origem não foi recebido um cabeçalho de Cache-Control, esta opção adiciona o cabeçalho de Cache-Control produzido pela funcionalidade de idade de máxima externo. Esta opção é útil para assegurar que todos os elementos serão atribuídos um cabeçalho de Cache-Control.
Remover| Esta opção garante que um cabeçalho de Cache-Control não está incluído com a resposta de cabeçalho. Se já tiver sido atribuído um cabeçalho de Cache-Control, em seguida, irá ser eliminado da resposta de cabeçalho.

**Comportamento predefinido:** substituir.

###<a name="cache-key-query-string"></a>Cadeia de consulta de chave da cache
**Objetivo:** determina se a chave de cache irá incluir ou excluir os parâmetros de cadeia de consulta associados a um pedido.

Informações da chave:

- Especifique um ou mais nomes de parâmetro de cadeia de consulta. Cada nome de parâmetro deve ser delimitado com um único espaço.
- Esta funcionalidade determina se os parâmetros de cadeia de consulta irão ser incluídos ou excluídos da chave da cache. Para cada opção abaixo, são fornecidas informações adicionais.

Tipo|Descrição
--|--
 Incluir|  Indica se cada parâmetro especificado deve ser incluído na chave de cache. Uma chave exclusiva de cache será gerada para cada pedido que contém um valor exclusivo para um parâmetro de cadeia de consulta definido nesta funcionalidade. 
 Incluir todos os  |Indica que será criada uma chave de cache exclusiva para cada pedido para um recurso que inclui uma cadeia de consulta exclusivo. Este tipo de configuração não é normalmente recomendado, uma vez que poderá provocar numa pequena percentagem de acertos na cache. Isto irá aumentar a carga no servidor de origem, uma vez que terá que processar mais pedidos. Esta configuração duplica o comportamento de colocação em cache conhecido como "exclusivo-cache" na página de colocação em cache de cadeia de consulta. 
 Excluir | Indica que apenas os parâmetros especificados serão excluídos da chave da cache. Todos os outros parâmetros de cadeia de consulta serão incluídos na chave da cache. 
 Excluir todos os  |Indica que todos os parâmetros de cadeia de consulta serão excluídos da chave da cache. Esta configuração duplica o predefinido comportamento, que é conhecido como "standard cache" na página de colocação em cache de cadeia de consulta a colocação em cache. 

Potência de motor de regras de HTTP permite-lhe personalizar a forma no qual está implementada o colocação em cache de cadeia de consulta. Por exemplo, pode especificar que a consulta colocação em cache apenas ser efetuada em determinadas localizações ou tipos de ficheiro.

Se gostaria de duplicar a cadeia de consulta comportamento conhecido como "não-cache" na página de colocação em cache de cadeia de consulta a colocação em cache, terá de criar uma regra que contém uma condição de correspondência de carateres universais de consulta de URL e uma funcionalidade de ignorar a Cache. A condição de correspondência de consulta de URL com carateres universais deve ser definida como um asterisco (*).

#### <a name="sample-scenarios"></a>Cenários de exemplo

A utilização de exemplo seguinte para esta funcionalidade fornece um exemplo de pedido e a chave de cache predefinida:

- **Exemplo de pedido:** http://wpc.0001.&lt; Domínio&gt;/800001/Origin/folder/asset.htm?sessionid=1234 e idioma = EN & userid = 01
- **Predefinido da chave de cache:** /800001/Origin/folder/asset.htm

##### <a name="include"></a>Incluir

Configuração de exemplo:

- **Tipo:** incluem
- **Parâmetro (s):** idioma

Este tipo de configuração irá gerar a seguinte cadeia parâmetro cache-chave de consulta:

    /800001/Origin/folder/asset.htm?language=EN

##### <a name="include-all"></a>Incluir todos os

Configuração de exemplo:

- **Tipo:** incluir todos os

Este tipo de configuração irá gerar a seguinte cadeia parâmetro cache-chave de consulta:

    /800001/Origin/folder/asset.htm?sessionid=1234&language=EN&userid=01

##### <a name="exclude"></a>Excluir

Configuração de exemplo:

- **Tipo:** excluir
- **Parâmetro (s):** sessionid userid

Este tipo de configuração irá gerar a seguinte cadeia parâmetro cache-chave de consulta:

    /800001/Origin/folder/asset.htm?language=EN

##### <a name="exclude-all"></a>Excluir todos os

Configuração de exemplo:

- **Tipo:** excluir todos os

Este tipo de configuração irá gerar a seguinte cadeia parâmetro cache-chave de consulta:

    /800001/Origin/folder/asset.htm

###<a name="cache-key-rewrite"></a>Chave da cache reescrever
**Objetivo:** reescreve chave da cache associado a um pedido.

Uma chave de cache é o caminho relativo que identifique um recurso para efeitos de colocação em cache. Por outras palavras, os nossos servidores irão verificar para uma versão em cache de um recurso, de acordo com o caminho como definido pela respetiva chave de cache.

Configure esta funcionalidade, definindo ambas as opções seguintes:

Opção|Descrição
--|--
Caminho original| Defina o caminho relativo para os tipos de pedidos cuja chave de cache irá ser foi reescrita. Pode ser definido um caminho relativo ao selecionar um caminho de base de origem e, em seguida, definir um padrão de expressão regular.
Novo caminho|Defina o caminho relativo para a nova chave de cache. Pode ser definido um caminho relativo ao selecionar um caminho de base de origem e, em seguida, definir um padrão de expressão regular. Este caminho relativo que pode ser construído dinamicamente através da utilização de variáveis HTTP
**Comportamento predefinido:** -chave um pedido da cache é determinado pelo URI do pedido.

###<a name="complete-cache-fill"></a>Concluir o preenchimento da Cache
**Objetivo:** determina o que acontece quando um pedido resulta numa falha de acerto na cache parciais num servidor edge.

Uma falha de acerto na cache parciais descreve o estado de cache para um recurso que não foi completamente transferido para um servidor edge. Se um recurso é colocado em cache apenas parcialmente num servidor edge, em seguida, o pedido seguinte para esse recurso será reencaminhado novamente para o servidor de origem.
<!---
This feature is not available for the ADN platform. The typical traffic on this platform consists of relatively small assets. The size of the assets served through these platforms helps mitigate the effects of partial cache misses, since the next request will typically result in the asset being cached on that POP.
--->
Uma falha de acerto na cache parcial ocorre normalmente depois de um utilizador cancela uma transferência ou para recursos de que apenas são pedidos através de pedidos de intervalo HTTP. Esta funcionalidade é mais útil para grandes ativos onde os utilizadores serão não normalmente transferi-los do início ao fim (por exemplo, vídeos). Como consequência, esta funcionalidade está ativada por predefinição na plataforma grande de HTTP. Está desativada em todas as outras plataformas.

Recomenda-se para deixar a configuração predefinida para a plataforma de HTTP grande, uma vez que irá reduzir a carga no seu servidor de origem do cliente e aumentar a velocidade a que os seus clientes transferem o conteúdo.

Devido da forma na cache de que as definições são registadas, esta funcionalidade não pode ser associada com as seguintes condições de correspondência: Edge Cname, Literal de cabeçalho de pedido, universal do cabeçalho de pedido, o Literal de consulta do URL e consulta de URL com carateres universais.

Valor|Resultado
--|--
Ativado|Restaura o comportamento predefinido. É o comportamento predefinido para forçar o servidor edge para iniciar a obtenção um fundo do elemento do servidor de origem. Após o qual, o elemento estarão disponíveis na cache local do servidor edge.
Desativado|Impede que um servidor edge efetuar uma obtenção de fundo para o elemento. Isto significa que o pedido seguinte para esse recurso a que região fará com que um servidor edge para solicitá-la a partir do servidor de origem do cliente.

**Comportamento predefinido:** ativada.

###<a name="compress-file-types"></a>Comprima os tipos de ficheiro
**Objetivo:** define os formatos de ficheiro que irão ser comprimidos no servidor.

Um formato de ficheiro pode ser especificado utilizando o respetivo tipo de suporte de Internet (por exemplo, Content-Type). Tipo de suporte de Internet é metadados independentes da plataforma que permite que os nossos servidores identificar o formato de ficheiro de um recurso específico. É fornecida uma lista dos tipos de suportes de dados de Internet comuns abaixo.

Tipo de suporte de Internet|Descrição
--|--
texto/plain|Ficheiros de texto simples
texto/html| Ficheiros HTML
texto/css|Folhas de estilo em cascata (CSS)
Application/x-javascript|Javascript
aplicação/javascript|Javascript
Informações da chave:

- Especifique vários tipos de suportes de dados de Internet por delimiting cada um com um único espaço. 
- Esta funcionalidade só irá comprimir ativos cujo tamanho é inferior a 1 MB. Maior ativos não serão comprimidos pelos nossos servidores.
- Determinados tipos de conteúdos, tais como imagens, vídeos e os recursos de suporte de dados de áudio (por exemplo, JPG, MP3, MP4, etc.), já são comprimidos. Compressão adicional nestes tipos de recursos não irá reduzir significativamente o tamanho de ficheiro. Por conseguinte, é recomendado que não ativar a compressão nestes tipos de recursos.
- Carateres universais, tais como a série de asteriscos, não são suportadas.
- Antes de adicionar esta funcionalidade a uma regra, certifique-se de que defina a opção de compressão desativado na página de compressão para a plataforma para os quais será aplicada esta regra.

###<a name="default-internal-max-age"></a>Idade de máx. de interno predefinida
**Objetivo:** determina o intervalo de idade máxima de predefinido para o servidor edge a revalidação de cache do servidor de origem. Por outras palavras, a quantidade de tempo que irá passar antes de um servidor edge irá verificar se um recurso em cache corresponde ao elemento armazenado no servidor de origem.

Informações da chave:

- Esta ação só ocorrerá de respostas de um servidor de origem que não foi possível atribuir uma indicação de idade máxima no cabeçalho de Cache-Control ou Expires.
- Esta ação não irá demorar local para recursos que não são considerados colocáveis.
- Esta ação não afeta o browser para revalidations de cache do servidor edge. Estes tipos de revalidations são determinados pela Cache-Control ou cabeçalhos expira enviados para o browser, que pode ser personalizado com a funcionalidade de idade de máxima externo.
- Os resultados desta ação não tem um efeito observable nos cabeçalhos de resposta e o conteúdo devolvido de servidores edge para o conteúdo, mas pode ter um efeito na quantidade de tráfego de revalidação enviado a partir de servidores edge ao seu servidor de origem.
- Configure esta funcionalidade por:
    - Selecionar o código de estado para os quais pode ser aplicada a idade interno-máxima uma predefinição.
    - Especificar um valor inteiro e, em seguida, selecionar a unidade de tempo pretendido (por exemplo, segundos, minutos, horas, etc.). Este valor define o intervalo de idade máxima interno predefinido.

- Definir a unidade de tempo "Desligado" atribuirá um intervalo de idade máxima interno predefinido de 7 dias para pedidos que não tenham sido atribuídos uma indicação de idade máxima no respetivo cabeçalho Cache-Control ou Expires.
- Devido da forma na cache de que as definições são registadas, esta funcionalidade não pode ser associada com as seguintes condições de correspondência: 
    - Limite 
    - CNAME
    - Literal de cabeçalho de pedido
    - Caráter universal de cabeçalho de pedido
    - Método de pedido
    - Literal de consulta de URL
    - Consulta de URL com carateres universais

**Valor predefinido:** 7 dias

###<a name="expires-header-treatment"></a>Tratamento de cabeçalho de expirar
**Objetivo:** controla a geração dos cabeçalhos de expira por um servidor edge, quando a funcionalidade de idade de máxima externo está ativa.

A forma mais fácil para alcançar este tipo de configuração é colocar a idade máxima externos e as funcionalidades de expira tratamento de cabeçalho na mesma instrução.

Valor|Resultado
--|--
Substituir|Garante que as seguintes ações irão ocorrer:<br/>-Substitui o cabeçalho de expira gerado pelo servidor de origem.<br/>-Adiciona o cabeçalho de expira produzido pela funcionalidade de idade de máxima externo para a resposta.
Pass Through|Garante que o cabeçalho de expira produzido pela funcionalidade de idade de máxima externo nunca é adicionado à resposta. <br/> Se o servidor de origem produz um cabeçalho de expira, este irá percorrer para o utilizador final. <br/>Se o servidor de origem não produz um cabeçalho de expira, em seguida, esta opção pode fazer com que o cabeçalho de resposta que não contém um cabeçalho de expira.
Adicionar se estiverem em falta| Se o servidor de origem não foi recebido um cabeçalho de expira, esta opção adiciona o cabeçalho de expira produzido pela funcionalidade de idade de máxima externo. Esta opção é útil para assegurar que todos os elementos serão atribuídos um cabeçalho de expira.
Remover| Garante que um cabeçalho de expira não está incluído com a resposta de cabeçalho. Se já tiver sido atribuído um cabeçalho de expira, em seguida, este irá ser eliminado da resposta de cabeçalho.

**Comportamento predefinido:** substituir

###<a name="external-max-age"></a>Externa de atribuição de idade máxima
**Objetivo:** determina o intervalo de idade máxima de browser de modo a revalidação de cache do servidor edge. Por outras palavras, pode verificar a quantidade de tempo que irá passar antes de um browser para uma nova versão de um ativo de um servidor edge.

A ativação desta funcionalidade irá gerar Cache-controlo: max-idade e expira cabeçalhos a partir de nossos servidores edge e enviá-los para o cliente HTTP. Por predefinição, estes cabeçalhos substituirá as criadas pelo servidor de origem. No entanto, o tratamento de cabeçalho de Cache-Control e as funcionalidades de tratamento de cabeçalho expira podem ser utilizadas para alterar este comportamento.

Informações da chave:

- Esta ação não afeta o servidor edge para revalidations de cache do servidor de origem. Estes tipos de revalidations são determinados por cabeçalhos Cache-controlo/expira recebidos do servidor de origem e podem ser personalizados com a predefinição interna-idade máxima e as funcionalidades de idade de máxima de interno Force.
- Configure esta funcionalidade, especificando um valor inteiro e selecionar a unidade de tempo pretendido (por exemplo, segundos, minutos, horas, etc.).
- A definição desta funcionalidade para um valor negativo faz com que os nossos servidores edge enviar uma Cache-controlo: não-cache e um período de tempo expira que está definido no passado com cada resposta para o browser. Apesar de um cliente HTTP irá coloca a resposta, esta definição não irá afetar a capacidade dos nossos servidores edge coloca a resposta do servidor de origem.
- Definir a unidade de tempo "Desligado" irá desativar esta funcionalidade. Os cabeçalhos Cache-controlo/expira em cache com a resposta do servidor de origem passará para o browser.

**Comportamento predefinido:** desativado

###<a name="force-internal-max-age"></a>Forçar interna de atribuição de idade máxima
**Objetivo:** determina o intervalo de idade máxima para o servidor edge revalidação de cache do servidor de origem. Por outras palavras, a quantidade de tempo que irá passar antes de um servidor edge, pode verificar se um recurso em cache corresponde ao elemento armazenado no servidor de origem.

Informações da chave:

- Esta funcionalidade irá substituir o intervalo de idade máxima definido na Cache-Control ou Expires os cabeçalhos de gerado a partir de um servidor de origem.
- Esta funcionalidade não afeta o browser para revalidations de cache do servidor edge. Estes tipos de revalidations são determinados pelo Cache-Control ou Expires os cabeçalhos de enviada para o browser.
- Esta funcionalidade não tem um efeito observable na resposta fornecida por um servidor edge para o autor do pedido. No entanto, pode ter um efeito na quantidade de tráfego de revalidação enviado do nosso servidores edge para o servidor de origem.
- Configure esta funcionalidade por:
    - Selecionar o código de estado para os quais será aplicada uma idade máxima interna.
    - Especificar um valor inteiro e selecionar a unidade de tempo pretendido (por exemplo, segundos, minutos, horas, etc.). Este valor define o intervalo de idade máxima do pedido.

- Definir a unidade de tempo "Desligado" desativa esta funcionalidade. Um intervalo de idade máxima interno não será atribuído aos recursos de pedido. Se o cabeçalho original não contém instruções de colocação em cache, em seguida, o elemento irá ser colocados em cache, de acordo com a definição de Active Directory na funcionalidade de predefinida interno-idade máxima.
- Devido da forma na cache de que as definições são registadas, esta funcionalidade não pode ser associada com as seguintes condições de correspondência: 
    - Limite 
    - CNAME
    - Literal de cabeçalho de pedido
    - Caráter universal de cabeçalho de pedido
    - Método de pedido
    - Literal de consulta de URL
    - Consulta de URL com carateres universais

**Comportamento predefinido:** desativado

###<a name="h264-support-http-progressive-download"></a>Suporte de 264 (transferência progressiva HTTP)
**Objetivo:** determina os tipos de formatos de ficheiro 264 que podem ser utilizados para transmitir o conteúdo.

Informações da chave:

- Defina um conjunto delimitada por espaços de extensões de nome de ficheiro permitidas 264 na opção de extensões de ficheiro. A opção de extensões de ficheiro irá substituir o comportamento predefinido. Manter MP4 e F4V suporte, incluindo as extensões de nome de ficheiro quando definir esta opção. 
- Não se esqueça de incluir um período ao especificar cada extensão de nome de ficheiro (por exemplo, mp4 .f4v).

**Comportamento predefinido:** HTTP transferência progressiva transferir suporta MP4 e F4V suporte de dados por predefinição.

###<a name="honor-no-cache-request"></a>Pedido de cache não honor
**Objetivo:** determina se um cliente HTTP's não cache serão reencaminhados pedidos para o servidor de origem.

Um pedido de cache não ocorre quando o cliente HTTP envia uma Cache-controlo: não-cache e/ou Pragma:no-cabeçalho de cache do pedido de HTTP.

Valor|Resultado
--|--
Ativado|Permite que os pedidos de cache não de um cliente HTTP para ser reencaminhado para o servidor de origem e o servidor de origem irá devolver os cabeçalhos de resposta e o corpo através do servidor edge novamente para o cliente HTTP.
Desativado|Restaura o comportamento predefinido. O comportamento predefinido é para impedir que pedidos na cache não reencaminhados para o servidor de origem.

Para todo o tráfego de produção, recomenda vivamente para deixar esta funcionalidade no respectivo estado predefinido desativado. Caso contrário, os servidores de origem não serão possível proteger os utilizadores finais que inadvertidamente poderão acionar muitos pedidos não-cache ao atualizar as páginas web ou a partir de muitos jogadores popular do suporte de dados que são codificados para enviar um cabeçalho de cache não com cada pedido de vídeo. Contudo, esta funcionalidade pode ser útil para aplicar a determinados não a produção de teste ou testes diretórios, para permitir que o novo conteúdo ser solicitados a pedido do servidor de origem.

O estado da cache que será reportado para um pedido que tem permissão para ser reencaminhados para um servidor de origem devido a esta funcionalidade é TCP_Client_Refresh_Miss. O relatório de Estados de Cache, o que está disponível do núcleo do módulo de relatórios, fornece informações estatísticas por Estado da cache. Isto permite-lhe controlar o número e a percentagem de pedidos que são reencaminhados para um servidor de origem devido a esta funcionalidade.

**Comportamento predefinido:** desativado.

###<a name="ignore-origin-no-cache"></a>Ignorar a cache não de origem
**Objetivo:** determina se o nosso CDN ignorará as diretivas de seguintes servidas a partir de um servidor de origem:

- Cache-Control: privada
- Cache-Control: arquivo não
- Cache-Control: cache não
- Pragma: cache não

Informações da chave:

- Configure esta funcionalidade, definir uma lista delimitada por espaços de códigos de estado para o qual as diretivas acima serão ignoradas.
- O conjunto de códigos de estado válido para esta funcionalidade são: 200, 203, 300, 301, 302, 305, 307, 400, 401, 402, 403, 404, 405, 406, 407, 408, 409, 410, 411, 412, 413, 414, 415, 416, 417, 500, 501, 502, 503, 504 e 505.
- Desative esta funcionalidade ao defini-la como um valor em branco.
- Devido da forma na cache de que as definições são registadas, esta funcionalidade não pode ser associada com as seguintes condições de correspondência: 
    - Limite 
    - CNAME
    - Literal de cabeçalho de pedido
    - Caráter universal de cabeçalho de pedido
    - Método de pedido
    - Literal de consulta de URL
    - Consulta de URL com carateres universais

**Comportamento predefinido:** o comportamento predefinido é que respeite as diretivas acima.

###<a name="ignore-unsatisfiable-ranges"></a>Ignorar intervalos Unsatisfiable 
**Objetivo:** determina a resposta que será devolvida aos clientes quando um pedido gera um código de estado 416 pedido intervalo não satisfatório.

Por predefinição, este código de estado é devolvido quando não é possível satisfazer o pedido de intervalo de bytes especificada por um servidor edge e um campo de cabeçalho de pedido de intervalo se não foi especificado.

Valor|Resultado
-|-
Ativado|Impede que o nosso servidores edge responder a um pedido de intervalo de byte inválido com um código de estado 416 pedido intervalo não satisfatório. Em vez disso nossos servidores irão fornecer o recurso pedido e devolver um 200 OK para o cliente.
Desativado|Restaura o comportamento predefinido. O comportamento predefinido é que respeite o código de estado 416 pedido intervalo não satisfatório.

**Comportamento predefinido:** desativado.

###<a name="internal-max-stale"></a>Interna obsoleta máx.
**Objetivo:** controlos quanto posterior à hora de expiração normal um recurso em cache poderão ser servido de um servidor edge, quando o servidor edge não consegue revalidate o elemento em cache com o servidor de origem.

Normalmente, quando o período de idade máxima de um recurso expira, o servidor edge irá enviar um pedido de revalidação para o servidor de origem. O será de servidor de origem, em seguida, responder com qualquer um 304 não modificado para dar o servidor edge termos da concessão no elemento em cache or else com 200 OK para fornecer o servidor edge com uma versão atualizada do elemento em cache.

Se o servidor edge não é possível estabelecer uma ligação com o servidor de origem durante a tentativa de tal uma revalidação, esta funcionalidade obsoleta máximo interno controla se e para o tempo limite servidor pode continuar a servir o elemento agora obsoleta.

Tenha em atenção que este intervalo de tempo é iniciado quando idade máxima do elemento expira, não quando a revalidação de falha ocorre. Por conseguinte, o período máximo durante o qual um recurso pode ser servido sem êxito revalidação é a quantidade de tempo especificado pela combinação de idade máxima plus máximo obsoleta. Por exemplo, se um recurso foi colocado em cache no 9:00, com uma duração do número máximo de 30 minutos e obsoleta uma máximo de 15 minutos, em seguida, uma tentativa de falha revalidação de 9:44 resultaria num utilizador final receber o elemento em cache obsoleto, enquanto uma tentativa de falha revalidação de 9:46 resultaria no i utilizador ND receber um tempo limite do Gateway 504.

Qualquer valor configurado para esta funcionalidade é substituída pela Cache-controlo: tem-revalidate ou colocar em Cache-controlo: proxy-revalidate cabeçalhos recebidos do servidor de origem. Se qualquer um desses cabeçalhos não for recebida do servidor de origem quando um recurso inicialmente é colocado em cache, em seguida, o servidor edge não processará os um recurso em cache obsoleto. Nesse caso, se o servidor edge não é possível revalidate com a origem quando o intervalo de idade máxima do elemento tiver expirado, em seguida, o servidor edge irá devolver um tempo limite do Gateway 504.

Informações da chave:

- Configure esta funcionalidade por:
    - Selecionar o código de estado para o qual uma obsoleta máximo será aplicada.
    - Especificar um valor inteiro e, em seguida, selecionar a unidade de tempo pretendido (por exemplo, segundos, minutos, horas, etc.). Este valor define a interna máximo obsoleta que será aplicada.

- Definir a unidade de tempo "Desligado" irá desativar esta funcionalidade. Um recurso em cache não será servido que ultrapassem o tempo de expiração normal.
- Devido da forma na cache de que as definições são registadas, esta funcionalidade não pode ser associada com as seguintes condições de correspondência: 
    - Limite 
    - CNAME
    - Literal de cabeçalho de pedido
    - Caráter universal de cabeçalho de pedido
    - Método de pedido
    - Literal de consulta de URL
    - Consulta de URL com carateres universais

**Comportamento predefinido:** dois minutos

###<a name="partial-cache-sharing"></a>Partilha de Cache parciais
**Objetivo:** determina se um pedido pode gerar o conteúdo parcialmente em cache.

Esta cache parcial, em seguida, pode ser utilizado para satisfazer os pedidos de novo para esse conteúdo até que os conteúdos solicitados totalmente é colocado em cache.

Valor|Resultado
-|-
Ativado|Pedidos podem gerar o conteúdo parcialmente em cache.
Desativado|Pedidos só é possível gerar uma versão totalmente em cache do conteúdo pedido.

**Comportamento predefinido:** desativado.

###<a name="prevalidate-cached-content"></a>Prevalidate conteúdos em cache
**Objetivo:** determina se o conteúdo em cache vai ser elegível para revalidação antecipada antes do valor de TTL expire.

Defina o período de tempo antes da expiração do TTL os conteúdos solicitados durante o qual vai ser elegível para revalidação antecipada.

Informações da chave:

- Selecionar "Off" como revalidação para ocorrer após o conteúdo em cache, precisa da unidade de tempo TTL expirou. Não deve ser especificado o tempo e serão ignorada.

**Comportamento predefinido:** desativado. Revalidação pode apenas ocorrer depois de TTL o conteúdo em cache expirou.

###<a name="refresh-zero-byte-cache-files"></a>Atualizar os ficheiros de Cache Zero bytes
**Objetivo:** determina o modo como os pedidos de um cliente HTTP para um elemento de cache de 0 bytes é processado pelos nossos servidores edge.

Os valores válidos são:

Valor|Resultado
--|--
Ativado|Faz com que o nosso servidor edge refetch o elemento a partir do servidor de origem.
Desativado|Restaura o comportamento predefinido. É o comportamento predefinido para servir a recursos de cache válido após pedido.
Esta funcionalidade não é necessária para a colocação em cache correto e de entrega de conteúdos, mas poderá ser útil como uma solução. Por exemplo, generators conteúdos dinâmicos nos servidores de origem inadvertidamente podem resultar em respostas 0 bytes enviadas para os servidores edge. Estes tipos de respostas são normalmente colocadas em cache pelos nossos servidores edge. Se souber que uma resposta de 0 bytes nunca é uma resposta válida 

para esse conteúdo, em seguida, esta funcionalidade pode impedir que estes tipos de recursos de ser servido aos seus clientes.

**Comportamento predefinido:** desativado.

###<a name="set-cacheable-status-codes"></a>Códigos de estado colocáveis de conjunto
**Objetivo:** define o conjunto de códigos de estado que podem resultar num conteúdos em cache.

Por predefinição, a colocação em cache é apenas ativada para 200 respostas OK.

Defina um conjunto delimitada por espaços dos códigos de estado pretendido.

Informações da chave:

- Ative a funcionalidade de ignorar não-Cache de origem. Se esta funcionalidade não estiver ativada, em seguida, as respostas de não 200 OK poderão não ser colocadas em cache.
- O conjunto de códigos de estado válido para esta funcionalidade são: 203, 300, 301, 302, 305, 307, 400, 401, 402, 403, 404, 405, 406, 407, 408, 409, 410, 411, 412, 413, 414, 415, 416, 417, 500, 501, 502, 503, 504 e 505.
- Esta funcionalidade não pode ser utilizada para desativar a colocação em cache para as respostas que geram um código de estado OK 200.

**Comportamento predefinido:** colocação em cache está ativada apenas para as respostas que geram um código de estado OK 200.
###<a name="stale-content-delivery-on-error"></a>Entrega de conteúdos obsoleta com o erro
**Objetivo:** 

Determina se expirado os conteúdos em cache serão entregues quando ocorre um erro durante a revalidação de cache ou ao obter os conteúdos solicitados a partir do servidor de origem do cliente.

Valor|Resultado
-|-
Ativado|Conteúdo obsoleto será servido para o autor do pedido quando ocorre um erro durante uma ligação a um servidor de origem.
Desativado|Erro do servidor de origem será reencaminhado para o autor do pedido.

**Comportamento predefinido:** desativado

###<a name="stale-while-revalidate"></a>Obsoleta ao Revalidate
**Objetivo:** melhora o desempenho ao permitir que os nossos servidores edge servir conteúdo obsoleto para o autor do pedido, enquanto a revalidação ocorre.

Informações da chave:

- O comportamento desta funcionalidade varia de acordo com a unidade de tempo selecionado.
    - **Unidade de tempo:** especificar um período de tempo e selecione uma unidade de tempo (por exemplo, segundos, minutos, horas, etc.) para permitir que a entrega de conteúdos obsoleta. Este tipo de configuração permite que a CDN para estender o período de tempo que pode fornecer conteúdo antes de exigir validação, de acordo com a seguinte fórmula:**TTL** + **obsoletos enquanto Revalidate tempo** 
    - **Desativar:** selecione "desligado" para exigir a revalidação antes de um pedido para conteúdo obsoleto pode ser servido.
        - Não especifique um período de tempo, uma vez que é forçando e serão ignorada.

**Comportamento predefinido:** desativado. Revalidação tem ocorrer antes dos conteúdos solicitados podem ser fornecidos.

###<a name="comment"></a>Comentar
**Objetivo:** permite uma nota a adicionar dentro de uma regra.

Uma utilização para esta funcionalidade é fornecer informações adicionais sobre o objetivo geral de uma regra ou o motivo pelo qual um determinado correspondem à condição funcionalidade foi adicionada à regra.

Informações da chave:

- Pode ser especificado um máximo de 150 carateres.
- Utilize apenas caracteres alfanuméricos.
- Esta funcionalidade não afeta o comportamento da regra. Destina-se apenas para fornecer uma área onde pode fornecer informações para referência futura ou que podem ajudar ao resolver problemas com a regra.
 
## <a name="headers"></a>Cabeçalhos

Estas funcionalidades são concebidas para adicionar, modificar ou eliminar os cabeçalhos do pedido ou resposta.

Nome | Objetivo
-----|--------
Cabeçalho de resposta de antiguidade | Determina se um cabeçalho de resposta de idade será incluído na resposta enviada para o autor do pedido.
Cabeçalhos de resposta de Cache de depuração | Determina se uma resposta pode incluir o cabeçalho de resposta de EC-X-Debug que fornece informações sobre a política de cache para o recurso pedido.
Modificar o cabeçalho de pedido de cliente | Substitui, acrescenta ou elimina um cabeçalho de um pedido.
Modificar o cabeçalho de resposta do cliente | Substitui, acrescenta ou elimina um cabeçalho de resposta.
Cabeçalho IP do conjunto de cliente personalizado | Permite que o endereço IP do cliente efetuou a ser adicionado ao pedido como um cabeçalho de pedido personalizado.

###<a name="age-response-header"></a>Cabeçalho de resposta de antiguidade
**Objetivo**: determina se um cabeçalho de resposta de idade será incluído na resposta enviada para o autor do pedido.
Valor|Resultado
--|--
Ativado | O cabeçalho de resposta de idade será incluído na resposta enviada para o autor do pedido.
Desativado | O cabeçalho de resposta de idade será excluído da resposta enviada para o autor do pedido.

**Comportamento de predefinido**: desativado.

###<a name="debug-cache-response-headers"></a>Cabeçalhos de resposta de Cache de depuração
**Objetivo:** determina se uma resposta pode incluir o cabeçalho de resposta de EC-X-Debug que fornece informações sobre a política de cache para o recurso pedido.

Depurar a resposta de cache cabeçalhos serão incluídos na resposta quando ambos os procedimentos seguintes se verificarem:

- Foi ativada a funcionalidade cabeçalhos da resposta de Cache de depuração no pedido pretendido.
- O pedido acima define o conjunto de cabeçalhos de resposta de cache de depuração que será incluído na resposta.

Depurar cabeçalhos podem ser solicitados, incluindo o seguinte cabeçalho e as diretivas pretendidas no pedido de resposta de cache:

X-EC-Debug: _Directive1_,_Directive2_,_DirectiveN_

**Exemplo:**

EC-X-Debug: x-ec-cache,x-ec-check-cacheable,x-ec-cache-key,x-ec-cache-state

Valor|Resultado
-|-
Ativado|Pedidos de cabeçalhos de resposta de cache de depuração irão devolver uma resposta que inclui o cabeçalho X-EC-Debug.
Desativado|O cabeçalho de resposta EC-X-Debug será excluído da resposta.

**Comportamento predefinido:** desativado.

###<a name="modify-client-request-header"></a>Modificar o cabeçalho de pedido de cliente
**Objetivo:** cada pedido contém um conjunto de [cabeçalhos de pedido]() que descrevem-lo. Esta funcionalidade pode optar por:

- Acrescentar ou substituir o valor atribuído a um cabeçalho de pedido. Se o cabeçalho de pedido especificado não existir, em seguida, esta funcionalidade irá adicioná-la para o pedido.
- Elimine um cabeçalho de pedido do pedido.

Pedidos que são reencaminhados para um servidor de origem irão refletir as alterações efetuadas por esta funcionalidade.

Uma das seguintes ações pode ser efetuada num cabeçalho de pedido:

Opção|Descrição|Exemplo
-|-|-
Acrescentar|O valor especificado será adicionado ao fim do valor de cabeçalho de pedido existente.|**O valor do cabeçalho (cliente) do pedido:**Value1 <br/> **O valor do cabeçalho (motor de regras de HTTP) do pedido:** Value2 <br/>**Novo valor de cabeçalho de pedido:** Value1Value2
Substituir|O valor do cabeçalho de pedido será definido para o valor especificado.|**O valor do cabeçalho (cliente) do pedido:**Value1 <br/>**O valor do cabeçalho (motor de regras de HTTP) do pedido:** Value2 <br/>**Novo valor de cabeçalho de pedido:** Value2 <br/>
Eliminar|Elimina o cabeçalho de pedido especificado.|**O valor do cabeçalho (cliente) do pedido:**Value1 <br/> **Modificar configuração de cabeçalho de pedido de cliente:** eliminar o cabeçalho do pedido em questão. <br/>**Resultado:** o cabeçalho de pedido especificado não será reencaminhado para o servidor de origem.

Informações da chave:

- Certifique-se de que o valor especificado na opção de nome é uma correspondência exata para o cabeçalho de pedido pretendido.
- Caso não está a ser levado em consideração para fins de identificação de um cabeçalho. Por exemplo, qualquer um das variações do nome de cabeçalho de Cache-Control seguintes podem ser utilizados para identificá-lo:
    - cache-control
    - CACHE-CONTROL
    - cachE-Control
- Quando especificar um nome de cabeçalho, utilize apenas carateres alfanuméricos, traços ou carateres de sublinhado.
- Eliminar um cabeçalho irá impedi-lo de que está a ser reencaminhados para um servidor de origem pelos nossos servidores edge.
- Os seguintes cabeçalhos estão reservados e não podem ser modificados por esta funcionalidade:
    - reencaminhado
    - anfitrião
    - através do
    - aviso
    - x-reencaminhados-para
    - Todos os nomes de cabeçalho que começam por "x-ec" estão reservados.

###<a name="modify-client-response-header"></a>Modificar o cabeçalho de resposta do cliente
Cada resposta contém um conjunto de [cabeçalhos de resposta]() que descrevem-lo. Esta funcionalidade pode optar por:

- Acrescentar ou substituir o valor atribuído a um cabeçalho de resposta. Se o cabeçalho de resposta especificado não existir, em seguida, esta funcionalidade irá adicioná-la para a resposta.
- Elimine um cabeçalho de resposta da resposta.

Por predefinição, os valores de cabeçalho de resposta são definidos por um servidor de origem e pelos nossos servidores edge.

Uma das seguintes ações pode ser efetuada num cabeçalho de resposta:

Opção|Descrição|Exemplo
-|-|-
Acrescentar|O valor especificado será adicionado ao fim do valor de cabeçalho de resposta existente.|**O valor de cabeçalho de resposta (cliente):**Value1 <br/> **O valor de cabeçalho de resposta (motor de regras de HTTP):** Value2 <br/>**Novo valor de cabeçalho de resposta:** Value1Value2
Substituir|O valor do cabeçalho de resposta será definido para o valor especificado.|**O valor de cabeçalho de resposta (cliente):**Value1 <br/>**O valor de cabeçalho de resposta (motor de regras de HTTP):** Value2 <br/>**Novo valor de cabeçalho de resposta:** Value2 <br/>
Eliminar|Elimina o cabeçalho de resposta especificado.|**O valor de cabeçalho de resposta (cliente):** Value1 <br/> **Modificar configuração de cabeçalho de resposta do cliente:** eliminar o cabeçalho de resposta em questão. <br/>**Resultado:** o cabeçalho de resposta especificado não será reencaminhado para o autor do pedido.

Informações da chave:

- Certifique-se de que o valor especificado na opção de nome é uma correspondência exata para o cabeçalho de resposta pretendido. 
- Caso não está a ser levado em consideração para fins de identificação de um cabeçalho. Por exemplo, qualquer um das variações do nome de cabeçalho de Cache-Control seguintes podem ser utilizados para identificá-lo:
    - cache-control
    - CACHE-CONTROL
    - cachE-Control
- Eliminar um cabeçalho irá impedi-lo de que está a ser reencaminhados para o autor do pedido.
- Os seguintes cabeçalhos estão reservados e não podem ser modificados por esta funcionalidade:
    - codificação aceitar
    - idade
    - ligação
    - codificação de conteúdo
    - comprimento do conteúdo
    - intervalo de conteúdo
    - data
    - servidor
    - trailer
    - codificação de transferência
    - atualização
    - variar
    - através do
    - aviso
    - Todos os nomes de cabeçalho que começam por "x-ec" estão reservados.

###<a name="set-client-ip-custom-header"></a>Cabeçalho IP do conjunto de cliente personalizado
**Objetivo:** adiciona um cabeçalho personalizado que identifica o cliente efetuou por endereço IP para o pedido.

A opção de nome de cabeçalho define o nome do cabeçalho do pedido personalizado onde será armazenado endereço IP do cliente.

Esta funcionalidade permite que um cliente endereços de servidor de origem para determinar o IP de cliente através de um cabeçalho de pedido personalizado. Se o pedido é servido a partir da cache, o servidor de origem não a ser informado de endereço IP do cliente. Por conseguinte, é recomendado que esta funcionalidade ser utilizada com ADN ou recursos que não irão ser colocados em cache.

Certifique-se de que o nome de cabeçalho especificado não corresponde a nenhum dos nomes dos seguintes:

- Nomes de cabeçalho de pedido padrão. É possível encontrar uma lista de nomes de cabeçalho padrão no [RFC 2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).
- Nomes de cabeçalho reservado:
    - reencaminhado para
    - anfitrião
    - variar
    - através do
    - aviso
    - x-reencaminhados-para
    - Todos os nomes de cabeçalho que começam por "x-ec" estão reservados.
 
## <a name="logs"></a>Registos

Estas funcionalidades são concebidas para personalizar os dados armazenados nos ficheiros de registo não processados.

Nome | Objetivo
-----|--------
Campo de registo personalizado 1 | Determina o formato e o conteúdo que será atribuído para o campo de registo personalizado num ficheiro de registo não processados.
Cadeia de consulta de registo | Determina se uma cadeia de consulta será armazenada juntamente com o URL nos registos de acesso.

###<a name="custom-log-field-1"></a>Campo de registo personalizado 1
**Objetivo:** determina o formato e o conteúdo que será atribuído para o campo de registo personalizado num ficheiro de registo não processados.

O objetivo principal atrás este campo personalizado é permitir-lhe determinar qual o pedido e os valores de cabeçalho de resposta serão armazenados nos seus ficheiros de registo.

Por predefinição, o campo de registo personalizado é designado por "x-ec_custom-1." No entanto, o nome deste campo pode ser personalizado do [página de definições de registo em bruto]().

A formatação que deve utilizar para especificar os cabeçalhos de pedido e resposta está definida abaixo.

Tipo de cabeçalho|Formato|Exemplos
-|-|-
Cabeçalho de pedido|%{[RequestHeader]()}[i]() | % {Aceitar codificação} i <br/> {Referenciador} i <br/> % {Autorização} i
Cabeçalho de Resposta|%{[ResponseHeader]()}[Nã]()| O % {idade} <br/> O % {content-Type} <br/> O % {cookie}

Informações da chave:

- Um campo de registo personalizado pode conter qualquer combinação de campos de cabeçalho e texto simples.
- Os carateres válidos para este campo incluem o seguinte: alfanuméricos (0-9, a-z e A-Z), travessões, vírgula, ponto e vírgula, apostrophes, vírgulas, períodos, carateres de sublinhado, sinais de igual, parênteses, parênteses Retos e espaços. O símbolo de percentagem e chavetas só são permitidas quando são utilizados para especificar um campo de cabeçalho.
- A ortografia para cada campo de cabeçalho especificado tem de corresponder ao nome de cabeçalho de pedido/resposta pretendido.
- Se pretende especificar vários cabeçalhos, em seguida, é recomendado que utilize um separador para indicar o cabeçalho de cada. Por exemplo, pode utilizar uma abreviatura para cada cabeçalho. Sintaxe de exemplo é fornecida abaixo.
    - AE: % {aceitar codificação} i r: % {autorização} i Cionar: o % {Content-Type} 

**Valor predefinido:** -

###<a name="log-query-string"></a>Cadeia de consulta de registo
**Objetivo:** determina se uma cadeia de consulta será armazenada juntamente com o URL nos registos de acesso.

Valor|Resultado
-|-
Ativado|Permite que o armazenamento de cadeias de consulta ao gravar os URLs no registo de acesso. Se um URL contém uma cadeia de consulta, em seguida, esta opção só terá um efeito.
Desativado|Restaura o comportamento predefinido. É o comportamento predefinido para ignorar as cadeias de consulta aquando a gravar URLs no registo de acesso.

**Comportamento predefinido:** desativado.

<!---
## Optimize

These features determine whether a request will undergo the optimizations provided by Edge Optimizer.

Name | Purpose
-----|--------
Edge Optimizer | Determines whether Edge Optimizer can be applied to a request.
Edge Optimizer – Instantiate Configuration | Instantiates or activates the Edge Optimizer configuration associated with a site.

###Edge Optimizer
**Purpose:** Determines whether Edge Optimizer can be applied to a request.

If this feature has been enabled, then the following criteria must also be met before the request will be processed by Edge Optimizer:

- The requested content must use an edge CNAME URL.
- The edge CNAME referenced in the URL must correspond to a site whose configuration has been activated in a rule.

This feature requires the ADN platform and the Edge Optimizer feature.

Value|Result
-|-
Enabled|Indicates that the request is eligible for Edge Optimizer processing.
Disabled|Restores the default behavior. The default behavior is to deliver content over the ADN platform without any additional processing.

**Default Behavior:** Disabled
 

###Edge Optimizer - Instantiate Configuration
**Purpose:** Instantiates or activates the Edge Optimizer configuration associated with a site.

This feature requires the ADN platform and the Edge Optimizer feature.

Key information:

- Instantiation of a site configuration is required before requests to the corresponding edge CNAME can be processed by Edge Optimizer.
- This instantiation only needs to be performed a single time per site configuration. A site configuration that has been instantiated will remain in that state until the Edge Optimizer – Instantiate Configuration feature that references it is removed from the rule.
- The instantiation of a site configuration does not mean that all requests to the corresponding edge CNAME will automatically be processed by Edge Optimizer. The Edge Optimizer feature determines whether an individual request will be processed.

If the desired site does not appear in the list, then you should edit its configuration and verify that the Active option has been marked.

**Default Behavior:** Site configurations are inactive by default.
--->

## <a name="origin"></a>Origem

Estas funcionalidades são concebidas para controlar a forma como o CDN comunica com um servidor de origem.

Nome | Objetivo
-----|--------
Máximo de pedidos de ligação Keep-Alive | Define o número máximo de pedidos para uma ligação Keep-Alive antes de que está fechado.
Cabeçalhos especiais de proxy | Define o conjunto de cabeçalhos de pedido de CDN específicos que serão reencaminhados de um servidor edge para um servidor de origem.


###<a name="maximum-keep-alive-requests"></a>Máximo de pedidos de ligação Keep-Alive
**Objetivo:** define o número máximo de pedidos para uma ligação Keep-Alive antes de que está fechado.

Definir o número máximo de pedidos para um valor baixo é vivamente desaconselhável e pode resultar numa degradação do desempenho.

Informações da chave:

- Especificar este valor como um número inteiro de todo.
- Não inclua vírgulas ou pontos finais no valor especificado.

**Valor predefinido:** 10 000 pedidos

###<a name="proxy-special-headers"></a>Cabeçalhos especiais de proxy
**Objetivo:** define o conjunto de [cabeçalhos de pedido específicos CDN]() que serão reencaminhados de um servidor edge para um servidor de origem.

Informações da chave:

- Cada cabeçalho de pedido de CDN específica definido nesta funcionalidade será reencaminhado para um servidor de origem.
- Impedi que um cabeçalho de pedido de CDN específico que está a ser reencaminhados para um servidor de origem, removendo-os desta lista.

**Comportamento predefinido:** todos os [cabeçalhos de pedido específicos CDN]() serão reencaminhados para o servidor de origem.

## <a name="specialty"></a>Specialty

Estas funcionalidades oferecem funcionalidades avançadas que apenas devem ser utilizada por utilizadores avançados.

Nome | Objetivo
-----|--------
Métodos de HTTP colocáveis | Determina o conjunto de métodos HTTP adicionais que podem ser colocadas em cache na nossa rede.
Tamanho do corpo do pedido colocáveis | Define o limiar para determinar se uma resposta POST pode ser colocadas em cache.

###<a name="cacheable-http-methods"></a>Métodos de HTTP colocáveis
**Objetivo:** determina o conjunto de métodos HTTP adicionais que podem ser colocadas em cache na nossa rede.

Informações da chave:

- Esta funcionalidade parte do princípio de que GET respostas devem sempre ser colocado em cache. Como resultado, o método HTTP obter não deve estar incluído quando definir esta funcionalidade.
- Esta funcionalidade só suporta o método de POST HTTP. Ativar o caching de resposta POST ao definir esta funcionalidade para: POST 
- Por predefinição, apenas os pedidos cujo corpo é menor do que 14 Kb irão ser colocadas em cache. Utilize a funcionalidade de tamanho de corpo do pedido colocáveis para definir o tamanho do corpo pedido máximo.

**Comportamento predefinido:** apenas respostas GET serão colocadas em cache.

###<a name="cacheable-request-body-size"></a>Tamanho do corpo do pedido colocáveis

**Objetivo:** define o limiar para determinar se uma resposta POST pode ser colocadas em cache.

Este limiar é determinado através da especificação de um tamanho do corpo do pedido máximo. Não serão possível em cache pedidos que contêm um grande corpo do pedido.

Informações da chave:

- Esta funcionalidade só é aplicável quando as respostas POST são elegíveis para colocar em cache. Utilize a funcionalidade de métodos de HTTP colocáveis para ativar a colocação em cache de pedido POST.
- O corpo do pedido é levado em consideração para:
    - valores x-www-form-urlencoded
    - Garantir uma chave exclusiva de cache
- Definir o tamanho do corpo um grande pedido máximo pode afetar o desempenho de entrega de dados.
    - **Valor recomendado:** 14 Kb
    - **Valor mínimo:** 1 Kb

**Comportamento predefinido:** 14 Kb
 
## <a name="url"></a>URL

Estas funcionalidades permitem um pedido para ser redirecionado ou rescrita para um URL diferente.

Nome | Objetivo
-----|--------
Seguir redirecionamentos | Determina se os pedidos podem ser redirecionados para o nome de anfitrião definida no cabeçalho de localização devolvido por um servidor de origem do cliente.
URL de redirecionamento | Redireciona pedidos através do cabeçalho de localização.
URL reescrever  | Reescreve o URL do pedido.

###<a name="follow-redirects"></a>Seguir redirecionamentos
**Objetivo:** determina se os pedidos podem ser redirecionados para o nome de anfitrião definida no cabeçalho de localização devolvido por um servidor de origem do cliente.

Informações da chave:

- Pedidos só podem ser redirecionados para o limite CNAMEs que correspondem a mesma plataforma.

Valor|Resultado
-|-
Ativado|Os pedidos podem ser redirecionados.
Desativado|Pedidos não serão redirecionados.

**Comportamento predefinido:** desativado.
###<a name="url-redirect"></a>URL de redirecionamento
**Objetivo:** redireciona pedidos através do cabeçalho de localização.

A configuração desta funcionalidade precisa de definir as opções seguintes:

Opção|Descrição
-|-
Código|Selecione o código de resposta que vai ser devolvido ao autor do pedido.
Origem & padrão| Estas definições, defina um padrão URI do pedido que identifica o tipo de pedidos que podem ser redirecionados. Serão redirecionados apenas pedidos cujo URL satisfaça ambos os seguintes critérios: <br/> <br/> **Origem (ou ponto de acesso ao conteúdo):** selecione um caminho relativo que identifica um servidor de origem. Esta é a secção "/XXXX/" e o nome do ponto final. <br/> **Origem (padrão):** um padrão que identifica pedidos pelo caminho relativo tem de ser definido. Neste padrão de expressão regular tem de definir um caminho que inicia diretamente após o acesso ao conteúdo anteriormente selecionado ponto (consultar acima). <br/> -Certifique-se de que o pedido URI critérios (ou seja, origem & padrão) definidos anteriormente não entra em conflito com quaisquer condições de correspondência definidas para esta funcionalidade. <br/> -Especifique um padrão; Se utilizar um valor em branco como o padrão, todas as cadeias são correspondidas.
Destino| Defina o URL ao qual serão redirecionados os pedidos acima. <br/> Dinamicamente construir este URL a utilizar: <br/> -Um padrão de expressão regular <br/>-Variáveis HTTP <br/> Substitua os valores capturados o padrão de origem para o padrão de destino utilizando $ _n_  onde  _n_  identifica um valor pela ordem em que foi capturada. Por exemplo, $1 representa o primeiro valor capturado o padrão de origem, enquanto $2 representa o segundo valor. <br/> 
Recomenda-se vivamente a utilização de um URL absoluto. A utilização de um URL relativo pode redirecionar os URLs da CDN para um caminho inválido.

**Cenário de exemplo**

Neste exemplo, vamos demonstrar como redirecionar o URL de CNAME que resolve para este URL base do CDN contorno: http://marketing.azureedge.net/brochures

Elegíveis pedidos serão redirecionado para este limite de base de URL de CNAME: http://cdn.mydomain.com/resources

Redirecionamento este URL pode ser conseguido através da configuração seguinte:![](./media/cdn-rules-engine-reference/cdn-rules-engine-redirect.png)

**Pontos de chave:**

- A funcionalidade redirecionar URL define o pedido de URL que será redirecionado. Como resultado, as condições de correspondência adicionais não são necessárias. Embora a condição de correspondência foi definida como "Sempre", serão redirecionados apenas os pedidos que apontam para a pasta "brochures" na origem de cliente "marketing". 
- Todos os pedidos correspondentes serão redirecionados para o limite definido do URL de CNAME na opção de destino. 
    - Cenário de exemplo #1: 
        - Exemplo de pedido (CDN URL): http://marketing.azureedge.net/brochures/widgets.pdf 
        - O URL de pedido (após o redirecionamento): http://cdn.mydomain.com/resources/widgets.pdf  
    - Cenário de exemplo #2: 
        - Exemplo de pedido (Edge CNAME URL): http://marketing.mydomain.com/brochures/widgets.pdf 
        - O URL de pedido (após o redirecionamento): http://cdn.mydomain.com/resources/widgets.pdf cenário de exemplo
    - Cenário de exemplo #3: 
        - Exemplo de pedido (Edge CNAME URL): http://brochures.mydomain.com/campaignA/final/productC.ppt 
        - O URL de pedido (após o redirecionamento): http://cdn.mydomain.com/resources/campaignA/final/productC.ppt  
- A variável de esquema pedido (% {esquema}) foi utilizada na opção de destino. Isto garante que permanece inalterada esquema o pedido após o redirecionamento.
- Os segmentos de URL que foram capturados do pedido são acrescentados para o novo URL através de "$1."
 
###<a name="url-rewrite"></a>URL reescrever
**Objetivo:** reescreve o URL do pedido.

Informações da chave:

- A configuração desta funcionalidade precisa de definir as opções seguintes:

Opção|Descrição
-|-
 Origem & padrão | Estas definições, defina um padrão URI do pedido que identifica o tipo de pedidos que podem ser foi reescrita. Irão ser foi reescritos apenas pedidos cujo URL satisfaça ambos os seguintes critérios: <br/>     - **Origem (ou ponto de acesso ao conteúdo):** selecione um caminho relativo que identifica um servidor de origem. Esta é a secção "/XXXX/" e o nome do ponto final. <br/> - **Origem (padrão):** um padrão que identifica pedidos pelo caminho relativo tem de ser definido. Neste padrão de expressão regular tem de definir um caminho que inicia diretamente após o acesso ao conteúdo anteriormente selecionado ponto (consultar acima). <br/> Certifique-se de que o pedido URI critérios (ou seja, origem & padrão) definidos anteriormente não entra em conflito com qualquer uma das condições definidas para esta funcionalidade correspondência. Especificar um padrão; Se utilizar um valor em branco como o padrão, todas as cadeias são correspondidas. 
 Destino  |Defina o URL relativo ao qual os pedidos acima irão ser foi reescritos por: <br/>    1. Selecionar um ponto de acesso ao conteúdo que identifica um servidor de origem. <br/>    2. Definir a utilizar um caminho relativo: <br/>        -Um padrão de expressão regular <br/>        -Variáveis HTTP <br/> <br/> Substitua os valores capturados o padrão de origem para o padrão de destino utilizando $ _n_  onde  _n_  identifica um valor pela ordem em que foi capturada. Por exemplo, $1 representa o primeiro valor capturado o padrão de origem, enquanto $2 representa o segundo valor. 
 Esta funcionalidade permite que os nossos servidores edge de reescrever o URL sem efetuar um redirecionamento tradicional. Isto significa que o autor do pedido irá receber o mesmo código de resposta, como se o URL de conversão tinha sido pedido.

**Cenário de exemplo 1**

Neste exemplo, vamos demonstrar como redirecionar o URL de CNAME que resolve para este URL base do CDN contorno: http://marketing.azureedge.net/brochures/

Elegíveis pedidos serão redirecionado para este limite de base de URL de CNAME: http://MyOrigin.azureedge.net/resources/

Redirecionamento este URL pode ser conseguido através da configuração seguinte:![](./media/cdn-rules-engine-reference/cdn-rules-engine-rewrite.png)

**Cenário de exemplo 2**

Neste exemplo, vamos demonstrar como redirecionar um limite de URL de CNAME de UPPERCASE para minúsculas utilizando expressões regulares.

Redirecionamento este URL pode ser conseguido através da configuração seguinte:![](./media/cdn-rules-engine-reference/cdn-rules-engine-to-lowercase.png)


**Pontos de chave:**

- A funcionalidade de URL Rewrite define o pedido de URLs que irão ser foi reescritos. Como resultado, as condições de correspondência adicionais não são necessárias. Embora a condição de correspondência foi definida como "Sempre", apenas os pedidos que apontam para a pasta "brochures" na origem de cliente "marketing" irão ser foi reescritos.

- Os segmentos de URL que foram capturados do pedido são acrescentados para o novo URL através de "$1."



###<a name="compatibility"></a>Compatibilidade

Esta funcionalidade inclui correspondentes aos critérios que devem ser satisfeitos antes de poder ser aplicado a um pedido. Para evitar a configurar os critérios de correspondência em conflito, esta funcionalidade é incompatível com as seguintes condições de correspondência:

- COMO número
- Origem da CDN
- Endereço IP do Cliente
- Origem de cliente
- Esquema de pedido
- Diretório de caminho de URL
- Extensão de caminho de URL
- Nome de ficheiro de caminho de URL
- Literal de caminho de URL
- Regex de caminho de URL
- Caminho de URL com carateres universais
- Literal de consulta de URL
- Parâmetro de consulta de URL
- Regex de consulta de URL
- Consulta de URL com carateres universais


## <a name="next-steps"></a>Passos Seguintes
* [Referência do motor de regras](cdn-rules-engine-reference.md)
* [Expressões condicionais de motor de regras](cdn-rules-engine-reference-conditional-expressions.md)
* [Condições de correspondência do motor de regras](cdn-rules-engine-reference-match-conditions.md)
* [Substituir o comportamento HTTP predefinido utilizando o motor de regras](cdn-rules-engine.md)
* [Descrição geral da CDN do Azure](cdn-overview.md)
