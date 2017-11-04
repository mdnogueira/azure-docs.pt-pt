---
title: "Recolha de desempenho de aplicações do Linux na análise de registos do OMS | Microsoft Docs"
description: "Este artigo fornece detalhes de configuração do agente do OMS para Linux recolher os contadores de desempenho para o MySQL e Apache HTTP Server."
services: log-analytics
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: f1d5bde4-6b86-4b8e-b5c1-3ecbaba76198
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/04/2017
ms.author: magoedte
ms.openlocfilehash: 04ea6f728e59ec8b47e54fe45e1adc6cbbfb85ff
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="collect-performance-counters-for-linux-applications-in-log-analytics"></a>Recolher contadores de desempenho de aplicações do Linux na análise de registos 
Este artigo fornece detalhes para configurar o [agente do OMS para Linux](https://github.com/Microsoft/OMS-Agent-for-Linux) para recolher contadores de desempenho para aplicações específicas.  As aplicações incluídas neste artigo são:  

- [MySQL](#MySQL)
- [Apache HTTP Server](#apache-http-server)

## <a name="mysql"></a>MySQL
Se o servidor MySQL ou servidor MariaDB é detetado no computador quando é instalado o agente do OMS, o fornecedor do servidor de MySQL da monitorização de desempenho será instalado automaticamente. Este fornecedor estabelece ligação ao servidor local MySQL/MariaDB para expor as estatísticas de desempenho. As credenciais de utilizador MySQL tem de ser configuradas para que o fornecedor pode aceder ao servidor de MySQL.

### <a name="configure-mysql-credentials"></a>Configure as credenciais de MySQL
O fornecedor de MySQL OMI necessita de um utilizador MySQL pré-configurada e instalado MySQL bibliotecas de cliente para consultar as informações de estado de funcionamento da instância de MySQL e desempenho.  Estas credenciais são armazenadas num ficheiro de autenticação que é armazenado no agente Linux.  O ficheiro de autenticação especifica o endereço de enlace e a porta a instância de MySQL está a escutar e que credenciais a utilizar para recolher métricas.  

Durante a instalação do agente do OMS para Linux o OMI MySQL fornecedor irá analisar MySQL my.cnf de ficheiros de configuração (localizações predefinidas) para o enlace de endereço e a porta e parcialmente definir o OMI MySQL do ficheiro de autenticação.

O ficheiro de autenticação MySQL é armazenado em `/var/opt/microsoft/mysql-cimprov/auth/omsagent/mysql-auth`.


### <a name="authentication-file-format"></a>Formato de ficheiro de autenticação
Segue-se o formato do ficheiro de autenticação MySQL OMI

    [Port]=[Bind-Address], [username], [Base64 encoded Password]
    (Port)=(Bind-Address), (username), (Base64 encoded Password)
    (Port)=(Bind-Address), (username), (Base64 encoded Password)
    AutoUpdate=[true|false]

As entradas no ficheiro de autenticação são descritas na seguinte tabela.

| Propriedade | Descrição |
|:--|:--|
| Porta | Representa a porta atual, que a instância de MySQL está a escutar. A porta 0 Especifica que as propriedades seguintes são utilizadas para a instância predefinida. |
| Endereço de enlace| Endereço-atual MySQL enlace. |
| o nome de utilizador| Utilizador MySQL utilizado para utilizar para monitorizar a instância de servidor MySQL. |
| Palavra-passe de codificados Base64| Palavra-passe do utilizador de monitorização MySQL codificado em Base64. |
| AutoUpdate| Especifica se pretende voltar a analisar as alterações no ficheiro my.cnf e substituir o ficheiro de MySQL OMI autenticação quando o fornecedor de OMI MySQL é atualizado. |

### <a name="default-instance"></a>Instância predefinida
O ficheiro de autenticação MySQL OMI pode definir um número de instância e a porta predefinida para tornar a gestão de várias instâncias de MySQL num anfitrião Linux mais fácil.  A instância predefinida é a designação por uma instância com a porta 0. Todas as instâncias adicionais irão herdar as propriedades definidas na instância predefinida, a menos que especificarem valores diferentes. Por exemplo, se for adicionada a instância de MySQL escutar na porta '3308', enlace de endereço a instância predefinida, nome de utilizador e palavra-passe de codificado em Base64 serão utilizados e monitorizar a instância está a escutar 3308. Se a instância 3308 está vinculada a outro endereço e utiliza o mesmo par de nome de utilizador e palavra-passe de MySQL não é necessário apenas para o enlace-endereço e irão ser herdadas as outras propriedades.

A tabela seguinte tem definições de instância de exemplo 

| Descrição | Ficheiro |
|:--|:--|
| Instância predefinida e instância com a porta 3308. | `0=127.0.0.1, myuser, cnBwdA==`<br>`3308=, ,`<br>`AutoUpdate=true` |
| Instância predefinida e instância com a porta 3308 e outro nome de utilizador e palavra-passe. | `0=127.0.0.1, myuser, cnBwdA==`<br>`3308=127.0.1.1, myuser2,cGluaGVhZA==`<br>`AutoUpdate=true` |


### <a name="mysql-omi-authentication-file-program"></a>Programa de ficheiro de autenticação MySQL OMI
Incluído com a instalação do fornecedor MySQL OMI é um programa de ficheiro de autenticação de MySQL OMI que pode ser utilizado para editar o ficheiro de autenticação de OMI MySQL. O programa de ficheiro de autenticação pode ser encontrado na seguinte localização.

    /opt/microsoft/mysql-cimprov/bin/mycimprovauth

> [!NOTE]
> O ficheiro de credenciais tem de ser legível pela conta omsagent. Recomenda-se a execução do comando de mycimprovauth como omsgent

A tabela seguinte fornece detalhes sobre a sintaxe para utilizar mycimprovauth.

| Operação | Exemplo | Descrição
|:--|:--|:--|
| AutoUpdate * false\|VERDADEIRO * | mycimprovauth autoupdate false | Define o ficheiro de autenticação ou não será atualizado automaticamente no reiniciar ou atualizar. |
| predefinição *palavra-passe de nome de utilizador de enlace de endereço* | mycimprovauth predefinido 127.0.0.1 raiz pwd | Define a instância predefinida o OMI MySQL ficheiro authentication.<br>Deve ser introduzido o campo de palavra-passe em texto simples – a palavra-passe no ficheiro de autenticação de MySQL OMI será Base 64 codificado. |
| Eliminar * default\|port_num * | mycimprovauth 3308 | Elimina a instância especificada por uma predefinição ou por número de porta. |
| Ajuda | mycimprov ajuda | Impressões fora de uma lista de comandos a utilizar. |
| Imprimir | mycimprov impressão | Impressões terminar uma fácil de ler MySQL OMI o ficheiro de autenticação. |
| Atualizar port_num *palavra-passe de nome de utilizador de enlace de endereço* | mycimprov atualização 3307 127.0.0.1 raiz pwd | Atualiza a instância especificada ou adiciona a instância se não existir. |

Os comandos de exemplo seguintes definem uma conta de utilizador predefinido para o servidor de MySQL no localhost.  Deve ser introduzido o campo de palavra-passe em texto simples – a palavra-passe no ficheiro de autenticação de MySQL OMI será Base 64 codificado

    sudo su omsagent -c '/opt/microsoft/mysql-cimprov/bin/mycimprovauth default 127.0.0.1 <username> <password>'
    sudo /opt/omi/bin/service_control restart

### <a name="database-permissions-required-for-mysql-performance-counters"></a>Permissões de base de dados necessárias para contadores de desempenho de MySQL
O utilizador MySQL necessita de acesso para as seguintes consultas para recolher dados de desempenho do servidor de MySQL. 

    SHOW GLOBAL STATUS;
    SHOW GLOBAL VARIABLES:


O utilizador MySQL também requer acesso SELECIONE as seguintes tabelas de predefinição.

- INFORMATION_SCHEMA
- MySQL. 

Podem ser concedidos esses privilégios executando os seguintes comandos de concessão.

    GRANT SELECT ON information_schema.* TO ‘monuser’@’localhost’;
    GRANT SELECT ON mysql.* TO ‘monuser’@’localhost’;


> [!NOTE]
> Para conceder permissões a um MySQL utilizador monitorização o utilizador conceder tem de ter o privilégio 'GRANT option', bem como o privilégio ser concedido.

### <a name="define-performance-counters"></a>Definir os contadores de desempenho

Depois de configurar o agente do OMS para Linux enviar dados para análise de registos, tem de configurar os contadores de desempenho para recolher.  Utilize o procedimento no [Windows e Linux origens de dados de desempenho na análise de registos](log-analytics-data-sources-windows-events.md) com os contadores na seguinte tabela.

| Nome do objeto | Nome do contador |
|:--|:--|
| Base de Dados MySQL | Espaço em disco em Bytes |
| Base de Dados MySQL | Tabelas |
| Servidor de MySQL | Ligação abortada Pct |
| Servidor de MySQL | Pct de utilização de ligação |
| Servidor de MySQL | Utilização de espaço em disco em Bytes |
| Servidor de MySQL | A tabela completa análise Pct |
| Servidor de MySQL | Atingiu o conjunto de memória intermédia de InnoDB Pct |
| Servidor de MySQL | Pct de utilização do conjunto de memória intermédia de InnoDB |
| Servidor de MySQL | Pct de utilização do conjunto de memória intermédia de InnoDB |
| Servidor de MySQL | Pct de acertos na Cache da chave |
| Servidor de MySQL | Pct de utilização de chave de Cache |
| Servidor de MySQL | Pct de escrita de Cache da chave |
| Servidor de MySQL | Pct de acessos de Cache de consulta |
| Servidor de MySQL | Consulta Cache Prunes Pct |
| Servidor de MySQL | Pct de utilização de Cache de consulta |
| Servidor de MySQL | Pct de acertos da Cache de tabela |
| Servidor de MySQL | Pct de utilização de Cache de tabela |
| Servidor de MySQL | Pct de contenção de bloqueio de tabela |

## <a name="apache-http-server"></a>Apache HTTP Server 
Apache HTTP Server seja detetada no computador quando o pacote de omsagent é instalado, o fornecedor do servidor de HTTP do Apache da monitorização de desempenho será instalado automaticamente. Este fornecedor depende de um módulo do Apache que têm de ser carregado no Apache HTTP Server para aceder aos dados de desempenho. É possível carregar o módulo com o seguinte comando:
```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -c
```

Ao descarregar o módulo de monitorização do Apache, execute o seguinte comando:
```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -u
```

### <a name="define-performance-counters"></a>Definir os contadores de desempenho

Depois de configurar o agente do OMS para Linux enviar dados para análise de registos, tem de configurar os contadores de desempenho para recolher.  Utilize o procedimento no [Windows e Linux origens de dados de desempenho na análise de registos](log-analytics-data-sources-windows-events.md) com os contadores na seguinte tabela.

| Nome do objeto | Nome do contador |
|:--|:--|
| Apache HTTP Server | Trabalhadores ocupados |
| Apache HTTP Server | Trabalhadores Inativos |
| Apache HTTP Server | Trabalhadores ocupado PCT |
| Apache HTTP Server | Pct total da CPU |
| Anfitrião Virtual Apache | Erros por minuto - cliente |
| Anfitrião Virtual Apache | Erros por minuto - servidor |
| Anfitrião Virtual Apache | KB por pedido |
| Anfitrião Virtual Apache | Pedidos KB por segundo |
| Anfitrião Virtual Apache | Pedidos por segundo |



## <a name="next-steps"></a>Passos seguintes
* [Recolher contadores de desempenho](log-analytics-data-sources-performance-counters.md) de agentes Linux.
* Saiba mais sobre [pesquisas de registo](log-analytics-log-searches.md) para analisar os dados recolhidos a partir de origens de dados e soluções. 