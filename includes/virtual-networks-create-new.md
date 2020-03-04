---
title: incluir arquivo
description: incluir arquivo
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.topic: include
ms.date: 03/01/2020
ms.author: allensu
ms.custom: include file
ms.openlocfilehash: 81db46b52c9b4fe800f2fbfeadad966995d66e12
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2020
ms.locfileid: "78244962"
---
## <a name="create-the-virtual-network"></a>Criar a rede virtual

Nesta seção, você criará uma rede virtual e uma sub-rede.

1. No canto superior esquerdo da tela, selecione **Criar um recurso > Rede > Rede virtual** ou pesquise por **Rede virtual** na caixa de pesquisa.

2. Em **Criar rede virtual**, insira ou selecione estas informações na guia **Básico**:

    | **Configuração**          | **Valor**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Detalhes do projeto**  |                                                                 |
    | Subscription     | Selecionar sua assinatura do Azure                                  |
    | Grupo de recursos   | Selecione **Criar**, insira o **\<nome_do_grupo_de_recursos>** , em seguida, selecione OK ou selecione um **\<nome_do_grupo_de_recursos>** existente com base nos parâmetros. |
    | **Detalhes da instância** |                                                                 |
    | Nome             | Insira o **\<nome_da_rede_virtual>**                                    |
    | Região           | Selecione o **\<nome_da_região>** |

3. Selecione a guia **Endereços IP** ou selecione o botão **Avançar: Endereços IP** na parte inferior da página.

4. Na guia **Endereços IP**, insira estas informações:

    | Configuração            | Valor                      |
    |--------------------|----------------------------|
    | Espaço de endereço IPv4 | Insira o **\<espaço_de_endereço_IPv4>** |

5. Em **Nome da sub-rede**, selecione a palavra **padrão**.

6. Em **Editar sub-rede**, insira estas informações:

    | Configuração            | Valor                      |
    |--------------------|----------------------------|
    | Nome da sub-rede | Insira o **\<nome_da_sub_rede>** |
    | Intervalo de endereços da sub-rede | Insira o **\<intervalo_de_endereços_da_sub_rede>**

7. Clique em **Salvar**.

8. Selecione a guia **Revisar + criar** ou o botão **Revisar + criar**.

9. Selecione **Criar**.