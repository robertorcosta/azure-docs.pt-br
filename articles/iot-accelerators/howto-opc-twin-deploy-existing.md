---
title: Como implantar um módulo OPC Twin em um projeto Azure existente | Microsoft Docs
description: Este artigo descreve como implantar o OPC Twin em um projeto existente. Você também pode aprender como solucionar falhas de implantação.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: b971ec13c71ccfd7d28ae6987593d09201b9b764
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73824112"
---
# <a name="deploy-opc-twin-to-an-existing-project"></a>Implantar o OPC Twin em um projeto existente

O módulo OPC Twin é executado no IoT Edge e fornece vários serviços de borda para os serviços OPC Twin and Registry.

O microserviço OPC Twin facilita a comunicação entre operadores de fábrica e dispositivos de servidor OPC UA no chão de fábrica através de um módulo OPC Twin IoT Edge. O microserviço expõe os serviços oPC UA (Procurar, Ler, Gravar e Executar) através de sua API REST. 

O microserviço de registro de dispositivos OPC UA fornece acesso a aplicativos OPC UA registrados e seus pontos finais. Os operadores e administradores podem registrar e cancelar o registro de novos aplicativos OPC UA e navegar pelos já existentes, incluindo seus pontos finais. Além do gerenciamento de aplicativos e pontos finais, o serviço de registro também cataloga módulos OPC Twin IoT Edge registrados. A API de serviço oferece controle da funcionalidade do módulo de borda, por exemplo, iniciando ou parando a detecção de servidores (serviços de varredura) ou ativando novos gêmeos de ponto final que podem ser acessados usando o microserviço OPC Twin.

O núcleo do módulo é a identidade do Supervisor. O supervisor gerencia o endpoint twin, que corresponde aos pontos finais do servidor OPC UA que são ativados usando a API de registro OPC UA correspondente. Este ponto final gêmeos traduzem OPC UA JSON recebido do microserviço OPC Twin rodando na nuvem em mensagens binárias OPC UA, que são enviadas através de um canal seguro stateful para o ponto final gerenciado. O supervisor também fornece serviços de descoberta que enviam eventos de detecção de dispositivos para o serviço de onboarding de dispositivos OPC UA para processamento, onde esses eventos resultam em atualizações para o registro oPC UA.  Este artigo mostra como implantar o módulo OPC Twin em um projeto existente.

> [!NOTE]
> Para obter mais informações sobre detalhes e instruções de implantação, consulte o [repositório](https://github.com/Azure/azure-iiot-opc-twin-module)GitHub .

## <a name="prerequisites"></a>Pré-requisitos

Certifique-se de que as extensões PowerShell e [AzureRM PowerShell estão](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) instaladas. Se você ainda não fez isso, clone este repositório do GitHub. Execute os seguintes comandos no PowerShell:

```powershell
git clone --recursive https://github.com/Azure/azure-iiot-components.git
cd azure-iiot-components
```

## <a name="deploy-industrial-iot-services-to-azure"></a>Implantar serviços industriais de IoT para o Azure

1. Em sua sessão powershell, execute:

    ```powershell
    set-executionpolicy -ExecutionPolicy Unrestricted -Scope Process
    .\deploy.cmd
    ```

2. Siga as instruções para atribuir um nome ao grupo de recursos da implantação e um nome ao site.   O script implanta os microserviços e suas dependências da plataforma Azure no grupo de recursos em sua assinatura do Azure.  O script também registra um aplicativo no seu inquilino Azure Active Directory (AAD) para suportar a autenticação baseada em OAUTH.  A implantação levará vários minutos.  Um exemplo do que você veria quando a solução for implantada com sucesso:

   ![Industrial IoT OPC Twin implantar para projeto existente](media/howto-opc-twin-deploy-existing/opc-twin-deploy-existing1.png)

   A saída inclui a URL do ponto final público. 

3. Uma vez que o script seja concluído `.env` com sucesso, selecione se deseja salvar o arquivo.  Você precisa `.env` do arquivo de ambiente se quiser se conectar ao ponto final da nuvem usando ferramentas como o Console ou implantar módulos para desenvolvimento e depuração.

## <a name="troubleshooting-deployment-failures"></a>Falhas de implantação de solução de problemas

### <a name="resource-group-name"></a>Nome do grupo de recursos

Certifique-se de usar um nome de grupo de recursos curto e simples.  O nome também é usado para nomear recursos como tal, ele deve cumprir os requisitos de nomeação de recursos.  

### <a name="website-name-already-in-use"></a>Nome do site já em uso

É possível que o nome do site já esteja em uso.  Se você encontrar esse erro, você precisa usar um nome de aplicativo diferente.

### <a name="azure-active-directory-aad-registration"></a>Registro do Azure Active Directory (AAD)

O script de implantação tenta registrar dois aplicativos AAD no Azure Active Directory.  Dependendo dos seus direitos para o inquilino AAD selecionado, a implantação pode falhar. Há duas opções:

1. Se você escolheu um inquilino AAD de uma lista de inquilinos, reinicie o script e escolha um diferente da lista.
2. Alternativamente, implante um inquilino AAD privado em outra assinatura, reinicie o script e selecione para usá-lo.

> [!WARNING]
> NUNCA continue sem autenticação.  Se você optar por fazê-lo, qualquer pessoa pode acessar seus pontos finais OPC Twin da Internet não autenticados.   Você sempre pode escolher a [opção de implantação "local"](howto-opc-twin-deploy-dependencies.md) para chutar os pneus.

## <a name="deploy-an-all-in-one-industrial-iot-services-demo"></a>Implantar uma demonstração de serviços de IoT industrial tudo-em-um

Em vez de apenas os serviços e dependências, você também pode implantar uma demonstração all-in-one.  A demonstração em uma contém três servidores OPC UA, o módulo OPC Twin, todos os microsserviços e uma amostra de Aplicativo Web.  Destina-se a fins de demonstração.

1. Certifique-se de ter um clone do repositório (veja acima). Abra um prompt PowerShell na raiz do repositório e execute:

    ```powershell
    set-executionpolicy -ExecutionPolicy Unrestricted -Scope Process
    .\deploy -type demo
    ```

2. Siga as instruções para atribuir um novo nome ao grupo de recursos e um nome ao site.  Uma vez implantado com sucesso, o script exibirá a URL do ponto final do aplicativo web.

## <a name="deployment-script-options"></a>Opções de script de implantação

O script toma os seguintes parâmetros:

```powershell
-type
```

O tipo de implantação (vm, local, demonstração)

```powershell
-resourceGroupName
```

Pode ser o nome de um grupo de recursos existente ou de um novo.

```powershell
-subscriptionId
```

Opcional, o ID de assinatura onde os recursos serão implantados.

```powershell
-subscriptionName
```

Ou o nome da assinatura.

```powershell
-resourceGroupLocation
```

Opcional, um local de grupo de recursos. Se especificado, tentará criar um novo grupo de recursos neste local.

```powershell
-aadApplicationName
```

Um nome para o aplicativo AAD para registrar em.

```powershell
-tenantId
```

Inquilino AAD para usar.

```powershell
-credentials
```

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como implantar o OPC Twin em um projeto existente, aqui está o próximo passo sugerido:

> [!div class="nextstepaction"]
> [Comunicação segura do Cliente OPC UA e OPC UA PLC](howto-opc-vault-secure.md)
