# Guia do codebase do CortexAI

O codebase do CortexAI não é tão intimidador quanto parece!

A maior parte do código do editor com IA está na pasta `src/vs/workbench/contrib/void/`. O caminho da pasta permanece `void` por compatibilidade com o Void (não é necessário alterar imports).

Este documento explica como o codebase do CortexAI funciona. Se você quer instruções de build, veja [CONTRIBUTING.md](CONTRIBUTING.md).

## Guia do codebase do CortexAI

### Visão geral do VSCode

Segue um resumo do VSCode para quem está começando com o CortexAI. Você também pode ver a [wiki](https://github.com/microsoft/vscode/wiki/Source-Code-Organization) da Microsoft para alguns diagramas. O VSCode é um app Electron. O Electron roda dois processos: o processo **main** (internos) e o processo **browser** (browser significa HTML em geral, não só "navegador web").

<p align="center" >
<img src="https://github.com/user-attachments/assets/eef80306-2bfe-4cac-ba15-6156f65ab3bb" alt="Crédito - https://github.com/microsoft/vscode/wiki/Source-Code-Organization" width="700px">
</p>

- Código em uma pasta `browser/` sempre roda no processo browser e pode usar `window` e outros recursos do browser.
- Código em uma pasta `electron-main/` sempre roda no processo main e pode importar `node_modules`.
- Código em `common/` pode ser usado por qualquer um dos processos, mas não recebe imports especiais.
- O ambiente browser não pode importar `node_modules`. Criamos duas alternativas:
  1. Empacotar o código bruto do node_module no browser — fazemos isso com React.
  2. Implementar o código em `electron-main/` e criar um canal entre main/browser — fazemos isso para sendLLMMessage.

### Terminologia

Alguns termos úteis ao trabalhar no VSCode:

- **Editor**: onde você digita o código. Se há 10 abas abertas, ainda é um único editor. Editores contêm abas (ou "models").
- **Model**: representação interna do conteúdo de um arquivo. É compartilhado entre editores (por exemplo, ao pressionar `Cmd+\` para abrir um novo editor, o model de um arquivo como `A.ts` é compartilhado entre eles. Dois editores, um model — é assim que as alterações sincronizam).
- Cada model tem uma **URI** que representa, por exemplo `/Users/.../my_file.txt`. (URI ou "resource" em geral é só um caminho.)
- **Workbench**: o container que reúne todos os editores, o terminal, a árvore do sistema de arquivos, etc.
- Em geral você usa o tipo `ITextModel` para models e `ICodeEditor` para editores. Não há muitos outros tipos.

<p align="center" >
<img src="https://github.com/user-attachments/assets/6521c228-dc96-4cf5-a673-6b9ca78b9b06" alt="Crédito - https://code.visualstudio.com/docs/getstarted/userinterface" width="400px">
</p>

- O VSCode é organizado em "**Services**". Um service é uma classe que é montada uma única vez (em teoria isso se chama "singleton"). Você registra services com `registerSingleton` e pode usá-los em qualquer construtor com `@<Service>`. Veja \_dummyContrib para um exemplo de como registrá-los. O registro é sempre igual.

- "**Actions**" são funções que você registra no VSCode para você ou o usuário chamar depois. Também são chamadas de "**Commands**".
  - O usuário pode executar actions com Cmd+Shift+P (abre a paleta de comandos) ou você pode chamá-las internamente pelo ID usando o commandService. Usamos actions para registrar listeners de atalhos como Cmd+L, Cmd+K, etc. A vantagem é que o usuário pode alterar os keybindings.

### Pipeline interno de mensagens LLM

Segue um diagrama das dependências relevantes entre o envio de uma mensagem pela sidebar do CortexAI e o envio da requisição ao seu provedor.

Enviar mensagens LLM pelo processo main evita problemas de CSP com provedores locais e facilita o uso de node_modules.

<div align="center">
	<img width="100%" src="https://github.com/user-attachments/assets/9cf54dbb-82c4-4488-97a2-bd8dea890b50">
</div>

**Nota:** o arquivo `modelCapabilities` é importante e deve ser atualizado quando surgirem novos modelos!

### Apply

O CortexAI tem dois tipos de Apply: **Fast Apply** (usa Search/Replace, veja abaixo) e **Slow Apply** (reescreve o arquivo inteiro).

Quando você clica em Apply e o Fast Apply está ativo, pedimos ao LLM que emita blocos Search/Replace como:

```
<<<<<<< ORIGINAL
// original code goes here
=======
// replaced code goes here
>>>>>>> UPDATED
```

Isso permite que o CortexAI aplique código rapidamente mesmo em arquivos de 1000 linhas. É o equivalente a pedir ao LLM para usar Ctrl+F e preencher uma busca/substituição.

### Funcionamento interno do Apply

O arquivo `editCodeService` executa o Apply. O mesmo código é usado quando o LLM chama a ferramenta Edit e quando você envia Cmd+K. Apenas com variantes de modo Fast/Slow Apply.

Terminologia importante:

- **DiffZone**: região de texto {startLine, endLine} onde calculamos e exibimos áreas em vermelho/verde, ou **Diffs**. Quando há alterações em um arquivo, percorremos todas as DiffAreas desse arquivo e atualizamos os Diffs.
- **DiffArea**: generalização que só rastreia números de linha, como uma DiffZone.
- O único tipo de DiffArea que pode "stream" é DiffZone. Cada DiffZone tem um llmCancelToken quando está em streaming.

Como o Apply funciona:

- Ao clicar em Apply, criamos uma **DiffZone** sobre o arquivo inteiro para que as alterações do LLM apareçam em vermelho/verde. Em seguida fazemos stream da alteração.
- Quando o LLM chama Edit, na prática está chamando Apply.
- Ao enviar Cmd+K, é o mesmo que Apply, mas criamos uma DiffZone menor (não no arquivo inteiro).

### Funcionamento da escrita em arquivos

Quando o CortexAI quer alterar seu código, ele apenas escreve no text model. Ou seja, para escrever em um arquivo basta saber sua URI — não é preciso carregar, salvar, etc. Há detalhes de URI/model em segundo plano que foram tratados no `voidModelService` (nome legado no código).

### Funcionamento das configurações do CortexAI

O service `voidSettingsService` (nome legado no código) armazena todas as configurações do CortexAI (provedores, modelos, configurações globais, etc.). Pense nele como dependência implícita dos services principais:

<div align="center">
	<img width="800" src="https://github.com/user-attachments/assets/9f3cb68c-a61b-4810-8429-bb90b992b3fa">
</div>

Terminologia usada:

- **FeatureName**: Autocomplete | Chat | CtrlK | Apply
- **ModelSelection**: par {providerName, modelName}.
- **ProviderName**: Nome do provedor: `'ollama'`, `'openAI'`, etc.
- **ModelName**: Nome do modelo (string, ex.: `'gpt-4o'`).
- **RefreshProvider**: provedor que pingamos repetidamente para atualizar a lista de modelos.
- **ChatMode** = normal | gather | agent

### Estado de aprovação

As estruturas de dados do `editCodeService` contêm todas as informações das alterações que o usuário precisa revisar, mas não em um formato prático. Criamos o service abaixo para obter um estado derivado mais útil:

<div align="center">
	<img width="600" src="https://github.com/user-attachments/assets/f3645355-dff6-467c-bc38-ffe52077c08b">
</div>

### Processo de build

Para entender como o pipeline de build pode ser configurado, veja o repositório [void-builder](https://github.com/voideditor/void-builder) (base do Void, adaptável para o CortexAI).

## Guia do codebase do VSCode

Para referências adicionais, segue uma lista de links para começar a usar o VSCode.

<details>

#### Links para iniciantes

- [Guia de UI do VSCode](https://code.visualstudio.com/docs/getstarted/userinterface) — cobre auxbar, painéis, etc.
- [Guia de UX](https://code.visualstudio.com/api/ux-guidelines/overview) — cobre Containers, Views, Items, etc.

#### Links para contribuidores

- [Como o código-fonte do VSCode é organizado](https://github.com/microsoft/vscode/wiki/Source-Code-Organization) — explica onde estão os entry points, o que significam `browser/` e `common/`, etc. É a leitura mais importante desta lista. Recomendamos ler por completo.
- [Estilos embutidos do VSCode](https://code.visualstudio.com/api/references/theme-color) — variáveis CSS do VSCode. Use `var(--vscode-{theme but replacing . with -})`. Veja também o [guia de theming de Webview](https://code.visualstudio.com/api/extension-guides/webview#theming-webview-content).

#### Diversos

- [Todos os comandos](https://code.visualstudio.com/api/references/commands) embutidos no VSCode — pouco usados, mas disponíveis como referência.
- Nota: o repositório do VSCode é o código-fonte do editor Monaco. Um "editor" é um editor Monaco e compartilha o código de ITextModel, etc.

#### API de extensão do VSCode

O CortexAI não é uma extensão, então esses links não são obrigatórios, mas podem ser úteis para extensões ou integrações.

- [Arquivos necessários em uma extensão](https://code.visualstudio.com/api/get-started/extension-anatomy).
- [Schema do `package.json` de uma extensão](https://code.visualstudio.com/api/references/extension-manifest).
- [Guia "Contributes"](https://code.visualstudio.com/api/references/contribution-points) — a parte `"contributes"` do `package.json` é como uma extensão é montada.
- [API completa de extensão do VSCode](https://code.visualstudio.com/api/references/vscode-api) — veja o lado direito para a organização. O [final](https://code.visualstudio.com/api/references/vscode-api#api-patterns) da página é fácil de ignorar mas é útil — cancellation tokens, events, disposables.
- [Eventos de ativação](https://code.visualstudio.com/api/references/activation-events) que você pode definir em `package.json` (não dos mais úteis).

</details>
