# ETAPA 1: CRIAR DICIONÁRIO DAS VARIÁVEIS DE USO

# ============================================================
# 1. LIMPAR AMBIENTE DE TRABALHO
# ============================================================
rm(list = ls())

# ============================================================
# 2. INSTALAR BIBLIOTECAS (apenas na primeira execução)
# ============================================================
install.packages("dplyr")
install.packages("tidyr")
install.packages("stargazer")

# ============================================================
# 3. CARREGAR BIBLIOTECAS
# ============================================================
library(dplyr)
library(tidyr)
library(stargazer)

# ============================================================
# 4. CARREGAR O DICIONÁRIO ORIGINAL DA PENSE 2019 (formato .rds)
# ============================================================
dic <- readRDS("C:/Users/carlo/Downloads/dicionario_pense2019.rds")

head(dic, 20)
# ============================================================
# 5. RENOMEAR COLUNAS PARA NOMES MAIS INTUITIVOS
# ============================================================
dic_limpo <- dic %>%
  rename(
    codigo_var       = `QUESTIONÁRIO DO ALUNO`,
    desc_var         = ...2,
    cod_categoria    = ...3,
    rotulo_categoria = ...4)

# ============================================================
# 6. PREENCHER PARA BAIXO CÓDIGO E DESCRIÇÃO DAS VARIÁVEIS
#    (evita perder rótulos que aparecem com código_var = NA)
# ============================================================
dic_limpo <- dic_limpo %>%
  fill(codigo_var, desc_var, .direction = "down")

# ============================================================
# 7. REMOVER CATEGORIAS INVÁLIDAS E CÓDIGOS ADMINISTRATIVOS
# ============================================================
dic_limpo <- dic_limpo %>%
  filter(
    !cod_categoria %in% c("9", "99", "98", "8", "-1", "-2"),
    cod_categoria != "Categorias",
    !is.na(cod_categoria))

# ============================================================
# 8. FILTRAR APENAS AS VARIÁVEIS QUE SERÃO UTILIZADAS NO MODELO
# ============================================================
vars_uso <- c(
  "B05007",   # vezes que ficou realmente bêbado
  "B11001",   # percepção do corpo
  "B11007",   # satisfação com o corpo
  "B01001a",  # sexo (observação: "a" minúsculo no dicionário)
  "B01003",   # idade
  "B01002",   # cor/raça
  "B01019a",  # número de banheiros (também com "a" no dicionário)
  "B07004"    # apoio dos pais
)

dic_uso <- dic_limpo %>%
  filter(codigo_var %in% vars_uso)

# ============================================================
# 9. EXPORTAR O DICIONÁRIO FINAL PARA EXCEL
# ============================================================
install.packages("openxlsx")
library(openxlsx)

wb <- createWorkbook()
addWorksheet(wb, "dic_uso")
writeData(wb, "dic_uso", dic_uso)

saveWorkbook(wb, "C:/Users/carlo/Downloads/dic_uso.rds", overwrite = TRUE)
saveWorkbook(wb, "C:/Users/carlo/Downloads/dic_uso.xls", overwrite = TRUE)

# ============================================================
# ETAPA 2 — CRIAR MICROBASE DAS VARIÁVEIS DE USO

# ============================================================
# 1. CARREGAR MICRODADOS DA PENSE 2019
# ============================================================
micro <- readRDS("C:/Users/carlo/Downloads/microdados_pense2019.rds")

# ============================================================
# 2. SELECIONAR APENAS AS VARIÁVEIS DE INTERESSE
# ============================================================
dados <- micro %>%
  select(
    bebado_raw     = B05007,    # vezes que ficou muito bêbado
    autoimg_raw    = B11001,    # percepção corporal
    satisf_raw     = B11007,    # satisfação corporal
    sexo_raw       = B01001A,   # sexo
    idade_raw      = B01003,    # idade
    cor_raw        = B01002,    # cor/raça
    banheiros_raw  = B01019A,   # número de banheiros
    apoio_raw      = B07004     # apoio dos pais
  )

# ============================================================
# 3. TRANSFORMAR TODAS AS VARIÁVEIS PARA NUMÉRICAS
# ============================================================
dados <- dados %>%
  mutate(across(everything(), ~ suppressWarnings(as.numeric(.))))

# ============================================================
# 4. TRATAR VALORES INVÁLIDOS (9, 99, 98, 8, -1, -2)
# ============================================================
invalidos <- c(8, 9, 98, 99, -1, -2)

dados <- dados %>%
  mutate(across(everything(),
                ~ ifelse(.x %in% invalidos, NA, .x)
  ))

# ============================================================
# 5. CRIAR VARIÁVEL DEPENDENTE ln(1 + bebado_raw)
# ============================================================
dados <- dados %>%
  mutate(
    ln_bebado = log(bebado_raw)
  )

# ============================================================
# 6. CRIAR DUMMIES DE AUTOIMAGEM (categoria base = 3 = "normal")
# ============================================================
dados <- dados %>%
  mutate(
    M = ifelse(autoimg_raw == 1, 1, 0),  # muito magro
    m = ifelse(autoimg_raw == 2, 1, 0),  # magro
    g = ifelse(autoimg_raw == 4, 1, 0),  # gordo
    G = ifelse(autoimg_raw == 5, 1, 0)   # muito gordo
    # base = 3 ("normal")
  )

# ============================================================
# 7. CRIAR DUMMY DE SATISFAÇÃO CORPORAL (1 = satisfeito/muito satisfeito/indiferente)
# ============================================================
dados <- dados %>%
  mutate(
    S = ifelse(satisf_raw %in% c(1, 2, 3), 1, 0)
  )

# ============================================================
# 8. CRIAR DUMMY DE APOIO DOS PAIS
# ============================================================
dados <- dados %>%
  mutate(
    A = ifelse(apoio_raw %in% c(2, 3, 4, 5), 1, 0)   # 1 = tem apoio
  )

# ============================================================
# 9. REMOVER OBSERVAÇÕES COM NA EM VARIÁVEIS IMPORTANTES
# ============================================================
dados_clean <- dados %>%
  drop_na(
    ln_bebado,
    autoimg_raw,
    S,
    sexo_raw,
    idade_raw,
    cor_raw,
    banheiros_raw,
    A
  )

# ============================================================
# 10. VISUALIZAR BASE FINAL
# ============================================================
head(dados_clean)
summary(dados_clean)

# ETAPA 3: TABELA DE VARIÁVEIS DESCRITIVAS
# ============================================================
# 1. VARIÁVEIS PARA TABELA DESCRITIVA
# ============================================================
vars_desc <- c(
  "ln_bebado",        # variável dependente
  "M", "m", "g", "G", # autoimagem (dummies)
  "S",                # satisfação corporal
  "sexo_raw",         # sexo
  "idade_raw",        # idade
  "cor_raw",          # cor/raça
  "banheiros_raw",    # nº de banheiros
  "A"                 # apoio dos pais
)

df_desc <- dados_clean[, vars_desc]

# ============================================================
# 2. GARANTIR QUE TODAS AS VARIÁVEIS SÃO NUMÉRICAS
# ============================================================
df_desc <- as.data.frame(lapply(df_desc, function(x) as.numeric(as.character(x))))

# ============================================================
# 3. TABELA DESCRITIVA COM STARGAZER
# ============================================================
stargazer(
  df_desc,
  type = "latex",
  title = "Tabela 1 - Estatísticas Descritivas das Variáveis do Estudo",
  summary.stat = c("n","mean","sd","min","p25","median","p75","max"),
  digits = 2
)


# ============================================================
# MATRIZ DE CORRELAÇÃO DAS VARIÁVEIS DO MODELO
# ============================================================
install.packages("corrplot")
library(corrplot)

# ------------------------------------------------------------
# 1. Definir as variáveis que vão entrar na correlação
# ------------------------------------------------------------
vars_cor <- c(
  "ln_bebado",         # variável dependente
  "M", "m", "g", "G",  # dummies de autoimagem
  "S",                 # dummy de satisfação corporal
  "sexo_raw",          # sexo
  "idade_raw",         # idade
  "cor_raw",           # cor/raça
  "banheiros_raw",     # número de banheiros
  "A"                  # apoio dos pais
)

# ------------------------------------------------------------
# 2. Calcular a matriz de correlação (usando apenas pares válidos)
# ------------------------------------------------------------
cor_matrix <- cor(dados_clean[, vars_cor], use = "pairwise.complete.obs")
cor_matrix_round <- round(cor_matrix, 2)

library(corrplot)

# ------------------------------------------------------------
# 3. GERAR PDF COM TESTE DE ERRO
# ------------------------------------------------------------
arquivo_pdf <- "matriz_correlacao.pdf"

# Fechar qualquer device aberta
while(dev.cur() > 1) dev.off()

# Abrir PDF
res <- try(pdf(arquivo_pdf, width = 10, height = 10))

if(inherits(res, "try-error")){
  stop("Erro ao abrir dispositivo PDF. Caminho inválido ou sem permissão.")
}

# Plot
corrplot(
  cor_matrix_round,
  method = "color",
  type = "lower",
  addCoef.col = "black",
  tl.col = "black",
  tl.srt = 45,
  number.cex = 0.7,
  title = "Matriz de Correlação das Variáveis do Modelo",
  mar = c(0,0,4,0)
)

# Fechar PDF
dev.off()

# ------------------------------------------------------------
# 4. CONFIRMAR SE FOI CRIADO
# ------------------------------------------------------------
if(file.exists(arquivo_pdf)){
  cat("PDF criado com sucesso em:\n", normalizePath(arquivo_pdf), "\n")
} else {
  stop("PDF NÃO FOI CRIADO. Verifique permissões e diretório.")
}

# ============================================================
# ETAPA 3 — Regressões

# ============================================================
# 2. INSTALAR PACOTES (se necessário)
# ============================================================
packages <- c(
  "tidyverse", "janitor", "haven", "lmtest", "sandwich", "car",
  "broom", "stargazer", "pagedown", "gt", "webshot2", "purrr"
)
install.packages(setdiff(packages, installed.packages()[, 1]))
install.packages("purrr")

# ============================================================
# 3. CARREGAR BIBLIOTECAS
# ============================================================
library(tidyverse)
library(janitor)
library(haven)
library(lmtest)
library(sandwich)
library(car)
library(broom)
library(stargazer)
library(pagedown)
library(gt)
library(webshot2)
library(ggplot2)
library(broom)
library(purrr)

# ============================================================
# 4. CARREGAR DADOS
# ============================================================
micro <- readRDS("C:/Users/carlo/Downloads/microdados_pense2019.rds")

# ============================================================
# 4. LIMPAR VARIÁVEIS DEPENDENTES (Y)
# ============================================================
pense <- micro %>%
  mutate(
    B05007  = as.numeric(B05007),
    B05004A = as.numeric(B05004A),
    B05005A = as.numeric(B05005A)
  ) %>%
  mutate(
    B05007  = na_if(B05007, -1),
    B05007  = na_if(B05007, -2),
    B05007  = na_if(B05007, 9),
    B05004A = na_if(B05004A, -1),
    B05004A = na_if(B05004A, 9),
    B05005A = na_if(B05005A, -1),
    B05005A = na_if(B05005A, 9)
  ) %>%
  rename(
    bebado     = B05007,
    dias_bebeu = B05004A
  )

# ============================================================
# 5. CRIAR VARIÁVEIS DE AUTOIMAGEM
# ============================================================
pense <- pense %>%
  rename(
    satisf_corpo = B11007,
    autoimagem   = B11001,
    atitude      = B11002
  )
pense <- pense %>%
  mutate(
    auto_muito_magro  = ifelse(autoimagem == 1, 1, 0),
    auto_magro        = ifelse(autoimagem == 2, 1, 0),
    auto_gordo        = ifelse(autoimagem == 4, 1, 0),
    auto_muito_gordo  = ifelse(autoimagem == 5, 1, 0),
    auto_sem_resposta = ifelse(autoimagem == 9, 1, 0),
    satisf_corpo = case_when(
      satisf_corpo %in% c(1, 2) ~ 1,    # Satisfeito/Muito satisfeito
      satisf_corpo %in% c(4, 5) ~ 0,    # Insatisfeito/Muito insatisfeito
      TRUE ~ NA_real_                    # Indiferente (3) e outros viram NA
    )
  )

# ============================================================
# 6. RENOMEAR VARIÁVEIS DE CONTROLE
# ============================================================
pense <- pense %>%
  rename(
    sexo             = B01001A,
    idade            = B01003,
    cor              = B01002,
    serie            = B01021A,
    n_pessoas_casa   = B01010A,
    celular          = B01014,
    computador       = B01015B,
    internet         = B01016,
    carro            = B01017,
    moto             = B01018A,
    banheiros        = B01019A,
    empregada        = B01020A,
    escolaridade_mae = B01008B,
    mora_mae         = B01006,
    mora_pai         = B01007,
    supervisao_pais  = B07002,
    apoio_pais       = B07004,
    merenda_esc      = B02021A,
    consome_merenda  = B02020B,
    compra_cantina   = B02041,
    compra_ambulante = B02042
  ) %>%
  
  # Converter para numérico antes do na_if()
  mutate(
    sexo      = as.numeric(sexo),
    banheiros = as.numeric(banheiros)
  ) %>%
  
  # Criar dummy e substituir 9 por NA
  mutate(
    com_apoio = ifelse(apoio_pais %in% c(3,4), 1, 0),
    sexo      = na_if(sexo, 9),
    banheiros = na_if(banheiros, 9)
  )
# ============================================================
# 7. SELECIONAR VARIÁVEIS DE CONTROLE (MODIFICADO: serie → banheiros)
# ============================================================
controles1 <- c("sexo")
controles2 <- c("sexo", "idade")
controles3 <- c("sexo", "idade", "cor")
controles4 <- c("sexo", "idade", "cor", "banheiros")  
controles5 <- c("sexo", "idade", "cor", "banheiros", "com_apoio")

# Criar logs
pense <- pense %>%
  mutate(
    ln_bebado     = log(bebado),
    ln_dias_bebeu = log(dias_bebeu + 1)
  )

# ============================================================
# 8. FÓRMULAS AUTOMÁTICAS (ATUALIZADAS)
# ============================================================
formula1 <- as.formula("ln_bebado ~ auto_muito_magro + auto_magro + auto_gordo + auto_muito_gordo + satisf_corpo")

formula2 <- as.formula(
  paste("ln_bebado ~ auto_muito_magro + auto_magro + auto_gordo + auto_muito_gordo + satisf_corpo +", paste(controles1, collapse = " + "))
)

formula3 <- as.formula(
  paste("ln_bebado ~ auto_muito_magro + auto_magro + auto_gordo + auto_muito_gordo + satisf_corpo +", paste(controles2, collapse = " + "))
)

formula4 <- as.formula(
  paste("ln_bebado ~ auto_muito_magro + auto_magro + auto_gordo + auto_muito_gordo + satisf_corpo +", paste(controles3, collapse = " + "))
)

formula5 <- as.formula(
  paste("ln_bebado ~ auto_muito_magro + auto_magro + auto_gordo + auto_muito_gordo + satisf_corpo +", paste(controles4, collapse = " + "))
)

formula6 <- as.formula(
  paste("ln_bebado ~ auto_muito_magro + auto_magro + auto_gordo + auto_muito_gordo + satisf_corpo +", paste(controles5, collapse = " + "))
)

# ============================================================
# 9. RODAR OS 7 MODELOS
# ============================================================
modelos <- list(
  modelo1 = lm(formula1, data = pense),
  modelo2 = lm(formula2, data = pense),
  modelo3 = lm(formula3, data = pense),
  modelo4 = lm(formula4, data = pense),
  modelo5 = lm(formula5, data = pense),
  modelo6 = lm(formula6, data = pense)
)

# ============================================================
# 10. TESTES ECONOMÉTRICOS
# ============================================================
bp_tests    <- lapply(modelos, bptest)
reset_tests <- lapply(modelos, function(m) resettest(m, power=2:3))

vifs <- lapply(modelos, function(m){
  if(length(coef(m)) > 2) vif(m) else NA
})

influ_points <- lapply(modelos, function(m) which(cooks.distance(m) > 4/length(m$fitted.values)))

coeftests_HC3 <- lapply(modelos, function(m) coeftest(m, vcov=vcovHC(m, type="HC3")))
intervalos     <- lapply(modelos, confint)

hip_auto <- lapply(modelos, function(m){
  vars <- matchCoefs(m, pattern="^auto_")
  linearHypothesis(m, vars, vcov=vcovHC(m, type="HC3"))
})

bp_tests
vifs
coeftests_HC3
intervalos
hip_auto
influ_points

# ============================================================
# 11. TABELA FINAL
# ============================================================
robust_ses <- lapply(modelos, function(m) sqrt(diag(vcovHC(m, type="HC3"))))

stargazer(
  modelos,
  type = "html",
  out = "tabela_econometrica_pense2019_final.html",
  title = "6 Modelos — PeNSE 2019 (Erros-Padrão Robustos HC3)",
  dep.var.labels = c("ln(Bêbado)"),
  column.labels = paste("Modelo", 1:7),
  digits = 3,
  se = robust_ses,
  omit.stat = c("ll", "aic", "bic", "f"),
  report = "vc*p",
  star.cutoffs = c(0.10, 0.05, 0.01),
  notes = c("Erros robustos HC3.", "*, **, *** = 10%, 5%, 1%.")
)

webshot("tabela_econometrica_pense2019_final.html",
        file = "tabela_econometrica_pense2019_final.png",
        zoom = 2, vwidth = 1200)

chrome_print("tabela_econometrica_pense2019_final.html",
             output = "tabela_econometrica_pense2019_final.pdf")

stargazer(
  modelos,
  type = "latex",
  out = "tabela_regressoes.tex",
  title = "7 Modelos — PeNSE 2019",
  dep.var.labels = c("ln(Bêbado + 1)"),
  column.labels = paste("Modelo", 1:7),
  digits = 3,
  se = robust_ses,
  omit.stat = c("ll", "aic", "bic", "f"),
  report = "vc*p",
  star.cutoffs = c(0.10, 0.05, 0.01),
  notes = c("Erros robustos HC3.", "*, **, *** = 10%, 5%, 1%.")
)

# ============================================================
# 13. GRÁFICOS
# ============================================================
# Função para preparar coeficientes
prep_coef <- function(modelo, nome_modelo) {
  tidy(modelo, conf.int = TRUE) %>%
    filter(term != "(Intercept)") %>%
    mutate(modelo = nome_modelo)
}

# Preparar df de coeficientes para todos os modelos
nomes_modelos <- paste("Modelo", 1:6)
df_coefs <- imap_dfr(modelos, ~ prep_coef(.x, nomes_modelos[.y]))

# Gráfico de coeficientes com IC 95%
ggplot(df_coefs, aes(x = estimate, y = term, color = modelo)) +
  geom_point() +
  geom_errorbarh(aes(xmin = conf.low, xmax = conf.high), height = 0.2) +
  facet_wrap(~ modelo, scales = "free_y") +
  theme_minimal() +
  labs(
    title = "Coeficientes estimados (IC 95%)",
    x = "Estimativa",
    y = "Variáveis"
  )

coef_plot <- ggplot(df_coefs, aes(x = estimate, y = term, color = modelo)) +
  geom_point() +
  geom_errorbarh(aes(xmin = conf.low, xmax = conf.high), height = 0.2) +
  facet_wrap(~ modelo, scales = "free_y") +
  theme_minimal() +
  labs(
    title = "Coeficientes estimados (IC 95%)",
    x = "Estimativa",
    y = "Variáveis"
  )

ggsave("coeficientes_modelos.pdf", plot = coef_plot, width = 12, height = 8)

# Histograma dos resíduos
par(mfrow = c(2, 3))
imap(modelos, ~ hist(residuals(.x), main = paste("Resíduos -", nomes_modelos[.y]), xlab = ""))
par(mfrow = c(1, 1))

# Resíduos vs Ajustado (apenas primeiros 3 modelos para visualização)
par(mfrow = c(2, 3))
imap(modelos[1:6], ~ plot(.x, which = 1, main = nomes_modelos[.y]))
par(mfrow = c(1, 1))

# QQ-Plot dos resíduos
par(mfrow = c(2, 3))
imap(modelos, ~ plot(.x, which = 2, main = nomes_modelos[.y]))
par(mfrow = c(1, 1))


# ============================================================
# 13. GRÁFICOS - versão para LaTeX (PDF)
# ============================================================

library(tidyverse)
library(broom)
library(purrr)

# -------------------------
# Função para preparar coeficientes
# -------------------------
prep_coef <- function(modelo, nome_modelo) {
  tidy(modelo, conf.int = TRUE) %>%
    filter(term != "(Intercept)") %>%
    mutate(modelo = nome_modelo)
}

# -------------------------
# Preparar df de coeficientes para todos os modelos
# -------------------------
nomes_modelos <- paste("Modelo", 1:6)
df_coefs <- imap_dfr(modelos, ~ prep_coef(.x, nomes_modelos[.y]))

# -------------------------
# Gráfico de coeficientes com IC 95%
# -------------------------
coef_plot <- ggplot(df_coefs, aes(x = estimate, y = term, color = modelo)) +
  geom_point() +
  geom_errorbarh(aes(xmin = conf.low, xmax = conf.high), height = 0.2) +
  facet_wrap(~ modelo, scales = "free_y") +
  theme_minimal() +
  labs(
    title = "Coeficientes estimados (IC 95%)",
    x = "Estimativa",
    y = "Variáveis"
  )

ggsave("coeficientes_modelos.pdf", plot = coef_plot, width = 12, height = 8)

# ============================================================
# HISTOGRAMAS DOS RESÍDUOS
# ============================================================

# ---- 1) Histogramas em um único PDF (várias páginas) ----
pdf("hist_residuos.pdf", width = 7, height = 5)
imap(modelos, ~ {
  df <- tibble(res = residuals(.x))
  p <- ggplot(df, aes(x = res)) +
    geom_histogram(bins = 30, fill = "gray70", color = "black") +
    theme_minimal() +
    labs(title = paste("Histograma dos Resíduos -", nomes_modelos[.y]),
         x = "Resíduo", y = "Frequência")
  print(p)
})
dev.off()

# ---- 2) (opcional) PDF por modelo ----
imap(modelos, ~ {
  df <- tibble(res = residuals(.x))
  p <- ggplot(df, aes(x = res)) +
    geom_histogram(bins = 30, fill = "gray70", color = "black") +
    theme_minimal() +
    labs(title = paste("Histograma dos Resíduos -", nomes_modelos[.y]),
         x = "Resíduo", y = "Frequência")
  
  ggsave(paste0("hist_residuos_", nomes_modelos[.y], ".pdf"),
         plot = p, width = 7, height = 5)
})

# ============================================================
# RESÍDUOS vs AJUSTADO
# ============================================================

imap(modelos, ~ {
  df <- tibble(
    ajuste = fitted(.x),
    res = residuals(.x)
  )
  
  p <- ggplot(df, aes(x = ajuste, y = res)) +
    geom_point(alpha = 0.5) +
    geom_hline(yintercept = 0, linetype = "dashed") +
    theme_minimal() +
    labs(title = paste("Resíduos vs Ajustado -", nomes_modelos[.y]),
         x = "Valores Ajustados",
         y = "Resíduos")
  
  ggsave(paste0("residuos_vs_ajustado_", nomes_modelos[.y], ".pdf"),
         plot = p, width = 7, height = 5)
})

# ============================================================
# QQ-PLOTS
# ============================================================

imap(modelos, ~ {
  df <- tibble(res = residuals(.x))
  
  p <- ggplot(df, aes(sample = res)) +
    stat_qq() +
    stat_qq_line() +
    theme_minimal() +
    labs(title = paste("QQ-Plot dos Resíduos -", nomes_modelos[.y]),
         x = "Quantis Teóricos",
         y = "Quantis Amostrais")
  
  ggsave(paste0("qqplot_", nomes_modelos[.y], ".pdf"),
         plot = p, width = 7, height = 5)
})

# ============================================================
# RESÍDUOS vs AJUSTADOS – Modelo Final (modelo6)
# ============================================================
modelo6 = lm(formula6, data = pense)

df_resid <- tibble(
  ajuste = fitted(modelo6),
  res = residuals(modelo6)
)

plot_resid <- ggplot(df_resid, aes(x = ajuste, y = res)) +
  geom_point(alpha = 0.6) +
  geom_hline(yintercept = 0, linetype = "dashed") +
  theme_minimal() +
  labs(
    title = "Resíduos vs Ajustados – Modelo Final",
    x = "Valores Ajustados",
    y = "Resíduos"
  )

ggsave("residuos_vs_ajustado_modelo_final.pdf",
       plot = plot_resid,
       width = 7, height = 5)

# ============================================================
# QQ-PLOT – Modelo Final (modelo6)
# ============================================================
modelo6 = lm(formula6, data = pense)

df_resid2 <- tibble(res = residuals(modelo6))

plot_qq <- ggplot(df_resid2, aes(sample = res)) +
  stat_qq() +
  stat_qq_line() +
  theme_minimal() +
  labs(
    title = "QQ-Plot dos Resíduos – Modelo Final",
    x = "Quantis Teóricos",
    y = "Quantis Empíricos"
  )

ggsave("qqplot_modelo_final.pdf",
       plot = plot_qq,
       width = 7, height = 5)

