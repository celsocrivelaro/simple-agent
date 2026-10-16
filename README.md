# simple-agent

Um agente simples — um auditor de despesas com duas ferramentas, montado com
`create_agent` do LangChain — servido como API (FastAPI), empacotado em imagem
Docker e com um pipeline de CI/CD no GitHub Actions.

Material da disciplina **IA Aplicada com LLMs** (SENAC), aula de MLOps e
empacotamento.

## O pipeline (`.github/workflows/ci.yml`)

| Job | O que faz |
|---|---|
| `seguranca` | verifica o `Dockerfile` com hadolint e Trivy |
| `testes` | testes unitários (sem chamar o modelo) e, se houver chave, o *eval* do agente contra um gabarito |
| `publicar` | depois dos dois, e só em *push* na `main`: constrói a imagem e publica no Docker Hub |

Secrets usados (Settings > Secrets and variables > Actions):
`DOCKERHUB_USERNAME`, `DOCKERHUB_TOKEN` e, para o *eval*, `GROQ_API_KEY`. O modelo do *eval* vem da variável `GROQ_MODELO` (aba Variables); sem ela, vale `openai/gpt-oss-20b`.

## Rodar

```bash
cp .env.example .env              # chave do modelo
pip install -r requirements-dev.txt
pytest -v                         # testes unitários
uvicorn api:app --port 8000       # a API

docker build -t agent-ci:dev .
docker run --rm -p 8000:8000 --env-file .env agent-ci:dev
```

Os comandos de cada job, para reproduzir na máquina, estão em
[`comandos.md`](comandos.md).
