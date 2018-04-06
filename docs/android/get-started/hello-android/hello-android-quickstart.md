---
title: 'Hello, Android: início rápido'
description: Neste guia de duas partes, você compilará seu primeiro aplicativo Xamarin.Android (usando o Visual Studio ou o Visual Studio para Mac) e passará a entender os fundamentos de desenvolvimento de aplicativos Android com Xamarin. Ao longo do caminho, serão apresentaremos ferramentas, conceitos e etapas necessários para criar e implantar um aplicativo Xamarin.Android.
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: 44007FA1-3ABC-4935-BF52-4613AF0553A6
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/08/2018
ms.openlocfilehash: c5937cc86a8a1f8506b14774b0429bee3c8aa594
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="hello-android-quickstart"></a>Hello, Android: início rápido

_Neste guia de duas partes, você compilará seu primeiro aplicativo Xamarin.Android (usando o Visual Studio ou o Visual Studio para Mac) e passará a entender os fundamentos de desenvolvimento de aplicativos Android com Xamarin. Ao longo do caminho, serão apresentaremos ferramentas, conceitos e etapas necessários para criar e implantar um aplicativo Xamarin.Android._

## <a name="hello-android-quickstart"></a>Início rápido do Hello, Android

Neste passo a passo, você criará um aplicativo que converte um número de telefone alfanumérico (inserido pelo usuário) em numérico e exibe o número de telefone numérico ao usuário. O aplicativo final tem esta aparência:

[ ![Captura de tela do aplicativo quando ele está concluído](hello-android-quickstart-images/intro-app-examples-sml.png)](hello-android-quickstart-images/intro-app-examples.png#lightbox)


## <a name="requirements"></a>Requisitos

Para acompanhar este passo a passo, você precisará do seguinte:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

-   Windows 7 ou posterior.

-   Visual Studio 2015 Professional ou posterior.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

-   A versão mais recente do Visual Studio para Mac.

-   Sistema operacional X Yosemite ou posterior.

-----

Este passo a passo pressupõe que a versão mais recente do Xamarin.Android esteja instalada e em execução em sua plataforma preferida. Para obter um guia de como instalar o Xamarin.Android, consulte os guias [Instalação do Xamarin.Android](~/android/get-started/installation/index.md).
Antes de começar, baixe e descompacte o conjunto [Ícones e telas de inicialização do aplicativo Xamarin](https://github.com/xamarin/monodroid-samples/blob/master/Phoneword/Resources/XamarinAndroidIcons.zip?raw=true).

## <a name="configuring-emulators"></a>Configurando emuladores

Se estiver usando o emulador de SDK do Android da Google, recomendamos que você configure o emulador para usar a aceleração de hardware. As instruções para configurar a aceleração de hardware estão disponíveis em [Aceleração de Hardware](~/android/get-started/installation/android-emulator/hardware-acceleration.md).

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Se você estiver usando o Emulador de Android do Visual Studio, Hyper-V deverá ser habilitado no computador. Para saber mais sobre como configurar o Emulador de Android do Visual Studio, consulte [System Requirements for the Visual Studio Emulator for Android](https://msdn.microsoft.com/en-us/library/mt228280.aspx) (Requisitos do sistema para o Emulador de Android do Visual Studio).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

-----

## <a name="walkthrough"></a>Passo a passo

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Inicie o Visual Studio.  Clique em **Arquivo > Novo > Projeto** para criar um novo projeto.

Na caixa de diálogo **Novo Projeto**, clique no modelo **Aplicativo em Branco (Android)**.
Dê um nome ao novo projeto `Phoneword`. Clique em **OK** para criar o novo projeto:

[ ![O novo projeto é Phoneword](hello-android-quickstart-images/vs/02-new-project-name-sml.png)](hello-android-quickstart-images/vs/02-new-project-name.png#lightbox)

### <a name="creating-the-layout"></a>Criando o layout

Depois da criação do novo projeto, expanda a pasta **Recursos** e, em seguida, a pasta **Layout** no **Gerenciador de Soluções**.
Clique duas vezes em **Main.axml** para abri-lo no Designer do Android. Este é o arquivo de layout da tela do aplicativo:

[![Abrir Main.axml](hello-android-quickstart-images/vs/04-open-layout-sml.png)](hello-android-quickstart-images/vs/04-open-layout.png#lightbox)

Na **Caixa de Ferramentas** (a área à esquerda), insira `text` no campo de pesquisa e arraste um widget **Texto (Grande)** para a superfície de design (a área no centro):

[![Adicionar widget de texto grande](hello-android-quickstart-images/vs/04-large-text-sml.png)](hello-android-quickstart-images/vs/04-large-text.png#lightbox)

Com o controle **Texto (Grande)** selecionado na superfície de design, use o painel **Propriedades** para alterar a propriedade `text` do widget **Texto (Grande)** para `Enter a Phoneword:`, conforme é mostrado aqui:

[![Definir propriedades de texto grande](hello-android-quickstart-images/vs/05-enter-a-phoneword-sml.png)](hello-android-quickstart-images/vs/05-enter-a-phoneword.png#lightbox)

Arraste um widget **Texto Sem Formatação** da **Caixa de Ferramentas** para a superfície de design e coloque-o sob o widget **Texto (Grande)**:

[![Adicionar widget de texto sem formatação](hello-android-quickstart-images/vs/06-plain-text-sml.png)](hello-android-quickstart-images/vs/06-plain-text.png#lightbox)

Com o widget **Texto Sem Formatação** selecionado na superfície de design, use o painel **Propriedades** para alterar a propriedade `id` do widget **Texto Sem Formatação** para `@+id/PhoneNumberText` e a propriedade `text` para `1-855-XAMARIN`:

[![Definir propriedades de texto sem formatação](hello-android-quickstart-images/vs/07-add-properties-sml.png)](hello-android-quickstart-images/vs/07-add-properties.png#lightbox)

Arraste um **Botão** da **Caixa de Ferramentas** para a superfície de design e coloque-o sob o widget **Texto Sem Formatação**:

[![Arraste o botão mover para o design](hello-android-quickstart-images/vs/08-drag-button-sml.png)](hello-android-quickstart-images/vs/08-drag-button.png#lightbox)

Com o **Botão** selecionado na superfície de design, use o painel **Propriedades** para alterar a propriedade `id` do **Botão** para `@+id/TranslateButton` e a propriedade `text` para `Translate`:

[![Definir as propriedades do botão mover](hello-android-quickstart-images/vs/09-translate-button-sml.png)](hello-android-quickstart-images/vs/09-translate-button.png#lightbox)

Arraste um **TextView** da **Caixa de ferramentas** para a superfície de design e coloque-o no widget **Botão**. Defina a propriedade `id` do **TextView** para `@+id/TranslatedPhoneWord` e altere a `text` para uma cadeia de caracteres vazia:

[![Definir as propriedades de exibição de texto.](hello-android-quickstart-images/vs/10-textview-properties-sml.png)](hello-android-quickstart-images/vs/10-textview-properties.png#lightbox)    

Salve seu trabalho pressionando **CTRL + S**.

### <a name="writing-translation-code"></a>Escrevendo o código de conversão

A próxima etapa é adicionar algum código para converter números de telefone de alfanuméricos para numéricos. Adicione um novo arquivo ao projeto clicando com o botão direito do mouse no projeto **Phoneword** no painel **Gerenciador de Soluções** e escolhendo **Adicionar > Novo Item…**, conforme é mostrado abaixo:

[![Adicionar novo item](hello-android-quickstart-images/vs/12-add-new-item-sml.png)](hello-android-quickstart-images/vs/12-add-new-item.png#lightbox)

Na caixa de diálogo **Adicionar Novo Item**, selecione **Visual C# > Código** e nomeie o novo arquivo de código **PhoneTranslator.cs**:

[![Adicionar PhoneTranslator.cs](hello-android-quickstart-images/vs/14-add-class-sml.png)](hello-android-quickstart-images/vs/14-add-class.png#lightbox)

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

### <a name="wiring-up-the-interface"></a>Conectando a interface

A próxima etapa é adicionar o código para conectar a interface do usuário inserindo o código de suporte na classe `MainActivity`. Comece por conectando o botão **Traduzir**. Na classe `MainActivity` localize o método `OnCreate`. A próxima etapa é adicionar o código do botão dentro de `OnCreate`, abaixo das chamadas `base.OnCreate(bundle)` e `SetContentView
(Resource.Layout.Main)`. Primeiro, modifique o código do modelo para que o método `OnCreate` fique semelhante ao seguinte:

```csharp
using System;
using Android.App;
using Android.Content;
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

            // New code will go here
        }
    }
}
```

Obtenha uma referência para os controles criados no arquivo de layout por meio do Designer do Android. Adicione o seguinte código dentro do método `OnCreate` após a chamada para `SetContentView`:

```csharp
// Get our UI controls from the loaded layout
EditText phoneNumberText = FindViewById<EditText>(Resource.Id.PhoneNumberText);
TextView translatedPhoneWord = FindViewById<TextView>(Resource.Id.TranslatedPhoneWord);
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

Se houver erros, repita as etapas anteriores e corrija-os até que o aplicativo seja compilado com êxito. Se você receber um erro de build, como _O recurso não existe no contexto atual_, verifique se o nome do namespace em **MainActivity.cs** coincide com o nome do projeto (`Phoneword`) e, em seguida, recompile completamente a solução. Se você ainda receber erros de build, verifique se instalou as atualizações mais recentes do Xamarin.Android.

### <a name="setting-the-label-and-app-icon"></a>Definindo o ícone do aplicativo e o rótulo

Agora você deve ter um aplicativo funcionando &ndash; é hora de adicionar os toques finais! Em **MainActivity.cs**, edite o `Label` da `MainActivity`. O `Label` é o que Android exibe na parte superior da tela para que os usuários saibam em que ponto estão no aplicativo.
Na parte superior da classe `MainActivity`, altere o `Label` para `Phone Word` conforme mostrado aqui:

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

Agora é o momento de definir o ícone do aplicativo. Como procedimento, o Visual Studio fornecerá um ícone padrão para o projeto. Vamos excluir estes arquivos da solução e substituí-los por um ícone diferente. Expanda a pasta **Recursos** no **Painel de Soluções**. Observe que há cinco pastas que são prefixadas com **mipmap-** e que cada uma delas contém um único arquivo **Icon.png**:

[![Pastas mipmap- e arquivos Icon.png](hello-android-quickstart-images/vs/21-mipmap-folders-sml.png)](hello-android-quickstart-images/vs/21-mipmap-folders.png#lightbox)

É necessário excluir cada um desses arquivos de ícone do projeto. Clique com o botão direito do mouse em cada um dos arquivos **Icon.png** e selecione **Excluir** no menu de contexto:
   
[![Excluir Icon.png padrão](hello-android-quickstart-images/vs/21-delete-icon-sml.png)](hello-android-quickstart-images/vs/21-delete-icon.png#lightbox)
   
Clique no botão **Excluir** na caixa de diálogo.

Em seguida, baixe e descompacte o [conjunto de Ícones do Aplicativo Xamarin](https://github.com/xamarin/monodroid-samples/blob/master/Phoneword/Resources/XamarinAndroidIcons.zip?raw=true). Este arquivo zip contém ícones para o aplicativo. Cada ícone é visualmente idêntico. No entanto, com resoluções diferentes, ele será renderizado da maneira correta em dispositivos diferentes e com densidades de tela distintas.  O conjunto de arquivos deve ser copiado no projeto Xamarin.Android. No Visual Studio, no **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta **mipmap-hdpi** e selecione **Adicionar > Itens Existentes**:

[![Adicionar arquivos](hello-android-quickstart-images/vs/22-add-files-sml.png)](hello-android-quickstart-images/vs/22-add-files.png#lightbox)

Na caixa de diálogo de seleção, navegue até o diretório descompactado Ícones do Aplicativo Xamarin e abra a pasta **mipmap-hdpi**. Selecione **Icon.png** e clique em **Adicionar**.

Repita estas etapas para cada uma das pastas **mipmap-** até que o conteúdo das pastas **mipmap-** de Ícones do Aplicativo Xamarin seja copiado para as pastas **mipmap-** equivalentes no projeto **Phoneword**.

Depois que todos os ícones forem copiados para o projeto Xamarin.Android, abra a caixa de diálogo **Opções de Projeto** ao clicar com o botão direito do mouse no projeto, no **Painel de Soluções**. Selecione **Compilar > Aplicativo Android** e selecione **@mipmap/icon** na caixa de combinação **Ícone do aplicativo**:

[![Configuração do ícone do projeto](hello-android-quickstart-images/vs/25-set-project-icon-sml.png)](hello-android-quickstart-images/vs/25-set-project-icon.png#lightbox)

### <a name="running-the-app"></a>Executando o aplicativo

Por fim, teste o aplicativo ao executá-lo em um dispositivo ou emulador Android e ao converter um Phoneword:

[ ![Captura de tela do aplicativo quando ele está concluído](hello-android-quickstart-images/intro-app-examples-sml.png)](hello-android-quickstart-images/intro-app-examples.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Inicialize o Visual Studio para Mac na pasta **Aplicativos** ou **Destaque**. 

Clique em **Nova Solução...** para criar um novo projeto.

Na caixa de diálogo **Escolher um modelo para seu novo projeto**, clique em **Android > Aplicativo** e selecione o modelo **Aplicativo Android**. Clique em **Avançar**.

[![Escolher o modelo de aplicativo Android](hello-android-quickstart-images/xs/03-choose-template-sml.png)](hello-android-quickstart-images/xs/03-choose-template.png#lightbox)

Na caixa de diálogo **Configurar seu Aplicativo Android**, nomeie o novo aplicativo `Phoneword` e clique em **Avançar**.

[![Configurar o Aplicativo Android](hello-android-quickstart-images/xs/04-configure-android-app-sml.png)](hello-android-quickstart-images/xs/04-configure-android-app.png#lightbox)

Na caixa de diálogo **Configurar seu novo Aplicativo Android**, deixe os nomes de Solução e Projeto definidos como `Phoneword` e clique em **Criar** para criar o projeto.

### <a name="creating-the-layout"></a>Criando o layout

Depois de criar o novo projeto, expanda a pasta **Recursos** e, em seguida, a pasta **Layout** no painel **Solução**.
Clique duas vezes em **Main.axml** para abri-lo no Designer do Android. Este é o arquivo de layout da tela quando ele for exibido no Designer Android:

[![Abrir Main.axml](hello-android-quickstart-images/xs/05-open-layout-sml.png)](hello-android-quickstart-images/xs/05-open-layout.png#lightbox)

Selecione o botão **Hello World, Clique em Mim!** **na** superfície de design e pressione a tecla **Excluir** para removê-lo. 

Na **Caixa de ferramentas** (a área à direita), digite `text` no campo de pesquisa e arraste um widget de **Texto (Grande)** para a superfície de design (a área no centro):

[![Adicionar widget de texto grande](hello-android-quickstart-images/xs/06-large-text-sml.png)](hello-android-quickstart-images/xs/06-large-text.png#lightbox)

Com o widget **Texto (Grande)** selecionado na superfície de design, use o painel **Propriedades** para alterar a propriedade `Text` do widget **Texto (Grande)** para `Enter a Phoneword:` conforme mostrado aqui:

[![Definir propriedades do widget de texto grande](hello-android-quickstart-images/xs/07-enter-a-phoneword-sml.png)](hello-android-quickstart-images/xs/07-enter-a-phoneword.png#lightbox)

Em seguida, arraste um widget de **Texto Sem Formatação** da **Caixa de ferramentas** para a superfície de design e coloque-o sob o widget de **Texto (Grande)**. Observe que você pode usar o campo de pesquisa para ajudar a localizar widgets por nome:

[![Adicionar widget de texto sem formatação](hello-android-quickstart-images/xs/08-plain-text-sml.png)](hello-android-quickstart-images/xs/08-plain-text.png#lightbox)

Com o widget **Texto Sem Formatação** selecionado na superfície de design, use o painel **Propriedades** para alterar a propriedade `Id` do controle de **Texto Sem Formatação** para `@+id/PhoneNumberText` e alterar a propriedade `Text` para `1-855-XAMARIN`:

[![Definir propriedades do widget de texto sem formatação](hello-android-quickstart-images/xs/09-add-properties-sml.png)](hello-android-quickstart-images/xs/09-add-properties.png#lightbox)

Arraste um **Botão** da **Caixa de Ferramentas** para a superfície de design e coloque-o sob o widget **Texto Sem Formatação**:

[![Adicionar um botão](hello-android-quickstart-images/xs/10-drag-button-sml.png)](hello-android-quickstart-images/xs/10-drag-button.png#lightbox)

Com o **Botão** selecionado na superfície de design, use o painel **Propriedades** para alterar a propriedade `Id` do **Botão** para `@+id/TranslateButton` e alterar a propriedade `Text` para `Translate`:

[![Configurar como o botão mover](hello-android-quickstart-images/xs/11-translate-button-sml.png)](hello-android-quickstart-images/xs/11-translate-button.png#lightbox)

Arraste um **TextView** da **Caixa de ferramentas** para a superfície de design e coloque-o no widget **Botão**. Com o **TextView** selecionado, defina a propriedade `id` do **TextView** para `@+id/TranslatedPhoneWord` e altere a `text` para uma cadeia de caracteres vazia:

[![Definir as propriedades de exibição de texto.](hello-android-quickstart-images/xs/12-textview-properties-sml.png)](hello-android-quickstart-images/xs/12-textview-properties.png#lightbox)    

Salve seu trabalho pressionando **&#8984; + S**.

### <a name="writing-translation-code"></a>Escrevendo o código de conversão

Agora, adicione algum código para traduzir números de telefone de alfanuméricos para numéricos. Adicione um novo arquivo ao projeto clicando no ícone de engrenagem ao lado do projeto **Phoneword** no painel **Solução** e escolhendo **Adicionar > Novo Arquivo...**:

[![Adicionar um novo arquivo ao projeto](hello-android-quickstart-images/xs/14-add-new-file-sml.png)](hello-android-quickstart-images/xs/14-add-new-file.png#lightbox)

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

### <a name="wiring-up-the-interface"></a>Conectando a interface

A próxima etapa é adicionar código para conectar a interface do usuário adicionando código de apoio à classe `MainActivity`.
Clique duas vezes em **MainActivity.cs** no **Painel de Soluções** para abri-lo.

Comece adicionando um manipulador de eventos no botão **Converter**. Na classe `MainActivity` localize o método `OnCreate`. Adicione o código do botão dentro de `OnCreate`, abaixo das chamadas `base.OnCreate(bundle)` e `SetContentView (Resource.Layout.Main)`. Remova o botão de modelo no código de tratamento para que o método `OnCreate` seja semelhante ao seguinte:

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

### <a name="setting-the-label-and-app-icon"></a>Definindo o ícone do aplicativo e o rótulo

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

Agora é o momento de definir o ícone do aplicativo. Por padrão, o Visual Studio para Mac fornecerá um ícone padrão para o projeto. Vamos excluir estes arquivos da solução e substituí-los por um ícone diferente. Expanda a pasta **Recursos** no **Painel de Soluções**. Observe que há cinco pastas que são prefixadas com **mipmap-** e que cada uma delas contém um único arquivo **Icon.png**:

[![Pastas mipmap- e arquivos Icon.png](hello-android-quickstart-images/xs/23-mipmap-folders-sml.png)](hello-android-quickstart-images/xs/23-mipmap-folders.png#lightbox)

É necessário excluir cada um desses arquivos de ícone do projeto. Clique com o botão direito do mouse em cada um dos arquivos **Icon.png** e selecione **Remover** no menu de contexto:

[![Excluir Icon.png padrão](hello-android-quickstart-images/xs/23-delete-icon-sml.png)](hello-android-quickstart-images/xs/23-delete-icon.png#lightbox)

Clique no botão **Excluir** na caixa de diálogo.

Em seguida, baixe e descompacte o [conjunto de Ícones do Aplicativo Xamarin](https://github.com/xamarin/monodroid-samples/blob/master/Phoneword/Resources/XamarinAndroidIcons.zip?raw=true). Este arquivo zip contém ícones para o aplicativo. Cada ícone é visualmente idêntico. No entanto, com resoluções diferentes, ele será renderizado da maneira correta em dispositivos diferentes e com densidades de tela distintas.  O conjunto de arquivos deve ser copiado no projeto Xamarin.Android. No Visual Studio para Mac, no **Painel de Soluções**, clique com o botão direito do mouse na pasta **mipmap-hdpi** e selecione **Adicionar > Adicionar Arquivos**:

[![Adicionar arquivos](hello-android-quickstart-images/xs/24-add-files-sml.png)](hello-android-quickstart-images/xs/24-add-files.png#lightbox)

Na caixa de diálogo de seleção, navegue até o diretório descompactado Ícones do Aplicativo Xamarin e abra a pasta **mipmap-hdpi**. Selecione **Icon.png** e clique em **Abrir**.

Na caixa de diálogo **Adicionar Arquivo à Pasta**, selecione **Copiar o arquivo para o diretório** e clique em **OK**:

[![Copiar o arquivo para a caixa de diálogo do diretório](hello-android-quickstart-images/xs/26-copy-to-directory-sml.png)](hello-android-quickstart-images/xs/26-copy-to-directory.png#lightbox)

Repita estas etapas para cada uma das pastas **mipmap-** até que o conteúdo das pastas **mipmap-** de Ícones do Aplicativo Xamarin seja copiado para as pastas **mipmap-** equivalentes no projeto **Phoneword**.

Depois que todos os ícones forem copiados para o projeto Xamarin.Android, abra a caixa de diálogo **Opções de Projeto** ao clicar com o botão direito do mouse no projeto, no **Painel de Soluções**. Selecione **Compilar > Aplicativo Android** e selecione **@mipmap/icon** na caixa de combinação **Ícone do aplicativo**:

[![Configuração do ícone do projeto](hello-android-quickstart-images/xs/28-set-project-icon-sml.png)](hello-android-quickstart-images/xs/28-set-project-icon.png#lightbox)

### <a name="running-the-app"></a>Executando o aplicativo

Por fim, teste o aplicativo ao executá-lo em um dispositivo ou emulador Android e ao converter um Phoneword:

[ ![Captura de tela do aplicativo quando ele está concluído](hello-android-quickstart-images/intro-app-examples-sml.png)](hello-android-quickstart-images/intro-app-examples.png#lightbox)

-----

Parabéns por concluir seu primeiro aplicativo Xamarin.Android!
Agora é hora de dissecar as ferramentas e habilidades que você acabou de aprender. A próxima etapa é o [Aprofundamento no Hello, Android](~/android/get-started/hello-android/hello-android-deepdive.md).


## <a name="related-links"></a>Links relacionados

- [Ícones de aplicativo do Xamarin Android (ZIP)](https://github.com/xamarin/monodroid-samples/blob/master/Phoneword/Resources/XamarinAndroidIcons.zip?raw=true)
- [Phoneword (amostra)](https://developer.xamarin.com/samples/monodroid/Phoneword)
