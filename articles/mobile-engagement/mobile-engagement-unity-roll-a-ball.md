---
title: Unity Roll um tutorial Ball
description: "Passos para criar o clássico Unity Roll um jogo Ball que é um pré-requisito para todos os tutoriais do Unity do Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 0afd0eca-f74a-43aa-bba8-436a0265c312
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 6392d1f780b1bc2348fee5947550b05e86ea4de2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a id="unity-roll-a-ball"></a>Criar Unity Roll um jogo Ball
Este tutorial explica os principais passos para ligeiramente modificado [Unity Roll um tutorial Ball](http://unity3d.com/learn/tutorials/projects/roll-ball-tutorial). Este jogos de exemplo é composta por um objeto de spherical 'player' que é controlado pelo utilizador de aplicação e o objetivo no jogo é 'recolher' objectos coleccionáveis por colliding o objeto de leitor com estes objetos coleccionáveis. Isto pressupõe familiaridade básica com o ambiente de editor do Unity. Caso se depare com problemas devem consultar o tutorial completa. 

### <a name="setting-up-the-game"></a>Configurar no jogo
Os passos abaixo são do [Unity tutorial](https://unity3d.com/learn/tutorials/projects/roll-a-ball/set-up?playlist=17141)

1. Abra **Unity Editor** e clique em **novo**. 
   
    ![][51] 
2. Forneça um **nome do projeto** & **localização**, selecione **3D** e clique em **criar projeto**.
   
    ![][52]
3. Guardar cena acabou de criar como parte do novo projeto tal como acontece com o nome predefinido **MiniGame** dentro de um novo  **\_em segundo plano** pasta em **ativos** pasta:
   
    ![][53]
4. Criar um **objeto 3D -> Plane** como a reprodução de campo e mudar o nome deste objeto plane como **zero**
   
    ![][1]
5. Repor o componente de transformação para este **zero** objeto para que seja na origem. 
   
    ![][3]
6. Desmarque **grelha mostrar** de **Gizmos menu** para o **zero** objeto.
   
    ![][4]
7. Atualização do **escala** componente para o **zero** objeto seja [X = 2, Y = 1, Z = 2]. 
   
    ![][5]
8. Adicione um novo **objeto 3D -> Sphere** para o projeto e mudar o nome do objeto este sphere como **Player**. 
   
    ![][6]
9. Selecione o **Player** de objeto e clique em **repor transformar** semelhante para o objeto de Plane. 
10. Atualização **transformação -> posição -> coordenada Y** componente para o leitor como 0,5.  
    
    ![][7]
11. Criar uma nova pasta designada **materiais** no projeto onde iremos criar o material de leitor de cores. 
12. Crie um novo **Material** chamado **em segundo plano** nesta pasta. 
    
    ![][8]
13. Atualizar para atualizar a cor do material de **Albedo** propriedade do mesmo. Pode selecionar os valores RGB [0,32,64]. 
    
    ![][9]
14. Arrastar deste material para a vista de cenas para aplicar a cor para o **zero** objeto. 
    
    ![][10]
15. Por fim, atualize o **transformação -> rotação -> Y** e 60 no objeto leve Direcional para efeitos de clareza. 
    
    ![][12]

### <a name="moving-the-player"></a>Mover o leitor
Os passos abaixo são do [Unity tutorial](https://unity3d.com/learn/tutorials/projects/roll-a-ball/moving-the-player?playlist=17141)

1. Adicionar um **RigidBody** componente para o **Player** objeto. 
   
    ![][13]
2. Criar uma nova pasta designada **Scripts** no projeto. 
3. Clique em **adicionar componente -> Script novo -> c# Script**. Nome **PlayerController**e clique em **criar e adicionar**. Isto irá criar e anexar um script para o objeto de leitor.  
   
    ![][14]
4. Mover este script sob o **Scripts** pasta no projeto. 
5. Abra o script para edição no seu editor favorito script, atualize o código de script com o seguinte código e guardá-lo. 
   
        using UnityEngine;
        using System.Collections;
   
        public class PlayerController : MonoBehaviour 
        {
            public float speed;
            private Rigidbody rb;
            void Start ()
            {
                rb = GetComponent<Rigidbody>();
            }
            void FixedUpdate ()
            {
                float moveHorizontal = Input.GetAxis ("Horizontal");
                float moveVertical = Input.GetAxis ("Vertical");
                Vector3 movement = new Vector3 (moveHorizontal, 0.0f, moveVertical);
                rb.AddForce (movement * speed);
            }
        }
6. Tenha em atenção que o script acima utiliza um **velocidade** propriedade. No editor do Unity, atualize a propriedade de velocidade para 10.  
   
    ![][15]
7. Atingiu o **reproduzir** no Editor de Unity. Agora, deve conseguir controlar a ball com o teclado e deve rodar e mover-se. 

### <a name="moving-the-camera"></a>Mover a câmara
Os passos abaixo são do [Unity tutorial](https://unity3d.com/learn/tutorials/projects/roll-a-ball/moving-the-camera?playlist=17141) e irá associar o **Main câmara** para o **Player** objeto. 

1. Atualização do **Transform.Position** ser X = 0, Y = 10.5, a-Z = 10.  
2. Atualização do **Transform.Rotation** ser X = 45, Y = 0, Z = 0.  
   
    ![][16]
3. Adicionar um script novo chamado **CameraController** para o **MainCamera** e movê-la sob a pasta de Scripts. 
   
    ![][17]
4. Abrir o script para edição e adicione o seguinte código na mesma:
   
        using UnityEngine;
        using System.Collections;
   
        public class CameraController : MonoBehaviour {
   
            public GameObject player;
   
            private Vector3 offset;
   
            void Start ()
            {
                offset = transform.position - player.transform.position;
            }
   
            void LateUpdate ()
            {
                transform.position = player.transform.position + offset;
            }
        }
5. No ambiente do Unity - arraste a variável de leitor para a ranhura de leitor para o objeto principal câmara para que as duas associados a um do outro. 
   
    ![][18]
6. Agora se clicar em reproduzir no editor do Unity e rodar o objeto de leitor Ball, em seguida, verá a câmara a seguir ao mesmo no movimento.  

### <a name="setting-up-the-play-area"></a>Configurar a área de Play
Os passos abaixo são do [Unity tutorial](https://unity3d.com/learn/tutorials/projects/roll-a-ball/setting-up-the-play-area?playlist=17141). Iremos criar paredes envolvente zero para que o objeto de leitor Ball não remover a área de play no respetivo movimento. 

1. Clique em **criar -> Criar vazio -> jogo objeto** e dê-lhe nome **paredes**
   
    ![][19]
2. Sob este objeto de planos laterais - criar um novo **objeto 3D -> cubo** e dê-lhe o nome "Lateral Oeste". 
   
    ![][20]
3. Atualização do **transformação -> posição** e **transformação -> escala** para este objeto oeste lateral. 
   
    ![][21]
4. Duplicar o padrão de fundo do Oeste para criar um **lateral Leste** com a atualização transformar posição e o dimensionamento. 
   
    ![][22]
5. Duplicar o padrão de fundo do Leste para criar um **lateral Norte** com a atualização transformar posição & escala. 
   
    ![][23]
6. Duplicar o padrão de fundo do Norte e criar um **lateral Sul** com a atualização transformar posição & escala. 
   
    ![][24]

### <a name="creating-collectible-objects"></a>A criação de objetos coleccionáveis
Os passos abaixo são do [Unity tutorial](https://unity3d.com/learn/tutorials/projects/roll-a-ball/creating-collectables?playlist=17141). Vamos criar alguns objetos looking apelativo que irão formar o conjunto de objectos coleccionáveis que o objeto de leitor Ball precisa para recolher pelo colliding com os mesmos. 

1. Crie um novo **3D objeto do cubo** e dê-lhe o nome recolha. 
2. Ajustar o **transformação -> rotação** & **transformação -> escala** do objeto recolha. 
   
    ![][25]
3. Criar e anexar um **novo Script do c#** chamado **Rotator** ao objeto de recolha. Certifique-se colocar o script sob a pasta de Scripts. 
   
    ![][26]
4. Abra este script para edição e atualizá-la para o seguinte: 
   
        using UnityEngine;
        using System.Collections;
   
        public class Rotator : MonoBehaviour {
   
            void Update () 
            {
                transform.Rotate (new Vector3 (15, 30, 45) * Time.deltaTime);
            }
        }
5. Agora acessos rodar o modo de Play no Editor de Unity e mostrar a recolha do objeto no respetivo eixo.
6. Criar uma nova pasta designada **Prefabs** 
   
    ![][27]
7. Arraste o **recolha** objeto e colocá-la na pasta Prefabs.
   
    ![][28]
8. Crie um novo **objeto jogo vazio** chamado **Pickups**. Repor a sua posição para a origem e, em seguida, arraste o objeto recolha sob este objecto de jogo.  
   
    ![][29]
9. Duplicar o **recolha** de objeto e distribuídos-lo no **zero** à volta de objetos a **Player** objeto através da atualização o **do Transform.Position X & Z** valores adequadamente. 
   
    ![][30]
10. Criar um **material novo** chamado **recolha** e atualizá-lo de ser Red cor atualizando o **propriedade Albedo** semelhante ao que fizemos para atualizar o objeto de zero. 
    
    ![][31]
11. Aplica o material a todos os objetos de recolha 4.
    
    ![][32]

### <a name="collecting-the-pickup-objects"></a>Recolher os objetos de recolha
Os passos abaixo são do [Unity tutorial](https://unity3d.com/learn/tutorials/projects/roll-a-ball/collecting-pick-up-objects?playlist=17141). Iremos atualizar o leitor para que seja capaz de "collect" os objetos de recolha por colliding com os mesmos. 

1. Abra o **PlayerController** script ligado ao objeto de leitor para edição e a atualização para o seguinte:  
   
        using UnityEngine;
        using System.Collections;
   
        public class PlayerController : MonoBehaviour {
   
            public float speed;
   
            private Rigidbody rb;
   
            void Start ()
            {
                rb = GetComponent<Rigidbody>();
            }
   
            void FixedUpdate ()
            {
                float moveHorizontal = Input.GetAxis ("Horizontal");
                float moveVertical = Input.GetAxis ("Vertical");
   
                Vector3 movement = new Vector3 (moveHorizontal, 0.0f, moveVertical);
   
                rb.AddForce (movement * speed);
            }
   
            void OnTriggerEnter(Collider other) 
            {
                if (other.gameObject.CompareTag ("Pick Up"))
                {
                    other.gameObject.SetActive (false);
                }
            }
        }
2. Crie um novo **Tag** chamado **escolha segurança** (tem de corresponder Novidades no script)  
   
    ![][33]
   
    ![][34]
3. Aplicar isto **Tag** para o objeto de recolha Prefab. 
   
    ![][35]
4. Ativar **IsTrigger** caixa de verificação para o objeto de Prefab.
   
    ![][36]
5. Adicione um corpo Rigid ao objeto Prefab de recolha. Para a otimização de desempenho, atualizaremos a collider estático que utilizámos para um collider dinâmico. 
   
    ![][37]
6. Por fim, verifique o **IsKinematic** propriedade para o objeto prefab. 
   
    ![][38]
7. Atingiu o **reproduzir** do Unity editor e poderão reproduzir isto **implementar um Ball** jogos ao mover o objeto de leitor com as chaves de teclado para a entrada de direção. 

### <a name="updating-the-game-for-mobile-play"></a>A atualização no jogo para play móvel
As secções acima concluir o tutorial básico do Unity. Agora, vai modificar no jogo para torná-lo dispositivos móveis amigável. Tenha em atenção que utilizámos introdução por teclado no jogo até ao momento para fins de teste. Agora, irá modificá-lo para que iremos pode controlar o leitor, utilizando o movimento de telefone ou seja, utilizando Accelerometer como entrada. 

Abra o **PlayerController** criar scripts para edição e atualizar o **FixedUpdate** método a utilizar o movimento do accelerometer para mover o objeto de leitor. 

        void FixedUpdate()
        {
            //float moveHorizontal = Input.GetAxis("Horizontal");
            //float moveVertical = Input.GetAxis("Vertical");
            //Vector3 movement = new Vector3(moveHorizontal, 0.0f, moveVertical);
            rb.AddForce(Input.acceleration.x * Speed, 0, -Input.acceleration.z * Speed);
        }

Este tutorial conclui a criação de jogo básica com Unity e pode implementá-la num dispositivo à sua escolha para reproduzir no jogo. 

<!-- Images -->
[1]: ./media/mobile-engagement-unity-roll-a-ball/1.png    
[2]: ./media/mobile-engagement-unity-roll-a-ball/2.png
[3]: ./media/mobile-engagement-unity-roll-a-ball/3.png
[4]: ./media/mobile-engagement-unity-roll-a-ball/4.png
[5]: ./media/mobile-engagement-unity-roll-a-ball/5.png
[6]: ./media/mobile-engagement-unity-roll-a-ball/6.png
[7]: ./media/mobile-engagement-unity-roll-a-ball/7.png
[8]: ./media/mobile-engagement-unity-roll-a-ball/8.png
[9]: ./media/mobile-engagement-unity-roll-a-ball/9.png    
[10]: ./media/mobile-engagement-unity-roll-a-ball/10.png    
[11]: ./media/mobile-engagement-unity-roll-a-ball/11.png    
[12]: ./media/mobile-engagement-unity-roll-a-ball/12.png
[13]: ./media/mobile-engagement-unity-roll-a-ball/13.png
[14]: ./media/mobile-engagement-unity-roll-a-ball/14.png
[15]: ./media/mobile-engagement-unity-roll-a-ball/15.png
[16]: ./media/mobile-engagement-unity-roll-a-ball/16.png
[17]: ./media/mobile-engagement-unity-roll-a-ball/17.png
[18]: ./media/mobile-engagement-unity-roll-a-ball/18.png
[19]: ./media/mobile-engagement-unity-roll-a-ball/19.png    
[20]: ./media/mobile-engagement-unity-roll-a-ball/20.png    
[21]: ./media/mobile-engagement-unity-roll-a-ball/21.png    
[22]: ./media/mobile-engagement-unity-roll-a-ball/22.png    
[23]: ./media/mobile-engagement-unity-roll-a-ball/23.png    
[24]: ./media/mobile-engagement-unity-roll-a-ball/24.png    
[25]: ./media/mobile-engagement-unity-roll-a-ball/25.png    
[26]: ./media/mobile-engagement-unity-roll-a-ball/26.png    
[27]: ./media/mobile-engagement-unity-roll-a-ball/27.png    
[28]: ./media/mobile-engagement-unity-roll-a-ball/28.png    
[29]: ./media/mobile-engagement-unity-roll-a-ball/29.png    
[30]: ./media/mobile-engagement-unity-roll-a-ball/30.png    
[31]: ./media/mobile-engagement-unity-roll-a-ball/31.png    
[32]: ./media/mobile-engagement-unity-roll-a-ball/32.png    
[33]: ./media/mobile-engagement-unity-roll-a-ball/33.png    
[34]: ./media/mobile-engagement-unity-roll-a-ball/34.png    
[35]: ./media/mobile-engagement-unity-roll-a-ball/35.png    
[36]: ./media/mobile-engagement-unity-roll-a-ball/36.png    
[37]: ./media/mobile-engagement-unity-roll-a-ball/37.png    
[38]: ./media/mobile-engagement-unity-roll-a-ball/38.png    
[51]: ./media/mobile-engagement-unity-roll-a-ball/new-project.png
[52]: ./media/mobile-engagement-unity-roll-a-ball/new-project-properties.png
[53]: ./media/mobile-engagement-unity-roll-a-ball/save-scene.png













