# Gatekeeper de GCM com IA e n8n

Este repositório contém um projeto desenvolvido para a disciplina de **Gestão de Configuração e Mudanças (GCM)**. O objetivo é implementar um agente automatizado de controle de qualidade (**Gatekeeper**) capaz de interceptar e auditar alterações de código (commits) utilizando **n8n** e **Inteligência Artificial (LLM)**.

---

## Arquitetura e Tecnologias Utilizadas

O ecossistema do projeto é composto por:
* **Docker & Docker Compose:** Utilizado para hospedar e rodar o ambiente do **n8n** localmente.
* **n8n:** Plataforma de automação de fluxos baseada em nós para orquestrar a lógica do agente.
* **GitHub API:** Utilizada para extrair dinamicamente o *diff* puro das alterações de código feitas nos commits.
* **LLM (OpenAI / Google Gemini):** Motor de inteligência artificial responsável por ler o *diff* e emitir um veredito técnico com base em regras estritas de governança.

---

## Regra de Negócio Auditada

O agente audita exclusivamente a função de cálculo de descontos do sistema:
* **Função Alvo:** `calcularDesconto(preco, categoria)`
* **Política de Governança:** O limite máximo de desconto permitido aplicado diretamente no código é de **20% (0.20)**. 
* **Verdictos Possíveis:**
  * **APROVADO:** Quando o desconto é igual ou inferior a 20%.
  * **REPROVADO:** Quando o desconto ultrapassa 20%, apontando a violação da regra de negócio e exigindo aprovação gerencial externa.

---

## Fluxo de Funcionamento (Pipeline do Agente)

1. **Gatilho (Webhook / Simulação Local):** O fluxo é acionado recebendo metadados de um commit do GitHub (como o `owner`, `repo` e o `head_commit.id` / `sha`).
2. **Extração do Delta (HTTP Request):** O n8n faz uma requisição GET para a API oficial do GitHub (`https://api.github.com/repos/{owner}/{repo}/commits/{sha}`) utilizando o cabeçalho restrito `Accept: application/vnd.github.v3.diff` para capturar apenas as linhas alteradas (o *diff*).
3. **Análise Inteligente (LLM Chain):** O texto do *diff* é enviado para o modelo de linguagem configurado com um *System Prompt* restrito, garantindo uma auditoria semântica e matemática precisa da função de desconto.
4. **Saída Estruturada:** O agente retorna o veredito acompanhado de uma justificativa detalhada.

---

## Como Executar o Projeto Localmente

1. **Subir o ambiente n8n via Docker:**
   Na pasta onde tens o teu `docker-compose.yml`, executa:
   ```bash
   docker compose up -d

2. **Aceder ao n8n:**
    Abre o navegador em http://localhost:5678.

3. **Configurar o Fluxo:**
    *  Insere o SHA de um commit real do teu repositório que altere o ficheiro src/index.js.
    * Executa o passo ou fluxo para validar os cenários de aprovação e reprovação.