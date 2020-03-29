---
title: Armazenar blob de blocos nos dispositivos – Azure IoT Edge | Microsoft Docs
description: Entenda os recursos de hierarquica e tempo de vida, consulte as operações de armazenamento blob suportadas e conecte-se à sua conta de armazenamento blob.
author: kgremban
ms.author: kgremban
ms.reviewer: arduppal
ms.date: 12/13/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: bea00f429f31f2be62ee6a9c00f88873c595d94c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76509811"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge"></a>Armazenar dados na borda com o Armazenamento de Blobs do Azure no IoT Edge

O Azure Blob Storage no IoT Edge fornece uma solução de armazenamento de [blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) de bloco e [apêndice](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs) na borda. Um módulo de armazenamento blob em seu dispositivo IoT Edge se comporta como um serviço de blob Azure, exceto que as bolhas são armazenadas localmente em seu dispositivo IoT Edge. Você pode acessar suas bolhas usando os mesmos métodos de Armazenamento SDK do Azure ou chamadas de API blob que você já está acostumado. Este artigo explica os conceitos relacionados ao Azure Blob Storage no contêiner IoT Edge que executa um serviço blob em seu dispositivo IoT Edge.

Este módulo é útil em cenários:

* onde os dados precisam ser armazenados localmente até que possam ser processados ou transferidos para a nuvem. Esses dados podem ser vídeos, imagens, dados financeiros, dados hospitalares ou qualquer outro dado não estruturado.
* quando os dispositivos estão localizados em um lugar com conectividade limitada.
* quando você deseja processar os dados de forma eficiente localmente para obter acesso de baixa latência aos dados, de forma que você possa responder a emergências o mais rápido possível.
* quando você quiser reduzir os custos de largura de banda e evitar transferir terabytes de dados para a nuvem. Você pode processar os dados localmente e enviar apenas os dados processados para a nuvem.

Assista ao vídeo para introdução rápida
> [!VIDEO https://www.youtube.com/embed/xbwgMNGB_3Y]

Este módulo vem com **recursos de dispositivoToCloudUpload** e **dispositivoAutoDelete.**

**deviceToCloudUpload** é uma funcionalidade configurável. Esta função carrega automaticamente os dados do seu armazenamento local blob para o Azure com suporte intermitente de conectividade à internet. Ele permite que você:

* Ative/desligue o recurso DeviceToCloudUpload.
* Escolha a ordem na qual os dados são copiados para o Azure como NewestFirst ou OldestFirst.
* Especifique a conta do Azure Storage para a qual deseja que seus dados sejam carregados.
* Especifique os contêineres que deseja carregar para o Azure. Este módulo permite especificar nomes de origem e contêiner de destino.
* Escolha a capacidade de excluir as bolhas imediatamente, depois que o upload para o armazenamento em nuvem for concluído
* Faça upload completo de `Put Blob` blob (usando operação) `Put Block List` `Append Block` e upload de nível de bloco (usando `Put Block`, e operações).

Este módulo usa upload de nível de bloco, quando sua bolha consiste em blocos. Aqui estão alguns dos cenários comuns:

* Seu aplicativo atualiza alguns blocos de uma bolha de bloco sumida anteriormente carregada ou anexa novos blocos a uma bolha de apêndice, este módulo carrega apenas os blocos atualizados e não toda a bolha.
* O módulo está carregando blob e a conexão com a internet desaparece, quando a conectividade está de volta, ele carrega apenas os blocos restantes e não toda a bolha.

Se um término inesperado do processo (como falha de energia) acontecer durante um upload de blob, todos os blocos que deveriam ser enviados para o upload serão carregados novamente assim que o módulo voltar a funcionar.

**dispositivoAutoDelete** é uma funcionalidade configurável. Esta função exclui automaticamente suas bolhas do armazenamento local quando a duração especificada (medida em minutos) expirar. Ele permite que você:

* Ative/desligue o recurso DispositivoAutoDelete.
* Especifique o tempo em minutos (deleteAfterMinutes) após o qual as bolhas serão automaticamente excluídas.
* Escolha a capacidade de reter a bolha enquanto estiver carregando se o valor de deleteAfterMinutes expirar.

## <a name="prerequisites"></a>Pré-requisitos

Um dispositivo do Azure IoT Edge:

* Você pode usar sua máquina de desenvolvimento ou uma máquina virtual como um dispositivo IoT Edge seguindo as etapas do quickstart para dispositivos [Linux](quickstart-linux.md) ou [Windows](quickstart.md).

* Consulte [os sistemas suportados pelo Azure IoT Edge](support.md#operating-systems) para obter uma lista de sistemas operacionais e arquiteturas suportados. O armazenamento Azure Blob no módulo IoT Edge suporta as seguintes arquiteturas:
  * Windows AMD64
  * Linux AMD64
  * Linux ARM32
  * Linux ARM64 (visualização)

Recursos de nuvem:

Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) na camada padrão no Azure.

## <a name="devicetocloudupload-and-deviceautodelete-properties"></a>dispositivoToCloudUpload e dispositivoPropriedades AutoDelete

Use as propriedades desejadas do módulo para definir **o dispositivoToCloudUploadProperties** e **o dispositivoAutoDeleteProperties**. As propriedades desejadas podem ser definidas durante a implantação ou alteradas posteriormente editando o módulo gêmeo sem a necessidade de reimplantar. Recomendamos verificar o "Módulo `reported configuration` Gêmeo" e `configurationValidation` certificar-se de que os valores estão corretamente propagados.

### <a name="devicetoclouduploadproperties"></a>dispositivoToCloudUploadPropriedades

O nome desta `deviceToCloudUploadProperties`configuração é . Se você estiver usando o simulador IoT Edge, defina os valores para as variáveis de ambiente relacionadas para essas propriedades, que você pode encontrar na seção de explicação.

| Propriedade | Valores possíveis | Explicação |
| ----- | ----- | ---- |
| uploadOn | verdadeiro, falso | Definido `false` como padrão. Se você quiser ativar o recurso, `true`defina este campo para . <br><br> Variável de ambiente: `deviceToCloudUploadProperties__uploadOn={false,true}` |
| uploadOrder | Mais novoPrimeiro, Mais AntigoPrimeiro | Permite que você escolha a ordem em que os dados são copiados para o Azure. Definido `OldestFirst` como padrão. A ordem é determinada pelo último tempo modificado de Blob. <br><br> Variável de ambiente: `deviceToCloudUploadProperties__uploadOrder={NewestFirst,OldestFirst}` |
| cloudStorageConnectionString |  | `"DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>"`é uma seqüência de conexões que permite especificar a conta de armazenamento para a qual você deseja que seus dados sejam carregados. `Azure Storage Account Name`Especificar `Azure Storage Account Key` `End point suffix`, . Adicione o EndpointSufix apropriado do Azure, onde os dados serão carregados, ele varia para Global Azure, Government Azure e Microsoft Azure Stack. <br><br> Você pode optar por especificar a seqüência de conexão SAS do Azure Storage aqui. Mas você tem que atualizar esta propriedade quando ela expirar. <br><br> Variável de ambiente: `deviceToCloudUploadProperties__cloudStorageConnectionString=<connection string>` |
| contêineres de armazenamentoForUpload | `"<source container name1>": {"target": "<target container name>"}`,<br><br> `"<source container name1>": {"target": "%h-%d-%m-%c"}`, <br><br> `"<source container name1>": {"target": "%d-%c"}` | Permite especificar os nomes de contêiner que deseja carregar no Azure. Este módulo permite especificar nomes de origem e contêiner de destino. Se você não especificar o nome do contêiner de destino, `<IoTHubName>-<IotEdgeDeviceID>-<ModuleName>-<SourceContainerName>`ele atribuirá automaticamente o nome do contêiner como . Você pode criar strings de modelo para nome do contêiner de destino, confira a coluna de valores possíveis. <br>* %h -> Nome do Hub IoT (3-50 caracteres). <br>* %d -> ID do dispositivo de borda IoT (1 a 129 caracteres). <br>* %m -> nome do módulo (1 a 64 caracteres). <br>* %c -> Nome do recipiente de origem (3 a 63 caracteres). <br><br>O tamanho máximo do nome do contêiner é de 63 caracteres, enquanto atribui automaticamente o nome do contêiner de destino se o tamanho do contêiner exceder 63 caracteres, ele cortará cada seção (IoTHubName, IotEdgeDeviceID, ModuleName, SourceContainerName) para 15 Caracteres. <br><br> Variável de ambiente: `deviceToCloudUploadProperties__storageContainersForUpload__<sourceName>__target=<targetName>` |
| excluirAfterUpload | verdadeiro, falso | Definido `false` como padrão. Quando for `true`definido, ele excluirá automaticamente os dados quando o upload para o armazenamento em nuvem estiver concluído. <br><br> **ATENÇÃO**: Se você estiver usando blobs de apêndice, esta configuração excluirá blobs de apêndice do armazenamento local após um upload bem-sucedido, e quaisquer futuras operações de Bloco de Apêndice para essas bolhas falharão. Use esta configuração com cautela, não habilite isso se seu aplicativo não frequentar operações de apêndice ou não suportar operações de apêndice contínuos<br><br> Variável de `deviceToCloudUploadProperties__deleteAfterUpload={false,true}`ambiente: . |

### <a name="deviceautodeleteproperties"></a>dispositivoAutoExcluirpropriedades

O nome desta `deviceAutoDeleteProperties`configuração é . Se você estiver usando o simulador IoT Edge, defina os valores para as variáveis de ambiente relacionadas para essas propriedades, que você pode encontrar na seção de explicação.

| Propriedade | Valores possíveis | Explicação |
| ----- | ----- | ---- |
| excluir | verdadeiro, falso | Definido `false` como padrão. Se você quiser ativar o recurso, `true`defina este campo para . <br><br> Variável de ambiente: `deviceAutoDeleteProperties__deleteOn={false,true}` |
| excluirAfterMinutes | `<minutes>` | Especifique a hora em minutos. O módulo excluirá automaticamente suas bolhas do armazenamento local quando esse valor expirar. <br><br> Variável de ambiente: `deviceAutoDeleteProperties__ deleteAfterMinutes=<minutes>` |
| reterEnquantoCarregar | verdadeiro, falso | Por padrão, ele `true`está definido para , e ele manterá a bolha enquanto ele está carregando para o armazenamento na nuvem se excluirAfterMinutes expirar. Você pode configurá-lo `false` e ele excluirá os dados assim que excluirAfterMinutes expirar. Nota: Para que esta propriedade funcione, o uploadOn deve ser definido como verdadeiro.  <br><br> **ATENÇÃO**: Se você estiver usando blobs de apêndice, esta configuração excluirá bolhas de apêndice do armazenamento local quando o valor expirar, e quaisquer futuras operações de Bloco de Apêndice para essas bolhas falharão. Você pode querer ter certeza de que o valor de expiração é grande o suficiente para a frequência esperada de operações de apêndice realizadas pelo seu aplicativo.<br><br> Variável de ambiente: `deviceAutoDeleteProperties__retainWhileUploading={false,true}`|

## <a name="using-smb-share-as-your-local-storage"></a>Usando o compartilhamento de SMB como seu armazenamento local

Você pode fornecer o compartilhamento de SMB como seu caminho de armazenamento local, quando você implantar o contêiner do Windows deste módulo no host do Windows.

Certifique-se de que o compartilhamento de SMB e o dispositivo IoT estejam em domínios mutuamente confiáveis.

Você pode `New-SmbGlobalMapping` executar o comando PowerShell para mapear o compartilhamento de SMB localmente no dispositivo IoT executando o Windows.

Abaixo estão as etapas de configuração:

```PowerShell
$creds = Get-Credential
New-SmbGlobalMapping -RemotePath <remote SMB path> -Credential $creds -LocalPath <Any available drive letter>
```

Por exemplo: 

```powershell
$creds = Get-Credential
New-SmbGlobalMapping -RemotePath \\contosofileserver\share1 -Credential $creds -LocalPath G:
```

Este comando usará as credenciais para autenticar com o servidor SMB remoto. Em seguida, mapeie o caminho do compartilhamento remoto para a letra de unidade G: (pode ser qualquer outra letra de unidade disponível). O dispositivo IoT agora tem o volume de dados mapeado para um caminho na unidade G:.

Certifique-se de que o usuário em dispositivo IoT pode ler/gravar para o compartilhamento remoto de SMB.

Para sua implantação, `<storage mount>` o valor pode ser **G:/ContainerData:C:/BlobRoot**.

## <a name="granting-directory-access-to-container-user-on-linux"></a>Concessão de acesso de diretório ao usuário de contêiner no Linux

Se você usou [montagem de volume](https://docs.docker.com/storage/volumes/) para armazenamento em suas opções de criação para contêineres Linux, então você não precisa fazer nenhum passo extra, mas se você usou montagem de [vinculação,](https://docs.docker.com/storage/bind-mounts/) então essas etapas são necessárias para executar o serviço corretamente.

Seguindo o princípio do menor privilégio de limitar os direitos de acesso para os usuários desem permissões mínimas que precisam para realizar seu trabalho, este módulo inclui um usuário (nome: absie, ID: 11000) e um grupo de usuários (nome: absie, ID: 11000). Se o contêiner for iniciado como **raiz** (usuário padrão é **raiz),** nosso serviço será iniciado como o usuário **absie** de baixo privilégio.

Esse comportamento torna a configuração das permissões no caminho do host vincula-se crucial para que o serviço funcione corretamente, caso contrário, o serviço falhará com erros de acesso negados. O caminho usado na vinculação do diretório precisa ser acessível pelo usuário do contêiner (exemplo: absie 11000). Você pode conceder ao usuário do contêiner acesso ao diretório executando os comandos abaixo no host:

```terminal
sudo chown -R 11000:11000 <blob-dir>
sudo chmod -R 700 <blob-dir>
```

Por exemplo: 

```terminal
sudo chown -R 11000:11000 /srv/containerdata
sudo chmod -R 700 /srv/containerdata
```

Se você precisar executar o serviço como um usuário que não **seja absie,** você pode especificar seu ID de usuário personalizado em criarOpções em propriedade "Usuário" em seu manifesto de implantação. Nesse caso, você precisa usar o `0`ID padrão ou de grupo raiz .

```json
"createOptions": {
  "User": "<custom user ID>:0"
}
```

Agora, conceda ao usuário do contêiner acesso ao diretório

```terminal
sudo chown -R <user ID>:<group ID> <blob-dir>
sudo chmod -R 700 <blob-dir>
```

## <a name="configure-log-files"></a>Configurar arquivos de log

Para obter informações sobre a configuração de arquivos de log para o seu módulo, consulte essas [práticas recomendadas de produção](https://docs.microsoft.com/azure/iot-edge/production-checklist#set-up-logs-and-diagnostics).

## <a name="connect-to-your-blob-storage-module"></a>Conectar ao módulo do armazenamento de blobs

É possível usar o nome da conta e a chave de conta que você configurou para o módulo para acessar o armazenamento de blobs no seu dispositivo do IoT Edge.

Especifique o dispositivo do IoT Edge como ponto de extremidade do blob para quaisquer solicitações de armazenamento que você faz para esse dispositivo. É possível [Criar uma cadeia de conexão para um ponto de extremidade explícito](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint) usando as informações do dispositivo do IoT Edge e do nome da conta que você configurou.

* Para módulos que são implantados no mesmo dispositivo em que o módulo Azure Blob Storage `http://<module name>:11002/<account name>`no módulo IoT Edge está sendo executado, o ponto final do blob é: .
* Para módulos ou aplicativos em execução em um dispositivo diferente, você tem que escolher o ponto final certo para sua rede. Dependendo da configuração da rede, escolha um formato de ponto final para que o tráfego de dados do seu módulo ou aplicativo externo possa chegar ao dispositivo executando o Azure Blob Storage no módulo IoT Edge. O ponto final para este cenário é um dos seguintes:
  * `http://<device IP >:11002/<account name>`
  * `http://<IoT Edge device hostname>:11002/<account name>`
  * `http://<fully qualified domain name>:11002/<account name>`

## <a name="azure-blob-storage-quickstart-samples"></a>Amostras de partida rápida do Azure Blob Storage

A documentação do Azure Blob Storage inclui código de amostra quickstart em vários idiomas. Você pode executar essas amostras para testar o Armazenamento Azure Blob no IoT Edge alterando o ponto final do blob para se conectar ao módulo de armazenamento local.

As seguintes amostras de quickstart usam linguagens que também são suportadas pelo IoT Edge, para que você possa implantá-las como módulos IoT Edge ao lado do módulo de armazenamento blob:

* [.NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
* [Python](../storage/blobs/storage-quickstart-blobs-python.md)
  * Versões antes do V2.1 do Python SDK têm um problema conhecido onde o módulo não retorna o tempo de criação do blob. Por causa desse problema, alguns métodos como os blobs de lista não funcionam. Como solução de solução, defina explicitamente a versão da API no cliente blob para '2017-04-17'. Exemplo: `block_blob_service._X_MS_VERSION = '2017-04-17'`
  * [Amostra de bolha de apêndice](https://github.com/Azure/azure-storage-python/blob/master/samples/blob/append_blob_usage.py)
* [Node.js](../storage/blobs/storage-quickstart-blobs-nodejs-legacy.md)
* [JS/HTML](../storage/blobs/storage-quickstart-blobs-javascript-client-libraries-legacy.md)
* [Ruby](../storage/blobs/storage-quickstart-blobs-ruby.md)
* [Ir](../storage/blobs/storage-quickstart-blobs-go.md)
* [Php](../storage/blobs/storage-quickstart-blobs-php.md)

## <a name="connect-to-your-local-storage-with-azure-storage-explorer"></a>Conecte-se ao seu armazenamento local com o Azure Storage Explorer

Você pode usar [o Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) para se conectar à sua conta de armazenamento local.

1. Baixar e instalar o Gerenciador de Armazenamento do Azure

1. Conecte-se ao Armazenamento Azure usando uma seqüência de conexões

1. Fornecer string de conexão:`DefaultEndpointsProtocol=http;BlobEndpoint=http://<host device name>:11002/<your local account name>;AccountName=<your local account name>;AccountKey=<your local account key>;`

1. Passe pelos degraus para se conectar.

1. Criar contêiner dentro de sua conta de armazenamento local

1. Comece a carregar arquivos como blobs block ou Blobs de apêndice.
   > [!NOTE]
   > Este módulo não suporta blobs de página.

1. Você também pode optar por conectar suas contas de armazenamento do Azure no Storage Explorer. Esta configuração oferece uma única visualização para sua conta de armazenamento local e conta de armazenamento do Azure

## <a name="supported-storage-operations"></a>Operações de armazenamento com suporte

Os módulos de armazenamento Blob no IoT Edge usam os SDKs de armazenamento Azure e são consistentes com a versão 2017-04-17 da API de armazenamento Azure para pontos finais de blob de bloco.

Como nem todas as operações de armazenamento do Azure Blob são suportadas pelo Azure Blob Storage no IoT Edge, esta seção lista o status de cada um.

### <a name="account"></a>Conta

Com suporte:

* Listar contêineres

Sem suporte:

* Obter e definir propriedades do serviço Blob
* Solicitação de blob de simulação
* Obter estatísticas do serviço Blob
* Obter Informações da conta

### <a name="containers"></a>Contêineres

Com suporte:

* Criar e excluir contêiner
* Obter Propriedades do Contêiner
* Listar blobs
* Obter e definir ACL do contêiner
* Definir Metadados do Contêiner

Sem suporte:

* Concessão de contêiner

### <a name="blobs"></a>Blobs

Com suporte:

* Colocar, obter e excluir blob
* Obter e definir propriedades do blob
* Obter e definir Metadados do Blob

Sem suporte:

* Concessão de blob
* Instantâneo do blob
* Copiar e cancelar cópia do blob
* Cancelar exclusão do blob
* Definir camada do blob

### <a name="block-blobs"></a>Blobs de bloco

Com suporte:

* Colocar bloco
* Colocar e obter lista de blocos

Sem suporte:

* Colocar bloco pela URL

### <a name="append-blobs"></a>Blobs de acréscimo

Com suporte:

* Bloco de apêndice

Sem suporte:

* Bloco de apêndice da URL

## <a name="event-grid-on-iot-edge-integration"></a>Grade de eventos na Integração de Bordas ioT

> [!CAUTION]
> A integração com event grid no IoT Edge está na pré-visualização

Este módulo Azure Blob Storage no IoT Edge agora fornece integração com a Event Grid no IoT Edge. Para obter informações detalhadas sobre essa integração, consulte o [tutorial para implantar os módulos, publicar eventos e verificar a entrega de eventos.](../event-grid/edge/react-blob-storage-events-locally.md)

## <a name="release-notes"></a>Notas de versão

Aqui estão as [notas de lançamento no docker hub](https://hub.docker.com/_/microsoft-azure-blob-storage) para este módulo

## <a name="feedback"></a>Comentários

Seu feedback é importante para nós para tornar este módulo e seus recursos úteis e fáceis de usar. Por favor, compartilhe sua opinião e deixe-nos saber como podemos melhorar.

Você pode nos alcançar emabsiotfeedback@microsoft.com

## <a name="next-steps"></a>Próximas etapas

Saiba como [implantar o armazenamento Azure Blob na borda IoT](how-to-deploy-blob.md)

Mantenha-se atualizado com as recentes atualizações e anúncios no [Azure Blob Storage no blog IoT Edge](https://aka.ms/abs-iot-blogpost)
