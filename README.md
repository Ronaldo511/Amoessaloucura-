# Amoessaloucura-
conteudoabap
*&---------------------------------------------------------------------*
*& Report Z_EXIBE_RESERVAS_V3
*&---------------------------------------------------------------------*
*&
*&---------------------------------------------------------------------*
REPORT z_exibe_reservas_v3.
TABLES: zreservas_db_v3.
"variaveis locais para botões e logica

DATA: gt_reservas   TYPE STANDARD TABLE OF zreservas_db_v3,
      ls_reserva    TYPE zreservas_db_v3,
      btn_gera_text TYPE smp_dyntxt.
*-----------------------------------------------------------------------*

"TELA DE SELEÇÃO ATUALIZADA COM MELHORIAS EM USABILIDADE

SELECTION-SCREEN BEGIN OF BLOCK b1 WITH FRAME TITLE TEXT-001.
  "NOVA BARRA DO NOME DO CLIENTE QUE SERA IMPRESSO
  PARAMETERS: p_nome TYPE string LOWER CASE.
*------------------------------------------------------------------------*
  "NOVAS LACUNAS PARA FAIXA DE PREÇO(SUBSTITUINDO A BUSCA POR ID QUE NÃO ESTAVA FUNCIONANDO

  PARAMETERS: p_v_min TYPE p DECIMALS 2 DEFAULT '300.00',
              p_v_max TYPE p DECIMALS 2 DEFAULT '500.00'.

*----------------------------------------------------------------------------------------*

  "INTERVALO DE DATAS  JA EXISTENTE

  SELECT-OPTIONS s_data FOR zreservas_db_v3-data_res.

  SELECTION-SCREEN SKIP.
  SELECTION-SCREEN PUSHBUTTON /1(30) btn_gera USER-COMMAND gera.
SELECTION-SCREEN END OF BLOCK b1.
*-----------------------------------------------------------------------------------------*

"EVENTOS DE TELA

INITIALIZATION.

  btn_gera = 'GERA DADOS DE TEXTO'.

AT SELECTION-SCREEN.

  IF sy-ucomm = 'GERA'.
    "LOGICA DE GERAÇÃO DE TEXTE
    DO 5 TIMES.
      CLEAR ls_reserva.

      ls_reserva-cliente  = |cliente_{ sy-index }|.
      ls_reserva-carro    = 'FROTA_PREMIUM'.
      LS_reserva-DATA_res = sy-datum.
      ls_reserva-valor    = 100 * sy-index.
      INSERT zreservas_db_v3 FROM ls_reserva.
    ENDDO.
    COMMIT WORK.
    MESSAGE 'DADOS GERADOS! ' TYPE 'S'.
  ENDIF.
*---------------------------------------------------------------------------------------------------------*

  "PROCESSAMENTO COM A REGRA DE NEGOCIO (10%)

START-OF-SELECTION.

  " DEFINIÇÃO DA REGRA PROBABILISTICA DE EFEITO CASCATA DE (10%)
  TYPES: BEGIN OF ty_frota,
           modelo TYPE string,
           ano    TYPE i,
           valor  TYPE p DECIMALS 2,
         END OF ty_frota.
  DATA: lt_frota TYPE TABLE OF ty_frota,
        lv_found TYPE abap_bool VALUE abap_false.
*-------------------------------------------------------------------------------------------------------------------*
  "INICIO DA LOGICA DE DISTRIBUIÇÃO DE 20%
  DATA: lv_data_inicio TYPE d,
        lv_data_fim    TYPE d,
        lv_total_dias  TYPE i,
        lv_janela      TYPE i,
        lv_data_carro  TYPE d,
        lv_contador    TYPE i VALUE 0.
  lv_data_inicio = s_data-low.
  lv_data_fim    = s_data-high.
*-----------------------------------------------------------------------------------------------------------------*
  "VALIDAÇÃO: SE O INTERVALO FOR MAIOR QUE  5 DIAS, FORÇAMOS 5 DIAS PARA A LOGICA FUNCIONAR
  IF ( lv_data_fim - lv_data_inicio ) < 4.
    lv_data_fim = lv_data_inicio + 4.
  ENDIF.
*-------------------------------------------------------------------------------------------------------------------*
  " CALCULO DE INTERVALO TOTAL E DA FATIA DE 20%(JANELA)
  lv_total_dias = ( lv_data_fim - lv_data_inicio ) + 1.
  lv_janela     = lv_total_dias / 5.
*------------------------------------------------------------------------------------------------------------------*
  "GARANTINDO QUE A JANELA SEJA DE PELOMENOS 1 DIA
  IF lv_janela = 0.
    lv_janela = 1.
  ENDIF.
*------------------------------------------------------------------------------------------------------------------*
  "CALCULO DA REGRA DE PRECIFICAÇÃO

  DATA: lv_base TYPE p DECIMALS 2 VALUE '500.00'.


  lt_frota = VALUE #(
          ( modelo = 'lexus ES 300h'                 ano = 2026 valor = lv_base )
          ( modelo = 'audi Q3 sportback'             ano = 2025 valor = lv_base * '0.90')
          ( modelo = 'mercedez benz classe c w206'   ano = 2024 valor = lv_base * '0.81')
          ( modelo = 'bmw 320i (G20)'                ano = 2023 valor = lv_base * '0.729')
          ( modelo = 'volvo xc60 recharge'           ano = 2022 valor = lv_base * '0.6561')

      ).
*----------------------------------------------------------------------------------------------------------------*

  "CABEÇALHO DAS IMPRESSÕES FORMATADAS

  WRITE: / sy-vline, (20) 'NOME DO CLIENTE',
           sy-vline, (30) 'CARRO (MODELO/ANO)',
           sy-vline, (10) 'DATA',
           sy-Vline, (15) 'VALOR LOCAÇÃO', sy-vline.
*-----------------------------------------------------------------------------------------------------------------*

  "LOGICA DE IMPRESSÃO BASEADA NA FAIXA DE VALOR
  LOOP AT LT_frota INTO DATA(ls_car).

*------------------------------------------------------------------------------------------------------------------*

    "VERIFICA SE O PREÇO DO MODELOS ESTÃO DENTRO DAS LACUNAS P_V_MIN E P_V_MAX

    IF ls_car-valor >= p_v_min AND ls_car-valor <= p_v_max.
      lv_found = abap_true.

*-----------------------------------------------------------------------------------------------------------------------*
      "aplicar a regra dos 20% cada carro pula uma janela de dias
      lv_data_carro = lv_data_inicio + ( lv_contador * lv_janela ).
      lv_contador = lv_contador + 1.
*------------------------------------------------------------------------------------------------------------------------*
      "JUNÇÃO DAS INFORMAÇÕES CARRO E ANO
      DATA(lv_carro_info) = |{ ls_car-modelo }({ ls_car-ano })|.

*------------------------------------------------------------------------------------------------------------------*

      "IMPRESSÃO DAS LINHAS COM AS DATAS DEFINIDAS
      WRITE: /'-----------------------------------------------------------------------------------------------------'.
      WRITE: / sy-vline, (20) p_nome,
               sy-vline, (30) LV_carro_info,
               sy-vline, (10) lv_data_carro,
               sy-vline, (15) ls_car-valor CURRENCY 'BRL' , sy-vline.
      WRITE: /'-----------------------------------------------------------------------------------------------------'.

    ENDIF.

  ENDLOOP.


*----------------------------------------------------------------------------------------------------------------*

  "COMDICIONAL DE RESULTADO NULO

  IF LV_Found = abap_false.
    FORMAT COLOR COL_NEGATIVE.
    WRITE: /    sy-vline, (77) 'RESULTADO NULO: SE NENHUM CARRO NA FAIXA DE PREÇO OU ANO PREVISTO.', sy-vline.
    FORMAT COLOR OFF.
  ENDIF.

  WRITE: /'-----------------------------------------------------------'.


https://github.com/user-attachments/assets/7d87ab6b-e7bd-46b3-bed4-71108dd1d2a0


**Desenvolvido por:** RonaldoBS | [Seu LinkedIn](https://www.linkedin.com/in/ronaldo-silva-550151322)
