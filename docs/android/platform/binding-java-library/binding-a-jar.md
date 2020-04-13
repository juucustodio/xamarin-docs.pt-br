---
title: Associação de um .JAR
description: Este passo a passo fornece instruções passo-a-passo para criar uma Biblioteca de Vinculações Xamarin.Android Java a partir de um Android . Arquivo JAR.
ms.prod: xamarin
ms.assetid: 93F1D5C5-E2AF-46EA-8460-485A0860C176
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/11/2018
ms.openlocfilehash: 59969abae739db1d9035ec31738c39a3912f47ae
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027770"
---
# <a name="binding-a-jar"></a>Associação de um .JAR

_Este passo a passo fornece instruções passo-a-passo para criar uma Biblioteca de Vinculações Xamarin.Android Java a partir de um Android . Arquivo JAR._

## <a name="overview"></a>Visão geral

A comunidade Android oferece muitas bibliotecas Java que você pode querer usar em seu aplicativo. Essas bibliotecas Java são frequentemente embaladas em . Formato JAR (Java Archive), mas você pode empacotar um . Jar-lo em uma *Biblioteca de Vinculações Java* para que sua funcionalidade esteja disponível para aplicativos Xamarin.Android. O objetivo da biblioteca Java Bindings é fazer as APIs no . Arquivo JAR disponível para código C# através de invólucros de código gerados automaticamente.

As ferramentas Xamarin podem gerar uma Biblioteca de Vinculações a partir de uma ou mais entradas . Arquivos JAR. A Biblioteca de Vinculações (. O conjunto DLL) contém o seguinte: 

- O conteúdo do original. Arquivos JAR(s).

- Wrappers callable gerenciados (MCW), que são tipos C# que envolvem tipos Java correspondentes dentro do . Arquivos JAR(s).

O código MCW gerado usa JNI (Java Native Interface) para encaminhar suas chamadas de API para o subjacente . Arquivo JAR. Você pode criar bibliotecas de vinculações para qualquer . Arquivo JAR que foi originalmente direcionado para ser usado com Android (note que a ferramenta Xamarin não suporta atualmente a vinculação de bibliotecas Java não-Android). Você também pode optar por construir a Biblioteca de Vinculações sem incluir o conteúdo do . Arquivo JAR para que o DLL tenha uma dependência do . JAR em tempo de execução.

Neste guia, vamos passar pelo básico da criação de uma Biblioteca de Vinculações para um único . Arquivo JAR. Vamos ilustrar com um exemplo &ndash; onde tudo dá certo que é, onde não é necessária personalização ou depuração de vinculações. 
[Criar vinculações usando metadados](~/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata.md) oferece um exemplo de um cenário mais avançado onde o processo de vinculação não é totalmente automático e alguma quantidade de intervenção manual é necessária. Para obter uma visão geral da vinculação da biblioteca Java em geral (com um exemplo de código básico), consulte [Vincular uma Biblioteca Java](~/android/platform/binding-java-library/index.md). 

## <a name="walkthrough"></a>Passo a passo

No passo a passo a seguir, criaremos uma Biblioteca de Vinculações para [Picasso](https://square.github.io/picasso/), um Android popular. JAR que fornece funcionalidade de carregamento e cache de imagem. Usaremos as seguintes etapas para vincular **picasso-2.x.x.jar** para criar um novo conjunto .NET que podemos usar em um projeto Xamarin.Android: 

1. Crie um novo projeto da Biblioteca de Vinculações Java.

2. Adicione o . Arquivo JAR para o projeto.

3. Defina a ação de construção apropriada para o . Arquivo JAR.

4. Escolha uma estrutura de destino que o . Jar suporta.

5. Construa a Biblioteca de Vinculações.

Assim que criarmos a Biblioteca de Vinculações, desenvolveremos um pequeno aplicativo para Android que demonstra nossa capacidade de chamar APIs na Biblioteca de Vinculações. Neste exemplo, queremos acessar métodos de **picasso-2.x.x.jar**:

```java
package com.squareup.picasso

public class Picasso
{ 
    ...
    public static Picasso with (Context context) { ... };
    ...
    public RequestCreator load (String path) { ... };
    ...
}
```

Depois de gerarmos uma Biblioteca de Vinculações para **picasso-2.x.x.jar,** podemos chamar esses métodos de C#. Por exemplo:

```csharp
using Com.Squareup.Picasso;
...
Picasso.With (this)
    .Load ("http://mydomain.myimage.jpg")
    .Into (imageView);

```

### <a name="creating-the-bindings-library"></a>Criando a Biblioteca de Vinculações

Antes de começar com as etapas abaixo, baixe [picasso-2.x.x.jar](http://repo1.maven.org/maven2/com/squareup/picasso/picasso/2.5.2/picasso-2.5.2.jar).

Primeiro, crie um novo projeto de Biblioteca de Vinculações. No Visual Studio para Mac ou Visual Studio, crie uma nova solução e selecione o modelo *da Biblioteca de Vinculações* do Android. (As capturas de tela neste passo a passo usam o Visual Studio, mas o Visual Studio para Mac é muito semelhante.) Nomeie o **jarbinding de solução:** 

[![Criar projeto de biblioteca JarBinding](binding-a-jar-images/01-new-bindings-library-sml.w157.png)](binding-a-jar-images/01-new-bindings-library.w157.png#lightbox)

O modelo inclui uma pasta **Jars** onde você adiciona o seu . JAR(s) para o projeto Biblioteca de Vinculações. Clique com o botão direito do mouse na pasta **Jars** e **selecione Adicionar > item existente**: 

[![Adicionar item existente](binding-a-jar-images/02-add-existing-item-sml.png)](binding-a-jar-images/02-add-existing-item.png#lightbox)

Navegue até o arquivo **picasso-2.x.x.jar** baixado anteriormente, selecione-o e clique **em Adicionar**: 

[![Selecione arquivo de frasco e clique em Adicionar](binding-a-jar-images/03-select-jar-file-sml.png)](binding-a-jar-images/03-select-jar-file.png#lightbox)

Verifique se o arquivo **picasso-2.x.x.jar** foi adicionado com sucesso ao projeto: 

[![Jar adicionado ao projeto](binding-a-jar-images/04-jar-added-sml.png)](binding-a-jar-images/04-jar-added.png#lightbox)

Ao criar um projeto de biblioteca Java Bindings, você deve especificar se o . Jar deve ser incorporado na Biblioteca de Vinculações ou embalado separadamente. Para fazer isso, você especifica uma das seguintes *ações de compilação:* 

- **EmbeddedJar** &ndash; o . O JAR será incorporado na Biblioteca de Vinculações.

- **InputJar** &ndash; o . O JAR será mantido separado da Biblioteca de Vinculações.

Normalmente, você usa a ação **embeddedJar** para que o . Jar é automaticamente embalado na biblioteca de vinculações. Esta é a &ndash; opção mais simples Java bytecode no . Jar é convertido em bytecode Dex e é incorporado (juntamente com os Wrappers Callable Gerenciados) em seu APK. Se você quiser ficar com o. JAR separado da biblioteca de vinculações, você pode usar a opção **InputJar;** no entanto, você deve garantir que o . O arquivo JAR está disponível no dispositivo que executa seu aplicativo. 

Defina a ação de compilação **para EmbeddedJar**: 

[![Selecione a ação de compilação do EmbeddedJar](binding-a-jar-images/05-embeddedjar-sml.png)](binding-a-jar-images/05-embeddedjar.png#lightbox)

Em seguida, abra as propriedades do projeto para configurar o *Quadro de destino*. Se o . Jar usa quaisquer APIs do Android, defina o Target Framework para o nível de API que o . JAR espera. Normalmente, o desenvolvedor do . O arquivo JAR indicará qual nível de API (ou níveis) que o . Jar é compatível com. (Para obter mais informações sobre a configuração do Quadro de Destino e os níveis de API do Android em geral, consulte [Entendendo os níveis de API do Android](~/android/app-fundamentals/android-api-levels.md).)

Defina o nível de API de destino para sua Biblioteca de Vinculações (neste exemplo, estamos usando o nível 19 da API): 

[![Nível de API de destino definido como API 19](binding-a-jar-images/06-set-target-framework-sml.png)](binding-a-jar-images/06-set-target-framework.png#lightbox)

Finalmente, construa a Biblioteca de Vinculações. Embora algumas mensagens de aviso possam ser exibidas, o projeto Biblioteca de Vinculações deve ser construído com sucesso e produzir uma saída . DLL no seguinte local: **JarBinding/bin/Debug/JarBinding.dll**

### <a name="using-the-bindings-library"></a>Usando a Biblioteca de Vinculações

Para consumir isso. DLL em seu aplicativo Xamarin.Android, faça o seguinte:

1. Adicione uma referência à Biblioteca de Vinculações.

2. Faça chamadas para o . JAR através dos Wrappers Callable Gerenciados. 

Nas etapas seguintes, criaremos um aplicativo mínimo que usa a Biblioteca `ImageView`de Vinculações para baixar e exibir uma imagem em um ; o "levantamento pesado" é feito pelo código que reside no . Arquivo JAR. 

Primeiro, crie um novo aplicativo Xamarin.Android que consuma a Biblioteca de Vinculações. Clique com o botão direito do mouse na solução e selecione **Adicionar novo projeto;** nomear o novo projeto **BindingTest**. Estamos criando este aplicativo na mesma solução que a Biblioteca de Vinculações para simplificar esse passo a passo; no entanto, o aplicativo que consome a Biblioteca de Vinculações poderia, em vez disso, residir em uma solução diferente: 

[![Adicionar novo projeto BindingTest](binding-a-jar-images/07-add-new-project-sml.w157.png)](binding-a-jar-images/07-add-new-project.w157.png#lightbox)

Clique com o botão direito do mouse no nó **Referências** do projeto **BindingTest** e **selecione Adicionar referência...**:

[![Referência de adicionar direito](binding-a-jar-images/08-add-reference.png)](binding-a-jar-images/08-add-reference.png#lightbox)

Verifique o projeto **JarBinding** criado anteriormente e clique em **OK**:

[![Selecione o projeto JarBinding](binding-a-jar-images/09-choose-jar-binding-sml.png)](binding-a-jar-images/09-choose-jar-binding.png#lightbox)

Abra o nó **Referências** do projeto **BindingTest** e verifique se a referência **JarBinding** está presente: 

[![JarBinding aparece em Referências](binding-a-jar-images/10-references-shows-jarbinding-sml.png)](binding-a-jar-images/10-references-shows-jarbinding.png#lightbox)

Modifique o layout **BindingTest** **(Main.axml)** para `ImageView`que ele tenha um único :

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:minWidth="25px"
    android:minHeight="25px">
    <ImageView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:id="@+id/imageView" />
</LinearLayout>
```

Adicione a `using` seguinte declaração a **MainActivity.cs** &ndash; isso torna possível acessar `Picasso` facilmente os métodos da classe baseada em Java que reside na Biblioteca de Vinculações:

```csharp
using Com.Squareup.Picasso;
```

Modifique `OnCreate` o método para `Picasso` que ele use a classe para carregar `ImageView`uma imagem de uma URL e exibi-la no : 

```csharp
public class MainActivity : Activity
{
    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);
        SetContentView(Resource.Layout.Main);
        ImageView imageView = FindViewById<ImageView>(Resource.Id.imageView);

        // Use the Picasso jar library to load and display this image:
        Picasso.With (this)
            .Load ("http://i.imgur.com/DvpvklR.jpg")
            .Into (imageView);
    }
}
```

Compile e execute o projeto **BindingTest.** O aplicativo será inicializados e, após um pequeno atraso (dependendo das condições da rede), ele deve baixar e exibir uma imagem semelhante à seguinte captura de tela:

[![Captura de tela de BindingTest em execução](binding-a-jar-images/11-result-sml.png)](binding-a-jar-images/11-result.png#lightbox)

Parabéns! Você conseguiu uma biblioteca Java com sucesso. JAR e usado em seu aplicativo Xamarin.Android.

## <a name="summary"></a>Resumo

Neste passo a passo, criamos uma Biblioteca de Vinculações para terceiros. Arquivo JAR, adicionou a Biblioteca de Vinculações a um aplicativo de teste mínimo e, em seguida, executou o aplicativo para verificar se nosso código C# pode chamar código Java residente no . Arquivo JAR. 

## <a name="related-links"></a>Links relacionados

- [Construindo uma Biblioteca de Vinculações Java (vídeo)](https://university.xamarin.com/classes#10090)
- [Associação de uma biblioteca Java](~/android/platform/binding-java-library/index.md)
