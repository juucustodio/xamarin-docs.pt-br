---
title: Entrada de texto no xamarin. IOS
description: Este documento descreve a entrada de texto em um aplicativo xamarin. IOS. Ele discute usando UITextField e UITextVIew por meio de programação e o Designer do iOS.
ms.prod: xamarin
ms.assetid: 03A7F1DC-017D-4501-91FD-82C78272CDB1
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: b309cbdf37acaa71740a4d5d03e4824efd40f359
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61200773"
---
# <a name="text-input-in-xamarinios"></a>Entrada de texto no xamarin. IOS

Aceitar entrada de texto do usuário é realizada com o `UITextField` para entradas de linha única e UITextView para texto editável de várias linhas. Você pode arrastar qualquer um desses controles para uma tela e clique duas vezes para definir o texto inicial.

As capturas de tela abaixo mostram os ícones para esses controles, localizados no painel da caixa de ferramentas no Visual Studio para Mac:

 [![](text-input-images/image11a.png "UITextField")](text-input-images/image11a.png#lightbox)

 [![](text-input-images/image13a.png "UITextView")](text-input-images/image13a.png#lightbox)

Depois que você tenha nomeado na tomada e salvar o arquivo de Storyboard, Visual Studio para Mac atualizará os `.designer.cs` classe parcial e você pode adicionar C# código que faz referência ao controle ao seu arquivo de classe. Cada controle tem seu próprio propriedades exclusivas e eventos que podem ser acessados em seu C# código.

 <a name="UITextField" />


## <a name="uitextfield"></a>UITextField

O `UITextField` controle geralmente é usado para aceitar uma única linha de entrada de texto como um nome de usuário ou senha. Algumas das opções disponíveis para personalizar o controle são mostradas aqui:

 [![](text-input-images/image15a.png "Propriedades de UITextField")](text-input-images/image15a.png#lightbox)

Esses controles são explicados abaixo:

-  **Espaço reservado** – isso é opcional. Se definido, ele será exibido quando o campo de texto está vazio, geralmente para explicar ao usuário qual entrada é esperada.
-  **Botão Limpar** – Isso controla quando o botão Limpar padrão (o círculo cinza com (X)) é exibido no campo de texto, como uma maneira para o usuário limpar o texto rapidamente. Ele pode ser permanentemente ocultas, visíveis permanentemente ou mostrado, dependendo se o campo está sendo editado.
-  **Tamanho da fonte mínimo** e **ajustar para caber** – permite que o tamanho da fonte a ser ajustado automaticamente para caber mais texto e evitar truncamento, mas limitado ou nenhum menor do que o tamanho especificado.
-  **Capitalização** – se deseja aproveitar automaticamente palavras, frases ou toda a entrada.
-  **Correção** – indica se a verificação ortográfica e sugestões são habilitadas.
-  **Teclado** – o estilo de teclado de controles exibidos para a entrada e, portanto, quais teclas estão disponíveis no teclado. Isso inclui o teclado de número, teclado do telefone, Email, URL junto com outras opções.
-  **Aparência** – controla o estilo de aparência do teclado e serão ambos escuro ou tema de luz.
-  **Retornar a chave** – alterar o rótulo em que a tecla Return para refletir melhor a ação que será executada. Valores com suporte incluem Go, junção, Avançar, rota, feito e pesquisa.
-  **Proteger** – identifica se a entrada é mascarada (por exemplo, para uma entrada de senha).


Se for chamado de um UITextField `textfield1` foi adicionado a uma tela com o designer, você pode definir ou alterar suas propriedades no C# da seguinte maneira:

```csharp
textfield1.Placeholder = "type email here...";
textfield1.KeyboardType = UIKeyboardType.EmailAddress;
textfield1.ReturnKeyType = UIReturnKeyType.Send;
textfield1.MinimumFontSize = 17f;
textfield1.AdjustsFontSizeToFitWidth = true;
```

Xamarin. IOS fornece enumerações onde for apropriado para torná-lo mais fácil selecionar as configurações que você deseja, tal como o `UIKeyboardType` e `UIReturnKeyType` no trecho de código acima.

### <a name="display-text-programmatically"></a>Exibir o texto de forma programática

Se você não deseja projetar sua tela com o designer ou se você quiser adicionar dinamicamente algum texto em tempo de execução, você pode criar e exibir um UITextField com programação no `ViewDidLoad` método de um controlador de exibição como esta:

```csharp
var frame = new CGRect(10, 10, 300, 40);
textfield1 = new UITextField(frame);
View.Add(textfield1);
```

 <a name="UITextView" />


## <a name="uitextview"></a>UITextView

O `UITextView` controle pode ser usado para exibir o texto de somente leitura ou para aceitar a entrada de texto de várias linhas. Ele tem muitas das mesmas opções que o `UITextField` (como o uso de correção, etc.).

 [![](text-input-images/image16a.png "Propriedades de UITextView")](text-input-images/image16a.png#lightbox)

Propriedades específicas incluem:

-  **Comportamento** – indica se o texto é editável ou somente leitura.
-  **Detecção de** – detecta e converte os dados de valores em elementos clicáveis, como números de telefone que podem disparar uma chamada, endereços que se tornam links para mapas de URLs que abrem no Safari ou datas e horas que se tornam a eventos no calendário.


Se um UITextView tiver sido adicionado a uma tela com o designer, você pode definir ou alterar suas propriedades como este:

```csharp
textview1.Text = "Lorem ipsum..."; // lots of text can go here
textview1.Editable = true;
textview1.DataDetectorTypes = UIDataDetectorType.PhoneNumber | UIDataDetectorType.Link;
```



## <a name="related-links"></a>Links relacionados

- [Controles (amostra)](https://developer.xamarin.com/samples/Controls/)
