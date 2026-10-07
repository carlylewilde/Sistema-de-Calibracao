# Auditoria de segurança — Sistema de Gestão de Calibração v3.2

Data: 06/10/2026

## Resultado

A v3.2 foi revisada para uso local/rede corporativa controlada. O código passou em 40 testes automatizados do projeto e em 20 verificações estáticas específicas de segurança.

A classificação desta revisão é **aprovada para homologação controlada**, condicionada a implantação com HTTPS, ACLs do sistema operacional e política de backup adequadas ao ambiente da empresa.

## Controles confirmados

- autenticação obrigatória, sem fallback administrativo;
- RBAC aplicado no backend para Administração, Validação, Operação e Consulta;
- proteção CSRF em POST/PUT/PATCH/DELETE e também no login;
- cookies de sessão HttpOnly e SameSite=Lax;
- sessão com expiração e revogação por `auth_version` após troca/reset de senha ou alteração de perfil/status;
- proteção contra remoção do último administrador ativo;
- bloqueio temporário após tentativas repetidas de senha incorreta;
- PBKDF2-HMAC-SHA256 com salt aleatório e 600.000 iterações;
- chave de assinatura de sessão aleatória e protegida em arquivo local quando não fornecida por variável de ambiente;
- consultas SQL parametrizadas nas entradas do usuário;
- headers de segurança HTTP: CSP, X-Frame-Options, X-Content-Type-Options, Referrer-Policy, Permissions-Policy, COOP e CORP;
- páginas dinâmicas com `Cache-Control: no-store`;
- upload com allowlist de extensões, validação de assinatura binária, nome de armazenamento aleatório, SHA-256 e permissão de arquivo restritiva quando suportada pelo SO;
- arquivos enviados pelo usuário entregues como download, reduzindo risco de conteúdo ativo no mesmo origin;
- path traversal bloqueado no acesso aos arquivos;
- nenhuma rota de exclusão física de certificados;
- restauração de backup protegida contra path traversal, symlinks, entradas duplicadas, arquivos criptografados, excesso de arquivos, tamanho descompactado excessivo e taxa de compressão suspeita;
- backup do SQLite via Backup API e `PRAGMA integrity_check`;
- `/health` não divulga versão da aplicação nem detalhes do banco;
- Waitress configurado sem banner de identificação e com limpeza de headers de proxy não confiáveis;
- `.gitignore` impede o versionamento de banco, segredo de sessão, credencial inicial, uploads, backups, logs e `.env`.

## Dependências

A entrega fixa:

- Flask 3.1.3;
- Waitress 3.0.2.

O repositório inclui GitHub Actions para testes e `pip-audit`, além de Dependabot semanal para dependências Python.

## Riscos residuais

### CSP ainda permite JavaScript inline

Algumas telas antigas ainda usam handlers e blocos JavaScript inline. Para preservar o layout e a funcionalidade nesta revisão, a CSP usa `script-src 'self' 'unsafe-inline'` e `style-src 'self' 'unsafe-inline'`.

Isso não equivale a uma CSP estrita. Jinja continua com escaping padrão e os dados dinâmicos não são inseridos em HTML bruto fora dos badges gerados internamente, mas a remoção de `unsafe-inline` deve ser tratada como melhoria futura.

### HTTPS não é fornecido pela aplicação

Waitress está servindo HTTP. Em localhost isso é adequado para desenvolvimento. Para rede corporativa, o sistema deve ficar atrás de reverse proxy HTTPS e `CALIB_COOKIE_SECURE=1` deve ser configurado. Sem TLS, credenciais e cookies podem ser interceptados na rede.

### Backup é íntegro, não autenticado criptograficamente

O manifesto usa SHA-256 para detectar corrupção ou alteração acidental. Ele não possui assinatura HMAC externa. Como somente Administrador pode restaurar, o risco é reduzido, mas uma política de backup corporativa deve proteger os ZIPs contra escrita não autorizada.

### Controle de código depende do sistema operacional

RBAC impede alteração pelo navegador, mas não impede que alguém com permissão NTFS/Linux edite `app.py`, `db.py`, banco ou arquivos diretamente. Em produção, a pasta deve pertencer a uma conta técnica e usuários comuns não devem ter escrita nela.

### MFA/SSO não implementados

A autenticação é local. Para ambiente corporativo com requisitos mais altos, a evolução recomendada é SSO corporativo/MFA.

## Referências de segurança usadas na revisão

- OWASP Session Management Cheat Sheet
- OWASP File Upload Cheat Sheet
- OWASP HTTP Security Response Headers Cheat Sheet
- OWASP Secure Code Review Cheat Sheet
- OWASP ASVS

## Comandos de verificação

```bash
python self_test.py
python security_audit.py
```

Resultado desta entrega:

```text
40/40 testes automatizados: OK
20/20 controles da auditoria estática: OK
```
