---
title: "Guia de resolução de problemas de Explorador de armazenamento do Azure | Microsoft Docs"
description: "Descrição geral dos dois depuração funcionalidade do Azure"
services: virtual-machines
documentationcenter: 
author: Deland-Han
manager: cshepard
editor: 
ms.assetid: 
ms.service: virtual-machines
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/08/2017
ms.author: delhan
ms.openlocfilehash: e06c73c2c00b27178f8431b83b5c5a42110b6b1e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Guia de resolução de problemas de Explorador de armazenamento do Azure

Explorador de armazenamento do Microsoft Azure (pré-visualização) é uma aplicação autónoma que lhe permite trabalhar facilmente com dados de armazenamento do Azure no Windows, macOS e Linux. A aplicação pode ligar a contas de armazenamento alojadas no Azure, Sovereign nuvens e pilha do Azure.

Este guia resume soluções para problemas comuns vistos no Explorador de armazenamento.

## <a name="sign-in-issues"></a>Inicie sessão no problemas

São suportadas apenas contas do Azure Active Directory (AAD). Se utilizar uma conta do AD FS, é esperado que o início de sessão Explorador de armazenamento não funciona. Antes de continuar, tente reiniciar a aplicação e se os problemas podem ser corrigido.

### <a name="error-self-signed-certificate-in-certificate-chain"></a>Erro: O certificado Autoassinado na cadeia de certificados

Existem vários motivos por que razão poderá encontrar este erro, e as razões mais comuns de dois são os seguintes:

1. A aplicação está ligada através de "proxy transparente", que significa um servidor (por exemplo, o servidor da empresa) é a intercetar tráfego HTTPS, desencriptação-lo e, em seguida, encriptá-la através de um certificado autoassinado.

2. Está a executar uma aplicação, tal como software antivírus, o que é inserirem-se um certificado SSL autoassinado para as mensagens HTTPS recebidos.

Quando o Explorador de armazenamento encontra um dos problemas, pode já não sabe se a mensagem recebida do HTTPS é adulterada. Se tiver uma cópia do certificado autoassinado, pode deixar o Explorador de armazenamento confiar nele. Se não souber de que é inserirem-se o certificado, siga estes passos para encontrá-lo:

1. Instalar SSL aberta

    - [Windows](https://slproweb.com/products/Win32OpenSSL.html) (qualquer uma das versões leves deve ser suficiente)

    - Mac e Linux: devem ser incluídos com o sistema operativo

2. Executar SSL aberta

    - Windows: Abra o diretório de instalação, clique em **/bin/**e, em seguida, faça duplo clique **openssl.exe**.
    - Mac e Linux: executar **openssl** de um terminal.

3. Executar s_client - showcerts-ligar microsoft.com:443

4. Procure certificados autoassinados. Se não souber qual são autoassinados, procure em qualquer lugar do assunto ("s:") e o emissor ("i:") são os mesmos.

5. Quando encontrar quaisquer certificados autoassinados, para cada um, copie e cole tudo a partir e incluindo **---BEGIN CERTIFICATE---** para **---fim certificado---** para um novo ficheiro. cer.

6. Abra o Explorador de armazenamento, clique em **editar** > **certificados SSL** > **importar certificados**e, em seguida, utilize o Seletor de ficheiros para localizar, selecione e abra os ficheiros. cer que criou.

Se não encontrar quaisquer certificados autoassinados, utilizando os passos acima, contacte-nos através da ferramenta de comentários para obter mais ajuda.

### <a name="unable-to-retrieve-subscriptions"></a>Não é possível obter subscrições

Se não for possível obter as suas subscrições, depois de se inscrever com êxito, siga estes passos para resolver este problema:

- Certifique-se de que a conta tem acesso às subscrições inscrevendo-os no portal do Azure.

- Certifique-se de que se inscreveu no utilizando o ambiente correto (do Azure, Azure China, Datacenters do Azure, Azure US Government ou pilha de ambiente/Azure personalizado).

- Se estiver atrás de um proxy, certifique-se de que configurou o proxy do Explorador de armazenamento corretamente.

- Tente remover e readding a conta.

- Tente eliminar os seguintes ficheiros do diretório de raiz (ou seja, C:\Users\ContosoUser) e, em seguida, voltar a adicionar a conta:

    - .adalcache

    - .devaccounts

    - .extaccounts

- Veja as ferramentas de programador da consola (por prima F12) quando iniciar sessão para as mensagens de erro:

![Ferramentas de programador](./media/storage-explorer-troubleshooting/4022501_en_2.png)

### <a name="unable-to-see-the-authentication-page"></a>Não é possível ver a página de autenticação

Se não for possível ver a página de autenticação, siga estes passos para resolver este problema:

- Consoante a velocidade da sua ligação, este poderá demorar algum tempo para a página de início de sessão carregar, aguarde, pelo menos, um minuto antes de fechar a caixa de diálogo de autenticação.

- Se estiver atrás de um proxy, certifique-se de que configurou o proxy do Explorador de armazenamento corretamente.

- Ver a consola de programador, premindo a tecla F12. Veja as respostas a partir da consola do programador e veja se pode encontrar qualquer clue porquê autenticação não está a funcionar.

### <a name="cannot-remove-account"></a>Não é possível remover a conta

Se não for possível remover uma conta, ou se a ligação reautenticação fazer nada, siga estes passos para resolver este problema:

- Tente eliminar os seguintes ficheiros do diretório de raiz e, em seguida, readding a conta:

    - .adalcache

    - .devaccounts

    - .extaccounts

- Se pretender remover SAS ligado recursos de armazenamento, elimine os seguintes ficheiros:

    - Pasta de %AppData%/StorageExplorer para Windows

    - /Users/ < your_name >/biblioteca/Applicaiton suporte/StorageExplorer para Mac

    - ~/.config/StorageExplorer para Linux

> [!NOTE]
>  Terá de reintroduzir as suas credenciais, se eliminar estes ficheiros.

## <a name="proxy-issues"></a>Problemas de proxy

Em primeiro lugar, certifique-se de que as seguintes informações que introduziu estão corretos:

- O URL de proxy e o número de porta

- Nome de utilizador e palavra-passe se for necessário pelo proxy

### <a name="common-solutions"></a>Soluções comuns

Se ainda ocorrerem problemas, siga estes passos para resolvê-los:

- Se pode ligar à Internet sem utilizar o proxy, certifique-se de que o Explorador de armazenamento funciona sem as definições de proxy ativadas. Se for este o caso, poderá existir um problema com as definições de proxy. Trabalhar com o seu administrador de proxy para identificar os problemas.

- Certifique-se de que as outras aplicações utilizando o servidor proxy funcionam conforme esperado.

- Certifique-se de que pode ligar ao portal do Microsoft Azure com o seu browser

- Certifique-se de que pode receber respostas do seu pontos finais de serviço. Introduza um URL de ponto final no seu browser. Se pode ligar, deverá receber um InvalidQueryParameterValue ou semelhante resposta XML.

- Se também que mais alguém está a utilizar o Explorador de armazenamento com o seu servidor proxy, certifique-se de que possam estabelecer a ligação. Se pode ligar-se, poderá ter de contactar o administrador do servidor proxy.

### <a name="tools-for-diagnosing-issues"></a>Ferramentas para diagnosticar problemas

Se tiver de ferramentas de rede, como o Fiddler para Windows, poderá diagnosticar problemas da seguinte forma:

- Se tiver de funcionar através do proxy, poderá ter de configurar a ferramenta de rede para ligar através do proxy.

- Verifique o número da porta utilizado pela ferramenta de sua rede.

- Introduza o URL de anfitrião local e o número de porta a ferramenta de rede, como definições de proxy no Explorador de armazenamento. Se isto é feito corretamente, a ferramenta de rede inicia o registo de pedidos de rede efetuados pelo Explorador de armazenamento, gestão e pontos finais de serviço. Por exemplo, introduza https://cawablobgrs.blob.core.windows.net/ para o ponto final do blob num browser, e irá receber uma resposta é semelhante ao seguinte, o que sugere o recurso existe, apesar de não é possível aceder-lhe.

![exemplo de código](./media/storage-explorer-troubleshooting/4022502_en_2.png)

### <a name="contact-proxy-server-admin"></a>Contacte o administrador do servidor proxy

Se as definições de proxy estão corretas, poderá ter de contactar o seu administrador de servidor proxy, e

- Certifique-se de que o proxy não bloqueia o tráfego para pontos finais do Azure de gestão ou recurso.

- Certifique-se o protocolo de autenticação utilizado pelo seu servidor proxy. Explorador de armazenamento não suporta atualmente os proxies NTLM.

## <a name="unable-to-retrieve-children-error-message"></a>Mensagem de erro "Não é possível obter subordinados"

Se estiver ligado ao Azure através de um proxy, certifique-se de que as definições de proxy estão corretas. Se foi concedido acesso a um recurso do proprietário da subscrição ou conta, certifique-se de que leu ou lista de permissões para esse recurso.

### <a name="issues-with-sas-url"></a>Problemas com o SAS URL
Se estiver a ligar a um serviço utilizando um URL SAS e estão a ocorrer este erro:

- Certifique-se de que o URL fornece as permissões necessárias para ler ou lista de recursos.

- Certifique-se de que o URL não expirou.

- Se o URL de SAS baseiam-se uma política de acesso, certifique-se de que a política de acesso não foi revogada.

## <a name="next-steps"></a>Passos seguintes

Se nenhuma das soluções de resolver o problema, submeter o seu problema através da ferramenta de comentários com o seu e-mail e tantos detalhes sobre o problema incluídas como podem, para que podemos contactá-lo para corrigir o problema.

Para tal, clique em **ajudar** e, em seguida, clique em **enviar comentários**.

![Comentários](./media/storage-explorer-troubleshooting/4022503_en_1.png)
