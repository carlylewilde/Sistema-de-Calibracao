# Sistema de Gestão de Calibração — v3.2 Auditada

Sistema web local em Python/Flask para gestão de instrumentos, calibrações, certificados, padrões, validação, auditoria, alertas, contas de usuário e backup/restauração.

## Instalação

O repositório usa um instalador autocontido que recria toda a estrutura do projeto, incluindo backend, banco, templates, CSS, JavaScript e testes.

```bash
python Sistema_Calibracao_v3_2_INSTALADOR.py
cd Sistema_Calibracao_v3_2
python -m pip install -r requirements.txt
python self_test.py
python security_audit.py
python run_server.py
```

Acesso local:

```text
http://localhost:8080
```

Na primeira execução é criado o usuário `admin` com senha aleatória em `INITIAL_ADMIN_CREDENTIALS.txt`. A troca é obrigatória no primeiro acesso.

## Interface

A v3.2 mantém o layout corporativo da v3.1:

- barra superior azul-marinho;
- menu lateral;
- cards de KPI;
- conformidade e próximos vencimentos;
- tabelas, formulários e modais padronizados;
- interface responsiva;
- marca discreta **“Criado por Carlyle Wilde”** no canto inferior direito, com link para [github.com/carlylewilde](https://github.com/carlylewilde).

## Perfis de acesso

- **Administrador — Nível 1:** contas, parâmetros, backup/restauração, inativação e administração.
- **Validador — Nível 2:** certificados e validação, sem exclusões administrativas nem alteração de cadastros mestres.
- **Consulta — Nível 3:** somente leitura e download.
- **Técnico/Operador:** cadastro e atualização sem aprovação final.
- **Gestor:** consulta gerencial e auditoria.

As permissões são verificadas no backend; esconder botões não é usado como controle de segurança.

## Cybersecurity

A v3.2 inclui:

- autenticação obrigatória e RBAC;
- CSRF, inclusive no login;
- cookies HttpOnly/SameSite e expiração;
- revogação de sessões após troca/reset de senha e mudança de perfil/status;
- bloqueio por tentativas repetidas;
- PBKDF2-HMAC-SHA256 com salt;
- proteção contra remoção do último administrador;
- headers de segurança HTTP;
- validação de uploads por extensão e assinatura binária;
- nomes aleatórios e SHA-256 para documentos;
- preservação de revisões de certificados;
- proteção contra path traversal e ataques comuns em ZIP de restauração;
- backup consistente do SQLite com `integrity_check`;
- GitHub Actions com testes e `pip-audit`;
- Dependabot semanal.

Resultado da revisão local:

```text
40/40 testes automatizados: OK
20/20 controles da auditoria estática: OK
```

Veja [AUDITORIA_CYBERSECURITY_v3_2.md](AUDITORIA_CYBERSECURITY_v3_2.md) e [SECURITY.md](SECURITY.md).

## Implantação em rede

Para uso corporativo, coloque o Waitress atrás de HTTPS/reverse proxy e configure:

```powershell
$env:CALIB_HOST='0.0.0.0'
$env:CALIB_COOKIE_SECURE='1'
python run_server.py
```

A pasta da aplicação, banco e certificados também deve ser protegida por ACLs do Windows/Linux. RBAC controla a aplicação; não substitui permissões do sistema operacional.

## Dependências

- Flask 3.1.3
- Waitress 3.0.2

## Autor

**Carlyle Wilde** — [GitHub](https://github.com/carlylewilde)
