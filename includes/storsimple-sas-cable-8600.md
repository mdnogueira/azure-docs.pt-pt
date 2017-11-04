<!--author=alkohli last changed:02/22/16-->

#### <a name="to-attach-the-sas-cables"></a>Ligar os cabos SAS
1. Identifica o site primário e as inclusões EBOD. As dois inclusões podem ser identificadas ao observar os respetivos planos de back. Ver a imagem seguinte para obter orientações. 
   
    ![Fazer uma cópia plane de principal e inclusões EBOD](./media/storsimple-sas-cable-8600/HCSBackplaneofprimaryandEBODenclosure.png)
   
    **Criar uma vista de site primário e inclusões EBOD**
   
   | Etiqueta | Descrição |
   |:--- |:--- |
   | 1 |Inclusão principal |
   | 2 |Inclusão EBOD |
2. Localize os números de série no principal e as inclusões EBOD. O número de série sticker é affixed para o back-ear de cada bastidor. Os números de série tem de ser idênticos em ambas as caixas. [Contacte o Support Microsoft](../articles/storsimple/storsimple-contact-microsoft-support.md) imediatamente se os números de série não correspondem. Veja a ilustração seguinte para localizar os números de série.
   
    ![Vista de rear de inclusão que mostra o número de série](./media/storsimple-sas-cable-8600/HCSRearviewofenclosureindicatinglocationofserialnumbersticker.png)
   
    **Localização do número de série sticker**
   
   | Etiqueta | Descrição |
   |:--- |:--- |
   | 1 |EAR do bastidor |
3. Utilize os cabos SAS fornecidos para ligar a inclusão EBOD ao bastidor primário da seguinte forma:
   
   1. Identifica as quatro portas SAS a inclusão principal e a inclusão EBOD. As portas SAS estão identificadas como EBOD na caixa primária e correspondem a uma porta na caixa EBOD, conforme mostrado no SAS cablagem ilustração, abaixo.
   2. Utilize os cabos SAS fornecidos para ligar a porta a porta EBOD A.
   3. A porta EBOD no controlador 0 deve estar ligada à uma porta no controlador EBOD 0. A porta EBOD no controlador 1 deve estar ligada à uma porta no controlador EBOD 1. Veja a ilustração seguinte para obter orientações. 
      
      ![SAS cablagem para o seu dispositivo](./media/storsimple-sas-cable-8600/HCSSAScablingforyourdevice.png)
      
      **SAS de cablagem**
      
      | Etiqueta | Descrição |
      |:--- |:--- |
      | A |Inclusão principal |
      | B |Inclusão EBOD |
      | 1 |Controlador 0 |
      | 2 |Controlador 1 |
      | 3 |EBOD controlador 0 |
      | 4 |EBOD controlador 1 |
      | 5, 6 |Portas SAS no bastidor primário (EBOD identificado) |
      | 7, 8 |Portas SAS no bastidor EBOD (A porta) |

