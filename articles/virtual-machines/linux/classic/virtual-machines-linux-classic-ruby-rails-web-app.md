---
title: Alojar um Ruby no Web site Rails de uma VM com Linux | Microsoft Docs
description: "Configurar e alojar um Ruby no Web site baseado em Rails no Azure através de uma máquina virtual Linux."
services: virtual-machines-linux
documentationcenter: ruby
author: rmcmurray
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: aad32685-3550-4bff-9c73-beb8d70b3291
ms.service: virtual-machines-linux
ms.workload: web
ms.tgt_pltfrm: vm-linux
ms.devlang: ruby
ms.topic: article
ms.date: 06/27/2017
ms.author: robmcm
ms.openlocfilehash: fb6ded1dcba2ac0f78fc6f1f4f7de9238cd752bd
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2017
---
# <a name="ruby-on-rails-web-application-on-an-azure-vm"></a>Aplicação Web Ruby on Rails numa VM do Azure
Este tutorial mostra como alojar um Ruby no Web site de Rails no Azure através de uma máquina virtual Linux.  

Este tutorial foi validado utilizando Ubuntu Server 14.04 LTS. Se utilizar uma distribuição diferente do Linux, poderá ter de modificar os passos para instalar Rails.

> [!IMPORTANT]
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com os recursos: [Resource Manager e clássico](../../../azure-resource-manager/resource-manager-deployment-model.md).  Este artigo cobre a utilização do modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]
>

## <a name="create-an-azure-vm"></a>Criar uma VM do Azure
Comece por criar uma VM do Azure com uma imagem de Linux.

Para criar a VM, pode utilizar o portal do Azure ou a Interface de linha de comandos (CLI do Azure).

### <a name="azure-portal"></a>Portal do Azure
1. Inicie sessão no [portal do Azure](https://portal.azure.com)
2. Clique em **novo**, em seguida, escreva "Ubuntu Server 14.04" na caixa de pesquisa. Clique na entrada devolvida pela pesquisa. Para o modelo de implementação, selecione **clássico**, em seguida, clique em "Criar".
3. No painel Noções básicas, fornecer valores para os campos obrigatórios: nome (para a VM), nome de utilizador, tipo de autenticação e as credenciais correspondentes, a subscrição do Azure, o grupo de recursos e localização.

   ![Criar uma nova imagem de Ubuntu](./media/virtual-machines-linux-classic-ruby-rails-web-app/createvm.png)

4. Depois da VM é aprovisionada, clique no nome da VM e, em **pontos finais** no **definições** categoria. Localizar o ponto final de SSH, listado na **autónomo**.

   ![Ponto final predefinido](./media/virtual-machines-linux-classic-ruby-rails-web-app/endpointsnewportal.png)

### <a name="azure-cli"></a>CLI do Azure
Siga os passos no [criar com uma Máquina Virtual a executar Linux][vm-instructions].

Depois da VM é aprovisionada, pode obter o ponto final SSH, executando o seguinte comando:

    azure vm endpoint list <vm-name>  

## <a name="install-ruby-on-rails"></a>Instalar Ruby Rails
1. Utilize o SSH para ligar à VM.
2. A partir de sessão SSH, utilize os seguintes comandos para instalar o Ruby na VM:

        sudo apt-get update -y
        sudo apt-get upgrade -y

        sudo apt-add-repository ppa:brightbox/ruby-ng
        sudo apt-get update
        sudo apt-get install ruby2.4

        > [!TIP]
        > The brightbox repository contains the current Ruby distribution.

    A instalação pode demorar alguns minutos. Quando estiver concluída, utilize o seguinte comando para verificar que Ruby está instalado:

        ruby -v

3. Utilize o seguinte comando para instalar Rails:

        sudo gem install rails --no-rdoc --no-ri -V

    Utilize os - rdoc não sinalizadores delayvalidation e – não ri para ignorar a instalar a documentação, que é mais rápida.
    Este comando provavelmente irá demorar muito tempo a executar, pelo que adicionar -V irá apresentar informações sobre o progresso da instalação.

## <a name="create-and-run-an-app"></a>Criar e executar uma aplicação
Enquanto continuam a ser registados através de SSH, execute os seguintes comandos:

    rails new myapp
    cd myapp
    rails server -b 0.0.0.0 -p 3000

O [novo](http://guides.rubyonrails.org/command_line.html#rails-new) comando cria uma nova aplicação Rails. O [servidor](http://guides.rubyonrails.org/command_line.html#rails-server) comando inicia o servidor de web de WEBrick que vem com Rails. (Para utilização em produção, seria provavelmente pretende utilizar um servidor diferente, tal como Unicorn ou Passenger.)

Deverá ver um resultado semelhante ao seguinte.

    => Booting WEBrick
    => Rails 4.2.1 application starting in development on http://0.0.0.0:3000
    => Run `rails server -h` for more startup options
    => Ctrl-C to shutdown server
    [2015-06-09 23:34:23] INFO  WEBrick 1.3.1
    [2015-06-09 23:34:23] INFO  ruby 1.9.3 (2013-11-22) [x86_64-linux]
    [2015-06-09 23:34:23] INFO  WEBrick::HTTPServer#start: pid=27766 port=3000

## <a name="add-an-endpoint"></a>Adicionar um ponto final
1. Aceda a [portal do Azure] [https://portal.azure.com] e selecione a VM.

2. Selecione **pontos finais** no **definições** ao longo da esquerda contorno da página.

3. Clique em **adicionar** na parte superior da página.

4. No **adicionar ponto final** diálogo página, introduza as seguintes informações:

   * **Nome**: HTTP
   * **Protocolo**: TCP
   * **Porta pública**: 80
   * **Porta privada**: 3000
   * **Endereço de instalador de plataforma de vírgula flutuante**: desativado
   * **Lista de controlo de acesso - ordem**: 1001 ou outro valor que define a prioridade desta regra de acesso.
   * **Lista de controlo de acesso - nome**: allowHTTP
   * **Lista de controlo de acesso - ação**: permitir
   * **Lista de controlo de acesso - sub-rede remota**: 1.0.0.0/16

     Este ponto final possui uma porta pública 80 que irá encaminhar o tráfego para a porta privada 3000, onde o servidor de Rails está à escuta. A regra de lista de controlo de acesso permite tráfego público na porta 80.

     ![novo ponto final](./media/virtual-machines-linux-classic-ruby-rails-web-app/createendpoint.png)

5. Clique em OK para guardar o ponto final.

6. Deve apresentada uma mensagem que indica **guardar ponto final de máquina virtual**. Assim que esta mensagem desaparece, o ponto final está ativo. Pode agora testar a aplicação ao navegar para o nome DNS da sua máquina virtual. O Web site deve ser semelhante ao seguinte:

    ![página de rails predefinida][default-rails-cloud]

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, fez a maior parte dos passos manualmente. Num ambiente de produção, teria de escrever a sua aplicação no computador de desenvolvimento e implementá-la à VM do Azure. Além disso, a maior parte dos ambientes de produção alojar a aplicação de Rails juntamente com outro processo do servidor, tais como o Apache ou NginX, os identificadores de pedem de encaminhamento para várias instâncias da aplicação Rails e que serve recursos estáticos. Para obter mais informações, consulte http://rubyonrails.org/deploy/.

Para mais informações sobre Ruby no Rails, visite o [Ruby no Rails guias][rails-guides].

Para utilizar os serviços do Azure da sua aplicação Ruby, consulte:

* [Armazenar dados não estruturados com blobs][blobs]
* [Pares chave/valor de arquivo com tabelas][tables]
* [Servir conteúdo de largura de banda alta com a rede de entrega de conteúdo][cdn-howto]

<!-- WA.com links -->
[blobs]:../../../storage/blobs/storage-ruby-how-to-use-blob-storage.md
[cdn-howto]:https://azure.microsoft.com/develop/ruby/app-services/
[tables]:../../../cosmos-db/table-storage-how-to-use-ruby.md
[vm-instructions]:createportal.md

<!-- External Links -->
[rails-guides]:http://guides.rubyonrails.org/
[sqlite3]:http://www.sqlite.org/

<!-- Images -->

[default-rails-cloud]:./media/virtual-machines-linux-classic-ruby-rails-web-app/basicrailscloud.png
[vmlist]:./media/virtual-machines-linux-classic-ruby-rails-web-app/vmlist.png
[endpoints]:./media/virtual-machines-linux-classic-ruby-rails-web-app/endpoints.png
[new-endpoint]:./media/virtual-machines-linux-classic-ruby-rails-web-app/newendpoint.png
[new-endpoint1]:./media/virtual-machines-linux-classic-ruby-rails-web-app/newendpoint1.png
