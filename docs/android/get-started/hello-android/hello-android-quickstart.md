---
title: 'Olá, Android: Guia de Início Rápido'
description: Neste guia de duas partes, você compilará seu primeiro aplicativo Xamarin.Android (usando o Visual Studio ou o Visual Studio para Mac) e passará a entender os fundamentos de desenvolvimento de aplicativos Android com Xamarin. Ao longo do caminho, serão apresentaremos ferramentas, conceitos e etapas necessários para criar e implantar um aplicativo Xamarin.Android.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: 44007FA1-3ABC-4935-BF52-4613AF0553A6
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 10/05/2018
ms.openlocfilehash: 71343aa69cc7fa34fb716ea39454e6437a64169f
ms.sourcegitcommit: c4be32ef914465e808d89767c4d5ee72afe93cc6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2019
ms.locfileid: "58855075"
---
# <a name="hello-android-quickstart"></a>Olá, Android: Guia de Início Rápido

_Neste guia de duas partes, você criará seu primeiro aplicativo Xamarin.Android com o Visual Studio e passará a entender os fundamentos do desenvolvimento de aplicativos Android com Xamarin._

[![DBaixar a amostra](~/media/shared/download.png) Baixar a amostra](https://developer.xamarin.com/samples/monodroid/Phoneword/)

Você criará um aplicativo que converte um número de telefone alfanumérico (inserido pelo usuário) em numérico e exibe o número de telefone numérico ao usuário. O aplicativo final tem esta aparência:

[![SCaptura de tela do aplicativo quando ele está concluído](hello-android-quickstart-images/vs/15-running-app-sml.png)](hello-android-quickstart-images/vs/15-running-app.png#lightbox)

::: zone pivot="windows"

## <a name="windows-requirements"></a>Requisitos do Windows

Para acompanhar este passo a passo, você precisará do seguinte:

- Windows 10.

- Visual Studio 2019 ou Visual Studio 2017 (versão 15.8 ou posterior): Community, Professional ou Enterprise.

::: zone-end
::: zone pivot="macos"

## <a name="macos-requirements"></a>requisitos do macOS

Para acompanhar este passo a passo, você precisará do seguinte:

- A versão mais recente do Visual Studio para Mac.

- Um Mac executando o macOS High Sierra (10.13) ou posterior.

::: zone-end

Este passo a passo pressupõe que a versão mais recente do Xamarin.Android esteja instalada e em execução em sua plataforma preferida. Para obter um guia de como instalar o Xamarin.Android, consulte os guias [Instalação do Xamarin.Android](~/android/get-started/installation/index.md).

## <a name="configuring-emulators"></a>Configurando emuladores

Se estiver usando o emulador do Android, recomendamos configurá-lo para usar a aceleração de hardware. As instruções para configurar a aceleração de hardware estão disponíveis em [Aceleração de hardware para desempenho do emulador](~/android/get-started/installation/android-emulator/hardware-acceleration.md).

## <a name="create-the-project"></a>Criar o projeto

::: zone pivot="windows"

Inicie o Visual Studio. Clique em **Arquivo > Novo > Projeto** para criar um novo projeto.

Na caixa de diálogo **Novo Projeto**, clique no modelo **Aplicativo Android**.
Nomeie o novo projeto `Phoneword` e clique em **OK**:

[![NO novo projeto é Phoneword](hello-android-quickstart-images/vs/01-new-project-name-w158-sml.png)](hello-android-quickstart-images/vs/01-new-project-name-w158.png#lightbox)

Na caixa de diálogo **Novo Aplicativo Android**, clique em **Aplicativo em Branco** e clique em **OK** para criar o novo projeto:

[![SSelecionar o modelo Aplicativo em Branco](hello-android-quickstart-images/vs/02-blank-app-w158-sml.png)](hello-android-quickstart-images/vs/02-blank-app-w158.png#lightbox)

## <a name="create-a-layout"></a>Criar um layout

Depois da criação do novo projeto, expanda a pasta **Recursos** e, em seguida, a pasta **Layout** no **Gerenciador de Soluções**.
Clique duas vezes em **activity_main.axml** para abri-lo no Designer Android. Este é o arquivo de layout da tela do aplicativo:

[![OAbrir o arquivo axml da atividade](hello-android-quickstart-images/vs/03-open-layout-w158-sml.png)](hello-android-quickstart-images/vs/03-open-layout-w158.png#lightbox)

> [!TIP]
> As versões mais recentes do Visual Studio contêm um modelo de aplicativo um pouco diferente.
>
> 1. Em vez de estar em **activity_main.axml**, o layout está em **content_main.axml**.
> 2. O layout padrão será um `RelativeLayout`. Para que o restante das etapas nesta página funcionem, você deve alterar a tag `<RelativeLayout>` para `<LinearLayout>` e adicionar outro atributo `android:orientation="vertical"` à tag de abertura `LinearLayout`.

Na **Caixa de Ferramentas** (a área à esquerda), insira `text` no campo de pesquisa e arraste um widget **Texto (Grande)** para a superfície de design (a área no centro):

[![AAdicionar widget de texto grande](hello-android-quickstart-images/vs/04-large-text-w158-sml.png)](hello-android-quickstart-images/vs/04-large-text-w158.png#lightbox)

Com o controle **Texto (Grande)** selecionado na superfície de design, use o painel **Propriedades** para alterar a propriedade `Text` do widget **Texto (Grande)** para `Enter a Phoneword:`:

[![SDefinir propriedades de texto grande](hello-android-quickstart-images/vs/05-enter-a-phoneword-w158-sml.png)](hello-android-quickstart-images/vs/05-enter-a-phoneword-w158.png#lightbox)

Arraste um widget **Texto Sem Formatação** da **Caixa de ferramentas** para a superfície de design e coloque-o sob o widget de **Texto (Grande)**. O posicionamento do widget não ocorrerá até que você mova o ponteiro do mouse até um local no layout que aceite o widget. Nas capturas de tela abaixo, o widget não poderá ser posicionado (como visto à esquerda) até que o ponteiro do mouse esteja logo abaixo do `TextView` anterior (conforme mostrado à direita):

[![MO mouse indica onde o widget pode ser posicionado](hello-android-quickstart-images/vs/06a-cant-drop-w158-sml.png)](hello-android-quickstart-images/vs/06a-cant-drop-w158.png#lightbox)

Quando o **Texto Sem Formatação** (um widget `EditText`) for posicionado corretamente, ele será exibido conforme ilustrado na captura de tela a seguir:

[![AAdicionar widget de texto sem formatação](hello-android-quickstart-images/vs/06b-plain-text-w158-sml.png)](hello-android-quickstart-images/vs/06b-plain-text-w158.png#lightbox)

Com o widget **Texto Sem Formatação** selecionado na superfície de design, use o painel **Propriedades** para alterar a propriedade `Id` do widget **Texto Sem Formatação** para `@+id/PhoneNumberText` e a propriedade `Text` para `1-855-XAMARIN`:

[![SDefinir propriedades de texto sem formatação](hello-android-quickstart-images/vs/07-add-properties-w158-sml.png)](hello-android-quickstart-images/vs/07-add-properties-w158.png#lightbox)

Arraste um **Botão** da **Caixa de Ferramentas** para a superfície de design e coloque-o sob o widget **Texto Sem Formatação**:

[![DArrastar o botão mover para o design](hello-android-quickstart-images/vs/08-drag-button-w158-sml.png)](hello-android-quickstart-images/vs/08-drag-button-w158.png#lightbox)

Com o **Botão** selecionado na superfície de design, use o painel **Propriedades** para alterar sua propriedade `Text` para `Translate` e sua propriedade `Id` para `@+id/TranslateButton`:

[![SDefinir as propriedades do botão mover](hello-android-quickstart-images/vs/09-translate-button-w158-sml.png)](hello-android-quickstart-images/vs/09-translate-button-w158.png#lightbox)

Arraste um **TextView** da **Caixa de ferramentas** para a superfície de design e coloque-o no widget **Botão**. Altere a propriedade `Text` do **TextView** para uma cadeia de caracteres vazia e defina sua propriedade `Id` para `@+id/TranslatedPhoneword`:

[![SDefinir as propriedades de exibição de texto.](hello-android-quickstart-images/vs/10-textview-properties-w158-sml.png)](hello-android-quickstart-images/vs/10-textview-properties-w158.png#lightbox)

Salve seu trabalho pressionando **CTRL + S**.

## <a name="write-some-code"></a>Escrever algum código

A próxima etapa é adicionar algum código para converter números de telefone de alfanuméricos para numéricos. Adicione um novo arquivo ao projeto clicando com o botão direito do mouse no projeto **Phoneword** no painel **Gerenciador de Soluções** e escolhendo **Adicionar > Novo Item…**, conforme é mostrado abaixo:

[![AAdicionar novo item](hello-android-quickstart-images/vs/12-add-new-item-w158-sml.png)](hello-android-quickstart-images/vs/12-add-new-item-w158.png#lightbox)

Na caixa de diálogo **Adicionar Novo Item**, selecione **Visual C# > Código > Arquivo de Código** e nomeie o novo arquivo de código **PhoneTranslator.cs**:

[![AAdicionar PhoneTranslator.cs](hello-android-quickstart-images/vs/14-add-class-w158-sml.png)](hello-android-quickstart-images/vs/14-add-class-w158.png#lightbox)

Isso cria uma nova classe C# vazia. Insira o seguinte código nesse arquivo:

```csharp
using System.Text;
using System;
namespace Core
{
    public static class PhonewordTranslator
    {
        public static string ToNumber(string raw)
        {
            if (string.IsNullOrWhiteSpace(raw))
                return "";
            else
                raw = raw.ToUpperInvariant();

            var newNumber = new StringBuilder();
            foreach (var c in raw)
            {
                if (" -0123456789".Contains(c))
                {
                    newNumber.Append(c);
                }
                else
                {
                    var result = TranslateToNumber(c);
                    if (result != null)
                        newNumber.Append(result);
                }
                // otherwise we've skipped a non-numeric char
            }
            return newNumber.ToString();
        }
        static bool Contains (this string keyString, char c)
        {
            return keyString.IndexOf(c) >= 0;
        }
        static int? TranslateToNumber(char c)
        {
            if ("ABC".Contains(c))
                return 2;
            else if ("DEF".Contains(c))
                return 3;
            else if ("GHI".Contains(c))
                return 4;
            else if ("JKL".Contains(c))
                return 5;
            else if ("MNO".Contains(c))
                return 6;
            else if ("PQRS".Contains(c))
                return 7;
            else if ("TUV".Contains(c))
                return 8;
            else if ("WXYZ".Contains(c))
                return 9;
            return null;
        }
    }
}
```

Salve as alterações no arquivo **PhoneTranslator.cs** clicando em **Arquivo > Salvar** (ou pressionando **CTRL + S**) e fechando o arquivo.

## <a name="wire-up-the-user-interface"></a>Conectar a interface do usuário

A próxima etapa é adicionar o código para conectar a interface do usuário inserindo o código de suporte na classe `MainActivity`. Comece por conectando o botão **Traduzir**. Na classe `MainActivity` localize o método `OnCreate`. A próxima etapa é adicionar o código do botão dentro de `OnCreate`, abaixo das chamadas `base.OnCreate(savedInstanceState)` e `SetContentView(Resource.Layout.activity_main)`. Primeiro, modifique o código do modelo para que o método `OnCreate` fique semelhante ao seguinte:

```csharp
using Android.App;
using Android.OS;
using Android.Support.V7.App;
using Android.Runtime;
using Android.Widget;

namespace Phoneword
{
    [Activity(Label = "@string/app_name", Theme = "@style/AppTheme", MainLauncher = true)]
    public class MainActivity : AppCompatActivity
    {
        protected override void OnCreate(Bundle savedInstanceState)
        {
            base.OnCreate(savedInstanceState);

            // Set our view from the "main" layout resource
            SetContentView(Resource.Layout.activity_main);

            // New code will go here
        }
    }
}
```

Obtenha uma referência para os controles criados no arquivo de layout por meio do Designer do Android. Adicione o seguinte código dentro do método `OnCreate` após a chamada para `SetContentView`:

```csharp
// Get our UI controls from the loaded layout
EditText phoneNumberText = FindViewById<EditText>(Resource.Id.PhoneNumberText);
TextView translatedPhoneWord = FindViewById<TextView>(Resource.Id.TranslatedPhoneword);
Button translateButton = FindViewById<Button>(Resource.Id.TranslateButton);
```

Adicione o código que responda aos pressionamentos do botão **Traduzir** pelo usuário.
Adicione o seguinte código ao método `OnCreate` (após as linhas adicionadas na etapa anterior):

```csharp
// Add code to translate number
translateButton.Click += (sender, e) =>
{
    // Translate user's alphanumeric phone number to numeric
    string translatedNumber = Core.PhonewordTranslator.ToNumber(phoneNumberText.Text);
    if (string.IsNullOrWhiteSpace(translatedNumber))
    {
        translatedPhoneWord.Text = string.Empty;
    }
    else
    {
        translatedPhoneWord.Text = translatedNumber;
    }
};
```

Salve seu trabalho selecionando **Arquivo > Salvar Tudo** (ou pressionando **CTRL-SHIFT-S**) e compile o aplicativo selecionando **Compilar > Recompilar Solução** (ou pressionando **CTRL-SHIFT-B**). 

Se houver erros, repita as etapas anteriores e corrija-os até que o aplicativo seja compilado com êxito. Se você receber um erro de build, como _O recurso não existe no contexto atual_, verifique se o nome do namespace em **MainActivity.cs** coincide com o nome do projeto (`Phoneword`) e, em seguida, recompile completamente a solução. Se você ainda receber erros de build, verifique se instalou as atualizações mais recentes do Visual Studio.

## <a name="set-the-app-name"></a>Definir o nome do aplicativo

Agora você deve ter um aplicativo funcionando &ndash; é hora de definir o nome do aplicativo. Expanda a pasta **valores** (dentro da pasta **Recursos**) e abra o arquivo **strings.xml**. Altere a cadeia de caracteres do nome do aplicativo para `Phone Word` conforme mostrado aqui:

```xml
<resources>
    <string name="app_name">Phone Word</string>
    <string name="action_settings">Settings</string>
</resources>
```

## <a name="run-the-app"></a>Executar o aplicativo

Teste o aplicativo ao executá-lo em um dispositivo ou emulador Android.
Toque no botão **CONVERTER** para converter **1-855-XAMARIN** em um número de telefone:

[![SCaptura de tela do aplicativo em execução](hello-android-quickstart-images/vs/15-running-app-sml.png)](hello-android-quickstart-images/vs/15-running-app.png#lightbox)

Para executar o aplicativo em um dispositivo Android, confira como [configurar seu dispositivo para desenvolvimento](~/android/get-started/installation/set-up-device-for-development.md).

::: zone-end
::: zone pivot="macos"

Inicialize o Visual Studio para Mac na pasta **Aplicativos** ou **Destaque**.

Clique em **Novo Projeto...** para criar um novo projeto.

Na caixa de diálogo **Escolher um modelo para seu novo projeto**, clique em **Android > Aplicativo** e selecione o modelo **Aplicativo Android**. Clique em **Avançar**.

[![CEscolher o modelo de aplicativo do Android](hello-android-quickstart-images/xs/03-choose-template-sml.png)](hello-android-quickstart-images/xs/03-choose-template.png#lightbox)

Na caixa de diálogo **Configurar seu Aplicativo Android**, nomeie o novo aplicativo `Phoneword` e clique em **Avançar**.

[![CConfigurar o aplicativo Android](hello-android-quickstart-images/xs/04-configure-android-app-sml.png)](hello-android-quickstart-images/xs/04-configure-android-app.png#lightbox)

Na caixa de diálogo **Configurar seu novo Aplicativo Android**, deixe os nomes de Solução e Projeto definidos como `Phoneword` e clique em **Criar** para criar o projeto.

## <a name="create-a-layout"></a>Criar um layout

Depois de criar o novo projeto, expanda a pasta **Recursos** e, em seguida, a pasta **Layout** no painel **Solução**.
Clique duas vezes em **Main.axml** para abri-lo no Designer do Android. Este é o arquivo de layout da tela quando ele for exibido no Designer Android:

[![OAbrir Main.axml](hello-android-quickstart-images/xs/05-open-layout-sml.png)](hello-android-quickstart-images/xs/05-open-layout.png#lightbox)

Selecione o botão **Hello World, Clique em Mim!** **na** superfície de design e pressione a tecla **Excluir** para removê-lo. 

Na **Caixa de ferramentas** (a área à direita), digite `text` no campo de pesquisa e arraste um widget de **Texto (Grande)** para a superfície de design (a área no centro):

[![AAdicionar widget de texto grande](hello-android-quickstart-images/xs/06-large-text-sml.png)](hello-android-quickstart-images/xs/06-large-text.png#lightbox)

Com o widget **Texto (Grande)** selecionado na superfície de design, use o painel **Propriedades** para alterar a propriedade `Text` do widget **Texto (Grande)** para `Enter a Phoneword:` conforme mostrado aqui:

[![SDefinir propriedades do widget de texto grande](hello-android-quickstart-images/xs/07-enter-a-phoneword-sml.png)](hello-android-quickstart-images/xs/07-enter-a-phoneword.png#lightbox)

Em seguida, arraste um widget de **Texto Sem Formatação** da **Caixa de ferramentas** para a superfície de design e coloque-o sob o widget de **Texto (Grande)**. Observe que você pode usar o campo de pesquisa para ajudar a localizar widgets por nome:

[![AAdicionar widget de texto sem formatação](hello-android-quickstart-images/xs/08-plain-text-sml.png)](hello-android-quickstart-images/xs/08-plain-text.png#lightbox)

Com o widget **Texto Sem Formatação** selecionado na superfície de design, use o painel **Propriedades** para alterar a propriedade `Id` do controle de **Texto Sem Formatação** para `@+id/PhoneNumberText` e alterar a propriedade `Text` para `1-855-XAMARIN`:

[![SDefinir propriedades do widget de texto sem formatação](hello-android-quickstart-images/xs/09-add-properties-sml.png)](hello-android-quickstart-images/xs/09-add-properties.png#lightbox)

Arraste um **Botão** da **Caixa de Ferramentas** para a superfície de design e coloque-o sob o widget **Texto Sem Formatação**:

[![AAdicionar um botão](hello-android-quickstart-images/xs/10-drag-button-sml.png)](hello-android-quickstart-images/xs/10-drag-button.png#lightbox)

Com o **Botão** selecionado na superfície de design, use o painel **Propriedades** para alterar a propriedade `Id` do **Botão** para `@+id/TranslateButton` e alterar a propriedade `Text` para `Translate`:

[![CConfigurar como o botão mover](hello-android-quickstart-images/xs/11-translate-button-sml.png)](hello-android-quickstart-images/xs/11-translate-button.png#lightbox)

Arraste um **TextView** da **Caixa de ferramentas** para a superfície de design e coloque-o no widget **Botão**. Com o **TextView** selecionado, defina a propriedade `id` do **TextView** para `@+id/TranslatedPhoneWord` e altere a `text` para uma cadeia de caracteres vazia:

[![SDefinir as propriedades de exibição de texto.](hello-android-quickstart-images/xs/12-textview-properties-sml.png)](hello-android-quickstart-images/xs/12-textview-properties.png#lightbox)    

Salve seu trabalho pressionando **&#8984; + S**.

## <a name="write-some-code"></a>Escrever algum código

Agora, adicione algum código para traduzir números de telefone de alfanuméricos para numéricos. Adicione um novo arquivo ao projeto clicando no ícone de engrenagem ao lado do projeto **Phoneword** no painel **Solução** e escolhendo **Adicionar > Novo Arquivo...**:

[![AAdicionar um novo arquivo ao projeto](hello-android-quickstart-images/xs/14-add-new-file-sml.png)](hello-android-quickstart-images/xs/14-add-new-file.png#lightbox)

Na caixa de diálogo **Novo Arquivo**, selecione **Geral > Classe Vazia**, nomeie o novo arquivo **PhoneTranslator** e clique em **Novo**. Isso cria uma nova classe C# vazia para nós.

Remova todo o código de modelo na nova classe e substitua-o pelo código a seguir:

```csharp
using System.Text;
using System;
namespace Core
{
    public static class PhonewordTranslator
    {
        public static string ToNumber(string raw)
        {
            if (string.IsNullOrWhiteSpace(raw))
                return "";
            else
                raw = raw.ToUpperInvariant();

            var newNumber = new StringBuilder();
            foreach (var c in raw)
            {
                if (" -0123456789".Contains(c))
                {
                    newNumber.Append(c);
                }
                else
                {
                    var result = TranslateToNumber(c);
                    if (result != null)
                        newNumber.Append(result);
                }
                // otherwise we've skipped a non-numeric char
            }
            return newNumber.ToString();
        }
        static bool Contains (this string keyString, char c)
        {
            return keyString.IndexOf(c) >= 0;
        }
        static int? TranslateToNumber(char c)
        {
            if ("ABC".Contains(c))
                return 2;
            else if ("DEF".Contains(c))
                return 3;
            else if ("GHI".Contains(c))
                return 4;
            else if ("JKL".Contains(c))
                return 5;
            else if ("MNO".Contains(c))
                return 6;
            else if ("PQRS".Contains(c))
                return 7;
            else if ("TUV".Contains(c))
                return 8;
            else if ("WXYZ".Contains(c))
                return 9;
            return null;
        }
    }
}
```

Salve as alterações no arquivo **PhoneTranslator.cs** escolhendo **Arquivo > Salvar** (ou pressionando **&#8984; + S**) e fechando o arquivo. Verifique se não há erros de tempo de compilação recriando a solução.

## <a name="wire-up-the-user-interface"></a>Conectar a interface do usuário

A próxima etapa é adicionar código para conectar a interface do usuário adicionando código de apoio à classe `MainActivity`.
Clique duas vezes em **MainActivity.cs** no **Painel de Soluções** para abri-lo.

Comece adicionando um manipulador de eventos no botão **Converter**. Na classe `MainActivity` localize o método `OnCreate`. Adicione o código do botão dentro de `OnCreate`, abaixo das chamadas `base.OnCreate(bundle)` e `SetContentView (Resource.Layout.Main)`. Remova qualquer código de tratamento de botão existente (ou seja, código que faça referência a `Resource.Id.myButton` e crie um manipulador de cliques para ele) para que o método `OnCreate` seja semelhante ao seguinte:

```csharp
using System;
using Android.App;
using Android.Content;
using Android.Runtime;
using Android.Views;
using Android.Widget;
using Android.OS;

namespace Phoneword
{
    [Activity (Label = "Phone Word", MainLauncher = true)]
    public class MainActivity : Activity
    {
        protected override void OnCreate (Bundle bundle)
        {
            base.OnCreate (bundle);

            // Set our view from the "main" layout resource
            SetContentView (Resource.Layout.Main);

            // Our code will go here
        }
    }
}
```

Em seguida, uma referência é necessária para os controles que foram criados no arquivo de layout com o Designer do Android. Adicione o seguinte código dentro do método `OnCreate` (após a chamada para `SetContentView`):

```csharp
// Get our UI controls from the loaded layout
EditText phoneNumberText = FindViewById<EditText>(Resource.Id.PhoneNumberText);
TextView translatedPhoneWord = FindViewById<TextView>(Resource.Id.TranslatedPhoneWord);
Button translateButton = FindViewById<Button>(Resource.Id.TranslateButton);
```

Adicione o código que responde às pressões de usuário do botão **Traduzir** adicionando o seguinte código ao método `OnCreate` (após as linhas adicionadas na última etapa):

```csharp
// Add code to translate number
string translatedNumber = string.Empty;

translateButton.Click += (sender, e) =>
{
    // Translate user's alphanumeric phone number to numeric
    translatedNumber = PhonewordTranslator.ToNumber(phoneNumberText.Text);
    if (string.IsNullOrWhiteSpace(translatedNumber))
    {
        translatedPhoneWord.Text = string.Empty;
    }
    else
    {
        translatedPhoneWord.Text = translatedNumber;
    }
};
```

Salve seu trabalho e compile o aplicativo selecionando **Compilar > Compilar Tudo** (ou pressionando **&#8984; + B**). Se o aplicativo for compilado, você receberá uma mensagem de êxito na parte superior do Visual Studio para Mac:

Se houver erros, repita as etapas anteriores e corrija-os até que o aplicativo seja compilado com êxito. Se você receber um erro de build, como _O recurso não existe no contexto atual_, verifique se o nome do namespace em **MainActivity.cs** coincide com o nome do projeto (`Phoneword`) e, em seguida, recompile completamente a solução. Se você ainda receber erros de build, verifique se instalou as atualizações mais recentes do Xamarin.Android e do Visual Studio para Mac.

## <a name="set-the-label-and-app-icon"></a>Definir o rótulo e o ícone do aplicativo

Agora que você tem um aplicativo funcionando, é hora de adicionar os toques finais! Comece editando o `Label` para `MainActivity`.
O `Label` é o que Android exibe na parte superior da tela para que os usuários saibam em que ponto estão no aplicativo. Na parte superior da classe `MainActivity`, altere o `Label` para `Phone Word` conforme mostrado aqui:

```csharp
namespace Phoneword
{
    [Activity (Label = "Phone Word", MainLauncher = true)]
    public class MainActivity : Activity
    {
        ...
    }
}
```

Agora é o momento de definir o ícone do aplicativo. Por padrão, o Visual Studio para Mac fornecerá um ícone padrão para o projeto. Exclua estes arquivos da solução e substitua-os por um ícone diferente. Expanda a pasta **Recursos** no **Painel de Soluções**. Observe que há cinco pastas que são prefixadas com **mipmap-** e que cada uma delas contém um único arquivo **Icon.png**:

[![mPastas mipmap- e arquivos Icon.png](hello-android-quickstart-images/xs/23-mipmap-folders-sml.png)](hello-android-quickstart-images/xs/23-mipmap-folders.png#lightbox)

É necessário excluir cada um desses arquivos de ícone do projeto. Clique com o botão direito do mouse em cada um dos arquivos **Icon.png** e selecione **Remover** no menu de contexto:

[![DExcluir Icon.png padrão](hello-android-quickstart-images/xs/23-delete-icon-sml.png)](hello-android-quickstart-images/xs/23-delete-icon.png#lightbox)

Clique no botão **Excluir** na caixa de diálogo.

Em seguida, baixe e descompacte o [conjunto de Ícones do Aplicativo Xamarin](https://github.com/xamarin/monodroid-samples/blob/master/Phoneword/Resources/XamarinAndroidIcons.zip?raw=true). Este arquivo zip contém ícones para o aplicativo. Cada ícone é visualmente idêntico. No entanto, com resoluções diferentes, ele será renderizado da maneira correta em dispositivos diferentes e com densidades de tela distintas.  O conjunto de arquivos deve ser copiado no projeto Xamarin.Android. No Visual Studio para Mac, no **Painel de Soluções**, clique com o botão direito do mouse na pasta **mipmap-hdpi** e selecione **Adicionar > Adicionar Arquivos**:

[![AAdicionar arquivos](hello-android-quickstart-images/xs/24-add-files-sml.png)](hello-android-quickstart-images/xs/24-add-files.png#lightbox)

Na caixa de diálogo de seleção, navegue até o diretório descompactado Ícones do Aplicativo Xamarin e abra a pasta **mipmap-hdpi**. Selecione **Icon.png** e clique em **Abrir**.

Na caixa de diálogo **Adicionar Arquivo à Pasta**, selecione **Copiar o arquivo para o diretório** e clique em **OK**:

[![CCopiar o arquivo para a caixa de diálogo do diretório](hello-android-quickstart-images/xs/26-copy-to-directory-sml.png)](hello-android-quickstart-images/xs/26-copy-to-directory.png#lightbox)

Repita estas etapas para cada uma das pastas **mipmap-** até que o conteúdo das pastas **mipmap-** de Ícones do Aplicativo Xamarin seja copiado para as pastas **mipmap-** equivalentes no projeto **Phoneword**.

Depois que todos os ícones forem copiados para o projeto Xamarin.Android, abra a caixa de diálogo **Opções de Projeto** ao clicar com o botão direito do mouse no projeto, no **Painel de Soluções**. Selecione **Compilar > Aplicativo Android** e selecione `@mipmap/icon` na caixa de combinação **Ícone do aplicativo**:

[![SConfiguração do ícone do projeto](hello-android-quickstart-images/xs/28-set-project-icon-sml.png)](hello-android-quickstart-images/xs/28-set-project-icon.png#lightbox)

## <a name="run-the-app"></a>Executar o aplicativo

Por fim, teste o aplicativo ao executá-lo em um dispositivo ou emulador Android e ao converter um Phoneword:

[![SCaptura de tela do aplicativo quando ele está concluído](hello-android-quickstart-images/intro-app-examples-sml.png)](hello-android-quickstart-images/intro-app-examples.png#lightbox)

Para executar o aplicativo em um dispositivo Android, confira como [configurar seu dispositivo para desenvolvimento](~/android/get-started/installation/set-up-device-for-development.md).

::: zone-end

Parabéns por concluir seu primeiro aplicativo Xamarin.Android!
Agora é hora de dissecar as ferramentas e habilidades que você acabou de aprender. A próxima etapa é o [Aprofundamento no Hello, Android](~/android/get-started/hello-android/hello-android-deepdive.md).

## <a name="related-links"></a>Links relacionados

- [Ícones de aplicativo do Xamarin Android (ZIP)](https://github.com/xamarin/monodroid-samples/blob/master/Phoneword/Resources/XamarinAndroidIcons.zip?raw=true)
- [Phoneword (exemplo)](https://developer.xamarin.com/samples/monodroid/Phoneword)
