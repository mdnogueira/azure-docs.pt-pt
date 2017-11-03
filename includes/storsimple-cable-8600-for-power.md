<!--author=alkohli last changed: 9/16/15-->


#### <a name="to-cable-your-device-for-power"></a>Para instalar os cabos do dispositivo de energia
> [!NOTE]
> Ambas as inclusões no dispositivo StorSimple incluem PCMs redundantes. Para cada bastidor, os PCMs tem de ser instalados e ligados a origens de energia diferentes, para garantir a elevada disponibilidade.
> 
> 

1. Certifique-se de que os comutadores de energia em todos os PCMs na posição OFF.
2. Na caixa primária, ligar os cabos de energia para ambos os PCMs. Os cabos de energia são identificados vermelho no diagrama de cablagem de energia, abaixo.
3. Certifique-se de que os dois PCMs na caixa primária utilizam fontes de alimentação separado.
4. Ligue os cabos de energia para o poder das unidades de distribuição de bastidor conforme mostrado no power cablagem diagrama.
5. Repita os passos 2 a 4 para o bastidor EBOD.
6. Ative o bastidor EBOD ao indicar o botão Ligar / desligar em cada PCM para a posição de no.
7. Certifique-se de que o bastidor EBOD está ativado por verificar que o LEDs verdes na cópia de segurança do controlador de EBOD estão ativadas.
8. Ative o bastidor primário por indicar cada comutador PCM para a posição de no.
9. Verifique se o sistema, assegurando que o controlador de dispositivo que LEDs ter ativado.
10. Certifique-se de que a ligação entre o controlador de EBOD e o controlador de dispositivo está ativa, verificando que o quatro LEDs junto a porta SAS no controlador de EBOD são verdes.
    
    > [!IMPORTANT]
    > Para garantir a elevada disponibilidade para o seu sistema, é recomendável que estritamente aderem à potência cablagem esquema mostrada no diagrama seguinte.
    > 
    > 
    
    ![Instalar os cabos do dispositivo 4U de energia](./media/storsimple-cable-8600-for-power/HCSCableYour4UDeviceforPower.png)
    
    **Cablagem de energia**
    
    | Etiqueta | Descrição |
    |:--- |:--- |
    | 1 |Inclusão principal |
    | 2 |PCM 0 |
    | 3 |PCM 1 |
    | 4 |Controlador 0 |
    | 5 |Controlador 1 |
    | 6 |Controlador EBOD 0 |
    | 7 |Controlador EBOD 1 |
    | 8 |Inclusão EBOD |
    | 9 |PDUs |

