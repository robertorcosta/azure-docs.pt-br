---
title: Como implantar o serviço de gerenciamento de certificados OPC Vault - Azure | Microsoft Docs
description: Como implantar o serviço de gerenciamento de certificados OPC Vault do zero.
author: mregen
ms.author: mregen
ms.date: 08/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: f577059e1ebf70e3a9dfe9e538a9d3d49d7c8e96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "71200002"
---
# <a name="build-and-deploy-the-opc-vault-certificate-management-service"></a>Construir e implantar o serviço de gerenciamento de certificados OPC Vault

Este artigo explica como implantar o serviço de gerenciamento de certificados OPC Vault no Azure.

> [!NOTE]
> Para obter mais informações, consulte o [repositório](https://github.com/Azure/azure-iiot-opc-vault-service)do GitHub OPC Vault .

## <a name="prerequisites"></a>Pré-requisitos

### <a name="install-required-software"></a>Instalar o software necessário

Atualmente, a operação de compilação e implantação está limitada ao Windows.
As amostras são todas escritas para C# .NET Standard, que você precisa para construir o serviço e amostras para implantação.
Todas as ferramentas necessárias para o .NET Standard vêm com as ferramentas .NET Core. Consulte [Começar com .NET Core](https://docs.microsoft.com/dotnet/articles/core/getting-started).

1. [Instale o .NET Core 2.1+][dotnet-install].
2. [Instale o Docker][docker-url] (opcional, somente se a compilação docker local for necessária).
4. Instale as [ferramentas de linha de comando do Azure para o PowerShell][powershell-install].
5. Inscreva-se para uma [assinatura do Azure][azure-free].

### <a name="clone-the-repository"></a>Clonar o repositório

Se você ainda não fez isso, clone este repositório do GitHub. Abra um prompt de comando ou terminal e execute o seguinte:

```bash
git clone https://github.com/Azure/azure-iiot-opc-vault-service
cd azure-iiot-opc-vault-service 
```

Alternativamente, você pode clonar o repo diretamente no Visual Studio 2017.

### <a name="build-and-deploy-the-azure-service-on-windows"></a>Construa e implante o serviço Azure no Windows

Um script PowerShell fornece uma maneira fácil de implantar o microserviço OPC Vault e o aplicativo.

1. Abra uma janela PowerShell na raiz do repo. 
3. Vá para a `cd deploy`pasta de implantação .
3. Escolha um `myResourceGroup` nome para que isso não cause um conflito com outras páginas da Web implantadas. Consulte a seção "Nome do site já em uso" mais tarde neste artigo.
5. Inicie a `.\deploy.ps1` implantação com a instalação interativa ou insira uma linha de comando completa:  
`.\deploy.ps1  -subscriptionName "MySubscriptionName" -resourceGroupLocation "East US" -tenantId "myTenantId" -resourceGroupName "myResourceGroup"`
7. Se você planeja desenvolver com `-development 1` essa implantação, adicione para ativar a ui Swagger e implantar compilações de depuração.
6. Siga as instruções no script para fazer login em sua assinatura e forneça informações adicionais.
9. Depois de uma operação de compilação e implantação bem-sucedida, você deve ver a seguinte mensagem:
   ```
   To access the web client go to:
   https://myResourceGroup.azurewebsites.net

   To access the web service go to:
   https://myResourceGroup-service.azurewebsites.net

   To start the local docker GDS server:
   .\myResourceGroup-dockergds.cmd

   To start the local dotnet GDS server:
   .\myResourceGroup-gds.cmd
   ```

   > [!NOTE]
   > Em caso de problemas, consulte a seção "Falhas de implantação de solução de problemas" mais tarde no artigo.

8. Abra seu navegador favorito e abra a página do aplicativo:`https://myResourceGroup.azurewebsites.net`
8. Dê ao aplicativo web e ao microserviço OPC Vault alguns minutos para aquecer após a implantação. A página inicial da web pode ser pendurada no primeiro uso, por até um minuto, até que você obtenha as primeiras respostas.
11. Para dar uma olhada na API da Swagger, abra:`https://myResourceGroup-service.azurewebsites.net`
13. Para iniciar um servidor GDS local `.\myResourceGroup-gds.cmd`com dotnet, inicie . Com Docker, `.\myResourceGroup-dockergds.cmd`comece.

É possível reimplantar uma compilação com exatamente as mesmas configurações. Esteja ciente de que tal operação renova todos os segredos do aplicativo e pode redefinir algumas configurações nos registros de aplicativos do Azure Active Directory (Azure AD).

Também é possível reimplantar apenas os binários do aplicativo web. Com o `-onlyBuild 1`parâmetro, novos pacotes zip do serviço e do aplicativo são implantados nos aplicativos web.

Após a implantação bem-sucedida, você pode começar a usar os serviços. Consulte [Gerenciar o serviço de gerenciamento de certificados OPC Vault](howto-opc-vault-manage.md).

## <a name="delete-the-services-from-the-subscription"></a>Exclua os serviços da assinatura

Veja como:

1. Faça login no [portal Azure](https://portal.azure.com).
2. Vá para o grupo de recursos em que o serviço foi implantado.
3. Selecione **Excluir grupo de recursos** e confirme.
4. Depois de pouco tempo, todos os componentes de serviço implantados são excluídos.
5. Vá para as inscrições **do Azure Active Directory** > **App**.
6. Deve haver três registros listados para cada grupo de recursos implantados. Os registros têm os `resourcegroup-client`seguintes nomes: , `resourcegroup-module`. `resourcegroup-service` Exclua cada registro separadamente.

Agora todos os componentes implantados são removidos.

## <a name="troubleshooting-deployment-failures"></a>Falhas de implantação de solução de problemas

### <a name="resource-group-name"></a>Nome do grupo de recursos

Use um nome de grupo de recursos curto e simples. O nome também é usado para nomear recursos e o prefixo de URL do serviço. Como tal, ele deve cumprir os requisitos de nomeação de recursos.  

### <a name="website-name-already-in-use"></a>Nome do site já em uso

É possível que o nome do site já esteja em uso. Você precisa usar um nome de grupo de recursos diferente. Os nomes de host em uso https://resourcegroupname.azurewebsites.net https://resourgroupname-service.azurewebsites.netpelo script de implantação são: e .
Outros nomes de serviços são construídos pela combinação de hashes de nomes curtos, e são improváveis de entrar em conflito com outros serviços.

### <a name="azure-ad-registration"></a>Registro do Azure AD 

O script de implantação tenta registrar três aplicativos Azure AD no Azure AD. Dependendo de suas permissões no inquilino Azure AD selecionado, esta operação pode falhar. Há duas opções:

- Se você escolheu um inquilino Azure AD de uma lista de inquilinos, reinicie o script e escolha um diferente da lista.
- Alternativamente, implante um inquilino AD privado do Azure em outra assinatura. Reinicie o script e selecione para usá-lo.

## <a name="deployment-script-options"></a>Opções de script de implantação

O script toma os seguintes parâmetros:


```
-resourceGroupName
```

Este pode ser o nome de um grupo de recursos existente ou de um novo.

```
-subscriptionId
```


Este é o ID de assinatura onde os recursos serão implantados. É opcional.

```
-subscriptionName
```


Alternativamente, você pode usar o nome da assinatura.

```
-resourceGroupLocation
```


Esta é uma localização de grupo de recursos. Se especificado, este parâmetro tentará criar um novo grupo de recursos neste local. Este parâmetro também é opcional.


```
-tenantId
```


Este é o inquilino azure AD para usar. 

```
-development 0|1
```

Isso é para implantar para o desenvolvimento. Use a compilação de depuração e defina o ambiente ASP.NET para o Desenvolvimento. Criar `.publishsettings` para importação no Visual Studio 2017, para permitir que ele implante o aplicativo e o serviço diretamente. Este parâmetro também é opcional.

```
-onlyBuild 0|1
```

Isso é para reconstruir e reimplantar apenas os aplicativos da Web e reconstruir os contêineres Docker. Este parâmetro também é opcional.

[azure-free]:https://azure.microsoft.com/free/
[powershell-install]:https://azure.microsoft.com/downloads/#powershell
[docker-url]: https://www.docker.com/
[dotnet-install]: https://www.microsoft.com/net/learn/get-started

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como implantar o OPC Vault do zero, você pode:

> [!div class="nextstepaction"]
> [Gerenciar o cofre OPC](howto-opc-vault-manage.md)
