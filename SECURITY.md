# Segurança

## Uso previsto

O sistema foi projetado para ambiente local ou rede corporativa controlada. Não exponha diretamente à Internet.

## Controles implementados

- autenticação obrigatória e RBAC no backend;
- sessão assinada, HttpOnly, SameSite e expiração;
- revogação de sessões após troca/reset de senha e mudança de perfil/status;
- CSRF em operações de escrita, inclusive login;
- bloqueio temporário após tentativas de senha inválidas;
- headers de segurança HTTP e cache `no-store` em páginas dinâmicas;
- consultas SQL parametrizadas;
- uploads com extensão permitida, nome gerado, assinatura de arquivo e SHA-256;
- documentos servidos como anexo;
- proteção contra path traversal e ZIP bomb básica na restauração;
- backup consistente do SQLite e `integrity_check`;
- trilha de auditoria com usuário, perfil e endereço remoto;
- preservação de revisão documental; não há exclusão física de certificados pela interface.

## Implantação

Em rede corporativa, use HTTPS por reverse proxy e configure `CALIB_COOKIE_SECURE=1`. Restrinja a pasta da aplicação à conta de serviço e aos administradores do servidor. Usuários do sistema devem acessar somente pelo navegador.

Execute periodicamente uma auditoria das dependências (`pip-audit`, quando disponível no ambiente) e mantenha Flask/Waitress atualizados após homologação.

## Limites conhecidos

A interface atual ainda possui JavaScript inline em algumas telas. Por compatibilidade, a CSP permite `unsafe-inline` para scripts e estilos. Isso reduz a força da CSP contra XSS; a saída Jinja permanece escapada por padrão. Uma versão futura deve mover todos os handlers inline para `static/app.js` e remover `unsafe-inline`.

MFA/SSO, TLS, EDR/antivírus, backup externo e ACLs do sistema operacional dependem da infraestrutura onde o sistema for instalado.
