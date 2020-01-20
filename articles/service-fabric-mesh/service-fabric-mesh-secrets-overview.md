---
title: Armazenando e usando os segredos do aplicativo de malha de Service Fabric do Azure
description: A Malha do Service Fabric dá suporte aos Segredos como recursos do Azure. Veja como armazenar e gerenciar segredos com seus aplicativos de Service Fabric malha.
author: erikadoyle
ms.author: edoyle
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: da4eaa34840f14714616b85e24fd62cf65602b84
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2020
ms.locfileid: "76277629"
---
# <a name="service-fabric-mesh-application-secrets"></a>Segredos do aplicativo de Malha do Fabric Mesh
A Malha do Service Fabric dá suporte aos Segredos como recursos do Azure. Um segredo da Malha do Service Fabric pode ser qualquer informação de texto confidencial como cadeias de conexão de armazenamento, senhas ou outros valores que devem ser armazenados e transmitidos de forma segura.

![Visão geral dos Segredos de Malha][sf-mesh-secrets-overview]

## <a name="mesh-secrets-resources"></a>Recursos dos segredos de Malha
Um Segredo de aplicativo de Malha consiste em:
* Um recurso **Segredos**, que é um contêiner que armazena os segredos de texto. Os segredos contidos no recurso **Segredos** são armazenados e transmitidos de forma segura.
* Um ou mais recursos **Segredos/Valores** que são armazenados no contêiner do recurso **Segredos**. Cada recurso **Segredos/Valores** é diferenciado por um número de versão.

## <a name="next-steps"></a>Próximos passos 
Para saber mais sobre os Segredos de Malha do Service Fabric, confira:
- [Gerenciar Segredos do Aplicativo do Service Fabric Mesh](service-fabric-mesh-howto-manage-secrets.md)
- [Introdução ao Modelo de Recurso do Service Fabric](service-fabric-mesh-service-fabric-resources.md)

<!-- pics -->
[sf-mesh-secrets-overview]: ./media/service-fabric-mesh-secrets-overview/MeshAppSecretsOverview.png
