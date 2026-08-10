# Meu Pipeline FCM

Template para criar um pipeline multi-agente de composição musical
culturalmente ancorada, integrado à plataforma **fcm-platform**.

## Passo a passo

### 1. Use este template

Clique em **Use this template** no GitHub (ou `gh repo create --template ...`).
Nomeie seu repo (ex.: `manezinho-feedback-loop`).

### 2. Escreva sua âncora

Edite `.fcm/ancora.yaml` — declaração longa, honesta, sem folclorização.
Edite `ancora:` em `fcm.yaml` — versão curta que agentes vão receber.

### 3. Escreva/ajuste os prompts

`agents/letrista.md`, `agents/prosodista.md`, `agents/critico.md` já
estão preenchidos. **Ajuste os system prompts** para a sua hipótese.

Se sua topologia usa Arranjador, Timbrista ou Curador Cultural, crie os
`.md` correspondentes e declare-os em `agents:` do `fcm.yaml`.

### 4. Adicione seus áudios de campo

Grave pelo menos um áudio de campo com seu celular. Hospede
(SoundCloud unlisted, Drive, S3) e registre em `field_audio/manifest.json`
com URL + sha256 + local + data.

### 5. Registre na plataforma

```bash
gh repo view --json owner,name
# copie owner + name

curl -X POST https://fcm.ifloripa.com.br/api/pipelines \
  -H "Authorization: Bearer $FCM_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "owner": "seu-user",
    "repo":  "seu-repo",
    "installation_id": 12345,
    "branch": "main"
  }'
```

### 6. Configure secrets do repo

Em Settings → Secrets and variables → Actions:

- `ANTHROPIC_API_KEY` — sua chave (BYOK, não vaza para outros participantes)
- `FCM_PLATFORM_TOKEN` — token JWT emitido por `/auth/poll`

### 7. Rode o pipeline

**Local:**
```bash
export ANTHROPIC_API_KEY=sk-ant-...
python fcm_runner.py execute --manifest fcm.yaml --output-dir runs/
```

**GitHub Actions:**
Actions → FCM Run → Run workflow → informe `pipeline_id`.

### 8. Faça commit dos resultados

O runner grava `runs/*.jsonl` (log imutável) e `outputs/*.md` (artefatos).
Comite os dois. A plataforma indexa o run via webhook.

## Estrutura do repo

```
.
├── fcm.yaml                    # manifesto (declaração do pipeline)
├── agents/                     # system prompts
│   ├── letrista.md
│   ├── prosodista.md
│   └── critico.md
├── field_audio/
│   └── manifest.json
├── runs/                       # logs JSONL (append-only, commitados)
├── outputs/                    # artefatos gerados (letra, análise, etc)
├── .fcm/
│   └── ancora.yaml             # âncora longa, para você e para curador
└── .github/workflows/
    └── fcm-run.yml             # workflow oficial
```

## Regras do desafio (resumo)

1. **BYOK.** Sua chave Anthropic, seu limite, seu custo.
2. **Áudios só seus.** Nenhum sample de terceiros. Nenhum trecho de música existente.
3. **Letra original.** Ancorada em território real que você conhece.
4. **Log obrigatório.** Todos os runs commitados. Sem log, submissão inválida.
5. **Licença.** CC-BY-SA-4.0 default para prompts + áudio; ajuste em `pipeline.license`
   se souber o que está fazendo.

## Fork ao vivo

Durante o encontro presencial, participantes forkam pipelines uns dos outros,
mudam UMA coisa (um agente, uma aresta, um modelo) e rerrodam com áudio
ambiente captado no local. O fork é literal (`gh repo fork`), a atribuição
é automática pelo Git.
