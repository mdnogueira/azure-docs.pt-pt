---
title: "Trabalhar com módulos do Node.js"
description: "Saiba como trabalhar com módulos do Node.js ao utilizar o App Service do Azure ou serviços em nuvem."
services: 
documentationcenter: nodejs
author: TomArcher
manager: routlaw
editor: 
ms.assetid: c0e6cd3d-932d-433e-b72d-e513e23b4eb6
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2016
ms.author: tarcher
ms.openlocfilehash: 76679ea0ff2c1e88d1923488717a245351437165
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="using-nodejs-modules-with-azure-applications"></a>Utilizar Módulos do Node.js com aplicações do Azure
Este documento fornece orientações sobre a utilização de módulos do Node.js com aplicações alojadas no Azure. Oferece orientações para assegurar que a sua aplicação utiliza uma versão específica do módulo, bem como módulos nativos com o Azure.

Se já estiver familiarizado com a utilização de módulos do Node.js, **Package. JSON** e **npm shrinkwrap.json** ficheiros, as seguintes informações fornecem um resumo rápido do que é abordado neste artigo:

* App Service do Azure compreende **Package. JSON** e **npm shrinkwrap.json** ficheiros e pode instalar os módulos com base nas entradas nestes ficheiros.

* Serviços Cloud do Azure esperar todos os módulos para ser instalada no ambiente de desenvolvimento e o **nó\_módulos** diretório a incluir como parte do pacote de implementação. É possível ativar o suporte para a instalação de módulos com **Package. JSON** ou **npm shrinkwrap.json** ficheiros nos serviços de Cloud; no entanto, esta configuração requer a personalização dos scripts predefinida utilizada pelos projetos do serviço em nuvem. Para obter um exemplo de como configurar este ambiente, consulte [tarefas de arranque do Azure para executar a instalação de npm para evitar a implementação de módulos de nó](https://github.com/woloski/nodeonazure-blog/blob/master/articles/startup-task-to-run-npm-in-azure.markdown)

> [!NOTE]
> Máquinas virtuais do Azure não são abordadas neste artigo, como a experiência de implementação numa VM está dependente do sistema de operativo alojado pela máquina Virtual.
> 
> 

## <a name="nodejs-modules"></a>Módulos do node.js
Módulos são carregável pacotes JavaScript que fornecem funcionalidades específicas para a sua aplicação. Módulos, normalmente, são instalados utilizando o **npm** da linha de comandos da ferramenta de, no entanto alguns módulos (por exemplo, o módulo de http) são fornecidos como parte do pacote de Node.js core.

Quando os módulos são instalados, que estão armazenados no **nó\_módulos** diretório na raiz da sua estrutura do diretório de aplicação. Cada módulo dentro de **nó\_módulos** diretório mantém o seus próprios **nó\_módulos** diretório que contém os módulos que depende e este comportamento é repetido para cada módulo até a para baixo a cadeia de dependência. Este ambiente permite que cada módulo instalado para que os seus próprios requisitos de versão para os módulos depende, no entanto pode resultar numa bastante grande estrutura de diretórios.

Implementar o **nó\_módulos** diretório como parte da sua aplicação aumenta o tamanho da implementação quando comparado com a utilização de um **Package. JSON** ou **npm shrinkwrap.json** ficheiro; no entanto, garante que as versões dos módulos utilizados na produção são os mesmos que os módulos utilizados no desenvolvimento.

### <a name="native-modules"></a>Módulos nativos
Enquanto a maioria dos módulos são simplesmente-JavaScript ficheiros de texto, alguns módulos são imagens binárias específicos da plataforma. Estes módulos são compilados no momento da instalação, normalmente, utilizando o Python e gyp de nó. Uma vez que dependem do Cloud Services do Azure a **nó\_módulos** pasta que está a ser implementada como parte do módulo de aplicação, qualquer nativo incluído como parte dos módulos instalados deverão funcionar num serviço em nuvem, desde foi instalado e compilado num sistema de desenvolvimento do Windows.

App Service do Azure não suporta todos os módulos nativos e poderá falhar quando Compilar módulos com os pré-requisitos específicos. Embora alguns módulos populares, como o MongoDB têm dependências nativas opcionais e ajustar a funcionar sem-los, duas soluções foi tentativa com êxito com quase todos os módulos nativos disponíveis hoje:

* Executar **npm instalar** num computador Windows que tem os pré-requisitos do todos os o módulo nativo instalado. Em seguida, implemente o criado **nó\_módulos** pasta como parte da aplicação App Service do Azure.

  * Antes de compilação, verifique se a instalação do Node.js local tem correspondência da arquitetura e a versão é o mais parecida possível com utilizada no Azure (os valores atuais podem ser verificados no tempo de execução a partir das propriedades **process.arch** e **process.version**).

* App Service do Azure pode ser configurado para executar bash personalizado ou scripts de shell durante a implementação, dando-lhe a oportunidade para executar comandos personalizados e precisamente configurar a forma como **npm instalar** está a ser executado. Para obter um vídeo que mostra como configurar este ambiente, consulte [Scripts de implementação do Web site personalizado com o Kudu].

### <a name="using-a-packagejson-file"></a>Utilizando um ficheiro Package. JSON

O **Package. JSON** ficheiros é uma forma para especificar as dependências de nível superior requer que a aplicação para que a plataforma de alojamento pode instalar as dependências, em vez de exigir a inclusão de **nó\_ módulos** pasta como parte da implementação. Depois da aplicação foi implementada, o **npm instalar** comando é utilizado para analisar o **Package. JSON** ficheiros e de instalar todas as dependências listadas.

Durante o desenvolvimento, pode utilizar o **– guardar**, **– guardar dev**, ou **– guardar opcional** parâmetros quando instalar módulos para adicionar uma entrada para o módulo para sua **Package. JSON** automaticamente o ficheiro. Para obter mais informações, consulte [instalação de npm](https://docs.npmjs.com/cli/install).

Um potencial problema com o **Package. JSON** ficheiro é que especifica apenas a versão de dependências de nível superior. Cada módulo instalado, poderá ou não é possível especificar a versão dos módulos depende, e, por isso, é possível que podem ficar com uma cadeia de dependência diferentes que um utilizado no desenvolvimento.

> [!NOTE]
> Quando implementar no App Service do Azure, se sua <b>Package. JSON</b> ficheiro faz referência a um módulo nativo, poderá ver um erro semelhante ao exemplo a seguir quando publicar a aplicação utilizando o Git:
> 
> npm ERR! module-name@0.6.0instalar: 'nó gyp configurar compilação'
> 
> npm ERR! ' cmd "/ c" "Configurar o nó gyp compilação" ' falhou com 1
> 
> 

### <a name="using-a-npm-shrinkwrapjson-file"></a>Utilizando um ficheiro de npm shrinkwrap.json
O **npm shrinkwrap.json** ficheiros é uma tentativa para abordar as limitações de controlo de versões do módulo do **Package. JSON** ficheiro. Enquanto o **Package. JSON** ficheiro inclui apenas versões para os módulos de nível superior, o **npm shrinkwrap.json** ficheiro contém os requisitos de versão para a cadeia de dependência do módulo completa.

Quando a aplicação está pronta para produção, pode bloquear requisitos da versão e criar um **npm shrinkwrap.json** ficheiro utilizando o **npm shrinkwrap** comando. Este comando irá utilizar as versões atualmente instaladas no **nó\_módulos** pasta e registe estas versões para o **npm shrinkwrap.json** ficheiro. Depois da aplicação foi implementada para o ambiente de alojamento, o **npm instalar** comando é utilizado para analisar o **npm shrinkwrap.json** ficheiros e de instalar todas as dependências listadas. Para obter mais informações, consulte [npm shrinkwrap](https://docs.npmjs.com/cli/shrinkwrap).

> [!NOTE]
> Quando implementar no App Service do Azure, se sua <b>npm shrinkwrap.json</b> ficheiro faz referência a um módulo nativo, poderá ver um erro semelhante ao exemplo a seguir quando publicar a aplicação utilizando o Git:
> 
> npm ERR! module-name@0.6.0instalar: 'nó gyp configurar compilação'
> 
> npm ERR! ' cmd "/ c" "Configurar o nó gyp compilação" ' falhou com 1
> 
> 

## <a name="next-steps"></a>Passos seguintes
Agora que sabe como utilizar módulos do Node.js com o Azure, saiba como [especifique a versão do Node.js], [criar e implementar uma aplicação web Node.js](app-service/app-service-web-get-started-nodejs.md), e [como utilizar a Interface de linha de comandos do Azure para Mac e Linux].

Para obter mais informações, consulte o [Centro para Programadores do Node.js](/nodejs/azure/).

[especifique a versão do Node.js]: nodejs-specify-node-version-azure-apps.md
[como utilizar a Interface de linha de comandos do Azure para Mac e Linux]:cli-install-nodejs.md
[Scripts de implementação do Web site personalizado com o Kudu]: https://channel9.msdn.com/Shows/Azure-Friday/Custom-Web-Site-Deployment-Scripts-with-Kudu-with-David-Ebbo
