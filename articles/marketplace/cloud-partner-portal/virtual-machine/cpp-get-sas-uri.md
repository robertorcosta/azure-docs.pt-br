---
title: Obter o URI da assinatura de acesso compartilhado para sua imagem VM baseada em Microsoft Azure | Azure Marketplace
description: Explica como obter o URI de assinatura de acesso compartilhado (SAS) para a sua imagem VM.
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
ms.service: marketplace
ms.topic: article
ms.date: 10/19/2018
ms.author: pabutler
ms.openlocfilehash: c242fbcd19187abb608ca80a49d04dae195bd7c6
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72374363"
---
# <a name="get-shared-access-signature-uri-for-your-vm-image"></a>Obtenha o URI de assinatura de acesso compartilhado para suas imagens VM

Durante o processo de publicação, você deve fornecer um identificador de recursos uniformes (URI) para cada disco rígido virtual (VHD) associado às suas SKUs. A Microsoft precisa de acesso a esses VHDs durante o processo de certificação. Este artigo explica como gerar uma assinatura de acesso compartilhado (SAS) URI para cada VHD. Você vai inserir esse URI na guia **SKUs** no Portal do Cloud Partner. 

Ao gerar URI SAS para seus VHDs, atenda aos seguintes requisitos:

- Há suporte para apenas os VHDs não gerenciados.
- `List` e `Read` permissões são suficientes. *Não* fornecer `Write` ou `Delete` acessar.
- A duração de acesso (*data de expiração*) deve ser no mínimo três semanas a partir de quando o URI de SAS é criado.
- Para se proteger contra variações da hora de UTC, defina a data de início para um dia antes da data atual. Por exemplo, se a data atual for 6 de outubro de 2014, selecione 5/10/2014.

## <a name="generate-the-sas-url"></a>Gere novamente a URL SAS

A URL de SAS pode ser gerada de duas maneiras comuns usando as seguintes ferramentas:

-   Microsoft Storage Explorer - ferramenta gráfica disponível para Windows, macOS e Linux
-   CLI do Azure - recomendado para ambientes de integração contínua ou automatizada e sistemas operacionais Windows


### <a name="azure-cli"></a>Azure CLI

Use as etapas a seguir para gerar um URI de SAS com a CLI do Azure.

1. Fazer o download e instalar o [CLI do Azure](https://azure.microsoft.com/documentation/articles/xplat-cli-install/).  As versões estão disponíveis Windows. macOS e várias distribuições do Linux. 
2. Crie um arquivo PowerShell (`.ps1` extensão de arquivo), copie o código a seguir, em seguida, salvá-o localmente.

   ``` powershell
   az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' --name <vhd-name> --permissions rl --start '<start-date>' --expiry '<expiry-date>'
   ```
    
3. Edite o arquivo para fornecer os seguintes valores de parâmetro.  Datas devem ser fornecidas no formato de data e hora UTC, por exemplo `2016-10-25T00:00:00Z`.
   - `<account-name>` -O nome da conta de armazenamento do Azure
   - `<account-key>` -Sua chave de conta de armazenamento do Azure
   - `<vhd-name>` - nome do VHD
   - `<start-date>` - Data inicial de permissão para acesso VHD. Forneça uma data um dia antes da data atual. 
   - `<expiry-date>` -Data de expiração da permissão para acesso a VHD.  Forneça uma data de pelo menos três semanas após a data atual. 
 
   O exemplo a seguir mostra os valores de parâmetro adequado (no momento da redação deste artigo).

   ``` powershell
       az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ONc+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net' --name vhds --permissions rl --start '2017-11-06T00:00:00Z' --expiry '2018-08-20T00:00:00Z'
   ```
 
4. Salve as alterações para este script do PowerShell.
5. Execute este script usando os privilégios administrativos, para gerar uma *Cadeia de caracteres de conexão SAS* para acesso do nível de contêiner.  Você também pode usar duas abordagens básicas:
   - Execute o script a partir do console.  Por exemplo, no Windows, clique em script e selecione **Executar como administrador**.
   - Execute o script em um editor de script do PowerShell, como [Windows PowerShell ISE](https://docs.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise), usando os privilégios administrativos. 
     O exemplo a seguir demonstra uma cadeia de caracteres de conexão de SAS que está sendo gerada dentro deste editor. 

     ![Geração de URI de SAS no ISE do PowerShell](./media/publishvm_032.png)

6. Copie a cadeia de caracteres de conexão SAS resultante e salvá-o em um arquivo de texto em um local seguro.  Você editará essa cadeia de caracteres para adicionar as informações de localização do VHD associadas a ele para criar o URI de SAS final. 
7. No portal do Azure, navegue até o armazenamento de blob que contém o VHD associado ao URI recém-gerado.
8. Copie o valor da URL do **ponto de extremidade de serviço Blob**, conforme mostrado abaixo.

    ![Ponto de extremidade de serviço Blob no portal do Azure](./media/publishvm_033.png)

9. Edite o arquivo de texto com a cadeia de caracteres de conexão SAS da etapa 6.  Você construirá o URI SAS completo usando o seguinte formato:

    `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`

    Por exemplo, se o nome do VDH é `TestRGVM2.vhd`, em seguida, o URI de SAS resultante seria:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

Repita essas etapas para cada VHD em SKUs do qual você planeja publicar.


### <a name="microsoft-storage-explorer"></a>Microsoft Storage Explorer

Use as etapas a seguir para gerar um URI de SAS com o Gerenciador de Armazenamento do Microsoft Azure.

1. Baixe e instale o [Gerenciador de Armazenamento do Microsoft Azure](https://azure.microsoft.com/features/storage-explorer/).
2. Abra o explorer e, na barra de menus à esquerda, clique no ícone **Adicionar conta**.  A caixa de diálogo **Conectar-se ao Armazenamento do Microsoft Azure** é exibida.
3. Selecione **Adicionar uma Conta do Azure** e clique em **Entrar**.  Continue as etapas necessárias para entrar em sua conta do Azure.
4. No painel do **Explorer**, navegue até suas **Contas de Armazenamento** e expanda esse nó.
5. Clique com botão direito no seu VHD e selecione **Obter Assinatura de Acesso de Compartilhamento** no menu de contexto. 

    ![Obter item SAS no Gerenciador do Azure](./media/publishvm_034.png)

6. A caixa de diálogo **Assinatura de Acesso Compartilhado** é exibida. Insira os valores dos campos a seguir:
   - **Hora de início** - Data de início de permissão para acesso VHD. Forneça uma data que é um dia antes da data atual.
   - **Hora de expiração** - data de expiração de permissão para acesso ao VHD.  Forneça uma data de pelo menos três semanas após a data atual.
   - **Permissões** - Selecione o `Read` e `List` permissões. 

     ![Caixa de diálogo SAS no Gerenciador do Azure](./media/publishvm_035.png)

7. Clique em **Criar** para criar o URI de SAS associado a este VHD.  Agora, a caixa de diálogo exibe detalhes a respeito dessa operação. 
8. Copie o valor da **URL** e salve-o em um arquivo de texto em um local seguro. 

    ![Criar URI de SAS no Gerenciador do Azure](./media/publishvm_036.png)

    Essa URL SAS é gerada para o acesso de nível de contêiner.  Para torná-lo específico, o nome do VHD associado deve ser adicionado a ele.

9. Edite o arquivo de texto. Insira seu nome de VHD após a `vhds` cadeia de caracteres em URL da SAS (incluir uma barra à esquerda).  O URI de SAS final deve estar no formato:

    `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`

    Por exemplo, se o nome do VDH é `TestRGVM2.vhd`, em seguida, o URI de SAS resultante seria:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

Repita essas etapas para cada VHD em SKUs do qual você planeja publicar.


## <a name="verify-the-sas-uri"></a>Verifique o URI SAS

Revise e verifique se que cada URI SAS gerado usando a seguinte lista de verificação.  Verifique se:
- O URI está no formato:       `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`
- O URI contém seu nome de arquivo de imagem do VHD, incluindo a extensão de nome de arquivo ". vhd".
- Em direção à parte central do URI, `sp=rl` é exibida. Essa cadeia de caracteres indica que o acesso `Read` e `List` é especificado.
- Após esse ponto, `sr=c` também será exibido. Essa cadeia de caracteres indica que o acesso de nível de contêiner é especificado.
- Copie e cole o URI em um navegador para começar a fazer o download do blob associado.  (você pode cancelar a operação antes que o download seja concluído)


## <a name="next-steps"></a>Próximos passos

Se você estiver tendo dificuldades para gerar um URI de SAS, consulte [URL de SAS comum emite](./cpp-common-sas-url-issues.md).  Caso contrário, salve as URI(s) a SAS para um local seguro para uso posterior. Será necessário [publicar sua oferta VM](./cpp-publish-offer.md) no Portal do Cloud Partner.
