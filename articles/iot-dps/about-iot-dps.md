---
title: "Descrição geral do serviço de aprovisionamento de dispositivos do Azure IoT Hub (pré-visualização) | Microsoft Docs"
description: "Descreve o aprovisionamento de dispositivos no Azure com o serviço de aprovisionamento de dispositivos e o IoT Hub"
services: iot-dps
keywords: 
author: nberdy
ms.author: nberdy
ms.date: 09/05/2017
ms.topic: article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: a9df3f4e27e0d6e11b9d85a44467f3c62f453121
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="provisioning-devices-with-azure-iot-hub-device-provisioning-service-preview"></a>Aprovisionamento de dispositivos com o serviço de aprovisionamento de dispositivos do Azure IoT Hub (pré-visualização)
Microsoft Azure fornece um conjunto avançado de serviços de nuvem pública integrada para todas as suas necessidades de solução de IoT. O serviço de aprovisionamento de dispositivos do IoT Hub é um serviço de programa auxiliar para o IoT Hub que permite zero touch, just-in-time aprovisionamento para o IoT hub direito sem necessidade de intervenção humana, permitindo que os clientes aprovisionar milhões de dispositivos segura e escalável forma.

## <a name="when-to-use-device-provisioning-service"></a>Quando utilizar o serviço de aprovisionamento de dispositivos
Existem vários cenários de aprovisionamento em que o serviço de aprovisionamento de dispositivos é uma excelente opção para ao obter dispositivos ligados e configurado para o IoT Hub, tais como:

* Para uma solução de IoT único sem codificar informações de ligação do IoT Hub na fábrica de (configuração inicial) de aprovisionamento de touch de zero
* Balanceamento de carga dispositivos entre vários hubs
* Ligação de dispositivos à solução de IoT respetivo proprietário com base nos dados de vendas transação (arquitetura "multitenancy")
* Dispositivos de ligação para uma solução de IoT específica, consoante o caso de utilização (isolamento de solução)
* Ligar um dispositivo ao IoT hub com a latência mais baixa (georreplicação fragmentação)
* Novamente aprovisionamento com base numa alteração no dispositivo
* A anular as chaves utilizadas pelo dispositivo para estabelecer ligação ao IoT Hub (quando não utilizar certificados x. 509 para ligar)

## <a name="behind-the-scenes"></a>Nos bastidores
Todos os cenários listados na secção anterior, podem ser feitos utilizando o serviço de aprovisionamento para o aprovisionamento de zero touch com o mesmo fluxo. Muitos dos passos manuais tradicionalmente envolvidos no aprovisionamento são automatizados com o serviço de aprovisionamento de dispositivos para reduzir o tempo para implementar dispositivos IoT e reduzir o risco de erro manual. Segue-se uma descrição do que está a suceder bastidores para obter um dispositivo aprovisionado. O primeiro passo é manual, todos os passos seguintes são automatizados.

![Fluxo básico de aprovisionamento](./media/about-iot-dps/dps-provisioning-flow.png)

1. Fabricante do dispositivo adiciona as informações de registo do dispositivo para a lista de inscrição no portal do Azure.
2. Dispositivo contacta o aprovisionamento ponto final de serviço definido na fábrica de. O dispositivo passa o serviço de aprovisionamento as informações de identificação para provar a sua identidade.
3. O serviço de aprovisionamento valida a identidade do dispositivo ao validar o ID de registo e a chave contra a entrada da lista de inscrição utilizando um desafio de nonce ([Trusted Platform Module](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/)) ou standard (de verificação de x. 509 X. 509).
4. O serviço de aprovisionamento regista o dispositivo com um hub IoT e preenche o dispositivo [pretendido Estado duplo](../iot-hub/iot-hub-devguide-device-twins.md).
5. O IoT hub devolve as informações de ID de dispositivo para o serviço de aprovisionamento.
6. O serviço de aprovisionamento devolve as informações de ligação do IoT hub para o dispositivo. O dispositivo pode agora começar a enviar dados diretamente para o IoT hub.
7. O dispositivo estabelece ligação ao IoT hub.
8. O dispositivo obtém o estado pretendido do respetivo dispositivo duplo no IoT hub.

## <a name="provisioning-process"></a>Processo de aprovisionamento
Existem dois passos distintos no processo de implementação de um dispositivo em que o serviço de aprovisionamento de dispositivo demora uma parte que pode ser feita de forma independente:

* O **fabrico passo** em que o dispositivo é criado e preparado no fábrica, e
* O **passo de configuração de nuvem** no qual o serviço de aprovisionamento de dispositivos está configurado para o aprovisionamento automatizado.

Ambos estes passos cabem numa forma totalmente integrada com processos de implementação e de produção existentes. O serviço de aprovisionamento de dispositivos, mesmo simplifica alguns processos de implementação que envolvem muitas outras tarefas manuais para obter informações de ligação no dispositivo.

### <a name="manufacturing-step"></a>Passo de fabrico
Este passo é tudo sobre o que acontece na linha de fabrico. As funções envolvidos neste passo incluem silicon designer, fabricante silicon, integrador de e/ou o fabricante de fim do dispositivo. Este passo é preocupações com a criação de hardware próprio.

O serviço de aprovisionamento de dispositivos não introduz um novo passo no processo de fabrico; em vez disso, vincula para o passo existente que instala o software inicial bem como o HSM (Idealmente) no dispositivo. Em vez de criar um ID de dispositivo neste passo, o dispositivo é simplesmente programado com as informações de aprovisionamento de serviço para chama o serviço de aprovisionamento para obter a respetiva atribuição de solução de IoT/informações de ligação quando está ativado.

Também neste passo, o fabricante fornece o implementador/operador do dispositivo, identificando informações da chave. Isto pode ser tão simple como confirmar que todos os dispositivos têm um certificado x. 509 gerado a partir de uma fornecida pela dispositivo implementador/operadora de rede, a extrair a parte pública da chave de endossamento TPM do dispositivo cada TPM de AC de raiz. Estes serviços são oferecidos por vários fabricantes de silicon hoje.

### <a name="cloud-setup-step"></a>Passo de configuração de nuvem
Este passo é sobre como configurar a nuvem para o aprovisionamento automático adequado. Geralmente, existem dois tipos de utilizadores envolvidas no passo de configuração de nuvem: alguém que sabe como dispositivos têm de ser configurar inicialmente (um operador de dispositivo) e alguém que conheça a forma como os dispositivos são para serem divididas entre os hubs IoT (um operador de solução).

Existe uma única configuração inicial de aprovisionamento que tem de ocorrer, e esta tarefa, geralmente, é processada pelo operador de solução. Depois de ter configurado o serviço de aprovisionamento, não tem de ser modificados, a menos que altera o caso de utilização.

Depois do serviço foi configurado para o aprovisionamento automático, tem de ser preparado para inscrever dispositivos. Este passo é efetuado pelo operador de dispositivo que confie configuração desejada dos dispositivos e é responsável pela certificar-se de que o serviço de aprovisionamento pode atestem corretamente para a identidade do dispositivo quando se trata de procura para o seu IoT hub. O operador de dispositivo demora as informações de chaves de identificação do fabricante e adiciona-o à lista de inscrição. Pode haver atualizações subsequentes à lista de inscrição como novas entradas são adicionadas ou entradas existentes são atualizadas com as informações mais recentes sobre os dispositivos.

## <a name="registration-and-provisioning"></a>Registo e aprovisionamento
*Aprovisionamento* significa várias coisas consoante a indústria na qual é utilizado o termo. No contexto de aprovisionamento de dispositivos de IoT a respetiva solução de nuvem, o aprovisionamento é um processo de dois parte:

1. A primeira parte consiste em estabelecer a ligação inicial entre o dispositivo e a solução de IoT ao registar o dispositivo.
2. A segunda parte estiver a aplicar a configuração adequada para o dispositivo com base nos requisitos específicos da solução que foi registado para.

Apenas depois de concluir os dois passos pode vamos supor que o dispositivo foi totalmente aprovisionado. Apenas alguns serviços em nuvem fornecem o primeiro passo do processo de aprovisionamento, registar dispositivos para o ponto de final para a solução de IoT, mas não fornecerem a configuração inicial. O serviço de aprovisionamento de dispositivos automatiza os dois passos para fornecer uma experiência totalmente integrada de aprovisionamento para o dispositivo.

## <a name="features-of-the-device-provisioning-service"></a>Funcionalidades do dispositivo do serviço de fornecimento
O serviço de aprovisionamento de dispositivos tem muitas funcionalidades que tornam ideal para aprovisionamento de dispositivos.

* **Proteger o atestado** suporte para x. 509 e identidades baseado no TPM.
* **Lista de inscrição** que contém o registo de dispositivos/grupos de dispositivos que podem antecedência register concluído. A lista de inscrição contém informações sobre a configuração pretendida do dispositivo, assim que a mesma ser registada e pode ser atualizada em qualquer altura.
* **Várias políticas de alocação** para controlar a forma como o serviço de aprovisionamento de dispositivos atribui dispositivos para os hubs IoT para suportar os cenários.
* **Os registos de monitorização e diagnóstico** para se certificar de que tudo está a funcionar corretamente.
* **Suporte de várias hub** que permite que o serviço de aprovisionamento de dispositivos para atribuir dispositivos ao mais do que um IoT hub. O serviço de aprovisionamento de dispositivos pode comunicar com os hubs entre várias subscrições do Azure.

Pode saber mais sobre os conceitos e funcionalidades envolvidas no aprovisionamento de dispositivos no [conceitos de dispositivo](concepts-device.md), [conceitos do serviço](concepts-service.md), e [conceitos de segurança](concepts-security.md).

## <a name="cross-platform-support"></a>Suporte em várias plataformas
O aprovisionamento de serviço de dispositivos, como todos os serviços do Azure IoT, funciona em diferentes plataformas com uma variedade de sistemas operativos. A pré-visualização pública suporta um conjunto limitado de idiomas/protocolos suportados, apesar de muitas mais estará disponíveis quando o serviço de aprovisionamento de dispositivos estiver geralmente disponível. Para a pré-visualização pública, o serviço de aprovisionamento de dispositivos só suporta ligações de HTTPS para operações de serviço e dispositivo. O dispositivo está SDK no C e o serviço que SDK está em c#.

## <a name="regions"></a>Regiões
O serviço de aprovisionamento de dispositivos está disponível nos EUA leste, Europa Ocidental e Sudeste asiático para a pré-visualização pública. Vamos manter que uma lista atualizada de existente e recentemente comunicada regiões para todos os serviços.

* [Regiões do Azure](https://azure.microsoft.com/regions/)

## <a name="availability"></a>Disponibilidade
Vamos manter melhor esforço disponibilidade do serviço durante a pré-visualização pública. Não há nenhum contrato de nível de serviço durante a pré-visualização pública. O [SLA do Azure](https://azure.microsoft.com/support/legal/sla/) completo explica a disponibilidade garantida do Azure em termos globais.

## <a name="quotas"></a>Quotas
Cada subscrição do Azure tem limites de quota predefinidos que pode afetar o âmbito da sua solução de IoT. O limite atual numa base por subscrição é 10 grupos de serviços de aprovisionamento de dispositivos por subscrição.

Para obter mais detalhes sobre os limites de quota:

* [Limites de serviço de subscrição do Azure](../azure-subscription-service-limits.md)

## <a name="related-azure-components"></a>Componentes do Azure relacionados
O serviço de aprovisionamento de dispositivos automatiza o aprovisionamento de dispositivos IoT hub do Azure. Saiba mais sobre [IoT Hub](https://docs.microsoft.com/en-us/azure/iot-hub/).

## <a name="next-steps"></a>Passos seguintes
Tem agora uma descrição geral do aprovisionamento de dispositivos de IoT no Azure. O passo seguinte é para experimentar um cenário de IoT ponto-a-ponto.
> [!div class="nextstepaction"]
> [Configurar o serviço de aprovisionamento de dispositivos IoT Hub com o portal do Azure](quick-setup-auto-provision.md)
> [criar e aprovisionar um dispositivo simulado](quick-create-simulated-device.md)
> [configurar o dispositivo para o aprovisionamento](tutorial-set-up-device.md)
