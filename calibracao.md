Este documento descreve a lógica e a implementação do processo de calibração dos sensores infravermelhos do robô seguidor de linha.

1. Objetivo
A calibração é essencial para garantir que o robô funcione em diferentes condições de iluminação. Ela ensina ao software quais são os valores de leitura para o Preto (Arena) e para o Branco (Linha), estabelecendo um limite (threshold) confiável para o PID.

2. Hardware utilizado 
- Microcontrolador: ESP32 
- Sensores: Array QTR-8 Sensors (8 sensores).
- Atuadores: 2 Motores DC com caixa de redução.
- Driver de Motor: Ponte H controlada por PWM (50kHz).

3. Lógica de Funcionamento
O robô utiliza o método de Giro em Torno do Próprio Eixo. Esta abordagem foi escolhida por permitir que, em poucos segundos, o array de sensores cruze a linha de contraste diversas vezes. As vantagens desse método são a precisão elevada garantindo que todos os sensores (1 a 8) capturem os valores extremos de refletância, a  minimização de interferências de luz externa e sombras do chassi e também eliminando erros humanos como poderia acontecer em uma calibração manual, já o movimento que ele faz entorno do seu eixo é constante e repetível.

4. Código de Calibragem
void calibrationRoutine() {
  // Inicia o giro: Motor R para frente, Motor L para trás
  // Ajuste a velocidade (150) conforme a aderência do pneu
  motors.setSpeeds(150, -150); 

  // Executa a leitura por aproximadamente 3 segundos
  for (uint16_t i = 0; i < 150; i++) { 
    qtr.calibrate(); // Atualiza os registros internos de min/max
    delay(20);       // Pequena pausa para garantir amostragem estável
  }

  // Para os motores após completar a varredura
  motors.setSpeeds(0, 0);
  
  // Sinaliza que o robô está pronto para seguir a linha
  calibrationFlag = true; 
}

5. Como usar
Coloque o robô centralizado sobre a linha branca. Ative o modo de calibração (via chave seletora ou botão). O robô girará para ambos os lados. Certifique-se de que todos os sensores passaram pela linha branca durante o giro. Assim que o robô parar, ele estará pronto para iniciar o percurso.



