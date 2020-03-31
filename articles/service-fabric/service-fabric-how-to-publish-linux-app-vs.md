---
title: Criar e publicar a.Net aplicativo Core para um cluster Linux remoto
description: Crie e publique aplicativos .Net Core visando um cluster Linux remoto do Visual Studio
author: peterpogorski
ms.topic: troubleshooting
ms.date: 5/20/2019
ms.author: pepogors
ms.openlocfilehash: c30eedb6782e4172d677f16e27441f28c78cdd89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614342"
---
# <a name="use-visual-studio-to-create-and-publish-net-core-applications-targeting-a-remote-linux-service-fabric-cluster"></a>Use o Visual Studio para criar e publicar aplicativos .Net Core direcionados a um cluster remoto de malha de serviço linux
Com as ferramentas do Visual Studio, você pode desenvolver e publicar aplicativos Service Fabric .Net Core direcionados a um cluster Linux Service Fabric. A versão do SDK deve ser 3.4 ou superior para implantar um aplicativo .Net Core direcionado aos clusters Linux Service Fabric do Visual Studio.

> [!Note]
> O Visual Studio não suporta aplicativos de depuração service fabric que visam o Linux.
>

## <a name="create-a-service-fabric-application-targeting-net-core"></a>Crie um aplicativo de malha de serviço direcionado ao .Net Core
1. Inicie o Visual Studio como um **administrador**.
2. Crie um projeto com **o Projeto >nova->>de arquivos**.
3. Na caixa de diálogo **Novo projeto,** escolha **Aplicativo de malha de serviço > em nuvem**.
![create-application]
4. Nomeie o aplicativo e clique **em Ok**.
5. Na página **Serviço de malha de novo serviço,** selecione o tipo de serviço que você gostaria de criar na **Seção .Net Core**.
![criar-serviço]

## <a name="deploy-to-a-remote-linux-cluster"></a>Implantar em um cluster Linux remoto
1. No explorador de soluções, clique com o botão direito do mouse no aplicativo e selecione **Build**.
![aplicativo de construção]
2. Uma vez que o processo de compilação do aplicativo tenha sido concluído, clique com o botão direito do mouse no serviço e selecione editar o **arquivo csproj**.
![edit-csproj]
3. Editar a propriedade UpdateServiceFabricManifestEnabled de True to **False** se o serviço for um **tipo de projeto de ator**. Se a sua solicitação não tiver um serviço de ator, pule para o passo 4.
```xml
    <UpdateServiceFabricManifestEnabled>False</UpdateServiceFabricManifestEnabled>
```
> [!Note]
> Configuração updateServiceFabricManifestHabilitado a falso, desativará as atualizações para o ServiceManifest.xml durante uma compilação. Qualquer alteração como adicionar, remover ou renomear para o serviço não será refletida no ServiceManifest.xml. Se alguma alteração for feita, você deve atualizar o ServiceManifest manualmente ou definir temporariamente UpdateServiceBuildManifestoHabilitado para true e criar o serviço que atualizará o ServiceManifest.xml e, em seguida, reverta-o para falso.
>

4. Atualize o RuntimeIndetifier do win7-x64 para a plataforma de destino no projeto de serviço.
```xml
    <RuntimeIdentifier>ubuntu.16.04-x64</RuntimeIdentifier>
```
5. No ServiceManifest, atualize o programa entrypoint para remover .exe. 
```xml
    <EntryPoint> 
    <ExeHost> 
        <Program>Actor1</Program> 
    </ExeHost> 
    </EntryPoint>
```
6. No Solution Explorer, clique com o botão direito do mouse no aplicativo e selecione **Publicar**. A caixa de diálogo **Publicar** é exibida.
7. No **Connection Endpoint**, selecione o ponto final para o cluster Linux de malha de serviço remoto que você gostaria de segmentar.
![aplicativo de publicação]

<!--Image references-->
[create-application]:./media/service-fabric-how-to-vs-remote-linux-cluster/create-application-remote-linux.png
[criar-serviço]:./media/service-fabric-how-to-vs-remote-linux-cluster/create-service-remote-linux.png
[aplicativo de construção]:./media/service-fabric-how-to-vs-remote-linux-cluster/build-application-remote-linux.png
[edit-csproj]:./media/service-fabric-how-to-vs-remote-linux-cluster/edit-csproj-remote-linux.png
[aplicativo de publicação]:./media/service-fabric-how-to-vs-remote-linux-cluster/publish-remote-linux.png

## <a name="next-steps"></a>Próximas etapas
* Saiba como [começar com o Service Fabric com .Net Core](https://azure.microsoft.com/resources/samples/service-fabric-dotnet-core-getting-started/)
