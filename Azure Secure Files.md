
# Azure Secure Files em Pipelines

- [Azure Secure Files em Pipelines](#azure-secure-files-em-pipelines)
  - [O que é o Azure Secure Files?](#o-que-é-o-azure-secure-files)
  - [Adicionar Ficheiros ao Azure Secure Files](#adicionar-ficheiros-ao-azure-secure-files)
    - [Passos para adicionar ficheiros:](#passos-para-adicionar-ficheiros)
      - [Nota Importante:](#nota-importante)
    - [Definir Permissões de Acesso ao Ficheiro](#definir-permissões-de-acesso-ao-ficheiro)
      - [Configurar Permissões de Acesso:](#configurar-permissões-de-acesso)
    - [Utilizar o Ficheiro nas Pipelines](#utilizar-o-ficheiro-nas-pipelines)
      - [Usar Secure Files em Pipelines](#usar-secure-files-em-pipelines)
    - [Utilização do Ficheiro nos Scripts](#utilização-do-ficheiro-nos-scripts)
  - [Conclusão](#conclusão)

## O que é o Azure Secure Files?

O Azure Secure Files é uma funcionalidade do Azure DevOps que permite guardar e gerir ficheiros sensíveis de forma segura, como certificados, chaves SSH ou outros ficheiros confidenciais.  
Esses ficheiros podem ser utilizados durante as pipelines de build e de releases, sem expor diretamente no código ou em variáveis de ambiente não seguras.

## Adicionar Ficheiros ao Azure Secure Files

Antes de utilizar os ficheiros nas pipelines, é necessário adicioná-los ao Azure DevOps. 

### Passos para adicionar ficheiros:

1. Aceder ao Azure DevOps
2. Navegar até ao projeto no qual se deseja adicionar o(s) ficheiro(s).
3. No painel lateral esquerdo, selecionar **Pipelines** e depois **Library**.
4. Selecionar **Secure Files**.
5. Clicar no botão **+ Secure File**.
6. Na janela de upload, selecionar o ficheiro confidencial que se deseja carregar (por exemplo, um certificado `.env`, ou uma chave SSH).
7. Uma vez carregado o ficheiro, ele será guardado de forma segura.

#### Nota Importante:
- **Permissões**: Somente utilizadoores com permissões específicas podem visualizar ou gerir os ficheiros guardados.  
- É importante configurar essas permissões corretamente, principalmente se o ficheiro for altamente sensível.

### Definir Permissões de Acesso ao Ficheiro

Após adicionar o ficheiro ao Azure DevOps, é crucial controlar quem pode acessar e utilizar esse ficheiro.

#### Configurar Permissões de Acesso:

- Após o ficheiro ser carregado, voltar à lista de **Secure Files**.
- Clicar no ficheiro carregado.
- No canto superior direito, clicar em **More Actions (⋮)** e depois em **Security**.
- Configurar as permissões para o ficheiro. Definir quais grupos ou indivíduos terão acesso de leitura/escrita ao ficheiro.

### Utilizar o Ficheiro nas Pipelines

Agora que o ficheiro está armazenado de forma segura, pode ser utilizado dentro das pipelines.  

#### Usar Secure Files em Pipelines

O ficheiro pode ser utilizado diretamente nas definições do pipeline.  
Aqui está um exemplo detalhado de como utilizar o ficheiro:

```yaml
trigger:
  - main

pool:
  vmImage: 'ubuntu-latest'

steps:
  # Passo 1: Baixar o ficheiro seguro
  - task: DownloadSecureFile@1
    name: secureFileTask
    inputs:
      secureFile: 'businessos-tests.env'

  # Passo 2: Verificar se o ficheiro foi descarregado corretamente
  - script: |
      echo "O ficheiro foi transferido para o agente!"
      ls $(Agent.TempDirectory)
    displayName: 'Verificar Download'

  # Passo 3: Usar o ficheiro em outro script
  - script: |
      echo "Usando o ficheiro seguro..."
      cat $(Agent.TempDirectory)/businessos-tests.env
    displayName: 'Usar Ficheiro Seguro'

  # Passo 4: Eliminar o ficheiro após o uso
  - task: DeleteSecureFile@1
    inputs:
      secureFileId: $(secureFileTask.secureFileId)  # Referência ao ID do ficheiro descarregado
    displayName: 'Eliminar Ficheiro Seguro'
```

**Explicação das Tasks:**

- **`DownloadSecureFile@1`**: Faz o download do ficheiro seguro do Azure DevOps para o agente de build. O ficheiro é guardado num diretório temporário.
- **`$(Agent.TempDirectory)`**: Esta variável representa o caminho temporário onde o ficheiro é armazenado.  
  Deve ser usada sempre que for necessário aceder ao ficheiro
- **`DeleteSecureFile@1`**: Elimina o ficheiro temporário após ele ter sido utilizado, garantindo que o ficheiro não permanece no sistema.

### Utilização do Ficheiro nos Scripts

Depois de o ficheiro ser descarregado para o agente, você pode acessá-lo nos seus scripts (sejam scripts Bash, PowerShell, ou outros) utilizando o caminho fornecido por `$(Agent.TempDirectory)`.


## Conclusão

O **Azure Secure Files** oferece uma maneira eficiente e segura de guardar e gerir ficheiros confidenciais dentro do **Azure DevOps**.  
Ao integrar essa funcionalidade nas pipelines, é possível utilizar ficheiros como certificados, chaves e outros artefatos de forma segura, protegendo-os contra exposição.  
Seguindo as melhores práticas e utilizando o recurso de eliminar ficheiros quando já não é necessário,estará a ser garantida uma gestão segura desses ficheiros ao longo do ciclo de vida da aplicação.  

[Go Back](./Index.md)
