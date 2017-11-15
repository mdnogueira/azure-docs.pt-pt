---
title: "Configure o HTTPS um domínio personalizado de rede de entrega de conteúdos do Azure | Microsoft Docs"
description: "Saiba como ativar ou desativar HTTPS no ponto final de CDN do Azure com um domínio personalizado."
services: cdn
documentationcenter: 
author: dksimpson
manager: 
editor: 
ms.assetid: 10337468-7015-4598-9586-0b66591d939b
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2017
ms.author: casoper
ms.openlocfilehash: 82de79cde208cdce1ed7cbd600f1e804ff1d45ff
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2017
---
# <a name="configure-https-on-an-azure-content-delivery-network-custom-domain"></a>Configure o HTTPS um domínio personalizado de rede de entrega de conteúdos do Azure

[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Microsoft suporta o protocolo HTTPS para domínios personalizados na rede de entrega de conteúdos (CDN) do Azure. Com suporte de domínio personalizado de HTTPS, pode fornecer segura do conteúdo através de SSL com o seu próprio nome de domínio para melhorar a segurança dos dados em trânsito. O fluxo de trabalho para ativar HTTPS para o domínio personalizado é simplificado através de ativação de um clique e gestão de certificados completo, todos os sem custos adicionais.

É fundamental para garantir a privacidade e a integridade dos dados de dados confidenciais da sua aplicação web enquanto esta se encontra em trânsito. Ao utilizar o protocolo HTTPS, certifique-se de que os dados sensíveis são encriptados quando é enviado através da internet. Fornece confiança de autenticação e protege as suas aplicações web contra ataques. Por predefinição, a CDN do Azure suporta HTTPS no ponto final da CDN. Por exemplo, se criar um ponto final da CDN do Azure CDN (por exemplo, `https://contoso.azureedge.net`), HTTPS é ativado automaticamente. Além disso, com suporte HTTPS de domínio personalizado, também pode ativar a entrega segura para um domínio personalizado (por exemplo, `https://www.contoso.com`). 

Alguns dos principais atributos da funcionalidade HTTPS são:

- Sem custos adicionais: existem sem custos de aquisição do certificado ou a renovação e sem custos adicionais para tráfego HTTPS. Só paga pelos GB de saída da CDN.

- Ativação Simple: aprovisionamento de um clique está disponível a partir de [portal do Azure](https://portal.azure.com). Também pode utilizar a REST API ou outras ferramentas de programador para ativar a funcionalidade.

- Conclua a gestão de certificados: todos os certificados de aprovisionamento e gestão é processada por si. Certificados são automaticamente aprovisionados e renovados antes da expiração, o que elimina os riscos de interrupção do serviço devido a um certificado prestes a expirar.

>[!NOTE] 
>Antes de ativar o suporte de HTTPS, tem de ter já estabeleceu um [domínio personalizado de CDN do Azure](./cdn-map-content-to-custom-domain.md).

## <a name="enabling-https"></a>Ativar HTTPS

Para ativar HTTPS, um domínio personalizado, siga estes passos:

### <a name="step-1-enable-the-feature"></a>Passo 1: Ativar a funcionalidade 

1. No [portal do Azure](https://portal.azure.com), navegue para o perfil de CDN da Verizon standard ou premium.

2. Na lista de pontos finais, clique no ponto final que contém o domínio personalizado.

3. Clique no domínio personalizado para o qual pretende ativar HTTPS.

    ![Lista de domínios personalizados](./media/cdn-custom-ssl/cdn-custom-domain.png)

4. Clique em **no** para ativar HTTPS, em seguida, clique em **aplicar**.

    ![Estado HTTPS de domínio personalizado](./media/cdn-custom-ssl/cdn-enable-custom-ssl.png)


### <a name="step-2-validate-domain"></a>Passo 2: Validar o domínio

>[!IMPORTANT] 
>Validação do domínio deve ser concluída antes de HTTPS estará ativo no seu domínio personalizado. Tem de seis dias úteis para aprovar o domínio. Pedidos que não estão aprovados dentro de seis dias úteis automaticamente foram cancelados. 

Depois de ativar HTTPS no seu domínio personalizado, a autoridade de certificação (AC) de DigiCert valida a propriedade do seu domínio ao contactar o respetivo registrant, de acordo com o domínio [WHOIS](http://whois.domaintools.com/) informações registrant. Contacte é efetuada através do endereço de e-mail (por predefinição) ou o número de telefone listados no registo WHOIS. 

>[!NOTE]
>Se tiver um registo de autorização de autoridade de certificado (CAA) com o seu fornecedor DNS, tem de incluir DigiCert como uma AC válida. Um registo de CAA permite que os proprietários de domínio especificar os seus fornecedores de serviços de DNS que as ACs estão autorizados a emitir certificados para os domínios. Se uma AC recebe uma ordem de um certificado para um domínio que tenha um registo de CAA e essa AC não está indicada como um emissor autorizado, é proibido de emitir o certificado para esse domínio ou um subdomínio. Para obter informações sobre como gerir registos de CAA, consulte [registos gerir CAA](https://support.dnsimple.com/articles/manage-caa-record/). Para obter uma ferramenta de registo CAA, consulte [CAA registo auxiliar](https://sslmate.com/caa/).

![Registo WHOIS](./media/cdn-custom-ssl/whois-record.png)

DigiCert também envia uma mensagem de verificação para endereços de e-mail adicionais. Se as informações de registrant WHOIS são privadas, certifique-se de que podem aprovar diretamente a partir de um dos seguintes endereços:

administração @&lt;name.com o domínio&gt;  
administrador @&lt;name.com o domínio&gt;  
Webmaster @&lt;name.com o domínio&gt;  
hostmaster @&lt;name.com o domínio&gt;  
postmaster @&lt;name.com o domínio&gt;  

Deverá receber uma mensagem de e-mail dentro de alguns minutos, semelhantes ao seguinte exemplo, pedir-lhe aprovar o pedido. Se estiver a utilizar um filtro de spam, adicione admin@digicert.com a lista branca. Se não receber uma mensagem de e-mail dentro de 24 horas, contacte o suporte da Microsoft.
    
![Correio eletrónico de validação do domínio](./media/cdn-custom-ssl/domain-validation-email.png)

Ao clicar na ligação de aprovação, o utilizador é direcionado para o seguinte formato de aprovação online: 
    
![Formulário de validação do domínio](./media/cdn-custom-ssl/domain-validation-form.png)

Siga as instruções no formulário; tem duas opções de verificação:

- Pode aprovar todas as ordens futuras colocadas através da mesma conta para o mesmo domínio de raiz; Por exemplo, `contoso.com`. Esta abordagem é recomendada se planear adicionar domínios personalizados adicionais para o mesmo domínio de raiz.

- Pode aprovar apenas o nome de anfitrião específico utilizado neste pedido. Aprovação adicional é necessária para os pedidos subsequentes.

Após a aprovação, DigiCert adiciona o seu nome de domínio personalizado para o certificado de nomes de alternativo do requerente (SAN). O certificado é válido durante um ano e será automaticamente-renovada antes de ter expirado.

### <a name="step-3-wait-for-propagation"></a>Passo 3: Aguardar propagação

Depois do nome de domínio é validado, pode demorar até 6 8 horas para a funcionalidade HTTPS de domínio personalizado para ser ativado. Quando o processo estiver concluído, o estado HTTPS personalizado no portal do Azure está definido como **ativado** e os passos de operação de quatro na caixa de diálogo de domínio personalizado são marcados como concluídos. O domínio personalizado está agora pronto para utilizar HTTPS.

![Ativar a caixa de diálogo HTTPS](./media/cdn-custom-ssl/cdn-enable-custom-ssl-complete.png)

### <a name="operation-progress"></a>Progresso da operação

A tabela seguinte mostra o progresso da operação que ocorre quando ativar HTTPS. Depois de ativar HTTPS, quatro passos de operação aparecem na caixa de diálogo de domínio personalizado. À medida que cada passo for Active Directory, detalhes adicionais são apresentados no passo como que avança. Depois de um passo concluir com êxito, é apresentada uma marca de verificação verde junto ao mesmo. 

| Passo de operação | Detalhes do passo de operação | 
| --- | --- |
| 1 submeter pedido | A submeter o pedido |
| | O pedido HTTPS está a ser submetido. |
| | O pedido HTTPS foi submetido com êxito. |
| Validação de domínio 2 | Tem a enviada uma mensagem de e-mail a pedir-lhe para validar a propriedade de domínio. A aguardar que a sua confirmação. |
| | A propriedade de domínio foi validada com êxito. |
| | Pedido de validação de propriedade de domínio expirou (cliente provavelmente não responder dentro de dias 6). HTTPS não será ativado no seu domínio. * |
| | Pedido de validação de propriedade de domínio foi rejeitado pelo cliente. HTTPS não será ativado no seu domínio. * |
| Aprovisionamento de certificados 3 | A autoridade de certificação está atualmente a emitir o certificado necessário para ativar HTTPS no seu domínio. |
| | O certificado foi emitido e está atualmente a ser implementado à rede da CDN. Isto pode demorar até 6 horas. |
| | O certificado foi implementado com êxito à rede da CDN. |
| 4 concluída | HTTPS foi ativado com êxito no seu domínio. |

\*Esta mensagem não aparecer, a menos que ocorreu um erro. 

Se ocorrer um erro antes do pedido ser submetido, será apresentada a seguinte mensagem de erro:

<code>
We encountered an unexpected error while processing your HTTPS request. Please try again and contact support if the issue persists.
</code>

## <a name="disabling-https"></a>Desativar HTTPS

Depois de ter ativado HTTPS um domínio personalizado, pode desativá-lo mais tarde. Para desativar o HTTPS, siga estes passos:

### <a name="step-1-disable-the-feature"></a>Passo 1: Desativar a funcionalidade 

1. No [portal do Azure](https://portal.azure.com), navegue para o perfil de CDN da Verizon standard ou premium.

2. Na lista de pontos finais, clique no ponto final que contém o domínio personalizado.

3. Clique no domínio personalizado para o qual pretende desativar o HTTPS.

    ![Lista de domínios personalizados](./media/cdn-custom-ssl/cdn-custom-domain-HTTPS-enabled.png)

4. Clique em **desativar** para desativar o HTTPS, em seguida, clique em **aplicar**.

    ![Caixa de diálogo HTTPS personalizada](./media/cdn-custom-ssl/cdn-disable-custom-ssl.png)

### <a name="step-2-wait-for-propagation"></a>Passo 2: Aguardar propagação

Depois da funcionalidade HTTPS de domínio personalizado está desativada, pode demorar até 6 8 horas para este efeito. Quando o processo estiver concluído, o estado HTTPS personalizado no portal do Azure está definido como **desativado** e os passos de operação de três na caixa de diálogo de domínio personalizado são marcados como concluídos. O domínio personalizado já não pode utilizar o HTTPS.

![Desative a caixa de diálogo HTTPS](./media/cdn-custom-ssl/cdn-disable-custom-ssl-complete.png)

### <a name="operation-progress"></a>Progresso da operação

A tabela seguinte mostra o progresso da operação que ocorre quando desativar o HTTPS. Depois de desativar HTTPS, três passos de operação aparecem na caixa de diálogo de domínio personalizado. À medida que cada passo for Active Directory, são apresentados detalhes adicionais no passo. Depois de um passo concluir com êxito, é apresentada uma marca de verificação verde junto ao mesmo. 

| Progresso da operação | Detalhes de operação | 
| --- | --- |
| 1 submeter pedido | A submeter o pedido |
| 2 desaprovisionamento de certificado | A eliminar o certificado |
| 3 concluída | Certificado eliminado |

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

1. *Quem é o fornecedor de certificado e o tipo de certificado que é utilizado?*

    A Microsoft utiliza um certificado de nomes de alternativo do requerente (SAN) fornecido pelo DigiCert. Um certificado SAN pode proteger vários nomes de domínio completamente qualificado com um certificado.

2. *Pode utilizar a minha certificados dedicado?*
    
    Atualmente não, mas é no plano.

3. *E se não receber correio eletrónico de verificação de domínio do DigiCert?*

    Se não receber uma mensagem de e-mail dentro de 24 horas, contacte o suporte da Microsoft.

4. *Está a utilizar um certificado SAN menos seguro do que um certificado dedicado?*
    
    Um certificado SAN segue as normas de segurança e de encriptação mesmas como um certificado dedicado. Todos os certificados SSL emitidos utilizarem SHA-256 para segurança do servidor avançada.

5. *Pode utilizar um domínio personalizado HTTPS com o Azure CDN da Akamai?*

    Atualmente, esta funcionalidade só está disponível com a CDN do Azure da Verizon. Microsoft está a funcionar no suporte esta funcionalidade com o Azure CDN da Akamai nos próximos meses.

6. *É necessário um registo de autorização de autoridade do certificado com a minha fornecedor DNS?*
   Não, um registo de autorização de autoridade do certificado não é atualmente necessário. No entanto, se tiver um, tem de incluir DigiCert como uma AC válida.


## <a name="next-steps"></a>Passos seguintes

- Saiba como configurar um [domínio personalizado no ponto final de CDN do Azure](./cdn-map-content-to-custom-domain.md)


