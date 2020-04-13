---
title: Associação de um .AAR
description: Este passo a passo fornece instruções passo-a-passo para criar uma Biblioteca de Vinculações Xamarin.Android Java a partir de um Android . Arquivo AAR.
ms.prod: xamarin
ms.assetid: 380413B8-6A99-4BB8-B64C-3EAF9F359C22
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/11/2018
ms.openlocfilehash: 103720c8cb47b1ac4cfe5cfadeb6b18828318ad3
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73618544"
---
# <a name="binding-an-aar"></a>Associação de um .AAR

_Este passo a passo fornece instruções passo-a-passo para criar uma Biblioteca de Vinculações Xamarin.Android Java a partir de um Android . Arquivo AAR._

## <a name="overview"></a>Visão geral

O *Arquivo Android (. Arquivo AAR)* é o formato de arquivo para bibliotecas Android.
Um. Arquivo AAR é um . Arquivo ZIP que contém o seguinte:

- Código Java compilado
- IDs de recurso
- Recursos
- Meta-dados (por exemplo, declarações de atividade, permissões)

Neste guia, vamos passar pelo básico da criação de uma Biblioteca de Vinculações para um único . Arquivo AAR. Para obter uma visão geral da vinculação da biblioteca Java em geral (com um exemplo de código básico), consulte [Vincular uma Biblioteca Java](~/android/platform/binding-java-library/index.md).

> [!IMPORTANT]
> Um projeto de vinculação só pode incluir um . Arquivo AAR. Se o . AAR depende de outros . AAR, então essas dependências devem ser contidas em seu próprio projeto de vinculação e, em seguida, referenciadas. Veja [o Bug 44573](https://bugzilla.xamarin.com/show_bug.cgi?id=44573).

## <a name="walkthrough"></a>Passo a passo

Criaremos uma Biblioteca de Vinculações para um exemplo de arquivo Android que foi criado no Android Studio, [textanalyzer.aar](https://github.com/xamarin/monodroid-samples/blob/master/JavaIntegration/AarBinding/Resources/textanalyzer.aar?raw=true). Este. AAR contém `TextCounter` uma classe com métodos estáticos que contam o número de vogais e consoantes em uma seqüência. Além disso, **textanalyzer.aar** contém um recurso de imagem para ajudar a exibir os resultados da contagem.

Usaremos as seguintes etapas para criar uma Biblioteca de Vinculações a partir do . Arquivo AAR:

1. Crie um novo projeto da Biblioteca de Vinculações Java.

2. Adicione um único . Arquivo AAR para o projeto. Um projeto de vinculação só pode conter um único . Aar.

3. Defina a ação de construção apropriada para o . Arquivo AAR.

4. Escolha uma estrutura de destino que o . AAR suporta.

5. Construa a Biblioteca de Vinculações.

Uma vez que criamos a Biblioteca de Vinculações, desenvolveremos um pequeno aplicativo para Android que solicita ao usuário uma seqüência de texto, chamadas . Métodos AAR para analisar o texto, recupera a imagem do . AAR, e exibe os resultados junto com a imagem.

O aplicativo de `TextCounter` exemplo acessará a classe de **textanalyzer.aar**:

```java
package com.xamarin.textcounter;

public class TextCounter
{
    ...
    public static int numVowels (String text) { ... };
    ...
    public static int numConsonants (String text) { ... };
    ...
}
```

Além disso, este aplicativo de exemplo recuperará e exibirá um recurso de imagem que está embalado em **textanalyzer.aar**:

[![Imagem de macaco xamarin](binding-an-aar-images/00-monkey-sml.png)](binding-an-aar-images/00-monkey.png#lightbox)

Este recurso de imagem reside em **res/drawable/monkey.png** em **textanalyzer.aar**.

### <a name="creating-the-bindings-library"></a>Criando a Biblioteca de Vinculações

Antes de começar com as etapas abaixo, baixe o exemplo [textanalyzer.aar](https://github.com/xamarin/monodroid-samples/blob/master/JavaIntegration/AarBinding/Resources/textanalyzer.aar?raw=true) arquivo Android:

1. Crie um novo projeto de Biblioteca de Vinculações começando com o modelo da Biblioteca de Vinculações do Android. Você pode usar o Visual Studio para Mac ou Visual Studio (as capturas de tela abaixo mostram o Visual Studio, mas o Visual Studio para Mac é muito semelhante). Nomeie a solução **AarBinding:**

    [![Criar projeto AarBindings](binding-an-aar-images/01-new-bindings-library-vs-sml.w160.png)](binding-an-aar-images/01-new-bindings-library-vs.w160.png#lightbox)

2. O modelo inclui uma pasta **Jars** onde você adiciona o seu . AAR(s) para o projeto Biblioteca de Vinculações. Clique com o botão direito do mouse na pasta **Jars** e **selecione Adicionar > item existente**:

    [![Adicionar item existente](binding-an-aar-images/02-add-existing-item-vs-sml.png)](binding-an-aar-images/02-add-existing-item-vs.png#lightbox)

3. Navegue até o arquivo **textanalyzer.aar** baixado anteriormente, selecione-o e clique **em Adicionar**:

    [![Adicionar textanalayzer.aar](binding-an-aar-images/03-select-aar-file-vs-sml.png)](binding-an-aar-images/03-select-aar-file-vs.png#lightbox)

4. Verifique se o arquivo **textanalyzer.aar** foi adicionado com sucesso ao projeto:

    [![O arquivo textanalyzer.aar foi adicionado](binding-an-aar-images/04-aar-added-vs-sml.png)](binding-an-aar-images/04-aar-added-vs.png#lightbox)

5. Defina a Ação de Compilação `LibraryProjectZip`para **textanalyzer.aar** para . No Visual Studio for Mac, clique com o botão direito **do mouse textanalyzer.aar** para definir a Ação de Compilação. No Visual Studio, a Ação de Construção pode ser definida no painel **Propriedades):**

    [![Definindo a ação textanalyzer.aar build to LibraryProjectZip](binding-an-aar-images/05-embedded-aar-vs-sml.png)](binding-an-aar-images/05-embedded-aar-vs.png#lightbox)

6. Abra as propriedades do projeto para configurar o *Quadro de Destino*. Se o . AAR usa quaisquer APIs do Android, defina o Target Framework para o nível de API que o . AAr espera. (Para obter mais informações sobre a configuração do Quadro de Destino e os níveis de API do Android em geral, consulte [Entendendo os níveis de API do Android](~/android/app-fundamentals/android-api-levels.md).)

    Defina o nível de API de destino para sua Biblioteca de Vinculações. Neste exemplo, estamos livres para usar o nível de API da plataforma mais recente (nível DePI 23) porque nosso **textanalyzer** não tem uma dependência de APIs Android:

    [![Definindo o nível de destino para API 23](binding-an-aar-images/06-set-target-framework-vs-sml.png)](binding-an-aar-images/06-set-target-framework-vs.png#lightbox)

7. Construa a Biblioteca de Vinculações. O projeto Biblioteca de Vinculações deve ser construído com sucesso e produzir uma saída . DLL no seguinte local: **AarBinding/bin/Debug/AarBinding.dll**

### <a name="using-the-bindings-library"></a>Usando a Biblioteca de Vinculações

Para consumir isso. DLL em seu aplicativo Xamarin.Android, você deve primeiro adicionar uma referência à Biblioteca de Vinculações. Use as seguintes etapas:

1. Estamos criando este aplicativo na mesma solução que a Biblioteca de Vinculações para simplificar esse passo a passo. (O aplicativo que consome a Biblioteca de Vinculações também pode residir em uma solução diferente.) Crie um novo aplicativo Xamarin.Android: clique com o botão direito do mouse na solução e selecione **Adicionar novo projeto**. Nomeie o novo projeto **BindingTest**:

    [![Criar novo projeto BindingTest](binding-an-aar-images/07-add-new-project-vs-sml.w157.png)](binding-an-aar-images/07-add-new-project-vs.w157.png#lightbox)

2. Clique com o botão direito do mouse no nó **Referências** do projeto **BindingTest** e **selecione Adicionar referência...**:

    [![Clique em Adicionar referência](binding-an-aar-images/08-add-reference-vs-sml.png)](binding-an-aar-images/08-add-reference-vs.png#lightbox)

3. Selecione o **projeto AarBinding** criado anteriormente e clique em **OK**:

    [![Verifique o projeto de vinculação AAR](binding-an-aar-images/09-choose-aar-binding-vs-sml.png)](binding-an-aar-images/09-choose-aar-binding-vs.png#lightbox)

4. Abra o nó **Referências** do projeto **BindingTest** para verificar se a referência **AarBinding** está presente:

    [![AarBinding está listado em Referências](binding-an-aar-images/10-references-shows-aarbinding-vs-sml.png)](binding-an-aar-images/10-references-shows-aarbinding-vs.png#lightbox)

Se você quiser visualizar o conteúdo do projeto Biblioteca de Vinculação, clique duas vezes na referência para abri-la no **Navegador de Objetos**. Você pode ver o conteúdo `Com.Xamarin.Textcounter` mapeado do `com.xamarin.textanalyzezr` namespace (mapeado a `TextCounter` partir do pacote Java) e você pode visualizar os membros da classe:

[![Visualização do navegador de objetos](binding-an-aar-images/11-object-browser-vs-sml.png)](binding-an-aar-images/11-object-browser-vs.png#lightbox)

A captura de tela `TextAnalyzer` acima destaca os dois `NumConsonants` métodos que o aplicativo `numConsonants` de `NumVowels` exemplo chamará: (que `numVowels` envolve o método Java subjacente) e (que envolve o método Java subjacente).

### <a name="accessing-aar-types"></a>Acessar. Tipos AAR

Depois de adicionar uma referência ao seu aplicativo que aponta para a Biblioteca de Vinculação, você pode acessar tipos Java no . AAR como você acessaria os tipos C# (graças aos invólucros C#). O código do `TextAnalyzer` aplicativo C# pode chamar métodos como ilustrado neste exemplo:

```csharp
using Com.Xamarin.Textcounter;
...
int numVowels = TextCounter.NumVowels (myText);
int numConsonants = TextCounter.NumConsonants (myText);
```

No exemplo acima, estamos chamando métodos `TextCounter` estáticos na classe. No entanto, você também pode instanciar classes e métodos de instância de chamada. Por exemplo, se o seu . AAR envolve uma `Employee` classe chamada que `buildFullName`tem o `MyClass` método de instância, você pode instanciar e usá-lo como visto aqui:

```csharp
var employee = new Com.MyCompany.MyProject.Employee();
var name = employee.BuildFullName ();
```

As etapas a seguir adicionam código ao aplicativo para `TextCounter` que ele indiste o usuário para texto, use para analisar o texto e, em seguida, exiba os resultados.

Substitua o layout **BindingTest** **(Main.axml)** pelo seguinte XML. Este layout `EditText` tem uma entrada de texto para entrada de texto e dois botões para o início da contagem de vogais e consoantes:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation             ="vertical"
    android:layout_width            ="fill_parent"
    android:layout_height           ="fill_parent" >
    <TextView
        android:text                ="Text to analyze:"
        android:textSize            ="24dp"
        android:layout_marginTop    ="30dp"
        android:layout_gravity      ="center"
        android:layout_width        ="wrap_content"
        android:layout_height       ="wrap_content" />
    <EditText
        android:id                  ="@+id/input"
        android:text                ="I can use my .AAR file from C#!"
        android:layout_marginTop    ="10dp"
        android:layout_gravity      ="center"
        android:layout_width        ="300dp"
        android:layout_height       ="wrap_content"/>
    <Button
        android:id                  ="@+id/vowels"
        android:layout_marginTop    ="30dp"
        android:layout_width        ="240dp"
        android:layout_height       ="wrap_content"
        android:layout_gravity      ="center"
        android:text                ="Count Vowels" />
    <Button
        android:id                  ="@+id/consonants"
        android:layout_width        ="240dp"
        android:layout_height       ="wrap_content"
        android:layout_gravity      ="center"
        android:text                ="Count Consonants" />
</LinearLayout>
```

Substitua o conteúdo do **MainActivity.cs** pelo seguinte código. Como visto neste exemplo, os manipuladores `TextCounter` de eventos de botão chamam métodos embrulhados que residem no . AAR e use torradas para exibir os resultados. Observe `using` a declaração para o namespace da `Com.Xamarin.Textcounter`biblioteca vinculada (neste caso, ):

```csharp
using System;
using Android.App;
using Android.Content;
using Android.Runtime;
using Android.Views;
using Android.Widget;
using Android.OS;
using Android.Views.InputMethods;
using Com.Xamarin.Textcounter;

namespace BindingTest
{
    [Activity(Label = "BindingTest", MainLauncher = true, Icon = "@drawable/icon")]
    public class MainActivity : Activity
    {
        InputMethodManager imm;

        protected override void OnCreate(Bundle bundle)
        {
            base.OnCreate(bundle);

            SetContentView(Resource.Layout.Main);

            imm = (InputMethodManager)GetSystemService(Context.InputMethodService);

            var vowelsBtn = FindViewById<Button>(Resource.Id.vowels);
            var consonBtn = FindViewById<Button>(Resource.Id.consonants);
            var edittext = FindViewById<EditText>(Resource.Id.input);
            edittext.InputType = Android.Text.InputTypes.TextVariationPassword;

            edittext.KeyPress += (sender, e) =>
            {
                imm.HideSoftInputFromWindow(edittext.WindowToken, HideSoftInputFlags.NotAlways);
                e.Handled = true;
            };

            vowelsBtn.Click += (sender, e) =>
            {
                int count = TextCounter.NumVowels(edittext.Text);
                string msg = count + " vowels found.";
                Toast.MakeText (this, msg, ToastLength.Short).Show ();
            };

            consonBtn.Click += (sender, e) =>
            {
                int count = TextCounter.NumConsonants(edittext.Text);
                string msg = count + " consonants found.";
                Toast.MakeText (this, msg, ToastLength.Short).Show ();
            };

        }
    }
}
```

Compile e execute o projeto **BindingTest.** O aplicativo iniciará e apresentará a `EditText` captura de tela à esquerda (a é inicializada com algum texto, mas você pode tocá-la para alterá-la). Quando você toca **EM COUNT VOWELS**, uma torrada exibe o número de vogais como mostrado à direita:

[![Capturas de tela da execução do BindingTest](binding-an-aar-images/12-count-vowels.png)](binding-an-aar-images/12-count-vowels.png#lightbox)

Tente tocar no botão **COUNT CONSONANTS.** Além disso, você pode modificar a linha de texto e tocar nestes botões novamente para testar para diferentes contagens vocálicas e consoantes.

### <a name="accessing-aar-resources"></a>Acessar. Recursos AAR

A ferramenta Xamarin mescla os dados **R** do . AAR na classe **Resource** do seu aplicativo. Como resultado, você pode acessar . Recursos AAR do seu layout (e do code-behind) da mesma forma que você acessaria recursos que estão no caminho **recursos** do seu projeto.

Para acessar um recurso de imagem, use o nome **Resource.Drawable** para a imagem embalada dentro do . Aar. Por exemplo, você pode referenciar **image.png** no . Arquivo AAR `@drawable/image`usando:

```xml
<ImageView android:src="@drawable/image" ... />
```

Você também pode acessar layouts de recursos que residem no . Aar. Para fazer isso, você usa o nome **Resource.Layout** para o layout embalado dentro do . Aar. Por exemplo:

```csharp
var a = new ArrayAdapter<string>(this, Resource.Layout.row_layout, ...);
```

O exemplo **textanalyzer.aar** contém um arquivo de imagem que reside em **res/drawable/monkey.png**. Vamos acessar este recurso de imagem e usá-lo em nosso aplicativo de exemplo:

Edite o layout **BindingTest** **(Main.axml)** e adicione um `ImageView` à extremidade do `LinearLayout` recipiente. Isso `ImageView` exibe a imagem encontrada em ** \@desenho/macaco;** esta imagem será carregada a partir da seção de recursos do **textanalyzer.aar**:

```xml
    ...
    <ImageView
        android:src                 ="@drawable/monkey"
        android:layout_marginTop    ="40dp"
        android:layout_width        ="200dp"
        android:layout_height       ="200dp"
        android:layout_gravity      ="center" />

</LinearLayout>
```

Compile e execute o projeto **BindingTest.** O aplicativo iniciará e apresentará &ndash; a captura de tela à esquerda quando você tocar em **COUNT CONSONANTS**, os resultados são exibidos como mostrado à direita:

[![AtadaTeste de exibição de contagem de consoantes](binding-an-aar-images/13-count-consonants.png)](binding-an-aar-images/13-count-consonants.png#lightbox)

Parabéns! Você conseguiu uma biblioteca Java com sucesso. Aar!

## <a name="summary"></a>Resumo

Neste passo a passo, criamos uma Biblioteca de Vinculações para um . Arquivo AAR, adicionou a Biblioteca de Vinculações a um aplicativo de teste mínimo e executou o aplicativo para verificar se nosso código C# pode chamar código Java residente no . Arquivo AAR.
Além disso, ampliamos o aplicativo para acessar e exibir um recurso de imagem que reside no . Arquivo AAR.

## <a name="related-links"></a>Links relacionados

- [Construindo uma Biblioteca de Vinculações Java (vídeo)](https://university.xamarin.com/classes#10090)
- [Associação de um .JAR](~/android/platform/binding-java-library/binding-a-jar.md)
- [Associação de uma biblioteca Java](~/android/platform/binding-java-library/index.md)
- [AarBinding (amostra)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/javaintegration-aarbinding)
- [Bug 44573 - Um projeto não pode vincular vários arquivos .aar](https://bugzilla.xamarin.com/show_bug.cgi?id=44573)
