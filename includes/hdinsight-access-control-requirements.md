Pode utilizar uma subscrição do Azure da qual não seja administrador ou proprietário, como uma subscrição que seja propriedade de uma empresa. Neste caso, tem de verificar se o seguinte se verifica, de modo a seguir os passos deste artigo:

* Acesso de contribuinte. Para iniciar sessão no Azure, tem de ter pelo menos o acesso de Contribuinte para o grupo de recursos Azure. Este grupo de recursos é utilizado para criar um cluster do HDInsight e outros recursos do Azure.
* Registo de fornecedor. Alguém com, pelo menos, o acesso de Contribuinte para a subscrição do Azure tem de ter registado previamente o fornecedor para o recurso que está a utilizar. O registo do fornecedor acontece quando um utilizador com o acesso Contribuidor para a subscrição cria um recurso na mesma pela primeira vez. Também pode ser feito sem a criação de um recurso, ao [registar o fornecedor através de REST](https://msdn.microsoft.com/library/azure/dn790548.aspx).

Para obter mais informações sobre como trabalhar com a gestão de acesso, veja os seguintes artigos:

* [Get started with access management in the Azure portal (Introdução à gestão do acesso no portal do Azure)](../articles/active-directory/role-based-access-control-what-is.md)
* [Utilize atribuições de funções para gerir o acesso aos recursos de subscrição do Azure](../articles/active-directory/role-based-access-control-configure.md)
