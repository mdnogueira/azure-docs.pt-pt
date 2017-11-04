---
title: "Azure de autenticação - ferramenta de modelação de ameaça Microsoft - | Microsoft Docs"
description: "Mitigações ameaças exposta na ferramenta de modelação de ameaça"
services: security
documentationcenter: na
author: RodSan
manager: RodSan
editor: RodSan
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: e547469dc61eddd1d772571ab0919532ac91f128
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="security-frame-authentication--mitigations"></a>Período de segurança: Autenticação | Mitigações 
| Produtos/serviços | Artigo |
| --------------- | ------- |
| **Aplicação Web**    | <ul><li>[Considere a utilização de um mecanismo de autenticação padrão para autenticar a aplicação Web](#standard-authn-web-app)</li><li>[As aplicações devem processar cenários de autenticação falhada de forma segura](#handle-failed-authn)</li><li>[Passo ativar cópia de segurança ou a autenticação adaptável](#step-up-adaptive-authn)</li><li>[Certifique-se de que interfaces administrativas adequadamente são bloqueadas](#admin-interface-lockdown)</li><li>[Implementar esqueceu funcionalidades de palavra-passe de forma segura](#forgot-pword-fxn)</li><li>[Certifique-se de que a política de conta e palavra-passe são implementados](#pword-account-policy)</li><li>[Implemente controlos para impedir a enumeração de nome de utilizador](#controls-username-enum)</li></ul> |
| **Base de Dados** | <ul><li>[Sempre que possível, utilize a autenticação do Windows para ligar ao SQL Server](#win-authn-sql)</li><li>[Sempre que possível utilizar a autenticação do Active Directory do Azure para ligar à base de dados do SQL Server](#aad-authn-sql)</li><li>[Quando é utilizado o modo de autenticação do SQL Server, certifique-se de que a política de conta e palavra-passe são impostas no SQL server](#authn-account-pword)</li><li>[Não utilize a autenticação de SQL nas bases de dados nele contidos](#autn-contained-db)</li></ul> |
| **Hub de eventos do Azure** | <ul><li>[Utilize pelas credenciais de autenticação de dispositivo utilizando SaS tokens](#authn-sas-tokens)</li></ul> |
| **Limite de fidedignidade do Azure** | <ul><li>[Ativar o Azure multi-factor Authentication para os administradores do Azure](#multi-factor-azure-admin)</li></ul> |
| **Limite de fidedignidade de recursos de infraestrutura de serviço** | <ul><li>[Restringir o acesso anónimo ao Cluster do Service Fabric](#anon-access-cluster)</li><li>[Certifique-se de que o certificado de cliente para o nó de Service Fabric é diferente do certificado do nó de nó](#fabric-cn-nn)</li><li>[Utilizar o AAD para autenticar clientes para os clusters de recursos de infraestrutura de serviço](#aad-client-fabric)</li><li>[Certifique-se de que os certificados de infraestrutura de serviço são obtidos a partir de um aprovados autoridade de certificação (CA)](#fabric-cert-ca)</li></ul> |
| **Servidor de identidades** | <ul><li>[Utilizar cenários de autenticação padrão suportados pelo servidor de identidades](#standard-authn-id)</li><li>[Ignorar a cache de token do servidor de identidades predefinido com uma alternativa dimensionável](#override-token)</li></ul> |
| **Limite de fidedignidade de máquina** | <ul><li>[Certifique-se de que os binários da aplicação implementada estejam assinados digitalmente](#binaries-signed)</li></ul> |
| **WCF** | <ul><li>[Ativar a autenticação quando a ligação a filas MSMQ no WCF](#msmq-queues)</li><li>[Efetue WCF mensagem clientCredentialType não definido como nenhum](#message-none)</li><li>[Efetue WCF clientCredentialType de transporte não definido como none](#transport-none)</li></ul> |
| **API Web** | <ul><li>[Certifique-se de que a autenticação standard técnicas são utilizadas para proteger a APIs da Web](#authn-secure-api)</li></ul> |
| **Azure AD** | <ul><li>[Utilizar cenários de autenticação padrão suportados pelo Azure Active Directory](#authn-aad)</li><li>[Substituir predefinição ADAL token cache com uma alternativa dimensionável](#adal-scalable)</li><li>[Certifique-se de que TokenReplayCache é utilizada para impedir a reprodução de tokens de autenticação da ADAL](#tokenreplaycache-adal)</li><li>[Utilizar as bibliotecas ADAL para gerir pedidos de token de OAuth2 clientes para o AAD (ou AD no local)](#adal-oauth2)</li></ul> |
| **Gateway de campo de IoT** | <ul><li>[Autenticar a ligação para o Gateway de campo de dispositivos](#authn-devices-field)</li></ul> |
| **Gateway de nuvem do IoT** | <ul><li>[Certifique-se de que os dispositivos que se ligam ao gateway de nuvem são autenticados](#authn-devices-cloud)</li><li>[Utilizar as credenciais de autenticação por dispositivo](#authn-cred)</li></ul> |
| **Armazenamento do Azure** | <ul><li>[Certifique-se de que apenas os contentores necessários e os blobs recebem acesso de leitura anónimo](#req-containers-anon)</li><li>[Conceder acesso limitado para os objetos no storage do Azure através de SAS ou SAP](#limited-access-sas)</li></ul> |

## <a id="standard-authn-web-app"></a>Considere a utilização de um mecanismo de autenticação padrão para autenticar a aplicação Web

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| Detalhes | <p>A autenticação é o processo de onde uma entidade comprova a respetiva identidade, normalmente, através de credenciais, tais como o nome de utilizador e palavra-passe. Existem vários protocolos de autenticação disponíveis que podem ser considerados. Alguns deles são listados abaixo:</p><ul><li>Certificados de cliente</li><li>Baseado no Windows</li><li>Formulários com base</li><li>Federação - ADFS</li><li>Federação - do Azure AD</li><li>Federação - servidor de identidades</li></ul><p>Considere a utilização de um mecanismo de autenticação padrão para identificar o processo de origem</p>|

## <a id="handle-failed-authn"></a>As aplicações devem processar cenários de autenticação falhada de forma segura

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| Detalhes | <p>As aplicações que explicitamente autenticar os utilizadores devem processar cenários de autenticação falhada em segurança. O mecanismo de autenticação tem de:</p><ul><li>Negar o acesso a recursos com privilégios quando ocorre uma falha de autenticação</li><li>Apresentar uma mensagem de erro genérica após a falha na autenticação e ocorre de acesso negado</li></ul><p>Teste para:</p><ul><li>Proteção de recursos com privilégios após inícios de sessão falhados</li><li>É apresentada uma mensagem de erro genérico na autenticação falhada e o acesso negado evento (s)</li><li>As contas estão desativadas após um número excessivo de tentativas falhadas</li><ul>|

## <a id="step-up-adaptive-authn"></a>Passo ativar cópia de segurança ou a autenticação adaptável

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| Detalhes | <p>Certifique-se de que a aplicação tem autorização adicionais (tais como passo cópias de segurança ou autenticação adaptável, através da autenticação multifator, tais como o envio de OTP de SMS, e-mail, etc. ou pedir para reautenticação) para o utilizador é solicitado antes de ser concedido acesso ao informações confidenciais. Também se aplica esta regra para efetuar alterações de crítico para uma conta ou uma ação</p><p>Isto também significa que o adaptation de autenticação tem de ser implementadas de forma a que a aplicação impõe corretamente sensíveis ao contexto autorização, de modo a não permite a manipulação não autorizada através do exemplo, a adulteração de parâmetro</p>|

## <a id="admin-interface-lockdown"></a>Certifique-se de que interfaces administrativas adequadamente são bloqueadas

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| Detalhes | A primeira solução é conceder acesso apenas a partir de um determinado intervalo IP de origem para a interface administrativa. Se não seria possível que é sempre essa solução recomendada para impor uma autenticação Step ou adaptável para iniciar sessão para a interface administrativa |

## <a id="forgot-pword-fxn"></a>Implementar esqueceu funcionalidades de palavra-passe de forma segura

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| Detalhes | <p>A primeira coisa que é verificar se esqueceu a palavra-passe e outros caminhos de recuperação enviar uma ligação, incluindo um token de ativação de tempo limitado, em vez da palavra-passe em si. Autenticação adicional, com base na configuração soft-tokens (por exemplo, aplicações móveis SMS token, nativas, etc.) pode ser necessária bem antes do envio através de ligação. Segundo, deve não bloqueia a conta de utilizadores enfrenta o processo de obtenção de uma nova palavra-passe está em curso.</p><p>Isto pode levar a um ataque de recusa de serviço sempre que um atacante decide intencionalmente bloquear os utilizadores com um ataque automatizado. Terceira, sempre que o novo pedido de palavra-passe foi definido em curso, a mensagem a apresenta deve ser generalizada para evitar a enumeração de nome de utilizador. Fourth, sempre não permitir a utilização de palavras-passe antigas e implemente uma política de palavra-passe segura.</p> |

## <a id="pword-account-policy"></a>Certifique-se de que a política de conta e palavra-passe são implementados

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| Detalhes | <p>Política de palavra-passe e a conta em conformidade com a política organizacional e melhores práticas deve ser implementada.</p><p>Para se Defender contra força bruta e deteção de dicionário com base em: política de palavra-passe segura tem de ser implementada para se certificar de que os utilizadores criar palavra-passe complexa (por exemplo, comprimento mínimo de 12 carateres, carateres alfanuméricos e especiais).</p><p>Políticas de bloqueio de conta podem ser implementadas da seguinte forma:</p><ul><li>**Limite de bloqueio de forma recuperável:** Isto pode ser uma boa opção para proteger os seus utilizadores contra ataques de força bruta. Por exemplo, sempre que o utilizador introduz uma palavra-passe errada três vezes a aplicação foi possível bloquear a conta para um minuto para abrandar o processo de força forçar a palavra-passe tornando menos rentável para o atacante continuar. Se implementar o disco rígidas de bloqueio medidas preventivas para este exemplo seria alcançar uma "Dos" bloqueando permanentemente saída contas. Em alternativa, a aplicação poderá gerar uma OTP (um tempo de palavra-passe de) e enviá-lo fora de banda (através de correio eletrónico, sms etc.) para o utilizador. Outra abordagem poderá implementar CAPTCHA após um número de limiar de tentativas falhadas é atingido.</li><li>**Disco rígido de limite de bloqueio:** deve ser aplicado este tipo de bloqueio sempre que detetar um utilizador atacar a sua aplicação e contador-lo através de bloqueio permanentemente a conta até que uma equipa de resposta tido tempo para as respetivas forense. Após este processo que pode optar por conceder ao utilizador efetuar uma cópia a conta ou efetuar mais ações legais em relação a ele. Este tipo de abordagem impede que o atacante penetrating ainda mais a sua aplicação e infraestrutura.</li></ul><p>Para se Defender contra ataques em predefinido e previsíveis contas, certifique-se de que todas as chaves e as palavras-passe são substituível em e são gerados ou substituídos após o tempo de instalação.</p><p>Se a aplicação tem de gerar automaticamente palavras-passe, certifique-se de que as palavras-passe geradas estão aleatórias e têm elevada entropia.</p>|

## <a id="controls-username-enum"></a>Implemente controlos para impedir a enumeração de nome de utilizador

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Todas as mensagens de erro devem ser generalizadas para evitar a enumeração de nome de utilizador. Também por vezes, não é possível evitar informações funcionalidades, tais como uma página de registo de fuga de dados. Aqui tem de utilizar os métodos de limitação de taxa de mensagens em fila, como CAPTCHA para evitar um ataque automatizado por um atacante. |

## <a id="win-authn-sql"></a>Sempre que possível, utilize a autenticação do Windows para ligar ao SQL Server

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | OnPrem |
| **Atributos**              | Versão do SQL Server - todos os |
| **Referências**              | [SQL Server - escolher um modo de autenticação](https://msdn.microsoft.com/library/ms144284.aspx) |
| **Passos** | Autenticação Windows utiliza o protocolo de segurança do Kerberos, fornece a imposição de políticas de palavra-passe em relação a validação de complexidade de palavras-passe seguras, fornece suporte para o bloqueio da conta e suporta expiração de palavra-passe.|

## <a id="aad-authn-sql"></a>Sempre que possível utilizar a autenticação do Active Directory do Azure para ligar à base de dados do SQL Server

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | SQL Azure |
| **Atributos**              | Versão do SQL Server - V12 |
| **Referências**              | [Ligar à base de dados do SQL Server utilizando a autenticação do Azure Active Directory](https://azure.microsoft.com/documentation/articles/sql-database-aad-authentication/) |
| **Passos** | **Versão mínima:** Azure SQL Database V12 necessário para permitir a base de dados do Azure SQL Server utilizar a autenticação do AAD contra Microsoft Directory |

## <a id="authn-account-pword"></a>Quando é utilizado o modo de autenticação do SQL Server, certifique-se de que a política de conta e palavra-passe são impostas no SQL server

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Política de palavras-passe do SQL Server](https://technet.microsoft.com/library/ms161959(v=sql.110).aspx) |
| **Passos** | Quando utilizar a autenticação do SQL Server, são criados os inícios de sessão no SQL Server que não são baseadas em contas de utilizador do Windows. O nome de utilizador e a palavra-passe são criadas através do SQL Server e armazenados no SQL Server. SQL Server pode utilizar mecanismos de política de palavra-passe do Windows. Podem ser aplicados a complexidade da mesma e políticas de expiração utilizadas no Windows para palavras-passe utilizadas dentro do SQL Server. |

## <a id="autn-contained-db"></a>Não utilize a autenticação de SQL nas bases de dados nele contidos

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | OnPrem, Azure SQL Server |
| **Atributos**              | SQL Server versão - MSSQL2012, versão do SQL - V12 |
| **Referências**              | [Melhores práticas de segurança com bases de dados contidas](http://msdn.microsoft.com/library/ff929055.aspx) |
| **Passos** | A ausência de uma política de palavra-passe imposta pode aumentar a probabilidade de uma credencial fraca a ser estabelecida na base de dados contida. Tire partido da autenticação Windows. |

## <a id="authn-sas-tokens"></a>Utilize pelas credenciais de autenticação de dispositivo utilizando SaS tokens

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Hub de eventos do Azure | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Event Hubs autenticação e segurança descrição geral do modelo](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Passos** | <p>O modelo de segurança de Event Hubs baseia-se numa combinação de tokens de assinatura de acesso partilhado (SAS) e publicadores de eventos. O nome do publicador representa o DeviceID que recebe o token. Isto iria ajudar a associar os tokens gerados com os respetivos dispositivos.</p><p>Todas as mensagens são etiquetadas com originador no lado do serviço que permite a deteção de origem no payload spoofing tentativas. Quando a autenticação de dispositivos, gerar um por um âmbito para um fabricante exclusivo de token SaS do dispositivo.</p>|

## <a id="multi-factor-azure-admin"></a>Ativar o Azure multi-factor Authentication para os administradores do Azure

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de fidedignidade do Azure | 
| **Fase SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [O que é o Multi-Factor Authentication do Azure?](https://azure.microsoft.com/documentation/articles/multi-factor-authentication/) |
| **Passos** | <p>Autenticação multifator (MFA) é um método de autenticação que requer mais do que um método de verificação e adiciona uma segunda camada crítica de segurança aos inícios de sessão de utilizador e de transações. Funciona exigindo quaisquer dois ou mais dos seguintes métodos de verificação:</p><ul><li>Algo sabe (normalmente uma palavra-passe)</li><li>Algo que tem (um dispositivo fidedigno não for facilmente duplicado, como um telefone)</li><li>Algo que são (biometria)</li><ul>|

## <a id="anon-access-cluster"></a>Restringir o acesso anónimo ao Cluster do Service Fabric

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de fidedignidade de recursos de infraestrutura de serviço | 
| **Fase SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | Ambiente - Azure  |
| **Referências**              | [Cenários de segurança de cluster do Service Fabric](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security) |
| **Passos** | <p>Clusters devem sempre ser protegidos para impedir que utilizadores não autorizados a ligar ao seu cluster, sobretudo quando tem cargas de trabalho de produção em execução no mesmo.</p><p>Ao criar um cluster do service fabric, certifique-se de que o modo de segurança está definido como "seguro" e configurar o certificado de servidor de x. 509 necessário. Criar um cluster de "inseguras" irá permitir que qualquer utilizador anónimo ligar ao mesmo se expõe pontos finais de gestão para a Internet pública.</p>|

## <a id="fabric-cn-nn"></a>Certifique-se de que o certificado de cliente para o nó de Service Fabric é diferente do certificado do nó de nó

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de fidedignidade de recursos de infraestrutura de serviço | 
| **Fase SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | Ambiente - do Azure, ambiente - autónomo |
| **Referências**              | [Segurança de certificado de cliente para o nó de Service Fabric](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/#_client-to-node-certificate-security), [ligar a um cluster seguro utilizando o certificado de cliente](https://azure.microsoft.com/documentation/articles/service-fabric-connect-to-secure-cluster/) |
| **Passos** | <p>Configuração da segurança do certificado de cliente para o nó ao criar o cluster, quer através do portal do Azure, modelos do Resource Manager ou um modelo JSON autónomo, especificando um certificado de cliente de administrador e/ou um certificado de cliente de utilizador.</p><p>Os cliente e utilizador certificados de cliente administrativo que especificar devem ser diferentes de certificados primários e secundários que especificar para a segurança do nó ao nó.</p>|

## <a id="aad-client-fabric"></a>Utilizar o AAD para autenticar clientes para os clusters de recursos de infraestrutura de serviço

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de fidedignidade de recursos de infraestrutura de serviço | 
| **Fase SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | Ambiente - Azure |
| **Referências**              | [Cenários de segurança - recomendações de segurança do cluster](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/#security-recommendations) |
| **Passos** | Clusters em execução no Azure também podem proteger o acesso para os pontos finais de gestão utilizando o Azure Active Directory (AAD), para além dos certificados de cliente. Para os clusters do Azure, é recomendado que utilize a segurança do AAD para autenticar clientes e certificados de segurança do nó de nó.|

## <a id="fabric-cert-ca"></a>Certifique-se de que os certificados de infraestrutura de serviço são obtidos a partir de um aprovados autoridade de certificação (CA)

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de fidedignidade de recursos de infraestrutura de serviço | 
| **Fase SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | Ambiente - Azure |
| **Referências**              | [Certificados x. 509 e o Service Fabric](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/#x509-certificates-and-service-fabric) |
| **Passos** | <p>O Service Fabric utiliza certificados de servidor de x. 509 para autenticação de nós e os clientes.</p><p>Alguns pontos importantes a ter em consideração ao utilizar certificados nos recursos de infraestrutura de serviço:</p><ul><li>Certificados utilizados em clusters que executam cargas de trabalho de produção devem ser criados utilizando um serviço de certificado de servidor Windows corretamente configurado ou obtidos a partir de um aprovados autoridade de certificação (CA). A AC pode ser uma AC externa aprovada ou um corretamente geridos interno chave infraestrutura públicas (PKI)</li><li>Nunca utilize qualquer temporário ou certificados de teste em produção que são criados com ferramentas como o MakeCert.exe</li><li>Pode utilizar um certificado autoassinado, mas deve apenas fazer para clusters de teste e não na produção</li></ul>|

## <a id="standard-authn-id"></a>Utilizar cenários de autenticação padrão suportados pelo servidor de identidades

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Servidor de identidades | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [IdentityServer3 - a visão geral](https://identityserver.github.io/Documentation/docsv2/overview/bigPicture.html) |
| **Passos** | <p>Seguem-se as interações típicas suportadas pelo servidor de identidades:</p><ul><li>Browsers comunicam com aplicações web</li><li>As aplicações Web comunicam com as APIs web (por vezes, por si próprios, por vezes em nome de um utilizador)</li><li>Aplicações baseadas no browser comuniquem com as APIs web</li><li>Aplicações nativas comunicam com as APIs web</li><li>Aplicações baseadas em servidor comunicam com as APIs web</li><li>APIs da Web comunicam com as APIs web (por vezes, por si próprios, por vezes em nome de um utilizador)</li></ul>|

## <a id="override-token"></a>Ignorar a cache de token do servidor de identidades predefinido com uma alternativa dimensionável

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Servidor de identidades | 
| **Fase SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Implementação de servidores de identidade - colocação em cache](https://identityserver.github.io/Documentation/docsv2/advanced/deployment.html) |
| **Passos** | <p>IdentityServer tem uma cache de memória incorporada simple. Apesar de este é ideal para aplicações nativas de pequena escala, não dimensionar para aplicações de camada e back-end mid pelos seguintes motivos:</p><ul><li>Estas aplicações são acedidas por vários utilizadores em simultâneo. Guardar todos os tokens de acesso no mesmo arquivo cria problemas de isolamento e apresenta os desafios durante o funcionamento em escala: vários utilizadores, cada um com tokens tantos como os recursos a aplicação acede em seu nome, podem significar operações de pesquisa muito dispendiosa e enormes números</li><li>Estas aplicações, normalmente, são implementadas no topologias distribuídas, em que vários nós tem de ter acesso à cache do mesma</li><li>Tokens em cache tem continuam a vigorar após recicla de processo e deactivations</li><li>Por motivos acima, ao implementar aplicações web, é recomendado para substituir a cache de token do servidor de identidades predefinido com uma alternativa dimensionável, tais como cache de Redis do Azure</li></ul>|

## <a id="binaries-signed"></a>Certifique-se de que os binários da aplicação implementada estejam assinados digitalmente

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de fidedignidade de máquina | 
| **Fase SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Certifique-se de que os binários da aplicação implementada estejam assinados digitalmente para que possa ser verificada a integridade dos binários|

## <a id="msmq-queues"></a>Ativar a autenticação quando a ligação a filas MSMQ no WCF

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico, o NET Framework 3 |
| **Atributos**              | N/D |
| **Referências**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx) |
| **Passos** | Programa não consegue ativar a autenticação quando a ligação a filas MSMQ, um atacante anonimamente pode submeter mensagens para a fila para processamento. Se a autenticação não é utilizada para ligar a uma fila MSMQ utilizada para entregar uma mensagem ao outro programa, um atacante pode submeter uma mensagem de anónima é maliciosa.|

### <a name="example"></a>Exemplo
O `<netMsmqBinding/>` elemento do ficheiro de configuração de WCF abaixo dá instruções ao WCF para desativar a autenticação ao ligar a uma fila MSMQ para entrega de mensagens.
```
<bindings>
    <netMsmqBinding>
        <binding>
            <security>
                <transport msmqAuthenticationMode=""None"" />
            </security>
        </binding>
    </netMsmqBinding>
</bindings>
```
Configure MSMQ para exigir autenticação de domínio do Windows ou o certificado permanente para quaisquer mensagens de entrada ou de saída.

### <a name="example"></a>Exemplo
O `<netMsmqBinding/>` elemento do ficheiro de configuração de WCF abaixo dá instruções ao WCF para ativar a autenticação de certificado ao ligar a uma fila MSMQ. O cliente é autenticado utilizando certificados x. 509. O certificado de cliente tem de estar presente no arquivo de certificados do servidor.
```
<bindings>
    <netMsmqBinding>
        <binding>
            <security>
                <transport msmqAuthenticationMode=""Certificate"" />
            </security>
        </binding>
    </netMsmqBinding>
</bindings>
```

## <a id="message-none"></a>Efetue WCF mensagem clientCredentialType não definido como nenhum

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | .NET framework 3 |
| **Atributos**              | Tipo de credencial de cliente - nenhuma |
| **Referências**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify](https://vulncat.fortify.com/en/vulncat/index.html) |
| **Passos** | A ausência de autenticação significa que todas as pessoas é capaz de aceder a este serviço. Um serviço que não autentica os clientes permite o acesso a todos os utilizadores. Configure a aplicação para autenticar em relação às credenciais de cliente. Isto pode ser efetuado definindo o clientCredentialType de mensagem para Windows ou o certificado. |

### <a name="example"></a>Exemplo
```
<message clientCredentialType=""Certificate""/>
```

## <a id="transport-none"></a>Efetue WCF clientCredentialType de transporte não definido como none

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico, .NET Framework 3 |
| **Atributos**              | Tipo de credencial de cliente - nenhuma |
| **Referências**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify](https://vulncat.fortify.com/en/vulncat/index.html) |
| **Passos** | A ausência de autenticação significa que todas as pessoas é capaz de aceder a este serviço. Um serviço que não autentica os clientes permite que todos os utilizadores acedam a sua funcionalidade. Configure a aplicação para autenticar em relação às credenciais de cliente. Isto pode ser efetuado definindo o clientCredentialType de transporte para Windows ou o certificado. |

### <a name="example"></a>Exemplo
```
<transport clientCredentialType=""Certificate""/>
```

## <a id="authn-secure-api"></a>Certifique-se de que a autenticação standard técnicas são utilizadas para proteger a APIs da Web

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Autenticação e autorização na API Web do ASP.NET](http://www.asp.net/web-api/overview/security/authentication-and-authorization-in-aspnet-web-api), [serviços de autenticação externo com a API Web do ASP.NET (c#)](http://www.asp.net/web-api/overview/security/external-authentication-services) |
| **Passos** | <p>A autenticação é o processo de onde uma entidade comprova a respetiva identidade, normalmente, através de credenciais, tais como o nome de utilizador e palavra-passe. Existem vários protocolos de autenticação disponíveis que podem ser considerados. Alguns deles são listados abaixo:</p><ul><li>Certificados de cliente</li><li>Baseado no Windows</li><li>Formulários com base</li><li>Federação - ADFS</li><li>Federação - do Azure AD</li><li>Federação - servidor de identidades</li></ul><p>Ligações na secção referências fornecem detalhes de baixo nível em como cada uma dos esquemas de autenticação pode ser implementada para proteger uma API Web.</p>|

## <a id="authn-aad"></a>Utilizar cenários de autenticação padrão suportados pelo Azure Active Directory

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Azure AD | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Cenários de autenticação para o Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-authentication-scenarios/), [exemplos de código do Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-code-samples/), [guia para programadores do Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-developers-guide/) |
| **Passos** | <p>Azure Active Directory (Azure AD) simplifica a autenticação para os programadores, fornecendo identidade como um serviço, com suporte para protocolos de norma da indústria, como o OAuth 2.0 e o OpenID Connect. Seguem-se a cenários de principal da aplicação cinco suportados pelo Azure AD:</p><ul><li>Web Browser para a aplicação Web: um utilizador tem de iniciar sessão para uma aplicação web que está protegida pelo Azure AD</li><li>Aplicação de página única (SPA): Um utilizador tem de iniciar sessão para uma aplicação de página única que esteja protegida pelo Azure AD</li><li>Aplicação nativa à Web API: uma aplicação nativa que é executada num telemóvel, tablet ou PC tem de autenticar um utilizador para obter recursos de uma API web que está protegida pelo Azure AD</li><li>Aplicação Web API Web: uma aplicação web precisa de obter recursos a partir de um APIS da web protegidas pelo Azure AD</li><li>O daemon ou aplicações de servidor para a Web API: uma aplicação do daemon ou uma aplicação de servidor sem interface de utilizador web tem de obter recursos de uma API web do protegida pelo Azure AD</li></ul><p>Consulte as ligações na secção de referências para detalhes de implementação de baixo nível</p>|

## <a id="adal-scalable"></a>Substituir predefinição ADAL token cache com uma alternativa dimensionável

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Azure AD | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [A autenticação moderna no Azure Active Directory para aplicações Web](https://blogs.msdn.microsoft.com/microsoft_press/2016/01/04/new-book-modern-authentication-with-azure-active-directory-for-web-applications/), [utilizando Redis como cache de token ADAL](https://blogs.msdn.microsoft.com/mrochon/2016/09/19/using-redis-as-adal-token-cache/)  |
| **Passos** | <p>A cache de predefinido que utiliza a ADAL (Active Directory Authentication Library) é uma cache de memória que depende de um arquivo estático, nível de processo disponível. Embora isto funciona para aplicações nativas, não dimensionar para aplicações de camada e back-end mid pelos seguintes motivos:</p><ul><li>Estas aplicações são acedidas por vários utilizadores em simultâneo. Guardar todos os tokens de acesso no mesmo arquivo cria problemas de isolamento e apresenta os desafios durante o funcionamento em escala: vários utilizadores, cada um com tokens tantos como os recursos a aplicação acede em seu nome, podem significar operações de pesquisa muito dispendiosa e enormes números</li><li>Estas aplicações, normalmente, são implementadas no topologias distribuídas, em que vários nós tem de ter acesso à cache do mesma</li><li>Tokens em cache tem continuam a vigorar após recicla de processo e deactivations</li></ul><p>Por motivos acima, ao implementar aplicações web, recomenda-para substituir a predefinição ADAL token cache com uma alternativa dimensionável, tais como cache de Redis do Azure.</p>|

## <a id="tokenreplaycache-adal"></a>Certifique-se de que TokenReplayCache é utilizada para impedir a reprodução de tokens de autenticação da ADAL

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Azure AD | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Autenticação moderna com o Azure Active Directory para aplicações Web](https://blogs.msdn.microsoft.com/microsoft_press/2016/01/04/new-book-modern-authentication-with-azure-active-directory-for-web-applications/) |
| **Passos** | <p>A propriedade TokenReplayCache permite aos programadores definir uma cache de reprodução de tokens, um arquivo que pode ser utilizado para guardar os tokens para fins de verificar que nenhum token pode ser utilizado mais do que uma vez.</p><p>Esta é uma medida contra um ataque comuns, ataque de reprodução de tokens aptly chamado: um intruso intercetar o token enviado no início de sessão poderá tentar enviá-lo novamente para a aplicação ("repetição"-la) para estabelecer uma sessão de novo. Por exemplo, o fluxo de concessão do código no OIDC, após a autenticação com êxito do utilizador, um pedido para "/ oidc de início de sessão" ponto final da entidade confiadora é feita com "id_token", "code" e "Estado" parâmetros.</p><p>A entidade confiadora valida este pedido e estabelece uma sessão de novo. Se um adversário este pedido de captura e, este partirá, he/she pode estabelecer uma sessão com êxito e falsificar o utilizador. A presença de nonce no OpenID Connect pode limitar mas não totalmente eliminar circunstâncias nas quais se pode enacted com êxito o ataque. Para proteger as aplicações, os programadores podem fornecer uma implementação de ITokenReplayCache e atribuir uma instância TokenReplayCache.</p>|

### <a name="example"></a>Exemplo
```C#
// ITokenReplayCache defined in ADAL
public interface ITokenReplayCache
{
bool TryAdd(string securityToken, DateTime expiresOn);
bool TryFind(string securityToken);
}
```

### <a name="example"></a>Exemplo
Eis uma implementação de exemplo da ITokenReplayCache interface. (. Personalizar e implementar a sua arquitetura de colocação em cache específicas ao projecto)
```C#
public class TokenReplayCache : ITokenReplayCache
{
    private readonly ICacheProvider cache; // Your project-specific cache provider
    public TokenReplayCache(ICacheProvider cache)
    {
        this.cache = cache;
    }
    public bool TryAdd(string securityToken, DateTime expiresOn)
    {
        if (this.cache.Get<string>(securityToken) == null)
        {
            this.cache.Set(securityToken, securityToken);
            return true;
        }
        return false;
    }
    public bool TryFind(string securityToken)
    {
        return this.cache.Get<string>(securityToken) != null;
    }
}
```
A cache implementada tem de ser referenciados nas opções de OIDC através da propriedade "TokenValidationParameters" da seguinte forma.
```C#
OpenIdConnectOptions openIdConnectOptions = new OpenIdConnectOptions
{
    AutomaticAuthenticate = true,
    ... // other configuration properties follow..
    TokenValidationParameters = new TokenValidationParameters
    {
        TokenReplayCache = new TokenReplayCache(/*Inject your cache provider*/);
    }
}
```

Tenha em atenção que para testar a eficácia nesta configuração, o início de sessão na sua aplicação protegida OIDC local e o pedido de captura `"/signin-oidc"` ponto final no fiddler. Quando a proteção não está no local, replaying este pedido no fiddler irá definir um novo cookie de sessão. Quando o pedido é reproduzido após a proteção de TokenReplayCache é adicionada, a aplicação irá gerar uma exceção da seguinte forma:`SecurityTokenReplayDetectedException: IDX10228: The securityToken has previously been validated, securityToken: 'eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ1......`

## <a id="adal-oauth2"></a>Utilizar as bibliotecas ADAL para gerir pedidos de token de OAuth2 clientes para o AAD (ou AD no local)

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Azure AD | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [ADAL](https://azure.microsoft.com/documentation/articles/active-directory-authentication-libraries/) |
| **Passos** | <p>A autenticação do Azure AD Library (ADAL) permite aos programadores de aplicações de cliente autenticar facilmente os utilizadores na nuvem ou no local do Active Directory (AD) e, em seguida, obter os tokens de acesso para proteger a chamadas de API.</p><p>ADAL tem muitas funcionalidades de que marca a autenticação mais fácil para os programadores, como o suporte assíncrono, de uma cache de token configurável que armazena os tokens de acesso e atualização de tokens, atualização automática de token quando um token de acesso expira e um token de atualização está disponível, e mais.</p><p>Ao processar a maioria da complexidade, ADAL pode ajudar um focarem-se do programador em lógica de negócio na respetiva aplicação e facilmente proteger recursos sem estar um especialista em segurança. Separado bibliotecas estão disponíveis para .NET, JavaScript (cliente e Node.js), iOS, Android e Java.</p>|

## <a id="authn-devices-field"></a>Autenticar a ligação para o Gateway de campo de dispositivos

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway de campo de IoT | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Certifique-se de que cada dispositivo é autenticado pelo Gateway de campo antes de a aceitar dados dos mesmos e antes do mesmo a montante comunicações com o Gateway de nuvem. Além disso, certifique-se de que os dispositivos se ligam com um por dispositivo para que os dispositivos individuais podem ser identificados exclusivamente de credenciais.|

## <a id="authn-devices-cloud"></a>Certifique-se de que os dispositivos que se ligam ao gateway de nuvem são autenticados

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway de nuvem do IoT | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico, c#, Node.JS,  |
| **Atributos**              | N/d, escolha de Gateway - IoT Hub do Azure |
| **Referências**              | N/d, [hub IoT do Azure com o .NET](https://azure.microsoft.com/documentation/articles/iot-hub-csharp-csharp-getstarted/), [introdução wih IoT hub e o nó JS](https://azure.microsoft.com/documentation/articles/iot-hub-node-node-getstarted), [proteger IoT com SAS e os certificados](https://azure.microsoft.com/documentation/articles/iot-hub-sas-tokens/), [repositório de Git](https://github.com/Azure/azure-iot-sdks/tree/master/node) |
| **Passos** | <ul><li>**Genérico:** autenticar o dispositivo utilizar IPSec ou o Transport Layer Security (TLS). Infraestrutura deve suportar a utilização de chave pré-partilhada (PSK) nesses dispositivos que não é possível processar a criptografia completa assimétrica. Tire partido do Azure AD, Oauth.</li><li>**C#:** ao criar uma instância de DeviceClient, por predefinição, o método Create cria uma instância de DeviceClient que utiliza o protocolo AMQP para comunicar com o IoT Hub. Para utilizar o protocolo HTTPS, utilize a substituição do método de criação que permite-lhe especificar o protocolo. Se utilizar o protocolo HTTPS, deve também adicionar o `Microsoft.AspNet.WebApi.Client` pacote NuGet ao seu projeto para incluir o `System.Net.Http.Formatting` espaço de nomes.</li></ul>|

### <a name="example"></a>Exemplo
```C#
static DeviceClient deviceClient;

static string deviceKey = "{device key}";
static string iotHubUri = "{iot hub hostname}";

var messageString = "{message in string format}";
var message = new Message(Encoding.ASCII.GetBytes(messageString));

deviceClient = DeviceClient.Create(iotHubUri, new DeviceAuthenticationWithRegistrySymmetricKey("myFirstDevice", deviceKey));

await deviceClient.SendEventAsync(message);
```

### <a name="example"></a>Exemplo
**Node.JS: autenticação**
#### <a name="symmetric-key"></a>Chave simétrica
* Criar um hub IoT no azure
* Criar uma entrada no registo de identidade do dispositivo
    ```javascript
    var device = new iothub.Device(null);
    device.deviceId = <DeviceId >
    registry.create(device, function(err, deviceInfo, res) {})
    ```
* Criar um dispositivo simulado
    ```javascript
    var clientFromConnectionString = require('azure-iot-device-amqp').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    var connectionString = 'HostName=<HostName>DeviceId=<DeviceId>SharedAccessKey=<SharedAccessKey>';
    var client = clientFromConnectionString(connectionString);
    ```
#### <a name="sas-token"></a>SAS Token
* Obtém geradas internamente ao utilizar a chave simétrica, mas iremos pode gerar e utilizá-lo explicitamente bem
* Defina um protocolo de:`var Http = require('azure-iot-device-http').Http;`
* Crie um token sas:
    ```javascript
    resourceUri = encodeURIComponent(resourceUri.toLowerCase()).toLowerCase();
    var deviceName = "<deviceName >";
    var expires = (Date.now() / 1000) + expiresInMins * 60;
    var toSign = resourceUri + '\n' + expires;
    // using crypto
    var decodedPassword = new Buffer(signingKey, 'base64').toString('binary');
    const hmac = crypto.createHmac('sha256', decodedPassword);
    hmac.update(toSign);
    var base64signature = hmac.digest('base64');
    var base64UriEncoded = encodeURIComponent(base64signature);
    // construct authorization string
    var token = "SharedAccessSignature sr=" + resourceUri + "%2fdevices%2f"+deviceName+"&sig="
    + base64UriEncoded + "&se=" + expires;
    if (policyName) token += "&skn="+policyName;
    return token;
    ```
* Ligar utilizando o sas token: 
    ```javascript
    Client.fromSharedAccessSignature(sas, Http); 
    ```
#### <a name="certificates"></a>Certificados
* Gerar um X509 assinado automática de certificado utilizando qualquer ferramenta tais como OpenSSL para gerar um ficheiros .cert e .key para armazenar o certificado e a chave, respetivamente
* Aprovisione um dispositivo que aceita ligação segura através de certificados.
    ```javascript
    var connectionString = '<connectionString>';
    var registry = iothub.Registry.fromConnectionString(connectionString);
    var deviceJSON = {deviceId:"<deviceId>",
    authentication: {
        x509Thumbprint: {
        primaryThumbprint: "<PrimaryThumbprint>",
        secondaryThumbprint: "<SecondaryThumbprint>"
        }
    }}
    var device = deviceJSON;
    registry.create(device, function (err) {});
    ```
* Ligar um dispositivo ao utilizar um certificado
    ```javascript
    var Protocol = require('azure-iot-device-http').Http;
    var Client = require('azure-iot-device').Client;
    var connectionString = 'HostName=<HostName>DeviceId=<DeviceId>x509=true';
    var client = Client.fromConnectionString(connectionString, Protocol);
    var options = {
        key: fs.readFileSync('./key.pem', 'utf8'),
        cert: fs.readFileSync('./server.crt', 'utf8')
    }; 
    // Calling setOptions with the x509 certificate and key (and optionally, passphrase) will configure the client //transport to use x509 when connecting to IoT Hub
    client.setOptions(options);
    //call fn to execute after the connection is set up
    client.open(fn);
    ```

## <a id="authn-cred"></a>Utilizar as credenciais de autenticação por dispositivo

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway de nuvem do IoT  | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | Opção de gateway - IoT Hub do Azure |
| **Referências**              | [Tokens de segurança de IoT Hub do Azure](https://azure.microsoft.com/documentation/articles/iot-hub-sas-tokens/) |
| **Passos** | Utilize pelas credenciais de autenticação de dispositivo utilizando SaS tokens com base na chave do dispositivo ou o certificado de cliente, em vez de IoT Hub nível partilhado políticas de acesso. Isto impede a reutilização de tokens de autenticação de um gateway de campo ou dispositivo por outro |

## <a id="req-containers-anon"></a>Certifique-se de que apenas os contentores necessários e os blobs recebem acesso de leitura anónimo

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Storage do Azure | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | StorageType - BLOBs |
| **Referências**              | [Gerir o acesso de leitura anónimo a contentores e blobs](https://azure.microsoft.com/documentation/articles/storage-manage-access-to-resources/), [assinaturas de acesso partilhado, parte 1: compreender o modelo SAS](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/) |
| **Passos** | <p>Por predefinição, um contentor e blobs dentro do mesmo podem ser acedidos apenas pelo proprietário da conta de armazenamento. Para atribuir utilizadores anónimos permissões de leitura para um contentor e os respetivos blobs, um pode definir as permissões de contentor para permitir o acesso público. Utilizadores anónimos podem ler os blobs num contentor acessível publicamente sem autenticar o pedido.</p><p>Contentores fornecem as seguintes opções para gerir o acesso do contentor:</p><ul><li>Total de acesso de leitura público: dados blob e contentor podem ser lidos através do pedido anónimo. Os clientes podem enumerar os blobs no contentor através do pedido anónimo, mas não é possível enumerar os contentores na conta de armazenamento.</li><li>Acesso para blobs só de leitura de público: dados BLOBs neste contentor podem ser lidos através do pedido anónimo, mas não estão disponíveis dados de contentor. Os clientes não é possível enumerar os blobs no contentor via pedidos anónimos</li><li>Acesso de leitura não público: dados blob e contentor podem ser lida por apenas o proprietário da conta</li></ul><p>Acesso anónimo é melhor para cenários em que determinados blobs devem ser sempre disponíveis para acesso de leitura anónimo. Para o controlo de bloqueio, um pode criar uma assinatura de acesso partilhado, o que permite acesso de delegado restringido com permissões diferentes e ao longo de um intervalo de tempo especificado. Certifique-se de que contentores e blobs, que potencialmente podem conter dados confidenciais, não recebem acesso anónimo acidentalmente</p>|

## <a id="limited-access-sas"></a>Conceder acesso limitado para os objetos no storage do Azure através de SAS ou SAP

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Storage do Azure | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D |
| **Referências**              | [Assinaturas de acesso partilhado, parte 1: Compreender o modelo SAS](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/), [assinaturas de acesso partilhado, parte 2: criar e utilizar um SAS com o Blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-2/), [como delegar o acesso a objetos na sua conta utilizando partilhados Assinaturas de acesso e políticas de acesso armazenada](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_how-to-delegate-access-to-objects-in-your-account-using-shared-access-signatures-and-stored-access-policies) |
| **Passos** | <p>Utilizar uma assinatura de acesso partilhado (SAS) é uma forma poderosa para conceder acesso limitado a objetos numa conta do storage para outros clientes, sem ter de expor a chave de acesso da conta. O SAS é um URI que abrange nos respetivos parâmetros de consulta todas as informações necessárias para autenticar o acesso a um recurso de armazenamento. Para aceder aos recursos de armazenamento com a SAS, o cliente só tem de passar a SAS para o método ou construtor adequado.</p><p>Pode utilizar um SAS quando se pretende fornecer acesso a recursos na sua conta de armazenamento para um cliente que não pode ser considerados fidedigno com a chave de conta. As chaves de conta de armazenamento incluem-se de uma chave primária e secundária, que conceda acesso administrativo à sua conta e todos os recursos nele. A exposição de qualquer uma das suas chaves de conta, abre-se a possibilidade de utilização por acidente ou maliciosa na sua conta. Assinaturas de acesso partilhado fornecem uma alternativa segura que permite aos outros clientes de leitura, escrita e eliminação de dados na sua conta de armazenamento, de acordo com as permissões que tiver concedidas e sem a necessidade para a chave de conta.</p><p>Se tiver um conjunto lógico de parâmetros que são semelhantes a cada hora, utilizar uma política de acesso armazenada (SAP) é uma ideia mais precisa. Uma vez com uma SAS derivada de uma política de acesso armazenada dá-lhe a capacidade para revogar esse SAS imediatamente, é recomendável utilizar sempre armazenadas as políticas de acesso sempre que possível.</p>|