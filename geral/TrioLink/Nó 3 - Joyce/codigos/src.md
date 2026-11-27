
/*
 * @file    BspButton.c
 * @brief   Implementacao do debounce e da fila de eventos dos botoes.
 */

#include "BspButton.h"

#include "BspGpio.h"

#include <stddef.h>

/// Quantidade de botoes do projeto
#define dBUTTON_COUNT               2

/// Leituras consecutivas iguais para aceitar uma mudanca [amostras]
#define dBUTTON_DEBOUNCE_SAMPLES    3

/// Capacidade da fila de eventos [eventos]
#define dBUTTON_QUEUE_SIZE          8

/// Descricao fixa de cada botao (pino e evento gerado ao pressionar)
typedef struct
{
    bspGpioPin_t pin;
    bspButtonEvent_t event;
} buttonConfig_t;

/// Estado de debounce de cada botao
typedef struct
{
    bool stableLevel;       // Ultimo nivel aceito (true = solto)
    bool candidateLevel;    // Nivel que esta sendo validado
    uint8_t sampleCount;    // Leituras consecutivas do candidato
} buttonState_t;

/// Tabela de botoes (pull-up: pressionado = nivel baixo)
static const buttonConfig_t buttonConfig[dBUTTON_COUNT] =
{
    { eBSP_GPIO_BTN_SELECT, eBSP_BUTTON_EVENT_SELECT },
    { eBSP_GPIO_BTN_ENTER,  eBSP_BUTTON_EVENT_ENTER  }
};

/// Dados locais do modulo
static struct
{
    buttonState_t state[dBUTTON_COUNT];
    bspButtonEvent_t queue[dBUTTON_QUEUE_SIZE];
    uint8_t head;
    uint8_t tail;
    uint8_t count;
} bspButton;

/*
 * @brief   Insere um evento no fim da fila. Se estiver cheia, descarta.
 * @param   event - Evento a ser enfileirado
 * @retval  void
 */
static void BspButton_Push(bspButtonEvent_t event)
{
    if(bspButton.count < dBUTTON_QUEUE_SIZE)
    {
        bspButton.queue[bspButton.head] = event;
        bspButton.head = (uint8_t)((bspButton.head + 1U) % dBUTTON_QUEUE_SIZE);
        bspButton.count++;
    }
}

/*
 * @brief   Processa uma amostra de um botao (debounce e deteccao de clique).
 * @param   index - Indice do botao na tabela buttonConfig
 * @param   level - Nivel lido agora (true = solto, false = pressionado)
 * @retval  void
 */
static void BspButton_Process(uint8_t index, bool level)
{
    buttonState_t *state = &bspButton.state[index];

    if(level != state->candidateLevel)
    {
        state->candidateLevel = level;
        state->sampleCount = 0;
        return;
    }

    if(state->sampleCount < dBUTTON_DEBOUNCE_SAMPLES)
    {
        state->sampleCount++;
    }

    if((state->sampleCount == dBUTTON_DEBOUNCE_SAMPLES)
       && (state->candidateLevel != state->stableLevel))
    {
        state->stableLevel = state->candidateLevel;

        // Clique registrado apenas na borda de descida (botao pressionado)
        if(state->stableLevel == false)
        {
            BspButton_Push(buttonConfig[index].event);
        }
    }
}

bool BspButton_Init(void)
{
    uint8_t index;
    bool level = true;

    bspButton.head = 0;
    bspButton.tail = 0;
    bspButton.count = 0;

    for(index = 0; index < dBUTTON_COUNT; index++)
    {
        if(BspGpio_Read(buttonConfig[index].pin, &level) == false)
        {
            return false;
        }

        bspButton.state[index].stableLevel = level;
        bspButton.state[index].candidateLevel = level;
        bspButton.state[index].sampleCount = dBUTTON_DEBOUNCE_SAMPLES;
    }

    return true;
}

void BspButton_Poll(void)
{
    uint8_t index;
    bool level = true;

    for(index = 0; index < dBUTTON_COUNT; index++)
    {
        if(BspGpio_Read(buttonConfig[index].pin, &level) == true)
        {
            BspButton_Process(index, level);
        }
    }
}

bspButtonEvent_t BspButton_GetEvent(void)
{
    bspButtonEvent_t event = eBSP_BUTTON_EVENT_NONE;

    if(bspButton.count > 0)
    {
        event = bspButton.queue[bspButton.tail];
        bspButton.tail = (uint8_t)((bspButton.tail + 1U) % dBUTTON_QUEUE_SIZE);
        bspButton.count--;
    }

    return event;
}



---


/*
 * @file    BspGpio.c
 * @brief   Implementacao da abstracao de GPIO usando libgpiod v2.
 */

#include "BspGpio.h"

#include <gpiod.h>
#include <stddef.h>

/// Caminho do chip de GPIO do header de 40 pinos (RP1 no Raspberry Pi 5)
#define dGPIO_CHIP_PATH         "/dev/gpiochip0"

/// Nome do consumidor exibido no gpioinfo
#define dGPIO_CONSUMER_NAME     "cascata"

/// Linhas do barramento de dados do display
#define dGPIO_LCD_D0_LINE       16
#define dGPIO_LCD_D1_LINE       17
#define dGPIO_LCD_D2_LINE       18
#define dGPIO_LCD_D3_LINE       19
#define dGPIO_LCD_D4_LINE       20
#define dGPIO_LCD_D5_LINE       21
#define dGPIO_LCD_D6_LINE       22
#define dGPIO_LCD_D7_LINE       23

/// Linhas de controle do display
#define dGPIO_LCD_RST_LINE      27
#define dGPIO_LCD_CS_LINE       5
#define dGPIO_LCD_RS_LINE       24
#define dGPIO_LCD_WR_LINE       25
#define dGPIO_LCD_RD_LINE       26

/// Linhas dos botoes
#define dGPIO_BTN_ENTER_LINE    4
#define dGPIO_BTN_SELECT_LINE   6

/// Quantidade de pinos de saida (display) e de entrada (botoes)
#define dGPIO_OUTPUT_COUNT      13
#define dGPIO_INPUT_COUNT       2

/// Tabela de linhas fisicas, indexada por bspGpioPin_t (saidas primeiro)
static const unsigned int pinLines[eBSP_GPIO_COUNT] =
{
    dGPIO_LCD_D0_LINE,
    dGPIO_LCD_D1_LINE,
    dGPIO_LCD_D2_LINE,
    dGPIO_LCD_D3_LINE,
    dGPIO_LCD_D4_LINE,
    dGPIO_LCD_D5_LINE,
    dGPIO_LCD_D6_LINE,
    dGPIO_LCD_D7_LINE,
    dGPIO_LCD_RST_LINE,
    dGPIO_LCD_CS_LINE,
    dGPIO_LCD_RS_LINE,
    dGPIO_LCD_WR_LINE,
    dGPIO_LCD_RD_LINE,
    dGPIO_BTN_ENTER_LINE,
    dGPIO_BTN_SELECT_LINE
};

/// Dados locais do modulo
static struct
{
    struct gpiod_chip *chip;
    struct gpiod_line_request *outputRequest;
    struct gpiod_line_request *inputRequest;
    bool initialized;
} bspGpio;

/*
 * @brief   Requisita um grupo de linhas ao kernel como saida ou entrada.
 * @param   lines    - Vetor com os numeros das linhas
 * @param   count    - Quantidade de linhas no vetor
 * @param   isOutput - true = saida (nivel baixo inicial), false = entrada com pull-up
 * @retval  Ponteiro da requisicao, ou NULL em caso de falha
 */
static struct gpiod_line_request *BspGpio_RequestLines(const unsigned int *lines,
                                                       size_t count,
                                                       bool isOutput)
{
    struct gpiod_line_settings *settings = gpiod_line_settings_new();
    struct gpiod_line_config *lineConfig = gpiod_line_config_new();
    struct gpiod_request_config *requestConfig = gpiod_request_config_new();
    struct gpiod_line_request *request = NULL;

    if((settings != NULL) && (lineConfig != NULL) && (requestConfig != NULL))
    {
        if(isOutput == true)
        {
            gpiod_line_settings_set_direction(settings, GPIOD_LINE_DIRECTION_OUTPUT);
            gpiod_line_settings_set_output_value(settings, GPIOD_LINE_VALUE_INACTIVE);
        }
        else
        {
            gpiod_line_settings_set_direction(settings, GPIOD_LINE_DIRECTION_INPUT);
            gpiod_line_settings_set_bias(settings, GPIOD_LINE_BIAS_PULL_UP);
        }

        if(gpiod_line_config_add_line_settings(lineConfig, lines, count, settings) == 0)
        {
            gpiod_request_config_set_consumer(requestConfig, dGPIO_CONSUMER_NAME);
            request = gpiod_chip_request_lines(bspGpio.chip, requestConfig, lineConfig);
        }
    }

    gpiod_request_config_free(requestConfig);
    gpiod_line_config_free(lineConfig);
    gpiod_line_settings_free(settings);

    return request;
}

bool BspGpio_Init(void)
{
    bool result = false;

    if(bspGpio.initialized == true)
    {
        return true;
    }

    bspGpio.chip = gpiod_chip_open(dGPIO_CHIP_PATH);

    if(bspGpio.chip != NULL)
    {
        bspGpio.outputRequest = BspGpio_RequestLines(&pinLines[0],
                                                     dGPIO_OUTPUT_COUNT,
                                                     true);
        bspGpio.inputRequest = BspGpio_RequestLines(&pinLines[dGPIO_OUTPUT_COUNT],
                                                    dGPIO_INPUT_COUNT,
                                                    false);

        if((bspGpio.outputRequest != NULL) && (bspGpio.inputRequest != NULL))
        {
            bspGpio.initialized = true;
            result = true;
        }
        else
        {
            BspGpio_Deinit();
        }
    }

    return result;
}

void BspGpio_Deinit(void)
{
    if(bspGpio.outputRequest != NULL)
    {
        gpiod_line_request_release(bspGpio.outputRequest);
        bspGpio.outputRequest = NULL;
    }

    if(bspGpio.inputRequest != NULL)
    {
        gpiod_line_request_release(bspGpio.inputRequest);
        bspGpio.inputRequest = NULL;
    }

    if(bspGpio.chip != NULL)
    {
        gpiod_chip_close(bspGpio.chip);
        bspGpio.chip = NULL;
    }

    bspGpio.initialized = false;
}

bool BspGpio_Write(bspGpioPin_t pin, bool level)
{
    enum gpiod_line_value value = GPIOD_LINE_VALUE_INACTIVE;

    if((bspGpio.initialized == false) || (pin >= eBSP_GPIO_BTN_ENTER))
    {
        return false;
    }

    if(level == true)
    {
        value = GPIOD_LINE_VALUE_ACTIVE;
    }

    return (gpiod_line_request_set_value(bspGpio.outputRequest,
                                         pinLines[pin],
                                         value) == 0);
}

bool BspGpio_Read(bspGpioPin_t pin, bool *level)
{
    enum gpiod_line_value value;

    if((bspGpio.initialized == false) || (level == NULL)
       || (pin < eBSP_GPIO_BTN_ENTER) || (pin >= eBSP_GPIO_COUNT))
    {
        return false;
    }

    value = gpiod_line_request_get_value(bspGpio.inputRequest, pinLines[pin]);

    if(value == GPIOD_LINE_VALUE_ERROR)
    {
        return false;
    }

    *level = (value == GPIOD_LINE_VALUE_ACTIVE);

    return true;
}

bool BspGpio_WriteDataBus(uint8_t value)
{
    enum gpiod_line_value values[dBSP_GPIO_DATA_BUS_WIDTH];
    uint8_t bit;

    if(bspGpio.initialized == false)
    {
        return false;
    }

    for(bit = 0; bit < dBSP_GPIO_DATA_BUS_WIDTH; bit++)
    {
        if((value & (1U << bit)) != 0U)
        {
            values[bit] = GPIOD_LINE_VALUE_ACTIVE;
        }
        else
        {
            values[bit] = GPIOD_LINE_VALUE_INACTIVE;
        }
    }

    return (gpiod_line_request_set_values_subset(bspGpio.outputRequest,
                                                 dBSP_GPIO_DATA_BUS_WIDTH,
                                                 &pinLines[eBSP_GPIO_LCD_D0],
                                                 values) == 0);
}

---

/*
 * @file    BspScreen.c
 * @brief   Interface desenhada com Cairo em uma superficie RGB565 de 320x240.
 *          A mesma superficie e enviada ao ILI9341 (barramento paralelo de
 *          8 bits via BspGpio) e exibida em uma janela GTK4.
 */

#include "BspScreen.h"

#include "BspButton.h"
#include "BspGpio.h"
#include "Fusion.h"

#include <cairo.h>
#include <glib-unix.h>
#include <gtk/gtk.h>
#include <signal.h>
#include <stdio.h>
#include <string.h>
#include <unistd.h>

/// Janela em tela cheia (1) ou 320x240 sem redimensionar (0)
#define dSCREEN_FULLSCREEN              0

/// Resolucao da interface (paisagem) [pixels]
#define dSCREEN_WIDTH                   320
#define dSCREEN_HEIGHT                  240

/// Registrador MADCTL do ILI9341: 0x28 = paisagem (MV) com ordem BGR.
/// Se a imagem sair espelhada, ajustar os bits MX (0x40) e MY (0x80)
#define dSCREEN_MADCTL                  0x68

/// Periodo de atualizacao dos dados na tela [ms]
#define dSCREEN_REFRESH_MS              200

/// Titulo da janela GTK
#define dSCREEN_WINDOW_TITLE            "Cascata de Sensores - TRIOLIKN"

/// Comandos do ILI9341
#define dILI_CMD_SOFT_RESET             0x01
#define dILI_CMD_SLEEP_OUT              0x11
#define dILI_CMD_DISPLAY_ON             0x29
#define dILI_CMD_COLUMN_ADDR            0x2A
#define dILI_CMD_PAGE_ADDR              0x2B
#define dILI_CMD_MEMORY_WRITE           0x2C
#define dILI_CMD_MADCTL                 0x36
#define dILI_CMD_PIXEL_FORMAT           0x3A

/// Formato de pixel RGB565 (16 bits)
#define dILI_PIXEL_FORMAT_16BIT         0x55

/// Maximo de parametros de um passo da sequencia de init
#define dILI_INIT_MAX_PARAMS            15

/// Tempos de espera do display [us]
#define dILI_RESET_PULSE_US             10000
#define dILI_RESET_RECOVERY_US          120000
#define dILI_SLEEP_OUT_DELAY_US         120000

/// Manipulacao do pixel de 16 bits
#define dBYTE_SHIFT                     8
#define dBYTE_MASK                      0xFF

/// Fontes [pixels]
#define dFONT_FAMILY                    "Sans"
#define dFONT_SIZE_TITLE                16.0
#define dFONT_SIZE_MENU_BUTTON          22.0
#define dFONT_SIZE_BUTTON               15.0
#define dFONT_SIZE_VALUE                15.0
#define dFONT_SIZE_INFO                 14.0

/// Formas
#define dLINE_WIDTH                     2.0
#define dLINE_WIDTH_SELECTED            4.0
#define dCORNER_RADIUS                  8.0
#define dDOT_RADIUS                     5.0
#define dFULL_CIRCLE                    6.283185307179586

/// Textos
#define dTEXT_BUFFER_SIZE               32
#define dTEXT_NO_DATA                   "---"
#define dFORMAT_PRESSURE                "%.0f Pa"
#define dFORMAT_TEMPERATURE             "%.2f °C"
#define dFORMAT_ACCEL                   "%.2f m/s²"
#define dFORMAT_GYRO                    "%.2f rad/s"

/// Layout da tela inicial
#define dMENU_TITLE_Y                   30.0

/// Layout das telas dos sensores
#define dSENSOR_TITLE_X                 16.0
#define dSENSOR_TITLE_Y                 24.0
#define dSENSOR_LINK_Y                  58.0
#define dSENSOR_DOT_X                   22.0
#define dSENSOR_LINK_TEXT_X             34.0
#define dSENSOR_ROW_FIRST_Y             78.0
#define dSENSOR_ROW_STEP_Y              42.0
#define dSENSOR_ROW_HEIGHT              32.0
#define dSENSOR_LABEL_X                 16.0
#define dSENSOR_VALUE_X                 140.0
#define dSENSOR_VALUE_WIDTH             164.0
#define dSENSOR_STATE_Y                 218.0
#define dSENSOR_STATE_VALUE_X           84.0

/// Layout da tela do MPU: duas colunas (acelerometro | giroscopio) x 3 eixos
#define dMPU_COLUMN_LEFT_X              8.0
#define dMPU_COLUMN_RIGHT_X             164.0
#define dMPU_COLUMN_WIDTH               148.0
#define dMPU_HEADING_Y                  84.0
#define dMPU_ROW_FIRST_Y                100.0
#define dMPU_ROW_STEP_Y                 36.0
#define dMPU_ROW_HEIGHT                 30.0
#define dMPU_LABEL_OFFSET_X             6.0
#define dMPU_BOX_OFFSET_X               26.0
#define dMPU_BOX_WIDTH                  122.0
#define dMPU_FONT_SIZE_VALUE            14.0

/// Botoes que existem na tela inicial
typedef enum
{
    eMENU_BUTTON_MPU = 0,
    eMENU_BUTTON_BMP,
    eMENU_BUTTON_COUNT
} menuButton_t;

/// Telas do sistema
typedef enum
{
    eSCREEN_MENU = 0,
    eSCREEN_MPU,
    eSCREEN_BMP
} screenId_t;

/// Cor com componentes de 0 a 1
typedef struct
{
    double r;
    double g;
    double b;
} color_t;

/// Retangulo de um elemento de tela [pixels]
typedef struct
{
    double x;
    double y;
    double width;
    double height;
} rect_t;

/// Passo da sequencia de inicializacao do ILI9341
typedef struct
{
    uint8_t command;
    uint8_t numParams;
    uint8_t params[dILI_INIT_MAX_PARAMS];
} iliInitStep_t;

/// Paleta da interface (tema escuro)
static const color_t colorBackground = { 0.07, 0.07, 0.07 };
static const color_t colorForeground = { 0.93, 0.93, 0.93 };
static const color_t colorBorder     = { 0.55, 0.55, 0.55 };
static const color_t colorAccent     = { 0.15, 0.45, 0.90 };
static const color_t colorOk         = { 0.20, 0.80, 0.40 };
static const color_t colorError      = { 0.90, 0.20, 0.20 };
static const color_t colorWarning    = { 0.96, 0.75, 0.10 };

/// Botoes da tela inicial, indexados por menuButton_t
static const rect_t menuButtonRects[eMENU_BUTTON_COUNT] =
{
    [eMENU_BUTTON_MPU] = { 24.0,  80.0, 128.0, 90.0 },
    [eMENU_BUTTON_BMP] = { 168.0, 80.0, 128.0, 90.0 }
};

/// Rotulos dos botoes da tela inicial, indexados por menuButton_t
static const char *const menuButtonLabels[eMENU_BUTTON_COUNT] =
{
    [eMENU_BUTTON_MPU] = "MPU",
    [eMENU_BUTTON_BMP] = "BMP"
};

/// Botao VOLTAR das telas dos sensores (sempre selecionado)
static const rect_t backButtonRect = { 212.0, 8.0, 100.0, 32.0 };

/// Sequencia de inicializacao do ILI9341 (modulo 2.4" LCDwiki)
static const iliInitStep_t iliInitSteps[] =
{
    { 0xCF, 3,  { 0x00, 0xC1, 0x30 } },
    { 0xED, 4,  { 0x64, 0x03, 0x12, 0x81 } },
    { 0xE8, 3,  { 0x85, 0x00, 0x78 } },
    { 0xCB, 5,  { 0x39, 0x2C, 0x00, 0x34, 0x02 } },
    { 0xF7, 1,  { 0x20 } },
    { 0xEA, 2,  { 0x00, 0x00 } },
    { 0xC0, 1,  { 0x1B } },
    { 0xC1, 1,  { 0x01 } },
    { 0xC5, 2,  { 0x30, 0x30 } },
    { 0xC7, 1,  { 0xB7 } },
    { dILI_CMD_MADCTL, 1, { dSCREEN_MADCTL } },
    { dILI_CMD_PIXEL_FORMAT, 1, { dILI_PIXEL_FORMAT_16BIT } },
    { 0xB1, 2,  { 0x00, 0x1A } },
    { 0xB6, 2,  { 0x0A, 0xA2 } },
    { 0xF2, 1,  { 0x00 } },
    { 0x26, 1,  { 0x01 } },
    { 0xE0, 15, { 0x0F, 0x2A, 0x28, 0x08, 0x0E, 0x08, 0x54, 0xA9,
                  0x43, 0x0A, 0x0F, 0x00, 0x00, 0x00, 0x00 } },
    { 0xE1, 15, { 0x00, 0x15, 0x17, 0x07, 0x11, 0x06, 0x2B, 0x56,
                  0x3C, 0x05, 0x10, 0x0F, 0x3F, 0x3F, 0x0F } }
};

/// Dados locais do modulo
static struct
{
    GMainLoop *loop;
    GtkWidget *window;          // NULL se nao houver sessao grafica
    GtkWidget *drawingArea;     // NULL se nao houver sessao grafica
    cairo_surface_t *surface;
    uint16_t previousFrame[dSCREEN_HEIGHT][dSCREEN_WIDTH];
    bool frameValid;            // true = previousFrame reflete o display
    screenId_t screen;
    menuButton_t selected;
} bspScreen;

/*
 * @brief   Envia um byte ao barramento de dados e gera o pulso de escrita.
 * @param   value - Byte a ser enviado
 * @retval  void
 */
static void BspScreen_PanelWriteByte(uint8_t value)
{
    (void)BspGpio_WriteDataBus(value);
    (void)BspGpio_Write(eBSP_GPIO_LCD_WR, false);
    (void)BspGpio_Write(eBSP_GPIO_LCD_WR, true);
}

/*
 * @brief   Envia um comando ao ILI9341 (RS baixo).
 * @param   command - Codigo do comando
 * @retval  void
 */
static void BspScreen_PanelWriteCommand(uint8_t command)
{
    (void)BspGpio_Write(eBSP_GPIO_LCD_RS, false);
    (void)BspGpio_Write(eBSP_GPIO_LCD_CS, false);
    BspScreen_PanelWriteByte(command);
    (void)BspGpio_Write(eBSP_GPIO_LCD_CS, true);
}

/*
 * @brief   Envia um dado ao ILI9341 (RS alto).
 * @param   data - Byte de dado
 * @retval  void
 */
static void BspScreen_PanelWriteData(uint8_t data)
{
    (void)BspGpio_Write(eBSP_GPIO_LCD_RS, true);
    (void)BspGpio_Write(eBSP_GPIO_LCD_CS, false);
    BspScreen_PanelWriteByte(data);
    (void)BspGpio_Write(eBSP_GPIO_LCD_CS, true);
}

/*
 * @brief   Reseta e configura o ILI9341 (paisagem, RGB565).
 * @retval  void
 */
static void BspScreen_PanelInit(void)
{
    size_t step;
    uint8_t param;

    // Todas as saidas iniciam em nivel baixo; WR, RD e CS ficam em repouso (alto)
    (void)BspGpio_Write(eBSP_GPIO_LCD_WR, true);
    (void)BspGpio_Write(eBSP_GPIO_LCD_RD, true);
    (void)BspGpio_Write(eBSP_GPIO_LCD_CS, true);

    (void)BspGpio_Write(eBSP_GPIO_LCD_RST, true);
    usleep(dILI_RESET_PULSE_US);
    (void)BspGpio_Write(eBSP_GPIO_LCD_RST, false);
    usleep(dILI_RESET_PULSE_US);
    (void)BspGpio_Write(eBSP_GPIO_LCD_RST, true);
    usleep(dILI_RESET_RECOVERY_US);

    BspScreen_PanelWriteCommand(dILI_CMD_SOFT_RESET);
    usleep(dILI_RESET_RECOVERY_US);

    for(step = 0; step < (sizeof(iliInitSteps) / sizeof(iliInitSteps[0])); step++)
    {
        BspScreen_PanelWriteCommand(iliInitSteps[step].command);

        for(param = 0; param < iliInitSteps[step].numParams; param++)
        {
            BspScreen_PanelWriteData(iliInitSteps[step].params[param]);
        }
    }

    BspScreen_PanelWriteCommand(dILI_CMD_SLEEP_OUT);
    usleep(dILI_SLEEP_OUT_DELAY_US);
    BspScreen_PanelWriteCommand(dILI_CMD_DISPLAY_ON);

    bspScreen.frameValid = false;
}

/*
 * @brief   Define a janela de escrita da memoria do display e abre a escrita.
 * @param   x0 - Coluna inicial [pixels]
 * @param   y0 - Linha inicial [pixels]
 * @param   x1 - Coluna final, inclusive [pixels]
 * @param   y1 - Linha final, inclusive [pixels]
 * @retval  void
 */
static void BspScreen_PanelSetWindow(uint16_t x0, uint16_t y0, uint16_t x1, uint16_t y1)
{
    BspScreen_PanelWriteCommand(dILI_CMD_COLUMN_ADDR);
    BspScreen_PanelWriteData((uint8_t)(x0 >> dBYTE_SHIFT));
    BspScreen_PanelWriteData((uint8_t)(x0 & dBYTE_MASK));
    BspScreen_PanelWriteData((uint8_t)(x1 >> dBYTE_SHIFT));
    BspScreen_PanelWriteData((uint8_t)(x1 & dBYTE_MASK));

    BspScreen_PanelWriteCommand(dILI_CMD_PAGE_ADDR);
    BspScreen_PanelWriteData((uint8_t)(y0 >> dBYTE_SHIFT));
    BspScreen_PanelWriteData((uint8_t)(y0 & dBYTE_MASK));
    BspScreen_PanelWriteData((uint8_t)(y1 >> dBYTE_SHIFT));
    BspScreen_PanelWriteData((uint8_t)(y1 & dBYTE_MASK));

    BspScreen_PanelWriteCommand(dILI_CMD_MEMORY_WRITE);
}

/*
 * @brief   Envia ao display apenas o retangulo que mudou desde o ultimo
 *          quadro. O envio e por bit-banging, entao transmitir so a regiao
 *          alterada mantem os botoes responsivos.
 * @retval  void
 */
static void BspScreen_PanelFlush(void)
{
    const uint8_t *data;
    const uint16_t *row;
    int stride;
    uint16_t x;
    uint16_t y;
    uint16_t minX = dSCREEN_WIDTH;
    uint16_t minY = dSCREEN_HEIGHT;
    uint16_t maxX = 0;
    uint16_t maxY = 0;
    bool hasChange = false;

    cairo_surface_flush(bspScreen.surface);
    data = cairo_image_surface_get_data(bspScreen.surface);
    stride = cairo_image_surface_get_stride(bspScreen.surface);

    for(y = 0; y < dSCREEN_HEIGHT; y++)
    {
        row = (const uint16_t *)(data + ((size_t)y * (size_t)stride));

        for(x = 0; x < dSCREEN_WIDTH; x++)
        {
            if((bspScreen.frameValid == false) || (row[x] != bspScreen.previousFrame[y][x]))
            {
                hasChange = true;
                minX = (x < minX) ? x : minX;
                maxX = (x > maxX) ? x : maxX;
                minY = (y < minY) ? y : minY;
                maxY = (y > maxY) ? y : maxY;
            }
        }
    }

    if(hasChange == false)
    {
        return;
    }

    BspScreen_PanelSetWindow(minX, minY, maxX, maxY);

    (void)BspGpio_Write(eBSP_GPIO_LCD_RS, true);
    (void)BspGpio_Write(eBSP_GPIO_LCD_CS, false);

    for(y = minY; y <= maxY; y++)
    {
        row = (const uint16_t *)(data + ((size_t)y * (size_t)stride));

        for(x = minX; x <= maxX; x++)
        {
            BspScreen_PanelWriteByte((uint8_t)(row[x] >> dBYTE_SHIFT));
            BspScreen_PanelWriteByte((uint8_t)(row[x] & dBYTE_MASK));
            bspScreen.previousFrame[y][x] = row[x];
        }
    }

    (void)BspGpio_Write(eBSP_GPIO_LCD_CS, true);
    bspScreen.frameValid = true;
}

/*
 * @brief   Define a cor de desenho atual.
 * @param   cr    - Contexto Cairo
 * @param   color - Cor desejada
 * @retval  void
 */
static void BspScreen_SetColor(cairo_t *cr, const color_t *color)
{
    cairo_set_source_rgb(cr, color->r, color->g, color->b);
}

/*
 * @brief   Desenha um texto com a linha de base padronizada, para que textos
 *          diferentes na mesma linha nao "pulem" na vertical.
 * @param   cr         - Contexto Cairo
 * @param   text       - Texto UTF-8
 * @param   x          - Centro (se centralizado) ou inicio do texto [pixels]
 * @param   centerY    - Centro vertical [pixels]
 * @param   size       - Tamanho da fonte [pixels]
 * @param   isBold     - true para negrito
 * @param   isCentered - true para centralizar horizontalmente em x
 * @param   color      - Cor do texto
 * @retval  void
 */
static void BspScreen_DrawText(cairo_t *cr,
                               const char *text,
                               double x,
                               double centerY,
                               double size,
                               bool isBold,
                               bool isCentered,
                               const color_t *color)
{
    cairo_text_extents_t extents;
    cairo_font_extents_t fontExtents;
    double textX = x;
    double baseline;

    cairo_select_font_face(cr, dFONT_FAMILY, CAIRO_FONT_SLANT_NORMAL,
                           (isBold == true) ? CAIRO_FONT_WEIGHT_BOLD : CAIRO_FONT_WEIGHT_NORMAL);
    cairo_set_font_size(cr, size);
    cairo_font_extents(cr, &fontExtents);
    cairo_text_extents(cr, text, &extents);

    if(isCentered == true)
    {
        textX = x - ((extents.width / 2.0) + extents.x_bearing);
    }

    baseline = centerY + ((fontExtents.ascent - fontExtents.descent) / 2.0);

    BspScreen_SetColor(cr, color);
    cairo_move_to(cr, textX, baseline);
    cairo_show_text(cr, text);
}

/*
 * @brief   Cria o caminho de um retangulo de cantos arredondados.
 * @param   cr   - Contexto Cairo
 * @param   rect - Posicao e tamanho do retangulo
 * @retval  void
 */
static void BspScreen_RoundedRect(cairo_t *cr, const rect_t *rect)
{
    const double quarter = G_PI / 2.0;
    const double radius = dCORNER_RADIUS;

    cairo_new_sub_path(cr);
    cairo_arc(cr, rect->x + rect->width - radius, rect->y + radius,
              radius, -quarter, 0.0);
    cairo_arc(cr, rect->x + rect->width - radius, rect->y + rect->height - radius,
              radius, 0.0, quarter);
    cairo_arc(cr, rect->x + radius, rect->y + rect->height - radius,
              radius, quarter, 2.0 * quarter);
    cairo_arc(cr, rect->x + radius, rect->y + radius,
              radius, 2.0 * quarter, 3.0 * quarter);
    cairo_close_path(cr);
}

/*
 * @brief   Desenha um botao arredondado com o rotulo centralizado.
 *          O botao selecionado ganha borda azul.
 * @param   cr         - Contexto Cairo
 * @param   rect       - Posicao e tamanho do botao
 * @param   label      - Texto do botao
 * @param   fontSize   - Tamanho da fonte do rotulo [pixels]
 * @param   isSelected - true para destacar o botao
 * @retval  void
 */
static void BspScreen_DrawButton(cairo_t *cr,
                                 const rect_t *rect,
                                 const char *label,
                                 double fontSize,
                                 bool isSelected)
{
    BspScreen_RoundedRect(cr, rect);
    BspScreen_SetColor(cr, (isSelected == true) ? &colorAccent : &colorBorder);
    cairo_set_line_width(cr, (isSelected == true) ? dLINE_WIDTH_SELECTED : dLINE_WIDTH);
    cairo_stroke(cr);

    BspScreen_DrawText(cr, label, rect->x + (rect->width / 2.0),
                       rect->y + (rect->height / 2.0), fontSize, true, true,
                       &colorForeground);
}

/*
 * @brief   Desenha o cabecalho das telas dos sensores: titulo, botao VOLTAR
 *          (sempre selecionado) e indicador de conexao.
 * @param   cr        - Contexto Cairo
 * @param   title     - Nome do sensor
 * @param   connected - true = ONLINE, false = OFFLINE
 * @retval  void
 */
static void BspScreen_DrawSensorHeader(cairo_t *cr, const char *title, bool connected)
{
    const color_t *color = (connected == true) ? &colorOk : &colorError;

    BspScreen_DrawText(cr, title, dSENSOR_TITLE_X, dSENSOR_TITLE_Y,
                       dFONT_SIZE_TITLE, true, false, &colorForeground);
    BspScreen_DrawButton(cr, &backButtonRect, "VOLTAR", dFONT_SIZE_BUTTON, true);

    BspScreen_SetColor(cr, color);
    cairo_arc(cr, dSENSOR_DOT_X, dSENSOR_LINK_Y, dDOT_RADIUS, 0.0, dFULL_CIRCLE);
    cairo_fill(cr);

    BspScreen_DrawText(cr, (connected == true) ? "ONLINE" : "OFFLINE",
                       dSENSOR_LINK_TEXT_X, dSENSOR_LINK_Y, dFONT_SIZE_INFO,
                       true, false, color);
}

/*
 * @brief   Desenha uma linha "rotulo: [valor]" das telas dos sensores.
 * @param   cr    - Contexto Cairo
 * @param   row   - Indice da linha (0 a 2)
 * @param   label - Rotulo a esquerda
 * @param   value - Texto exibido dentro da caixa
 * @retval  void
 */
static void BspScreen_DrawValueRow(cairo_t *cr, uint8_t row, const char *label, const char *value)
{
    const double rowY = dSENSOR_ROW_FIRST_Y + ((double)row * dSENSOR_ROW_STEP_Y);
    const double centerY = rowY + (dSENSOR_ROW_HEIGHT / 2.0);
    const rect_t box = { dSENSOR_VALUE_X, rowY, dSENSOR_VALUE_WIDTH, dSENSOR_ROW_HEIGHT };

    BspScreen_DrawText(cr, label, dSENSOR_LABEL_X, centerY, dFONT_SIZE_VALUE,
                       false, false, &colorForeground);

    BspScreen_RoundedRect(cr, &box);
    BspScreen_SetColor(cr, &colorBorder);
    cairo_set_line_width(cr, dLINE_WIDTH);
    cairo_stroke(cr);

    BspScreen_DrawText(cr, value, box.x + (box.width / 2.0), centerY,
                       dFONT_SIZE_VALUE, false, true, &colorForeground);
}

/*
 * @brief   Desenha uma linha "eixo: [valor]" dentro de uma coluna da tela do MPU.
 * @param   cr      - Contexto Cairo
 * @param   columnX - Posicao x da coluna [pixels]
 * @param   row     - Indice da linha (0 a 2)
 * @param   label   - Nome do eixo
 * @param   value   - Texto exibido dentro da caixa
 * @retval  void
 */
static void BspScreen_DrawMpuAxisRow(cairo_t *cr,
                                     double columnX,
                                     uint8_t row,
                                     const char *label,
                                     const char *value)
{
    const double rowY = dMPU_ROW_FIRST_Y + ((double)row * dMPU_ROW_STEP_Y);
    const double centerY = rowY + (dMPU_ROW_HEIGHT / 2.0);
    const rect_t box = { columnX + dMPU_BOX_OFFSET_X, rowY, dMPU_BOX_WIDTH, dMPU_ROW_HEIGHT };

    BspScreen_DrawText(cr, label, columnX + dMPU_LABEL_OFFSET_X, centerY,
                       dFONT_SIZE_VALUE, false, false, &colorForeground);

    BspScreen_RoundedRect(cr, &box);
    BspScreen_SetColor(cr, &colorBorder);
    cairo_set_line_width(cr, dLINE_WIDTH);
    cairo_stroke(cr);

    BspScreen_DrawText(cr, value, box.x + (box.width / 2.0), centerY,
                       dMPU_FONT_SIZE_VALUE, false, true, &colorForeground);
}

/*
 * @brief   Desenha o estado do sensor no rodape.
 *          Prioridade: SEM DADOS > DADO OBSOLETO > ANORMAL > NORMAL.
 * @param   cr      - Contexto Cairo
 * @param   hasData - true se ja houve alguma leitura
 * @param   stale   - true se o dado esta obsoleto
 * @param   health  - Classificacao feita pelo Fusion
 * @retval  void
 */
static void BspScreen_DrawState(cairo_t *cr, bool hasData, bool stale, fusionHealth_t health)
{
    const char *text = "NORMAL";
    const color_t *color = &colorOk;

    if(hasData == false)
    {
        text = "SEM DADOS";
        color = &colorWarning;
    }
    else if(stale == true)
    {
        text = "DADO OBSOLETO";
        color = &colorWarning;
    }
    else if(health != eFUSION_HEALTH_NORMAL)
    {
        text = "ANORMAL";
        color = &colorError;
    }

    BspScreen_DrawText(cr, "estado:", dSENSOR_LABEL_X, dSENSOR_STATE_Y,
                       dFONT_SIZE_VALUE, false, false, &colorForeground);
    BspScreen_DrawText(cr, text, dSENSOR_STATE_VALUE_X, dSENSOR_STATE_Y,
                       dFONT_SIZE_VALUE, true, false, color);
}

/*
 * @brief   Desenha a tela inicial: titulo e os botoes MPU e BMP.
 * @param   cr - Contexto Cairo
 * @retval  void
 */
static void BspScreen_DrawMenu(cairo_t *cr)
{
    uint8_t button;

    BspScreen_DrawText(cr, dSCREEN_WINDOW_TITLE, dSCREEN_WIDTH / 2.0, dMENU_TITLE_Y,
                       dFONT_SIZE_TITLE, true, true, &colorForeground);

    for(button = 0; button < eMENU_BUTTON_COUNT; button++)
    {
        BspScreen_DrawButton(cr, &menuButtonRects[button], menuButtonLabels[button],
                             dFONT_SIZE_MENU_BUTTON,
                             (bool)(bspScreen.selected == (menuButton_t)button));
    }
}

/*
 * @brief   Desenha a tela do BMP: pressao, temperatura e estado.
 * @param   cr - Contexto Cairo
 * @retval  void
 */
static void BspScreen_DrawBmp(cairo_t *cr)
{
    fusionBmp_t bmp;
    char pressure[dTEXT_BUFFER_SIZE] = dTEXT_NO_DATA;
    char temperature[dTEXT_BUFFER_SIZE] = dTEXT_NO_DATA;

    (void)memset(&bmp, 0, sizeof(bmp));
    (void)Fusion_GetBmp(&bmp);

    if(bmp.hasData == true)
    {
        (void)snprintf(pressure, sizeof(pressure), dFORMAT_PRESSURE, (double)bmp.pressure);
        (void)snprintf(temperature, sizeof(temperature), dFORMAT_TEMPERATURE,
                       (double)bmp.temperature);
    }

    BspScreen_DrawSensorHeader(cr, "BMP", bmp.connected);
    BspScreen_DrawValueRow(cr, 0, "pressão:", pressure);
    BspScreen_DrawValueRow(cr, 1, "temperatura:", temperature);
    BspScreen_DrawState(cr, bmp.hasData, bmp.stale, bmp.health);
}

/*
 * @brief   Desenha a tela do MPU: acelerometro (x, y, z) a esquerda,
 *          giroscopio (x, y, z) a direita e estado.
 * @param   cr - Contexto Cairo
 * @retval  void
 */
static void BspScreen_DrawMpu(cairo_t *cr)
{
    static const char *const axisNames[] = { "x:", "y:", "z:" };
    fusionMpu_t mpu;
    float accel[3];
    float gyro[3];
    char accelText[3][dTEXT_BUFFER_SIZE];
    char gyroText[3][dTEXT_BUFFER_SIZE];
    uint8_t axis;

    (void)memset(&mpu, 0, sizeof(mpu));
    (void)Fusion_GetMpu(&mpu);

    accel[0] = mpu.accelX;
    accel[1] = mpu.accelY;
    accel[2] = mpu.accelZ;
    gyro[0] = mpu.gyroX;
    gyro[1] = mpu.gyroY;
    gyro[2] = mpu.gyroZ;

    for(axis = 0; axis < 3; axis++)
    {
        (void)snprintf(accelText[axis], sizeof(accelText[axis]), "%s", dTEXT_NO_DATA);
        (void)snprintf(gyroText[axis], sizeof(gyroText[axis]), "%s", dTEXT_NO_DATA);

        if(mpu.hasData == true)
        {
            (void)snprintf(accelText[axis], sizeof(accelText[axis]), dFORMAT_ACCEL,
                           (double)accel[axis]);
            (void)snprintf(gyroText[axis], sizeof(gyroText[axis]), dFORMAT_GYRO,
                           (double)gyro[axis]);
        }
    }

    BspScreen_DrawSensorHeader(cr, "MPU", mpu.connected);

    BspScreen_DrawText(cr, "ACELERÔMETRO", dMPU_COLUMN_LEFT_X + (dMPU_COLUMN_WIDTH / 2.0),
                       dMPU_HEADING_Y, dFONT_SIZE_INFO, true, true, &colorAccent);
    BspScreen_DrawText(cr, "GIROSCÓPIO", dMPU_COLUMN_RIGHT_X + (dMPU_COLUMN_WIDTH / 2.0),
                       dMPU_HEADING_Y, dFONT_SIZE_INFO, true, true, &colorAccent);

    for(axis = 0; axis < 3; axis++)
    {
        BspScreen_DrawMpuAxisRow(cr, dMPU_COLUMN_LEFT_X, axis, axisNames[axis], accelText[axis]);
        BspScreen_DrawMpuAxisRow(cr, dMPU_COLUMN_RIGHT_X, axis, axisNames[axis], gyroText[axis]);
    }

    BspScreen_DrawState(cr, mpu.hasData, mpu.stale, mpu.health);
}

/*
 * @brief   Desenha a tela atual na superficie.
 * @retval  void
 */
static void BspScreen_Render(void)
{
    cairo_t *cr = cairo_create(bspScreen.surface);

    BspScreen_SetColor(cr, &colorBackground);
    cairo_paint(cr);

    switch(bspScreen.screen)
    {
        case eSCREEN_MENU:
            BspScreen_DrawMenu(cr);
            break;

        case eSCREEN_MPU:
            BspScreen_DrawMpu(cr);
            break;

        case eSCREEN_BMP:
            BspScreen_DrawBmp(cr);
            break;

        default:
            break;
    }

    cairo_destroy(cr);
}

/*
 * @brief   Redesenha a interface e atualiza o display e a janela.
 * @retval  void
 */
static void BspScreen_Update(void)
{
    BspScreen_Render();
    BspScreen_PanelFlush();

    if(bspScreen.drawingArea != NULL)
    {
        gtk_widget_queue_draw(bspScreen.drawingArea);
    }
}

/*
 * @brief   Aplica um evento de botao na navegacao.
 *          Tela inicial: SELECIONAR alterna MPU/BMP e ENTER abre a tela.
 *          Telas dos sensores: VOLTAR esta sempre selecionado, entao
 *          ENTER volta para a tela inicial.
 * @param   event - Evento recebido
 * @retval  void
 */
static void BspScreen_HandleEvent(bspButtonEvent_t event)
{
    if(bspScreen.screen == eSCREEN_MENU)
    {
        if(event == eBSP_BUTTON_EVENT_SELECT)
        {
            bspScreen.selected = (menuButton_t)(((uint8_t)bspScreen.selected + 1U)
                                                % (uint8_t)eMENU_BUTTON_COUNT);
        }
        else if(event == eBSP_BUTTON_EVENT_ENTER)
        {
            bspScreen.screen = (bspScreen.selected == eMENU_BUTTON_MPU)
                               ? eSCREEN_MPU : eSCREEN_BMP;
        }
    }
    else if(event == eBSP_BUTTON_EVENT_ENTER)
    {
        bspScreen.screen = eSCREEN_MENU;
    }
}

/*
 * @brief   Tarefa periodica: le os botoes fisicos e trata os eventos.
 * @param   userData - Nao utilizado
 * @retval  G_SOURCE_CONTINUE para manter a tarefa
 */
static gboolean BspScreen_OnPollTick(gpointer userData)
{
    bspButtonEvent_t event;
    bool changed = false;

    (void)userData;

    BspButton_Poll();
    event = BspButton_GetEvent();

    while(event != eBSP_BUTTON_EVENT_NONE)
    {
        BspScreen_HandleEvent(event);
        changed = true;
        event = BspButton_GetEvent();
    }

    if(changed == true)
    {
        BspScreen_Update();
    }

    return G_SOURCE_CONTINUE;
}

/*
 * @brief   Tarefa periodica: atualiza os dados mostrados na tela.
 * @param   userData - Nao utilizado
 * @retval  G_SOURCE_CONTINUE para manter a tarefa
 */
static gboolean BspScreen_OnRefreshTick(gpointer userData)
{
    (void)userData;

    BspScreen_Update();

    return G_SOURCE_CONTINUE;
}

/*
 * @brief   Teclado de teste: Espaco = SELECIONAR, Enter = ENTER.
 * @param   controller - Controlador de teclado (nao utilizado)
 * @param   keyval     - Tecla pressionada
 * @param   keycode    - Codigo da tecla (nao utilizado)
 * @param   state      - Modificadores (nao utilizado)
 * @param   userData   - Nao utilizado
 * @retval  TRUE se a tecla foi tratada
 */
static gboolean BspScreen_OnKeyPressed(GtkEventControllerKey *controller,
                                       guint keyval,
                                       guint keycode,
                                       GdkModifierType state,
                                       gpointer userData)
{
    gboolean handled = FALSE;

    (void)controller;
    (void)keycode;
    (void)state;
    (void)userData;

    if(keyval == GDK_KEY_space)
    {
        BspScreen_HandleEvent(eBSP_BUTTON_EVENT_SELECT);
        handled = TRUE;
    }
    else if((keyval == GDK_KEY_Return) || (keyval == GDK_KEY_KP_Enter))
    {
        BspScreen_HandleEvent(eBSP_BUTTON_EVENT_ENTER);
        handled = TRUE;
    }

    if(handled == TRUE)
    {
        BspScreen_Update();
    }

    return handled;
}

/*
 * @brief   Funcao de desenho da janela: copia a superficie do display,
 *          mantendo a proporcao (importante em tela cheia).
 * @param   area     - Area de desenho (nao utilizada)
 * @param   cr       - Contexto Cairo da janela
 * @param   width    - Largura disponivel [pixels]
 * @param   height   - Altura disponivel [pixels]
 * @param   userData - Nao utilizado
 * @retval  void
 */
static void BspScreen_GtkDraw(GtkDrawingArea *area,
                              cairo_t *cr,
                              int width,
                              int height,
                              gpointer userData)
{
    const double scaleX = (double)width / (double)dSCREEN_WIDTH;
    const double scaleY = (double)height / (double)dSCREEN_HEIGHT;
    const double scale = (scaleX < scaleY) ? scaleX : scaleY;

    (void)area;
    (void)userData;

    cairo_set_source_rgb(cr, 0.0, 0.0, 0.0);
    cairo_paint(cr);

    cairo_translate(cr, ((double)width - ((double)dSCREEN_WIDTH * scale)) / 2.0,
                    ((double)height - ((double)dSCREEN_HEIGHT * scale)) / 2.0);
    cairo_scale(cr, scale, scale);
    cairo_set_source_surface(cr, bspScreen.surface, 0.0, 0.0);
    cairo_paint(cr);
}

/*
 * @brief   Fechar a janela encerra o loop de eventos.
 * @param   window   - Janela (nao utilizada)
 * @param   userData - Nao utilizado
 * @retval  FALSE para permitir o fechamento
 */
static gboolean BspScreen_OnCloseRequest(GtkWindow *window, gpointer userData)
{
    (void)window;
    (void)userData;

    g_main_loop_quit(bspScreen.loop);

    return FALSE;
}

/*
 * @brief   SIGINT/SIGTERM encerram o loop, para liberar os GPIOs com
 *          BspGpio_Deinit na Main.
 * @param   userData - Nao utilizado
 * @retval  G_SOURCE_REMOVE
 */
static gboolean BspScreen_OnSignal(gpointer userData)
{
    (void)userData;

    g_main_loop_quit(bspScreen.loop);

    return G_SOURCE_REMOVE;
}

/*
 * @brief   Cria a janela GTK (espelho do display e teclado de teste).
 *          Sem sessao grafica a janela nao existe e o display continua
 *          funcionando normalmente.
 * @retval  void
 */
static void BspScreen_CreateWindow(void)
{
    GtkEventController *keyController;

    if(gtk_init_check() == FALSE)
    {
        (void)fprintf(stderr, "BspScreen: sem sessao grafica, janela desativada\n");
        return;
    }

    bspScreen.window = gtk_window_new();
    bspScreen.drawingArea = gtk_drawing_area_new();

    gtk_window_set_title(GTK_WINDOW(bspScreen.window), dSCREEN_WINDOW_TITLE);
    gtk_window_set_resizable(GTK_WINDOW(bspScreen.window), FALSE);
    gtk_drawing_area_set_content_width(GTK_DRAWING_AREA(bspScreen.drawingArea),
                                       dSCREEN_WIDTH);
    gtk_drawing_area_set_content_height(GTK_DRAWING_AREA(bspScreen.drawingArea),
                                        dSCREEN_HEIGHT);
    gtk_drawing_area_set_draw_func(GTK_DRAWING_AREA(bspScreen.drawingArea),
                                   BspScreen_GtkDraw, NULL, NULL);
    gtk_window_set_child(GTK_WINDOW(bspScreen.window), bspScreen.drawingArea);

    keyController = gtk_event_controller_key_new();
    g_signal_connect(keyController, "key-pressed", G_CALLBACK(BspScreen_OnKeyPressed), NULL);
    gtk_widget_add_controller(bspScreen.window, keyController);

    g_signal_connect(bspScreen.window, "close-request",
                     G_CALLBACK(BspScreen_OnCloseRequest), NULL);

#if dSCREEN_FULLSCREEN == 1
    gtk_window_fullscreen(GTK_WINDOW(bspScreen.window));
#endif

    gtk_window_present(GTK_WINDOW(bspScreen.window));
}

int BspScreen_Run(int argc, char **argv)
{
    int status = 1;

    (void)argc;
    (void)argv;

    bspScreen.surface = cairo_image_surface_create(CAIRO_FORMAT_RGB16_565,
                                                   dSCREEN_WIDTH, dSCREEN_HEIGHT);
    bspScreen.loop = g_main_loop_new(NULL, FALSE);

    if((cairo_surface_status(bspScreen.surface) == CAIRO_STATUS_SUCCESS)
       && (bspScreen.loop != NULL))
    {
        bspScreen.screen = eSCREEN_MENU;
        bspScreen.selected = eMENU_BUTTON_MPU;

        BspScreen_PanelInit();
        BspScreen_CreateWindow();

        (void)g_timeout_add(dBSP_BUTTON_POLL_PERIOD_MS, BspScreen_OnPollTick, NULL);
        (void)g_timeout_add(dSCREEN_REFRESH_MS, BspScreen_OnRefreshTick, NULL);
        (void)g_unix_signal_add(SIGINT, BspScreen_OnSignal, NULL);
        (void)g_unix_signal_add(SIGTERM, BspScreen_OnSignal, NULL);

        BspScreen_Update();
        g_main_loop_run(bspScreen.loop);

        status = 0;
    }

    if(bspScreen.window != NULL)
    {
        gtk_window_destroy(GTK_WINDOW(bspScreen.window));
        bspScreen.window = NULL;
        bspScreen.drawingArea = NULL;
    }

    if(bspScreen.loop != NULL)
    {
        g_main_loop_unref(bspScreen.loop);
        bspScreen.loop = NULL;
    }

    cairo_surface_destroy(bspScreen.surface);
    bspScreen.surface = NULL;

    return status;
}


---

/*
 * @file    Fusion.c
 * @brief   Implementacao do recebimento MQTT (fusion/bmp_mpu e status/#),
 *          parse cJSON, staleness, filtragem de status retido e analise
 *          dos dados dos sensores.
 */

#include "Fusion.h"

#include "MqttWrapper.h"

#include <cjson/cJSON.h>
#include <math.h>
#include <pthread.h>
#include <stdio.h>
#include <string.h>
#include <strings.h>
#include <time.h>

/// Topicos assinados
#define dFUSION_TOPIC_BMP_MPU           "fusion/bmp_mpu"
#define dFUSION_TOPIC_STATUS_ALL        "status/#"
#define dFUSION_TOPIC_STATUS_RASP1      "status/rasp1"   // BMP
#define dFUSION_TOPIC_STATUS_RASP2      "status/rasp2"   // MPU

/// Valores do campo Status
#define dFUSION_STATUS_ONLINE           "ONLINE"
#define dFUSION_STATUS_OFFLINE          "OFFLINE"
#define dFUSION_STATUS_TEXT_LEN         32

/// OBS: a conexao MQTT (TLS, usuario/senha, Last Will e status/rasp3) e feita
/// pelo Main via MqttWrapper; este modulo apenas assina e interpreta os dados.

/// Intervalo de publicacao esperado de fusion/bmp_mpu [ms]
#define dFUSION_BMP_INTERVAL_MS         1000
#define dFUSION_MPU_INTERVAL_MS         1000

/// Dado e obsoleto apos este multiplo do intervalo esperado
#define dFUSION_STALENESS_FACTOR        3

/// Janela inicial apos Subscribe [ms] em que o broker descarrega mensagens
/// retidas antigas. Mensagens "ONLINE" recebidas nessa janela ficam pendentes
/// ate confirmacao por trafego ao vivo, evitando iniciar em ONLINE se o no
/// estiver desligado.
#define dFUSION_RETAINED_WINDOW_MS      300U

/// Faixas consideradas normais para ambiente comum
#define dFUSION_BMP_TEMP_MIN            -10.0f      // [°C]
#define dFUSION_BMP_TEMP_MAX            50.0f       // [°C]
#define dFUSION_BMP_PRESSURE_MIN        80000.0f    // [Pa]
#define dFUSION_BMP_PRESSURE_MAX        110000.0f   // [Pa]
#define dFUSION_MPU_ACCEL_MAX           20.0f       // Modulo maximo [m/s²]
#define dFUSION_MPU_GYRO_MAX            10.0f       // Modulo maximo [rad/s]

/// Conversoes de tempo
#define dFUSION_MS_PER_SECOND           1000U
#define dFUSION_NS_PER_MS               1000000U

/// Dados locais do modulo
static struct
{
    pthread_mutex_t mutex;
    uint64_t subscribeMs;

    struct
    {
        bool valid;
        bool online;
        bool pendingRetainedOnline;
        bool remoteStale;
        uint64_t lastRxMs;
        fusionBmp_t data;
    } bmp;

    struct
    {
        bool valid;
        bool online;
        bool pendingRetainedOnline;
        uint64_t lastRxMs;
        fusionMpu_t data;
    } mpu;
} fusion = { .mutex = PTHREAD_MUTEX_INITIALIZER };

/*
 * @brief   Le um relogio do sistema em milissegundos.
 * @param   clockId - CLOCK_MONOTONIC (staleness) ou CLOCK_REALTIME (timestamp)
 * @retval  Tempo atual [ms]
 */
static uint64_t Fusion_GetClockMs(clockid_t clockId)
{
    struct timespec now;

    clock_gettime(clockId, &now);

    return ((uint64_t)now.tv_sec * dFUSION_MS_PER_SECOND)
           + ((uint64_t)now.tv_nsec / dFUSION_NS_PER_MS);
}

/*
 * @brief   Indica se a mensagem chegou dentro da janela inicial de descarga
 *          de mensagens retidas logo apos Fusion_Subscribe().
 *          Deve ser chamada com fusion.mutex travado.
 * @param   nowMs - Instante atual [ms, monotonico]
 * @retval  true  - Dentro da janela inicial (possivel mensagem retida)
 * @retval  false - Trafego ao vivo apos a janela inicial
 */
static bool Fusion_IsInInitialRetainedWindow(uint64_t nowMs)
{
    if(fusion.subscribeMs == 0U)
    {
        return true;
    }

    return ((nowMs - fusion.subscribeMs) <= (uint64_t)dFUSION_RETAINED_WINDOW_MS);
}

/*
 * @brief   Verifica se o dado esta obsoleto por timeout local.
 * @param   valid      - true se ja houve alguma leitura valida
 * @param   lastRxMs   - Instante da ultima mensagem ao vivo [ms, monotonico]
 * @param   nowMs      - Instante atual [ms, monotonico]
 * @param   intervalMs - Intervalo de publicacao esperado [ms]
 * @retval  true  - Dado obsoleto (ou inexistente / apenas retido)
 * @retval  false - Dado dentro do prazo
 */
static bool Fusion_IsStale(bool valid,
                           uint64_t lastRxMs,
                           uint64_t nowMs,
                           uint32_t intervalMs)
{
    if((valid == false) || (lastRxMs == 0U))
    {
        return true;
    }

    return ((nowMs - lastRxMs) > ((uint64_t)intervalMs * dFUSION_STALENESS_FACTOR));
}

/*
 * @brief   Classifica os valores do BMP como normais ou anormais.
 * @param   temperature - Temperatura [°C]
 * @param   pressure    - Pressao [Pa]
 * @param   altitude    - Altitude [m]
 * @retval  Classificacao dos valores
 */
static fusionHealth_t Fusion_EvaluateBmp(float temperature,
                                         float pressure,
                                         float altitude)
{
    bool normal = ((isfinite(temperature) != 0)
                   && (isfinite(pressure) != 0)
                   && (isfinite(altitude) != 0)
                   && (temperature >= dFUSION_BMP_TEMP_MIN)
                   && (temperature <= dFUSION_BMP_TEMP_MAX)
                   && (pressure >= dFUSION_BMP_PRESSURE_MIN)
                   && (pressure <= dFUSION_BMP_PRESSURE_MAX));

    return (normal == true) ? eFUSION_HEALTH_NORMAL : eFUSION_HEALTH_ABNORMAL;
}

/*
 * @brief   Classifica os valores do MPU como normais ou anormais.
 *          Compara os modulos ao quadrado para nao depender da libm.
 * @param   data - Dados do MPU ja preenchidos
 * @retval  Classificacao dos valores
 */
static fusionHealth_t Fusion_EvaluateMpu(const fusionMpu_t *data)
{
    float accelSq = (data->accelX * data->accelX)
                    + (data->accelY * data->accelY)
                    + (data->accelZ * data->accelZ);
    float gyroSq = (data->gyroX * data->gyroX)
                   + (data->gyroY * data->gyroY)
                   + (data->gyroZ * data->gyroZ);
    bool normal = ((isfinite(accelSq) != 0)
                   && (isfinite(gyroSq) != 0)
                   && (accelSq <= (dFUSION_MPU_ACCEL_MAX * dFUSION_MPU_ACCEL_MAX))
                   && (gyroSq <= (dFUSION_MPU_GYRO_MAX * dFUSION_MPU_GYRO_MAX)));

    return (normal == true) ? eFUSION_HEALTH_NORMAL : eFUSION_HEALTH_ABNORMAL;
}

/*
 * @brief   Le um campo numerico de um objeto cJSON.
 * @param   parent - Objeto que contem o campo
 * @param   key    - Nome do campo
 * @param   value  - Ponteiro que recebe o valor
 * @retval  true  - Campo existe e e numerico
 * @retval  false - Campo ausente ou de outro tipo
 */
static bool Fusion_GetNumber(const cJSON *parent, const char *key, double *value)
{
    const cJSON *item = cJSON_GetObjectItemCaseSensitive(parent, key);

    if(cJSON_IsNumber(item) == 0)
    {
        return false;
    }

    *value = item->valuedouble;

    return true;
}

/*
 * @brief   Le um campo booleano de um objeto cJSON (opcional com valor padrao).
 * @param   parent       - Objeto que contem o campo
 * @param   key          - Nome do campo
 * @param   defaultValue - Valor retornado caso ausente ou nao booleano
 * @retval  Valor booleano lido ou defaultValue
 */
static bool Fusion_GetBoolOrDefault(const cJSON *parent, const char *key, bool defaultValue)
{
    const cJSON *item = cJSON_GetObjectItemCaseSensitive(parent, key);

    if(cJSON_IsBool(item) == 0)
    {
        return defaultValue;
    }

    return (cJSON_IsTrue(item) != 0);
}

/*
 * @brief   Le um sub-objeto com os campos x, y e z.
 * @param   parent - Objeto que contem o sub-objeto
 * @param   key    - Nome do sub-objeto (ex: "accel")
 * @param   x      - Ponteiro que recebe x
 * @param   y      - Ponteiro que recebe y
 * @param   z      - Ponteiro que recebe z
 * @retval  true  - Os tres eixos foram lidos
 * @retval  false - Sub-objeto ou algum eixo ausente
 */
static bool Fusion_GetVector(const cJSON *parent,
                             const char *key,
                             float *x,
                             float *y,
                             float *z)
{
    const cJSON *object = cJSON_GetObjectItemCaseSensitive(parent, key);
    double vx;
    double vy;
    double vz;

    if((cJSON_IsObject(object) == 0)
       || (Fusion_GetNumber(object, "x", &vx) == false)
       || (Fusion_GetNumber(object, "y", &vy) == false)
       || (Fusion_GetNumber(object, "z", &vz) == false))
    {
        return false;
    }

    *x = (float)vx;
    *y = (float)vy;
    *z = (float)vz;

    return true;
}

/*
 * @brief   Extrai e atualiza o sub-objeto "bmp" de fusion/bmp_mpu.
 * @param   bmpObj - Sub-objeto JSON "bmp"
 * @param   nowMs  - Instante atual [ms, monotonico]
 * @retval  true  - Sub-objeto valido processado
 * @retval  false - Sub-objeto ausente ou invalido
 */
static bool Fusion_ParseBmpObject(const cJSON *bmpObj, uint64_t nowMs)
{
    double timestamp;
    double temperature;
    double pressure;
    double altitude = 0.0;
    bool remoteStale;
    bool isInitialBurst;

    if((cJSON_IsObject(bmpObj) == 0)
       || (Fusion_GetNumber(bmpObj, "timestamp", &timestamp) == false)
       || (Fusion_GetNumber(bmpObj, "temperature", &temperature) == false)
       || (Fusion_GetNumber(bmpObj, "pressure", &pressure) == false))
    {
        return false;
    }

    (void)Fusion_GetNumber(bmpObj, "altitude", &altitude);
    remoteStale = Fusion_GetBoolOrDefault(bmpObj, "stale", false);

    pthread_mutex_lock(&fusion.mutex);

    isInitialBurst = Fusion_IsInInitialRetainedWindow(nowMs);

    fusion.bmp.data.timestamp = (int64_t)timestamp;
    fusion.bmp.data.temperature = (float)temperature;
    fusion.bmp.data.pressure = (float)pressure;
    fusion.bmp.data.altitude = (float)altitude;
    fusion.bmp.data.health = Fusion_EvaluateBmp(fusion.bmp.data.temperature,
                                                fusion.bmp.data.pressure,
                                                fusion.bmp.data.altitude);
    fusion.bmp.remoteStale = remoteStale;
    fusion.bmp.valid = true;

    if(isInitialBurst == false)
    {
        fusion.bmp.lastRxMs = nowMs;

        if((fusion.bmp.pendingRetainedOnline == true) && (remoteStale == false))
        {
            fusion.bmp.online = true;
            fusion.bmp.pendingRetainedOnline = false;
        }
    }

    pthread_mutex_unlock(&fusion.mutex);

    return true;
}

/*
 * @brief   Extrai e atualiza o sub-objeto "mpu" de fusion/bmp_mpu.
 * @param   mpuObj - Sub-objeto JSON "mpu"
 * @param   nowMs  - Instante atual [ms, monotonico]
 * @retval  true  - Sub-objeto valido processado
 * @retval  false - Sub-objeto ausente ou invalido
 */
static bool Fusion_ParseMpuObject(const cJSON *mpuObj, uint64_t nowMs)
{
    fusionMpu_t data;
    double timestamp;
    bool isInitialBurst;

    if(cJSON_IsObject(mpuObj) == 0)
    {
        return false;
    }

    memset(&data, 0, sizeof(data));

    if((Fusion_GetNumber(mpuObj, "timestamp", &timestamp) == false)
       || (Fusion_GetVector(mpuObj, "accel", &data.accelX, &data.accelY, &data.accelZ) == false)
       || (Fusion_GetVector(mpuObj, "gyro", &data.gyroX, &data.gyroY, &data.gyroZ) == false))
    {
        return false;
    }

    data.timestamp = (int64_t)timestamp;
    data.health = Fusion_EvaluateMpu(&data);

    pthread_mutex_lock(&fusion.mutex);

    isInitialBurst = Fusion_IsInInitialRetainedWindow(nowMs);

    fusion.mpu.data = data;
    fusion.mpu.valid = true;

    if(isInitialBurst == false)
    {
        fusion.mpu.lastRxMs = nowMs;

        if(fusion.mpu.pendingRetainedOnline == true)
        {
            fusion.mpu.online = true;
            fusion.mpu.pendingRetainedOnline = false;
        }
    }

    pthread_mutex_unlock(&fusion.mutex);

    return true;
}

/*
 * @brief   Trata uma mensagem consolidada de fusion/bmp_mpu:
 *          { "timestamp": ..., "bmp": {...}, "mpu": {...} }
 * @param   payload    - Texto JSON recebido
 * @param   payloadLen - Tamanho do texto [bytes]
 * @retval  void
 */
static void Fusion_HandleBmpMpu(const char *payload, size_t payloadLen)
{
    cJSON *root = cJSON_ParseWithLength(payload, payloadLen);
    const cJSON *bmpObj;
    const cJSON *mpuObj;
    uint64_t nowMs;
    bool bmpOk;
    bool mpuOk;

    if(root == NULL)
    {
        (void)fprintf(stderr, "[fusion] AVISO: JSON invalido em %s\n", dFUSION_TOPIC_BMP_MPU);
        return;
    }

    nowMs = Fusion_GetClockMs(CLOCK_MONOTONIC);
    bmpObj = cJSON_GetObjectItemCaseSensitive(root, "bmp");
    mpuObj = cJSON_GetObjectItemCaseSensitive(root, "mpu");

    bmpOk = Fusion_ParseBmpObject(bmpObj, nowMs);
    mpuOk = Fusion_ParseMpuObject(mpuObj, nowMs);

    if((bmpOk == true) || (mpuOk == true))
    {
        (void)printf("[fusion] %s recebido: %.*s\n",
                     dFUSION_TOPIC_BMP_MPU, (int)payloadLen, payload);
    }
    else
    {
        (void)fprintf(stderr, "[fusion] AVISO: campos bmp/mpu ausentes em %s\n",
                      dFUSION_TOPIC_BMP_MPU);
    }

    cJSON_Delete(root);
}

/*
 * @brief   Interpreta o payload de status: aceita JSON {"Status":"ONLINE"}
 *          (chave "Status"/"status"), string JSON ("ONLINE") ou texto puro
 *          ONLINE/OFFLINE, sem diferenciar maiusculas de minusculas.
 * @param   payload    - Conteudo recebido
 * @param   payloadLen - Tamanho do conteudo [bytes]
 * @param   online     - Recebe true (ONLINE) ou false (OFFLINE)
 * @retval  true  - Payload reconhecido
 * @retval  false - Payload sem status valido (deve ser ignorado)
 */
static bool Fusion_ParseStatus(const char *payload, size_t payloadLen, bool *online)
{
    cJSON *root = cJSON_ParseWithLength(payload, payloadLen);
    const cJSON *status = NULL;
    const char *text = NULL;
    char plain[dFUSION_STATUS_TEXT_LEN];
    bool valid = false;

    if(root != NULL)
    {
        if(cJSON_IsObject(root) != 0)
        {
            status = cJSON_GetObjectItemCaseSensitive(root, "Status");

            if(status == NULL)
            {
                status = cJSON_GetObjectItemCaseSensitive(root, "status");
            }
        }
        else
        {
            status = root;
        }

        if((cJSON_IsString(status) != 0) && (status->valuestring != NULL))
        {
            text = status->valuestring;
        }
    }
    else if(payloadLen < sizeof(plain))
    {
        // Texto puro: copia sem espacos/quebras nas pontas
        size_t start = 0;
        size_t length = payloadLen;

        while((start < length) && (payload[start] <= ' '))
        {
            start++;
        }

        while((length > start) && (payload[length - 1U] <= ' '))
        {
            length--;
        }

        memcpy(plain, &payload[start], length - start);
        plain[length - start] = '\0';
        text = plain;
    }

    if(text != NULL)
    {
        if(strcasecmp(text, dFUSION_STATUS_ONLINE) == 0)
        {
            *online = true;
            valid = true;
        }
        else if(strcasecmp(text, dFUSION_STATUS_OFFLINE) == 0)
        {
            *online = false;
            valid = true;
        }
    }

    cJSON_Delete(root);   // aceita NULL

    return valid;
}

/*
 * @brief   Trata uma mensagem de status/raspN (indicador de conexao).
 *          Se "ONLINE" chegar na janela inicial de descarga de retidos logo
 *          apos Subscribe, aguarda sinal vivo antes de marcar ONLINE, evitando
 *          comecar em ONLINE quando o no esta desligado.
 * @param   isBmpNode  - true = status/rasp1 (BMP), false = status/rasp2 (MPU)
 * @param   payload    - Conteudo recebido
 * @param   payloadLen - Tamanho do conteudo [bytes]
 * @retval  void
 */
static void Fusion_HandleStatus(bool isBmpNode, const char *payload, size_t payloadLen)
{
    bool online = false;
    uint64_t nowMs;
    bool isInitialBurst;
    bool effectiveOnline;

    if(Fusion_ParseStatus(payload, payloadLen, &online) == false)
    {
        return;
    }

    nowMs = Fusion_GetClockMs(CLOCK_MONOTONIC);

    pthread_mutex_lock(&fusion.mutex);

    isInitialBurst = Fusion_IsInInitialRetainedWindow(nowMs);

    if(isBmpNode == true)
    {
        if(online == false)
        {
            fusion.bmp.online = false;
            fusion.bmp.pendingRetainedOnline = false;
        }
        else if(isInitialBurst == false)
        {
            fusion.bmp.online = true;
            fusion.bmp.pendingRetainedOnline = false;
        }
        else
        {
            fusion.bmp.pendingRetainedOnline = true;
        }

        effectiveOnline = fusion.bmp.online;
    }
    else
    {
        if(online == false)
        {
            fusion.mpu.online = false;
            fusion.mpu.pendingRetainedOnline = false;
        }
        else if(isInitialBurst == false)
        {
            fusion.mpu.online = true;
            fusion.mpu.pendingRetainedOnline = false;
        }
        else
        {
            fusion.mpu.pendingRetainedOnline = true;
        }

        effectiveOnline = fusion.mpu.online;
    }

    pthread_mutex_unlock(&fusion.mutex);

    (void)printf("[fusion] %s -> %s%s\n",
                 (isBmpNode == true) ? dFUSION_TOPIC_STATUS_RASP1 : dFUSION_TOPIC_STATUS_RASP2,
                 (effectiveOnline == true) ? dFUSION_STATUS_ONLINE : dFUSION_STATUS_OFFLINE,
                 ((online == true) && (isInitialBurst == true)) ? " (aguardando sinal vivo)" : "");
}

void Fusion_Init(void)
{
    pthread_mutex_lock(&fusion.mutex);
    fusion.subscribeMs = 0U;
    memset(&fusion.bmp, 0, sizeof(fusion.bmp));
    memset(&fusion.mpu, 0, sizeof(fusion.mpu));
    pthread_mutex_unlock(&fusion.mutex);
}

bool Fusion_Subscribe(void)
{
    bool ok = true;

    pthread_mutex_lock(&fusion.mutex);
    fusion.subscribeMs = Fusion_GetClockMs(CLOCK_MONOTONIC);
    fusion.bmp.online = false;
    fusion.bmp.pendingRetainedOnline = false;
    fusion.bmp.lastRxMs = 0U;
    fusion.mpu.online = false;
    fusion.mpu.pendingRetainedOnline = false;
    fusion.mpu.lastRxMs = 0U;
    pthread_mutex_unlock(&fusion.mutex);

    ok &= (Mqtt_Wrapper_Subscribe(dFUSION_TOPIC_BMP_MPU, eMQTT_WRAPPER_QOS_1)
           == eMQTT_WRAPPER_RETURN_OK);
    ok &= (Mqtt_Wrapper_Subscribe(dFUSION_TOPIC_STATUS_ALL, eMQTT_WRAPPER_QOS_1)
           == eMQTT_WRAPPER_RETURN_OK);

    return ok;
}

void Fusion_OnMessage(const char *topic, const char *payload, uint32_t payloadLen)
{
    if((topic == NULL) || (payload == NULL) || (payloadLen == 0U))
    {
        return;
    }

    if(strcmp(topic, dFUSION_TOPIC_BMP_MPU) == 0)
    {
        Fusion_HandleBmpMpu(payload, (size_t)payloadLen);
    }
    else if(strcmp(topic, dFUSION_TOPIC_STATUS_RASP1) == 0)
    {
        Fusion_HandleStatus(true, payload, (size_t)payloadLen);
    }
    else if(strcmp(topic, dFUSION_TOPIC_STATUS_RASP2) == 0)
    {
        Fusion_HandleStatus(false, payload, (size_t)payloadLen);
    }
}

void Fusion_OnBrokerDisconnected(void)
{
    pthread_mutex_lock(&fusion.mutex);
    fusion.bmp.online = false;
    fusion.bmp.pendingRetainedOnline = false;
    fusion.mpu.online = false;
    fusion.mpu.pendingRetainedOnline = false;
    pthread_mutex_unlock(&fusion.mutex);
}

bool Fusion_GetBmp(fusionBmp_t *data)
{
    uint64_t nowMs;

    if(data == NULL)
    {
        return false;
    }

    nowMs = Fusion_GetClockMs(CLOCK_MONOTONIC);

    pthread_mutex_lock(&fusion.mutex);

    *data = fusion.bmp.data;
    data->hasData = fusion.bmp.valid;
    data->connected = fusion.bmp.online;
    data->stale = (fusion.bmp.remoteStale == true)
                  || Fusion_IsStale(fusion.bmp.valid, fusion.bmp.lastRxMs,
                                    nowMs, dFUSION_BMP_INTERVAL_MS);

    pthread_mutex_unlock(&fusion.mutex);

    return true;
}

bool Fusion_GetMpu(fusionMpu_t *data)
{
    uint64_t nowMs;

    if(data == NULL)
    {
        return false;
    }

    nowMs = Fusion_GetClockMs(CLOCK_MONOTONIC);

    pthread_mutex_lock(&fusion.mutex);

    *data = fusion.mpu.data;
    data->hasData = fusion.mpu.valid;
    data->connected = fusion.mpu.online;
    data->stale = Fusion_IsStale(fusion.mpu.valid, fusion.mpu.lastRxMs,
                                 nowMs, dFUSION_MPU_INTERVAL_MS);

    pthread_mutex_unlock(&fusion.mutex);

    return true;
}

---

/*
 * @file    Main.c
 * @brief   Integra os modulos do no de display (No 3): GPIO, botoes, MQTT
 *          (MqttWrapper + Fusion) e interface (BspScreen).
 * @details
 *  - Este no e tambem o broker Mosquitto: host padrao "localhost" e porta
 *    TLS 8883. Uso: ./cascata [host] [porta]
 *  - A senha vem da variavel de ambiente MQTT_PASSWORD (ou o valor padrao).
 *  - UMA unica conexao MQTT (MqttWrapper, TLS + usuario/senha) faz tudo:
 *    recebe os dados (callback = Fusion_OnMessage), assina os topicos e
 *    publica status/rasp3 (ONLINE retido, Last Will OFFLINE).
 *  - Fluxo igual ao da main de teste: Init -> espera a conexao -> assina
 *    -> publica ONLINE. Como a sessao e limpa, a thread auxiliar refaz as
 *    assinaturas e o ONLINE a cada reconexao.
 */

#include "BspButton.h"
#include "BspGpio.h"
#include "BspScreen.h"
#include "Fusion.h"
#include "MqttWrapper.h"

#include <errno.h>
#include <pthread.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <time.h>
#include <unistd.h>

/// Broker MQTT padrao (este no e o proprio broker) e porta TLS
#define dMAIN_BROKER_HOST           "localhost"
#define dMAIN_BROKER_PORT           8883

/// Posicao dos argumentos opcionais: ./cascata [host] [porta]
#define dMAIN_ARG_HOST              1
#define dMAIN_ARG_PORT              2

/// Credenciais e certificado da conexao segura
#define dMAIN_MQTT_CLIENT_ID        "no3_display"
#define dMAIN_MQTT_USERNAME         "no3"
#define dMAIN_MQTT_PASSWORD_ENV     "MQTT_PASSWORD"
#define dMAIN_MQTT_PASSWORD_DEFAULT "triolink"
#define dMAIN_CA_FILE_PATH          "/etc/mosquitto/certs/ca.crt"

/// Topico de status deste no e valores do campo Status
#define dMAIN_TOPIC_STATUS          "status/rasp3"
#define dMAIN_STATUS_ONLINE         "ONLINE"
#define dMAIN_STATUS_OFFLINE        "OFFLINE"

/// Tamanho do buffer do payload de status [bytes]
#define dMAIN_STATUS_BUFFER_LEN     64

/// Espera pela conexao inicial: tempo maximo e passo de verificacao [ms]
#define dMAIN_CONNECT_TIMEOUT_MS    5000
#define dMAIN_CONNECT_POLL_MS       100

/// Periodo de verificacao da conexao pela thread auxiliar [us]
#define dMAIN_WATCHDOG_PERIOD_US    200000

/// Tempo para o broker receber o OFFLINE antes de desconectar [us]
#define dMAIN_SHUTDOWN_FLUSH_US     300000

/// Conversoes de tempo
#define dMAIN_MS_PER_SECOND         1000
#define dMAIN_NS_PER_MS             1000000
#define dMAIN_US_PER_MS             1000

/// Variaveis internas do programa principal
static struct
{
    pthread_t watchdogThread;
    volatile int stopRequested;
    bool wasConnected;
} mainNode3;

static long long Main_GetTimestampMs(void);
static bool Main_PublishStatus(const char *status);
static bool Main_BrokerInit(const char *brokerHost, int brokerPort);
static bool Main_WaitForConnection(void);
static void Main_OnConnected(void);
static void Main_BrokerDeinit(void);
static void *Main_WatchdogThread(void *argument);

/*
 * @brief   Le o relogio de parede em milissegundos (Unix, UTC).
 * @retval  Tempo atual [ms]
 */
static long long Main_GetTimestampMs(void)
{
    struct timespec now;

    clock_gettime(CLOCK_REALTIME, &now);

    return ((long long)now.tv_sec * dMAIN_MS_PER_SECOND)
           + ((long long)now.tv_nsec / dMAIN_NS_PER_MS);
}

/*
 * @brief   Publica o status deste no (QoS 1, retido) em JSON.
 * @param   status - "ONLINE" ou "OFFLINE"
 * @retval  true  - Publicacao entregue a MqttWrapper
 * @retval  false - Falha ao montar ou publicar
 */
static bool Main_PublishStatus(const char *status)
{
    char payload[dMAIN_STATUS_BUFFER_LEN];
    int length = snprintf(payload, sizeof(payload),
                          "{\"timestamp\":%lld,\"Status\":\"%s\"}",
                          Main_GetTimestampMs(), status);

    if((length <= 0) || ((size_t)length >= sizeof(payload)))
    {
        return false;
    }

    return (Mqtt_Wrapper_Publish(dMAIN_TOPIC_STATUS, payload, (u32)length,
                                 eMQTT_WRAPPER_QOS_1, true)
            == eMQTT_WRAPPER_RETURN_OK);
}

/*
 * @brief   Configura e inicia a conexao com o broker (TLS, usuario/senha,
 *          Last Will de status/rasp3 e callback de mensagens do Fusion).
 * @param   brokerHost - Endereco do broker
 * @param   brokerPort - Porta TLS do broker
 * @retval  true  - Mqtt_Wrapper_Init OK
 * @retval  false - Falha ao iniciar
 */
static bool Main_BrokerInit(const char *brokerHost, int brokerPort)
{
    mqttWrapperConfig_t config;
    const char *password = getenv(dMAIN_MQTT_PASSWORD_ENV);

    if((password == NULL) || (password[0] == '\0'))
    {
        password = dMAIN_MQTT_PASSWORD_DEFAULT;
    }

    memset(&config, 0, sizeof(config));

    strncpy(config.brokerHost, brokerHost, sizeof(config.brokerHost) - 1);
    config.brokerPort = (u16)brokerPort;
    strncpy(config.clientId, dMAIN_MQTT_CLIENT_ID, sizeof(config.clientId) - 1);
    strncpy(config.username, dMAIN_MQTT_USERNAME, sizeof(config.username) - 1);
    strncpy(config.password, password, sizeof(config.password) - 1);
    config.useTls = true;
    strncpy(config.caFilePath, dMAIN_CA_FILE_PATH, sizeof(config.caFilePath) - 1);
    strncpy(config.willTopic, dMAIN_TOPIC_STATUS, sizeof(config.willTopic) - 1);
    (void)snprintf(config.willPayload, sizeof(config.willPayload),
                   "{\"timestamp\":%lld,\"Status\":\"%s\"}",
                   Main_GetTimestampMs(), dMAIN_STATUS_OFFLINE);
    config.willQos = eMQTT_WRAPPER_QOS_1;
    config.willRetain = true;
    config.messageCallback = Fusion_OnMessage;

    (void)printf("[no3] Conectando em %s:%d (TLS)...\n", brokerHost, brokerPort);

    return (Mqtt_Wrapper_Init(&config) == eMQTT_WRAPPER_RETURN_OK);
}

/*
 * @brief   Espera o broker confirmar a conexao (ate dMAIN_CONNECT_TIMEOUT_MS).
 * @retval  true  - Conectado
 * @retval  false - Tempo esgotado
 */
static bool Main_WaitForConnection(void)
{
    bool isConnected = false;
    u32 waitedMs = 0;

    while((isConnected == false) && (waitedMs < dMAIN_CONNECT_TIMEOUT_MS))
    {
        (void)Mqtt_Wrapper_IsConnected(&isConnected);

        if(isConnected == false)
        {
            usleep(dMAIN_CONNECT_POLL_MS * dMAIN_US_PER_MS);
            waitedMs += dMAIN_CONNECT_POLL_MS;
        }
    }

    return isConnected;
}

/*
 * @brief   Acoes a cada (re)conexao: assina os topicos e publica ONLINE.
 */
static void Main_OnConnected(void)
{
    if(Fusion_Subscribe() == false)
    {
        (void)fprintf(stderr, "[no3] AVISO: falha ao assinar algum topico\n");
    }

    (void)Main_PublishStatus(dMAIN_STATUS_ONLINE);
    (void)printf("[no3] Conectado: topicos assinados e ONLINE publicado.\n");
}

/*
 * @brief   Publica OFFLINE (desconexao limpa nao dispara o Last Will) e
 *          encerra a MqttWrapper.
 */
static void Main_BrokerDeinit(void)
{
    bool isConnected = false;

    if((Mqtt_Wrapper_IsConnected(&isConnected) == eMQTT_WRAPPER_RETURN_OK)
       && (isConnected == true))
    {
        (void)Main_PublishStatus(dMAIN_STATUS_OFFLINE);
        usleep(dMAIN_SHUTDOWN_FLUSH_US);
    }

    (void)Mqtt_Wrapper_Deinit();
}

/*
 * @brief   Thread auxiliar: em cada reconexao refaz assinaturas e ONLINE;
 *          em cada queda marca os outros nos como offline.
 * @param   argument - Nao utilizado
 * @retval  NULL
 */
static void *Main_WatchdogThread(void *argument)
{
    bool isConnected;

    (void)argument;

    while(mainNode3.stopRequested == 0)
    {
        isConnected = false;

        if(Mqtt_Wrapper_IsConnected(&isConnected) == eMQTT_WRAPPER_RETURN_OK)
        {
            if((isConnected == true) && (mainNode3.wasConnected == false))
            {
                Main_OnConnected();
            }
            else if((isConnected == false) && (mainNode3.wasConnected == true))
            {
                (void)printf("[no3] Conexao com o broker perdida (reconectando).\n");
                Fusion_OnBrokerDisconnected();
            }

            mainNode3.wasConnected = isConnected;
        }

        usleep(dMAIN_WATCHDOG_PERIOD_US);
    }

    return NULL;
}

int main(int argc, char **argv)
{
    int status = 1;
    const char *brokerHost = dMAIN_BROKER_HOST;
    int brokerPort = dMAIN_BROKER_PORT;

    if(argc > dMAIN_ARG_HOST)
    {
        brokerHost = argv[dMAIN_ARG_HOST];
    }

    if(argc > dMAIN_ARG_PORT)
    {
        brokerPort = atoi(argv[dMAIN_ARG_PORT]);
    }

    memset(&mainNode3, 0, sizeof(mainNode3));
    errno = 0;

    if(BspGpio_Init() == false)
    {
        // errno ajuda a diferenciar: EACCES/EPERM (permissao), EBUSY
        // (linha em uso por outro processo), ENOENT (gpiochip inexistente)
        (void)fprintf(stderr, "Falha ao iniciar o GPIO (errno=%d: %s)\n",
                      errno, strerror(errno));
    }
    else if(BspButton_Init() == false)
    {
        (void)fprintf(stderr, "Falha ao iniciar os botoes (errno=%d: %s)\n",
                      errno, strerror(errno));
    }
    else
    {
        Fusion_Init();

        if(Main_BrokerInit(brokerHost, brokerPort) == false)
        {
            (void)fprintf(stderr, "ERRO: Mqtt_Wrapper_Init falhou. O broker esta rodando?\n");
        }
        else if(Main_WaitForConnection() == false)
        {
            (void)fprintf(stderr, "ERRO: broker nao confirmou a conexao a tempo.\n");
            Main_BrokerDeinit();
        }
        else
        {
            Main_OnConnected();
            mainNode3.wasConnected = true;

            if(pthread_create(&mainNode3.watchdogThread, NULL, Main_WatchdogThread, NULL) != 0)
            {
                (void)fprintf(stderr, "Falha ao iniciar a thread de conexao\n");
            }
            else
            {
                status = BspScreen_Run(argc, argv);

                mainNode3.stopRequested = 1;
                (void)pthread_join(mainNode3.watchdogThread, NULL);
            }

            Main_BrokerDeinit();
        }
    }

    BspGpio_Deinit();

    return status;
}

---

/**************************************
* @file MqttWrapper.c
* @addtogroup MqttWrapper
* @brief Wrapper sobre libmosquitto para publish/subscribe MQTT com TLS e
         autenticacao usuario/senha, com reconexao automatica.
* @author Caio Oliveira
* @details
* \n <b>Ferramentas:</b>
* - Raspberry Pi 5, Linux, libmosquitto (pacote mosquitto).
* \n <b>Dependencias:</b>
* - AssertTypes.
* - libmosquitto (link com -lmosquitto).
* - pthread (link com -lpthread), usado apenas para o mutex interno; a
*   thread de rede em si e gerenciada pela propria libmosquitto via
*   mosquitto_loop_start().
* \n <b>Observacoes:</b>
* - Biblioteca singleton: suporta uma unica conexao MQTT por processo,
*   adequado ao projeto porque cada no roda um unico binario cliente.
* - mosquitto_loop_start() cria uma thread de rede interna da propria
*   libmosquitto: os callbacks desta biblioteca (conexao, mensagem
*   recebida) executam NESSA thread, nao na thread principal da
*   aplicacao. Por isso o estado interno (isConnected, ponteiro de
*   callback) e protegido por mutex.
* - Limitacao conhecida: em caso de reconexao apos queda, assinaturas
*   anteriores (Mqtt_Wrapper_Subscribe) nao sao refeitas automaticamente
*   pela biblioteca. Quem utiliza a wrapper deve chamar Subscribe
*   novamente dentro do fluxo de deteccao de reconexao, caso isso seja
*   necessario para o caso de uso.
* @copyright https://github.com/joy-rocha/TrioLink
**************************************/

/**************************************
* INCLUDES
**************************************/
#include "MqttWrapper.h"
#include <mosquitto.h>
#include <pthread.h>
#include <stdio.h>
#include <string.h>

/**************************************
* DEFINES LOCAIS
**************************************/
/* Nao ha defines locais nesta biblioteca */

/**************************************
* CONSTANTES
**************************************/
/* Nao ha constantes internas nesta biblioteca */

/**************************************
* ESTRUTURAS DE DADOS LOCAIS
**************************************/
/// Variaveis internas da biblioteca (conexao MQTT e sincronizacao entre
/// a thread de rede da libmosquitto e a thread principal da aplicacao)
static struct
{
    /// Handle da conexao mantido pela libmosquitto
    struct mosquitto *mosq;
    /// Funcao de callback fornecida pelo usuario em Mqtt_Wrapper_Init()
    mqttWrapperMessageCallback_t messageCallback;
    /// Protege isConnected e messageCallback contra acesso simultaneo
    /// pela thread de rede (callbacks) e pela thread principal
    pthread_mutex_t lock;
    /// Indica se Mqtt_Wrapper_Init() ja foi chamada com sucesso
    bool isInitialized;
    /// Indica se a conexao com o broker esta ativa no momento
    bool isConnected;
} mqttWrapper;

/**************************************
* PROTOTIPOS LOCAIS
**************************************/
static int Mqtt_Wrapper_QosToInt(mqttWrapperQos_t qos);
static void Mqtt_Wrapper_OnConnect(struct mosquitto *mosq, void *userData, int resultCode);
static void Mqtt_Wrapper_OnDisconnect(struct mosquitto *mosq, void *userData, int resultCode);
static void Mqtt_Wrapper_OnMessage(struct mosquitto *mosq, void *userData, const struct mosquitto_message *message);

/**************************************
* FUNCOES PUBLICAS
**************************************/
/**************************************
* @brief Inicializa a biblioteca libmosquitto, configura TLS, credenciais
           e Last Will, conecta ao broker e inicia a thread de rede.
* @param config: ponteiro para a estrutura de configuracao preenchida pelo
                 chamador. Nao e necessario manter este ponteiro valido
                 apos o retorno da funcao: os dados sao copiados.
* @retval eMQTT_WRAPPER_RETURN_OK se a conexao foi estabelecida com sucesso.
          eMQTT_WRAPPER_RETURN_INVALID_ARGUMENT se config for dNULL.
          eMQTT_WRAPPER_RETURN_ALREADY_INITIALIZED se chamada mais de uma vez.
          eMQTT_WRAPPER_RETURN_CONNECTION_ERROR se a conexao inicial falhar.
* @details Apos retornar eMQTT_WRAPPER_RETURN_OK, a reconexao em caso de
           queda e automatica (gerenciada pela thread interna da
           libmosquitto), com atraso crescente entre tentativas (ver
           dMQTT_WRAPPER_RECONNECT_DELAY_MIN_SECONDS e
           dMQTT_WRAPPER_RECONNECT_DELAY_MAX_SECONDS).
**************************************/
mqttWrapperReturn_t Mqtt_Wrapper_Init(const mqttWrapperConfig_t *config)
{
    mqttWrapperReturn_t result = eMQTT_WRAPPER_RETURN_INVALID_ARGUMENT;

    if(config == dNULL)
    {
        result = eMQTT_WRAPPER_RETURN_INVALID_ARGUMENT;
    }
    else if(mqttWrapper.isInitialized == true)
    {
        result = eMQTT_WRAPPER_RETURN_ALREADY_INITIALIZED;
    }
    else
    {
        memset(&mqttWrapper, 0, sizeof(mqttWrapper));
        pthread_mutex_init(&mqttWrapper.lock, dNULL);
        mqttWrapper.messageCallback = config->messageCallback;

        mosquitto_lib_init();

        mqttWrapper.mosq = mosquitto_new(config->clientId, true, dNULL);

        if(mqttWrapper.mosq == dNULL)
        {
            result = eMQTT_WRAPPER_RETURN_CONNECTION_ERROR;
        }
        else
        {
            // Usuario e senha, se configurados (string vazia desabilita)
            if(strlen(config->username) > 0)
            {
                mosquitto_username_pw_set(mqttWrapper.mosq, config->username, config->password);
            }

            // TLS, se habilitado
            if(config->useTls == true)
            {
                mosquitto_tls_set(mqttWrapper.mosq, config->caFilePath, dNULL, dNULL, dNULL, dNULL);
            }

            // Last Will, configurado antes do connect: o broker precisa
            // saber o testamento deste cliente desde o pacote CONNECT
            if(strlen(config->willTopic) > 0)
            {
                mosquitto_will_set(mqttWrapper.mosq, config->willTopic,
                                    (int)strlen(config->willPayload), config->willPayload,
                                    Mqtt_Wrapper_QosToInt(config->willQos), config->willRetain);
            }

            mosquitto_connect_callback_set(mqttWrapper.mosq, Mqtt_Wrapper_OnConnect);
            mosquitto_disconnect_callback_set(mqttWrapper.mosq, Mqtt_Wrapper_OnDisconnect);
            mosquitto_message_callback_set(mqttWrapper.mosq, Mqtt_Wrapper_OnMessage);

            // Define o atraso entre tentativas de reconexao automatica,
            // crescendo exponencialmente ate o maximo configurado
            mosquitto_reconnect_delay_set(mqttWrapper.mosq,
                                           dMQTT_WRAPPER_RECONNECT_DELAY_MIN_SECONDS,
                                           dMQTT_WRAPPER_RECONNECT_DELAY_MAX_SECONDS, true);

            if(mosquitto_connect(mqttWrapper.mosq, config->brokerHost, (int)config->brokerPort, dMQTT_WRAPPER_KEEPALIVE_SECONDS) != MOSQ_ERR_SUCCESS)
            {
                result = eMQTT_WRAPPER_RETURN_CONNECTION_ERROR;
            }
            else if(mosquitto_loop_start(mqttWrapper.mosq) != MOSQ_ERR_SUCCESS)
            {
                result = eMQTT_WRAPPER_RETURN_CONNECTION_ERROR;
            }
            else
            {
                mqttWrapper.isInitialized = true;
                result = eMQTT_WRAPPER_RETURN_OK;
            }
        }
    }
    return result;
}

/**************************************
* @brief Publica uma mensagem em um topico.
* @param topic: nome do topico.
* @param payload: conteudo a ser publicado.
* @param payloadLen: tamanho de payload, em bytes.
* @param qos: nivel de QoS da publicacao.
* @param retain: se true, o broker guarda esta mensagem como a ultima
                 conhecida deste topico, entregue a novos assinantes.
* @retval eMQTT_WRAPPER_RETURN_OK se a mensagem foi entregue a libmosquitto
          para envio (nao garante que o broker ja a recebeu; QoS 1/2 fazem
          essa garantia de forma assincrona, via a propria biblioteca).
          eMQTT_WRAPPER_RETURN_NOT_INITIALIZED se Init() nao foi chamada.
          eMQTT_WRAPPER_RETURN_INVALID_ARGUMENT se algum parametro for invalido.
          eMQTT_WRAPPER_RETURN_PUBLISH_ERROR em caso de falha da libmosquitto.
**************************************/
mqttWrapperReturn_t Mqtt_Wrapper_Publish(const char *topic, const char *payload, u32 payloadLen, mqttWrapperQos_t qos, bool retain)
{
    mqttWrapperReturn_t result = eMQTT_WRAPPER_RETURN_INVALID_ARGUMENT;

    if((topic == dNULL) || (payload == dNULL))
    {
        result = eMQTT_WRAPPER_RETURN_INVALID_ARGUMENT;
    }
    else if(mqttWrapper.isInitialized == false)
    {
        result = eMQTT_WRAPPER_RETURN_NOT_INITIALIZED;
    }
    else
    {
        int publishResult = mosquitto_publish(mqttWrapper.mosq, dNULL, topic,
                                               (int)payloadLen, payload,
                                               Mqtt_Wrapper_QosToInt(qos), retain);

        if(publishResult == MOSQ_ERR_SUCCESS)
        {
            result = eMQTT_WRAPPER_RETURN_OK;
        }
        else
        {
            result = eMQTT_WRAPPER_RETURN_PUBLISH_ERROR;
        }
    }
    return result;
}

/**************************************
* @brief Assina um topico, passando a receber suas mensagens via o
           callback configurado em Mqtt_Wrapper_Init().
* @param topic: nome do topico, podendo conter curingas ("+", "#").
* @param qos: nivel de QoS desejado para a assinatura.
* @retval eMQTT_WRAPPER_RETURN_OK se a assinatura foi solicitada com sucesso.
          eMQTT_WRAPPER_RETURN_NOT_INITIALIZED se Init() nao foi chamada.
          eMQTT_WRAPPER_RETURN_INVALID_ARGUMENT se topic for dNULL.
          eMQTT_WRAPPER_RETURN_SUBSCRIBE_ERROR em caso de falha da libmosquitto.
**************************************/
mqttWrapperReturn_t Mqtt_Wrapper_Subscribe(const char *topic, mqttWrapperQos_t qos)
{
    mqttWrapperReturn_t result = eMQTT_WRAPPER_RETURN_INVALID_ARGUMENT;

    if(topic == dNULL)
    {
        result = eMQTT_WRAPPER_RETURN_INVALID_ARGUMENT;
    }
    else if(mqttWrapper.isInitialized == false)
    {
        result = eMQTT_WRAPPER_RETURN_NOT_INITIALIZED;
    }
    else
    {
        int subscribeResult = mosquitto_subscribe(mqttWrapper.mosq, dNULL, topic, Mqtt_Wrapper_QosToInt(qos));

        if(subscribeResult == MOSQ_ERR_SUCCESS)
        {
            result = eMQTT_WRAPPER_RETURN_OK;
        }
        else
        {
            result = eMQTT_WRAPPER_RETURN_SUBSCRIBE_ERROR;
        }
    }
    return result;
}

/**************************************
* @brief Informa se a conexao com o broker esta ativa neste instante.
* @param isConnected: ponteiro onde o estado sera escrito.
* @retval eMQTT_WRAPPER_RETURN_OK se a leitura foi realizada.
          eMQTT_WRAPPER_RETURN_INVALID_ARGUMENT se isConnected for dNULL.
          eMQTT_WRAPPER_RETURN_NOT_INITIALIZED se Init() nao foi chamada.
**************************************/
mqttWrapperReturn_t Mqtt_Wrapper_IsConnected(bool *isConnected)
{
    mqttWrapperReturn_t result = eMQTT_WRAPPER_RETURN_INVALID_ARGUMENT;

    if(isConnected == dNULL)
    {
        result = eMQTT_WRAPPER_RETURN_INVALID_ARGUMENT;
    }
    else if(mqttWrapper.isInitialized == false)
    {
        result = eMQTT_WRAPPER_RETURN_NOT_INITIALIZED;
    }
    else
    {
        // Protegido por mutex: isConnected tambem e escrito pela thread de rede,
        // dentro de Mqtt_Wrapper_OnConnect/OnDisconnect
        pthread_mutex_lock(&mqttWrapper.lock);
        *isConnected = mqttWrapper.isConnected;
        pthread_mutex_unlock(&mqttWrapper.lock);

        result = eMQTT_WRAPPER_RETURN_OK;
    }
    return result;
}

/**************************************
* @brief Encerra a conexao, para a thread de rede e libera os recursos da libmosquitto.
* @param Nenhum.
* @retval eMQTT_WRAPPER_RETURN_OK se finalizado com sucesso.
          eMQTT_WRAPPER_RETURN_NOT_INITIALIZED se Init() nao foi chamada.
**************************************/
mqttWrapperReturn_t Mqtt_Wrapper_Deinit(void)
{
    mqttWrapperReturn_t result = eMQTT_WRAPPER_RETURN_NOT_INITIALIZED;

    if(mqttWrapper.isInitialized == true)
    {
        mosquitto_disconnect(mqttWrapper.mosq);
        mosquitto_loop_stop(mqttWrapper.mosq, false);
        mosquitto_destroy(mqttWrapper.mosq);
        mosquitto_lib_cleanup();
        pthread_mutex_destroy(&mqttWrapper.lock);

        memset(&mqttWrapper, 0, sizeof(mqttWrapper));

        result = eMQTT_WRAPPER_RETURN_OK;
    }
    return result;
}

/**************************************
* FUNCOES LOCAIS
**************************************/
/**************************************
* @brief Converte o enum publico de QoS para o inteiro esperado pela API
           da libmosquitto.
* @param qos: nivel de QoS no formato desta biblioteca.
* @retval O valor inteiro correspondente (0, 1 ou 2).
**************************************/
static int Mqtt_Wrapper_QosToInt(mqttWrapperQos_t qos)
{
    int qosInt;

    if(qos == eMQTT_WRAPPER_QOS_1)
    {
        qosInt = 1;
    }
    else if(qos == eMQTT_WRAPPER_QOS_2)
    {
        qosInt = 2;
    }
    else
    {
        qosInt = 0;
    }
    return qosInt;
}

/**************************************
* @brief Callback da libmosquitto, chamado na thread de rede interna
           sempre que a conexao com o broker e estabelecida (incluindo
           reconexoes automaticas).
* @param mosq: handle da conexao (nao utilizado).
* @param userData: dado de usuario passado em mosquitto_new (nao utilizado).
* @param resultCode: 0 indica sucesso; qualquer outro valor indica falha.
**************************************/
static void Mqtt_Wrapper_OnConnect(struct mosquitto *mosq, void *userData, int resultCode)
{
    (void)mosq;
    (void)userData;

    pthread_mutex_lock(&mqttWrapper.lock);
    mqttWrapper.isConnected = (resultCode == 0);
    pthread_mutex_unlock(&mqttWrapper.lock);
}

/**************************************
* @brief Callback da libmosquitto, chamado na thread de rede interna
           sempre que a conexao com o broker e perdida.
* @param mosq: handle da conexao (nao utilizado).
* @param userData: dado de usuario passado em mosquitto_new (nao utilizado).
* @param resultCode: motivo da desconexao, reportado pela libmosquitto.
**************************************/
static void Mqtt_Wrapper_OnDisconnect(struct mosquitto *mosq, void *userData, int resultCode)
{
    (void)mosq;
    (void)userData;
    (void)resultCode;

    pthread_mutex_lock(&mqttWrapper.lock);
    mqttWrapper.isConnected = false;
    pthread_mutex_unlock(&mqttWrapper.lock);
}

/**************************************
* @brief Callback da libmosquitto, chamado na thread de rede interna
           sempre que uma mensagem chega em um topico assinado. Repassa a
           mensagem para o callback do usuario configurado em Init().
* @param mosq: handle da conexao (nao utilizado).
* @param userData: dado de usuario passado em mosquitto_new (nao utilizado).
* @param message: estrutura da libmosquitto contendo topico e payload.
**************************************/
static void Mqtt_Wrapper_OnMessage(struct mosquitto *mosq, void *userData, const struct mosquitto_message *message)
{
    (void)mosq;
    (void)userData;

    mqttWrapperMessageCallback_t callbackToCall = dNULL;

    pthread_mutex_lock(&mqttWrapper.lock);
    callbackToCall = mqttWrapper.messageCallback;
    pthread_mutex_unlock(&mqttWrapper.lock);

    if((callbackToCall != dNULL) && (message->payload != dNULL))
    {
        callbackToCall(message->topic, (const char *)message->payload, (u32)message->payloadlen);
    }
}
/** @} DOXYGEN GROUP TAG END OF FILE */


