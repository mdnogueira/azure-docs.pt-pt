---
title: "Configurar o SSL para um serviço em nuvem (clássica) | Microsoft Docs"
description: "Saiba como especificar um ponto final de HTTPS para uma função da web e como carregar um certificado SSL para proteger a sua aplicação."
services: cloud-services
documentationcenter: .net
author: Thraka
manager: timlt
editor: 
ms.assetid: 4cbb7f38-7994-454d-b4f0-7259b558c766
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/14/2016
ms.author: adegeo
ms.openlocfilehash: 7df2371f64f0d8a9fabc0df37c17d4dfbc47cd7e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configuring-ssl-for-an-application-in-azure"></a>Configurar o SSL para uma aplicação no Azure
> [!div class="op_single_selector"]
> * [Portal do Azure](cloud-services-configure-ssl-certificate-portal.md)
> * [Portal Clássico do Azure](cloud-services-configure-ssl-certificate.md)
> 
> 

A encriptação Secure Socket Layer (SSL) é o método mais utilizado para proteger dados enviados através da Internet. Esta tarefa comum debate como especificar um ponto final HTTPS para uma função Web e como carregar um certificado SSL para proteger a sua aplicação.

> [!NOTE]
> Os procedimentos nesta tarefa se aplicam ao Cloud Services do Azure; para os serviços de aplicação, consulte [isto](../app-service/app-service-web-tutorial-custom-ssl.md) artigo.
> 
> 

Esta tarefa utiliza uma implementação de produção. Informações sobre como utilizar uma implementação de teste são fornecidas no final deste tópico.

Leitura [isto](cloud-services-how-to-create-deploy.md) artigo primeiro se ainda não tiver criado um serviço em nuvem.

## <a name="step-1-get-an-ssl-certificate"></a>Passo 1: Obter um certificado SSL
Para configurar o SSL para uma aplicação, terá primeiro de obter um certificado SSL assinado por uma autoridade de certificação (CA), uma terceiros fidedigna, que emite certificados para esta finalidade. Se já tiver um, tem de obter um de uma empresa que sells certificados SSL.

O certificado tem de cumprir os seguintes requisitos para certificados SSL no Azure:

* O certificado tem de conter uma chave privada.
* O certificado tem de ser criado para a troca de chaves, exportável para um ficheiro Personal Information Exchange (. pfx).
* Nome do requerente do certificado deve corresponder ao domínio utilizado para aceder ao serviço de nuvem. Não é possível obter um certificado SSL a partir de uma autoridade de certificação (AC) para o domínio cloudapp.net. Tem de adquirir um nome de domínio personalizado para utilizar quando aceder ao seu serviço. Quando solicitar um certificado a partir de uma AC, o nome de requerente do certificado tem de corresponder ao nome de domínio personalizado utilizado para aceder à sua aplicação. Por exemplo, se o nome de domínio personalizado for **contoso.com** seria pedir um certificado da AC para ***. contoso.com** ou **www.contoso.com**.
* O certificado tem de utilizar um mínimo de encriptação de 2048 bits.

Para fins de teste, pode [criar](cloud-services-certs-create.md) e utilizar um certificado autoassinado. Um certificado autoassinado não é autenticado através de uma AC e pode utilizar o domínio cloudapp.net como o URL de Web site. Por exemplo, a seguinte tarefa utiliza um certificado autoassinado em que é o nome comum (CN) utilizado no certificado **sslexample.cloudapp.net**.

Em seguida, tem de incluir informações sobre o certificado na sua definição de serviço e os ficheiros de configuração de serviço.

## <a name="step-2-modify-the-service-definition-and-configuration-files"></a>Passo 2: Modificar os ficheiros de configuração e definição de serviço
A aplicação tem de ser configurada para utilizar o certificado e um ponto final de HTTPS tem de ser adicionado. Como resultado, a definição de serviço e os ficheiros de configuração do serviço tem de ser atualizados.

1. No seu ambiente de desenvolvimento, abra o ficheiro de definição de serviço (. CSDEF), adicione um **certificados** secção dentro de **WebRole** secção e incluem as seguintes informações sobre o certificado (e certificados intermediários):
   
    ```xml
    <WebRole name="CertificateTesting" vmsize="Small">
    ...
        <Certificates>
            <Certificate name="SampleCertificate" 
                        storeLocation="LocalMachine" 
                        storeName="My"
                        permissionLevel="limitedOrElevated" />
            <!-- IMPORTANT! Unless your certificate is either
            self-signed or signed directly by the CA root, you
            must include all the intermediate certificates
            here. You must list them here, even if they are
            not bound to any endpoints. Failing to list any of
            the intermediate certificates may cause hard-to-reproduce
            interoperability problems on some clients.-->
            <Certificate name="CAForSampleCertificate"
                        storeLocation="LocalMachine"
                        storeName="CA"
                        permissionLevel="limitedOrElevated" />
        </Certificates>
    ...
    </WebRole>
    ```
   
   O **certificados** secção define o nome do nosso certificado, a localização e o nome da loja onde está localizado.
   
   As permissões (`permisionLevel` atributo) pode ser definido como um dos seguintes valores:
   
   | Valor de permissão | Descrição |
   | --- | --- |
   | limitedOrElevated |**(Predefinição)**  Todos os processos de função podem aceder à chave privada. |
   | elevada |Apenas os processos elevados podem aceder à chave privada. |
2. No seu ficheiro de definição de serviço, adicione um **InputEndpoint** elemento o **pontos finais** secção ativar HTTPS:
   
    ```xml
    <WebRole name="CertificateTesting" vmsize="Small">
    ...
        <Endpoints>
            <InputEndpoint name="HttpsIn" protocol="https" port="443" 
                certificate="SampleCertificate" />
        </Endpoints>
    ...
    </WebRole>
    ```

3. No seu ficheiro de definição de serviço, adicione um **enlace** elemento o **Sites** secção. Esta secção adiciona um enlace de HTTPS para mapear o ponto final para o site:
   
    ```xml   
    <WebRole name="CertificateTesting" vmsize="Small">
    ...
        <Sites>
            <Site name="Web">
                <Bindings>
                    <Binding name="HttpsIn" endpointName="HttpsIn" />
                </Bindings>
            </Site>
        </Sites>
    ...
    </WebRole>
    ```
   
   As alterações necessárias para o ficheiro de definição de serviço foram concluídas, mas terá de adicionar as informações do certificado para o ficheiro de configuração do serviço.
4. No seu ficheiro de configuração serviço (CSCFG), Serviceconfiguration, adicione um **certificados** secção dentro de **função** secção, substituindo o valor do thumbprint de exemplo mostrado abaixo, com que o certificado:
   
    ```xml   
    <Role name="Deployment">
    ...
        <Certificates>
            <Certificate name="SampleCertificate" 
                thumbprint="9427befa18ec6865a9ebdc79d4c38de50e6316ff" 
                thumbprintAlgorithm="sha1" />
            <Certificate name="CAForSampleCertificate"
                thumbprint="79d4c38de50e6316ff9427befa18ec6865a9ebdc" 
                thumbprintAlgorithm="sha1" />
        </Certificates>
    ...
    </Role>
    ```

(O anterior exemplo utiliza **sha1** para o algoritmo de thumbprint. Especifique o valor adequado para o algoritmo de thumbprint do certificado.)

Agora que a definição de serviço e os ficheiros de configuração de serviço foram atualizados, o pacote a implementação do carregamento para o Azure. Se estiver a utilizar **cspack**, não utilize o **/generateConfigurationFile** sinalizador, como de que substitui as informações do certificado é inserido.

## <a name="step-3-upload-a-certificate"></a>Passo 3: Carregar um certificado
O pacote de implementação foi atualizado para utilizar o certificado e foi adicionado um ponto final de HTTPS. Agora pode carregar o pacote e o certificado para o Azure com o portal clássico do Azure.

1. Inicie sessão no [portal clássico do Azure][Azure classic portal]. 
2. Clique em **serviços em nuvem** no painel de navegação esquerda.
3. Clique no serviço de nuvem pretendido.
4. Clique em de **certificados** separador.
   
    ![Clique no separador de certificados](./media/cloud-services-configure-ssl-certificate/click-cert.png)

5. Clique no botão **Carregar**.
   
    ![Carregar](./media/cloud-services-configure-ssl-certificate/upload-button.png)
    
6. Forneça o **ficheiro**, **palavra-passe**, em seguida, clique em **concluída** (a marca de verificação).

## <a name="step-4-connect-to-the-role-instance-by-using-https"></a>Passo 4: Ligar à instância de função através de HTTPS
Agora que a implementação está a funcionar no Azure, pode ligar ao mesmo através de HTTPS.

1. No portal clássico do Azure, selecione a implementação, em seguida, clique na hiperligação em **URL do Site**.
   
   ![Determinar o URL do site][2]
2. No seu browser, modificar a ligação para utilizar **https** em vez de **http**e, em seguida, visite a página.
   
   > [!NOTE]
   > Se estiver a utilizar um certificado autoassinado, ao navegar para um ponto final de HTTPS tem associado o certificado autoassinado vir um erro de certificado no browser. Utilizar um certificado assinado por uma autoridade de certificação fidedignas elimina este problema; Entretanto, pode ignorar o erro. (É outra opção Adicionar o certificado autoassinado ao arquivo de certificados de autoridade de certificado fidedigno do utilizador.)
   > 
   > 
   
   ![Exemplo de SSL de web site][3]

Se pretender utilizar o SSL para uma implementação de teste em vez de uma implementação de produção, primeiro terá de determinar o URL utilizado para a implementação de teste. Implemente o serviço em nuvem para o ambiente de teste sem incluir quaisquer informações de certificado ou um certificado. Depois de implementada, pode determinar o URL com base no GUID, que está listado no portal clássico do Azure **URL do Site** campo. Criar um certificado com o nome comum (CN) igual para o URL com base no GUID (por exemplo, **32818777-6e77-4ced-a8fc-57609d404462.cloudapp.net**). Utilize o portal clássico do Azure para adicionar o certificado ao seu serviço de nuvem faseada. Em seguida, adicionar as informações do certificado aos seus ficheiros CSDEF e CSCFG, Empacote novamente esta aplicação e atualizar a sua implementação faseada para utilizar o novo pacote.

## <a name="next-steps"></a>Passos seguintes
* [Configuração geral do seu serviço de nuvem](cloud-services-how-to-configure.md).
* Saiba como [implementar um serviço em nuvem](cloud-services-how-to-create-deploy.md).
* Configurar um [nome de domínio personalizado](cloud-services-custom-domain-name.md).
* [Gerir o serviço de nuvem](cloud-services-how-to-manage.md).

[Azure classic portal]: http://manage.windowsazure.com
[0]: ./media/cloud-services-configure-ssl-certificate/CreateCloudService.png
[1]: ./media/cloud-services-configure-ssl-certificate/AddCertificate.png
[2]: ./media/cloud-services-configure-ssl-certificate/CopyURL.png
[3]: ./media/cloud-services-configure-ssl-certificate/SSLCloudService.png
[4]: ./media/cloud-services-configure-ssl-certificate/AddCertificateComplete.png  
