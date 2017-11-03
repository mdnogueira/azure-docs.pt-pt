---
title: "Adicionar um certificado SSL para a sua aplicação do App Service do Azure | Microsoft Docs"
description: "Saiba como adicionar um certificado SSL para a sua aplicação de serviço de aplicações."
services: app-service
documentationcenter: .net
author: ahmedelnably
manager: stefsch
editor: cephalin
tags: buy-ssl-certificates
ms.assetid: cdb9719a-c8eb-47e5-817f-e15eaea1f5f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2016
ms.author: apurvajo
ms.openlocfilehash: 214f05f45f59b0403e6902988f9184d6b62618bd
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2017
---
# <a name="buy-and-configure-an-ssl-certificate-for-your-azure-app-service"></a>Comprar e Configurar um Certificado SSL para o Serviço de Aplicações do Azure

Este tutorial mostra como proteger a sua aplicação web por comprar um certificado SSL para o seu  **[App Service do Azure](http://go.microsoft.com/fwlink/?LinkId=529714)**, de forma segura armazenar no [Cofre de chaves do Azure](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-whatis), e associar a um domínio personalizado.

## <a name="step-1---log-in-to-azure"></a>Passo 1 - início de sessão para o Azure

Inicie sessão no portal do Azure em http://portal.azure.com

## <a name="step-2---place-an-ssl-certificate-order"></a>Passo 2 - coloque uma ordem de certificado SSL

Pode colocar um pedido de certificado SSL ao criar um novo [certificado de serviço de aplicação](https://portal.azure.com/#create/Microsoft.SSL) no **portal do Azure**.

![Criação do certificado](./media/app-service-web-purchase-ssl-web-site/createssl.png)

Introduza um amigável **nome** para o SSL de certificado e introduza o **nome de domínio**

> [!NOTE]
> Este passo é uma das partes do processo de compra mais críticas. Certifique-se de que introduz o nome de anfitrião correto (domínio personalizado) que pretende proteger com este certificado. **NÃO** acrescentar o nome de anfitrião com WWW. 
>

Selecione o **subscrição**, **grupo de recursos**, e **SKU de certificado**

> [!WARNING]
> Certificados de serviço de aplicações só podem ser utilizados por outros serviços de aplicações dentro da mesma subscrição.  
>

## <a name="step-3---store-the-certificate-in-azure-key-vault"></a>Passo 3 - armazenar o certificado no Cofre de chaves do Azure

> [!NOTE]
> [O Cofre de chaves](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-whatis) é um serviço Azure que ajuda a salvaguardar as chaves criptográficas e segredos utilizados pelas aplicações em nuvem e de serviços.
>

Assim que a compra do certificado SSL estiver concluída, é necessário abrir o [certificados de serviço de aplicações](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) página.

![Inserir uma imagem de pronto para armazenar na KV](./media/app-service-web-purchase-ssl-web-site/ReadyKV.png)

O estado do certificado é **"Emissão pendente"** porque existem alguns passos mais precisa para concluir antes de começar a utilizar este certificado.

Clique em **certificado configuração** dentro as propriedades do certificado de página e clique em **passo 1: armazenar** para armazenar este certificado no Cofre de chaves do Azure.

Do **estado do Cofre de chave** página, clique em **repositório do Cofre de chave** para escolher um cofre de chaves existente para armazenar este certificado **ou criar nova chave de cofre** para criar o novo cofre de chaves dentro do mesmo grupo de recursos e de subscrição.

> [!NOTE]
> O Cofre de chaves do Azure tem custos mínimos para armazenar este certificado.
> Para obter mais informações, consulte  **[detalhes de preços do Azure chave de cofre](https://azure.microsoft.com/pricing/details/key-vault/)**.
>

Assim que tiver selecionado o repositório do Cofre de chaves para armazenar este certificado, o **armazenar** opção deve mostrar com êxito.

![Inserir uma imagem de êxito de arquivo em KV](./media/app-service-web-purchase-ssl-web-site/KVStoreSuccess.png)

## <a name="step-4---verify-the-domain-ownership"></a>Passo 4 - verificar a propriedade de domínio

> [!NOTE]
> Existem três tipos de verificação de domínio suportados por certificados de serviço de aplicações: verificação do domínio, correio, Manual. Estes tipos de verificação são explicados com mais detalhes no [avançadas secção](#advanced).

Do mesmo **certificado configuração** página utilizada no passo 3, clique em **passo 2: verificar**.

**Verificação de domínio** este é o processo mais conveniente **apenas se** tiver  **[comprou o domínio personalizado do App Service do Azure.](custom-dns-web-site-buydomains-web-app.md)**
Clique em **verifique** botão para concluir este passo.

![Inserir uma imagem de verificação de domínio](./media/app-service-web-purchase-ssl-web-site/DomainVerificationRequired.png)

Depois de clicar em **verifique**, utilizar o **atualizar** botão até o **verifique** opção deve mostrar êxito.

![imagem de inserção de confirmar o êxito no KV](./media/app-service-web-purchase-ssl-web-site/KVVerifySuccess.png)

## <a name="step-5---assign-certificate-to-app-service-app"></a>Passo 5 - certificado de atribuir a aplicação do App Service

> [!NOTE]
> Antes de efetuar os passos nesta secção, tem de ter associadas um nome de domínio personalizado com a sua aplicação. Para obter mais informações, consulte  **[configurar um nome de domínio personalizado para uma aplicação web.](app-service-web-tutorial-custom-domain.md)**
>

No  **[portal do Azure](https://portal.azure.com/)**, clique em de **do serviço de aplicações** opção no lado esquerdo da página.

Clique no nome da sua aplicação para o qual pretende atribuir este certificado.

No **definições**, clique em **certificados SSL**.

Clique em **importar certificado de serviço de aplicações** e selecione o certificado que acabou de comprou.

![Inserir uma imagem de importar certificado](./media/app-service-web-purchase-ssl-web-site/ImportCertificate.png)

No **enlaces ssl** secção clique no **adicionar enlaces**e utilize as dropdowns para selecionar o nome de domínio para proteger com SSL e o certificado a utilizar. Também pode selecionar se pretende utilizar  **[indicação de nome de servidor (SNI)](http://en.wikipedia.org/wiki/Server_Name_Indication)**  ou IP baseada em SSL.

![Inserir a imagem dos enlaces SSL](./media/app-service-web-purchase-ssl-web-site/SSLBindings.png)

Clique em **Adicionar enlace de** para guardar as alterações e ativar o SSL.

> [!NOTE]
> Se tiver selecionado **IP baseada em SSL** e o domínio personalizado está configurado com um registo a, tem de efetuar os seguintes passos adicionais. Estes são explicados com maior detalhe no [avançadas secção](#Advanced).

Nesta fase, deve ser capaz de visitar a sua aplicação utilizar `HTTPS://` em vez de `HTTP://` para verificar se o certificado foi configurado corretamente.

<!--![insert image of https](./media/app-service-web-purchase-ssl-web-site/Https.png)-->

## <a name="step-6---management-tasks"></a>Passo 6 – tarefas de gestão

### <a name="azure-cli"></a>CLI do Azure

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom SSL certificate to a web app")]

## <a name="advanced"></a>Avançado

### <a name="verifying-domain-ownership"></a>Verificação da propriedade de domínio

Existem dois tipos de mais de verificação de domínio suportados por certificados de serviço de aplicações: Mail e verificação Manual.

#### <a name="mail-verification"></a>Verificação de correio

Correio eletrónico de verificação já foi enviado para os endereços de E-mail associado este domínio personalizado.
Para concluir o passo de verificação de E-Mail, abra o e-mail e clique na hiperligação de verificação.

![Inserir uma imagem de verificação de e-mail](./media/app-service-web-purchase-ssl-web-site/KVVerifyEmailSuccess.png)

Se for necessário reenviar o correio de eletrónico de verificação, clique em de **Reenviar correio eletrónico** botão.

#### <a name="manual-verification"></a>Verificação manual

> [!IMPORTANT]
> Verificação de página Web HTML (apenas funciona com o padrão de certificado de SKU)
>

1. Criar um ficheiro HTML com o nome **"starfield.html"**

1. O conteúdo deste ficheiro deve ser o nome exato do Token de verificação do domínio. (Pode copiar o token da página de estado de verificação de domínio)

1. Carregar este ficheiro na raiz do servidor web que aloja o seu domínio`/.well-known/pki-validation/starfield.html`

1. Clique em **atualizar** para atualizar o estado de certificado depois de concluída a verificação. Poderá demorar alguns minutos para fins de verificação concluir.

> [!TIP]
> Certifique-se na utilização de um terminal `curl -G http://<domain>/.well-known/pki-validation/starfield.html` a resposta deve conter o `<verification-token>`.

#### <a name="dns-txt-record-verification"></a>Verificação do registo TXT de DNS

1. Utilizando o Gestor de DNS, crie um registo TXT no `@` subdomínio com valor igual ao Token de verificação do domínio.
1. Clique em **'Atualizar'** para atualizar o estado de certificado depois de concluída a verificação.

> [!TIP]
> Tem de criar um registo TXT no `@.<domain>` com valor `<verification-token>`.

### <a name="assign-certificate-to-app-service-app"></a>Atribuir o certificado para a aplicação de serviço de aplicações

Se tiver selecionado **IP baseada em SSL** e o domínio personalizado está configurado com um registo a, tem de efetuar os seguintes passos adicionais:

Depois de ter configurado um IP com base em enlace SSL, é atribuído um endereço IP dedicado à sua aplicação. Pode encontrar este endereço IP no **domínio personalizado** página com as definições da sua aplicação, direito acima o **Hostnames** secção. Este é listado como **endereço IP externo**

![Inserir uma imagem de IP SSL](./media/app-service-web-purchase-ssl-web-site/virtual-ip-address.png)

Este endereço IP é diferente do endereço IP virtual utilizado anteriormente para configurar o registo para o seu domínio. Se estiverem configurados para utilizar SNI baseada em SSL, ou não estão configurados para utilizar SSL, nenhum endereço está listado para esta entrada.

Utilizar as ferramentas fornecidas pelo sua entidade de registo de nome de domínio, modifique o registo para o nome de domínio personalizado apontar para o endereço IP do passo anterior.

## <a name="rekey-and-sync-the-certificate"></a>Recodificar e sincronizar o certificado

Se alguma vez precisar de recodificar o certificado, selecione o **recodificação e sincronização** opção do **propriedades do certificado** página.

Clique em **recodificar** botão para iniciar o processo. Este processo pode demorar 1 a 10 minutos a concluir.

![Inserir uma imagem do recodificar SSL](./media/app-service-web-purchase-ssl-web-site/Rekey.png)

Rekeying o seu certificado faz o certificado com um novo certificado emitido a partir da autoridade de certificação.

<a name="notrenewed"></a>
## <a name="why-is-my-ssl-certificate-not-auto-renewed"></a>Por que motivo o meu certificado SSL não for automaticamente-renovado?

Se o certificado SSL está configurado para renovação automática, mas não for renovado automaticamente, pode ter uma verificação de domínio pendente. Tenha em atenção o seguinte: 

- GoDaddy, gerando certificados de serviço de aplicações, necessita de uma vez a cada três anos de verificação de domínio. O administrador de domínio recebe uma mensagem de e-mail uma vez a cada três anos para verificar o domínio. Falha ao verificar o e-mail ou verifique o seu domínio impede que o certificado de serviço de aplicações que está a ser renovado automaticamente. 
- Todos os serviço de aplicações certificados emitidos antes 31 de Março de 2017 requerem reverification do domínio no momento da renovação seguinte (mesmo se a renovação automática está ativada para o certificado). Este é um resultado de alterações na política da GoDaddy. Verifique o seu correio eletrónico e concluir esta verificação de domínio única para continuar a renovação automática do certificado do serviço de aplicações. 

## <a name="next-steps"></a>Passos Seguintes

* [Adicionar uma rede de entrega de conteúdos](app-service-web-tutorial-content-delivery-network.md)