# Development Container do Code - OSS

[![Open in Dev Containers](https://img.shields.io/static/v1?label=Dev%20Containers&message=Open&color=blue)](https://vscode.dev/redirect?url=vscode://ms-vscode-remote.remote-containers/cloneInVolume?url=https://github.com/microsoft/vscode)

Este repositório inclui configuração de um development container para trabalhar com o Code - OSS em um container local ou usando [GitHub Codespaces](https://github.com/features/codespaces).

> **Dica:** A senha padrão do VNC é `vscode`. O servidor VNC roda na porta `5901` e um cliente web está disponível na porta `6080`.

## Início rápido — local

Se você já tem o VS Code e o Docker instalados, pode clicar no badge acima ou [aqui](https://vscode.dev/redirect?url=vscode://ms-vscode-remote.remote-containers/cloneInVolume?url=https://github.com/microsoft/vscode) para começar. Ao clicar nesses links, o VS Code instalará a extensão Dev Containers se necessário, clonará o código-fonte em um volume do container e subirá um dev container para uso.

1. Instale o Docker Desktop ou o Docker for Linux na sua máquina. (Veja a [documentação](https://aka.ms/vscode-remote/containers/getting-started) para mais detalhes.)

2. **Importante:** o Docker precisa de pelo menos **4 cores e 8 GB de RAM** para um build completo; **9 GB de RAM** é o recomendado. No macOS ou ao usar o motor Hyper-V antigo no Windows, ajuste esses valores no Docker Desktop clicando com o botão direito no ícone do Docker na barra de status e indo em **Preferences/Settings > Resources > Advanced**.

   > **Nota:** A extensão [Resource Monitor](https://marketplace.visualstudio.com/items?itemName=mutantdino.resourcemonitor) vem no container para você acompanhar CPU/memória na barra de status.

3. Instale o [Visual Studio Code Stable](https://code.visualstudio.com/) ou [Insiders](https://code.visualstudio.com/insiders/) e a extensão [Dev Containers](https://aka.ms/vscode-remote/download/containers).

   ![Imagem da extensão Dev Containers](https://microsoft.github.io/vscode-remote-release/images/dev-containers-extn.png)

   > **Nota:** A extensão Dev Containers exige a distribuição Visual Studio Code do Code - OSS. Veja a [FAQ](https://aka.ms/vscode-remote/faq/license) para detalhes.

4. Pressione <kbd>Ctrl/Cmd</kbd> + <kbd>Shift</kbd> + <kbd>P</kbd> ou <kbd>F1</kbd> e selecione **Dev Containers: Clone Repository in Container Volume...**.

   > **Dica:** Você pode usar sua árvore de código local, mas operações como `npm i` podem ser lentas no macOS ou com o motor Hyper-V no Windows. Recomendamos usar o sistema de arquivos WSL no Windows ou a opção "clone repository in container" no Windows e macOS, pois usam "named volume" em vez do sistema de arquivos local.

5. Digite `https://github.com/microsoft/vscode` (ou uma branch ou URL de PR) na caixa de entrada e pressione <kbd>Enter</kbd>.

6. Com o container em execução:
   1. Se as variáveis de ambiente `DISPLAY` ou `WAYLAND_DISPLAY` estiverem definidas localmente (ou no WSL no Windows), os apps de desktop do container aparecerão em janelas locais.
   2. Se não estiverem definidas, abra um navegador em [http://localhost:6080](http://localhost:6080) ou use um [VNC Viewer][def] para conectar em `localhost:5901` e digite `vscode` como senha. Tudo que você iniciar no VS Code ou no terminal integrado aparecerá aí.

Em seguida: **[Experimente!](#experimente)**

## Início rápido — GitHub Codespaces

1. No [repositório microsoft/vscode no GitHub](https://github.com/microsoft/vscode), clique no menu **Code**, selecione **Open with Codespaces** e depois **New codespace**. Se pedir, escolha o tamanho **Standard** (4 cores, 8 GB), que também é o padrão.

   > **Nota:** Essas opções não aparecem no GitHub se você não estiver no beta do Codespaces.

2. Com o codespace rodando no navegador, pressione <kbd>Ctrl/Cmd</kbd> + <kbd>Shift</kbd> + <kbd>P</kbd> ou <kbd>F1</kbd> e selecione **Ports: Focus on Ports View**.

3. Na lista de portas você deve ver **VNC web client (6080)**. Selecione a linha e clique no ícone de globo para abrir em uma aba do navegador.

   > **Dica:** Se a porta não aparecer, <kbd>Ctrl/Cmd</kbd> + <kbd>Shift</kbd> + <kbd>P</kbd> ou <kbd>F1</kbd>, selecione **Forward a Port** e informe a porta `6080`.

4. Na nova aba deve aparecer o noVNC. Clique em **Connect** e digite `vscode` como senha.

Tudo que você iniciar no VS Code ou no terminal integrado aparecerá aí.

Em seguida: **[Experimente!](#experimente)**

### Usando o VS Code com o GitHub Codespaces

A responsividade do VNC pode ser melhor ao acessar o codespace pelo cliente VS Code, pois você pode usar um [VNC Viewer][def]. Passos:

1. Instale o [Visual Studio Code Stable](https://code.visualstudio.com/) ou [Insiders](https://code.visualstudio.com/insiders/) e a extensão [GitHub Codespaces](https://marketplace.visualstudio.com/items?itemName=GitHub.codespaces).

   > **Nota:** A extensão GitHub Codespaces exige a distribuição Visual Studio Code do Code - OSS.

2. Com o VS Code aberto, pressione <kbd>Ctrl/Cmd</kbd> + <kbd>Shift</kbd> + <kbd>P</kbd> ou <kbd>F1</kbd>, escolha **Codespaces: Create New Codespace** e use:

- `microsoft/vscode` como repositório.
- Selecione qualquer branch (ex.: **main**) — você pode trocar depois.
- Tamanho **Standard** (4 cores, 8 GB).

3. Após conectar ao codespace, use um [VNC Viewer][def] para conectar em `localhost:5901` e digite `vscode` como senha.

   > **Dica:** Pode ser necessário mudar **Picture Quality** do cliente VNC para **High** para ver o desktop em cores.

4. Tudo que você iniciar no VS Code ou no terminal integrado aparecerá aí.

Em seguida: **[Experimente!](#experimente)**

## Experimente

Este container usa o gerenciador de janelas [Fluxbox](http://fluxbox.org/) para manter o ambiente enxuto. **Clique com o botão direito na área de trabalho** para ver as opções de menu. Funciona com aplicações GNOME e GTK; outras ferramentas podem ser instaladas se precisar.

> **Nota:** Você também pode definir a resolução pelo terminal com o comando `set-resolution`.

Para começar a trabalhar com o Code - OSS:

1. No seu cliente VS Code local, abra um terminal (<kbd>Ctrl/Cmd</kbd> + <kbd>Shift</kbd> + <kbd>\`</kbd>) e execute:

   ```bash
   npm i
   bash scripts/code.sh
   ```

2. Quando o build terminar, abra um navegador ou um [VNC Viewer][def] para conectar ao ambiente de desktop como no início rápido e digite `vscode` como senha.

3. O Code - OSS deve aparecer na tela.

Agora vamos testar o debug.

1. Feche o Code - OSS clicando no X no canto superior direito da janela (pelo navegador ou VNC viewer).

2. No seu cliente VS Code local, use a view **Run / Debug** para iniciar a configuração **VS Code**. (Geralmente é a padrão; você pode apenas pressionar <kbd>F5</kbd>.)

   > **Nota:** Se der timeout ao iniciar, você pode aumentar o valor de `timeout` nas configurações "VS Code", "Attach Main Process", "Attach Extension Host" e "Attach to Shared Process" em [launch.json](../../.vscode/launch.json). Rodar `./scripts/code.sh` antes costuma configurar o Electron e resolver o timeout.

3. Em seguida o Code - OSS deve abrir com o debugger conectado.

Bom uso!

### Notas

O container vem com o VS Code Insiders instalado. Para rodá-lo a partir de um Terminal Integrado use `VSCODE_IPC_HOOK_CLI= /usr/bin/code-insiders .`.

[def]: https://www.realvnc.com/en/connect/download/viewer/
