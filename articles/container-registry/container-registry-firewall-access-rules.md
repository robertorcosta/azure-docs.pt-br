---
title: Regras de acesso ao firewall
description: Configure regras para acessar um registro de contêiner do Azure atrás de um firewall, permitindo acesso à API REST ("whitelisting") e nomes de domínio de ponto final de armazenamento ou intervalos de endereços IP específicos de serviço.
ms.topic: article
ms.date: 02/11/2020
ms.openlocfilehash: 06fedea2adf5e73929f5752279f2bd7e7227e570
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77168015"
---
# <a name="configure-rules-to-access-an-azure-container-registry-behind-a-firewall"></a>Configure regras para acessar um registro de contêiner do Azure atrás de um firewall

Este artigo explica como configurar regras em seu firewall para permitir o acesso a um registro de contêiner do Azure. Por exemplo, um dispositivo Azure IoT Edge atrás de um firewall ou servidor proxy pode precisar acessar um registro de contêiner para puxar uma imagem de contêiner. Ou, um servidor bloqueado em uma rede local pode precisar de acesso para empurrar uma imagem.

Se, em vez disso, você quiser configurar as regras de acesso de rede de entrada em um registro de contêiner apenas dentro de uma rede virtual do Azure ou de uma faixa pública de endereçoIP, consulte [Restringir o acesso a um registro de contêiner Do Zure a partir de uma rede virtual](container-registry-vnet.md).

## <a name="about-registry-endpoints"></a>Sobre os pontos finais do registro

Para puxar ou empurrar imagens ou outros artefatos para um registro de contêiner do Azure, um cliente como um daemon Docker precisa interagir sobre HTTPS com dois pontos finais distintos.

* **Ponto final da API de registro REST** - As operações de autenticação e gerenciamento de registro são tratadas através do ponto final da API de descanso público do registro. Este ponto final é o nome do servidor de login do registro ou uma faixa de endereço IP associada. 

* **Ponto final de armazenamento** - O Azure [aloca o armazenamento blob](container-registry-storage.md) em contas do Azure Storage em nome de cada registro para gerenciar os dados para imagens de contêineres e outros artefatos. Quando um cliente acessa camadas de imagem em um registro de contêiner do Azure, ele faz solicitações usando um ponto final de conta de armazenamento fornecido pelo registro.

Se o seu registro for [replicado geo-replicado,](container-registry-geo-replication.md)um cliente pode precisar interagir com pontos finais de REST e armazenamento em uma região específica ou em várias regiões replicadas.

## <a name="allow-access-to-rest-and-storage-domain-names"></a>Permitir acesso a nomes de domínio REST e armazenamento

* **Ponto final REST** - Permitir acesso ao nome do servidor de login de registro totalmente qualificado, como`myregistry.azurecr.io`
* **Ponto final de armazenamento (dados)** - Permitir o acesso a todas as contas de armazenamento do Azure blob usando o curinga`*.blob.core.windows.net`


## <a name="allow-access-by-ip-address-range"></a>Permitir acesso por faixa de endereço IP

Se sua organização tiver políticas para permitir acesso apenas a endereços IP específicos ou faixas de endereços, baixe [Faixas ip do Azure e tags de serviço – Nuvem Pública](https://www.microsoft.com/download/details.aspx?id=56519).

Para encontrar as faixas ip de ponto final do ACR REST para as quais você precisa permitir o acesso, procure **o AzureContainerRegistry** no arquivo JSON.

> [!IMPORTANT]
> As faixas de endereçoip para serviços do Azure podem ser alteradas e as atualizações são publicadas semanalmente. Baixe o arquivo JSON regularmente e faça as atualizações necessárias em suas regras de acesso. Se o seu cenário envolve a configuração das regras do grupo de segurança de rede em uma rede virtual do Azure para acessar o Registro de Contêineres do Azure, use a [tag de serviço](#allow-access-by-service-tag) **AzureContainerRegistry.**
>

### <a name="rest-ip-addresses-for-all-regions"></a>Endereços IP REST para todas as regiões

```json
{
  "name": "AzureContainerRegistry",
  "id": "AzureContainerRegistry",
  "properties": {
    "changeNumber": 10,
    "region": "",
    "platform": "Azure",
    "systemService": "AzureContainerRegistry",
    "addressPrefixes": [
      "13.66.140.72/29",
    [...]
```

### <a name="rest-ip-addresses-for-a-specific-region"></a>Endereços IP REST para uma região específica

Pesquise a região específica, como **AzureContainerRegistry.AustraliaEast**.

```json
{
  "name": "AzureContainerRegistry.AustraliaEast",
  "id": "AzureContainerRegistry.AustraliaEast",
  "properties": {
    "changeNumber": 1,
    "region": "australiaeast",
    "platform": "Azure",
    "systemService": "AzureContainerRegistry",
    "addressPrefixes": [
      "13.70.72.136/29",
    [...]
```

### <a name="storage-ip-addresses-for-all-regions"></a>Armazenamento de endereços IP para todas as regiões

```json
{
  "name": "Storage",
  "id": "Storage",
  "properties": {
    "changeNumber": 19,
    "region": "",
    "platform": "Azure",
    "systemService": "AzureStorage",
    "addressPrefixes": [
      "13.65.107.32/28",
    [...]
```

### <a name="storage-ip-addresses-for-specific-regions"></a>Armazenamento de endereços IP para regiões específicas

Pesquise a região específica, como **Storage.AustraliaCentral**.

```json
{
  "name": "Storage.AustraliaCentral",
  "id": "Storage.AustraliaCentral",
  "properties": {
    "changeNumber": 1,
    "region": "australiacentral",
    "platform": "Azure",
    "systemService": "AzureStorage",
    "addressPrefixes": [
      "52.239.216.0/23"
    [...]
```

## <a name="allow-access-by-service-tag"></a>Permitir acesso por tag de serviço

Em uma rede virtual do Azure, use regras de segurança de rede para filtrar o tráfego de um recurso, como uma máquina virtual, para um registro de contêineres. Para simplificar a criação das regras da rede Azure, use a tag de [serviço](../virtual-network/security-overview.md#service-tags) **AzureContainerRegistry** . Uma tag de serviço representa um grupo de prefixos de endereço IP para acessar um serviço Azure globalmente ou por região do Azure. A tag é atualizada automaticamente quando os endereços mudam. 

Por exemplo, crie uma regra de grupo de segurança de rede de saída com destino **AzureContainerRegistry** para permitir o tráfego em um registro de contêiner do Azure. Para permitir o acesso à tag de serviço apenas em uma região específica, especifique a região no seguinte formato: **AzureContainerRegistry**. [*nome da região*].

## <a name="configure-client-firewall-rules-for-mcr"></a>Configure as regras de firewall do cliente para MCR

Se você precisar acessar o Microsoft Container Registry (MCR) por trás de um firewall, consulte a orientação para configurar [as regras de firewall do cliente MCR](https://github.com/microsoft/containerregistry/blob/master/client-firewall-rules.md). MCR é o registro principal de todas as imagens docker publicadas pela Microsoft, como imagens do Windows Server.

## <a name="next-steps"></a>Próximas etapas

* Conheça as [melhores práticas do Azure para segurança de rede](../security/fundamentals/network-best-practices.md)

* Saiba mais sobre [grupos de segurança](/azure/virtual-network/security-overview) em uma rede virtual Do Zure



<!-- IMAGES -->

<!-- LINKS - External -->

<!-- LINKS - Internal -->

