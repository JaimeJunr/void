# Contribuindo com o CortexAI

### Bem-vindo! 👋

Este é o guia oficial de como contribuir com o CortexAI. Queremos tornar a contribuição o mais fácil possível; se tiver dúvidas ou comentários, abra uma [Issue](https://github.com/cortexai/cortexai/issues).

Há algumas formas de contribuir:

- Abrir novas Issues — veja [Issues](https://github.com/cortexai/cortexai/issues).
- Enviar Pull Requests com melhorias ou correções.

### Guia do codebase

Recomendamos [fortemente a leitura do guia do codebase](CORTEXAI_CODEBASE_GUIDE.md) se você quiser adicionar novas funcionalidades.

O repositório não é tão intimidador quanto parece depois de ler o guia!

A maior parte do código do editor com IA está na pasta `src/vs/workbench/contrib/void/` (o caminho da pasta permanece por compatibilidade com o Void).

## Editando o código do CortexAI

Se você está fazendo alterações no código do CortexAI como contribuidor, vai querer rodar uma versão local do CortexAI para garantir que suas alterações funcionaram. O Modo de Desenvolvedor permite isso. Veja como usar.

### a. Mac — Pré-requisitos

No Mac, você precisa de Python e XCode. Provavelmente já os tem por padrão.

### b. Windows — Pré-requisitos

No Windows, primeiro instale [Visual Studio 2022](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=Community) (recomendado) ou [VS Build Tools](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=BuildTools) (não recomendado). Se já tiver ambos, pode ser necessário executar os próximos passos nos dois.

Vá na aba "Workloads" e selecione:

- `Desktop development with C++`
- `Node.js build tools`

Vá na aba "Individual Components" e selecione:

- `MSVC v143 - VS 2022 C++ x64/x86 Spectre-mitigated libs (Latest)`
- `C++ ATL for latest build tools with Spectre Mitigations`
- `C++ MFC for latest build tools with Spectre Mitigations`

Por fim, clique em Install.

### c. Linux — Pré-requisitos

Primeiro execute `npm install -g node-gyp`. Em seguida:

- Debian (Ubuntu, etc.): `sudo apt-get install build-essential g++ libx11-dev libxkbfile-dev libsecret-1-dev libkrb5-dev python-is-python3`.
- Red Hat (Fedora, etc.): `sudo dnf install @development-tools gcc gcc-c++ make libsecret-devel krb5-devel libX11-devel libxkbfile-devel`.
- SUSE (openSUSE, etc.): `sudo zypper install patterns-devel-C-C++-devel_C_C++  krb5-devel libsecret-devel libxkbfile-devel libX11-devel`.
- Outros: veja [How to Contribute](https://github.com/microsoft/vscode/wiki/How-to-Contribute).

### Instruções do Modo de Desenvolvedor

Assim você começa a alterar o código do CortexAI. Estes passos cobrem desde clonar o repositório até abrir uma janela do Modo de Desenvolvedor para testar suas alterações.

1. `git clone https://github.com/cortexai/cortexai` para clonar o repositório.
2. `npm install` para instalar todas as dependências.
3. Abra o CortexAI ou o VSCode e inicialize o Modo de Desenvolvedor (pode levar ~5 min; está pronto quando 2 dos 3 spinners virarem check marks):
   - Windows: <kbd>Ctrl+Shift+B</kbd>.
   - Mac: <kbd>Cmd+Shift+B</kbd>.
   - Linux: <kbd>Ctrl+Shift+B</kbd>.
4. Abra a janela do Modo de Desenvolvedor do CortexAI:
   - Windows: `./scripts/code.bat`.
   - Mac: `./scripts/code.sh`.
   - Linux: `./scripts/code.sh`.
5. Pronto para editar o código do CortexAI!
   - Suas alterações só aparecem após <kbd>Ctrl+R</kbd> (<kbd>Cmd+R</kbd>) na nova janela para recarregar. Ou use <kbd>Ctrl+Shift+P</kbd> e `Reload Window`.
   - Você pode adicionar as flags `--user-data-dir ./.tmp/user-data --extensions-dir ./.tmp/extensions` ao comando do passo 4; assim você pode resetar alterações da IDE apagando a pasta `.tmp`.
   - Para encerrar qualquer script de build, use <kbd>Ctrl+D</kbd> no terminal. <kbd>Ctrl+C</kbd> fecha o script na tela, mas ele continua rodando em segundo plano.

Se aparecer algum erro, role até as correções comuns.

#### Correções comuns

- Confirme que seguiu os pré-requisitos acima.
- Confirme que está na versão do Node `20.18.2` (a versão no `.nvmrc`).
  - Com [nvm](https://github.com/nvm-sh/nvm) você pode fazer isso sem mudar a versão global: execute `nvm install` e depois `nvm use` para usar a versão do `.nvmrc` localmente.
- Confirme que o caminho da pasta do CortexAI não tem espaços.
- Se aparecer `"TypeError: Failed to fetch dynamically imported module"`, confira se todos os imports terminam com `.js`.
- Se der erro com React, tente `NODE_OPTIONS="--max-old-space-size=8192" npm run buildreact`.
- Se faltarem estilos, espere alguns segundos e recarregue.
- Se aparecer `npm error libtool:   error: unrecognised option: '-static'` ao rodar `./scripts/code.sh`, use o GNU libtool em vez do BSD (no macOS o padrão é o BSD).
- Se aparecer `The SUID sandbox helper binary was found, but is not configured correctly` ao rodar `./scripts/code.sh`, execute `sudo chown root:root .build/electron/chrome-sandbox && sudo chmod 4755 .build/electron/chrome-sandbox` e rode `./scripts/code.sh` de novo.
- Para outras dúvidas, [abra uma issue](https://github.com/cortexai/cortexai/issues/new). Você também pode consultar a página completa do VSCode [How to Contribute](https://github.com/microsoft/vscode/wiki/How-to-Contribute).

#### Compilando o CortexAI pelo terminal

Para compilar o CortexAI pelo terminal em vez de dentro do VSCode, siga os passos acima, mas em vez de <kbd>Cmd+Shift+B</kbd> execute `npm run watch`. A compilação termina quando aparecer algo como:

```
[watch-extensions] [00:37:39] Finished compilation extensions with 0 errors after 19303 ms
[watch-client    ] [00:38:06] Finished compilation with 0 errors after 46248 ms
[watch-client    ] [00:38:07] Starting compilation...
[watch-client    ] [00:38:07] Finished compilation with 0 errors after 5 ms
```

### Distribuição

O pipeline de build pode ser baseado no [void-builder](https://github.com/voideditor/void-builder) (fork do VSCodium, GitHub Actions para instaladores), com adaptações para o CortexAI. Veja o repositório [void-builder](https://github.com/voideditor/void-builder) para instruções e notas sobre auto-atualização e rebase.

#### Compilando um executável local

Em geral não recomendamos compilar um executável local do CortexAI — o ideal é usar um pipeline de build (ex.: baseado no void-builder) ou usar o Modo de Desenvolvedor para rodar o CortexAI localmente, que é bem mais rápido. Se tiver certeza de que é isso que quer, veja os detalhes abaixo.

<details>
	<summary>Compilar localmente (não recomendado)</summary>

Se você tem certeza de que quer compilar um executável local do CortexAI, siga estes passos. Pode levar ~25 minutos.

Certifique-se de já ter entrado no Modo de Desenvolvedor com o CortexAI antes; depois execute um dos comandos abaixo. Isso criará uma pasta com nome `VSCode-darwin-arm64` ou similar fora do repositório (veja abaixo).

##### Mac

- `npm run gulp vscode-darwin-arm64` — mais comum (Apple Silicon)
- `npm run gulp vscode-darwin-x64` (Intel)

##### Windows

- `npm run gulp vscode-win32-x64` — mais comum
- `npm run gulp vscode-win32-arm64`

##### Linux

- `npm run gulp vscode-linux-x64` — mais comum
- `npm run gulp vscode-linux-arm64`

##### Saída do executável local

O executável local ficará em uma pasta fora do repositório:

```bash
workspace/
├── cortexai/   # Seu fork do CortexAI
└── VSCode-darwin-arm64/ # Saída gerada
```

</details>

## Diretrizes de Pull Request

- Envie um pull request após fazer uma alteração.
- Não é obrigatório abrir Issue, exceto se for uma funcionalidade nova que possa envolver vários PRs.
