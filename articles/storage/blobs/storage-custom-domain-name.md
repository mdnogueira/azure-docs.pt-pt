---
title: "Configurar um nome de domínio personalizado para o ponto final de armazenamento de Blobs do Azure | Microsoft Docs"
description: "Utilize o portal do Azure para mapear o seus próprios nome canónico (CNAME) para o ponto final de armazenamento de BLOBs numa conta do Storage do Azure."
services: storage
documentationcenter: 
author: tamram
manager: timlt
editor: tysonn
ms.assetid: aaafd8c5-eacb-49dc-8c8b-3f7011ad5e92
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2017
ms.author: tamram
ms.openlocfilehash: 4dfca37e3a369fdfb41b4064b72ccd027bd8cbd3
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2017
---
# <a name="configure-a-custom-domain-name-for-your-blob-storage-endpoint"></a>Configurar um nome de domínio personalizado para o ponto final do Armazenamento de Blobs

Pode configurar um domínio personalizado para aceder aos dados de BLOBs na sua conta do storage do Azure. O ponto final predefinido para o Blob storage é `<storage-account-name>.blob.core.windows.net`. Se mapear um domínio personalizado e o subdomínio como **www.contoso.com** ao ponto final do blob para o armazenamento de conta, pode utilizadores, em seguida, aceder a dados de BLOBs na sua conta do storage utilizar esse domínio.

> [!IMPORTANT]
> Armazenamento do Azure não ainda nativamente suporta HTTPS com domínios personalizados. Pode atualmente [utilizar a CDN do Azure para aceder a blobs com domínios personalizados através de HTTPS](storage-https-custom-domain-cdn.md).
>

A tabela seguinte mostra alguns URLs de exemplo de dados de blob localizados numa conta do storage com o nome **mystorageaccount**. O domínio personalizado registado para a conta de armazenamento é **www.contoso.com**:

| Tipo de Recurso | URL predefinido | URL do domínio personalizado |
| --- | --- | --- |
| Conta de armazenamento | http://mystorageaccount.blob.Core.Windows.NET | http://www.contoso.com |
| Blobs |http://mystorageaccount.blob.Core.Windows.NET/mycontainer/myblob | http://www.contoso.com/mycontainer/myblob |
| Recipiente-raiz | http://mystorageaccount.blob.Core.Windows.NET/myblob ou http://mystorageaccount.blob.core.windows.net/$ raiz/myblob| http://www.contoso.com/myblob ou http://www.contoso.com/$ raiz/myblob |

## <a name="direct-vs-intermediary-domain-mapping"></a>Direto vs mapeamento intermédios domínio

Existem duas formas para apontar o domínio personalizado para o ponto final do blob para a sua conta de armazenamento: direcionar CNAME mapeamento e utilizar o *asverify* subdomínio intermediário.

### <a name="direct-cname-mapping"></a>Direcionar o mapeamento CNAME

O método primeiro e mais simples, é criar um registo de nome canónico (CNAME), que mapeia o seu domínio personalizado e o subdomínio diretamente para o ponto final do blob. Um registo CNAME é uma funcionalidade de sistema (DNS) do nome de domínio que mapeia um domínio de origem para um domínio de destino. Neste caso, o domínio de origem é o seu domínio personalizado e subdomínio, por exemplo *www.contoso.com*. O domínio de destino é o ponto de final de serviço Blob, por exemplo *mystorageaccount.blob.core.windows.net*.

O método direto é abordado [registar um domínio personalizado](#register-a-custom-domain).

### <a name="intermediary-mapping-with-asverify"></a>Mapeamento intermédios com *asverify*

O segundo método também utiliza registos CNAME, mas primeiro emprega um subdomínio especial reconhecido pelo Azure para evitar períodos de indisponibilidade: **asverify**.

O processo de mapeamento do domínio personalizado para um ponto final do blob pode resultar num breve período de tempo de inatividade para o domínio, enquanto está a registá-lo no [portal do Azure](https://portal.azure.com). Se o domínio personalizado é atualmente suportar uma aplicação com um contrato de nível de serviço (SLA) que necessita de período de indisponibilidade zero, em seguida, pode utilizar o Azure *asverify* subdomínio como passo intermédio de registo. Este passo intermédio garante que os utilizadores conseguem aceder ao seu domínio, enquanto o mapeamento de DNS ocorre.

O método intermediário é abordado [registar um domínio personalizado utilizando a *asverify* subdomínio](#register-a-custom-domain-using-the-asverify-subdomain).

## <a name="register-a-custom-domain"></a>Registar um domínio personalizado
Utilize este procedimento para registar o domínio personalizado, se tiver sem preocupações sobre o indisponível por breves instantes aos seus utilizadores de domínio ou se o domínio personalizado não aloja atualmente uma aplicação. Pode utilizar o DNS do Azure para configurar um nome DNS personalizado para o arquivo de Blob do Azure. Para obter mais informações, consulte [DNS do Azure de utilização para fornecer definições de domínio personalizado para um serviço do Azure](https://docs.microsoft.com/azure/dns/dns-custom-domain#blob-storage).

Se o domínio personalizado atualmente é o suporte para uma aplicação que não pode ter qualquer período de inatividade, siga o procedimento descrito no [registar um domínio personalizado utilizando a *asverify* subdomínio](#register-a-custom-domain-using-the-asverify-subdomain).

Para configurar um nome de domínio personalizado, tem de criar um novo registo CNAME no DNS. O registo CNAME Especifica um alias para um nome de domínio. Neste caso, mapeie o endereço do seu domínio personalizado para o ponto de final de armazenamento de BLOBs para a sua conta de armazenamento.

Normalmente, pode gerir as definições de DNS do domínio no Web site do seu domínio da entidade de registo. Cada entidade de registo tem um método semelhante, mas ligeiramente diferente de especificação de um registo CNAME, mas o conceito é o mesmo. Alguns pacotes de registo do domínio básico não oferecem a configuração de DNS, por isso terá de atualizar o pacote de registo do domínio, antes de poder criar o registo CNAME.

1. Navegue até à sua conta do storage no [portal do Azure](https://portal.azure.com).
1. Em **serviço BLOB** no painel de menu, selecione **domínio personalizado** para abrir o *domínio personalizado* painel.
1. Inicie sessão no Web site do seu domínio da entidade de registo e aceda à página para gerir o DNS. Poderá encontrar isto numa secção como **Nome de Domínio**, **DNS** ou **Gestão de Nome de Servidor**.
1. Encontre a secção para gerir CNAMEs. Poderá ter de aceder à página de definições avançadas e procure as palavras **CNAME**, **Alias**, ou **subdomínios**.
1. Criar um novo registo CNAME e fornecer um alias de subdomínio como **www** ou **fotografias**. Em seguida, forneça um nome de anfitrião, o que é o Blob ponto final de serviço, no formato **mystorageaccount.blob.core.windows.net** (onde *mystorageaccount* é o nome da conta de armazenamento). O nome de anfitrião a utilizar é apresentada no item #1 do *domínio personalizado* painel no [portal do Azure](https://portal.azure.com).
1. Na caixa de texto no *domínio personalizado* painel no [portal do Azure](https://portal.azure.com), introduza o nome do seu domínio personalizado, incluindo o subdomínio. Por exemplo, se o seu domínio for **contoso.com** e é o seu alias subdomínio **www**, introduza **www.contoso.com**. Se for o subdomínio **fotografias**, introduza **photos.contoso.com**. É o subdomínio *necessário*.
1. Selecione **guardar** no *domínio personalizado* painel para registar o seu domínio personalizado. Se o registo for bem sucedido, verá uma notificação do portal que a conta de armazenamento foi atualizada com êxito.

Depois do novo registo CNAME tiver sido propagada através de DNS, os utilizadores podem ver dados de BLOBs, utilizando o seu domínio personalizado, desde que têm as permissões adequadas.

## <a name="register-a-custom-domain-using-the-asverify-subdomain"></a>Registar um domínio personalizado utilizando a *asverify* subdomínio
Utilize este procedimento para registar a personalizada domínio se o domínio personalizado é atualmente suportar uma aplicação com um SLA que requer que não existir nenhum período de indisponibilidade. Ao criar um CNAME que aponta de `asverify.<subdomain>.<customdomain>` para `asverify.<storageaccount>.blob.core.windows.net`, previamente pode registar o seu domínio com o Azure. Em seguida, pode criar um segundo CNAME que aponta de `<subdomain>.<customdomain>` para `<storageaccount>.blob.core.windows.net`, altura em que o tráfego para o domínio personalizado será direcionado para o ponto final do blob.

O **asverify** subdomínio é um subdomínio especial reconhecido pelo Azure. Por prefixação `asverify` para o seus próprios subdomínio é permitir que o Azure para reconhecer o domínio personalizado sem modificar o registo DNS para o domínio. Quando modificar o registo DNS para o domínio, serão mapeado para o ponto final do blob sem período de indisponibilidade.

1. Navegue até à sua conta do storage no [portal do Azure](https://portal.azure.com).
1. Em **serviço BLOB** no painel de menu, selecione **domínio personalizado** para abrir o *domínio personalizado* painel.
1. Inicie sessão no Web site do seu fornecedor de DNS e aceda à página para gerir o DNS. Poderá encontrar isto numa secção como **Nome de Domínio**, **DNS** ou **Gestão de Nome de Servidor**.
1. Encontre a secção para gerir CNAMEs. Poderá ter de aceder à página de definições avançadas e procure as palavras **CNAME**, **Alias**, ou **subdomínios**.
1. Criar um novo registo CNAME e fornecer um alias de subdomínio, que inclui o *asverify* subdomínio. Por exemplo, **asverify.www** ou **asverify.photos**. Em seguida, forneça um nome de anfitrião, o que é o Blob ponto final de serviço, no formato **asverify.mystorageaccount.blob.core.windows.net** (onde **mystorageaccount** é o nome da conta de armazenamento). O nome de anfitrião a utilizar é apresentada no item #2 do *domínio personalizado* painel no [portal do Azure](https://portal.azure.com).
1. Na caixa de texto no *domínio personalizado* painel no [portal do Azure](https://portal.azure.com), introduza o nome do seu domínio personalizado, incluindo o subdomínio. Não incluir *asverify*. Por exemplo, se o seu domínio for **contoso.com** e é o seu alias subdomínio **www**, introduza **www.contoso.com**. Se for o subdomínio **fotografias**, introduza **photos.contoso.com**. É necessário o subdomínio.
1. Selecione o **utilizam a validação de CNAME indireta** caixa de verificação.
1. Selecione **guardar** no *domínio personalizado* painel para registar o seu domínio personalizado. Se o registo for bem sucedido, verá uma notificação do portal a indicar que a conta de armazenamento foi atualizada com êxito. Neste momento, o domínio personalizado foi verificado pelo Azure, mas o tráfego para o seu domínio ainda não está a ser encaminhado para a sua conta de armazenamento.
1. Regresse ao Web site do seu fornecedor de DNS e crie outro registo CNAME que mapeia o subdomínio para o ponto de final de serviço Blob. Por exemplo, especifique o subdomínio como **www** ou **fotografias** (sem o *asverify*) e o nome do anfitrião como **mystorageaccount.blob.core.windows.net** (onde **mystorageaccount** é o nome da conta de armazenamento). Neste passo, o registo do seu domínio personalizado está concluído.
1. Por fim, pode eliminar o registo CNAME que criou que contém o **asverify** subdomínio, dado que foi necessário apenas como um passo intermediário.

Depois do novo registo CNAME tiver sido propagada através de DNS, os utilizadores podem ver dados de BLOBs, utilizando o seu domínio personalizado, desde que têm as permissões adequadas.

## <a name="test-your-custom-domain"></a>Testar o seu domínio personalizado

Para confirmar que o domínio personalizado, de facto, está mapeado para o ponto final de serviço de Blob, crie um blob num contentor público na sua conta de armazenamento. Em seguida, num browser, utilize um URI no seguinte formato para aceder a BLOBs:

`http://<subdomain.customdomain>/<mycontainer>/<myblob>`

Por exemplo, poderá utilizar o URI seguinte para aceder a um formulário web no **myforms** contentor no **photos.contoso.com** subdomínio personalizado:

`http://photos.contoso.com/myforms/applicationform.htm`

## <a name="deregister-a-custom-domain"></a>Anular o registo um domínio personalizado

Ao anular o registo um domínio personalizado para o ponto final de armazenamento de BLOBs, utilize um dos seguintes procedimentos.

### <a name="azure-portal"></a>Portal do Azure

Execute o seguinte no portal do Azure para remover a definição de domínio personalizado:

1. Navegue até à sua conta do storage no [portal do Azure](https://portal.azure.com).
1. Em **serviço BLOB** no painel de menu, selecione **domínio personalizado** para abrir o *domínio personalizado* painel.
1. Limpe o conteúdo da caixa de texto que contém o nome de domínio personalizado.
1. Selecione o botão **Guardar**.

Quando o domínio personalizado foi removido com êxito, verá uma notificação do portal a indicar que a conta de armazenamento foi atualizada com êxito.

### <a name="azure-cli-20"></a>CLI 2.0 do Azure

Utilize o [atualização az da conta de armazenamento](https://docs.microsoft.com/cli/azure/storage/account#az_storage_account_update) CLI de comando e especifique uma cadeia vazia (`""`) para o `--custom-domain` o valor do argumento para remover um registo de domínio personalizado.

* Formato de comando:

  ```azurecli
  az storage account update \
      --name <storage-account-name> \
      --resource-group <resource-group-name> \
      --custom-domain ""
  ```

* Exemplo de comando:

  ```azurecli
  az storage account update \
      --name mystorageaccount \
      --resource-group myresourcegroup \
      --custom-domain ""
  ```

### <a name="powershell"></a>PowerShell

Utilize o [Set-AzureRmStorageAccount](/powershell/module/azurerm.storage/set-azurermstorageaccount) cmdlet do PowerShell e especifique uma cadeia vazia (`""`) para o `-CustomDomainName` o valor do argumento para remover um registo de domínio personalizado.

* Formato de comando:

  ```powershell
  Set-AzureRmStorageAccount `
      -ResourceGroupName "<resource-group-name>" `
      -AccountName "<storage-account-name>" `
      -CustomDomainName ""
  ```

* Exemplo de comando:

  ```powershell
  Set-AzureRmStorageAccount `
      -ResourceGroupName "myresourcegroup" `
      -AccountName "mystorageaccount" `
      -CustomDomainName ""
  ```

## <a name="next-steps"></a>Passos seguintes
* [Mapear um domínio personalizado para um ponto final do Azure rede de entrega conteúdos (CDN)](../../cdn/cdn-map-content-to-custom-domain.md)
* [Utilizar a CDN do Azure para aceder a blobs com domínios personalizados através de HTTPS](storage-https-custom-domain-cdn.md)
