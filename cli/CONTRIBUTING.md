# Setup

0. Clone e depois execute `git submodule update --init --recursive`
1. Instale as extensões: [rust-analyzer](https://marketplace.visualstudio.com/items?itemName=rust-lang.rust-analyzer) e [CodeLLDB](https://marketplace.visualstudio.com/items?itemName=vadimcn.vscode-lldb)
2. Defina o workspace com a pasta `launcher` como raiz.

## Compilando a CLI no Windows

Por enquanto é necessário ter OpenSSL no Windows, onde ele normalmente não vem instalado. Para instalar:

1. Siga os passos 1 e 2 de [Set up vcpkg](https://learn.microsoft.com/en-us/vcpkg/get_started/get-started-msbuild?pivots=shell-powershell#1---set-up-vcpkg) para obter o executável.
2. Adicione o caminho da pasta `vcpkg` ao PATH do sistema ou do usuário.
3. Execute `vcpkg install openssl:x64-windows-static-md` (após reiniciar o terminal para que as mudanças do PATH tenham efeito).
4. Depois disso você deve conseguir rodar `cargo build` com sucesso.

O OpenSSL é usado na troca de chaves ao encaminhar túneis Basis. Quando todos os clientes Basis relevantes suportarem ED25519, poderemos usar apenas libsodium. No momento, porém, [não há desenvolvimento ativo](https://chromestatus.com/feature/4913922408710144) disso no Chromium.

# Debug

1. Use as tarefas de Debug já configuradas para rodar o launcher.
