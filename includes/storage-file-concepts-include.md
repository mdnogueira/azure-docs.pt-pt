## O que é o Armazenamento de ficheiros do Azure?

O Armazenamento de ficheiros oferece um armazenamento partilhado para aplicações com o protocolo SMB padrão 2.1 ou 3.0. As máquinas virtuais e serviços em nuvem do Microsoft Azure podem partilhar dados de ficheiros entre componentes de aplicações através de partilhas montadas e as aplicações no local podem aceder a dados de ficheiros numa partilha através da API do Armazenamento de ficheiros.

As aplicações em execução nas máquinas virtuais ou serviços em nuvem do Azure podem montar uma partilha de armazenamento de ficheiros,do mesmo modo que uma aplicação de ambiente de trabalho montaria uma partilha SMB típica. Qualquer número de máquinas virtuais ou funções do Azure pode montar e aceder em simultâneo à partilha do Armazenamento de ficheiros.

Uma vez que uma partilha do Armazenamento de ficheiros é uma partilha de ficheiros no Azure com o protocolo SMB, aplicações em execução no Azure podem aceder a dados na partilha através das APIs de E/S dos ficheiros. Os programadores podem, por conseguinte, tirar partido do respetivo código existente e competências para migrar as aplicações existentes. Os profissionais de TI podem utilizar cmdlets PowerShell para criar, montar e gerir as partilhas do Armazenamento de ficheiros como parte da administração das aplicações do Azure. Este guia mostra exemplos de ambos.

Utilizações comuns do Armazenamento de ficheiros:

- Migração de aplicações no local que dependem de partilhas de ficheiros para serem executadas em máquinas virtuais ou serviços em nuvem do Azure, sem reescritas dispendiosas
- Armazenamento das definições partilhadas das aplicações, por exemplo, em ficheiros de configuração
- Armazenamento de dados de diagnóstico, como registos, métricas e informações de falhas numa localização partilhada 
- Armazenamento de ferramentas e utilitários necessários para desenvolver ou administrar as máquinas virtuais ou osserviços em nuvem do Azure

## Conceitos do Armazenamento de ficheiros

O Armazenamento de ficheiros contém os seguintes componentes:

![files-concepts][files-concepts]

-   **Conta de Armazenamento:** todos os acessos ao Storage do Azure são efetuados através de uma conta de armazenamento. Veja [Metas de Desempenho e Escalabilidade do Storage do Azure](../articles/storage/storage-scalability-targets.md) para obter detalhes acerca da capacidade das contas de armazenamento.

-   **Partilhar:** uma partilha do Armazenamento de ficheiros é uma partilha de ficheiros SMB no Azure. 
    Todos os ficheiros e diretórios têm de ser criados numa partilha principal. Uma conta pode conter um número ilimitado de partilhas e uma partilha pode armazenar um número ilimitado de ficheiros, até à capacidade total de 5 TB da partilha de ficheiros.

-   **Diretório:** uma hierarquia opcional de diretórios. 

-   **Ficheiro:** um ficheiro na partilha. Um ficheiro pode ter um tamanho até 1 TB.

-   **Formato do URL:** os ficheiros são endereçáveis utilizando o seguinte formato de URL:   
    https://`<storage
    account>`.file.core.windows.net/`<share>`/`<directory/directories>`/`<file>`  
    
    O URL do exemplo seguinte poderá ser utilizado para endereçar um dos ficheiros no diagrama acima:  
    `http://samples.file.core.windows.net/logs/CustomLogs/Log1.txt`

Para obter detalhes sobre a nomenclatura das partilhas, dos diretórios e dos ficheiros, veja [Naming and Referencing Shares, Directories, Files, and Metadata (Nomenclatura e Referência de Partilhas, Diretórios, Ficheiros e Metadados)](http://msdn.microsoft.com/library/azure/dn167011.aspx).

[ficheiros-conceitos]: ./media/storage-file-concepts-include/files-concepts.png


<!--HONumber=sep16_HO2-->


