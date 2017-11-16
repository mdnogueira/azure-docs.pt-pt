---
title: "Segurança no Azure IoT Edge | Microsoft Docs"
description: "Segurança, a autenticação e autorização de dispositivos de IoT Edge"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 10/05/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 8a5bf1f35fcdd779cf27edeba7dfd5705cbae205
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="securing-azure-iot-edge---preview"></a>Proteger o limite de IoT do Azure - preview

Proteger o contorno inteligente é necessário confer confiança na operação de uma solução de IoT ponto a ponto. Limite de IoT do Azure foi concebida para segurança extensível para perfis de risco diferentes, cenários de implementação, e oferece a mesma proteção que se espera de todos os serviços do Azure.

Limite de IoT do Azure é executado em hardware diferente, suporta Linux e Windows e é aplicável a cenários de implementação diversos.  Risco avaliado depende em conta muitos aspetos, incluindo a propriedade de solução, geografia de implementação, confidencialidade de dados, privacidade, requisitos de vertical e de regulamentação de aplicação.  Em vez de oferta concretas soluções para cenários específicos, faz sentido para estruturar uma arquitetura extensível segurança com base no princípios bem grounded concebidos para dimensionamento. 
 
Este artigo fornece uma descrição geral do framework de segurança. Para obter mais informações, consulte [proteger o contorno inteligente][lnk-edge-blog].

>[!NOTE]
>O framework de segurança descrito abaixo é a adicionar ao produto agora e estará disponível na versão de disponibilidade geral do Azure IoT Edge. O produto atualmente em pré-visualização pública, uma versão destina-se permitir o desenvolvimento e fazer o protótipo das soluções de limite, as implementações de produção não completo que têm o framework de segurança completa.   

## <a name="standards"></a>Normas

As normas promover o facilitar scrutiny e facilidade de implementação, que são hallmark de segurança.  Uma solução de segurança bem nova deve cada scrutiny em avaliação para criar confiança e não deve ser um hurdle à implementação.  A estrutura da estrutura de proteger o limite do Azure IoT emanates de time-tested e protocolos de segurança da indústria comprovada tirar partido familiaridade e reutilização. 

## <a name="authentication"></a>Autenticação

É essencial na criação de confiança saber sem uma dúvida que atores, dispositivos e componentes estão a participar na entrega de valor através de uma solução de IoT ponto a ponto.  Esses dados de conhecimento oferece accountability segura de participantes a ativação de base para admissão.  Limite de IoT do Azure attains estes conhecimentos através da autenticação.  O mecanismo de principal para a autenticação para a plataforma do Azure IoT Edge é autenticação baseada em certificado.  Este mecanismo deriva de um conjunto de normas que rege a infraestrutura de chaves públicas (PKiX) pela Internet Engineering Task Force (IETF).     

Chama o framework de segurança do Azure IoT Edge para identidades de certificado exclusivo para todos os dispositivos, módulos (contentores que encapsulam lógica dentro do dispositivo) e atores interagir com o dispositivo de limite de IoT do Azure seja fisicamente ou através de uma ligação de rede.  Não cada cenário ou componente poderá cada autenticação baseada em certificado para o qual a extensibilidade do framework de segurança oferece seguras vias para accommodation. 

## <a name="authorization"></a>Autorização

A capacidade para delegar o acesso de autoridade e o controlo é fundamental para alcançar um principal de segurança fundamentais – o princípio do menor privilégio.  Dispositivos, módulos e atores obter acesso apenas para dados dentro do âmbito da sua permissão e apenas quando é permitido em termos de arquitetura e de recursos.  Isto significa que alguns permissões são configuráveis com privilégios e outros em termos de arquitetura imposta suficientes.  Por exemplo, enquanto um módulo pode ser autorizado através da configuração com privilégios para iniciar uma ligação ao IoT Hub do Azure, não há nenhum motivo por que razão um módulo de um dispositivo de limite de IoT do Azure deve aceder duplo de um módulo no outro dispositivo de limite de IoT do Azure.  Por este motivo, a última opção seria possível em termos de arquitetura precluded. 

Outros esquemas de autorização incluem direitos de assinatura de certificado e o controlo de acesso baseado em funções (RBAC).  A extensibilidade do framework de segurança permite a adoção de outros esquemas de autorização madura. 

## <a name="attestation"></a>Atestado

Atestado assegura a integridade de bits de software.  É importante para a deteção e prevenção de software maligno.  O framework de segurança do Azure IoT Edge classifica atestado em três categorias principais:

* Atestado estático
* Atestado de tempo de execução
* Atestado de software

### <a name="static-attestation"></a>Atestado estático

Atestado estático é a verificação de integridade de todos os bits de software, incluindo os sistemas operativos, todos os tempos de execução, e informações de configuração no dispositivo energia cópia de segurança.  Este é frequentemente referido como o arranque seguro.  A arquitetura de segurança de dispositivos de limite de IoT do Azure expande para fornecedores de silicon e incorpora capacidades de hardware seguro ingrained para assegurar que os processos de atestado estático. Estes processos incluem arranque seguro e firmware segura atualizar processos.  Trabalhar em colaboração fechar com fornecedores de silicon elimina as camadas de firmware supérfluas assim que minimiza a superfície de ameaça. 

### <a name="runtime-attestation"></a>Atestado de tempo de execução

Depois de um sistema tem de concluir um processo de arranque validado e está a funcionar e em execução, bem estruturados sistemas seguros seriam detetar tenta injetar software maligno através de portas de sistemas e interfaces e tomar medidas preventivas adequadas.  Para dispositivos de limite inteligente no físico custódia dos atores maliciosos, é possível inserir malcontent através de meios que não sejam de interfaces de dispositivo, como a adulteração e do lado do canal de ataques.   Essa malcontent, que pode ter o formato de software maligno ou alterações de configuração não autorizado, normalmente, não seria possível detetar pelo processo de arranque seguro porque estes ocorrem após o processo de arranque.  Medidas preventivas oferecidas ou imposto pelo hardware do dispositivo significativamente contribui para warding desativar estas ameaças.  O framework de segurança para o Azure IoT Edge explicitamente chama a atenção para as extensões para combatting ameaças de tempo de execução.     

### <a name="software-attestation"></a>Atestado de software

Todos os sistemas de bom estado de funcionamento, incluindo sistemas inteligente contorno tem de ser propensos a correções de erros e atualizações.  Segurança é importante para os processos de atualização podem ser vetores de ameaças potenciais.  O framework de segurança para chamadas de limite de IoT do Azure para atualizações através de medido e assinado pacotes para garantir a integridade e autenticar-se a origem dos pacotes.  Isto é aplicável a todos os sistemas operativos e bits de software da aplicação. 

## <a name="hardware-root-of-trust"></a>Raiz de hardware de confiança

Para muitas implementações do dispositivos de limite inteligente, especialmente dos implementados em locais onde atores maliciosos potenciais tenham acesso físico ao dispositivo, a segurança oferecida pelo hardware do dispositivo é a último defesa para proteção.  Por este motivo, a confiança anchoring no hardware resistente a adulterações é máximo crucial para implementações deste tipo.  O framework de segurança para o Azure IoT Edge envolve a colaboração segura silicon fabricantes de hardware diferentes tipos de raiz de hardware de confiança para acomodar os vários cenários de implementação de perfis de risco e a oferta. Estes incluem a utilização de silicon segura a cumprir os padrões de protocolo de segurança comuns, como o Trusted Platform Module (ISO/IEC 11889) e dispositivos identificador composição motor (ORGANIZAM fidedigno informática do grupo de).  Estes incluem também enclave segura de tecnologias como TrustZones e extensões de proteção de Software (SGX). 

## <a name="certification"></a>Certificação

Para ajudar a tomar decisões informadas ao aquisição de dispositivos de limite de IoT do Azure para as suas implementações de clientes, a arquitetura de limite de IoT do Azure inclui os requisitos de certificação.  Fundamentais sobre estes requisitos são certificações relativas à segurança afirmações e certificações relativas a validação da implementação de segurança.  Por exemplo, uma certificação de afirmação de segurança seria informar que o dispositivo de limite de IoT utiliza o hardware seguro conhecido resist ataques de arranque. Certificação uma validação seria informar que o hardware seguro foi implementado corretamente para oferecer este valor no dispositivo.  In keeping with o princípio de simplicidade, a estrutura oferece a visão manter o fardo de certificação mínima.   

## <a name="extensibility"></a>Extensibilidade

Extensibilidade é uma primeira classe citizen no framework de segurança do Azure IoT Edge.  Com a tecnologia de IoT a ocasionar a diferentes tipos de transformações de negócio,-representa pelo motivo que a segurança deve perfeitamente evoluir no lockstep para endereço emergentes cenários.  O framework de segurança do Azure IoT Edge começa com uma base sólida no qual se baseia-se no extensibilidade em dimensões diferentes para incluir: 

* Primeiro serviços de segurança de terceiros, como o serviço de aprovisionamento de dispositivos de IoT Hub do Azure.
* Serviços de terceiros, como serviços de segurança geridos para verticals diferente da aplicação (como industriais ou cuidados de saúde) ou o foco de tecnologia (como a monitorização em segurança mesh redes ou os serviços de atestado de hardware silicon) através de uma rede de avançado parceiros.
* Sistemas legados para incluir retrofitting com estratégias de segurança alternativas, incluindo utilizando a tecnologia segura que não sejam certificados para a gestão de identidade e autenticação.
* Proteja o hardware para totalmente integrada adoção de tecnologias emergentes de hardware seguro e silicon contribuições de parceiro.

Estes são alguns exemplos de dimensões extensibilidade e framework de segurança do Azure IoT Edge foi concebido para ser seguro a partir do zero para suportar este extensibilidade. 

No final, o êxito proteger inteligente limite mais alto resulta da colaboração contribuições de uma Comunidade abra condicionadas pelo interesse comuns proteger IoT.  Estes contribuições poderão estar sob a forma de tecnologias seguras ou serviços.  O framework de segurança do Azure IoT Edge oferece uma base sólida para segurança extensível para a cobertura máxima oferecer o mesmo nível de confiança e integridade no limite inteligente como com a nuvem do Azure.  

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como é o Azure IoT Edge [proteger o contorno inteligente][lnk-edge-blog].

<!-- Links -->
[lnk-edge-blog]: https://azure.microsoft.com/blog/securing-the-intelligent-edge/ 