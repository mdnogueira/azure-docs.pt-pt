---
title: "Resolver problemas de dimensionamento automático com conjuntos de dimensionamento de Máquina Virtual | Microsoft Docs"
description: "Resolver problemas de dimensionamento automático com conjuntos de dimensionamento de Máquina Virtual. Compreenda os problemas típicos encontrados e como resolvê-los."
services: virtual-machine-scale-sets
documentationcenter: 
author: gbowerman
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: c7d87b72-ee24-4e52-9377-a42f337f76fa
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: windows
ms.devlang: na
ms.topic: article
ms.date: 11/16/2017
ms.author: guybo
ms.openlocfilehash: 19871cd0433c6df88c631cf6e6e8e477dc902448
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2017
---
# <a name="troubleshooting-autoscale-with-virtual-machine-scale-sets"></a>Resolução de problemas de dimensionamento automático com conjuntos de dimensionamento de Máquina Virtual
**Problema** – criar uma infraestrutura de dimensionamento automático no Azure Resource Manager utilizando conjuntos de dimensionamento de máquina virtual – por exemplo, ao implementar um modelo como este: https://github.com/Azure/azure-quickstart-templates/tree/master/201- vmss-bottle-dimensionamento do automático – tiver as regras de dimensionamento definidas e funciona great, exceto, independentemente da quantidade de carga colocar nas VMs, não de dimensionamento automático.

## <a name="troubleshooting-steps"></a>Passos de resolução de problemas
Alguns aspetos a considerar incluem:

* Quantos vCPUs a cada VM tem e está a carregar cada vCPU?
  O modelo de início rápido do Azure anterior exemplo tem um script do_work.php, que é carregado um vCPU único. Se estiver a utilizar uma VM maior do que um tamanho VM única vCPU como Standard_A1 ou D1, terá de executar este carregamento várias vezes. Verifique quantos vCPUs para as suas VMs revendo [máquinas de virtuais de tamanhos para Windows no Azure](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* Quantas VMs no conjunto de dimensionamento de máquina virtual, está, a fazer trabalho em cada um deles?
  
    Um evento de escalamento horizontal só é efetuada quando a CPU média em **todos os** as VMs num conjunto de dimensionamento excede o valor do limiar, ao longo do tempo interno definido nas regras de dimensionamento automático.
* Perdeu quaisquer eventos de dimensionamento?
  
    Verifique os registos de auditoria no portal do Azure para eventos de escala. Talvez Ocorreu uma escala cópias de segurança e escala baixo que foi perdida. Pode filtrar por "Horizontal".
  
    ![Registos de Auditoria][audit]
* Os limiares em dimensionamento horizontal e são suficientemente diferentes?
  
    Suponha que defina uma regra para aumentar horizontalmente quando CPU média é superior a 50% ao longo de cinco minutos e a escala em quando média da CPU é inferior a 50%. Esta definição filiais pode causar um problema "flapping" quando a utilização de CPU é próximo do limiar, com ações de dimensionamento constantemente aumentar e diminuir o tamanho do conjunto. Devido a esta definição, o serviço de dimensionamento automático tenta impedir "em oscilação", que pode manifestam como não dimensionamento. Por conseguinte, não se esqueça de que limiares de escalamento horizontal e escala são suficientemente diferentes para permitir algum espaço entre dimensionamento.
* Foi possível escrever o seu próprio modelo JSON?
  
    É fácil fizer, por isso, comece com um modelo como um acima, que é provado para trabalhar e, efetuar pequenas alterações incrementais. 
* Pode poderá dimensionar manualmente entrada ou saída?
  
    Tente implementar novamente que o máquina virtual conjunto de dimensionamento de recursos com uma diferente "capacidade" para alterar o número de VMs manualmente a definição. Um modelo de exemplo está aqui: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing – poderá ser necessário editar o modelo para se certificar de que tem o mesmo tamanho de máquina como utiliza o seu conjunto de dimensionamento. Se com êxito, pode alterar o número de VMs manualmente, em seguida, sabe que o problema está isolado para dimensionamento automático.
* Verifique a sua Microsoft.Compute/virtualMachineScaleSet e recursos insights o [Explorador de recursos do Azure](https://resources.azure.com/)
  
    O Explorador de recursos do Azure é uma ferramenta de resolução de problemas indispensable que mostra o estado dos seus recursos do Azure Resource Manager. Clique na sua subscrição e observe o grupo de recursos que é de resolução de problemas. Sob o fornecedor de recursos de computação, observe o conjunto de dimensionamento de máquina virtual criada e verificar a vista de instância, que mostra o estado de uma implementação. Além disso, verifique a vista de instância de VMs no conjunto de dimensionamento de máquina virtual. Em seguida, vá para o fornecedor de recursos insights e verificar que as regras de dimensionamento automático parecer à direita.
* A extensão de diagnóstico está a funcionar e emitir dados de desempenho?
  
    **Atualização:** dimensionamento automático do Azure foi melhorado para utilizar um pipeline de métricas baseada no anfitrião, que já não necessita de uma extensão de diagnóstico para ser instalada. Os parágrafos alguns seguintes já não se aplicam se criar uma aplicação de dimensionamento automático utilizando o novo pipeline. Um exemplo de modelos do Azure que foi convertido para utilizar o pipeline de anfitrião está disponível aqui: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale. 
  
    Utilizar métricas baseada no anfitrião para o dimensionamento automático é melhor pelos seguintes motivos:
  
  * Menos partes mover como nenhuma extensão de diagnóstico tem de ser instalado.
  * Modelos de mais simples. Acabou de adicionar regras de dimensionamento automático insights a um modelo de conjunto de dimensionamento existente.
  * Relatórios mais fiável e iniciar mais rápida de VMs de novo.
    
    Os motivos pelos quais só poderá continuar a utilizar uma extensão de diagnóstico é se necessita de memória diagnóstico reporting/dimensionamento. Métricas baseada no anfitrião não comunicam a memória.
    
    Com que em mente, siga apenas o resto deste artigo se estiver a utilizar extensões de diagnóstico para o dimensionamento automático.
    
    O dimensionamento automático no Gestor de recursos do Azure pode trabalhar (mas já não tem) através de uma VM extensão chamado a extensão de diagnóstico. -Emite uma conta de armazenamento que define no modelo de dados de desempenho. Estes dados são agregados, em seguida, pelo serviço de monitorização do Azure.
    
    Se o serviço de informações não é possível ler os dados das VMs, este deve para enviar-lhe uma mensagem de e-mail. Por exemplo, receberá um e-mail se as VMs estão em baixo. Não se esqueça de verificar o seu endereço de e-mail, o endereço de e-mail que especificou quando criou a conta do Azure.
    
    Pode também ver os dados por si. Observe a conta de armazenamento do Azure utilizando o Explorador de nuvem. Por exemplo, utilizando o [Visual Studio Cloud Explorer](https://visualstudiogallery.msdn.microsoft.com/aaef6e67-4d99-40bc-aacf-662237db85a2), iniciar sessão e escolha a subscrição do Azure que está a utilizar. Em seguida, observe o nome de conta de armazenamento do Diagnostics referenciado na definição da extensão de diagnóstico no seu modelo de implementação.
    
    ![Cloud Explorer][explorer]
    
   Verá um bunch das tabelas onde os dados de cada VM está a ser armazenados. Colocar Linux e a métrica de CPU, por exemplo, procure linhas, o mais recentes. O Explorador de nuvem do Visual Studio suporta uma linguagem de consulta, pelo que pode executar uma consulta. Por exemplo, pode executar uma consulta para "Timestamp gt datetime'2016-02-02T21:20:00Z'" para se certificar de que obtém os eventos mais recentes. Corresponde o fuso horário UTC. Funciona os dados que vê na existe correspondem às regras de dimensionamento configurou o? No exemplo seguinte, a CPU para a máquina 20 iniciado aumentar para 100% nos últimos cinco minutos.
    
    ![Tabelas do Storage][tables]
    
    Se os dados não existe, isso implica que é o problema com a extensão de diagnóstico em execução em VMs. Se os dados não existe, implica que existe um problema com as regras de dimensionamento, ou com o serviço de informações. Verifique [Estado Azure](https://azure.microsoft.com/status/).
    
    Assim que tiver sido seguir esses passos, se ainda está a ter problemas de dimensionamento automático, pode tentar os seguintes recursos: 
    * Continue a ler os fóruns [MSDN](https://social.msdn.microsoft.com/forums/azure/home?category=windowsazureplatform%2Cazuremarketplace%2Cwindowsazureplatformctp), ou [capacidade excedida da pilha](http://stackoverflow.com/questions/tagged/azure) 
    * Inicie uma chamada de suporte. Esteja preparado para partilhar o modelo e uma vista dos seus dados de desempenho.

[audit]: ./media/virtual-machine-scale-sets-troubleshoot/image3.png
[explorer]: ./media/virtual-machine-scale-sets-troubleshoot/image1.png
[tables]: ./media/virtual-machine-scale-sets-troubleshoot/image4.png
