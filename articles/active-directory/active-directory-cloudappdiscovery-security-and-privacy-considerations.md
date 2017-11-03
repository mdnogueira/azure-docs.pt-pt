---
title: "Considerações de privacidade e segurança de deteção de aplicação de nuvem | Microsoft Docs"
description: "Este tópico descreve as considerações de segurança e privacidade relacionadas com a Cloud App Discovery."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 2fce5c82-d3de-4097-808f-40214768df9e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: cec3d2cb02dd34dd5ac631e572936cfd7c8de033
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="cloud-app-discovery-security-and-privacy-considerations"></a>Considerações de privacidade e segurança do cloud App Discovery
Este tópico explica como os dados são recolhidos, processados e protegidos no Azure Active Directory Cloud App Discovery. A Microsoft está empenhada em proteger a sua privacidade e proteger os seus dados. Microsoft respeita a proteger o desenvolvimento de software do ciclo de vida práticas para o funcionamento de um serviço. Proteger e a proteção de dados é uma prioridade superior na Microsoft.

> [!TIP] 
> Conheça a nova sem agente Cloud App Discovery no Azure Active Directory (Azure AD), que são melhorados por [integração com o Microsoft Cloud App Security](https://portal.cloudappsecurity.com). 

## <a name="overview"></a>Descrição geral
O cloud App Discovery é uma funcionalidade do Azure AD e está alojada no Microsoft Azure.  
O endpoint agent do Cloud App Discovery é utilizado para recolher dados de deteção de aplicação a partir de máquinas IT gerido. Os dados recolhidos são enviados em segurança num canal encriptado para o serviço Azure AD Cloud App Discovery. Os dados da Cloud App Discovery para uma organização, em seguida, são visíveis no portal do Azure. 

![Como funciona a Cloud App Discovery](./media/active-directory-cloudappdiscovery-security-and-privacy-considerations/cad01.png) 

As secções seguintes seguem o fluxo seguro de informações da sua organização para o serviço de Cloud App Discovery e, em última análise ao portal da Cloud App Discovery.

## <a name="collecting-data-from-your-organization"></a>Recolha de dados da sua organização
Para utilizar a funcionalidade de deteção de aplicação na nuvem do Azure Active Directory para obter informações sobre as aplicações utilizadas pelos empregados na sua organização, terá de implementar primeiro o agente do Azure AD Cloud App Discovery endpoint em computadores na sua organização.

Os administradores de inquilino do Azure Active Directory (ou os respetivos delegado) podem transferir o pacote de instalação do agente do portal do Azure. O agente pode ser manualmente instalado ou instalado em várias máquinas na organização através do SCCM ou política de grupo.

Para obter mais instruções sobre as opções de implementação, consulte [guia de implementação de política de grupo deteção aplicações Cloud](http://social.technet.microsoft.com/wiki/contents/articles/30965.cloud-app-discovery-group-policy-deployment-guide.aspx).


### <a name="data-collected-by-the-agent"></a>Dados recolhidos pelo agente
As informações descritas na lista seguinte são recolhidas pelo agente quando é efetuada uma ligação a uma aplicação Web. As informações são recolhidas apenas para as aplicações que o administrador tiver configurado para a deteção. Pode editar a lista de aplicações em nuvem que o agente monitoriza através de Cloud App Discovery no Azure AD no Microsoft [portal do Azure](https://portal.azure.com/), em **definições**->**recolha de dados**  -> **Lista de coleção de aplicações**. 

**Categoria de informações**: informações do utilizador  
**Descrição**: nome de utilizador do Windows do processo que efetuou um pedido para o destino da aplicação Web (por exemplo, domínio \ nomedeutilizador), bem como o identificador de segurança do Windows (SID) do utilizador.

**Categoria de informações**: processar as informações  
**Descrição**: O nome do processo que efetuou o pedido para o destino da aplicação Web (por exemplo, iexplore.exe)

**Categoria de informações**: informações do computador  
**Descrição**: nome da NetBIOS do computador em que o agente está instalado.

**Categoria de informações**: informações de tráfego de aplicações  
**Descrição**: as seguintes informações de ligação:

* A origem (computador local) e endereços IP de destino e de números de porta
* O endereço IP público da organização através do qual o pedido sai.
* O tempo do pedido
* O volume de tráfego enviado e recebido
* A versão do IP (4 ou 6)
* TLS apenas para ligações: O nome de anfitrião de destino a extensão de indicação do nome de servidor ou o certificado de servidor.

As seguintes informações de HTTP:

* Método (GET, POST, etc.)
* Protocolo (HTTP/1.1, etc.)
* Cadeia de agente do utilizador
* Nome de anfitrião
* O URI (excluindo a cadeia de consulta) de destino
* Informações de tipo de conteúdo
* Informações de URL de referência (excluindo a cadeia de consulta)

> [!NOTE]
> As informações de HTTP acima são recolhidas para todas as ligações não encriptada.
> Para ligações TLS, estas informações apenas são capturadas quando a definição de 'Inspeção profunda' está ativada no portal. A definição é 'ON' por predefinição.
> Para obter mais detalhes, consulte abaixo, e [obter iniciado com o Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx)
> 
> 

Para além dos dados que o agente recolhe sobre a atividade de rede, também recolhe dados anónimos sobre
* Configuração de hardware e software
* Relatórios de erros
* Dados na forma como o agente está a ser utilizado.


### <a name="how-the-agent-works"></a>Como funciona o agente
A instalação do agente inclui dois componentes:

* Um componente de modo de utilizador
* Um componente de controlador do modo de kernel (controlador de plataforma de filtragem do Windows)

Quando o agente é instalado pela primeira vez armazena um certificado fidedigno máquina específicos no computador que utiliza, em seguida, para estabelecer uma ligação segura com o serviço de Cloud App Discovery. O agente periodicamente obtém a configuração de política do serviço de Cloud App Discovery através desta ligação segura. A política inclui informações sobre as aplicações em nuvem para monitorizar e se a atualização automática deve ser ativada, entre outras coisas.

Como o tráfego Web seja enviado e recebido na máquina do Internet Explorer e o Chrome, o agente de Cloud App Discovery analisa o tráfego e extrai metadados relevantes (consulte o **dados recolhidos pelo agente** secção acima).  
Cada minuto, o agente carrega os metadados recolhidos para o serviço de Cloud App Discovery num canal encriptado.

O componente de controlador interceta o tráfego encriptado e insere-se para o fluxo encriptado. Obter mais detalhes no **Intercetar dados encriptados ligações (a inspeção)** secção abaixo.

### <a name="respecting-user-privacy"></a>Respecting privacidade do utilizador
O nosso objetivo é fornecer administradores as ferramentas para definir o equilíbrio entre optics detalhadas para privacidade de utilização e o utilizador de aplicação conforme adequado para a sua organização. Para esse fim, fornecemos os botões seguintes na página de definições no Portal:

* **Recolha de dados**: os administradores podem optar por especificar que pretendem obter dados de deteção de categorias de aplicação ou aplicações.
* **A inspeção**: os administradores podem optar por especificar se o agente recolhe tráfego HTTP para ligações SSL/TLS (aka **'Inspeção profunda'**). Obter mais informações sobre este na secção seguinte.
* **Opções de consentimento**: os administradores podem utilizar o portal de Cloud App Discovery escolher se pretende notificar os utilizadores de coleção de dados pelo agente e, se requer utilizador consentimento antes do início de agente recolha de dados de utilizador.

O endpoint agent do Cloud App Discovery só recolhe as informações descritas no **dados recolhidos pelo agente** secção acima.

### <a name="intercepting-data-from-encrypted-connections-deep-inspection"></a>A intercetar dados encriptados ligações (a inspeção total)
Conforme foi mencionado anteriormente, os administradores podem configurar o agente a monitorizar os dados de ligações encriptados ('a inspeção total'). TLS ([Transport Layer Security](https://msdn.microsoft.com/library/windows/desktop/aa380516%28v=vs.85%29.aspx)) é um dos protocolos mais comuns em utilização na Internet de hoje. Ao encriptar a comunicação com TLS, um cliente pode estabelecer um canal de comunicação segura e privada com um servidor web; TLS fornece proteção essencial para passar as credenciais de autenticação e impedir a divulgação de informações confidenciais.

Enquanto o ponto-a-ponto encriptado canal seguro fornecido pelo TLS permite proteção de privacidade e segurança importante, o protocolo é frequentemente abused para fins de possui conteúdo nefasto ou maliciosos. Muitas, por isso, na verdade, esse TLS é frequentemente referido como o protocolo"universal firewall-ignorar". A raiz do problema é que a maioria das firewalls são não é possível inspecionar a comunicação de TLS porque os dados de camada de aplicação são encriptados com SSL. Saber isto, os atacantes frequentemente tirar partido do TLS para fornecer payloads maliciosos a um utilizador a certeza de que, mesmo as firewalls de camada de aplicação mais inteligentes estão completamente oculta a TLS e tem de reencaminhamento simplesmente comunicação TLS entre anfitriões. Os utilizadores finais frequentemente tirar partido do TLS para controlos de acesso impostos por firewalls empresariais e servidores proxy, ignorar a utilizar para ligar proxies públicas e para os protocolos TLS não através da firewall que caso contrário, poderá ser bloqueado pela política de túnel.

A inspeção total permite ao agente de Cloud App Discovery atuar como um fidedigna ataques man-in-the-middle. Quando é efetuado um pedido de cliente para aceder a um recurso protegido de HTTPS, o controlador de agente do Endpoint interceta a ligação e estabelece que uma nova ligação para o servidor de destino para obtém o respetivo certificado SSL em nome do cliente. O agente, em seguida, verifica que o pode confiar no certificado (verificar que não foi revogado e efetuar outras verificações de certificado) e, se estes passagem, o agente de ponto final, em seguida, copia as informações do certificado de servidor e cria o seu próprio servidor certificado – ao conhecido como um certificado de interception – utilizar essas informações. O certificado de interception está assinado no-a-momento pelo agente de ponto final com um certificado de raiz, que é instalado no arquivo de certificados fidedignos do Windows. Este certificado de raiz autoassinado está marcada como não exportável e é ACL teve de administradores. Destina-se para nunca deixar a máquina em que foi criada. Quando a aplicação de cliente do utilizador final recebe o certificado de interception, este confie-la porque pode validar com êxito a cadeia de certificados para o certificado de raiz. Este processo é principalmente transparente do ponto de vista de uma-do utilizador final com algumas limitações, conforme descrito abaixo.

Ao ativar a inspeção total, o Cloud App Discovery Endpoint Agent pode desencriptar e inspecionar comunicações TLS encriptados, permitindo que o serviço para reduzir o ruído e fornecem informações sobre a utilização das aplicações cloud encriptados.

#### <a name="a-word-of-caution"></a>Uma palavra de atenção
Antes de ativar a inspeção total, este é vivamente sugerida que comunicar as intenções às suas informações legais e departamentos de RH e obter o seu consentimento. Inspecionar comunicações encriptadas de privado do utilizador final pode ser um assunto confidencial, por motivos de óbvios. Antes de uma agregação de produção-out de inspeção profunda, efetuar determinadas que a segurança da empresa e políticas de uso aceitável foram atualizadas para indicar que irão ser inspecionada comunicações encriptadas. Notificação do utilizador e exclusão de sites consideradas confidenciais (por exemplo, banking e médicas sites) também poderão ser necessários se configurar a Cloud App Discovery a monitorizá-las. Tal como mencionado acima, os administradores podem utilizar o portal de Cloud App Discovery para escolher notificar os utilizadores da coleção de dados pelo agente e se requer o consentimento do utilizador antes do agente inicia a recolha de dados de utilizador.

### <a name="known-issues-and-drawbacks"></a>Problemas conhecidos e desvantagens
Existem alguns casos onde TLS interception pode afetar a experiência de utilizador final:

* Certificados de validação (EV) expandida compor a barra de endereço do browser verde para atuar como um visual clue que são visitam um web site fidedigno. Inspeção TLS não é possível duplicar EV no certificado que emite para o cliente, para que web sites que utilizam certificados EV funcionarão normalmente, mas a barra de endereço não apresentará a verde.  
* Afixação chave do público (também conhecido como afixação de certificado) foram concebidos para ajudar a proteger os utilizadores de ataques man-in-the-middle e não autorizados autoridades de certificação. Quando o certificado de raiz de um site afixado corresponde a uma da AC boa conhecida, o browser rejeita a ligação com um erro. Uma vez que TLS interception, na verdade, uma ataques man-in-the-middle, estas ligações irão falhar.
* Se os utilizadores clicarem no ícone de bloqueio no browser de barra de endereço do browser para inspecionar as informações do site, não verá uma cadeia que termine em autoridade de certificação utilizada para assinar o certificado de Web site, mas em vez disso, uma cadeia de certificados que termina com o Windows fidedigno arquivo de certificados.

Para reduzir as ocorrências destes problemas, controlamos serviços em nuvem e aplicações de cliente conhecidas por utilizar validação expandida ou afixação de chave pública e instruir o agente de ponto final para evitar a intercetar ligações afetadas. Mesmo nestes casos, no entanto, ainda receberá relatórios de utilização destas aplicações na nuvem e o volume de dados a serem transferidos, mas uma vez que não são profundas inspecioná-los, não existem detalhes sobre como as aplicações foram utilizadas estará disponíveis.

## <a name="sending-data-to-cloud-app-discovery"></a>Enviar dados para a Cloud App Discovery
Depois dos metadados foram recolhidos pelo agente, este é colocado em cache no computador para até um minuto ou até que os dados em cache atingirem um tamanho de 5MB. Em seguida, é comprimido e enviado através de uma ligação segura para o serviço de Cloud App Discovery.

Se o agente não consegue comunicar com o serviço de Cloud App Discovery por qualquer motivo, os metadados recolhidos é armazenado numa cache de ficheiros local que só pode ser acedida pelos utilizadores com privilégios no computador (por exemplo, o grupo de administradores).  
O agente tenta automaticamente reenviar os metadados em cache até com êxito foram recebido pelo serviço de Cloud App Discovery.

## <a name="receiving-the-data-at-the-service-end"></a>Receber os dados no final de serviço
Os agentes de autenticar para a Cloud App Discovery serviço utilizando o certificado de autenticação de cliente específico do computador referenciado acima e reencaminha dados num canal encriptado.  
Pipeline de análise do serviço Cloud App Discovery processa os metadados para cada cliente em separado por logicamente a criação de partições mesmo através de todas as fases do pipeline de análise.
Os metadados analisados unidades vários relatórios no portal.

Os metadados não processados e os metadados analisados são armazenadas durante 180 dias. Além disso, os clientes podem optar por capturar os metadados analisado numa conta de armazenamento de Blobs do Azure à sua escolha.
Isto é útil para análise offline de metadados, bem como tempo retenção dos dados.

## <a name="accessing-the-data-using-the-azure-portal"></a>Aceder aos dados no portal do Azure
Na tentativa de manter os metadados recolhidos segura, por predefinição, apenas os administradores globais do inquilino tem acesso para a funcionalidade de Cloud App Discovery no portal do Azure.  
No entanto, os administradores podem optar por delegar o acesso a outros utilizadores ou grupos.

> [!NOTE]
> Para obter mais detalhes, consulte [obter iniciado com o Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx)
> 
> 


Qualquer utilizador aceder aos dados no portal, tem de estar licenciados com uma licença do Azure AD Premium.

## <a name="additional-resources"></a>Recursos Adicionais
* [Como pode detetar aplicações na nuvem não sancionadas utilizadas dentro da minha organização](active-directory-cloudappdiscovery-whatis.md)
* [Índice de Artigos da Gestão da Aplicação no Azure Active Directory](active-directory-apps-index.md)

