---
title: "O que é o Azure RemoteApp? | Microsoft Docs"
description: "Saiba como partilhar aplicações e recursos para qualquer dispositivo através do Azure RemoteApp."
services: remoteapp
documentationcenter: 
author: lizap
manager: mbaldwin
editor: 
ms.assetid: d7a8a311-e70a-4463-ac85-c7f62c500921
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/15/2016
ms.author: elizapo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 95b4e6914de787bb2c25527ce9635f872b4746a6


---
# <a name="what-is-azure-remoteapp"></a>O que é o Azure RemoteApp?
> [!IMPORTANT]
> O Azure RemoteApp está a ser descontinuado. Leia o [anúncio](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.
> 
> 

O Azure RemoteApp traz as funcionalidades do programa Microsoft RemoteApp no local, apoiado pelos Serviços de Ambiente de Trabalho Remoto, para o Azure. O Azure RemoteApp ajuda a fornecer um acesso remoto seguro às aplicações a partir de vários dispositivos de utilizador distintos. O Azure RemoteApp aloja, basicamente, sessões do Servidor de Terminais não persistentes na nuvem que pode utilizar e partilhar com os utilizadores.

Com o Azure RemoteApp pode partilhar aplicações e recursos com utilizadores em quase todos os dispositivos. Alojamos as aplicações na nuvem, o que significa que asseguramos o hardware e o dimensionamento para satisfazer as exigências dos utilizadores. Apenas necessita de carregar as aplicações que pretende partilhar e, em seguida, incentivar os utilizadores a utilizar essas aplicações. [Os utilizadores mantêm os seus próprios dispositivos](remoteapp-clients.md), enquanto gere tudo através do Portal do Azure. Tem inclusivamente a opção de utilizar as credenciais da sua empresa, permitindo-lhe garantir a segurança das aplicações e dos dados.

Continue a ler para obter mais informações sobre o Azure RemoteApp ou, caso já esteja convencido, [experimente agora](https://azure.microsoft.com/services/remoteapp/).

Tem perguntas sobre o Azure RemoteApp? Consulte as [FAQs](remoteapp-faq.md).

O Azure RemoteApp faz parte da [Infraestrutura de Ambiente de Trabalho Virtual da Microsoft](http://www.microsoft.com/server-cloud/products/virtual-desktop-infrastructure/explore.aspx).

**Novo!** Pretende saber mais sobre o Azure RemoteApp? Ou está preparado para validar o Azure RemoteApp em dimensionamento?  Participe no nosso [seminário virtual de perguntas para especialistas](https://azureinfo.microsoft.com/AzureRemoteAppAskTheExperts-Registration-Page.html?ls=Website) semanal.

## <a name="azure-remoteapp-collections"></a>Coleções do Azure RemoteApp
Existem dois tipos de [coleções do Azure RemoteApp](remoteapp-collections.md):

* Uma **coleção na nuvem** está alojada e armazena dados de programas na nuvem. Os utilizadores podem aceder às aplicações iniciando sessão com a conta Microsoft ou as credenciais da empresa sincronizadas ou federadas com o Azure Active Directory.
  
    Escolha uma coleção na nuvem quando a aplicação que pretende partilhar não pedir uma ligação a qualquer recurso da rede privada da sua empresa (por exemplo, através de um dispositivo VPN). Se a aplicação utilizar recursos na Internet, no OneDrive ou no Azure, uma coleção na nuvem será a ideal para si. A sua criação é também a mais rápida.
* Uma **coleção híbrida** está alojada e armazena dados na nuvem do Azure, contudo, também permite aos utilizadores o acesso aos dados e aos recursos armazenados na rede local. Os utilizadores podem aceder às aplicações iniciando sessão com as credenciais da empresa sincronizadas ou federadas com o Azure Active Directory.
  
    Escolha uma coleção híbrida caso seja necessária uma ligação aos recursos na rede privada da empresa. Por exemplo, se a aplicação precisar de aceder a um dos seguintes:
  
  * Servidores de ficheiros localizados na intranet
  * Quicken
  * Bases de dados protegidas por uma firewall
    
    Esta coleção é normalmente mais útil para grandes empresas com muitos recursos nas redes privadas que não podem ser movidos para a nuvem.

As diversas coleções têm opções diferentes, incluindo redes, portanto, determine [qual a coleção](remoteapp-collections.md) mais adequada para si. 

### <a name="updating-your-collection"></a>Atualizar a coleção
Uma das principais diferenças entre a coleção híbrida e a coleção na nuvem é o modo como as atualizações de software são processadas. Com uma coleção na nuvem que utilize a imagem pré-instalada do Office 365 ProPlus ou do Office 2013, não precisa de se preocupar com as atualizações. O serviço realiza a própria manutenção e lança atualizações de modo contínuo, quer para aplicações quer para o sistema operativo.

Para coleções híbridas e para coleções na nuvem que utilizam uma imagem de modelo personalizada, o utilizador é responsável pela manutenção da imagem e das aplicações. Para imagens associadas a um domínio, pode controlar as atualizações através de ferramentas como o Windows Update, a Política de Grupo ou o System Center.

Após atualizar a imagem de modelo personalizada, carrega a nova imagem para a nuvem do Azure e, em seguida, atualiza a coleção para utilizar a nova imagem. (Pode fazê-lo a partir da página **Início Rápido** ou do Dashboard do Azure RemoteApp.)

Consulte [Atualizar a coleção](remoteapp-update.md) para obter mais informações.

## <a name="supported-remoteapp-clients"></a>Clientes suportados do RemoteApp
O Azure RemoteApp é suportado nas aplicações de cliente RemoteApp para Windows e Windows RT, bem como nas aplicações de Ambiente de Trabalho Remoto da Microsoft para Mac, iOS e Android. Os utilizadores podem utilizar essas aplicações nos dispositivos móveis ou de computação para aceder aos programas do novo Azure RemoteApp. 

Consulte [Aceder às aplicações no Azure RemoteApp](remoteapp-clients.md) para obter mais informações sobre os clientes.

## <a name="next-steps"></a>Passos seguintes
Aceda! Experimente! Estes artigos ajudam a começar a utilizar o Azure RemoteApp:

* [Que tipo de coleção precisa para o Azure RemoteApp?](remoteapp-collections.md)
* [Criar uma imagem do Azure RemoteApp](remoteapp-imageoptions.md)
* [Como criar uma coleção na nuvem do Azure RemoteApp](remoteapp-create-cloud-deployment.md)
* [Como criar uma coleção híbrida do Azure RemoteApp](remoteapp-create-hybrid-deployment.md)
* [Como funciona o licenciamento no Azure RemoteApp?](remoteapp-licensing.md)
* [As melhores práticas para utilizar o Azure RemoteApp](remoteapp-bestpractices.md)
* [Perguntas mais frequentes (FAQ) do Azure RemoteApp](remoteapp-faq.md)

### <a name="help-us-help-you"></a>Ajude-nos para o podermos ajudar
Sabia que além de classificar este artigo e deixar um comentário a seguir, pode alterar o próprio artigo? Falta algo? Algo errado? Escrevi algo que é simplesmente confuso? Desloque-se para cima e clique em **Editar no GitHub** ou **Editar** para efetuar alterações – estas serão enviadas para nós para revisão e, em seguida, após serem aceites, as suas alterações e melhorias serão apresentadas aqui.




<!--HONumber=Nov16_HO2-->


