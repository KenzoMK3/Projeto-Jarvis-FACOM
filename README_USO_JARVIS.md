# README de Uso — JARVIS

Este README explica como instalar, configurar e executar o **JARVIS**, um assistente acadêmico em Python que integra:

- consulta de agenda acadêmica;
- gerenciamento de tarefas;
- busca em materiais de estudo com RAG;
- integração com um modelo Gemma via API compatível com OpenAI;
- registro de chamadas de ferramentas em logs.

---

## 1. Estrutura esperada do projeto

A estrutura principal do projeto deve ficar parecida com esta:

```text
JARVIS/
├── main.py
├── config.py
├── requirements.txt
├── data/
│   └── seus_materiais.pdf / .txt / .docx / .md
├── database/
│   ├── db_manager.py
│   ├── jarvis.db
│   └── rag_index.pkl
├── llm/
│   └── gemma_client.py
├── logs/
│   ├── tool_logger.py
│   └── tool_calls.jsonl
├── rag/
│   ├── chunker.py
│   ├── document_loader.py
│   ├── embeddings.py
│   └── retriever.py
└── tools/
    ├── agenda.py
    ├── rag_search.py
    ├── tasks.py
    └── tool_registry.py
```

As pastas `data`, `logs` e `database` são usadas automaticamente pelo sistema. A pasta `data` guarda os materiais de estudo; `database` guarda o banco SQLite e o índice RAG; `logs` guarda registros das chamadas de ferramentas.

---

## 2. Requisitos

Antes de executar, você precisa ter instalado:

- Python 3.10 ou superior;
- `pip`;
- acesso ao terminal/PowerShell;
- token da API Gemma fornecido pelo professor;
- conexão com a internet na primeira execução, pois o modelo de embeddings pode ser baixado automaticamente.

Dependências principais:

```text
openai
requests
sentence-transformers
numpy
PyPDF2
python-docx
```

---

## 3. Instalação no Windows com PowerShell

Entre na pasta do projeto:

```powershell
cd C:\Users\kimur\Downloads\JARVIS
```

Crie um ambiente virtual:

```powershell
python -m venv .venv
```

Ative o ambiente virtual:

```powershell
.\.venv\Scripts\Activate.ps1
```

Instale as dependências:

```powershell
pip install -r requirements.txt
```

Caso o PowerShell bloqueie a ativação do ambiente virtual, execute:

```powershell
Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass
.\.venv\Scripts\Activate.ps1
```

---

## 4. Configuração da chave da API

O projeto **não deve ter o token escrito diretamente no código**. Configure a variável de ambiente `GEMMA_API_TOKEN` antes de executar.

No PowerShell, para a sessão atual:

```powershell
$env:GEMMA_API_TOKEN="SEU_TOKEN_AQUI"
```

Teste se a biblioteca OpenAI está instalada:

```powershell
python -c "from openai import OpenAI; print('OpenAI OK')"
```

Teste se o cliente Gemma consegue responder:

```powershell
python -c "from llm.gemma_client import GemmaClient; c=GemmaClient(); print(c.generate('Responda apenas: funcionando'))"
```

Se tudo estiver correto, a resposta esperada será algo parecido com:

```text
funcionando
```

---

## 5. Execução do JARVIS

Com o ambiente virtual ativado e a variável `GEMMA_API_TOKEN` configurada, execute:

```powershell
python main.py
```

Ao iniciar corretamente, o sistema deve exibir mensagens de inicialização parecidas com:

```text
🤖 Inicializando JARVIS...
📡 Conectando ao Gemma 12B...
💾 Inicializando banco de dados...
📚 Inicializando sistema RAG...
🔧 Registrando ferramentas...
✓ JARVIS está pronto para ajudar!
```

Depois disso, basta digitar perguntas no terminal.

Para encerrar:

```text
sair
```

Também funcionam:

```text
exit
quit
```

---

## 6. Como usar a agenda

O JARVIS possui ferramentas para consultar agenda, verificar provas próximas e adicionar eventos.

Exemplos de comandos no chat:

```text
O que eu tenho na agenda hoje?
```

```text
Tenho alguma prova nos próximos 7 dias?
```

```text
Adicione uma prova de Inteligência Artificial para 2026-05-30 às 14:00.
```

```text
O que eu tenho esta semana?
```

Formato de data recomendado:

```text
YYYY-MM-DD
```

Exemplo:

```text
2026-05-30
```

Formato de hora recomendado:

```text
HH:MM
```

Exemplo:

```text
14:00
```

Tipos de evento úteis:

- `aula`
- `prova`
- `trabalho`
- `evento`

---

## 7. Como usar tarefas

O JARVIS também permite listar tarefas, adicionar novas tarefas e concluir tarefas pelo ID.

Exemplos:

```text
Liste minhas tarefas pendentes.
```

```text
Adicione uma tarefa chamada estudar RAG com prioridade alta para 2026-05-28.
```

```text
Concluir tarefa 1.
```

```text
Liste minhas tarefas incluindo as concluídas.
```

Prioridades aceitas:

- `alta`
- `média`
- `baixa`

---

## 8. Como usar o RAG com materiais de estudo

O sistema carrega materiais da pasta:

```text
JARVIS/data/
```

Formatos aceitos:

- `.pdf`
- `.txt`
- `.docx`
- `.md`

Para adicionar materiais, copie os arquivos para a pasta `data` e execute novamente:

```powershell
python main.py
```

Na inicialização, o JARVIS carrega os documentos, divide em chunks, gera embeddings e salva um índice em:

```text
JARVIS/database/rag_index.pkl
```

Exemplos de perguntas:

```text
Busque nos meus materiais o que é regressão logística.
```

```text
Liste os materiais disponíveis.
```

```text
Com base nos materiais, explique o que é gradiente descendente.
```

```text
Procure nos PDFs informações sobre OpenMP.
```

Observação: quando a resposta vier do RAG, o ideal é que o JARVIS responda com base apenas nos trechos recuperados. Se os trechos forem insuficientes, ele deve avisar que não encontrou informação suficiente nos materiais.

---

## 9. Banco de dados

O projeto usa SQLite para armazenar agenda e tarefas.

Arquivo principal:

```text
JARVIS/database/jarvis.db
```

Tabelas principais:

```text
agenda
```

Campos principais:

```text
id, title, description, date, time, event_type
```

```text
tasks
```

Campos principais:

```text
id, title, description, due_date, priority, completed, created_at
```

Se quiser começar com um banco limpo, é possível apagar o arquivo:

```text
JARVIS/database/jarvis.db
```

Na próxima execução, o sistema recria o banco automaticamente.

---

## 10. Dados de exemplo

O projeto possui uma configuração opcional para popular dados de exemplo.

Para ativar no PowerShell:

```powershell
$env:JARVIS_POPULATE_SAMPLE_DATA="1"
python main.py
```

Para não popular exemplos:

```powershell
$env:JARVIS_POPULATE_SAMPLE_DATA="0"
python main.py
```

A recomendação para uso normal é manter:

```powershell
$env:JARVIS_POPULATE_SAMPLE_DATA="0"
```

Assim, o sistema não duplica eventos e tarefas de teste a cada execução.

---

## 11. Logs

As chamadas de ferramentas são registradas em:

```text
JARVIS/logs/tool_calls.jsonl
```

Durante a execução do JARVIS, você pode digitar:

```text
logs
```

O sistema deve mostrar um relatório simples com quantidade de chamadas, erros e uso por ferramenta.

---

## 12. Problemas comuns e soluções

### 12.1. `GEMMA_API_TOKEN não definido`

Causa: a variável de ambiente não foi configurada.

Solução:

```powershell
$env:GEMMA_API_TOKEN="SEU_TOKEN_AQUI"
python main.py
```

---

### 12.2. `ModuleNotFoundError`

Exemplo:

```text
ModuleNotFoundError: No module named 'openai'
```

Causa provável: dependências não instaladas ou ambiente virtual não ativado.

Solução:

```powershell
.\.venv\Scripts\Activate.ps1
pip install -r requirements.txt
```

---

### 12.3. Erro ao importar módulos internos, como `llm.gemma_client`

Causa provável: o comando foi executado fora da pasta raiz do projeto.

Solução:

```powershell
cd C:\Users\kimur\Downloads\JARVIS
python main.py
```

---

### 12.4. Nenhum documento encontrado em `/data`

Causa: a pasta `data` está vazia ou os arquivos estão em formato não suportado.

Solução: coloque arquivos `.pdf`, `.txt`, `.docx` ou `.md` dentro de:

```text
JARVIS/data/
```

Depois execute novamente:

```powershell
python main.py
```

---

### 12.5. O RAG não reflete os materiais novos

Causa provável: o índice antigo ainda está salvo.

Solução: apague o índice antigo e execute de novo:

```powershell
Remove-Item .\database\rag_index.pkl
python main.py
```

---

### 12.6. Primeira execução muito demorada

Causa provável: o modelo de embeddings está sendo baixado e carregado pela primeira vez.

Solução: aguarde a instalação/cache do modelo. Execuções seguintes tendem a ser mais rápidas.

---

### 12.7. `IndentationError` ou `SyntaxError` em `main.py`

Causa: algum trecho do arquivo ficou com indentação incorreta após edições.

Solução: revise principalmente:

- métodos dentro da classe `JARVIS`;
- blocos `try/except`;
- funções `_get_tool_calls`, `_parse_tool_arguments`, `_get_system_prompt` e `chat`;
- registro das ferramentas em `_init_tools`.

Para verificar rapidamente:

```powershell
python -m py_compile main.py
```

Se o comando não mostrar erro, a sintaxe do arquivo está válida.

---

## 13. Fluxo recomendado para testar tudo

Execute os passos nesta ordem:

```powershell
cd C:\Users\kimur\Downloads\JARVIS
.\.venv\Scripts\Activate.ps1
$env:GEMMA_API_TOKEN="SEU_TOKEN_AQUI"
pip install -r requirements.txt
python -m py_compile main.py
python -c "from llm.gemma_client import GemmaClient; c=GemmaClient(); print(c.generate('Responda apenas: funcionando'))"
python main.py
```

Dentro do JARVIS, teste:

```text
Liste os materiais disponíveis.
```

```text
O que eu tenho na agenda hoje?
```

```text
Adicione uma tarefa chamada testar o JARVIS com prioridade alta para 2026-05-30.
```

```text
Liste minhas tarefas pendentes.
```

```text
Busque nos materiais o que é machine learning.
```

```text
logs
```

```text
sair
```

---

## 14. Resumo rápido

Instalação:

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
pip install -r requirements.txt
```

Configuração:

```powershell
$env:GEMMA_API_TOKEN="SEU_TOKEN_AQUI"
```

Execução:

```powershell
python main.py
```

Comandos úteis dentro do JARVIS:

```text
O que tenho hoje na agenda?
Tenho provas nos próximos 7 dias?
Liste minhas tarefas pendentes.
Adicione uma tarefa chamada revisar RAG para 2026-05-30 com prioridade alta.
Busque nos materiais o que é regressão linear.
logs
sair
```
