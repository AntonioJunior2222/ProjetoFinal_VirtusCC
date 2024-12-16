# Projeto Sistemas Embarcados VIRTUS-CC

Este documento descreve as funções implementadas no projeto para a integração de sensores e controle de dispositivos utilizando uma MCU STM32 e os periféricos associados.

## Portas utilizadas na STM32 pelos sensores e atuadores

- **MPU6050 (I2C)**
  - SDA - GPIO PIN PB7
  - SCL - GPIO PIN PB6
- **BMP280 (I2C)**
  - SDA - GPIO PIN PB7
  - SCL - GPIO PIN PB6
- **Ultrassônico HCSR04**
  - TRIG - GPIO PIN PA6
  - ECHO - GPIO PIN PA7
- **Servo Motor (PWM)**
  - Controle - GPIO PIN PA8 (TIM1_CH1)
- **Buzzer (PWM)**
  - Controle - GPIO PIN PB0 (TIM3_CH3)
- **Botões 1 e 2**
  - GPIO PIN PA1 e PA4

## Funções Implementadas no `main.c`

### `void modules_init(void)`

**Descrição**:\
Inicializa os módulos utilizados no projeto, incluindo sensores (MPU6050, BMP280, ultrassônico), display OLED e atuadores (servo motor e buzzer).

**Por que é feito**:\
Essa função é responsável por garantir que todos os módulos estejam configurados e prontos para uso.

---

### `void lcd_write_mpu(void)`

**Descrição**:\
Exibe no display OLED os dados lidos do MPU6050, como ângulos calculados e acelerações nos eixos X, Y e Z.

**Por que é feito**:\
Permite visualizar as informações de orientação e movimento em tempo real.

---

### `void lcd_write_bmp(void)`

**Descrição**:\
Exibe no display OLED os dados do sensor BMP280, como pressão, temperatura e altitude calculada.

**Por que é feito**:\
Fornece informações ambientais em tempo real no display.

---

### `void HCSR04_Read(void)`

**Descrição**:\
Realiza a leitura do sensor ultrassônico HCSR04, calculando a distância até o objeto mais próximo.

**Por que é feito**:\
Para integrar medidas de distância no controle de atuadores, como o buzzer.

---

### `void servo_motor(uint16_t step)`

**Descrição**:\
Controla o ângulo do servo motor ajustando o sinal PWM.

**Parâmetros**:
- `step` - Valor de duty cycle do PWM, correspondente ao ângulo desejado.

**Por que é feito**:\
Movimenta o servo motor para posições específicas com base nos dados de sensores.

---

### `void buzzer(uint16_t duty)`

**Descrição**:\
Ajusta a intensidade do buzzer proporcionalmente à distância medida pelo HCSR04.

**Parâmetros**:
- `duty` - Intensidade do sinal PWM para o buzzer.

**Por que é feito**:\
Alerta o usuário de objetos próximos com intensidade variável.

## Funções Implementadas no `mpu6050.c`

### `uint8_t MPU6050_Init(I2C_HandleTypeDef *hi2c)`

**Descrição**:\
Inicializa o sensor MPU6050, configurando os registradores para medições de aceleração e giroscópio.

**Parâmetros**:
- `hi2c` - Ponteiro para a interface I2C utilizada.

**Retorno**:
- `0` - Inicialização bem-sucedida.
- `1` - Falha na inicialização.

**Por que é feito**:\
Garante que o sensor esteja operacional para leituras subsequentes.

---

### `void MPU6050_Read_Accel(I2C_HandleTypeDef *hi2c, MPU6050_t *DataStruct)`

**Descrição**:\
Lê os valores brutos de aceleração e os converte para unidades de gravidade.

**Parâmetros**:
- `hi2c` - Ponteiro para a interface I2C utilizada.
- `DataStruct` - Estrutura para armazenar os dados lidos.

**Por que é feito**:\
Obtém informações sobre a aceleração nos três eixos.

---

### `void MPU6050_Read_Gyro(I2C_HandleTypeDef *hi2c, MPU6050_t *DataStruct)`

**Descrição**:\
Lê os valores brutos do giroscópio e os converte para graus por segundo.

**Parâmetros**:
- `hi2c` - Ponteiro para a interface I2C utilizada.
- `DataStruct` - Estrutura para armazenar os dados lidos.

**Por que é feito**:\
Mede a velocidade angular do dispositivo.

---

### `void MPU6050_Read_All(I2C_HandleTypeDef *hi2c, MPU6050_t *DataStruct)`

**Descrição**:\
Realiza leituras completas do sensor, incluindo aceleração, giroscópio e temperatura. Também calcula ângulos de rotação com o filtro de Kalman.

**Parâmetros**:
- `hi2c` - Ponteiro para a interface I2C utilizada.
- `DataStruct` - Estrutura para armazenar os dados lidos.

**Por que é feito**:\
Fornece um conjunto completo de informações de movimento e orientação em uma única operação.

---

### `double Kalman_getAngle(Kalman_t *Kalman, double newAngle, double newRate, double dt)`

**Descrição**:\
Calcula um ângulo suavizado com base em leituras de sensores e um filtro de Kalman.

**Parâmetros**:
- `Kalman` - Estrutura contendo os parâmetros do filtro de Kalman.
- `newAngle` - Novo ângulo medido.
- `newRate` - Nova taxa de variação angular.
- `dt` - Intervalo de tempo desde a última medição.

**Retorno**:
- Ângulo suavizado calculado.

**Por que é feito**:\
Reduz ruídos nas medições de ângulos, garantindo maior precisão nos cálculos de orientação.

## Conclusão

Este projeto integra sensores diversos e controla dispositivos atuadores para aplicações embarcadas. O `main.c` gerencia o fluxo principal, enquanto o `mpu6050.c` implementa a comunicação com o sensor de movimento. Essa estrutura modular permite fácil expansão e reutilização em outras aplicações.

## Autores

- [Antonio Roberto](https://github.com/antoniojunior2222)
- [Aryelson Gonçalves](https://github.com/aryelson1)

