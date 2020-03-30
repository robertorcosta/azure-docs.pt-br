---
title: Suporte ao Azure IoT Hub para redes virtuais
description: Como usar o padrão de conectividade de redes virtuais com o IoT Hub
services: iot-hub
author: rezasherafat
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: rezas
ms.openlocfilehash: 34f66c13b0e7eb7092332a48744f9abfd8f0db80
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501426"
---
# <a name="iot-hub-support-for-virtual-networks"></a>Suporte ao IoT Hub para redes virtuais

Este artigo introduz o padrão de conectividade VNET e elabora sobre como configurar uma experiência de conectividade privada a um hub de IoT através de um Azure VNET de propriedade do cliente.

> [!NOTE]
> Os recursos do IoT Hub descritos neste artigo estão atualmente disponíveis para hubs de IoT [criados com identidade de serviço gerenciado](#create-an-iot-hub-with-managed-service-identity) nas seguintes regiões: Leste dos EUA, Centro-Sul dos EUA e Us$ 2.


## <a name="introduction"></a>Introdução

Por padrão, os nomes de hostdo do IoT Hub mapeiam para um ponto final público com um endereço IP publicamente roteável na Internet. Como mostrado na ilustração abaixo, este ponto final público do IoT Hub é compartilhado entre hubs de diferentes clientes e pode ser acessado por dispositivos IoT em redes de ampla área, bem como redes locais.

Vários recursos do IoT Hub, incluindo [roteamento de mensagens,](./iot-hub-devguide-messages-d2c.md) [upload de arquivos](./iot-hub-devguide-file-upload.md)e [importação/exportação de dispositivos em massa,](./iot-hub-bulk-identity-mgmt.md) exigem conectividade do IoT Hub a um recurso Azure de propriedade do cliente sobre seu ponto final público. Como ilustrado abaixo, esses caminhos de conectividade constituem coletivamente o tráfego de saída do IoT Hub para os recursos do cliente.
![Ponto final público do IoT Hub](./media/virtual-network-support/public-endpoint.png)


Por várias razões, os clientes podem querer restringir a conectividade aos seus recursos do Azure (incluindo o IoT Hub) através de um VNET que eles possuem e operam. Essas razões incluem:

* Introduzindo camadas adicionais de segurança através do isolamento de nível de rede para o seu hub de IoT, impedindo a exposição de conectividade ao seu hub através da Internet pública.

* Habilitar uma experiência de conectividade privada a partir de seus ativos de rede local, garantindo que seus dados e tráfego sejam transmitidos diretamente para a rede backbone do Azure.

* Prevenção de ataques de exfiltração de redes locais sensíveis. 

* Seguindo padrões de conectividade estabelecidos em todo o Azure usando [pontos finais privados.](../private-link/private-endpoint-overview.md)


Este artigo descreve como alcançar esses objetivos usando [endpoints privados](../private-link/private-endpoint-overview.md) para a conectividade de entrada no IoT Hub, como usar a exceção de serviços de primeira parte confiáveis do Azure para a conectividade de saída do IoT Hub para outros recursos do Azure.


## <a name="ingress-connectivity-to-iot-hub-using-private-endpoints"></a>Conectividade de ingress ao IoT Hub usando pontos finais privados

Um ponto final privado é um endereço IP privado alocado dentro de um VNET de propriedade do cliente através do qual um recurso do Azure é acessível. Ao ter um ponto final privado para o seu hub de IoT, você poderá permitir que os serviços que operam dentro do seu VNET cheguem ao IoT Hub sem exigir que o tráfego seja enviado para o ponto final público do IoT Hub. Da mesma forma, os dispositivos que operam em suas instalações podem usar [a Virtual Private Network (VPN)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) ou o [ExpressRoute](https://azure.microsoft.com/services/expressroute/) Private Peering para obter conectividade com seu VNET no Azure e, posteriormente, ao seu IoT Hub (através de seu ponto final privado). Como resultado, os clientes que desejam restringir a conectividade aos pontos finais públicos de seu hub de IoT (ou possivelmente bloqueá-lo completamente) podem alcançar esse objetivo usando as regras de [firewall do IoT Hub,](./iot-hub-ip-filtering.md) mantendo a conectividade com seu Hub usando o ponto final privado.

> [!NOTE]
> O foco principal desta configuração é para dispositivos dentro de uma rede local. Essa configuração não é aconselhada para dispositivos implantados em uma rede de ampla área.

![Ponto final público do IoT Hub](./media/virtual-network-support/virtual-network-ingress.png)

Antes de prosseguir, certifique-se de que os seguintes pré-requisitos sejam cumpridos:

* Seu hub de IoT deve ser provisionado com [identidade de serviço gerenciada](#create-an-iot-hub-with-managed-service-identity).

* Seu hub de IoT deve ser provisionado em uma das [regiões suportadas.](#regional-availability-private-endpoints)

* Você forneceu um VNET do Azure com uma sub-rede na qual o ponto final privado será criado. Consulte [criar uma rede virtual usando o Azure CLI](../virtual-network/quick-create-cli.md) para obter mais detalhes.

* Para dispositivos que operam dentro de redes locais, configure [a Virtual Private Network (VPN)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) ou a [ExpressRoute](https://azure.microsoft.com/services/expressroute/) private peering em seu Azure VNET.


### <a name="regional-availability-private-endpoints"></a>Disponibilidade regional (pontos finais privados)

Pontos finais privados suportados no IoT Hub's criado nas seguintes regiões:

* Leste dos EUA

* Centro-Sul dos Estados Unidos

* Oeste dos EUA 2


### <a name="set-up-a-private-endpoint-for-iot-hub-ingress"></a>Configure um ponto final privado para a ingestão do IoT Hub

Para configurar um ponto final privado, siga estas etapas:

1. Execute o seguinte comando Azure CLI para recadastrar o provedor Azure IoT Hub com sua assinatura:

    ```azurecli-interactive
    az provider register --namespace Microsoft.Devices --wait --subscription  <subscription-name>
    ```

2. Navegue até a guia **Conexões de ponto final Privado** em seu portal IoT Hub (esta guia só está disponível para em Hubs IoT nas [regiões suportadas)](#regional-availability-private-endpoints)e clique no **+** sinal para adicionar um novo ponto final privado.

3. Forneça a assinatura, o grupo de recursos, o nome e a região para criar o novo endpoint privado (idealmente, o ponto final privado deve ser criado na mesma região que o seu hub; consulte a [seção de disponibilidade regional](#regional-availability-private-endpoints) para obter mais detalhes).

4. Clique **em Next: Resource**e forneça a assinatura do seu recurso ioT Hub e selecione **"Microsoft.Devices/IotHubs"** como tipo de recurso, seu nome IoT Hub como **recurso**e **iotHub** como sub-recurso de destino.

5. Clique **em Next: Configuração** e forneça sua rede virtual e sub-rede para criar o ponto final privado. Selecione a opção de integração com a zona DNS privada do Azure, se desejar.

6. Clique **em Next: Tags**e, opcionalmente, forneça quaisquer tags para o seu recurso.

7. Clique **em 'Revisar + criar** para criar seu recurso de ponto final privado'.


### <a name="pricing-private-endpoints"></a>Preços (pontos finais privados)

Para obter detalhes de preço, confira [Preço do Link Privado do Azure](https://azure.microsoft.com/pricing/details/private-link).


## <a name="egress-connectivity-from-iot-hub-to-other-azure-resources"></a>Conectividade de saída do IoT Hub para outros recursos do Azure

O IoT Hub precisa ter acesso ao armazenamento de blob do Azure, hubs de eventos, recursos de barramento de serviço para [roteamento de mensagens,](./iot-hub-devguide-messages-d2c.md) [upload de arquivos](./iot-hub-devguide-file-upload.md)e [importação/exportação de dispositivos em massa,](./iot-hub-bulk-identity-mgmt.md)que normalmente ocorre sobre o ponto final público dos recursos. No caso de você vincular sua conta de armazenamento, hubs de eventos ou recurso de barramento de serviço a um VNET, a configuração aconselhada bloqueará a conectividade com o recurso por padrão. Consequentemente, isso impedirá a funcionalidade do IoT Hub, que requer acesso a esses recursos.

Para aliviar essa situação, você precisa ativar a conectividade do recurso IoT Hub para sua conta de armazenamento, hubs de eventos ou recursos de barramento de serviço através da opção **de serviços confiáveis do Azure.**

Os pré-requisitos são os seguintes:

* Seu hub de IoT deve ser provisionado em uma das [regiões suportadas.](#regional-availability-trusted-microsoft-first-party-services)

* Seu IoT Hub deve ser atribuído a uma identidade de serviço gerenciada no tempo de provisionamento do hub. Siga instruções sobre como [criar um hub com identidade de serviço gerenciada.](#create-an-iot-hub-with-managed-service-identity)


### <a name="regional-availability-trusted-microsoft-first-party-services"></a>Disponibilidade regional (serviços confiáveis do primeiro grupo da Microsoft)

A exceção de serviços de primeira parte confiável do Azure para contornar as restrições de firewall ao armazenamento do Azure, hubs de eventos e recursos de barramento de serviço só é suportado para Hubs IoT nas seguintes regiões:

* Leste dos EUA

* Centro-Sul dos Estados Unidos

* Oeste dos EUA 2


### <a name="pricing-trusted-microsoft-first-party-services"></a>Preços (serviços confiáveis da Microsoft first party)

O recurso de exceção de serviços de primeira parte confiável da Microsoft é gratuito em Hubs de IoT nas [regiões suportadas](#regional-availability-trusted-microsoft-first-party-services). Os encargos para as contas de armazenamento provisionadas, hubs de eventos ou recursos de barramento de serviço são aplicados separadamente.


### <a name="create-an-iot-hub-with-managed-service-identity"></a>Crie um hub de IoT com identidade de serviço gerenciada

Uma identidade de serviço gerenciada pode ser atribuída ao seu hub no tempo de provisionamento de recursos (esse recurso não é suportado atualmente para hubs existentes), o que exige que o hub IoT use o TLS 1.2 como a versão mínima. Para isso, você precisa usar o modelo de recurso ARM abaixo:

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
      "identity": {
        "type": "SystemAssigned"
      },
      "properties": {
        "minTlsVersion": "1.2"
      },
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
      "dependsOn": [
        "<provide-a-valid-resource-name>"
      ],
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
              "identity": {
                "type": "SystemAssigned"
              },
              "properties": {
                "minTlsVersion": "1.2"
              },
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

Depois de substituir os `name`valores pelo seu recurso, `location` `SKU.name` e, `SKU.tier`você pode usar o Azure CLI para implantar o recurso em um grupo de recursos existente usando:

```azurecli-interactive
az group deployment create --name <deployment-name> --resource-group <resource-group-name> --template-file <template-file.json>
```

Depois que o recurso é criado, você pode recuperar a identidade de serviço gerenciada atribuída ao seu hub usando o Azure CLI:

```azurecli-interactive
az resource show --resource-type Microsoft.Devices/IotHubs --name <iot-hub-resource-name> --resource-group <resource-group-name>
```

Uma vez que o IoT Hub com uma identidade de serviço gerenciada seja provisionado, siga a seção correspondente para configurar pontos finais de roteamento para [contas de armazenamento,](#egress-connectivity-to-storage-account-endpoints-for-routing) [hubs de eventos](#egress-connectivity-to-event-hubs-endpoints-for-routing)e recursos de [barramento](#egress-connectivity-to-service-bus-endpoints-for-routing) de serviço ou para configurar [o upload de arquivos](#egress-connectivity-to-storage-accounts-for-file-upload) e [importação/exportação de dispositivos em massa.](#egress-connectivity-to-storage-accounts-for-bulk-device-importexport)


### <a name="egress-connectivity-to-storage-account-endpoints-for-routing"></a>Conectividade de egresso aos pontos finais da conta de armazenamento para roteamento

O IoT Hub pode ser configurado para direcionar mensagens para uma conta de armazenamento de propriedade do cliente. Para permitir que a funcionalidade de roteamento acesse uma conta de armazenamento enquanto as restrições de firewall estão em vigor, seu IoT Hub precisa ter uma identidade de serviço gerenciada (veja como [criar um hub com identidade de serviço gerenciada).](#create-an-iot-hub-with-managed-service-identity) Uma vez que uma identidade de serviço gerenciada seja provisionada, siga as etapas abaixo para dar permissão ao RBAC para a identidade de recursos do seu hub para acessar sua conta de armazenamento.

1. No portal Azure, navegue até a guia De controle de **acesso (IAM)** da sua conta de armazenamento e clique em **Adicionar** na seção Adicionar uma função **de atribuição.**

2. Selecione **O contribuinte de dados blob de armazenamento** como **função,** usuário, grupo ou principal de serviço do **Azure AD** como **Atribuindo acesso** e selecione o nome de recurso do seu IoT Hub na lista de paradas. Clique no botão **Salvar**.

3. Navegue até a guia **Firewalls e redes virtuais** em sua conta de armazenamento e habilite permitir o acesso a partir da opção **de redes selecionadas.** Na lista **Exceções,** verifique a caixa para **permitir que serviços confiáveis da Microsoft acessem essa conta de armazenamento**. Clique no botão **Salvar**.

4. Na página de recursos do seu IoT Hub, navegue até a guia **de roteamento de mensagens.**

5. Navegue até a seção **Pontos finais personalizados** e clique **em Adicionar**. Selecione **Armazenamento** como o tipo de ponto final.

6. Na página que aparece, forneça um nome para o seu ponto final, selecione o contêiner que você pretende usar no armazenamento blob, forneça codificação e formato de nome do arquivo. Selecione **Sistema Atribuído** como o **tipo de autenticação** ao ponto final do armazenamento. Clique no botão **Criar**.

Agora, seu ponto final de armazenamento personalizado está configurado para usar a identidade atribuída ao sistema do seu hub, e ele tem permissão para acessar seu recurso de armazenamento, apesar de suas restrições de firewall. Agora você pode usar este ponto final para configurar uma regra de roteamento.


### <a name="egress-connectivity-to-event-hubs-endpoints-for-routing"></a>Conectividade de saída para hubs de eventos para roteamento

O IoT Hub pode ser configurado para direcionar mensagens para um namespace de hubs de eventos de propriedade do cliente. Para permitir que a funcionalidade de roteamento acesse um recurso de hubs de eventos enquanto as restrições de firewall estão em vigor, seu IoT Hub precisa ter uma identidade de serviço gerenciada (veja como [criar um hub com identidade de serviço gerenciada).](#create-an-iot-hub-with-managed-service-identity) Uma vez que uma identidade de serviço gerenciada seja provisionada, siga as etapas abaixo para dar permissão ao RBAC para a identidade de recursos do seu hub para acessar seus hubs de eventos.

1. No portal Azure, navegue até a guia **de controle de acesso (IAM)** dos hubs de eventos e clique em **Adicionar** na seção Adicionar uma função **de atribuição.**

2. Selecione **o Remetente de Dados do Event Hubs** como **função,** usuário, grupo ou principal de serviço do **Azure** Como **Atribuindo acesso** e selecione o nome de recurso do seu IoT Hub na lista de paradas. Clique no botão **Salvar**.

3. Navegue até a guia **Firewalls e redes virtuais** em seus hubs de eventos e habilite permitir o acesso a partir da opção **de redes selecionadas.** Na lista **Exceções,** verifique a caixa para **permitir que serviços confiáveis da Microsoft acessem hubs de eventos**. Clique no botão **Salvar**.

4. Na página de recursos do seu IoT Hub, navegue até a guia **de roteamento de mensagens.**

5. Navegue até a seção **Pontos finais personalizados** e clique **em Adicionar**. Selecione **os hubs de evento** como o tipo de ponto final.

6. Na página que aparece, forneça um nome para o seu ponto final, selecione o namespace e a instância dos hubs de eventos e clique no botão **Criar.**

Agora, o ponto final dos hubs de eventos personalizados está configurado para usar a identidade atribuída ao sistema do seu hub, e ele tem permissão para acessar o recurso de hubs de eventos, apesar de suas restrições de firewall. Agora você pode usar este ponto final para configurar uma regra de roteamento.


### <a name="egress-connectivity-to-service-bus-endpoints-for-routing"></a>Conectividade de saída para pontos finais de ônibus de serviço para roteamento

O IoT Hub pode ser configurado para encaminhar mensagens para um namespace de barramento de serviço de propriedade do cliente. Para permitir que a funcionalidade de roteamento acesse um recurso de barramento de serviço enquanto as restrições de firewall estão em vigor, seu IoT Hub precisa ter uma identidade de serviço gerenciada (veja como [criar um hub com identidade de serviço gerenciada).](#create-an-iot-hub-with-managed-service-identity) Uma vez que uma identidade de serviço gerenciada seja provisionada, siga as etapas abaixo para dar permissão ao RBAC para a identidade de recursos do seu hub para acessar seu barramento de serviço.

1. No portal Azure, navegue até a guia **De controle de acesso (IAM)** do seu barramento de serviço e clique em **Adicionar** na seção Adicionar uma função **de atribuição.**

2. Selecione **O Remetente de Dados de Barra de Serviço** como **função,** usuário, grupo ou principal de serviço do **Azure** Como **Atribuindo acesso** e selecione o nome de recurso do seu IoT Hub na lista de paradas. Clique no botão **Salvar**.

3. Navegue até a guia **Firewalls e redes virtuais** em seu barramento de serviço e habilite permitir o acesso a partir da opção **de redes selecionadas.** Na lista **Exceções,** verifique a caixa para **permitir que serviços confiáveis da Microsoft acessem este barramento de serviço**. Clique no botão **Salvar**.

4. Na página de recursos do seu IoT Hub, navegue até a guia **de roteamento de mensagens.**

5. Navegue até a seção **Pontos finais personalizados** e clique **em Adicionar**. Selecione **a fila de barramento de serviço** ou o tópico de **barramento de serviço** (conforme aplicável) como o tipo de ponto final.

6. Na página que aparece, forneça um nome para o seu ponto final, selecione o espaço de nome do seu barramento de serviço e a fila ou tópico (conforme aplicável). Clique no botão **Criar**.

Agora, o ponto final do seu ponto final de barramento de serviço personalizado está configurado para usar a identidade atribuída ao sistema do seu hub, e ele tem permissão para acessar seu recurso de barramento de serviço, apesar de suas restrições de firewall. Agora você pode usar este ponto final para configurar uma regra de roteamento.


### <a name="egress-connectivity-to-storage-accounts-for-file-upload"></a>Conectividade de saída para contas de armazenamento para upload de arquivos

O recurso de upload de arquivos do IoT Hub permite que os dispositivos carreguem arquivos para uma conta de armazenamento de propriedade do cliente. Para permitir que o upload do arquivo funcione, tanto os dispositivos quanto o IoT Hub precisam ter conectividade com a conta de armazenamento. Se as restrições de firewall estiverem em vigor na conta de armazenamento, seus dispositivos precisarão usar qualquer mecanismo da conta de armazenamento suportado (incluindo [pontos finais privados,](../private-link/create-private-endpoint-storage-portal.md) [pontos finais de serviço](../virtual-network/virtual-network-service-endpoints-overview.md) ou [configuração de firewall direto)](../storage/common/storage-network-security.md)para obter conectividade. Da mesma forma, se as restrições de firewall estiverem em vigor na conta de armazenamento, o IoT Hub precisa ser configurado para acessar o recurso de armazenamento através da exceção confiável dos serviços da Microsoft. Para isso, seu IoT Hub deve ter uma identidade de serviço gerenciada (veja como [criar um hub com identidade de serviço gerenciada).](#create-an-iot-hub-with-managed-service-identity) Uma vez que uma identidade de serviço gerenciada seja provisionada, siga as etapas abaixo para dar permissão ao RBAC para a identidade de recursos do seu hub para acessar sua conta de armazenamento.

1. No portal Azure, navegue até a guia De controle de **acesso (IAM)** da sua conta de armazenamento e clique em **Adicionar** na seção Adicionar uma função **de atribuição.**

2. Selecione **O contribuinte de dados blob de armazenamento** como **função,** usuário, grupo ou principal de serviço do **Azure AD** como **Atribuindo acesso** e selecione o nome de recurso do seu IoT Hub na lista de paradas. Clique no botão **Salvar**.

3. Navegue até a guia **Firewalls e redes virtuais** em sua conta de armazenamento e habilite permitir o acesso a partir da opção **de redes selecionadas.** Na lista **Exceções,** verifique a caixa para **permitir que serviços confiáveis da Microsoft acessem essa conta de armazenamento**. Clique no botão **Salvar**.

4. Na página de recursos do seu IoT Hub, navegue até a guia **De upload de arquivo.**

5. Na página que aparece, selecione o contêiner que você pretende usar no armazenamento blob, configure as **configurações de notificação de arquivo,** **SAS TTL,** **TTL padrão** e **contagem máxima de entrega** conforme desejado. Selecione **Sistema Atribuído** como o **tipo de autenticação** ao ponto final do armazenamento. Clique no botão **Criar**.

Agora, seu ponto final de armazenamento para upload de arquivos está configurado para usar a identidade atribuída ao sistema do seu hub, e ele tem permissão para acessar seu recurso de armazenamento, apesar de suas restrições de firewall.


### <a name="egress-connectivity-to-storage-accounts-for-bulk-device-importexport"></a>Egress a conectividade com as contas de armazenamento para importação/exportação de dispositivos a granel

O IoT Hub oferece suporte à funcionalidade de [importar/exportar](./iot-hub-bulk-identity-mgmt.md) informações de dispositivos em massa a partir de/para um blob de armazenamento fornecido pelo cliente. Para permitir que o recurso de importação/exportação em massa funcione, tanto os dispositivos quanto o IoT Hub precisam ter conectividade com a conta de armazenamento.

Essa funcionalidade requer conectividade do IoT Hub à conta de armazenamento. Para acessar um recurso de barramento de serviço enquanto as restrições de firewall estão em vigor, seu IoT Hub precisa ter uma identidade de serviço gerenciada (veja como [criar um hub com identidade de serviço gerenciada).](#create-an-iot-hub-with-managed-service-identity) Uma vez que uma identidade de serviço gerenciada seja provisionada, siga as etapas abaixo para dar permissão ao RBAC para a identidade de recursos do seu hub para acessar seu barramento de serviço.

1. No portal Azure, navegue até a guia De controle de **acesso (IAM)** da sua conta de armazenamento e clique em **Adicionar** na seção Adicionar uma função **de atribuição.**

2. Selecione **O contribuinte de dados blob de armazenamento** como **função,** usuário, grupo ou principal de serviço do **Azure AD** como **Atribuindo acesso** e selecione o nome de recurso do seu IoT Hub na lista de paradas. Clique no botão **Salvar**.

3. Navegue até a guia **Firewalls e redes virtuais** em sua conta de armazenamento e habilite permitir o acesso a partir da opção **de redes selecionadas.** Na lista **Exceções,** verifique a caixa para **permitir que serviços confiáveis da Microsoft acessem essa conta de armazenamento**. Clique no botão **Salvar**.

Agora você pode usar as API's Azure IoT REST para [criar empregos de exportação](https://docs.microsoft.com/rest/api/iothub/service/jobclient/getimportexportjobs) de importação para obter informações sobre como usar a funcionalidade de importação/exportação em massa. Observe que você precisará `storageAuthenticationType="identityBased"` fornecer o corpo `inputBlobContainerUri="https://..."` e `outputBlobContainerUri="https://..."` o uso em sua solicitação e como URL de entrada e saída da sua conta de armazenamento, respectivamente.


O Azure IoT Hub SDK's também suporta essa funcionalidade no gerenciador de registro do cliente de serviço. O trecho de código a seguir mostra como iniciar um trabalho de importação ou exportação usando o C# SDK.

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


Para usar esta versão limitada da região dos SDKs Azure IoT com suporte a rede virtual para C#, Java e Node.js:

1. Crie uma variável `EnableStorageIdentity` de ambiente `1`nomeada e defina seu valor para .

2. Baixe o SDK: [Java](https://aka.ms/vnetjavasdk) | [C#](https://aka.ms/vnetcsharpsdk) | [Node.js](https://aka.ms/vnetnodesdk)
 
Para Python, baixe nossa versão limitada do GitHub.

1. Navegue até a página de versão do [GitHub](https://aka.ms/vnetpythonsdk).

2. Baixe o arquivo a seguir, que você encontrará na parte inferior da página de versão o cabeçalho chamado **ativos**.
    > *azure_iot_hub-2.2.0_limited-py2.py3-none-any.whl*

3. Abra um terminal e navegue até a pasta com o arquivo baixado.

4. Execute o seguinte comando para instalar o SDK do Serviço Python com suporte para redes virtuais:
    > instalação pip ./azure_iot_hub-2.2.0_limited-py2.py3-none-any.whl


## <a name="next-steps"></a>Próximas etapas

Use os links abaixo para saber mais sobre os recursos do IoT Hub:

* [Roteamento de mensagem](./iot-hub-devguide-messages-d2c.md)
* [Upload de arquivos](./iot-hub-devguide-file-upload.md)
* [Importação/exportação de dispositivos a granel](./iot-hub-bulk-identity-mgmt.md) 
