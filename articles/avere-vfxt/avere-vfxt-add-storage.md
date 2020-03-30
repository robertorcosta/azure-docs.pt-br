---
title: Configurar o armazenamento do Avere vFXT – Azure
description: Como adicionar um sistema de armazenamento de back-end ao seu Avere vFXT para o Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: dfffef90201ba4bbb5a912df6101e8338012df44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252604"
---
# <a name="configure-storage"></a>Configurar o armazenamento

Esta etapa configura um sistema de armazenamento de back-end para seu cluster do vFXT.

> [!TIP]
> Se você criou um novo contêiner Azure Blob juntamente com o cluster Avere vFXT, esse contêiner já está configurado e pronto para uso.

Siga estas instruções se você não tiver criado um novo contêiner de Blob com o cluster ou se quiser adicionar um hardware extra ou um sistema de armazenamento baseado em nuvem.

Há duas tarefas principais:

1. [Crie um arquivo principal](#create-a-core-filer), que conecta seu cluster vFXT a um sistema de armazenamento existente ou a um contêiner de conta do Azure Storage.

1. [Criar uma junção de namespace](#create-a-junction), que define o caminho em que os clientes serão montados.

Essas etapas usam o Painel de Controle do Avere. Leia [Acessar o cluster do vFXT](avere-vfxt-cluster-gui.md) para aprender a usá-lo.

## <a name="create-a-core-filer"></a>Criar um arquivista central

"Arquivo principal" é um termo vFXT para um sistema de armazenamento back-end. O armazenamento pode ser um dispositivo de hardware, como NetApp ou Isilon, ou pode ser um armazenamento de objetos de nuvem. Mais informações sobre os filers principais podem ser encontradas no [guia de configurações do cluster Avere](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#managing-core-filers).

Para adicionar um arquivista central, escolha um dos dois tipos principais de arquivistas centrais:

* [Arquivista central NAS](#nas-core-filer) – descreve como adicionar um arquivista central NAS
* [O arquivo núcleo da nuvem do Azure Storage](#azure-blob-storage-cloud-core-filer) - descreve como adicionar um contêiner de armazenamento Azure Blob como um filer núcleo de nuvem

### <a name="nas-core-filer"></a>Arquivista central do NAS

Um arquivo núcleo NAS pode ser um aparelho NetApp ou Isilon no local ou um ponto final NAS na nuvem. O sistema de armazenamento deve ter uma conexão confiável de alta velocidade com o cluster do Avere vFXT, por exemplo, conexão do ExpressRoute (não uma VPN) de 1 Gbps e deve fornecer acesso à raiz do cluster para as exportações NAS que estão sendo usadas.

Siga estas etapas para adicionar um arquivo núcleo NAS:

1. No Painel de Controle do Avere, clique na guia **Configurações** na parte superior.

1. Clique **em 'Core Filer'** > **Gerenciar arquivos principais** à esquerda.

1. Clique em **Criar**.

   ![Captura de tela da página Adicionar novo arquivador principal com um cursor sobre o botão Criar](media/avere-vfxt-add-core-filer-start.png)

1. Preencha as informações obrigatórias no assistente:

   * Dar um nome ao arquivista central.
   * Forneça um FQDN (nome de domínio totalmente qualificado) se disponível. Caso contrário, forneça um endereço IP ou nome de host que seja resolvido para seu arquivista central.
   * Escolha sua classe de arquivista na lista. Se não tiver certeza, escolha **Outro**.

     ![Captura de tela da página Adicionar novo arquivista central com o nome do arquivista central e seu nome de domínio totalmente qualificado](media/avere-vfxt-add-core-filer.png)
  
   * Clique em **Avançar** e escolha uma política de cache.
   * Clique em **Adicionar Arquivista**.
   * Para obter mais informações, veja [Como adicionar um novo arquivista central NAS](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_nas.html) no guia de configurações de cluster do Avere.

Em seguida, vá para [Criar uma junção](#create-a-junction).  

### <a name="azure-blob-storage-cloud-core-filer"></a>Arquivo núcleo de nuvem do Azure Blob Storage

Para usar o armazenamento Azure Blob como o armazenamento back-end do cluster vFXT, você precisa de um recipiente vazio para adicionar como um arquivo principal.

Adicionar o Armazenamento de Blobs ao seu cluster requer estas tarefas:

* Criar uma conta de armazenamento (etapa 1, abaixo)
* Criar um contêiner de Blob vazio (etapas 2 e 3)
* Adicionar a chave de acesso de armazenamento como uma credencial de nuvem para o cluster vFXT (etapas 4 a 6)
* Adicionar o contêiner de Blob como um arquivista central para o cluster vFXT (etapas 7 a 9)
* Criar uma junção de namespace que os clientes usam para montar o arquivista central ([Criar uma junção](#create-a-junction), mesmo para armazenamento de hardware e de nuvem)

> [!TIP]
> Se você criar um novo contêiner Blob ao criar um cluster Avere vFXT for Azure, o modelo de implantação configura automaticamente o contêiner como um arquivo principal. (Isso também é verdade se você usar o script de criação, que está disponível a pedido.) Você não precisa configurar o arquivo principal depois.
>
> A ferramenta de criação de cluster faz essas tarefas de configuração para você:
>
> * Cria um novo contêiner Blob na conta de armazenamento fornecida
> * Define o recipiente como um arquivo principal
> * Cria uma junção de namespace para o contêiner
> * Cria um ponto final de serviço de armazenamento dentro da rede virtual do cluster

Para adicionar o Armazenamento de Blobs depois de criar o cluster, siga estas etapas.

1. Crie uma conta de armazenamento V2 de uso geral com estas configurações:

   * **Assinatura** – mesma que a do cluster do vFXT
   * **Grupo de recursos** – mesmo que o grupo de clusters do vFXT (opcional)
   * **Localização** – a mesma que a do cluster vFXT
   * **Desempenho** – padrão (não há suporte para armazenamento Premium)
   * **Tipo de conta**: uso geral V2 (StorageV2)
   * **Replicação** – LRS (armazenamento com redundância local)
   * **Camada de acesso** – frequente
   * **Transferência segura obrigatória** – desabilite essa opção (valor não padrão)
   * **Redes virtuais** – não obrigatórias

   Você pode usar o portal do Azure ou clicar no botão "Implantar no Azure" abaixo.

   [![botão para criar conta de armazenamento](media/deploytoazure.png)](https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAvere%2Fmaster%2Fsrc%2Fvfxt%2Fstorageaccount%2Fazuredeploy.json)

1. Depois de criar a conta, navegue até a página da conta de armazenamento.

   ![Nova conta de armazenamento no portal do Azure](media/avere-vfxt-new-storage-acct.png)

1. Criar um novo contêiner Blob: Clique em **Contêineres** na página de visão geral e clique **em +Container**. Use qualquer nome de contêiner e verifique se o acesso é definido como **Privado**.

   ![Página de bolhas de armazenamento com o botão +container circulado e um novo contêiner sendo criado em uma página pop-up](media/avere-vfxt-new-blob.png)

1. Obtenha a chave da conta do Azure Storage clicando em **teclas de acesso** em **Configurações**. Copie uma das chaves fornecidas.

   ![GUI do portal do Azure para copiar a chave](media/avere-vfxt-copy-storage-key.png)

1. Abra o Painel de Controle do Avere para seu cluster. Clique em **Configurações** e abra **Cluster** > **Credenciais de Nuvem** no painel de navegação esquerdo. Na página de credenciais de nuvem, clique em **Adicionar Credencial**.

   ![Clicar no botão Adicionar Credencial na página de configuração de Credenciais de Nuvem](media/avere-vfxt-new-credential-button.png)

1. Preencha as informações a seguir para criar uma credencial para o arquivista central de nuvem:

   | Campo | Valor |
   | --- | --- |
   | Nome da credencial | qualquer nome descritivo |
   | Tipo de serviço | (selecione a chave de acesso do Azure Storage) |
   | Locatário | nome da conta de armazenamento |
   | Subscription | ID da assinatura |
   | Chave de Acesso de Armazenamento | Chave de conta de Armazenamento do Azure (copiada na etapa anterior) |

   Clique em **Enviar**.

   ![Concluído o formulário de credencial de nuvem no Painel de Controle do Avere](media/avere-vfxt-new-credential-submit.png)

1. Em seguida, crie o arquivista central. No lado esquerdo do painel de controle Avere, clique **em Core Filer** >  **Manage Core Filers**.

1. Clique no botão **Criar** na página de configurações **Gerenciar Arquivistas Centrais**.

1. Preencher o assistente:

   * Selecione o tipo de arquivista **Nuvem**.
   * Dê um nome ao novo arquivista central e clique em **Avançar**.
   * Aceite a política de cache padrão e prossiga para a terceira página.
   * Em **Tipo de serviço**, escolha **Armazenamento do Azure**.
   * Escolha a credencial criada anteriormente.
   * Definir **Conteúdos do Bucket** como **Vazio**
   * Mude **Verificação de certificado** para **Desabilitada**
   * Mude **Modo de compactação** para **Nenhum**
   * Clique em **Avançar**.
   * Na quarta página, digite o nome do contêiner em **Nome do Bucket** como *storage_account_name*/*container_name*.
   * Opcionalmente, defina **Tipo de Criptografia** como **Nenhum**.  O Armazenamento do Azure é criptografado por padrão.
   * Clique em **Adicionar Arquivista**.

   Para obter informações mais detalhadas, leia [Como adicionar um novo arquivista central de nuvem](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html>) no guia de configuração de cluster do Avere.

A página será atualizada ou você poderá atualizar a página para exibir seu novo arquivista central.

Em seguida, você precisará [Criar uma junção](#create-a-junction).

## <a name="create-a-junction"></a>Criar uma junção

Uma junção é um caminho que você cria para os clientes. Os clientes montam o caminho e chegam ao destino escolhido.

Por exemplo, você pode criar `/vfxt/files` para mapear sua exportação `/vol0/data` do arquivista central NetApp e o subdiretório `/project/resources`.

Mais informações sobre junções podem ser encontradas na [seção de namespace do guia de configuração de cluster do Avere](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_namespace.html).

Siga estas etapas na interface do Painel de Controle Avere:

* Clique em **VServer** > **Namespace** no canto superior esquerdo.
* Forneça um caminho de namespace que comece com / (barra), como ``/vfxt/data``.
* Escolha seu arquivista central.
* Escolha a exportação do arquivista central.
* Clique em **Avançar**.

  ![Captura de tela da página "Adicionar nova junção" com os campos preenchidos para junção, arquivista central e exportação](media/avere-vfxt-add-junction.png)

A junção será exibida após alguns segundos. Crie junções adicionais conforme necessário.

Depois que a junção foi criada, os clientes usam o caminho namespace para acessar os arquivos do sistema de armazenamento.

## <a name="next-steps"></a>Próximas etapas

* [Montar o cluster do Avere vFXT](avere-vfxt-mount-clients.md)
* Aprenda maneiras eficientes [de mover dados para um novo contêiner Blob](avere-vfxt-data-ingest.md)
