---
title: Como importar uma nova atualização | Microsoft Docs
description: Guia de How-To para importar uma nova atualização para a atualização de dispositivo do Hub IoT para o Hub IoT.
author: andbrown
ms.author: andbrown
ms.date: 2/11/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: d8757f3076f784576f95bbdfc30abf578446c776
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101661802"
---
# <a name="import-new-update"></a>Importar nova atualização
Saiba como importar uma nova atualização para a atualização de dispositivo para o Hub IoT.

## <a name="prerequisites"></a>Pré-requisitos

* [Acesso a um hub IOT com a atualização de dispositivo para o Hub IOT habilitado](create-device-update-account.md). É recomendável que você use uma camada S1 (padrão) ou superior para o Hub IoT. 
* Um dispositivo IoT (ou simulador) provisionado para atualização de dispositivo no Hub IoT.
   * Se estiver usando um dispositivo real, você precisará de um arquivo de imagem de atualização para atualização de imagem ou [arquivo de manifesto apt](device-update-apt-manifest.md) para atualização de pacote.
* [PowerShell 5](https://docs.microsoft.com/powershell/scripting/install/installing-powershell) ou posterior.
* Navegadores compatíveis:
  * [Microsoft Edge](https://www.microsoft.com/edge)
  * Google Chrome

> [!NOTE]
> Alguns dados enviados para esse serviço podem ser processados em uma região fora da região em que essa instância foi criada.

## <a name="create-device-update-import-manifest"></a>Criar manifesto de importação de atualização de dispositivo

1. Verifique se o arquivo de imagem de atualização ou o arquivo de manifesto APT está localizado em um diretório acessível do PowerShell.

2. Clonar [atualização de dispositivo para o repositório do Hub IOT](https://github.com/azure/iot-hub-device-update)ou baixá-lo como um arquivo. zip para um local acessível do PowerShell (depois que o arquivo zip for baixado, clique com o botão direito do mouse para obter a `Properties`  >  `General` guia > faça check- `Unblock` in na `Security` seção para evitar prompts de aviso de segurança do PowerShell).

3. No PowerShell, navegue até o `tools/AduCmdlets` diretório e execute:

    ```powershell
    Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process
    Import-Module .\AduUpdate.psm1
    ```

4. Execute os comandos a seguir substituindo os valores de parâmetro de exemplo para gerar um manifesto de importação, um arquivo JSON que descreve a atualização:
    ```powershell
    $compat = New-AduUpdateCompatibility -DeviceManufacturer 'deviceManufacturer' -DeviceModel 'deviceModel'

    $importManifest = New-AduImportManifest -Provider 'updateProvider' -Name 'updateName' -Version 'updateVersion' `
                                            -UpdateType 'updateType' -InstalledCriteria 'installedCriteria' `
                                            -Compatibility $compat -Files 'updateFilePath(s)'

    $importManifest | Out-File '.\importManifest.json' -Encoding UTF8
    ```

    Para referência rápida, aqui estão alguns exemplos de valores para os parâmetros acima. Para obter a documentação completa, consulte o esquema de manifesto de importação completa abaixo.

    | Parâmetro | Descrição |
    | --------- | ----------- |
    | deviceManufacturer | Fabricante do dispositivo ao qual a atualização é compatível, por exemplo, contoso
    | deviceModel | Modelo do dispositivo ao qual a atualização é compatível, por exemplo, torradeira
    | updateprovider | Parte do provedor da identidade de atualização, por exemplo, fabrikam
    | updatename | Nome parte da identidade de atualização, por exemplo, ImageUpdate
    | updateVersion | Versão de atualização, por exemplo, 2,0
    | updateType | <ul><li>Especificar `microsoft/swupdate:1` para atualização de imagem</li><li>Especificar `microsoft/apt:1` para a atualização do pacote</li></ul>
    | installedCriteria | <ul><li>Especificar o valor de SWVersion para o `microsoft/swupdate:1` tipo de atualização</li><li>Especifique o valor recomendado para o `microsoft/apt:1` tipo de atualização.
    | updateFilePath (s) | Caminho para os arquivos de atualização no computador

    Esquema de manifesto de importação completa

    | Nome | Tipo | Descrição | Restrições |
    | --------- | --------- | --------- | --------- |
    | UpdateID | Objeto `UpdateId` | Atualizar identidade. |
    | UpdateType | string | Tipo de atualização: <ul><li>Especifique `microsoft/apt:1` ao executar uma atualização baseada em pacote usando o agente de referência.</li><li>Especifique `microsoft/swupdate:1` ao executar uma atualização baseada em imagem usando o agente de referência.</li><li>Especifique `microsoft/simulator:1` ao usar o simulador de agente de exemplo.</li><li>Especifique um tipo personalizado se estiver desenvolvendo um agente personalizado.</li></ul> | <ul><li>Formato: `{provider}/{type}:{typeVersion}`</li><li>Máximo de 32 caracteres no total</li></ul> |
    | InstalledCriteria | string | Cadeia de caracteres interpretada pelo agente para determinar se a atualização foi aplicada com êxito:  <ul><li>Especifique o **valor** de SWVersion para o tipo de atualização `microsoft/swupdate:1` .</li><li>Especifique `{name}-{version}` para o tipo de atualização `microsoft/apt:1` , de qual nome e versão são obtidos do arquivo apt.</li><li>Especifique o hash do arquivo de atualização para o tipo de atualização `microsoft/simulator:1` .</li><li>Especifique uma cadeia de caracteres personalizada se estiver desenvolvendo um agente personalizado.</li></ul> | Máximo de 64 caracteres |
    | Compatibilidade | Matriz de `CompatibilityInfo` objetos | Informações de compatibilidade do dispositivo compatível com esta atualização. | Máximo de 10 itens |
    | CreatedDateTime | data/hora | Data e hora em que a atualização foi criada. | Formato de data e hora ISO 8601 delimitado, em UTC |
    | ManifestVersion | string | Importar versão do esquema de manifesto. Especifique `2.0` , que será compatível com `urn:azureiot:AzureDeviceUpdateCore:1` interface e `urn:azureiot:AzureDeviceUpdateCore:4` interface.</li></ul> | Precisa ser `2.0` |
    | Arquivos | Matriz de `File` objetos | Atualizar arquivos de conteúdo | Máximo de 5 arquivos |

Observação: todos os campos são obrigatórios.

## <a name="review-generated-import-manifest"></a>Examinar o manifesto de importação gerado

Exemplo:
```json
{
  "updateId": {
    "provider": "Microsoft",
    "name": "Toaster",
    "version": "2.0"
  },
  "updateType": "microsoft/swupdate:1",
  "installedCriteria": "5",
  "compatibility": [
    {
      "deviceManufacturer": "Fabrikam",
      "deviceModel": "Toaster"
    },
    {
      "deviceManufacturer": "Contoso",
      "deviceModel": "Toaster"
    }
  ],
  "files": [
    {
      "filename": "file1.json",
      "sizeInBytes": 7,
      "hashes": {
        "sha256": "K2mn97qWmKSaSaM9SFdhC0QIEJ/wluXV7CoTlM8zMUo="
      }
    },
    {
      "filename": "file2.zip",
      "sizeInBytes": 11,
      "hashes": {
        "sha256": "gbG9pxCr9RMH2Pv57vBxKjm89uhUstD06wvQSioLMgU="
      }
    }
  ],
  "createdDateTime": "2020-10-08T03:32:52.477Z",
  "manifestVersion": "2.0"
}
```

## <a name="import-update"></a>Importar atualização

1. Faça logon no [portal do Azure](https://portal.azure.com) e navegue até o Hub IOT com a atualização do dispositivo.

2. No lado esquerdo da página, selecione "atualizações de dispositivo" em "gerenciamento de dispositivo automático".

   :::image type="content" source="media/import-update/import-updates-3.png" alt-text="Importar atualizações" lightbox="media/import-update/import-updates-3.png":::

3. Você verá várias guias na parte superior da tela. Selecione a guia Atualizações.

   :::image type="content" source="media/import-update/updates-tab.png" alt-text="Atualizações" lightbox="media/import-update/updates-tab.png":::

4. Selecione "+ importar nova atualização" abaixo do cabeçalho "pronto para implantar".

   :::image type="content" source="media/import-update/import-new-update-2.png" alt-text="Importar nova atualização" lightbox="media/import-update/import-new-update-2.png":::

5. Selecione o ícone de pasta ou caixa de texto em "selecionar um arquivo de manifesto de importação". Você verá uma caixa de diálogo Seletor de arquivos. Selecione o manifesto de importação criado anteriormente usando o cmdlet do PowerShell. Em seguida, selecione o ícone de pasta ou caixa de texto em "selecionar um ou mais arquivos de atualização". Você verá uma caixa de diálogo Seletor de arquivos. Selecione o arquivo (s) de atualização.

   :::image type="content" source="media/import-update/select-update-files.png" alt-text="Selecionar arquivos de atualização" lightbox="media/import-update/select-update-files.png":::

6. Selecione o ícone de pasta ou caixa de texto em "selecionar um contêiner de armazenamento". Em seguida, selecione a conta de armazenamento apropriada. O contêiner de armazenamento é usado para preparar temporariamente os arquivos de atualização.

   :::image type="content" source="media/import-update/storage-account.png" alt-text="Conta de armazenamento" lightbox="media/import-update/storage-account.png":::

7. Se você já tiver criado um contêiner, poderá reutilizá-lo. (Caso contrário, selecione "+ contêiner" para criar um novo contêiner de armazenamento para atualizações.).  Selecione o contêiner que você deseja usar e clique em "selecionar".

   :::image type="content" source="media/import-update/container.png" alt-text="Selecionar contêiner" lightbox="media/import-update/container.png":::

8. Selecione "enviar" para iniciar o processo de importação.

   :::image type="content" source="media/import-update/publish-update.png" alt-text="Publicar atualização" lightbox="media/import-update/publish-update.png":::

9. O processo de importação é iniciado e a tela alterna para a seção "importar histórico". Selecione "atualizar" para exibir o progresso até que o processo de importação seja concluído (dependendo do tamanho da atualização, isso pode ser concluído em alguns minutos, mas pode levar mais tempo).

   :::image type="content" source="media/import-update/update-publishing-sequence-2.png" alt-text="Atualizar sequenciamento de importação" lightbox="media/import-update/update-publishing-sequence-2.png":::

10. Quando a coluna status indicar que a importação foi bem-sucedida, selecione o cabeçalho "pronto para implantar". Você deve ver a atualização importada na lista agora.

   :::image type="content" source="media/import-update/update-ready.png" alt-text="Status do Trabalho" lightbox="media/import-update/update-ready.png":::

## <a name="next-steps"></a>Próximas etapas

[Criar grupos](create-update-group.md)

[Saiba mais sobre os conceitos de importação](import-concepts.md)
