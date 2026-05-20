# Agent Dev Container com gVisor

Template simples de **Dev Container** para rodar agentes de IA de desenvolvimento, como **Claude Code**, **Codex** e **OpenCode**, dentro de um ambiente mais isolado usando **VS Code + Docker + gVisor**.

A ideia é evitar rodar agentes diretamente no seu sistema operacional principal.

---

## Por que usar isso?

Agentes de IA no terminal podem:

- ler arquivos do projeto;
- editar código;
- executar comandos;
- instalar dependências;
- rodar testes;
- iniciar servidores locais;
- tentar acessar arquivos fora da pasta do projeto;
- acessar a internet, dependendo das permissões.

Rodar esse tipo de ferramenta direto no seu ambiente real pode trazer riscos, principalmente se o agente executar comandos errados, se você aprovar algo sem perceber ou se algum conteúdo do projeto induzir o agente a fazer uma ação perigosa.

Este template cria uma camada extra de isolamento:

```text
VS Code
→ Dev Container
→ Docker
→ gVisor / runsc-ptrace
→ agente de IA
→ projeto em /workspaces
```

---

## O que este template usa?

- VS Code Dev Containers
- Docker Engine
- gVisor
- runtime `runsc-ptrace`
- Ubuntu como imagem base
- Node.js 22
- Python 3.12
- pnpm via Corepack

---

## Pré-requisitos

Antes de usar este template, você precisa ter no seu ambiente:

- Linux ou WSL2;
- Docker funcionando;
- VS Code;
- extensão **Dev Containers** no VS Code;
- gVisor instalado;
- runtime `runsc-ptrace` registrado no Docker.

Para verificar se o runtime está disponível:

```bash
docker info | grep -i runtimes -A 5
```

Você deve ver algo parecido com:

```text
Runtimes: runsc-ptrace runsc io.containerd.runc.v2 runc
Default Runtime: runc
```

> Este template não instala Docker nem gVisor. Ele assume que você já tem o runtime `runsc-ptrace` configurado no Docker.

---

## Uso rápido

Copie a pasta `.devcontainer` deste repositório para a raiz do seu projeto.

Exemplo:

```bash
cp -r caminho/para/agent-devcontainer-gvisor-template/.devcontainer /caminho/do/seu/projeto/
```

Depois entre no projeto:

```bash
cd /caminho/do/seu/projeto
code .
```

No VS Code, execute:

```text
Ctrl + Shift + P
Dev Containers: Reopen in Container
```

Aguarde o container ser criado e carregado.

---

## Como saber se funcionou?

Depois que o VS Code reabrir o projeto no container, abra um terminal integrado e rode:

```bash
pwd
```

O esperado é algo como:

```text
/workspaces/nome-do-projeto
```

Se aparecer `/workspaces/...`, você está dentro do Dev Container.

Se aparecer algo como `/home/seu-usuario/...`, você ainda está no terminal normal do WSL/Linux.

---

## Como confirmar se está usando gVisor?

Em outro terminal, fora do Dev Container, rode:

```bash
docker ps
```

Depois rode:

```bash
docker inspect $(docker ps -q | head -n 1) --format '{{.HostConfig.Runtime}}'
```

O esperado é:

```text
runsc-ptrace
```

Se aparecer `runsc-ptrace`, o container do VS Code está usando o runtime do gVisor.

---

## Instalando agentes dentro do container

Depois que o Dev Container estiver aberto, instale os agentes dentro do próprio container.

### Claude Code

```bash
npm install -g @anthropic-ai/claude-code
```

Depois rode:

```bash
claude
```

Recomendação inicial dentro do Claude Code:

```text
/sandbox
```

Escolha:

```text
Sandbox BashTool, with regular permissions
```

---

### Codex

```bash
npm install -g @openai/codex
```

Depois rode:

```bash
codex
```

Recomendação inicial de permissão:

```text
Default
```

Esse modo permite ao Codex ler, editar arquivos e executar comandos dentro do workspace, mas exige aprovação para acessar internet ou editar arquivos fora do projeto.

---

## Fluxo recomendado

Use os agentes sempre dentro do terminal que mostra:

```text
/workspaces/nome-do-projeto
```

Evite rodar agentes diretamente no terminal real do WSL/Linux, como:

```text
/home/seu-usuario/projetos/nome-do-projeto
```

A diferença prática é:

```text
Terminal em /home/...       → ambiente real do seu sistema
Terminal em /workspaces/... → ambiente isolado do Dev Container
```

---

## Sobre `runsc-ptrace`

Em alguns ambientes, especialmente no WSL2, o `runsc` padrão com `systrap` pode falhar ao iniciar containers.

Por isso este template usa:

```json
"runArgs": [
  "--runtime=runsc-ptrace"
]
```

O `ptrace` pode ser mais lento que `systrap`, mas costuma ser mais compatível em ambientes como WSL2.

---

## Quando usar permissões máximas?

Evite usar permissões máximas sem necessidade.

Use modos como `Full Access`, `danger-full-access` ou equivalentes somente quando:

- você estiver em um ambiente descartável;
- não houver credenciais sensíveis;
- você souber exatamente o que o agente vai fazer;
- o projeto for confiável;
- você estiver disposto a revisar as mudanças depois.

Para uso normal, prefira:

```text
workspace limitado
aprovação manual
rede controlada
```

---

## Estrutura do template

```text
agent-devcontainer-gvisor-template/
└── .devcontainer/
    └── devcontainer.json
```

O arquivo principal é:

```text
.devcontainer/devcontainer.json
```

Ele define a imagem base, as ferramentas instaladas e o runtime Docker usado pelo container.

---

## Exemplo de uso em qualquer projeto

Estando dentro da pasta de um projeto:

```bash
cp -r ~/caminho/para/agent-devcontainer-gvisor-template/.devcontainer .
code .
```

Depois, no VS Code:

```text
Ctrl + Shift + P
Dev Containers: Reopen in Container
```

Pronto. O projeto será aberto dentro de um Dev Container usando `runsc-ptrace`.

---

## Objetivo

Este template não promete segurança perfeita.

O objetivo é criar uma prática mais segura e simples para desenvolvedores que usam agentes de IA no terminal, reduzindo o risco de rodar ferramentas autônomas diretamente no sistema principal.

Ele é uma base inicial para trabalhar com mais isolamento, previsibilidade e controle.

---

## Licença

MIT
