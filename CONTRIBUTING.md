# Como contribuir na Jusflow

Combinados de como trabalhamos no código. Vale para todos os repositórios da organização que não tiverem um `CONTRIBUTING.md` próprio.

## Idioma

- **Em inglês:** código, nomes de repositórios, branches, labels e mensagens de commit.
- **Em português:** pull requests e documentação.

## Fluxo de trabalho

1. Crie uma **branch curta** a partir da `main`.
2. Abra um **pull request** pequeno.
3. Espere o **CI passar** antes de juntar.
4. Junte na `main`: isso publica em **staging**.
5. Para publicar em **produção**, crie uma **tag de versão** (`v0.3.0`).

**Nada sobe direto na `main`.** No plano gratuito do GitHub a regra não é forçada, então ela depende de nós.

## Branches

Formato: `<tipo>/<descrição-curta>`, com os mesmos tipos dos commits.

```
feat/ingest-signature
fix/token-refresh-lock
chore/terraform-staging
docs/painel-mvp
```

Branches vivem pouco: no máximo alguns dias. Depois de juntar, apague.

## Commits

Seguimos o padrão **Conventional Commits** (referências no fim deste arquivo).

### Formato

```
<tipo>(<escopo opcional>): <descrição no imperativo, em inglês>

<corpo opcional: o porquê da mudança>

<rodapé opcional: BREAKING CHANGE>
```

### Tipos

| Tipo | Quando usar |
|---|---|
| `feat` | Nova funcionalidade |
| `fix` | Correção de bug |
| `docs` | Só documentação |
| `style` | Formatação, sem mudar comportamento |
| `refactor` | Mudança de código que não corrige bug nem adiciona funcionalidade |
| `perf` | Melhoria de desempenho |
| `test` | Testes novos ou corrigidos |
| `build` | Build, dependências, Dockerfile |
| `ci` | Pipelines do GitHub Actions |
| `chore` | Manutenção que não entra nas categorias acima |
| `revert` | Desfaz um commit anterior |

### Escopos

Usamos o nome da parte do sistema que mudou:

- **ai-sdr:** `ingest`, `worker`, `api`, `dashboard`, `packages`
- **platform-infra:** `terraform`, `iam`, `sql`, `pubsub`, `secrets`
- **whatsapp-gateway:** `gateway`

### Mudança que quebra compatibilidade

Marque com `!` depois do tipo ou do escopo, e explique no rodapé com `BREAKING CHANGE:`.

```
feat(api)!: rename agent stage field to pipelineStageId

BREAKING CHANGE: the dashboard must send pipelineStageId instead of stage.
```

### Exemplos

```
feat(ingest): verify X-GHL-Signature with Ed25519 on raw body
fix(worker): serialize token refresh per location
docs: add painel MVP spec
ci(ingest): deploy to staging on push to main
chore(terraform): create Pub/Sub topic ai-sdr-messages
refactor(worker): extract echo filter into its own module
```

### Regras

- Descrição no imperativo ("add", não "added"), em minúsculas, sem ponto final, com até 72 caracteres.
- Um commit, uma mudança. Se precisar de "e" na descrição, provavelmente são dois commits.

## Pull requests

- **Pequenos:** de preferência menos de 400 linhas alteradas.
- **Título** no padrão Conventional Commits, porque vira o commit final ao juntar com squash. O CI valida o título em todo PR (workflow `PR title`).
- **Descrição em português:** o que muda, por que muda e como testar.
- **CI passando** antes de juntar.
- **Revisão:** todo PR precisa da revisão do outro antes de juntar. No plano gratuito do GitHub a aprovação não é forçada, então ela depende de nós.
- **Junte com squash and merge**, para a `main` ter um commit por PR.

## Versões e deploy

Seguimos o **Versionamento Semântico** (`MAJOR.MINOR.PATCH`):

- `PATCH` (`v0.3.1`): correções (`fix`).
- `MINOR` (`v0.4.0`): funcionalidades novas compatíveis (`feat`).
- `MAJOR` (`v1.0.0`): mudanças que quebram compatibilidade (`!` ou `BREAKING CHANGE`).

Enquanto estivermos em `0.x`, qualquer versão pode mudar a API.

| Ação | Resultado |
|---|---|
| Juntar na `main` | Deploy automático em `jusflow-staging` |
| Criar tag `v*` | Deploy automático em `jusflow-prod` |

## Segredos e dados

- **Nunca** coloque segredo no código, em `.env` versionado, em PR ou em log.
- Segredos ficam no **Secret Manager** do Google Cloud. O deploy usa Workload Identity Federation, sem chave salva no GitHub.
- A **chave de API do cliente** nunca aparece em log, nunca vai para o navegador e fica sempre criptografada.
- Dados de clientes reais não entram em testes, fixtures nem prints. Use dados de exemplo.

## Quando algo quebra em produção

1. **Primeiro, volte** para a revisão anterior no Cloud Run (leva segundos).
2. Avise o outro.
3. Depois investigue, corrija com `fix:` e publique uma nova tag.
4. Se afetou clientes, registre o que aconteceu em `docs`.

## Padrões de código

A definir: linguagem, formatador e linter de cada repositório. Quando decidirmos, registramos aqui.

## Referências

- Conventional Commits 1.0.0, em português: https://www.conventionalcommits.org/pt-br/v1.0.0/
- Convenção de commits do Angular, que deu origem aos tipos: https://github.com/angular/angular/blob/main/CONTRIBUTING.md#commit
- Versionamento Semântico 2.0.0, em português: https://semver.org/lang/pt-BR/
- commitlint, para validar as mensagens no CI ou num hook: https://commitlint.js.org/
- Keep a Changelog, para quando gerarmos changelog: https://keepachangelog.com/pt-BR/
