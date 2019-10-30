---
title: Entrada de texto no Xamarin. iOS
description: Este documento descreve a entrada de texto em um aplicativo Xamarin. iOS. Ele aborda o uso de UITextField e UITextVIew de forma programática e no designer do iOS.
ms.prod: xamarin
ms.assetid: 03A7F1DC-017D-4501-91FD-82C78272CDB1
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 4833d8a03649341cb5c6d9f2692410b89e6cea4c
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73021829"
---
# <a name="text-input-in-xamarinios"></a>Entrada de texto no Xamarin. iOS

A aceitação da entrada de texto de usuário é realizada com a `UITextField` para entradas de linha única e UITextView para texto editável de várias linhas. Você pode arrastar um desses controles para uma tela e clicar duas vezes para definir o texto inicial.

As capturas de tela abaixo mostram os ícones para esses controles, localizados no painel caixa de ferramentas no Visual Studio para Mac:

 [![](text-input-images/image11a.png "UITextField")](text-input-images/image11a.png#lightbox)

 [![](text-input-images/image13a.png "UITextView")](text-input-images/image13a.png#lightbox)

Depois de ter nomeado a tomada e salvo o arquivo de storyboard, Visual Studio para Mac atualizará a `.designer.cs` classe parcial e você poderá C# adicionar um código que referencie o controle em seu arquivo de classe. Cada controle tem suas próprias propriedades e eventos exclusivos que podem ser acessados C# em seu código.

 <a name="UITextField" />

## <a name="uitextfield"></a>UITextField

O controle de `UITextField` é usado com mais frequência para aceitar uma única linha de entrada de texto, como um nome de usuário ou senha. Algumas das opções disponíveis para personalizar o controle são mostradas aqui:

 [![](text-input-images/image15a.png "UITextField Properties")](text-input-images/image15a.png#lightbox)

Esses controles são explicados abaixo:

- **Espaço reservado** – isso é opcional. Se definido, ele será exibido quando o campo de texto estiver vazio, geralmente para explicar ao usuário qual entrada é esperada.
- **Botão limpar** – isso controla quando o botão limpar padrão (o círculo cinza com (X)) aparece no campo de texto, como uma maneira para o usuário limpar o texto rapidamente. Ele pode ser permanentemente oculto, visível permanentemente ou mostrado, dependendo se o campo está sendo editado ou não.
- **Tamanho mínimo da fonte** e **ajustar para ajustar** – permite que o tamanho da fonte seja ajustado automaticamente para ajustar o texto mais longo e evitar truncamento, mas limitado a nenhum tamanho menor do que o especificado.
- **Capitalização** – se as palavras, frases ou todas as entradas devem ser colocadas automaticamente em maiúsculas.
- **Correção** – se a verificação ortográfica e as sugestões estão habilitadas.
- **Teclado** – controla o estilo de teclado exibido para a entrada e, portanto, quais chaves estão disponíveis no teclado. Isso inclui o teclado numérico, o Phone pad, o email e a URL junto com outras opções.
- **Aparência** – controla o estilo de aparência do teclado e será escuro ou leve.
- **Chave de retorno** – altere o rótulo na chave de retorno para refletir melhor qual ação será tomada. Os valores com suporte incluem Go, junção, avançar, rota, concluído e pesquisa.
- **Seguro** – identifica se a entrada é mascarada (por exemplo, para uma entrada de senha).

Se um UITextField chamado `textfield1` tiver sido adicionado a uma tela com o designer, você poderá definir ou alterar suas propriedades no C# da seguinte maneira:

```csharp
textfield1.Placeholder = "type email here...";
textfield1.KeyboardType = UIKeyboardType.EmailAddress;
textfield1.ReturnKeyType = UIReturnKeyType.Send;
textfield1.MinimumFontSize = 17f;
textfield1.AdjustsFontSizeToFitWidth = true;
```

O Xamarin. iOS fornece enumerações quando apropriado para facilitar a seleção das configurações desejadas, como a `UIKeyboardType` e `UIReturnKeyType` no trecho de código acima.

### <a name="display-text-programmatically"></a>Exibir texto programaticamente

Se você não quiser projetar sua tela com o designer ou se desejar adicionar dinamicamente algum texto em tempo de execução, poderá criar e exibir um UITextField programaticamente no método de `ViewDidLoad` de um controlador de exibição como este:

```csharp
var frame = new CGRect(10, 10, 300, 40);
textfield1 = new UITextField(frame);
View.Add(textfield1);
```

 <a name="UITextView" />

## <a name="uitextview"></a>UITextView

O controle de `UITextView` pode ser usado para exibir texto somente leitura ou para aceitar a entrada de texto de várias linhas. Ele tem muitas das mesmas opções que o `UITextField` (como capitalização, correção, etc.).

 [![](text-input-images/image16a.png "UITextView Properties")](text-input-images/image16a.png#lightbox)

As propriedades específicas incluem:

- **Comportamento** – se o texto é editável ou somente leitura.
- **Detecção** – detecta e converte os dados inseridos em elementos clicáveis, como números de telefone que podem disparar uma chamada, endereços que se tornam links para mapas, URLs que são abertas no Safari ou datas e horas que se tornam eventos no calendário.

Se um UITextView tiver sido adicionado a uma tela com o designer, você poderá definir ou alterar suas propriedades como esta:

```csharp
textview1.Text = "Lorem ipsum..."; // lots of text can go here
textview1.Editable = true;
textview1.DataDetectorTypes = UIDataDetectorType.PhoneNumber | UIDataDetectorType.Link;
```

## <a name="related-links"></a>Links relacionados

- [Controles (exemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/controls)
