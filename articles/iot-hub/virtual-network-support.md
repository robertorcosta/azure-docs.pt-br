---
title: Suporte ao Hub IoT do Azure para redes virtuais
description: Como usar o padrão de conectividade de redes virtuais com o Hub IoT
services: iot-hub
author: rezasherafat
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: rezas
ms.openlocfilehash: 1b250bee302cb305ee613010238283ceac4014ed
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/14/2020
ms.locfileid: "79375077"
---
# <a name="iot-hub-support-for-virtual-networks"></a>Suporte do Hub IoT para redes virtuais

Este artigo apresenta o padrão de conectividade VNET e elabora como configurar uma experiência de conectividade privada para um hub IoT por meio de uma VNET do Azure de Propriedade do cliente.

> [!NOTE]
> Os recursos do Hub IoT descritos neste artigo estão disponíveis no momento para o Hub IoT criado nas seguintes regiões: leste dos EUA, Sul EUA Central e oeste dos EUA 2.


## <a name="introduction"></a>Introdução

Por padrão, os nomes de host do Hub IoT são mapeados para um ponto de extremidade público com um endereço IP roteável publicamente pela Internet. Conforme mostrado na ilustração a seguir, esse ponto de extremidade público do Hub IoT é compartilhado entre hubs de propriedade de clientes diferentes e pode ser acessado por dispositivos IoT em redes de longa distância, bem como em redes locais semelhantes.

Vários recursos do Hub IoT, incluindo [Roteamento de mensagens](./iot-hub-devguide-messages-d2c.md), [carregamento de arquivos](./iot-hub-devguide-file-upload.md)e [importação/exportação de dispositivos em massa](./iot-hub-bulk-identity-mgmt.md) , exigem conectividade do Hub IOT para um recurso do Azure de Propriedade do cliente em seu ponto de extremidade público. Conforme ilustrado abaixo, esses caminhos de conectividade constituem coletivamente o tráfego de saída do Hub IoT para recursos do cliente.
ponto de extremidade público do ![Hub IoT](./media/virtual-network-support/public-endpoint.png)


Por vários motivos, os clientes podem querer restringir a conectividade com seus recursos do Azure (incluindo o Hub IoT) por meio de uma VNET que eles possuem e operam. Esses motivos incluem:

* Apresentando camadas adicionais de segurança por meio do isolamento de nível de rede para o Hub IoT, impedindo a exposição da conectividade ao seu hub pela Internet pública.

* Habilitação de uma experiência de conectividade privada de seus ativos de rede local, garantindo que seus dados e tráfego sejam transmitidos diretamente para a rede de backbone do Azure.

* Impedir ataques de vazamento de redes locais confidenciais. 

* Seguindo os padrões de conectividade de todo o Azure estabelecidos usando [pontos de extremidade privados](../private-link/private-endpoint-overview.md).


Este artigo descreve como atingir essas metas usando [pontos de extremidade privados](../private-link/private-endpoint-overview.md) para a conectividade de entrada ao Hub IOT, como usando a exceção de serviços de primeira parte confiável do Azure para a conectividade de saída do Hub IOT para outros recursos do Azure.


## <a name="ingress-connectivity-to-iot-hub-using-private-endpoints"></a>Conectividade de entrada ao Hub IoT usando pontos de extremidade privados

Um ponto de extremidade privado é um endereço IP privado alocado dentro de uma VNET de Propriedade do cliente por meio da qual um recurso do Azure pode ser acessado. Tendo um ponto de extremidade privado para o Hub IoT, você poderá permitir que os serviços que operam dentro de sua VNET alcancem o Hub IoT sem exigir que o tráfego seja enviado ao ponto de extremidade público do Hub IoT. Da mesma forma, os dispositivos que operam no local podem usar a [VPN (rede virtual privada)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) ou o emparelhamento privado do [ExpressRoute](https://azure.microsoft.com/services/expressroute/) para obter conectividade com sua VNET no Azure e subsequentemente para o Hub IOT (por meio de seu ponto de extremidade privado). Como resultado, os clientes que desejam restringir a conectividade aos pontos de extremidade públicos do seu hub IoT (ou possivelmente bloqueá-lo completamente) podem atingir esse objetivo usando as [regras de firewall do Hub IOT](./iot-hub-ip-filtering.md) e mantendo a conectividade com o Hub usando o ponto de extremidade privado.

> [!NOTE]
> O foco principal dessa configuração é para dispositivos dentro de uma rede local. Essa configuração não é recomendável para dispositivos implantados em uma rede de longa distância.

![Ponto de extremidade público do Hub IoT](./media/virtual-network-support/virtual-network-ingress.png)

Antes de continuar, verifique se os seguintes pré-requisitos foram atendidos:

* O Hub IoT deve ser provisionado em uma das [regiões com suporte](#regional-availability-private-endpoints).

* Você provisionou uma VNET do Azure com uma sub-rede na qual o ponto de extremidade privado será criado. Consulte [criar uma rede virtual usando CLI do Azure](../virtual-network/quick-create-cli.md) para obter mais detalhes.

* Para dispositivos que operam dentro de redes locais, configure a [VPN (rede virtual privada)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) ou o emparelhamento privado do [EXPRESSROUTE](https://azure.microsoft.com/services/expressroute/) em sua VNET do Azure.


### <a name="regional-availability-private-endpoints"></a>Disponibilidade regional (pontos de extremidade privados)

Pontos de extremidade privados com suporte no Hub IoT criado nas seguintes regiões:

* Leste dos EUA

* Centro-Sul dos Estados Unidos

* Oeste dos EUA 2


### <a name="set-up-a-private-endpoint-for-iot-hub-ingress"></a>Configurar um ponto de extremidade privado para a entrada do Hub IoT

Para configurar um ponto de extremidade privado, siga estas etapas:

1. Execute o seguinte comando CLI do Azure para registrar novamente o provedor de Hub IoT do Azure com sua assinatura:

    ```azurecli-interactive
    az provider register --namespace Microsoft.Devices --wait --subscription  <subscription-name>
    ```

2. Navegue até a guia **conexões de ponto de extremidade privado** no portal do Hub IOT (essa guia só está disponível para os hubs IOT nas [regiões com suporte](#regional-availability-private-endpoints)) e clique no sinal de **+** para adicionar um novo ponto de extremidade privado.

3. Forneça a assinatura, o grupo de recursos, o nome e a região para criar o novo ponto de extremidade privado (idealmente, o ponto de extremidade privado deve ser criado na mesma região que o Hub; consulte a [seção disponibilidade regional](#regional-availability-private-endpoints) para obter mais detalhes).

4. Clique em **Avançar: recurso**e forneça a assinatura para o recurso do Hub IOT e selecione **"Microsoft. Devices/IotHubs"** como tipo de recurso, o nome do Hub IOT como **recurso**e **iotHub** como um subrecurso de destino.

5. Clique em **Avançar: configuração** e forneça sua rede virtual e sub-rede para criar o ponto de extremidade privado no. Selecione a opção para integrar com a zona DNS privada do Azure, se desejado.

6. Clique em **Avançar: marcas**e, opcionalmente, forneça todas as marcas para o recurso.

7. Clique em **revisar + criar** para criar seu recurso de ponto de extremidade privado.


### <a name="pricing-private-endpoints"></a>Preços (pontos de extremidade privados)

Para obter detalhes de preço, confira [Preço do Link Privado do Azure](https://azure.microsoft.com/pricing/details/private-link).


## <a name="egress-connectivity-from-iot-hub-to-other-azure-resources"></a>Conectividade de saída do Hub IoT para outros recursos do Azure

O Hub IoT precisa de acesso ao armazenamento de BLOBs do Azure, aos hubs de eventos, aos recursos do barramento de serviço para [Roteamento de mensagens](./iot-hub-devguide-messages-d2c.md), [carregamento de arquivos](./iot-hub-devguide-file-upload.md)e [importação/exportação de dispositivos em massa](./iot-hub-bulk-identity-mgmt.md), que normalmente ocorre no ponto de extremidade público dos recursos. Caso você associe sua conta de armazenamento, hubs de eventos ou recurso do barramento de serviço a uma VNET, a configuração aconselhada bloqueará a conectividade com o recurso por padrão. Consequentemente, isso impedirá a funcionalidade do Hub IoT que exige acesso a esses recursos.

Para aliviar essa situação, você precisa habilitar a conectividade do seu recurso de Hub IoT para sua conta de armazenamento, hubs de eventos ou recursos de barramento de serviço por meio da opção de **serviços confiáveis de primeira parte do Azure** .

Os pré-requisitos são os seguintes:

* O Hub IoT deve ser provisionado em uma das [regiões com suporte](#regional-availability-trusted-microsoft-first-party-services).

* O Hub IoT deve ser atribuído a uma identidade de serviço gerenciada no tempo de provisionamento do Hub. Siga a instrução sobre como [criar um hub com a identidade de serviço gerenciada](#create-a-hub-with-managed-service-identity).


### <a name="regional-availability-trusted-microsoft-first-party-services"></a>Disponibilidade regional (serviços de terceiros da Microsoft confiáveis)

A exceção de serviços de primeira parte confiável do Azure para ignorar as restrições de firewall para o armazenamento do Azure, os hubs de eventos e os recursos do barramento de serviço tem suporte apenas para hubs IoT nas seguintes regiões:

* Leste dos EUA

* Centro-Sul dos Estados Unidos

* Oeste dos EUA 2


### <a name="pricing-trusted-microsoft-first-party-services"></a>Preços (serviços de terceiros da Microsoft confiáveis)

O recurso de exceção de serviços de terceiros da Microsoft confiável é gratuito em hubs IoT nas [regiões com suporte](#regional-availability-trusted-microsoft-first-party-services). Os encargos das contas de armazenamento provisionadas, dos hubs de eventos ou dos recursos do barramento de serviço se aplicam separadamente.


### <a name="create-a-hub-with-managed-service-identity"></a>Criar um hub com identidade de serviço gerenciada

Uma identidade de serviço gerenciada pode ser atribuída ao seu Hub no momento do provisionamento de recursos (atualmente, não há suporte para esse recurso em hubs existentes). Para essa finalidade, você precisa usar o modelo de recurso ARM abaixo:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Devices/IotHubs",
            "apiVersion": "2020-03-01",
            "name": "<provide-a-valid-resource-name>",
            "location": "<any-of-supported-regions>",
            "identity": { "type": "SystemAssigned" },
            "properties": { "minTlsVersion": "1.2" },
            "sku": {
                "name": "<your-hubs-SKU-name>",
                "tier": "<your-hubs-SKU-tier>",
                "capacity": 1
            }
        },
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2018-02-01",
            "name": "updateIotHubWithKeyEncryptionKey",
            "dependsOn": [ "<provide-a-valid-resource-name>" ],
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
                    "contentVersion": "0.9.0.0",
                    "resources": [
                        {
                            "type": "Microsoft.Devices/IotHubs",
                            "apiVersion": "2020-03-01",
                            "name": "<provide-a-valid-resource-name>",
                            "location": "<any-of-supported-regions>",
                            "identity": { "type": "SystemAssigned" },
                            "properties": { "minTlsVersion": "1.2" },
                            "sku": {
                                "name": "<your-hubs-SKU-name>",
                                "tier": "<your-hubs-SKU-tier>",
                                "capacity": 1
                            }
                        }
                    ]
                }
            }
        }
    ]
}
```

Depois de substituir os valores do `name`de recursos, `location`, `SKU.name` e `SKU.tier`, você pode usar CLI do Azure para implantar o recurso em um grupo de recursos existente usando:

```azurecli-interactive
az group deployment create --name <deployment-name> --resource-group <resource-group-name> --template-file <template-file.json>
```

Depois que o recurso for criado, você poderá recuperar a identidade de serviço gerenciada atribuída ao seu hub usando CLI do Azure:

```azurecli-interactive
az resource show --resource-type Microsoft.Devices/IotHubs --name <iot-hub-resource-name> --resource-group <resource-group-name>
```

Depois que o Hub IoT com uma identidade de serviço gerenciada for provisionado, siga a seção correspondente para configurar pontos de extremidade de roteamento para [contas de armazenamento](#egress-connectivity-to-storage-account-endpoints-for-routing), [hubs de eventos](#egress-connectivity-to-event-hubs-endpoints-for-routing)e recursos de barramento de [serviço](#egress-connectivity-to-service-bus-endpoints-for-routing) ou para configurar o [upload de arquivo](#egress-connectivity-to-storage-accounts-for-file-upload) e [importação/exportação de dispositivo em massa](#egress-connectivity-to-storage-accounts-for-bulk-device-importexport).


### <a name="egress-connectivity-to-storage-account-endpoints-for-routing"></a>Conectividade de saída para pontos de extremidade da conta de armazenamento para roteamento

O Hub IoT pode ser configurado para rotear mensagens para uma conta de armazenamento de Propriedade do cliente. Para permitir que a funcionalidade de roteamento acesse uma conta de armazenamento enquanto as restrições de firewall estão em vigor, o Hub IoT precisa ter uma identidade de serviço gerenciada (consulte como [criar um hub com a identidade de serviço gerenciada](#create-a-hub-with-managed-service-identity)). Depois que uma identidade de serviço gerenciada for provisionada, siga as etapas abaixo para conceder permissão de RBAC à identidade de recurso do hub para acessar sua conta de armazenamento.

1. Na portal do Azure, navegue até a guia controle de acesso da conta de armazenamento **(iam)** e clique em **Adicionar** na seção **Adicionar uma atribuição de função** .

2. Selecione **colaborador de dados de armazenamento de blob** como **função**, **usuário do Azure AD, grupo ou entidade de serviço** , como **atribuir acesso ao** e selecione o nome do recurso do Hub IOT na lista suspensa. Clique no botão **Salvar**.

3. Navegue até a guia **firewalls e redes virtuais** na sua conta de armazenamento e habilite a opção **permitir acesso de redes selecionadas** . Na lista **exceções** , marque a caixa **permitir que serviços confiáveis da Microsoft acessem esta conta de armazenamento**. Clique no botão **Salvar**.

4. Na página de recursos do Hub IoT, navegue até a guia **Roteamento de mensagens** .

5. Navegue até a seção **pontos de extremidade personalizados** e clique em **Adicionar**. Selecione **armazenamento** como o tipo de ponto de extremidade.

6. Na página que aparece, forneça um nome para o ponto de extremidade, selecione o contêiner que você pretende usar no armazenamento de BLOBs, forneça a codificação e o formato de nome de arquivo. Selecione **sistema atribuído** como o **tipo de autenticação** para o ponto de extremidade de armazenamento. Clique no botão **Criar**.

Agora seu ponto de extremidade de armazenamento personalizado está configurado para usar a identidade atribuída pelo sistema do Hub e tem permissão para acessar seu recurso de armazenamento, apesar de suas restrições de firewall. Agora você pode usar esse ponto de extremidade para configurar uma regra de roteamento.


### <a name="egress-connectivity-to-event-hubs-endpoints-for-routing"></a>Conectividade de saída para pontos de extremidade de hubs de eventos para roteamento

O Hub IoT pode ser configurado para rotear mensagens para um namespace de hubs de eventos de Propriedade do cliente. Para permitir que a funcionalidade de roteamento acesse um recurso de hubs de eventos enquanto as restrições de firewall estiverem em vigor, o Hub IoT precisará ter uma identidade de serviço gerenciada (consulte como [criar um hub com a identidade de serviço gerenciada](#create-a-hub-with-managed-service-identity)). Depois que uma identidade de serviço gerenciada for provisionada, siga as etapas abaixo para conceder permissão RBAC à identidade de recurso do hub para acessar seus hubs de eventos.

1. Na portal do Azure, navegue até a guia **iam (controle de acesso** dos hubs de eventos) e clique em **Adicionar** na seção **Adicionar uma atribuição de função** .

2. Selecione os **hubs de eventos remetente de dados** como **função**, **usuário do Azure AD, grupo ou entidade de serviço** , como **atribuir acesso ao** e selecione o nome do recurso do Hub IOT na lista suspensa. Clique no botão **Salvar**.

3. Navegue até a guia **firewalls e redes virtuais** em seus hubs de eventos e habilite a opção **permitir acesso de redes selecionadas** . Na lista **exceções** , marque a caixa **permitir que os serviços confiáveis da Microsoft acessem os hubs de eventos**. Clique no botão **Salvar**.

4. Na página de recursos do Hub IoT, navegue até a guia **Roteamento de mensagens** .

5. Navegue até a seção **pontos de extremidade personalizados** e clique em **Adicionar**. Selecione **hubs de eventos** como o tipo de ponto de extremidade.

6. Na página que aparece, forneça um nome para o ponto de extremidade, selecione o namespace e a instância dos hubs de eventos e clique no botão **criar** .

Agora seu ponto de extremidade de hubs de eventos personalizado está configurado para usar a identidade atribuída pelo sistema do Hub e tem permissão para acessar seu recurso de hubs de eventos, apesar de suas restrições de firewall. Agora você pode usar esse ponto de extremidade para configurar uma regra de roteamento.


### <a name="egress-connectivity-to-service-bus-endpoints-for-routing"></a>Conectividade de saída para pontos de extremidade do barramento de serviço para roteamento

O Hub IoT pode ser configurado para rotear mensagens para um namespace do barramento de serviço de Propriedade do cliente. Para permitir que a funcionalidade de roteamento acesse um recurso do barramento de serviço enquanto as restrições de firewall estiverem em vigor, o Hub IoT precisará ter uma identidade de serviço gerenciada (consulte como [criar um hub com a identidade de serviço gerenciada](#create-a-hub-with-managed-service-identity)). Depois que uma identidade de serviço gerenciada for provisionada, siga as etapas abaixo para conceder permissão de RBAC à identidade de recurso do hub para acessar o barramento de serviço.

1. Na portal do Azure, navegue até a guia controle de acesso do barramento de serviço **(iam)** e clique em **Adicionar** na seção **Adicionar uma atribuição de função** .

2. Selecione o **remetente de dados do barramento de serviço** como **função**, **usuário do Azure AD, grupo ou entidade de serviço** , como **atribuir acesso ao** e selecionar o nome do recurso do Hub IOT na lista suspensa. Clique no botão **Salvar**.

3. Navegue até a guia **firewalls e redes virtuais** em seu barramento de serviço e habilite a opção **permitir acesso de redes selecionadas** . Na lista **exceções** , marque a caixa **permitir que serviços confiáveis da Microsoft acessem este barramento de serviço**. Clique no botão **Salvar**.

4. Na página de recursos do Hub IoT, navegue até a guia **Roteamento de mensagens** .

5. Navegue até a seção **pontos de extremidade personalizados** e clique em **Adicionar**. Selecione o tópico **fila do barramento de serviço** ou barramento de **serviço** (conforme aplicável) como o tipo de ponto de extremidade.

6. Na página exibida, forneça um nome para o ponto de extremidade, selecione o namespace e a fila ou o tópico do barramento de serviço (conforme aplicável). Clique no botão **Criar**.

Agora o ponto de extremidade do barramento de serviço personalizado está configurado para usar a identidade atribuída pelo sistema do Hub e tem permissão para acessar o recurso do barramento de serviço, apesar de suas restrições de firewall. Agora você pode usar esse ponto de extremidade para configurar uma regra de roteamento.


### <a name="egress-connectivity-to-storage-accounts-for-file-upload"></a>Conectividade de saída para contas de armazenamento para upload de arquivo

O recurso de upload de arquivo do Hub IoT permite que os dispositivos carreguem arquivos para uma conta de armazenamento de Propriedade do cliente. Para permitir que o upload do arquivo funcione, os dispositivos e o Hub IoT precisam ter conectividade com a conta de armazenamento. Se as restrições de firewall estiverem em vigor na conta de armazenamento, os dispositivos precisarão usar qualquer um dos mecanismos da conta de armazenamento com suporte (incluindo [pontos de extremidade privados](../private-link/create-private-endpoint-storage-portal.md), [pontos de extremidade de serviço](../virtual-network/virtual-network-service-endpoints-overview.md) ou configuração de [Firewall direto](../storage/common/storage-network-security.md)) para obter conectividade. Da mesma forma, se as restrições de firewall estiverem em vigor na conta de armazenamento, o Hub IoT precisará ser configurado para acessar o recurso de armazenamento por meio da exceção de serviços confiáveis da Microsoft. Para essa finalidade, o Hub IoT deve ter uma identidade de serviço gerenciada (consulte como [criar um hub com a identidade de serviço gerenciada](#create-a-hub-with-managed-service-identity)). Depois que uma identidade de serviço gerenciada for provisionada, siga as etapas abaixo para conceder permissão de RBAC à identidade de recurso do hub para acessar sua conta de armazenamento.

1. Na portal do Azure, navegue até a guia controle de acesso da conta de armazenamento **(iam)** e clique em **Adicionar** na seção **Adicionar uma atribuição de função** .

2. Selecione **colaborador de dados de armazenamento de blob** como **função**, **usuário do Azure AD, grupo ou entidade de serviço** , como **atribuir acesso ao** e selecione o nome do recurso do Hub IOT na lista suspensa. Clique no botão **Salvar**.

3. Navegue até a guia **firewalls e redes virtuais** na sua conta de armazenamento e habilite a opção **permitir acesso de redes selecionadas** . Na lista **exceções** , marque a caixa **permitir que serviços confiáveis da Microsoft acessem esta conta de armazenamento**. Clique no botão **Salvar**.

4. Na página de recursos do Hub IoT, navegue até a guia **upload de arquivo** .

5. Na página exibida, selecione o contêiner que você pretende usar em seu armazenamento de BLOBs, defina as configurações de **notificação de arquivo**, **TTL SAS**, **TTL padrão** e **contagem máxima de entrega** conforme desejado. Selecione **sistema atribuído** como o **tipo de autenticação** para o ponto de extremidade de armazenamento. Clique no botão **Criar**.

Agora seu ponto de extremidade de armazenamento para upload de arquivo está configurado para usar a identidade atribuída pelo sistema do Hub e tem permissão para acessar seu recurso de armazenamento, apesar de suas restrições de firewall.


### <a name="egress-connectivity-to-storage-accounts-for-bulk-device-importexport"></a>Conectividade de saída para contas de armazenamento para importação/exportação de dispositivos em massa

O Hub IoT dá suporte à funcionalidade para [importar/exportar](./iot-hub-bulk-identity-mgmt.md) informações de dispositivos em massa de/para um blob de armazenamento fornecido pelo cliente. Para permitir que o recurso de importação/exportação em massa funcione, ambos os dispositivos e o Hub IoT precisam ter conectividade com a conta de armazenamento.

Essa funcionalidade requer conectividade do Hub IoT para a conta de armazenamento. Para acessar um recurso do barramento de serviço enquanto as restrições de firewall estiverem em vigor, o Hub IoT precisará ter uma identidade de serviço gerenciada (consulte como [criar um hub com a identidade de serviço gerenciada](#create-a-hub-with-managed-service-identity)). Depois que uma identidade de serviço gerenciada for provisionada, siga as etapas abaixo para conceder permissão de RBAC à identidade de recurso do hub para acessar o barramento de serviço.

1. Na portal do Azure, navegue até a guia controle de acesso da conta de armazenamento **(iam)** e clique em **Adicionar** na seção **Adicionar uma atribuição de função** .

2. Selecione **colaborador de dados de armazenamento de blob** como **função**, **usuário do Azure AD, grupo ou entidade de serviço** , como **atribuir acesso ao** e selecione o nome do recurso do Hub IOT na lista suspensa. Clique no botão **Salvar**.

3. Navegue até a guia **firewalls e redes virtuais** na sua conta de armazenamento e habilite a opção **permitir acesso de redes selecionadas** . Na lista **exceções** , marque a caixa **permitir que serviços confiáveis da Microsoft acessem esta conta de armazenamento**. Clique no botão **Salvar**.

Agora você pode usar a API REST do Azure IoT para [criar trabalhos de exportação de importação](https://docs.microsoft.com/rest/api/iothub/jobclient/getimportexportjobs) para obter informações sobre como usar a funcionalidade de importação/exportação em massa. Observe que você precisará fornecer o `storageAuthenticationType="identityBased"` no seu corpo de solicitação e usar `inputBlobContainerUri="https://..."` e `outputBlobContainerUri="https://..."` como a URL de entrada e saída da sua conta de armazenamento, respectivamente.


O SDK do Hub IoT do Azure também dá suporte a essa funcionalidade no Gerenciador de registro do cliente de serviço. O trecho de código a seguir mostra como iniciar um trabalho de importação ou de exportação no C# usando o SDK do.

```csharp
// Call an import job on the IoT Hub
JobProperties importJob = 
await registryManager.ImportDevicesAsync(
  JobProperties.CreateForImportJob(inputBlobContainerUri, outputBlobContainerUri, null, StorageAuthenticationType.IdentityBased), 
  cancellationToken);

// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = 
await registryManager.ExportDevicesAsync(
    JobProperties.CreateForExportJob(outputBlobContainerUri, true, null, StorageAuthenticationType.IdentityBased),
    cancellationToken);
```


Para usar esta versão limitada por região dos SDKs do IoT do Azure com suporte VNET C#para, Java e node. js:

1. Crie uma variável de ambiente chamada `EnableStorageIdentity` e defina seu valor como `1`.

2. Baixe o SDK:
    - > [Java](https://aka.ms/vnetjavasdk)
    - > [C#](https://aka.ms/vnetcsharsdk)
    - > [Node.js](https://aka.ms/vnetnodesdk)
 
Para o Python, baixe nossa versão limitada do github.

1. Navegue até a [página de versão do GitHub](https://aka.ms/vnetpythonsdk).

2. Baixe o arquivo a seguir, que você encontrará na parte inferior da página de versão sob o cabeçalho chamado **ativos**.
    > *azure_iot_hub-2.2.0. Limited-PY2. PY3-None-any. WHL*

3. Abra um terminal e navegue até a pasta com o arquivo baixado.

4. Execute o seguinte comando para instalar o SDK do serviço Python com suporte para VNETs:
    > instalação de Pip./azure_iot_hub-2.2.0. Limited-PY2. PY3-None-any. WHL


## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

Use os links abaixo para saber mais sobre os recursos do Hub IoT:

* [Roteamento de mensagens](./iot-hub-devguide-messages-d2c.md)
* [Upload de arquivo](./iot-hub-devguide-file-upload.md)
* [Importação/exportação de dispositivo em massa](./iot-hub-bulk-identity-mgmt.md) 