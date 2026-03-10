# O impacto da autoimagem no consumo de álcool entre adolescentes brasileiros

Este repositório contém os materiais e códigos do trabalho de conclusão da disciplina de Econometria I, desenvolvido por **Carlos Henrique Costa** e **Julia Xiao**, sob orientação da **Fundação Getulio Vargas - Escola de Economia de São Paulo (FGV EESP)**.

---

## 📋 Sobre o Projeto

O estudo investiga a relação entre **autoimagem corporal** e **consumo de álcool entre adolescentes brasileiros**, utilizando os microdados da **Pesquisa Nacional de Saúde do Escolar (PeNSE) 2019**.

### Objetivo

Avaliar se percepções negativas sobre o corpo — como sentir-se gordo(a), magro(a) ou insatisfeito(a) com a aparência — estão associadas à maior intensidade de episódios de embriaguez na adolescência.

### Abordagem Metodológica

**Modelo econométrico:**  
Regressão linear múltipla estimada por **Mínimos Quadrados Ordinários (MQO)**

**Variável dependente:**  
Logaritmo do número de episódios de embriaguez ao longo da vida

**Variáveis de interesse:**  
Percepção corporal (muito magro, magro, gordo, muito gordo) e satisfação com o corpo

**Controles:**  
Sexo, idade, cor/raça, número de banheiros no domicílio (proxy socioeconômica) e apoio familiar

---

## 🔍 Principais Resultados

**Autoimagem extrema:**  
Adolescentes que se percebem como "muito magros" ou "muito gordos" apresentam maior propensão a episódios de embriaguez (efeitos estáveis mesmo após controles).

**Insatisfação corporal:**  
Estudantes insatisfeitos com o próprio corpo relatam sistematicamente mais episódios de embriaguez (coeficiente negativo e significante para a variável de satisfação).

**Gênero:**  
Meninas apresentam menor probabilidade de embriaguez em comparação aos meninos (coeficientes entre **-3,3% e -4,9%**).

**Idade:**  
Relação monotônica positiva — adolescentes mais velhos (16–17 e >17 anos) apresentam coeficientes expressivos (**0,24 a 0,38**), indicando forte associação com o consumo.

**Fatores socioeconômicos:**

- Maior número de banheiros (proxy de renda) → maior consumo  
- Apoio familiar → efeito protetivo significativo (**-0,049**)

**Raça/cor:**  
Estudantes pretos apresentam coeficientes positivos (baixa magnitude); pardos, coeficientes negativos.

---

## 📊 Estatísticas Descritivas (Amostra Final)

| Variável | Média | Desvio Padrão | Observações |
|---|---|---|---|
| ln(bêbado) | 0,46 | 0,55 | 81.309 |
| Muito magro | 0,07 | 0,26 | 81.309 |
| Magro | 0,22 | 0,42 | 81.309 |
| Gordo | 0,21 | 0,40 | 81.309 |
| Muito gordo | 0,04 | 0,19 | 81.309 |
| Satisfeito com o corpo | 0,70 | 0,46 | 81.309 |
| Idade (categoria) | 2,54 | 0,63 | 81.309 |
| Banheiros | 2,78 | 0,94 | 81.309 |
| Apoio familiar | 0,82 | 0,39 | 81.309 |

---

## 📚 Referências Principais

**BECKER, G. (1964).** *Human Capital*

**MALTA, D. C. et al. (2021).** *Trends in unhealthy behaviors among adolescents in Brazil*

**O'DONOGHUE, T.; RABIN, M. (2001).** *Addiction and Self-Control*

**STEINBERG, L. (2017).** *Adolescence (11ª ed.)*

---

## 📌 Notas Técnicas

**Erros-padrão robustos:**  
Utilizado **HC3** para lidar com heterocedasticidade detectada

**Transformação logarítmica:**  
Aplicada à variável dependente para reduzir assimetria

**Observações:**  
Amostra final com **81.309 estudantes** (após tratamento de missings)

**Limitações:**  
Natureza transversal dos dados impede inferências causais; ausência de medida específica de **binge drinking**

---

## 👥 Autores

**Carlos Henrique Costa**  
**Julia Xiao**

**Instituição:** Fundação Getulio Vargas - Escola de Economia de São Paulo (FGV EESP)  
**Ano:** 2025  
**Disciplina:** Econometria I
