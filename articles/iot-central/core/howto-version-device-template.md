---
title: Noções básicas sobre o controle de versão de modelo de dispositivo para aplicativos Azure IoT Central | Microsoft Docs
description: Iterar modelos de dispositivo criando novas versões e sem afetar os dispositivos conectados em tempo real
author: sarahhubbard
ms.author: sahubbar
ms.date: 12/09/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 37c7bc99881c8d1106c8464cfe18c9e63b8a1b02
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81756728"
---
# <a name="create-a-new-device-template-version"></a>Criar uma nova versão do modelo de dispositivo

*Este artigo se aplica a construtores de soluções e desenvolvedores de dispositivos.*

A Microsoft IoT Central permite o rápido desenvolvimento de Aplicativos IoT. Você pode iterar rapidamente os projetos do modelo do dispositivo adicionando, editando ou excluindo recursos, visualizações e personalizações do dispositivo. Depois de publicar o modelo do dispositivo, o modelo de capacidade do dispositivo será **publicado** com ícones de bloqueio ao lado do modelo. Para fazer alterações no modelo de capacidade do dispositivo, você precisará criar uma nova versão do modelo do dispositivo. Enquanto isso, as propriedades na nuvem, as personalizações e as visualizações podem ser editadas a qualquer momento sem a necessidade de versácia o modelo do dispositivo. Depois de salvar qualquer uma dessas alterações, você pode publicar o modelo do dispositivo para tornar as últimas alterações disponíveis para o operador visualizar no Device Explorer.

> [!NOTE]
> Para saber mais sobre como criar um modelo de dispositivo, [consulte Configurar e gerenciar um modelo de dispositivo](howto-set-up-template.md)

## <a name="add-customizations-to-the-device-template-without-versioning"></a>Adicionar personalizações ao modelo do dispositivo sem versões

Certos elementos dos recursos do dispositivo podem ser editados sem precisar versá-lo no modelo e interfaces do dispositivo. Por exemplo, alguns desses campos incluem nome de exibição, tipo semântico, valor mínimo, valor máximo, casas decimais, cor, unidade, unidade de exibição, comentário e descrição. Para adicionar uma dessas personalizações:

1. Vá para a página **Modelos de dispositivo.**
1. Selecione o modelo de dispositivo que deseja personalizar.
1. Escolha a guia **Personalizar.**
1. Todos os recursos definidos no modelo de capacidade do dispositivo serão listados aqui. Todos os campos que você pode editar aqui podem ser salvos e usados em todo o seu aplicativo, sem precisar versificar o modelo do seu dispositivo. Se houver campos que você deseja editar que são somente leitura, você precisará versácia seu modelo de dispositivo para alterá-los. Selecione um campo que deseja editar e insira em quaisquer novos valores.
1. Clique em **Save** (Salvar). Agora, esses valores substituirão qualquer coisa que foi inicialmente salva no modelo do seu dispositivo e será usada em todo o aplicativo.

## <a name="versioning-a-device-template"></a>Versão de um modelo de dispositivo

A criação de uma nova versão do modelo do dispositivo criará uma versão de rascunho do modelo onde o modelo de capacidade do dispositivo pode ser editado. Quaisquer interfaces publicadas permanecerão publicadas até que sejam versão individual. Para modificar uma interface publicada, você deve primeiro criar uma nova versão do modelo do dispositivo.

O modelo do dispositivo só deve ser versão quando você estiver tentando editar uma parte do modelo de capacidade do dispositivo que você não pode editar na seção de personalizações do modelo do dispositivo. 

Para a versão de um modelo de dispositivo:

1. Vá para a página **Modelos de dispositivo.**
1. Selecione o modelo de dispositivo que você está tentando versificar.
1. Clique no botão **Versão** no topo da página e dê um novo nome ao modelo. Sugerimos um novo nome para você que pode ser editado.
1. Clique em **Criar**.
1. Agora o modelo do dispositivo está no modo rascunho. Você verá que suas interfaces ainda estão bloqueadas e devem ser versão individualmente para serem editadas. 

### <a name="versioning-an-interface"></a>Versão de uma interface

A versão de uma interface permite adicionar, atualizar e remover os recursos dentro da interface que você já havia criado. 

Para a versão de uma interface:

1. Vá para a página **Modelos de dispositivo.**
1. Selecione o modelo de dispositivo que você tem em um modo de rascunho.
1. Selecione a interface que está no modo publicado que deseja fazer a versão e a edição.
1. Clique no botão **Versão** na parte superior da página da interface. 
1. Clique em **Criar**.
1. Agora sua interface está no modo rascunho. Você poderá adicionar ou editar recursos à sua interface sem quebrar as personalizações e visualizações existentes. 

> [!NOTE]
> As interfaces padrão publicadas pelo Azure IoT não podem ser versadas ou editadas. Essas interfaces padrão são usadas para a certificação de dispositivos.

> [!NOTE]
> Uma vez que a interface tenha sido publicada, você não pode excluir nenhuma de suas capacidades, mesmo em um modo de rascunho. Os recursos só podem ser editados ou adicionados à interface no modo rascunho.


## <a name="migrate-a-device-across-device-template-versions"></a>Migrar um dispositivo nas versões de modelos de dispositivos

É possível criar várias versões do modelo de dispositivo. Com o tempo, você terá vários dispositivos conectados usando esses modelos de dispositivo. Você pode migrar dispositivos de uma versão do modelo de dispositivo para outra. As etapas a seguir descrevem como migrar um dispositivo:

1. Vá para a página **Explorador de dispositivos.**
1. Selecione o dispositivo que você precisa migrar para outra versão.
1. Escolha **Migrar**.
1. Selecione o modelo do dispositivo com o número da versão para a qual deseja migrar o dispositivo e escolha **Migrar**.

![Como migrar um dispositivo](media/howto-version-device-template/pick-version.png)

## <a name="next-steps"></a>Próximas etapas

Se você é um desenvolvedor de dispositivos, um próximo passo sugerido é ler sobre [dispositivos Azure IoT Edge e Azure IoT Central](./concepts-iot-edge.md).
