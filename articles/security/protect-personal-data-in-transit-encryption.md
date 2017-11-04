---
title: "Proteger dados pessoais em trânsito através de encriptação no Azure | Microsoft Docs"
description: "Utilizar a encriptação no Azure para proteger os dados pessoais"
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2017
ms.author: barclayn
ms.custom: 
ms.openlocfilehash: 461ddfda796bfe6639e27f6c4cd53e82b4e397d1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-encryption-technologies-protect-personal-data-in-transit-with-encryption"></a>Tecnologias de encriptação do Azure: proteger os dados pessoais em trânsito através da encriptação

Este artigo irá ajudá-lo a compreender e utilizar tecnologias de encriptação do Azure para proteger os dados em trânsito. 

Proteger a privacidade dos dados pessoais como que circula na rede é uma parte essencial de uma estratégia de segurança por várias camadas de defesa em profundidade. Encriptação em trânsito foi concebida para impedir que o atacante interceta as transmissões de conseguir ver ou utilizar os dados.

## <a name="scenario"></a>Cenário

Uma empresa de grande cruise headquartered nos Estados Unidos, está a expandir as suas operações para oferecer itineraries no Mediterranean, Adriatic e Baltic seas, bem como o Isles território. Para suportar os esforços, obteve várias linhas cruise mais pequenas com base em (Itália), na Alemanha, Dinamarca e o U.K. 

A empresa utiliza o Microsoft Azure para armazenar dados empresariais na nuvem. Isto inclui informações pessoais tais como nomes, endereços, números de telefone e informações de cartão de crédito da respetiva base de cliente global. Também inclui informações de recursos humanos tradicionais, como endereços, números de telefone, os números de identificação de dedução dos impostos e outras informações sobre os funcionários da empresa em todas as localizações. A linha cruise mantém também uma grande base de dados de membros de programa reward e loyalty que inclui informações pessoais, para controlar as relações com clientes atuais e anteriores.

Dados pessoais dos clientes são introduzidos na base de dados de escritórios remotos da empresa e de agentes levar localizados em todo o mundo. Documentos que contenham informações de cliente são transferidos através da rede ao armazenamento do Azure.

## <a name="problem-statement"></a>Declaração do problema

A empresa tem de proteger a privacidade dos dados pessoais dos clientes e dos empregados enquanto esta se encontra em trânsito e de serviços do Azure.

## <a name="company-goal"></a>Objetivo da empresa

O objetivo da empresa para se certificar de que os dados pessoais são encriptados quando desativar o disco. Se as pessoas não autorizadas interceta os dados pessoais fora do disco, tem de ter um formulário que irá compor ilegível. Aplicar encriptação deve ser fácil ou completamente transparente para os administradores e utilizadores.

## <a name="solutions"></a>Soluções

Serviços do Azure fornecem várias ferramentas e tecnologias para ajudar a proteger os dados pessoais em trânsito.

### <a name="azure-storage"></a>Storage do Azure

Os dados armazenados na nuvem tem de se deslocar do cliente, que pode estar localizado fisicamente em qualquer lugar do mundo, para o Centro de dados do Azure. Quando os dados são obtidos por utilizadores, mudar novamente, na direção oposta. Dados que se encontram em trânsito através da Internet pública estão sempre em risco de interception pelos atacantes. É importante proteger a privacidade dos dados pessoais através de encriptação de nível de transporte para proteger são transmitidos entre localizações.

O protocolo HTTPS fornece um canal de comunicações seguro e encriptado através da Internet. HTTPS deve ser utilizado para aceder a objetos no Storage do Azure e ao chamar as APIs REST. Impor a utilização do protocolo HTTPS quando utilizar [assinaturas de acesso partilhado](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) (SAS) para delegar o acesso a objetos de armazenamento do Azure. Existem dois tipos de SAS: serviço SAS e conta SAS.

#### <a name="how-do-i-construct-a-service-sas"></a>Como posso construir uma SAS de serviço?

Um serviço SAS delega o acesso a um recurso em apenas um dos serviços de armazenamento (blob, fila, tabela ou ficheiro de serviço). Para construir um serviço SAS, efetue o seguinte:

1. Especificar o campo de versão assinado

2. Especifique os recursos assinados (Blob e apenas o serviço de ficheiros)

3. Especifique parâmetros de consulta para substituir os cabeçalhos de resposta (serviço Blob e o serviço de ficheiros apenas)

4. Especifique o nome da tabela (apenas para serviço de tabela)

5. Especificar a política de acesso

6. Especifique o intervalo de validade de assinatura

8. Especificar permissões

9. Especifique o endereço IP ou intervalo de IP

10. Especifique o protocolo HTTP

11. Especifique os intervalos de acesso de tabela

12. Especifique o identificador assinado

13. Especifique a assinatura

Para obter instruções mais detalhadas, consulte [construir um serviço SAS](https://docs.microsoft.com/rest/api/storageservices/Constructing-a-Service-SAS?redirectedfrom=MSDN).

#### <a name="how-do-i-construct-an-account-sas"></a>Como construir o SAS de uma conta?

Uma conta SAS delega o acesso a recursos em um ou mais dos serviços de armazenamento. Também pode delegar o acesso às operações de leitura, escrita e eliminação em contentores de blobs, tabelas, filas e partilhas de ficheiros que não são permitidos com um serviço SAS. A construção de uma conta SAS é semelhante ao de um serviço SAS. Para obter instruções detalhadas, consulte [construir uma conta SAS.](https://docs.microsoft.com/rest/api/storageservices/Constructing-an-Account-SAS?redirectedfrom=MSDN)

#### <a name="how-do-i-enforce-https-when-calling-rest-apis"></a>Como impor HTTPS ao chamar as APIs REST?

Para impor a utilização de HTTPS ao chamar as APIs REST para objetos de acesso em contas do storage, pode ativar a proteger transferência necessário para a conta de armazenamento. 

1. No portal do Azure, selecione **criar conta de armazenamento**, ou para uma conta de armazenamento existente, selecione **definições** e, em seguida, **configuração**.

2. Em **Secure transferência necessário**, selecione **ativado**.

![Criar uma conta de armazenamento](media/protect-personal-data-in-transit-encryption/create-storage-account.png)

Para obter instruções mais detalhadas, incluindo como ativar Secure transferência necessário através de programação, consulte [necessitam de proteger transferência](https://docs.microsoft.com/azure/storage/storage-require-secure-transfer).

#### <a name="how-do-i-encrypt-data-in-azure-file-storage"></a>Como encriptar dados na File Storage do Azure?

Para encriptar os dados em trânsito com [File Storage do Azure](https://docs.microsoft.com/azure/storage/storage-file-how-to-use-files-portal), pode utilizar o SMB 3 com o Windows 8, 8.1 e 10 e Windows Server 2012 R2 e Windows Server 2016. Quando estiver a utilizar o serviço de ficheiros do Azure, todas as ligações sem encriptação falha quando "Seguro transferência necessária" está ativada. Isto inclui cenários utilizando o SMB 2.1, SMB 3.0 sem encriptação e alguns tipos do cliente SMB do Linux.

#### <a name="azure-client-side-encryption"></a>Encriptação do lado do cliente do Azure

Outra opção para proteger os dados pessoais enquanto está a ser transferido entre uma aplicação de cliente e o armazenamento do Azure é [encriptação do lado do cliente](https://docs.microsoft.com/azure/storage/storage-client-side-encryption). Os dados são encriptados antes de serem transferidos para o armazenamento do Azure e ao obter os dados do armazenamento do Azure, os dados são desencriptados depois recebido do lado do cliente.

### <a name="azure-site-to-site-vpn"></a>VPN de Site para Site do Azure

Uma forma eficaz para proteger os dados pessoais em trânsito entre uma rede empresarial ou utilizador e a rede virtual do Azure está a utilizar um [site para site](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) ou [ponto a site](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal) rede privada Virtual (VPN). Uma ligação VPN cria um túnel seguro encriptado através da Internet.

#### <a name="how-do-i-create-a-site-to-site-vpn-connection"></a>Como posso criar uma ligação de VPN de site a site?

Uma VPN de site para site liga-se vários utilizadores na rede empresarial para o Azure. Para criar uma ligação site a site no portal do Azure, efetue o seguinte:

1. Crie uma rede virtual

2. Especifique um servidor DNS.

3. Crie a sub-rede de gateway.

4. Crie o gateway VPN. 

    ![](media/protect-personal-data-in-transit-encryption/vpn-step-01.png)

5. Crie o gateway de rede local.

    ![](media/protect-personal-data-in-transit-encryption/vpn-step-02.png)

6. Configure o seu dispositivo VPN.

7. Crie a ligação VPN.

    ![](media/protect-personal-data-in-transit-encryption/vpn-step-03.png)

8. Certifique-se a ligação VPN.

Para obter instruções mais detalhadas sobre como criar uma ligação site a site no portal do Azure, consulte [criar uma ligação Site a Site no Portal do Azure.] (https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal)

#### <a name="how-do-i-create-a-point-to-site-vpn-connection"></a>Como posso criar uma ligação de VPN ponto a site?

Uma VPN ponto a Site cria uma ligação segura a partir de um computador cliente individual para uma rede virtual. Isto é útil quando pretende ligar ao Azure a partir de uma localização remota, como, a partir do Centro de casa ou um átrios ou conferência. Para criar uma ligação de ponto a site no portal do Azure

1. Crie uma rede virtual

2. Adicione uma sub-rede de gateway.

3. Especifique um servidor DNS. (opcional)

4. Crie um gateway de rede virtual.

5. Gere os certificados.

6. Adicione o conjunto de endereços de cliente.

7. Carregar os dados de certificado pública do certificado de raiz.

8. Gerar e instale o pacote de configuração de cliente VPN.

9. Instale um certificado de cliente exportado.

10. Ligar ao Azure.

11. Verifique a ligação.

Para obter instruções mais detalhadas, consulte [configurar uma ligação ponto a Site para uma VNet com autenticação de certificados: Portal do Azure.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal)

### <a name="ssltls"></a>SSL/TLS

A Microsoft recomenda que utilize sempre protocolos SSL/TLS para trocar dados entre localizações diferentes. As organizações que optar por utilizar [ExpressRoute](https://docs.microsoft.com/azure/expressroute/) mover grandes conjuntos de dados através de uma WAN de alta velocidade dedicada ligação também pode encriptar os dados ao nível da aplicação a utilizar SSL/TLS ou outros protocolos para proteção adicionada.

### <a name="encryption-by-default"></a>Encriptação por predefinição

A Microsoft utiliza encriptação para proteger dados em trânsito entre clientes e serviços em nuvem do Azure. Se interage com o Storage do Azure através do Portal do Azure, todas as transações ocorrem através de HTTPS.

[Segurança de camada de transporte](https://en.wikipedia.org/wiki/Transport_Layer_Security) (TLS) é o protocolo de centros de dados da Microsoft irão tentar negociar com sistemas de cliente que se ligam aos serviços em nuvem da Microsoft. TLS fornece autenticação forte, privacidade de mensagens e integridade (permite a deteção de adulteração de mensagem, interception e falsificação), interoperabilidade, flexibilidade do algoritmo, facilidade de implementação e utilização.

[Perfect Forward Secrecy](https://en.wikipedia.org/wiki/Forward_secrecy) (PFS) também é utilizada para que cada ligação entre sistemas de cliente dos clientes e serviços em nuvem da Microsoft utilizar chaves exclusivas. Ligações a serviços em nuvem da Microsoft também tirar partido da encriptação de 2,048-bit RSA com base comprimentos de chave. A combinação de TLS, comprimentos de chave RSA 2,048-bit, e PFS torna muito mais difícil alguém intercept e acesso dados que estão em trânsito entre os cloud services da Microsoft e os clientes.

Dados em trânsito são sempre encriptados no [Data Lake Store] (https://docs.microsoft.com/azure/data-lake-store/data-lake-store-security-overview). Para além de encriptar os dados antes de serem armazenados em suportes de dados persistentes, os dados também são sempre protegidos em trânsito com HTTPS. O HTTPS é o único protocolo que as interfaces REST do Data Lake Store suportam.

## <a name="summary"></a>Resumo

A empresa pode realizar o seu objetivo de proteger os dados pessoais e a privacidade desses dados impor as ligações HTTPS ao Storage do Azure, utilizando assinaturas de acesso partilhado e ativar o Secure transferência necessário as contas de armazenamento. Pode também protegem os dados pessoais ao utilizar ligações de SMB 3.0 e implementar a encriptação do lado do cliente. Ligações de VPN de site para site da rede empresarial para a Azure virtual network e ligações de VPN ponto a site a utilizadores individuais, irão criar um túnel seguro através do qual os dados pessoais podem viajam com segurança. Práticas de encriptação de predefinida da Microsoft mais irão proteger a privacidade dos dados pessoais.

## <a name="next-steps"></a>Passos seguintes

- [Melhores práticas de segurança de dados do Azure e encriptação](https://docs.microsoft.com/azure/security/azure-security-data-encryption-best-practices)

- [Planear e conceber para o Gateway de VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design)

- [FAQ do Gateway de VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vpn-faq)

- [Comprar e configurar um certificado SSL para o serviço de aplicações do Azure](https://docs.microsoft.com/azure/app-service/web-sites-purchase-ssl-web-site)
