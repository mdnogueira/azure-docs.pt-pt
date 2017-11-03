---
title: "Histórico de lançamento do conector versão | Microsoft Docs"
description: "Este tópico apresenta uma lista de todas as versões dos conectores para o Forefront Identity Manager (FIM) e o Microsoft Identity Manager (MIM)"
services: active-directory
documentationcenter: 
author: fimguy
manager: femila
editor: 
ms.assetid: 6a0c66ab-55df-4669-a0c7-1fe1a091a7f9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/06/2017
ms.author: fimguy
ms.openlocfilehash: 98eb9b3a58737da2436eed591d69a900166c6af9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="connector-version-release-history"></a>Histórico de Versões do Conector
Os conectores do Forefront Identity Manager (FIM) e o Microsoft Identity Manager (MIM) são atualizados com frequência.

> [!NOTE]
> Este tópico é apenas no FIM e de MIM. Estes conectores não são suportados para instalar no Azure AD Connect. Conectores lançados são pré-instalada em AADConnect quando atualizar para especificar a compilação.

Este tópico lista todas as versões dos conectores que tenham sido publicadas.

Ligações relacionadas:

* [Transferir conectores mais recentes](http://go.microsoft.com/fwlink/?LinkId=717495)
* [Conector de LDAP genérico](active-directory-aadconnectsync-connector-genericldap.md) documentação de referência
* [Conector do SQL Server genérico](active-directory-aadconnectsync-connector-genericsql.md) documentação de referência
* [Conector de serviços de Web](http://go.microsoft.com/fwlink/?LinkID=226245) documentação de referência
* [Conector de PowerShell](active-directory-aadconnectsync-connector-powershell.md) documentação de referência
* [Lotus Domino Connector](active-directory-aadconnectsync-connector-domino.md) documentação de referência


## <a name="116040-aadconnect-116140"></a>1.1.604.0 (AADConnect 1.1.614.0)


### <a name="fixed-issues"></a>Fixos problemas:

* Serviços Web genérico:
  * Foi corrigido um problema a impedir que um projeto SOAP a ser criada quando ocorreram dois ou mais pontos finais.
* SQL genérico:
  * Na operação de importação a GSQL foi não ao converter em hora corretamente, quando guardado no espaço de conector. O formato de data e hora predefinido para o espaço de conector do GSQL foi alterado de 'aaaa-MM-dd: ssZ' para 'aaaa-MM-dd: ssZ'.

## <a name="115510-aadconnect-115530"></a>1.1.551.0 (AADConnect 1.1.553.0)

### <a name="fixed-issues"></a>Fixos problemas:

* Serviços Web genérico:
  * A ferramenta de Wsconfig não converter corretamente a matriz Json de "exemplo de pedido" para o método do serviço REST. Esta situação ocorrer problemas com a serialização desta matriz Json para o pedido REST.
  * Ferramenta de configuração do Web Service conector não suporta a utilização de símbolos de espaço em nomes de atributo JSON 
    * Um padrão de substituição pode ser adicionado manualmente ao ficheiro WSConfigTool.exe.config, por exemplo```<appSettings> <add key=”JSONSpaceNamePattern” value="__" /> </appSettings>```

* Notas de Lotus:
  * Quando a opção **permitir certifiers personalizados para unidades de organização/Organizational** está desativada, em seguida, o conector falha durante a exportação (atualização) depois do fluxo de exportação, todos os atributos são exportados para Domino mas no momento da exportação um KeyNotFoundException é devolvido para sincronização. 
    * Isto acontece porque a operação de mudança de nome ocorre uma falha ao tentar alterar DN (atributo de nome de utilizador), alterar um dos atributos abaixo:  
      - Apelido
      - Nome próprio
      - MiddleInitial
      - AltFullName
      - AltFullNameLanguage
      - UO
      - altcommonname

  * Quando **permitir certifiers personalizados para unidades de organização/Organizational** opção estiver ativada, mas certifiers necessários estão vazios ainda, em seguida, KeyNotFoundException ocorre.

### <a name="enhancements"></a>Melhoramentos:

* SQL genérico:
  * **Cenário: reestruturado implementado:** "*" funcionalidade
  * **Descrição da solução:** alterado abordagem para [processamento de atributos de referência com múltiplos valor](active-directory-aadconnectsync-connector-genericsql.md).


### <a name="fixed-issues"></a>Fixos problemas:

* Serviços Web genérico:
  * Não é possível importar a configuração do servidor se o conector do serviço Web estiver presente
  * Conector do serviço Web não está a funcionar com vários serviços Web

* SQL genérico:
  * Não existem tipos de objeto são listados para o atributo referenciado de valor único
  * Importação delta no registo de alterações quando o valor é removido da tabela de valor múltiplos o objeto de eliminações de estratégia
  * OverflowException no conector GSQL com DB2 nas / 400

Lotus:
  * Opção adicional para enable\disable pesquisar UOs antes de abrir a página de GlobalParameters

## <a name="114430"></a>1.1.443.0

Lançadas: Março de 2017

### <a name="enhancements"></a>Melhoramentos

* SQL genérico:</br>
  **Cenário sintomas:** é uma limitação conhecida com o conector do SQL Server onde iremos só permite uma referência ao tipo de um objeto e necessitam de referência cruzada com membros. </br>
  **Descrição da solução:** no passo de processamento para referências foram "*" for escolhida a opção, todas as combinações de tipos de objeto serão devolvidas para o motor de sincronização.

>[!Important]
- Esta ação irá criar marcadores de posição muitos
- É necessário para se certificar de que a atribuição de nome é exclusiva entre tipos de objeto.


* LDAP genérico:</br>
 **Cenário:** quando estão selecionados apenas alguns contentores na partição específica, em seguida, a pesquisa ainda será efetuada na partição de toda. Específicos serão filtrados pelo serviço de sincronização, mas não por MA que poderá provocar a degradação do desempenho. </br>

 **Descrição da solução:** código do conector GLDAP alterado para torná-lo aceda possível através de todos os contentores e objetos de pesquisa em cada um deles, em vez de procurar na partição de toda.


* Lotus Domino:

  **Cenário:** suporte de eliminação Domino correio para uma remoção de pessoa durante uma exportação. </br>
  **Solução:** suporte de eliminação de correio configuráveis para uma remoção de pessoa durante uma exportação.

### <a name="fixed-issues"></a>Fixos problemas:
* Serviços Web genérico:
 * Quando alterar o URL do serviço predefinido em projetos SAP wsconfig através da ferramenta de configuração do serviço Web, em seguida, acontece o seguinte erro: não foi possível encontrar uma parte do caminho

      ``'C:\Users\cstpopovaz\AppData\Local\Temp\2\e2c9d9b0-0d8a-4409-b059-dceeb900a2b3\b9bedcc0-88ac-454c-8c69-7d6ea1c41d17\cfg.config\cloneconfig.xml'. ``

* LDAP genérico:
 * Conector GLDAP não consegue ver todos os atributos no AD LDS
 * Assistente de quebras de linha quando são detetados sem atributos UPN do esquema de diretório LDAP
 * Diferenças importações falhar com erros de deteção não presentes durante a importação completa, quando o atributo "objectclass" não está seleccionado.
 * Uma página de configuração "Configurar partições e hierarquias", não mostra os objetos que tipo de é igual para a partição para servidores Novel genérica  
MA DE LDAP. Estes mostrou apenas os objetos da partição de RootDSE.


* SQL genérico:
 * Corrigir para a marca de água SQL genérico erros de atributo com múltiplos valores não importado de importação Delta
 * Ao exportar deleted\added valores de atributo com múltiplos valores, não são deleted\added na origem de dados.  


* Notas de Lotus:
 * Um campo específico "Nome completo" é apresentado corretamente no metaverso no entanto, ao exportar a notas o valor para o atributo é Null ou está vazia.
 * Corrija o erro de Certifier duplicado
 * Quando o objeto sem quaisquer dados estiver selecionado no conector Lotus Domino com outros objetos, em seguida, iremos receber o erro de deteção ao efetuar a importação completa.
 * Quando a importação Delta está a ser executado no conector Lotus Domino, no final do que executar o Microsoft.IdentityManagement.MA.LotusDomino.Service.exe do serviço, por vezes, devolve um erro da aplicação.
 * Associação ao grupo global funciona bem e é mantida, exceto quando a exportação para tentar remover a associação a um utilizador a executar mostra como concluída com êxito com uma atualização, mas o utilizador, na verdade, não obter removido associação Lotus notas.
 * Uma oportunidade para escolher o modo de exportação que foi adicionado "acrescentar Item na parte inferior" na configuração de GUI do Lotus MA acrescentar novos itens na parte inferior durante a exportação para atributos com múltiplos valores.
 * Conector irá adicionar a lógica necessária para eliminar o ficheiro da pasta de correio e ID de cofre.
 * Elimine a associação não está a funcionar para cruzada NAB membro.
 * Valores devem ser eliminados com êxito do atributo com múltiplos valor

## <a name="111170"></a>1.1.117.0
Data da versão: Março de 2016

**Novo conector**  
Inicial de versão do [genérico conector do SQL Server](active-directory-aadconnectsync-connector-genericsql.md).

**Novas funcionalidades:**

* Conector LDAP genérico:
  * Adicionado suporte para a importação delta com Isode.
* Conector de serviços Web:
  * Atualizar o csEntryChangeResult atividade e a atividade de setImportErrorCode para permitir que os erros ao nível do objeto a ser devolvido para o motor de sincronização.
  * Atualizar os modelos SAP6 e SAP6User para utilizar a nova funcionalidade de nível de erro do objeto.
* Lotus Domino Connector:
  * Para exportar, precisa de um certifier no livro de endereços. Agora, pode utilizar a mesma palavra-passe para todos os certifiers para facilitar a gestão.

**Fixos problemas:**

* Conector LDAP genérico:
  * Para o IBM Tivoli DS, alguns atributos de referência não foram detetados corretamente.
  * Para abrir LDAP durante uma importação delta, espaços em branco no início e fim de cadeias foram truncados.
  * Novell e NetIQ, uma exportação que mover de um objeto entre UOs/contentores e ao mesmo tempo mudar o nome do objecto falhou.
* Conector de serviços Web:
  * Se o serviço web tinha vários pontos finais para o mesmo enlace, em seguida, o conector não corretamente detetar estes pontos finais.
* Lotus Domino Connector:
  * Não funcionar uma exportação do atributo fullName para uma base de dados no correio.
  * Uma exportação que tanto adicionados e removidos membro de um grupo exportados apenas os membros adicionados.
  * Se um documento de notas é inválido (o atributo isValid definido como false), em seguida, a falha do conector.

## <a name="older-releases"></a>Versões mais antigas
Antes de Março de 2016, os conectores foram lançados como tópicos de suporte.

**LDAP genérico**

* [KB3078617](https://support.microsoft.com/kb/3078617) -1.0.0597, Setembro de 2015
* [KB3044896](https://support.microsoft.com/kb/3044896) -1.0.0549, Março de 2015
* [KB3031009](https://support.microsoft.com/kb/3031009) -1.0.0534, Janeiro de 2015
* [KB3008177](https://support.microsoft.com/kb/3008177) -1.0.0419, Setembro de 2014
* [KB2936070](https://support.microsoft.com/kb/2936070) -4.3.1082, Março de 2014

**WebServices**

* [KB3008178](https://support.microsoft.com/kb/3008178) -1.0.0419, Setembro de 2014

**PowerShell**

* [KB3008179](https://support.microsoft.com/kb/3008179) -1.0.0419, Setembro de 2014

**Lotus Domino**

* [KB3096533](https://support.microsoft.com/kb/3096533) -1.0.0597, Setembro de 2015
* [KB3044895](https://support.microsoft.com/kb/3044895) -1.0.0549, Março de 2015
* [KB2977286](https://support.microsoft.com/kb/2977286) -5.3.0712, Agosto de 2014
* [KB2932635](https://support.microsoft.com/kb/2932635) -5.3.1003, Fevereiro de 2014  
* [KB2899874](https://support.microsoft.com/kb/2899874) -5.3.0721, Outubro de 2013
* [KB2875551](https://support.microsoft.com/kb/2875551) -5.3.0534, Agosto de 2013

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre o [sincronização do Azure AD Connect](active-directory-aadconnectsync-whatis.md) configuração.

Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](active-directory-aadconnect.md).
