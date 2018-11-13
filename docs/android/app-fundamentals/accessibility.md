---
title: Acessibilidade no Android
ms.prod: xamarin
ms.assetid: 157F0899-4E3E-4538-90AF-B59B8A871204
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/28/2018
ms.openlocfilehash: 3cce3270b9df2aad0037b1ab96f169cc4b564766
ms.sourcegitcommit: 849bf6d1c67df943482ebf3c80c456a48eda1e21
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2018
ms.locfileid: "51528124"
---
# <a name="accessibility-on-android"></a>Acessibilidade no Android

Esta página descreve como usar APIs de acessibilidade do Android para criar aplicativos de acordo com o [lista de verificação de acessibilidade](~/cross-platform/app-fundamentals/accessibility.md).
Consulte a [iOS acessibilidade](~/ios/app-fundamentals/accessibility.md) e [acessibilidade dos X](~/mac/app-fundamentals/accessibility.md) páginas para outras APIs da plataforma.


## <a name="describing-ui-elements"></a>Que descreve os elementos de interface do usuário

O Android fornece um `ContentDescription` propriedade que é usada por APIs de leitura de tela para fornecer uma descrição acessível da finalidade do controle.

A descrição do conteúdo pode ser definida no C# ou no arquivo de layout AXML.

**C#**

A descrição pode ser definida no código para qualquer cadeia de caracteres (ou um recurso de cadeia de caracteres):

```csharp
saveButton.ContentDescription = "Save data";
```

**Layout AXML**

No XML de layouts de usam o `android:contentDescription` atributo:

```xml
<ImageButton
    android:id=@+id/saveButton"
    android:src="@drawable/save_image"
    android:contentDescription="Save data" />
```

### <a name="use-hint-for-textview"></a>Use a dica para TextView

Para `EditText` e `TextView` controles de entrada de dados, use o `Hint` propriedade para fornecer uma descrição de quais entrada é esperada (em vez de `ContentDescription`).
Quando algum texto foi inserido, o próprio texto serão "lidos", em vez da dica.

**C#**

Defina o `Hint` propriedade no código:

```csharp
someText.Hint = "Enter some text"; // displays (and is "read") when control is empty
```

**Layout AXML**

No XML de arquivos de layout usam o `android:hint` atributo:

```xml
<EditText
    android:id="@+id/someText"
    android:hint="Enter some text" />
```


### <a name="labelfor-links-input-fields-with-labels"></a>Campos com rótulos de entrada de LabelFor links

Para associar um rótulo com um controle de entrada de dados, use o `LabelFor` propriedade

**C#**

No C#, defina o `LabelFor` propriedade para a ID do recurso de controle que descreve este conteúdo (normalmente essa propriedade é definida em um rótulo e faz referência a um outro controle de entrada):

```csharp
EditText edit = FindViewById<EditText> (Resource.Id.editFirstName);
TextView tv = FindViewById<TextView> (Resource.Id.labelFirstName);
tv.LabelFor = Resource.Id.editFirstName;
```

**Layout AXML**

No uso do XML de layout a `android:labelFor` propriedade a referenciar o identificador do controle para outro:

```xml
<TextView
    android:id="@+id/labelFirstName"
    android:hint="Enter some text"
    android:labelFor="@+id/editFirstName" />
<EditText
    android:id="@+id/editFirstName"
    android:hint="Enter some text" />
```

### <a name="announce-for-accessibility"></a>Anunciar para acessibilidade

Use o `AnnounceForAccessibility` controle para se comunicar uma alteração de status ou evento aos usuários quando é habilitado na acessibilidade de exibição do método em qualquer um. Esse método não é necessário para a maioria das operações em que a narração interna fornece comentários suficientes, mas deve ser usada, quais informações adicionais pode ser útil para o usuário.

O código a seguir mostra uma chamada de exemplo simples `AnnounceForAccessibility`:

```csharp
button.Click += delegate {
  button.Text = string.Format ("{0} clicks!", count++);
  button.AnnounceForAccessibility (button.Text);
};
```

## <a name="changing-focus-settings"></a>Alterando as configurações de foco

Navegação acessível se baseia em controles que têm o foco para ajudar o usuário a compreender quais operações estão disponíveis. O Android fornece um `Focusable` propriedade que pode marcar controles como especificamente capaz de receber o foco durante a navegação.

**C#**

Para impedir que um controle ganhando o foco com C#, defina o `Focusable` propriedade a ser `false`:

```csharp
label.Focusable = false;
```

**Layout AXML**

Conjunto de arquivos do XML no layout de `android:focusable` atributo:

```xml
<android:focusable="false" />
```

Você também pode controlar a ordem de foco com o `nextFocusDown`, `nextFocusLeft`, `nextFocusRight`, `nextFocusUp` atributos, normalmente é definidos no layout AXML. Use esses atributos para garantir que o usuário pode navegar facilmente por meio de controles na tela.


## <a name="accessibility-and-localization"></a>Acessibilidade e localização

Nos exemplos acima são a descrição da dica e o conteúdo definido diretamente como o valor de exibição. É preferível usar valores em uma **strings. XML** arquivo, como este:

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="enter_info">Enter some text</string>
    <string name="save_info">Save data</string>
</resources>
```

Usando o texto de um arquivo de cadeias de caracteres é mostrado abaixo em C# e arquivos de layout AXML:

**C#**

Em vez de usar literais de cadeia de caracteres no código, pesquisar valores traduzidos de arquivos de cadeias de caracteres com `Resources.GetText`:

```csharp
someText.Hint = Resources.GetText (Resource.String.enter_info);
saveButton.ContentDescription = Resources.GetText (Resource.String.save_info);
```

**AXML**

No XML de layout, como atributos de acessibilidade `hint` e `contentDescription` pode ser definido como um identificador de cadeia de caracteres:

```xml
<TextView
    android:id="@+id/someText"
    android:hint="@string/enter_info" />
<ImageButton
    android:id=@+id/saveButton"
    android:src="@drawable/save_image"
    android:contentDescription="@string/save_info" />
```

O benefício de armazenar texto em um arquivo separado é que várias traduções de idioma do arquivo podem ser fornecidas em seu aplicativo. Consulte a [guia de localização Android](~/android/app-fundamentals/localization.md) para saber como adicionar arquivos de cadeia de caracteres localizada para um projeto de aplicativo.


## <a name="testing-accessibility"></a>Teste de acessibilidade

Siga [essas etapas](http://developer.android.com/training/accessibility/testing.html#how-to) para habilitar o TalkBack e explorar por toque testar a acessibilidade em dispositivos Android.

Talvez você precise instalar [TalkBack](https://play.google.com/store/apps/details?id=com.google.android.marvin.talkback) no Google Play, se ele não aparecer na **Configurações > acessibilidade**.


## <a name="related-links"></a>Links relacionados

- [Acessibilidade de plataforma cruzada](~/cross-platform/app-fundamentals/accessibility.md)
- [APIs de acessibilidade do Android](http://developer.android.com/guide/topics/ui/accessibility/index.html)
