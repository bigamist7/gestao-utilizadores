# Gestão de Utilizadores em PHP

Sistema de registo, autenticação e gestão de utilizadores em PHP e MySQL, desenvolvido a partir de um enunciado fornecido pelo formador, com todas as melhorias de segurança sugeridas implementadas sobre o código-base original.

## 🎯 Objetivos

- Criar uma pasta isolada para o projeto e uma base de dados/utilizador MySQL dedicados;
- Implementar todas as melhorias de segurança sugeridas no enunciado: sessões, logout, hash de palavra-passe, prepared statements, não exposição de dados sensíveis por GET, ficheiro único de credenciais e bloqueio de conta após tentativas falhadas;
- Estilizar a aplicação final.

## 🖥️ Ambiente / Stack

| | |
|---|---|
| **Sistema operativo** | Ubuntu Server 26.04 LTS |
| **IP do servidor** | 192.168.1.93/24 |
| **Projeto** | `/var/www/html/gestao_utilizadores` |
| **Base de dados** | `gestao_utilizadores` (utilizador dedicado `gu_user`) |
| **Linguagens** | PHP (mysqli, prepared statements), MySQL, HTML, CSS |
| **Continuação de** | Laboratórios UC0635 (LAMP + Webmin, Samba, FTP, SSH + phpMyAdmin, admin + MySQL Workbench, WordPress) |

## 🔒 Melhorias de segurança implementadas sobre o enunciado original

| Melhoria | Descrição |
|---|---|
| **Hash de password** | `password_hash()` / `password_verify()` em vez de comparação em texto simples |
| **Prepared statements** | `mysqli_prepare` + `bind_param` em todas as queries de login, registo, alteração e eliminação — elimina SQL Injection |
| **Sessões** | `session_start()` + `verificar_sessao.php` centralizado + `logout.php` |
| **Bloqueio de conta** | Colunas `tentativas_falhadas` e `bloqueado`; bloqueio permanente ao fim de 3 tentativas falhadas, mesmo com password correta subsequente |
| **Sem dados sensíveis por GET** | Alterar/Eliminar usam apenas `?id=`, nunca nome/email na URL |
| **Ficheiro único de credenciais** | `acesso_bd.php`, incluído em todas as páginas que acedem à base de dados |
| **Utilizador MySQL dedicado** | `gu_user`, privilégios restritos apenas à base de dados `gestao_utilizadores` |

## 🔧 Funcionalidades

- Registo, login/logout, visualização, alteração e eliminação de utilizadores;
- Verificação de sessão centralizada, protegendo o acesso direto a páginas internas;
- Mensagem de erro genérica no login ("Erro de autenticação!"), independente de o utilizador existir ou a password estar errada.

## 🐞 Problemas encontrados e soluções

- **Root do MySQL inacessível** — password desconhecida, herdada da mudança de plugin de autenticação (auth_socket → caching_sha2_password) feita no laboratório de phpMyAdmin. Recuperado com arranque em `--skip-grant-tables`, reset da password via `ALTER USER`, e reposição do serviço normal.
- **Coluna de password demasiado pequena** — o enunciado original definia `VARCHAR(8)` (adequado a texto simples); um hash `password_hash()` ocupa ≈60 caracteres. Corrigido para `VARCHAR(255)`.
- **Formulário de alteração sem efeito real** — os campos de texto (nome, email) ficaram por lapso fora da tag `<form>`, pelo que não eram enviados, apesar de a mensagem de sucesso aparecer. Corrigido movendo a tag `<form>` para envolver a totalidade do formulário.

## ✅ Resultado

Fluxo completo (login, registo, listagem, alteração, eliminação, logout, proteção de sessão, bloqueio de conta) implementado, testado e validado de fio a pavio, incluindo estilização final de todas as páginas.

## 📚 Aprendizagens

- Um bloqueio de conta só é eficaz se persistir mesmo perante credenciais corretas subsequentes;
- Um formulário HTML só envia os campos fisicamente dentro da tag `<form>` — campos "visualmente" dentro mas estruturalmente fora nunca chegam ao servidor;
- Passar apenas um `id` na URL (em vez de todos os dados) evita expor informação pessoal, sem complicar a implementação;
- Centralizar lógica repetida (verificação de sessão) num único ficheiro incluído reduz duplicação e facilita alterações futuras.

## ➡️ Ver também

Este projeto serve de base à demonstração de [SQL Injection](../sqli-demo) realizada de seguida, que reutiliza a mesma base de dados para comparar a versão vulnerável com a versão protegida aqui implementada.

---
*Relatório de laboratório — UC0635, CET em Cibersegurança (IEFP Alcoitão).*
