---
title: URI de assinatura de acesso compartilhado para imagens de VM – Azure Marketplace
description: Gere um URI de SAS (assinatura de acesso compartilhado) para seus VHDs (discos rígidos virtuais) no Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: anbene
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 569d9ce04422230c1d6b9ebb0957f01c320c5ba0
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86536140"
---
# <a name="get-shared-access-signature-uri-for-your-vm-image"></a>Obtenha o URI de assinatura de acesso compartilhado para suas imagens VM

Este artigo descreve como gerar um URI (Uniform Resource Identifier) de SAS (assinatura de acesso compartilhado) para cada VHD (disco rígido virtual).

Durante o processo de publicação, você deve fornecer um URI para cada VHD associado aos seus planos (anteriormente chamados de SKUs). A Microsoft precisa de acesso a esses VHDs durante o processo de certificação. Você inserirá esse URI na guia **Planos** do Partner Center.

Ao gerar URIs de SAS para seus VHDs, siga estes requisitos:

* Há suporte para apenas os VHDs não gerenciados.
* Somente as permissões `List` e `Read` são necessárias. Não forneça acesso para gravação ou exclusão.
* A duração de acesso (data de expiração) deve ser no mínimo três semanas a partir de quando o URI de SAS é criado.
* Para se proteger contra alterações da hora de UTC, defina a data de início para um dia antes da data atual. Por exemplo, se a data atual for 6 de outubro de 2019, selecione 5/10/2019.

## <a name="generate-the-sas-address"></a>Gerar o endereço de SAS

Há duas ferramentas comuns usadas para criar um endereço de SAS (URL):

* **Microsoft Storage Explorer** – Ferramenta gráfica disponível para Windows, macOS e Linux.
* **CLI do Microsoft Azure** – Recomendado para ambientes de integração contínua ou automatizada e sistemas operacionais não Windows.

### <a name="use-microsoft-storage-explorer"></a>Usar o Gerenciador de Armazenamento do Microsoft Azure

1. Baixe e instale o [Gerenciador de Armazenamento do Microsoft Azure](https://azure.microsoft.com/features/storage-explorer/).
2. Abra o Explorer e, no menu à esquerda, selecione **Adicionar conta**. A caixa de diálogo **Conectar-se ao Armazenamento do Microsoft Azure** aparece.
3. Selecione **Adicionar uma Conta do Azure** e **Entrar**. Conclua as etapas necessárias para entrar em sua conta do Azure.
4. No painel **Explorer** à esquerda, navegue até as **Contas de Armazenamento** e expanda esse nó.
5. Clique com o botão direito do mouse no VHD e selecione **Obter Assinatura de Acesso de Compartilhado**.
6. A caixa de diálogo **Assinatura de Acesso Compartilhado** aparece. Preencha os seguintes campos:

    * **Hora de início** – Data de início de permissão para acesso ao VHD. Forneça uma data que é um dia antes da data atual.
    * **Hora de expiração** – Data de expiração de permissão para acesso ao VHD. Forneça uma data de pelo menos três semanas após a data atual.
    * **Permissões** – Selecione as permissões de leitura e listagem.
    * **Nível de contêiner** – Marque a caixa de seleção **Gerar URI de assinatura de acesso compartilhado no nível de contêiner**.

        :::image type="content" source="media/create-sas-uri-storage-explorer.png" alt-text="Ilustra a caixa de diálogo Assinatura de Acesso Compartilhado":::.

7. Para criar o URI de SAS associado a este VHD, selecione **Criar**. A caixa de diálogo é atualizada e mostra detalhes sobre a operação.
8. Copie o **URI** e salve-o em um arquivo de texto em um local seguro.

    :::image type="content" source="media/create-sas-uri-shared-access-signature-details.png" alt-text="Ilustra a caixa de detalhes Assinatura de Acesso Compartilhado":::

    Esse URI de SAS é gerado para o acesso de nível de contêiner. Para torná-lo específico, edite o arquivo de texto para adicionar o nome do VHD (próxima etapa).

9. Insira seu nome de VHD após a cadeia de caracteres vhds no URI de SAS (incluir uma barra /). O URI de SAS final deve ficar assim:

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>` Por exemplo, se o nome do VDH fosse `TestRGVM2.vhd`, o URI de SAS resultante seria:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

10. Repita essas etapas para cada VHD nos planos que você vai publicar.

### <a name="using-azure-cli"></a>Usando a CLI do Azure

1. Faça o download e instale o [CLI do Microsoft Azure](https://azure.microsoft.com/documentation/articles/xplat-cli-install/). As versões estão disponíveis Windows. macOS e várias distribuições do Linux.
2. Crie um arquivo PowerShell (extensão de arquivo .ps1), copie o código a seguir e salve-o localmente.

    ```PowerShell
    az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' --name <vhd-name> --permissions rl --start '<start-date>' --expiry '<expiry-date>'
    ```

3. Edite o arquivo para usar os seguintes valores de parâmetro. Forneça datas no formato DateTime UTC, como `2020-04-01T00:00:00Z`.

    * `<account-name>` – O nome da conta de armazenamento do Azure.
    * `<account-key>` – Sua chave de conta de armazenamento do Azure.
    * `<vhd-name>` – Nome do VHD.
    * `<start-date>` – Data inicial de permissão para acesso VHD. Forneça uma data um dia antes da data atual.
    * `<expiry-date>` – Data de expiração da permissão para acesso a VHD. Forneça uma data de pelo menos três semanas após a data atual.

    Este exemplo mostra os valores de parâmetro adequado (no momento da redação deste artigo):

    ```PowerShell
    az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ONc+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net' --name vhds --permissions rl --start '2020-04-01T00:00:00Z' --expiry '2021-04-01T00:00:00Z'
    ```

4. Salve as alterações.
5. Usando um dos seguintes métodos, execute este script com privilégios administrativos para criar uma **Cadeia de caracteres de conexão SAS** para acesso do nível de contêiner:

    * Execute o script a partir do console. No Windows, clique com o botão direito do mouse no script e selecione **Executar como administrador**.
    * Execute o script em um editor de script do PowerShell, como [ISE do Windows PowerShell](https://docs.microsoft.com/powershell/scripting/components/ise/introducing-the-windows-powershell-ise). Esta tela mostra a criação de uma cadeia de conexão de SAS dentro neste editor:

     :::image type="content" source="media/create-sas-uri-power-shell-ise.png" alt-text="Ilustra a criação de uma cadeia de conexão de SAS com o ISE do Windows PowerShell":::

6. Copie a cadeia de caracteres de conexão de SAS e salve-o em um arquivo de texto em um local seguro. Edite essa cadeia de caracteres para adicionar as informações de localização do VHD para criar o URI de SAS final.
7. No portal do Azure, navegue até o armazenamento de Blobs que inclui o VHD associado ao novo URI.
8. Copie a URL do **ponto de extremidade do serviço Blob**, conforme mostrado na captura de tela a seguir

    :::image type="content" source="media/create-sas-uri-blob-endpoint.png" alt-text="Ilustra o ponto de extremidade do serviço Blob":::

9. Edite o arquivo de texto com a cadeia de caracteres de conexão SAS da etapa 6. Crie o URI de SAS completo usando este formato:

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

    Por exemplo, se o nome do VHD for `TestRGVM2.vhd`, o URI de SAS será:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

Repita essas etapas para cada VHD nos planos que você vai publicar.

## <a name="verify-the-sas-uri"></a>Verifique o URI SAS

Use a seguinte lista de verificação para analisar cada URI de SAS criado:

* O URI tem esta aparência: `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`
* O URI inclui seu nome de arquivo de imagem do VHD, incluindo a extensão de nome de arquivo ".vhd".
* `sp=rl` aparece próximo ao meio do URI. Essa cadeia de caracteres mostra que o acesso `Read` e `List` é especificado.
* Quando `sr=c` aparece, isso significa que o acesso de nível de contêiner é especificado.
* Copie e cole o URI em um navegador para fazer um teste de download do blob (você pode cancelar a operação antes que o download seja concluído).

## <a name="next-step"></a>Próxima etapa

Se você tiver dificuldades para criar um URI de SAS, consulte [Problemas comuns com a URL de SAS](common-sas-uri-issues.md). Caso contrário, salve as URI(s) a SAS para um local seguro para uso posterior. Você precisará delas para publicar sua oferta de VM no Partner Center.

* [Criar uma oferta de máquina virtual do Azure](azure-vm-create-offer.md)
