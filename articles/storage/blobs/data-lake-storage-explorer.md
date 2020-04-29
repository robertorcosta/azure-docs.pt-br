---
title: Usar Gerenciador de Armazenamento do Azure com Azure Data Lake Storage Gen2
description: Use o Gerenciador de Armazenamento do Azure para gerenciar diretórios e listas de controle de acesso (ACL) de arquivos e diretórios em contas de armazenamento que têm o namespace hierárquico (HNS) habilitado.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: fca9fa8a964c6c9d69ffbb3036bd4774e0d1bd34
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79255542"
---
# <a name="use-azure-storage-explorer-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Use Gerenciador de Armazenamento do Azure para gerenciar diretórios, arquivos e ACLs no Azure Data Lake Storage Gen2

Este artigo mostra como usar [Gerenciador de armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/) para criar e gerenciar diretórios, arquivos e permissões em contas de armazenamento que têm o namespace HIERÁRQUICO (HNS) habilitado.

## <a name="prerequisites"></a>Pré-requisitos

> [!div class="checklist"]
> * Uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Uma conta de armazenamento que tem o namespace hierárquico (HNS) habilitado. Siga [estas](data-lake-storage-quickstart-create-account.md) instruções para criar uma.
> * Gerenciador de Armazenamento do Azure instalado no computador local. Para instalar o Gerenciador de Armazenamento do Azure para Windows, Macintosh ou Linux, confira o [Gerenciador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/).

## <a name="sign-in-to-storage-explorer"></a>Conectar-se ao Gerenciador de Armazenamento

Quando você iniciar o Gerenciador de Armazenamento pela primeira vez, a janela **Gerenciador de Armazenamento do Microsoft Azure - Conectar** será exibida. Embora o Gerenciador de Armazenamento forneça várias maneiras de se conectar às contas de armazenamento, apenas uma maneira tem suporte atualmente para gerenciar ACLs.

|Tarefa|Finalidade|
|---|---|
|Adicionar uma conta do Azure | Redireciona você para a página de entrada de sua organização para autenticá-lo no Azure. Atualmente, esse será o método de autenticação com suporte apenas se você quiser gerenciar e definir ACLs.|
|Usar uma cadeia de conexão ou um URI de assinatura de acesso compartilhado | Podem ser usados para acessar diretamente um contêiner ou uma conta de armazenamento com um token SAS ou uma cadeia de conexão compartilhada. |
|Usar um nome e uma chave da conta de armazenamento| Use o nome e a chave da sua conta de armazenamento para se conectar ao armazenamento do Azure.|

Selecione **Adicionar uma conta do Azure** e clique em **entrar..**. Siga os prompts na tela para entrar em sua conta do Azure.

![Gerenciador de Armazenamento do Microsoft Azure – Janela Conexão](media/storage-quickstart-blobs-storage-explorer/connect.png)

Ao concluir a conexão, o Gerenciador de Armazenamento do Azure carrega exibindo a guia **Explorer**. Essa exibição lhe dá informações de todas suas contas de armazenamento do Azure e do armazenamento local, configuradas por meio do [Emulador de Armazenamento do Azure](../common/storage-use-emulator.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json), das contas do [Cosmos DB](../../cosmos-db/storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) ou dos ambientes do [Azure Stack](/azure-stack/user/azure-stack-storage-connect-se?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

![Gerenciador de Armazenamento do Microsoft Azure – Janela Conexão](media/storage-quickstart-blobs-storage-explorer/mainpage.png)

## <a name="create-a-container"></a>Criar um contêiner

Um contêiner contém diretórios e arquivos. Para criar um, expanda a conta de armazenamento que você criou na etapa de continuação. Selecione **Contêineres de Blobs**, clique com botão direito do mouse e selecione **Criar Contêiner de Blobs**. Insira o nome do seu contêiner. Consulte a seção [criar um contêiner](storage-quickstart-blobs-dotnet.md#create-a-container) para obter uma lista de regras e restrições sobre os contêineres de nomenclatura. Ao concluir, pressione **Enter** para criar o contêiner. Depois que o contêiner tiver sido criado com êxito, ele será exibido na pasta **contêineres de blob** para a conta de armazenamento selecionada.

![Gerenciador de Armazenamento do Microsoft Azure-criando um contêiner](media/data-lake-storage-explorer/creating-a-filesystem.png)

## <a name="create-a-directory"></a>Criar um diretório

Para criar um diretório, selecione o contêiner que você criou na etapa de continuação. Na faixa de seleção do contêiner, escolha o botão **nova pasta** . Insira o nome do seu diretório. Ao concluir, pressione **Enter** para criar o diretório. Depois que o diretório tiver sido criado com êxito, ele aparecerá na janela do editor.

![Gerenciador de Armazenamento do Microsoft Azure-criando um diretório](media/data-lake-storage-explorer/creating-a-directory.png)

## <a name="upload-blobs-to-the-directory"></a>Carregar blobs para o diretório

Na faixa de escolhas do diretório, escolha o botão **carregar** . Essa operação lhe dá a opção de carregar um arquivo ou uma pasta.

Escolha os arquivos ou pastas a serem carregados.

![Gerenciador de Armazenamento do Microsoft Azure – carregar um blob](media/data-lake-storage-explorer/upload-file.png)

Ao selecionar **OK**, os arquivos selecionados são colocados na fila para carregamento e cada arquivo é carregado. Quando o carregamento for concluído, os resultados são mostrados na janela **Atividades**.

## <a name="view-blobs-in-a-directory"></a>Exibir blobs em um diretório

No aplicativo **Gerenciador de Armazenamento do Azure**, selecione um diretório em uma conta de armazenamento. O painel principal mostra uma lista dos blobs no diretório selecionado.

![Gerenciador de Armazenamento do Microsoft Azure – listar blobs em um diretório](media/data-lake-storage-explorer/list-files.png)

## <a name="download-blobs"></a>Baixar blobs

Para baixar arquivos usando **Gerenciador de armazenamento do Azure**, com um arquivo selecionado, selecione **baixar** na faixa de opções. Uma caixa de diálogo é aberta, na qual é possível inserir um nome de arquivo. Selecione **salvar** para iniciar o download de um arquivo para o local local.

## <a name="managing-access"></a>Gerenciando o acesso

É possível definir permissões na raiz do seu contêiner. Para fazer isso, você deve estar conectado ao Gerenciador de Armazenamento do Azure com sua conta individual com direitos para fazê-lo (se comparado com uma cadeia de caracteres de conexão). Clique com o botão direito do mouse no contêiner e selecione **Gerenciar Permissões** abrindo a caixa de diálogo **Gerenciar Permissão**.

![Gerenciador de Armazenamento do Microsoft Azure – gerenciar o acesso ao diretório](media/storage-quickstart-blobs-storage-Explorer/manageperms.png)

A caixa de diálogo **Gerenciar Permissão** permite gerenciar permissões para o proprietário e o grupo de proprietários. Ele também permite adicionar novos usuários e grupos à lista de controle de acesso para os quais você então pode gerenciar permissões.

Para adicionar um novo usuário ou grupo à lista de controle de acesso, selecione o campo **Adicionar usuário ou grupo**.

Insira a entrada correspondente do AAD (Azure Active Directory) que deseja adicionar à lista e, em seguida, selecione **Adicionar**.

O usuário ou grupo agora aparecerão no campo **Usuários e grupos:**, permitindo que você comece a gerenciar suas permissões.

> [!NOTE]
> É uma melhor prática e recomendamos criar um grupo de segurança no AAD e manter as permissões no grupo, em vez de usuários individuais. Para obter detalhes sobre essa recomendação, bem como outras melhores práticas, confira [melhores práticas para o Data Lake Storage Gen2](data-lake-storage-best-practices.md).

Há duas categorias de permissões que você pode atribuir: ACLs de acesso e ACLs padrão.

* **Acesso**: ACLs de acesso controlam o acesso a um objeto. Arquivos e diretórios têm ambos ACLs de acesso.

* **Padrão**: um modelo de ACLs associado a um diretório que determina as ACLs de acesso para todos os itens filho que são criados nesse diretório. Arquivos não têm ACLs padrão.

Em ambas as categorias, há três permissões que você pode atribuir em arquivos ou diretórios: **ler**, **gravar**e **executar**.

>[!NOTE]
> Fazer seleções aqui não definirá permissões em nenhum item existente no momento dentro do diretório. Você deverá ir para cada item individual e definir as permissões manualmente se o arquivo já existir.

Você pode gerenciar permissões em diretórios individuais, bem como arquivos individuais, que são o que permite o controle de acesso refinado. O processo para gerenciar permissões para diretórios e arquivos é o mesmo descrito acima. Clique com o botão direito do mouse no arquivo ou diretório cujas permissões você deseja gerenciar no e siga o mesmo processo.

## <a name="next-steps"></a>Próximas etapas

Aprenda as listas de controle de acesso no Data Lake Storage Gen2.

> [!div class="nextstepaction"]
> [Controle de acesso no Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)
