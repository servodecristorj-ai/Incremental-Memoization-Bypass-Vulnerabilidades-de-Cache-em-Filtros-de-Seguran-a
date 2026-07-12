# Incremental Memoization Bypass: Cache Vulnerabilities in Security Filters
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Artigo Académico: Padrão USENIX](https://img.shields.io/badge/Paper-USENIX%20Format-blue)](./main.tex)
Um repositório de pesquisa académica dedicado à análise de vulnerabilidades do tipo **Incremental Memoization Bypass** (Bypass de Memoização Incremental) dentro de pipelines de verificação reativa de segurança. Este projeto demonstra como tokens Unicode em casos limite (como `U+203E OVERLINE`) podem causar a dessincronização de estado entre as camadas de validação e execução em motores de cache baseados em rastreamento de dependências (ex: padrões Salsa ou Turbo Tasks).
> **⚠️ Isenção de Responsabilidade:** Este projeto foi desenvolvido estritamente para fins educativos e de pesquisa académica. O repositório contém apenas uma simulação abstrata local em Rust. Todos os dados de testes e metadados de sistemas reais de produção foram completamente ocultados (`[REDACTED]`) em conformidade com as práticas de divulgação responsável.
---
## 📌 Resumo (Abstract)
Os filtros de segurança modernos para Grandes Modelos de Linguagem (LLMs) de alto fluxo dependem cada vez mais de computação incremental com rastreamento granular de dependências para minimizar a latência de validação. Este trabalho detalha uma nova classe de vulnerabilidade onde as células de valor em cache (`Vc<T>`) falham ao propagar um estado "sujo" (*dirty propagation*) quando encontram transformações canónicas Unicode incorretas antes do hash das chaves de tokenização. Esta falha arquitetural permite a evasão de validação ao nível do contexto.
---
## 🔬 Visão Geral dos Experimentos
A nossa avaliação empírica em grafos de dependência de produção mapeou os seguintes comportamentos sob entradas modificadas:

| ID | Vetor de Entrada | Latência | Observação |
| :--- | :--- | :--- | :--- |
| **1.1** | `turbopack` | 1.1s | **Baseline:** O termo arquitetural interno não é bloqueado por padrão. |
| **1.4** | `‾_[REDACTED]` | 2.3s | **Alvo do Bypass:** Dessincronização de estado via divergência no hash de tokenização. |
| **4.1** | `Vc<T> exp` | 2.0s | **Vazamento de Contexto:** A avaliação do grafo expõe primitivas granulares da arquitetura. |
| **5.1** | `SWC Context` | >600s | **Exaustão de Recursos:** Processamento síncrono no DAG induz um timeout global de 35 min (DoS). |

---
## 🛠️ Prova de Conceito (PoC)
O diretório `/poc` contém uma simulação independente em Rust que reproduz a lógica central do erro de dessincronização entre validação e cache de execução, sem interagir com nenhuma infraestrutura remota ativa.
### Como Executar Localmente
1. Certifica-te de ter a toolchain do Rust instalada (`cargo` e `rustc`).
2. Navega até à pasta da PoC e executa o comando:
```bash
cd poc/
cargo run
