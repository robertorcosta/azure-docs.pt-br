---
title: Políticas de alocação personalizadas com o Serviço de Provisionamento de Dispositivos Hub Azure IoT
description: Como usar políticas de alocação personalizadas com o DPS (Azure IoT Hub Device Provisioning Service, serviço de provisionamento de dispositivos do Hub Azure)
author: wesmc7777
ms.author: wesmc
ms.date: 11/14/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 87ffca1957d4ec449753f1966ed05cf3948f5ca2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75453937"
---
# <a name="how-to-use-custom-allocation-policies"></a>Como usar políticas de alocação personalizadas

Uma política de alocação personalizada proporciona a você mais controle sobre como os dispositivos são atribuídos a um Hub IoT. Isso é feito usando código personalizado em uma [função do Azure](../azure-functions/functions-overview.md) para atribuir dispositivos a um Hub IoT. O serviço de provisionamento de dispositivos chama o código de função do Azure, fornecendo todas as informações relevantes sobre o dispositivo e o registro. O código de função é executado e retorna as informações do Hub IoT para provisionar o dispositivo.

Ao usar políticas de alocação personalizadas, você define suas próprias políticas de alocação quando as políticas fornecidas pelo Serviço de Provisionamento de Dispositivos não atendem aos requisitos do seu cenário.

Por exemplo, talvez você queira examinar o certificado que um dispositivo está usando durante o provisionamento e atribuir o dispositivo a um hub de IoT com base em uma propriedade de certificado. Ou, talvez você tenha informações armazenadas em um banco de dados para seus dispositivos e precise consultar o banco de dados para determinar a qual hub de IoT um dispositivo deve ser atribuído.

Este artigo demonstra uma política de alocação personalizada usando uma função do Azure escrita em C#. Dois novos Hubs IoT são criados, representando uma *Divisão de Resistências da Contoso* e uma *Divisão de Bombas Térmicas da Contoso*. Dispositivos solicitando provisionamento devem ter uma ID de registro com um dos sufixos a seguir para serem aceitos para o provisionamento:

* **-contoso-tstrsd-007**: Divisão de Resistências da Contoso
* **-contoso-hpsd-088**: Divisão de Bombas Térmicas da Contoso

Os dispositivos serão provisionados com base em um destes sufixos necessários na ID do registro. Esses dispositivos serão simulados usando uma amostra de provisionamento incluída no [SDK de C do IoT do Azure](https://github.com/Azure/azure-iot-sdk-c).

Você executa as seguintes etapas neste artigo:

* Usar a CLI do Azure para criar dois Hubs IoT da divisão da Contoso (**Divisão de Resistências da Contoso** e **Divisão de Bombas Térmicas da Contoso**)
* Criar um registro de grupo usando uma função do Azure para a política de alocação personalizada
* Crie chaves de dispositivo para duas simulações do dispositivo.
* Preparar o ambiente de desenvolvimento para o SDK de C do IoT do Azure
* Simule os dispositivos e verifique se eles estão provisionados de acordo com o código de exemplo na política de alocação personalizada

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Os pré-requisitos a seguir são para um ambiente de desenvolvimento do Windows. Para Linux ou macOS, confira a seção apropriada em [Preparar seu ambiente de desenvolvimento](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) na documentação do SDK.

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) com a carga de trabalho ["Desenvolvimento para desktop com C++"](https://docs.microsoft.com/cpp/?view=vs-2019#pivot=workloads) habilitada. Também há suporte para o Visual Studio 2015 e o Visual Studio 2017.

* Versão mais recente do [Git](https://git-scm.com/download/) instalada.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-the-provisioning-service-and-two-divisional-iot-hubs"></a>Crie o serviço de provisionamento e dois hubs de IoT divisionais

Nesta seção, você usa o Azure Cloud Shell para criar um serviço de provisionamento e dois hubs de IoT representando a **Divisão de Torções contoso** e a **divisão Bombas de Calor Contoso**.

> [!TIP]
> Os comandos utilizados neste artigo criam o serviço de provisionamento e outros recursos na localização dos EUA Ocidentais. Recomendamos que você crie seus recursos na região mais próxima de você que suporta o Serviço de Provisionamento de Dispositivos. Você pode exibir uma lista de locais disponíveis executando o comando `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` ou acessando a página [Status do Azure](https://azure.microsoft.com/status/) e pesquisando por "Serviço de Provisionamento de Dispositivos". Nos comandos, os locais podem ser especificados em um formato de palavra ou de várias palavras; por exemplo: Westus, West US, WEST US, etc. O valor não é sensível ao caso. Se você usar o formato de várias palavras para especificar o local, coloque o valor entre aspas, por exemplo, `-- location "West US"`.
>

1. Use o Azure Cloud Shell para criar um grupo de recursos com o comando [az group create](/cli/azure/group#az-group-create). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

    O exemplo a seguir cria um grupo de recursos chamado *contoso-us-resource-group* na região *do westus.* É recomendável que você use esse grupo para todos os recursos criados neste artigo. Esta abordagem tornará a limpeza mais fácil depois que você terminar.

    ```azurecli-interactive 
    az group create --name contoso-us-resource-group --location westus
    ```

2. Use o Azure Cloud Shell para criar um serviço de provisionamento de dispositivos com o comando [az iot dps create.](/cli/azure/iot/dps#az-iot-dps-create) O serviço de provisionamento será adicionado ao *grupo de recursos contoso-us.*

    O exemplo a seguir cria um serviço de provisionamento chamado *contoso-provisioning-service-1098* na localização *westus.* Você deve usar um nome de serviço único. Coma seu próprio sufixo no nome do serviço no lugar de **1098**.

    ```azurecli-interactive 
    az iot dps create --name contoso-provisioning-service-1098 --resource-group contoso-us-resource-group --location westus
    ```

    Esse comando pode demorar um pouco para ser concluído.

3. Use o Azure Cloud Shell para criar o Hub IoT **Divisão de Resistências da Contoso** com o comando [az iot hub create](/cli/azure/iot/hub#az-iot-hub-create). O Hub IoT será adicionado ao *contoso-us-resource-group*.

    O exemplo a seguir cria um hub de IoT chamado *contoso-torasias-hub-1098* na localização *westus.* Você deve usar um nome de hub único. Crie seu próprio sufixo no nome do hub no lugar de **1098**. O código de exemplo para a política de alocação personalizada requer `-toasters-` no nome do hub.

    ```azurecli-interactive 
    az iot hub create --name contoso-toasters-hub-1098 --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    Esse comando pode demorar um pouco para ser concluído.

4. Use o Azure Cloud Shell para criar o Hub IoT **Divisão de Bombas Térmicas da Contoso** com o comando [az iot hub create](/cli/azure/iot/hub#az-iot-hub-create). Este Hub IoT também será adicionado ao *contoso-us-resource-group*.

    O exemplo a seguir cria um hub de IoT chamado *contoso-heatpumps-hub-1098* na localização *westus.* Você deve usar um nome de hub único. Crie seu próprio sufixo no nome do hub no lugar de **1098**. O código de exemplo para a política de alocação personalizada requer `-heatpumps-` no nome do hub.

    ```azurecli-interactive 
    az iot hub create --name contoso-heatpumps-hub-1098 --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    Esse comando pode demorar um pouco para ser concluído.

## <a name="create-the-custom-allocation-function"></a>Crie a função de alocação personalizada

Nesta seção, você cria uma função Azure que implementa sua política de alocação personalizada. Esta função decide para qual hub de IoT divisional um dispositivo deve ser registrado com base em se seu ID de registro contém a string **-contoso-tstrsd-007** ou **-contoso-hpsd-088**. Ele também define o estado inicial do dispositivo gêmeo com base em se o dispositivo é uma torradeira ou uma bomba de calor.

1. Faça login no [portal Azure](https://portal.azure.com). Na sua página inicial, selecione **+ Crie um recurso**.

2. Na caixa de pesquisa *Pesquisar no Marketplace,* digite "Function App". Na lista de baixa, selecione **Function App**e selecione **Criar**.

3. Na página **criar aplicativo de função,** na guia **Noções básicas,** digite as seguintes configurações para o seu novo aplicativo de função e selecione **'Revisar+ criar':**

    **Grupo de recursos**: Selecione o **grupo de recursos contoso-us para** manter todos os recursos criados neste artigo juntos.

    **Nome do aplicativo de função**: Digite um nome de aplicativo de função exclusiva. Este exemplo usa **contoso-function-app-1098**.

    **Publicar**: Verificar se **o Código** está selecionado.

    **Pilha de tempo de execução**: Selecione **.NET Core** a partir da queda.

    **Região**: Selecione a mesma região do seu grupo de recursos. Este exemplo usa **o Oeste dos EUA.**

    > [!NOTE]
    > Por padrão, o Application Insights está ativado. O Application Insights não é necessário para este artigo, mas pode ajudá-lo a entender e investigar quaisquer problemas que você encontrar com a alocação personalizada. Se preferir, você pode desativar o Application Insights selecionando a guia **Monitoramento** e, em seguida, selecionando **Não** para **Habilitar insights de aplicativos**.

    ![Crie um aplicativo de função azure para hospedar a função de alocação personalizada](./media/how-to-use-custom-allocation-policies/create-function-app.png)

4. Na página **Resumo,** selecione **Criar** para criar o aplicativo de função. A implantação pode levar vários minutos. Quando ele for concluído, **selecione Ir para recurso**.

5. No painel esquerdo da página **Visão geral** **+** do aplicativo de função, selecione ao lado **de Funções** para adicionar uma nova função.

    ![Adicione uma função ao Aplicativo de Função](./media/how-to-use-custom-allocation-policies/create-function.png)

6. Nas **funções do Azure para .NET - para iniciar** a página, para a etapa **ESCOLHA UM AMBIENTE DE IMPLANTAÇÃO,** selecione o azulejo **no portal** e selecione **Continuar**.

    ![Selecione o ambiente de desenvolvimento de portais](./media/how-to-use-custom-allocation-policies/function-choose-environment.png)

7. Na página seguinte, para a etapa **CRIAR UMA FUNÇÃO,** selecione o **webhook + aPI** e selecione **Criar**. Uma função chamada **HttpTrigger1** é criada e o portal exibe o conteúdo do arquivo de código **run.csx.**

8. A referência exigia pacotes Nuget. Para criar o dispositivo inicial gêmeo, a função de alocação personalizada usa classes definidas em dois pacotes Nuget que devem ser carregados no ambiente de hospedagem. Com funções do Azure, os pacotes Nuget são referenciados usando um arquivo *function.host.* Nesta etapa, você salva e faz upload de um arquivo *function.host.*

    1. Copie as linhas a seguir no seu editor favorito e salve o arquivo no computador como *function.host*.

        ```xml
        <Project Sdk="Microsoft.NET.Sdk">  
            <PropertyGroup>  
                <TargetFramework>netstandard2.0</TargetFramework>  
            </PropertyGroup>  
            <ItemGroup>  
                <PackageReference Include="Microsoft.Azure.Devices.Provisioning.Service" Version="1.5.0" />  
                <PackageReference Include="Microsoft.Azure.Devices.Shared" Version="1.16.0" />  
            </ItemGroup>  
        </Project>
        ```

    2. Na função **HttpTrigger1,** expanda a guia **Exibir arquivos** no lado direito da janela.

        ![Arquivos de exibição abertos](./media/how-to-use-custom-allocation-policies/function-open-view-files.png)

    3. Selecione **Upload**, navegue até o arquivo **function.proj** e selecione **Abrir** para carregar o arquivo.

        ![Selecione o arquivo de upload](./media/how-to-use-custom-allocation-policies/function-choose-upload-file.png)

9. Substitua o código para a função **HttpTrigger1** pelo seguinte código e selecione **Salvar**:

    ```csharp
    #r "Newtonsoft.Json"

    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;

    using Microsoft.Azure.Devices.Shared;               // For TwinCollection
    using Microsoft.Azure.Devices.Provisioning.Service; // For TwinState

    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        // Get request body
        string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
        dynamic data = JsonConvert.DeserializeObject(requestBody);

        log.LogInformation("Request.Body:...");
        log.LogInformation(requestBody);

        // Get registration ID of the device
        string regId = data?.deviceRuntimeContext?.registrationId;

        string message = "Uncaught error";
        bool fail = false;
        ResponseObj obj = new ResponseObj();

        if (regId == null)
        {
            message = "Registration ID not provided for the device.";
            log.LogInformation("Registration ID : NULL");
            fail = true;
        }
        else
        {
            string[] hubs = data?.linkedHubs.ToObject<string[]>();

            // Must have hubs selected on the enrollment
            if (hubs == null)
            {
                message = "No hub group defined for the enrollment.";
                log.LogInformation("linkedHubs : NULL");
                fail = true;
            }
            else
            {
                // This is a Contoso Toaster Model 007
                if (regId.Contains("-contoso-tstrsd-007"))
                {
                    //Find the "-toasters-" IoT hub configured on the enrollment
                    foreach(string hubString in hubs)
                    {
                        if (hubString.Contains("-toasters-"))
                            obj.iotHubHostName = hubString;
                    }

                    if (obj.iotHubHostName == null)
                    {
                        message = "No toasters hub found for the enrollment.";
                        log.LogInformation(message);
                        fail = true;
                    }
                    else
                    {
                        // Specify the initial tags for the device.
                        TwinCollection tags = new TwinCollection();
                        tags["deviceType"] = "toaster";

                        // Specify the initial desired properties for the device.
                        TwinCollection properties = new TwinCollection();
                        properties["state"] = "ready";
                        properties["darknessSetting"] = "medium";

                        // Add the initial twin state to the response.
                        TwinState twinState = new TwinState(tags, properties);
                        obj.initialTwin = twinState;
                    }
                }
                // This is a Contoso Heat pump Model 008
                else if (regId.Contains("-contoso-hpsd-088"))
                {
                    //Find the "-heatpumps-" IoT hub configured on the enrollment
                    foreach(string hubString in hubs)
                    {
                        if (hubString.Contains("-heatpumps-"))
                            obj.iotHubHostName = hubString;
                    }

                    if (obj.iotHubHostName == null)
                    {
                        message = "No heat pumps hub found for the enrollment.";
                        log.LogInformation(message);
                        fail = true;
                    }
                    else
                    {
                        // Specify the initial tags for the device.
                        TwinCollection tags = new TwinCollection();
                        tags["deviceType"] = "heatpump";

                        // Specify the initial desired properties for the device.
                        TwinCollection properties = new TwinCollection();
                        properties["state"] = "on";
                        properties["temperatureSetting"] = "65";

                        // Add the initial twin state to the response.
                        TwinState twinState = new TwinState(tags, properties);
                        obj.initialTwin = twinState;
                    }
                }
                // Unrecognized device.
                else
                {
                    fail = true;
                    message = "Unrecognized device registration.";
                    log.LogInformation("Unknown device registration");
                }
            }
        }

        log.LogInformation("\nResponse");
        log.LogInformation((obj.iotHubHostName != null) ? JsonConvert.SerializeObject(obj) : message);

        return (fail)
            ? new BadRequestObjectResult(message) 
            : (ActionResult)new OkObjectResult(obj);
    }

    public class ResponseObj
    {
        public string iotHubHostName {get; set;}
        public TwinState initialTwin {get; set;}
    }
    ```

## <a name="create-the-enrollment"></a>Criar o registro

Nesta seção, você criará um novo grupo de inscrição que usa a política de alocação personalizada. Para simplificar, este artigo usa o [Atestado de chave simétrica](concepts-symmetric-key-attestation.md) com o registro. Para uma solução mais segura, considere usar o [Atestado de certificado X.509](concepts-security.md#x509-certificates) com uma cadeia de confiança.

1. Ainda no [portal Azure,](https://portal.azure.com)abra seu serviço de provisionamento.

2. Selecione **Gerenciar inscrições** no painel esquerdo e, em seguida, selecione o botão **Adicionar grupo** de inscrição na parte superior da página.

3. No **Grupo adicionar inscrição,** digite as seguintes informações e selecione o botão **Salvar.**

    **Nome do grupo**: insira **contoso-custom-allocated-devices**.

    **Tipo de atestado**: Selecione **Chave simétrica**.

    **Gerar chaves automaticamente**: esta caixa de seleção já deve estar marcada.

    **Selecione como você deseja atribuir dispositivos a hubs**: selecione **Personalizado (Usar a função do Azure)**.

    ![Adicionar grupo de registros de alocação personalizado para atestado de chave simétrica](./media/how-to-use-custom-allocation-policies/create-custom-allocation-enrollment.png)

4. No **Add Registration Group**, **selecione Link um novo hub de IoT** para vincular ambos os seus novos hubs de IoT divisionais.

    Execute este passo para ambos os seus hubs de IoT divisional.

    **Assinatura**: se você tem várias assinaturas, escolha a assinatura em que você criou os Hubs IoT divisionários.

    **Hub IoT**: selecione um dos hubs divisionários que você criou.

    **Política de acesso**: escolha **iothubowner**.

    ![Vincular os Hubs IoT divisionários ao serviço de provisionamento](./media/how-to-use-custom-allocation-policies/link-divisional-hubs.png)

5. Em **Adicionar grupo de registros**, depois que ambos os Hubs IoT divisionários foram vinculados, você deve selecioná-los como o grupo de Hubs IoT para o grupo de registro, conforme mostrado abaixo:

    ![Criar o grupo de hubs divisionários para o registro](./media/how-to-use-custom-allocation-policies/enrollment-divisional-hub-group.png)

6. Ao **adicionar grupo de inscrição,** desça até a seção Selecionar função do **Azure,** selecione o aplicativo Função que você criou na seção anterior. Em seguida, selecione a função criada e selecione Salvar para salvar o grupo de inscrição.

    ![Selecione a função e salve o grupo de inscrição](./media/how-to-use-custom-allocation-policies/save-enrollment.png)

7. Após salvar o registro, abra-o novamente e anote a **Chave primária**. Para que as chaves sejam geradas, é preciso primeiro salvar o registro. Essa chave será usada mais tarde para gerar chaves de dispositivo exclusivas para dispositivos simulados.

## <a name="derive-unique-device-keys"></a>Derivar chaves de dispositivo exclusivas

Nesta seção, você cria duas chaves de dispositivo exclusivas. Uma chave será usada para um dispositivo de resistência simulado. A outra chave será usada para um dispositivo de bomba térmica simulado.

Para gerar a chave do dispositivo, você usa a **chave primária** que você observou anteriormente para calcular o [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) do ID de registro do dispositivo para cada dispositivo e converter o resultado em formato Base64. Para obter mais informações sobre como criar chaves de dispositivo derivadas com grupos de registro, veja a seção de registros de grupo de [Atestado de chave simétrica](concepts-symmetric-key-attestation.md).

Para o exemplo neste artigo, use as duas IDs de registro de dispositivo a seguir e compute uma chave de dispositivo para ambos os dispositivos. As duas IDs de registro têm um sufixo válido para trabalhar com o código de exemplo para a política de alocação personalizada:

* **breakroom499-contoso-tstrsd-007**
* **mainbuilding167-contoso-hpsd-088**

### <a name="linux-workstations"></a>Estações de trabalho do Linux

Se você estiver usando uma estação de trabalho Linux, você pode usar o openssl para gerar as chaves do dispositivo derivado, como mostrado no exemplo a seguir.

1. Substitua o valor de **CHAVE** pela **Chave primária** que você anotou anteriormente.

    ```bash
    KEY=oiK77Oy7rBw8YB6IS6ukRChAw+Yq6GC61RMrPLSTiOOtdI+XDu0LmLuNm11p+qv2I+adqGUdZHm46zXAQdZoOA==

    REG_ID1=breakroom499-contoso-tstrsd-007
    REG_ID2=mainbuilding167-contoso-hpsd-088

    keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
    devkey1=$(echo -n $REG_ID1 | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64)
    devkey2=$(echo -n $REG_ID2 | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64)

    echo -e $"\n\n$REG_ID1 : $devkey1\n$REG_ID2 : $devkey2\n\n"
    ```

    ```bash
    breakroom499-contoso-tstrsd-007 : JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=
    mainbuilding167-contoso-hpsd-088 : 6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=
    ```

### <a name="windows-based-workstations"></a>Estações de trabalho baseadas em Windows

Se você estiver usando uma estação de trabalho baseada no Windows, você pode usar o PowerShell para gerar a chave do dispositivo derivado, como mostrado no exemplo a seguir.

1. Substitua o valor de **CHAVE** pela **Chave primária** que você anotou anteriormente.

    ```powershell
    $KEY='oiK77Oy7rBw8YB6IS6ukRChAw+Yq6GC61RMrPLSTiOOtdI+XDu0LmLuNm11p+qv2I+adqGUdZHm46zXAQdZoOA=='

    $REG_ID1='breakroom499-contoso-tstrsd-007'
    $REG_ID2='mainbuilding167-contoso-hpsd-088'

    $hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
    $hmacsha256.key = [Convert]::FromBase64String($key)
    $sig1 = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID1))
    $sig2 = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID2))
    $derivedkey1 = [Convert]::ToBase64String($sig1)
    $derivedkey2 = [Convert]::ToBase64String($sig2)

    echo "`n`n$REG_ID1 : $derivedkey1`n$REG_ID2 : $derivedkey2`n`n"
    ```

    ```powershell
    breakroom499-contoso-tstrsd-007 : JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=
    mainbuilding167-contoso-hpsd-088 : 6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=
    ```

Os dispositivos simulados usarão as chaves do dispositivo derivadas com cada ID de registro para realizar o atestado de chave simétrica.

## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Preparar um ambiente de desenvolvimento do SDK de C do IoT do Azure

Nesta seção, você prepara o ambiente de desenvolvimento usado para construir o [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c). O SDK inclui o código de exemplo para o dispositivo simulado. Este dispositivo simulado tentará realizar provisionamento durante a sequência de inicialização do dispositivo.

Esta seção é voltada para uma estação de trabalho baseada em Windows. Para obter um exemplo de Linux, consulte a configuração das VMs em [Como provisionar para multilocação](how-to-provision-multitenant.md).

1. Baixe o [sistema de build CMake](https://cmake.org/download/).

    É importante que os pré-requisitos do Visual Studio (Visual Studio e a carga de trabalho de "Desenvolvimento para Desktop com C++") estejam instalados em seu computador, **antes** da instalação de `CMake`. Após a instalação dos pré-requisitos e verificação do download, instale o sistema de compilação CMake.

2. Localize o nome da tag para a [versão mais recente](https://github.com/Azure/azure-iot-sdk-c/releases/latest) do SDK.

3. Abra um prompt de comando ou o shell Bash do Git. Execute os comandos a seguir para clonar a versão mais recente do repositório do GitHub do [SDK do Azure IoT C](https://github.com/Azure/azure-iot-sdk-c). Use a tag que você encontrou na etapa anterior como o valor para o parâmetro `-b`:

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Essa operação deve demorar alguns minutos.

4. Crie um subdiretório `cmake` no diretório raiz do repositório git e navegue até essa pasta. Execute os seguintes comandos do diretório `azure-iot-sdk-c`:

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

5. Execute o comando a seguir, que cria uma versão do SDK específica para a plataforma cliente de desenvolvimento. Uma solução do Visual Studio para o dispositivo simulado será gerada no diretório `cmake`. 

    ```cmd
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```

    Se `cmake` não encontrar o compilador C++, você pode ter erros de compilação durante a execução do comando. Se isso acontecer, tente executar o comando no [prompt de comando do Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs).

    Após o sucesso da compilação, as últimas linhas de saída serão semelhantes à seguinte saída:

    ```cmd/sh
    $ cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```

## <a name="simulate-the-devices"></a>Simular os dispositivos

Nesta seção, você atualiza uma amostra de provisionamento chamada **prov\_dev\_client\_sample** localizada no Azure IoT C SDK que você configurou anteriormente.

Esse código de exemplo simula uma sequência de inicialização do dispositivo que envia a solicitação de provisionamento à sua instância do Serviço de Provisionamento de Dispositivos. A sequência de inicialização fará com que o dispositivo de resistência seja reconhecido e atribuído ao Hub IoT usando a política de alocação personalizada.

1. No portal do Azure, selecione a guia **Visão Geral** de seu serviço de Provisionamento de Dispositivos e anote o valor de **_Escopo da ID_**.

    ![Extrair informações do ponto de extremidade do Serviço de Provisionamento de Dispositivo na folha do portal](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. No Visual Studio, abra o arquivo de solução **azure_iot_sdks.sln**, que foi gerado pela execução de CMake anteriormente. O arquivo da solução deve estar no seguinte local:

    ```
    azure-iot-sdk-c\cmake\azure_iot_sdks.sln
    ```

3. Na janela *Gerenciador de Soluções* do Visual Studio, navegue até a pasta **Provisionar\_Exemplos**. Expanda o projeto de exemplo chamado **prov\_dev\_client\_sample**. Expanda **Arquivos de Origem** e abra **prov\_dev\_client\_sample.c**.

4. Localize a constante `id_scope` e substitua o valor pelo seu valor de **Escopo de ID** copiado anteriormente. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

5. Encontre a definição da função `main()` no mesmo arquivo. Certifique-se de que a variável `hsm_type` está configurada para `SECURE_DEVICE_TYPE_SYMMETRIC_KEY`, conforme mostrado abaixo:

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

6. Clique com botão direito do mouse no projeto **prov\_dev\_client\_sample** e selecione **Definir como Projeto de Inicialização**.

### <a name="simulate-the-contoso-toaster-device"></a>Simular o dispositivo de resistência da Contoso

1. Para simular o dispositivo de resistência, localize a chamada para `prov_dev_set_symmetric_key_info()` em **prov\_dev\_client\_sample.c** que é comentado.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Descomente a chamada de função e substitua os valores do espaço reservado (incluindo os suportes angulares) pelo ID de registro de torradeira e a chave do dispositivo derivado que você gerou anteriormente. O valor da chave **JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=** mostrado abaixo é fornecido apenas como um exemplo.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("breakroom499-contoso-tstrsd-007", "JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=");
    ```

    Salve o arquivo.

2. No menu Visual Studio, selecione **Debug** > **Start sem depuração** para executar a solução. No prompt para reconstruir o projeto, selecione **Sim**, para reconstruir o projeto antes de ser executado.

    A saída a seguir é um exemplo do dispositivo de torradeira simulado inicializando e conectando-se com sucesso à instância de serviço de provisionamento a ser atribuído ao hub de Torças IoT pela política de alocação personalizada:

    ```cmd
    Provisioning API Version: 1.3.6

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-toasters-hub-1098.azure-devices.net, deviceId: breakroom499-contoso-tstrsd-007

    Press enter key to exit:
    ```

### <a name="simulate-the-contoso-heat-pump-device"></a>Simular o dispositivo de bomba térmica da Contoso

1. Para simular o dispositivo de bomba térmica, atualize a chamada para `prov_dev_set_symmetric_key_info()` em **prov\_dev\_client\_sample.c** novamente com a ID de registro de bomba térmica e a chave do dispositivo derivada gerada anteriormente. O valor de chave **6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=** mostrado abaixo também é fornecido apenas como um exemplo.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("mainbuilding167-contoso-hpsd-088", "6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=");
    ```

    Salve o arquivo.

2. No menu Visual Studio, selecione **Debug** > **Start sem depuração** para executar a solução. No prompt para recompilar o projeto, selecione **Sim** para recompilar o projeto antes da execução.

    A saída a seguir é um exemplo do dispositivo de bomba de calor simulado inicializando e conectando-se com sucesso à instância de serviço de provisionamento a ser atribuída ao hub IoT das bombas de calor Contoso pela política de alocação personalizada:

    ```cmd
    Provisioning API Version: 1.3.6

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-heatpumps-hub-1098.azure-devices.net, deviceId: mainbuilding167-contoso-hpsd-088

    Press enter key to exit:
    ```

## <a name="troubleshooting-custom-allocation-policies"></a>Solução de problemas em políticas de alocação personalizadas

A tabela a seguir mostra os cenários esperados e os códigos de erro de resultados que você pode receber. Use esta tabela para ajudar a solucionar problemas de falhas de política de alocação personalizada com o Azure Functions.

| Cenário | Resultado de registro do serviço de provisionamento | Resultados do SDK de provisionamento |
| -------- | --------------------------------------------- | ------------------------ |
| O webhook retorna 200 OK com 'iotHubHostName' definido como um nome de host do Hub IoT válido | Status do resultado: Atribuído  | O SDK retorna PROV_DEVICE_RESULT_OK juntamente com informações de hub |
| O webhook retorna 200 OK com 'iotHubHostName' presente na resposta, mas definido como uma cadeia de caracteres vazia ou nula | Status do resultado: Com falha<br><br> Código de erro: CustomAllocationIotHubNotSpecified (400208) | O SDK retorna PROV_DEVICE_RESULT_HUB_NOT_SPECIFIED |
| O webhook retorna 401 Não Autorizado | Status do resultado: Com falha<br><br>Código de erro: CustomAllocationUnauthorizedAccess (400209) | O SDK retorna PROV_DEVICE_RESULT_UNAUTHORIZED |
| Um registro individual foi criado para desabilitar o dispositivo | Status do resultado: Desabilitado | O SDK retorna PROV_DEVICE_RESULT_DISABLED |
| O webhook retorna o código de erro >= 429 | Orquestração do DPS tentará novamente várias vezes. A política de repetição atualmente é:<br><br>&nbsp;&nbsp;- Contagem de repetições: 10<br>&nbsp;&nbsp;- Intervalo inicial: 1 s<br>&nbsp;&nbsp;- Incremento: 9 s | O SDK ignorará o erro e enviará outra mensagem de obtenção de status no tempo especificado |
| O webhook retorna qualquer outro código de status | Status do resultado: Com falha<br><br>Código de erro: CustomAllocationFailed (400207) | O SDK retorna PROV_DEVICE_RESULT_DEV_AUTH_ERROR |

## <a name="clean-up-resources"></a>Limpar recursos

Se você planeja continuar trabalhando com os recursos criados neste artigo, você pode deixá-los. Se você não planeja continuar usando os recursos, use as seguintes etapas para excluir todos os recursos criados neste artigo para evitar cobranças desnecessárias.

As etapas aqui supõem que você criou todos os recursos neste artigo, conforme instruído no mesmo grupo de recursos chamado **contoso-us-resource-group**.

> [!IMPORTANT]
> A exclusão de um grupo de recursos é irreversível. O grupo de recursos e todos os recursos contidos nele são excluídos permanentemente. Não exclua acidentalmente grupo de recursos ou recursos incorretos. Caso tenha criado o Hub IoT dentro de um grupo de recursos existente que contém recursos que você deseja manter, exclua apenas o próprio recurso do Hub IoT em vez de excluir o grupo de recursos.
>

Para excluir o grupo de recursos por nome:

1. Faça login no [portal Azure](https://portal.azure.com) e selecione **grupos de recursos**.

2. Na caixa de texto **Filtrar por nome...**, digite o nome do grupo de recursos que contém seus recursos, **contoso-us-resource-group**. 

3. À direita do seu grupo de recursos, na lista de resultados, selecione **...** e **Excluir grupo de recursos**.

4. Você será solicitado a confirmar a exclusão do grupo de recursos. Digite o nome do grupo de recursos novamente para confirmar e selecione **Excluir**. Após alguns instantes, o grupo de recursos, e todos os recursos contidos nele, serão excluídos.

## <a name="next-steps"></a>Próximas etapas

* Para saber mais Reprovisionamento, consulte conceitos de [reprovisionamento do Dispositivo Hub IoT](concepts-device-reprovision.md) 
* Para saber mais Deprovisionamento, consulte [Como desprovisionar dispositivos que foram previamente provisionados](how-to-unprovision-devices.md) 
