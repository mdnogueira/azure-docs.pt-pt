## <a name="incremental-and-complete-deployments"></a>Implementações de incrementais e completas
Quando implementar os recursos, especifica que a implementação é uma atualização incremental ou uma atualização completa. A principal diferença entre estes dois modos é a forma como o Gestor de recursos processa os recursos existentes no grupo de recursos que não estão no modelo:

* No modo de conclusão, o Gestor de recursos **elimina** recursos que existem no grupo de recursos, mas não foram especificados no modelo. 
* No modo de incremental, o Gestor de recursos **deixa inalterados** recursos que existem no grupo de recursos, mas não foram especificados no modelo.

Para ambos os modos, o Resource Manager tenta aprovisionar todos os recursos especificados no modelo. Se o recurso já existe no grupo de recursos e as respetivas definições são iguais, a operação resulta numa nenhuma alteração. Se alterar as definições para um recurso, o recurso é aprovisionado com as novas definições. Se tentar atualizar a localização ou o tipo de um recurso existente, a implementação falhar com um erro. Em vez disso, implemente um novo recurso com a localização ou escreva o que precisa.

Por predefinição, o Gestor de recursos utiliza o modo de incremental.

Para ilustrar a diferença entre os modos de incrementais e completas, considere o seguinte cenário.

**Grupo de recursos existente** contém:

* Recurso A
* Recurso B
* Recurso C

**Modelo** define:

* Recurso A
* Recurso B
* Recurso D

Quando implementada no **incremental** modo, o grupo de recursos contém:

* Recurso A
* Recurso B
* Recurso C
* Recurso D

Quando implementada no **concluída** modo, C recurso é eliminado. O grupo de recursos contém:

* Recurso A
* Recurso B
* Recurso D
