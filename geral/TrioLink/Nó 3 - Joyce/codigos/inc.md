/*******************************************************************************************************/
/** @file         AssertTypes.h
*   @addtogroup   AssertTypes
*   @brief        Definicoes auxiliares para configuracao e tratamento de tipos de variaveis
*   @author       Luiz Neto
*   @details
*   \n <b>Ferramentas:</b> generico
*
*   Changelog
*   @version      <b>1.0.0 - 12/05/2026</b> \n Luiz Neto \n Primeira versao.
*
*   @copyright
*   @{
********************************************************************************************************/

#ifndef ASSERTTYPES_H_
#define ASSERTTYPES_H_

/********************************************************************************************************
*   INCLUDES NECESSARIOS
********************************************************************************************************/

#include <stdint.h>
#include <stdbool.h>
#include <stddef.h>

/*******************************************************************************************************/
/** @addtogroup assertTypes_types Tipos de dados
*   @brief Definicoes para o padrao de typedefs utilizados no Assert.
*   @{
********************************************************************************************************/

typedef int64_t     s64;
typedef int32_t     s32;
typedef int16_t     s16;
typedef int8_t      s8;

typedef uint64_t    u64;
typedef uint32_t    u32;
typedef uint16_t    u16;
typedef uint8_t     u8;

typedef float       f32;

#define dNULL       ((void *)0)
#define dFALSE      (0U)
#define dTRUE       (1U)

/** @} assertTypes_types */

/*******************************************************************************************************/
/** @addtogroup assertTypes_limits Valores maximos de dados
*   @brief Valores uteis para manipulacao algebrica.
*   @{
********************************************************************************************************/

#define dMAX_U8     (255)
#define dMAX_S8     127
#define dMIN_S8     (-127)

#define dMAX_U16    (65535)
#define dMAX_U32    (0xFFFFFFFF)    //4294967295u

/** @} assertTypes_limits */

/*******************************************************************************************************/
/** @addtogroup assertTypes_bitOps Operacoes de manipulacao de bits
 *  @brief Macros uteis para operacoes de bits.
 *  @{
 *******************************************************************************************************/

/// Seta o bit na posicao POS do valor VAL
#define dBIT_SET(VAL, POS)          (VAL |= (1UL << POS))

/// Limpa o bit na posicao POS do valor VAL
#define dBIT_CLEAR(VAL, POS)        (VAL &= ~(1UL << POS))

/// Inverte o bit na posicao POS do valor VAL
#define dBIT_TOGGLE(VAL, POS)       (VAL ^= (1UL << POS))

/// Escreve o valor BIT (0 ou 1) no bit da posicao POS do valor VAL
#define dBIT_WRITE(VAL, POS, BIT)   (VAL = (VAL & ~(1UL << POS)) | ((BIT & 1UL) << POS))

/// Retorna o valor (0 ou 1) do bit na posicao POS do valor VAL
#define dBIT_READ(VAL, POS)         ((VAL >> POS) & 1UL)

/** @} assertTypes_bitOps */

#endif /* ASSERTTYPES_H_ */

/** @} FILE */

---

/*
 * @file    BspButton.h
 * @brief   Leitura dos botoes de SELECAO e ENTER com debounce.
 *          Gera eventos de clique que sao consumidos pela aplicacao.
 */

#ifndef BSP_BUTTON_H
#define BSP_BUTTON_H

#include <stdbool.h>

/// Periodo recomendado de chamada do BspButton_Poll [ms]
#define dBSP_BUTTON_POLL_PERIOD_MS      10

/// Eventos gerados pelos botoes
typedef enum
{
    eBSP_BUTTON_EVENT_NONE = 0,
    eBSP_BUTTON_EVENT_SELECT,
    eBSP_BUTTON_EVENT_ENTER
} bspButtonEvent_t;

/*
 * @brief   Reinicia o estado interno dos botoes e limpa a fila de eventos.
 *          O BspGpio_Init() deve ter sido chamado antes.
 * @param   void
 * @retval  true  - Inicializacao concluida
 * @retval  false - Falha ao ler o estado inicial dos botoes
 */
bool BspButton_Init(void);

/*
 * @brief   Amostra os botoes, aplica o debounce e enfileira os cliques.
 *          Deve ser chamada periodicamente a cada dBSP_BUTTON_POLL_PERIOD_MS.
 * @param   void
 * @retval  void
 */
void BspButton_Poll(void);

/*
 * @brief   Retira o evento mais antigo da fila.
 * @param   void
 * @retval  Evento do botao, ou eBSP_BUTTON_EVENT_NONE se a fila estiver vazia
 */
bspButtonEvent_t BspButton_GetEvent(void);

#endif /* BSP_BUTTON_H */


---
/*
 * @file    BspGpio.h
 * @brief   Abstracao de GPIO (libgpiod v2) para o projeto Cascata de Sensores.
 *          Configura todos os pinos do display ILI9341 (barramento paralelo
 *          de 8 bits) e dos botoes de ENTER e SELECAO.
 */

#ifndef BSP_GPIO_H
#define BSP_GPIO_H

#include <stdbool.h>
#include <stdint.h>

/// Largura do barramento de dados do display [bits]
#define dBSP_GPIO_DATA_BUS_WIDTH    8

/// Pinos logicos do projeto (a ordem define o indice interno da tabela)
typedef enum
{
    eBSP_GPIO_LCD_D0 = 0,
    eBSP_GPIO_LCD_D1,
    eBSP_GPIO_LCD_D2,
    eBSP_GPIO_LCD_D3,
    eBSP_GPIO_LCD_D4,
    eBSP_GPIO_LCD_D5,
    eBSP_GPIO_LCD_D6,
    eBSP_GPIO_LCD_D7,
    eBSP_GPIO_LCD_RST,
    eBSP_GPIO_LCD_CS,
    eBSP_GPIO_LCD_RS,
    eBSP_GPIO_LCD_WR,
    eBSP_GPIO_LCD_RD,
    eBSP_GPIO_BTN_ENTER,
    eBSP_GPIO_BTN_SELECT,
    eBSP_GPIO_COUNT
} bspGpioPin_t;

/*
 * @brief   Abre o gpiochip e configura todos os pinos (saidas do display
 *          e entradas com pull-up dos botoes).
 * @param   void
 * @retval  true  - Inicializacao concluida
 * @retval  false - Falha ao abrir o chip ou requisitar as linhas
 */
bool BspGpio_Init(void);

/*
 * @brief   Libera as linhas requisitadas e fecha o gpiochip.
 * @param   void
 * @retval  void
 */
void BspGpio_Deinit(void);

/*
 * @brief   Escreve um nivel logico em um pino de saida.
 * @param   pin   - Pino logico (apenas pinos do display)
 * @param   level - true = nivel alto, false = nivel baixo
 * @retval  true  - Escrita realizada
 * @retval  false - Pino invalido, nao inicializado ou erro na escrita
 */
bool BspGpio_Write(bspGpioPin_t pin, bool level);

/*
 * @brief   Le o nivel logico de um pino de entrada.
 * @param   pin   - Pino logico (apenas botoes)
 * @param   level - Ponteiro que recebe o nivel lido (true = alto)
 * @retval  true  - Leitura realizada
 * @retval  false - Pino invalido, ponteiro nulo ou erro na leitura
 */
bool BspGpio_Read(bspGpioPin_t pin, bool *level);

/*
 * @brief   Escreve um byte no barramento de dados D0..D7 do display em
 *          uma unica chamada.
 * @param   value - Byte a ser colocado no barramento (D0 = bit menos significativo)
 * @retval  true  - Escrita realizada
 * @retval  false - Nao inicializado ou erro na escrita
 */
bool BspGpio_WriteDataBus(uint8_t value);

#endif /* BSP_GPIO_H */


---


/*
 * @file    BspScreen.h
 * @brief   Interface grafica (GTK4 + Cairo) e driver do display ILI9341.
 */

#ifndef BSP_SCREEN_H
#define BSP_SCREEN_H

#include <stdbool.h>
#include <stdint.h>

/*
 * @brief   Inicializa o display, abre a janela GTK e executa o loop de
 *          eventos (botoes, atualizacao dos dados e desenho) ate o usuario
 *          fechar a janela ou receber SIGINT/SIGTERM.
 * @param   argc - Quantidade de argumentos da linha de comando
 * @param   argv - Argumentos da linha de comando
 * @retval  0 - Encerrou normalmente
 * @retval  1 - Falha ao criar os recursos graficos
 * @warning BspGpio_Init, BspButton_Init e Fusion_Init devem ter sido
 *          executadas antes.
 */
int BspScreen_Run(int argc, char **argv);

#endif /* BSP_SCREEN_H */

---


/*
 * @file    Fusion.h
 * @brief   Recebe os dados MQTT dos outros nos (BMP e MPU), valida o JSON,
 *          controla a obsolescencia (staleness) e classifica os dados como
 *          normais ou anormais.
 */

#ifndef FUSION_H
#define FUSION_H

#include <stdbool.h>
#include <stdint.h>

/// Classificacao dos valores de um sensor
typedef enum
{
    eFUSION_HEALTH_NORMAL = 0,
    eFUSION_HEALTH_ABNORMAL
} fusionHealth_t;

/// Ultimos dados do sensor BMP
typedef struct
{
    bool hasData;               // true se ja chegou ao menos uma leitura valida
    bool connected;             // true se o status do no 1 for ONLINE
    bool stale;                 // true se os dados estao obsoletos
    fusionHealth_t health;      // Classificacao normal / anormal
    int64_t timestamp;          // Instante da leitura [ms Unix UTC]
    float temperature;          // [°C]
    float pressure;             // [Pa]
    float altitude;             // [m]
} fusionBmp_t;

/// Ultimos dados do sensor MPU
typedef struct
{
    bool hasData;               // true se ja chegou ao menos uma leitura valida
    bool connected;             // true se o status do no 2 for ONLINE
    bool stale;                 // true se os dados estao obsoletos
    fusionHealth_t health;      // Classificacao normal / anormal
    int64_t timestamp;          // Instante da leitura [ms Unix UTC]
    float accelX;               // [m/s²]
    float accelY;               // [m/s²]
    float accelZ;               // [m/s²]
    float gyroX;                // [rad/s]
    float gyroY;                // [rad/s]
    float gyroZ;                // [rad/s]
} fusionMpu_t;

/*
 * @brief   Zera o estado dos sensores. Nao cria conexao MQTT: a conexao e
 *          da MqttWrapper (ver Main.c), que entrega as mensagens em
 *          Fusion_OnMessage().
 * @param   void
 * @retval  void
 */
void Fusion_Init(void);

/*
 * @brief   Assina os topicos de dados e de status dos outros nos usando a
 *          MqttWrapper. Deve ser chamada a cada (re)conexao com o broker,
 *          pois a sessao e limpa (clean_session) e as assinaturas se perdem.
 * @param   void
 * @retval  true  - Todas as assinaturas foram solicitadas
 * @retval  false - Alguma assinatura falhou
 */
bool Fusion_Subscribe(void);

/*
 * @brief   Callback de mensagens (mesma assinatura da MqttWrapper): direciona
 *          o payload ao tratador do topico. Roda na thread de rede.
 * @param   topic      - Topico da mensagem
 * @param   payload    - Conteudo (nao e garantido terminar em zero)
 * @param   payloadLen - Tamanho do conteudo [bytes]
 * @retval  void
 */
void Fusion_OnMessage(const char *topic, const char *payload, uint32_t payloadLen);

/*
 * @brief   Informa que a conexao com o broker caiu: sem broker nao ha como
 *          saber o status dos outros nos, entao ambos voltam para offline.
 * @param   void
 * @retval  void
 */
void Fusion_OnBrokerDisconnected(void);

/*
 * @brief   Copia o estado atual do sensor BMP (thread-safe).
 * @param   data - Ponteiro que recebe os dados
 * @retval  true  - Copia realizada
 * @retval  false - Ponteiro nulo
 */
bool Fusion_GetBmp(fusionBmp_t *data);

/*
 * @brief   Copia o estado atual do sensor MPU (thread-safe).
 * @param   data - Ponteiro que recebe os dados
 * @retval  true  - Copia realizada
 * @retval  false - Ponteiro nulo
 */
bool Fusion_GetMpu(fusionMpu_t *data);

#endif /* FUSION_H */


---


/**************************************
* @file MqttWrapper.h
* @addtogroup MqttWrapper
* @{
**************************************/
#ifndef _MQTT_WRAPPER_H_
#define _MQTT_WRAPPER_H_

/**************************************
* INCLUDES NECESSARIOS
**************************************/
#include "Asserttypes.h"

/**************************************
* CONFIGURACOES
**************************************/
/// Intervalo maximo (segundos) sem trafego antes do broker considerar a
/// conexao morta e disparar o Last Will deste cliente
#define dMQTT_WRAPPER_KEEPALIVE_SECONDS            60  // [s]

/// Menor atraso entre tentativas de reconexao automatica
#define dMQTT_WRAPPER_RECONNECT_DELAY_MIN_SECONDS  2   // [s]

/// Maior atraso entre tentativas de reconexao automatica (o atraso cresce
/// exponencialmente a cada falha, ate este limite)
#define dMQTT_WRAPPER_RECONNECT_DELAY_MAX_SECONDS  30  // [s]

/**************************************
* DEFINES PUBLICOS
**************************************/
/// Tamanho maximo de um nome de topico
#define dMQTT_WRAPPER_MAX_TOPIC_LEN     128 // [bytes]
/// Tamanho maximo de um payload publicado ou recebido
#define dMQTT_WRAPPER_MAX_PAYLOAD_LEN   2048 // [bytes]
/// Tamanho maximo dos campos de configuracao de conexao (host, client id,
/// usuario, senha, caminhos de arquivo)
#define dMQTT_WRAPPER_MAX_FIELD_LEN     128 // [bytes]

/**************************************
* TIPOS DE DADOS PUBLICOS
**************************************/
/// Possiveis retornos das funcoes desta biblioteca
typedef enum
{
    eMQTT_WRAPPER_RETURN_OK,
    eMQTT_WRAPPER_RETURN_INVALID_ARGUMENT,
    eMQTT_WRAPPER_RETURN_NOT_INITIALIZED,
    eMQTT_WRAPPER_RETURN_ALREADY_INITIALIZED,
    eMQTT_WRAPPER_RETURN_CONNECTION_ERROR,
    eMQTT_WRAPPER_RETURN_PUBLISH_ERROR,
    eMQTT_WRAPPER_RETURN_SUBSCRIBE_ERROR,

    eMQTT_WRAPPER_RETURN_END_ENUM
} mqttWrapperReturn_t;

/// Niveis de QoS suportados
typedef enum
{
    eMQTT_WRAPPER_QOS_0,
    eMQTT_WRAPPER_QOS_1,
    eMQTT_WRAPPER_QOS_2,

    eMQTT_WRAPPER_QOS_END_ENUM
} mqttWrapperQos_t;

/// Assinatura da funcao de callback chamada sempre que uma mensagem chega em algum topico assinado. 
/// Executada na thread de rede interna da biblioteca: nao deve bloquear por muito tempo,
/// e qualquer acesso a dados compartilhados com a thread principal da aplicacao deve ser
/// protegido pelo proprio chamador (ex.: mutex, variavel atomica).
/// @param topic: nome do topico onde a mensagem chegou (string terminada em zero).
/// @param payload: conteudo da mensagem. Nao e garantido ser terminado em zero.
/// @param payloadLen: tamanho de payload, em bytes.
typedef void (*mqttWrapperMessageCallback_t)(const char *topic, const char *payload, u32 payloadLen);

/// Parametros de configuracao passados a Mqtt_Wrapper_Init(). 
/// Cada no do projeto preenche esta estrutura com seus proprios dados de conexao.
typedef struct
{
    /// Endereco (IP ou hostname) do broker
    char brokerHost[dMQTT_WRAPPER_MAX_FIELD_LEN];
    /// Porta do broker (8883 para TLS, 1883 para conexao sem criptografia)
    u16 brokerPort;
    /// Identificador unico deste cliente perante o broker
    char clientId[dMQTT_WRAPPER_MAX_FIELD_LEN];
    /// Usuario para autenticacao. Vazio ("") se nao utilizado.
    char username[dMQTT_WRAPPER_MAX_FIELD_LEN];
    /// Senha para autenticacao. Vazio ("") se nao utilizado.
    char password[dMQTT_WRAPPER_MAX_FIELD_LEN];
    /// Habilita (true) ou desabilita (false) TLS
    bool useTls;
    /// Caminho do certificado da CA, usado para validar o broker quando
    /// useTls for true. Ignorado se useTls for false.
    char caFilePath[dMQTT_WRAPPER_MAX_FIELD_LEN];
    /// Topico do Last Will deste cliente. Vazio ("") para nao configurar LWT.
    char willTopic[dMQTT_WRAPPER_MAX_TOPIC_LEN];
    /// Payload do Last Will, publicado automaticamente pelo broker caso
    /// este cliente caia sem se desconectar de forma limpa
    char willPayload[dMQTT_WRAPPER_MAX_PAYLOAD_LEN];
    /// QoS do Last Will
    mqttWrapperQos_t willQos;
    /// Define se o Last Will deve ser publicado com retain
    bool willRetain;
    /// Funcao chamada a cada mensagem recebida em um topico assinado.
    /// Pode ser dNULL se este cliente apenas publica, sem assinar nada.
    mqttWrapperMessageCallback_t messageCallback;
} mqttWrapperConfig_t;

/**************************************
* PROTOTIPOS PUBLICOS
**************************************/
mqttWrapperReturn_t Mqtt_Wrapper_Init(const mqttWrapperConfig_t *config);
mqttWrapperReturn_t Mqtt_Wrapper_Publish(const char *topic, const char *payload, u32 payloadLen, mqttWrapperQos_t qos, bool retain);
mqttWrapperReturn_t Mqtt_Wrapper_Subscribe(const char *topic, mqttWrapperQos_t qos);
mqttWrapperReturn_t Mqtt_Wrapper_IsConnected(bool *isConnected);
mqttWrapperReturn_t Mqtt_Wrapper_Deinit(void);

#endif /* _MQTT_WRAPPER_H_ */
/** @} DOXYGEN GROUP TAG END OF FILE */

---

