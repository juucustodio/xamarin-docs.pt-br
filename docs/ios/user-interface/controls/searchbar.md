---
title: Pesquisar barras no Xamarin. iOS
description: Este documento descreve como usar barras de pesquisa no Xamarin. iOS. Ele aborda como criar barras de pesquisa programaticamente e em um Storyboard.
ms.prod: xamarin
ms.assetid: 22A8249A-19C6-4734-8331-E49FE3170771
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 07/11/2017
ms.openlocfilehash: 36e339139a0a7f853a770fdb188b5f03ee93f7ee
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70283355"
---
# <a name="search-bars-in-xamarinios"></a>Pesquisar barras no Xamarin. iOS

O UISearchBar é usado para pesquisar uma lista de valores.

Ele contém três componentes principais:

- Um campo usado para inserir texto. Os usuários podem utilizar isso para inserir o termo de pesquisa.
- Um botão limpar para remover qualquer texto do campo de pesquisa.
- Um botão de cancelamento para sair da função de pesquisa.

![Barra de pesquisa](searchbar-images/image1.png)

## <a name="implementing-the-search-bar"></a>Implementando a barra de pesquisa

Para implementar a barra de pesquisa, comece criando uma nova:

```csharp
searchBar = new UISearchBar();
```

E, em seguida, colocá-lo. O exemplo a seguir mostra como colocá-lo na barra de navegação ou no HeaderView de uma tabela:

```csharp
NavigationItem.TitleView = searchBar;

// or

TableView.TableHeaderView = searchBar;
```

Definindo propriedades na barra de pesquisa:

```csharp
 searchBar = new UISearchBar(){
                Placeholder = "Enter your search Item",
                Prompt = "Search Entered here",
                ShowsScopeBar = true,
                ScopeButtonTitles = new string[]{ "Boston", "London", "SF" },
            };
```

![Propriedades da barra de pesquisa](searchbar-images/image6.png)

Acionar `SearchButtonClicked` o evento quando o botão Pesquisar for pressionado. Isso chamará a lógica de pesquisa:

```csharp
searchBar.SearchButtonClicked += (sender, e) => {
                Search ();
            };
```

Para obter informações sobre como gerenciar a apresentação dos resultados da barra de pesquisa e da pesquisa, consulte a receita do [controlador de pesquisa](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/search-controller) .

## <a name="using-the-search-bar-in-the-designer"></a>Usando a barra de pesquisa no designer

O designer oferece duas opções para implementar uma barra de pesquisa no designer

- Barra de pesquisa
- Barra de pesquisa com o controlador de exibição de pesquisa (preterido)

![Controles de barra de pesquisa no designer](searchbar-images/image2.png)

Use o painel de propriedades para definir propriedades na barra de pesquisa

![Designer de propriedades da barra de pesquisa](searchbar-images/image3.png)

Essas propriedades são explicadas abaixo:

- **Texto, espaço reservado, prompt** – essas propriedades são usadas para sugerir e instruir como os usuários devem usar a barra de pesquisa. Por exemplo, se seu aplicativo exibir uma lista de lojas, você poderá usar a propriedade prompt para informar que os usuários podem "inserir uma cidade, nome da história ou CEP"
- **Estilo de pesquisa** – você pode definir a barra de pesquisa para ser **proeminente** ou **mínima**. Usar as proeminentes divisará tudo na tela, exceto para a barra de pesquisa, fazendo com que o foco seja desenhado na barra de pesquisa. A barra de pesquisa de estilo mínimo será combinada com seu ambiente.
- **Recursos** – habilitar essas propriedades exibe apenas o elemento de interface do usuário. A funcionalidade deve ser implementada para isso gerando o evento correto conforme detalhado nos [documentos da API da barra de pesquisa](xref:UIKit.UISearchBar)
  - Mostra os resultados da pesquisa/botão indicadores – mostra um ícone de resultados da pesquisa ou indicadores na barra de pesquisa
  - Mostra o botão Cancelar – permite que os usuários saiam da função de pesquisa. É recomendável que isso esteja selecionado.
  - Mostra a barra de escopo – isso permite que os usuários limitem o escopo de sua pesquisa. Por exemplo, ao pesquisar no aplicativo de música, o usuário pode selecionar se deseja pesquisar Apple Music ou sua biblioteca para uma música ou um artista específico. Para exibir várias opções, adicione uma matriz de títulos à propriedade **ScopeBarTitles** .
  ![Títulos de escopo da barra de pesquisa](searchbar-images/image4.png)

- **Comportamento do texto** – essas opções são usadas para abordar como a entrada do usuário é formatada quando estão digitando. A capitalização definirá o início de cada palavra ou frase, ou todos os caracteres como letras maiúsculas. Correção e verificação ortográfica com prompt para o usuário com a grafia sugerida de palavras conforme elas digitam.
- **Teclado** – controla o estilo de teclado exibido para a entrada e, portanto, quais chaves estão disponíveis no teclado. Isso inclui o teclado numérico, o Phone pad, o email e a URL junto com outras opções.
- **Aparência** – controla o estilo de aparência do teclado e será escuro ou leve.
- **Chave de retorno** – altere o rótulo na chave de retorno para refletir melhor qual ação será tomada. Os valores com suporte incluem Go, junção, avançar, rota, concluído e pesquisa.
- **Seguro** – identifica se a entrada é mascarada (por exemplo, para uma entrada de senha).

## <a name="related-links"></a>Links relacionados

- [Controlador de pesquisa](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/search-controller)
