Em seguida, se todos os servidores no cluster estão a executar o Windows Server 2008 R2 ou Windows Server 2012, tem de verificar se a correção [KB2854082](http://support.microsoft.com/kb/2854082) está instalado em cada um dos servidores no local ou VMs do Azure que fazem parte do cluster. Qualquer servidor ou a VM está em cluster, mas não no grupo de disponibilidade, deve também ter esta correção está instalada.

A sessão de ambiente de trabalho remota para cada um de nós do cluster, transferir [KB2854082](http://support.microsoft.com/kb/2854082) para um diretório local. Em seguida, instale sequencialmente a correção em cada nó de cluster. Se o serviço de cluster está atualmente em execução no nó de cluster, o servidor é reiniciado no fim da instalação de correção.

> [!WARNING]
> A parar o serviço de cluster ou reiniciar o servidor afeta o estado de funcionamento do quórum do cluster e do grupo de disponibilidade e pode fazer com que o cluster fique offline. Para manter a elevada disponibilidade do cluster durante a instalação, certifique-se de que:
> 
> * O cluster está num Estado de funcionamento de quórum ideais. 
> * Antes de instalar a correção em qualquer nó, todos os nós de cluster estão online.
> * Antes de instalar a correção no outro nó no cluster, permitir que a instalação de correção até serem concluídas num único nó, incluindo totalmente reiniciar o servidor.
> 
> 

