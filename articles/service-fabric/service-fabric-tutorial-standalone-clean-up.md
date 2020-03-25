---
title: Limpar um cluster autônomo
description: Neste tutorial, você aprenderá a limpar os recursos do AWS ou do Azure no cluster autônomo do Service Fabric.
author: dkkapur
ms.topic: tutorial
ms.date: 07/22/2019
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: bfb23ca5f5eb9540491fbd05efdfd6997db15e6b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "75639013"
---
# <a name="tutorial-clean-up-your-standalone-cluster"></a>Tutorial: Limpar seu cluster autônomo

Os clusters autônomos do Service Fabric oferecem a opção de escolher seu próprio ambiente e criar um cluster como parte da abordagem “qualquer sistema operacional, qualquer nuvem” que está sendo adotada pelo Service Fabric. Nesta série de tutoriais, você cria um cluster autônomo hospedado no AWS ou no Azure e instala um aplicativo nele.

Este tutorial é a parte quatro de uma série. Esta parte do tutorial mostra como limpar os recursos do AWS ou do Azure que você criou para hospedar o cluster do Service Fabric.

Na primeira parte da série, você aprende a:

> [!div class="checklist"]
> * Limpar um cluster do Service Fabric
> * Limpar o AWS ou os recursos do Azure

## <a name="clean-up-service-fabric-cluster"></a>Limpar um cluster do Service Fabric

1. RDP para a instância de VM que você usou para instalar o Service Fabric.
2. Abra o PowerShell.
3. Altere o diretório para a pasta extraída do segundo tutorial.
4. Execute o seguinte comando para remover o cluster do Service Fabric:

  ```powershell
  .\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json
  ```

5. Insira `Y` quando solicitado; se tiver sido bem-sucedido, a saída será semelhante à mostrada a seguir, com seus próprios endereços IP substituídos:

  ```powershell
  Best Practices Analyzer completed successfully.
  Removing configuration from machine 172.31.21.141
  Removing configuration from machine 172.31.27.1
  Removing configuration from machine 172.31.20.163
  Configuration removed from machine 172.31.21.141
  Configuration removed from machine 172.31.27.1
  Configuration removed from machine 172.31.20.163
  The cluster is successfully removed.
  ```

## <a name="clean-up-aws-resources"></a>Limpe os recursos do AWS

1. Entre na sua conta do AWS.
2. Vá para o Console EC2.
3. Selecione os três nós que você criou na primeira parte do tutorial.
4. Clique em **Ações** > **Estado da instância** > **Terminar**.

## <a name="clean-up-azure-resources"></a>Limpar recursos do Azure

1. Entre no portal do Azure.
2. Vá para a seção **Máquinas Virtuais**.
3. Marque as caixas de seleção dos três nós que você criou na primeira parte do tutorial.
4. Clique em **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Na parte quatro da série, você aprendeu a limpar os recursos que foram criados nas etapas anteriores.

> [!div class="checklist"]
> * Limpar seus recursos

> [!div class="nextstepaction"]
> [Voltando ao início](service-fabric-tutorial-standalone-create-infrastructure.md)
