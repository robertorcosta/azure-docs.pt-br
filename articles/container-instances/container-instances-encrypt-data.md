---
title: Criptografar dados de implantação
description: Saiba mais sobre a criptografia de dados persistidos para os recursos de instância do contêiner e como criptografar os dados com uma chave gerenciada pelo cliente
ms.topic: article
ms.date: 01/17/2020
author: dkkapur
ms.author: dekapur
ms.openlocfilehash: ad232c5d9df9f6bfae3a79dbd72e2c68143be949
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79080353"
---
# <a name="encrypt-deployment-data"></a>Criptografar dados de implantação

Ao executar os recursos azure Container Instances (ACI) na nuvem, o serviço ACI coleta e persiste dados relacionados aos seus contêineres. A ACI criptografa automaticamente esses dados quando ele é persistido na nuvem. Essa criptografia protege seus dados para ajudar a cumprir os compromissos de segurança e conformidade da sua organização. A ACI também lhe dá a opção de criptografar esses dados com sua própria chave, dando-lhe maior controle sobre os dados relacionados às suas implantações de ACI.

## <a name="about-aci-data-encryption"></a>Sobre a criptografia de dados ACI 

Os dados em ACI são criptografados e descriptografados usando criptografia AES de 256 bits. Ele está habilitado para todas as implantações de ACI, e você não precisa modificar sua implantação ou contêineres para tirar proveito dessa criptografia. Isso inclui metadados sobre a implantação, variáveis de ambiente, chaves sendo passadas para seus contêineres e registros persistidos depois que seus contêineres são interrompidos para que você ainda possa vê-los. A criptografia não afeta o desempenho do grupo de contêineres e não há custo adicional para criptografia.

## <a name="encryption-key-management"></a>Gerenciamento de chaves de criptografia

Você pode confiar em chaves gerenciadas pela Microsoft para a criptografia de seus dados de contêiner, ou pode gerenciar a criptografia com suas próprias chaves. A tabela a seguir compara essas opções: 

|    |    Chaves gerenciadas pela Microsoft     |     Chaves gerenciadas pelo cliente     |
|----|----|----|
|    Operações de criptografia/descriptografia    |    Azure    |    Azure    |
|    Armazenamento de chaves    |    Loja-chave da Microsoft    |    Cofre de Chave do Azure    |
|    Responsabilidade de rotação chave    |    Microsoft    |    Cliente    |
|    Acesso à chave    |    Somente microsoft    |    Microsoft, Cliente    |

O restante do documento abrange as etapas necessárias para criptografar seus dados de implantação de ACI com sua chave (chave gerenciada pelo cliente). 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="encrypt-data-with-a-customer-managed-key"></a>Criptografar dados com uma chave gerenciada pelo cliente

### <a name="create-service-principal-for-aci"></a>Criar diretor de serviço para ACI

O primeiro passo é garantir que o [seu inquilino do Azure](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) tenha um principal de serviço designado para conceder permissões ao serviço Azure Container Instances. 

> [!IMPORTANT]
> Para executar o seguinte comando e criar um diretor de serviço com sucesso, confirme que você tem permissões para criar diretores de serviço em seu inquilino.
>

O seguinte comando CLI configurará a Controladoria ACI no ambiente Azure:

```azurecli-interactive
az ad sp create --id 6bb8e274-af5d-4df2-98a3-4fd78b4cafd9
```

A saída de execução deste comando deve mostrar-lhe um principal de serviço que foi configurado com "displayName": "Azure Container Instance Service".

No caso de você não conseguir criar com sucesso o principal do serviço:
* confirmar que você tem permissões para fazê-lo em seu inquilino
* verifique se um diretor de serviço já existe em seu inquilino para implantação na ACI. Você pode fazer `az ad sp show --id 6bb8e274-af5d-4df2-98a3-4fd78b4cafd9` isso executando e usando esse principal de serviço em vez

### <a name="create-a-key-vault-resource"></a>Crie um recurso do Key Vault

Crie um Cofre de Chaves Azure usando [o portal Azure,](https://docs.microsoft.com/azure/key-vault/quick-create-portal#create-a-vault) [CLI](https://docs.microsoft.com/azure/key-vault/quick-create-cli)ou [PowerShell.](https://docs.microsoft.com/azure/key-vault/quick-create-powershell) 

Para as propriedades do cofre principal, use as seguintes diretrizes: 
* Nome: é necessário um nome exclusivo. 
* Assinatura: escolha uma assinatura.
* Em Grupo de recursos, escolha um grupo de recursos existente ou crie um novo e insira um nome de grupo de recursos.
* No menu suspenso Local, escolha um local.
* Você pode deixar as outras opções para seus padrões ou escolher com base em requisitos adicionais.

> [!IMPORTANT]
> Ao usar as chaves gerenciadas pelo cliente para criptografar um modelo de implantação de ACI, recomenda-se que as duas propriedades seguintes sejam definidas no cofre de chaves, Soft Delete e Não Purga. Essas propriedades não são habilitadas por padrão, mas podem ser habilitadas usando o PowerShell ou o Azure CLI em um cofre de chaves novo ou existente.

### <a name="generate-a-new-key"></a>Gerar uma nova chave 

Uma vez que seu cofre principal seja criado, navegue até o recurso no portal Azure. No menu de navegação à esquerda da lâmina de recursos, em Configurações, clique em **Teclas**. Na exibição de "Chaves", clique em "Gerar/Importar" para gerar uma nova tecla. Use qualquer nome exclusivo para esta chave e quaisquer outras preferências com base em seus requisitos. 

![Gerar uma nova chave](./media/container-instances-encrypt-data/generate-key.png)

### <a name="set-access-policy"></a>Definir política de acesso

Crie uma nova política de acesso para permitir que o serviço ACI acesse sua Chave.

* Uma vez que sua chave tenha sido gerada, volte para a lâmina de recurso do cofre principal, em Configurações, clique em **Políticas de acesso**.
* Na página "Políticas de acesso" para o cofre principal, clique em **Adicionar política de acesso**.
* Defina as *permissões de chave* para incluir as permissões de chave **get** e **desembrulhar** ![](./media/container-instances-encrypt-data/set-key-permissions.png)
* Para *selecionar principal,* selecione **O serviço de instância do contêiner Azure**
* Clique em **Adicionar** na parte inferior 

A política de acesso deve agora aparecer nas políticas de acesso do seu cofre principal.

![Nova política de acesso](./media/container-instances-encrypt-data/access-policy.png)

### <a name="modify-your-json-deployment-template"></a>Modifique seu modelo de implantação JSON

> [!IMPORTANT]
> A criptografia de dados de implantação com uma chave gerenciada pelo cliente está disponível na versão mais recente da API (2019-12-01) que está sendo desatualizada no momento. Especifique esta versão da API no modelo de implantação. Se você tiver algum problema com isso, entre em contato com o Azure Support.

Uma vez que a chave do cofre chave e a política de acesso sejam configuradas, adicione as seguintes propriedades ao modelo de implantação da ACI. Saiba mais sobre a implantação de recursos ACI com um modelo no [Tutorial: Implante um grupo de vários contêineres usando um modelo de Gerenciador de recursos](https://docs.microsoft.com/azure/container-instances/container-instances-multi-container-group). 
* Em `resources`, `apiVersion` `2019-12-01`definido para .
* Na seção propriedades do grupo de `encryptionProperties`contêiner do modelo de implantação, adicione um , que contém os seguintes valores:
  * `vaultBaseUrl`: o nome DNS do seu cofre de chaves, pode ser encontrado na lâmina de visão geral do recurso do cofre chave no Portal
  * `keyName`: o nome da chave gerada anteriormente
  * `keyVersion`: a versão atual da chave. Isso pode ser encontrado clicando na própria chave (em "Chaves" na seção Configurações do recurso do cofre de chaves)
* as propriedades do `sku` grupo de `Standard`contêineres, adicione uma propriedade com valor. A `sku` propriedade é exigida na versão API 2019-12-01.

O trecho do modelo a seguir mostra essas propriedades adicionais para criptografar dados de implantação:

```json
[...]
"resources": [
    {
        "name": "[parameters('containerGroupName')]",
        "type": "Microsoft.ContainerInstance/containerGroups",
        "apiVersion": "2019-12-01",
        "location": "[resourceGroup().location]",    
        "properties": {
            "encryptionProperties": {
                "vaultBaseUrl": "https://example.vault.azure.net",
                "keyName": "acikey",
                "keyVersion": "xxxxxxxxxxxxxxxx"
            },
            "sku": "Standard",
            "containers": {
                [...]
            }
        }
    }
]
```

A seguir está um modelo completo, adaptado do modelo em [Tutorial: Implante um grupo de vários contêineres usando um modelo do Gerenciador de Recursos](https://docs.microsoft.com/azure/container-instances/container-instances-multi-container-group). 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "containerGroupName": {
      "type": "string",
      "defaultValue": "myContainerGroup",
      "metadata": {
        "description": "Container Group name."
      }
    }
  },
  "variables": {
    "container1name": "aci-tutorial-app",
    "container1image": "mcr.microsoft.com/azuredocs/aci-helloworld:latest",
    "container2name": "aci-tutorial-sidecar",
    "container2image": "mcr.microsoft.com/azuredocs/aci-tutorial-sidecar"
  },
  "resources": [
    {
      "name": "[parameters('containerGroupName')]",
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2019-12-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "encryptionProperties": {
            "vaultBaseUrl": "https://example.vault.azure.net",
            "keyName": "acikey",
            "keyVersion": "xxxxxxxxxxxxxxxx"
        },
        "sku": "Standard",  
        "containers": [
          {
            "name": "[variables('container1name')]",
            "properties": {
              "image": "[variables('container1image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "port": 80
                },
                {
                  "port": 8080
                }
              ]
            }
          },
          {
            "name": "[variables('container2name')]",
            "properties": {
              "image": "[variables('container2image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              }
            }
          }
        ],
        "osType": "Linux",
        "ipAddress": {
          "type": "Public",
          "ports": [
            {
              "protocol": "tcp",
              "port": "80"
            },
            {
                "protocol": "tcp",
                "port": "8080"
            }
          ]
        }
      }
    }
  ],
  "outputs": {
    "containerIPv4Address": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.ContainerInstance/containerGroups/', parameters('containerGroupName'))).ipAddress.ip]"
    }
  }
}
```

### <a name="deploy-your-resources"></a>Implante seus recursos

Se você criou e editou o arquivo de modelo em sua área de trabalho, você pode carregá-lo para o diretório Cloud Shell arrastando o arquivo para ele. 

Crie um grupo de recursos com o comando [az group create.][az-group-create]

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Implante o modelo com o comando [az group deployment create][az-group-deployment-create].

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file deployment-template.json
```

Em alguns segundos, você deverá receber uma resposta inicial do Azure. Uma vez que a implantação seja concluída, todos os dados relacionados a ele persistidos pelo serviço ACI serão criptografados com a chave que você forneceu.

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
