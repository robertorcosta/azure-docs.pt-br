---
title: Obtenha assinatura de acesso compartilhado URI para sua imagem de VM | Mercado Azure
description: Este artigo explica como obter o URI de assinatura de acesso compartilhado (SAS) para cada disco rígido virtual (VHD).
author: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: mingshen
ms.openlocfilehash: c226d35647e4a5a2b1d583dd6328bfb73dae2a1c
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732633"
---
# <a name="get-shared-access-signature-uri-for-your-vm-image"></a>Obtenha o URI de assinatura de acesso compartilhado para suas imagens VM

> [!IMPORTANT]
> Estamos mudando o gerenciamento das ofertas da Sua Máquina Virtual Do Portal de Parceiros em Nuvem para o Partner Center. Até que suas ofertas sejam migradas, siga as instruções em [Obter assinatura de acesso compartilhado URI para sua imagem VM](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-get-sas-uri) para Cloud Partner Portal para gerenciar suas ofertas.

Este artigo descreve como gerar um identificador de recurso uniforme (Uri) de assinatura de acesso compartilhado (Uri) para cada disco rígido virtual (VHD).

Durante o processo de publicação, você deve fornecer um URI para cada VHD associado aos seus planos. Esses planos foram previamente referidos como SKUs, ou unidades de manutenção de estoque. A Microsoft precisa de acesso a esses VHDs durante o processo de certificação. Você entrará neste URI na guia **Planos** no Partner Center.

Ao gerar URIs SAS para seus VHDs, siga estes requisitos:

* Há suporte para apenas os VHDs não gerenciados.
* Apenas `List` `Read` e permissões são necessárias. Não forneça acesso gravar ou excluir.
* A duração de acesso (data de expiração) deve ser no mínimo três semanas a partir de quando o URI de SAS é criado.
* Para proteger contra alterações de tempo da UTC, defina a data de início para um dia antes da data atual. Por exemplo, se a data atual for 6 de outubro de 2019, selecione 5/10/2019.

## <a name="generate-the-sas-address"></a>Gerar o endereço SAS

Existem duas ferramentas comuns usadas para criar um endereço SAS (URL):

* **Microsoft Storage Explorer** – Ferramenta gráfica disponível para Windows, macOS e Linux.
* **Microsoft Azure CLI** – Recomendado para sistemas operacionais não Windows e ambientes de integração automatizados ou contínuos.

### <a name="use-microsoft-storage-explorer"></a>Use Microsoft Storage Explorer

1. Baixe e instale [o Microsoft Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).
2. Abra o explorador e, no menu esquerdo, **selecione Adicionar conta**. A caixa de diálogo **Conectar ao armazenamento do Azure** é exibida.
3. Selecione **Adicionar uma conta azure** e, em seguida, **fazer login**. Complete as etapas necessárias para entrar na sua conta do Azure.
4. No painel esquerdo**do Explorer,** vá para suas **contas de armazenamento** e expanda este nó.
5. Clique com o botão direito do mouse no seu VHD e, em seguida, **selecione Obter assinatura de acesso ao compartilhamento**.
6. A caixa de diálogo **Assinatura de acesso compartilhado** é exibida. Preencha os seguintes campos:

    * **Tempo de início** – Data de início da permissão para acesso ao VHD. Forneça uma data que é um dia antes da data atual.
    * **Prazo de validade** – Data de validade da permissão para acesso ao VHD. Forneça uma data que seja pelo menos três semanas além da data atual.
    * **Permissões** – Selecione as permissões de leitura e lista.
    * **Nível de contêiner** – Verifique a caixa de seleção **URI de assinatura de acesso compartilhado em nível de contêiner.**

        :::image type="content" source="media/create-sas-uri-storage-explorer.png" alt-text="Ilustra a caixa de diálogo Assinatura de Acesso Compartilhado":::

7. Para criar o URI SAS associado para este VHD, selecione **Criar**. A caixa de diálogo atualiza e mostra detalhes sobre esta operação.
8. Copie o **URI** e salve-o em um arquivo de texto em um local seguro.

    :::image type="content" source="media/create-sas-uri-shared-access-signature-details.png" alt-text="Ilustra a caixa de detalhes da Assinatura de Acesso Compartilhado":::

    Este Uri SAS gerado é para acesso em nível de contêiner. Para torná-lo específico, edite o arquivo de texto para adicionar o nome VHD (próximo passo).

9. Insira o nome VHD após a seqüência de vhds no Uri SAS (inclua uma barra para a frente). O URI final do SAS deve ser assim:

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`Por exemplo, se o nome do `TestRGVM2.vhd`VDH for, então o URI SAS resultante seria:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

10. Repita estas etapas para cada VHD nos planos que você publicará.

### <a name="using-azure-cli"></a>Usando a CLI do Azure

1. Baixe e instale [o Microsoft Azure CLI](https://azure.microsoft.com/documentation/articles/xplat-cli-install/). As versões estão disponíveis Windows. macOS e várias distribuições do Linux.
2. Crie um arquivo PowerShell (extensão de arquivo.ps1), copie no código a seguir e salve-o localmente.

    ```PowerShell
    az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' --name <vhd-name> --permissions rl --start '<start-date>' --expiry '<expiry-date>'
    ```

3. Edite o arquivo para usar os seguintes valores de parâmetro. Fornecer datas no formato utc `2020-04-01T00:00:00Z`data-hora, tais como .

    * `<account-name>`– O nome da sua conta de armazenamento Do Zure
    * `<account-key>`– A chave da conta de armazenamento do Azure
    * `<vhd-name>`– Seu nome VHD
    * `<start-date>`– Data de início da permissão para acesso ao VHD. Forneça uma data um dia antes da data atual.
    * `<expiry-date>`– Data de validade da permissão para acesso a VHD. Forneça uma data pelo menos três semanas após a data atual.

    Este exemplo mostra valores parâmetros adequados (no momento desta escrita):

    ```PowerShell
    az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ONc+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net' --name vhds --permissions rl --start '2020-04-01T00:00:00Z' --expiry '2021-04-01T00:00:00Z'
    ```

4. Salve as alterações.
5. Usando um dos seguintes métodos, execute este script com privilégios administrativos para criar uma **seqüência de conexão SAS** para acesso em nível de contêiner:

    * Execute o script a partir do console. No Windows, clique com o botão direito do mouse no script e **selecione Executar como administrador**.
    * Execute o script de um editor de script saudoso do [Windows PowerShell ISE](https://docs.microsoft.com/powershell/scripting/components/ise/introducing-the-windows-powershell-ise). Esta tela mostra a criação de uma seqüência de conexão SAS dentro deste editor:

     :::image type="content" source="media/create-sas-uri-power-shell-ise.png" alt-text="Ilustra a criação de uma seqüência de conexão SAS com o Windows PowerShell ISE":::

6. Copie a seqüência de conexão SAS e salve-a em um arquivo de texto em um local seguro. Edite esta seqüência para adicionar as informações de localização do VHD para criar o URI SAS final.
7. No portal Azure, vá para o armazenamento blob que inclui o VHD associado ao novo URI.
8. Copie a URL do ponto final do **serviço Blob,** conforme mostrado na captura de tela a seguir

    :::image type="content" source="media/create-sas-uri-blob-endpoint.png" alt-text="Ilustra o ponto final do serviço Blob":::

9. Edite o arquivo de texto com a cadeia de caracteres de conexão SAS da etapa 6. Crie o URI SAS completo usando este formato:

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

    Por exemplo, se o nome `TestRGVM2.vhd`do VHD for, o Uri SAS será:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

Repita essas etapas para cada VHD em SKUs do qual você planeja publicar.

## <a name="verify-the-sas-uri"></a>Verifique o URI SAS

Revise cada URI SAS criado usando a lista de verificação a seguir para verificar se:

* O URI é assim:`<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`
* O URI inclui o nome do arquivo de imagem VHD, incluindo a extensão de nome de arquivo ".vhd".
* `sp=rl`aparece perto do meio do seu URI. Esta seqüência mostra isso `Read` e `List` o acesso é especificado.
* Quando `sr=c` aparece, isso significa que o acesso ao nível do contêiner é especificado.
* Copie e cole o URI em um navegador para testar o download da bolha (você pode cancelar a operação antes que o download seja concluído).

## <a name="next-step"></a>Próxima etapa

Se você tiver dificuldades em criar um Uri SAS, consulte [problemas comuns de URL SAS](https://docs.microsoft.com/azure/marketplace/partner-center-portal/common-sas-uri-issues). Caso contrário, salve as URI(s) a SAS para um local seguro para uso posterior. Você precisará dele para publicar sua oferta de VM no Partner Center.

* [Criar uma oferta de máquina virtual do Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer)
