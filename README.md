# Agent Dev Container com gVisor

Template simples de Dev Container para rodar agentes de IA de desenvolvimento, como Claude Code, Codex e OpenCode, dentro de um ambiente mais isolado usando Docker + gVisor.

A ideia é evitar rodar agentes diretamente no seu sistema operacional principal.

## Para que serve?

Quando você usa agentes de IA no terminal, eles podem:

- ler arquivos do projeto;
- editar código;
- executar comandos;
- instalar dependências;
- rodar testes;
- abrir servidores locais;
- em alguns casos, tentar acessar arquivos fora do projeto.

Rodar tudo diretamente no seu ambiente real pode ser arriscado, principalmente se o agente executar comandos errados, for induzido por prompt injection ou se você aprovar sem perceber uma ação perigosa.

Este template ajuda a criar uma camada extra de segurança:

```text
VS Code
→ Dev Container
→ Docker
→ gVisor / runsc-ptrace
→ agente de IA
→ projeto em /workspaces
