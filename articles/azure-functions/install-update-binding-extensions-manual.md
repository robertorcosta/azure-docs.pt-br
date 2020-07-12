---
title: Instalar ou atualizar manualmente as extensões de associação do Azure Functions
description: Saiba como instalar ou atualizar as extensões de ligação do Azure Functions para aplicativos de função implantados.
ms.topic: conceptual
ms.date: 09/26/2018
ms.openlocfilehash: f0705e62adc4acb26797b937a6dd8c684a598ebc
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2020
ms.locfileid: "86252616"
---
# <a name="manually-install-or-update-azure-functions-binding-extensions-from-the-portal"></a>Instalar ou atualizar manualmente as extensões de associação do Azure Functions no portal

A partir da versão 2. x, o tempo de execução do Azure Functions usa extensões de associação para implementar o código para gatilhos e associações. As extensões de associação são fornecidas nos pacotes NuGet. Registrar uma extensão é basicamente instalar um pacote. Ao desenvolver funções, a maneira de instalar as extensões de associação depende do ambiente de desenvolvimento. Para obter mais informações, consulte [Registrar extensões de associação](./functions-bindings-register.md) no artigo de gatilhos e associações.

Às vezes, é necessário instalar ou atualizar manualmente as extensões de associação no portal do Azure. Por exemplo, talvez seja necessário atualizar uma associação registrada para uma versão mais recente. Também pode ser necessário registrar uma associação com suporte que não pode ser instalada na guia **Integrar** no portal.

## <a name="install-a-binding-extension"></a>Instalar uma extensão de associação

Siga as etapas abaixo para instalar ou atualizar manualmente as extensões do portal.

1. No [portal do Azure](https://portal.azure.com), localize o aplicativo de funções e selecione-o. Escolha a guia **Visão geral** e selecione **Parar**.  Parar o aplicativo de funções desbloqueia arquivos para que as alterações possam ser efetivadas.

1. Escolha a guia **Recursos da plataforma** e em **Ferramentas de Desenvolvimento** selecione **Ferramentas Avançadas (Kudu)**. O ponto de extremidade kudu ( `https://<APP_NAME>.scm.azurewebsites.net/` ) é aberto em uma nova janela.

1. Na janela kudu, selecione **console de depuração**  >  **cmd**.  

1. Na janela de comando, navegue até `D:\home\site\wwwroot` e escolha o ícone de exclusão próximo a `bin` para excluir a pasta. Selecione **OK** para confirmar a exclusão.

1. Escolha o ícone de edição próximo ao arquivo `extensions.csproj`, que define as extensões de associação para o aplicativo de funções. O arquivo de projeto é aberto no editor online.

1. Faça as adições e atualizações necessárias dos itens **PackageReference** em **ItemGroup**, e, em seguida, selecione **Salvar**. A lista atua de versões de pacotes com suporte pode ser encontrada no artigo do wiki [Quais pacotes eu preciso?](https://github.com/Azure/azure-functions-host/wiki/Updating-your-function-app-extensions#what-nuget-packages-do-i-need). Todas as três associações de Armazenamento do Azure exigem o pacote Microsoft.Azure.WebJobs.Extensions.Storage.

1. Na pasta `wwwroot` execute o seguinte comando para reconstruir os assemblies referenciados na pasta `bin`.

    ```cmd
    dotnet build extensions.csproj -o bin --no-incremental --packages D:\home\.nuget
    ```

1. Retorne à guia **Visão geral** no portal, escolha **Iniciar** para iniciar o aplicativo de funções.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Aprenda mais sobre gatilhos e de associações do Azure Functions](functions-triggers-bindings.md)
