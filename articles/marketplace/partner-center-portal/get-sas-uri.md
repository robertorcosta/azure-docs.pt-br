---
title: URI de assinatura de acesso compartilhado para imagens de VM-Azure Marketplace
description: Gere um URI de SAS (assinatura de acesso compartilhado) para seus discos rígidos virtuais (VHD) no Azure Marketplace.
author: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: mingshen
ms.openlocfilehash: b521a3a035044e2f0c1b625df19d265cfa35b49a
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857933"
---
# <a name="get-shared-access-signature-uri-for-your-vm-image"></a>Obtenha o URI de assinatura de acesso compartilhado para suas imagens VM

> [!IMPORTANT]
> Estamos movendo o gerenciamento de suas ofertas de máquina virtual do Azure de Portal do Cloud Partner para o Partner Center. Até que suas ofertas sejam migradas, siga as instruções em [obter URI de assinatura de acesso compartilhado para sua imagem de VM](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-get-sas-uri) para Portal do Cloud Partner gerenciar suas ofertas.

Este artigo descreve como gerar um URI (Uniform Resource Identifier) de SAS (assinatura de acesso compartilhado) para cada VHD (disco rígido virtual).

Durante o processo de publicação, você deve fornecer um URI para cada VHD associado aos seus planos. Esses planos foram mencionados anteriormente como SKUs ou unidades de manutenção de estoque. A Microsoft precisa de acesso a esses VHDs durante o processo de certificação. Você inserirá esse URI na guia **planos** no Partner Center.

Ao gerar URIs SAS para seus VHDs, siga estes requisitos:

* Há suporte para apenas os VHDs não gerenciados.
* Somente `List` as `Read` permissões e são necessárias. Não forneça acesso de gravação ou exclusão.
* A duração de acesso (data de expiração) deve ser no mínimo três semanas a partir de quando o URI de SAS é criado.
* Para proteger contra alterações de hora UTC, defina a data de início como um dia antes da data atual. Por exemplo, se a data atual for 6 de outubro de 2019, selecione 10/5/2019.

## <a name="generate-the-sas-address"></a>Gerar o endereço SAS

Há duas ferramentas comuns usadas para criar um endereço SAS (URL):

* **Microsoft Gerenciador de armazenamento** – ferramenta gráfica disponível para Windows, MacOS e Linux.
* **Microsoft Azure CLI** – recomendado para sistemas operacionais não Windows e ambientes de integração automatizados ou contínuos.

### <a name="use-microsoft-storage-explorer"></a>Usar o Microsoft Gerenciador de Armazenamento

1. Baixe e instale o [Gerenciador de armazenamento do Microsoft Azure](https://azure.microsoft.com/features/storage-explorer/).
2. Abra o Explorer e, no menu à esquerda, selecione **adicionar conta**. A caixa de diálogo **conectar ao armazenamento do Azure** é exibida.
3. Selecione **Adicionar uma conta do Azure** e, em seguida, **entrar**. Conclua as etapas necessárias para entrar em sua conta do Azure.
4. No painel do**Explorer** esquerdo, vá para suas **contas de armazenamento** e expanda este nó.
5. Clique com o botão direito do mouse no VHD e selecione **obter assinatura de acesso de compartilhamento**.
6. A caixa de diálogo **assinatura de acesso compartilhado** é exibida. Preencha os seguintes campos:

    * **Hora de início** – data de início da permissão para acesso VHD. Forneça uma data que é um dia antes da data atual.
    * **Hora de expiração** – data de expiração da permissão para acesso VHD. Forneça uma data que tenha pelo menos três semanas além da data atual.
    * **Permissões** – selecione as permissões de leitura e lista.
    * **Nível de contêiner** – marque a caixa de seleção **gerar URI de assinatura de acesso compartilhado no nível de contêiner** .

        :::image type="content" source="media/create-sas-uri-storage-explorer.png" alt-text="Ilustra a caixa de diálogo assinatura de acesso compartilhado":::

7. Para criar o URI SAS associado para este VHD, selecione **criar**. A caixa de diálogo é atualizada e mostra detalhes sobre essa operação.
8. Copie o **URI** e salve-o em um arquivo de texto em um local seguro.

    :::image type="content" source="media/create-sas-uri-shared-access-signature-details.png" alt-text="Ilustra a caixa detalhes da assinatura de acesso compartilhado":::

    Esse URI de SAS gerado é para o acesso em nível de contêiner. Para torná-lo específico, edite o arquivo de texto para adicionar o nome do VHD (próxima etapa).

9. Insira o nome do VHD após a cadeia de caracteres de VHDs no URI de SAS (inclua uma barra invertida). O URI de SAS final deve ter a seguinte aparência:

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`Por exemplo, se o nome do VDH for `TestRGVM2.vhd`, o URI de SAS resultante será:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

10. Repita essas etapas para cada VHD nos planos que você publicará.

### <a name="using-azure-cli"></a>Usando a CLI do Azure

1. Baixe e instale a [CLI do Microsoft Azure](https://azure.microsoft.com/documentation/articles/xplat-cli-install/). As versões estão disponíveis Windows. macOS e várias distribuições do Linux.
2. Crie um arquivo do PowerShell (extensão de arquivo. ps1), copie no código a seguir e salve-o localmente.

    ```PowerShell
    az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' --name <vhd-name> --permissions rl --start '<start-date>' --expiry '<expiry-date>'
    ```

3. Edite o arquivo para usar os valores de parâmetro a seguir. Forneça datas no formato UTC DateTime, como `2020-04-01T00:00:00Z`.

    * `<account-name>`– O nome da conta de armazenamento do Azure
    * `<account-key>`– Sua chave de conta de armazenamento do Azure
    * `<vhd-name>`– O nome do VHD
    * `<start-date>`– Data de início da permissão para acesso do VHD. Forneça uma data um dia antes da data atual.
    * `<expiry-date>`– Data de expiração da permissão para acesso do VHD. Forneça uma data pelo menos três semanas após a data atual.

    Este exemplo mostra os valores de parâmetro adequados (no momento da redação deste artigo):

    ```PowerShell
    az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ONc+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net' --name vhds --permissions rl --start '2020-04-01T00:00:00Z' --expiry '2021-04-01T00:00:00Z'
    ```

4. Salve as alterações.
5. Usando um dos seguintes métodos, execute este script com privilégios administrativos para criar uma **cadeia de conexão SAS** para acesso no nível do contêiner:

    * Execute o script a partir do console. No Windows, clique com o botão direito do mouse no script e selecione **Executar como administrador**.
    * Execute o script de um editor de script do PowerShell, como [ISE do Windows PowerShell](https://docs.microsoft.com/powershell/scripting/components/ise/introducing-the-windows-powershell-ise). Esta tela mostra a criação de uma cadeia de conexão SAS dentro deste editor:

     :::image type="content" source="media/create-sas-uri-power-shell-ise.png" alt-text="Ilustra a criação de uma cadeia de conexão SAS com ISE do Windows PowerShell":::

6. Copie a cadeia de conexão SAS e salve-a em um arquivo de texto em um local seguro. Edite esta cadeia de caracteres para adicionar as informações de local do VHD para criar o URI de SAS final.
7. Na portal do Azure, vá para o armazenamento de BLOBs que inclui o VHD associado ao novo URI.
8. Copie a URL do **ponto de extremidade do serviço blob**, conforme mostrado na captura de tela a seguir

    :::image type="content" source="media/create-sas-uri-blob-endpoint.png" alt-text="Ilustra o ponto de extremidade do serviço blob":::

9. Edite o arquivo de texto com a cadeia de caracteres de conexão SAS da etapa 6. Crie o URI de SAS completo usando este formato:

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

    Por exemplo, se o nome do VHD for `TestRGVM2.vhd`, o URI de SAS será:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

Repita essas etapas para cada VHD em SKUs do qual você planeja publicar.

## <a name="verify-the-sas-uri"></a>Verifique o URI SAS

Examine cada URI de SAS criado usando a seguinte lista de verificação para verificar se:

* O URI tem esta aparência:`<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`
* O URI inclui o nome de arquivo da imagem VHD, incluindo a extensão de nome de arquivo ". vhd".
* `sp=rl`aparece próximo ao meio do URI. Essa cadeia de caracteres `Read` mostra `List` que o e o acesso são especificados.
* Quando `sr=c` aparece, isso significa que o acesso no nível de contêiner é especificado.
* Copie e cole o URI em um navegador para baixar o blob (você pode cancelar a operação antes que o download seja concluído).

## <a name="next-step"></a>Próxima etapa

Se você tiver dificuldades para criar um URI de SAS, consulte [problemas comuns de URL de SAS](https://docs.microsoft.com/azure/marketplace/partner-center-portal/common-sas-uri-issues). Caso contrário, salve as URI(s) a SAS para um local seguro para uso posterior. Você precisará dela para publicar sua oferta de VM no Partner Center.

* [Criar uma oferta de máquina virtual do Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer)
