# Sistema de Gestão de Calibração — v3.2 auditada

Aplicação web local em Python/Flask para gestão de instrumentos, calibrações, resultados metrológicos, certificados, padrões, validação, auditoria, alertas, usuários e backup/restauração.

## O que mudou na v3.2

A v3.2 mantém o layout da v3.1 e acrescenta a assinatura do autor e uma rodada adicional de hardening de segurança.

### Interface e autoria

A interface usa barra superior, menu lateral, dashboard com KPIs, tabelas, formulários, modais e layout responsivo. A marca discreta **“Criado por Carlyle Wilde”** fica no canto inferior direito e aponta para o perfil do autor no GitHub.

### Perfis de acesso

- **ADMINISTRADOR — Nível 1**: contas, parâmetros, backups, inativação e administração completa.
- **VALIDADOR — Nível 2**: inclui/substitui certificados, revisa e valida; não altera cadastros mestres nem executa exclusões administrativas.
- **CONSULTA — Nível 3**: somente leitura, dashboard, pesquisa e download de documentos.
- **TÉCNICO / OPERADOR**: inclusão e atualização sem validação final.
- **GESTOR**: consulta gerencial e auditoria.

A aplicação não possui rota para exclusão física de certificados. Substituições criam nova revisão e preservam a anterior.

### Segurança

A v3.2 inclui CSRF no login e nas operações de escrita, revogação de sessão após eventos críticos, RBAC no backend, headers de segurança, cache `no-store`, validação de assinatura em uploads, nomes aleatórios de armazenamento, SHA-256, proteção de restauração contra path traversal/symlink/ZIP bomb, e proteção contra remoção do último administrador ativo.

Flask e Waitress estão fixados nesta entrega em **Flask 3.1.3** e **Waitress 3.0.2**.

## Instalação

```bash
python -m pip install -r requirements.txt
python self_test.py
python security_audit.py
python run_server.py
```

Na primeira execução o sistema cria o usuário `admin` com senha aleatória e grava temporariamente `INITIAL_ADMIN_CREDENTIALS.txt`. Troque a senha no primeiro acesso.

## Acesso

Local:

```text
http://localhost:8080
```

Para rede corporativa, use firewall/segmentação e HTTPS por reverse proxy. Quando estiver atrás de HTTPS:

```powershell
$env:CALIB_COOKIE_SECURE='1'
```

## Regras metrológicas

- Erro = Valor indicado − Valor de referência
- Correção = −Erro
- Se `U` não for informada e existirem `u` e `k`: `U = |u × k|`
- `NONE`: não declara conformidade
- `LIMITS_INDICATION`: indicação dentro dos limites cadastrados
- `ABS_ERROR_TOL`: `|Erro| <= Tolerância`
- `GUARD_BAND_U`: `|Erro| + U <= Tolerância`

Nenhuma regra é presumida universal. O procedimento da organização define a regra aplicável.

## Testes

A entrega foi validada com **40 testes automatizados** e **20 verificações estáticas de segurança**. O relatório está em `AUDITORIA_CYBERSECURITY_v3_2.md`.

## Segurança operacional

RBAC controla o que cada usuário pode fazer pela aplicação. Para impedir alteração direta do código, banco e arquivos, a pasta do sistema também precisa de ACL adequada no Windows/Linux e deve ser executada por uma conta técnica.

Autor: [Carlyle Wilde](https://github.com/carlylewilde)
