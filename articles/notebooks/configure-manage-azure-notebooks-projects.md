---
title: Configurar e gerenciar projetos do Azure Notebooks
description: Como gerenciar os metadados do projeto, arquivos de projeto, as etapas de instalação e de ambiente do projeto por meio da interface do usuário do Azure Notebooks e de acesso direto pelo terminal.
services: app-service
documentationcenter: ''
author: kraigb
manager: barbkess
ms.assetid: 35dd6ff1-a14a-4a2e-b173-6d8467de3e89
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/13/2019
ms.author: kraigb
ms.openlocfilehash: fca98594be08f04b2f266f3aa574837ac024ecf4
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973123"
---
# <a name="manage-and-configure-projects"></a>Gerenciar e configurar projetos

Um projeto no Azure Notebooks é essencialmente uma configuração da máquina virtual do Linux subjacente na qual os Jupyter Notebooks são executados, juntamente com uma pasta de arquivos e metadados descritivos. O painel do projeto no Azure Notebooks permite que você gerencie arquivos e configure as demais características do projeto:

- A camada de computação na qual o projeto é executado, que pode ser a camada gratuita ou uma máquina virtual do Azure.
- Os metadados do projeto, que incluem um nome, uma descrição, um identificador (que é usado ao compartilhar o projeto) e a informação sobre o projeto ser público ou privado.
- O bloco de anotações do projeto, dados e outros arquivos, que você gerencia como qualquer outro sistema de arquivos.
- O ambiente de um projeto, que você gerencia por meio de scripts de inicialização ou diretamente por meio do terminal.
- Logs, que você acessa pelo terminal.

> [!Note]
> Os recursos de gerenciamento e configuração descritos aqui estão disponíveis somente para o proprietário do projeto que o criou inicialmente. No entanto, você pode clonar o projeto em sua própria conta; assim, você se torna o proprietário e pode configurar o projeto conforme desejado.

O Azure Notebooks inicia a máquina virtual subjacente sempre que você executa um notebook ou outro arquivo. O servidor automaticamente salva arquivos e desliga após 60 minutos de inatividade. Você também pode interromper o servidor a qualquer momento com o comando **Desligar** (atalho de teclado: h).

## <a name="compute-tier"></a>Camada de computação

Por padrão, os projetos são executados na camada de **computação gratuita** , que é limitada a 4 GB de memória e 1GB de dados para evitar abusos. Você pode ignorar essas limitações e aumentar a capacidade de computação usando uma máquina virtual diferente que você provisionou em uma assinatura do Azure. Para obter mais informações, consulte [como usar máquinas virtuais de ciência de dados](use-data-science-virtual-machine.md).

## <a name="edit-project-metadata"></a>Editar metadados do projeto

No painel do projeto, selecione **Configurações do Projeto** e, em seguida, selecione a guia **Informações**, que contém os metadados do projeto, conforme descrito na tabela a seguir. Você pode alterar os metadados do projeto a qualquer momento.

| Configuração | DESCRIÇÃO |
| --- | --- |
| Nome do projeto | Um nome amigável para seu projeto que usa o Azure Notebooks para fins de exibição. Por exemplo, "Olá, Mundo no Python". |
| ID do Projeto | Um identificador personalizado que se torna parte da URL que você usa para compartilhar um projeto. Essa ID pode usar apenas letras, números e hifens, é limitada a 30 caracteres e não pode ser uma [ID de projeto reservada](create-clone-jupyter-notebooks.md#reserved-project-ids). Se você não tiver certeza sobre o que usar, uma convenção comum é usar uma versão em letras minúsculas do nome do seu projeto, na qual espaços são transformados em hifens, por exemplo “projeto-meu-notebook” (truncado se necessário para encaixar o limite de comprimento). |
| Projeto público | Se definido, permite que qualquer pessoa com o link acesse o projeto. Ao criar um projeto privado, desmarque essa opção. |
| Ocultar clones | Se definido, outros usuários não poderão ver uma lista de clones que foram feitos para este projeto. Ocultar clones é útil para projetos que são compartilhados com muitas pessoas que não fazem parte da mesma organização, como ao usar um notebook para dar uma aula. |

> [!Important]
>
> Alterar a ID do projeto invalida todos os links para o projeto que você pode ter compartilhado anteriormente.

## <a name="manage-project-files"></a>Gerenciar arquivos de projeto

O painel do projeto mostra o conteúdo do sistema de pastas do projeto. Você pode usar vários comandos para gerenciar esses arquivos.

### <a name="create-new-files-and-folders"></a>Criar novos arquivos e pastas

O comando **+ Novo** (atalho de teclado: n) cria novos arquivos ou pastas. Ao usar o comando, primeiro selecione o tipo de item para criar:

| Tipo de item | DESCRIÇÃO | Comportamento do comando |
| --- | --- | --- |
| **Notebook** | Um Jupyter Notebook | Exibe um pop-up em que você especifica o nome do arquivo e a linguagem de programação do notebook. |
| **Pasta** | Uma subpasta | Cria um campo de edição na lista de arquivos do projeto em que você insere o nome da pasta. |
| **Arquivo em branco** | Um arquivo no qual você pode armazenar qualquer conteúdo como texto, dados, etc. | Cria um campo de edição na lista de arquivos do projeto em que você insere o nome do arquivo. |
| **Markdown** | Um arquivo markdown. | Cria um campo de edição na lista de arquivos do projeto em que você insere o nome do arquivo. |

### <a name="upload-files"></a>Carregar arquivos

O comando **Upload** fornece duas opções para importar dados de outros locais: **Da URL** e **Do Computador**. Para obter mais informações, confira [Trabalhar com arquivos de dados em projetos do Azure Notebooks](work-with-project-data-files.md).

### <a name="select-file-specific-commands"></a>Selecionar comandos específicos do arquivo

Cada item na lista de arquivos do projeto fornece comandos por meio de um menu de contexto aberto clicando com o botão direito do mouse:

![Comandos em um menu de contexto do arquivo](media/project-file-commands.png)

| Comando | Atalho do teclado | Ação |
| --- | --- | --- |
| Executar | r (ou clique) | Executa um arquivo do notebook. Outros tipos de arquivos são abertos para visualização.  |
| Copiar Link | y | Copia um link para o arquivo na área de transferência. |
| Executar no Laboratório do Jupyter | j | Executa um notebook no JupyterLab, que é uma interface mais voltada para desenvolvedores do que aquela normalmente oferecida pelo Jupyter. |
| Visualização | p | Abre uma visualização do arquivo em HTML. Para notebooks, a visualização é uma renderização somente leitura do notebook. Para obter mais informações, confira a seção [Visualização](#preview). |
| Editar arquivo | i | Abre o arquivo para edição. |
| Baixar | d | Baixa um arquivo zip que contém o arquivo ou o conteúdo de uma pasta. |
| Renomear | a | Solicita um novo nome para o arquivo ou pasta. |
| Excluir | x | Solicita confirmação e, em seguida, remove permanentemente o arquivo do projeto. Exclusões não podem ser desfeitas. |
| Mover | m | Move um arquivo para uma pasta diferente no mesmo projeto. |

#### <a name="preview"></a>Visualização

Uma visualização de um arquivo ou notebook é uma exibição somente leitura do conteúdo; a execução de células do notebook é desabilitada. Uma visualização é mostrada para qualquer pessoa que tenha um link para um arquivo ou notebook, mas não tenha se conectado ao Azure Notebooks. Uma vez conectado, um usuário pode clonar o notebook para sua própria conta ou pode baixar o notebook para o seu computador local.

A página de visualização dá suporte a vários comandos da barra de ferramentas com atalhos do teclado:

| Comando | Atalho do teclado | Ação |
| --- | --- | --- |
| Compartilhar | s | Exibe o pop-up de compartilhamento do qual você pode obter um link, compartilhar em mídias sociais, obter o HTML para inserção e enviar um email. |
| Clone | c  | Clonar o notebook para sua conta. |
| Executar | r | Executa o notebook se você tem permissão para fazer isso. |
| Baixar | d | Baixa uma cópia do notebook. |

## <a name="configure-the-project-environment"></a>Configurar o ambiente do projeto

Há três maneiras de configurar o ambiente da máquina virtual subjacente na qual os notebooks são executados:

- Incluir um script de inicialização única
- Use as configurações de ambiente do projeto para especificar as etapas de configuração
- Acessar a máquina virtual por meio de um terminal.

Todas as formas de configuração do projeto são aplicadas sempre que a máquina virtual é iniciada e, portanto, essa configuração afeta todos os notebooks dentro do projeto.

### <a name="one-time-initialization-script"></a>Script de inicialização única

O Azure Notebooks executado pela primeira vez cria um servidor para o projeto e procura por um arquivo no projeto chamado *aznbsetup.sh*. Se esse arquivo estiver presente, o Azure Notebooks o executará. A saída do script é armazenada na pasta do projeto como *.aznbsetup.log*.

### <a name="environment-setup-steps"></a>Etapas de configuração do ambiente

Você pode usar as configurações de ambiente do projeto para criar etapas individuais que configuram o ambiente.

No painel do projeto, selecione **Configurações do Projeto**, depois selecione a guia **Ambiente** em que você adiciona, remove e modifica as etapas de configuração para o projeto:

![O pop-up de configurações do projeto com a guia Ambiente selecionada](media/project-settings-environment-steps.png)

Para adicionar uma etapa, primeiro selecione **+ Adicionar** e, em seguida, selecione um tipo de etapa na lista suspensa **Operação**:

![Seletor de operação para uma nova etapa de Configuração do ambiente](media/project-settings-environment-details.png)

As informações que você projeta em seguida dependem do tipo de operação que você escolheu:

- **Requirements.txt**: Na segunda lista suspensa, selecione um arquivo *Requirements.txt* que já está no projeto. Em seguida, selecione uma versão do Python na terceira lista suspensa que aparece. Usando um arquivo *Requirements.txt*, o Azure Notebooks executa o `pip install -r` com o arquivo *Requirements.txt* ao iniciar um servidor de notebook. Você não precisa instalar explicitamente os pacotes de dentro do notebook propriamente dito.

- **Script de shell**: Na segunda lista suspensa, selecione um script de shell do Bash no projeto (geralmente um arquivo com a extensão *.sh*) que contém quaisquer comandos que você deseja executar para inicializar o ambiente.

- **Environment.yml**: Na segunda lista suspensa, selecione um arquivo *environments.yml* para projetos do Python usando um ambiente do Conda.

Quando você terminar de adicionar etapas, selecione **Salvar**.

### <a name="use-the-terminal"></a>Usar o terminal

No painel do projeto, o comando **Terminal** abre um terminal do Linux que dá acesso direto ao servidor. No terminal você pode baixar os dados, editar ou gerenciar arquivos, inspecionar os processos e até mesmo usar ferramentas, tais como vi e nano.

> [!Note]
> Se você tiver scripts de inicialização no ambiente do seu projeto, a abertura de terminal pode exibir uma mensagem indicando que a instalação ainda está em andamento.

Você pode emitir quaisquer comandos padrão do Linux no terminal. Você também pode usar `ls` na pasta base para ver os diferentes ambientes em que existem na máquina virtual, como *anaconda2_501*, *anaconda3_420*, *anaconda3_501*, *IfSharp* e *R*, juntamente com uma pasta *project* que contém o projeto:

![Terminal de projeto no Azure Notebooks](media/project-terminal.png)

Para afetar um ambiente específico, primeiro altere os diretórios nessa pasta de ambiente.

Para os ambientes do Python, você pode encontrar `pip` e `conda` na pasta *bin* de cada ambiente. Você também pode usar aliases internos para os ambientes:

```bash
# Anaconda 2 5.3.0/Python 2.7: python27
python27 -m pip install <package>

# Anaconda 3 4.2.0/Python 3.5: python35
python35 -m pip install <package>

# Anaconda 3 5.3.0/Python 3.6: python36
python36 -m pip install <package>
```

As alterações feitas no servidor aplicam-se somente à sessão atual, exceto para arquivos e pastas que você criar na pasta *project* propriamente dita. Por exemplo, a edição de um arquivo na pasta project é mantida entre sessões, mas pacotes com `pip install` não são.

> [!Note]
> Se usar `python` ou `python3`, você invocará as versões instaladas pelo sistema do Python, que não são usados para notebooks. Você também não tem permissões para operações como `pip install`, então não se esqueça de usar os aliases específicos da versão.

## <a name="access-notebook-logs"></a>Acessar logs de notebook

Se você enfrenta problemas ao executar um notebook, a saída do Jupyter é armazenada em uma pasta chamada *.nb.log*. Você pode acessar esses logs por meio do comando **Terminal** ou do painel do projeto.

Muitas vezes, quando você está executando o Jupyter localmente, você pode tê-lo iniciado de uma janela do terminal. A janela do terminal mostra a saída, assim como o status do kernel.

Para exibir logs, use o seguinte comando no terminal:

```bash
cat .nb.log
```

Você também pode usar o comando de uma célula de código em um notebook do Python:

```bash
!cat .nb.log
```

## <a name="next-steps"></a>Próximas etapas

- [Como: Trabalhar com arquivos de dados do projeto](work-with-project-data-files.md)
- [Acessar dados de nuvem em um notebook](access-data-resources-jupyter-notebooks.md)
