---
title: Configurar o armazenamento do Avere vFXT – Azure
description: Como adicionar um sistema de armazenamento de back-end ao seu Avere vFXT para o Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: rohogue
ms.openlocfilehash: 86b63e6d9799387347093e469015fbd3019069d1
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255052"
---
# <a name="configure-storage"></a>Configurar o armazenamento

Esta etapa configura um sistema de armazenamento de back-end para seu cluster do vFXT.

> [!TIP]
> Se você tiver criado um novo contêiner de Blob do Azure junto com o cluster do Avere vFXT, esse contêiner já estará configurado para uso e você não precisará adicionar o armazenamento.

Siga estas instruções se você não tiver criado um novo contêiner de Blob com o cluster ou se quiser adicionar um hardware extra ou um sistema de armazenamento baseado em nuvem.

Há duas tarefas principais:

1. [Criar um arquivista central](#create-a-core-filer), que conecta seu cluster do vFXT a um sistema de armazenamento existente ou a uma conta de Armazenamento do Azure.

1. [Criar uma junção de namespace](#create-a-junction), que define o caminho em que os clientes serão montados.

Essas etapas usam o Painel de Controle do Avere. Leia [Acessar o cluster do vFXT](avere-vfxt-cluster-gui.md) para aprender a usá-lo.

## <a name="create-a-core-filer"></a>Criar um arquivista central

"Arquivista central" é um termo do vFXT para um sistema de armazenamento de back-end. O armazenamento pode ser um dispositivo de hardware, como NetApp ou Isilon, ou pode ser um armazenamento de objetos de nuvem. Mais informações sobre arquivistas centrais podem ser encontradas [no guia de configurações do cluster do Avere](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#managing-core-filers).

Para adicionar um arquivista central, escolha um dos dois tipos principais de arquivistas centrais:

  * [Arquivista central NAS](#nas-core-filer) – descreve como adicionar um arquivista central NAS 
  * [Arquivista central de nuvem de Armazenamento do Azure](#azure-storage-cloud-core-filer) – descreve como adicionar uma conta de Armazenamento do Azure como um arquivista central de nuvem

### <a name="nas-core-filer"></a>Arquivista central do NAS

Um arquivista central NAS pode ser um Isilon ou NetApp local ou um ponto de extremidade na nuvem. O sistema de armazenamento deve ter uma conexão confiável de alta velocidade com o cluster do Avere vFXT, por exemplo, conexão do ExpressRoute (não uma VPN) de 1 Gbps e deve fornecer acesso à raiz do cluster para as exportações NAS que estão sendo usadas.

As etapas a seguir adicionam um arquivista central do NAS:

1. No Painel de Controle do Avere, clique na guia **Configurações** na parte superior.

1. Clique em **Arquivista Central** > **Gerenciar Arquivistas Centrais** à esquerda.

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

### <a name="azure-storage-cloud-core-filer"></a>Arquivista central de nuvem do Armazenamento do Azure

Para usar o Armazenamento de Blobs do Azure como armazenamento de back-end do cluster do vFXT, você precisa de um contêiner vazio para ser adicionado como um arquivista central.

> [!TIP] 
> Se você optar por criar um contêiner de blob ao mesmo tempo em que cria o cluster do Avere vFXT, o modelo de implantação ou o script criará um contêiner de armazenamento, o definirá como um arquivista principal e criará a junção de namespace como parte da criação do cluster vFXT. O modelo também cria um ponto de extremidade de serviço de armazenamento dentro da rede virtual do cluster. 

Adicionar o Armazenamento de Blobs ao seu cluster requer estas tarefas:

* Criar uma conta de armazenamento (etapa 1, abaixo)
* Criar um contêiner de Blob vazio (etapas 2 e 3)
* Adicionar a chave de acesso de armazenamento como uma credencial de nuvem para o cluster vFXT (etapas 4 a 6)
* Adicionar o contêiner de Blob como um arquivista central para o cluster vFXT (etapas 7 a 9)
* Criar uma junção de namespace que os clientes usam para montar o arquivista central ([Criar uma junção](#create-a-junction), mesmo para armazenamento de hardware e de nuvem)

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

1. Crie um contêiner de blob clicando em **Blobs** na página de visão geral e depois clique em **+Contêiner**. Use qualquer nome de contêiner e verifique se o acesso é definido como **Privado**.

   ![Página de blobs de armazenamento sem contêiner existente](media/avere-vfxt-blob-no-container.png)

1. Obtenha a chave de conta de Armazenamento do Azure clicando em **Chaves de acesso** em **Configurações**:

   ![GUI do portal do Azure para copiar a chave](media/avere-vfxt-copy-storage-key.png) 

1. Abra o Painel de Controle do Avere para seu cluster. Clique em **Configurações** e abra **Cluster** > **Credenciais de Nuvem** no painel de navegação esquerdo. Na página de credenciais de nuvem, clique em **Adicionar Credencial**.

   ![Clicar no botão Adicionar Credencial na página de configuração de Credenciais de Nuvem](media/avere-vfxt-new-credential-button.png)

1. Preencha as informações a seguir para criar uma credencial para o arquivista central de nuvem: 

   | Campo | Valor |
   | --- | --- |
   | Nome da credencial | qualquer nome descritivo |
   | Tipo de serviço | (selecione a Chave de Acesso de Armazenamento do Azure) |
   | Locatário | nome da conta de armazenamento |
   | Assinatura | ID da assinatura |
   | Chave de Acesso de Armazenamento | Chave de conta de Armazenamento do Azure (copiada na etapa anterior) | 

   Clique em **Enviar**.

   ![Concluído o formulário de credencial de nuvem no Painel de Controle do Avere](media/avere-vfxt-new-credential-submit.png)

1. Em seguida, crie o arquivista central. No lado esquerdo do Painel de Controle do Avere, clique em **Arquivista Central** >  **Gerenciar Arquivistas Centrais**. 

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

Por exemplo, você pode criar `/avere/files` para mapear sua exportação `/vol0/data` do arquivista central NetApp e o subdiretório `/project/resources`.

Mais informações sobre junções podem ser encontradas na [seção de namespace do guia de configuração de cluster do Avere](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_namespace.html).

Siga estas etapas na interface de configurações do painel de controle do Avere:

* Clique em **VServer** > **Namespace** na parte superior esquerda.
* Forneça um caminho de namespace que comece com / (barra), como ``/avere/data``.
* Escolha seu arquivista central.
* Escolha a exportação do arquivista central.
* Clique em **Avançar**.

  ![Captura de tela da página "Adicionar nova junção" com os campos preenchidos para junção, arquivista central e exportação](media/avere-vfxt-add-junction.png)

A junção será exibida após alguns segundos. Crie junções adicionais conforme necessário.

Depois que a junção tiver sido criada, os clientes poderão [Montar o cluster do Avere vFXT](avere-vfxt-mount-clients.md) para acessar o sistema de arquivos.
