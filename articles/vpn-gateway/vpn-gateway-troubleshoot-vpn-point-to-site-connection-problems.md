---
title: "Resolução de problemas de ligação de ponto a site do Azure | Microsoft Docs"
description: "Saiba como resolver problemas de ligação de ponto a site."
services: vpn-gateway
documentationcenter: na
author: chadmath
manager: cshepard
editor: 
tags: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/23/2017
ms.author: genli
ms.openlocfilehash: 76ab1600903705aad7f18f48f41cb7119c3c09bf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshooting-azure-point-to-site-connection-problems"></a>Resolução de problemas: Problemas de ligação de ponto a site do Azure

Este artigo apresenta uma lista de problemas de ligação de ponto a site comuns que podem ocorrer. Também descreve possíveis causas e soluções para esses problemas.

## <a name="vpn-client-error-a-certificate-could-not-be-found"></a>Erro de cliente VPN: não foi possível encontrar um certificado

### <a name="symptom"></a>Sintoma

Ao tentar ligar a uma rede virtual do Azure utilizando o cliente VPN, recebe a seguinte mensagem de erro:

**Não foi possível encontrar um certificado que pode ser utilizado com este protocolo de autenticação extensível. (Erro 798)**

### <a name="cause"></a>Causa

Este problema ocorre se o certificado de cliente está em falta **certificados - User\Personal\Certificates atual**.

### <a name="solution"></a>Solução

Para resolver este problema, siga estes passos:

1. Certifique-se de que os seguintes certificados na localização correta:

    | Certificado | Localização |
    | ------------- | ------------- |
    | AzureClient.pfx  | User\Personal\Certificates atual |
    | Azuregateway -*GUID*. cloudapp.net  | Autoridades de certificação de raiz User\Trusted atual|
    | AzureGateway -*GUID*. cloudapp.net, AzureRoot.cer    | Autoridades de certificação de raiz local Computer\Trusted|

2. Aceda aos utilizadores\<UserName > \AppData\Roaming\Microsoft\Network\Connections\Cm\<GUID >, manualmente instalar o certificado (ficheiro *.cer) no arquivo do computador e o utilizador.

Para obter mais informações sobre como instalar o certificado de cliente, consulte [gerar e exportar certificados para ligações ponto a site](vpn-gateway-certificates-point-to-site.md).

> [!NOTE]
> Ao importar o certificado de cliente, não selecione a **ativar a proteção forte por chave privada** opção.

## <a name="vpn-client-error-the-message-received-was-unexpected-or-badly-formatted"></a>Erro de cliente VPN: A mensagem recebida era inesperada ou formatado incorretamente

### <a name="symptom"></a>Sintoma

Ao tentar ligar a uma rede virtual do Azure utilizando o cliente VPN, recebe a seguinte mensagem de erro:

**A mensagem recebida era inesperada ou incorretamente formatado. (Erro 0x80090326)**

### <a name="cause"></a>Causa

Este problema ocorre se a chave pública do certificado de raiz não está carregada para o gateway de VPN do Azure. Também pode ocorrer se a chave está danificada ou expirada.

### <a name="solution"></a>Solução

Para resolver este problema, verifique o estado do certificado de raiz no portal do Azure para ver se foi revogado. Se não for revogado, tente eliminar o certificado de raiz e reupload. Para obter mais informações, consulte [criar certificados](vpn-gateway-howto-point-to-site-classic-azure-portal.md#generatecerts).

## <a name="vpn-client-error-a-certificate-chain-processed-but-terminated"></a>Erro de cliente VPN: uma cadeia de certificados processada, mas foi terminado 

### <a name="symptom"></a>Sintoma 

Ao tentar ligar a uma rede virtual do Azure utilizando o cliente VPN, recebe a seguinte mensagem de erro:

**Uma cadeia de certificados processados mas terminada num certificado de raiz que não é considerado fidedigno pelo fornecedor de confiança.**

### <a name="solution"></a>Solução

1. Certifique-se de que os seguintes certificados na localização correta:

    | Certificado | Localização |
    | ------------- | ------------- |
    | AzureClient.pfx  | User\Personal\Certificates atual |
    | Azuregateway -*GUID*. cloudapp.net  | Autoridades de certificação de raiz User\Trusted atual|
    | AzureGateway -*GUID*. cloudapp.net, AzureRoot.cer    | Autoridades de certificação de raiz local Computer\Trusted|

2. Se já estiverem na localização de certificados, tente eliminar os certificados e reinstalá-los. O  **azuregateway -*GUID*. cloudapp.net** certificado está no pacote de configuração de cliente VPN que transferiu a partir do portal do Azure. Pode utilizar archivers de ficheiro para extrair os ficheiros do pacote.

## <a name="file-download-error-target-uri-is-not-specified"></a>Erro de transferência de ficheiro: o URI de destino não for especificado.

### <a name="symptom"></a>Sintoma

Recebe a seguinte mensagem de erro:

**Erro de transferência do ficheiro. URI de destino não está especificado.**

### <a name="cause"></a>Causa 

Este problema ocorre devido a um tipo de gateway incorreto. 

### <a name="solution"></a>Solução

O tipo de gateway VPN tem de ser **VPN**, e o tipo de VPN tem de ser **RouteBased**.

## <a name="vpn-client-error-azure-vpn-custom-script-failed"></a>Erro de cliente VPN: falha no script personalizado VPN do Azure 

### <a name="symptom"></a>Sintoma

Ao tentar ligar a uma rede virtual do Azure utilizando o cliente VPN, recebe a seguinte mensagem de erro:

**Falha de script personalizado (para atualizar a tabela de encaminhamento). (Erro 8007026f)**

### <a name="cause"></a>Causa

Este problema pode ocorrer se estiver a tentar abrir a ligação de VPN ponto a site utilizando um atalho.

### <a name="solution"></a>Solução 

Abra o pacote de VPN diretamente em vez de abri-lo a partir do atalho.

## <a name="cannot-install-the-vpn-client"></a>Não é possível instalar o cliente VPN

### <a name="cause"></a>Causa 

É necessário um certificado adicional para confiar o gateway VPN da rede virtual. O certificado está incluído no pacote de configuração de cliente VPN que é gerado a partir do portal do Azure.

### <a name="solution"></a>Solução

Extraia o pacote de configuração de cliente VPN e localize o ficheiro. cer. Para instalar o certificado, siga estes passos:

1. Abra mmc.exe.
2. Adicionar o **certificados** snap-in.
3. Selecione o **computador** conta para o computador local.
4. Clique com botão direito do **autoridades de certificação de raiz fidedigna** nós. Clique em **todas as tarefas** > **importação**e procure o ficheiro. cer extraídos a partir do pacote de configuração de cliente VPN.
5. Reinicie o computador. 
6. Tente instalar o cliente VPN.

## <a name="azure-portal-error-failed-to-save-the-vpn-gateway-and-the-data-is-invalid"></a>Erro de portal do Azure: Falha ao guardar o gateway VPN e os dados são inválidos

### <a name="symptom"></a>Sintoma

Ao tentar guardar as alterações para o gateway VPN no portal do Azure, receber a seguinte mensagem de erro:

**Falha ao guardar o gateway de rede virtual &lt;* nome do gateway*&gt;. Dados do certificado &lt; *ID de certificado* &gt; é invalid.* *

### <a name="cause"></a>Causa 

Este problema pode ocorrer se a chave pública o certificado de raiz que carregou contém um caráter inválido, por exemplo, um espaço.

### <a name="solution"></a>Solução

Certifique-se de que os dados no certificado não contém carateres inválidos, tais como as quebras de linha (mudanças). O valor inteiro deve ser uma linha de tempo. O texto seguinte é um exemplo do certificado:

    -----BEGIN CERTIFICATE-----
    MIIC5zCCAc+gAwIBAgIQFSwsLuUrCIdHwI3hzJbdBjANBgkqhkiG9w0BAQsFADAW
    MRQwEgYDVQQDDAtQMlNSb290Q2VydDAeFw0xNzA2MTUwMjU4NDZaFw0xODA2MTUw
    MzE4NDZaMBYxFDASBgNVBAMMC1AyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEF
    AAOCAQ8AMIIBCgKCAQEAz8QUCWCxxxTrxF5yc5uUpL/bzwC5zZ804ltB1NpPa/PI
    sa5uwLw/YFb8XG/JCWxUJpUzS/kHUKFluqkY80U+fAmRmTEMq5wcaMhp3wRfeq+1
    G9OPBNTyqpnHe+i54QAnj1DjsHXXNL4AL1N8/TSzYTm7dkiq+EAIyRRMrZlYwije
    407ChxIp0stB84MtMShhyoSm2hgl+3zfwuaGXoJQwWiXh715kMHVTSj9zFechYd7
    5OLltoRRDyyxsf0qweTFKIgFj13Hn/bq/UJG3AcyQNvlCv1HwQnXO+hckVBB29wE
    sF8QSYk2MMGimPDYYt4ZM5tmYLxxxvGmrGhc+HWXzMeQIDAQABozEwLzAOBgNVHQ8B
    Af8EBAMCAgQwHQYDVR0OBBYEFBE9zZWhQftVLBQNATC/LHLvMb0OMA0GCSqGSIb3
    DQEBCwUAA4IBAQB7k0ySFUQu72sfj3BdNxrXSyOT4L2rADLhxxxiK0U6gHUF6eWz
    /0h6y4mNkg3NgLT3j/WclqzHXZruhWAXSF+VbAGkwcKA99xGWOcUJ+vKVYL/kDja
    gaZrxHlhTYVVmwn4F7DWhteFqhzZ89/W9Mv6p180AimF96qDU8Ez8t860HQaFkU6
    2Nw9ZMsGkvLePZZi78yVBDCWMogBMhrRVXG/xQkBajgvL5syLwFBo2kWGdC+wyWY
    U/Z+EK9UuHnn3Hkq/vXEzRVsYuaxchta0X2UNRzRq+o706l+iyLTpe6fnvW6ilOi
    e8Jcej7mzunzyjz4chN0/WVF94MtxbUkLkqP
    -----END CERTIFICATE-----

## <a name="azure-portal-error-failed-to-save-the-vpn-gateway-and-the-resource-name-is-invalid"></a>Erro de portal do Azure: Falha ao guardar o gateway VPN e o nome de recurso é inválido

### <a name="symptom"></a>Sintoma

Ao tentar guardar as alterações para o gateway VPN no portal do Azure, receber a seguinte mensagem de erro: 

**Falha ao guardar o gateway de rede virtual &lt;* nome do gateway*&gt;. Nome do recurso &lt; *nome do certificado tentar carregar* &gt; é inválido. o * *.

### <a name="cause"></a>Causa

Este problema ocorre porque o nome do certificado contém um caráter inválido, por exemplo, um espaço. 

## <a name="azure-portal-error-vpn-package-file-download-error-503"></a>Erro de portal do Azure: erro de transferência de ficheiro de pacote VPN 503

### <a name="symptom"></a>Sintoma

Ao tentar transferir o pacote de configuração de cliente VPN, recebe a seguinte mensagem de erro:

**Falha ao transferir o ficheiro. Detalhes do erro: erro 503. O servidor está ocupado.**
 
### <a name="solution"></a>Solução

Este erro pode ser causado por um problema temporário de rede. Tente transferir o pacote de VPN novamente após alguns minutos.

## <a name="azure-vpn-gateway-upgrade-all-p2s-clients-are-unable-to-connect"></a>A atualização de Gateway de VPN do Azure: P2S todos os clientes não conseguem ligar

### <a name="cause"></a>Causa

Se o certificado é mais de 50 por cento através da respetiva duração, o certificado é distribuído.

### <a name="solution"></a>Solução

Para resolver este problema, crie e redistribuir novos certificados para os clientes VPN. 

## <a name="too-many-vpn-clients-connected-at-once"></a>Demasiados clientes VPN ligados em simultâneo

Para cada gateway de VPN, o número máximo de ligações permitidos é de 128. Pode ver o número total de clientes ligados no portal do Azure.

## <a name="point-to-site-vpn-incorrectly-adds-a-route-for-100008-to-the-route-table"></a>VPN ponto a site adiciona incorretamente uma rota para 10.0.0.0/8 para a tabela de rotas

### <a name="symptom"></a>Sintoma

Ao marcar a ligação VPN no cliente ponto a site, o cliente VPN deve adicionar uma rota para a rede virtual do Azure. O serviço de programa auxiliar IP deve adicionar uma rota para a sub-rede de clientes VPN. 

O intervalo de cliente VPN pertence a uma sub-rede de 10.0.0.0/8, tais como 10.0.12.0/24 mais pequena. Em vez de uma rota para 10.0.12.0/24, uma rota para 10.0.0.0/8 é adicionada que tenha a prioridade mais alta. 

Esta rota incorreta de quebras de conectividade com outras redes de no local que pode pertencer a outra sub-rede dentro do intervalo 10.0.0.0/8, tais como 10.50.0.0/24, que não tenham uma rota específica definida. 

### <a name="cause"></a>Causa

Este comportamento é por predefinição para clientes Windows. Quando o cliente utiliza o protocolo PPP IPCP, obtém o endereço IP para a interface de túnel do servidor (o gateway VPN neste caso). No entanto, devido a uma limitação no protocolo, o cliente não tem a máscara de sub-rede. Porque não há nenhuma outra forma obtê-lo, o cliente tenta adivinhar a máscara de sub-rede com base na classe do endereço IP da interface de túnel. 

Por conseguinte, uma rota foi adicionada com base no mapeamento estático do seguinte: 

Se o endereço pertence à classe A--> aplicar /8

Se o endereço pertence à classe B--> aplicar /16

Se o endereço pertence à classe C--> aplicar /24

## <a name="vpn-client-cannot-access-network-file-shares"></a>Cliente VPN não é possível aceder a partilhas de ficheiros de rede

### <a name="symptom"></a>Sintoma

O cliente VPN estabeleceu ligação à rede virtual do Azure. No entanto, o cliente não é possível aceder às partilhas de rede.

### <a name="cause"></a>Causa

O protocolo SMB é utilizado para acesso de partilha de ficheiros. Quando a ligação é iniciada, o cliente VPN adiciona as credenciais de sessão e a falha ocorre. Depois da ligação for estabelecida, o cliente é forçado a utilizar as credenciais de cache para a autenticação Kerberos. Este processo inicia as consultas para o Centro de distribuição de chaves (um controlador de domínio) para obter um token. Porque o cliente se liga a partir da Internet, poderá não conseguir contactar o controlador de domínio. Por conseguinte, o cliente não é possível efetuar a ativação pós-falha do Kerberos NTLM. 

O único momento em que o cliente é pedido uma credencial é quando tem um certificado válido (com SAN = UPN) emitidos pelo domínio ao qual está associado. O cliente também tem de estar fisicamente ligado à rede de domínio. Neste caso, o cliente tenta utilizar o certificado e acede ao controlador de domínio. Em seguida, o Centro de distribuição de chaves devolve um erro de "KDC_ERR_C_PRINCIPAL_UNKNOWN". O cliente é forçado a efetuar a ativação pós-falha NTLM. 

### <a name="solution"></a>Solução

Para solucionar o problema, desative a colocação em cache de credenciais de domínio do seguinte subchave do registo: 

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\DisableDomainCreds - Set the value to 1 


## <a name="cannot-find-the-point-to-site-vpn-connection-in-windows-after-reinstalling-the-vpn-client"></a>Não é possível localizar a ligação de VPN ponto a site no Windows após a reinstalação do cliente VPN

### <a name="symptom"></a>Sintoma

Remova a ligação de VPN ponto a site e, em seguida, reinstalar o cliente VPN. Nesta situação, a ligação VPN não está configurada com êxito. Não vir a ligação VPN no **ligações de rede** definições do Windows.

### <a name="solution"></a>Solução

Para resolver o problema, elimine os ficheiros de configuração de cliente VPN antigos do **C:\Users\TheUserName\AppData\Roaming\Microsoft\Network\Connections**, e, em seguida, execute novamente o instalador do cliente VPN.
