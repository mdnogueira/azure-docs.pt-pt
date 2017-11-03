## <a name="microsoft-open-source-code-of-conduct"></a>Microsoft Open origem código de conduta

Este projeto adotou o [Microsoft Open Source Code of Conduct (Código de Conduta do Microsoft Open Source)](https://opensource.microsoft.com/codeofconduct/).
Para obter mais informações, consulte o [código de conduta FAQ](https://opensource.microsoft.com/codeofconduct/faq/) ou contacte [ opencode@microsoft.com ](mailto:opencode@microsoft.com) com quaisquer perguntas ou comentários adicionais.

## <a name="contribute-to-azure-technical-documentation"></a>Contribuir para a documentação técnica do Azure
Apreciamos contribuições da nossa Comunidade (utilizadores, os clientes, parceiros, funcionários MSFT fora unidades de produto do Azure de núcleos, etc.), bem como de funcionários que trabalhem em unidades de produto do Azure core. Como contribuir depende quem são:

* **Comunidade - atualizações secundárias**: se estão a contribuir atualizações secundárias fora goodness do seu heart, pode encontrar o artigo neste repositório ou visite o artigo em [https://docs.microsoft.com/azure](https://docs.microsoft.com/azure) e clique em do **Editar** ligação no artigo que vai para a origem do GitHub para o artigo. Em seguida, utilize apenas a IU do GitHub para disponibilizar as atualizações. Em alternativa, são bem-vindo ao bifurcação repositório e submeter atualizações a partir do seu bifurcação.

* **Comunidade - novos artigos**: Se tiver a parte da Comunidade do Azure e pretender criar um novo artigo, terá de trabalhar com um empregado para ajudar a colocar esse novo repositório de conteúdo através de uma combinação de trabalho a pública e privado.

* **Os funcionários**: Se tiver um escritor técnica, o Gestor de programa, ou programador da equipa do produto para um serviço do Azure e é a tarefa para contribuir para ou criar artigos técnicos, deve utilizar o repositório privado (https://github.com/ MicrosoftDocs/azure-docs-pr). Se estiver a efetuar alterações substanciais a um artigo existente, adicionar ou alterar imagens ou a contribuir com um novo artigo, terá de copiar este repositório, instalar o Git Bash, um editor de markdown e aprender alguns comandos git. Consulte [Guia do contribuinte interno](https://review.docs.microsoft.com/en-us/help/contribute/?branch=master) para obter mais informações.


## <a name="about-your-contributions-to-azure-content"></a>Sobre as suas contribuições para conteúdo do Azure
### <a name="minor-corrections"></a>Correções secundárias
Correções secundárias ou clarificações que submeter relativamente a documentação e exemplos de código neste repositório estão abrangidos pelo [docs.microsoft.com os termos de utilização](https://docs.microsoft.com/legal/termsofuse).

### <a name="larger-submissions"></a>Submissões maiores
Se submeter um pedido de solicitação com alterações novas ou significativas a documentação e exemplos de códigos, enviaremos um comentário no GitHub pedir-lhe para submeter um contrato de licença de contribuição online (CLA) se não tiver um empregado da Microsoft. Antes de podermos aceitar o seu pedido de aviso, tem de preencher o formulário online.

## <a name="tools-and-setup"></a>Ferramentas e configuração
Os contribuintes da Comunidade podem utilizar a IU do GitHub ou bifurcar repositório para contribuir. Os funcionários devem visite [Guia do contribuinte interno](https://review.docs.microsoft.com/en-us/help/contribute/?branch=master) para obter mais informações sobre como contribuir para a documentação técnica definido.

## <a name="repository-organization"></a>Organização do repositório
O conteúdo no repositório de documentos do azure segue a organização da documentação em https://docs.microsoft.com/azure. Este repositório inclui duas pastas raiz:

### <a name="articles"></a>\articles
A pasta *\articles* contém os artigos de documentação formatados como ficheiros markdown com uma extensão *.md*. Artigos são, normalmente, agrupados pelo serviço do Azure.

O *\articles* pasta contém os *\media* pasta diretório artigo suporte de dados para ficheiros de raiz, dentro da qual estão subpastas com as imagens de cada artigo.  As pastas de serviço contêm uma pasta de multimédia em separado para os artigos dentro de cada uma daquelas pastas. As pastas de imagens de artigos são denominadas de forma idêntica ao ficheiro do artigo, sem a extensão de ficheiro *.md*.

### <a name="includes"></a>\includes
Pode criar secções de conteúdos reutilizáveis para serem incluídos num ou mais artigos. 

## <a name="how-to-use-markdown-to-format-your-topic"></a>Como utilizar markdown para formatar o seu tópico
Todos os artigos neste repositório utilizam o markdown do GitHub.  Eis uma lista de recursos.

* [Noções básicas de markdown](https://help.github.com/articles/markdown-basics/)
* [Cábula de markdown imprimível](./contributor-guide/media/documents/markdown-cheatsheet.pdf?raw=true)


## <a name="labels"></a>Etiquetas
No repositório público do azure-docs, etiquetas automáticas estão atribuídas a pedidos para o ajudar-na gerir o fluxo de trabalho do pedido de solicitação e para ajudar a informá-lo de que está a suceder com o seu pedido de solicitação de extração:

* Contrato de licença de contribuição relacionados com o
  * cla não necessário: A alteração é relativamente pequenas e não necessita que inicie sessão um CLA.
  * necessário-cla: O âmbito da alteração é relativamente grande e requer que inicie sessão um CLA.
  * com sessão iniciada cla: O contribuinte assinado CLA, pelo que o pedido de solicitação pode agora avançar para revisão.
* Alteração enviada para o autor: foi notificado o autor do pedido de solicitação pendente.
* pronto para intercalação: prontos para revisão através da nossa equipa de revisão do pedido de solicitação.


