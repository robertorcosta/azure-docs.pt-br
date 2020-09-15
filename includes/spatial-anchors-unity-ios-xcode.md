---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 08/14/2020
ms.author: rgarcia
ms.openlocfilehash: b93243a537fafce6d865ec207b12dc2654cafd20
ms.sourcegitcommit: c52e50ea04dfb8d4da0e18735477b80cafccc2cf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/08/2020
ms.locfileid: "89536230"
---
Selecione **Compilar**. Na caixa de diálogo que é aberta, selecione uma pasta para exportar o projeto Xcode.

Quando a exportação estiver concluída, uma pasta contendo o projeto Xcode aparecerá.

> [!NOTE]
> Se for exibida uma janela perguntando se você deseja substituir ou anexar, é recomendável que você selecione **Acrescentar** porque ele é mais rápido. Você só precisa selecionar **Substituir** se você estiver alterando ativos na sua cena. (por exemplo, se você estiver adicionando, removendo, ou alterando relações pai/filho ou se você estiver adicionando, removendo ou alterando as propriedades.) Se você estiver fazendo alterações no código fonte, apenas **Acrescentar** deve ser suficiente.

## <a name="open-the-xcode-project"></a>Abrir o projeto do Xcode

Agora, você pode abrir `Unity-iPhone.xcodeproj` no Xcode. Você pode iniciar o Xcode e abrir o projeto `Unity-iPhone.xcodeproj` exportado ou iniciar o projeto no Xcode executando o seguinte comando no local para o qual exportou o projeto:

```bash
open ./Unity-iPhone.xcodeproj
```

Selecione o nó raiz **Unity-iPhone** para exibir as configurações do projeto e, em seguida, selecione a guia **Geral**.

Em **Assinando**, verifique se **Gerenciar assinatura automaticamente** está habilitado. Se não estiver, habilitá-o e, em seguida, selecione **Habilitar Automático** na caixa de diálogo que aparece para redefinir as configurações de compilação.

Em **Informações de Implantação**, verifique se o **Destino de Implantação** está definido como `11.0`.

## <a name="deploy-the-app-to-your-ios-device"></a>Implantar o aplicativo em seu dispositivo iOS

Conecte o dispositivo iOS ao Mac e defina o **esquema ativo** como o dispositivo iOS.

![Selecione o dispositivo](./media/spatial-anchors-unity/select-device.png)

Selecione **Compilar e, em seguida, executar o esquema atual**.

![Implantar e executar](./media/spatial-anchors-unity/deploy-run.png)
