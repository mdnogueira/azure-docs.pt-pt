Aqui estão as restrições de utilização e outros limites de serviço para o serviço do Azure Active Directory.

| Categoria | Limites |
| --- | --- |
| Diretórios |Só pode ser associado um único utilizador com um máximo de 20 diretórios do Azure Active Directory.<br />Exemplos de combinações possíveis: <ul> <li>Um único utilizador cria 20 diretórios.</li><li>Um único utilizador é adicionado a 20 diretórios como membro.</li><li>Um único utilizador cria 10 diretórios e mais tarde, é adicionado por outros para 10 diretórios diferentes.</li></ul> |
| Objetos |<ul><li>Um máximo de 500.000 objetos podem ser utilizados num único diretório por utilizadores da edição Gratuita do Azure Active Directory.</li><li>Um utilizador não-administrador não pode criar mais do que 250 objetos.</li></ul> |
| Extensões de esquema |<ul><li>As extensões de tipo de cadeia podem ter até 256 carateres. </li><li>As extensões do tipo binário estão limitadas a 256 bytes.</li><li>100 valores de extensão (em TODOS os tipos e TODAS as aplicações) podem ser escritos para qualquer objeto único.</li><li>Apenas as entidades "Utilizador", "Grupo", "TenantDetail", "Dispositivo", "Aplicação" e "ServicePrincipal" podem ser expandidas com os atributos de valor único do tipo "Cadeia" ou "Binário".</li><li>As extensões de esquema estão disponíveis apenas na pré-visualização da versão 1.21 da Graph API. A aplicação tem de ter acesso de escrita para registar uma extensão.</li></ul> |
| Aplicações |Um máximo de 10 utilizadores podem ser proprietários de uma única aplicação. |
| Grupos |<ul><li>Um máximo de 10 utilizadores podem ser proprietários de um único grupo.</li><li>Não existe um limite no número de objetos que podem ser membros de um único grupo no Azure Active Directory.</li><li>O número de membros de um grupo que pode sincronizar a partir do Active Directory no local para o Azure Active Directory está limitado a 15.000 membros, com a Sincronização de Diretórios do Azure Active Directory (DirSync).</li><li>O número de membros de um grupo que pode sincronizar a partir do Active Directory no local para o Azure Active Directory com o Azure AD Connect é limitado a 50.000 membros.</li></ul> |
| Painel de Acesso |<ul><li>Não existe um limite no número de aplicações que podem ser vistas no Painel de Acesso por utilizador final, para licenças atribuídas aos utilizadores para o Azure AD Premium ou o Enterprise Mobility Suite.</li><li>Pode ser visto um máximo de 10 mosaicos de aplicação (exemplos: Box, Salesforce ou Dropbox) no Painel de Acesso para cada utilizador final para licenças atribuídas a utilizadores para edições Gratuitas ou Básicas do Azure AD do Azure Active Directory. Este limite não é aplicável a contas de Administrador.</li></ul> |
| Relatórios | Pode ser visto um máximo de 1000 linhas ou transferido em qualquer relatório. Quaisquer dados adicionais são truncados. |
| Unidades administrativas | Um objeto não pode ser um membro de mais de 30 unidades administrativas. |


<!--HONumber=Feb17_HO2-->


