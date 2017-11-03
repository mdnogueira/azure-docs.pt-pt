---
ms.assetid: 
title: "Eliminação de forma recuperável Cofre de chaves do Azure | Microsoft Docs"
ms.service: key-vault
author: BrucePerlerMS
ms.author: bruceper
manager: mbaldwin
ms.date: 09/25/2017
ms.openlocfilehash: 384b65bc89401780b174c143d84b3b8f552fba3d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-key-vault-soft-delete-overview"></a>Descrição geral da eliminação de forma recuperável Cofre de chaves do Azure

A funcionalidade de eliminação de forma recuperável do Cofre de chave permite a recuperação dos cofres eliminados e objetos do cofre, conhecidos como eliminar de forma recuperável. Especificamente, vamos abordar os seguintes cenários:

- Suporte para eliminação recuperável de um cofre de chaves
- Suporte para eliminação recuperável de objetos do Cofre de chaves (ex. as chaves, segredos, certificados)

## <a name="supporting-interfaces"></a>Interfaces de suporte

A funcionalidade de eliminação de forma recuperável está inicialmente disponível através de REST, .NET / C #, PowerShell e CLI interfaces.

Para obter informações gerais, consulte as referências para estes para obter mais detalhes, [referência do Cofre de chave](https://docs.microsoft.com/azure/key-vault/).

## <a name="scenarios"></a>Cenários

Cofres de chaves do Azure são recursos controlados, geridos pelo Gestor de recursos do Azure. O Azure Resource Manager também especifica um comportamento definido especificamente para eliminação, o que necessita que uma operação de eliminação com êxito têm de resultar nesse recurso não estão a ser acessível já. A funcionalidade de eliminação de forma recuperável aborda a recuperação do objeto eliminado, se a eliminação foi acidental ou intencional.

1. O cenário típico, um utilizador pode ter inadvertidamente eliminado um cofre de chaves ou um objeto do Cofre de chaves; que a chave de cofre ou chave do objeto de cofre foram ser possível recuperar durante um período predeterminado, o utilizador pode anular a eliminação e recuperar os seus dados.

2. Outro cenário, um utilizador não autorizado pode tentar eliminar um cofre de chaves ou um objeto de Cofre de chaves, tal como uma chave no interior de um cofre, a causar uma interrupção de negócios. Separar a eliminação do Cofre de chaves ou do objeto do Cofre de chaves contra a eliminação de dados subjacentes real pode ser utilizada como uma medida de segurança ao, por exemplo, restringir permissões de eliminação de dados para outro, fidedigna função. Esta abordagem requer efetivamente quórum para uma operação que caso contrário, poderá resultar em perda de dados de imediato.

### <a name="soft-delete-behavior"></a>Comportamento de eliminação de forma recuperável

Com esta funcionalidade, a operação de eliminação de um cofre de chaves ou o objeto do Cofre de chaves é soft eliminar eficazmente que contém os recursos para um período de retenção especificado, ao transmitir o aspeto que o objeto é eliminado. O serviço mais fornece um mecanismo para recuperar o objeto eliminado, essencialmente undoing a eliminação. 

Eliminar de forma recuperável é um comportamento opcional do Cofre de chaves e é **não ativada por predefinição** nesta versão. 

### <a name="key-vault-recovery"></a>Recuperação do Cofre de chaves

Após eliminar um cofre de chaves, o serviço cria um recurso de proxy sob a subscrição, adicionar metadados suficientes para recuperação. O recurso de proxy é um objeto armazenado, disponível na mesma localização que o Cofre de chaves foi eliminado. 

### <a name="key-vault-object-recovery"></a>Recuperação de objeto do Cofre de chaves

Após eliminar um objeto de Cofre de chaves, tal como uma chave, o serviço irá colocar o objeto num Estado eliminado, deste modo, tornando-o acessível para quaisquer operações de obtenção. Enquanto estiver neste estado, o objeto do Cofre de chaves pode apenas ser apresentado, recuperado ou forçadamente/permanentemente eliminado. 

Ao mesmo tempo, o Cofre de chaves ação irá agendar a eliminação de dados subjacentes correspondente para o Cofre de chaves eliminada ou o objeto de Cofre de chaves para execução após um intervalo de retenção predeterminada. O registo DNS correspondente para o Cofre também é mantido durante o intervalo de retenção.

### <a name="soft-delete-retention-period"></a>Período de retenção de eliminação de forma recuperável

Recursos eliminados de forma recuperável são retidos durante um período de tempo, 90 dias definido. Durante o intervalo de retenção de eliminação de forma recuperável, situações seguintes:

- Pode listar todos os cofres de chaves e objetos do Cofre de chaves no estado de eliminação de forma recuperável para a sua subscrição, bem como informações de recuperação e eliminação de acesso sobre os mesmos.
    - Apenas os utilizadores com permissões especiais podem listar os cofres eliminados. Recomendamos que os nossos utilizadores criar uma função personalizada com estas permissões especiais para processamento eliminado cofres.
- Não é possível criar um cofre de chaves com o mesmo nome na mesma localização; proporcionalmente, um objeto do Cofre de chaves não é possível criar um cofre fornecido se esse cofre de chaves contém um objeto com o mesmo nome e a que se encontra no Estado eliminado 
- Apenas um utilizador especificamente com privilégios poderá restaurar um cofre de chaves ou o objeto de Cofre de chaves ao emitir um comando de recuperar no recurso de proxy correspondente.
    - O utilizador, o membro da função personalizada, que tem os privilégios para criar um cofre de chaves sob o grupo de recursos pode restaurar o cofre.
- Apenas um utilizador especificamente com privilégios a forçar a pode eliminar um cofre de chaves ou o objeto de Cofre de chaves ao emitir um comando de eliminação no recurso de proxy correspondente.

A menos que um cofre de chaves ou o objeto de Cofre de chaves é recuperado, no final do intervalo de retenção do serviço efetua uma remoção de eliminação de forma recuperável Cofre de chaves ou o objeto do Cofre de chaves e o respetivo conteúdo. Eliminação de recurso não pode ser reagendada.

### <a name="permitted-purge"></a>Remoção permitida

Permanentemente a eliminar, remover, um cofre de chaves é possível através de uma operação POST no recurso de proxy e necessita de privilégios especiais. Geralmente, apenas o proprietário da subscrição será capaz de remover um cofre de chaves. A operação POST aciona a eliminação imediata e irrecuperável de que o cofre. 

Uma exceção é o caso quando a subscrição do Azure tiver sido marcada como *undeletable*. Neste caso, apenas o serviço, em seguida, pode efetuar a eliminação real e é feito como um processo agendado. 

## <a name="next-steps"></a>Passos seguintes

Os guias seguintes dois oferecem os cenários de utilização principal para utilizar a eliminação de forma recuperável.

- [Como utilizar a eliminação de forma recuperável do Key Vault com o PowerShell](key-vault-soft-delete-powershell.md) 
- [Como utilizar a eliminação de forma recuperável do Key Vault com a CLI](key-vault-soft-delete-cli.md)

