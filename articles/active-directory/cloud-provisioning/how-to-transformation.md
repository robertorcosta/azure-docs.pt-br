---
title: Azure AD Connect transformações de provisionamento em nuvem
description: Este artigo descreve como usar transformações para alterar os mapeamentos de atributos padrão.
author: billmath
ms.author: billmath
manager: davba
ms.date: 12/02/2019
ms.topic: article
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: ec12927b40096b7ff04fae6b7cbc69a7bc11e8f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75549288"
---
# <a name="transformations"></a>Transformações

Com uma transformação, você pode alterar o comportamento padrão de como um atributo é sincronizado com o Azure Active Directory (Azure AD) usando provisionamento em nuvem.

Para fazer essa tarefa, você precisa editar o esquema e reenviá-lo através de uma solicitação da Web.

Para obter mais informações sobre atributos de provisionamento em nuvem, consulte [Entendendo o esquema AD do Azure](concept-attributes.md).


## <a name="retrieve-the-schema"></a>Recupere o esquema
Para recuperar o esquema, siga os passos em [Exibir o esquema](concept-attributes.md#view-the-schema). 

## <a name="custom-attribute-mapping"></a>Mapeamento de atributos personalizados
Para adicionar um mapeamento de atributos personalizados, siga essas etapas.

1. Copie o esquema em um texto ou editor de código, como [Visual Studio Code](https://code.visualstudio.com/).
1. Localize o objeto que deseja atualizar no esquema.

   ![Objeto no esquema](media/how-to-transformation/transform1.png)</br>
1. Localize o `ExtensionAttribute3` código sob o objeto do usuário.

    ```
                            {
                                "defaultValue": null,
                                "exportMissingReferences": false,
                                "flowBehavior": "FlowWhenChanged",
                                "flowType": "Always",
                                "matchingPriority": 0,
                                "targetAttributeName": "ExtensionAttribute3",
                                "source": {
                                    "expression": "Trim([extensionAttribute3])",
                                    "name": "Trim",
                                    "type": "Function",
                                    "parameters": [
                                        {
                                            "key": "source",
                                            "value": {
                                                "expression": "[extensionAttribute3]",
                                                "name": "extensionAttribute3",
                                                "type": "Attribute",
                                                "parameters": []
                                            }
                                        }
                                    ]
                                }
                            },
    ```
1. Editar o código para que o atributo da empresa seja mapeado para `ExtensionAttribute3`.

   ```
                                    {
                                        "defaultValue": null,
                                        "exportMissingReferences": false,
                                        "flowBehavior": "FlowWhenChanged",
                                        "flowType": "Always",
                                        "matchingPriority": 0,
                                        "targetAttributeName": "ExtensionAttribute3",
                                        "source": {
                                            "expression": "Trim([company])",
                                            "name": "Trim",
                                            "type": "Function",
                                            "parameters": [
                                                {
                                                    "key": "source",
                                                    "value": {
                                                        "expression": "[company]",
                                                        "name": "company",
                                                        "type": "Attribute",
                                                        "parameters": []
                                                    }
                                                }
                                            ]
                                        }
                                    },
   ```
 1. Copie o esquema de volta ao Graph Explorer, altere o **tipo de solicitação** para **PUT**e selecione **'Executar consulta '**.

    ![Executar Consulta](media/how-to-transformation/transform2.png)

 1. Agora, no portal Azure, vá para a configuração de provisionamento na nuvem e selecione **Reinicialização .**

    ![Reinicie o provisionamento](media/how-to-transformation/transform3.png)

 1. Depois de um tempo, verifique se os atributos estão sendo `https://graph.microsoft.com/beta/users/{Azure AD user UPN}`preenchidos executando a seguinte consulta no Graph Explorer: .
 1. Agora você deve ver o valor.

    ![O valor aparece](media/how-to-transformation/transform4.png)

## <a name="custom-attribute-mapping-with-function"></a>Mapeamento de atributos personalizados com função
Para um mapeamento mais avançado, você pode usar funções que permitem manipular os dados e criar valores para atributos que atendem às necessidades da sua organização.

Para fazer essa tarefa, siga as etapas anteriores e edite a função usada para construir o valor final.

Para obter informações sobre a sintaxe e exemplos de expressões, consulte [Escrever expressões para mapeamentos de atributos no Azure Active Directory](reference-expressions.md).


## <a name="next-steps"></a>Próximas etapas 

- [O que é provisionamento?](what-is-provisioning.md)
- [O que é o provisionamento em nuvem do Azure AD Connect?](what-is-cloud-provisioning.md)
