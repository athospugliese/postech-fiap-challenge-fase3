# Descrição da Infra criada para o Tech Challenge Fase 3

**Provedor de nuvem escolhido**: Microsoft Azure\
**Região padrão**: Sul do Brasil\
**Engine de banco de dados**: Azure SQL Database\
**Engine de processamento de dados**: Azure Databricks (Spark)\
Os níveis de serviço foram mantidos no mínimo para reduzir custos de processamento e armazenamento.\

## Arquitetura simplificada
![Fase 3](https://github.com/user-attachments/assets/95784122-df1b-4c5c-8e3e-e04a93ed1b6f)

### Serviços criados no Azure:

**Databricks:**
<img width="1275" alt="databricks_screenshot" src="https://github.com/user-attachments/assets/ae173a32-d4b4-4620-b209-fb96495b050c" />

**Azure SQL Database:**
<img width="1067" alt="azuresql_screenshot" src="https://github.com/user-attachments/assets/c455c5c0-72a1-4ac2-8239-89597c9a9759" />

## Processo de carga de dados
Os dados foram baixados do site do PNAD em formato CSV (comma separated value) e carregados para uma tabela dentro da base SQL Server:
A tabela de armazenamento dos dados crus (RAW) foi criada com o seguinte código:
```
--drop table if exists covid_data_fact
GO
CREATE TABLE covid_data_fact (
    Ano SMALLINT NOT NULL,
    UF TINYINT NOT NULL,
    CAPITAL TINYINT NULL,
    RM_RIDE TINYINT NULL,
    Numero_selecao_domicilio_V1008 TINYINT NOT NULL,
    Semana_no_mes_V1012 TINYINT NOT NULL,
    Mes_pesquisa_V1013 TINYINT NOT NULL,
    Num_entrevista_domicilio_V1016 TINYINT NOT NULL,
    Estrato VARCHAR(50) NOT NULL,
    UPA BIGINT NOT NULL,
    Situacao_domicilio_V1022 TINYINT NOT NULL CONSTRAINT Chk_Situacao_domicilio CHECK(Situacao_domicilio_V1022 in (1,2)), --1 = Urbana, 2 = Rural
    Tipo_Area_V1023 TINYINT NOT NULL,
    Projecao_Populacao_V1030 INT NOT NULL,
    Peso_Domicilio_sem_estrat_V1031 NUMERIC(18,2) NOT NULL,
    Peso_Domicilio_com_estrat_V1032 NUMERIC(18,2) NOT NULL,
    posest SMALLINT NOT NULL,
    nro_ordem_A001 TINYINT NOT NULL,
    Condicao_no_domicilio_A001A TINYINT NOT NULL,
    Dia_nascimento_A001B1 TINYINT NOT NULL,
    Mes_nascimento_A001B2 TINYINT NOT NULL,
    Ano_nascimento_A001B3 SMALLINT NOT NULL,
    Idade_A002 TINYINT NOT NULL,
    Sexo_A003 TINYINT NOT NULL CONSTRAINT Chk_Sexo CHECK(Sexo_A003 in (1,2)), --1 = Homem, 2 = Mulher
    Cor_raca_A004 TINYINT NOT NULL,
    Escolaridade_A005 TINYINT NOT NULL,
    Febre_B0011 TINYINT NULL,
    Tosse_B0012 TINYINT NULL,
    Dor_Garganta_B0013 TINYINT NULL,
    Dificuldade_respiracao_B0014 TINYINT NULL,
    Dor_cabeca_B0015 TINYINT NULL,
    Dor_Peito_B0016 TINYINT NULL,
    Nausea_B0017 TINYINT NULL,
    Nariz_Entupido_escorrendo_B0018 TINYINT NULL,
    Fadiga_B0019 TINYINT NULL,
    Dor_olhos_B00110 TINYINT NULL,
    Perda_cheiro_B00111 TINYINT NULL,
    Dor_muscular_B00112 TINYINT NULL,
    Foi_estabelecimento_saude_B002 TINYINT NULL,
    Ficou_em_casa_B0031 TINYINT NULL,
    Ligou_profiss_saude_B0032 TINYINT NULL,
    Auto_medicacao_B0033 TINYINT NULL,
    Medicacao_profiss_saude_B0034 TINYINT NULL,
    Recebeu_visita_profiss_saude_sus_B0035 TINYINT NULL,
    Recebeu_visita_profiss_saude_particular_B0036 TINYINT NULL,
    outra_conduta_B0037 TINYINT NULL,
    Local_buscado_posto_B0041 TINYINT NULL,
    Local_buscado_ps_sus_B0042 TINYINT NULL,
    Local_buscado_hosp_sus_B0043 TINYINT NULL,
    Local_buscado_ambulatorio_consult_part_B0044 TINYINT NULL,
    Local_buscado_ps_part_B0045 TINYINT NULL,
    Local_buscado_hosp_part_B0046 TINYINT NULL,
    Local_buscado_hosp_internado_B005 TINYINT NULL,
    sedado_entubado_quando_internado_B006 TINYINT NULL,
    plano_saude_B007 TINYINT NULL,
    fez_bico_C001 TINYINT NULL,
    afastado_C002 TINYINT NULL,
    motivo_afastamento_C003 TINYINT NULL,
    continuou_sendo_remunerado_C004 TINYINT NULL,
    tempo_afastado_C005 TINYINT NULL,
    tempo_afastado_menos_1_ano_C0051 TINYINT NULL,
    tempo_afastado_1_ano_a_2_C0052 TINYINT NULL,
    tempo_afastado_2_anos_C0053 TINYINT NULL,
    tem_mais_de_um_trabalho_C006 TINYINT NULL Constraint Chk_multiplos_trabalhos CHECK(tem_mais_de_um_trabalho_C006 in (1,2)), --1 = Sim, 2 = Nao
    cargo_C007 TINYINT NULL,
    area_cargo_C007A TINYINT NULL,
    carteira_trab_func_publico_C007B TINYINT NULL,
    tipo_trab_C007C TINYINT NULL,
    atividade_trab_C007D TINYINT NULL,
    quantos_empregados_C007E TINYINT NULL,
    quantos_empregados_C007E1 TINYINT NULL,
    quantos_empregados_C007E2 TINYINT NULL,
    horas_semana_normal_C008 TINYINT NULL,
    horas_semana_efetivo_C009 TINYINT NULL,
    quantos_recebia_C010 TINYINT NULL,
    recebia_em_dinheiro_C0101 TINYINT NULL,
    faixa_rendimento_dinheiro_C01011 TINYINT NULL,
    valor_rendimento_dinheiro_C01012 BIGINT NULL,
    recebia_em_produtos_C0102 TINYINT NULL Constraint Chk_recebia_em_produtos CHECK(recebia_em_produtos_C0102 = 2), --2 = em produtos
    faixa_rendimento_mercadorias_C01021 TINYINT NULL,
    valor_rendimento_mercadorias_C01022 INT NULL,
    recebia_somente_beneficios_C0103 TINYINT NULL Constraint Chk_recebia_em_beneficios CHECK(recebia_somente_beneficios_C0103 = 3), --3 = em beneficios
    nao_remunerado_C0104 TINYINT NULL Constraint Chk_nao_remunerado CHECK(nao_remunerado_C0104 = 4), --4 = nao remunerado
    quanto_recebia_respondido_C011A BIT NULL, 
    recebia_em_dinheiro_efetivamente_C011A1 BIT NULL,
    faixa_rendimento_dinheiro_C011A11 TINYINT NULL,
    valor_rendimento_dinheiro_C011A12 INT NULL,
    recebia_efetivamente_em_produtos_C011A2 TINYINT NULL Constraint Chk_recebia_efet_em_produtos CHECK(recebia_efetivamente_em_produtos_C011A2 = 2), --2 = em produtos
    faixa_rendimento_efet_produtos_C011A21 TINYINT NULL,
    valor_efet_produtos_C011A22 INT NULL,
    mesmo_trabalho_de_sempre_C012 TINYINT NULL Constraint Chk_mesmo_trabalho_de_sempre CHECK(mesmo_trabalho_de_sempre_C012 in (1,2)), --1 = sim, 2 = não
    trabalho_remoto_C013 TINYINT NULL Constraint Chk_trabalho_remoto CHECK(trabalho_remoto_C013 in (1,2)), --1 = sim, 2 = não
    inss_C014 TINYINT NULL Constraint Chk_inss CHECK(inss_C014 in (1,2)), --1 = sim, 2 = não
    tomou_providencia_conseguir_trabalho_C015 TINYINT NULL Constraint Chk_tomou_providencia_conseguir_trabalho CHECK(tomou_providencia_conseguir_trabalho_C015 in (1,2)), --1 = sim, 2 = não
    motivo_p_nao_procurar_trabalho_C016 TINYINT NULL,
    gostaria_de_ter_trabalhado_C017A TINYINT NULL Constraint Chk_gostaria_de_ter_trabalhado CHECK(gostaria_de_ter_trabalhado_C017A in (1,2)), --1 = sim, 2 = não
    rendimento_recebido_aposentadoria_pensao_todos_moradores_D0011 TINYINT NULL Constraint Chk_rendimento_recebido_aposentadoria_pensao_todos_moradores CHECK(rendimento_recebido_aposentadoria_pensao_todos_moradores_D0011 in (1,2)), --1 = sim, 2 = não
    somatorio_valores_recebidos_aposentadoria_D0013 INT NULL,
    rendimento_pensao_doacao_de_pessoa_nao_morava_D0021 TINYINT NULL Constraint Chk_rendimento_pensao_doacao_de_pessoa_nao_morava CHECK(rendimento_pensao_doacao_de_pessoa_nao_morava_D0021 in (1,2)), --1 = sim, 2 = não 
    somatorio_valores_recebidos_pensao_nao_morava_D0023 INT NULL,
    rendimento_bolsa_familia_D0031 TINYINT NULL Constraint Chk_rendimento_bolsa_familia CHECK(rendimento_bolsa_familia_D0031 in (1,2)), --1 = sim, 2 = não 
    somatorio_valores_bolsa_familia_D0033 INT NULL,
    rendimento_beneficio_assistencial_loas_D0041 TINYINT NULL Constraint Chk_rendimento_beneficio_assistencial_loas CHECK(rendimento_beneficio_assistencial_loas_D0041 in (1,2)), --1 = sim, 2 = não 
    somatorio_valores_loas_D0043 INT NULL,
    auxilio_emergencial_covid_D0051 TINYINT NULL Constraint Chk_auxilio_emergencial_covid CHECK(auxilio_emergencial_covid_D0051 in (1,2)), --1 = sim, 2 = não 
    somatorio_valores_auxilios_D0053 INT NULL,
    seguro_desemprego_D0061 TINYINT NULL Constraint Chk_seguro_desemprego CHECK(seguro_desemprego_D0061 in (1,2)), --1 = sim, 2 = não 
    somatorio_seguro_desemprego_D0063 INT NULL,
    outros_rendimentos_D0071 TINYINT NULL Constraint Chk_outros_rendimentos CHECK(outros_rendimentos_D0071 in (1,2)), --1 = sim, 2 = não 
    somatorio_outros_rendimentos_D0073 INT NULL,
    domicilio_F001 TINYINT NULL,
    valor_aluguel_mensal_F0021 INT NULL,
    faixa_aluguel_pago_F0022 TINYINT NULL,
    quem_respondeu_questionario_F0061 TINYINT NULL Constraint Chk_quem_respondeu_questionario CHECK(quem_respondeu_questionario_F0061 in (1,2,9)), --1 = morador, 2 = não morador, 9 = ignorado
    nro_ordem_morador_que_respondeu_questionario_F006 TINYINT NULL 
);

```
Todas as colunas disponíveis nos arquivos csv foram adicionadas - durante a fase de preprocessamento somente as colunas relevantes são selecionadas (ver notebook 01).






