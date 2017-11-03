---
title: Instalar o Python e o SDK - Azure
description: Saiba como instalar o Python e o SDK para utilizar com o Azure.
services: 
documentationcenter: python
author: lmazuel
manager: wpickett
editor: 
ms.assetid: f36294be-daeb-4caf-9129-fce18130f552
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 09/06/2016
ms.author: lmazuel
ms.openlocfilehash: e69fff29be5b12c3c0004b4101eba69c7da87d3d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="installing-python-and-the-sdk"></a>Instalar o Python e o SDK
Python é fácil de configurar no Windows e é previamente instalado no Mac, Linux, e [Bash para Windows](https://msdn.microsoft.com/commandline/wsl/about). Este guia explica como instalação e preparar o seu computador para utilização com o Azure.

## <a name="whats-in-the-python-azure-sdk"></a>Novidades no Python SDK do Azure?
O Azure SDK para Python inclui os componentes que lhe permitem desenvolver, implementar e gerir aplicações de Python para o Azure. Especificamente, o Azure SDK para Python inclui o seguinte:

* **Bibliotecas de gestão**. Estas bibliotecas de classes fornecem uma interface de gestão de recursos do Azure, tais como contas de armazenamento, máquinas virtuais.
* **Bibliotecas de tempo de execução**. Estas bibliotecas de classes facultar uma interface para aceder às funcionalidades do Azure, por exemplo, o barramento de serviço e de armazenamento.

## <a name="which-python-and-which-version-to-use"></a>O Python e a versão a utilizar
Existem vários tipos de Python interpreters disponíveis - os exemplos incluem:

* CPython - o interpretador Python frequentemente utilizado e standard
* PyPy - implementação alternativa rápida, em conformidade para CPython
* IronPython - interpretador Python, que é executado no .net/CLR
* Jython - interpretador Python, que é executado na máquina de Virtual de Java

**CPython** v2.7 ou v3.3 + e PyPy 5.4.0 são testadas e suportada para o Azure SDK Python.

## <a name="where-to-get-python"></a>Onde obtê Python?
Existem várias formas de obter CPython:

* Diretamente a partir do [www.python.org][www.python.org]
* A partir de um distro seguras, tais como [www.continuum.io][www.continuum.io], [www.enthought.com] [ www.enthought.com] ou [www.activestate.com][www.activestate.com]
* Crie a partir da origem!

Exceto se tiver obrigatoriamente, recomendamos as primeiras duas opções.

## <a name="sdk-installation-on-windows-linux-and-macos-client-libraries-only"></a>Instalação do SDK no Windows, Linux e MacOS (apenas para bibliotecas de cliente)
Se já tiver instalado de Python, pode utilizar o pip para instalar um pacote de todas as bibliotecas de cliente no seu existente Python 2.7 ou o ambiente do Python 3.3 +. Esta ação transfere os pacotes do [índice de pacote do Python] [ Python Package Index] (PyPI).

Poderá ter direitos de administrador:

* Linux e MacOS, utilize o `sudo` comando: `sudo pip install azure-mgmt-compute`.
* Windows: Abra a linha de comando do PowerShell como administrador

Pode instalar individualmente cada biblioteca para cada serviço do Azure:

```console
   $ pip install azure-batch          # Install the latest Batch runtime library
   $ pip install azure-mgmt-scheduler # Install the latest Storage management library
```

Pré-visualização pacotes podem ser instaladas utilizando o `--pre` sinalizador:

```console
   $ pip install --pre azure-mgmt-compute # installs only the latest Compute Management library
```

Também pode instalar um conjunto de bibliotecas do Azure numa única linha através de `azure` meta-package. Uma vez que nem todos os pacotes deste pacote meta são publicados como estável, ainda, a `azure` meta-package ainda está em pré-visualização.
No entanto, os pacotes núcleo, perspetivas de qualidade/alternância de código do podem ser considerados "estáveis" neste momento

* Este é oficialmente etiquetada como tal, sincronizadas com outros idiomas logo que possível.
  Iremos não planear alterações mais significativas até.

Uma vez que é uma versão de pré-visualização, tem de utilizar o `--pre` sinalizador:

```console
   $ pip install --pre azure
```

ou diretamente

```console
   $ pip install azure==2.0.0rc6
```

## <a name="getting-more-packages"></a>Obter mais pacotes
O [índice de pacote do Python] [ Python Package Index] (PyPI) tem uma seleção avançada de bibliotecas do Python.  Se optar por instalar um Distro, já terá mais interessante bits para vários cenários de desenvolvimento de web para computação técnica.

## <a name="python-tools-for-visual-studio"></a>Ferramentas do Python para Visual Studio
[Ferramentas do Python para Visual Studio][ferramentas do Python para Visual Studio] (PTVS) é um plug-in / OSS gratuita da Microsoft, que ativa a VS para um IDE do Python totalmente funcional:

![How-to-install-python-ptvs](./media/python-how-to-install/how-to-install-python-ptvs.png)

Utilizar PTVS é opcional, mas é recomendado como proporciona suporte Python e projeto/solução Web, depuração, a criação de perfis, janela interativa, edição do modelo e o Intellisense.

PTVS também facilita a implementação para o Microsoft Azure, com suporte para implementação [serviços em nuvem](cloud-services/cloud-services-python-ptvs.md) e [sites](app-service/app-service-web-overview.md).

PTVS funciona com a instalação do Visual Studio 2013, 2015 ou 2017 existente.  Para a documentação, transferências e debates, consulte [ferramentas do Python para Visual Studio].  

## <a name="python-azure-scenarios-for-linux-and-macos"></a>Python cenários do Azure para Linux e MacOS
Para Linux ou MacOS, principais cenários do Azure que são suportados:

1. Utilizar os serviços do Azure utilizando as bibliotecas de cliente para o Python
2. Executar a sua aplicação numa VM com Linux
3. Desenvolver e da publicação para Web sites do Azure utilizando o Git

O primeiro cenário permite-lhe criar aplicações web avançado que tirar partido das capacidades do Azure PaaS, tais como [armazenamento de BLOBs](virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), [fila armazenamento](storage/queues/storage-python-how-to-use-queue-storage.md), [tabela armazenamento](cosmos-db/table-storage-how-to-use-python.md) etc. através de wrappers Pythonic para as APIs REST do Azure. Estes forma idêntica funcionam no Windows, Mac e Linux.  Também pode utilizar estas bibliotecas de cliente a partir do seu computador de desenvolvimento local ou uma VM com Linux em execução no Azure.

Para o cenário VM, basta iniciar uma VM com Linux à sua escolha (Ubuntu, CentOS, Suse) e executar/gerir que gosta.  Por exemplo, pode executar [IPython] [ IPython] REPL/bloco de notas no seu Windows/Mac/Linux do computador e o browser apontar para um Linux ou VM de várias proc do Windows com o motor de IPython no Azure.

Para obter informações sobre como configurar uma VM com Linux, consulte o [criar com uma Máquina Virtual a executar Linux](virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) tutorial.

Utilizando a implementação de Git, pode desenvolver uma aplicação web do Python e publicá-lo para um Web site do Azure a partir de qualquer sistema operativo.  Quando enviar o seu repositório para o Azure, este cria automaticamente um ambiente virtual e pip instala os pacotes necessários.

Para obter mais informações sobre como utilizar qualquer estrutura WSGI compatíveis, consulte [configurar Python com Web sites do Azure](app-service/web-sites-python-configure.md).

## <a name="additional-software-and-resources"></a>Software adicional e de recursos:
* [Azure SDK para Python ReadTheDocs](http://azure-sdk-for-python.readthedocs.io/en/latest/)
* [Azure SDK para Python GitHub](https://github.com/Azure/azure-sdk-for-python)
* [Oficiais exemplos do Azure para Python](https://azure.microsoft.com/documentation/samples/?platform=python)
* [Distribuição do Python continuum análise][Continuum Analytics Python Distribution]
* [Distribuição de Enthought Python][Enthought Python Distribution]
* [Distribuição de ActiveState Python][ActiveState Python Distribution]
* [SciPy - um conjunto de bibliotecas de Python científicos][SciPy - A suite of Scientific Python libraries]
* [NumPy - uma biblioteca de números de Python][NumPy - A numerics library for Python]
* [Django projeto - um framework/CMS web madura][Django Project - A mature web framework/CMS]
* [IPython - um REPL/bloco de notas avançado para o Python][IPython - an advanced REPL/Notebook for Python]
* [Ferramentas do Python para Visual Studio no GitHub][Python Tools for Visual Studio on GitHub]
* [Centro para Programadores do Python](/develop/python/)

[Continuum Analytics Python Distribution]: http://continuum.io
[Enthought Python Distribution]: http://www.enthought.com
[ActiveState Python Distribution]: http://www.activestate.com
[www.python.org]: http://www.python.org
[www.continuum.io]: http://continuum.io
[www.enthought.com]: http://www.enthought.com
[www.activestate.com]: http://www.activestate.com
[SciPy - A suite of Scientific Python libraries]: http://www.scipy.org
[NumPy - A numerics library for Python]: http://www.numpy.org
[Django Project - A mature web framework/CMS]: http://www.djangoproject.com
[IPython - an advanced REPL/Notebook for Python]: http://ipython.org
[IPython]: http://ipython.org
[ferramentas do Python para Visual Studio]: http://aka.ms/ptvs
[Python Tools for Visual Studio on GitHub]: https://github.com/microsoft/ptvs
[Python Package Index]: http://pypi.python.org/pypi
[Microsoft Azure SDK for Python 2.7]: http://go.microsoft.com/fwlink/?LinkId=254281
[Microsoft Azure SDK for Python 3.4]: http://go.microsoft.com/fwlink/?LinkID=516990
[blob storage]:storage/blobs/storage-python-how-to-use-blob-storage.md
