---
title: Opções do vinculador do Xamarin.Mac
description: Este documento descreve a vinculação no Xamarin.Mac. A vinculação é uma poderosa ferramenta de otimização que reduz o tamanho do seu aplicativo, removendo o código não utilizado.
ms.prod: xamarin
ms.assetid: F03176C3-F8D4-4DE8-870C-7F27D8CE525A
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 11/10/2017
ms.openlocfilehash: f4ab94c4eede4a122ac834e075270a375bca0807
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030003"
---
# <a name="xamarinmac-linker-options"></a>Opções do vinculador do Xamarin.Mac

_A vinculação é uma poderosa ferramenta de otimização que reduz o tamanho do seu aplicativo, removendo o código não utilizado._

## <a name="overview"></a>Visão Geral

Com base na [Estrutura de Destino](~/mac/platform/target-framework.md) utilizada pelo seu projeto, as opções de vinculador disponíveis podem ser limitadas. Isso ocorre devido ao fato de que a vinculação requer a criação de um gráfico de objeto de todo tipo usado pelo seu aplicativo e isso não é possível no Completo (ou Sem Suporte) devido a System.Configuration.

Há quatro opções disponíveis:

- **Nenhum** – Desabilitar todas as vinculações. O padrão na configuração de Depuração no Moderno e todas as configurações no Completo.
- **SDK** – Vincula todos os assemblies do SDK, exceto os assemblies de usuário. Padrão na configuração de Versão no Moderno. Indisponível no Completo.
- **Completo** – Vincula todos os assemblies. Isso requer que o código do usuário seja seguro para o vinculador, confira as [notas](~/ios/deploy-test/linker.md) para obter mais informações. Indisponível no Completo.
- **Plataforma** – Vincula somente o Xamarin.Mac.dll. Veja mais detalhes a seguir.

## <a name="platform-linking"></a>Vinculação de plataforma

Os aplicativos de vinculação que usam a [Estrutura de Destino](~/mac/platform/target-framework.md) Completa são geralmente inseguros, mas há uma série de cenários em que uma forma bastante limitada de vinculação é necessária.

Por exemplo, os Aplicativos enviados para a App Store do macOS não devem fazer referência a uma série de APIs obsoletas (como QTKit), algumas das quais contêm associações do Xamarin.Mac. Mesmo se um aplicativo não chamar essas associações, a invocação existirá no SDK e será rejeitada.

A vinculação da plataforma pressupõe que o aplicativo e a BCL são inseguras para o vinculador e apenas removem o código não utilizado do Xamarin.Mac.dll. 

Os aplicativos que não refletirem nos tipos de Xamarin.Mac.dll observarão uma pequena melhoria de inicialização com a remoção dos tipos desnecessários.

A vinculação de plataforma geralmente só é útil para aplicativos que usam a estrutura de destino Completa, uma vez que um aplicativo Moderno pode usar a opção de SDK mais eficiente.

## <a name="setting-the-linker-configuration"></a>Definir a configuração do vinculador

Para alterar a configuração do vinculador para um projeto Xamarin.Mac, faça o seguinte:

1. Abra o projeto Xamarin.Mac no Visual Studio para Mac.
2. No **Gerenciador de Soluções**, clique duas vezes no arquivo do projeto para abrir a caixa de diálogo **Opções de Projeto**.
3. Na guia **Build do Mac**, selecione o tipo de **Comportamento do Vinculador** que atende às necessidades do aplicativo:

    ![Escolher o comportamento do vinculador a ser usado](linker-images/link-behavior.png "Escolher o comportamento do vinculador a ser usado")

4. A vinculação da plataforma para Estruturas de Destino Completas não aparecerá no IDE até uma atualização futura. Até lá, adicione `--linkplatform` aos **Argumentos mmp adicionais** em vez disso.
5. Clique no botão **OK** para salvar suas alterações.

## <a name="related-links"></a>Links relacionados

- [Vinculação no iOS](~/ios/deploy-test/linker.md)
