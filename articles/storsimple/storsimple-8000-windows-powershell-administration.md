---
title: "PowerShell para gestão de dispositivos do StorSimple | Microsoft Docs"
description: Saiba como utilizar o Windows PowerShell para StorSimple para gerir o dispositivo StorSimple.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 04/03/2017
ms.author: alkohli@microsoft.com
ms.openlocfilehash: 89e1054117f19e787da5330932021351fb016209
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-windows-powershell-for-storsimple-to-administer-your-device"></a>Utilizar o Windows PowerShell para StorSimple para administrar o seu dispositivo

## <a name="overview"></a>Descrição geral

O Windows PowerShell para StorSimple fornece uma interface de linha de comandos que pode utilizar para gerir o seu dispositivo do Microsoft Azure StorSimple. Como o nome sugere, é uma interface de linha de comandos baseada no Windows PowerShell, que é criada num espaço de execução restrita. Da perspetiva do utilizador na linha de comandos, um espaço de execução restrita aparece como uma versão restrita do Windows PowerShell. Enquanto mantém algumas das funcionalidades básicas do Windows PowerShell, esta interface tem cmdlets dedicados adicionais que são adaptados para gerir o seu dispositivo do Microsoft Azure StorSimple.

Este artigo descreve o Windows PowerShell para StorSimple funcionalidades, incluindo a forma como pode ligar a esta interface e inclui hiperligações para procedimentos passo a passo ou fluxos de trabalho que pode realizar com esta interface. Os fluxos de trabalho incluem como registar o seu dispositivo, configure a interface de rede no seu dispositivo, instale as atualizações que exigem o dispositivo para estar no modo de manutenção, altere o estado do dispositivo e resolva quaisquer problemas que podem ocorrer.

Depois de ler este artigo, será capaz de:

* Ligar ao dispositivo StorSimple com o Windows PowerShell para StorSimple.
* Administrar o dispositivo StorSimple com o Windows PowerShell para StorSimple.
* Obter ajuda do Windows PowerShell para StorSimple.

> [!NOTE]
> * O Windows PowerShell para StorSimple cmdlets permitem-lhe gerir o dispositivo StorSimple da consola de série ou remotamente através da gestão remota do Windows PowerShell. Para obter mais informações sobre cada um dos cmdlets individuais que pode ser utilizados nesta interface, aceda a [referência de cmdlets do Windows PowerShell para StorSimple](https://technet.microsoft.com/library/dn688168.aspx).
> * Os cmdlets do Azure PowerShell StorSimple são uma coleção diferente de cmdlets que permitem automatizar o nível de serviço do StorSimple e tarefas de migração na linha de comandos. Para obter mais informações sobre os cmdlets do Azure PowerShell para StorSimple, vá para o [referência de cmdlets do Azure StorSimple](https://docs.microsoft.com/powershell/servicemanagement/azure.storsimple/v3.1.0/azure.storsimple).


Pode aceder do Windows PowerShell para StorSimple utilizando um dos seguintes métodos:

* [Ligar a consola de série de dispositivo do StorSimple](#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console)
* [Ligar remotamente à StorSimple com o Windows PowerShell](#connect-remotely-to-storsimple-using-windows-powershell-for-storsimple)

## <a name="connect-to-windows-powershell-for-storsimple-via-the-device-serial-console"></a>Ligar ao Windows PowerShell para StorSimple através da consola de série do dispositivo

Pode [transfira o PuTTY](http://www.putty.org/) ou semelhante software de emulação do terminal para ligar ao Windows PowerShell para StorSimple. Terá de configurar o PuTTY especificamente para aceder ao dispositivo Microsoft Azure StorSimple. Os tópicos seguintes contêm os passos detalhados sobre como configurar o PuTTy e ligar ao dispositivo. Também são explicados com várias opções de menu na consola de série.

### <a name="putty-settings"></a>Definições do PuTTY

Certifique-se de que utiliza as seguintes definições do PuTTY para ligar a interface do Windows PowerShell a partir da consola de série.

#### <a name="to-configure-putty"></a>Para configurar o PuTTY

1. No PuTTY **reconfiguração** caixa de diálogo a **categoria** painel, selecione **teclado**.
2. Certifique-se de que estão selecionadas as seguintes opções (estas são as predefinições de iniciar uma nova sessão).
   
   | Item de teclado | Selecione |
   | --- | --- |
   | Chave de retrocesso |Controlo-? (127) |
   | Chaves inicial e final |Standard |
   | Teclas de função e de teclado |ESC [n ~ |
   | Estado inicial de chaves do cursor |Normal |
   | Estado inicial do teclado numérico |Normal |
   | Ativar funcionalidades adicionais de teclado |Controlo Alt é diferente do AltGr |
   
    ![Definições de Putty suportadas](./media/storsimple-windows-powershell-administration/IC740877.png)
3. Clique em **Aplicar**.
4. No **categoria** painel, selecione **tradução**.
5. No **conjunto de carateres remoto** caixa de lista, selecione **UTF-8**.
6. Em **processamento de carateres de desenho de linha**, selecione **pontos de código de desenho de linha de utilização Unicode**. A seguinte captura de ecrã mostra as seleções PuTTY corretas.
   
    ![Definições do Putty UTF](./media/storsimple-windows-powershell-administration/IC740878.png)
7. Clique em **Aplicar**.

Agora, pode utilizar o PuTTY para ligar a consola de série do dispositivo, efetuando os seguintes passos.

[!INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]

### <a name="about-the-serial-console"></a>Acerca da consola de série

Ao aceder a do Windows PowerShell é apresentada a interface do dispositivo StorSimple através da consola de série, uma mensagem de faixa, seguido de opções de menu.

A mensagem de faixa contém informações de dispositivo StorSimple básicas, tais como o modelo, o nome, a versão do software instalado e o estado do controlador de que está a aceder. A imagem seguinte mostra um exemplo de uma mensagem de faixa.

![Mensagem de faixa de série](./media/storsimple-windows-powershell-administration/IC741098.png)

> [!IMPORTANT]
> Pode utilizar a mensagem de faixa para identificar se o controlador de que está ligado à _Active Directory_ ou _passivo_.

A imagem seguinte mostra as várias opções de espaço de execução que estão disponíveis no menu da consola de série.

![Registar o seu dispositivo 2](./media/storsimple-windows-powershell-administration/IC740906.png)

Pode escolher entre as seguintes definições:

1. **Iniciar sessão com acesso total** esta opção permite-lhe ligar (com as credenciais corretas) para o **SSAdminConsole** espaço de execução no controlador de local. (O controlador de local é o controlador que atualmente estão a aceder através da consola de série do dispositivo StorSimple.) Esta opção também pode ser utilizada para permitir Support da Microsoft para acesso sem restrições espaço de execução (uma sessão de suporte) para resolver quaisquer problemas com dispositivos possíveis. Depois de utilizar a opção 1 para iniciar sessão, pode permitir que o engenheiro de Support da Microsoft aceder ao espaço de execução sem restrições ao executar um cmdlet específico. Para obter detalhes, consulte [iniciar uma sessão de suporte](storsimple-8000-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple).
   
2. **Inicie sessão no controlador de elemento de rede com acesso total** esta opção é o mesmo que a opção 1, exceto que pode ligar (com as credenciais corretas) para o **SSAdminConsole** espaço de execução no controlador de ponto a ponto. Porque o dispositivo StorSimple é um dispositivo de elevada disponibilidade com dois controladores de uma configuração de ativo-passivo, ponto a ponto refere-se ao controlador no dispositivo que estão a aceder através da consola de série).
   Semelhante a opção 1, esta opção também pode ser utilizada para permitir Support da Microsoft para aceder ao espaço de execução ilimitado num controlador de ponto a ponto.

3. **Estabelecer ligação com acesso limitado** esta opção é utilizada para aceder à interface do Windows PowerShell no modo limitado. Não lhe forem pedidas as credenciais de acesso. Esta opção estabelece ligação ao espaço de execução mais restrito em comparação comparado as opções de 1 e 2.  Algumas das tarefas que estão disponíveis através da opção 1 que **não é possível* ser efetuada neste espaço de execução são:
   
   * Repor as definições de fábrica
   * Alterar a palavra-passe
   * Ativar ou desativar o acesso de suporte
   * Aplicar atualizações
   * Instalar correções

    > [!NOTE]
    > Esta é a opção preferencial se tenha esquecido a palavra-passe de administrador do dispositivo e não é possível ligar através da opção 1 ou 2.

4. **Alterar idioma** esta opção permite-lhe alterar o idioma de apresentação na interface do Windows PowerShell. Os idiomas suportados são inglês, japonês, russo, francês, coreano Sul, espanhol, italiano, alemão, chinês e Português-Brasil.

## <a name="connect-remotely-to-storsimple-using-windows-powershell-for-storsimple"></a>Ligar remotamente à StorSimple com o Windows PowerShell para StorSimple

Pode utilizar a comunicação remota do Windows PowerShell para ligar ao dispositivo StorSimple. Quando se liga desta forma, não verá um menu. (Pode ver um menu apenas se utilizar a consola de série do dispositivo para ligar. Ligar remotamente leva-o diretamente para o equivalente a "opção 1 – acesso total" na consola de série.) Com comunicação remota do Windows PowerShell, ligar a um espaço de execução específico. Também pode especificar o idioma de apresentação.

O idioma de apresentação é independente do idioma que defina utilizando o **alterar idioma** opção no menu da consola de série. PowerShell remoto selecionará automaticamente a região do dispositivo que está a ligar de se não for especificado nenhum.

> [!NOTE]
> Se estiver a trabalhar com anfitriões virtuais do Microsoft Azure e aplicações de nuvem do StorSimple, pode utilizar a comunicação remota do Windows PowerShell e o anfitrião virtual para ligar ao dispositivo de nuvem. Se configurou uma localização de partilha no anfitrião onde pretende guardar as informações da sessão do Windows PowerShell, deve ter em atenção que o _todas as pessoas_ principal inclui apenas os utilizadores autenticados. Por conseguinte, se configurou a partilha para permitir o acesso por _todas as pessoas_ e ligar sem especificar as credenciais, será utilizado o principal anónimo não autenticado e verá um erro. Para corrigir este problema, na partilha de anfitrião tem de ativar a conta de convidado e, em seguida, dê o convidado acesso completo à conta para a partilha ou tem de especificar credenciais válidas juntamente com o cmdlet Windows PowerShell.


Pode utilizar HTTP ou HTTPS para ligar através de comunicação remota do Windows PowerShell. Utilize as instruções nos seguintes tutoriais:

* [Ligar remotamente utilizando HTTP](storsimple-remote-connect.md#connect-through-http)
* [Ligar remotamente através de HTTPS](storsimple-remote-connect.md#connect-through-https)

## <a name="connection-security-considerations"></a>Considerações de segurança de ligação

Quando são decidir como ligar ao Windows PowerShell para StorSimple, considere o seguinte:

* Ligar diretamente a consola de série do dispositivo é seguro, mas a ligação à consola de série através de comutadores de rede não está. Tenha cuidado do risco de segurança ao ligar à série do dispositivo através de comutadores de rede.
* Ligar através de uma sessão HTTP poderá oferecem mais segurança a que se ligam através da consola de série sobre rede. Embora não seja o método mais seguro, é aceitável em redes fidedignas.
* Ligar através de uma sessão HTTPS é a mais segura e a opção recomendada.

## <a name="administer-your-storsimple-device-using-windows-powershell-for-storsimple"></a>Administrar o dispositivo StorSimple com o Windows PowerShell para StorSimple

A tabela seguinte mostra um resumo de todas as tarefas de gestão comuns e fluxos de trabalho complexos que podem ser efetuados na interface do Windows PowerShell do dispositivo StorSimple. Para obter mais informações sobre cada fluxo de trabalho, clique na entrada adequada na tabela.

#### <a name="windows-powershell-for-storsimple-workflows"></a>Windows PowerShell para StorSimple fluxos de trabalho

| Se pretender efetuar este procedimento... | Utilize este procedimento. |
| --- | --- |
| Registar o seu dispositivo |[Configurar e registar o dispositivo com o Windows PowerShell para StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple) |
| Configurar proxy Web</br>Ver definições de proxy web |[Configurar o proxy web para o dispositivo StorSimple](storsimple-8000-configure-web-proxy.md) |
| Modificar as definições da interface de rede 0 dados no seu dispositivo |[Modificar interface rede DATA 0 para o dispositivo StorSimple](storsimple-8000-modify-data-0.md) |
| Parar um controlador </br> Reiniciar ou encerrar um controlador </br> Encerrar um dispositivo</br>Repor o dispositivo para as predefinições de fábrica |[Gerir controladores de dispositivo](storsimple-8000-manage-device-controller.md) |
| Instalar atualizações de modo de manutenção e as correções |[Atualizar o seu dispositivo](storsimple-update-device.md) |
| Introduza o modo de manutenção </br>Modo de manutenção de saída |[Modos de dispositivo do StorSimple](storsimple-8000-device-modes.md) |
| Criar um pacote de suporte</br>Desencriptar e editar um pacote de suporte |[Criar e gerir um pacote de suporte](storsimple-8000-create-manage-support-package.md) |
| Iniciar uma sessão de suporte</br> |[Iniciar uma sessão de suporte no Windows PowerShell para StorSimple](storsimple-8000-create-manage-support-package.md#create-a-support-package) |

## <a name="get-help-in-windows-powershell-for-storsimple"></a>Obter ajuda do Windows PowerShell para StorSimple

No Windows PowerShell para StorSimple, a ajuda do cmdlet está disponível. Uma versão atualizada online desta ajuda também está disponível, que pode utilizar para atualizar a ajuda no seu sistema.

Obter ajuda nesta interface é semelhante ao Windows PowerShell e a maioria dos cmdlets relacionados com a ajuda irá funcionar. Pode encontrar ajuda para o Windows PowerShell online na Biblioteca TechNet: [Scripting com o Windows PowerShell](http://go.microsoft.com/fwlink/?LinkID=108518).

Segue-se uma breve descrição dos tipos de ajuda para esta interface do Windows PowerShell, incluindo como atualizar a ajuda.

### <a name="to-get-help-for-a-cmdlet"></a>Para obter ajuda para um cmdlet

* Para obter ajuda para qualquer cmdlet ou uma função, utilize o seguinte comando:`Get-Help <cmdlet-name>`
* Para obter ajuda online para qualquer cmdlet, utilize o cmdlet anterior com a `-Online` parâmetro:`Get-Help <cmdlet-name> -Online`
* Para obter ajuda completa, pode utilizar o `–Full` parâmetro e para obter exemplos, utilize o `–Examples` parâmetro.

### <a name="to-update-help"></a>Para atualizar a ajuda

Pode facilmente atualizar a ajuda na interface do Windows PowerShell. Execute os seguintes passos para atualizar a ajuda no seu sistema.

#### <a name="to-update-cmdlet-help"></a>Ao atualizar a ajuda do cmdlet
1. Inicie o Windows PowerShell com o **executar como administrador** opção.
2. Na linha de comandos, escreva:`Update-Help`
3. Os ficheiros de ajuda atualizados serão instalados.
4. Depois de instalar os ficheiros de ajuda, escreva: `Get-Help Get-Command`. Esta ação apresenta uma lista de cmdlets para o qual ajuda está disponível.

> [!NOTE]
> Para obter uma lista de todos os cmdlets disponíveis num espaço de execução, inicie sessão para a opção de menu correspondente e execute o `Get-Command` cmdlet.


## <a name="next-steps"></a>Passos seguintes

Se ocorrer algum problema com o dispositivo StorSimple ao efetuar um dos fluxos de trabalho acima, consulte [ferramentas para resolução de problemas de implementações do StorSimple](storsimple-troubleshoot-deployment.md#tools-for-troubleshooting-storsimple-deployments).

