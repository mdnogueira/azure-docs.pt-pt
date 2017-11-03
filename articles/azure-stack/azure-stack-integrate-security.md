---
title: "Integração do Centro de dados do Azure pilha - segurança"
description: "Saiba como integrar a segurança de pilha do Azure com a segurança do Centro de dados"
services: azure-stack
author: troettinger
ms.service: azure-stack
ms.topic: article
ms.date: 10/17/2017
ms.author: victorh
keywords: 
ms.openlocfilehash: eb7c651362838d44d6558e080e6130b4a8041d1e
ms.sourcegitcommit: bd0d3ae20773fc87b19dd7f9542f3960211495f9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2017
---
# <a name="azure-stack-datacenter-integration---security"></a>Integração do Centro de dados do Azure pilha - segurança

*Aplica-se a: Azure pilha integrado sistemas*

Pilha do Azure foi concebida e criada com a segurança em mente. Pilha do Azure é um sistema de baixo bloqueado, pelo que não é suportada a instalação do agente de segurança de software.

Este artigo ajuda-o a integrar as funcionalidades de segurança da pilha do Azure com as soluções de segurança já implementadas no seu centro de dados.

## <a name="security-logs"></a>Registos de segurança

Pilha do Azure recolhe os eventos de segurança para nós de unidade de escala e as funções de infraestrutura e sistema operativo cada dois minutos. Os registos são armazenados em contentores de BLOBs de conta de armazenamento.

Há uma conta de armazenamento por função de infraestrutura e uma conta de armazenamento geral para todos os eventos de sistema de operativo comuns.

O fornecedor de recursos do Estado de funcionamento pode ser chamado através do protocolo REST para obter o URL para o contentor de blob. Soluções de segurança de terceiros podem utilizar as contas de armazenamento e de API para obter eventos para processamento.

### <a name="use-azure-storage-explorer-to-view-events"></a>Utilize o Explorador de armazenamento do Azure para ver eventos

Pode obter eventos recolhidos pela pilha de Azure utilizando uma ferramenta chamada Explorador de armazenamento do Azure. Pode transferir o Explorador de armazenamento do Azure de [http://storageexplorer.com](http://storageexplorer.com).

O procedimento seguinte é um exemplo que pode utilizar para configurar o Explorador de armazenamento do Azure para a pilha do Azure:

1. Inicie sessão no portal de administrador de pilha do Azure como um operador.
2. Procurar **contas do Storage** e procure **frphealthaccount**. O **frphealthaccount** conta é a conta de armazenamento geral utilizada para armazenar todos os eventos de sistema operativo.

   ![Contas de armazenamento](media/azure-stack-integrate-security/storage-accounts.png)

3. Selecione **frphealthaccount**, em seguida, clique em **chaves de acesso**.

   ![Chaves de acesso](media/azure-stack-integrate-security/access-keys.png)

4. Copie a chave de acesso à sua área de transferência.
5. Abra o Explorador de armazenamento do Azure.
6. No **editar** menu, selecione **pilha do Azure de destino**.
7. Selecione **adicionar conta**e, em seguida, selecione **utilizar um nome de conta de armazenamento e a chave**.

   ![Ligar o armazenamento](media/azure-stack-integrate-security/connect-storage.png)

8. Clique em **Seguinte**.
9. No **anexar armazenamento externo** página:

   a. Escreva o nome de conta **frphealthaccount**.

   b. Cole a chave de acesso da conta de armazenamento.

   c. Em **domínio de pontos finais de armazenamento**, selecione **outros**e especifique o ponto final de armazenamento **[Região]. [ DomainName]**.

   d. Selecione o **utilizar HTTP** caixa de verificação.

   ![Anexar armazenamento externo](media/azure-stack-integrate-security/attach-storage.png)

10. Clique em **seguinte**, reveja o resumo, e **concluir** o assistente.
11. Agora pode procurar os contentores de BLOBs individuais e transferir os eventos.

   ![Procurar blobs](media/azure-stack-integrate-security/browse-blob.png)

### <a name="use-programming-languages-to-access-events"></a>Utilizar linguagens de programação para eventos de acesso

Pode utilizar várias linguagens de programação para aceder a uma conta de armazenamento. Utilize a seguinte documentação para escolher um exemplo que corresponda ao seu idioma:

[https://Azure.microsoft.com/resources/Samples/?term=Storage+Account](https://azure.microsoft.com/resources/samples/?term=storage+account)

## <a name="device-access-auditing"></a>Auditoria de acesso do dispositivo

Todos os dispositivos físicos na pilha do Azure suportam a utilização de TACACS ou RADIUS. Isto inclui o acesso ao controlador de gestão de placa base (BMC) e comutadores de rede.

Soluções de pilha do Azure não são enviados com RADIUS ou TACACS incorporada. No entanto, as soluções têm foi validadas para suportar a utilização de RADIUS ou TACACS soluções existentes disponíveis no mercado.

Para RADIUS, MSCHAPv2 foi validada. Isto representa a implementação mais segura com o RADIUS.
Consulte o seu fornecedor de hardware do OEM para ativar TACAS ou RADIUS nos dispositivos incluídos na sua solução de pilha do Azure.

## <a name="syslog"></a>Syslog

Todos os dispositivos físicos na pilha do Azure podem enviar mensagens Syslog. Soluções de pilha do Azure não são enviados juntamente com um servidor Syslog. No entanto, as soluções têm foi validadas para suportar o envio de mensagens Syslog as soluções existentes disponíveis no mercado.

O endereço de destino do Syslog é um parâmetro opcional recolhido para a implementação, mas pode também ser adicionada implementação post.

## <a name="next-steps"></a>Passos seguintes

[Azure pilha integração do Centro de dados - publicar pontos finais](azure-stack-integrate-endpoints.md)
