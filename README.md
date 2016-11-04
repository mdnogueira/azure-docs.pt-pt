# Guia do Contribuidor de Documentação Técnica do Azure
Encontrou o repositório do GitHub que aloja a fonte da documentação técnica que é publicada no Centro de Documentação do Azure, em [http://azure.microsoft.com/documentation](http://azure.microsoft.com/documentation).

Este repositório também contém orientações que o ajudam a contribuir para a nossa documentação técnica.  Para obter uma lista dos artigos no guia dos contribuidores, veja [o índice](https://github.com/Azure/azure-content/blob/master/contributor-guide/contributor-guide-index.md).

## Contribuir para a documentação do Azure
Obrigado pelo seu interesse na documentação do Azure!

* [Formas de contribuir](#ways-to-contribute)
* [Código de conduta](#code-of-conduct)
* [Sobre as suas contribuições para conteúdo do Azure](#about-your-contributions-to-azure-content)
* [Organização do repositório](#repository-organization)
* [Utilizar o GitHub, o Git e este repositório](#use-github-git-and-this-repository)
* [Como utilizar markdown para formatar o seu tópico](#how-to-use-markdown-to-format-your-topic)
* [Comentários, opiniões e suporte](./contributor-guide/feedback-and-comments.md)
* [Mais recursos](#more-resources)
* [Índice de todos os artigos de guia para os contribuidores](./contributor-guide/contributor-guide-index.md) (abre uma nova página)

## Formas de contribuir
Pode contribuir para a [Documentação do Azure](http://azure.microsoft.com/documentation/) de várias formas:

* Contribuir para um [debate no fórum](http://social.msdn.microsoft.com/Forums/windowsazure/home).
* Submeter comentários do Disqus na parte inferior dos artigos.
* Pode contribuir facilmente para artigos técnicos na interface de utilizador do GitHub. Encontre o artigo neste repositório ou visite-o em [http://azure.microsoft.com/documentation](http://azure.microsoft.com/documentation) e clique na ligação no artigo que direciona para a fonte do mesmo no GitHub.
* Se estiver a efetuar alterações substanciais a um artigo existente, a adicionar ou alterar imagens ou a contribuir com um artigo novo, terá de copiar este repositório, instalar o Git Bash, o Markdown Pad e aprender alguns comandos git.

## Código de conduta
Este projeto adotou o [Microsoft Open Source Code of Conduct (Código de Conduta do Microsoft Open Source)](https://opensource.microsoft.com/codeofconduct/). Para obter mais informações, consulte [Code of Conduct FAQ (FAQ do Código de Conduta)](https://opensource.microsoft.com/codeofconduct/faq/) ou envie um e-mail para [opencode@microsoft.com](mailto:opencode@microsoft.com) com perguntas ou comentários adicionais.

## Sobre as suas contribuições para conteúdo do Azure
### Correções secundárias
As correções secundárias ou clarificações que submeter relativamente a documentação e exemplos de códigos neste repositório estão abrangidas pelos [Termos de Utilização do Web Site do Azure (ToU)](http://azure.microsoft.com/support/legal/website-terms-of-use/).

### Submissões maiores
Se submeter um pedido de aviso com alterações novas ou significativas a documentação e exemplos de códigos, enviaremos um comentário no GitHub a pedir-lhe para submeter um Contribution License Agreement (CLA, Contrato de Licença de Contribuição) online, se fizer parte de um destes grupos:

* Membros do grupo Microsoft Open Technologies.
* Contribuidores que não trabalham para a Microsoft.

Antes de podermos aceitar o seu pedido de aviso, tem de preencher o formulário online.

Estão disponíveis todos os detalhes em [http://azure.github.io/guidelines/#cla](http://azure.github.io/guidelines/#cla).

## Organização do repositório
O conteúdo do repositório azure-content segue a organização da documentação em [Azure.Microsoft.com](http://azure.microsoft.com). Este repositório inclui duas pastas raiz:

### \articles
A pasta *\articles* contém os artigos de documentação formatados como ficheiros markdown com uma extensão *.md*.

Os artigos no diretório de raiz são publicados em Azure.Microsoft.com, no caminho *http://azure.microsoft.com/documentation/articles/{nome-do-artigo-sem-md}/*.

* **Nomes de ficheiros de artigos:** consulte a [nossa orientação relativa à nomenclatura de ficheiros](./contributor-guide/file-names-and-locations.md).

Os artigos na respetiva pasta de serviço são publicados em Azure.Microsoft.com, no caminho *http://azure.microsoft.com/documentation/articles/service-folder/{nome-do-artigo-sem-md}/*

* **Subpastas de multimédia:** a pasta *\articles* contém a pasta *\media* para ficheiros de multimédia de artigos do diretório de raiz, dentro da qual estão subpastas com as imagens de cada artigo.  As pastas de serviço contêm uma pasta de multimédia em separado para os artigos dentro de cada uma daquelas pastas. As pastas de imagens de artigos são denominadas de forma idêntica ao ficheiro do artigo, sem a extensão de ficheiro *.md*.

### \includes
Pode criar secções de conteúdos reutilizáveis para serem incluídos num ou mais artigos. Consulte [Custom extensions used in our technical content (Extensões personalizadas utilizadas no nosso conteúdo técnico)](./contributor-guide/custom-markdown-extensions.md).

### \markdown templates
Esta pasta contém o nosso modelo padrão de markdown com a formatação de markdown básica necessária para os artigos.

### \contributor-guide
Esta pasta contém artigos que fazem parte do guia dos nossos contribuidores.  

## Utilizar o GitHub, o Git e este repositório
Para obter informações sobre como contribuir, como utilizar a IU do GitHub para contribuir com pequenas alterações e como copiar e clonar o repositório para criar contribuições mais significativas, consulte [Install and set up tools for authoring in GitHub (Instalar e configurar ferramentas para criação no GitHub)](./contributor-guide/tools-and-setup.md).

Se instalar o Git Bash e optar por trabalhar localmente, os passos para criar um novo ramo de trabalho local, realizar alterações e submeter as alterações novamente para o ramo principal estão indicados em [Git commands for creating a new article or updating an existing article (Comandos Git para criar um artigo novo ou atualizar um artigo existente)](./contributor-guide/git-commands-for-master.md)

### Ramos
Recomendamos que crie ramos de trabalho local direcionados para um âmbito específico de alteração. Cada ramo deve estar limitado a um único conceito/artigo, quer para otimizar o fluxo de trabalho, quer para reduzir a possibilidade de conflitos de intercalação.  Os esforços seguintes correspondem ao âmbito adequado para um ramo novo:

* Um artigo novo (e imagens associadas)
* Edições de ortografia e gramática num artigo.
* Aplicar uma alteração de formatação única num conjunto grande de artigos (por exemplo, um rodapé de direitos de autor novo).

## Como utilizar markdown para formatar o seu tópico
Todos os artigos neste repositório utilizam o markdown do GitHub.  Eis uma lista de recursos.

* [Noções básicas de markdown](https://help.github.com/articles/markdown-basics/)
* [Printable markdown cheatsheet (Referência rápida do markdown imprimível)](./contributor-guide/media/documents/markdown-cheatsheet.pdf?raw=true)
* Para a nossa lista de editores de markdown, consulte o [tópico de ferramentas e configuração](./contributor-guide/tools-and-setup.md#install-a-markdown-editor).

## Metadados dos artigos
Os metadados dos artigos ativam determinadas funcionalidades no Web site azure.microsoft.com, como a atribuição do autor e do contribuidor, a navegação estrutural, descrições de artigos e otimizações de SEO, bem como relatórios que a Microsoft utiliza para avaliar o desempenho do conteúdo. Por este motivo, os metadados são importantes! [Esta é a orientação para se certificar de que cria os seus metadados corretamente](./contributor-guide/article-metadata.md).

## Mais recursos
Consulte o [índice do guia do contribuidor](./contributor-guide/contributor-guide-index.md) para ver todos os tópicos de orientação.

<!--HONumber=Aug16_HO1-->


