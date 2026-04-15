# Amoessaloucura-
conteudoabap
# 🚗 Sistema de Gestão de Reservas - Frota Premium (ABAP OO)

Este projeto foi desenvolvido como parte do meu portfólio técnico para a posição de **Desenvolvedor ABAP Júnior**. O sistema simula um ambiente real de locação de veículos, aplicando regras de negócio complexas e utilizando tecnologias modernas do ecossistema SAP S/4HANA.

## 🚀 Funcionalidades Principais

- **Cálculo Dinâmico de Depreciação:** Aplicação de efeito cascata (10%) sobre o valor da locação baseada no ano do modelo do veículo.
- **Algoritmo de Escalonamento (Lógica dos 20%):** Distribuição automática de uma frota de 5 veículos dentro de um intervalo de datas variável, garantindo que cada carro cubra uma "janela" proporcional do período selecionado.
- **Relatório ALV Moderno:** Utilização da classe `CL_SALV_TABLE` para exibição de dados com recursos nativos de exportação (Excel), filtros e ordenação.
- **Persistência de Dados:** Inserção e gerenciamento de registros em tabelas transparentes customizadas (Z).

## 🛠️ Tecnologias e Conceitos Utilizados

- **Linguagem:** ABAP (Versão 7.40+)
- **Programação Orientada a Objetos (OO):** Separação de responsabilidades entre camada de banco de dados e lógica de negócio.
- **Novas Sintaxes:** Inline Declarations, String Templates e Value Operators.
- **Dicionário de Dados (SE11):** Tabelas transparentes, domínios e elementos de dados.
- **Aritmética de Datas:** Manipulação avançada de intervalos de calendário.

## 📊 O Algoritmo de Distribuição (Destaque Técnico)

Um dos maiores desafios deste projeto foi criar um escalonamento preciso. O sistema calcula a diferença total de dias entre `S_DATA-LOW` e `S_DATA-HIGH` e divide o intervalo por 5. 
Cada veículo recebe uma data de reserva correspondente a:
`Data Inicial + (Índice do Veículo * Janela de Dias)`

Isso garante que, independentemente do período (5 dias ou 50 dias), a frota esteja distribuída uniforme.


https://github.com/user-attachments/assets/7d87ab6b-e7bd-46b3-bed4-71108dd1d2a0


**Desenvolvido por:** RonaldoBS | [Seu LinkedIn](https://www.linkedin.com/in/ronaldo-silva-550151322)
