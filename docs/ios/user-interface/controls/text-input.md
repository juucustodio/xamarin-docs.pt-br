---
title: Entrada de Texto
ms.prod: xamarin
ms.assetid: 03A7F1DC-017D-4501-91FD-82C78272CDB1
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 43f1f0318bceb3da8a3d6216a49ec2af48ac155c
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="text-input"></a>Entrada de Texto

Aceitar entrada de texto do usuário é realizada com o `UITextField` para entradas de linha única e UITextView para várias linhas de texto editável. Você pode arrastar um desses controles em uma tela e clique duas vezes para definir o texto inicial.

Capturas de tela abaixo mostram os ícones para esses controles, localizados no painel de ferramentas do Visual Studio para Mac:

 [![](text-input-images/image11a.png "UITextField")](text-input-images/image11a.png#lightbox)

 [![](text-input-images/image13a.png "UITextView")](text-input-images/image13a.png#lightbox)

Depois de ter chamado na tomada e salvar o arquivo de Storyboard, Visual Studio para Mac atualizará o `.designer.cs` classe parcial e você pode adicionar o código c# que referencia o controle ao seu arquivo de classe. Cada controle tem suas próprias propriedades exclusivas e os eventos que podem ser acessados em seu código c#.

 <a name="UITextField" />


## <a name="uitextfield"></a>UITextField

O `UITextField` controle geralmente é usado para aceitar uma única linha de entrada de texto como um nome de usuário ou senha. Algumas das opções disponíveis para personalizar o controle são mostradas aqui:

 [![](text-input-images/image15a.png "Propriedades de UITextField")](text-input-images/image15a.png#lightbox)

Esses controles são explicados abaixo:

-  **Espaço reservado** – isso é opcional. Se definido, ele será exibido quando o campo de texto estiver vazio, geralmente para explicar para o usuário que a entrada é esperada.
-  **Botão Limpar** – Isso controla quando o botão Limpar padrão (o círculo cinza com (X)) é exibido no campo de texto, como uma maneira para o usuário limpar o texto rapidamente. Ele pode ser oculto permanentemente, permanentemente visível ou mostrado, dependendo se o campo está sendo editado.
-  **Tamanho de fonte mínimo** e **ajustar para ajustar** – permite que o tamanho da fonte ser ajustado automaticamente para ajustar mais texto e impedir o truncamento, mas pouca ou nenhuma menor do que o tamanho especificado.
-  **O uso de maiusculas** – se aproveitar automaticamente palavras, frases ou todas as entradas.
-  **Correção** – se a verificação ortográfica e sugestões estão habilitadas.
-  **Teclado** – controla o estilo de teclado exibido para a entrada e, portanto, quais chaves estão disponíveis no teclado. Isso inclui o número preenchimento, preenchimento de telefone, Email, URL junto com outras opções.
-  **Aparência** – controla o estilo de aparência do teclado e serão ambos escuro ou claro com tema.
-  **Retorna a chave** – alterar o rótulo na chave de retorno para refletir melhor a ação que será executada. Valores com suporte incluem Go, junção, Avançar, rota, concluído e a pesquisa.
-  **Proteger** – indica se a entrada é mascarada (como uma entrada de senha).


Se um UITextField chamado `textfield1` foi adicionado a uma tela com o designer, você pode definir ou alterar suas propriedades no c# da seguinte maneira:

```csharp
textfield1.Placeholder = "type email here...";
textfield1.KeyboardType = UIKeyboardType.EmailAddress;
textfield1.ReturnKeyType = UIReturnKeyType.Send;
textfield1.MinimumFontSize = 17f;
textfield1.AdjustsFontSizeToFitWidth = true;
```

Xamarin fornece enumerações onde apropriado para tornar mais fácil selecionar as configurações desejadas, como o `UIKeyboardType` e `UIReturnKeyType` no trecho de código acima.

### <a name="display-text-programmatically"></a>Exibir texto programaticamente

Se você não deseja projetar sua tela com o designer ou se você quiser adicionar algum texto dinamicamente em tempo de execução, você pode criar e exibir um UITextField programaticamente o `ViewDidLoad` método de um controlador de exibição como esta:

```csharp
var frame = new CGRect(10, 10, 300, 40);
textfield1 = new UITextField(frame);
View.Add(textfield1);
```

 <a name="UITextView" />


## <a name="uitextview"></a>UITextView

O `UITextView` controle pode ser usado para exibir texto somente leitura ou aceitar entrada de texto de várias linhas. Ele tem muitas das mesmas opções que o `UITextField` (como o uso de correção, etc).

 [![](text-input-images/image16a.png "Propriedades de UITextView")](text-input-images/image16a.png#lightbox)

Propriedades específicas incluem:

-  **Comportamento** – se o texto é editável ou somente leitura.
-  **Detecção de** – detecta e converte os dados de valores em clicáveis elementos, como números de telefone que podem disparar uma chamada de endereços que se tornam links de mapas, de URLs que são abertos no Safari ou datas e horas que se tornam a eventos no calendário.


Se um UITextView tiver sido adicionado a uma tela com o designer, você pode definir ou alterar suas propriedades como este:

```csharp
textview1.Text = "Lorem ipsum..."; // lots of text can go here
textview1.Editable = true;
textview1.DataDetectorTypes = UIDataDetectorType.PhoneNumber | UIDataDetectorType.Link;
```



## <a name="related-links"></a>Links relacionados

- [Controles (exemplo)](https://developer.xamarin.com/samples/Controls/)
